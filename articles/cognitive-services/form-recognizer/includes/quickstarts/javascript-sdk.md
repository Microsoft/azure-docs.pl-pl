---
title: 'Szybki start: Rozpoznawanie formularzy klienta dla języka JavaScript'
description: Użyj biblioteki Rozpoznawanie formularzy dla języka JavaScript, aby utworzyć aplikację do przetwarzania formularzy, która wyodrębnia pary klucz/wartość i dane tabeli z dokumentów niestandardowych.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 04/14/2021
ms.author: lajanuar
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 7098cfbc2fbe2236687eb7d621a0e587497fcebc
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516450"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!IMPORTANT]
>
> * Kod w tym artykule używa metod synchronicznych i niezabędnego magazynu poświadczeń dla uproszczenia. Zapoznaj się z dokumentacją referencyjną poniżej.

[Dokumentacja referencyjna](../../index.yml)  |  [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/)  |  [Pakiet (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— tworzenie bezpłatnej subskrypcji](https://azure.microsoft.com/free/cognitive-services)
* Bieżąca wersja [Node.js](https://nodejs.org/)
* Obiekt blob usługi Azure Storage zawierający zestaw danych szkoleniowych. Aby uzyskać porady i opcje dotyczące kompilowania zestawu danych treningowych, zobacz Build a training data [set for a custom model](../../build-training-data-set.md) (Tworzenie zestawu danych treningowych dla modelu niestandardowego). W tym przewodniku Szybki start możesz użyć plików w folderze **Train** przykładowego zestawu danych [(pobierz](https://go.microsoft.com/fwlink/?linkid=2090451) i *wyodrębnij* sample_data.zip).
* Po utworzeniu subskrypcji platformy Azure utwórz zasób Rozpoznawanie formularzy utwórz zasób usługi Rozpoznawanie formularzy w witrynie Azure Portal, aby uzyskać <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" "  target="_blank"> klucz i punkt </a> końcowy. Po wdrożeniu wybierz pozycję **Przejdź do zasobu**.
  * Klucz i punkt końcowy z zasobu, który utworzysz, będą potrzebne do połączenia aplikacji z Rozpoznawanie formularzy API. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
  * Możesz użyć bezpłatnej warstwy cenowej ( ), aby wypróbować usługę, a następnie przejść na warstwę płatną w `F0` środowisku produkcyjnym.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (takim jak cmd, PowerShell lub Bash) utwórz nowy katalog dla aplikacji i przejdź do niego.

```console
mkdir myapp && cd myapp
```

Uruchom polecenie `npm init` , aby utworzyć aplikację node z `package.json` plikiem.

```console
npm init
```

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

Zainstaluj `ai-form-recognizer` pakiet NPM:

```console
npm install @azure/ai-form-recognizer
```

Plik aplikacji `package.json` zostanie zaktualizowany o zależności.

Utwórz plik o nazwie `index.js` , otwórz go i zaimportuj następujące biblioteki:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_imports)]

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można go znaleźć w witrynie [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js)która zawiera przykłady kodu w tym przewodniku Szybki start.

Utwórz zmienne dla klucza i punktu końcowego platformy Azure zasobu.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli zasób Rozpoznawanie formularzy utworzony w sekcji **Wymagania** wstępne został pomyślnie wdrożony, kliknij przycisk Przejdź do **zasobu** w obszarze Następne **kroki.** Klucz i punkt końcowy można znaleźć  na stronie klucza i punktu końcowego zasobu w obszarze **zarządzanie zasobami**.
>
> Pamiętaj, aby usunąć klucz z kodu, gdy wszystko będzie gotowe, i nigdy nie publikować go publicznie. W środowisku produkcyjnym rozważ użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Zobacz artykuł Cognitive Services [zabezpieczeń,](../../../cognitive-services-security.md) aby uzyskać więcej informacji.

## <a name="object-model"></a>Model obiektów

Za Rozpoznawanie formularzy można utworzyć dwa różne typy klientów. Pierwszy z nich `FormRecognizerClient` służy do wykonywania zapytań w usłudze w celu rozpoznanych pól formularza i zawartości. Druga służy do tworzenia modeli niestandardowych, których można używać do ulepszania rozpoznawania, i zarządzania `FormTrainingClient` nimi.

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` Udostępnia operacje dla:

* Rozpoznawanie pól formularzy i zawartości przy użyciu modeli niestandardowych wytrenowanych do analizowania formularzy niestandardowych. Te wartości są zwracane w kolekcji `RecognizedForm` obiektów.
* Rozpoznawanie zawartości formularza, w tym tabel, linii i słów, bez konieczności trenowania modelu. Zawartość formularza jest zwracana w kolekcji `FormPage` obiektów.
* Rozpoznawanie typowych pól z dokumentów paragonów w USA, wizytówek, faktur i tożsamości przy użyciu wstępnie wytrenowany model w Rozpoznawanie formularzy usługi.

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` Udostępnia operacje dla:

* Trenuj modele niestandardowe w celu analizowania wszystkich pól i wartości znalezionych w formularzach niestandardowych. Zwracany jest typ formularza, który będzie analizowany przez model, oraz pola `CustomFormModel` wyodrębnione dla każdego typu formularza. _Aby_ uzyskać więcej informacji, zobacz dokumentację usługi [dotyczącą trenowania modelu](#train-a-model-without-labels) bez etykiet.
* Trenowanie modeli niestandardowych w celu analizowania określonych pól i wartości określonych przez etykietowanie formularzy niestandardowych. Jest `CustomFormModel` zwracana wartość wskazująca pola, które model wyodrębni, a także szacowaną dokładność dla każdego pola. Zapoznaj się z [dokumentacją usługi dotyczącą](#train-a-model-with-labels) trenowania modelu z etykietami, aby uzyskać bardziej szczegółowe wyjaśnienie stosowania etykiet do zestawu danych treningowych.
* Zarządzanie modelami utworzonymi na koncie.
* Kopiowanie modelu niestandardowego z jednego zasobu Rozpoznawanie formularzy do innego.

> [!NOTE]
> Modele można również wytrenować przy użyciu graficznego interfejsu użytkownika, takiego [jak Rozpoznawanie formularzy Labeling Tool.](../../quickstarts/label-tool.md)

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania za pomocą Rozpoznawanie formularzy klienta dla języka JavaScript:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Analizowanie układu](#analyze-layout)
* [Analizowanie paragonów](#analyze-receipts)
* [Analizowanie wizytówek](#analyze-business-cards)
* [Analizowanie faktur](#analyze-invoices)
* [Analizowanie dokumentów tożsamości](#analyze-identity-documents)
* [Trenowanie modelu niestandardowego](#train-a-custom-model)
* [Analizowanie formularzy za pomocą modelu niestandardowego](#analyze-forms-with-a-custom-model)
* [Zarządzanie modelami niestandardowymi](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Uwierzytelnij obiekt klienta przy użyciu zdefiniowanych zmiennych subskrypcji. Użyjesz obiektu , aby w razie potrzeby można było zaktualizować klucz `AzureKeyCredential` interfejsu API bez tworzenia nowych obiektów klienta. Utworzysz również obiekt klienta trenowania.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_auth)]

## <a name="get-assets-for-testing"></a>Pobierz zasoby do testowania

Musisz również dodać odwołania do adresów URL dla danych szkoleniowych i testowych.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Pobieranie adresu URL sygnatury dostępu współdzielonego":::
* Użyj przykładowych obrazów z przykładów i paragonów zawartych w poniższych przykładach (dostępnych również w witrynie [GitHub)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/test-assets)lub skorzystaj z powyższych kroków, aby uzyskać adres URL sygnatury dostępu współdzielonego pojedynczego dokumentu w magazynie obiektów blob.

## <a name="analyze-layout"></a>Analizowanie układu

Za pomocą Rozpoznawanie formularzy można analizować tabele, wiersze i słowa w dokumentach bez konieczności trenowania modelu. Aby uzyskać więcej informacji na temat wyodrębniania układu, zobacz [Layout conceptual guide (Przewodnik koncepcyjny dotyczący układu).](../../concept-layout.md) Aby przeanalizować zawartość pliku o danym URI, użyj `beginRecognizeContentFromUrl` metody .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_getcontent)]

> [!TIP]
> Zawartość można również pobrać z pliku lokalnego. Zobacz metody [FormRecognizerClient,](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) takie **jak beginRecognizeContent.** Możesz też zobaczyć przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) uzyskać scenariusze obejmujące obrazy lokalne.

### <a name="output"></a>Dane wyjściowe

```console
Page 1: width 8.5 and height 11 with unit inch
cell [0,0] has text Invoice Number
cell [0,1] has text Invoice Date
cell [0,2] has text Invoice Due Date
cell [0,3] has text Charges
cell [0,5] has text VAT ID
cell [1,0] has text 34278587
cell [1,1] has text 6/18/2017
cell [1,2] has text 6/24/2017
cell [1,3] has text $56,651.49
cell [1,5] has text PT
```

## <a name="analyze-receipts"></a>Analizowanie paragonów

W tej sekcji pokazano, jak analizować i wyodrębniać typowe pola z paragonów w USA przy użyciu wstępnie wytrenowany model paragonu. Aby uzyskać więcej informacji na temat analizy paragonów, zobacz [Przewodnik koncepcyjny dotyczący paragonów](../../concept-receipts.md).

Aby analizować paragony z URI, użyj `beginRecognizeReceiptsFromUrl` metody . Poniższy kod przetwarza potwierdzenie przy danym URI i drukuje główne pola i wartości w konsoli.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_receipts)]

> [!TIP]
> Można również analizować lokalne obrazy paragonów. Zobacz metody [FormRecognizerClient,](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) takie **jak beginRecognizeReceipts.** Możesz też zobaczyć przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) uzyskać scenariusze obejmujące obrazy lokalne.

### <a name="output"></a>Dane wyjściowe

```console
status: notStarted
status: running
status: succeeded
First receipt:
  Receipt Type: 'Itemized', with confidence of 0.659
  Merchant Name: 'Contoso Contoso', with confidence of 0.516
  Transaction Date: 'Sun Jun 09 2019 17:00:00 GMT-0700 (Pacific Daylight Time)', with confidence of 0.985
    Item Name: '8GB RAM (Black)', with confidence of 0.916
    Item Name: 'SurfacePen', with confidence of 0.858
  Total: '1203.39', with confidence of 0.774
```

## <a name="analyze-business-cards"></a>Analizowanie wizytówek

W tej sekcji pokazano, jak analizować i wyodrębniać typowe pola z wizytówek w języku angielskim przy użyciu wstępnie wytrenowany model. Aby uzyskać więcej informacji na temat analizy wizytówek, zobacz przewodnik koncepcyjny [Dotyczący wizytówek.](../../concept-business-cards.md)

Aby analizować wizytówki z adresu URL, użyj `beginRecognizeBusinessCardsFromURL` metody .

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js" id="snippet_bc":::

> [!TIP]
> Można również analizować lokalne obrazy wizytówków. Zobacz metody [FormRecognizerClient,](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) takie **jak beginRecognizeBusinessCards.** Możesz też zobaczyć przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) uzyskać scenariusze obejmujące obrazy lokalne.

## <a name="analyze-invoices"></a>Analizowanie faktur

W tej sekcji pokazano, jak analizować i wyodrębniać typowe pola z faktur sprzedaży przy użyciu wstępnie wytrenowany model. Aby uzyskać więcej informacji na temat analizy faktur, zobacz [Przewodnik koncepcyjny dotyczący faktur](../../concept-invoices.md).

Aby analizować faktury na podstawie adresu URL, użyj `beginRecognizeInvoicesFromUrl` metody .

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js" id="snippet_invoice":::

> [!TIP]
> Można również analizować lokalne obrazy wizytówków. Zobacz metody [FormRecognizerClient,](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) takie **jak beginRecognizeInvoices.** Możesz też zobaczyć przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) uzyskać scenariusze obejmujące obrazy lokalne.

## <a name="analyze-identity-documents"></a>Analizowanie dokumentów tożsamości

W tej sekcji pokazano, jak analizować i wyodrębniać kluczowe informacje z dokumentów identyfikacyjnych wystawionych przez rząd — na całym świecie paszportów i licencji kierowcy w Stanach Zjednoczonych — przy użyciu Rozpoznawanie formularzy wstępnie utworzonego modelu identyfikatorów. Aby uzyskać więcej informacji na temat analizy faktur, zobacz nasz przewodnik koncepcyjny dotyczący [wstępnie utworzonego modelu identyfikacji](../../concept-identification-cards.md).

Aby analizować dokumenty tożsamości z adresu URL, użyj `beginRecognizeIdDocumentsFromUrl` metody .

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js" id="snippet_id":::

## <a name="train-a-custom-model"></a>Trenowanie modelu niestandardowego

W tej sekcji pokazano, jak trenować model przy użyciu własnych danych. Wytrenowany model może wyprowadzać dane ustrukturyzowane, które obejmują relacje klucz/wartość w oryginalnym dokumencie formularza. Po wytrenowania modelu można go przetestować i ponownie wytrenować, a następnie użyć go do niezawodnego wyodrębniania danych z większej liczby formularzy zgodnie z potrzebami.

> [!NOTE]
> Modele można również szkolić za pomocą graficznego interfejsu użytkownika, takiego [jak Rozpoznawanie formularzy przykładowe narzędzie do etykietowania](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Trenowanie modelu bez etykiet

Trenowanie modeli niestandardowych w celu analizowania wszystkich pól i wartości znalezionych w formularzach niestandardowych bez ręcznego etykietowania dokumentów szkoleniowych.

Następująca funkcja szkoli model na podstawie danego zestawu dokumentów i drukuje stan modelu w konsoli.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_train)]

### <a name="output"></a>Dane wyjściowe

Są to dane wyjściowe dla modelu przeszkolonego przy użyciu danych szkoleniowych dostępnych z zestawu [SDK języka JavaScript.](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer) Te przykładowe dane wyjściowe zostały obcięte w celu ich czytelności.

```console
training status: creating
training status: ready
Model ID: 9d893595-1690-4cf2-a4b1-fbac0fb11909
Status: ready
Training started on: Thu Aug 20 2020 20:27:26 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:27:37 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'field-0'
The model found field 'field-1'
The model found field 'field-2'
The model found field 'field-3'
The model found field 'field-4'
The model found field 'field-5'
The model found field 'field-6'
The model found field 'field-7'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors:
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors:
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors:
...
```

### <a name="train-a-model-with-labels"></a>Trenowanie modelu przy użyciu etykiet

Możesz również szkolić modele niestandardowe, ręcznie oznaczając etykietami dokumenty szkoleniowe. Trenowania za pomocą etykiet prowadzi do lepszej wydajności w niektórych scenariuszach. Aby trenować przy użyciu etykiet, musisz mieć specjalne pliki z informacjami o etykietach ( ) w kontenerze `\<filename\>.pdf.labels.json` magazynu obiektów blob obok dokumentów szkoleniowych. Przykładowe [Rozpoznawanie formularzy przykładowe narzędzie do etykietowania](../../quickstarts/label-tool.md) udostępnia interfejs użytkownika ułatwiający tworzenie tych plików etykiet. Gdy już je masz, możesz wywołać metodę `beginTraining` z `uselabels` parametrem ustawionym na `true` .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_trainlabels)]

### <a name="output"></a>Dane wyjściowe

Są to dane wyjściowe dla modelu przeszkolonego przy użyciu danych szkoleniowych dostępnych z zestawu [SDK języka JavaScript.](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) Te przykładowe dane wyjściowe zostały obcięte w celu ich czytelności.

```console
training status: creating
training status: ready
Model ID: 789b1b37-4cc3-4e36-8665-9dde68618072
Status: ready
Training started on: Thu Aug 20 2020 20:30:37 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:30:43 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'CompanyAddress'
The model found field 'CompanyName'
The model found field 'CompanyPhoneNumber'
The model found field 'DatedAs'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
...
```

## <a name="analyze-forms-with-a-custom-model"></a>Analizowanie formularzy za pomocą modelu niestandardowego

W tej sekcji pokazano, jak wyodrębniać informacje o kluczu/wartości i inną zawartość z niestandardowych typów formularzy przy użyciu modeli przeszkolonych przy użyciu własnych formularzy.

> [!IMPORTANT]
> Aby zaimplementować ten scenariusz, musisz już wytrenować model, aby można było przekazać jego identyfikator do poniższej metody. Zobacz [sekcję Train a model (Trenowanie](#train-a-model-without-labels) modelu).

Użyjesz metody `beginRecognizeCustomFormsFromUrl` . Zwrócona wartość jest kolekcją `RecognizedForm` obiektów: po jednym dla każdej strony w przesłanym dokumencie.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_analyze)]

