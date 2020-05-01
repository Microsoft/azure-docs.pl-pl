---
title: Opracowywanie funkcji usługi Azure Functions przy użyciu programu Visual Studio
description: Dowiedz się, jak opracowywać i testować Azure Functions przy użyciu narzędzi Azure Functions Tools for Visual Studio 2019.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: b3d683153a1e70f4c65dcc5e401f4ee702af8b49
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277104"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Opracowywanie funkcji usługi Azure Functions przy użyciu programu Visual Studio  

Program Visual Studio umożliwia tworzenie, testowanie i wdrażanie funkcji biblioteki klas w języku C# na platformie Azure. Jeśli to środowisko jest pierwsze dzięki Azure Functions, możesz dowiedzieć się więcej na temat [wprowadzenia do Azure Functions](functions-overview.md).

Program Visual Studio oferuje następujące korzyści podczas opracowywania funkcji: 

* Edytuj, Kompiluj i uruchamiaj funkcje na lokalnym komputerze deweloperskim. 
* Opublikuj projekt Azure Functions bezpośrednio na platformie Azure i Utwórz zasoby platformy Azure zgodnie z potrzebami. 
* Użyj atrybutów języka C#, aby zadeklarować powiązania funkcji bezpośrednio w kodzie C#.
* Opracowywanie i wdrażanie wstępnie skompilowanych funkcji języka C#. Funkcje wstępnie spełnione zapewniają lepszą wydajność na zimno niż w przypadku funkcji opartych na skrypcie języka C#. 
* Zakoduj funkcje w języku C#, a wszystkie zalety programowania w programie Visual Studio. 

Ten artykuł zawiera szczegółowe informacje dotyczące sposobu korzystania z programu Visual Studio do opracowywania funkcji biblioteki klas w języku C# i publikowania ich na platformie Azure. Przed przeczytaniem tego artykułu należy wykonać [funkcje szybkiego startu dla programu Visual Studio](functions-create-your-first-function-visual-studio.md). 

O ile nie wskazano inaczej, przedstawione procedury i przykłady dotyczą programu Visual Studio 2019. 

## <a name="prerequisites"></a>Wymagania wstępne

Narzędzia Azure Functions są dołączone do obciążeń programistycznych platformy Azure programu Visual Studio, począwszy od programu Visual Studio 2017. Upewnij się, że dołączysz obciążenie **Programowanie na platformie Azure** w instalacji programu Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Inne zasoby, które są potrzebne, takie jak konto usługi Azure Storage, są tworzone w ramach subskrypcji podczas procesu publikowania.

