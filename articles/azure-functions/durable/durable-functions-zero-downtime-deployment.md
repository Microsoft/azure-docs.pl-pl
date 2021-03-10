---
title: Wdrożenie bez przestojów dla Durable Functions
description: Dowiedz się, jak włączyć aranżację Durable Functions w przypadku wdrożeń bez przestojów.
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.custom: fasttrack-edit
ms.openlocfilehash: 707d624c47c536e00e98910a8902772703733515
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558767"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Wdrożenie bez przestojów dla Durable Functions

[Model niezawodnego wykonywania](./durable-functions-orchestrations.md) Durable Functions wymaga deterministycznia aranżacji, co tworzy dodatkowe wyzwanie do uwzględnienia podczas wdrażania aktualizacji. Gdy wdrożenie zawiera zmiany sygnatury funkcji działania lub logiki programu Orchestrator, wystąpienia aranżacji w locie kończą się niepowodzeniem. Ta sytuacja dotyczy szczególnie problemów z wystąpieniami długotrwałych aranżacji, które mogą reprezentować godziny lub dni pracy.

Aby zapobiec wystąpieniu tych błędów, dostępne są dwie opcje: 
- Opóźnij wdrożenie, dopóki nie zostaną ukończone wszystkie uruchomione wystąpienia aranżacji.
- Upewnij się, że wszystkie uruchomione wystąpienia aranżacji korzystają z istniejących wersji funkcji. 

Poniższy wykres zawiera porównanie trzech głównych strategii w celu osiągnięcia wdrożenia bez przestojów dla Durable Functions: 

