---
title: Porównanie bieżącej wersji usługi Azure Data Factory z wersją 1 usługi Data Factory
description: Ten artykuł zawiera porównanie bieżącej wersji usługi Azure Data Factory z wersją 1 usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: kromerm
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: overview
ms.date: 04/09/2018
ms.author: makromer
ms.openlocfilehash: c6a46f6c8a57b681f66bb98fced17bf0e2464fcd
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638248"
---
# <a name="compare-azure-data-factory-with-data-factory-version-1"></a>Porównanie bieżącej wersji usługi Azure Data Factory z wersją 1 usługi Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ten artykuł zawiera porównanie bieżącej wersji usługi Data Factory z wersją 1 usługi Data Factory. Aby zapoznać się z wprowadzeniem do bieżącej wersji usługi Data Factory, zobacz [Wprowadzenie do usługi Data Factory](introduction.md). Aby zapoznać się z wprowadzeniem do wersji 1 usługi Data Factory, zobacz [Wprowadzenie do usługi Azure Data Factory](v1/data-factory-introduction.md). 

## <a name="feature-comparison"></a>Porównanie funkcji
Poniższa tabela zawiera porównanie funkcji bieżącej wersji usługi Data Factory oraz funkcji wersji 1 usługi Data Factory. 

| Cechy | Wersja 1 | Bieżąca wersja | 
| ------- | --------- | --------- | 
| Zestawy danych | Nazwany widok danych odwołujący się do danych, które mają być używane w działaniach jako dane wejściowe lub wyjściowe. Zestawy danych identyfikują dane w różnych magazynach danych, takich jak tabele, pliki, foldery i dokumenty. Na przykład zestaw danych obiektów blob platformy Azure określa kontener obiektów blob i folder w usłudze Azure Blob Storage, z których działanie ma odczytywać dane.<br/><br/>**Dostępność** definiuje model tworzenia wycinków okien przetwarzania dla zestawu danych (na przykład co godzinę, codziennie itd.). | Zestawy danych są takie same w bieżącej wersji. Nie trzeba jednak definiować harmonogramów **dostępności** dla zestawów danych. Można zdefiniować zasób wyzwalający, który może planować potoki z paradygmatu harmonogramu zegarowego. Aby uzyskać więcej informacji, zobacz [Triggers](concepts-pipeline-execution-triggers.md#trigger-execution) (Wyzwalacze) i [Datasets](concepts-datasets-linked-services.md) (Zestawy danych). | 
| Połączone usługi | Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji dla usługi Data Factory, które są niezbędne do nawiązywania połączeń z zasobami zewnętrznymi. | Połączone usługi są takie same jak w usłudze Data Factory w wersji 1, ale z nową właściwością **connectVia** do korzystania ze środowiska obliczeniowego Integration Runtime bieżącej wersji usługi Data Factory. Aby uzyskać więcej informacji, zobacz [Infrastruktura Integration Runtime w usłudze Azure Data Factory](concepts-integration-runtime.md) i [Linked service properties for Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) (Właściwości usługi połączonej dla usługi Azure Blob Storage). |
| Potoki | Fabryka danych może obejmować jeden lub wiele potoków. Potok jest logicznym grupowaniem działań, które wspólnie wykonują zadanie. Do planowania i uruchamiania potoków są używane parametry startTime, endTime i isPaused. | Potoki są grupami działań wykonywanych na danych. Jednak planowanie działań w potoku zostało rozdzielone na nowe zasoby wyzwalające. Potoki w bieżącej wersji usługi Data Factory można traktować raczej jako „jednostki przepływu pracy”, które są planowane oddzielnie za pośrednictwem wyzwalaczy. <br/><br/>W bieżącej wersji usługi Data Factory potoki nie mają „okien” czasu wykonywania. Koncepcje startTime, endTime i isPaused z wersji 1 usługi Data Factory nie są już dostępne w bieżącej wersji usługi Data Factory. Aby uzyskać więcej informacji, zobacz [Wyzwalacze i wykonywanie potoku](concepts-pipeline-execution-triggers.md) oraz [Potoki i działania](concepts-pipelines-activities.md). |
| Działania | Działania definiują akcje do wykonania na danych w potoku. Obsługiwane są działania przenoszenia danych (działanie kopiowania) i przekształcania danych (takie jak Hive, Pig i MapReduce). | W bieżącej wersji Data Factory działania nadal są zdefiniowane w ramach potoku. Bieżąca wersja Data Factory wprowadza nowe [działania przepływu sterowania](concepts-pipelines-activities.md#control-flow-activities). Te działania są używane w przepływie sterowania (zapętlanie i rozgałęzianie). Działania przenoszenia i przekształcania danych, które były obsługiwane w wersji 1, są obsługiwane w bieżącej wersji. W bieżącej wersji można zdefiniować działania przekształcania bez używania zestawów danych. |
| Hybrydowe przenoszenie danych i wysyłanie działania | Teraz nazywana środowiskiem Integration Runtime, [brama zarządzania danymi](v1/data-factory-data-management-gateway.md) obsługiwała przenoszenie danych między środowiskiem lokalnym i chmurą.| Brama zarządzania danymi jest teraz nazywana środowiskiem Integration Runtime (Self-hosted). Zapewnia te same funkcje, co w wersji 1. <br/><br/> Infrastruktura Azure-SSIS Integration Runtime w bieżącej wersji usługi Data Factory obsługuje również wdrażanie i uruchamianie pakietów usług SQL Server Integration Services (SSIS) w chmurze. Aby uzyskać więcej informacji, zobacz [Infrastruktura Integration Runtime w usłudze Azure Data Factory](concepts-integration-runtime.md).|
| Parametry | Nie dotyczy | Parametry to pary klucz-wartość ustawień konfiguracji tylko do odczytu, które są zdefiniowane w potokach. Argumenty dla parametrów można przekazywać podczas ręcznego uruchamiania potoku. Jeśli używasz wyzwalacza harmonogramu, wyzwalacz też może przekazywać wartości parametrów. Działania w ramach potoku wykorzystują wartości parametrów.  |
| Wyrażenia | Usługa Data Factory V1 pozwala używać funkcji i zmiennych systemowych w zapytaniach wyboru danych i właściwościach działania/zestawu danych. | W bieżącej wersji usługi Data Factory można używać wyrażeń w dowolnym miejscu w wartości ciągu JSON. Aby uzyskać więcej informacji, zobacz [Expressions and functions in the current version of Data Factory](control-flow-expression-language-functions.md) (Wyrażenia i funkcje w bieżącej wersji usługi Data Factory).|
| Uruchomienia potoków | Nie dotyczy | Pojedyncze wystąpienie wykonania potoku. Załóżmy na przykład istnienie potoku, który jest wykonywany o 8:00, 9:00 i 10:00. W takim przypadku występują trzy osobne uruchomienia potoku. Każde uruchomienie potoku ma unikatowy identyfikator uruchomienia potoku. Każdy identyfikator uruchomienia potoku jest identyfikatorem GUID, który w sposób unikatowy definiuje to konkretne uruchomienie potoku. Uruchomienia potoku są tworzone zazwyczaj przez przekazanie argumentów do parametrów, które są definiowane w potokach. |
| Uruchomienia działania | Nie dotyczy | Wystąpienie wykonania działania w potoku. | 
| Uruchomienia wyzwalacza | Nie dotyczy | Wystąpienie wykonania wyzwalacza. Aby uzyskać więcej informacji, zobacz [Triggers](concepts-pipeline-execution-triggers.md) (Wyzwalacze). |
| Planowanie | Planowanie zależy od godzin rozpoczęcia/zakończenia potoku i dostępności zestawu danych. | Wyzwalacz harmonogramu lub wykonywanie przez zewnętrzny harmonogram. Aby uzyskać więcej informacji, zobacz [Wyzwalacze i wykonywanie potoku](concepts-pipeline-execution-triggers.md). |

Następujące sekcje zawierają więcej informacji na temat funkcji bieżącej wersji. 

## <a name="control-flow"></a>Przepływ sterowania  
W celu zapewnienia obsługi różnych przepływów i wzorców integracji w nowoczesnym magazynie danych w bieżącej wersji usługi Data Factory wprowadzono nowy, elastyczny model potoku danych, który nie jest już powiązany z danymi szeregów czasowych. Udostępniono kilka typowych przepływów, które wcześniej nie były możliwe. Zostały one opisane w poniższych sekcjach.   

### <a name="chaining-activities"></a>Tworzenie łańcuchów działań
W wersji 1 trzeba było skonfigurować dane wyjściowe działania jako dane wejściowe kolejnego działania, aby połączyć je w łańcuch. W bieżącej wersji możesz łączyć działania w sekwencję w ramach potoku. Możesz użyć właściwości **dependsOn** w definicji działania, aby połączyć ją z działaniem nadrzędnym. Więcej informacji i przykład możesz znaleźć w artykułach [Potoki i działania](concepts-pipelines-activities.md#multiple-activities-in-a-pipeline) oraz [Rozgałęzianie działań i tworzenie łańcuchów działań](tutorial-control-flow.md). 

### <a name="branching-activities"></a>Rozgałęzianie działań
W bieżącej wersji można rozgałęziać działania w potoku. Działanie [If-condition](control-flow-if-condition-activity.md) pełni taką samą rolę, co instrukcja `if` w językach programowania. Powoduje ono obliczenie zestawu działań, gdy warunek zostanie obliczony na wartość `true`, oraz innego zestawu działań, gdy warunek zostanie obliczony na wartość `false`. Aby poznać przykłady rozgałęziania działań, zobacz samouczek [Rozgałęzianie działań i tworzenie łańcuchów działań](tutorial-control-flow.md).

### <a name="parameters"></a>Parametry 
Parametry można definiować na poziomie potoku, a argumenty przekazywać w trakcie wywoływania potoku na żądanie lub przy użyciu wyzwalacza. Działania mogą wykorzystywać argumenty przekazywane do potoku. Aby uzyskać więcej informacji, zobacz [Pipelines and triggers](concepts-pipeline-execution-triggers.md) (Potoki i wyzwalacze). 

### <a name="custom-state-passing"></a>Przekazywanie stanów niestandardowych
Dane wyjściowe działania, w tym jego stan, mogą być wykorzystywane przez kolejne działania w potoku. Na przykład w definicji JSON działania można uzyskać dostęp do danych wyjściowych poprzedniego działania przy użyciu następującej składni: `@activity('NameofPreviousActivity').output.value`. Przy użyciu tej funkcji można tworzyć przepływy pracy, w których wartości mogą być przekazywane przez działania.

### <a name="looping-containers"></a>Tworzenie pętli kontenerów
[Działanie ForEach](control-flow-for-each-activity.md) definiuje powtarzający się przepływ sterowania w potoku. To działanie iteruje po kolekcji i uruchamia określone działania w pętli. Implementacja pętli tego działania przypomina strukturę pętli Foreach w językach programowania. 

Działanie [Until](control-flow-until-activity.md) udostępnia te same funkcje, co struktura pętli do-until w językach programowania. Służy do uruchamiania zestawu działań w pętli do momentu, gdy warunek skojarzony z działaniem zostanie obliczony na wartość `true`. W usłudze Data Factory można określić wartość limitu czasu działania Until.  

### <a name="trigger-based-flows"></a>Przepływy na podstawie wyzwalaczy
Potoki mogą być wywoływane na żądanie (na podstawie zdarzenia, np. opublikowania obiektu blob) lub zgodnie z zegarem. Artykuł dotyczący [potoków i wyzwalaczy](concepts-pipeline-execution-triggers.md) zawiera szczegółowe informacje o wyzwalaczach. 

### <a name="invoking-a-pipeline-from-another-pipeline"></a>Wywoływanie potoku z poziomu innego potoku
[Działanie Execute Pipeline](control-flow-execute-pipeline-activity.md) umożliwia potokowi usługi Data Factory wywoływanie innego potoku.

### <a name="delta-flows"></a>Przepływy delta
Przypadek użycia klucza we wzorcach ETL to „ładowania delta”, w których ładowane są tylko te dane, które zmieniły się od czasu ostatniej iteracji potoku. Nowe funkcje w bieżącej wersji, takie jak [działanie wyszukiwania](control-flow-lookup-activity.md), elastyczne harmonogramy i przepływ sterowania, w naturalny sposób umożliwiają ten przypadek użycia. Aby uzyskać samouczek z instrukcjami krok po kroku, zobacz [Samouczek: kopia przyrostowa](tutorial-incremental-copy-powershell.md).

### <a name="other-control-flow-activities"></a>Inne działania przepływu sterowania
Poniżej przedstawiono kilka kolejnych działań przepływu sterowania, które są obsługiwane w bieżącej wersji usługi Data Factory. 

Działanie sterowania | Opis
---------------- | -----------
[Działanie ForEach](control-flow-for-each-activity.md) | Definiuje powtarzający się przepływ sterowania w potoku. To działanie służy do wykonywania iteracji po kolekcji i uruchamia określone działania w pętli. Implementacja pętli tego działania przypomina strukturę pętli Foreach w językach programowania.
[Aktywność sieci Web](control-flow-web-activity.md) | Wywołuje niestandardowy punkt końcowy REST z potoku usługi Data Factory. Można przekazywać zestawy danych i połączone usługi do zużycia i dostępu przez działanie. 
[Działanie Lookup](control-flow-lookup-activity.md) | Odczytuje lub wyszukuje wartość nazwy rekordu lub tabeli z dowolnego źródła zewnętrznego. Do tych danych wyjściowych mogą także odwoływać się kolejne działania. 
[Działanie pobierania metadanych](control-flow-get-metadata-activity.md) | Pobiera metadane dowolnych danych z usługi Azure Data Factory. 
[Działanie oczekiwania](control-flow-wait-activity.md) | Wstrzymuje potok na określony przedział czasu.

## <a name="deploy-ssis-packages-to-azure"></a>Wdrażanie pakietów usług SSIS na platformie Azure 
Użyj usług Azure-SSIS, jeśli chcesz przenieść obciążenia usług SSIS do chmury, utworzyć fabrykę danych przy użyciu bieżącej wersji usługi i zainicjować środowisko Azure-SSIS Integration Runtime.

Azure-SSIS Integration Runtime to w pełni zarządzany klaster maszyn wirtualnych platformy Azure (węzłów), których przeznaczeniem jest uruchamianie pakietów SSIS w chmurze. Po zainicjowaniu środowiska Azure-SSIS Integration Runtime możesz użyć tych samych narzędzi, których używano do wdrażania pakietów SSIS w lokalnym środowisku SSIS. 

Na przykład możesz użyć programu SQL Server Data Tools lub SQL Server Management Studio do wdrożenia pakietów usług SSIS w tym środowisku uruchomieniowym na platformie Azure. Instrukcje krok po kroku znajdują się w samouczku [wdrażanie pakietów usług Integration services SQL Server na platformie Azure](./tutorial-deploy-ssis-packages-azure.md). 

## <a name="flexible-scheduling"></a>Elastyczne harmonogramy
W bieżącej wersji usługi Data Factory nie trzeba definiować harmonogramów dostępności zestawu danych. Można zdefiniować zasób wyzwalający, który może planować potoki z paradygmatu harmonogramu zegarowego. Parametry można też przekazać do potoków z wyzwalacza dla modelu elastycznego harmonogramu i wykonywania. 

W bieżącej wersji usługi Data Factory potoki nie mają „okien” czasu wykonywania. Koncepcje startTime, endTime i isPaused z wersji 1 usługi Data Factory nie istnieją w bieżącej wersji usługi Data Factory. Aby uzyskać więcej informacji o sposobie tworzenia, a następnie planowania potoku w bieżącej wersji usługi Data Factory, zobacz [Wyzwalacze i wykonywanie potoku](concepts-pipeline-execution-triggers.md).

## <a name="support-for-more-data-stores"></a>Obsługa większej liczby magazynów danych
Bieżąca wersja obsługuje kopiowanie danych do i z większej liczby magazynów danych niż wersja 1. Listę obsługiwanych magazynów danych zawierają następujące artykuły:

- [Wersja 1 — obsługiwane magazyny danych](v1/data-factory-data-movement-activities.md#supported-data-stores-and-formats)
- [Bieżąca wersja — obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="support-for-on-demand-spark-cluster"></a>Obsługa klastra platformy Spark na żądanie
Bieżąca wersja obsługuje tworzenie klastra platformy Spark na żądanie w usłudze Azure HDInsight. Aby utworzyć klaster platformy Spark na żądanie, określ typ klastra Spark w definicji usługi połączonej HDInsight na żądanie. Następnie możesz skonfigurować działanie platformy Spark w potoku, aby używać tej usługi połączonej. 

W czasie wykonywania, gdy jest wykonywane działanie, usługa Data Factory automatycznie tworzy klaster platformy Spark. Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Spark Activity in the current version of Data Factory (Działanie platformy Spark w bieżącej wersji usługi Data Factory)](transform-data-using-spark.md)
- [Połączona usługa Azure HDInsight na żądanie](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)

## <a name="custom-activities"></a>Działania niestandardowe
W wersji 1 kod działania DotNet (niestandardowy) jest wdrażany przez tworzenie projektu biblioteki klas .NET w klasie, która implementuje metodę Execute interfejsu IDotNetActivity. Dlatego kod niestandardowy należy napisać w języku .NET Framework 4.5.2 i uruchomić w węzłach puli usługi Azure Batch opartych na systemie Windows. 

W działaniu niestandardowym w bieżącej wersji nie trzeba implementować interfejsu .NET. Można uruchamiać bezpośrednio polecenia i skrypty oraz własny kod niestandardowy skompilowany jako wykonywalny. 

Aby uzyskać więcej informacji, zobacz [Difference between custom activity in Data Factory and version 1 (Różnice między działaniami niestandardowymi w bieżącej wersji usługi Data Factory i wersji 1)](transform-data-using-dotnet-custom-activity.md#compare-v2-v1).

## <a name="sdks"></a>Zestawy SDK
 Bieżąca wersja usługi Data Factory udostępnia bogatszy zestaw środowisk SDK, których można używać do tworzenia i monitorowania potoków oraz zarządzania nimi.

- **.NET SDK** : zestaw .NET SDK został zaktualizowany w bieżącej wersji.

- **PowerShell** : polecenia cmdlet programu PowerShell zostały zaktualizowane w bieżącej wersji. Polecenia cmdlet dla bieżącej wersji mają **nazwie datafactoryv2** w nazwie, na przykład: Get-AzDataFactoryV2. 

- **Python SDK** : ten zestaw SDK jest nowym składnikiem w bieżącej wersji.

- **REST API** : interfejs API REST został zaktualizowany w bieżącej wersji. 

Zestawy SDK, które zostały zaktualizowane w bieżącej wersji, nie są zgodne wstecz z klientami w wersji 1. 

## <a name="authoring-experience"></a>Środowisko tworzenia

| | Wersja 2 | Wersja 1 |
| ------ | -- | -- | 
| **Witryna Azure Portal** | [Tak](quickstart-create-data-factory-portal.md) | Nie |
| **Azure PowerShell** | [Tak](quickstart-create-data-factory-powershell.md) | [Tak](./v1/data-factory-build-your-first-pipeline-using-powershell.md) |
| **Zestaw SDK platformy .NET** | [Tak](quickstart-create-data-factory-dot-net.md) | [Tak](./v1/data-factory-build-your-first-pipeline-using-vs.md) |
| **Interfejs API REST** | [Tak](quickstart-create-data-factory-rest-api.md) | [Tak](./v1/data-factory-build-your-first-pipeline-using-rest-api.md) |
| **Zestaw SDK dla języka Python** | [Tak](quickstart-create-data-factory-python.md) | Nie |
| **Szablon usługi Resource Manager** | [Tak](quickstart-create-data-factory-resource-manager-template.md) | [Tak](./v1/data-factory-build-your-first-pipeline-using-arm.md) | 

## <a name="roles-and-permissions"></a>Role i uprawnienia

Roli współautora usługi Data Factory w wersji 1 nie można użyć do tworzenia zasobów bieżącej wersji usługi Data Factory i zarządzania nimi. Aby uzyskać więcej informacji, zobacz [Współautor Data Factory](../role-based-access-control/built-in-roles.md#data-factory-contributor).

## <a name="monitoring-experience"></a>Środowisko monitorowania
W bieżącej wersji można także monitorować fabryki danych za pomocą usługi [Azure Monitor](monitor-using-azure-monitor.md). Nowe polecenia cmdlet programu PowerShell obsługują monitorowanie [środowisk Integration Runtime](monitor-integration-runtime.md). Zarówno wersja 1, jak i wersja 2, obsługują monitorowanie wizualne za pośrednictwem aplikacji monitorowania, którą można uruchamiać z witryny Azure Portal.


## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak utworzyć fabrykę danych, wykonując instrukcje krok po kroku w następujących przewodnikach szybki start: [PowerShell](quickstart-create-data-factory-powershell.md), [.NET](quickstart-create-data-factory-dot-net.md), [Python](quickstart-create-data-factory-python.md), [interfejs API REST](quickstart-create-data-factory-rest-api.md).