---
title: Samouczek C# dotyczący używania AI w obiektach Blob platformy Azure
titleSuffix: Azure Cognitive Search
description: Przechodzenie między przykładem wyodrębniania tekstu i przetwarzania języka naturalnego za pośrednictwem zawartości w usłudze BLOB Storage przy użyciu języka C# i zestawu Azure Wyszukiwanie poznawcze .NET SDK.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 08/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 13e3f5a7d86d2e9b705fbeb104ba4f8eb690cb3a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91534106"
---
# <a name="tutorial-ai-generated-searchable-content-from-azure-blobs-using-the-net-sdk"></a>Samouczek: wygenerowane przez program AI zawartość do przeszukiwania z obiektów blob platformy Azure przy użyciu zestawu .NET SDK

Jeśli w usłudze Azure Blob Storage znajduje się tekst lub obrazy bez struktury, [potok wzbogacenia AI](cognitive-search-concept-intro.md) może wyodrębnić informacje i utworzyć nową zawartość, która jest przydatna w przypadku wyszukiwania pełnotekstowego lub scenariuszy wyszukiwania w bazie wiedzy. W tym samouczku w języku C# Zastosuj optyczne rozpoznawanie znaków (OCR) na obrazach i wykonaj przetwarzanie języka naturalnego, aby utworzyć nowe pola, których można użyć w zapytaniach, aspektach i filtrach.

Ten samouczek używa języka C# i [zestawu SDK platformy .NET](/dotnet/api/overview/azure/search) do wykonywania następujących zadań:

> [!div class="checklist"]
> * Zacznij od plików aplikacji i obrazów w usłudze Azure Blob Storage.
> * Zdefiniuj potok w celu dodania OCR, wyodrębnienia tekstu, wykrywania języka, rozpoznawania jednostek i frazy kluczowej.
> * Zdefiniuj indeks do przechowywania danych wyjściowych (nieprzetworzona zawartość oraz pary nazwa-wartość w postaci potoku).
> * Wykonaj potok, aby rozpocząć transformacje i analizę oraz utworzyć i załadować indeks.
> * Eksplorowanie wyników przy użyciu wyszukiwania pełnotekstowego i zaawansowanej składni zapytań.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Otwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Program Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Utwórz](search-create-service-portal.md) lub [Znajdź istniejącą usługę wyszukiwania](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Możesz skorzystać z bezpłatnej usługi dla tego samouczka. Bezpłatna usługa wyszukiwania ogranicza do trzech indeksów, trzech indeksatorów i trzech źródeł danych. W ramach tego samouczka tworzony jest jeden element każdego z tych typów. Przed rozpoczęciem upewnij się, że masz miejsce w usłudze, aby akceptować nowe zasoby.

## <a name="download-files"></a>Pobieranie plików

1. Otwórz ten [folder w usłudze OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) i w lewym górnym rogu, kliknij pozycję **Pobierz** , aby skopiować pliki do komputera. 

1. Kliknij prawym przyciskiem myszy plik zip i wybierz polecenie **Wyodrębnij wszystko**. Istnieje 14 plików różnych typów. W tym ćwiczeniu należy użyć 7.

Możesz również pobrać kod źródłowy dla tego samouczka. Kod źródłowy znajduje się w samouczku — folder wzbogacania (AI) w repozytorium [Azure-Search-dotnet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) .

## <a name="1---create-services"></a>1 — Tworzenie usług

Ten samouczek używa usługi Azure Wyszukiwanie poznawcze do indeksowania i zapytań, Cognitive Services w zapleczu do wzbogacania AI i usługi Azure Blob Storage, aby zapewnić dane. Ten samouczek jest objęty bezpłatną alokacją 20 transakcji na indeksator dziennie na Cognitive Services, więc jedyne usługi, które należy utworzyć, to wyszukiwanie i magazynowanie.

Jeśli to możliwe, Utwórz zarówno w tym samym regionie, jak i w grupie zasobów, co umożliwia bliskość i łatwość zarządzania. W tym przypadku konto usługi Azure Storage może znajdować się w dowolnym regionie.

### <a name="start-with-azure-storage"></a>Rozpoczynanie pracy z usługą Azure Storage

1. [Zaloguj się do Azure Portal](https://portal.azure.com/) i kliknij pozycję **+ Utwórz zasób**.

1. Wyszukaj *konto magazynu* i wybierz ofertę konta magazynu firmy Microsoft.

   ![Utwórz konto magazynu](media/cognitive-search-tutorial-blob/storage-account.png "Utwórz konto magazynu")

1. Na karcie podstawowe wymagane są następujące elementy. Zaakceptuj wartości domyślne dla wszystkich innych elementów.

   + **Grupa zasobów**. Wybierz istniejący lub Utwórz nowy, ale Użyj tej samej grupy dla wszystkich usług, aby można było zarządzać nimi zbiorczo.

   + **Nazwa konta magazynu**. Jeśli uważasz, że może istnieć wiele zasobów tego samego typu, użyj nazwy, aby odróżnić według typu i regionu, na przykład *blobstoragewestus*. 

   + **Lokalizacja**. Jeśli to możliwe, wybierz tę samą lokalizację, która jest używana dla usługi Azure Wyszukiwanie poznawcze i Cognitive Services. Pojedyncza lokalizacja unieważnia opłaty za przepustowość.

   + **Rodzaj konta**. Wybierz wartość domyślną *StorageV2 (ogólnego przeznaczenia w wersji 2)*.

1. Kliknij przycisk **Przegląd + Utwórz** , aby utworzyć usługę.

1. Po jego utworzeniu kliknij pozycję **Przejdź do zasobu** , aby otworzyć stronę przegląd.

1. Kliknij pozycję **obiekty blob** usługa.

1. Kliknij pozycję **+ kontener** , aby utworzyć kontener i nadaj mu nazwę *koło zębate-Search-demonstracyjn*.

1. Wybierz pozycję *koło zębate-Search-demonstracyjny* , a następnie kliknij pozycję **Przekaż** , aby otworzyć folder, w którym zapisano pliki do pobrania. Zaznacz wszystkie czternaście plików i kliknij przycisk **OK** , aby przekazać.

   ![Przekaż pliki przykładowe](media/cognitive-search-quickstart-blob/sample-data.png "Przekaż pliki przykładowe")

1. Przed opuszczeniem usługi Azure Storage należy uzyskać parametry połączenia, aby można było sformułować połączenie w usłudze Azure Wyszukiwanie poznawcze. 

   1. Przejdź z powrotem do strony Przegląd Twojego konta magazynu (na przykład użyto *blobstoragewestus* ). 
   
   1. W okienku nawigacji po lewej stronie wybierz pozycję **klucze dostępu** i skopiuj jeden z parametrów połączenia. 

   Ciąg połączenia jest adresem URL podobnym do poniższego przykładu:

      ```http
      DefaultEndpointsProtocol=https;AccountName=blobstoragewestus;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Zapisz parametry połączenia w Notatniku. Będzie ona potrzebna później podczas konfigurowania połączenia ze źródłem danych.

### <a name="cognitive-services"></a>Cognitive Services

Wzbogacanie AI jest obsługiwane przez Cognitive Services, w tym analiza tekstu i przetwarzanie obrazów dla przetwarzania w języku naturalnym i obrazie. Jeśli celem było ukończenie rzeczywistego prototypu lub projektu, w tym momencie należy zastanowić się, Cognitive Services (w tym samym regionie co usługa Azure Wyszukiwanie poznawcze), aby można było dołączyć go do operacji indeksowania.

W tym ćwiczeniu można jednak pominąć Inicjowanie obsługi zasobów, ponieważ usługa Azure Wyszukiwanie poznawcze może nawiązać połączenie Cognitive Services w tle i zapewnić 20 bezpłatnych transakcji dla indeksatora. Ponieważ w tym samouczku jest używanych 14 transakcji, wystarczające jest bezpłatne przydzielanie. W przypadku większych projektów Zaplanuj obsługę Cognitive Services w warstwie płatność zgodnie z rzeczywistym użyciem. Aby uzyskać więcej informacji, zobacz [Attach Cognitive Services](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Trzeci składnik to Wyszukiwanie poznawcze platformy Azure, który można [utworzyć w portalu](search-create-service-portal.md). Aby ukończyć ten przewodnik, możesz skorzystać z warstwy Bezpłatna. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Pobierz klucz API i adres URL administratora dla usługi Azure Wyszukiwanie poznawcze

Aby można było korzystać z usługi Azure Wyszukiwanie poznawcze, wymagany jest adres URL usługi i klucz dostępu. Usługa wyszukiwania jest tworzona razem z usługą, więc jeśli do subskrypcji dodano Wyszukiwanie poznawcze platformy Azure, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W obszarze **Ustawienia**  >  **klucze**Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

   Pobierz również klucz zapytania. Najlepszym rozwiązaniem jest wydawanie żądań zapytań z dostępem tylko do odczytu.

   ![Pobieranie nazwy usługi i administratora oraz kluczy zapytań](media/search-get-started-nodejs/service-name-and-keys.png)

Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="2---set-up-your-environment"></a>2 — Konfigurowanie środowiska

Zacznij od otwierania programu Visual Studio i tworzenia nowego projektu aplikacji konsolowego, który można uruchomić w środowisku .NET Core.

### <a name="install-nuget-packages"></a>Instalowanie pakietów NuGet

[Zestaw Azure wyszukiwanie poznawcze .NET SDK](/dotnet/api/overview/azure/search) składa się z kilku bibliotek klienckich, które umożliwiają zarządzanie indeksami, źródłami danych, indeksatorami i umiejętności, a także przekazywaniem i zarządzaniem dokumentami oraz wykonywanie zapytań, bez konieczności zajmowania się szczegółowymi informacjami dotyczącymi protokołu HTTP i JSON. Te biblioteki klienckie są dystrybuowane jako pakiety NuGet.

Dla tego projektu Zainstaluj pakiet NuGet w wersji 9 lub nowszej `Microsoft.Azure.Search` .

1. W przeglądarce przejdź na [stronę pakietu NuGet Microsoft. Azure. Search](https://www.nuget.org/packages/Microsoft.Azure.Search).

1. Wybierz najnowszą wersję (9 lub nowszą).

1. Skopiuj polecenie Menedżera pakietów.

1. Otwórz konsolę Menedżera pakietów. Wybierz kolejno pozycje **Narzędzia**Menedżer  >  **pakietów NuGet**  >  **konsola Menedżera pakietów**. 

1. Wklej i uruchom polecenie skopiowane w poprzednim kroku.

Następnie zainstaluj najnowszy `Microsoft.Extensions.Configuration.Json` pakiet NuGet.

1. Wybierz kolejno pozycje **Narzędzia**  >  **Menedżer pakietów NuGet**  >  **Zarządzaj pakietami NuGet dla rozwiązania...** 

1. Kliknij przycisk **Przeglądaj** i Wyszukaj `Microsoft.Extensions.Configuration.Json` pakiet NuGet. 

1. Wybierz pakiet, wybierz projekt, potwierdź, że wersja to najnowsza stabilna wersja, a następnie kliknij przycisk **Instaluj**.

### <a name="add-service-connection-information"></a>Dodaj informacje o połączeniu z usługą

1. Kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań i wybierz polecenie **Dodaj**  >  **nowy element.** ... 

1. Nazwij plik `appsettings.json` i wybierz pozycję **Dodaj**. 

1. Dołącz ten plik do katalogu wyjściowego.
    1. Kliknij prawym przyciskiem myszy `appsettings.json` i wybierz pozycję **Właściwości**. 
    1. Zmień wartość w polu **Kopiuj do katalogu wyjściowego** na **Kopiuj, jeśli nowszy**.

1. Skopiuj poniższy kod JSON do nowego pliku JSON.

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

Dodaj usługę wyszukiwania i informacje o koncie usługi BLOB Storage. Odwołaj te informacje można uzyskać, korzystając z kroków inicjowania obsługi usługi, wskazanych w poprzedniej sekcji.

W polu **SearchServiceName**wprowadź krótką nazwę usługi, a nie pełny adres URL.

### <a name="add-namespaces"></a>Dodaj przestrzenie nazw

W programie `Program.cs` Dodaj następujące przestrzenie nazw.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Tworzenie klienta

Utwórz wystąpienie `SearchServiceClient` klasy w obszarze `Main` .

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient` Tworzy nowe `SearchServiceClient` wartości przy użyciu, które są przechowywane w pliku konfiguracyjnym aplikacji (appsettings.json).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string adminApiKey = configuration["SearchServiceAdminApiKey"];

   SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
   return serviceClient;
}
```

> [!NOTE]
> Klasa `SearchServiceClient` zarządza połączeniami z usługą wyszukiwania. W celu uniknięcia otwarcia zbyt wielu połączeń, należy, w miarę możliwości, udostępnić pojedyncze wystąpienie klasy `SearchServiceClient` w aplikacji. Metody tej klasy są bezpieczne wątkowo, co pozwala na tego rodzaju udostępnianie.
> 

### <a name="add-function-to-exit-the-program-during-failure"></a>Dodaj funkcję, aby wyjść z programu w trakcie awarii

Ten samouczek ma pomóc zrozumieć każdy krok potoku indeksowania. W przypadku wystąpienia problemu krytycznego, który uniemożliwia programowi utworzenie źródła danych, zestawu umiejętności, indeksu lub indeksatora, program będzie wyprowadzał komunikat o błędzie i wyjść, aby można było zrozumieć i rozwiązać problem.

Dodaj `ExitProgram` do, aby `Main` obsługiwać scenariusze, które wymagają zakończenia działania programu.

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3 — Tworzenie potoku

W przypadku usługi Azure Wyszukiwanie poznawcze przetwarzanie AI odbywa się podczas indeksowania (lub pozyskiwania danych). W tej części przewodnika utworzono cztery obiekty: Źródło danych, definicja indeksu, zestawu umiejętności, indeksator. 

### <a name="step-1-create-a-data-source"></a>Krok 1. Tworzenie źródła danych

`SearchServiceClient` ma właściwość `DataSources`. Ta właściwość zawiera wszystkie metody potrzebne do tworzenia, wyświetlania, aktualizowania lub usuwania źródeł danych Azure Wyszukiwanie poznawcze.

Utwórz nowe `DataSource` wystąpienie, wywołując element `serviceClient.DataSources.CreateOrUpdate(dataSource)` . `DataSource.AzureBlobStorage` wymaga określenia nazwy źródła danych, parametrów połączenia i nazwy kontenera obiektów BLOB.

```csharp
private static DataSource CreateOrUpdateDataSource(SearchServiceClient serviceClient, IConfigurationRoot configuration)
{
    DataSource dataSource = DataSource.AzureBlobStorage(
        name: "demodata",
        storageConnectionString: configuration["AzureBlobConnectionString"],
        containerName: "cog-search-demo",
        description: "Demo files to demonstrate cognitive search capabilities.");

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.DataSources.CreateOrUpdate(dataSource);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

W przypadku pomyślnego żądania Metoda zwróci źródło danych, które zostało utworzone. Jeśli wystąpi problem z żądaniem, na przykład nieprawidłowym parametrem, Metoda zgłosi wyjątek.

Teraz Dodaj wiersz `Main` do wywołania `CreateOrUpdateDataSource` funkcji, która właśnie została dodana.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    // Create or Update the data source
    Console.WriteLine("Creating or updating the data source...");
    DataSource dataSource = CreateOrUpdateDataSource(serviceClient, configuration);
```

Skompiluj i uruchom rozwiązanie. Ponieważ jest to Twoje pierwsze żądanie, sprawdź Azure Portal, aby potwierdzić, że źródło danych zostało utworzone w usłudze Azure Wyszukiwanie poznawcze. Na stronie pulpitu nawigacyjnego usługi wyszukiwania zweryfikuj, czy kafelek Źródła danych zawiera nowy element. Może trzeba będzie zaczekać kilka minut na odświeżenie strony portalu.

  ![Kafelek źródła danych w portalu](./media/cognitive-search-tutorial-blob/data-source-tile.png "Kafelek źródła danych w portalu")

### <a name="step-2-create-a-skillset"></a>Krok 2. Tworzenie elementu zestawu umiejętności

W tej sekcji definiujesz zestaw kroków wzbogacania, które chcesz zastosować do danych. Każdy krok wzbogacania jest określany jako *umiejętność* i zestaw kroków wzbogacania *zestawu umiejętności*. W tym samouczku są stosowane [wbudowane umiejętności poznawcze](cognitive-search-predefined-skills.md) dla zestawu umiejętności:

+ [Optyczne rozpoznawanie znaków](cognitive-search-skill-ocr.md) do rozpoznawania tekstu napisanego i odręcznego w plikach obrazów.

+ [Łączenie tekstu](cognitive-search-skill-textmerger.md) do konsolidowania tekstu z kolekcji pól w jedno pole.

+ [Wykrywanie języka](cognitive-search-skill-language-detection.md) — identyfikowanie języka zawartości.

+ [Dzielenie tekstu](cognitive-search-skill-textsplit.md) w celu podziału dużej zawartości na mniejsze fragmenty przed wywołaniem umiejętności wyodrębniania kluczowych fraz i umiejętności rozpoznawania jednostek. Wyodrębnianie kluczowych fraz i rozpoznawanie jednostek akceptuje dane wejściowe z 50 000 znaków lub mniej. Kilka przykładowych plików należy podzielić, aby zmieścić się w tym limicie.

+ [Rozpoznawanie jednostek](cognitive-search-skill-entity-recognition.md) do wyodrębniania nazw organizacji z zawartości kontenera obiektów BLOB.

+ [Wyodrębnianie kluczowych fraz](cognitive-search-skill-keyphrases.md) — określanie najczęściej występujących fraz kluczowych.

Podczas wstępnego przetwarzania platforma Azure Wyszukiwanie poznawcze pęknięcia każdego dokumentu w celu odczytania zawartości z różnych formatów plików. Tekst znaleziony w pliku źródłowym jest umieszczany w polu ```content``` generowanym pojedynczo dla każdego dokumentu. W związku z tym ustaw wartość wejściową, tak ```"/document/content"``` aby używała tego tekstu. 

Dane wyjściowe można mapować na indeks i/lub używać ich jako danych wejściowych umiejętności podrzędnej — jak w przypadku kodu języka. W indeksie kod języka jest przydatny do filtrowania. Kod języka jest używany jako dane wejściowe przez umiejętności analizy tekstu w celu określenia zasad podziału wyrazów przez reguły językowe.

Aby uzyskać więcej podstawowych informacji na temat zestawów umiejętności, zobacz [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>Umiejętność OCR

Umiejętność **OCR** wyodrębnia tekst z obrazów. Ta umiejętność zakłada, że pole normalized_images istnieje. Aby wygenerować to pole, w dalszej części samouczka ustawimy ```"imageAction"``` konfigurację w definicji indeksatora na ```"generateNormalizedImages"``` .

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "image",
        source: "/document/normalized_images/*")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "text",
        targetName: "text")
    };

    OcrSkill ocrSkill = new OcrSkill(
        description: "Extract text (plain and structured) from image",
        context: "/document/normalized_images/*",
        inputs: inputMappings,
        outputs: outputMappings,
        defaultLanguageCode: OcrSkillLanguage.En,
        shouldDetectOrientation: true);

    return ocrSkill;
}
```

### <a name="merge-skill"></a>Scal umiejętność

W tej sekcji utworzysz umiejętność **scalania** , która scala pole zawartości dokumentu z tekstem wyprodukowanym przez umiejętność OCR.

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/content"),
        new InputFieldMappingEntry(
        name: "itemsToInsert",
        source: "/document/normalized_images/*/text"),
        new InputFieldMappingEntry(
        name: "offsets",
        source: "/document/normalized_images/*/contentOffset")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "mergedText",
        targetName: "merged_text")
    };

    MergeSkill mergeSkill = new MergeSkill(
        description: "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        insertPreTag: " ",
        insertPostTag: " ");

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>Umiejętność wykrywania języka

Umiejętność **wykrywanie języka** wykrywa język tekstu wejściowego i raportuje jeden kod języka dla każdego dokumentu przesłanego w żądaniu. Będziemy używać danych wyjściowych **wykrywanie języka** w ramach danych wejściowych do kwalifikacji **tekstu** .

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "languageCode",
        targetName: "languageCode")
    };

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
        description: "Detect the language used in the document",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings);

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>Umiejętność podziału tekstu

Poniższa umiejętność **podziału** podzieli tekst na strony i Ogranicz długość strony do 4 000 znaków, mierzoną przez `String.Length` . Algorytm podejmie próbę podzielenia tekstu na fragmenty o największej `maximumPageLength` wielkości. W takim przypadku algorytm będzie najlepiej przekroczyć zdanie na granicy zdania, dzięki czemu rozmiar fragmentu może być nieco mniejszy niż `maximumPageLength` .

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"),
        new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "textItems",
        targetName: "pages")
    };

    SplitSkill splitSkill = new SplitSkill(
        description: "Split content into pages",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        textSplitMode: TextSplitMode.Pages,
        maximumPageLength: 4000);

    return splitSkill;
}
```

### <a name="entity-recognition-skill"></a>Umiejętność rozpoznawania jednostek

To `EntityRecognitionSkill` wystąpienie jest ustawione na rozpoznawanie typu kategorii `organization` . Umiejętność **rozpoznawania jednostek** może również rozpoznać typy kategorii `person` i `location` .

Zwróć uwagę, że pole "context" ma ustawioną ```"/document/pages/*"``` gwiazdkę, co oznacza, że krok wzbogacania jest wywoływany dla każdej strony w obszarze ```"/document/pages"``` .

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "organizations",
        targetName: "organizations")
    };

    List<EntityCategory> entityCategory = new List<EntityCategory>
    {
        EntityCategory.Organization
    };

    EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(
        description: "Recognize organizations",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings,
        categories: entityCategory,
        defaultLanguageCode: EntityRecognitionSkillLanguage.En);

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>Umiejętność wyodrębniania kluczowych fraz

Podobnie jak w przypadku `EntityRecognitionSkill` wystąpienia, które zostało właśnie utworzone, **wyodrębnianie kluczowych fraz** umiejętności są wywoływane dla każdej strony dokumentu.

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "keyPhrases",
        targetName: "keyPhrases"));

    KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(
        description: "Extract the key phrases",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings);

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>Kompilowanie i tworzenie zestawu umiejętności

Kompiluj `Skillset` przy użyciu utworzonych umiejętności.

```csharp
private static Skillset CreateOrUpdateDemoSkillSet(SearchServiceClient serviceClient, IList<Skill> skills)
{
    Skillset skillset = new Skillset(
        name: "demoskillset",
        description: "Demo skillset",
        skills: skills);

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.Skillsets.CreateOrUpdate(skillset);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a skillset");
    }

    return skillset;
}
```

Dodaj następujące wiersze do `Main` .

```csharp
    // Create the skills
    Console.WriteLine("Creating the skills...");
    OcrSkill ocrSkill = CreateOcrSkill();
    MergeSkill mergeSkill = CreateMergeSkill();
    EntityRecognitionSkill entityRecognitionSkill = CreateEntityRecognitionSkill();
    LanguageDetectionSkill languageDetectionSkill = CreateLanguageDetectionSkill();
    SplitSkill splitSkill = CreateSplitSkill();
    KeyPhraseExtractionSkill keyPhraseExtractionSkill = CreateKeyPhraseExtractionSkill();

    // Create the skillset
    Console.WriteLine("Creating or updating the skillset...");
    List<Skill> skills = new List<Skill>
    {
        ocrSkill,
        mergeSkill,
        languageDetectionSkill,
        splitSkill,
        entityRecognitionSkill,
        keyPhraseExtractionSkill
    };

    Skillset skillset = CreateOrUpdateDemoSkillSet(serviceClient, skills);
```

### <a name="step-3-create-an-index"></a>Krok 3. Tworzenie indeksu

W tej sekcji zdefiniujesz schemat indeksu przez określenie pól do umieszczenia w indeksie z możliwością wyszukiwania oraz atrybutów wyszukiwania dla każdego pola. Pola mogą mieć typ i atrybuty, które określają sposób używania pola (z możliwością wyszukiwania, sortowania itp.). Nazwy pól w indeksie nie muszą dokładnie zgadzać się z nazwami pól w źródle. W kolejnym kroku są dodawane mapowania pól w indeksatorze w celu połączenia pól źródłowych z polami docelowymi. W tym kroku zdefiniuj indeks przy użyciu konwencji nazewnictwa pól właściwej dla aplikacji wyszukiwania.

W tym ćwiczeniu są używane następujące pola i typy pól:

| Nazwy pól | Typy pól |
| --- | --- |
| `id` | Edm.String |
| `content` | Edm.String |
| `languageCode` | Edm.String |
| `keyPhrases` | List<Edm.String> |
| `organizations` | List<Edm.String> |

#### <a name="create-demoindex-class"></a>Utwórz klasę DemoIndex

Pola dla tego indeksu są definiowane przy użyciu klasy modelu. Każda właściwość klasy modelu ma atrybuty, które określają związane z wyszukiwaniem zachowania odpowiedniego pola indeksu. 

Dodamy klasę modelu do nowego pliku C#. Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Dodaj**  >  **nowy element...**, wybierz pozycję "Klasa" i Nazwij plik `DemoIndex.cs` , a następnie wybierz pozycję **Dodaj**.

Upewnij się, że chcesz użyć typów z `Microsoft.Azure.Search` `Microsoft.Azure.Search.Models` przestrzeni nazw i.

Dodaj poniższą definicję klasy modelu do `DemoIndex.cs` i Uwzględnij ją w tej samej przestrzeni nazw, w której utworzysz indeks.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;

namespace EnrichwithAI
{
    // The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
    // It ensures that Pascal-case property names in the model class are mapped to camel-case
    // field names in the index.
    [SerializePropertyNamesAsCamelCase]
    public class DemoIndex
    {
        [System.ComponentModel.DataAnnotations.Key]
        [IsSearchable, IsSortable]
        public string Id { get; set; }

        [IsSearchable]
        public string Content { get; set; }

        [IsSearchable]
        public string LanguageCode { get; set; }

        [IsSearchable]
        public string[] KeyPhrases { get; set; }

        [IsSearchable]
        public string[] Organizations { get; set; }
    }
}
```

Teraz, po zdefiniowaniu klasy modelu, `Program.cs` można łatwo utworzyć definicję indeksu. Nazwa tego indeksu będzie `demoindex` . Jeśli istnieje już indeks o tej nazwie, zostanie on usunięty.

```csharp
private static Index CreateDemoIndex(SearchServiceClient serviceClient)
{
    var index = new Index()
    {
        Name = "demoindex",
        Fields = FieldBuilder.BuildForType<DemoIndex>()
    };

    try
    {
        bool exists = serviceClient.Indexes.Exists(index.Name);

        if (exists)
        {
            serviceClient.Indexes.Delete(index.Name);
        }

        serviceClient.Indexes.Create(index);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without an index");
    }

    return index;
}
```

Podczas testowania może się okazać, że próbujesz utworzyć indeks więcej niż raz. Z tego powodu Sprawdź, czy indeks, który ma zostać utworzony, już istnieje, zanim spróbujesz go utworzyć.

Dodaj następujące wiersze do `Main` .

```csharp
    // Create the index
    Console.WriteLine("Creating the index...");
    Microsoft.Azure.Search.Models.Index demoIndex = CreateDemoIndex(serviceClient);
```

Dodaj następującą instrukcję using, aby usunąć niejednoznaczne odwołanie.

```csharp
using Index = Microsoft.Azure.Search.Models.Index;
```

Aby dowiedzieć się więcej na temat definiowania indeksu, zobacz [create index (Azure wyszukiwanie POZNAWCZE REST API)](/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Krok 4. Tworzenie i uruchamianie indeksatora

Do tej pory utworzono źródło danych, zestaw umiejętności i indeks. Te trzy składniki staną się częścią [indeksatora](search-indexer-overview.md), który łączy wszystkie części w pojedynczą operację obejmującą wiele faz. Aby powiązać je razem w indeksatorze, należy zdefiniować mapowania pól.

+ FieldMappings są przetwarzane przed zestawu umiejętności, mapując pola źródłowe ze źródła danych do pól docelowych w indeksie. Jeśli nazwy pól i typy są takie same na obu końcach, mapowanie nie jest wymagane.

+ OutputFieldMappings są przetwarzane po zestawu umiejętności, do których odwołuje się sourceFieldNames, które nie istnieją do momentu utworzenia przez nich pęknięć lub wzbogacania dokumentów. TargetFieldName to pole w indeksie.

Oprócz podłączania danych wejściowych do danych wyjściowych, można również używać mapowań pól do spłaszczania struktur. Aby uzyskać więcej informacji, zobacz [Jak mapować wzbogacone pola na indeks wyszukiwania](cognitive-search-output-field-mapping.md).

```csharp
private static Indexer CreateDemoIndexer(SearchServiceClient serviceClient, DataSource dataSource, Skillset skillSet, Index index)
{
    IDictionary<string, object> config = new Dictionary<string, object>();
    config.Add(
        key: "dataToExtract",
        value: "contentAndMetadata");
    config.Add(
        key: "imageAction",
        value: "generateNormalizedImages");

    List<FieldMapping> fieldMappings = new List<FieldMapping>
    {
        new FieldMapping(
        sourceFieldName: "metadata_storage_path",
        targetFieldName: "id",
        mappingFunction: new FieldMappingFunction(
            name: "base64Encode")),
        new FieldMapping(
        sourceFieldName: "content",
        targetFieldName: "content")
    };

    List<FieldMapping> outputMappings = new List<FieldMapping>
    {
        new FieldMapping(
        sourceFieldName: "/document/pages/*/organizations/*",
        targetFieldName: "organizations"),
        new FieldMapping(
        sourceFieldName: "/document/pages/*/keyPhrases/*",
        targetFieldName: "keyPhrases"),
        new FieldMapping(
        sourceFieldName: "/document/languageCode",
        targetFieldName: "languageCode")
    };

    Indexer indexer = new Indexer(
        name: "demoindexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        description: "Demo Indexer",
        skillsetName: skillSet.Name,
        parameters: new IndexingParameters(
            maxFailedItems: -1,
            maxFailedItemsPerBatch: -1,
            configuration: config),
        fieldMappings: fieldMappings,
        outputFieldMappings: outputMappings);

    try
    {
        bool exists = serviceClient.Indexers.Exists(indexer.Name);

        if (exists)
        {
            serviceClient.Indexers.Delete(indexer.Name);
        }

        serviceClient.Indexers.Create(indexer);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```
Dodaj następujące wiersze do `Main` .

```csharp
    // Create the indexer, map fields, and execute transformations
    Console.WriteLine("Creating the indexer and executing the pipeline...");
    Indexer demoIndexer = CreateDemoIndexer(serviceClient, dataSource, skillset, demoIndex);
```

Należy oczekiwać, że tworzenie indeksatora trwa trochę czasu. Mimo że zestaw danych jest mały, umiejętności analityczne wykorzystują znaczną moc obliczeniową. Wykonywanie niektórych umiejętności, takich jak analiza obrazu, jest długotrwałe.

> [!TIP]
> Utworzenie indeksatora powoduje wywołanie potoku. Jeśli występują problemy z dostępem do danych, mapowaniem danych wejściowych i wyjściowych lub kolejnością operacji, pojawią się one na tym etapie.

### <a name="explore-creating-the-indexer"></a>Eksplorowanie tworzenia indeksatora

Kod jest ustawiony ```"maxFailedItems"```  na wartość-1, co powoduje ignorowanie błędów podczas importowania danych przez aparat indeksowania. Jest to przydatne, ponieważ pokazowe źródło danych zawiera tak mało dokumentów. W przypadku większego źródła danych należy ustawić wartość większą od 0.

Zauważ również, że ```"dataToExtract"``` jest ustawiony na ```"contentAndMetadata"``` . Ta instrukcja nakazuje indeksatorowi automatyczne wyodrębnianie zawartości z plików w różnych formatach, a także metadanych związanych z każdym plikiem.

Gdy zawartość zostanie wyodrębniona, możesz ustawić element `imageAction`, aby wyodrębnić tekst z obrazów znalezionych w źródle danych. ```"imageAction"```Zestaw do ```"generateNormalizedImages"``` konfiguracji, w połączeniu z umiejętnością OCR i umiejętnością scalania tekstu, informuje indeksator, aby wyodrębnił tekst z obrazów (na przykład słowo "Stop" od znaku zatrzymania ruchu) i osadzić je jako część pola zawartość. To zachowanie dotyczy zarówno obrazów osadzonych w dokumentach (np. w pliku PDF), jak i znalezionych w źródle danych (np. pliku JPG).

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 — monitorowanie indeksowania

Po zdefiniowaniu indeksatora jest on uruchamiany automatycznie przy przesyłaniu żądania. W zależności od tego, które umiejętności poznawcze zdefiniowano, indeksowanie może trwać dłużej, niż oczekujesz. Aby dowiedzieć się, czy indeksator jest nadal uruchomiony, użyj `GetStatus` metody.

```csharp
private static void CheckIndexerOverallStatus(SearchServiceClient serviceClient, Indexer indexer)
{
    try
    {
        IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Status)
        {
            case IndexerStatus.Error:
                ExitProgram("Indexer has error status. Check the Azure Portal to further understand the error.");
                break;
            case IndexerStatus.Running:
                Console.WriteLine("Indexer is running");
                break;
            case IndexerStatus.Unknown:
                Console.WriteLine("Indexer status is unknown");
                break;
            default:
                Console.WriteLine("No indexer information");
                break;
        }
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", e.Message);
    }
}
```

`IndexerExecutionInfo` reprezentuje bieżący stan i historię wykonywania indeksatora.

Ostrzeżenia często występują dla niektórych kombinacji plików źródłowych i umiejętności oraz nie zawsze wskazują istnienie problemu. W tym samouczku ostrzeżenia są niegroźne (np. brak tekstowych danych wejściowych dla plików JPEG).

Dodaj następujące wiersze do `Main` .

```csharp
    // Check indexer overall status
    Console.WriteLine("Check the indexer overall status...");
    CheckIndexerOverallStatus(serviceClient, demoIndexer);
```
 
## <a name="5---search"></a>5 — wyszukiwanie

Po zakończeniu indeksowania można uruchamiać zapytania, które zwracają zawartość poszczególnych pól. Domyślnie usługa Azure Wyszukiwanie poznawcze zwraca górne wyniki 50. Przykładowych danych jest mało, więc wartość domyślna działa dobrze. Jednak podczas pracy z większymi zestawami danych może być konieczne dołączenie do ciągu zapytania parametrów powodujących zwrócenie większej liczby wyników. Aby uzyskać instrukcje, zobacz [How to Page Results in Azure wyszukiwanie poznawcze](search-pagination-page-layout.md).

W ramach kroku weryfikacji odpytaj indeks o wszystkie pola.

Dodaj następujące wiersze do `Main` .

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
}
catch (Exception e)
{
    // Handle exception
}
```

`CreateSearchIndexClient` Tworzy nowe `SearchIndexClient` wartości przy użyciu, które są przechowywane w pliku konfiguracyjnym aplikacji (appsettings.json). Zwróć uwagę, że klucz interfejsu API zapytania usługi wyszukiwania jest używany, a nie klucz administratora.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

Dodaj następujący kod do `Main` . Pierwsza funkcja try-catch zwraca definicję indeksu, z nazwą, typem i atrybutami każdego pola. Druga jest zapytanie parametryczne, gdzie `Select` określa pola do uwzględnienia w wynikach, na przykład `organizations` . Ciąg wyszukiwania `"*"` zwraca całą zawartość pojedynczego pola.

```csharp
//Verify content is returned after indexing is finished
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
    Console.WriteLine("First query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("First query failed\n Exception message: {0}\n", e.Message);
}

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
    Console.WriteLine("Second query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("Second query failed\n Exception message: {0}\n", e.Message);
}
```

Powtórz te czynności w przypadku dodatkowych pól: Content, languageCode, phrase kluczs i organizacji w tym ćwiczeniu. Można zwrócić wiele pól za pośrednictwem właściwości [SELECT](/dotnet/api/microsoft.azure.search.models.searchparameters.select) przy użyciu listy rozdzielanej przecinkami.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetowanie i ponowne uruchamianie

W przypadku wczesnych eksperymentalnych etapów tworzenia najlepszym podejściem do iteracji projektu jest usunięcie obiektów z platformy Azure Wyszukiwanie poznawcze i umożliwienie kodowi odbudowania. Nazwy zasobów są unikatowe. Usunięcie obiektu umożliwia jego ponowne utworzenie przy użyciu tej samej nazwy.

Przykładowy kod dla tego samouczka sprawdza istniejące obiekty i usuwa je, aby można było ponownie uruchomić kod.

Możesz również użyć portalu, aby usunąć indeksy, indeksatory, źródła danych i umiejętności.

## <a name="takeaways"></a>Wnioski

W tym samouczku przedstawiono podstawowe kroki tworzenia przebudowanego potoku indeksowania przez tworzenie części składników: źródła danych, zestawu umiejętności, indeksu i indeksatora.

Wprowadzono [wbudowane umiejętności](cognitive-search-predefined-skills.md) , wraz z definicją zestawu umiejętności oraz Mechanics umiejętności związanych z łańcuchem, a także za pomocą danych wejściowych i wyjściowych. Poznasz również, że `outputFieldMappings` w definicji indeksatora jest wymagany do routingu wzbogaconych wartości z potoku do indeksu wyszukiwania w usłudze Azure wyszukiwanie poznawcze.

Ponadto przedstawiono sposób testowania wyników i resetowania systemu na potrzeby przyszłych iteracji. Omówiono proces, w ramach którego odpytanie indeksu powoduje zwrócenie danych wyjściowych utworzonych przez wzbogacony potok indeksowania. Zaprezentowano procedurę sprawdzania stanu indeksatora i usuwania obiektów przed ponownym uruchomieniem potoku.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy Pracujesz w ramach własnej subskrypcji, na końcu projektu warto usunąć zasoby, które nie są już potrzebne. Uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku wszystkie zasoby lub grupy zasobów w okienku nawigacji po lewej stronie.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz wszystkie obiekty w potoku wzbogacenia, przyjrzyjmy się dokładniej definicjom zestawu umiejętności i indywidualnym umiejętnościom.

> [!div class="nextstepaction"]
> [Jak utworzyć zestawu umiejętności](cognitive-search-defining-skillset.md)