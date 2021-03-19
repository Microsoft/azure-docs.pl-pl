---
title: Wyświetl metryki z Azure Monitor
description: W tym artykule przedstawiono sposób monitorowania metryk przy użyciu wykresów Azure Portal i interfejsu wiersza polecenia platformy Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/17/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 306381465919d3fde7135315b69d450a496c2898
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609732"
---
# <a name="monitor-media-services-metrics"></a>Monitorowanie metryk usług Media Services

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

[Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview.md) umożliwia monitorowanie metryk i dzienników diagnostycznych, które ułatwiają zrozumienie sposobu działania aplikacji. Aby uzyskać szczegółowy opis tej funkcji i zrozumieć, dlaczego należy używać Azure Media Services metryk i dzienników diagnostycznych, zobacz [monitorowanie Media Services metryk i dzienników diagnostycznych](monitor-media-services-data-reference.md).

Azure Monitor oferuje kilka sposobów współpracy z metrykami, w tym wykresów w portalu, uzyskiwania do nich dostępu za pośrednictwem interfejsu API REST lub wykonywania zapytań przy użyciu wiersza polecenia platformy Azure. W tym artykule przedstawiono sposób monitorowania metryk przy użyciu wykresów Azure Portal i interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- [Tworzenie konta usługi Media Services](../create-account-howto.md)
- Przegląd  [metryk Media Services monitora i dzienników diagnostycznych](monitor-media-services-data-reference.md)

## <a name="view-metrics-in-azure-portal"></a>Wyświetlanie metryk w Azure Portal

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
1. Przejdź do konta Azure Media Services i wybierz pozycję **metryki**.
1. Kliknij pole **zakres** i wybierz zasób, który chcesz monitorować.

    Po prawej stronie zostanie wyświetlone okno **Wybierz zakres** z listą dostępnych zasobów. W takim przypadku zobaczysz:

    * &lt;Nazwa konta Media Services&gt;
    * &lt;Nazwa konta programu Media Services nazwa &gt; / &lt; punktu końcowego przesyłania strumieniowego&gt;
    * &lt;Nazwa konta magazynu&gt;

    Odfiltruj, a następnie wybierz zasób i naciśnij przycisk **Zastosuj**. Aby uzyskać szczegółowe informacje o obsługiwanych zasobach i metrykach, zobacz [monitorowanie Media Services metryki](monitor-media-services-data-reference.md).

    > [!NOTE]
    > Aby przełączać się między zasobami, które chcesz monitorować, kliknij ponownie pole **źródłowe** i powtórz ten krok.

1. Opcjonalne: nadaj wykresowi nazwę (Edytuj nazwę, naciskając ołówek u góry).
1. Dodaj metryki, które chcesz wyświetlić.
1. Możesz przypiąć wykres do pulpitu nawigacyjnego.

## <a name="view-metrics-with-azure-cli"></a>Wyświetlanie metryk przy użyciu interfejsu wiersza polecenia platformy Azure

Aby uzyskać metryki "wychodzące" przy użyciu interfejsu wiersza polecenia platformy Azure, uruchom następujące `az monitor metrics` polecenie:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Aby uzyskać inne metryki, Zastąp ciąg "wychodzące" dla nazwy metryki, która Cię interesuje.

## <a name="see-also"></a>Zobacz też

- [Metryki usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/data-platform.md)
- [Twórz i wyświetlaj alerty metryk i zarządzaj nimi za pomocą Azure monitor](https://docs.microsoft.com/azure/azure-monitor/alerts/alerts-metric.md).

## <a name="next-steps"></a>Następne kroki

[Dzienniki diagnostyczne](../media-services-diagnostic-logs-howto.md)