> [!TIP]
> Można również analizować pliki lokalne. Zobacz metody [FormRecognizerClient,](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) takie **jak beginRecognizeCustomForms.** Możesz też zobaczyć przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) uzyskać scenariusze obejmujące obrazy lokalne.

### <a name="output"></a>Dane wyjściowe

```console
status: notStarted
status: succeeded
Forms:
custom:form, page range: [object Object]
Pages:
Page number: 1
Tables
cell (0,0) Invoice Number
cell (0,1) Invoice Date
cell (0,2) Invoice Due Date
cell (0,3) Charges
cell (0,5) VAT ID
cell (1,0) 34278587
cell (1,1) 6/18/2017
cell (1,2) 6/24/2017
cell (1,3) $56,651.49
cell (1,5) PT
Fields:
Field Merchant has value 'Invoice For:' with a confidence score of 0.116
Field CompanyPhoneNumber has value '$56,651.49' with a confidence score of 0.249
Field VendorName has value 'Charges' with a confidence score of 0.145
Field CompanyAddress has value '1 Redmond way Suite 6000 Redmond, WA' with a confidence score of 0.258
Field CompanyName has value 'PT' with a confidence score of 0.245
Field Website has value '99243' with a confidence score of 0.114
Field DatedAs has value 'undefined' with a confidence score of undefined
Field Email has value 'undefined' with a confidence score of undefined
Field PhoneNumber has value 'undefined' with a confidence score of undefined
Field PurchaseOrderNumber has value 'undefined' with a confidence score of undefined
Field Quantity has value 'undefined' with a confidence score of undefined
Field Signature has value 'undefined' with a confidence score of undefined
Field Subtotal has value 'undefined' with a confidence score of undefined
Field Tax has value 'undefined' with a confidence score of undefined
Field Total has value 'undefined' with a confidence score of undefined
```

