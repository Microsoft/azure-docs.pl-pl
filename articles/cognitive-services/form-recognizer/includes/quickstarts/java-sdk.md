---
title: 'Szybki start: Rozpoznawanie formularzy klienta dla języka Java'
description: Użyj biblioteki Rozpoznawanie formularzy dla języka Java, aby utworzyć aplikację do przetwarzania formularzy, która wyodrębnia pary klucz/wartość i dane tabeli z dokumentów niestandardowych.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 04/14/2021
ms.custom: devx-track-java
ms.author: lajanuar
ms.openlocfilehash: cd5e6383e71e3f37a26b866156b64c86302f6990
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516449"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!IMPORTANT]
> Kod w tym artykule używa metod synchronicznych i niezabędnego magazynu poświadczeń dla uproszczenia.

[Dokumentacja referencyjna](/java/api/overview/azure/ai-formrecognizer-readme)  |  [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src)  |  [Pakiet (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— tworzenie bezpłatnej subskrypcji](https://azure.microsoft.com/free/cognitive-services)
* Bieżąca wersja [zestawu Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Narzędzie [kompilacji Gradle](https://gradle.org/install/)lub inny menedżer zależności.
* Po utworzeniu subskrypcji platformy Azure utwórz zasób Rozpoznawanie formularzy utwórz zasób usługi Rozpoznawanie formularzy w witrynie Azure Portal, aby uzyskać <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" "  target="_blank"> klucz i punkt </a> końcowy. Po wdrożeniu kliknij pozycję **Przejdź do zasobu**.
  * Klucz i punkt końcowy z zasobu, który utworzysz, będą potrzebne do połączenia aplikacji z Rozpoznawanie formularzy API. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
  * Możesz użyć bezpłatnej warstwy cenowej ( ), aby wypróbować usługę, a następnie przejść na warstwę płatną w `F0` środowisku produkcyjnym.
* Obiekt blob usługi Azure Storage zawierający zestaw danych szkoleniowych. Aby uzyskać porady i opcje dotyczące kompilowania zestawu danych treningowych, zobacz Build a training data [set for a custom model](../../build-training-data-set.md) (Tworzenie zestawu danych treningowych dla modelu niestandardowego). W tym przewodniku Szybki start możesz użyć plików w folderze **Train** przykładowego zestawu danych [(pobierz](https://go.microsoft.com/fwlink/?linkid=2090451) i *wyodrębnij* sample_data.zip).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-gradle-project"></a>Tworzenie nowego projektu gradle

W oknie konsoli (takim jak cmd, PowerShell lub Bash) utwórz nowy katalog dla aplikacji i przejdź do niego.

```console
mkdir myapp && cd myapp
```

Uruchom polecenie `gradle init` z katalogu roboczego. To polecenie spowoduje utworzenie podstawowych plików kompilacji dla programu Gradle, w tym *pliku build.gradle.kts,* który jest używany w czasie wykonywania do tworzenia i konfigurowania aplikacji.

```console
gradle init --type basic
```

Po wyświetleniu monitu wybierz pozycję **Język DSL**, a następnie **Kotlin**.

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

W tym przewodniku Szybki start jest używany menedżer zależności gradle. Bibliotekę klienta i informacje dla innych menedżerów zależności można znaleźć w centralnym [repozytorium maven.](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer)

W pliku *build.gradle.kts* projektu dołącz bibliotekę klienta jako instrukcje wraz z wymaganymi `implementation` wtyczkami i ustawieniami.

#### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/preview)

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.1.0-beta.3")
}
```

> [!NOTE]
> Zestaw SDK Rozpoznawanie formularzy 3.1.0-beta.3 odzwierciedla interfejs API w wersji _2.1-preview.3._

#### <a name="v20"></a>[Wersja 2.0](#tab/ga)

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.1.0-beta.3")
}
```

> [!NOTE]
> Zestaw SDK Rozpoznawanie formularzy 3.0.0 odzwierciedla interfejs API w wersji 2.1-preview.3

---

### <a name="create-a-java-file"></a>Tworzenie pliku języka Java


Z katalogu roboczego uruchom następujące polecenie:

```console
mkdir -p src/main/java
```

Przejdź do nowego folderu i utwórz plik o nazwie *FormRecognizer.java.* Otwórz go w preferowanym edytorze lub w środowiskach IDE i dodaj następujące `import` instrukcje:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_imports)]

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można go znaleźć w witrynie [GitHub,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/FormRecognizer/FormRecognizer.java)która zawiera przykłady kodu w tym przewodniku Szybki start.


W klasie **FormRecognizer** aplikacji utwórz zmienne dla klucza i punktu końcowego zasobu.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_creds)]

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli zasób Rozpoznawanie formularzy utworzony w sekcji **Wymagania** wstępne został pomyślnie wdrożony, kliknij przycisk Przejdź do **zasobu** w obszarze Następne **kroki.** Klucz i punkt końcowy można znaleźć  na stronie klucza i punktu końcowego zasobu w obszarze **zarządzanie zasobami**.
>
> Pamiętaj, aby usunąć klucz z kodu, gdy wszystko będzie gotowe, i nigdy nie publikować go publicznie. W środowisku produkcyjnym rozważ użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Zobacz artykuł Cognitive Services [zabezpieczeń,](../../../cognitive-services-security.md) aby uzyskać więcej informacji.

W metodzie main **aplikacji** dodaj wywołania metod używanych w tym przewodniku Szybki start. Zdefiniujesz je później. Musisz również dodać odwołania do adresów URL dla danych szkoleniowych i testowych.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Pobieranie adresu URL sygnatury dostępu współdzielonego":::
* Aby uzyskać adres URL formularza do przetestowania, możesz wykonać powyższe kroki, aby uzyskać adres URL sygnatury dostępu współdzielonego pojedynczego dokumentu w magazynie obiektów blob. Możesz też wziąć adres URL dokumentu znajdującego się w innym miejscu.
* Użyj powyższej metody, aby uzyskać adres URL obrazu potwierdzenia.
<!-- markdownlint-disable MD024 -->
#### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/preview)

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_maincalls)]