> [!NOTE]
> W programie Visual Studio 2017 obciążenie Programowanie na platformie Azure instaluje Azure Functions narzędzia jako oddzielne rozszerzenie. Podczas aktualizowania programu Visual Studio 2017 upewnij się również, że używasz najnowszej [wersji](#check-your-tools-version) narzędzi Azure Functions. W poniższych sekcjach pokazano, jak sprawdzić i (w razie potrzeby) zaktualizować rozszerzenie narzędzi Azure Functions w programie Visual Studio 2017. 
>
> Pomiń tę sekcję w przypadku korzystania z programu Visual Studio 2019.

### <a name="check-your-tools-version-in-visual-studio-2017"></a><a name="check-your-tools-version"></a>Sprawdź wersję narzędzi w programie Visual Studio 2017

1. W menu **Narzędzia** wybierz pozycję **rozszerzenia i aktualizacje**. Rozwiń węzeł **zainstalowane** > **Narzędzia** i wybierz **Azure Functions i narzędzia do zadań w sieci Web**.

    ![Weryfikowanie wersji narzędzi funkcji](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Zanotuj zainstalowaną **wersję**. Tę wersję można porównać z najnowszą wersją wymienioną [w informacjach o wersji](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

1. Jeśli Twoja wersja jest starsza, zaktualizuj swoje narzędzia w programie Visual Studio, jak pokazano w poniższej sekcji.

### <a name="update-your-tools-in-visual-studio-2017"></a>Aktualizowanie narzędzi w programie Visual Studio 2017

1. W oknie dialogowym **rozszerzenia i aktualizacje** rozwiń węzeł **aktualizacje** > **Visual Studio Marketplace**, wybierz pozycję **narzędzia Azure Functions i zadania sieci Web** , a następnie wybierz pozycję **Aktualizuj**.

    ![Aktualizowanie wersji narzędzi funkcji](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. Po pobraniu aktualizacji narzędzi Zamknij program Visual Studio, aby wyzwolić aktualizację narzędzi przy użyciu Instalatora VSIX.

1. W instalatorze wybierz **przycisk OK** , aby rozpocząć, a następnie **zmodyfikuj** , aby zaktualizować narzędzia. 

1. Po zakończeniu aktualizacji wybierz pozycję **Zamknij** i uruchom ponownie program Visual Studio.

> [!NOTE]  
> W programie Visual Studio 2019 i nowszych rozszerzenie narzędzi Azure Functions zostało zaktualizowane w ramach programu Visual Studio.  

## <a name="create-an-azure-functions-project"></a>Tworzenie projektu usługi Azure Functions

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Szablon projektu tworzy projekt w języku C#, instaluje pakiet `Microsoft.NET.Sdk.Functions` NuGet i ustawia platformę docelową. Nowy projekt zawiera następujące pliki:

* plik **host. JSON**: umożliwia skonfigurowanie hosta funkcji. Te ustawienia są stosowane zarówno w przypadku uruchamiania lokalnego, jak i na platformie Azure. Aby uzyskać więcej informacji, zobacz [Dokumentacja pliku host. JSON](functions-host-json.md).

* **Local. Settings. JSON**: utrzymuje ustawienia używane lokalnie do uruchamiania funkcji. Te ustawienia nie są używane podczas uruchamiania na platformie Azure. Aby uzyskać więcej informacji, zobacz [plik ustawień lokalnych](#local-settings-file).

    >[!IMPORTANT]
    >Ponieważ plik Local. Settings. JSON może zawierać wpisy tajne, należy go wykluczyć z kontroli źródła projektu. Ustawienie **Kopiuj do katalogu wyjściowego** dla tego pliku powinno być zawsze **kopiowane, jeśli nowsze**. 

Aby uzyskać więcej informacji, zobacz [Functions Library Project](functions-dotnet-class-library.md#functions-class-library-project).

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Ustawienia w pliku Local. Settings. JSON nie są przekazywane automatycznie przy publikowaniu projektu. Aby upewnić się, że te ustawienia istnieją także w aplikacji funkcji na platformie Azure, należy je przekazać po opublikowaniu projektu. Aby dowiedzieć się więcej, zobacz [Funkcja ustawienia aplikacji](#function-app-settings).

Wartości w **connectionStrings** nigdy nie są publikowane.

Wartości ustawień aplikacji funkcji można także odczytać w kodzie jako zmienne środowiskowe. Aby uzyskać więcej informacji, zobacz [zmienne środowiskowe](functions-dotnet-class-library.md#environment-variables).

## <a name="configure-the-project-for-local-development"></a>Konfigurowanie projektu na potrzeby lokalnego projektowania

Środowisko uruchomieniowe funkcji używa wewnętrznego konta usługi Azure Storage. Dla wszystkich typów wyzwalaczy innych niż HTTP i webhook, należy ustawić wartość **. AzureWebJobsStorage** klucza jako prawidłowe parametry połączenia konta usługi Azure Storage. Aplikacja funkcji może również użyć [emulatora usługi Azure Storage](../storage/common/storage-use-emulator.md) dla ustawienia połączenia **AzureWebJobsStorage** , które jest wymagane przez projekt. Aby użyć emulatora, ustaw wartość **AzureWebJobsStorage** na `UseDevelopmentStorage=true`. Zmień to ustawienie na rzeczywiste parametry połączenia konta magazynu przed wdrożeniem.

Aby ustawić parametry połączenia konta magazynu:

1. W programie Visual Studio Otwórz program **Cloud Explorer**, rozwiń węzeł **konto** > magazynu**konta magazynu**, a następnie na karcie **Właściwości** skopiuj wartość **podstawowe parametry połączenia** .

2. W projekcie Otwórz plik Local. Settings. JSON i ustaw wartość klucza **AzureWebJobsStorage** na skopiowane parametry połączenia.

3. Powtórz poprzedni krok, aby dodać unikatowe klucze do tablicy **wartości** dla innych połączeń wymaganych przez funkcje. 

## <a name="add-a-function-to-your-project"></a>Dodawanie funkcji do projektu

W funkcjach biblioteki klas języka C# powiązania używane przez funkcję są definiowane przez zastosowanie atrybutów w kodzie. Gdy tworzysz wyzwalacze funkcji z dostarczonych szablonów, zostaną zastosowane atrybuty wyzwalacza. 

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy węzeł projektu i wybierz polecenie **Dodaj** > **Nowy element**. Wybierz pozycję **Funkcja platformy Azure**, wpisz **nazwę** klasy, a następnie kliknij przycisk **Dodaj**.

2. Wybierz wyzwalacz, ustaw właściwości powiązania, a następnie kliknij przycisk **Utwórz**. Poniższy przykład przedstawia ustawienia podczas tworzenia funkcji wyzwalanej przez magazyn kolejek. 

    ![Tworzenie funkcji wyzwalanej przez kolejkę](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Ten przykład wyzwalacza używa parametrów połączenia z kluczem o nazwie **QueueStorage**. To ustawienie parametrów połączenia musi być zdefiniowane w [pliku Local. Settings. JSON](functions-run-local.md#local-settings-file).

3. Zapoznaj się z nowo dodaną klasą. Zostanie wyświetlona statyczna metoda **Run** , która ma atrybut **FunctionName** . Ten atrybut wskazuje, że metoda jest punktem wejścia dla funkcji.

    Na przykład następująca Klasa języka C# reprezentuje funkcję wyzwalaną przez podstawowy magazyn kolejek:

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

    Atrybut specyficzny dla powiązania jest stosowany do każdego parametru powiązania dostarczonego do metody punktu wejścia. Ten atrybut przyjmuje informacje o powiązaniu jako parametry. W poprzednim przykładzie pierwszy parametr ma zastosowany atrybut **QueueTrigger** , wskazujący funkcję wyzwalaną przez kolejkę. Nazwa kolejki i ustawienia parametrów połączenia są przenoszone jako parametry do atrybutu **QueueTrigger** . Aby uzyskać więcej informacji, zobacz [powiązania usługi Azure queue storage dla Azure Functions](functions-bindings-storage-queue-trigger.md).

Korzystając z powyższej procedury, można dodać więcej funkcji do projektu aplikacji funkcji. Każda funkcja w projekcie może mieć inny wyzwalacz, ale funkcja musi mieć dokładnie jeden wyzwalacz. Aby uzyskać więcej informacji, zobacz temat [Azure Functions wyzwalacze i koncepcje powiązań](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Dodawanie powiązań

Podobnie jak w przypadku wyzwalaczy, powiązania wejściowe i wyjściowe są dodawane do funkcji jako atrybuty powiązania. Dodaj powiązania do funkcji w następujący sposób:

1. Upewnij się, że [projekt został skonfigurowany do lokalnego projektowania](#configure-the-project-for-local-development).

2. Dodaj odpowiedni pakiet rozszerzenia NuGet dla określonego powiązania. Aby uzyskać więcej informacji, zobacz [lokalne programowanie w języku C# przy użyciu programu Visual Studio](./functions-bindings-register.md#local-csharp) w artykule wyzwalacze i powiązania. Wymagania pakietu NuGet dotyczące powiązań są dostępne w artykule referencyjnym dla powiązania. Na przykład Znajdź wymagania dotyczące pakietu dla wyzwalacza Event Hubs w [artykule Event Hubs powiązania](functions-bindings-event-hubs.md).

3. Jeśli istnieją ustawienia aplikacji wymagane przez powiązanie, Dodaj je do kolekcji **wartości** w [lokalnym pliku ustawień](functions-run-local.md#local-settings-file). Te wartości są używane, gdy funkcja jest uruchamiana lokalnie. Gdy funkcja jest uruchamiana w aplikacji funkcji na platformie Azure, zostaną użyte [Ustawienia aplikacji funkcji](#function-app-settings) .

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
   Połączenie z usługą queue storage jest uzyskiwane z `AzureWebJobsStorage` tego ustawienia. Aby uzyskać więcej informacji, zobacz artykuł referencyjny dla określonego powiązania. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Testowanie funkcji

Podstawowe narzędzia usługi Azure Functions umożliwiają uruchamianie projektu usługi Azure Functions na lokalnym komputerze deweloperskim. Monit o zainstalowanie tych narzędzi pojawia się przy pierwszym uruchomieniu funkcji w programie Visual Studio.

Aby przetestować funkcję, naciśnij klawisz F5. Po wyświetleniu monitu zaakceptuj żądanie programu Visual Studio dotyczące pobrania i zainstalowania podstawowych narzędzi usługi Azure Functions (CLI). Może także być konieczne włączenie wyjątku zapory, aby umożliwić narzędziom obsługę żądań HTTP.

Po uruchomieniu projektu można testować kod tak jak w przypadku testowania wdrożonej funkcji. Aby uzyskać więcej informacji, zobacz [strategie testowania kodu w Azure Functions](functions-test-a-function.md). Podczas działania w trybie debugowania punkty przerwania są trafień w programie Visual Studio zgodnie z oczekiwaniami. 

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->

Aby dowiedzieć się więcej o korzystaniu z Azure Functions Core Tools, zobacz temat [kod i testowanie usługi Azure Functions lokalnie](functions-run-local.md).

## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Podczas publikowania z programu Visual Studio są używane jedną z dwóch metod wdrażania:

* [Web Deploy](functions-deployment-technologies.md#web-deploy-msdeploy): pakiety i wdrożenia aplikacji systemu Windows na dowolnym serwerze usług IIS.
* [Wdrożenie pliku zip z włączonym uruchamianiem pakietu](functions-deployment-technologies.md#zip-deploy): zalecane w przypadku wdrożeń Azure Functions.

Wykonaj następujące kroki, aby opublikować projekt w aplikacji funkcji na platformie Azure.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Ustawienia aplikacji funkcji

Wszystkie ustawienia dodane w pliku Local. Settings. JSON należy również dodać do aplikacji funkcji na platformie Azure. Te ustawienia nie są przekazywane automatycznie przy publikowaniu projektu.

Najprostszym sposobem przekazania wymaganych ustawień do aplikacji funkcji na platformie Azure jest użycie linku **Zarządzaj ustawieniami aplikacji...** , który jest wyświetlany po pomyślnym opublikowaniu projektu.

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Spowoduje to wyświetlenie okna dialogowego **Ustawienia aplikacji** dla aplikacji funkcji, w którym można dodać nowe ustawienia aplikacji lub zmodyfikować istniejące.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Local** reprezentuje wartość ustawienia w pliku Local. Settings. JSON, a dla opcji **Remote** to bieżące ustawienie w aplikacji funkcji na platformie Azure.  Wybierz pozycję **Dodaj ustawienie** , aby utworzyć nowe ustawienie aplikacji. Użyj linku **Wstaw wartość z lokalnego** , aby skopiować wartość ustawienia do pola **zdalnego** . Oczekujące zmiany są zapisywane w pliku ustawień lokalnych i aplikacji funkcji po wybraniu **przycisku OK**.

> [!NOTE]
> Domyślnie plik Local. Settings. JSON nie jest sprawdzany w kontroli źródła. Oznacza to, że w przypadku klonowania projektu funkcji lokalnych z kontroli źródła projekt nie ma pliku Local. Settings. JSON. W takim przypadku należy ręcznie utworzyć plik Local. Settings. JSON w katalogu głównym projektu, aby okno dialogowe **Ustawienia aplikacji** działało zgodnie z oczekiwaniami. 

Można również zarządzać ustawieniami aplikacji w jeden z następujących sposobów:

* [Przy użyciu Azure Portal](functions-how-to-use-azure-function-app-settings.md#settings).
* [Użycie opcji `--publish-local-settings` Publikuj w Azure Functions Core Tools](functions-run-local.md#publish).
* [Przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

## <a name="monitoring-functions"></a>Funkcje monitorowania

Zalecanym sposobem monitorowania wykonywania funkcji jest zintegrowanie aplikacji funkcji z usługą Azure Application Insights. Po utworzeniu aplikacji funkcji w Azure Portal Ta integracja jest domyślnie wykonywana. Jednak podczas tworzenia aplikacji funkcji podczas publikowania programu Visual Studio nie jest przeprowadzana integracja w aplikacji funkcji na platformie Azure.

Aby włączyć Application Insights aplikacji funkcji:

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Aby dowiedzieć się więcej, zobacz [Monitor Azure Functions](functions-monitoring.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Azure Functions Core Tools, zobacz temat [kod i testowanie usługi Azure Functions lokalnie](functions-run-local.md).

Aby dowiedzieć się więcej na temat opracowywania funkcji jako bibliotek klas .NET, zobacz [Azure Functions C# Developer Reference](functions-dotnet-class-library.md). Ten artykuł zawiera również linki do przykładów użycia atrybutów do deklarowania różnych typów powiązań obsługiwanych przez Azure Functions.    
