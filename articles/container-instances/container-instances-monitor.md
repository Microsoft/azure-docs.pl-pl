---
title: Monitorowanie wystąpień kontenerów
description: Jak monitorować użycie zasobów obliczeniowych, takich jak procesor CPU i pamięć, w Azure Container Instances.
ms.topic: article
ms.date: 04/24/2019
ms.openlocfilehash: b10c370b599233d00b2b4a65268f6c61a11cbd5c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007260"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Monitorowanie zasobów kontenerów w usłudze Azure Container Instances

[Azure monitor][azure-monitoring] zapewnia wgląd w zasoby obliczeniowe używane przez wystąpienia kontenerów. Te dane użycia zasobów ułatwiają określenie najlepszych ustawień zasobów dla grup kontenerów. Azure Monitor udostępnia również metryki, które śledzą aktywność sieci w wystąpieniach kontenerów.

Ten dokument zawiera szczegółowe informacje o zbieraniu Azure Monitor metryk dla wystąpień kontenerów za pomocą Azure Portal i interfejsu wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Azure Monitor metryki w Azure Container Instances są obecnie dostępne w wersji zapoznawczej, a niektóre [ograniczenia mają zastosowanie](#preview-limitations). Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

W tej chwili Azure Monitor metryki są dostępne tylko dla kontenerów systemu Linux.

## <a name="available-metrics"></a>Dostępne metryki

Azure Monitor udostępnia następujące [metryki dla Azure Container Instances][supported-metrics]. Te metryki są dostępne dla grupy kontenerów i poszczególnych kontenerów. Domyślnie metryki są agregowane jako średnie.

* **Użycie procesora CPU** — mierzone w **millicores**. Jeden millicore to 1/1000th rdzeń procesora CPU, więc 500 millicores reprezentuje użycie procesora CPU 0,5.

* **Użycie pamięci** — w bajtach.

* **Bajty odebrane przez sieć na sekundę** i **bajty przesyłane przez sieć na sekundę**. 

## <a name="get-metrics---azure-portal"></a>Pobieranie metryk — Azure Portal

Po utworzeniu grupy kontenerów dane usługi Azure Monitor są dostępne w witrynie Azure Portal. Aby wyświetlić metryki dla grupy kontenerów, przejdź do strony **Przegląd** dla grupy kontenerów. W tym miejscu można wyświetlić wstępnie utworzone wykresy dla każdej z dostępnych metryk.

![podwójny wykres][dual-chart]

W grupie kontenerów zawierającej wiele kontenerów Użyj [wymiaru][monitor-dimension] , aby przedstawić metryki według kontenera. Aby utworzyć wykres z metrykami pojedynczego kontenera, wykonaj następujące kroki:

1. Na stronie **Przegląd** wybierz jeden z wykresów metryk, takich jak **procesor CPU**. 
1. Wybierz przycisk **Zastosuj podział** , a następnie wybierz pozycję **nazwa kontenera**.

![Przechwytywanie ekranu pokazuje metryki wystąpienia kontenera z wybraną opcją Zastosuj podział i nazwę kontenera.][dimension]

## <a name="get-metrics---azure-cli"></a>Pobieranie metryk — interfejs wiersza polecenia platformy Azure

Metryki wystąpień kontenerów można również zbierać przy użyciu interfejsu wiersza polecenia platformy Azure. Najpierw pobierz identyfikator grupy kontenerów za pomocą następującego polecenia. Zastąp element `<resource-group>` nazwą grupy zasobów, a element `<container-group>` zastąp nazwą grupy kontenerów.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Użyj następującego polecenia, aby pobrać metryki użycia **procesora**.

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table
```

```output
Timestamp            Name       Average
-------------------  ---------  ---------
2019-04-23 22:59:00  CPU Usage
2019-04-23 23:00:00  CPU Usage
2019-04-23 23:01:00  CPU Usage  0.0
2019-04-23 23:02:00  CPU Usage  0.0
2019-04-23 23:03:00  CPU Usage  0.5
2019-04-23 23:04:00  CPU Usage  0.5
2019-04-23 23:05:00  CPU Usage  0.5
2019-04-23 23:06:00  CPU Usage  1.0
2019-04-23 23:07:00  CPU Usage  0.5
2019-04-23 23:08:00  CPU Usage  0.5
2019-04-23 23:09:00  CPU Usage  1.0
2019-04-23 23:10:00  CPU Usage  0.5
```

Zmień wartość `--metric` parametru w poleceniu, aby uzyskać inne [obsługiwane metryki][supported-metrics]. Na przykład użyj poniższego polecenia, aby uzyskać metryki użycia **pamięci** . 

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table
```

```output
Timestamp            Name          Average
-------------------  ------------  ----------
2019-04-23 22:59:00  Memory Usage
2019-04-23 23:00:00  Memory Usage
2019-04-23 23:01:00  Memory Usage  0.0
2019-04-23 23:02:00  Memory Usage  8859648.0
2019-04-23 23:03:00  Memory Usage  9181184.0
2019-04-23 23:04:00  Memory Usage  9580544.0
2019-04-23 23:05:00  Memory Usage  10280960.0
2019-04-23 23:06:00  Memory Usage  7815168.0
2019-04-23 23:07:00  Memory Usage  7739392.0
2019-04-23 23:08:00  Memory Usage  8212480.0
2019-04-23 23:09:00  Memory Usage  8159232.0
2019-04-23 23:10:00  Memory Usage  8093696.0
```

Dla grupy wielokontenera `containerName` można dodać wymiar do metryk zwracanych dla kontenera.

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table
```

```output
Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2019-04-23 22:59:00  Memory Usage  aci-tutorial-app
2019-04-23 23:00:00  Memory Usage  aci-tutorial-app
2019-04-23 23:01:00  Memory Usage  aci-tutorial-app      0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-app      16834560.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-app      17534976.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-app      18329600.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-app      19742720.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-app      14786560.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-app      14651392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-app      15470592.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-app      15450112.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-app      15339520.0
2019-04-23 22:59:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:00:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:01:00  Memory Usage  aci-tutorial-sidecar  0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-sidecar  884736.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-sidecar  831488.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-sidecar  819200.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-sidecar  843776.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-sidecar  954368.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-sidecar  868352.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-sidecar  847872.0
```

## <a name="next-steps"></a>Następne kroki

Zobacz [Omówienie monitorowania na platformie Azure][azure-monitoring], aby dowiedzieć się więcej na temat monitorowania na platformie Azure.

Dowiedz się, jak tworzyć [alerty metryk][metric-alert] , aby otrzymywać powiadomienia, gdy metryka Azure Container Instances przekracza próg.

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-monitoring]: ../azure-monitor/overview.md
[metric-alert]: ..//azure-monitor/platform/alerts-metric.md
[monitor-dimension]: ../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics
[supported-metrics]: ../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups
