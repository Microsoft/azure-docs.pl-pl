---
title: Opracowywanie funkcji usługi Azure Functions przy użyciu programu Visual Studio
description: Dowiedz się, jak opracowywać i testować Azure Functions przy użyciu narzędzi Azure Functions Tools for Visual Studio 2019.
ms.custom: vs-azure, devx-track-csharp
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 2cba0a9ad63e319af0a5eaa1c1c018c3b285c28a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765579"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Opracowywanie funkcji usługi Azure Functions przy użyciu programu Visual Studio  

Visual Studio umożliwia opracowywanie, testowanie i wdrażanie funkcji biblioteki klas języka C# na platformie Azure. Jeśli to środowisko jest Twoją pierwszą Azure Functions, zobacz [An introduction to Azure Functions](functions-overview.md)(Wprowadzenie do Azure Functions ).

Visual Studio zapewnia następujące korzyści podczas opracowywania funkcji: 

* Edytowanie, kompilowanie i uruchamianie funkcji na lokalnym komputerze dewelopera. 
* Publikowanie projektu Azure Functions na platformie Azure i tworzenie zasobów platformy Azure zgodnie z potrzebami. 
* Użyj atrybutów języka C#, aby zadeklarować powiązania funkcji bezpośrednio w kodzie języka C#.
* Opracowywanie i wdrażanie wstępnie skompilowanych funkcji języka C#. Wstępnie spełnione funkcje zapewniają lepszą wydajność zimnego startu niż funkcje oparte na skryptach języka C#. 
* Koduj funkcje w języku C#, jednocześnie mając wszystkie zalety Visual Studio tworzenia aplikacji. 

Ten artykuł zawiera szczegółowe informacje na temat używania biblioteki Visual Studio do tworzenia funkcji biblioteki klas języka C# i publikowania ich na platformie Azure. Przed przeczytaniem tego artykułu rozważ ukończenie przewodnika [Szybki start dla](functions-create-your-first-function-visual-studio.md)Visual Studio. 

O ile nie określono inaczej, przedstawione procedury i przykłady mają Visual Studio 2019 r. 

## <a name="prerequisites"></a>Wymagania wstępne

- Azure Functions Tools. Aby dodać narzędzia funkcji platformy Azure, uwzględnij obciążenie tworzenie aplikacji na platformie **Azure** w Visual Studio instalacji. Azure Functions Tools są dostępne w obciążeniu tworzenie aplikacji na platformie Azure od Visual Studio 2017 r.

