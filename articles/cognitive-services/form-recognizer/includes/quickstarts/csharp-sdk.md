---
title: 'Szybki start: Rozpoznawanie formularzy klienta dla programu .NET'
description: Użyj biblioteki Rozpoznawanie formularzy klienta dla programu .NET, aby utworzyć aplikację do przetwarzania formularzy, która wyodrębnia pary klucz/wartość i dane tabeli z dokumentów niestandardowych.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 04/14/2021
ms.author: lajanuar
ms.openlocfilehash: b7a35046a7f170365974c50a5be99f35cb4a868f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516476"
---
<!-- markdownlint-disable MD024 -->
> [!IMPORTANT]
> Kod w tym artykule używa metod synchronicznych i niezabędnego magazynu poświadczeń dla uproszczenia.

[Dokumentacja referencyjna](/dotnet/api/overview/azure/ai.formrecognizer-readme)  |  [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src)  |  [Pakiet (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Program [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) lub bieżącą wersję programu [.NET Core.](https://dotnet.microsoft.com/download/dotnet-core)
* Obiekt blob usługi Azure Storage zawierający zestaw danych szkoleniowych. Aby uzyskać porady i opcje dotyczące budowania zestawu danych treningowych, zobacz Build a training data [set for a custom model](../../build-training-data-set.md) (Tworzenie zestawu danych treningowych dla modelu niestandardowego). W tym przewodniku Szybki start możesz użyć plików w folderze **Train** przykładowego zestawu danych [(pobierz](https://go.microsoft.com/fwlink/?linkid=2090451) i wyodrębnij plik *sample_data.zip*).
* Po utworzeniu subskrypcji platformy Azure utwórz zasób Rozpoznawanie formularzy zasobów Rozpoznawanie formularzy witrynie Azure Portal, aby uzyskać <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" "  target="_blank"> klucz i punkt </a> końcowy. Po wdrożeniu kliknij pozycję **Przejdź do zasobu**.
  * Klucz i punkt końcowy z zasobu, który utworzysz, będą potrzebne do połączenia aplikacji z Rozpoznawanie formularzy API. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
  * Możesz użyć warstwy cenowej Bezpłatna ( ), aby wypróbować usługę, i przejść później na warstwę płatną `F0` na użytek produkcji.

## <a name="setting-up"></a>Konfigurowanie

W oknie konsoli (takim jak cmd, PowerShell lub Bash) użyj polecenia , aby utworzyć nową aplikację `dotnet new` konsolową o nazwie `formrecognizer-quickstart` . To polecenie tworzy prosty projekt języka C# "Hello world" z pojedynczym plikiem źródłowym: *program.cs.*

```console
dotnet new console -n formrecognizer-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować za pomocą:

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

### <a name="install-the-client-library"></a>Instalowanie biblioteki klienta

W katalogu aplikacji zainstaluj bibliotekę Rozpoznawanie formularzy klienta dla programu .NET za pomocą następującego polecenia:

#### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/preview)

```console
dotnet add package Azure.AI.FormRecognizer --version 3.1.0-beta.4
```

> [!NOTE]
> Zestaw SDK Rozpoznawanie formularzy 3.1.0-beta.4 odzwierciedla _API wersji 2.1-preview.3.

#### <a name="v20"></a>[Wersja 2.0](#tab/ga)

```console
dotnet add package Azure.AI.FormRecognizer --version 3.0.0
```

> [!NOTE]
> Zestaw SDK Rozpoznawanie formularzy 3.0.0 odzwierciedla interfejs API w wersji 2.0

---

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można go znaleźć w witrynie [GitHub,](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)która zawiera przykłady kodu w tym przewodniku Szybki start.

W katalogu projektu otwórz plik *Program.cs* w preferowanym edytorze lub w preferowanym idee. Dodaj następujące `using` dyrektywy:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_using)]

W klasie **Program** aplikacji utwórz zmienne dla klucza i punktu końcowego zasobu.

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli zasób Rozpoznawanie formularzy utworzony w sekcji **Wymagania** wstępne został pomyślnie wdrożony, kliknij przycisk Przejdź do **zasobu** w obszarze Następne **kroki.** Klucz i punkt końcowy można znaleźć  na stronie klucza i punktu końcowego zasobu w obszarze **zarządzanie zasobami**.
>
> Pamiętaj, aby usunąć klucz z kodu, gdy wszystko będzie gotowe, i nigdy nie publikować go publicznie. W środowisku produkcyjnym rozważ użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Zobacz artykuł Cognitive Services [zabezpieczeń,](../../../cognitive-services-security.md) aby uzyskać więcej informacji.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_creds)]

W metodzie **Main aplikacji** dodaj wywołanie do zadań asynchronicznych używanych w tym przewodniku Szybki start. Zostaną one zaimplementowane później.

#### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/preview)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_main)]

#### <a name="v20"></a>[Wersja 2.0](#tab/ga)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_main)]

---

## <a name="object-model"></a>Model obiektów

Za Rozpoznawanie formularzy można utworzyć dwa różne typy klientów. Pierwszy z nich `FormRecognizerClient` służy do wykonywania zapytań w usłudze w celu rozpoznanych pól formularza i zawartości. Drugi to użycie do tworzenia modeli niestandardowych, których można używać do ulepszania rozpoznawania, oraz `FormTrainingClient` zarządzania nimi.

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` Udostępnia operacje dla:

* Rozpoznawanie pól formularza i zawartości przy użyciu modeli niestandardowych wytrenowanych do analizowania formularzy niestandardowych.  Te wartości są zwracane w kolekcji `RecognizedForm` obiektów. Zobacz przykład [Analyze custom forms (Analizowanie formularzy niestandardowych).](#analyze-forms-with-a-custom-model)
* Rozpoznawanie zawartości formularza, w tym tabel, linii i słów, bez konieczności trenowania modelu.  Zawartość formularza jest zwracana w kolekcji `FormPage` obiektów. Zobacz przykład [układu Analizowanie](#analyze-layout).
* Rozpoznawanie typowych pól z dokumentów paragonów w USA, wizytówek, faktur i tożsamości przy użyciu wstępnie wytrenowany model w Rozpoznawanie formularzy usługi.

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` Udostępnia operacje dla:

* Trenuj modele niestandardowe w celu analizowania wszystkich pól i wartości znalezionych w formularzach niestandardowych.  Zwracany jest typ formularza, który będzie analizowany przez model, oraz pola `CustomFormModel` wyodrębnione dla każdego typu formularza.
* Trenowanie modeli niestandardowych w celu analizowania określonych pól i wartości określonych przez etykietowanie formularzy niestandardowych.  Jest `CustomFormModel` zwracana wartość wskazująca pola, które model wyodrębni, a także szacowaną dokładność dla każdego pola.
* Zarządzanie modelami utworzonymi na koncie.
* Kopiowanie modelu niestandardowego z jednego zasobu Rozpoznawanie formularzy do innego.

Zobacz przykłady [dotyczące trenowania modelu i](#train-a-custom-model) zarządzania [modelami niestandardowymi.](#manage-custom-models)

> [!NOTE]
> Modele można również wytrenować przy użyciu graficznego interfejsu użytkownika, takiego [jak Rozpoznawanie formularzy Labeling Tool.](../../quickstarts/label-tool.md)

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące zadania przy użyciu Rozpoznawanie formularzy klienta dla programu .NET:
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

Poniżej **main** utwórz nową metodę o nazwie `AuthenticateClient` . Użyjesz jej w innych zadaniach do uwierzytelniania żądań w Rozpoznawanie formularzy usługi. Ta metoda używa obiektu , aby w razie potrzeby można było zaktualizować klucz `AzureKeyCredential` interfejsu API bez tworzenia nowych obiektów klienta.

> [!IMPORTANT]
> Pobierz klucz i punkt końcowy z Azure Portal. Jeśli zasób Rozpoznawanie formularzy utworzony w sekcji **Wymagania wstępne** został pomyślnie wdrożony, kliknij przycisk Przejdź do **zasobu** w obszarze **Następne kroki.** Klucz i punkt końcowy można znaleźć  na stronie klucza i punktu końcowego zasobu w obszarze **zarządzania zasobami.**
>
> Pamiętaj, aby usunąć klucz z kodu, gdy wszystko będzie gotowe, i nigdy nie publikować go publicznie. W środowisku produkcyjnym rozważ użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Na przykład Azure [Key Vault](../../../../key-vault/general/overview.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth)]

Powtórz powyższe kroki dla nowej metody, która uwierzytelnia klienta trenowania.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth_training)]

## <a name="get-assets-for-testing"></a>Pobierz zasoby do testowania

Musisz również dodać odwołania do adresów URL dla danych szkoleniowych i testowych. Dodaj je do katalogu głównego klasy **Program.**

* [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Pobieranie adresu URL sygnatury dostępu współdzielonego":::
* Następnie powtórz powyższe kroki, aby uzyskać adres URL sygnatury dostępu współdzielonego pojedynczego dokumentu w kontenerze magazynu obiektów blob. Zapisz go również w lokalizacji tymczasowej.
* Na koniec zapisz adres URL przykładowych obrazów dołączonych poniżej (dostępny również w [witrynie GitHub).](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms)

#### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/preview)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_urls)]

#### <a name="v20"></a>[Wersja 2.0](#tab/ga)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_urls)]

---

## <a name="analyze-layout"></a>Analizowanie układu

Za pomocą Rozpoznawanie formularzy można analizować tabele, wiersze i słowa w dokumentach bez konieczności trenowania modelu. Zwrócona wartość jest kolekcją obiektów **FormPage:** po jednym dla każdej strony w przesłanym dokumencie. Aby uzyskać więcej informacji na temat wyodrębniania układu, zobacz [Przewodnik koncepcyjny układu](../../concept-layout.md).

Aby przeanalizować zawartość pliku pod danym adresem URL, użyj `StartRecognizeContentFromUri` metody .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_call)]

> [!TIP]
> Zawartość można również pobrać z pliku lokalnego. Zobacz metody [FormRecognizerClient,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) takie jak **StartRecognizeContent.** Możesz też zobaczyć przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) uzyskać scenariusze obejmujące obrazy lokalne.

W pozostałej części tego zadania informacje o zawartości są drukowane w konsoli programu .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_print)]

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

## <a name="analyze-receipts"></a>Analizowanie paragonów

W tej sekcji pokazano, jak analizować i wyodrębniać typowe pola z paragonów w USA przy użyciu wstępnie wytrenowanych modeli paragonów. Aby uzyskać więcej informacji na temat analizy paragonów, zobacz Przewodnik koncepcyjny [dotyczący paragonów](../../concept-receipts.md).

Aby analizować paragony z adresu URL, użyj `StartRecognizeReceiptsFromUri` metody .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_call)]

> [!TIP]
> Można również analizować lokalne obrazy paragonów. Zobacz metody [FormRecognizerClient,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) takie jak **StartRecognizeReceipts.** Możesz też zobaczyć przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) uzyskać scenariusze obejmujące obrazy lokalne.

Zwrócona wartość jest kolekcją `RecognizedForm` obiektów: po jednym dla każdej strony w przesłanym dokumencie. Poniższy kod przetwarza potwierdzenie przy danym URI i drukuje główne pola i wartości w konsoli.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_print)]

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

## <a name="analyze-business-cards"></a>Analizowanie wizytówek

####  <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/preview)

W tej sekcji pokazano, jak analizować i wyodrębniać typowe pola z wizytówek w języku angielskim przy użyciu wstępnie wytrenowany model. Aby uzyskać więcej informacji na temat analizy wizytówek, zobacz przewodnik koncepcyjny [Dotyczący wizytówek.](../../concept-business-cards.md)

Aby analizować wizytówki z adresu URL, użyj `StartRecognizeBusinessCardsFromUriAsync` metody .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_bc_call)]

> [!TIP]
> Można również analizować lokalne obrazy paragonów. Zobacz metody [FormRecognizerClient,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) takie jak **StartRecognizeBusinessCards.** Możesz też zobaczyć przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) uzyskać scenariusze obejmujące obrazy lokalne.

Poniższy kod przetwarza wizytówkę pod danym URI i drukuje główne pola i wartości w konsoli.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_bc_print)]

#### <a name="v20"></a>[Wersja 2.0](#tab/ga)

> [!IMPORTANT]
> Ta funkcja nie jest dostępna w wybranej wersji interfejsu API.

---

## <a name="analyze-invoices"></a>Analizowanie faktur

#### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/preview)

W tej sekcji pokazano, jak analizować i wyodrębniać typowe pola z faktur sprzedaży przy użyciu wstępnie wytrenego modelu. Aby uzyskać więcej informacji na temat analizy faktur, zobacz [Przewodnik koncepcyjny dotyczący faktur](../../concept-invoices.md).

Aby analizować faktury na podstawie adresu URL, użyj `StartRecognizeInvoicesFromUriAsync` metody .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_invoice_call)]

> [!TIP]
> Można również analizować lokalne obrazy faktur. Zobacz metody [FormRecognizerClient,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) takie jak **StartRecognizeInvoices.** Możesz też zobaczyć przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) uzyskać scenariusze obejmujące obrazy lokalne.

Poniższy kod przetwarza fakturę pod danym URI i drukuje główne pola i wartości w konsoli.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_invoice_print)]

#### <a name="v20"></a>[Wersja 2.0](#tab/ga)

> [!IMPORTANT]
> Ta funkcja nie jest dostępna w wybranej wersji interfejsu API.

---

## <a name="analyze-identity-documents"></a>Analizowanie dokumentów tożsamości

#### <a name="v21-preview"></a>[Wersja zapoznawcza 2.1](#tab/preview)

W tej sekcji pokazano, jak analizować i wyodrębniać kluczowe informacje z dokumentów identyfikacyjnych wystawionych przez rząd — na całym świecie paszportów i prawa jazdy w Stanach Zjednoczonych — przy użyciu Rozpoznawanie formularzy wstępnie utworzonego modelu identyfikatorów. Aby uzyskać więcej informacji na temat analizy dokumentów tożsamości, zobacz nasz przewodnik koncepcyjny dotyczący wstępnie [utworzonego modelu](../../concept-identification-cards.md)identyfikacji .

Aby analizować dokumenty tożsamości z użyciem URI, użyj `StartRecognizeIdDocumentsFromUriAsync` metody .

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs" id="snippet_id_call":::

> [!TIP]
> Można również analizować obrazy dokumentów tożsamości lokalnej. Zobacz metody [FormRecognizerClient,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) takie jak **StartRecognizeIdDocumentsAsync**. Zapoznaj się również z przykładowym kodem w witrynie [GitHub, aby uzyskać](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) scenariusze obejmujące obrazy lokalne.

Poniższy kod przetwarza dokument tożsamości pod danym URI i drukuje główne pola i wartości w konsoli.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs" id="snippet_id_print":::

#### <a name="v20"></a>[Wersja 2.0](#tab/ga)

> [!IMPORTANT]
> Ta funkcja nie jest dostępna w wybranej wersji interfejsu API.

---

## <a name="train-a-custom-model"></a>Trenowanie modelu niestandardowego

W tej sekcji pokazano, jak trenować model przy użyciu własnych danych. Wytrenowany model może wyprowadzać dane ustrukturyzowane, które obejmują relacje klucz/wartość w oryginalnym dokumencie formularza. Po wytrenowania modelu możesz go przetestować i ponownie wytrenować, a następnie użyć go w celu niezawodnego wyodrębnienia danych z większej liczby formularzy zgodnie z potrzebami.

> [!NOTE]
> Modele można również trenować za pomocą graficznego interfejsu użytkownika, takiego [Rozpoznawanie formularzy przykładowego narzędzia do etykietowania](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Trenowanie modelu bez etykiet

Trenowanie modeli niestandardowych w celu analizowania wszystkich pól i wartości znalezionych w formularzach niestandardowych bez ręcznego oznaczania etykietami dokumentów szkoleniowych. Następująca metoda szkoli model na podstawie danego zestawu dokumentów i drukuje stan modelu w konsoli.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train)]

Zwrócony obiekt zawiera informacje o typach formularzy, które model może analizować, oraz polach, które może `CustomFormModel` wyodrębnić z każdego typu formularza. Poniższy blok kodu drukuje te informacje w konsoli.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_response)]

Na koniec zwróć identyfikator wytrenowany modelu do użycia w kolejnych krokach.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_return)]

### <a name="output"></a>Dane wyjściowe

Ta odpowiedź została obcięta w celu czytelności.

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

### <a name="train-a-model-with-labels"></a>Trenowanie modelu przy użyciu etykiet

Modele niestandardowe można również trenować, ręcznie oznaczając etykietami dokumenty szkoleniowe. Trenowania za pomocą etykiet prowadzi do lepszej wydajności w niektórych scenariuszach. Aby trenować za pomocą etykiet, musisz mieć specjalne pliki z informacjami o etykietach ( ) w kontenerze `\<filename\>.pdf.labels.json` magazynu obiektów blob obok dokumentów szkoleniowych. Przykładowe [Rozpoznawanie formularzy etykietowania](../../quickstarts/label-tool.md) udostępnia interfejs użytkownika ułatwiający tworzenie tych plików etykiet. Gdy już je masz, możesz wywołać metodę `StartTrainingAsync` z `uselabels` parametrem ustawionym na `true` .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels)]

Zwrócona `CustomFormModel` wartość wskazuje pola, które model może wyodrębnić, wraz z szacowaną dokładnością w każdym polu. Poniższy blok kodu drukuje te informacje w konsoli.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels_response)]

### <a name="output"></a>Dane wyjściowe

Ta odpowiedź została obcięta, aby była czytelna.

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

## <a name="analyze-forms-with-a-custom-model"></a>Analizowanie formularzy za pomocą modelu niestandardowego

W tej sekcji pokazano, jak wyodrębniać informacje o kluczu/wartości i inną zawartość z niestandardowych typów formularzy przy użyciu modeli przeszkolonych przy użyciu własnych formularzy.

> [!IMPORTANT]
> Aby zaimplementować ten scenariusz, musisz już wytrenować model, aby można było przekazać jego identyfikator do poniższej metody.

Użyjesz metody `StartRecognizeCustomFormsFromUri` .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze)]

> [!TIP]
> Można również analizować plik lokalny. Zobacz metody [FormRecognizerClient,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) takie jak **StartRecognizeCustomForms.** Możesz też zobaczyć przykładowy kod w [usłudze GitHub, aby](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) uzyskać scenariusze obejmujące obrazy lokalne.

Zwrócona wartość jest kolekcją `RecognizedForm` obiektów: po jednym dla każdej strony w przesłanym dokumencie. Poniższy kod drukuje wyniki analizy w konsoli. Drukuje każde rozpoznane pole i odpowiadającą mu wartość wraz z wynikiem ufności.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze_response)]

### <a name="output"></a>Dane wyjściowe

Ta odpowiedź została obcięta, aby była czytelna.

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

W tej sekcji pokazano, jak zarządzać modelami niestandardowymi przechowywanymi na koncie. W ramach następującej metody będziesz wykonać wiele operacji:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage)]

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Sprawdzanie liczby modeli na koncie zasobu FormRecognizer

Poniższy blok kodu sprawdza liczbę modeli zapisanych na koncie Rozpoznawanie formularzy i porównuje go z limitem konta.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_count)]

### <a name="output"></a>Dane wyjściowe

```console
Account has 20 models.
It can have at most 5000 models.
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Lista modeli aktualnie przechowywanych na koncie zasobu

Poniższy blok kodu wyświetla listę bieżących modeli na Twoim koncie i wyświetla ich szczegóły w konsoli.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_list)]

### <a name="output"></a>Dane wyjściowe

Ta odpowiedź została obcięta, aby była czytelna.

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

### <a name="get-a-specific-model-using-the-models-id"></a>Uzyskiwanie określonego modelu przy użyciu identyfikatora modelu

Poniższy blok kodu szkoli nowy model (podobnie jak w sekcji [Trenowanie](#train-a-model-without-labels) modelu), a następnie pobiera drugie odwołanie do niego przy użyciu jego identyfikatora.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_get)]

### <a name="output"></a>Dane wyjściowe

Ta odpowiedź została obcięta w celu czytelności.

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

Możesz również usunąć model z konta, odwołując się do jego identyfikatora. Ten krok zamyka również metodę .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_delete)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację z katalogu aplikacji za pomocą `dotnet run` polecenia .

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services zasobów, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie wszystkich innych skojarzonych z nią zasobów.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfejs wiersza polecenia platformy Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Podczas interakcji z biblioteką Cognitive Services Rozpoznawanie formularzy klienta przy użyciu zestawu SDK platformy .NET błędy zwracane przez usługę będą powodować błąd `RequestFailedException` . Będą one zawierać ten sam kod stanu HTTP, który zostałby zwrócony przez żądanie interfejsu API REST.

Jeśli na przykład przesyłasz obraz paragonu z nieprawidłowym identyfikatorem URI, zwracany jest błąd z informacją `400` o nieprawidłowym żądaniu.

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

Zauważysz, że dodatkowe informacje, takie jak identyfikator żądania klienta operacji, są rejestrowane.

``

Komunikat: Azure.RequestFailedException: Żądanie usługi nie powiodło się.
Stan: 400 (Złe żądanie)

Zawartość: {"error":{"code":"FailedToDownloadImage","innerError": {"requestId":"8ca04feb-86db-4552-857c-fde903251518"}, "message":"Nie można pobrać obrazu z wejściowego adresu URL."}}

Headers: Transfer-Encoding: chunked x-envoy-upstream-service-time: REDACTED apim-request-id: REDACTED Strict-Transport-Security: REDACTED X-Content-Type-Options: REDACTED Date: Mon, 20 Kwi 2020 22:48:35 GMT Content-Type: application/json; charset=utf-8 ''

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start do trenowania modeli i analizowania formularzy na różne sposoby Rozpoznawanie formularzy biblioteki klienta .NET. Następnie zapoznaj się z poradami dotyczącymi tworzenia lepszego zestawu danych treningowych i tworzenia dokładniej określonych modeli.

> [!div class="nextstepaction"]
> [Tworzenie zestawu danych szkoleniowych](../../build-training-data-set.md)

* [Co to jest rozpoznawanie formularzy?](../../overview.md)
* Przykładowy kod z tego przewodnika (i innych) można znaleźć w witrynie [GitHub.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)
