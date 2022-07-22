---
title: "Quickstart: Azure Cognitive Services Translator"
titleSuffix: Azure Cognitive Services
description: "Learn to translate text with the Translator service. Examples are provided in C#, Go, Java, JavaScript and Python."
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 07/11/2022
ms.author: lajanuar
ms.devlang: csharp, golang, java, javascript, python
---

<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD001 -->

# Quickstart: Azure Cognitive Services Translator

In this quickstart, you'll get started using the Translator service to [translate text](reference/v3-0-translate.md) with a programming language of your choice and the REST API.

> [!NOTE]
>
> * For this quickstart it is recommended that you use a Translator text single-service subscription.
> * With the single-service subscription you'll include one authorization header (**Ocp-Apim-Subscription-key**) with the REST API request. The value for Ocp-Apim-Subscription-key is your Azure secret key for your Translator Text subscription.
> * If you choose to use the multi-service Cognitive Services subscription, it requires two authentication headers (**Ocp-Api-Subscription-Key** and **Ocp-Apim-Subscription-Region**). The value for Ocp-Apim-Subscription-Region is the region associated with your subscription.
> * For more information on how to use the Ocp-Apim-Subscription-Region header, _see_ [Use the Text Translator APIs](translator-text-apis.md).

## Prerequisites