#### <a name="v20"></a>[Wersja 2.0](#tab/ga)

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_maincalls)]

---

## <a name="object-model"></a>Model obiektu

Za Rozpoznawanie formularzy można utworzyć dwa różne typy klientów. Pierwszy z nich `FormRecognizerClient` służy do wykonywania zapytań o usługę w celu rozpoznania pól formularza i zawartości. Drugi to tworzenie modeli niestandardowych, których można używać do ulepszania rozpoznawania, oraz zarządzanie `FormTrainingClient` nimi.

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` Udostępnia operacje dla:

* Rozpoznawanie pól formularzy i zawartości przy użyciu modeli niestandardowych przeszkolonych do analizowania formularzy niestandardowych.  Te wartości są zwracane w kolekcji `RecognizedForm` obiektów . Zobacz przykład [Analyze custom forms (Analizowanie formularzy niestandardowych).](#analyze-forms-with-a-custom-model)
* Rozpoznawanie zawartości formularza, w tym tabel, wierszy i słów, bez konieczności trenowania modelu.  Zawartość formularza jest zwracana w kolekcji `FormPage` obiektów. Zobacz [przykładowy układ Analizowanie](#analyze-layout).
* Rozpoznawanie typowych pól z dokumentów dokumentów tożsamości, wizytówek, wizytówek, dokumentów z USA przy użyciu wstępnie wytrenowany model w Rozpoznawanie formularzy service.

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` Udostępnia operacje dla:

* Trenuj modele niestandardowe w celu analizowania wszystkich pól i wartości znalezionych w formularzach niestandardowych.  Zwracany jest typ formularza, który będzie analizowany przez model, oraz pola `CustomFormModel` wyodrębnione dla każdego typu formularza.
* Trenowanie modeli niestandardowych w celu analizowania określonych pól i wartości określonych przez etykietowanie formularzy niestandardowych.  Jest `CustomFormModel` zwracana wartość wskazująca pola, które model wyodrębni, a także szacowaną dokładność dla każdego pola.
* Zarządzanie modelami utworzonymi na koncie.
* Kopiowanie modelu niestandardowego z jednego zasobu Rozpoznawanie formularzy do innego.

> [!NOTE]
> Modele można również wytrenować przy użyciu graficznego interfejsu użytkownika, takiego [jak Rozpoznawanie formularzy Labeling Tool.](../../quickstarts/label-tool.md)

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania za pomocą biblioteki Rozpoznawanie formularzy klienta dla języka Java:
<!-- markdownlint-disable MD001 -->
#### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/preview)

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Analizowanie układu](#analyze-layout)
* [Analizowanie paragonów](#analyze-receipts)
* [Analizowanie wizytówek](#analyze-business-cards)
* [Analizowanie faktur](#analyze-invoices)
* [Analizowanie dokumentów tożsamości](#analyze-identity-documents)
* [Trenowanie modelu niestandardowego](#train-a-custom-model)
* [Analizowanie formularzy za pomocą modelu niestandardowego](#analyze-forms-with-a-custom-model)
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

W górnej części **metody main** dodaj następujący kod. W tym miejscu uwierzytelnisz dwa obiekty klienta przy użyciu zdefiniowanych powyżej zmiennych subskrypcji. Użyjesz obiektu **AzureKeyCredential,** aby w razie potrzeby można było zaktualizować klucz interfejsu API bez tworzenia nowych obiektów klienta.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_auth)]

## <a name="analyze-layout"></a>Analizowanie układu

Za pomocą Rozpoznawanie formularzy można analizować tabele, linie i wyrazy w dokumentach bez konieczności trenowania modelu. Aby uzyskać więcej informacji na temat wyodrębniania układu, zobacz [Przewodnik koncepcyjny układu](../../concept-layout.md).

Aby przeanalizować zawartość pliku pod danym adresem URL, użyj **metody beginRecognizeContentFromUrl.**

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_call)]

> [!TIP]
> Możesz również pobrać zawartość z pliku lokalnego. Zobacz metody [FormRecognizerClient,](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) takie **jak beginRecognizeContent.** Możesz też zobaczyć przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) uzyskać scenariusze obejmujące obrazy lokalne.

Zwrócona wartość jest kolekcją obiektów **FormPage:** po jednym dla każdej strony w przesłanym dokumencie. Poniższy kod iteruje po tych obiektach i drukuje wyodrębnione pary klucz/wartość i dane tabeli.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_print)]
### <a name="output"></a>Dane wyjściowe

```console
Get form content...
----Recognizing content ----
Has width: 8.500000 and height: 11.000000, measured with unit: inch.
Table has 2 rows and 6 columns.
Cell has text Invoice Number.
Cell has text Invoice Date.
Cell has text Invoice Due Date.
Cell has text Charges.
Cell has text VAT ID.
Cell has text 458176.
Cell has text 3/28/2018.
Cell has text 4/16/2018.
Cell has text $89,024.34.
Cell has text ET.
```
## <a name="analyze-receipts"></a>Analizowanie paragonów

W tej sekcji pokazano, jak analizować i wyodrębniać typowe pola z paragonów w USA przy użyciu wstępnie wytrenowanych modeli paragonów. Aby uzyskać więcej informacji na temat analizy paragonów, zobacz Przewodnik koncepcyjny [dotyczący paragonów](../../concept-receipts.md).

Aby analizować paragony z URI, użyj **metody beginRecognizeReceiptsFromUrl.**

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_call)]

> [!TIP]
> Można również analizować lokalne obrazy paragonów. Zobacz metody [FormRecognizerClient,](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) takie **jak beginRecognizeReceipts.** Możesz też zobaczyć przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) uzyskać scenariusze obejmujące obrazy lokalne.

Zwrócona wartość jest kolekcją **obiektów RecognizedReceipt:** po jednym dla każdej strony w przesłanym dokumencie. Następny blok kodu iteruje po paragonach i drukuje ich szczegóły w konsoli.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print)]

Następny blok kodu iteruje po poszczególnych elementach wykrytych na paragonie i drukuje ich szczegóły w konsoli.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print_items)]

### <a name="output"></a>Dane wyjściowe