- Inne potrzebne zasoby, takie jak konto usługi Azure Storage, są tworzone w ramach subskrypcji podczas procesu publikowania.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> W Visual Studio 2017 r. obciążenie Tworzenie aplikacji na platformie Azure instaluje narzędzia Azure Functions Tools jako osobne rozszerzenie. Podczas aktualizowania instalacji Visual Studio 2017 upewnij się, że [](#check-your-tools-version) używasz najnowszej wersji narzędzi Azure Functions 2017. W poniższych sekcjach przedstawiono sposób sprawdzania i (w razie potrzeby) aktualizowania rozszerzenia Azure Functions Tools w programie Visual Studio 2017. 
>
> Pomiń te sekcje, jeśli używasz programu Visual Studio 2019.

### <a name="check-your-tools-version-in-visual-studio-2017"></a><a name="check-your-tools-version"></a>Sprawdź wersję narzędzi w programie Visual Studio 2017

1. W menu **Narzędzia** wybierz pozycję **Rozszerzenia i aktualizacje.** Rozwiń **pozycję**  >  **Zainstalowane narzędzia,** a następnie **wybierz Azure Functions i Web Jobs Tools.**

    ![Weryfikowanie wersji narzędzi funkcji](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Zwróć uwagę na **zainstalowaną wersję** i porównaj ją z najnowszą wersją wymienioną w [informacjach o wersji.](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md) 

1. Jeśli Twoja wersja jest starsza, zaktualizuj narzędzia w Visual Studio, jak pokazano w poniższej sekcji.

### <a name="update-your-tools-in-visual-studio-2017"></a>Aktualizowanie narzędzi w programie Visual Studio 2017

1. W **oknie dialogowym Rozszerzenia i** aktualizacje rozwiń pozycję Aktualizacje   >  **Visual Studio Marketplace,** wybierz pozycję Narzędzia Azure Functions i **Web Jobs,** a następnie wybierz pozycję **Aktualizuj.**

    ![Aktualizowanie wersji narzędzi usługi Functions](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. Po pobraniu aktualizacji narzędzi wybierz pozycję **Zamknij,** a następnie zamknij Visual Studio, aby wyzwolić aktualizację narzędzi za pomocą Instalatora VSIX.

1. W Instalatorze VSIX wybierz pozycję **Modyfikuj,** aby zaktualizować narzędzia. 

1. Po zakończeniu aktualizacji wybierz pozycję **Zamknij,** a następnie ponownie uruchom Visual Studio.

> [!NOTE]  
> W Visual Studio 2019 r. i nowszych rozszerzenie narzędzi Azure Functions jest aktualizowane w ramach Visual Studio.  

## <a name="create-an-azure-functions-project"></a>Tworzenie projektu usługi Azure Functions

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Po utworzeniu projektu Azure Functions szablon projektu tworzy projekt w języku C#, instaluje pakiet NuGet i `Microsoft.NET.Sdk.Functions` ustawia platformę docelową. Nowy projekt ma następujące pliki:

* **host.jsna**: umożliwia skonfigurowanie hosta usługi Functions. Te ustawienia są stosowane zarówno podczas uruchamiania lokalnego, jak i na platformie Azure. Aby uzyskać więcej informacji, zobacz [host.jsna stronie .](functions-host-json.md)

* **local.settings.jsw programie**: przechowuje ustawienia używane podczas lokalnego uruchamiania funkcji. Te ustawienia nie są używane podczas uruchamiania na platformie Azure. Aby uzyskać więcej informacji, zobacz [Plik ustawień lokalnych](#local-settings-file).

    >[!IMPORTANT]
    >Ponieważ local.settings.jsw pliku może zawierać wpisy tajne, należy wykluczyć je z kontroli źródła projektu. Upewnij **się, że ustawienie Kopiuj do katalogu wyjściowego** dla tego pliku ma wartość **Kopiuj, jeśli nowsze .** 

Aby uzyskać więcej informacji, zobacz [Projekt biblioteki klas funkcji](functions-dotnet-class-library.md#functions-class-library-project).

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Visual Studio nie przekaże automatycznie ustawień w local.settings.jspodczas publikowania projektu. Aby upewnić się, że te ustawienia również istnieją w aplikacji funkcji na platformie Azure, przekaż je po opublikowaniu projektu. Aby uzyskać więcej informacji, zobacz [Ustawienia aplikacji funkcji](#function-app-settings). Wartości w kolekcji `ConnectionStrings` nigdy nie są publikowane.

Kod może również odczytywać wartości ustawień aplikacji funkcji jako zmienne środowiskowe. Aby uzyskać więcej informacji, zobacz [Zmienne środowiskowe](functions-dotnet-class-library.md#environment-variables).

## <a name="configure-your-build-output-settings"></a>Konfigurowanie ustawień danych wyjściowych kompilacji

Podczas kompilowania Azure Functions narzędzia kompilacji optymalizują dane wyjściowe tak, aby zachować tylko jedną kopię wszystkich zestawów współużytkowanych ze środowiskiem uruchomieniowym funkcji. Wynikiem jest zoptymalizowana kompilacja, która pozwala zaoszczędzić jak najwięcej miejsca. Jednak w przypadku przechodzenia do najnowszej wersji dowolnego zestawu projektu narzędzia kompilacji mogą nie wiedzieć, że te zestawy muszą zostać zachowane. Aby upewnić się, że te zestawy są zachowywane podczas procesu optymalizacji, można je określić przy użyciu elementów `FunctionsPreservedDependencies` w pliku projektu (csproj):

```xml
  <ItemGroup>
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.dll" />
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.Extensions.dll" />
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.Features.dll" />
  </ItemGroup>
```

## <a name="configure-the-project-for-local-development"></a>Konfigurowanie projektu do opracowywania lokalnego

Środowisko uruchomieniowe usługi Functions używa konta usługi Azure Storage wewnętrznie. Dla wszystkich typów wyzwalaczy innych niż HTTP i webhook ustaw klucz na prawidłowe ciąg połączenia `Values.AzureWebJobsStorage` konta usługi Azure Storage. Aplikacja funkcji może również używać [emulatora usługi Azure Storage](../storage/common/storage-use-emulator.md) dla ustawienia połączenia wymaganego `AzureWebJobsStorage` przez projekt. Aby użyć emulatora, ustaw wartość `AzureWebJobsStorage` na `UseDevelopmentStorage=true` . Przed wdrożeniem zmień to ustawienie na rzeczywiste wartości parametrów połączenia konta magazynu.

Aby ustawić ciąg połączenia konta magazynu:

1. W Visual Studio wybierz pozycję **Wyświetl eksplorator**  >  **chmury.**

2. W **eksploratorze chmury** **rozwiń pozycję Konta magazynu,** a następnie wybierz konto magazynu. Na karcie **Właściwości** skopiuj wartość **Podstawowe ciągi** połączenia.

2. W projekcie otwórz local.settings.jspliku i ustaw wartość klucza na skopiowane `AzureWebJobsStorage` ciągi połączenia.

3. Powtórz poprzedni krok, aby dodać unikatowe klucze do tablicy dla `Values` wszystkich innych połączeń wymaganych przez funkcje. 

## <a name="add-a-function-to-your-project"></a>Dodawanie funkcji do projektu

W funkcjach biblioteki klas języka C# powiązania używane przez funkcję są definiowane przez zastosowanie atrybutów w kodzie. Podczas tworzenia wyzwalaczy funkcji na podstawie podanych szablonów są stosowane atrybuty wyzwalacza. 

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy węzeł projektu i wybierz **polecenie Dodaj** nowy  >  **element**. 

2. Wybierz **pozycję Funkcja platformy Azure,** wprowadź **nazwę** klasy, a następnie wybierz pozycję **Dodaj**.

3. Wybierz wyzwalacz, ustaw właściwości powiązania, a następnie wybierz przycisk **OK.** W poniższym przykładzie przedstawiono ustawienia tworzenia funkcji wyzwalacza magazynu kolejek. 

    ![Tworzenie funkcji wyzwalacza magazynu kolejek](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    W tym przykładzie wyzwalacza użyto parametrów połączenia z kluczem o nazwie `QueueStorage` . Zdefiniuj to ustawienie parametrów połączenia w [local.settings.jspliku](functions-run-local.md#local-settings-file).

4. Sprawdź nowo dodaną klasę. Zobaczysz statyczną `Run()` metodę przypisaną do `FunctionName` atrybutu . Ten atrybut wskazuje, że metoda jest punktem wejścia dla funkcji.

    Na przykład następująca klasa języka C# reprezentuje podstawową funkcję wyzwalacza magazynu kolejek:

    ```csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.Extensions.Logging;

    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]
            public static void Run([QueueTrigger("myqueue-items", 
                Connection = "QueueStorage")]string myQueueItem, ILogger log)
            {
                log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ```

Atrybut specyficzny dla powiązania jest stosowany do każdego parametru powiązania dostarczonego do metody punktu wejścia. Atrybut przyjmuje informacje o powiązaniu jako parametry. W poprzednim przykładzie pierwszy parametr ma zastosowany `QueueTrigger` atrybut wskazujący funkcję wyzwalacza magazynu kolejek. Nazwa kolejki i nazwa ustawienia parametrów połączenia są przekazywane jako parametry do `QueueTrigger` atrybutu. Aby uzyskać więcej informacji, zobacz [Powiązania usługi Azure Queue Storage dla Azure Functions](functions-bindings-storage-queue-trigger.md).

Użyj powyższej procedury, aby dodać więcej funkcji do projektu aplikacji funkcji. Każda funkcja w projekcie może mieć inny wyzwalacz, ale funkcja musi mieć dokładnie jeden wyzwalacz. Aby uzyskać więcej informacji, [zobacz Azure Functions wyzwalaczy i powiązań](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Dodawanie powiązań

Podobnie jak w przypadku wyzwalaczy powiązania wejściowe i wyjściowe są dodawane do funkcji jako atrybuty powiązania. Dodaj powiązania do funkcji w następujący sposób:

1. Upewnij się, że [projekt został skonfigurowany do tworzenia aplikacji lokalnych.](#configure-the-project-for-local-development)

2. Dodaj odpowiedni pakiet rozszerzenia NuGet dla określonego powiązania. 

   Aby uzyskać więcej informacji, zobacz Biblioteka [klas języka C# z Visual Studio](./functions-bindings-register.md#local-csharp). Wymagania dotyczące pakietu NuGet specyficzne dla powiązania można znaleźć w artykule referencyjnym dla powiązania. Na przykład wymagania dotyczące pakietu dla wyzwalacza Event Hubs można znaleźć w [Event Hubs referencyjnym powiązania.](functions-bindings-event-hubs.md)

3. Jeśli istnieją ustawienia aplikacji, których wymaga powiązanie, dodaj je do kolekcji `Values` w lokalnym pliku [ustawień](functions-run-local.md#local-settings-file). 

   Funkcja używa tych wartości, gdy jest uruchamiana lokalnie. Gdy funkcja jest uruchamiana w aplikacji funkcji na platformie Azure, używa [ustawień aplikacji funkcji](#function-app-settings).

4. Dodaj odpowiedni atrybut powiązania do sygnatury metody. W poniższym przykładzie komunikat w kolejce wyzwala funkcję, a powiązanie wyjściowe tworzy nowy komunikat w kolejce z tym samym tekstem w innej kolejce.

    ```csharp
    public static class SimpleExampleWithOutput
    {
        [FunctionName("CopyQueueMessage")]
        public static void Run(
            [QueueTrigger("myqueue-items-source", Connection = "AzureWebJobsStorage")] string myQueueItem, 
            [Queue("myqueue-items-destination", Connection = "AzureWebJobsStorage")] out string myQueueItemCopy,
            ILogger log)
        {
            log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
            myQueueItemCopy = myQueueItem;
        }
    }
    ```
   Połączenie z usługą Queue Storage jest uzyskiwane z `AzureWebJobsStorage` ustawienia . Aby uzyskać więcej informacji, zobacz artykuł referencyjny dla określonego powiązania. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Testowanie funkcji

Podstawowe narzędzia usługi Azure Functions umożliwiają uruchamianie projektu usługi Azure Functions na lokalnym komputerze deweloperskim. Aby uzyskać więcej informacji, zobacz [Praca z Azure Functions Core Tools](functions-run-local.md). Monit o zainstalowanie tych narzędzi jest wyświetlany przy pierwszym uruchomieniu funkcji z Visual Studio. 

Aby przetestować funkcję w Visual Studio:

1. Naciśnij F5. Po wyświetleniu monitu zaakceptuj żądanie programu Visual Studio dotyczące pobrania i zainstalowania podstawowych narzędzi usługi Azure Functions (CLI). Może być również konieczne włączenie wyjątku zapory, aby narzędzia mogły obsługiwać żądania HTTP.

2. Po uruchomieniu projektu przetestuj kod tak, jak w przypadku testowania wdrożonej funkcji. 

   Aby uzyskać więcej informacji, [zobacz Strategie testowania kodu w programie Azure Functions](functions-test-a-function.md). Po uruchomieniu Visual Studio w trybie debugowania punkty przerwania są uruchamiane zgodnie z oczekiwaniami.

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->


## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Podczas publikowania z Visual Studio jest używana jedna z dwóch metod wdrażania:

* [Web Deploy:](functions-deployment-technologies.md#web-deploy-msdeploy)pakuje i wdraża aplikacje systemu Windows na dowolnym serwerze usług IIS.
* [Zip Deploy with run-From-package enabled](functions-deployment-technologies.md#zip-deploy)(Wdrażanie z pliku zip z włączonym uruchamianiem z pakietu): zalecane Azure Functions wdrożeń.

Aby opublikować projekt w aplikacji funkcji na platformie Azure, należy wykonać poniższe kroki.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Ustawienia aplikacji funkcji

Ponieważ Visual Studio nie przekaże tych ustawień automatycznie podczas publikowania projektu, wszystkie ustawienia, które dodasz w witrynie local.settings.js, musisz również dodać do aplikacji funkcji na platformie Azure.

Najprostszym sposobem przekazania wymaganych ustawień do aplikacji funkcji  na platformie Azure jest wybranie linku Zarządzaj ustawieniami Azure App Service, który zostanie wyświetlony po pomyślnym opublikowaniu projektu.

:::image type="content" source="./media/functions-develop-vs/functions-vstools-app-settings.png" alt-text="Ustawienia w oknie Publikowanie":::

Wybranie tego linku powoduje **wyświetlenie okna dialogowego** Ustawienia aplikacji dla aplikacji funkcji, w którym można dodawać nowe ustawienia aplikacji lub modyfikować istniejące.

![Ustawienia aplikacji](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Ustawienie** Lokalne wyświetla wartość ustawienia w local.settings.jspliku, a zdalne wyświetla bieżącą wartość ustawienia w aplikacji funkcji na platformie Azure.  Wybierz **pozycję Dodaj ustawienie,** aby utworzyć nowe ustawienie aplikacji. Użyj wartości **Wstaw z linku lokalnego,** aby skopiować wartość ustawienia do **pola** Zdalne. Oczekujące zmiany są zapisywane w pliku ustawień lokalnych i aplikacji funkcji po wybraniu przycisku **OK.**

> [!NOTE]
> Domyślnie plik w local.settings.jsnie jest zaewidencjonowany do kontroli źródła. Oznacza to, że w przypadku sklonowania lokalnego projektu funkcji z kontroli źródła projekt nie ma local.settings.jspliku. W takim przypadku należy ręcznie utworzyć plik local.settings.jsw katalogu głównym projektu, aby okno dialogowe **Ustawienia** aplikacji działało zgodnie z oczekiwaniami. 

Ustawieniami aplikacji można również zarządzać na jeden z tych sposobów:

* [Użyj Azure Portal](functions-how-to-use-azure-function-app-settings.md#settings).
* [Użyj opcji `--publish-local-settings` publikowania w Azure Functions Core Tools](functions-run-local.md#publish).
* [Użyj interfejsu wiersza polecenia platformy Azure.](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set)

## <a name="monitoring-functions"></a>Funkcje monitorowania

Zalecanym sposobem monitorowania wykonywania funkcji jest zintegrowanie aplikacji funkcji z usługą Azure Application Insights. Podczas tworzenia aplikacji funkcji w Azure Portal ta integracja jest domyślnie wykonywana. Jednak podczas tworzenia aplikacji funkcji podczas publikowania Visual Studio integracja z aplikacją funkcji na platformie Azure nie jest wykonywana. Aby dowiedzieć się, jak nawiązać Application Insights z aplikacją funkcji, zobacz [Włączanie integracji Application Insights funkcji.](configure-monitoring.md#enable-application-insights-integration)

Aby dowiedzieć się więcej na temat monitorowania przy użyciu Application Insights, [zobacz Monitorowanie Azure Functions](functions-monitoring.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Functions Core Tools, zobacz [Praca z Azure Functions Core Tools](functions-run-local.md).

Aby uzyskać więcej informacji na temat tworzenia funkcji jako bibliotek klas .NET, [zobacz Azure Functions języka C# dla deweloperów.](functions-dotnet-class-library.md) Ten artykuł zawiera również linki do przykładów użycia atrybutów do deklarowania różnych typów powiązań obsługiwanych przez Azure Functions.    
