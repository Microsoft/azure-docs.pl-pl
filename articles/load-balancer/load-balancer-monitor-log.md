---
title: Monitoruj operacje, zdarzenia i liczniki dla Load Balancer publicznego w warstwie Podstawowa
titleSuffix: Azure Load Balancer
description: Dowiedz się, jak włączyć zdarzenia alertu i rejestrować stan kondycji sondy dla publicznych Load Balancer podstawowych
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 42ec5a661bd7b42ba5de5bfa99b3898291cc60fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935606"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Dzienniki usługi Azure Monitor dla usługi Load Balancer w warstwie Podstawowa

Możesz użyć różnych typów dzienników na platformie Azure do zarządzania podstawowymi modułami równoważenia obciążenia i rozwiązywania problemów. Dostęp do niektórych z tych dzienników można uzyskać za pomocą portalu. Dzienniki mogą być przesyłane strumieniowo do centrum zdarzeń lub obszaru roboczego Log Analytics. Wszystkie dzienniki można wyodrębnić z usługi Azure Blob Storage i przeglądać w różnych narzędziach, takich jak Excel i Power BI.  Więcej informacji o różnych typach dzienników można znaleźć na poniższej liście.

* **Dzienniki aktywności:** Za pomocą [widoku dzienniki aktywności można monitorować akcje dotyczące zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) , aby wyświetlić wszystkie działania przesyłane do subskrypcji platformy Azure i ich stan. Dzienniki aktywności są domyślnie włączone i można je wyświetlić w Azure Portal.
* **Dzienniki zdarzeń alertu:** Ten dziennik służy do wyświetlania alertów wygenerowanych przez moduł równoważenia obciążenia. Stan usługi równoważenia obciążenia jest zbierany co pięć minut. Ten dziennik jest zapisywana tylko w przypadku zgłoszenia zdarzenia alertu modułu równoważenia obciążenia.
* **Dzienniki sondowania kondycji:** Ten dziennik służy do wyświetlania problemów wykrytych przez sondę kondycji, takich jak liczba wystąpień w puli zaplecza, które nie otrzymują żądań z modułu równoważenia obciążenia z powodu błędów sondy kondycji. Ten dziennik jest zapisywana w przypadku zmiany stanu sondy kondycji.

