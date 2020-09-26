---
title: 'Szybki Start: Biblioteka klienta aparatu rozpoznawania formularzy dla platformy .NET'
description: Za pomocą biblioteki klienta aparatu rozpoznawania formularzy dla platformy .NET można utworzyć aplikację przetwarzającej formularze, która wyodrębnia pary klucz/wartość i dane tabeli z dokumentów niestandardowych.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 09/21/2020
ms.author: pafarley
ms.openlocfilehash: fc7b435d3abdd2e04f8beabf35b7ed337c5ff68b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318930"
---
> [!IMPORTANT]
> * Zestaw SDK aparatu rozpoznawania formularzy obecnie jest przeznaczony dla wersji v 2.0 z usługi rozpoznawania.
> * Kod w tym artykule używa metod synchronicznych i niezabezpieczonych magazynów poświadczeń z przyczyn uproszczenia. Zapoznaj się z dokumentacją referencyjną poniżej. 

[Dokumentacja](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src)  |  źródłowy biblioteki [Pakiet (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services).
* Obiekt BLOB usługi Azure Storage zawierający zestaw danych szkoleniowych. Zapoznaj się z tematem [Tworzenie zestawu danych szkoleniowych dla modelu niestandardowego](../../build-training-data-set.md) w celu uzyskania wskazówek i opcji związanych z zestawem danych szkoleniowych. W tym przewodniku szybki start można użyć plików w folderze **uczenie** [zestawu danych przykładowych](https://go.microsoft.com/fwlink/?linkid=2090451).
* Bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Gdy masz subskrypcję platformy Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" Utwórz zasób aparatu rozpoznawania formularzy "  target="_blank"> Utwórz zasób aparatu rozpoznawania formularza <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który utworzysz, aby połączyć aplikację z interfejsem API rozpoznawania formularzy. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-c-application"></a>Utwórz nową aplikację w języku C#

W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową o nazwie `formrecognizer-quickstart` . To polecenie tworzy prosty projekt C# "Hello world" z pojedynczym plikiem źródłowym: _program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Następnie skompiluj aplikację przy użyciu:

```console
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

W katalogu aplikacji zainstaluj bibliotekę klienta aparatu rozpoznawania dla platformy .NET za pomocą następującego polecenia:

```console
dotnet add package Azure.AI.FormRecognizer --version 3.0.0
```

> [!TIP]
> Jeśli używasz środowiska IDE programu Visual Studio, Biblioteka kliencka jest dostępna jako pakiet NuGet do pobrania.

W katalogu projektu Otwórz plik *program.cs* w preferowanym edytorze lub w środowisku IDE. Dodaj następujące `using` dyrektywy:

```csharp
using Azure;
using Azure.AI.FormRecognizer;
using Azure.AI.FormRecognizer.Models;
using Azure.AI.FormRecognizer.Training;

using System;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
```

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

Zapoznaj się z przykładami dotyczącymi [uczenia modelu](#train-a-custom-model) i zarządzania modelami [niestandardowymi](#manage-custom-models).

> [!NOTE]
> Modele mogą być również przeszkolone przy użyciu graficznego interfejsu użytkownika, takiego jak [Narzędzie do etykietowania aparatów rozpoznawania formularzy](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/label-tool).

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań za pomocą biblioteki klienckiej aparatu rozpoznawania w programie .NET:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Rozpoznaj zawartość formularza](#recognize-form-content)
* [Rozpoznawaj potwierdzenia](#recognize-receipts)
* [Trenowanie modelu niestandardowego](#train-a-custom-model)
* [Analizowanie formularzy przy użyciu modelu niestandardowego](#analyze-forms-with-a-custom-model)
* [Zarządzanie modelami niestandardowymi](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Poniżej `Main()` można utworzyć nową metodę o nazwie `AuthenticateClient` . Zostanie ona użyta w przyszłych zadaniach w celu uwierzytelnienia żądań do usługi aparat rozpoznawania formularzy. Ta metoda używa `AzureKeyCredential` obiektu, dlatego w razie potrzeby można zaktualizować klucz interfejsu API bez tworzenia nowych obiektów klienta.

> [!IMPORTANT]
> Pobierz klucz i punkt końcowy z Azure Portal. Jeśli zasób aparatu rozpoznawania formularza utworzony w sekcji **wymagania wstępne** został wdrożony pomyślnie, kliknij przycisk **Przejdź do zasobu** w obszarze **następne kroki**. Klucz i punkt końcowy można znaleźć na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami**. 
>
> Pamiętaj, aby usunąć klucz z kodu, gdy skończysz, i nigdy nie Publikuj go publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Na przykład [Magazyn kluczy platformy Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

```csharp
static private FormRecognizerClient AuthenticateClient(){
    string endpoint = "<replace-with-your-form-recognizer-endpoint-here>";
    string apiKey = "<replace-with-your-form-recognizer-key-here>";
    var credential = new AzureKeyCredential(apiKey);
    var client = new FormRecognizerClient(new Uri(endpoint), credential);
    return client;
}
```

## <a name="get-assets-for-testing"></a>Pobierz zasoby do testowania 

Fragmenty kodu w tym przewodniku korzystają z formularzy zdalnych, do których uzyskuje dostęp za pomocą adresów URL. Jeśli zamiast tego chcesz przetworzyć lokalne dokumenty formularzy, zapoznaj się z odpowiednimi metodami w [dokumentacji referencyjnej](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer) i [przykładami](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

Należy również dodać odwołania do adresów URL dla danych szkoleniowych i testowych.

* Aby pobrać adres URL sygnatury dostępu współdzielonego dla danych szkolenia modelu niestandardowego, Otwórz Eksplorator usługi Microsoft Azure Storage, kliknij prawym przyciskiem myszy kontener, a następnie wybierz pozycję **Pobierz sygnaturę dostępną**. Upewnij się, że uprawnienia do **odczytu** i **listy** są zaznaczone, a następnie kliknij przycisk **Utwórz**. Następnie skopiuj wartość z sekcji **URL** . Powinna ona mieć postać: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Skorzystaj z obrazów przykładowych z i paragonów zawartych w poniższych przykładach (dostępnych również w witrynie [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) ) lub wykonaj powyższe kroki, aby uzyskać adres URL sygnatury dostępu współdzielonego pojedynczego dokumentu w usłudze BLOB Storage. 

> [!NOTE]
> Fragmenty kodu w tym przewodniku korzystają z formularzy zdalnych, do których uzyskuje dostęp za pomocą adresów URL. Jeśli zamiast tego chcesz przetworzyć lokalne dokumenty formularzy, zapoznaj się z odpowiednimi metodami w [dokumentacji referencyjnej](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

## <a name="recognize-form-content"></a>Rozpoznaj zawartość formularza

Aparat rozpoznawania formularzy służy do rozpoznawania tabel, wierszy i słów w dokumentach, bez konieczności uczenia modelu. Zwracana wartość jest kolekcją obiektów **FormPage** : jeden dla każdej strony w przesłanym dokumencie. 

Aby rozpoznać zawartość pliku w danym identyfikatorze URI, użyj `StartRecognizeContentFromUri` metody.

```csharp
static async Task RecognizeContent(){
    var invoiceUri = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/forms/Invoice_1.pdf";
    var recognizeClient =  AuthenticateClient();
    FormPageCollection formPages = await recognizeClient
        .StartRecognizeContentFromUri(new Uri(invoiceUri))
        .WaitForCompletionAsync();
    foreach (FormPage page in formPages)
    {
        Console.WriteLine($"Form Page {page.PageNumber} has {page.Lines.Count} lines.");

        for (int i = 0; i < page.Lines.Count; i++)
        {
            FormLine line = page.Lines[i];
            Console.WriteLine($"    Line {i} has {line.Words.Count} word{(line.Words.Count > 1 ? "s" : "")}, and text: '{line.Text}'.");
        }

        for (int i = 0; i < page.Tables.Count; i++)
        {
            FormTable table = page.Tables[i];
            Console.WriteLine($"Table {i} has {table.RowCount} rows and {table.ColumnCount} columns.");
            foreach (FormTableCell cell in table.Cells)
            {
                Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex}) contains text: '{cell.Text}'.");
            }
        }
    }
}
```

Aby uruchomić tę metodę, należy wywołać ją z `Main` . 

```csharp
static void Main(string[] args)
{
    var analyzeForm = RecognizeContent();
    Task.WaitAll(analyzeForm);
}
```

### <a name="output"></a>Dane wyjściowe

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    Line 6 has 3 words, and text: 'Sunnayvale, CA 87659'.
    Line 7 has 1 word, and text: '99243'.
    Line 8 has 2 words, and text: 'Invoice Number'.
    Line 9 has 2 words, and text: 'Invoice Date'.
    Line 10 has 3 words, and text: 'Invoice Due Date'.
    Line 11 has 1 word, and text: 'Charges'.
    Line 12 has 2 words, and text: 'VAT ID'.
    Line 13 has 1 word, and text: '34278587'.
    Line 14 has 1 word, and text: '6/18/2017'.
    Line 15 has 1 word, and text: '6/24/2017'.
    Line 16 has 1 word, and text: '$56,651.49'.
    Line 17 has 1 word, and text: 'PT'.
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    Cell (0, 5) contains text: 'VAT ID'.
    Cell (1, 0) contains text: '34278587'.
    Cell (1, 1) contains text: '6/18/2017'.
    Cell (1, 2) contains text: '6/24/2017'.
    Cell (1, 3) contains text: '$56,651.49'.
    Cell (1, 5) contains text: 'PT'.
```

## <a name="recognize-receipts"></a>Rozpoznawaj potwierdzenia

W tej sekcji przedstawiono sposób rozpoznawania i wyodrębniania typowych pól z paragonów w Stanach Zjednoczonych przy użyciu wstępnie przeszkolonego modelu paragonów.

Aby rozpoznać potwierdzenia z identyfikatora URI, użyj `StartRecognizeReceiptsFromUri` metody. Zwracana wartość jest kolekcją `RecognizedReceipt` obiektów: jeden dla każdej strony w przesłanym dokumencie. Poniższy kod przetwarza potwierdzenie dla danego identyfikatora URI i drukuje główne pola i wartości w konsoli.

```csharp
static async Task RecognizeReceipts(){
    var receiptUrl = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/receipt/contoso-receipt.png";
    var recognizeClient = AuthenticateClient();
    RecognizedFormCollection receipts = await recognizeClient.StartRecognizeReceiptsFromUri(new Uri(receiptUrl)).WaitForCompletionAsync();

    // To see the list of the supported fields returned by service and its corresponding types, consult:
    // https://aka.ms/formrecognizer/receiptfields

    foreach (RecognizedForm receipt in receipts)
    {
        FormField merchantNameField;
        if (receipt.Fields.TryGetValue("MerchantName", out merchantNameField))
        {
            if (merchantNameField.Value.ValueType == FieldValueType.String)
            {
                string merchantName = merchantNameField.Value.AsString();

                Console.WriteLine($"Merchant Name: '{merchantName}', with confidence {merchantNameField.Confidence}");
            }
        }

        FormField transactionDateField;
        if (receipt.Fields.TryGetValue("TransactionDate", out transactionDateField))
        {
            if (transactionDateField.Value.ValueType == FieldValueType.Date)
            {
                DateTime transactionDate = transactionDateField.Value.AsDate();

                Console.WriteLine($"Transaction Date: '{transactionDate}', with confidence {transactionDateField.Confidence}");
            }
        }

        FormField itemsField;
        if (receipt.Fields.TryGetValue("Items", out itemsField))
        {
            if (itemsField.Value.ValueType == FieldValueType.List)
            {
                foreach (FormField itemField in itemsField.Value.AsList())
                {
                    Console.WriteLine("Item:");

                    if (itemField.Value.ValueType == FieldValueType.Dictionary)
                    {
                        IReadOnlyDictionary<string, FormField> itemFields = itemField.Value.AsDictionary();

                        FormField itemNameField;
                        if (itemFields.TryGetValue("Name", out itemNameField))
                        {
                            if (itemNameField.Value.ValueType == FieldValueType.String)
                            {
                                string itemName = itemNameField.Value.AsString();

                                Console.WriteLine($"    Name: '{itemName}', with confidence {itemNameField.Confidence}");
                            }
                        }

                        FormField itemTotalPriceField;
                        if (itemFields.TryGetValue("TotalPrice", out itemTotalPriceField))
                        {
                            if (itemTotalPriceField.Value.ValueType == FieldValueType.Float)
                            {
                                float itemTotalPrice = itemTotalPriceField.Value.AsFloat();

                                Console.WriteLine($"    Total Price: '{itemTotalPrice}', with confidence {itemTotalPriceField.Confidence}");
                            }
                        }
                    }
                }
            }
        }

        FormField totalField;
        if (receipt.Fields.TryGetValue("Total", out totalField))
        {
            if (totalField.Value.ValueType == FieldValueType.Float)
            {
                float total = totalField.Value.AsFloat();

                Console.WriteLine($"Total: '{total}', with confidence '{totalField.Confidence}'");
            }
        }
    }
}
```

