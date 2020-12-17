---
title: Tworzenie funkcji usługi Azure Functions w programie Visual Studio
description: Dowiedz się, jak opracowywać i testować Azure Functions przy użyciu narzędzi Azure Functions Tools for Visual Studio 2019.
ms.custom: vs-azure, devx-track-csharp
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 877c82e375b0ea469071402b83fadbd634177f3f
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655819"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Tworzenie funkcji usługi Azure Functions w programie Visual Studio  

Program Visual Studio umożliwia tworzenie, testowanie i wdrażanie funkcji biblioteki klas w języku C# na platformie Azure. Jeśli to środowisko jest pierwsze z Azure Functions, zobacz [wprowadzenie do Azure Functions](functions-overview.md).

Program Visual Studio zapewnia następujące korzyści podczas opracowywania funkcji: 

* Edytuj, Kompiluj i uruchamiaj funkcje na lokalnym komputerze deweloperskim. 
* Opublikuj projekt Azure Functions bezpośrednio na platformie Azure i Utwórz zasoby platformy Azure zgodnie z potrzebami. 
* Użyj atrybutów języka C#, aby zadeklarować powiązania funkcji bezpośrednio w kodzie C#.
* Opracowywanie i wdrażanie wstępnie skompilowanych funkcji języka C#. Funkcje wstępnie spełnione zapewniają lepszą wydajność na zimno niż w przypadku funkcji opartych na skrypcie języka C#. 
* Zakoduj funkcje w języku C#, a wszystkie zalety programowania w programie Visual Studio. 

Ten artykuł zawiera szczegółowe informacje dotyczące sposobu korzystania z programu Visual Studio do opracowywania funkcji biblioteki klas w języku C# i publikowania ich na platformie Azure. Przed przeczytaniem tego artykułu warto rozważyć zakończenie [funkcji szybkiego startu dla programu Visual Studio](functions-create-your-first-function-visual-studio.md). 

O ile nie wskazano inaczej, przedstawione procedury i przykłady dotyczą programu Visual Studio 2019. 

## <a name="prerequisites"></a>Wymagania wstępne

- Narzędzia Azure Functions. Aby dodać narzędzia usługi Azure Functions, Uwzględnij obciążenie **Programowanie na platformie Azure** w instalacji programu Visual Studio. Narzędzia Azure Functions są dostępne w ramach obciążeń programistycznych platformy Azure, począwszy od programu Visual Studio 2017.

