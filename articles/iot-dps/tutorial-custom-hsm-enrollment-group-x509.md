---
title: Tutorial - Provision X.509 devices to Azure IoT Hub using a custom Hardware Security Module (HSM)
description: This tutorial uses enrollment groups. In this tutorial, you learn how to provision X.509 devices using a custom Hardware Security Module (HSM) and the C device SDK for Azure IoT Hub Device Provisioning Service (DPS).
author: kgremban
ms.author: kgremban
ms.date: 07/12/2022
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps 
ms.custom: mvc
#Customer intent: As a new IoT developer, I want provision groups of devices using X.509 certificate chains and the C SDK.
---

# Tutorial: Provision multiple X.509 devices using enrollment groups

In this tutorial, you'll learn how to provision groups of IoT devices that use X.509 certificates for authentication. Sample device code from the [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) will be executed on your development machine to simulate provisioning of X.509 devices. On real devices, device code would be deployed and run from the IoT device.

The Azure IoT Hub Device Provisioning Service supports two types of enrollments for provisioning devices:

* [Enrollment groups](concepts-service.md#enrollment-group): Used to enroll multiple related devices. This tutorial demonstrates provisioning with enrollment groups.
* [Individual Enrollments](concepts-service.md#individual-enrollment): Used to enroll a single device.

The Azure IoT Hub Device Provisioning Service supports three forms of authentication for provisioning devices:

* [X.509 certificates](concepts-x509-attestation.md). This tutorial demonstrates X.509 certificate attestation.
* [Trusted platform module (TPM)](concepts-tpm-attestation.md)
* [Symmetric keys](./concepts-symmetric-key-attestation.md)

This tutorial uses the [custom HSM sample](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client/samples/custom_hsm_example), which provides a stub implementation for interfacing with hardware-based secure storage. A [Hardware Security Module (HSM)](./concepts-service.md#hardware-security-module) is used for secure, hardware-based storage of device secrets. An HSM can be used with symmetric key, X.509 certificate, or TPM attestation to provide secure storage for secrets. Hardware-based storage of device secrets isn't required, but it is strongly recommended to help protect sensitive information like your device certificate's private key.

In this tutorial, you'll complete the following objectives:

> [!div class="checklist"]
>
> * Create a certificate chain of trust to organize a set of devices using X.509 certificates.
> * Complete proof of possession with a signing certificate used with the certificate chain.
> * Create a new group enrollment that uses the certificate chain.
> * Set up the development environment for provisioning a device using code from the [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).
> * Provision a device using the certificate chain with the custom Hardware Security Module (HSM) sample in the SDK.

## Prerequisites

* If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) before you begin.

* Complete the steps in [Set up IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md).

The following prerequisites are for a Windows development environment used to simulate the devices. For Linux or macOS, see the appropriate section in [Prepare your development environment](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) in the SDK documentation.

* Install [Visual Studio](https://visualstudio.microsoft.com/vs/) 2022 with the ['Desktop development with C++'](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) workload enabled. Visual Studio 2015, Visual Studio 2017, and Visual Studio 19 are also supported.

* Install the latest [CMake build system](https://cmake.org/download/). Make sure you check the option that adds the CMake executable to your path.

    >[!IMPORTANT]
    >Confirm that the Visual Studio prerequisites (Visual Studio and the 'Desktop development with C++' workload) are installed on your machine, **before** starting the `CMake` installation. Once the prerequisites are in place, and the download is verified, install the CMake build system. Also, be aware that older versions of the CMake build system fail to generate the solution file used in this article. Make sure to use the latest version of CMake.

* Install the latest version of [Git](https://git-scm.com/download/). Make sure that Git is added to the environment variables accessible to the command window. See [Software Freedom Conservancy's Git client tools](https://git-scm.com/download/) for the latest version of `git` tools to install, which includes *Git Bash*, the command-line app that you can use to interact with your local Git repository.

* Make sure [OpenSSL](https://www.openssl.org/) is installed on your machine. On Windows, your installation of Git includes an installation of OpenSSL. You can access OpenSSL from the Git Bash prompt. To verify that OpenSSL is installed, open a Git Bash prompt and enter `openssl version`.

  >[!NOTE]
  > Unless you're familiar with OpenSSL and already have it installed on your Windows machine, we recommend using OpenSSL from the Git Bash prompt. Alternatively, you can choose to download the source code and build OpenSSL. To learn more, see the [OpenSSL Downloads](https://www.openssl.org/source/) page. Or, you can download OpenSSL pre-built from a third-party. To learn more, see the [OpenSSL wiki](https://wiki.openssl.org/index.php/Binaries). Microsoft makes no guarantees about the validity of packages downloaded from third-parties. If you do choose to build or download OpenSSL make sure that the OpenSSL binary is accessible in your path and that the `OPENSSL_CNF` environment variable is set to the path of your *openssl.cnf* file.

* Open both a Windows command prompt and a Git Bash prompt.

    The steps in this tutorial assume that you're using a Windows machine and the OpenSSL installation that is installed as part of Git. You'll use the Git Bash prompt to issue OpenSSL commands and the Windows command prompt for everything else. If you're using Linux, you can issue all commands from a Bash shell.

## Prepare the Azure IoT C SDK development environment

In this section, you'll prepare a development environment used to build the [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). The SDK includes sample code and tools used by devices provisioning with DPS.

1. Open a web browser, and go to the [Release page of the Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c/releases/latest).

2. Select the **Tags** tab at the top of the page.

3. Copy the tag name for the latest release of the Azure IoT C SDK.

4. In your Windows command prompt, run the following commands to clone the latest release of the [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub repository. Replace `<release-tag>` with the tag you copied in the previous step.

    ```cmd
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    This operation could take several minutes to complete.

5. When the operation is complete, run the following commands from the `azure-iot-sdk-c` directory:

    ```cmd
    mkdir cmake
    cd cmake
    ```

6. The code sample uses an X.509 certificate to provide attestation via X.509 authentication. Run the following command to build a version of the SDK specific to your development platform that includes the device provisioning client. A Visual Studio solution for the simulated device is generated in the `cmake` directory.

    When specifying the path used with `-Dhsm_custom_lib` in the command below, make sure to use the absolute path to the library in the `cmake` directory you previously created. The path shown below assumes that you cloned the C SDK in the root directory of the C drive. If you used another directory, adjust the path accordingly.

    ```cmd
    cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=c:/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    ```

    >[!TIP]
    >If `cmake` doesn't find your C++ compiler, you may get build errors while running the above command. If that happens, try running the command in the [Visual Studio command prompt](/dotnet/framework/tools/developer-command-prompt-for-vs).

7. When the build succeeds, the last few output lines look similar to the following output:

    ```output
    cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=c:/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    -- Building for: Visual Studio 17 2022
    -- Selecting Windows SDK version 10.0.19041.0 to target Windows 10.0.22000.
    -- The C compiler identification is MSVC 19.32.31329.0
    -- The CXX compiler identification is MSVC 19.32.31329.0
    
    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: C:/azure-iot-sdk-c/cmake
    ```

## Create an X.509 certificate chain

In this section, you'll generate an X.509 certificate chain of three certificates for testing each device with this tutorial. The certificates have the following hierarchy.

:::image type="content" source="./media/tutorial-custom-hsm-enrollment-group-x509/example-device-cert-chain.png" alt-text="Diagram that shows relationship of root C A, intermediate C A, and device certificates." border="false":::

[Root certificate](concepts-x509-attestation.md#root-certificate): You'll complete [proof of possession](how-to-verify-certificates.md) to verify the root certificate. This verification enables DPS to trust that certificate and verify certificates signed by it.

[Intermediate certificate](concepts-x509-attestation.md#intermediate-certificate): It's common to use intermediate certificates to group devices logically by product lines, company divisions, or other criteria. This tutorial uses a certificate chain with one intermediate certificate, but in a production scenario you may have several. The intermediate certificate in this chain is signed by the root certificate. This certificate is provided to the enrollment group created in DPS to logically group a set of devices. This configuration allows managing a whole group of devices that have device certificates signed by the same intermediate certificate.

[Device certificates](concepts-x509-attestation.md#end-entity-leaf-certificate): The device certificates (sometimes called leaf certificates) will be signed by the intermediate certificate and stored on the device along with its private key. Ideally these sensitive items would be stored securely with an HSM. Each device presents its certificate and private key, along with the certificate chain, when attempting provisioning.

### Set up the X.509 OpenSSL environment

In this section, you'll create the Openssl configuration files, directory structure, and other files used by the Openssl commands.

1. In your Git Bash command prompt, navigate to a folder where you want to generate the X.509 certificates and keys for this tutorial.

1. Create an OpenSSL configuration file for your root CA certificate. OpenSSL configuration files contain policies and definitions that are consumed by OpenSSL commands. Copy and paste the following text into a file named *openssl_root_ca.cnf*:

    ```text
    # OpenSSL root CA configuration file.

    [ ca ]
    default_ca = CA_default

    [ CA_default ]
    # Directory and file locations.
    dir               = .
    certs             = $dir/certs
    crl_dir           = $dir/crl
    new_certs_dir     = $dir/newcerts
    database          = $dir/index.txt
    serial            = $dir/serial
    RANDFILE          = $dir/private/.rand

    # The root key and root certificate.
    private_key       = $dir/private/azure-iot-test-only.root.ca.key.pem
    certificate       = $dir/certs/azure-iot-test-only.root.ca.cert.pem

    # For certificate revocation lists.
    crlnumber         = $dir/crlnumber
    crl               = $dir/crl/azure-iot-test-only.intermediate.crl.pem
    crl_extensions    = crl_ext
    default_crl_days  = 30

    # SHA-1 is deprecated, so use SHA-2 instead.
    default_md        = sha256

    name_opt          = ca_default
    cert_opt          = ca_default
    default_days      = 375
    preserve          = no
    policy            = policy_loose

    [ policy_strict ]
    # The root CA should only sign intermediate certificates that match.
    countryName             = optional
    stateOrProvinceName     = optional
    organizationName        = optional
    organizationalUnitName  = optional
    commonName              = supplied
    emailAddress            = optional

    [ policy_loose ]
    # Allow the intermediate CA to sign a more diverse range of certificates.
    countryName             = optional
    stateOrProvinceName     = optional
    localityName            = optional
    organizationName        = optional
    organizationalUnitName  = optional
    commonName              = supplied
    emailAddress            = optional

    [ req ]
    default_bits        = 2048
    distinguished_name  = req_distinguished_name
    string_mask         = utf8only

    # SHA-1 is deprecated, so use SHA-2 instead.
    default_md          = sha256

    # Extension to add when the -x509 option is used.
    x509_extensions     = v3_ca

    [ req_distinguished_name ]
    # See <https://en.wikipedia.org/wiki/Certificate_signing_request>.
    countryName                     = Country Name (2 letter code)
    stateOrProvinceName             = State or Province Name
    localityName                    = Locality Name
    0.organizationName              = Organization Name
    organizationalUnitName          = Organizational Unit Name
    commonName                      = Common Name
    emailAddress                    = Email Address

    # Optionally, specify some defaults.
    countryName_default             = US
    stateOrProvinceName_default     = WA
    localityName_default            =
    0.organizationName_default      = My Organization
    organizationalUnitName_default  =
    emailAddress_default            =

    [ v3_ca ]
    # Extensions for a typical CA.
    subjectKeyIdentifier = hash
    authorityKeyIdentifier = keyid:always,issuer
    basicConstraints = critical, CA:true
    keyUsage = critical, digitalSignature, cRLSign, keyCertSign

    [ v3_intermediate_ca ]
    # Extensions for a typical intermediate CA.
    subjectKeyIdentifier = hash
    authorityKeyIdentifier = keyid:always,issuer
    basicConstraints = critical, CA:true
    keyUsage = critical, digitalSignature, cRLSign, keyCertSign

    [ usr_cert ]
    # Extensions for client certificates.
    basicConstraints = CA:FALSE
    nsComment = "OpenSSL Generated Client Certificate"
    subjectKeyIdentifier = hash
    authorityKeyIdentifier = keyid,issuer
    keyUsage = critical, nonRepudiation, digitalSignature, keyEncipherment
    extendedKeyUsage = clientAuth

    [ server_cert ]
    # Extensions for server certificates.
    basicConstraints = CA:FALSE
    nsComment = "OpenSSL Generated Server Certificate"
    subjectKeyIdentifier = hash
    authorityKeyIdentifier = keyid,issuer:always
    keyUsage = critical, digitalSignature, keyEncipherment
    extendedKeyUsage = serverAuth

    [ crl_ext ]
    # Extension for CRLs.
    authorityKeyIdentifier=keyid:always

    [ ocsp ]
    # Extension for OCSP signing certificates.
    basicConstraints = CA:FALSE
    subjectKeyIdentifier = hash
    authorityKeyIdentifier = keyid,issuer
    keyUsage = critical, digitalSignature
    extendedKeyUsage = critical, OCSPSigning
    ```

1. Create an OpenSSL configuration file to use for intermediate and device certificates. Copy and paste the following text into a file named *openssl_device_intermediate_ca.cnf*:

    ```text
    # OpenSSL root CA configuration file.

    [ ca ]
    default_ca = CA_default

    [ CA_default ]
    # Directory and file locations.
    dir               = .
    certs             = $dir/certs
    crl_dir           = $dir/crl
    new_certs_dir     = $dir/newcerts
    database          = $dir/index.txt
    serial            = $dir/serial
    RANDFILE          = $dir/private/.rand

    # The root key and root certificate.
    private_key       = $dir/private/azure-iot-test-only.intermediate.key.pem
    certificate       = $dir/certs/azure-iot-test-only.intermediate.cert.pem

    # For certificate revocation lists.
    crlnumber         = $dir/crlnumber
    crl               = $dir/crl/azure-iot-test-only.intermediate.crl.pem
    crl_extensions    = crl_ext
    default_crl_days  = 30

    # SHA-1 is deprecated, so use SHA-2 instead.
    default_md        = sha256

    name_opt          = ca_default
    cert_opt          = ca_default
    default_days      = 375
    preserve          = no
    policy            = policy_loose

    [ policy_strict ]
    # The root CA should only sign intermediate certificates that match.
    countryName             = optional
    stateOrProvinceName     = optional
    organizationName        = optional
    organizationalUnitName  = optional
    commonName              = supplied
    emailAddress            = optional

    [ policy_loose ]
    # Allow the intermediate CA to sign a more diverse range of certificates.
    countryName             = optional
    stateOrProvinceName     = optional
    localityName            = optional
    organizationName        = optional
    organizationalUnitName  = optional
    commonName              = supplied
    emailAddress            = optional

    [ req ]
    default_bits        = 2048
    distinguished_name  = req_distinguished_name
    string_mask         = utf8only

    # SHA-1 is deprecated, so use SHA-2 instead.
    default_md          = sha256

    # Extension to add when the -x509 option is used.
    x509_extensions     = v3_ca

    [ req_distinguished_name ]
    # See <https://en.wikipedia.org/wiki/Certificate_signing_request>.
    countryName                     = Country Name (2 letter code)
    stateOrProvinceName             = State or Province Name
    localityName                    = Locality Name
    0.organizationName              = Organization Name
    organizationalUnitName          = Organizational Unit Name
    commonName                      = Common Name
    emailAddress                    = Email Address

    # Optionally, specify some defaults.
    countryName_default             = US
    stateOrProvinceName_default     = WA
    localityName_default            =
    0.organizationName_default      = My Organization
    organizationalUnitName_default  =
    emailAddress_default            =

    [ v3_ca ]
    # Extensions for a typical CA.
    subjectKeyIdentifier = hash
    authorityKeyIdentifier = keyid:always,issuer
    basicConstraints = critical, CA:true
    keyUsage = critical, digitalSignature, cRLSign, keyCertSign

    [ v3_intermediate_ca ]
    # Extensions for a typical intermediate CA.
    subjectKeyIdentifier = hash
    authorityKeyIdentifier = keyid:always,issuer
    basicConstraints = critical, CA:true
    keyUsage = critical, digitalSignature, cRLSign, keyCertSign

    [ usr_cert ]
    # Extensions for client certificates.
    basicConstraints = CA:FALSE
    nsComment = "OpenSSL Generated Client Certificate"
    subjectKeyIdentifier = hash
    authorityKeyIdentifier = keyid,issuer
    keyUsage = critical, nonRepudiation, digitalSignature, keyEncipherment
    extendedKeyUsage = clientAuth

    [ server_cert ]
    # Extensions for server certificates.
    basicConstraints = CA:FALSE
    nsComment = "OpenSSL Generated Server Certificate"
    subjectKeyIdentifier = hash
    authorityKeyIdentifier = keyid,issuer:always
    keyUsage = critical, digitalSignature, keyEncipherment
    extendedKeyUsage = serverAuth

    [ crl_ext ]
    # Extension for CRLs.
    authorityKeyIdentifier=keyid:always

    [ ocsp ]
    # Extension for OCSP signing certificates.
    basicConstraints = CA:FALSE
    subjectKeyIdentifier = hash
    authorityKeyIdentifier = keyid,issuer
    keyUsage = critical, digitalSignature
    extendedKeyUsage = critical, OCSPSigning
    ```

1. Create the directory structure, the database file (index.txt), and the serial number file (serial) used by OpenSSL commands in this article:

    ```bash
    mkdir certs csr newcerts private
    touch index.txt
    openssl rand -hex 16 > serial
    ```

### Create the root CA certificate

Run the following commands to create the root CA private key and the root CA certificate. You'll use this certificate and key to sign your intermediate certificate.

1. Create the root CA private key:

    ```bash
    openssl genrsa -aes256 -passout pass:1234 -out ./private/azure-iot-test-only.root.ca.key.pem 4096
    ```

1. Create the root CA certificate:

    # [Windows](#tab/windows)

    ```bash
    openssl req -new -x509 -config ./openssl_root_ca.cnf -passin pass:1234 -key ./private/azure-iot-test-only.root.ca.key.pem -subj '//CN=Azure IoT Hub CA Cert Test Only' -days 30 -sha256 -extensions v3_ca -out ./certs/azure-iot-test-only.root.ca.cert.pem
    ```

    > [!IMPORTANT]
    > The extra forward slash given for the subject name (`//CN=Azure IoT Hub CA Cert Test Only`) is only required to escape the string with Git on Windows platforms.

    # [Linux](#tab/linux)

    ```bash
    openssl req -new -x509 -config ./openssl_root_ca.cnf -passin pass:1234 -key ./private/azure-iot-test-only.root.ca.key.pem -subj '/CN=Azure IoT Hub CA Cert Test Only' -days 30 -sha256 -extensions v3_ca -out ./certs/azure-iot-test-only.root.ca.cert.pem
    ```

    ---

1. Examine the root CA certificate:

    ```bash
    openssl x509 -noout -text -in ./certs/azure-iot-test-only.root.ca.cert.pem
    ```

    Observe that the **Issuer** and the **Subject** are both the root CA.

    ```output
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number:
                1d:93:13:0e:54:07:95:1d:8c:57:4f:12:14:b9:5e:5f:15:c3:a9:d4
            Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN = Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Jun 20 22:52:23 2022 GMT
                Not After : Jul 20 22:52:23 2022 GMT
            Subject: CN = Azure IoT Hub CA Cert Test Only
            Subject Public Key Info:
                Public Key Algorithm: rsaEncryption
                    RSA Public-Key: (4096 bit)
    ```

### Create the intermediate CA certificate

Run the following commands to create the intermediate CA private key and the intermediate CA certificate. You'll use this certificate and key to sign your device certificate(s).

1. Create the intermediate CA private key:

    ```bash
    openssl genrsa -aes256 -passout pass:1234 -out ./private/azure-iot-test-only.intermediate.key.pem 4096
    ```

1. Create the intermediate CA certificate signing request (CSR):

    # [Windows](#tab/windows)

    ```bash
    openssl req -new -sha256 -passin pass:1234 -config ./openssl_device_intermediate_ca.cnf -subj '//CN=Azure IoT Hub Intermediate Cert Test Only' -key ./private/azure-iot-test-only.intermediate.key.pem -out ./csr/azure-iot-test-only.intermediate.csr.pem
    ```

    > [!IMPORTANT]
    > The extra forward slash given for the subject name (`//CN=Azure IoT Hub Intermediate Cert Test Only`) is only required to escape the string with Git on Windows platforms.

    # [Linux](#tab/linux)

    ```bash
    openssl req -new -sha256 -passin pass:1234 -config ./openssl_device_intermediate_ca.cnf -subj '/CN=Azure IoT Hub Intermediate Cert Test Only' -key ./private/azure-iot-test-only.intermediate.key.pem -out ./csr/azure-iot-test-only.intermediate.csr.pem
    ```

    ---

1. Sign the intermediate certificate with the root CA certificate

    ```bash
    openssl ca -batch -config ./openssl_root_ca.cnf -passin pass:1234 -extensions v3_intermediate_ca -days 30 -notext -md sha256 -in ./csr/azure-iot-test-only.intermediate.csr.pem -out ./certs/azure-iot-test-only.intermediate.cert.pem
    ```

1. Examine the intermediate CA certificate:

    ```bash
    openssl x509 -noout -text -in ./certs/azure-iot-test-only.intermediate.cert.pem
    ```

    Observe that the **Issuer** is the root CA, and the **Subject** is the intermediate CA.

    ```output
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number:
                d9:55:87:57:41:c8:4c:47:6c:ee:ba:83:5d:ae:db:39
            Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN = Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Jun 20 22:54:01 2022 GMT
                Not After : Jul 20 22:54:01 2022 GMT
            Subject: CN = Azure IoT Hub Intermediate Cert Test Only
            Subject Public Key Info:
                Public Key Algorithm: rsaEncryption
                    RSA Public-Key: (4096 bit)
     ```

### Create the device certificates

In this section, you create two device certificates and their full chain certificates. The full chain certificate contains the device certificate, the intermediate CA certificate, and the root CA certificate. The device must present its full chain certificate when it registers with DPS.

1. Create the first device private key.

    ```bash
    openssl genrsa -out ./private/device-01.key.pem 4096
    ```

1. Create the device certificate CSR.

    The subject common name (CN) of the device certificate must be set to the [registration ID](./concepts-service.md#registration-id) that your device will use to register with DPS. The registration ID is a case-insensitive string of alphanumeric characters plus the special characters: `'-'`, `'.'`, `'_'`, `':'`. The last character must be alphanumeric or dash (`'-'`). The common name must adhere to this format. DPS supports registration IDs up to 128 characters long; however, the maximum length of the subject common name in an X.509 certificate is 64 characters. The registration ID, therefore, is limited to 64 characters when using X.509 certificates. For group enrollments, the registration ID is also used as the device ID in IoT Hub.

    The subject common name is set using the `-subj` parameter. In the following command, the common name is set to **device-01**.

    # [Windows](#tab/windows)

    ```bash
    openssl req -config ./openssl_device_intermediate_ca.cnf -key ./private/device-01.key.pem -subj '//CN=device-01' -new -sha256 -out ./csr/device-01.csr.pem
    ```

    > [!IMPORTANT]
    > The extra forward slash given for the subject name (`//CN=device-01`) is only required to escape the string with Git on Windows platforms.

    # [Linux](#tab/linux)

    ```bash
    openssl req -config ./openssl_device_intermediate_ca.cnf -key ./private/device-01.key.pem -subj '/CN=device-01' -new -sha256 -out ./csr/device-01.csr.pem
    ```

    ---

1. Sign the device certificate.

    ```bash
    openssl ca -batch -config ./openssl_device_intermediate_ca.cnf -passin pass:1234 -extensions usr_cert -days 30 -notext -md sha256 -in ./csr/device-01.csr.pem -out ./certs/device-01.cert.pem
    ```

1. Examine the device certificate:

    ```bash
    openssl x509 -noout -text -in ./certs/device-01.cert.pem
    ```

    Observe that the **Issuer** is the intermediate CA, and the **Subject** is the device registration ID, `device-01`.

    ```output
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number:
                d9:55:87:57:41:c8:4c:47:6c:ee:ba:83:5d:ae:db:3a
            Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN = Azure IoT Hub Intermediate Cert Test Only
            Validity
                Not Before: Jun 20 22:55:39 2022 GMT
                Not After : Jul 20 22:55:39 2022 GMT
            Subject: CN = device-01
            Subject Public Key Info:
                Public Key Algorithm: rsaEncryption
                    RSA Public-Key: (4096 bit)
     ```

1. The device must present the full certificate chain when it authenticates with DPS. Use the following command to create the certificate chain:

    ```bash
    cat ./certs/device-01.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/device-01-full-chain.cert.pem
    ```  

1. Open the certificate chain file, *./certs/device-01-full-chain.cert.pem*, in a text editor to examine it. The certificate chain text contains the full chain of all three certificates. You'll use this text as the certificate chain with in the custom HSM device code later in this tutorial for `device-01`.

    The full chain text has the following format:

    ```output
    -----BEGIN CERTIFICATE-----
        <Text for the device certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the intermediate certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the root certificate includes public key>
    -----END CERTIFICATE-----
    ```

1. To create the private key, X.509 certificate, and full chain certificate for the second device, copy and paste this script into your GitBash command prompt. To create certificates for more devices, you can modify the `registration_id` variable declared at the beginning of the script.

    ```bash
    registration_id=device-02
    echo $registration_id
    openssl genrsa -out ./private/${registration_id}.key.pem 4096
    openssl req -config ./openssl_device_intermediate_ca.cnf -key ./private/${registration_id}.key.pem -subj "//CN=$registration_id" -new -sha256 -out ./csr/${registration_id}.csr.pem
    openssl ca -batch -config ./openssl_device_intermediate_ca.cnf -passin pass:1234 -extensions usr_cert -days 30 -notext -md sha256 -in ./csr/${registration_id}.csr.pem -out ./certs/${registration_id}.cert.pem
    cat ./certs/${registration_id}.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/${registration_id}-full-chain.cert.pem
    ```

    >[!NOTE]
    > This script uses the registration ID as the base filename for the private key and certificate files. If your registration ID contains characters that aren't valid filename characters, you'll need to modify the script accordingly.

    > [!WARNING]
    > The text for the certificates only contains public key information.
    >
    > However, the device must also have access to the private key for the device certificate. This is necessary because the device must perform verification using that key at runtime when it attempts to provision. The sensitivity of this key is one of the main reasons it is recommended to use hardware-based storage in a real HSM to help secure private keys.

You'll use the following files in the rest of this tutorial:

|   Certificate                 |  File  | Description |
| ---------------------------- | --------- | ---------- |
| root CA certificate.              | *certs/azure-iot-test-only.root.ca.cert.pem* | Will be uploaded to DPS and verified. |
| intermediate CA certificate   | *certs/azure-iot-test-only.intermediate.cert.pem* | Will be used to create an enrollment group in DPS. |
| device-01 private key          | *private/device-01.key.pem* | Used by the device to verify ownership of the device certificate during authentication with DPS. |
| device-01 full chain certificate  | *certs/device-01-full-chain.cert.pem* | Presented by the device to authenticate and register with DPS. |
| device-02 private key          | *private/device-02.key.pem* | Used by the device to verify ownership of the device certificate during authentication with DPS. |
| device-02 full chain certificate  | *certs/device-01-full-chain.cert.pem* | Presented by the device to authenticate and register with DPS. |

## Verify ownership of the root certificate

For DPS to be able to validate the device's certificate chain during authentication, you must upload and verify ownership of the root CA certificate. Because you created the root CA certificate in the last section, you'll auto-verify that it's valid when you upload it. Alternatively, you can do manual verification of the certificate if you're using a CA certificate from a 3rd-party. To learn more about verifying CA certificates, see [How to do proof-of-possession for X.509 CA certificates](how-to-verify-certificates.md).

To add the root CA certificate to your DPS instance, follow these steps:

1. Sign in to the [Azure portal](https://portal.azure.com), select the **All resources** button on the left-hand menu and open your Device Provisioning Service instance.

1. Open **Certificates** from the left-hand menu and then select **+ Add** at the top of the panel to add a new certificate.

1. Enter a friendly display name for your certificate. Browse to the location of the root CA certificate file `certs/azure-iot-test-only.root.ca.cert.pem`. Select **Upload**.

1. Select the box next to **Set certificate status to verified on upload**.

    :::image type="content" source="./media/tutorial-custom-hsm-enrollment-group-x509/add-root-certificate.png" alt-text="Screenshot that shows adding the root C A certificate and the set certificate status to verified on upload box selected.":::

1. Select **Save**.

1. Make sure your certificate is shown in the certificate tab with a status of *Verified*.
  
    :::image type="content" source="./media/tutorial-custom-hsm-enrollment-group-x509/verify-root-certificate.png" alt-text="Screenshot that shows the verified root C A certificate in the list of certificates.":::

## Update the certificate store on Windows-based devices

On non-Windows devices, you can pass the certificate chain from the code as the certificate store.

On Windows-based devices, you must add the signing certificates (root and intermediate) to a Windows [certificate store](/windows/win32/secauthn/certificate-stores). Otherwise, the signing certificates won't be transported to DPS by a secure channel with Transport Layer Security (TLS).

> [!TIP]
> It's also possible to use OpenSSL instead of secure channel (Schannel) with the C SDK. For more information on using OpenSSL, see [Using OpenSSL in the SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#using-openssl-in-the-sdk).

To add the signing certificates to the certificate store in Windows-based devices:

1. In a Git bash prompt, convert your signing certificates to `.pfx` as follows.

    Root CA certificate:

    ```bash
    openssl pkcs12 -inkey ./private/azure-iot-test-only.root.ca.key.pem -in ./certs/azure-iot-test-only.root.ca.cert.pem -export -passin pass:1234 -passout pass:1234 -out ./certs/root.pfx
    ```

    Intermediate CA certificate:

    ```bash
    openssl pkcs12 -inkey ./private/azure-iot-test-only.intermediate.key.pem -in ./certs/azure-iot-test-only.intermediate.cert.pem -export -passin pass:1234 -passout pass:1234 -out ./certs/intermediate.pfx
    ```

2. Right-click the Windows **Start** button. Then select **Run**. Enter *certmgr.msc* and select **Ok** to start certificate manager MMC snap-in.

3. In certificate manager, under **Certificates - Current User**, select **Trusted Root Certification Authorities**. Then on the menu, select **Action** > **All Tasks** > **Import** to import `root.pfx`.

    * Make sure to search by **Personal information Exchange (.pfx)**
    * Use `1234` as the password.
    * Place the certificate in the **Trusted Root Certification Authorities** certificate store.

4. In certificate manager, under **Certificates - Current User**, select **Intermediate Certification Authorities**. Then on the menu, select **Action** > **All Tasks** > **Import** to import `intermediate.pfx`.

    * Make sure to search by **Personal information Exchange (.pfx)**
    * Use `1234` as the password.
    * Place the certificate in the **Intermediate Certification Authorities** certificate store.

Your signing certificates are now trusted on the Windows-based device and the full chain can be transported to DPS.

## Create an enrollment group

1. From your DPS instance in Azure portal, select the **Manage enrollments** tab. then select the **Add enrollment group** button at the top.

1. In the **Add Enrollment Group** panel, enter the following information, then select **Save**.

    | Field        | Value           |
    | :----------- | :-------------- |
    | **Group name** | For this tutorial, enter **custom-hsm-x509-devices**. The enrollment group name is a case-insensitive string (up to 128 characters long) of alphanumeric characters plus the special characters: `'-'`, `'.'`, `'_'`, `':'`. The last character must be alphanumeric or dash (`'-'`). |
    | **Attestation Type** | Select **Certificate** |
    | **IoT Edge device** | Select **False** |
    | **Certificate Type** | Select **Intermediate Certificate** |
    | **Primary certificate .pem or .cer file** | Navigate to the intermediate certificate that you created earlier (*./certs/azure-iot-test-only.intermediate.cert.pem*). This intermediate certificate is signed by the root certificate that you already uploaded and verified. DPS trusts that root once it's verified. DPS can verify that the intermediate provided with this enrollment group is truly signed by the trusted root. DPS will trust each intermediate truly signed by that root certificate, and therefore be able to verify and trust leaf certificates signed by the intermediate.  |

    :::image type="content" source="./media/tutorial-custom-hsm-enrollment-group-x509/custom-hsm-enrollment-group-x509.png" alt-text="Screenshot that shows adding an enrollment group in the portal.":::

## Configure the provisioning device code

In this section, you update the sample code with your Device Provisioning Service instance information. If a device is authenticated, it will be assigned to an IoT hub linked to the Device Provisioning Service instance configured in this section.

1. In the Azure portal, select the **Overview** tab for your Device Provisioning Service instance and note the **ID Scope** value.

    :::image type="content" source="./media/tutorial-custom-hsm-enrollment-group-x509/copy-id-scope.png" alt-text="Screenshot that shows the ID scope on the DPS overview pane.":::

2. Launch Visual Studio and open the new solution file that was created in the `cmake` directory you created in the root of the azure-iot-sdk-c git repository. The solution file is named `azure_iot_sdks.sln`.

3. In Solution Explorer for Visual Studio, navigate to **Provision_Samples > prov_dev_client_sample > Source Files** and open *prov_dev_client_sample.c*.

4. Find the `id_scope` constant, and replace the value with your **ID Scope** value that you copied earlier. For example:

    ```c
    static const char* id_scope = "0ne00000A0A";
    ```

5. Find the definition for the `main()` function in the same file. Make sure the `hsm_type` variable is set to `SECURE_DEVICE_TYPE_X509` and that all other `hsm_type` lines are commented out. For example:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    //hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Save your changes.

7. Right-click the **prov\_dev\_client\_sample** project and select **Set as Startup Project**.

## Configure the custom HSM stub code

The specifics of interacting with actual secure hardware-based storage vary depending on the device hardware. The certificate chains used by the simulated devices in this tutorial will be hardcoded in the custom HSM stub code. In a real-world scenario, the certificate chain would be stored in the actual HSM hardware to provide better security for sensitive information. Methods similar to the stub methods used in this sample would then be implemented to read the secrets from that hardware-based storage.

While HSM hardware isn't required, it is recommended to protect sensitive information like the certificate's private key. If an actual HSM was being called by the sample, the private key wouldn't be present in the source code. Having the key in the source code exposes the key to anyone that can view the code. This is only done in this article to assist with learning.

To update the custom HSM stub code to simulate the identity of the device with ID `device-01`, perform the following steps:

1. In Solution Explorer for Visual Studio, navigate to **Provision_Samples > custom_hsm_example > Source Files** and open *custom_hsm_example.c*.

2. Update the string value of the `COMMON_NAME` string constant using the common name you used when generating the device certificate.

    ```c
    static const char* const COMMON_NAME = "device-01";
    ```

3. Update the string value of the `CERTIFICATE` constant string using the certificate chain you saved in *./certs/device-01-full-chain.cert.pem* after generating your certificates.

    The syntax of certificate text must follow the pattern below with no extra spaces or parsing done by Visual Studio.

    ```c
    // <Device/leaf cert>
    // <intermediates>
    // <root>
    static const char* const CERTIFICATE = "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV\n"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB\n"
    "-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFPDCCAySgAwIBAgIBATANBgkqhkiG9w0BAQsFADAqMSgwJgYDVQQDDB9BenVy\n"
        ...
    "MTEyMjIxMzAzM1owNDEyMDAGA1UEAwwpQXp1cmUgSW9UIEh1YiBJbnRlcm1lZGlh\n"
    "-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV\n"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB\n"
    "-----END CERTIFICATE-----";        
    ```

    Updating this string value manually can be prone to error. To generate the proper syntax, you can copy and paste the following command into your **Git Bash prompt**, and press **ENTER**. This command generates the syntax for the `CERTIFICATE` string constant value and writes it to the output.

    ```Bash
    sed -e 's/^/"/;$ !s/$/""\\n"/;$ s/$/"/' ./certs/device-01-full-chain.cert.pem
    ```

    Copy and paste the output certificate text for the constant value.

4. Update the string value of the `PRIVATE_KEY` constant with the private key for your device certificate.

    The syntax of the private key text must follow the pattern below with no extra spaces or parsing done by Visual Studio.

    ```c
    static const char* const PRIVATE_KEY = "-----BEGIN RSA PRIVATE KEY-----\n"
    "MIIJJwIBAAKCAgEAtjvKQjIhp0EE1PoADL1rfF/W6v4vlAzOSifKSQsaPeebqg8U\n"
        ...
    "X7fi9OZ26QpnkS5QjjPTYI/wwn0J9YAwNfKSlNeXTJDfJ+KpjXBcvaLxeBQbQhij\n"
    "-----END RSA PRIVATE KEY-----";
    ```

    Updating this string value manually can be prone to error. To generate the proper syntax, you can copy and paste the following command into your **Git Bash prompt**, and press **ENTER**. This command generates the syntax for the `PRIVATE_KEY` string constant value and writes it to the output.

    ```Bash
    sed -e 's/^/"/;$ !s/$/""\\n"/;$ s/$/"/' ./private/device-01.key.pem
    ```

    Copy and paste the output private key text for the constant value.

5. Save your changes.

6. Right-click the **custom_hsm_example** project and select **Build**.

    > [!IMPORTANT]
    > You must build the **custom_hsm_example** project before you build the rest of the solution in the next section.

### Run the sample

1. On the Visual Studio menu, select **Debug** > **Start without debugging** to run the solution. When prompted to rebuild the project, select **Yes** to rebuild the project before running.

    The following output is an example of simulated device `device-01` successfully booting up and connecting to the provisioning service. The device was assigned to an IoT hub and registered:

    ```output
    Provisioning API Version: 1.8.0

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-hub-2.azure-devices.net, deviceId: device-01
    Press enter key to exit:
    ```

1. Repeat the steps in [Configure the custom HSM stub code](#configure-the-custom-hsm-stub-code) for your second device (`device-02`) and run the sample again. Use the following values for that device:

    |   Description                 |  Value  |
    | :---------------------------- | :--------- |
    | Common name                | `"device-02"` |
    | Full certificate chain        | Generate the text using *./certs/device-02-full-chain.cert.pem* |
    | Private key                   | Generate the text using  *./private/device-02.key.pem* |

    The following output is an example of simulated device `device-02` successfully booting up, and connecting to the provisioning service. The device was assigned to an IoT hub and registered:

    ```output
    Provisioning API Version: 1.8.0

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-hub-2.azure-devices.net, deviceId: device-02
    Press enter key to exit:
    ```

## Confirm your device provisioning registration

Examine the registration records of the enrollment group to see the registration details for your devices:

1. In the Azure portal, go to your Device Provisioning Service instance.

1. In the **Settings** menu, select **Manage enrollments**.

1. Select **Enrollment Groups**. The X.509 enrollment group entry that you created previously, *custom-hsm-devices*, should appear in the list.

1. Select the enrollment entry. Then select the **Registration Records** tab to see the devices that have been registered through the enrollment group. The IoT hub that each of your devices was assigned to, their device IDs, and the dates and times they were registered appear in the list.

    :::image type="content" source="./media/tutorial-custom-hsm-enrollment-group-x509/enrollment-group-registration-records.png" alt-text="Screenshot that shows the registration records tab for the enrollment group on Azure portal.":::

1. You can select one of the devices to see further details for that device.

To verify the devices on your IoT hub:

1. In Azure portal, go to the IoT hub that your device was assigned to.

1. In the **Device management** menu, select **Devices**.

1. If your devices were provisioned successfully, their device IDs, *device-01* and *device-02*, should appear in the list, with **Status** set as *enabled*. If you don't see your devices, select **Refresh**.

    :::image type="content" source="./media/tutorial-custom-hsm-enrollment-group-x509/hub-provisioned-custom-hsm-x509-device.png" alt-text="Screenshot that shows the devices are registered with the I o T hub in Azure portal.":::

## Clean up resources

When you're finished testing and exploring this device client sample, use the following steps to delete all resources created by this tutorial.

1. Close the device client sample output window on your machine.

1. From the left-hand menu in the Azure portal, select **All resources** and then select your Device Provisioning Service instance. Open **Manage Enrollments** for your service, and then select the **Enrollment Groups** tab. Select the check box next to the *Group Name* of the device group you created in this tutorial, and select **Delete** at the top of the pane.

1. Select **Certificates** in DPS. For each certificate you uploaded and verified in this tutorial, select the certificate and select **Delete** to remove it.

1. From the left-hand menu in the Azure portal, select **All resources** and then select your IoT hub. Open **IoT devices** for your hub. Select the check box next to the *DEVICE ID* of the device that you registered in this tutorial. Select **Delete** at the top of the pane.

## Next steps

In this tutorial, you provisioned an X.509 device using a custom HSM to your IoT hub. To learn how to provision IoT devices to multiple hubs continue to the next tutorial.

> [!div class="nextstepaction"]
> [Tutorial: Provision devices across load-balanced IoT hubs](tutorial-provision-multiple-hubs.md)