## <a name="manage-your-custom-models"></a>Zarządzanie modelami niestandardowymi

W tej sekcji pokazano, jak zarządzać modelami niestandardowymi przechowywanymi na koncie. Poniższy kod na przykład umożliwia wykonywanie wszystkich zadań zarządzania modelami w jednej funkcji.

### <a name="get-number-of-models"></a>Uzyskiwanie liczby modeli

Poniższy blok kodu pobiera liczbę modeli aktualnie dostępnych na Twoim koncie.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_count)]

### <a name="get-list-of-models-in-account"></a>Uzyskiwanie listy modeli na koncie

Poniższy blok kodu zawiera pełną listę dostępnych modeli na Twoim koncie, w tym informacje o czasie utworzenia modelu i jego bieżącym stanie.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_list)]

### <a name="output"></a>Dane wyjściowe

```console
model 0:
{
  modelId: '453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e',
  status: 'invalid',
  trainingStartedOn: 2020-08-20T22:28:52.000Z,
  trainingCompletedOn: 2020-08-20T22:28:53.000Z
}
model 1:
{
  modelId: '628739de-779c-473d-8214-d35c72d3d4f7',
  status: 'ready',
  trainingStartedOn: 2020-08-20T23:16:51.000Z,
  trainingCompletedOn: 2020-08-20T23:16:59.000Z
}
model 2:
{
  modelId: '789b1b37-4cc3-4e36-8665-9dde68618072',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:30:37.000Z,
  trainingCompletedOn: 2020-08-21T03:30:43.000Z
}
model 3:
{
  modelId: '9d893595-1690-4cf2-a4b1-fbac0fb11909',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:27:26.000Z,
  trainingCompletedOn: 2020-08-21T03:27:37.000Z
}
```

### <a name="get-list-of-model-ids-by-page"></a>Uzyskiwanie listy identyfikatorów modeli według strony

Ten blok kodu zawiera listę modeli i identyfikatorów modeli podzielonych na strony.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_listpages)]

### <a name="output"></a>Dane wyjściowe

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
```

### <a name="get-model-by-id"></a>Uzyskiwanie modelu według identyfikatora

Następująca funkcja pobiera zgodny obiekt modelu z identyfikatorem modelu. Ta funkcja nie jest domyślnie wywoływana.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_getmodel)]

### <a name="delete-a-model-from-the-resource-account"></a>Usuwanie modelu z konta zasobu

Możesz również usunąć model z konta, odwołując się do jego identyfikatora. Ta funkcja usuwa model z danym identyfikatorem. Ta funkcja nie jest domyślnie wywoływana.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_delete)]

### <a name="output"></a>Dane wyjściowe

```console
Model with id 789b1b37-4cc3-4e36-8665-9dde68618072 has been deleted
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `node` polecenia w pliku szybkiego startu.

```console
node index.js
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services zasobów, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie wszystkich innych skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="enable-logs"></a>Włączanie dzienników

Możesz ustawić następującą zmienną środowiskową, aby wyświetlić dzienniki debugowania podczas korzystania z tej biblioteki.

```console
export DEBUG=azure*
```

Aby uzyskać bardziej szczegółowe instrukcje dotyczące włączania dzienników, zobacz [ @azure/logger dokumenty pakietu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start za pomocą biblioteki Rozpoznawanie formularzy JavaScript do trenowania modeli i analizowania formularzy na różne sposoby. Następnie zapoznaj się z poradami dotyczącymi tworzenia lepszego zestawu danych treningowych i tworzenia dokładniej określonych modeli.

> [!div class="nextstepaction"]
> [Tworzenie zestawu danych szkoleniowych](../../build-training-data-set.md)

## <a name="see-also"></a>Zobacz też

* [Co to jest rozpoznawanie formularzy?](../../overview.md)
* Przykładowy kod z tego przewodnika można znaleźć w witrynie [GitHub.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js)