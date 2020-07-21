---
title: 'Szybki Start: Biblioteka klienta aparatu rozpoznawania formularzy dla języka Java'
description: W tym przewodniku szybki start Rozpocznij pracę z biblioteką klienta aparatu rozpoznawania dla środowiska Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/15/2020
ms.author: pafarley
ms.openlocfilehash: 479891513eb48e4ced4c1dff2feb3215b3c8ea57
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544055"
---
[Dokumentacja](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme-pre?view=azure-java-preview)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src)  |  źródłowy biblioteki [Pakiet (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
* Obiekt BLOB usługi Azure Storage zawierający zestaw danych szkoleniowych. Zapoznaj się z tematem [Tworzenie zestawu danych szkoleniowych dla modelu niestandardowego](../../build-training-data-set.md) w celu uzyskania wskazówek i opcji związanych z zestawem danych szkoleniowych. W tym przewodniku szybki start można użyć plików w folderze **uczenie** [zestawu danych przykładowych](https://go.microsoft.com/fwlink/?linkid=2090451).
* Bieżąca wersja [zestawu Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Narzędzie kompilacji Gradle](https://gradle.org/install/)lub inny Menedżer zależności.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-form-recognizer-azure-resource"></a>Tworzenie usługi Azure Resource dla aparatu rozpoznawania

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Tworzenie zmiennych środowiskowych

[!INCLUDE [environment-variables](../environment-variables.md)]

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

Utwórz folder dla przykładowej aplikacji. W katalogu roboczym Uruchom następujące polecenie:

```console
mkdir -p src/main/java
```

Przejdź do nowego folderu i Utwórz plik o nazwie *formrecognizer-quickstart. Java*. Otwórz go w preferowanym edytorze lub środowisku IDE i Dodaj następujące `import` instrukcje:

```java
import Azure.AI.FormRecognizer;
import Azure.AI.FormRecognizer.Models;

import java.util.concurrent.atomic.AtomicReference;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.util.Context;
```

W `main` metodzie aplikacji Utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource. Jeśli zmienna środowiskowa została utworzona po uruchomieniu aplikacji, należy zamknąć i ponownie otworzyć Edytor, środowisko IDE lub powłokę w celu uzyskania dostępu do zmiennej. Metody zostaną zdefiniowane później.


```java
public static void Main(string[] args)
{
    String key = System.getenv("FORM_RECOGNIZER_KEY");
    String endpoint = System.getenv("FORM_RECOGNIZER_ENDPOINT");
}
```

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Ten przewodnik Szybki Start używa Menedżera zależności Gradle. Bibliotekę i informacje o kliencie można znaleźć dla innych menedżerów zależności w [repozytorium centralnym Maven](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

W pliku *Build. Gradle. KTS* projektu Pamiętaj o uwzględnieniu biblioteki klienta jako `implementation` instrukcji. 

```kotlin
dependencies {
    implementation group: 'com.azure', name: 'azure-ai-formrecognizer', version: '1.0.0-beta.3'
}
```

<!-- 
    Object model tbd
-->

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań za pomocą biblioteki klienckiej aparatu rozpoznawania w języku Java:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Rozpoznaj zawartość formularza](#recognize-form-content)
* [Rozpoznawaj potwierdzenia](#recognize-receipts)
* [Trenowanie modelu niestandardowego](#train-a-custom-model)
* [Analizowanie formularzy przy użyciu modelu niestandardowego](#analyze-forms-with-a-custom-model)
* [Zarządzanie modelami niestandardowymi](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Wewnątrz `Main` metody Dodaj następujący kod. W tym miejscu będziesz uwierzytelniać dwa obiekty klienckie przy użyciu zdefiniowanych powyżej zmiennych. Użyjesz obiektu **AzureKeyCredential** , aby w razie potrzeby można było zaktualizować klucz interfejsu API bez tworzenia nowych obiektów klienta.

```java
FormRecognizerClient recognizerClient = new FormRecognizerClientBuilder()
    .credential(new AzureKeyCredential("{key}"))
    .endpoint("{endpoint}")
    .buildClient();
    
FormTrainingClient trainingClient = recognizerClient.getFormTrainingClient();
```

### <a name="call-client-specific-methods"></a>Wywoływanie metod specyficznych dla klienta

Następny blok kodu używa obiektów klienta do wywoływania metod dla każdego z najważniejszych zadań w zestawie SDK aparatu rozpoznawania. Te metody zostaną zdefiniowane później.

Należy również dodać odwołania do adresów URL dla danych szkoleniowych i testowych. 
* Aby pobrać adres URL sygnatury dostępu współdzielonego dla danych szkolenia modelu niestandardowego, Otwórz Eksplorator usługi Microsoft Azure Storage, kliknij prawym przyciskiem myszy kontener, a następnie wybierz pozycję **Pobierz sygnaturę dostępną**. Upewnij się, że uprawnienia do **odczytu** i **listy** są zaznaczone, a następnie kliknij przycisk **Utwórz**. Następnie skopiuj wartość z sekcji **URL** . Powinna mieć postać: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .
* Aby uzyskać adres URL formularza do przetestowania, możesz wykonać powyższe kroki, aby uzyskać adres URL sygnatury dostępu współdzielonego pojedynczego dokumentu w usłudze BLOB Storage. Lub podejmij adres URL dokumentu znajdującego się w innym miejscu.
* Użyj powyższej metody, aby uzyskać również adres URL obrazu paragonu.

> [!NOTE]
> Fragmenty kodu w tym przewodniku korzystają z formularzy zdalnych, do których uzyskuje dostęp za pomocą adresów URL. Jeśli zamiast tego chcesz przetworzyć lokalne dokumenty formularzy, zapoznaj się z odpowiednimi metodami w [dokumentacji referencyjnej](https://docs.microsoft.com/java/api/overview/azure/ai-formrecognizer-readme-pre?view=azure-java-preview).

```java
    string trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    string formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
    string receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media"
    + "/contoso-allinone.jpg";

    // Call Form Recognizer scenarios:
    System.out.println("Get form content...");
    GetContent(recognizerClient, formUrl);

    System.out.println("Analyze receipt...");
    AnalyzeReceipt(recognizerClient, receiptUrl);

    System.out.println("Train Model with training data...");
    modelId = TrainModel(trainingClient, trainingDataUrl);

    System.out.println("Analyze PDF form...");
    AnalyzePdfForm(recognizerClient, modelId, formUrl);

    System.out.println("Manage models...");
    ManageModels(trainingClient, trainingDataUrl) ;
```


## <a name="recognize-form-content"></a>Rozpoznaj zawartość formularza

Aparat rozpoznawania formularzy służy do rozpoznawania tabel, wierszy i słów w dokumentach, bez konieczności uczenia modelu.

Aby rozpoznać zawartość pliku w danym identyfikatorze URI, użyj metody **beginRecognizeContentFromUrl** .

```java
private static void GetContent(
    FormRecognizerClient recognizerClient, String invoiceUri)
{
    String analyzeFilePath = invoiceUri;
    SyncPoller<OperationResult, List<FormPage>> recognizeContentPoller =
        recognizerClient.beginRecognizeContentFromUrl(analyzeFilePath);
    
    List<FormPage> contentResult = recognizeContentPoller.getFinalResult();
```

Zwracana wartość jest kolekcją obiektów **FormPage** : jeden dla każdej strony w przesłanym dokumencie. Poniższy kod wykonuje iterację tych obiektów i drukuje wyodrębnione pary klucz/wartość i dane tabeli.

```java
    contentResult.forEach(formPage -> {
        // Table information
        System.out.println("----Recognizing content ----");
        System.out.printf("Has width: %d and height: %d, measured with unit: %s.%n", formPage.getWidth(),
            formPage.getHeight(),
            formPage.getUnit());
        formPage.getTables().forEach(formTable -> {
            System.out.printf("Table has %d rows and %d columns.%n", formTable.getRowCount(),
                formTable.getColumnCount());
            formTable.getCells().forEach(formTableCell -> {
                System.out.printf("Cell has text %s.%n", formTableCell.getText());
            });
            System.out.println();
        });
    });
}
```

## <a name="recognize-receipts"></a>Rozpoznawaj potwierdzenia

W tej sekcji przedstawiono sposób rozpoznawania i wyodrębniania typowych pól z paragonów w Stanach Zjednoczonych przy użyciu wstępnie przeszkolonego modelu paragonów.

Aby rozpoznawać potwierdzenia z identyfikatora URI, należy użyć metody **beginRecognizeReceiptsFromUrl** . Zwracana wartość jest kolekcją obiektów **RecognizedReceipt** : jeden dla każdej strony w przesłanym dokumencie.

```java
private static void AnalyzeReceipt(
    FormRecognizerClient recognizerClient, string receiptUri)
{
    SyncPoller<OperationResult, List<RecognizedReceipt>> syncPoller =
        formRecognizerClient.beginRecognizeReceiptsFromUrl(receiptUri);
    List<RecognizedReceipt> receiptPageResults = syncPoller.getFinalResult();
```

Następny blok kodu iteruje przez potwierdzenia i drukuje szczegółowe informacje w konsoli programu.

```java
    for (int i = 0; i < receiptPageResults.size(); i++) {
        RecognizedReceipt recognizedReceipt = receiptPageResults.get(i);
        Map<String, FormField> recognizedFields = recognizedReceipt.getRecognizedForm().getFields();
        System.out.printf("----------- Recognized Receipt page %d -----------%n", i);
        FormField merchantNameField = recognizedFields.get("MerchantName");
        if (merchantNameField != null) {
            if (merchantNameField.getFieldValue().getType() == FieldValueType.STRING) {
                System.out.printf("Merchant Name: %s, confidence: %.2f%n",
                    merchantNameField.getFieldValue().asString(),
                    merchantNameField.getConfidence());
            }
        }
        FormField merchantAddressField = recognizedFields.get("MerchantAddress");
        if (merchantAddressField != null) {
            if (merchantAddressField.getFieldValue().getType() == FieldValueType.STRING) {
                System.out.printf("Merchant Address: %s, confidence: %.2f%n",
                    merchantAddressField.getFieldValue().asString(),
                    merchantAddressField.getConfidence());
            }
        }
        FormField transactionDateField = recognizedFields.get("TransactionDate");
        if (transactionDateField != null) {
            if (transactionDateField.getFieldValue().getType() == FieldValueType.DATE) {
                System.out.printf("Transaction Date: %s, confidence: %.2f%n",
                    transactionDateField.getFieldValue().asDate(),
                    transactionDateField.getConfidence());
            }
        }
```
Następny blok kodu iteruje poszczególne elementy wykryte na paragonie i drukuje ich szczegóły do konsoli.

```java
        FormField receiptItemsField = recognizedFields.get("Items");
        if (receiptItemsField != null) {
            System.out.printf("Receipt Items: %n");
            if (receiptItemsField.getFieldValue().getType() == FieldValueType.LIST) {
                List<FormField> receiptItems = receiptItemsField.getFieldValue().asList();
                receiptItems.forEach(receiptItem -> {
                    if (receiptItem.getFieldValue().getType() == FieldValueType.MAP) {
                        receiptItem.getFieldValue().asMap().forEach((key, formField) -> {
                            if (key.equals("Name")) {
                                if (formField.getFieldValue().getType() == FieldValueType.STRING) {
                                    System.out.printf("Name: %s, confidence: %.2fs%n",
                                        formField.getFieldValue().asString(),
                                        formField.getConfidence());
                                }
                            }
                            if (key.equals("Quantity")) {
                                if (formField.getFieldValue().getType() == FieldValueType.INTEGER) {
                                    System.out.printf("Quantity: %d, confidence: %.2f%n",
                                        formField.getFieldValue().asInteger(), formField.getConfidence());
                                }
                            }
                            if (key.equals("Price")) {
                                if (formField.getFieldValue().getType() == FieldValueType.FLOAT) {
                                    System.out.printf("Price: %f, confidence: %.2f%n",
                                        formField.getFieldValue().asFloat(),
                                        formField.getConfidence());
                                }
                            }
                            if (key.equals("TotalPrice")) {
                                if (formField.getFieldValue().getType() == FieldValueType.FLOAT) {
                                    System.out.printf("Total Price: %f, confidence: %.2f%n",
                                        formField.getFieldValue().asFloat(),
                                        formField.getConfidence());
                                }
                            }
                        });
                    }
                });
            }
        }
    }
}
```

## <a name="train-a-custom-model"></a>Trenowanie modelu niestandardowego

W tej sekcji pokazano, jak szkolić model przy użyciu własnych danych. Model przeszkolony może wyprowadzać dane strukturalne, które zawierają relacje klucz/wartość w oryginalnym dokumencie formularza. Po przeprowadzeniu szkolenia modelu można testować i przeszkolić go i ostatecznie użyć do niezawodnego wyodrębnienia danych z większej liczby formularzy zgodnie z potrzebami.

> [!NOTE]
> Możesz również nauczyć modele przy użyciu graficznego interfejsu użytkownika, takiego jak [Narzędzie do próbkowania przykładowego aparatu rozpoznawania formularzy](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Uczenie modelu bez etykiet

Uczenie modeli niestandardowych w celu rozpoznawania wszystkich pól i wartości znalezionych w formularzach niestandardowych bez ręcznego etykietowania dokumentów szkoleniowych.

Poniższa metoda pociąga za model dla danego zestawu dokumentów i drukuje stan modelu w konsoli programu. 

```java
private static String TrainModel(
    FormRecognizerClient trainingClient, string trainingDataUrl)
{
    String trainingSetSource = "{unlabeled_training_set_SAS_URL}";
    SyncPoller<OperationResult, CustomFormModel> trainingPoller =
        formTrainingClient.beginTraining(trainingSetSource, false);
    
    CustomFormModel customFormModel = trainingPoller.getFinalResult();
    
    // Model Info
    System.out.printf("Model Id: %s%n", customFormModel.getModelId());
    System.out.printf("Model Status: %s%n", customFormModel.getModelStatus());
    System.out.printf("Model created on: %s%n", customFormModel.getCreatedOn());
    System.out.printf("Model last updated: %s%n%n", customFormModel.getCompletedOn());
```
Zwrócony obiekt **CustomFormModel** zawiera informacje na temat typów formularzy, które może rozpoznać model, oraz pól, które mogą zostać wyodrębnione z każdego typu formularza. Poniższy blok kodu drukuje te informacje w konsoli programu.

```java 
    System.out.println("Recognized Fields:");
    // looping through the sub-models, which contains the fields they were trained on
    // Since the given training documents are unlabeled, we still group them but they do not have a label.
    customFormModel.getSubmodels().forEach(customFormSubModel -> {
        // Since the training data is unlabeled, we are unable to return the accuracy of this model
        customFormSubModel.getFieldMap().forEach((field, customFormModelField) ->
            System.out.printf("Field: %s Field Label: %s%n",
                field, customFormModelField.getLabel()));
    });
```

Na koniec ta metoda zwraca unikatowy identyfikator modelu.

```java
    return customFormModel.getModelId();
}
```

### <a name="train-a-model-with-labels"></a>Uczenie modelu z etykietami

Można także uczenie modeli niestandardowych przez ręczne etykietowanie dokumentów szkoleniowych. Szkolenie z etykietami prowadzi do lepszej wydajności w niektórych scenariuszach. Aby szkolić z etykietami, musisz mieć specjalne pliki informacji o etykietach (* \<filename\>.pdf.labels.json*) w kontenerze magazynu obiektów BLOB obok dokumentów szkoleniowych. [Narzędzie do etykietowania próbek aparatu rozpoznawania formularzy](../../quickstarts/label-tool.md) udostępnia interfejs użytkownika ułatwiający Tworzenie tych plików etykiet. Po ich utworzeniu można wywołać metodę **beginTraining** z parametrem *useTrainingLabels* ustawionym na wartość `true` .

```java
private static String TrainModelWithLabels(
    FormRecognizerClient trainingClient, String trainingDataUrl)
{
    // Train custom model
    String trainingSetSource = trainingDataUrl;
    SyncPoller<OperationResult, CustomFormModel> trainingPoller = client.beginTraining(trainingSetSource, true);

    CustomFormModel customFormModel = trainingPoller.getFinalResult();

    // Model Info
    System.out.printf("Model Id: %s%n", customFormModel.getModelId());
    System.out.printf("Model Status: %s%n", customFormModel.getModelStatus());
    System.out.printf("Model created on: %s%n", customFormModel.getRequestedOn());
    System.out.printf("Model last updated: %s%n%n", customFormModel.getCompletedOn());
```

Zwrócony **CustomFormModel** wskazuje pola, które mogą zostać wyodrębnione przez model, wraz z szacowaną dokładnością dla każdego pola. Poniższy blok kodu drukuje te informacje w konsoli programu.

```java
    // looping through the sub-models, which contains the fields they were trained on
    // The labels are based on the ones you gave the training document.
    System.out.println("Recognized Fields:");
    // Since the data is labeled, we are able to return the accuracy of the model
    customFormModel.getSubmodels().forEach(customFormSubModel -> {
        System.out.printf("Sub-model accuracy: %.2f%n", customFormSubModel.getAccuracy());
        customFormSubModel.getFieldMap().forEach((label, customFormModelField) ->
            System.out.printf("Field: %s Field Name: %s Field Accuracy: %.2f%n",
                label, customFormModelField.getName(), customFormModelField.getAccuracy()));
    });
    return customFormModel.getModelId();
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Analizowanie formularzy przy użyciu modelu niestandardowego

W tej sekcji pokazano, jak wyodrębnić informacje o kluczu/wartości i innej zawartości z niestandardowych typów formularzy przy użyciu modeli przeszkolonych za pomocą własnych formularzy.

> [!IMPORTANT]
> Aby zaimplementować ten scenariusz, należy wcześniej przeszkolić model, aby można było przekazać jego identyfikator do metody poniżej. Zobacz sekcję [uczenie modelu](#train-a-model-without-labels) .

Będziesz używać metody **beginRecognizeCustomFormsFromUrl** . Zwracana wartość jest kolekcją obiektów **RecognizedForm** : jeden dla każdej strony w przesłanym dokumencie.

```java
// Analyze PDF form data
private static void AnalyzePdfForm(
    FormRecognizerClient formClient, String modelId, String pdfFormUrl)
{    
    String modelId = modelId;
    SyncPoller<OperationResult, List<RecognizedForm>> recognizeFormPoller =
        client.beginRecognizeCustomFormsFromUrl(pdfFormUrl, modelId);

    List<RecognizedForm> recognizedForms = recognizeFormPoller.getFinalResult();
```

Poniższy kod drukuje wyniki analizy w konsoli programu. Wypisuje wszystkie rozpoznane pola i odpowiadające im wartości, a także ocenę ufności.

```java
    recognizedForms.forEach(form -> {
        System.out.println("----------- Recognized Form -----------");
        System.out.printf("Form type: %s%n", form.getFormType());
        form.getFields().forEach((label, formField) -> {
            System.out.printf("Field %s has value %s with confidence score of %.2f.%n", label,
                formField.getFieldValue(),
                formField.getConfidence());
        });
        System.out.print("-----------------------------------");
    });
}
```

## <a name="manage-your-custom-models"></a>Zarządzanie modelami niestandardowymi

W tej sekcji pokazano, jak zarządzać modelami niestandardowymi przechowywanymi na Twoim koncie. Poniższy kod wykonuje wszystkie zadania zarządzania modelami w ramach jednej metody, na przykład. Zacznij od skopiowania poniższego podpisu metody:

```java
private static void ManageModels(
    FormRecognizerClient trainingClient, String trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Sprawdź liczbę modeli na koncie zasobów FormRecognizer

Poniższy blok kodu sprawdza, ile modeli Zapisano na koncie aparatu rozpoznawania formularzy i porównuje je z limitem konta.

```java
    AtomicReference<String> modelId = new AtomicReference<>();

    // First, we see how many custom models we have, and what our limit is
    AccountProperties accountProperties = client.getAccountProperties();
    System.out.printf("The account has %s custom models, and we can have at most %s custom models",
        accountProperties.getCustomModelCount(), accountProperties.getCustomModelLimit());
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Wyświetlanie listy modeli przechowywanych obecnie na koncie zasobu

Poniższy blok kodu zawiera listę bieżących modeli na koncie i drukuje ich szczegóły do konsoli programu.

```java    
    // Next, we get a paged list of all of our custom models
    PagedIterable<CustomFormModelInfo> customModels = client.getModelInfos();
    System.out.println("We have following models in the account:");
    customModels.forEach(customFormModelInfo -> {
        System.out.printf("Model Id: %s%n", customFormModelInfo.getModelId());
        // get custom model info
        modelId.set(customFormModelInfo.getModelId());
        CustomFormModel customModel = client.getCustomModel(customFormModelInfo.getModelId());
        System.out.printf("Model Id: %s%n", customModel.getModelId());
        System.out.printf("Model Status: %s%n", customModel.getModelStatus());
        System.out.printf("Created on: %s%n", customModel.getRequestedOn());
        System.out.printf("Updated on: %s%n", customModel.getCompletedOn());
        customModel.getSubmodels().forEach(customFormSubModel -> {
            System.out.printf("Custom Model Form type: %s%n", customFormSubModel.getFormType());
            System.out.printf("Custom Model Accuracy: %.2f%n", customFormSubModel.getAccuracy());
            if (customFormSubModel.getFieldMap() != null) {
                customFormSubModel.getFieldMap().forEach((fieldText, customFormModelField) -> {
                    System.out.printf("Field Text: %s%n", fieldText);
                    System.out.printf("Field Accuracy: %.2f%n", customFormModelField.getAccuracy());
                });
            }

        });
    });
```

### <a name="delete-a-model-from-the-resource-account"></a>Usuwanie modelu z konta zasobu

Możesz również usunąć model z konta, odwołując się do jego identyfikatora.

```java
    // Delete Custom Model
    System.out.printf("Deleted model with model Id: %s operation completed with status: %s%n", modelId.get(),
        client.deleteModelWithResponse(modelId.get(), Context.NONE).getStatusCode());
}
```


## <a name="run-the-application"></a>Uruchamianie aplikacji

Aplikację można skompilować przy użyciu:

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