---
title: 'Szybki Start: Biblioteka klienta aparatu rozpoznawania formularzy dla języka Java'
description: Biblioteka klienta aparatu rozpoznawania formularzy dla języka Java służy do tworzenia aplikacji przetwarzającej formularze, która wyodrębnia pary klucz/wartość i dane tabeli z dokumentów niestandardowych.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 09/21/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: d53863ccf71970cca3900707c844a2e5add050fa
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356515"
---
> [!IMPORTANT]
> Kod w tym artykule używa metod synchronicznych i niezabezpieczonych magazynów poświadczeń z przyczyn uproszczenia.

[Dokumentacja](/java/api/overview/azure/ai-formrecognizer-readme?view=azure-java-preview)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src)  |  źródłowy biblioteki [Pakiet (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* Bieżąca wersja [zestawu Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Narzędzie kompilacji Gradle](https://gradle.org/install/)lub inny Menedżer zależności.
* Gdy masz subskrypcję platformy Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" Utwórz zasób aparatu rozpoznawania formularzy "  target="_blank"> Utwórz zasób aparatu rozpoznawania formularza <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który utworzysz, aby połączyć aplikację z interfejsem API rozpoznawania formularzy. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.
* Obiekt BLOB usługi Azure Storage zawierający zestaw danych szkoleniowych. Zapoznaj się z tematem [Tworzenie zestawu danych szkoleniowych dla modelu niestandardowego](../../build-training-data-set.md) w celu uzyskania wskazówek i opcji związanych z zestawem danych szkoleniowych. W tym przewodniku szybki start można użyć plików w folderze **uczenie** [zestawu danych przykładowych](https://go.microsoft.com/fwlink/?linkid=2090451) (pobierz i Wyodrębnij *sample_data.zip*).


## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-gradle-project"></a>Utwórz nowy projekt Gradle

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom `gradle init` polecenie z katalogu roboczego. To polecenie spowoduje utworzenie podstawowych plików kompilacji dla Gradle, w tym *Build. Gradle. KTS* , który jest używany w środowisku uruchomieniowym do tworzenia i konfigurowania aplikacji.

```console
gradle init --type basic
```

Po wyświetleniu monitu wybierz pozycję **Język DSL**, a następnie **Kotlin**.


### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Ten przewodnik Szybki Start używa Menedżera zależności Gradle. Bibliotekę i informacje o kliencie można znaleźć dla innych menedżerów zależności w [repozytorium centralnym Maven](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

W pliku *Build. Gradle. KTS* projektu Dołącz bibliotekę klienta jako `implementation` instrukcję wraz z wymaganymi wtyczkami i ustawieniami.

#### <a name="version-30"></a>[Wersja 3,0](#tab/ga)
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
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.0.0")
}
```
#### <a name="version-31-preview"></a>[Wersja 3,1 Preview](#tab/preview)
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
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.1.0-beta.1")
}
```
---

### <a name="create-a-java-file"></a>Tworzenie pliku języka Java


W katalogu roboczym Uruchom następujące polecenie:

```console
mkdir -p src/main/java
```

Przejdź do nowego folderu i Utwórz plik o nazwie *FormRecognizer. Java*. Otwórz go w preferowanym edytorze lub środowisku IDE i Dodaj następujące `import` instrukcje:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_imports)]

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/FormRecognizer/FormRecognizer.java), która zawiera przykłady kodu w tym przewodniku Szybki Start.


W klasie **FormRecognizer** aplikacji Utwórz zmienne dla klucza i punktu końcowego zasobu.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_creds)]

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli zasób [Product Name] utworzony w sekcji **wymagań wstępnych** został pomyślnie wdrożony, kliknij przycisk **Przejdź do zasobu** w obszarze **następne kroki**. Klucz i punkt końcowy można znaleźć na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami**. 
>
> Pamiętaj, aby usunąć klucz z kodu, gdy skończysz, i nigdy nie Publikuj go publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [zabezpieczeń](../../../cognitive-services-security.md) Cognitive Services.

W metodzie **głównej** aplikacji Dodaj wywołania metod używanych w tym przewodniku Szybki Start. Zdefiniujesz je później. Należy również dodać odwołania do adresów URL dla danych szkoleniowych i testowych.

* Aby pobrać adres URL sygnatury dostępu współdzielonego dla danych szkolenia modelu niestandardowego, Otwórz Eksplorator usługi Microsoft Azure Storage, kliknij prawym przyciskiem myszy kontener, a następnie wybierz pozycję **Pobierz sygnaturę dostępną**. Upewnij się, że uprawnienia do **odczytu** i **listy** są zaznaczone, a następnie kliknij przycisk **Utwórz**. Następnie skopiuj wartość z sekcji **URL** . Powinna ona mieć postać: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Aby uzyskać adres URL formularza do przetestowania, możesz wykonać powyższe kroki, aby uzyskać adres URL sygnatury dostępu współdzielonego pojedynczego dokumentu w usłudze BLOB Storage. Lub podejmij adres URL dokumentu znajdującego się w innym miejscu.
* Użyj powyższej metody, aby uzyskać również adres URL obrazu paragonu.

