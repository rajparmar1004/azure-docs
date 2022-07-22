---
title: "Quickstart: Form Recognizer JavaScript SDK (beta) | Preview"
titleSuffix: Azure Applied AI Services
description: Form and document processing, data extraction, and analysis using Form Recognizer JavaScript client library SDKs v3.0 (preview)
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 06/22/2022
ms.author: lajanuar
recommendations: false
---
<!-- markdownlint-disable MD025 -->
# Get Started: Form Recognizer JavaScript SDK (beta)

>[!NOTE]
> Form Recognizer beta version—4.0.0-beta.4—is currently in public preview. Some features may not be supported or have limited capabilities.

[Reference documentation](/javascript/api/@azure/ai-form-recognizer/?view=azure-node-preview&preserve-view=true) | [Library source code](https://github.com/Azure/azure-sdk-for-js/tree/@azure/ai-form-recognizer_4.0.0-beta.4/sdk/formrecognizer/ai-form-recognizer) | [Package (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer/v/4.0.0-beta.4) | [Samples](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/formrecognizer/ai-form-recognizer/samples/v4-beta)

Get started with Azure Form Recognizer using the JavaScript programming language. Azure Form Recognizer is a cloud-based Azure Applied AI Service that uses machine learning to extract key-value pairs, text, and tables from your documents. You can easily call Form Recognizer models by integrating our client library SDKs into your workflows and applications. We recommend that you use the free service when you're learning the technology. Remember that the number of free pages is limited to 500 per month.

To learn more about Form Recognizer features and development options, visit our [Overview](../overview.md#form-recognizer-features-and-development-options) page.

In this quickstart you'll use the following features to analyze and extract data and values from forms and documents:

* [🆕 **General document**](#general-document-model)—Analyze and extract key-value pairs, selection marks, and entities from documents.

* [**Layout**](#layout-model)—Analyze and extract tables, lines, words, and selection marks like radio buttons and check boxes in documents, without the need to train a model.

* [**Prebuilt Invoice**](#prebuilt-model)—Analyze and extract common fields from specific document types using a pre-trained invoice model.

## Prerequisites

* Azure subscription - [Create one for free](https://azure.microsoft.com/free/cognitive-services/).

* The latest version of [Visual Studio Code](https://code.visualstudio.com/) or your preferred IDE. For more information, *see* [Node.js in Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)

* The latest LTS version of [Node.js](https://nodejs.org/about/releases/)

* A Cognitive Services or Form Recognizer resource. Once you have your Azure subscription, create a [single-service](https://portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) or [multi-service](https://portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) Form Recognizer resource in the Azure portal to get your key and endpoint. You can use the free pricing tier (`F0`) to try the service, and upgrade later to a paid tier for production.

    > [!TIP]
    > Create a Cognitive Services resource if you plan to access multiple cognitive services under a single endpoint/key. For Form Recognizer access only, create a Form Recognizer resource. Please note that you'll  need a single-service resource if you intend to use [Azure Active Directory authentication](../../../active-directory/authentication/overview-authentication.md).

* After your resource deploys, select **Go to resource**. You need the key and endpoint from the resource you create to connect your application to the Form Recognizer API. You'll paste your key and endpoint into the code below later in the quickstart:

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Screenshot: keys and endpoint location in the Azure portal.":::

## Set up

1. Create a new Node.js Express application: In a console window (such as cmd, PowerShell, or Bash), create and navigate to a new directory for your app named `form-recognizer-app`.

    ```console
    mkdir form-recognizer-app && cd form-recognizer-app
    ```

1. Run the `npm init` command to initialize the application and scaffold your project.

    ```console
    npm init
    ```

1. Specify your project's attributes using the prompts presented in the terminal.

    * The most important attributes are name, version number, and entry point.
    * We recommend keeping `index.js` for the entry point name. The description, test command, GitHub repository, keywords, author, and license information are optional attributes—they can be skipped for this project.
    * Accept the suggestions in parentheses by selecting **Return** or **Enter**.
    * After you've completed the prompts, a `package.json` file will be created in your form-recognizer-app directory.

1. Install the `ai-form-recognizer` client library and `azure/identity` npm packages:

    ```console
    npm install @azure/ai-form-recognizer@4.0.0-beta.4 @azure/identity
    ```

    * Your app's `package.json` file will be updated with the dependencies.

1. Create a file named `index.js` in the application directory.

    > [!TIP]
    >
    > * You can create a new file using PowerShell.
    > * Open a PowerShell window in your project directory by holding down the Shift key and right-clicking the folder.
    > * Type the following command **New-Item index.js**.

## Build your application

To interact with the Form Recognizer service, you'll need to create an instance of the `DocumentAnalysisClient` class. To do so, you'll create an `AzureKeyCredential` with your `key` from the Azure portal and a `DocumentAnalysisClient` instance with the `AzureKeyCredential` and your Form Recognizer `endpoint`.

1. Open the `index.js` file in Visual Studio Code or your favorite IDE and select one of the following code samples to copy and paste into your application:

    * [**General document**](#general-document-model)

    * [**Layout**](#layout-model)

    * [**Prebuilt Invoice**](#prebuilt-model)

> [!IMPORTANT]
>
> Remember to remove the key from your code when you're done, and never post it publicly. For production, use secure methods to store and access your credentials. For more information, see* the Cognitive Services [security](../../../cognitive-services/cognitive-services-security.md).

<!-- markdownlint-disable MD036 -->

## General document model

Extract text, tables, structure, key-value pairs, and named entities from documents.

> [!div class="checklist"]
>
> * For this example, you'll need a **form document file from a URL**. You can use our [sample form document](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) for this quickstart.
> * To analyze a given file from a URL, you'll use the `beginAnalyzeDocuments` method and pass in `prebuilt-document` as the model Id.
> * We've added the file URL value to the `formUrl` variable near the top of the file.

**Add the following code sample to the `index.js` file. Make sure you update the key and endpoint variables with values from your Azure portal Form Recognizer instance:**

```javascript

  const { AzureKeyCredential, DocumentAnalysisClient } = require("@azure/ai-form-recognizer");

  // set `<your-key>` and `<your-endpoint>` variables with the values from the Azure portal.
  const key = "<your-key>";
  const endpoint = "<your-endpoint>";

  // sample document
  const formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

  async function main() {
    const client = new DocumentAnalysisClient(endpoint, new AzureKeyCredential(key));

    const poller = await client.beginAnalyzeDocument("prebuilt-document", formUrl);

    const {
        keyValuePairs,
        entities
    } = await poller.pollUntilDone();

    if (keyValuePairs.length <= 0) {
        console.log("No key-value pairs were extracted from the document.");
    } else {
        console.log("Key-Value Pairs:");
        for (const {
                key,
                value,
                confidence
            } of keyValuePairs) {
            console.log("- Key  :", `"${key.content}"`);
            console.log("  Value:", `"${value?.content ?? "<undefined>"}" (${confidence})`);
        }
    }

}

main().catch((error) => {
    console.error("An error occurred:", error);
    process.exit(1);
});

```

**Run your application**

Once you've added a code sample to your application, run your program:

1. Navigate to the folder where you have your form recognizer application (form-recognizer-app).

1. Type the following command in your terminal:

    ```console
    node index.js
    ```

### General document model output

Here's a snippet of the expected output:

```console
Key-Value Pairs:
- Key  : "For the Quarterly Period Ended"
  Value: "March 31, 2020" (0.35)
- Key  : "From"
  Value: "1934" (0.119)
- Key  : "to"
  Value: "<undefined>" (0.317)
- Key  : "Commission File Number"
  Value: "001-37845" (0.87)
- Key  : "(I.R.S. ID)"
  Value: "91-1144442" (0.87)
- Key  : "Class"
  Value: "Common Stock, $0.00000625 par value per share" (0.748)
- Key  : "Outstanding as of April 24, 2020"
  Value: "7,583,440,247 shares" (0.838)
Entities:
- "$0.00000625" Quantity - Currency (0.8)
- "MSFT" Organization - <none> (0.99)
- "NASDAQ" Organization - StockExchange (0.99)
- "2.125%" Quantity - Percentage (0.8)
- "2021" DateTime - DateRange (0.8)
```

To view the entire output, visit the Azure samples repository on GitHub to view the [general document model output](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/v3-javascript-sdk-general-document-output.md)

## Layout model

Extract text, selection marks, text styles, table structures, and bounding region coordinates from documents.

> [!div class="checklist"]
>
> * For this example, you'll need a **form document file from a URL**. You can use our [sample form document](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) for this quickstart.
> * We've added the file URL value to the `formUrl` variable near the top of the file.
> * To analyze a given file from a URL, you'll use the `beginAnalyzeDocuments` method and pass in `prebuilt-layout` as the model Id.

**Add the following code sample to the `index.js` file. Make sure you update the key and endpoint variables with values from your Azure portal Form Recognizer instance:**

```javascript

 const { AzureKeyCredential, DocumentAnalysisClient } = require("@azure/ai-form-recognizer");

    // set `<your-key>` and `<your-endpoint>` variables with the values from the Azure portal.
    const key = "<your-key>";
    const endpoint = "<your-endpoint>";

    // sample document
  const formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

  async function main() {
      const client = new DocumentAnalysisClient(endpoint, new AzureKeyCredential(key));

      const poller = await client.beginAnalyzeDocument("prebuilt-layout", formUrl);

      const {
          pages,
          tables
      } = await poller.pollUntilDone();

      if (pages.length <= 0) {
          console.log("No pages were extracted from the document.");
      } else {
          console.log("Pages:");
          for (const page of pages) {
              console.log("- Page", page.pageNumber, `(unit: ${page.unit})`);
              console.log(`  ${page.width}x${page.height}, angle: ${page.angle}`);
              console.log(`  ${page.lines.length} lines, ${page.words.length} words`);
          }
      }

      if (tables.length <= 0) {
          console.log("No tables were extracted from the document.");
      } else {
          console.log("Tables:");
          for (const table of tables) {
              console.log(
                  `- Extracted table: ${table.columnCount} columns, ${table.rowCount} rows (${table.cells.length} cells)`
              );
          }
      }
  }

  main().catch((error) => {
      console.error("An error occurred:", error);
      process.exit(1);
  });

```

**Run your application**

Once you've added a code sample to your application, run your program:

1. Navigate to the folder where you have your form recognizer application (form-recognizer-app).

1. Type the following command in your terminal:

    ```console
    node index.js
    ```

### Layout model output

Here's a snippet of the expected output:

```console
Pages:
- Page 1 (unit: inch)
  8.5x11, angle: 0
  69 lines, 425 words
Tables:
- Extracted table: 3 columns, 5 rows (15 cells)
```

To view the entire output, visit the Azure samples repository on GitHub to view the [layout model output](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/v3-javascript-sdk-layout-output.md)

## Prebuilt model

In this example, we'll analyze an invoice using the **prebuilt-invoice** model.

> [!TIP]
> You aren't limited to invoices—there are several prebuilt models to choose from, each of which has its own set of supported fields. The model to use for the analyze operation depends on the type of document to be analyzed. See [**model data extraction**](../concept-model-overview.md#model-data-extraction).

> [!div class="checklist"]
>
> * Analyze an invoice using the prebuilt-invoice model. You can use our [sample invoice document](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) for this quickstart.
> * We've added the file URL value to the `invoiceUrl` variable at the top of the file.
> * To analyze a given file at a URI, you'll use the `beginAnalyzeDocuments` method and pass `PrebuiltModels.Invoice` as the model Id. The returned value is a `result` object containing data about the submitted document.
> * For simplicity, all the key-value pairs that the service returns are not shown here. To see the list of all supported fields and corresponding types, see our [Invoice](../concept-invoice.md#field-extraction) concept page.

```javascript

  // using the PrebuiltModels object, rather than the raw model ID, adds strong typing to the model's output
  const { PrebuiltModels } = require("@azure/ai-form-recognizer");

  // set `<your-key>` and `<your-endpoint>` variables with the values from the Azure portal.
      const key = "<your-key>";
      const endpoint = "<your-endpoint>";

  // sample document
  const invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

  async function main() {

      const client = new DocumentAnalysisClient(endpoint, new AzureKeyCredential(key));

      const poller = await client.beginAnalyzeDocument(PrebuiltModels.Invoice, invoiceUrl);

      const {
          documents: [result]
      } = await poller.pollUntilDone();

      if (result) {
          const invoice = result.fields;

          console.log("Vendor Name:", invoice.vendorName?.value);
          console.log("Customer Name:", invoice.customerName?.value);
          console.log("Invoice Date:", invoice.invoiceDate?.value);
          console.log("Due Date:", invoice.dueDate?.value);

          console.log("Items:");
          for (const {
                  properties: item
              } of invoice.items?.values ?? []) {
              console.log("-", item.productCode?.value ?? "<no product code>");
              console.log("  Description:", item.description?.value);
              console.log("  Quantity:", item.quantity?.value);
              console.log("  Date:", item.date?.value);
              console.log("  Unit:", item.unit?.value);
              console.log("  Unit Price:", item.unitPrice?.value);
              console.log("  Tax:", item.tax?.value);
              console.log("  Amount:", item.amount?.value);
          }

          console.log("Subtotal:", invoice.subTotal?.value);
          console.log("Previous Unpaid Balance:", invoice.previousUnpaidBalance?.value);
          console.log("Tax:", invoice.totalTax?.value);
          console.log("Amount Due:", invoice.amountDue?.value);
      } else {
          throw new Error("Expected at least one receipt in the result.");
      }
  }

  main().catch((error) => {
      console.error("An error occurred:", error);
      process.exit(1);
  });
```

**Run your application**

Once you've added a code sample to your application, run your program:

1. Navigate to the folder where you have your form recognizer application (form-recognizer-app).

1. Type the following command in your terminal:

    ```console
    node index.js
    ```

### Prebuilt model output

Here's a snippet of the expected output:

```console
  Vendor Name: CONTOSO LTD.
  Customer Name: MICROSOFT CORPORATION
  Invoice Date: 2019-11-15T00:00:00.000Z
  Due Date: 2019-12-15T00:00:00.000Z
  Items:
  - <no product code>
    Description: Test for 23 fields
    Quantity: 1
    Date: undefined
    Unit: undefined
    Unit Price: 1
    Tax: undefined
    Amount: 100
```

To view the entire output, visit the Azure samples repository on GitHub to view the [prebuilt invoice model output](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/v3-javascript-sdk-prebuilt-invoice-output.md)

That's it, congratulations!

In this quickstart, you used the Form Recognizer JavaScript SDK to analyze various forms in different ways. Next, explore the Form Recognizer Studio and reference documentation to learn moe about Form Recognizer v3.0 API.

## Next steps

>[!div class="nextstepaction"]
> [**Form Recognizer Studio**](https://formrecognizer.appliedai.azure.com/studio)

> [!div class="nextstepaction"]
> [Form Recognizer REST API v3.0 (preview)](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-2022-06-30-preview/operations/AnalyzeDocument)