Aby uruchomić tę metodę, należy wywołać ją z `Main` . 

```csharp
static void Main(string[] args)
{
    var analyzeReceipts = RecognizeReceipts();
    Task.WaitAll(analyzeReceipts);
}
```

### <a name="output"></a>Dane wyjściowe 

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    Line 6 has 3 words, and text: 'Sunnayvale, CA 87659'.
    Line 7 has 1 word, and text: '99243'.
    Line 8 has 2 words, and text: 'Invoice Number'.
    Line 9 has 2 words, and text: 'Invoice Date'.
    Line 10 has 3 words, and text: 'Invoice Due Date'.
    Line 11 has 1 word, and text: 'Charges'.
    Line 12 has 2 words, and text: 'VAT ID'.
    Line 13 has 1 word, and text: '34278587'.
    Line 14 has 1 word, and text: '6/18/2017'.
    Line 15 has 1 word, and text: '6/24/2017'.
    Line 16 has 1 word, and text: '$56,651.49'.
    Line 17 has 1 word, and text: 'PT'.
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    Cell (0, 5) contains text: 'VAT ID'.
    Cell (1, 0) contains text: '34278587'.
    Cell (1, 1) contains text: '6/18/2017'.
    Cell (1, 2) contains text: '6/24/2017'.
    Cell (1, 3) contains text: '$56,651.49'.
    Cell (1, 5) contains text: 'PT'.
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
```

## <a name="train-a-custom-model"></a>Trenowanie modelu niestandardowego

W tej sekcji pokazano, jak szkolić model przy użyciu własnych danych. Model przeszkolony może wyprowadzać dane strukturalne, które zawierają relacje klucz/wartość w oryginalnym dokumencie formularza. Po przeprowadzeniu szkolenia modelu można testować i przeszkolić go i ostatecznie użyć do niezawodnego wyodrębnienia danych z większej liczby formularzy zgodnie z potrzebami.

> [!NOTE]
> Możesz również nauczyć modele przy użyciu graficznego interfejsu użytkownika, takiego jak [Narzędzie do próbkowania przykładowego aparatu rozpoznawania formularzy](../../quickstarts/label-tool.md).

### <a name="authenticate-the-training-client"></a>Uwierzytelnianie klienta szkoleniowego

Poniżej `AuthenticateClient` można utworzyć nową metodę o nazwie `AuthenticateTrainingClient` . Ta funkcja zostanie użyta w przyszłych zadaniach w celu uczenia modeli niestandardowych. Ta metoda używa `AzureKeyCredential` obiektu (na przykład `AuthenticateClient` ), więc w razie potrzeby można zaktualizować klucz interfejsu API bez tworzenia nowych obiektów klienta.

```csharp
static private FormTrainingClient AuthenticateTrainingClient()
{
    string endpoint = "https://formre-ga-sdk-testing.cognitiveservices.azure.com/";
    string apiKey = "<replace-with-your-form-recognizer-key-here>";
    var credential = new AzureKeyCredential(apiKey);
    var trainingClient = new FormTrainingClient(new Uri(endpoint), credential);
    return trainingClient;
}
```

### <a name="train-a-model-without-labels"></a>Uczenie modelu bez etykiet

Uczenie modeli niestandardowych w celu rozpoznawania wszystkich pól i wartości znajdujących się w formularzach niestandardowych bez ręcznego etykietowania dokumentów szkoleniowych. Poniższa metoda pociąga za model dla danego zestawu dokumentów i drukuje stan modelu w konsoli programu. Zwrócony `CustomFormModel` obiekt zawiera informacje na temat typów formularzy, które może rozpoznać model, oraz pól, które mogą zostać wyodrębnione z każdego typu formularza. Poniższy blok kodu drukuje te informacje w konsoli programu.

```csharp
static async Task TrainCustomModelNoLabels()
{
    var trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    var trainingClient = AuthenticateTrainingClient();
    CustomFormModel model = await trainingClient
        .StartTrainingAsync(new Uri(trainingDataUrl), useTrainingLabels: false)
        .WaitForCompletionAsync();
    Console.WriteLine($"Custom Model Info:");
    Console.WriteLine($"    Model Id: {model.ModelId}");
    Console.WriteLine($"    Model Status: {model.Status}");
    Console.WriteLine($"    Training model started on: {model.TrainingStartedOn}");
    Console.WriteLine($"    Training model completed on: {model.TrainingCompletedOn}");

    foreach (CustomFormSubmodel submodel in model.Submodels)
    {
        Console.WriteLine($"Submodel Form Type: {submodel.FormType}");
        foreach (CustomFormModelField field in submodel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Label != null)
            {
                Console.Write($", FieldLabel: {field.Label}");
            }
            Console.WriteLine("");
        }
    }
}
```

Aby uruchomić tę metodę, należy wywołać ją z `Main` . 

```csharp
static void Main(string[] args)
{
    var trainCustomModel = TrainCustomModelNoLabels();
    Task.WaitAll(trainCustomModel);
}
```

### <a name="output"></a>Dane wyjściowe

Ta odpowiedź została obcięta na potrzeby czytelności.

```console
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    ...
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    ... 
Custom Model Info:
    Model Id: 95035721-f19d-40eb-8820-0c806b42798b
    Model Status: Ready
    Training model started on: 8/24/2020 6:36:44 PM +00:00
    Training model completed on: 8/24/2020 6:36:50 PM +00:00