#### <a name="version-30"></a>[Wersja 3,0](#tab/ga)
[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_maincalls)]
#### <a name="version-31-preview"></a>[Wersja 3,1 Preview](#tab/preview)
[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_maincalls)]

---



## <a name="object-model"></a>Model obiektów 

Za pomocą aparatu rozpoznawania formularzy można utworzyć dwa różne typy klientów. Pierwszy `FormRecognizerClient` jest używany do wysyłania zapytań do usługi do rozpoznanych pól formularzy i zawartości. Drugi — `FormTrainingClient` służy do tworzenia modeli niestandardowych i zarządzania nimi, których można użyć w celu usprawnienia rozpoznawania. 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` zawiera operacje dla:

- Rozpoznawanie pól formularzy i zawartości przy użyciu modeli niestandardowych przeszkolonych w celu rozpoznawania formularzy niestandardowych.  Te wartości są zwracane w kolekcji `RecognizedForm` obiektów. Zobacz przykład [Analizowanie formularzy niestandardowych](#analyze-forms-with-a-custom-model).
- Rozpoznawanie zawartości formularza, w tym tabel, wierszy i słów, bez konieczności uczenia modelu.  Zawartość formularza jest zwracana w kolekcji `FormPage` obiektów. Zobacz przykład [Rozpoznaj zawartość formularza](#recognize-form-content).
- Rozpoznawanie typowych pól z paragonów w Stanach Zjednoczonych przy użyciu wstępnie przeszkolonego modelu paragonów w usłudze aparat rozpoznawania formularzy.  Te pola i meta dane są zwracane w kolekcji `RecognizedForm` obiektów. Zobacz przykład [rozpoznawania przyjęć](#recognize-receipts).

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` zawiera operacje dla:

- Szkolenie modeli niestandardowych w celu rozpoznania wszystkich pól i wartości znalezionych w formularzach niestandardowych.  `CustomFormModel`Jest zwracany, wskazując typy formularzy, które będą rozpoznawane przez model, oraz pola, które będą wyodrębniane dla każdego typu formularza.
- Szkolenie modeli niestandardowych w celu rozpoznawania określonych pól i wartości, które można określić przez etykietowanie formularzy niestandardowych.  `CustomFormModel`Zwraca wartość wskazującą pola, które będą wyodrębniane przez model, a także szacowaną dokładność dla każdego pola.
- Zarządzanie modelami utworzonymi na Twoim koncie.
- Kopiowanie modelu niestandardowego z jednego do drugiego zasobu aparatu rozpoznawania formularza.

> [!NOTE]
> Modele mogą być również przeszkolone przy użyciu graficznego interfejsu użytkownika, takiego jak [Narzędzie do etykietowania aparatów rozpoznawania formularzy](../../quickstarts/label-tool.md).

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań za pomocą biblioteki klienckiej aparatu rozpoznawania w języku Java:

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

W górnej części metody **Main** Dodaj następujący kod. W tym miejscu będziesz uwierzytelniać dwa obiekty klienckie przy użyciu zdefiniowanych powyżej zmiennych. Użyjesz obiektu **AzureKeyCredential** , aby w razie potrzeby można było zaktualizować klucz interfejsu API bez tworzenia nowych obiektów klienta.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_auth)]

## <a name="recognize-form-content"></a>Rozpoznaj zawartość formularza

Aparat rozpoznawania formularzy służy do rozpoznawania tabel, wierszy i słów w dokumentach, bez konieczności uczenia modelu.

Aby rozpoznać zawartość pliku pod podanym adresem URL, należy użyć metody **beginRecognizeContentFromUrl** .

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_call)]

> [!TIP]
> Możesz również pobrać zawartość z pliku lokalnego. Zobacz metody [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable) , takie jak **beginRecognizeContent**. Lub zapoznaj się z przykładowym kodem w witrynie [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) , aby poznać scenariusze dotyczące obrazów lokalnych.

Zwracana wartość jest kolekcją obiektów **FormPage** : jeden dla każdej strony w przesłanym dokumencie. Poniższy kod wykonuje iterację tych obiektów i drukuje wyodrębnione pary klucz/wartość i dane tabeli.

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

## <a name="recognize-receipts"></a>Rozpoznawaj potwierdzenia

W tej sekcji przedstawiono sposób rozpoznawania i wyodrębniania typowych pól z paragonów w Stanach Zjednoczonych przy użyciu wstępnie przeszkolonego modelu paragonów.

