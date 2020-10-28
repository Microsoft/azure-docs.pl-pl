---
title: Samouczek C# dotyczący używania AI w obiektach Blob platformy Azure
titleSuffix: Azure Cognitive Search
description: Przechodzenie między przykładem wyodrębniania tekstu i przetwarzania języka naturalnego za pośrednictwem zawartości w usłudze BLOB Storage przy użyciu języka C# i zestawu Azure Wyszukiwanie poznawcze .NET SDK.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: da7a80842bec68fde8cc44401bb04c2dd061741f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92787962"
---
# <a name="tutorial-ai-generated-searchable-content-from-azure-blobs-using-the-net-sdk"></a>Samouczek: wygenerowane przez program AI zawartość do przeszukiwania z obiektów blob platformy Azure przy użyciu zestawu .NET SDK

Jeśli w usłudze Azure Blob Storage znajduje się tekst lub obrazy bez struktury, [potok wzbogacenia AI](cognitive-search-concept-intro.md) może wyodrębnić informacje i utworzyć nową zawartość dla wyszukiwania pełnotekstowego lub scenariuszy wyszukiwania w bazie wiedzy. 

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj środowisko programistyczne.
> * Zdefiniuj potok przeznaczony dla obiektów BLOB przy użyciu OCR, wykrywania języka, rozpoznawania jednostek i fraz kluczy.
> * Wykonaj potok, aby wywoływać przekształcenia, i utworzyć i załadować indeks wyszukiwania.
> * Eksplorowanie wyników przy użyciu wyszukiwania pełnotekstowego i zaawansowanej składni zapytań.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Otwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="overview"></a>Omówienie

W tym samouczku do tworzenia źródła danych, indeksu, indeksatora i zestawu umiejętności jestAzure.Search.Docstosowana Biblioteka **uments** klienta.

