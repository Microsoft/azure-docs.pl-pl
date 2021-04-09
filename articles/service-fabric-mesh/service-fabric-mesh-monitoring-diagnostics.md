---
title: Monitorowanie i Diagnostyka w aplikacjach Service Fabric siatki platformy Azure
description: Dowiedz się więcej o monitorowaniu i diagnozowaniu aplikacji w Service Fabric siatki na platformie Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 02de8ea5dd5c53192d2b8c7beba8bc36143beac6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626998"
---
# <a name="monitoring-and-diagnostics"></a>Monitorowanie i diagnostyka

> [!IMPORTANT]
> Wersja zapoznawcza siatki Service Fabric platformy Azure została wycofana. Nowe wdrożenia nie będą już dozwolone za pomocą interfejsu API Service Fabric siatki. Obsługa istniejących wdrożeń będzie kontynuowana do 28 kwietnia 2021.
> 
> Aby uzyskać szczegółowe informacje, zobacz wycofywanie w [wersji zapoznawczej usługi Azure Service Fabric siatki](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Usługa Azure Service Fabric Mesh to w pełni zarządzana usługa, która pozwala deweloperom na wdrażanie aplikacji mikrousług bez zarządzania maszynami wirtualnymi, magazynem i siecią. Monitorowanie i Diagnostyka dla Service Fabric siatki jest podzielone na trzy główne typy danych diagnostycznych:

- Dzienniki aplikacji — są one definiowane jako dzienniki z aplikacji w kontenerze, w zależności od tego, jak dysponujesz Instrumentacją aplikacji (np. dzienniki platformy Docker)
- Zdarzenia platformy — zdarzenia z platformy siatki odpowiednie dla operacji kontenera, obecnie w tym aktywowanie kontenera, dezaktywację i zakończenie.
- Metryki kontenera — wykorzystanie zasobów i metryki wydajności dla kontenerów (statystyki platformy Docker)

W tym artykule omówiono opcje monitorowania i diagnostyki dostępne w najnowszej wersji zapoznawczej.

## <a name="application-logs"></a>Dzienniki aplikacji

Możesz wyświetlić dzienniki platformy Docker ze wdrożonych kontenerów na podstawie każdego kontenera. W modelu aplikacji Service Fabric siatki każdy kontener jest pakietem kodu w aplikacji. Aby wyświetlić skojarzone dzienniki z pakietem kodu, użyj następującego polecenia:

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Aby uzyskać nazwę repliki, można użyć polecenia "AZ siatk Service-Replica". Nazwy replik zwiększają liczbę całkowitą z zakresu od 0.

Oto, co jest podobne do wyświetlania dzienników z kontenera VotingWeb. Code z aplikacji do głosowania:

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Metryki kontenera 

Środowisko siatki przedstawia kilku metryki wskazujące, jak działają kontenery. Następujące metryki są dostępne za pośrednictwem interfejsu wiersza polecenia Azure Portal i Azure Monitor:

| Metric | Opis | Lekcji|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu jako wartość procentowa | % |
| MemoryUtilization | ActualMem/AllocatedMem jako wartość procentowa | % |
| AllocatedCpu | Procesor przydzielony do Azure Resource Manager szablonu | Millicores |
| AllocatedMemory | Pamięć przydzieloną zgodnie z szablonem Azure Resource Manager | MB |
| ActualCpu | Użycie procesora | Millicores |
| ActualMemory | Użycie pamięci | MB |
| ContainerStatus | 0 — nieprawidłowe: stan kontenera jest nieznany <br> 1 — oczekujące: zaplanowano uruchomienie kontenera <br> 2 — rozpoczęcie: trwa proces uruchamiania kontenera <br> 3 — rozpoczęto: kontener został pomyślnie uruchomiony <br> 4 — zatrzymywanie: Trwa przerywanie kontenera <br> 5 — zatrzymano: kontener został zatrzymany pomyślnie | Nie dotyczy |
| ApplicationStatus | 0-nieznany: stan nie umożliwia pobierania <br> 1 — gotowe: aplikacja działa prawidłowo <br> 2 — uaktualnianie: trwa uaktualnianie <br> 3 — Tworzenie: aplikacja jest tworzona <br> 4 — Usuwanie: aplikacja jest usuwana <br> 5 — niepowodzenie: wdrażanie aplikacji nie powiodło się | Nie dotyczy |
| Stan servicestatus | 0-nieprawidłowa: usługa nie ma obecnie stanu kondycji <br> 1 — OK: usługa jest w dobrej kondycji  <br> 2-ostrzeżenie: być może wystąpił problem z nieprawidłowym wymaganiem <br> 3 — błąd: Wystąpił problem, który wymaga badania <br> 4-nieznany: stan nie umożliwia pobierania | Nie dotyczy |
| ServiceReplicaStatus | 0-nieprawidłowa: replika nie ma obecnie stanu kondycji <br> 1 — OK: usługa jest w dobrej kondycji  <br> 2-ostrzeżenie: być może wystąpił problem z nieprawidłowym wymaganiem <br> 3 — błąd: Wystąpił problem, który wymaga badania <br> 4-nieznany: stan nie umożliwia pobierania | Nie dotyczy | 
| RestartCount | Liczba ponownych uruchomień kontenera | Nie dotyczy |

> [!NOTE]
> Wartości servicestatus i ServiceReplicaStatus są takie same jak dla [atrybutu HealthState](/dotnet/api/system.fabric.health.healthstate) w Service Fabric.

Każda Metryka jest dostępna w różnych wymiarach, dzięki czemu można zobaczyć zagregowane na różnych poziomach. Bieżąca lista wymiarów jest następująca:

* ApplicationName
* ServiceName
* Plik servicereplicaname
* CodePackageName

> [!NOTE]
> Wymiar CodePackageName nie jest dostępny dla aplikacji systemu Linux. 

Każdy wymiar odpowiada różnym składnikom [modelu aplikacji Service Fabric](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Interfejs wiersza polecenia Azure Monitor

Pełna lista poleceń jest dostępna w dokumentacji [interfejsu wiersza polecenia Azure monitor](/cli/azure/monitor/metrics#az-monitor-metrics-list) , ale zawarto kilka przydatnych przykładów poniżej 

W każdym przykładzie identyfikator zasobu jest zgodny z tym wzorcem

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Użycie procesora CPU przez kontenery w aplikacji

```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Użycie pamięci dla każdej repliki usługi
```azurecli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Ponowne uruchamianie dla każdego kontenera w oknie 1 godziny 
```azurecli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Średnie użycie procesora CPU przez usługi o nazwie "VotingWeb" w oknie 1 godziny
```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Eksplorator metryk

Eksplorator metryk jest blokiem w portalu, w którym można wizualizować wszystkie metryki dla aplikacji siatki. Ten blok jest dostępny na stronie aplikacji w portalu i w bloku usługi Azure monitor, który służy do wyświetlania metryk dla wszystkich zasobów platformy Azure, które obsługują Azure Monitor. 

![Eksplorator metryk](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, przeczytaj [omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md).
* Aby dowiedzieć się więcej na temat poleceń metryk Azure Monitor, zapoznaj się z dokumentacją [interfejsu wiersza polecenia Azure monitor](/cli/azure/monitor/metrics#az-monitor-metrics-list).