Aby rozpoznawać potwierdzenia z identyfikatora URI, należy użyć metody **beginRecognizeReceiptsFromUrl** . 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_call)]

> [!TIP]
> Możesz również rozpoznać lokalne obrazy paragonów. Zobacz metody [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable) , takie jak **beginRecognizeReceipts**. Lub zapoznaj się z przykładowym kodem w witrynie [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) , aby poznać scenariusze dotyczące obrazów lokalnych.

Zwracana wartość jest kolekcją obiektów **RecognizedReceipt** : jeden dla każdej strony w przesłanym dokumencie. Następny blok kodu iteruje przez potwierdzenia i drukuje szczegółowe informacje w konsoli programu.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print)]

Następny blok kodu iteruje poszczególne elementy wykryte na paragonie i drukuje ich szczegóły do konsoli.

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

#### <a name="version-30"></a>[Wersja 3,0](#tab/ga)

#### <a name="version-31-preview"></a>[Wersja 3,1 Preview](#tab/preview)

## <a name="recognize-business-cards"></a>Rozpoznawanie wizytówek

W tej sekcji przedstawiono sposób rozpoznawania i wyodrębniania typowych pól z angielskich kart firmowych przy użyciu wstępnie nauczonego modelu.

Aby rozpoznać karty biznesowe na podstawie adresu URL, użyj `beginRecognizeBusinessCardsFromUrl` metody. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_call)]

> [!TIP]
> Możesz również rozpoznać lokalne obrazy kart służbowych. Zobacz metody [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable) , takie jak **beginRecognizeBusinessCards**. Lub zapoznaj się z przykładowym kodem w witrynie [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) , aby poznać scenariusze dotyczące obrazów lokalnych.

Zwracana wartość jest kolekcją obiektów **RecognizedForm** : jeden dla każdej karty w dokumencie. Poniższy kod przetwarza kartę biznesową pod podanym identyfikatorem URI i drukuje główne pola i wartości w konsoli.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_print)]

## <a name="recognize-invoices"></a>Rozpoznaj faktury

W tej sekcji przedstawiono sposób rozpoznawania i wyodrębniania typowych pól z faktur sprzedaży przy użyciu wstępnie nauczonego modelu.

Aby rozpoznać karty biznesowe na podstawie adresu URL, użyj `beginRecognizeInvoicesFromUrl` metody. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_call)]

> [!TIP]
> Możesz również rozpoznać faktury lokalne. Zobacz metody [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable) , takie jak **beginRecognizeInvoices**. Lub zapoznaj się z przykładowym kodem w witrynie [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) , aby poznać scenariusze dotyczące obrazów lokalnych.

Zwracana wartość jest kolekcją obiektów **RecognizedForm** : jeden dla każdej faktury w dokumencie. Poniższy kod przetwarza kartę biznesową pod podanym identyfikatorem URI i drukuje główne pola i wartości w konsoli.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_print)]

---

## <a name="train-a-custom-model"></a>Trenowanie modelu niestandardowego

W tej sekcji pokazano, jak szkolić model przy użyciu własnych danych. Model przeszkolony może wyprowadzać dane strukturalne, które zawierają relacje klucz/wartość w oryginalnym dokumencie formularza. Po przeprowadzeniu szkolenia modelu można testować i przeszkolić go i ostatecznie użyć do niezawodnego wyodrębnienia danych z większej liczby formularzy zgodnie z potrzebami.

> [!NOTE]
> Możesz również nauczyć modele przy użyciu graficznego interfejsu użytkownika, takiego jak [Narzędzie do próbkowania przykładowego aparatu rozpoznawania formularzy](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Uczenie modelu bez etykiet

Uczenie modeli niestandardowych w celu rozpoznawania wszystkich pól i wartości znalezionych w formularzach niestandardowych bez ręcznego etykietowania dokumentów szkoleniowych.

Poniższa metoda pociąga za model dla danego zestawu dokumentów i drukuje stan modelu w konsoli programu. 


[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_call)]

Zwrócony obiekt **CustomFormModel** zawiera informacje na temat typów formularzy, które może rozpoznać model, oraz pól, które mogą zostać wyodrębnione z każdego typu formularza. Poniższy blok kodu drukuje te informacje w konsoli programu.

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

### <a name="train-a-model-with-labels"></a>Uczenie modelu z etykietami

Można także uczenie modeli niestandardowych przez ręczne etykietowanie dokumentów szkoleniowych. Szkolenie z etykietami prowadzi do lepszej wydajności w niektórych scenariuszach. Aby szkolić z etykietami, musisz mieć specjalne pliki informacji o etykietach (*\<filename\>.pdf.labels.json*) w kontenerze magazynu obiektów BLOB obok dokumentów szkoleniowych. [Narzędzie do etykietowania próbek aparatu rozpoznawania formularzy](../../quickstarts/label-tool.md) udostępnia interfejs użytkownika ułatwiający Tworzenie tych plików etykiet. Po ich utworzeniu można wywołać metodę **beginTraining** z parametrem *useTrainingLabels* ustawionym na wartość `true` .

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_call)]


Zwrócony **CustomFormModel** wskazuje pola, które mogą zostać wyodrębnione przez model, wraz z szacowaną dokładnością dla każdego pola. Poniższy blok kodu drukuje te informacje w konsoli programu.

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

## <a name="analyze-forms-with-a-custom-model"></a>Analizowanie formularzy przy użyciu modelu niestandardowego

W tej sekcji pokazano, jak wyodrębnić informacje o kluczu/wartości i innej zawartości z niestandardowych typów formularzy przy użyciu modeli przeszkolonych za pomocą własnych formularzy.

> [!IMPORTANT]
> Aby zaimplementować ten scenariusz, należy wcześniej przeszkolić model, aby można było przekazać jego identyfikator do metody poniżej. Zobacz sekcję [uczenie modelu](#train-a-model-without-labels) .

Będziesz używać metody **beginRecognizeCustomFormsFromUrl** . 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_call)]

> [!TIP]
> Możesz również analizować plik lokalny. Zobacz metody [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable) , takie jak **beginRecognizeCustomForms**. Lub zapoznaj się z przykładowym kodem w witrynie [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) , aby poznać scenariusze dotyczące obrazów lokalnych.

Zwracana wartość jest kolekcją obiektów **RecognizedForm** : jeden dla każdej strony w przesłanym dokumencie. Poniższy kod drukuje wyniki analizy w konsoli programu. Wypisuje wszystkie rozpoznane pola i odpowiadające im wartości, a także ocenę ufności.

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

W tej sekcji pokazano, jak zarządzać modelami niestandardowymi przechowywanymi na Twoim koncie. Poniższy kod wykonuje wszystkie zadania zarządzania modelami w ramach jednej metody, na przykład. Zacznij od skopiowania poniższego podpisu metody:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage)]


### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Sprawdź liczbę modeli na koncie zasobów FormRecognizer

Poniższy blok kodu sprawdza, ile modeli Zapisano na koncie aparatu rozpoznawania formularzy i porównuje je z limitem konta.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_count)]


#### <a name="output"></a>Dane wyjściowe 

```console
The account has 12 custom models, and we can have at most 250 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Wyświetlanie listy modeli przechowywanych obecnie na koncie zasobu

Poniższy blok kodu zawiera listę bieżących modeli na koncie i drukuje ich szczegóły do konsoli programu.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_list)]


#### <a name="output"></a>Dane wyjściowe 

Ta odpowiedź została obcięta na potrzeby czytelności.

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

Przejdź z powrotem do głównego katalogu projektu. Następnie skompiluj aplikację przy użyciu następującego polecenia:

```console
gradle build
```

Uruchom aplikację z `run` celem:

```console
gradle run
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Od klientów aparatu rozpoznawania `ErrorResponseException` wyjątków. Na przykład jeśli spróbujesz podać nieprawidłowy adres URL źródła pliku, `ErrorResponseException` zostanie zgłoszony błąd wskazujący przyczynę niepowodzenia. W poniższym fragmencie kodu błąd jest obsługiwany bezpiecznie przez przechwycenie wyjątku i wyświetlenie dodatkowych informacji o błędzie.

```java Snippet:FormRecognizerBadRequest
try {
    formRecognizerClient.beginRecognizeContentFromUrl("invalidSourceUrl");
} catch (ErrorResponseException e) {
    System.out.println(e.getMessage());
}
```

### <a name="enable-client-logging"></a>Włącz rejestrowanie klienta

Zestaw SDK platformy Azure dla języka Java oferuje spójny scenariusz rejestrowania, który pomaga pomóc w rozwiązywaniu problemów z błędami aplikacji i przyspieszeniu ich rozwiązywania. Utworzone dzienniki będą przechwytywać przepływ aplikacji przed osiągnięciem stanu terminalu, aby ułatwić znalezienie problemu głównego. Wyświetl informacje o [rejestrowaniu wiki](https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK) , aby uzyskać wskazówki dotyczące włączania rejestrowania.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto biblioteki klienckiej aparatu rozpoznawania języka Java do uczenia modeli i analizowania formularzy na różne sposoby. Następnie zapoznaj się z poradami, aby utworzyć lepszy zestaw danych szkoleniowych i uzyskać bardziej dokładne modele.

> [!div class="nextstepaction"]
> [Tworzenie zestawu danych szkoleniowych](../../build-training-data-set.md)

* [Co to jest rozpoznawanie formularzy?](../../overview.md)
* Przykładowy kod z tego przewodnika (i więcej) można znaleźć w witrynie [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples).