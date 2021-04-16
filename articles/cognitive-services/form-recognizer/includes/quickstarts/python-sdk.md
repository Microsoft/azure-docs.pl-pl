---
title: 'Szybki start: Rozpoznawanie formularzy klienta dla języka Python'
description: Użyj biblioteki Rozpoznawanie formularzy dla języka Python, aby utworzyć aplikację do przetwarzania formularzy, która wyodrębnia pary klucz/wartość i dane tabeli z dokumentów niestandardowych.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 04/09/2021
ms.author: lajanuar
ms.openlocfilehash: 606755333856f6dd97ab6c5158ac67f122a1237d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516463"
---
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!IMPORTANT]
>
> * Kod w tym artykule używa metod synchronicznych i niezabędnego magazynu poświadczeń dla uproszczenia. Zapoznaj się z dokumentacją referencyjną poniżej.

[Dokumentacja referencyjna](/python/api/azure-ai-formrecognizer)  |  [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer)  |  [Pakiet (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
  * Instalacja języka Python powinna zawierać adres [pip](https://pip.pypa.io/en/stable/). Możesz sprawdzić, czy masz zainstalowany program pip, `pip --version` uruchamiając polecenie w wierszu polecenia. Pobierz program pip, instalując najnowszą wersję języka Python.
* Obiekt blob usługi Azure Storage zawierający zestaw danych szkoleniowych. Aby uzyskać porady i opcje dotyczące budowania zestawu danych treningowych, zobacz Build a training data [set for a custom model](../../build-training-data-set.md) (Tworzenie zestawu danych treningowych dla modelu niestandardowego). W tym przewodniku Szybki start możesz użyć plików w folderze **Train** przykładowego zestawu danych [(pobierz](https://go.microsoft.com/fwlink/?linkid=2090451) i *wyodrębnij* sample_data.zip).
* Po utworzeniu subskrypcji platformy Azure utwórz zasób Rozpoznawanie formularzy zasobów Rozpoznawanie formularzy w witrynie Azure Portal, aby uzyskać <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" "  target="_blank"> klucz i punkt </a> końcowy. Po wdrożeniu kliknij pozycję **Przejdź do zasobu**.
  * Klucz i punkt końcowy z zasobu, który utworzysz, będą potrzebne do połączenia aplikacji z Rozpoznawanie formularzy API. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
  * Możesz użyć bezpłatnej warstwy cenowej ( ), aby wypróbować usługę, a następnie uaktualnić ją do warstwy `F0` płatnej w środowisku produkcyjnym.

## <a name="setting-up"></a>Konfigurowanie

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

Po zainstalowaniu języka Python możesz zainstalować najnowszą wersję biblioteki klienta Rozpoznawanie formularzy za pomocą:

#### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/preview)

```console
pip install azure-ai-formrecognizer --pre
```

> [!NOTE]
> Wersja Rozpoznawanie formularzy 3.1.0b4 jest najnowszą wersją zapoznawczą zestawu SDK i odzwierciedla interfejs API w wersji _2.1 (wersja zapoznawcza 3)._

#### <a name="v20"></a>[Wersja 2.0](#tab/ga)

```console
pip install azure-ai-formrecognizer
```

> [!NOTE]
> Zestaw SDK Rozpoznawanie formularzy 3.0.0 odzwierciedla interfejs API w wersji 2.0

---

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nową aplikację w języku Python w preferowanym edytorze lub środowisku IDE. Następnie zaimportuj następujące biblioteki.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_imports)]

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można go znaleźć w witrynie [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py)która zawiera przykłady kodu w tym przewodniku Szybki start.

Utwórz zmienne dla klucza i punktu końcowego platformy Azure zasobu.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_creds)]

## <a name="object-model"></a>Model obiektów

Za Rozpoznawanie formularzy można utworzyć dwa różne typy klientów. Pierwszy z nich `form_recognizer_client` służy do wykonywania zapytań w usłudze w celu rozpoznawania pól formularza i zawartości. Druga służy do tworzenia modeli niestandardowych, których można używać do ulepszania rozpoznawania, i zarządzania `form_training_client` nimi. 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`form_recognizer_client` Udostępnia operacje dla:

* Rozpoznawanie pól formularzy i zawartości przy użyciu modeli niestandardowych wytrenowanych do analizowania formularzy niestandardowych.
* Rozpoznawanie zawartości formularza, w tym tabel, linii i słów, bez konieczności trenowania modelu.
* Rozpoznawanie typowych pól z paragonów przy użyciu wstępnie wytrenowany model paragonu w Rozpoznawanie formularzy paragonu.

### <a name="formtrainingclient"></a>FormTrainingClient

`form_training_client` Udostępnia operacje dla:

* Trenuj modele niestandardowe w celu analizowania wszystkich pól i wartości znalezionych w formularzach niestandardowych. Zapoznaj się z dokumentacją usługi [dotyczącą trenowania](#train-a-model-without-labels) modelu bez etykiet, aby uzyskać bardziej szczegółowe wyjaśnienie dotyczące tworzenia zestawu danych treningowych.
* Trenowanie modeli niestandardowych w celu analizowania określonych pól i wartości określonych przez etykietowanie formularzy niestandardowych. Zapoznaj się z dokumentacją usługi [dotyczącą trenowania](#train-a-model-with-labels) modelu z etykietami, aby uzyskać bardziej szczegółowe wyjaśnienie stosowania etykiet do zestawu danych treningowych.
* Zarządzanie modelami utworzonymi na koncie.
* Kopiowanie modelu niestandardowego z jednego zasobu Rozpoznawanie formularzy do innego.

> [!NOTE]
> Modele można również wytrenować przy użyciu graficznego interfejsu użytkownika, takiego [jak Rozpoznawanie formularzy Labeling Tool.](../../quickstarts/label-tool.md)

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania przy użyciu Rozpoznawanie formularzy klienta dla języka Python:
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
#### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/preview)

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Analizowanie układu](#analyze-layout)
* [Analizowanie paragonów](#analyze-receipts)
* [Analizowanie wizytówek](#analyze-business-cards)
* [Analizowanie faktur](#analyze-invoices)
* [Analizowanie dokumentów tożsamości](#analyze-identity-documents)
* [Analizowanie formularzy za pomocą modelu niestandardowego](#analyze-forms-with-a-custom-model)
* [Trenowanie modelu niestandardowego](#train-a-custom-model)
* [Zarządzanie modelami niestandardowymi](#manage-your-custom-models)

#### <a name="v20"></a>[Wersja 2.0](#tab/ga)

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Analizowanie układu](#analyze-layout)
* [Analizowanie paragonów](#analyze-receipts)
* [Trenowanie modelu niestandardowego](#train-a-custom-model)
* [Analizowanie formularzy za pomocą modelu niestandardowego](#analyze-forms-with-a-custom-model)
* [Zarządzanie modelami niestandardowymi](#manage-your-custom-models)

---

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

W tym miejscu uwierzytelnisz dwa obiekty klienta przy użyciu zdefiniowanych powyżej zmiennych subskrypcji. Użyjesz obiektu **AzureKeyCredential,** aby w razie potrzeby można było zaktualizować klucz interfejsu API bez tworzenia nowych obiektów klienta.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_auth)]

## <a name="get-assets-for-testing"></a>Pobierz zasoby do testowania

Musisz dodać odwołania do adresów URL dla danych szkoleniowych i testowych.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Pobieranie adresu URL sygnatury dostępu współdzielonego":::

* Użyj przykładowego formularza i obrazów paragonów zawartych w poniższych przykładach (dostępnych również w witrynie [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) lub możesz użyć powyższych kroków, aby uzyskać adres URL sygnatury dostępu współdzielonego pojedynczego dokumentu w magazynie obiektów blob). 

> [!NOTE]
> Fragmenty kodu w tym przewodniku używają zdalnych formularzy, do których dostęp są uzyskiwane za pomocą adresów URL. Jeśli zamiast tego chcesz przetworzyć dokumenty formularza lokalnego, zapoznaj się z powiązanymi metodami w dokumentacji referencyjnej [i](/python/api/azure-ai-formrecognizer) [przykładach](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

## <a name="analyze-layout"></a>Analizowanie układu

Za pomocą Rozpoznawanie formularzy można analizować tabele, linie i wyrazy w dokumentach bez konieczności trenowania modelu. Aby uzyskać więcej informacji na temat wyodrębniania układu, zobacz [Przewodnik koncepcyjny układu](../../concept-layout.md).

Aby przeanalizować zawartość pliku pod danym adresem URL, użyj `begin_recognize_content_from_url` metody . Zwrócona wartość jest kolekcją `FormPage` obiektów: po jednym dla każdej strony w przesłanym dokumencie. Poniższy kod iteruje po tych obiektach i drukuje wyodrębnione pary klucz/wartość i dane tabeli.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_getcontent)]

> [!TIP]
> Możesz również pobrać zawartość z obrazów lokalnych. Zobacz metody [FormRecognizerClient,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) takie jak `begin_recognize_content` . Możesz też zobaczyć przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) uzyskać scenariusze obejmujące obrazy lokalne.

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

## <a name="analyze-receipts"></a>Analizowanie paragonów

W tej sekcji pokazano, jak analizować i wyodrębniać typowe pola z paragonów w USA przy użyciu wstępnie wytrenowanych modeli paragonów. Aby uzyskać więcej informacji na temat analizy paragonów, zobacz Przewodnik koncepcyjny [dotyczący paragonów](../../concept-receipts.md). Aby analizować paragony z adresu URL, użyj `begin_recognize_receipts_from_url` metody .

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_receipts)]

> [!TIP]
> Można również analizować lokalne obrazy paragonów. Zobacz metody [FormRecognizerClient,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) takie jak `begin_recognize_receipts` . Możesz też zobaczyć przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) uzyskać scenariusze obejmujące obrazy lokalne.

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

## <a name="analyze-business-cards"></a>Analizowanie wizytówek

#### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/preview)

W tej sekcji pokazano, jak analizować i wyodrębniać typowe pola z wizytówek w języku angielskim przy użyciu wstępnie wytrenowany model. Aby uzyskać więcej informacji na temat analizy wizytówek, zobacz przewodnik koncepcyjny [Dotyczący wizytówek.](../../concept-business-cards.md) 

Aby analizować wizytówki z adresu URL, użyj `begin_recognize_business_cards_from_url` metody .

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_bc)]

> [!TIP]
> Można również analizować lokalne obrazy wizytówków. Zobacz metody [FormRecognizerClient,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) takie jak `begin_recognize_business_cards` . Możesz też zobaczyć przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) uzyskać scenariusze obejmujące obrazy lokalne.

#### <a name="v20"></a>[Wersja 2.0](#tab/ga)

> [!IMPORTANT]
> Ta funkcja nie jest dostępna w wybranej wersji interfejsu API.

---

## <a name="analyze-invoices"></a>Analizowanie faktur

#### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/preview)

W tej sekcji pokazano, jak analizować i wyodrębniać typowe pola z faktur sprzedaży przy użyciu wstępnie wytrenowany model. Aby uzyskać więcej informacji na temat analizy faktur, zobacz [Przewodnik koncepcyjny dotyczący faktur](../../concept-invoices.md). 

Aby analizować faktury na podstawie adresu URL, użyj `begin_recognize_invoices_from_url` metody .

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_invoice)]

> [!TIP]
> Można również analizować lokalne obrazy faktur. Zobacz metody [FormRecognizerClient,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) takie jak `begin_recognize_invoices` . Możesz też zobaczyć przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) uzyskać scenariusze obejmujące obrazy lokalne.

#### <a name="v20"></a>[Wersja 2.0](#tab/ga)

> [!IMPORTANT]
> Ta funkcja nie jest dostępna w wybranej wersji interfejsu API.

---

## <a name="analyze-identity-documents"></a>Analizowanie dokumentów tożsamości

#### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/preview)

W tej sekcji pokazano, jak analizować i wyodrębniać kluczowe informacje z dokumentów identyfikacyjnych wystawionych przez rząd — na całym świecie paszportów i prawa jazdy w Stanach Zjednoczonych — przy użyciu Rozpoznawanie formularzy wstępnie utworzonego modelu identyfikatorów. Aby uzyskać więcej informacji na temat analizy faktur, zobacz nasz przewodnik koncepcyjny dotyczący wstępnie [utworzonego modelu identyfikacji](../../concept-identification-cards.md).

Aby analizować dokumenty tożsamości z adresu URL, użyj `begin_recognize_id_documents_from_url` metody .

:::code language="python" source="~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py" id="snippet_id":::

> [!TIP]
> Można również analizować obrazy dokumentów tożsamości. _Zobacz_ metody [FormRecognizerClient,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true#methods) takie jak `begin_recognize_id_documents` . _Zobacz również przykładowy_ kod w witrynie [GitHub, aby uzyskać](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) scenariusze obejmujące obrazy lokalne.

#### <a name="v20"></a>[Wersja 2.0](#tab/ga)

> [!IMPORTANT]
> Ta funkcja nie jest dostępna w wybranej wersji interfejsu API.

---

## <a name="train-a-custom-model"></a>Trenowanie modelu niestandardowego

W tej sekcji pokazano, jak trenować model przy użyciu własnych danych. Wytrenowany model może wyprowadzać dane ustrukturyzowane, które obejmują relacje klucz/wartość w oryginalnym dokumencie formularza. Po wytrenowania modelu można go przetestować i ponownie wytrenować, a następnie użyć go do niezawodnego wyodrębniania danych z większej liczby formularzy zgodnie z potrzebami.

> [!NOTE]
> Modele można również szkolić za pomocą graficznego interfejsu użytkownika, takiego [jak Rozpoznawanie formularzy przykładowe narzędzie do etykietowania](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Trenowanie modelu bez etykiet

Trenowanie modeli niestandardowych w celu analizowania wszystkich pól i wartości znalezionych w formularzach niestandardowych bez ręcznego etykietowania dokumentów szkoleniowych.

Poniższy kod używa klienta trenowania z funkcją `begin_training` do trenowania modelu na danym zestawie dokumentów. Zwrócony obiekt zawiera informacje o typach formularzy, które model może analizować, oraz polach, które może `CustomFormModel` wyodrębnić z każdego typu formularza. Poniższy blok kodu drukuje te informacje w konsoli.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_train)]

### <a name="output"></a>Dane wyjściowe

Są to dane wyjściowe dla modelu wytrenowane przy użyciu danych szkoleniowych dostępnych z zestawu [SDK języka Python.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training)

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

### <a name="train-a-model-with-labels"></a>Trenowanie modelu przy użyciu etykiet

Modele niestandardowe można również trenować, ręcznie oznaczając etykietami dokumenty szkoleniowe. Trenowania za pomocą etykiet prowadzi do lepszej wydajności w niektórych scenariuszach. Zwrócona `CustomFormModel` wartość wskazuje pola, które model może wyodrębnić, wraz z szacowaną dokładnością w każdym polu. Poniższy blok kodu drukuje te informacje w konsoli.

> [!IMPORTANT]
> Aby trenować za pomocą etykiet, musisz mieć specjalne pliki z informacjami o etykietach ( ) w kontenerze `\<filename\>.pdf.labels.json` magazynu obiektów blob obok dokumentów szkoleniowych. Przykładowe [Rozpoznawanie formularzy etykietowania](../../quickstarts/label-tool.md) udostępnia interfejs użytkownika ułatwiający tworzenie tych plików etykiet. Gdy już je masz, możesz wywołać funkcję z `begin_training` *parametrem use_training_labels* ustawionym na `true` .

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_trainlabels)]

### <a name="output"></a>Dane wyjściowe

Są to dane wyjściowe dla modelu wytrenowane przy użyciu danych szkoleniowych dostępnych z zestawu [SDK języka Python.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training)

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

## <a name="analyze-forms-with-a-custom-model"></a>Analizowanie formularzy za pomocą modelu niestandardowego

W tej sekcji pokazano, jak wyodrębniać informacje o kluczu/wartości i inną zawartość z niestandardowych typów formularzy przy użyciu modeli przeszkolonych przy użyciu własnych formularzy.

> [!IMPORTANT]
> Aby zaimplementować ten scenariusz, musisz już wytrenować model, aby można było przekazać jego identyfikator do poniższej metody. Zobacz [sekcję Train a model (Trenowanie](#train-a-model-without-labels) modelu).

Użyjesz metody `begin_recognize_custom_forms_from_url` . Zwrócona wartość jest kolekcją `RecognizedForm` obiektów: po jednym dla każdej strony w przesłanym dokumencie. Poniższy kod drukuje wyniki analizy w konsoli. Drukuje każde rozpoznane pole i odpowiadającą mu wartość wraz z wynikiem ufności.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_analyze)]

> [!TIP]
> Można również analizować obrazy lokalne. Zobacz metody [FormRecognizerClient,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) takie jak `begin_recognize_custom_forms` . Możesz też zobaczyć przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) uzyskać scenariusze obejmujące obrazy lokalne.