To get started, you'll need an active Azure subscription. If you don't have an Azure subscription, you can [create one for free](https://azure.microsoft.com/free/cognitive-services/)

* Once you have your Azure subscription, create a [Translator resource](https://portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) in the Azure portal.

* After your resource deploys, select **Go to resource** and retrieve your key and endpoint.

  * You need the key and endpoint from the resource to connect your application to the Translator service. You'll paste your key and endpoint into the code later in the quickstart. You can find these values on the Azure portal **Keys and Endpoint** page:

    :::image type="content" source="media/quickstarts/keys-and-endpoint-portal.png" alt-text="Screenshot: Azure portal keys and endpoint page.":::

* Use the free pricing tier (F0) to try the service and upgrade later to a paid tier for production.

## Headers

To call the Translator service via the [REST API](reference/rest-api-guide.md), you'll need to include the following headers with each request. Don't worry, we'll include the headers for you in the sample code for each programming language.

For more information on Translator authentication options, *see* the [Translator v3 reference](./reference/v3-0-reference.md#authentication) guide.

|Header|Value| Condition  |
|--- |:--- |:---|
|**Ocp-Apim-Subscription-Key** |Your Translator service key from the Azure portal.|<ul><li>***Required***</li></ul> |
|**Content-Type**|The content type of the payload. The accepted value is **application/json** or **charset=UTF-8**.|<ul><li>***Required***</li></ul>|
|**Content-Length**|The **length of the request** body.|<ul><li>***Optional***</li></ul> |
|**X-ClientTraceId**|A client-generated GUID to uniquely identify the request. You can omit this header if you include the trace ID in the query string using a query parameter named ClientTraceId.|<ul><li>***Optional***</li></ul>
|||

> [!IMPORTANT]
>
> Remember to remove the key from your code when you're done, and **never** post it publicly. For production, use secure methods to store and access your credentials. For more information, *see* Cognitive Services [security](../../cognitive-services/cognitive-services-security.md).

## Translate text

The core operation of the Translator service is translating text. In this quickstart, you'll build a request using a programming language of your choice that takes a single source (`from`) and provides two outputs (`to`). Then we'll review some parameters that can be used to adjust both the request and the response.

### [C#: Visual Studio](#tab/csharp)

### Set up

1. Make sure you have the current version of [Visual Studio IDE](https://visualstudio.microsoft.com/vs/).

   > [!TIP]
   >
   > If you're new to Visual Studio, try the [**Introduction to Visual Studio**](/learn/modules/go-get-started/) Microsoft Learn module.

1. Open Visual Studio.

1. On the Start page, choose **Create a new project**.

    :::image type="content" source="media/quickstarts/start-window.png" alt-text="Screenshot: Visual Studio start window.":::

1. On the **Create a new project page**, enter **console** in the search box. Choose the **Console Application** template, then choose **Next**.

    :::image type="content" source="media/quickstarts/create-new-project.png" alt-text="Screenshot: Visual Studio's create new project page.":::

1. In the **Configure your new project** dialog window, enter `translator_quickstart` in the Project name box. Leave the "Place solution and project in the same directory" checkbox **unchecked** and select **Next**.

    :::image type="content" source="media/quickstarts/configure-new-project.png" alt-text="Screenshot: Visual Studio's configure new project dialog window.":::

1. In the **Additional information** dialog window, make sure **.NET 6.0 (Long-term support)** is selected. Leave the "Don't use top-level statements" checkbox **unchecked** and select **Create**.

    :::image type="content" source="media/quickstarts/additional-information.png" alt-text="Screenshot: Visual Studio's additional information dialog window.":::

### Install the Newtonsoft.json package with NuGet

1. Right-click on your translator_quickstart project and select **Manage NuGet Packages...** .

    :::image type="content" source="media/quickstarts/manage-nuget.png" alt-text="Screenshot of the NuGet package search box.":::

1. Select the Browse tab and type Newtonsoft.json.

    :::image type="content" source="media/quickstarts/newtonsoft.png" alt-text="Screenshot of the NuGet package install window.":::

1. Select install from the right package manager window to add the package to your project.

    :::image type="content" source="media/quickstarts/install-newtonsoft.png" alt-text="Screenshot of the NuGet package install button.":::

### Build your application

> [!NOTE]
>
> * Starting with .NET 6, new projects using the `console` template generate a new program style that differs from previous versions.
> * The new output uses recent C# features that simplify the code you need to write.
> * When you use the newer version, you only need to write the body of the `Main` method. You don't need to include top-level statements, global using directives, or implicit using directives.
> * For more information, *see* [**New C# templates generate top-level statements**](/dotnet/core/tutorials/top-level-templates).

1. Open the **Program.cs** file.

1. Delete the pre-existing code, including the line `Console.Writeline("Hello World!")`. Copy and paste the code sample into your application's Program.cs file. Make sure you update the key variable with the value from your Azure portal Translator instance:

```csharp
using System.Text;
using Newtonsoft.Json;

class Program
{
    private static readonly string key = "<your-translator-key>";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com";

    static async Task Main(string[] args)
    {
        // Input and output languages are defined as parameters.
        string route = "/translate?api-version=3.0&from=en&to=fr&to=zu";
        string textToTranslate = "I would really like to drive your car around the block a few times!";
        object[] body = new object[] { new { Text = textToTranslate } };
        var requestBody = JsonConvert.SerializeObject(body);

        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", key);

            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}

```

### Run your C# application

Once you've added a code sample to your application, choose the green **start button** next to formRecognizer_quickstart to build and run your program, or press **F5**.

:::image type="content" source="media/quickstarts/run-program-visual-studio.png" alt-text="Screenshot of the rum program button in Visual Studio.":::

### [Go](#tab/go)

### Set up your Go environment

You can use any text editor to write Go applications. We recommend using the latest version of [Visual Studio Code and the Go extension](/azure/developer/go/configure-visual-studio-code).

> [!TIP]
>
> If you're new to Go, try the [**Get started with Go**](/learn/modules/go-get-started/) Microsoft Learn module.

1. If you haven't done so already, [download and install Go](https://go.dev/doc/install).

    * Download the Go version for your operating system.
    * Once the download is complete, run the installer.
    * Open a command prompt and enter the following to confirm Go was installed:

        ```console
          go version
        ```

1. In a console window (such as cmd, PowerShell, or Bash), create a new directory for your app called **translator-app**, and navigate to it.

1. Create a new GO file named **translation.go** from the **translator-app** directory.

1. Copy and paste the provided code sample into your **translation.go** file. Make sure you update the key variable with the value from your Azure portal Translator instance:

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    key := "<YOUR-TRANSLATOR-KEY>"
    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/translate?api-version=3.0"

    // Build the request URL. See: https://go.dev/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    q.Add("from", "en")
    q.Add("to", "fr")
    q.Add("to", "zu")
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "I would really like to drive your car around the block a few times."},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", key)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

### Run your Go application

Once you've added a code sample to your application, your Go program can be executed in a command or terminal prompt. Make sure your prompt's path is set to the **translator-app** folder and use the following command:

```console
 go run translation.go
```

### [Java](#tab/java)

### Set up your Java environment

* You should have the latest version of [Visual Studio Code](https://code.visualstudio.com/) or your preferred IDE. *See* [Java in Visual Studio Code](https://code.visualstudio.com/docs/languages/java).

  >[!TIP]
  >
  > * Visual Studio Code offers a **Coding Pack for Java** for Windows and macOS.The coding pack is a bundle of VS Code, the Java Development Kit (JDK), and a collection of suggested extensions by Microsoft. The Coding Pack can also be used to fix an existing development environment.
  > * If you are using VS Code and the Coding Pack For Java, install the [**Gradle for Java**](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-gradle) extension.

* If you aren't using VS Code, make sure you have the following installed in your development environment:

  * A [**Java Development Kit** (OpenJDK)](/java/openjdk/download#openjdk-17) version 8 or later.

  * [**Gradle**](https://docs.gradle.org/current/userguide/installation.html), version 6.8 or later.

### Create a new Gradle project

1. In console window (such as cmd, PowerShell, or Bash), create a new directory for your app called **translator-text-app**, and navigate to it.

    ```console
    mkdir translator-text-app && translator-text-app
    ```

   ```powershell
    mkdir translator-text-app; cd translator-text-app
   ```

1. Run the `gradle init` command from the translator-text-app directory. This command will create essential build files for Gradle, including *build.gradle.kts*, which is used at runtime to create and configure your application.

    ```console
    gradle init --type basic
    ```

1. When prompted to choose a **DSL**, select **Kotlin**.

1. Accept the default project name (translator-text-app) by selecting **Return** or **Enter**.

1. Update `build.gradle.kts` with the following code:

  ```kotlin
  plugins {
    java
    application
  }
  application {
    mainClass.set("TranslatorText")
  }
  repositories {
    mavenCentral()
  }
  dependencies {
    implementation("com.squareup.okhttp3:okhttp:4.10.0")
    implementation("com.google.code.gson:gson:2.9.0")
  }
  ```

### Create a Java Application

1. From the translator-text-app directory, run the following command:

    ```console
    mkdir -p src/main/java
    ```

   You'll create the following directory structure:

    :::image type="content" source="media/quickstarts/java-directories-2.png" alt-text="Screenshot: Java directory structure.":::

1. Navigate to the `java` directory and create a file named **`TranslatorText.java`**.

    > [!TIP]
    >
    > * You can create a new file using PowerShell.
    > * Open a PowerShell window in your project directory by holding down the Shift key and right-clicking the folder.
    > * Type the following command **New-Item TranslatorText.java**.
    >
    > * You can also create a new file in your IDE named `TranslatorText.java`  and save it to the `java` directory.

1. Open the `TranslatorText.java` file in your IDE and copy then paste the following code sample into your application. **Make sure you update the key with one of the key values from your Azure portal Translator instance:**

```java
import java.io.IOException;

import com.google.gson.*;
import okhttp3.MediaType;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.RequestBody;
import okhttp3.Response;

public class TranslatorText {
    private static String key = "<your-translator-key";


    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"I would really like to drive your car around the block a few times!\"}]");
        Request request = new Request.Builder()
                .url("https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&to=zu")
                .post(body)
                .addHeader("Ocp-Apim-Subscription-Key", key)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            TranslatorText translateRequest = new TranslatorText();
            String response = translateRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

### Build and run your application

Once you've added a code sample to your application, navigate back to your main project directory—**translator-text-app**, open a console window, and enter the following commands:

1. Build your application with the `build` command:

    ```console
    gradle build
    ```

1. Run your application with the `run` command:

    ```console
    gradle run
    ```

### [Node.js](#tab/nodejs)

### Create a Node.js Express application

1. If you haven't done so already, install the latest version of [Node.js](https://nodejs.org/en/download/). Node Package Manager (npm) is included with the Node.js installation.

    > [!TIP]
    >
    > If you're new to Node.js, try the [**Introduction to Node.js**](/learn/modules/intro-to-nodejs/) Microsoft Learn module.

1. In a console window (such as cmd, PowerShell, or Bash), create and navigate to a new directory for your app named `translator-app`.

    ```console
        mkdir translator-app && cd translator-app
    ```

   ```powershell
     mkdir translator-app; cd translator-app
   ```

1. Run the npm init command to initialize the application and scaffold your project.

    ```console
       npm init
    ```

1. Specify your project's attributes using the prompts presented in the terminal.

    * The most important attributes are name, version number, and entry point.
    * We recommend keeping `index.js` for the entry point name. The description, test command, GitHub repository, keywords, author, and license information are optional attributes—they can be skipped for this project.
    * Accept the suggestions in parentheses by selecting **Return** or **Enter**.
    * After you've completed the prompts, a `package.json` file will be created in your translator-app directory.

1. Open a console window and use npm to install the `axios` HTTP library and `uuid` package:

    ```console
       npm install axios uuid
    ```

1. Create the `index.js` file in the application directory.

     > [!TIP]
    >
    > * You can create a new file using PowerShell.
    > * Open a PowerShell window in your project directory by holding down the Shift key and right-clicking the folder.
    > * Type the following command **New-Item index.js**.
    >
    > * You can also create a new file named `index.js` in your IDE and save it to the `translator-app` directory.

1. Add the following code sample to your `index.js` file. **Make sure you update the key variable with the value from your Azure portal Translator instance**:

```javascript
    const axios = require('axios').default;
    const { v4: uuidv4 } = require('uuid');

    let key = "<your-translator-key>";
    let endpoint = "https://api.cognitive.microsofttranslator.com";

    axios({
        baseURL: endpoint,
        url: '/translate',
        method: 'post',
        headers: {
            'Ocp-Apim-Subscription-Key': key,
            'Content-type': 'application/json',
            'X-ClientTraceId': uuidv4().toString()
        },
        params: {
            'api-version': '3.0',
            'from': 'en',
            'to': ['fr', 'zu']
        },
        data: [{
            'text': 'I would really like to drive your car around the block a few times!'
        }],
        responseType: 'json'
    }).then(function(response){
        console.log(JSON.stringify(response.data, null, 4));
    })

```

### Run your application

Once you've added the code sample to your application, run your program:

1. Navigate to your application directory (translator-app).

1. Type the following command in your terminal:

    ```console
    node index.js
    ```

### [Python](#tab/python)

### Create a Python application

1. If you haven't done so already, install the latest version of [Python 3.x](https://www.python.org/downloads/). The Python installer package (pip) is included with the Python installation.

    > [!TIP]
    >
    > If you're new to Python, try the [**Introduction to Python**](/learn/paths/beginner-python/) Microsoft Learn module.

1. Open a terminal window and use pip to install the Requests library and uuid0 package:

    ```console
       pip install requests uuid
    ```

    > [!NOTE]
    > We will also use a Python built-in package called json. It's used to work with JSON data.

1. Create a new Python file called **translator-app.py** in your preferred editor or IDE.

1. Add the following code sample to your `translator-app.py` file. **Make sure you update the key with one of the values from your Azure portal Translator instance**.

```python
import requests, uuid, json

# Add your key and endpoint
key = "<your-translator-key>"
endpoint = "https://api.cognitive.microsofttranslator.com"

# Add your location, also known as region. The default is global.
# This is required if using a Cognitive Services resource.
location = "YOUR_RESOURCE_LOCATION"

path = '/translate'
constructed_url = endpoint + path

params = {
    'api-version': '3.0',
    'from': 'en',
    'to': ['fr', 'zu']
}

headers = {
    'Ocp-Apim-Subscription-Key': key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'I would really like to drive your car around the block a few times!'
}]

request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, ensure_ascii=False, indent=4, separators=(',', ': ')))

```

### Run your python application

Once you've added a code sample to your application, build and run your program:

1. Navigate to your **translator-app.py** file.

1. Type the following command in your console:

    ```console
    python translator-app.py
    ```

---

### Translation output

After a successful call, you should see the following response: 

```json
[
    {
        "detectedLanguage": {
            "language": "en",
            "score": 1.0
        },
        "translations": [
            {
                "text": "J'aimerais vraiment conduire votre voiture autour du pâté de maisons plusieurs fois!",
                "to": "fr"
            },
            {
                "text": "Ngingathanda ngempela ukushayela imoto yakho endaweni evimbelayo izikhathi ezimbalwa!",
                "to": "zu"
            }
        ]
    }
]

```

That's it, congratulations! You have learned to use the Translator service to translate text.

## Next step

 Explore our how-to documentation and take a deeper dive into Translation service capabilities:

* [**Translate text**](translator-text-apis.md#translate-text)

* [**Transliterate text**](translator-text-apis.md#transliterate-text)

* [**Detect and identify language**](translator-text-apis.md#detect-language)

* [**Get sentence length**](translator-text-apis.md#get-sentence-length)

* [**Dictionary lookup and alternate translations**](translator-text-apis.md#dictionary-examples-translations-in-context)