Submodel Form Type: form-95035721-f19d-40eb-8820-0c806b42798b
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    ... 
Custom Model Info:
    Model Id: e7a1181b-1fb7-40be-bfbe-1ee154183633
    Model Status: Ready
    Training model started on: 8/24/2020 6:36:44 PM +00:00
    Training model completed on: 8/24/2020 6:36:52 PM +00:00
Submodel Form Type: form-0
    FieldName: field-0, FieldLabel: Additional Notes:
    FieldName: field-1, FieldLabel: Address:
    FieldName: field-2, FieldLabel: Company Name:
    FieldName: field-3, FieldLabel: Company Phone:
    FieldName: field-4, FieldLabel: Dated As:
    FieldName: field-5, FieldLabel: Details
    FieldName: field-6, FieldLabel: Email:
    FieldName: field-7, FieldLabel: Hero Limited
    FieldName: field-8, FieldLabel: Name:
    FieldName: field-9, FieldLabel: Phone:
    ...
```

### <a name="train-a-model-with-labels"></a>Uczenie modelu z etykietami

Można także uczenie modeli niestandardowych przez ręczne etykietowanie dokumentów szkoleniowych. Szkolenie z etykietami prowadzi do lepszej wydajności w niektórych scenariuszach. Aby szkolić z etykietami, musisz mieć specjalne pliki informacji o etykietach ( `\<filename\>.pdf.labels.json` ) w kontenerze magazynu obiektów BLOB obok dokumentów szkoleniowych. [Narzędzie do etykietowania próbek aparatu rozpoznawania formularzy](../../quickstarts/label-tool.md) udostępnia interfejs użytkownika ułatwiający Tworzenie tych plików etykiet. Po ich utworzeniu można wywołać `StartTrainingAsync` metodę z `uselabels` parametrem ustawionym na `true` . Zwracana wartość `CustomFormModel` wskazuje pola, które mogą zostać wyodrębnione przez model, wraz z szacowaną dokładnością dla każdego pola. Poniższy blok kodu drukuje te informacje w konsoli programu.

```csharp
static async Task TrainCustomModelWithLabels()
{
    var trainingDataUrl = "<SAS-URL-of-your-form-folder-in-blob-storage>";
    var trainingClient = AuthenticateTrainingClient();

    CustomFormModel model = await trainingClient
        .StartTrainingAsync(new Uri(trainingDataUrl), useTrainingLabels: true)
        .WaitForCompletionAsync();
    Console.WriteLine($"Custom Model Info:");
    Console.WriteLine($"    Model Id: {model.ModelId}");
    Console.WriteLine($"    Model Status: {model.Status}");
    Console.WriteLine($"    Training model started on: {model.TrainingStartedOn}");
    Console.WriteLine($"    Training model completed on: {model.TrainingCompletedOn}");

    foreach (CustomFormSubmodel submodel in model.Submodels)
    {
        Console.WriteLine($"Submodel Form Type: {submodel.FormType}");
        foreach (CustomFormModelField field in submodel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Label != null)
            {
                Console.Write($", FieldLabel: {field.Label}");
            }
            Console.WriteLine("");
        }
    }
}
```

Aby uruchomić tę metodę, należy wywołać ją z `Main` . 

```csharp
static void Main(string[] args)
{
    var trainCustomModel = TrainCustomModelWithLabels();
    Task.WaitAll(trainCustomModel);
}
```

### <a name="output"></a>Dane wyjściowe

Ta odpowiedź została obcięta na potrzeby czytelności.

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    ...
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    ... 
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Custom Model Info:
    Model Id: 63c013e3-1cab-43eb-84b0-f4b20cb9214c
    Model Status: Ready
    Training model started on: 8/24/2020 6:42:54 PM +00:00
    Training model completed on: 8/24/2020 6:43:01 PM +00:00
Submodel Form Type: form-63c013e3-1cab-43eb-84b0-f4b20cb9214c
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    FieldName: DatedAs
    FieldName: Email
    FieldName: Merchant
    ... 
```

