---
title: 'Szybki Start: Biblioteka klienta aparatu rozpoznawania formularzy dla języka Python'
description: Biblioteka klienta aparatu rozpoznawania formularzy dla języka Python służy do tworzenia aplikacji przetwarzającej formularze, która wyodrębnia pary klucz/wartość i dane tabeli z dokumentów niestandardowych.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: 534916d81cfb4d3ad1e96d2934f43221067fb94f
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95867684"
---
> [!IMPORTANT]
> * Kod w tym artykule używa metod synchronicznych i niezabezpieczonych magazynów poświadczeń z przyczyn uproszczenia. Zapoznaj się z dokumentacją referencyjną poniżej. 

[Dokumentacja](/python/api/azure-ai-formrecognizer)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer)  |  źródłowy biblioteki [Pakiet (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Obiekt BLOB usługi Azure Storage zawierający zestaw danych szkoleniowych. Zapoznaj się z tematem [Tworzenie zestawu danych szkoleniowych dla modelu niestandardowego](../../build-training-data-set.md) w celu uzyskania wskazówek i opcji związanych z zestawem danych szkoleniowych. W tym przewodniku szybki start można użyć plików w folderze **uczenie** [zestawu danych przykładowych](https://go.microsoft.com/fwlink/?linkid=2090451) (pobierz i Wyodrębnij *sample_data.zip*).
* Gdy masz subskrypcję platformy Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" Utwórz zasób aparatu rozpoznawania formularzy "  target="_blank"> Utwórz zasób aparatu rozpoznawania formularza <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który utworzysz, aby połączyć aplikację z interfejsem API rozpoznawania formularzy. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Po zainstalowaniu języka Python można zainstalować najnowszą wersję biblioteki klienta aparatu rozpoznawania formularzy z:

#### <a name="version-30"></a>[Wersja 3,0](#tab/ga)

```console
pip install azure-ai-formrecognizer
```

#### <a name="version-31-preview"></a>[Wersja 3,1 Preview](#tab/preview)

```console
pip install azure-ai-formrecognizer --pre
```

---

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nową aplikację w języku Python przy użyciu preferowanego edytora lub środowiska IDE. Następnie zaimportuj poniższe biblioteki.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_imports)]

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py), która zawiera przykłady kodu w tym przewodniku Szybki Start.


Utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_creds)]


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
> Modele mogą być również przeszkolone przy użyciu graficznego interfejsu użytkownika, takiego jak [Narzędzie do etykietowania aparatów rozpoznawania formularzy](../../quickstarts/label-tool.md).

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań za pomocą biblioteki klienckiej aparatu rozpoznawania formularzy dla języka Python:

#### <a name="version-30"></a>[Wersja 3,0](#tab/ga)

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Rozpoznaj zawartość formularza](#recognize-form-content)
* [Rozpoznawaj potwierdzenia](#recognize-receipts)
* [Trenowanie modelu niestandardowego](#train-a-custom-model)
* [Analizowanie formularzy przy użyciu modelu niestandardowego](#analyze-forms-with-a-custom-model)
* [Zarządzanie modelami niestandardowymi](#manage-your-custom-models)

#### <a name="version-31-preview"></a>[Wersja 3,1 Preview](#tab/preview)

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Rozpoznaj zawartość formularza](#recognize-form-content)
* [Rozpoznawaj potwierdzenia](#recognize-receipts)
* [Rozpoznawanie wizytówek](#recognize-business-cards)
* [Rozpoznaj faktury](#recognize-invoices)
* [Trenowanie modelu niestandardowego](#train-a-custom-model)
* [Analizowanie formularzy przy użyciu modelu niestandardowego](#analyze-forms-with-a-custom-model)
* [Zarządzanie modelami niestandardowymi](#manage-your-custom-models)

---

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

W tym miejscu będziesz uwierzytelniać dwa obiekty klienckie przy użyciu zdefiniowanych powyżej zmiennych. Użyjesz obiektu **AzureKeyCredential** , aby w razie potrzeby można było zaktualizować klucz interfejsu API bez tworzenia nowych obiektów klienta.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_auth)]


## <a name="get-assets-for-testing"></a>Pobierz zasoby do testowania

Musisz dodać odwołania do adresów URL dla danych szkoleniowych i testowych.
* Aby pobrać adres URL sygnatury dostępu współdzielonego dla danych szkolenia modelu niestandardowego, Otwórz Eksplorator usługi Microsoft Azure Storage, kliknij prawym przyciskiem myszy kontener, a następnie wybierz pozycję **Pobierz sygnaturę dostępną**. Upewnij się, że uprawnienia do **odczytu** i **listy** są zaznaczone, a następnie kliknij przycisk **Utwórz**. Następnie skopiuj wartość z sekcji **URL** . Powinna ona mieć postać: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Skorzystaj z obrazów przykładowych z i paragonów zawartych w poniższych przykładach (dostępnych również w witrynie [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) ) lub wykonaj powyższe kroki, aby uzyskać adres URL sygnatury dostępu współdzielonego pojedynczego dokumentu w usłudze BLOB Storage. 

> [!NOTE]
> Fragmenty kodu w tym przewodniku korzystają z formularzy zdalnych, do których uzyskuje dostęp za pomocą adresów URL. Jeśli zamiast tego chcesz przetworzyć lokalne dokumenty formularzy, zapoznaj się z odpowiednimi metodami w [dokumentacji referencyjnej](/python/api/azure-ai-formrecognizer) i [przykładami](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

## <a name="recognize-form-content"></a>Rozpoznaj zawartość formularza

Aparat rozpoznawania formularzy służy do rozpoznawania tabel, wierszy i słów w dokumentach, bez konieczności uczenia modelu.

Aby rozpoznać zawartość pliku pod podanym adresem URL, użyj `begin_recognize_content_from_url` metody. Zwracana wartość jest kolekcją `FormPage` obiektów: jeden dla każdej strony w przesłanym dokumencie. Poniższy kod wykonuje iterację tych obiektów i drukuje wyodrębnione pary klucz/wartość i dane tabeli.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_getcontent)]

> [!TIP]
> Możesz również pobrać zawartość z obrazów lokalnych. Zobacz metody [FormRecognizerClient](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) , takie jak `begin_recognize_content` . Lub zapoznaj się z przykładowym kodem w witrynie [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) , aby poznać scenariusze dotyczące obrazów lokalnych.

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

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_receipts)]

> [!TIP]
> Możesz również rozpoznać lokalne obrazy paragonów. Zobacz metody [FormRecognizerClient](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) , takie jak `begin_recognize_receipts` . Lub zapoznaj się z przykładowym kodem w witrynie [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) , aby poznać scenariusze dotyczące obrazów lokalnych.

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

#### <a name="version-30"></a>[Wersja 3,0](#tab/ga)

#### <a name="version-31-preview"></a>[Wersja 3,1 Preview](#tab/preview)

## <a name="recognize-business-cards"></a>Rozpoznawanie wizytówek

W tej sekcji przedstawiono sposób rozpoznawania i wyodrębniania typowych pól z angielskich kart firmowych przy użyciu wstępnie nauczonego modelu. Aby rozpoznać karty biznesowe na podstawie adresu URL, użyj `begin_recognize_business_cards_from_url` metody. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_bc)]

> [!TIP]
> Możesz również rozpoznać lokalne obrazy kart służbowych. Zobacz metody [FormRecognizerClient](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) , takie jak `begin_recognize_business_cards` . Lub zapoznaj się z przykładowym kodem w witrynie [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) , aby poznać scenariusze dotyczące obrazów lokalnych.

## <a name="recognize-invoices"></a>Rozpoznaj faktury

W tej sekcji przedstawiono sposób rozpoznawania i wyodrębniania typowych pól z faktur sprzedaży przy użyciu wstępnie nauczonego modelu. Aby rozpoznać faktury z adresu URL, należy użyć `begin_recognize_invoices_from_url` metody. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_invoice)]

> [!TIP]
> Możesz również rozpoznać obrazy faktur lokalnych. Zobacz metody [FormRecognizerClient](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) , takie jak `begin_recognize_invoices` . Lub zapoznaj się z przykładowym kodem w witrynie [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) , aby poznać scenariusze dotyczące obrazów lokalnych.

---

## <a name="train-a-custom-model"></a>Trenowanie modelu niestandardowego

W tej sekcji pokazano, jak szkolić model przy użyciu własnych danych. Model przeszkolony może wyprowadzać dane strukturalne, które zawierają relacje klucz/wartość w oryginalnym dokumencie formularza. Po przeprowadzeniu szkolenia modelu można testować i przeszkolić go i ostatecznie użyć do niezawodnego wyodrębnienia danych z większej liczby formularzy zgodnie z potrzebami.

> [!NOTE]
> Możesz również nauczyć modele przy użyciu graficznego interfejsu użytkownika, takiego jak [Narzędzie do próbkowania przykładowego aparatu rozpoznawania formularzy](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Uczenie modelu bez etykiet

Uczenie modeli niestandardowych w celu rozpoznawania wszystkich pól i wartości znalezionych w formularzach niestandardowych bez ręcznego etykietowania dokumentów szkoleniowych.

Poniższy kod używa klienta szkoleniowego z `begin_training` funkcją do uczenia modelu w danym zestawie dokumentów. Zwrócony `CustomFormModel` obiekt zawiera informacje na temat typów formularzy, które może rozpoznać model, oraz pól, które mogą zostać wyodrębnione z każdego typu formularza. Poniższy blok kodu drukuje te informacje w konsoli programu.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_train)]


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

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_trainlabels)]

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

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_analyze)]

