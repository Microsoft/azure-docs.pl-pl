---
title: 'Szybki Start: wyodrębnianie danych z faktury przy użyciu języka Python — formularz rozpoznawania'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start użyjesz interfejsu API REST aparatu rozpoznawania formularzy w języku Python w celu wyodrębnienia danych z faktur
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/12/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 2ed8bdd167814ea21fced89042bdcf80fd3a73df
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602652"
---
# <a name="quickstart-extract-invoice-data-using-the-form-recognizer-rest-api-with-python"></a>Szybki Start: wyodrębnianie danych z faktury przy użyciu interfejsu API REST aparatu rozpoznawania formularzy w języku Python

W tym przewodniku szybki start użyjesz interfejsu API REST aparatu rozpoznawania formularzy platformy Azure w języku Python, aby wyodrębnić i zidentyfikować odpowiednie informacje na fakturach.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz dysponować:
- Zainstalowana w języku [Python](https://www.python.org/downloads/) (Jeśli chcesz uruchomić przykład lokalnie).
- Dokument faktury. Możesz użyć [przykładowej faktury](../media/sample-invoice.jpg) dla tego przewodnika Szybki Start.

> [!NOTE]
> Ten przewodnik Szybki Start używa pliku lokalnego. Aby zamiast tego użyć dokumentu faktury, do którego można uzyskać dostęp za pomocą adresu URL, zobacz [dokumentację referencyjną](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync).

## <a name="create-a-form-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania formularza

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-an-invoice"></a>Analizowanie faktury

Aby rozpocząć analizowanie faktury, Wywołaj interfejs API **[analizy faktur](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9843c2794cbb1a96291)** przy użyciu poniższego skryptu języka Python. Przed uruchomieniem skryptu wprowadź następujące zmiany:

1. Zamień na `<Endpoint>` punkt końcowy uzyskany w ramach subskrypcji aparatu rozpoznawania formularza.
1. Zamień `<subscription key>` na klucz subskrypcji skopiowany z poprzedniego kroku.
1. Zamień na `<path to your invoice>` ścieżkę lokalną, w której zapisano przykładową fakturę.

    ```python
        ########### Python Form Recognizer Async Invoice #############
    
        import json
        import time
        from requests import get, post
        
        # Endpoint URL
        endpoint = r"<Endpoint>"
        apim_key = "<subscription key>"
        post_url = endpoint + "/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyze"
        source = r"<path to your invoice>"
        
        headers = {
            # Request headers
            'Content-Type': '<file type>',
            'Ocp-Apim-Subscription-Key': apim_key,
        }
        
        params = {
            "includeTextDetails": True
            "locale": "en-US"
        }
        
        with open(source, "rb") as f:
            data_bytes = f.read()
        
        try:
            resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
            if resp.status_code != 202:
                print("POST analyze failed:\n%s" % resp.text)
                quit()
            print("POST analyze succeeded:\n%s" % resp.headers)
            get_url = resp.headers["operation-location"]
        except Exception as e:
            print("POST analyze failed:\n%s" % str(e))
            quit()
    ```

1. Zapisz kod w pliku z rozszerzeniem. pr. Na przykład *form-Recognizer-Invoice.py*.
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia użyj polecenia `python`, aby uruchomić próbkę. Na przykład `python form-recognizer-invoice.py`.

Otrzymasz `202 (Success)` odpowiedź, która zawiera nagłówek **operacji-Location** , który skrypt będzie drukowany w konsoli programu. Ten nagłówek zawiera identyfikator operacji, którego można użyć do zbadania stanu operacji asynchronicznej i uzyskania wyników. W poniższym przykładzie wartość ciąg po `operations/` to identyfikator operacji.

## <a name="get-the-invoice-results"></a>Pobierz wyniki faktury

Po wywołaniu interfejsu API **Analizowanie faktury** można wywołać interfejs API **[wyników uzyskiwania analizy faktury](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9acb78c40a2533aee83)** , aby uzyskać stan operacji i wyodrębnionych danych. Dodaj następujący kod w dolnej części skryptu języka Python. Spowoduje to użycie wartości identyfikatora operacji w nowym wywołaniu interfejsu API. Ten skrypt wywołuje interfejs API w regularnych odstępach czasu, dopóki wyniki nie będą dostępne. Zalecamy interwał co najmniej jednej sekundy.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Invoice results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Invoice Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Invoice Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Zapisz skrypt.
1. Ponownie Użyj `python` polecenia, aby uruchomić przykład. Na przykład `python form-recognizer-invoice.py`.

### <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Skrypt będzie drukował odpowiedzi do konsoli do momentu zakończenia operacji **analizy faktury** . Następnie drukuje wyodrębnione dane tekstowe w formacie JSON. `"readResults"`Pole zawiera wszystkie wiersze tekstu wyodrębnione z faktury, w tym `"pageResults"` znaczniki tabele i zaznaczenia wyodrębnione z faktury, a `"documentResults"` pole zawiera informacje o kluczu/wartości dla najbardziej odpowiednich części faktury.

Zapoznaj się z następującym dokumentem faktury i odpowiednim wyjściem JSON:

* [Przykładowa faktura](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)
* [Przykładowe dane wyjściowe JSON faktury](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice-output.json)


### <a name="sample-python-script-to-extract-invoice-or-a-batch-of-invoices-into-a-csv-file"></a>Przykładowy skrypt w języku Python służący do wyodrębniania faktury lub partii faktur do pliku CSV

Ten przykładowy skrypt w języku Python pokazuje, jak rozpocząć pracę z interfejsem API fakturowania. Można uruchamiać z użyciem pojedynczej faktury jako parametru lub folderu i będzie wyprowadzać plik JSON ".invoice.json" oraz plik CSV _invoiceResutls.csv_ z wynikami wyodrębnionych wartości. W przypadku uruchamiania w folderze Program przeskanuje wszystkie pliki "PDF", "jpg", "JPEG", "png", "BMP", "tif", "TIFF" i uruchamia je za pomocą interfejsu API. 
 
```python
########### Python Form Recognizer Async Invoice #############

import json
import time
import os
import ntpath
import sys
from requests import get, post
import csv


def analyzeInvoice(filename):
    invoiceResultsFilename = filename + ".invoice.json"

    # do not run analyze if .invoice.json file is present on disk
    if os.path.isfile(invoiceResultsFilename):
        with open(invoiceResultsFilename) as json_file:
            return json.load(json_file)

    # Endpoint URL
    #endpoint = r"<Endpoint>"
    #apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyze"
    headers = {
        # Request headers
        'Content-Type': 'application/octet-stream',
        'Ocp-Apim-Subscription-Key': apim_key,
    }

    params = {
        "includeTextDetails": True
    }

    with open(filename, "rb") as f:
        data_bytes = f.read()

    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            return None
        print("POST analyze succeeded: %s" % resp.headers["operation-location"])
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        return None

    n_tries = 50
    n_try = 0
    wait_sec = 6

    while n_try < n_tries:
        try:
            resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
            resp_json = json.loads(resp.text)
            if resp.status_code != 200:
                print("GET Invoice results failed:\n%s" % resp_json)
                return None
            status = resp_json["status"]
            if status == "succeeded":
                print("Invoice analysis succeeded.")
                with open(invoiceResultsFilename, 'w') as outfile:
                    json.dump(resp_json, outfile, indent=4)
                return resp_json
            if status == "failed":
                print("Analysis failed:\n%s" % resp_json)
                return None
            # Analysis still running. Wait and retry.
            time.sleep(wait_sec)
            n_try += 1     
        except Exception as e:
            msg = "GET analyze results failed:\n%s" % str(e)
            print(msg)
            return None

    return resp_json

def parseInvoiceResults(resp_json):
    docResults = resp_json["analyzeResult"]["documentResults"]
    invoiceResult = {}
    for docResult in docResults:
        for fieldName, fieldValue in sorted(docResult["fields"].items()):
            valueFields = list(filter(lambda item: ("value" in item[0]) and ("valueString" not in item[0]), fieldValue.items()))
            invoiceResult[fieldName] = fieldValue["text"]            
            if len(valueFields) == 1:
                print("{0:26} : {1:50}      NORMALIZED VALUE: {2}".format(fieldName , fieldValue["text"], valueFields[0][1]))
                invoiceResult[fieldName + "_normalized"] = valueFields[0][1]
            else:
                print("{0:26} : {1}".format(fieldName , fieldValue["text"]))
    print("")
    return invoiceResult

def main(argv):
    if (len(argv)  != 2):
        print("ERROR: Please provide invoice filename or root directory with invoice PDFs/images as an argument to the python script")
        return

    # list of invoice to analyze
    invoiceFiles = []
    csvPostfix = '-invoiceResults.csv'
    if os.path.isfile(argv[1]):
        # Single invoice
        invoiceFiles.append(argv[1])
        csvFileName = argv[1] + csvPostfix
    else:
        # Folder of invoices
        supportedExt = ['.pdf', '.jpg','.jpeg','.tif','.tiff','.png','.bmp']
        invoiceDirectory = argv[1]
        csvFileName = os.path.join(invoiceDirectory, os.path.basename(os.path.abspath(invoiceDirectory)) + csvPostfix)
        for root, directories, filenames in os.walk(invoiceDirectory):
            for invoiceFilename in filenames:
                ext = os.path.splitext(invoiceFilename)[-1].lower()
                if ext in supportedExt:
                    fullname = os.path.join(root, invoiceFilename)
                    invoiceFiles.append(fullname)
                    
    with open(csvFileName, mode='w', newline='\n', encoding='utf-8') as csv_file:
        fieldnames = ['Filename',
                      'FullFilename','InvoiceTotal','InvoiceTotal_normalized','AmountDue','AmountDue_normalized','SubTotal','SubTotal_normalized','TotalTax','TotalTax_normalized','CustomerName','VendorName',
                      'InvoiceId','CustomerId','PurchaseOrder','InvoiceDate','InvoiceDate_normalized','DueDate','DueDate_normalized',
                      'VendorAddress','VendorAddressRecipient','BillingAddress','BillingAddressRecipient','ShippingAddress','ShippingAddressRecipient','CustomerAddress','CustomerAddressRecipient','ServiceAddress','ServiceAddressRecipient','RemittanceAddress','RemittanceAddressRecipient', 'ServiceStartDate','ServiceStartDate_normalized','ServiceEndDate','ServiceEndDate_normalized','PreviousUnpaidBalance','PreviousUnpaidBalance_normalized']
        writer = csv.DictWriter(csv_file, fieldnames=fieldnames)
        writer.writeheader()
        counter = 0
        for invoiceFullFilename in invoiceFiles:
            counter = counter + 1
            invoiceFilename = ntpath.basename(invoiceFullFilename)
            print("----- Processing {0}/{1} : {2} -----".format(counter, len(invoiceFiles),invoiceFullFilename))
            
            resp_json = analyzeInvoice(invoiceFullFilename)

            if (resp_json is not None):
                invoiceResults = parseInvoiceResults(resp_json)
                invoiceResults["FullFilename"] = invoiceFullFilename
                invoiceResults["Filename"] = invoiceFilename
                writer.writerow(invoiceResults)
                    
if __name__ == '__main__':
    main(sys.argv)
```

1. Zapisz kod w pliku z rozszerzeniem. pr. Na przykład *form-Recognizer-Invoice-to-CSV.py*.
1. Zamień na `<Endpoint>` punkt końcowy uzyskany w ramach subskrypcji aparatu rozpoznawania formularza.
1. Zamień `<subscription key>` na klucz subskrypcji skopiowany z poprzedniego kroku.
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia użyj polecenia `python`, aby uruchomić próbkę. Na przykład `python form-recognizer-invoice.py {file name or folder name}` skrypt języka Python może być uruchamiany z jedną fakturą lub folderem jako parametrem i będzie wyprowadzał plik JSON ".invoice.json" oraz wartości wyodrębnione z faktur do pliku CSV "-invoiceResults.csv" z wynikami. W przypadku uruchamiania w folderze Program przeskanuje wszystkie pliki "PDF", "jpg", "JPEG", "png", "BMP", "tif", "TIFF" i uruchamia je za pomocą interfejsu API.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto interfejsu API REST aparatu rozpoznawania formularzy w języku Python w celu wyodrębnienia zawartości z faktur. Następnie zapoznaj się z dokumentacją referencyjną w celu eksplorowania interfejsu API rozpoznawania formularzy.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)

   
