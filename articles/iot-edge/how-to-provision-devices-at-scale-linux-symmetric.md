---
title: Create and provision IoT Edge devices using symmetric keys on Linux - Azure IoT Edge | Microsoft Docs
description: Use symmetric key attestation to test provisioning Linux devices at scale for Azure IoT Edge with device provisioning service
author: PatAltimore
ms.author: patricka
ms.date: 05/12/2022
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
---
# Create and provision IoT Edge devices at scale on Linux using symmetric key

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

This article provides end-to-end instructions for autoprovisioning one or more Linux IoT Edge devices using symmetric keys. You can automatically provision Azure IoT Edge devices with the [Azure IoT Hub device provisioning service](../iot-dps/index.yml) (DPS). If you're unfamiliar with the process of autoprovisioning, review the [provisioning overview](../iot-dps/about-iot-dps.md#provisioning-process) before continuing.

The tasks are as follows:

1. Create either an **individual enrollment** for a single device or a **group enrollment** for a set of devices.
1. Install the IoT Edge runtime and connect to the IoT Hub.

:::moniker range=">=iotedge-2020-11"
>[!TIP]
>For a simplified experience, try the [Azure IoT Edge configuration tool](https://github.com/azure/iot-edge-config). This command-line tool, currently in public preview, installs IoT Edge on your device and provisions it using DPS and symmetric key attestation.
:::moniker-end

Symmetric key attestation is a simple approach to authenticating a device with a device provisioning service instance. This attestation method represents a "Hello world" experience for developers who are new to device provisioning, or do not have strict security requirements. Device attestation using a [TPM](../iot-dps/concepts-tpm-attestation.md) or [X.509 certificates](../iot-dps/concepts-x509-attestation.md) is more secure, and should be used for more stringent security requirements.

## Prerequisites

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### Device requirements

A physical or virtual Linux device to be the IoT Edge device.

You will need to define a *unique* **registration ID** to identify each device. You can use the MAC address, serial number, or any unique information from the device. For example, you could use a combination of a MAC address and serial number forming the following string for a registration ID: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`. Valid characters are lowercase alphanumeric and dash (`-`).

<!-- Create a DPS enrollment using symmetric keys H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-symmetric.md](../../includes/iot-edge-create-dps-enrollment-symmetric.md)]

<!-- Install IoT Edge on Linux H2 and content -->
[!INCLUDE [install-iot-edge-linux.md](../../includes/iot-edge-install-linux.md)]

## Provision the device with its cloud identity

Once the runtime is installed on your device, configure the device with the information it uses to connect to the device provisioning service and IoT Hub.

Have the following information ready:

* The DPS **ID Scope** value
* The device **Registration ID** you created
* Either the **Primary Key** from an individual enrollment, or a [derived key](#derive-a-device-key) for devices using a group enrollment.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Open the configuration file on the IoT Edge device.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Find the provisioning configurations section of the file. Uncomment the lines for DPS symmetric key provisioning, and make sure any other provisioning lines are commented out.

   The `provisioning:` line should have no preceding whitespace, and nested items should be indented by two spaces.

    ```yml
    # DPS TPM provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "PASTE_YOUR_SCOPE_ID_HERE"
      attestation:
        method: "symmetric_key"
        registration_id: "PASTE_YOUR_REGISTRATION_ID_HERE"
        symmetric_key: "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"

   # always_reprovision_on_startup: true
   # dynamic_reprovisioning: true
   ```

1. Update the values of `scope_id`, `registration_id`, and `symmetric_key` with your DPS and device information.

1. Optionally, use the `always_reprovision_on_startup` or `dynamic_reprovisioning` lines to configure your device's reprovisioning behavior. If a device is set to reprovision on startup, it will always attempt to provision with DPS first and then fall back to the provisioning backup if that fails. If a device is set to dynamically reprovision itself, IoT Edge (and all modules) will restart and reprovision if a reprovisioning event is detected, like if the device is moved from one IoT Hub to another. Specifically, IoT Edge checks for `bad_credential` or `device_disabled` errors from the SDK to detect the reprovision event. To trigger this event manually, disable the device in IoT Hub. For more information, see [IoT Hub device reprovisioning concepts](../iot-dps/concepts-device-reprovision.md).

1. Restart the IoT Edge runtime so that it picks up all the configuration changes that you made on the device.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- iotedge-2020-11 -->
:::moniker range=">=iotedge-2020-11"

1. Create a configuration file for your device based on a template file that is provided as part of the IoT Edge installation.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Open the configuration file on the IoT Edge device.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Find the **Provisioning** section of the file. Uncomment the lines for DPS provisioning with symmetric key, and make sure any other provisioning lines are commented out.

    ```toml
    # DPS provisioning with symmetric key
    [provisioning]
    source = "dps"
    global_endpoint = "https://global.azure-devices-provisioning.net"
    id_scope = "PASTE_YOUR_SCOPE_ID_HERE"
    
    [provisioning.attestation]
    method = "symmetric_key"
    registration_id = "PASTE_YOUR_REGISTRATION_ID_HERE"
    
    symmetric_key = { value = "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE" }
    
    # auto_reprovisioning_mode = Dynamic
    ```

1. Update the values of `id_scope`, `registration_id`, and `symmetric_key` with your DPS and device information.

   The symmetric key parameter can accept a value of an inline key, a file URI, or a PKCS#11 URI. Uncomment just one symmetric key line, based on which format you're using.

   If you use any PKCS#11 URIs, find the **PKCS#11** section in the config file and provide information about your PKCS#11 configuration.

1. Optionally, find the auto reprovisioning mode section of the file. Use the `auto_reprovisioning_mode` parameter to configure your device's reprovisioning behavior. **Dynamic** - Reprovision when the device detects that it may have been moved from one IoT Hub to another. This is the default. **AlwaysOnStartup** - Reprovision when the device is rebooted or a crash causes the daemon(s) to restart. **OnErrorOnly** - Never trigger device reprovisioning automatically. Each mode has an implicit device reprovisioning fallback if the device is unable to connect to IoT Hub during identity provisioning due to connectivity errors. For more information, see [IoT Hub device reprovisioning concepts](../iot-dps/concepts-device-reprovision.md).

1. Save and close the config.toml file.

1. Apply the configuration changes that you made to IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end iotedge-2020-11 -->

## Verify successful installation

If the runtime started successfully, you can go into your IoT Hub and start deploying IoT Edge modules to your device.

# [Individual enrollment](#tab/individual-enrollment)

You can verify that the individual enrollment that you created in device provisioning service was used. Navigate to your device provisioning service instance in the Azure portal. Open the enrollment details for the individual enrollment that you created. Notice that the status of the enrollment is **assigned** and the device ID is listed.

# [Group enrollment](#tab/group-enrollment)

You can verify that the group enrollment that you created in device provisioning service was used. Navigate to your device provisioning service instance in the Azure portal. Open the enrollment details for the group enrollment that you created. Go to the **Registration Records** tab to view all devices registered in that group.

---

Use the following commands on your device to verify that the IoT Edge installed and started successfully.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Check the status of the IoT Edge service.

```cmd/sh
systemctl status iotedge
```

Examine service logs.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

List running modules.

```cmd/sh
iotedge list
```

:::moniker-end
<!-- end 1.1 -->

<!-- iotedge-2020-11 -->
:::moniker range=">=iotedge-2020-11"

Check the status of the IoT Edge service.

```cmd/sh
sudo iotedge system status
```

Examine service logs.

```cmd/sh
sudo iotedge system logs
```

List running modules.

```cmd/sh
sudo iotedge list
```

:::moniker-end

## Next steps

The device provisioning service enrollment process lets you set the device ID and device twin tags at the same time as you provision the new device. You can use those values to target individual devices or groups of devices using automatic device management. Learn how to [Deploy and monitor IoT Edge modules at scale using the Azure portal](how-to-deploy-at-scale.md) or [using Azure CLI](how-to-deploy-cli-at-scale.md).