Zestawu umiejętności korzysta z wbudowanych umiejętności opartych na interfejsy API usług Cognitive Services. Kroki w potoku obejmują optyczne rozpoznawanie znaków (OCR) na obrazach, wykrywanie języka dla tekstu, wyodrębnianie kluczowych fraz i rozpoznawanie jednostek (organizacje). Nowe informacje są przechowywane w nowych polach, które mogą być używane w zapytaniach, aspektach i filtrach.

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Visual Studio](https://visualstudio.microsoft.com/downloads/)
* [ Pakiet NuGetAzure.Search.Documents 11. x](https://www.nuget.org/packages/Azure.Search.Documents) 
* [Azure Storage](https://azure.microsoft.com/services/storage/)
* [Azure Cognitive Search](https://azure.microsoft.com/services/search/)

> [!Note]
> W tym samouczku możesz użyć bezpłatnej usługi wyszukiwania. Bezpłatna usługa wyszukiwania ogranicza do trzech indeksów, trzech indeksatorów i trzech źródeł danych. W ramach tego samouczka tworzony jest jeden element każdego z tych typów. Przed rozpoczęciem upewnij się, że masz miejsce w usłudze, aby akceptować nowe zasoby.

## <a name="download-sample-data"></a>Pobierz przykładowe dane

Przykładowe dane składają się z 14 plików typu zawartości mieszanej, który zostanie przekazany do usługi Azure Blob Storage w późniejszym kroku.

1. Otwórz ten [folder w usłudze OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) i w lewym górnym rogu, kliknij pozycję **Pobierz** , aby skopiować pliki do komputera. 

1. Kliknij prawym przyciskiem myszy plik zip i wybierz polecenie **Wyodrębnij wszystko** . Istnieje 14 plików różnych typów. W tym ćwiczeniu należy użyć 7.

Możesz również pobrać kod źródłowy dla tego samouczka. Kod źródłowy znajduje się w folderze **samouczek — wzbogacanie/v11** w repozytorium [Azure-Search-dotnet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) .

## <a name="1---create-services"></a>1 — Tworzenie usług

Ten samouczek używa usługi Azure Wyszukiwanie poznawcze do indeksowania i zapytań, Cognitive Services w zapleczu do wzbogacania AI i usługi Azure Blob Storage, aby zapewnić dane. Ten samouczek jest objęty bezpłatną alokacją 20 transakcji na indeksator dziennie na Cognitive Services, więc jedyne usługi, które należy utworzyć, to wyszukiwanie i magazynowanie.

Jeśli to możliwe, Utwórz zarówno w tym samym regionie, jak i w grupie zasobów, co umożliwia bliskość i łatwość zarządzania. W tym przypadku konto usługi Azure Storage może znajdować się w dowolnym regionie.

### <a name="start-with-azure-storage"></a>Rozpoczynanie pracy z usługą Azure Storage

1. [Zaloguj się do Azure Portal](https://portal.azure.com/) i kliknij pozycję **+ Utwórz zasób** .

1. Wyszukaj *konto magazynu* i wybierz ofertę konta magazynu firmy Microsoft.

   ![Utwórz konto magazynu](media/cognitive-search-tutorial-blob/storage-account.png "Utwórz konto magazynu")

1. Na karcie podstawowe wymagane są następujące elementy. Zaakceptuj wartości domyślne dla wszystkich innych elementów.

   * **Grupa zasobów** . Wybierz istniejący lub Utwórz nowy, ale Użyj tej samej grupy dla wszystkich usług, aby można było zarządzać nimi zbiorczo.

   * **Nazwa konta magazynu** . Jeśli uważasz, że może istnieć wiele zasobów tego samego typu, użyj nazwy, aby odróżnić według typu i regionu, na przykład *blobstoragewestus* . 

   * **Lokalizacja** . Jeśli to możliwe, wybierz tę samą lokalizację, która jest używana dla usługi Azure Wyszukiwanie poznawcze i Cognitive Services. Pojedyncza lokalizacja unieważnia opłaty za przepustowość.

   * **Rodzaj konta** . Wybierz wartość domyślną *StorageV2 (ogólnego przeznaczenia w wersji 2)* .

1. Kliknij przycisk **Przegląd + Utwórz** , aby utworzyć usługę.

1. Po jego utworzeniu kliknij pozycję **Przejdź do zasobu** , aby otworzyć stronę przegląd.

1. Kliknij pozycję **obiekty blob** usługa.

1. Kliknij pozycję **+ kontener** , aby utworzyć kontener i nadaj mu nazwę *koło zębate-Search-demonstracyjn* .

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

Trzeci składnik to Wyszukiwanie poznawcze platformy Azure, który można [utworzyć w portalu](search-create-service-portal.md) lub [znaleźć istniejącą usługę wyszukiwania](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach subskrypcji.

Aby ukończyć ten przewodnik, możesz skorzystać z warstwy Bezpłatna. 

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Kopiowanie klucza API administratora — klucz i adres URL dla usługi Azure Wyszukiwanie poznawcze

Aby można było korzystać z usługi Azure Wyszukiwanie poznawcze, wymagany jest adres URL usługi i klucz dostępu. Usługa wyszukiwania jest tworzona razem z usługą, więc jeśli do subskrypcji dodano Wyszukiwanie poznawcze platformy Azure, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W obszarze **Ustawienia**  >  **klucze** skopiuj klucz administratora, aby uzyskać pełne prawa do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

   Pobierz również klucz zapytania. Najlepszym rozwiązaniem jest wydawanie żądań zapytań z dostępem tylko do odczytu.

   ![Pobieranie nazwy usługi i administratora oraz kluczy zapytań](media/search-get-started-javascript/service-name-and-keys.png)

Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="2---set-up-your-environment"></a>2 — Konfigurowanie środowiska

Zacznij od otwierania programu Visual Studio i tworzenia nowego projektu aplikacji konsolowego, który można uruchomić w środowisku .NET Core.

### <a name="install-azuresearchdocuments"></a>Zainstaluj Azure.Search.Documents

[Zestaw Azure wyszukiwanie poznawcze .NET SDK](/dotnet/api/overview/azure/search) składa się z biblioteki klienckiej, która umożliwia zarządzanie indeksami, źródłami danych, indeksatorami i umiejętności, a także przekazywaniem i zarządzaniem dokumentami oraz wykonywanie zapytań, bez konieczności zajmowania się szczegółowymi informacjami dotyczącymi protokołu HTTP i JSON. Ta Biblioteka kliencka jest dystrybuowana jako pakiet NuGet.

W przypadku tego projektu Zainstaluj wersję 11 lub nowszą `Azure.Search.Documents` i najnowszą wersję programu `Microsoft.Extensions.Configuration` .

1. W programie Visual Studio wybierz kolejno pozycje **Narzędzia**  >  **Menedżer pakietów NuGet**  >  **Zarządzanie pakietami NuGet dla rozwiązania...**

1. Przeglądaj w poszukiwaniu [Azure.Search.Document](https://www.nuget.org/packages/Azure.Search.Documents).

1. Wybierz najnowszą wersję, a następnie kliknij przycisk **Instaluj** .

1. Powtórz poprzednie kroki, aby zainstalować [Microsoft.Extensions.Configwersja](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) i [Microsoft.Extensions.Configuration.Js](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json).

### <a name="add-service-connection-information"></a>Dodaj informacje o połączeniu z usługą

1. Kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań i wybierz polecenie **Dodaj**  >  **nowy element.** ... 

1. Nazwij plik `appsettings.json` i wybierz pozycję **Dodaj** . 

1. Dołącz ten plik do katalogu wyjściowego.
    1. Kliknij prawym przyciskiem myszy `appsettings.json` i wybierz pozycję **Właściwości** . 
    1. Zmień wartość w polu **Kopiuj do katalogu wyjściowego** na **Kopiuj, jeśli nowszy** .

1. Skopiuj poniższy kod JSON do nowego pliku JSON.

    ```json
    {
      "SearchServiceUri": "Put your search service URI here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

Dodaj usługę wyszukiwania i informacje o koncie usługi BLOB Storage. Odwołaj te informacje można uzyskać, korzystając z kroków inicjowania obsługi usługi, wskazanych w poprzedniej sekcji.

W polu **SearchServiceUri** wprowadź pełny adres URL.

### <a name="add-namespaces"></a>Dodaj przestrzenie nazw

W programie `Program.cs` Dodaj następujące przestrzenie nazw.

```csharp
using Azure;
using Azure.Search.Documents.Indexes;
using Azure.Search.Documents.Indexes.Models;
using Microsoft.Extensions.Configuration;
using System;
using System.Collections.Generic;
using System.Linq;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Tworzenie klienta

Utwórz wystąpienie a `SearchIndexClient` i `SearchIndexerClient` poniżej `Main` .

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    string searchServiceUri = configuration["SearchServiceUri"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];
    string cognitiveServicesKey = configuration["CognitiveServicesKey"];

    SearchIndexClient indexClient = new SearchIndexClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));
    SearchIndexerClient indexerClient = new SearchIndexerClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));
}
```

> [!NOTE]
> Klienci łączą się z usługą wyszukiwania. Aby uniknąć otwierania zbyt wielu połączeń, należy w miarę możliwości udostępnić pojedyncze wystąpienie w aplikacji. Metody są bezpieczne dla wątków, aby umożliwić takie udostępnianie.
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

`SearchIndexerClient` ma [`DataSourceName`](/dotnet/api/azure.search.documents.indexes.models.searchindexer.datasourcename) Właściwość, którą można ustawić na `SearchIndexerDataSourceConnection` obiekt. Ten obiekt zawiera wszystkie metody potrzebne do tworzenia, wyświetlania, aktualizowania lub usuwania źródeł danych Azure Wyszukiwanie poznawcze.

Utwórz nowe `SearchIndexerDataSourceConnection` wystąpienie, wywołując element `indexerClient.CreateOrUpdateDataSourceConnection(dataSource)` . Poniższy kod tworzy źródło danych typu `AzureBlob` .

```csharp
private static SearchIndexerDataSourceConnection CreateOrUpdateDataSource(SearchIndexerClient indexerClient, IConfigurationRoot configuration)
{
    SearchIndexerDataSourceConnection dataSource = new SearchIndexerDataSourceConnection(
        name: "demodata",
        type: SearchIndexerDataSourceType.AzureBlob,
        connectionString: configuration["AzureBlobConnectionString"],
        container: new SearchIndexerDataContainer("cog-search-demo"))
    {
        Description = "Demo files to demonstrate cognitive search capabilities."
    };

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        indexerClient.CreateOrUpdateDataSourceConnection(dataSource);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

W przypadku pomyślnego żądania Metoda zwróci źródło danych, które zostało utworzone. Jeśli wystąpi problem z żądaniem, na przykład nieprawidłowym parametrem, Metoda zgłosi wyjątek.

Teraz Dodaj wiersz `Main` do wywołania `CreateOrUpdateDataSource` funkcji, która właśnie została dodana.

```csharp
// Create or Update the data source
Console.WriteLine("Creating or updating the data source...");
SearchIndexerDataSourceConnection dataSource = CreateOrUpdateDataSource(indexerClient, configuration);
```

Skompiluj i uruchom rozwiązanie. Ponieważ jest to Twoje pierwsze żądanie, sprawdź Azure Portal, aby potwierdzić, że źródło danych zostało utworzone w usłudze Azure Wyszukiwanie poznawcze. Na stronie Przegląd usługi wyszukiwania Sprawdź, czy lista źródeł danych ma nowy element. Może trzeba będzie zaczekać kilka minut na odświeżenie strony portalu.

  ![Kafelek źródła danych w portalu](./media/cognitive-search-tutorial-blob/data-source-tile.png "Kafelek źródła danych w portalu")

### <a name="step-2-create-a-skillset"></a>Krok 2. Tworzenie elementu zestawu umiejętności

W tej sekcji definiujesz zestaw kroków wzbogacania, które chcesz zastosować do danych. Każdy krok wzbogacania jest nazywany *umiejętnością* i zestawem kroków wzbogacania, *zestawu umiejętności* . W tym samouczku są stosowane [wbudowane umiejętności poznawcze](cognitive-search-predefined-skills.md) dla zestawu umiejętności:

* [Optyczne rozpoznawanie znaków](cognitive-search-skill-ocr.md) do rozpoznawania tekstu napisanego i odręcznego w plikach obrazów.

* [Łączenie tekstu](cognitive-search-skill-textmerger.md) do konsolidowania tekstu z kolekcji pól w jedno pole.

* [Wykrywanie języka](cognitive-search-skill-language-detection.md) — identyfikowanie języka zawartości.

* [Dzielenie tekstu](cognitive-search-skill-textsplit.md) w celu podziału dużej zawartości na mniejsze fragmenty przed wywołaniem umiejętności wyodrębniania kluczowych fraz i umiejętności rozpoznawania jednostek. Wyodrębnianie kluczowych fraz i rozpoznawanie jednostek akceptuje dane wejściowe z 50 000 znaków lub mniej. Kilka przykładowych plików należy podzielić, aby zmieścić się w tym limicie.

* [Rozpoznawanie jednostek](cognitive-search-skill-entity-recognition.md) do wyodrębniania nazw organizacji z zawartości kontenera obiektów BLOB.

* [Wyodrębnianie kluczowych fraz](cognitive-search-skill-keyphrases.md) — określanie najczęściej występujących fraz kluczowych.

Podczas wstępnego przetwarzania platforma Azure Wyszukiwanie poznawcze pęknięć każdy dokument w celu wyodrębnienia zawartości z różnych formatów plików. Tekst pochodzący z pliku źródłowego jest umieszczany w wygenerowanym `content` polu, po jednym dla każdego dokumentu. W związku z tym ustaw wartość wejściową, tak `"/document/content"` aby używała tego tekstu. Zawartość obrazu jest umieszczana w wygenerowanym `normalized_images` polu, określonym w zestawu umiejętności jako `/document/normalized_images/*` .

Dane wyjściowe można mapować na indeks i/lub używać ich jako danych wejściowych umiejętności podrzędnej — jak w przypadku kodu języka. W indeksie kod języka jest przydatny do filtrowania. Kod języka jest używany jako dane wejściowe przez umiejętności analizy tekstu w celu określenia zasad podziału wyrazów przez reguły językowe.

Aby uzyskać więcej podstawowych informacji na temat zestawów umiejętności, zobacz [Jak zdefiniować zestaw umiejętności](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>Umiejętność OCR

Umiejętność **OCR** wyodrębnia tekst z obrazów. Ta umiejętność zakłada, że pole normalized_images istnieje. Aby wygenerować to pole, w dalszej części samouczka ustawimy ```"imageAction"``` konfigurację w definicji indeksatora na ```"generateNormalizedImages"``` .

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("image")
    {
        Source = "/document/normalized_images/*"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("text")
    {
        TargetName = "text"
    });

    OcrSkill ocrSkill = new OcrSkill(inputMappings, outputMappings)
    {
        Description = "Extract text (plain and structured) from image",
        Context = "/document/normalized_images/*",
        DefaultLanguageCode = OcrSkillLanguage.En,
        ShouldDetectOrientation = true
    };

    return ocrSkill;
}
```

### <a name="merge-skill"></a>Scal umiejętność

W tej sekcji utworzysz umiejętność **scalania** , która scala pole zawartości dokumentu z tekstem wyprodukowanym przez umiejętność OCR.

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/content"
    });
    inputMappings.Add(new InputFieldMappingEntry("itemsToInsert")
    {
        Source = "/document/normalized_images/*/text"
    });
    inputMappings.Add(new InputFieldMappingEntry("offsets")
    {
        Source = "/document/normalized_images/*/contentOffset"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("mergedText")
    {
        TargetName = "merged_text"
    });

    MergeSkill mergeSkill = new MergeSkill(inputMappings, outputMappings)
    {
        Description = "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
        Context = "/document",
        InsertPreTag = " ",
        InsertPostTag = " "
    };

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>Umiejętność wykrywania języka

Umiejętność **wykrywanie języka** wykrywa język tekstu wejściowego i raportuje jeden kod języka dla każdego dokumentu przesłanego w żądaniu. Będziemy używać danych wyjściowych **wykrywanie języka** w ramach danych wejściowych do kwalifikacji **tekstu** .

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/merged_text"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("languageCode")
    {
        TargetName = "languageCode"
    });

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(inputMappings, outputMappings)
    {
        Description = "Detect the language used in the document",
        Context = "/document"
    };

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>Umiejętność podziału tekstu

Poniższa umiejętność **podziału** podzieli tekst na strony i Ogranicz długość strony do 4 000 znaków, mierzoną przez `String.Length` . Algorytm podejmie próbę podzielenia tekstu na fragmenty o największej `maximumPageLength` wielkości. W takim przypadku algorytm będzie najlepiej przekroczyć zdanie na granicy zdania, dzięki czemu rozmiar fragmentu może być nieco mniejszy niż `maximumPageLength` .

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/merged_text"
    });
    inputMappings.Add(new InputFieldMappingEntry("languageCode")
    {
        Source = "/document/languageCode"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("textItems")
    {
        TargetName = "pages",
    });

    SplitSkill splitSkill = new SplitSkill(inputMappings, outputMappings)
    {
        Description = "Split content into pages",
        Context = "/document",
        TextSplitMode = TextSplitMode.Pages,
        MaximumPageLength = 4000,
        DefaultLanguageCode = SplitSkillLanguage.En
    };

    return splitSkill;
}
```

### <a name="entity-recognition-skill"></a>Umiejętność rozpoznawania jednostek

To `EntityRecognitionSkill` wystąpienie jest ustawione na rozpoznawanie typu kategorii `organization` . Umiejętność **rozpoznawania jednostek** może również rozpoznać typy kategorii `person` i `location` .

Zwróć uwagę, że pole "context" ma ustawioną ```"/document/pages/*"``` gwiazdkę, co oznacza, że krok wzbogacania jest wywoływany dla każdej strony w obszarze ```"/document/pages"``` .

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/pages/*"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("organizations")
    {
        TargetName = "organizations"
    });

    EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(inputMappings, outputMappings)
    {
        Description = "Recognize organizations",
        Context = "/document/pages/*",
        DefaultLanguageCode = EntityRecognitionSkillLanguage.En
    };
    entityRecognitionSkill.Categories.Add(EntityCategory.Organization);

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>Umiejętność wyodrębniania kluczowych fraz

Podobnie jak w przypadku `EntityRecognitionSkill` wystąpienia, które zostało właśnie utworzone, **wyodrębnianie kluczowych fraz** umiejętności są wywoływane dla każdej strony dokumentu.

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/pages/*"
    });
    inputMappings.Add(new InputFieldMappingEntry("languageCode")
    {
        Source = "/document/languageCode"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("keyPhrases")
    {
        TargetName = "keyPhrases"
    });

    KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(inputMappings, outputMappings)
    {
        Description = "Extract the key phrases",
        Context = "/document/pages/*",
        DefaultLanguageCode = KeyPhraseExtractionSkillLanguage.En
    };

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>Kompilowanie i tworzenie zestawu umiejętności

Kompiluj `Skillset` przy użyciu utworzonych umiejętności.

```csharp
private static SearchIndexerSkillset CreateOrUpdateDemoSkillSet(SearchIndexerClient indexerClient, IList<SearchIndexerSkill> skills,string cognitiveServicesKey)
{
    SearchIndexerSkillset skillset = new SearchIndexerSkillset("demoskillset", skills)
    {
        Description = "Demo skillset",
        CognitiveServicesAccount = new CognitiveServicesAccountKey(cognitiveServicesKey)
    };

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        indexerClient.CreateOrUpdateSkillset(skillset);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", ex.Message);
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
List<SearchIndexerSkill> skills = new List<SearchIndexerSkill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(entityRecognitionSkill);
skills.Add(keyPhraseExtractionSkill);

SearchIndexerSkillset skillset = CreateOrUpdateDemoSkillSet(indexerClient, skills, cognitiveServicesKey);
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

Dodamy klasę modelu do nowego pliku C#. Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Dodaj**  >  **nowy element...** , wybierz pozycję "Klasa" i Nazwij plik `DemoIndex.cs` , a następnie wybierz pozycję **Dodaj** .

Upewnij się, że chcesz użyć typów z `Azure.Search.Documents.Indexes` `System.Text.Json.Serialization` przestrzeni nazw i.

Dodaj poniższą definicję klasy modelu do `DemoIndex.cs` i Uwzględnij ją w tej samej przestrzeni nazw, w której utworzysz indeks.

```csharp
using Azure.Search.Documents.Indexes;
using System.Text.Json.Serialization;

namespace EnrichwithAI
{
    // The SerializePropertyNamesAsCamelCase is currently unsupported as of this writing. 
    // Replace it with JsonPropertyName
    public class DemoIndex
    {
        [SearchableField(IsSortable = true, IsKey = true)]
        [JsonPropertyName("id")]
        public string Id { get; set; }

        [SearchableField]
        [JsonPropertyName("content")]
        public string Content { get; set; }

        [SearchableField]
        [JsonPropertyName("languageCode")]
        public string LanguageCode { get; set; }

        [SearchableField]
        [JsonPropertyName("keyPhrases")]
        public string[] KeyPhrases { get; set; }

        [SearchableField]
        [JsonPropertyName("organizations")]
        public string[] Organizations { get; set; }
    }
}
```

Teraz, po zdefiniowaniu klasy modelu, `Program.cs` można łatwo utworzyć definicję indeksu. Nazwa tego indeksu będzie `demoindex` . Jeśli istnieje już indeks o tej nazwie, zostanie on usunięty.

```csharp
private static SearchIndex CreateDemoIndex(SearchIndexClient indexClient)
{
    FieldBuilder builder = new FieldBuilder();
    var index = new SearchIndex("demoindex")
    {
        Fields = builder.Build(typeof(DemoIndex))
    };

    try
    {
        indexClient.GetIndex(index.Name);
        indexClient.DeleteIndex(index.Name);
    }
    catch (RequestFailedException ex) when (ex.Status == 404)
    {
        //if the specified index not exist, 404 will be thrown.
    }

    try
    {
        indexClient.CreateIndex(index);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", ex.Message);
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
SearchIndex demoIndex = CreateDemoIndex(indexClient);
```

Dodaj następującą instrukcję using, aby usunąć niejednoznaczne odwołanie.

```csharp
using Index = Azure.Search.Documents.Indexes.Models;
```

Aby dowiedzieć się więcej o pojęciach dotyczących indeksów, zobacz [Tworzenie indeksu (interfejs API REST)](/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Krok 4. Tworzenie i uruchamianie indeksatora

Do tej pory utworzono źródło danych, zestaw umiejętności i indeks. Te trzy składniki staną się częścią [indeksatora](search-indexer-overview.md), który łączy wszystkie części w pojedynczą operację obejmującą wiele faz. Aby powiązać je razem w indeksatorze, należy zdefiniować mapowania pól.

* FieldMappings są przetwarzane przed zestawu umiejętności, mapując pola źródłowe ze źródła danych do pól docelowych w indeksie. Jeśli nazwy pól i typy są takie same na obu końcach, mapowanie nie jest wymagane.

* OutputFieldMappings są przetwarzane po zestawu umiejętności, do których odwołuje się sourceFieldNames, które nie istnieją do momentu utworzenia przez nich pęknięć lub wzbogacania dokumentów. TargetFieldName to pole w indeksie.

Oprócz podłączania danych wejściowych do danych wyjściowych, można również używać mapowań pól do spłaszczania struktur. Aby uzyskać więcej informacji, zobacz [Jak mapować wzbogacone pola na indeks wyszukiwania](cognitive-search-output-field-mapping.md).

```csharp
private static SearchIndexer CreateDemoIndexer(SearchIndexerClient indexerClient, SearchIndexerDataSourceConnection dataSource, SearchIndexerSkillset skillSet, SearchIndex index)
{
    IndexingParameters indexingParameters = new IndexingParameters()
    {
        MaxFailedItems = -1,
        MaxFailedItemsPerBatch = -1,
    };
    indexingParameters.Configuration.Add("dataToExtract", "contentAndMetadata");
    indexingParameters.Configuration.Add("imageAction", "generateNormalizedImages");

    SearchIndexer indexer = new SearchIndexer("demoindexer", dataSource.Name, index.Name)
    {
        Description = "Demo Indexer",
        SkillsetName = skillSet.Name,
        Parameters = indexingParameters
    };

    FieldMappingFunction mappingFunction = new FieldMappingFunction("base64Encode");
    mappingFunction.Parameters.Add("useHttpServerUtilityUrlTokenEncode", true);

    indexer.FieldMappings.Add(new FieldMapping("metadata_storage_path")
    {
        TargetFieldName = "id",
        MappingFunction = mappingFunction

    });
    indexer.FieldMappings.Add(new FieldMapping("content")
    {
        TargetFieldName = "content"
    });

    indexer.OutputFieldMappings.Add(new FieldMapping("/document/pages/*/organizations/*")
    {
        TargetFieldName = "organizations"
    });
    indexer.OutputFieldMappings.Add(new FieldMapping("/document/pages/*/keyPhrases/*")
    {
        TargetFieldName = "keyPhrases"
    });
    indexer.OutputFieldMappings.Add(new FieldMapping("/document/languageCode")
    {
        TargetFieldName = "languageCode"
    });

    try
    {
        indexerClient.GetIndexer(indexer.Name);
        indexerClient.DeleteIndexer(indexer.Name);
    }
    catch (RequestFailedException ex) when (ex.Status == 404)
    {
        //if the specified indexer not exist, 404 will be thrown.
    }

    try
    {
        indexerClient.CreateIndexer(indexer);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```

Dodaj następujące wiersze do `Main` .

```csharp
// Create the indexer, map fields, and execute transformations
Console.WriteLine("Creating the indexer and executing the pipeline...");
SearchIndexer demoIndexer = CreateDemoIndexer(indexerClient, dataSource, skillset, demoIndex);
```

Oczekiwanie na ukończenie przetwarzania indeksatora. Mimo że zestaw danych jest mały, umiejętności analityczne wykorzystują znaczną moc obliczeniową. Wykonywanie niektórych umiejętności, takich jak analiza obrazu, jest długotrwałe.

> [!TIP]
> Utworzenie indeksatora powoduje wywołanie potoku. Jeśli występują problemy z dostępem do danych, mapowaniem danych wejściowych i wyjściowych lub kolejnością operacji, pojawią się one na tym etapie.

### <a name="explore-creating-the-indexer"></a>Eksplorowanie tworzenia indeksatora

Kod jest ustawiony `"maxFailedItems"`  na wartość-1, co powoduje ignorowanie błędów podczas importowania danych przez aparat indeksowania. Jest to przydatne, ponieważ pokazowe źródło danych zawiera tak mało dokumentów. W przypadku większego źródła danych należy ustawić wartość większą od 0.

Zauważ również, że `"dataToExtract"` jest ustawiony na `"contentAndMetadata"` . Ta instrukcja nakazuje indeksatorowi automatyczne wyodrębnianie zawartości z plików w różnych formatach, a także metadanych związanych z każdym plikiem.

Gdy zawartość zostanie wyodrębniona, możesz ustawić element `imageAction`, aby wyodrębnić tekst z obrazów znalezionych w źródle danych. `"imageAction"`Zestaw do `"generateNormalizedImages"` konfiguracji, w połączeniu z umiejętnością OCR i umiejętnością scalania tekstu, informuje indeksator, aby wyodrębnił tekst z obrazów (na przykład słowo "Stop" od znaku zatrzymania ruchu) i osadzić je jako część pola zawartość. To zachowanie dotyczy zarówno obrazów osadzonych w dokumentach (np. w pliku PDF), jak i znalezionych w źródle danych (np. pliku JPG).

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 — monitorowanie indeksowania

Po zdefiniowaniu indeksatora jest on uruchamiany automatycznie przy przesyłaniu żądania. W zależności od tego, które umiejętności poznawcze zdefiniowano, indeksowanie może trwać dłużej, niż oczekujesz. Aby dowiedzieć się, czy indeksator jest nadal uruchomiony, użyj `GetStatus` metody.

```csharp
private static void CheckIndexerOverallStatus(SearchIndexerClient indexerClient, SearchIndexer indexer)
{
    try
    {
        var demoIndexerExecutionInfo = indexerClient.GetIndexerStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Value.Status)
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
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", ex.Message);
    }
}
```

`demoIndexerExecutionInfo` reprezentuje bieżący stan i historię wykonywania indeksatora.

Ostrzeżenia często występują dla niektórych kombinacji plików źródłowych i umiejętności oraz nie zawsze wskazują istnienie problemu. W tym samouczku ostrzeżenia są niegroźne (np. brak tekstowych danych wejściowych dla plików JPEG).

Dodaj następujące wiersze do `Main` .

```csharp
// Check indexer overall status
Console.WriteLine("Check the indexer overall status...");
CheckIndexerOverallStatus(indexerClient, demoIndexer);
```

## <a name="5---search"></a>5 — wyszukiwanie

W samouczku platformy Azure Wyszukiwanie poznawcze aplikacje konsolowe zwykle dodają 2-sekundowe opóźnienie przed uruchomieniem zapytań, które zwracają wyniki, ale ponieważ wzbogacanie trwa kilka minut, zamknie aplikację konsolową i użyjemy zamiast tego innego podejścia.

Najprostszym rozwiązaniem jest [Eksplorator wyszukiwania](search-explorer.md) w portalu. Można najpierw uruchomić puste zapytanie zwracające wszystkie dokumenty lub bardziej przeszukiwane wyszukiwanie zwracające nową zawartość pola utworzoną przez potok. 

1. W Azure Portal na stronie Przegląd wyszukiwania wybierz pozycję **indeksy** .

1. Znajdź **`demoindex`** na liście. Powinien on mieć 14 dokumentów. Jeśli liczba dokumentów wynosi zero, indeksator jest nadal uruchomiony lub strona nie została jeszcze odświeżona. 

1. Wybierz pozycję **`demoindex`** . Eksplorator wyszukiwania jest pierwszą kartą.

1. Zawartość jest przeszukiwana zaraz po załadowaniu pierwszego dokumentu. Aby sprawdzić, czy zawartość istnieje, uruchom nieokreślone zapytanie, klikając pozycję **Wyszukaj** . To zapytanie zwraca wszystkie aktualnie indeksowane dokumenty, co daje pomysł dotyczący tego, co zawiera indeks.

1. Następnie wklej następujący ciąg, aby uzyskać więcej możliwości zarządzania: `search=*&$select=id, languageCode, organizations`

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Resetowanie i ponowne uruchamianie

W przypadku wczesnych eksperymentalnych etapów tworzenia najlepszym podejściem do iteracji projektu jest usunięcie obiektów z platformy Azure Wyszukiwanie poznawcze i umożliwienie kodowi odbudowania. Nazwy zasobów są unikatowe. Usunięcie obiektu umożliwia jego ponowne utworzenie przy użyciu tej samej nazwy.

Przykładowy kod dla tego samouczka sprawdza istniejące obiekty i usuwa je, aby można było ponownie uruchomić kod. Możesz również użyć portalu, aby usunąć indeksy, indeksatory, źródła danych i umiejętności.

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