## <a name="analyze-forms-with-a-custom-model"></a>Analizowanie formularzy przy użyciu modelu niestandardowego

W tej sekcji pokazano, jak wyodrębnić informacje o kluczu/wartości i innej zawartości z niestandardowych typów formularzy przy użyciu modeli przeszkolonych za pomocą własnych formularzy.

> [!IMPORTANT]
> Aby zaimplementować ten scenariusz, należy wcześniej przeszkolić model, aby można było przekazać jego identyfikator do metody poniżej.

Ta metoda zostanie użyta `StartRecognizeCustomFormsFromUri` . Zwracana wartość jest kolekcją `RecognizedForm` obiektów: jeden dla każdej strony w przesłanym dokumencie. Poniższy kod drukuje wyniki analizy w konsoli programu. Wypisuje wszystkie rozpoznane pola i odpowiadające im wartości, a także ocenę ufności.

```csharp
static async Task RecognizeContentCustomModel()
{
    // Use the custom model ID returned in the previous example.
    string modelId = "<modelId>";
    var invoiceUri = "https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/tests/sample_forms/forms/Invoice_1.pdf";
    var recognizeClient = AuthenticateClient();

    RecognizedFormCollection forms = await recognizeClient
    .StartRecognizeCustomFormsFromUri(modelId, new Uri(invoiceUri))
    .WaitForCompletionAsync();
    foreach (RecognizedForm form in forms)
    {
        Console.WriteLine($"Form of type: {form.FormType}");
        foreach (FormField field in form.Fields.Values)
        {
            Console.WriteLine($"Field '{field.Name}: ");

            if (field.LabelData != null)
            {
                Console.WriteLine($"    Label: '{field.LabelData.Text}");
            }

            Console.WriteLine($"    Value: '{field.ValueData.Text}");
            Console.WriteLine($"    Confidence: '{field.Confidence}");
        }
        Console.WriteLine("Table data:");
        foreach (FormPage page in form.Pages.Values)
        {
            for (int i = 0; i < page.Tables.Count; i++)
            {
                FormTable table = page.Tables[i];
                Console.WriteLine($"Table {i} has {table.RowCount} rows and {table.ColumnCount} columns.");
                foreach (FormTableCell cell in table.Cells)
                {
                    Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex}) contains {(cell.IsHeader ? "header" : "text")}: '{cell.Text}'");
                }
            }
        }
    }
}
```

Aby uruchomić tę metodę, należy wywołać ją z `Main` . 

```csharp
static void Main(string[] args)
{
    var recognizeContentCustomModel = RecognizeContentCustomModel();
    Task.WaitAll(recognizeContentCustomModel);
}
```

### <a name="output"></a>Dane wyjściowe

Ta odpowiedź została obcięta na potrzeby czytelności.

```console
Custom Model Info:
    Model Id: 9b0108ee-65c8-450e-b527-bb309d054fc4
    Model Status: Ready
    Training model started on: 8/24/2020 7:00:31 PM +00:00
    Training model completed on: 8/24/2020 7:00:32 PM +00:00
Submodel Form Type: form-9b0108ee-65c8-450e-b527-bb309d054fc4
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    ...
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    ...

Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    ...
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Custom Model Info:
    Model Id: dc115156-ce0e-4202-bbe4-7426e7bee756
    Model Status: Ready
    Training model started on: 8/24/2020 7:00:31 PM +00:00
    Training model completed on: 8/24/2020 7:00:41 PM +00:00
Submodel Form Type: form-0
    FieldName: field-0, FieldLabel: Additional Notes:
    FieldName: field-1, FieldLabel: Address:
    FieldName: field-2, FieldLabel: Company Name:
    FieldName: field-3, FieldLabel: Company Phone:
    FieldName: field-4, FieldLabel: Dated As:
    ... 
Form of type: custom:form
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: '$56,651.49
    Confidence: '0.249
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: 'PT
    Confidence: '0.245
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: '99243
    Confidence: '0.114
   ...
```

## <a name="manage-custom-models"></a>Zarządzanie modelami niestandardowymi

W tej sekcji pokazano, jak zarządzać modelami niestandardowymi przechowywanymi na Twoim koncie. 

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Sprawdź liczbę modeli na koncie zasobów FormRecognizer

Poniższy blok kodu sprawdza, ile modeli Zapisano na koncie aparatu rozpoznawania formularzy i porównuje je z limitem konta.

```csharp
static void CheckNumberOfModels()
{
    var trainingClient = AuthenticateTrainingClient();
    AccountProperties accountProperties = trainingClient.GetAccountProperties();
    Console.WriteLine($"Account has {accountProperties.CustomModelCount} models.");
    Console.WriteLine($"It can have at most {accountProperties.CustomModelLimit} models.");
}
```

Aby uruchomić tę metodę, należy wywołać ją z `Main` . 

```csharp
static void Main(string[] args)
{
    CheckNumberOfModels();
}
```

### <a name="output"></a>Dane wyjściowe 

```console
Account has 20 models.
It can have at most 5000 models.
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Wyświetlanie listy modeli przechowywanych obecnie na koncie zasobu

Poniższy blok kodu zawiera listę bieżących modeli na koncie i drukuje ich szczegóły do konsoli programu.

```csharp
static void ListAllModels()
{
    var trainingClient = AuthenticateTrainingClient();

    Pageable<CustomFormModelInfo> models = trainingClient.GetCustomModels();

    foreach (CustomFormModelInfo modelInfo in models)
    {
        Console.WriteLine($"Custom Model Info:");
        Console.WriteLine($"    Model Id: {modelInfo.ModelId}");
        Console.WriteLine($"    Model Status: {modelInfo.Status}");
        Console.WriteLine($"    Training model started on: {modelInfo.TrainingStartedOn}");
        Console.WriteLine($"    Training model completed on: {modelInfo.TrainingCompletedOn}");
    }   
}
```

Aby uruchomić tę metodę, należy wywołać ją z `Main` . 

```csharp
static void Main(string[] args)
{
    ListAllModels();
}
```

### <a name="output"></a>Dane wyjściowe 

Ta odpowiedź została obcięta na potrzeby czytelności.

```console
Custom Model Info:
    Model Id: 05932d5a-a2f8-4030-a2ef-4e5ed7112515
    Model Status: Creating
    Training model started on: 8/24/2020 7:35:02 PM +00:00
    Training model completed on: 8/24/2020 7:35:02 PM +00:00
Custom Model Info:
    Model Id: 150828c4-2eb2-487e-a728-60d5d504bd16
    Model Status: Ready
    Training model started on: 8/24/2020 7:33:25 PM +00:00
    Training model completed on: 8/24/2020 7:33:27 PM +00:00
Custom Model Info:
    Model Id: 3303e9de-6cec-4dfb-9e68-36510a6ecbb2
    Model Status: Ready
    Training model started on: 8/24/2020 7:29:27 PM +00:00
    Training model completed on: 8/24/2020 7:29:36 PM +00:00
```

### <a name="get-a-specific-model-using-the-models-id"></a>Pobierz konkretny model przy użyciu identyfikatora modelu

Poniższy blok kodu pociąga za nowy model (podobnie jak w sekcji [uczenie modelu](#train-a-model-without-labels) ), a następnie pobiera drugie odwołanie przy użyciu jego identyfikatora.

```csharp
static void GetModelById()
{
    // Use the custom model ID returned in the previous example.
    string modelId = "<modelId>";
    var trainingClient = AuthenticateTrainingClient();
    CustomFormModel modelCopy = trainingClient.GetCustomModel(modelId);

    Console.WriteLine($"Custom Model {modelCopy.ModelId} recognizes the following form types:");

    foreach (CustomFormSubmodel submodel in modelCopy.Submodels)
    {
        Console.WriteLine($"Submodel Form Type: {submodel.FormType}");
        foreach (CustomFormModelField field in submodel.Fields.Values)
        {
            Console.Write($"    FieldName: {field.Name}");
            if (field.Label != null)
            {
                Console.Write($", FieldLabel: {field.Label}");
            }
            Console.WriteLine("");
        }
    }
}    
```

Aby uruchomić tę metodę, należy wywołać ją z `Main` . 

```csharp
static void Main(string[] args)
{
    GetModelById();
}
```

### <a name="output"></a>Dane wyjściowe 

Ta odpowiedź została obcięta na potrzeby czytelności.

```console
Custom Model Info:
    Model Id: 150828c4-2eb2-487e-a728-60d5d504bd16
    Model Status: Ready
    Training model started on: 8/24/2020 7:33:25 PM +00:00
    Training model completed on: 8/24/2020 7:33:27 PM +00:00
Submodel Form Type: form-150828c4-2eb2-487e-a728-60d5d504bd16
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    FieldName: DatedAs
    FieldName: Email
    FieldName: Merchant
    FieldName: PhoneNumber
    FieldName: PurchaseOrderNumber
    FieldName: Quantity
    FieldName: Signature
    FieldName: Subtotal
    FieldName: Tax
    FieldName: Total
    FieldName: VendorName
    FieldName: Website
...
```

### <a name="delete-a-model-from-the-resource-account"></a>Usuwanie modelu z konta zasobu

Możesz również usunąć model z konta, odwołując się do jego identyfikatora.

```csharp
static void DeleteModel()
{
    // Use the custom model ID returned in the previous example.
    string modelId = "<modelId>";
    var trainingClient = AuthenticateTrainingClient();
    trainingClient.DeleteModel(modelId);
} 
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aplikację można uruchomić w dowolnym momencie z dowolną liczbą funkcji, które zostały odczytane w tym przewodniku szybki start za pomocą tego polecenia:

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

W przypadku korzystania z biblioteki klienta aparatu rozpoznawania Cognitive Services formularzy przy użyciu zestawu .NET SDK błędy zwrócone przez usługę spowodują powstanie `RequestFailedException` . Zawierają one ten sam kod stanu HTTP, który został zwrócony przez żądanie interfejsu API REST.

Na przykład jeśli przesyłasz obraz paragonu z nieprawidłowym identyfikatorem URI, `400` zostanie zwrócony błąd, wskazujący na "złe żądanie".

```csharp
try
{
    RecognizedReceiptCollection receipts = await client.StartRecognizeReceiptsFromUri(new Uri(receiptUri)).WaitForCompletionAsync();
}
catch (RequestFailedException e)
{
    Console.WriteLine(e.ToString());
}
```

Zauważysz, że rejestrowane są dodatkowe informacje, takie jak identyfikator żądania klienta operacji.

```
Message:
    Azure.RequestFailedException: Service request failed.
    Status: 400 (Bad Request)

Content:
    {"error":{"code":"FailedToDownloadImage","innerError":
    {"requestId":"8ca04feb-86db-4552-857c-fde903251518"},
    "message":"Failed to download image from input URL."}}

Headers:
    Transfer-Encoding: chunked
    x-envoy-upstream-service-time: REDACTED
    apim-request-id: REDACTED
    Strict-Transport-Security: REDACTED
    X-Content-Type-Options: REDACTED
    Date: Mon, 20 Apr 2020 22:48:35 GMT
    Content-Type: application/json; charset=utf-8
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start użyto biblioteki klienta .NET rozpoznawania formularzy do uczenia modeli i analizowania formularzy na różne sposoby. Następnie zapoznaj się z poradami, aby utworzyć lepszy zestaw danych szkoleniowych i uzyskać bardziej dokładne modele.

> [!div class="nextstepaction"]
> [Tworzenie zestawu danych szkoleniowych](../../build-training-data-set.md)

* [Co to jest rozpoznawanie formularzy?](../../overview.md)
* Przykładowy kod z tego przewodnika (i więcej) można znaleźć w witrynie [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md).