- Inne zasoby, które są potrzebne, takie jak konto usługi Azure Storage, są tworzone w ramach subskrypcji podczas procesu publikowania.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> W programie Visual Studio 2017 obciążenie Programowanie na platformie Azure instaluje Azure Functions narzędzia jako oddzielne rozszerzenie. Podczas aktualizowania instalacji programu Visual Studio 2017 upewnij się, że używasz najnowszej [wersji](#check-your-tools-version) narzędzi Azure Functions. W poniższych sekcjach pokazano, jak sprawdzić i (w razie potrzeby) zaktualizować rozszerzenie narzędzi Azure Functions w programie Visual Studio 2017. 
>
> Pomiń te sekcje, jeśli używasz programu Visual Studio 2019.

### <a name="check-your-tools-version-in-visual-studio-2017"></a><a name="check-your-tools-version"></a>Sprawdź wersję narzędzi w programie Visual Studio 2017

1. W menu **Narzędzia** wybierz pozycję **rozszerzenia i aktualizacje**. Rozwiń węzeł **zainstalowane**  >  **Narzędzia**, a następnie wybierz **Azure Functions i narzędzia do zadań w sieci Web**.

    ![Weryfikowanie wersji narzędzi funkcji](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Zanotuj zainstalowaną **wersję** i porównaj tę wersję z najnowszą wersją wymienioną w [informacjach o wersji](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

1. Jeśli Twoja wersja jest starsza, zaktualizuj swoje narzędzia w programie Visual Studio, jak pokazano w poniższej sekcji.

### <a name="update-your-tools-in-visual-studio-2017"></a>Aktualizowanie narzędzi w programie Visual Studio 2017

1. W oknie dialogowym **rozszerzenia i aktualizacje** rozwiń węzeł **aktualizacje**  >  **Visual Studio Marketplace**, wybierz pozycję **narzędzia Azure Functions i zadania sieci Web** , a następnie wybierz pozycję **Aktualizuj**.

    ![Aktualizowanie wersji narzędzi funkcji](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. Po pobraniu aktualizacji narzędzi wybierz pozycję **Zamknij**, a następnie zamknij program Visual Studio, aby wyzwolić aktualizację narzędzi z instalatorem VSIX.

1. W instalatorze VSIX wybierz pozycję **Modyfikuj** , aby zaktualizować narzędzia. 

1. Po zakończeniu aktualizacji wybierz pozycję **Zamknij**, a następnie ponownie uruchom program Visual Studio.

> [!NOTE]  
> W programie Visual Studio 2019 i nowszych rozszerzenie narzędzi Azure Functions zostało zaktualizowane w ramach programu Visual Studio.  

## <a name="create-an-azure-functions-project"></a>Tworzenie projektu usługi Azure Functions

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Po utworzeniu projektu Azure Functions, szablon projektu tworzy projekt C#, instaluje `Microsoft.NET.Sdk.Functions` pakiet NuGet i ustawia platformę docelową. Nowy projekt zawiera następujące pliki:

* **host.js**: umożliwia skonfigurowanie hosta funkcji. Te ustawienia są stosowane zarówno w przypadku uruchamiania lokalnego, jak i na platformie Azure. Aby uzyskać więcej informacji, zobacz [host.json Reference](functions-host-json.md).

* **local.settings.js**: utrzymuje ustawienia używane podczas lokalnego uruchamiania funkcji. Te ustawienia nie są używane podczas uruchamiania na platformie Azure. Aby uzyskać więcej informacji, zobacz [plik ustawień lokalnych](#local-settings-file).

    >[!IMPORTANT]
    >Ponieważ local.settings.jsw pliku może zawierać wpisy tajne, należy wykluczyć je z kontroli źródła projektu. Upewnij się, że ustawienie **Kopiuj do katalogu wyjściowego** dla tego pliku jest ustawione na wartość **Kopiuj, jeśli nowszy**. 

Aby uzyskać więcej informacji, zobacz [Functions Library Project](functions-dotnet-class-library.md#functions-class-library-project).

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Program Visual Studio nie przekazuje automatycznie ustawień w local.settings.jsprzy publikowaniu projektu. Aby upewnić się, że te ustawienia istnieją także w aplikacji funkcji na platformie Azure, przekaż je po opublikowaniu projektu. Aby uzyskać więcej informacji, zobacz [Funkcja ustawienia aplikacji](#function-app-settings). Wartości w `ConnectionStrings` kolekcji nigdy nie są publikowane.

Kod może również odczytać wartości ustawień aplikacji funkcji jako zmienne środowiskowe. Aby uzyskać więcej informacji, zobacz [zmienne środowiskowe](functions-dotnet-class-library.md#environment-variables).

## <a name="configure-your-build-output-settings"></a>Skonfiguruj ustawienia danych wyjściowych kompilacji

Podczas kompilowania projektu Azure Functions narzędzia kompilacji optymalizują dane wyjściowe, tak że zachowywana jest tylko jedna kopia dowolnego zestawu, który jest udostępniany przez środowisko uruchomieniowe funkcji. Wynikiem jest zoptymalizowana kompilacja, która zapisuje tyle miejsca, ile to możliwe. Jednak po przejściu do nowszej wersji dowolnego zestawu projektu narzędzia kompilacji mogą nie wiedzieć, że te zestawy muszą zostać zachowane. Aby upewnić się, że te zestawy są zachowywane podczas procesu optymalizacji, można je określić przy użyciu `FunctionsPreservedDependencies` elementów w pliku projektu (. csproj):

```xml
  <ItemGroup>
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.dll" />
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.Extensions.dll" />
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.Features.dll" />
  </ItemGroup>
```

## <a name="configure-the-project-for-local-development"></a>Konfigurowanie projektu na potrzeby lokalnego projektowania

Środowisko uruchomieniowe funkcji używa wewnętrznego konta usługi Azure Storage. Dla wszystkich typów wyzwalaczy innych niż HTTP i webhook Ustaw `Values.AzureWebJobsStorage` klucz na prawidłowe parametry połączenia z kontem usługi Azure Storage. Aplikacja funkcji może również użyć [emulatora usługi Azure Storage](../storage/common/storage-use-emulator.md) do `AzureWebJobsStorage` Ustawienia połączenia, które jest wymagane przez projekt. Aby użyć emulatora, ustaw wartość `AzureWebJobsStorage` na `UseDevelopmentStorage=true` . Zmień to ustawienie na rzeczywiste parametry połączenia konta magazynu przed wdrożeniem.

Aby ustawić parametry połączenia konta magazynu:

1. W programie Visual Studio wybierz pozycję **Wyświetl**  >  **Eksplorator chmury**.

2. W programie **Cloud Explorer** rozwiń węzeł **konta magazynu**, a następnie wybierz konto magazynu. Na karcie **Właściwości** skopiuj wartość **podstawowe parametry połączenia** .

2. W projekcie Otwórz local.settings.jsw pliku i ustaw wartość `AzureWebJobsStorage` klucza na skopiowane parametry połączenia.

3. Powtórz poprzedni krok, aby dodać unikatowe klucze do `Values` tablicy dla innych połączeń wymaganych przez funkcje. 

## <a name="add-a-function-to-your-project"></a>Dodawanie funkcji do projektu

W funkcjach biblioteki klas języka C# powiązania używane przez funkcję są definiowane przez zastosowanie atrybutów w kodzie. Gdy tworzysz wyzwalacze funkcji z dostarczonych szablonów, zostaną zastosowane atrybuty wyzwalacza. 

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy węzeł projektu i wybierz polecenie **Dodaj**  >  **nowy element**. 

2. Wybierz pozycję **Funkcja platformy Azure**, wprowadź **nazwę** klasy, a następnie wybierz pozycję **Dodaj**.

3. Wybierz wyzwalacz, ustaw właściwości powiązania, a następnie wybierz przycisk **OK**. Poniższy przykład przedstawia ustawienia tworzenia funkcji wyzwalacza usługi queue storage. 

    ![Tworzenie funkcji wyzwalacza magazynu kolejki](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Ten przykład wyzwalacza używa parametrów połączenia z kluczem o nazwie `QueueStorage` . Zdefiniuj to ustawienie parametrów połączenia w [local.settings.jspliku](functions-run-local.md#local-settings-file).

4. Zapoznaj się z nowo dodaną klasą. Zostanie wyświetlona `Run()` metoda statyczna, która ma przypisany `FunctionName` atrybut. Ten atrybut wskazuje, że metoda jest punktem wejścia dla funkcji.

    Na przykład następująca Klasa języka C# reprezentuje funkcję wyzwalacza podstawowego magazynu kolejki:

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

Atrybut specyficzny dla powiązania jest stosowany do każdego parametru powiązania dostarczonego do metody punktu wejścia. Ten atrybut przyjmuje informacje o powiązaniu jako parametry. W poprzednim przykładzie pierwszy parametr ma `QueueTrigger` zastosowany atrybut wskazujący funkcję wyzwalacza magazynu kolejki. Nazwa kolejki i ustawienia parametrów połączenia są przenoszone jako parametry do `QueueTrigger` atrybutu. Aby uzyskać więcej informacji, zobacz [powiązania usługi Azure queue storage dla Azure Functions](functions-bindings-storage-queue-trigger.md).

Użyj powyższej procedury, aby dodać więcej funkcji do projektu aplikacji funkcji. Każda funkcja w projekcie może mieć inny wyzwalacz, ale funkcja musi mieć dokładnie jeden wyzwalacz. Aby uzyskać więcej informacji, zobacz temat [Azure Functions wyzwalacze i koncepcje powiązań](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Dodawanie powiązań

Podobnie jak w przypadku wyzwalaczy, powiązania wejściowe i wyjściowe są dodawane do funkcji jako atrybuty powiązania. Dodaj powiązania do funkcji w następujący sposób:

1. Upewnij się, że [projekt został skonfigurowany do lokalnego projektowania](#configure-the-project-for-local-development).

2. Dodaj odpowiedni pakiet rozszerzenia NuGet dla określonego powiązania. 

   Aby uzyskać więcej informacji, zobacz [Biblioteka klas języka C# w programie Visual Studio](./functions-bindings-register.md#local-csharp). Znajdź wymagania pakietu NuGet dotyczące powiązań w artykule referencyjnym dla powiązania. Na przykład Znajdź wymagania dotyczące pakietu dla wyzwalacza Event Hubs w [artykule Event Hubs powiązania](functions-bindings-event-hubs.md).

3. Jeśli istnieją ustawienia aplikacji wymagane przez powiązanie, Dodaj je do `Values` kolekcji w [lokalnym pliku ustawień](functions-run-local.md#local-settings-file). 

   Funkcja używa tych wartości, gdy działa lokalnie. Gdy funkcja działa w aplikacji funkcji na platformie Azure, używa [ustawień aplikacji funkcji](#function-app-settings).

4. Dodaj odpowiedni atrybut powiązania do sygnatury metody. W poniższym przykładzie komunikat kolejki wyzwala funkcję, a powiązanie danych wyjściowych tworzy nowy komunikat kolejki z tym samym tekstem w innej kolejce.

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
   Połączenie z usługą queue storage jest uzyskiwane z tego `AzureWebJobsStorage` Ustawienia. Aby uzyskać więcej informacji, zobacz artykuł referencyjny dla określonego powiązania. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Testowanie funkcji

Podstawowe narzędzia usługi Azure Functions umożliwiają uruchamianie projektu usługi Azure Functions na lokalnym komputerze deweloperskim. Aby uzyskać więcej informacji, zobacz [Work with Azure Functions Core Tools](functions-run-local.md). Po pierwszym uruchomieniu funkcji z programu Visual Studio zostanie wyświetlony monit o zainstalowanie tych narzędzi. 

Aby przetestować funkcję w programie Visual Studio:

1. Naciśnij F5. Po wyświetleniu monitu zaakceptuj żądanie programu Visual Studio dotyczące pobrania i zainstalowania podstawowych narzędzi usługi Azure Functions (CLI). Może być również konieczne włączenie wyjątku zapory, aby narzędzia mogły obsługiwać żądania HTTP.

2. Po uruchomieniu projektu Przetestuj swój kod, tak jak w przypadku testowania wdrożonej funkcji. 

   Aby uzyskać więcej informacji, zobacz [strategie testowania kodu w Azure Functions](functions-test-a-function.md). Po uruchomieniu programu Visual Studio w trybie debugowania punkty przerwania są trafień zgodnie z oczekiwaniami.

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->


## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Podczas publikowania z programu Visual Studio używa jednej z dwóch metod wdrażania:

* [Web Deploy](functions-deployment-technologies.md#web-deploy-msdeploy): pakiety i wdrożenia aplikacji systemu Windows na dowolnym serwerze usług IIS.
* [Wdrożenie pliku zip z włączonym uruchamianiem pakietu](functions-deployment-technologies.md#zip-deploy): zalecane w przypadku wdrożeń Azure Functions.

Wykonaj następujące kroki, aby opublikować projekt w aplikacji funkcji na platformie Azure.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Ustawienia aplikacji funkcji

Ponieważ program Visual Studio nie przekazuje tych ustawień automatycznie podczas publikowania projektu, wszystkie ustawienia, które należy dodać do local.settings.js, należy również dodać do aplikacji funkcji na platformie Azure.

Najprostszym sposobem przekazania wymaganych ustawień do aplikacji funkcji na platformie Azure jest wybranie linku **zarządzaj Azure App Service Settings** , które pojawia się po pomyślnym opublikowaniu projektu.

:::image type="content" source="./media/functions-develop-vs/functions-vstools-app-settings.png" alt-text="Ustawienia w oknie publikowania":::

Wybranie tego linku powoduje wyświetlenie okna dialogowego **Ustawienia aplikacji** dla aplikacji funkcji, w którym można dodać nowe ustawienia aplikacji lub zmodyfikować istniejące.

![Ustawienia aplikacji](./media/functions-develop-vs/functions-vstools-app-settings2.png)

Funkcja **Local** wyświetla wartość ustawienia w local.settings.jsw pliku, a element **zdalny** wyświetla bieżącą wartość ustawienia w aplikacji funkcji na platformie Azure. Wybierz pozycję **Dodaj ustawienie** , aby utworzyć nowe ustawienie aplikacji. Użyj linku **Wstaw wartość z lokalnego** , aby skopiować wartość ustawienia do pola **zdalnego** . Oczekujące zmiany są zapisywane w pliku ustawień lokalnych i aplikacji funkcji po wybraniu **przycisku OK**.

> [!NOTE]
> Domyślnie local.settings.jsna pliku nie jest sprawdzany w kontroli źródła. Oznacza to, że w przypadku klonowania projektu funkcji lokalnych z kontroli źródła projekt nie ma local.settings.jsw pliku. W takim przypadku należy ręcznie utworzyć local.settings.jsw pliku w katalogu głównym projektu, aby okno dialogowe **Ustawienia aplikacji** działało zgodnie z oczekiwaniami. 

Można również zarządzać ustawieniami aplikacji w jeden z następujących sposobów:

* [Użyj Azure Portal](functions-how-to-use-azure-function-app-settings.md#settings).
* [Użyj `--publish-local-settings` opcji publikuj w Azure Functions Core Tools](functions-run-local.md#publish).
* [Użyj interfejsu wiersza polecenia platformy Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

## <a name="monitoring-functions"></a>Funkcje monitorowania

Zalecanym sposobem monitorowania wykonywania funkcji jest zintegrowanie aplikacji funkcji z usługą Azure Application Insights. Po utworzeniu aplikacji funkcji w Azure Portal Ta integracja jest domyślnie wykonywana. Jednak podczas tworzenia aplikacji funkcji podczas publikowania programu Visual Studio nie jest przeprowadzana integracja w aplikacji funkcji na platformie Azure. Aby dowiedzieć się, jak połączyć Application Insights z aplikacją funkcji, zobacz [Włączanie integracji Application Insights](configure-monitoring.md#enable-application-insights-integration).

Aby dowiedzieć się więcej na temat monitorowania za pomocą Application Insights, zobacz [Azure Functions monitorowania](functions-monitoring.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Functions Core Tools, zobacz [Work with Azure Functions Core Tools](functions-run-local.md).

Aby uzyskać więcej informacji na temat opracowywania funkcji jako bibliotek klas .NET, zobacz [Azure Functions C# Developer Reference](functions-dotnet-class-library.md). Ten artykuł zawiera również linki do przykładów użycia atrybutów do deklarowania różnych typów powiązań obsługiwanych przez Azure Functions.    