### <a name="output"></a>Dane wyjściowe

Korzystając z modelu z poprzedniego przykładu, podano następujące dane wyjściowe.

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

W tej sekcji pokazano, jak zarządzać modelami niestandardowymi przechowywanymi na koncie.

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Sprawdzanie liczby modeli na koncie zasobu FormRecognizer

Poniższy blok kodu sprawdza liczbę modeli zapisanych na koncie Rozpoznawanie formularzy i porównuje go z limitem konta.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_count)]


### <a name="output"></a>Dane wyjściowe

```console
Our account has 5 custom models, and we can have at most 5000 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Lista modeli aktualnie przechowywanych na koncie zasobu

Poniższy blok kodu wyświetla listę bieżących modeli na Twoim koncie i wyświetla ich szczegóły w konsoli. Zapisuje również odwołanie do pierwszego modelu.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_list)]


### <a name="output"></a>Dane wyjściowe

Są to przykładowe dane wyjściowe dla konta testowego.

```console
We have models with the following ids:
453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
628739de-779c-473d-8214-d35c72d3d4f7
ae636292-0b14-4e26-81a7-a0bfcbaf7c91
b4b5df77-8538-4ffb-a996-f67158ecd305
c6309148-6b64-4fef-aea0-d39521452699
```

### <a name="get-a-specific-model-using-the-models-id"></a>Uzyskiwanie określonego modelu przy użyciu identyfikatora modelu

Poniższy blok kodu używa identyfikatora modelu zapisanego w poprzedniej sekcji i używa go do pobrania szczegółowych informacji o modelu.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_getmodel)]


### <a name="output"></a>Dane wyjściowe

Są to przykładowe dane wyjściowe dla modelu niestandardowego utworzonego w poprzednim przykładzie.

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

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie wszystkich innych skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="general"></a>Ogólne

Biblioteka Rozpoznawanie formularzy klienta będzie zgłaszać wyjątki zdefiniowane w [usłudze Azure Core.](https://aka.ms/azsdk-python-azure-core)

### <a name="logging"></a>Rejestrowanie

Ta biblioteka używa [standardowej biblioteki rejestrowania](https://docs.python.org/3/library/logging.html) do rejestrowania. Podstawowe informacje o sesjach HTTP (adresy URL, nagłówki itp.) są rejestrowane na poziomie INFORMACJI.

Szczegółowe rejestrowanie na poziomie DEBUGOWANIA, w tym treści żądań/odpowiedzi i nierozpoznanych nagłówków, można włączyć na kliencie za pomocą `logging_enable` argumentu słowa kluczowego:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_logging)]


Podobnie program może włączyć szczegółowe rejestrowanie dla jednej operacji, nawet jeśli `logging_enable` nie jest włączona dla klienta:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_example)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start do trenowania modeli i analizowania formularzy na różne sposoby Rozpoznawanie formularzy biblioteki klienta języka Python. Następnie zapoznaj się z poradami dotyczącymi tworzenia lepszego zestawu danych treningowych i tworzenia dokładniej określonych modeli.

> [!div class="nextstepaction"]
> [Tworzenie zestawu danych szkoleniowych](../../build-training-data-set.md)

* [Co to jest rozpoznawanie formularzy?](../../overview.md)
* Przykładowy kod z tego przewodnika można znaleźć w witrynie [GitHub.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py)
