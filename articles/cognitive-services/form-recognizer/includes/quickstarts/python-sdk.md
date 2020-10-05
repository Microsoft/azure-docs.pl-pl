---
title: 'Szybki Start: Biblioteka klienta aparatu rozpoznawania formularzy dla języka Python'
description: Biblioteka klienta aparatu rozpoznawania formularzy dla języka Python służy do tworzenia aplikacji przetwarzającej formularze, która wyodrębnia pary klucz/wartość i dane tabeli z dokumentów niestandardowych.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 09/21/2020
ms.author: pafarley
ms.openlocfilehash: 6d460fc9ded9416ef9d2a30b1a0effa863626888
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91318919"
---
> [!IMPORTANT]
> * Zestaw SDK aparatu rozpoznawania formularzy obecnie jest przeznaczony dla wersji v 2.0 z usługi rozpoznawania.
> * Kod w tym artykule używa metod synchronicznych i niezabezpieczonych magazynów poświadczeń z przyczyn uproszczenia. Zapoznaj się z dokumentacją referencyjną poniżej. 

[Dokumentacja](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer)  |  źródłowy biblioteki [Pakiet (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Obiekt BLOB usługi Azure Storage zawierający zestaw danych szkoleniowych. Zapoznaj się z tematem [Tworzenie zestawu danych szkoleniowych dla modelu niestandardowego](../../build-training-data-set.md) w celu uzyskania wskazówek i opcji związanych z zestawem danych szkoleniowych. W tym przewodniku szybki start można użyć plików w folderze **uczenie** [zestawu danych przykładowych](https://go.microsoft.com/fwlink/?linkid=2090451).
* [Python 2,7 lub 3,5 lub nowszy](https://www.python.org/)
* Gdy masz subskrypcję platformy Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" Utwórz zasób aparatu rozpoznawania formularzy "  target="_blank"> Utwórz zasób aparatu rozpoznawania formularza <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który utworzysz, aby połączyć aplikację z interfejsem API rozpoznawania formularzy. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="object-model"></a>Model obiektów 

Za pomocą aparatu rozpoznawania formularzy można utworzyć dwa różne typy klientów. Pierwszy `form_recognizer_client` jest używany do wysyłania zapytań do usługi do rozpoznanych pól formularzy i zawartości. Drugi — `form_training_client` służy do tworzenia modeli niestandardowych i zarządzania nimi, których można użyć w celu usprawnienia rozpoznawania. 

### <a name="formrecognizerclient"></a>FormRecognizerClient
`form_recognizer_client` zawiera operacje dla:

 * Rozpoznawanie pól formularzy i zawartości przy użyciu modeli niestandardowych przeszkolonych w celu rozpoznawania formularzy niestandardowych. 
 * Rozpoznawanie zawartości formularza, w tym tabel, wierszy i słów, bez konieczności uczenia modelu. 
 * Rozpoznawanie typowych pól z przyjęć przy użyciu wstępnie przeszkolonego modelu paragonów w usłudze aparat rozpoznawania formularzy.

### <a name="formtrainingclient"></a>FormTrainingClient
`form_training_client` zawiera operacje dla:

* Szkolenie modeli niestandardowych w celu rozpoznania wszystkich pól i wartości znalezionych w formularzach niestandardowych. Zapoznaj się z [dokumentacją usługi dotyczącą nieoznaczonego szkolenia modelu](#train-a-model-without-labels) , aby uzyskać bardziej szczegółowy opis tworzenia zestawu danych szkoleniowych.
* Szkolenie modeli niestandardowych w celu rozpoznawania określonych pól i wartości, które można określić przez etykietowanie formularzy niestandardowych. Zapoznaj się z [dokumentacją usługi w obszarze zatytułowany model szkoleń](#train-a-model-with-labels) , aby uzyskać bardziej szczegółowy opis stosowania etykiet do zestawu danych szkoleniowych.
* Zarządzanie modelami utworzonymi na Twoim koncie.
* Kopiowanie modelu niestandardowego z jednego do drugiego zasobu aparatu rozpoznawania formularza.

> [!NOTE]
> Modele mogą być również przeszkolone przy użyciu graficznego interfejsu użytkownika, takiego jak [Narzędzie do etykietowania aparatów rozpoznawania formularzy](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/label-tool).

## <a name="setting-up"></a>Konfigurowanie

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Po zainstalowaniu języka Python można zainstalować najnowszą wersję biblioteki klienta aparatu rozpoznawania formularzy z:

```console
pip install azure-ai-formrecognizer
```

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nową aplikację w języku Python przy użyciu preferowanego edytora lub środowiska IDE. Następnie zaimportuj poniższe biblioteki. Należy pamiętać, że importowane są biblioteki wymagające zarówno szkolenia, jak i rozpoznawania formy.

```python
import os
from azure.core.exceptions import ResourceNotFoundError
from azure.ai.formrecognizer import FormRecognizerClient
from azure.ai.formrecognizer import FormTrainingClient
from azure.core.credentials import AzureKeyCredential
```

Utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource. 

```python
endpoint = "<paste-your-form-recognizer-endpoint-here>"
key = "<paste-your-form-recognizer-key-here>"
```

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań za pomocą biblioteki klienckiej aparatu rozpoznawania formularzy dla języka Python:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Rozpoznaj zawartość formularza](#recognize-form-content)
* [Rozpoznawaj potwierdzenia](#recognize-receipts)
* [Trenowanie modelu niestandardowego](#train-a-custom-model)
* [Analizowanie formularzy przy użyciu modelu niestandardowego](#analyze-forms-with-a-custom-model)
* [Zarządzanie modelami niestandardowymi](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

W tym miejscu będziesz uwierzytelniać dwa obiekty klienckie przy użyciu zdefiniowanych powyżej zmiennych. Użyjesz `AzureKeyCredential` obiektu, tak aby w razie potrzeby można było zaktualizować klucz interfejsu API bez tworzenia nowych obiektów klienta.

```python
form_recognizer_client = FormRecognizerClient(endpoint, AzureKeyCredential(key))
form_training_client = FormTrainingClient(endpoint, AzureKeyCredential(key))
```

## <a name="get-assets-for-testing"></a>Pobierz zasoby do testowania

Fragmenty kodu w tym przewodniku korzystają z formularzy zdalnych, do których uzyskuje dostęp za pomocą adresów URL. Jeśli zamiast tego chcesz przetworzyć lokalne dokumenty formularzy, zapoznaj się z odpowiednimi metodami w [dokumentacji referencyjnej](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer) i [przykładami](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

Należy również dodać odwołania do adresów URL dla danych szkoleniowych i testowych.
* Aby pobrać adres URL sygnatury dostępu współdzielonego dla danych szkolenia modelu niestandardowego, Otwórz Eksplorator usługi Microsoft Azure Storage, kliknij prawym przyciskiem myszy kontener, a następnie wybierz pozycję **Pobierz sygnaturę dostępną**. Upewnij się, że uprawnienia do **odczytu** i **listy** są zaznaczone, a następnie kliknij przycisk **Utwórz**. Następnie skopiuj wartość z sekcji **URL** . Powinna ona mieć postać: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Skorzystaj z obrazów przykładowych z i paragonów zawartych w poniższych przykładach (dostępnych również w witrynie [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) ) lub wykonaj powyższe kroki, aby uzyskać adres URL sygnatury dostępu współdzielonego pojedynczego dokumentu w usłudze BLOB Storage. 

> [!NOTE]
> Fragmenty kodu w tym przewodniku korzystają z formularzy zdalnych, do których uzyskuje dostęp za pomocą adresów URL. Jeśli zamiast tego chcesz przetworzyć lokalne dokumenty formularzy, zapoznaj się z odpowiednimi metodami w [dokumentacji referencyjnej](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

## <a name="recognize-form-content"></a>Rozpoznaj zawartość formularza

Aparat rozpoznawania formularzy służy do rozpoznawania tabel, wierszy i słów w dokumentach, bez konieczności uczenia modelu.

Aby rozpoznać zawartość pliku pod podanym adresem URL, użyj `begin_recognize_content` metody. Zwracana wartość jest kolekcją `FormPage` obiektów: jeden dla każdej strony w przesłanym dokumencie. Poniższy kod wykonuje iterację tych obiektów i drukuje wyodrębnione pary klucz/wartość i dane tabeli.

```Python
formUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/forms/Invoice_1.pdf"

poller = form_recognizer_client.begin_recognize_content_from_url(formUrl)
page = poller.result()

table = page[0].tables[0] # page 1, table 1
print("Table found on page {}:".format(table.page_number))
for cell in table.cells:
    print("Cell text: {}".format(cell.text))
    print("Location: {}".format(cell.bounding_box))
    print("Confidence score: {}\n".format(cell.confidence))
```

### <a name="output"></a>Dane wyjściowe

```console
Table found on page 1:
Cell text: Invoice Number
Location: [Point(x=0.5075, y=2.8088), Point(x=1.9061, y=2.8088), Point(x=1.9061, y=3.3219), Point(x=0.5075, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Date
Location: [Point(x=1.9061, y=2.8088), Point(x=3.3074, y=2.8088), Point(x=3.3074, y=3.3219), Point(x=1.9061, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Due Date
Location: [Point(x=3.3074, y=2.8088), Point(x=4.7074, y=2.8088), Point(x=4.7074, y=3.3219), Point(x=3.3074, y=3.3219)]
Confidence score: 1.0

Cell text: Charges
Location: [Point(x=4.7074, y=2.8088), Point(x=5.386, y=2.8088), Point(x=5.386, y=3.3219), Point(x=4.7074, y=3.3219)]
Confidence score: 1.0

...

```

## <a name="recognize-receipts"></a>Rozpoznawaj potwierdzenia

W tej sekcji przedstawiono sposób rozpoznawania i wyodrębniania typowych pól z paragonów w Stanach Zjednoczonych przy użyciu wstępnie przeszkolonego modelu paragonów. Aby rozpoznać potwierdzenia z adresu URL, należy użyć `begin_recognize_receipts_from_url` metody. 

```python
receiptUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/receipt/contoso-receipt.png"

poller = form_recognizer_client.begin_recognize_receipts_from_url(receiptUrl)
result = poller.result()

for receipt in result:
    for name, field in receipt.fields.items():
        if name == "Items":
            print("Receipt Items:")
            for idx, items in enumerate(field.value):
                print("...Item #{}".format(idx + 1))
                for item_name, item in items.value.items():
                    print("......{}: {} has confidence {}".format(item_name, item.value, item.confidence))
        else:
            print("{}: {} has confidence {}".format(name, field.value, field.confidence))
```

### <a name="output"></a>Dane wyjściowe

```console
ReceiptType: Itemized has confidence 0.659
MerchantName: Contoso Contoso has confidence 0.516
MerchantAddress: 123 Main Street Redmond, WA 98052 has confidence 0.986
MerchantPhoneNumber: None has confidence 0.99
TransactionDate: 2019-06-10 has confidence 0.985
TransactionTime: 13:59:00 has confidence 0.968
Receipt Items:
...Item #1
......Name: 8GB RAM (Black) has confidence 0.916
......TotalPrice: 999.0 has confidence 0.559
...Item #2
......Quantity: None has confidence 0.858
......Name: SurfacePen has confidence 0.858
......TotalPrice: 99.99 has confidence 0.386
Subtotal: 1098.99 has confidence 0.964
Tax: 104.4 has confidence 0.713
Total: 1203.39 has confidence 0.774
```

## <a name="train-a-custom-model"></a>Trenowanie modelu niestandardowego

W tej sekcji pokazano, jak szkolić model przy użyciu własnych danych. Model przeszkolony może wyprowadzać dane strukturalne, które zawierają relacje klucz/wartość w oryginalnym dokumencie formularza. Po przeprowadzeniu szkolenia modelu można testować i przeszkolić go i ostatecznie użyć do niezawodnego wyodrębnienia danych z większej liczby formularzy zgodnie z potrzebami.

> [!NOTE]
> Możesz również nauczyć modele przy użyciu graficznego interfejsu użytkownika, takiego jak [Narzędzie do próbkowania przykładowego aparatu rozpoznawania formularzy](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Uczenie modelu bez etykiet

Uczenie modeli niestandardowych w celu rozpoznawania wszystkich pól i wartości znalezionych w formularzach niestandardowych bez ręcznego etykietowania dokumentów szkoleniowych.

Poniższy kod używa klienta szkoleniowego z `begin_training` funkcją do uczenia modelu w danym zestawie dokumentów. Zwrócony `CustomFormModel` obiekt zawiera informacje na temat typów formularzy, które może rozpoznać model, oraz pól, które mogą zostać wyodrębnione z każdego typu formularza. Poniższy blok kodu drukuje te informacje w konsoli programu.

```python
# To train a model you need an Azure Storage account.
# Use the SAS URL to access your training files.
trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>"

poller = form_training_client.begin_training(trainingDataUrl, use_training_labels=False)
model = poller.result()

print("Model ID: {}".format(model.model_id))
print("Status: {}".format(model.status))
print("Training started on: {}".format(model.training_started_on))
print("Training completed on: {}".format(model.training_completed_on))

print("\nRecognized fields:")
for submodel in model.submodels:
    print(
        "The submodel with form type '{}' has recognized the following fields: {}".format(
            submodel.form_type,
            ", ".join(
                [
                    field.label if field.label else name
                    for name, field in submodel.fields.items()
                ]
            ),
        )
    )

# Training result information
for doc in model.training_documents:
    print("Document name: {}".format(doc.name))
    print("Document status: {}".format(doc.status))
    print("Document page count: {}".format(doc.page_count))
    print("Document errors: {}".format(doc.errors))
```

### <a name="output"></a>Dane wyjściowe

Jest to dane wyjściowe dla modelu przeszkolonego z danymi szkoleniowymi dostępnymi z [zestawu SDK języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training).

```console
Model ID: 628739de-779c-473d-8214-d35c72d3d4f7
Status: ready
Training started on: 2020-08-20 23:16:51+00:00
Training completed on: 2020-08-20 23:16:59+00:00

Recognized fields:
The submodel with form type 'form-0' has recognized the following fields: Additional Notes:, Address:, Company Name:, Company Phone:, Dated As:, Details, Email:, Hero Limited, Name:, Phone:, Purchase Order, Purchase Order #:, Quantity, SUBTOTAL, Seattle, WA 93849 Phone:, Shipped From, Shipped To, TAX, TOTAL, Total, Unit Price, Vendor Name:, Website:
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

### <a name="train-a-model-with-labels"></a>Uczenie modelu z etykietami

Można także uczenie modeli niestandardowych przez ręczne etykietowanie dokumentów szkoleniowych. Szkolenie z etykietami prowadzi do lepszej wydajności w niektórych scenariuszach. Zwracana wartość `CustomFormModel` wskazuje pola, które mogą zostać wyodrębnione przez model, wraz z szacowaną dokładnością dla każdego pola. Poniższy blok kodu drukuje te informacje w konsoli programu.

> [!IMPORTANT]
> Aby szkolić z etykietami, musisz mieć specjalne pliki informacji o etykietach ( `\<filename\>.pdf.labels.json` ) w kontenerze magazynu obiektów BLOB obok dokumentów szkoleniowych. [Narzędzie do etykietowania próbek aparatu rozpoznawania formularzy](../../quickstarts/label-tool.md) udostępnia interfejs użytkownika ułatwiający Tworzenie tych plików etykiet. Po ich utworzeniu można wywołać `begin_training` funkcję z parametrem *use_training_labels* ustawionym na `true` .

```python
# To train a model you need an Azure Storage account.
# Use the SAS URL to access your training files.
trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>"

poller = form_training_client.begin_training(trainingDataUrl, use_training_labels=True)
model = poller.result()

print("Model ID: {}".format(model.model_id))
print("Status: {}".format(model.status))
print("Training started on: {}".format(model.training_started_on))
print("Training completed on: {}".format(model.training_completed_on))

print("\nRecognized fields:")
for submodel in model.submodels:
    print(
        "The submodel with form type '{}' has recognized the following fields: {}".format(
            submodel.form_type,
            ", ".join(
                [
                    field.label if field.label else name
                    for name, field in submodel.fields.items()
                ]
            ),
        )
    )

# Training result information
for doc in model.training_documents:
    print("Document name: {}".format(doc.name))
    print("Document status: {}".format(doc.status))
    print("Document page count: {}".format(doc.page_count))
    print("Document errors: {}".format(doc.errors))
```

### <a name="output"></a>Dane wyjściowe

Jest to dane wyjściowe dla modelu przeszkolonego z danymi szkoleniowymi dostępnymi z [zestawu SDK języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training).

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91

Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00

Recognized fields:
The submodel with form type 'form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91' has recognized the following fields: CompanyAddress, CompanyName, CompanyPhoneNumber, DatedAs, Email, Merchant, PhoneNumber, PurchaseOrderNumber, Quantity, Signature, Subtotal, Tax, Total, VendorName, Website
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

## <a name="analyze-forms-with-a-custom-model"></a>Analizowanie formularzy przy użyciu modelu niestandardowego

W tej sekcji pokazano, jak wyodrębnić informacje o kluczu/wartości i innej zawartości z niestandardowych typów formularzy przy użyciu modeli przeszkolonych za pomocą własnych formularzy.

> [!IMPORTANT]
> Aby zaimplementować ten scenariusz, należy wcześniej przeszkolić model, aby można było przekazać jego identyfikator do metody poniżej. Zobacz sekcję [uczenie modelu](#train-a-model-without-labels) .

Ta metoda zostanie użyta `begin_recognize_custom_forms_from_url` . Zwracana wartość jest kolekcją `RecognizedForm` obiektów: jeden dla każdej strony w przesłanym dokumencie. Poniższy kod drukuje wyniki analizy w konsoli programu. Wypisuje wszystkie rozpoznane pola i odpowiadające im wartości, a także ocenę ufności.

```python
# Model ID from when you trained your model.
model_id = "<your custom model id>"

poller = form_recognizer_client.begin_recognize_custom_forms_from_url(
    model_id=model_id, form_url=formUrl)
result = poller.result()

for recognized_form in result:
    print("Form type: {}".format(recognized_form.form_type))
    for name, field in recognized_form.fields.items():
        print("Field '{}' has label '{}' with value '{}' and a confidence score of {}".format(
            name,
            field.label_data.text if field.label_data else name,
            field.value,
            field.confidence
        ))
```

### <a name="output"></a>Dane wyjściowe

Korzystając z modelu z poprzedniego przykładu, dostarczane są następujące dane wyjściowe.

```console
Form type: form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Field 'Merchant' has label 'Merchant' with value 'Invoice For:' and a confidence score of 0.116
Field 'CompanyAddress' has label 'CompanyAddress' with value '1 Redmond way Suite 6000 Redmond, WA' and a confidence score of 0.258
Field 'Website' has label 'Website' with value '99243' and a confidence score of 0.114
Field 'VendorName' has label 'VendorName' with value 'Charges' and a confidence score of 0.145
Field 'CompanyPhoneNumber' has label 'CompanyPhoneNumber' with value '$56,651.49' and a confidence score of 0.249
Field 'CompanyName' has label 'CompanyName' with value 'PT' and a confidence score of 0.245
Field 'DatedAs' has label 'DatedAs' with value 'None' and a confidence score of None
Field 'Email' has label 'Email' with value 'None' and a confidence score of None
Field 'PhoneNumber' has label 'PhoneNumber' with value 'None' and a confidence score of None
Field 'PurchaseOrderNumber' has label 'PurchaseOrderNumber' with value 'None' and a confidence score of None
Field 'Quantity' has label 'Quantity' with value 'None' and a confidence score of None
Field 'Signature' has label 'Signature' with value 'None' and a confidence score of None
Field 'Subtotal' has label 'Subtotal' with value 'None' and a confidence score of None
Field 'Tax' has label 'Tax' with value 'None' and a confidence score of None
Field 'Total' has label 'Total' with value 'None' and a confidence score of None
```

## <a name="manage-your-custom-models"></a>Zarządzanie modelami niestandardowymi

W tej sekcji pokazano, jak zarządzać modelami niestandardowymi przechowywanymi na Twoim koncie. 

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Sprawdź liczbę modeli na koncie zasobów FormRecognizer

Poniższy blok kodu sprawdza, ile modeli Zapisano na koncie aparatu rozpoznawania formularzy i porównuje je z limitem konta.

```python
account_properties = form_training_client.get_account_properties()
print("Our account has {} custom models, and we can have at most {} custom models".format(
    account_properties.custom_model_count, account_properties.custom_model_limit
))
```

### <a name="output"></a>Dane wyjściowe

```console
Our account has 5 custom models, and we can have at most 5000 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Wyświetlanie listy modeli przechowywanych obecnie na koncie zasobu

Poniższy blok kodu zawiera listę bieżących modeli na koncie i drukuje ich szczegóły do konsoli programu. Zapisuje także odwołanie do pierwszego modelu.

```python
# Next, we get a paged list of all of our custom models
custom_models = form_training_client.list_custom_models()

print("We have models with the following ids:")

# Let's pull out the first model
first_model = next(custom_models)
print(first_model.model_id)
for model in custom_models:
    print(model.model_id)
```

### <a name="output"></a>Dane wyjściowe

To jest przykładowe dane wyjściowe dla konta testowego.

```console
We have models with the following ids:
453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
628739de-779c-473d-8214-d35c72d3d4f7
ae636292-0b14-4e26-81a7-a0bfcbaf7c91
b4b5df77-8538-4ffb-a996-f67158ecd305
c6309148-6b64-4fef-aea0-d39521452699
```

### <a name="get-a-specific-model-using-the-models-id"></a>Pobierz konkretny model przy użyciu identyfikatora modelu

Poniższy blok kodu używa identyfikatora modelu zapisanego w poprzedniej sekcji i używa go do pobierania szczegółowych informacji o modelu.

```python
model_id = "<model_id from the Train a Model sample>"

custom_model = form_training_client.get_custom_model(model_id=model_id)
print("Model ID: {}".format(custom_model.model_id))
print("Status: {}".format(custom_model.status))
print("Training started on: {}".format(custom_model.training_started_on))
print("Training completed on: {}".format(custom_model.training_completed_on))
```

### <a name="output"></a>Dane wyjściowe

Jest to przykładowe dane wyjściowe dla modelu niestandardowego utworzonego w poprzednim przykładzie.

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00
```

### <a name="delete-a-model-from-the-resource-account"></a>Usuwanie modelu z konta zasobu

Możesz również usunąć model z konta, odwołując się do jego identyfikatora. Ten kod usuwa model używany w poprzedniej sekcji.

```python
form_training_client.delete_model(model_id=custom_model.model_id)

try:
    form_training_client.get_custom_model(model_id=custom_model.model_id)
except ResourceNotFoundError:
    print("Successfully deleted model with id {}".format(custom_model.model_id))
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aplikację można uruchomić w dowolnym momencie z dowolną liczbą funkcji, które zostały odczytane w tym przewodniku szybki start za pomocą tego polecenia:

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="general"></a>Ogólne

Biblioteka klienta aparatu rozpoznawania formularzy będzie zgłaszać wyjątki zdefiniowane w [podstawowym systemie Azure](https://aka.ms/azsdk-python-azure-core).

## <a name="logging"></a>Rejestrowanie

Ta biblioteka używa [standardowej biblioteki rejestrowania](https://docs.python.org/3/library/logging.html) do rejestrowania. Podstawowe informacje o sesjach HTTP (adresach URL, nagłówkach itd.) są rejestrowane na poziomie informacji.

Szczegółowe rejestrowanie na poziomie debugowania, w tym treści żądania/odpowiedzi i nagłówki unredacted, można włączyć na kliencie z `logging_enable` argumentem słowa kluczowego:

```python
import sys
import logging
from azure.ai.formrecognizer import FormRecognizerClient
from azure.core.credentials import AzureKeyCredential

# Create a logger for the 'azure' SDK
logger = logging.getLogger('azure')
logger.setLevel(logging.DEBUG)

# Configure a console output
handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)

endpoint = "https://<my-custom-subdomain>.cognitiveservices.azure.com/"
credential = AzureKeyCredential("<api_key>")

# This client will log detailed information about its HTTP sessions, at DEBUG level
form_recognizer_client = FormRecognizerClient(endpoint, credential, logging_enable=True)
```

Podobnie, `logging_enable` można włączyć szczegółowe rejestrowanie dla jednej operacji, nawet wtedy, gdy nie jest ona włączona dla klienta:

```python
poller = form_recognizer_client.begin_recognize_receipts(receipt, logging_enable=True)
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto biblioteki klienckiej aparatu rozpoznawania języka Python do uczenia modeli i analizowania formularzy na różne sposoby. Następnie zapoznaj się z poradami, aby utworzyć lepszy zestaw danych szkoleniowych i uzyskać bardziej dokładne modele.

> [!div class="nextstepaction"]
> [Tworzenie zestawu danych szkoleniowych](../../build-training-data-set.md)

## <a name="see-also"></a>Zobacz też

* [Co to jest rozpoznawanie formularzy?](../../overview.md)