> [!TIP]
> Możesz również analizować obrazy lokalne. Zobacz metody [FormRecognizerClient](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) , takie jak `begin_recognize_custom_forms` . Lub zapoznaj się z przykładowym kodem w witrynie [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) , aby poznać scenariusze dotyczące obrazów lokalnych.


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

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_count)]


### <a name="output"></a>Dane wyjściowe

```console
Our account has 5 custom models, and we can have at most 5000 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Wyświetlanie listy modeli przechowywanych obecnie na koncie zasobu

Poniższy blok kodu zawiera listę bieżących modeli na koncie i drukuje ich szczegóły do konsoli programu. Zapisuje także odwołanie do pierwszego modelu.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_list)]


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

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_getmodel)]


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

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_delete)]


## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `python` polecenia w pliku szybkiego startu.

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

### <a name="logging"></a>Rejestrowanie

Ta biblioteka używa [standardowej biblioteki rejestrowania](https://docs.python.org/3/library/logging.html) do rejestrowania. Podstawowe informacje o sesjach HTTP (adresach URL, nagłówkach itd.) są rejestrowane na poziomie informacji.

Szczegółowe rejestrowanie na poziomie debugowania, w tym treści żądania/odpowiedzi i nagłówki unredacted, można włączyć na kliencie z `logging_enable` argumentem słowa kluczowego:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_logging)]


Podobnie, `logging_enable` można włączyć szczegółowe rejestrowanie dla jednej operacji, nawet wtedy, gdy nie jest ona włączona dla klienta:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_example)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto biblioteki klienckiej aparatu rozpoznawania języka Python do uczenia modeli i analizowania formularzy na różne sposoby. Następnie zapoznaj się z poradami, aby utworzyć lepszy zestaw danych szkoleniowych i uzyskać bardziej dokładne modele.

> [!div class="nextstepaction"]
> [Tworzenie zestawu danych szkoleniowych](../../build-training-data-set.md)

* [Co to jest rozpoznawanie formularzy?](../../overview.md)
* Przykładowy kod z tego przewodnika można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py).