> [!IMPORTANT]
> **Dzienniki zdarzeń sondy kondycji nie są obecnie funkcjonalne i są wymienione w [znanych problemach dotyczących Azure Load Balancer](whats-new.md#known-issues).** Dzienniki są dostępne tylko dla zasobów wdrożonych w modelu wdrażania Menedżer zasobów. Nie można używać dzienników dla zasobów w klasycznym modelu wdrażania. Aby uzyskać więcej informacji na temat modeli wdrażania, zobacz [opis Menedżer zasobów wdrożenia i wdrożenia klasycznego](../azure-resource-manager/management/deployment-models.md).

## <a name="enable-logging"></a>Włącz rejestrowanie

Rejestrowanie aktywności jest automatycznie włączone dla wszystkich zasobów usługi Resource Manager. Włącz rejestrowanie sondowania zdarzeń i kondycji, aby rozpocząć zbieranie danych dostępnych w tych dziennikach. Wykonaj następujące kroki, aby włączyć rejestrowanie.

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Jeśli nie masz jeszcze modułu równoważenia obciążenia, przed kontynuowaniem [Utwórz moduł równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal) .

1. W portalu kliknij pozycję **grupy zasobów**.
2. Wybierz **\<resource-group-name>** lokalizację modułu równoważenia obciążenia.
3. Wybierz swój moduł równoważenia obciążenia.
4. Wybierz **Activity log**pozycję  >  **Ustawienia diagnostyczne**dziennika aktywności.
5. W okienku **Ustawienia diagnostyki** w obszarze **Ustawienia diagnostyki**wybierz pozycję **+ Dodaj ustawienie diagnostyczne**.
6. W okienku tworzenie **ustawień diagnostycznych** wprowadź **MyLBDiagnostics** w polu **Nazwa** .
7. Dla **ustawień diagnostycznych**dostępne są trzy opcje.  Można wybrać jeden, dwa lub wszystkie trzy i skonfigurować każdy z nich w celu spełnienia wymagań:
   * **Archiwizowanie na koncie magazynu**
   * **Przesyłanie strumieniowe do centrum zdarzeń**
   * **Wysyłanie do usługi Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>Zarchiwizuj na koncie magazynu
    Konieczne jest już konto magazynu utworzone dla tego procesu.  Aby utworzyć konto magazynu, zobacz [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    1. Zaznacz pole wyboru obok pozycji **Archiwizowanie na koncie magazynu**.
    2. Wybierz pozycję **Konfiguruj** , aby otworzyć okienko **Wybierz konto magazynu** .
    3. Wybierz **subskrypcję** , w której konto magazynu zostało utworzone w polu ściągania.
    4. Wybierz nazwę konta magazynu w obszarze **konto magazynu** w polu ściąganie.
    5. Wybierz przycisk OK.

    ### <a name="stream-to-an-event-hub"></a>Przesyłaj strumieniowo do centrum zdarzeń
    Musisz już utworzyć centrum zdarzeń dla tego procesu.  Aby utworzyć centrum zdarzeń, zobacz [Szybki Start: tworzenie centrum zdarzeń przy użyciu Azure Portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

    1. Zaznacz pole wyboru obok pozycji **strumień do centrum zdarzeń**
    2. Wybierz pozycję **Konfiguruj** , aby otworzyć okienko **Wybierz centrum zdarzeń** .
    3. Wybierz **subskrypcję** , w której utworzono centrum zdarzeń w polu ściągania.
    4. **Wybierz pozycję przestrzeń nazw centrum zdarzeń** w polu ściągania.
    5. **Wybierz pozycję Nazwa zasad centrum zdarzeń** w polu ściągania.
    6. Wybierz przycisk OK.

    ### <a name="send-to-log-analytics"></a>Wysyłanie do usługi Log Analytics
    Musisz mieć już utworzony i skonfigurowany obszar roboczy usługi log Analytics dla tego procesu.  Aby utworzyć obszar roboczy Log Analytics, zobacz [tworzenie log Analytics obszaru roboczego w Azure Portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

    1. Zaznacz pole wyboru obok pozycji **Wyślij do log Analytics**.
    2. W polu rozwijanym wybierz **subskrypcję** , w której znajduje się obszar roboczy log Analytics.
    3. Wybierz **obszar roboczy log Analytics** w polu ściągania.


8. Poniżej sekcji **dziennika** w okienku **Ustawienia diagnostyczne** zaznacz pole wyboru obok obu:
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  Poniżej sekcji **Metryka** w okienku **Ustawienia diagnostyki** zaznacz pole wyboru obok pozycji:
   * **AllMetrics**

11. Sprawdź, czy wszystko wygląda poprawnie, a następnie kliknij pozycję **Zapisz** w górnej części okienka tworzenie **ustawień diagnostycznych** .

## <a name="activity-log"></a>Dziennik aktywności

Dziennik aktywności jest domyślnie generowany. Dzienniki są przechowywane przez 90 dni w magazynie dzienników zdarzeń platformy Azure. Więcej informacji na temat tych dzienników można znaleźć w artykule [Wyświetlanie dzienników aktywności w celu monitorowania akcji dotyczących zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) .

## <a name="archive-to-storage-account-logs"></a>Archiwizuj do dzienników konta magazynu

### <a name="alert-event-log"></a>Dziennik zdarzeń alertu

Ten dziennik jest generowany tylko wtedy, gdy włączono go na podstawie usługi równoważenia obciążenia. Zdarzenia są rejestrowane w formacie JSON i przechowywane na koncie magazynu określonym podczas włączania rejestrowania. Poniższy przykład jest zdarzeniem.

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

W danych wyjściowych JSON zostanie wyświetlona Właściwość *EventName* opisująca przyczynę modułu równoważenia obciążenia, który utworzył alert. W takim przypadku wygenerowany alert został spowodowany wyczerpaniem portów TCP przez limity NAT źródła IP.

### <a name="health-probe-log"></a>Dziennik sondy kondycji

Ten dziennik jest generowany tylko wtedy, gdy włączono go na podstawie usługi równoważenia obciążenia, jak opisano powyżej. Dane są przechowywane na koncie magazynu określonym podczas włączania rejestrowania. Utworzono kontener o nazwie "Insights-Logs-loadbalancerprobehealthstatus" i rejestrowane są następujące dane:

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

Dane wyjściowe JSON są wyświetlane w polu właściwości podstawowe informacje o stanie kondycji sondy. Właściwość *dipDownCount* pokazuje łączną liczbę wystąpień w zapleczu, które nie odbierają ruchu sieciowego z powodu niepowodzeń odpowiedzi sondy.

### <a name="view-and-analyze-the-activity-log"></a>Wyświetlanie i analizowanie dziennika aktywności

Dane dziennika aktywności można przeglądać i analizować przy użyciu dowolnej z następujących metod:

* **Narzędzia platformy Azure:** Pobierz informacje z dziennika aktywności za pośrednictwem Azure PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST platformy Azure lub Azure Portal. Instrukcje krok po kroku dla każdej metody opisano szczegółowo w [operacjach inspekcji w Menedżer zasobów](../azure-resource-manager/management/view-activity-logs.md) artykule.
* **Power BI:** Jeśli nie masz jeszcze konta [Power BI](https:// .microsoft.com/pricing) , możesz wypróbować go bezpłatnie. Za pomocą [pakietu zawartości dzienników inspekcji platformy Azure dla Power BI](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs)można analizować dane przy użyciu wstępnie skonfigurowanych pulpitów nawigacyjnych lub dostosować widoki zgodnie z wymaganiami.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>Wyświetlanie i analizowanie sondy kondycji i dziennika zdarzeń

Połącz się z kontem magazynu i Pobierz wpisy dziennika JSON dla dzienników sondowania zdarzeń i kondycji. Po pobraniu plików JSON można je przekonwertować na woluminy CSV i wyświetlać w programie Excel, Power BI lub dowolnym innym narzędziu do wizualizacji danych.

> [!TIP]
> Jeśli znasz program Visual Studio oraz podstawowe pojęcia dotyczące zmiany wartości stałych i zmiennych w języku C#, możesz skorzystać z [konwerterów dzienników](https://github.com/Azure-Samples/networking-dotnet-log-converter) dostępnych w witrynie GitHub.

## <a name="stream-to-an-event-hub"></a>Przesyłaj strumieniowo do centrum zdarzeń
Gdy informacje diagnostyczne są przesyłane strumieniowo do centrum zdarzeń, mogą służyć do scentralizowanej analizy dzienników w narzędziu SIEM innej firmy z integracją Azure Monitor. Aby uzyskać więcej informacji, zobacz [przesyłanie strumieniowe danych monitorowania platformy Azure do centrum zdarzeń](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Wysyłanie do usługi Log Analytics
Zasoby na platformie Azure mogą być wysyłane bezpośrednio do obszaru roboczego Log Analytics, w którym można uruchamiać złożone zapytania, aby uzyskać informacje na temat rozwiązywania problemów i analizy.  Aby uzyskać więcej informacji, zobacz [zbieranie dzienników zasobów platformy Azure w obszarze roboczym log Analytics w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace)

## <a name="next-steps"></a>Następne kroki

[Opis sond modułu równoważenia obciążenia](load-balancer-custom-probe-overview.md)