```console
Analyze receipt...
----------- Recognized Receipt page 0 -----------
Merchant Name: Contoso Contoso, confidence: 0.62
Merchant Address: 123 Main Street Redmond, WA 98052, confidence: 0.99
Transaction Date: 2020-06-10, confidence: 0.90
Receipt Items:
Name: Cappuccino, confidence: 0.96s
Quantity: null, confidence: 0.957s]
Total Price: 2.200000, confidence: 0.95
Name: BACON & EGGS, confidence: 0.94s
Quantity: null, confidence: 0.927s]
Total Price: null, confidence: 0.93
```

## <a name="analyze-business-cards"></a>Analizowanie wizytówek

#### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/preview)

W tej sekcji pokazano, jak analizować i wyodrębniać typowe pola z angielskich wizytówek przy użyciu wstępnie wytrenowany model. Aby uzyskać więcej informacji na temat analizy wizytówek, zobacz Przewodnik koncepcyjny [dotyczący wizytówek.](../../concept-business-cards.md)

Aby analizować wizytówki z adresu URL, użyj `beginRecognizeBusinessCardsFromUrl` metody .

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_call)]

> [!TIP]
> Można również analizować lokalne obrazy wizytówki. Zobacz metody [FormRecognizerClient,](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) takie **jak beginRecognizeBusinessCards.** Możesz też zobaczyć przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) uzyskać scenariusze obejmujące obrazy lokalne.

Zwrócona wartość jest kolekcją obiektów **RecognizedForm:** po jednym dla każdej karty w dokumencie. Poniższy kod przetwarza wizytówkę pod danym URI i drukuje główne pola i wartości w konsoli.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_print)]

#### <a name="v20"></a>[Wersja 2.0](#tab/ga)

> [!IMPORTANT]
> Ta funkcja nie jest dostępna w wybranej wersji interfejsu API.

---

## <a name="analyze-invoices"></a>Analizowanie faktur

#### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/preview)

W tej sekcji pokazano, jak analizować i wyodrębniać typowe pola z faktur sprzedaży przy użyciu wstępnie wytrenowany model. Aby uzyskać więcej informacji na temat analizy faktur, zobacz [Przewodnik koncepcyjny dotyczący faktur](../../concept-invoices.md).

Aby analizować faktury na podstawie adresu URL, użyj `beginRecognizeInvoicesFromUrl` metody .

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_call)]

> [!TIP]
> Możesz również analizować faktury lokalne. Zobacz metody [FormRecognizerClient,](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) takie **jak beginRecognizeInvoices.** Możesz też zobaczyć przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) uzyskać scenariusze obejmujące obrazy lokalne.

Zwrócona wartość jest kolekcją obiektów **RecognizedForm:** po jednym dla każdej faktury w dokumencie. Poniższy kod przetwarza fakturę pod danym URI i drukuje główne pola i wartości w konsoli.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_print)]

#### <a name="v20"></a>[Wersja 2.0](#tab/ga)

> [!IMPORTANT]
> Ta funkcja nie jest dostępna w wybranej wersji interfejsu API.

---

## <a name="analyze-identity-documents"></a>Analizowanie dokumentów tożsamości

#### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/preview)

W tej sekcji pokazano, jak analizować i wyodrębniać kluczowe informacje z dokumentów identyfikacyjnych wystawionych przez rząd — na całym świecie paszportów i licencji kierowcy w Stanach Zjednoczonych — przy użyciu Rozpoznawanie formularzy wstępnie utworzonego modelu identyfikatorów. Aby uzyskać więcej informacji na temat analizy dokumentów tożsamości, zobacz nasz przewodnik koncepcyjny dotyczący [wstępnie utworzonego modelu identyfikacji](../../concept-identification-cards.md).

Aby analizować dokumenty tożsamości z URI, użyj `beginRecognizeIdDocumentsFromUrl` metody .

:::code language="java" source="~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java" id="snippet_id_call":::

> [!TIP]
> Można również analizować obrazy dokumentów tożsamości lokalnej. Zobacz metody [FormRecognizerClient,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) takie **jak beginRecognizeIdDocuments.** Zapoznaj się również z przykładowym kodem w witrynie [GitHub, aby uzyskać](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) scenariusze obejmujące obrazy lokalne.

Poniższy kod przetwarza dokument tożsamości pod danym URI i drukuje główne pola i wartości w konsoli.

:::code language="java" source="~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java" id="snippet_id_print":::

#### <a name="v20"></a>[Wersja 2.0](#tab/ga)

> [!IMPORTANT]
> Ta funkcja nie jest dostępna w wybranej wersji interfejsu API.

---

## <a name="train-a-custom-model"></a>Trenowanie modelu niestandardowego

W tej sekcji pokazano, jak trenować model przy użyciu własnych danych. Wytrenowany model może wyprowadzać dane ustrukturyzowane, które obejmują relacje klucz/wartość w oryginalnym dokumencie formularza. Po wytrenowania modelu możesz go przetestować i ponownie wytrenować, a następnie użyć go w celu niezawodnego wyodrębnienia danych z większej liczby formularzy zgodnie z potrzebami.

> [!NOTE]
> Modele można również szkolić za pomocą graficznego interfejsu użytkownika, takiego [jak Rozpoznawanie formularzy przykładowe narzędzie do etykietowania](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Trenowanie modelu bez etykiet

Trenowanie modeli niestandardowych w celu analizowania wszystkich pól i wartości znalezionych w formularzach niestandardowych bez ręcznego etykietowania dokumentów szkoleniowych.

Następująca metoda szkoli model na podstawie danego zestawu dokumentów i drukuje stan modelu w konsoli.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_call)]

Zwrócony obiekt **CustomFormModel** zawiera informacje o typach formularzy, które model może analizować, oraz polach, które może wyodrębnić z każdego typu formularza. Poniższy blok kodu drukuje te informacje w konsoli.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_print)]

Na koniec ta metoda zwraca unikatowy identyfikator modelu.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_return)]

### <a name="output"></a>Dane wyjściowe

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

### <a name="train-a-model-with-labels"></a>Trenowanie modelu przy użyciu etykiet

Możesz również szkolić modele niestandardowe, ręcznie oznaczając etykietami dokumenty szkoleniowe. Trenowania za pomocą etykiet prowadzi do lepszej wydajności w niektórych scenariuszach. Aby trenować przy użyciu etykiet, musisz mieć specjalne pliki z informacjami o etykietach *\<filename\> (.pdf.labels.js* w chmurze ) w kontenerze magazynu obiektów blob obok dokumentów szkoleniowych. Przykładowe [Rozpoznawanie formularzy przykładowe narzędzie do etykietowania](../../quickstarts/label-tool.md) udostępnia interfejs użytkownika ułatwiający tworzenie tych plików etykiet. Po ich użyciu możesz wywołać metodę **beginTraining** z parametrem *useTrainingLabels* ustawionym na `true` wartość .

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_call)]


Zwrócony model **CustomFormModel** wskazuje pola, które model może wyodrębnić, wraz z szacowaną dokładnością w każdym polu. Poniższy blok kodu drukuje te informacje w konsoli.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_print)]


### <a name="output"></a>Dane wyjściowe

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

## <a name="analyze-forms-with-a-custom-model"></a>Analizowanie formularzy za pomocą modelu niestandardowego

W tej sekcji pokazano, jak wyodrębniać informacje o kluczu/wartości i inną zawartość z niestandardowych typów formularzy przy użyciu modeli przeszkolonych przy użyciu własnych formularzy.

> [!IMPORTANT]
> Aby zaimplementować ten scenariusz, musisz już wytrenować model, aby można było przekazać jego identyfikator do poniższej metody. Zobacz [sekcję Train a model (Trenowanie](#train-a-model-without-labels) modelu).

Użyjesz metody **beginRecognizeCustomFormsFromUrl.**

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_call)]

> [!TIP]
> Można również przeanalizować plik lokalny. Zobacz metody [FormRecognizerClient,](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) takie **jak beginRecognizeCustomForms.** Możesz też zobaczyć przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) uzyskać scenariusze obejmujące obrazy lokalne.

Zwrócona wartość jest kolekcją obiektów **RecognizedForm:** po jednym dla każdej strony w przesłanym dokumencie. Poniższy kod drukuje wyniki analizy w konsoli. Drukuje każde rozpoznane pole i odpowiadającą mu wartość wraz z wynikiem ufności.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_print)]


### <a name="output"></a>Dane wyjściowe