| Strategia |  Kiedy stosować | Zalety | Wady |
| -------- | ------------ | ---- | ---- |
| [Przechowywanie wersji](#versioning) |  Aplikacje, które nie są często spotykanymi [zmianami.](durable-functions-versioning.md) | Łatwość zaimplementowania. |  Zwiększony rozmiar aplikacji funkcji w pamięci i liczbie funkcji.<br/>Duplikowanie kodu. |
| [Sprawdzanie stanu z miejscem](#status-check-with-slot) | System, który nie ma długotrwałych aranżacji trwających dłużej niż 24 godziny lub często nakładających się aranżacji. | Prosta baza kodu.<br/>Nie wymaga dodatkowego zarządzania aplikacjami funkcji. | Wymaga dodatkowego konta magazynu lub zarządzania centrum zadań.<br/>Wymaga okresu czasu, gdy nie są uruchomione żadne aranżacje. |
| [Routing aplikacji](#application-routing) | System, który nie ma okresów czasu, gdy aranżacje nie działają, takich jak okresy czasowe z aranżacjami w ciągu ostatnich 24 godzin lub z często nakładającymi się aranżacjami. | Obsługuje nowe wersje systemów z ciągle uruchomionymi aranżacjami, które mają istotne zmiany. | Wymaga inteligentnego routera aplikacji.<br/>Można maksymalnie przekroczyć liczbę aplikacji funkcji dozwolonych przez twoją subskrypcję. Wartość domyślna to 100. |

## <a name="versioning"></a>Przechowywanie wersji

Zdefiniuj nowe wersje funkcji i pozostaw stare wersje w aplikacji funkcji. Jak widać na diagramie, wersja funkcji stanie się częścią nazwy. Ze względu na to, że poprzednie wersje funkcji są zachowywane, wystąpienia aranżacji w locie mogą nadal odwoływać się do nich. Tymczasem żądania nowych wystąpień aranżacji wywołują najnowszą wersję, którą funkcja klienta aranżacji może odwoływać się z poziomu aplikacji.

![Strategia przechowywania wersji](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

W tej strategii każda funkcja musi być kopiowana i należy zaktualizować odwołania do innych funkcji. Można to ułatwić, pisząc skrypt. Oto [przykładowy projekt](https://github.com/TsuyoshiUshio/DurableVersioning) z skryptem migracji.

>[!NOTE]
>Ta strategia używa miejsc wdrożenia, aby uniknąć przestoju podczas wdrażania. Aby uzyskać szczegółowe informacje na temat tworzenia i używania nowych miejsc wdrożenia, zobacz [Azure Functions miejsc wdrożenia](../functions-deployment-slots.md).

## <a name="status-check-with-slot"></a>Sprawdzanie stanu z miejscem

Gdy bieżąca wersja aplikacji funkcji jest uruchomiona w miejscu produkcyjnym, wdróż nową wersję aplikacji funkcji w miejscu przejściowym. Przed zamianą miejsc produkcyjnych i przejściowych Sprawdź, czy są uruchomione wystąpienia aranżacji. Po zakończeniu wszystkich wystąpień aranżacji można wykonać zamianę. Ta strategia działa w przypadku przewidywalnych okresów, gdy w locie nie ma wystąpień aranżacji. Jest to najlepsze rozwiązanie, gdy aranżacje nie są długotrwałe i kiedy wykonywanie aranżacji nie jest często nakładane.

### <a name="function-app-configuration"></a>Konfiguracja aplikacji funkcji

Aby skonfigurować ten scenariusz, należy wykonać poniższą procedurę.

1. [Dodawanie miejsc wdrożenia](../functions-deployment-slots.md#add-a-slot) do aplikacji funkcji na potrzeby przemieszczania i produkcji.

1. Dla każdego gniazda należy ustawić [ustawienie aplikacji AzureWebJobsStorage](../functions-app-settings.md#azurewebjobsstorage) na parametry połączenia dla konta magazynu udostępnionego. Te parametry połączenia konta magazynu są używane przez środowisko uruchomieniowe Azure Functions. To konto jest używane przez środowisko uruchomieniowe Azure Functions i zarządza kluczami funkcji.

1. Dla każdego miejsca Utwórz nowe ustawienie aplikacji, na przykład `DurableManagementStorage` . Ustaw jej wartość na parametry połączenia różnych kont magazynu. Te konta magazynu są używane przez rozszerzenie Durable Functions do [niezawodnego wykonywania](./durable-functions-orchestrations.md). Użyj oddzielnego konta magazynu dla każdego miejsca. Nie oznaczaj tego ustawienia jako ustawienia miejsca wdrożenia.

1. Whost.jsaplikacji funkcji w [ sekcji durableTask pliku](durable-functions-bindings.md#hostjson-settings)Określ `connectionStringName` (trwały 2. x) lub `azureStorageConnectionStringName` (trwałe 1. x) jako nazwę ustawienia aplikacji utworzonego w kroku 3.

Na poniższym diagramie przedstawiono opisaną konfigurację miejsc wdrożenia i kont magazynu. W tym potencjalnym scenariuszu preinstalacji wersja 2 aplikacji funkcji jest uruchomiona w miejscu produkcyjnym, natomiast wersja 1 pozostaje w miejscu przejściowym.

![Gniazda wdrożenia i konta magazynu](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples"></a>host.jsna przykład

Poniższe fragmenty kodu JSON to przykłady ustawienia parametrów połączenia w *host.js* pliku.

#### <a name="functions-20"></a>Funkcje 2,0

```json
{
  "version": 2.0,
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub",
      "storageProvider": {
        "connectionStringName": "DurableManagementStorage"
      }
    }
  }
}
```

#### <a name="functions-1x"></a>Functions w wersji 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "DurableManagementStorage"
  }
}
```

### <a name="cicd-pipeline-configuration"></a>Konfiguracja potoku ciągłej integracji/ciągłego wdrażania

Skonfiguruj potok ciągłej integracji/ciągłego wdrażania tylko wtedy, gdy aplikacja funkcji nie ma oczekujących lub uruchomionych wystąpień aranżacji. W przypadku korzystania z Azure Pipelines można utworzyć funkcję, która sprawdza, czy są spełnione te warunki, jak w poniższym przykładzie:

```csharp
[FunctionName("StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var result = await client.ListInstancesAsync(new OrchestrationStatusQueryCondition() { RuntimeStatus = runtimeStatus }, CancellationToken.None);
    return (ActionResult)new OkObjectResult(new { HasRunning = result.DurableOrchestrationState.Any() });
}
```

Następnie skonfiguruj bramę przemieszczania, aby czekać, aż żadna organizacja nie zostanie uruchomiona. Aby uzyskać więcej informacji, zobacz [wydawanie kontroli wdrożenia przy użyciu bram](/azure/devops/pipelines/release/approvals/gates)

![Brama wdrożenia](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Azure Pipelines sprawdza aplikację funkcji pod kątem uruchamiania wystąpień aranżacji przed rozpoczęciem wdrażania.

![Brama wdrażania (uruchomiona)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

Teraz Nowa wersja aplikacji funkcji powinna zostać wdrożona w miejscu przejściowym.

![Miejsce przejściowe](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

Na koniec zastępowanie miejsc. 

Ustawienia aplikacji, które nie są oznaczone jako ustawienia miejsca wdrożenia, są również zamieniane, więc aplikacja w wersji 2 zachowuje swoje odwołanie do konta magazynu A. Ponieważ stan aranżacji jest śledzony na koncie magazynu, wszystkie aranżacje działające w aplikacji w wersji 2 będą nadal działać w nowym gnieździe bez przeszkód.

![Miejsce wdrożenia](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

Aby użyć tego samego konta magazynu dla obu gniazd, można zmienić nazwy centrów zadań. W takim przypadku należy zarządzać stanem swoich miejsc i ustawień HubName aplikacji. Aby dowiedzieć się więcej, zobacz [centra zadań w Durable Functions](durable-functions-task-hubs.md).

## <a name="application-routing"></a>Routing aplikacji

Ta strategia jest najbardziej skomplikowana. Można go jednak używać dla aplikacji funkcji, które nie mają czasu między uruchomionymi aranżacjami.

W przypadku tej strategii należy utworzyć *router aplikacji* przed Durable Functions. Ten router można zaimplementować przy użyciu Durable Functions. Router ma odpowiedzialność za:

* Wdróż aplikację funkcji.
* Zarządzaj wersją Durable Functions. 
* Kierowanie żądań aranżacji do aplikacji funkcji.

Po pierwszym odebraniu żądania aranżacji router wykonuje następujące zadania:

1. Tworzy nową aplikację funkcji na platformie Azure.
2. Wdraża kod aplikacji funkcji w nowej aplikacji funkcji na platformie Azure.
3. Przekazuje żądanie aranżacji do nowej aplikacji.

Router zarządza stanem wdrożonej wersji kodu aplikacji na platformie Azure.

![Routing aplikacji (pierwszy raz)](media/durable-functions-zero-downtime-deployment/application-routing.png)

Router kieruje żądania wdrożenia i aranżacji do odpowiedniej aplikacji funkcji na podstawie wersji wysyłanej z żądaniem. Ignoruje wersję poprawki.

Podczas wdrażania nowej wersji aplikacji bez zmiany, można zwiększyć wersję poprawki. Router jest wdrażany w istniejącej aplikacji funkcji i wysyła żądania dla starych i nowych wersji kodu, które są kierowane do tej samej aplikacji funkcji.

![Routing aplikacji (nieprzerwana zmiana)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

Podczas wdrażania nowej wersji aplikacji z istotną zmianą można zwiększyć wersję główną lub pomocniczą. Następnie router aplikacji tworzy nową aplikację funkcji na platformie Azure, wdraża ją i przekierowuje do niej żądania nowej wersji aplikacji. Na poniższym diagramie uruchomiono aranżacje w wersji 1.0.1 aplikacji, ale żądania dotyczące wersji 1.1.0 są kierowane do nowej aplikacji funkcji.

![Routing aplikacji (zmiana istotna)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

Router monitoruje stan aranżacji w wersji 1.0.1 i usuwa aplikacje po zakończeniu wszystkich aranżacji. 

### <a name="tracking-store-settings"></a>Śledzenie ustawień magazynu

Każda aplikacja funkcji powinna używać oddzielnych kolejek planowania, prawdopodobnie w oddzielnym koncie magazynu. Jeśli chcesz wysyłać zapytania o wszystkie wystąpienia aranżacji we wszystkich wersjach aplikacji, możesz udostępniać tabele wystąpień i historii w aplikacjach funkcji. Tabele można udostępniać przez skonfigurowanie `trackingStoreConnectionStringName` `trackingStoreNamePrefix` ustawień i whost.jsw pliku [ ustawień](durable-functions-bindings.md#host-json) , aby wszystkie te same wartości były używane.

Aby uzyskać więcej informacji, zobacz [Zarządzanie wystąpieniami w Durable Functions na platformie Azure](durable-functions-instance-management.md).

![Śledzenie ustawień magazynu](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Durable Functions wersji](durable-functions-versioning.md)
