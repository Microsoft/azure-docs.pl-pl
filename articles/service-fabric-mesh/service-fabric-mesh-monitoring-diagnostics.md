---
title: Monitorowanie i diagnostyka w Azure Service Fabric Mesh aplikacji
description: Dowiedz się więcej o monitorowaniu i diagnozowaniu aplikacji w Service Fabric Mesh na platformie Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: d8859293b4853cbfa8c3b3dd0e7d1bfe4f75fc40
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766173"
---
# <a name="monitoring-and-diagnostics"></a>Monitorowanie i diagnostyka

> [!IMPORTANT]
> Wersja zapoznawcza Azure Service Fabric Mesh została wycofana. Nowe wdrożenia nie będą już dozwolone za pośrednictwem interfejsu API Service Fabric Mesh API. Obsługa istniejących wdrożeń będzie kontynuowana do 28 kwietnia 2021 r.
> 
> Aby uzyskać szczegółowe informacje, zobacz [Azure Service Fabric Mesh wersji zapoznawczej wycofanie](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Usługa Azure Service Fabric Mesh to w pełni zarządzana usługa, która pozwala deweloperom na wdrażanie aplikacji mikrousług bez zarządzania maszynami wirtualnymi, magazynem i siecią. Monitorowanie i diagnostyka Service Fabric Mesh są podzielone na trzy główne typy danych diagnostycznych:

- Dzienniki aplikacji — są one definiowane jako dzienniki z konteneryzowanych aplikacji na podstawie sposobu instrumentizacji aplikacji (np. dzienników platformy Docker)
- Zdarzenia platformy — zdarzenia z platformy usługi Mesh dotyczące operacji kontenera, w tym obecnie aktywacja kontenera, dezaktywacja i zakończenie.
- Metryki kontenerów — metryki wykorzystania zasobów i wydajności dla kontenerów (statystyki platformy Docker)

W tym artykule omówiono opcje monitorowania i diagnostyki dla najnowszej dostępnej wersji zapoznawczej.

## <a name="application-logs"></a>Dzienniki aplikacji

Dzienniki platformy Docker można wyświetlać z wdrożonych kontenerów, dla każdego kontenera. W modelu Service Fabric Mesh każdy kontener jest pakietem kodu w aplikacji. Aby wyświetlić skojarzone dzienniki z pakietem kodu, użyj następującego polecenia:

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Aby uzyskać nazwę repliki, możesz użyć polecenia "az mesh service-replica". Nazwy replik inkrementują liczby całkowite z 0.

Oto jak to wygląda, aby zobaczyć dzienniki z kontenera VotingWeb.Code z aplikacji do głosowania:

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Metryki kontenerów 

Środowisko mesh uwidacznia kilka metryk wskazujących sposób wykonywania kontenerów. Następujące metryki są dostępne za pośrednictwem interfejsu wiersza Azure Portal i interfejsu wiersza polecenia usługi Azure Monitor:

| Metric | Opis | Lekcji|
|----|----|----|
| Użycie procesora CPU | ActualCpu/AllocatedCpu jako wartość procentowa | % |
| Użycie pamięci | ActualMem/AllocatedMem jako wartość procentowa | % |
| AllocatedCpu | Procesor przydzielony zgodnie z szablonem Azure Resource Manager szablonu | Millicores |
| AllocatedMemory | Pamięć przydzielona zgodnie z szablonem Azure Resource Manager szablonu | MB |
| ActualCpu | Użycie procesora | Millicores |
| ActualMemory | Użycie pamięci | MB |
| ContainerStatus | 0 — nieprawidłowy: stan kontenera jest nieznany <br> 1 — Oczekiwanie: zaplanowano uruchomienie kontenera <br> 2 — Uruchamianie: trwa uruchamianie kontenera <br> 3 — Rozpoczęto: Kontener został pomyślnie uruchomiony <br> 4 — Zatrzymywanie: kontener jest zatrzymywany <br> 5 — Zatrzymany: kontener został pomyślnie zatrzymany | Nie dotyczy |
| ApplicationStatus | 0 — nieznany: nie można pobierania stanu <br> 1 — Gotowe: aplikacja działa pomyślnie <br> 2 — Uaktualnianie: trwa uaktualnianie <br> 3 — Tworzenie: aplikacja jest tworzona <br> 4 — Usuwanie: aplikacja jest usuwana <br> 5 — Niepowodzenie: nie można wdrożyć aplikacji | Nie dotyczy |
| ServiceStatus | 0 — nieprawidłowa: usługa nie ma obecnie stanu kondycji <br> 1 — OK: Usługa jest w dobrej kondycji  <br> 2 — Ostrzeżenie: Może wystąpić problem podczas badania <br> 3 — Błąd: Wystąpił problem, który wymaga badania <br> 4 — Nieznany: nie można pobierania stanu | Nie dotyczy |
| ServiceReplicaStatus | 0 — nieprawidłowe: replika nie ma obecnie stanu kondycji <br> 1 — OK: Usługa jest w dobrej kondycji  <br> 2 — Ostrzeżenie: Może wystąpić problem podczas badania <br> 3 — Błąd: Wystąpił problem, który wymaga badania <br> 4 — Nieznany: nie można pobierania stanu | Nie dotyczy | 
| RestartCount | Liczba ponownego uruchomienia kontenera | Nie dotyczy |

> [!NOTE]
> Wartości ServiceStatus i ServiceReplicaStatus są takie same jak [HealthState](/dotnet/api/system.fabric.health.healthstate) w Service Fabric.

Każda metryka jest dostępna w różnych wymiarach, dzięki czemu można zobaczyć agregacje na różnych poziomach. Bieżąca lista wymiarów jest następująca:

* ApplicationName
* ServiceName
* ServiceReplicaName (Nazwa usługi)
* CodePackageName

> [!NOTE]
> Wymiar CodePackageName nie jest dostępny dla aplikacji systemu Linux. 

Każdy wymiar odpowiada różnym składnikom modelu [Service Fabric Application](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Azure Monitor wiersza polecenia

Pełna lista poleceń jest dostępna w Azure Monitor wiersza [polecenia,](/cli/azure/monitor/metrics#az_monitor_metrics_list) ale poniżej przedstawiono kilka przydatnych przykładów 

W każdym przykładzie identyfikator zasobu jest zgodny z tym wzorcem

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Wykorzystanie procesora CPU kontenerów w aplikacji

```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Wykorzystanie pamięci dla każdej repliki usługi
```azurecli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Ponowne uruchomienia dla każdego kontenera w 1-godzinnym oknie 
```azurecli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Średnie wykorzystanie procesora CPU w usługach o nazwie "VotingWeb" w 1-godzinnym oknie
```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Eksplorator metryk

Eksplorator metryk to blok w portalu, w którym można zwizualizować wszystkie metryki dla aplikacji usługi Mesh. Ten blok jest dostępny na stronie aplikacji w portalu i w bloku Azure Monitor, z którego można używać do wyświetlania metryk dla wszystkich zasobów platformy Azure, które obsługują Azure Monitor. 

![Eksplorator metryk](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, przeczytaj [omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md).
* Aby dowiedzieć się więcej o poleceniach metryk Azure Monitor, zapoznaj się z Azure Monitor [cli (Interfejs wiersza polecenia).](/cli/azure/monitor/metrics#az_monitor_metrics_list)