```console
Analyze PDF form...
----------- Recognized custom form info for page 0 -----------
Form type: form-0
Field 'field-0' has label 'Address:' with a confidence score of 0.91.
Field 'field-1' has label 'Invoice For:' with a confidence score of 1.00.
Field 'field-2' has label 'Invoice Number' with a confidence score of 1.00.
Field 'field-3' has label 'Invoice Date' with a confidence score of 1.00.
Field 'field-4' has label 'Invoice Due Date' with a confidence score of 1.00.
Field 'field-5' has label 'Charges' with a confidence score of 1.00.
Field 'field-6' has label 'VAT ID' with a confidence score of 1.00.
```



## <a name="manage-custom-models"></a>Zarządzanie modelami niestandardowymi

W tej sekcji pokazano, jak zarządzać modelami niestandardowymi przechowywanymi na koncie. Poniższy kod na przykład umożliwia wykonywanie wszystkich zadań zarządzania modelami w ramach jednej metody. Zacznij od skopiowania poniższego podpisu metody:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage)]


### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Sprawdzanie liczby modeli na koncie zasobu FormRecognizer

Poniższy blok kodu sprawdza liczbę modeli zapisanych na koncie Rozpoznawanie formularzy i porównuje go z limitem konta.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_count)]


#### <a name="output"></a>Dane wyjściowe

```console
The account has 12 custom models, and we can have at most 250 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Lista modeli przechowywanych obecnie na koncie zasobu

Poniższy blok kodu wyświetla listę bieżących modeli na Twoim koncie i wyświetla ich szczegóły w konsoli.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_list)]


#### <a name="output"></a>Dane wyjściowe

Ta odpowiedź została obcięta w celu czytelności.

```console
We have following models in the account:
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Status: ready
Training started on: 2020-06-04T18:33:08Z
Training completed on: 2020-06-04T18:33:10Z
Custom Model Form type: form-0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Custom Model Accuracy: 1.00
Field Text: invoice date
Field Accuracy: 1.00
Field Text: invoice number
Field Accuracy: 1.00
...
```

### <a name="delete-a-model-from-the-resource-account"></a>Usuwanie modelu z konta zasobu

Możesz również usunąć model z konta, odwołując się do jego identyfikatora.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_delete)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Wróć do głównego katalogu projektu. Następnie skompilować aplikację za pomocą następującego polecenia:

```console
gradle build
```

Uruchom aplikację w `run` celu:

```console
gradle run
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services zasobów, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie wszystkich innych skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Klienci usługi Recognizer `ErrorResponseException` zgłaszają wyjątki. Jeśli na przykład spróbujesz podać nieprawidłowy adres URL źródła pliku, zostanie podniesiony komunikat o błędzie wskazujący `ErrorResponseException` przyczynę błędu. W poniższym fragmencie kodu błąd jest obsługiwany bezpiecznie, przechwytując wyjątek i wyświetlając dodatkowe informacje o błędzie.

```java Snippet:FormRecognizerBadRequest
try {
    formRecognizerClient.beginRecognizeContentFromUrl("invalidSourceUrl");
} catch (ErrorResponseException e) {
    System.out.println(e.getMessage());
}
```

### <a name="enable-client-logging"></a>Włączanie rejestrowania klienta

Zestawy Azure SDK dla języka Java oferują spójną historię rejestrowania, która pomaga w rozwiązywaniu problemów z błędami aplikacji i przyspieszaniu ich rozwiązywania. Zarejestrowane dzienniki przechwytują przepływ aplikacji przed osiągnięciem stanu końcowego, co pomoże zlokalizować główny problem. Aby uzyskać [wskazówki dotyczące włączania rejestrowania,](https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK) zobacz witrynę wiki rejestrowania.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start za pomocą biblioteki Rozpoznawanie formularzy Java do trenowania modeli i analizowania formularzy na różne sposoby. Następnie zapoznaj się z poradami dotyczącymi tworzenia lepszego zestawu danych treningowych i tworzenia dokładniej określonych modeli.

> [!div class="nextstepaction"]
> [Tworzenie zestawu danych szkoleniowych](../../build-training-data-set.md)

* [Co to jest rozpoznawanie formularzy?](../../overview.md)
* Przykładowy kod z tego przewodnika (i innych) można znaleźć w [witrynie GitHub.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples)
