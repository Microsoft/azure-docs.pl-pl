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
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: ab89c222648a66ad7451f9bb47e254c55b925630
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590752"
---
# <a name="monitor-media-services-metrics"></a>Monitorowanie metryk usług Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[Azure monitor](../../azure-monitor/overview.md) umożliwia monitorowanie metryk i dzienników diagnostycznych, które ułatwiają zrozumienie sposobu działania aplikacji. Aby uzyskać szczegółowy opis tej funkcji i zrozumieć, dlaczego należy używać Azure Media Services metryk i dzienników diagnostycznych, zobacz [monitorowanie Media Services metryk i dzienników diagnostycznych](media-services-metrics-diagnostic-logs.md).

Azure Monitor oferuje kilka sposobów współpracy z metrykami, w tym wykresów w portalu, uzyskiwania do nich dostępu za pośrednictwem interfejsu API REST lub wykonywania zapytań przy użyciu wiersza polecenia platformy Azure. W tym artykule przedstawiono sposób monitorowania metryk przy użyciu wykresów Azure Portal i interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- [Tworzenie konta usługi Media Services](./create-account-howto.md)
- Przegląd  [metryk Media Services monitora i dzienników diagnostycznych](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Wyświetlanie metryk w Azure Portal

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
1. Przejdź do konta Azure Media Services i wybierz pozycję **metryki**.
1. Kliknij pole **zakres** i wybierz zasób, który chcesz monitorować.

    Po prawej stronie zostanie wyświetlone okno **Wybierz zakres** z listą dostępnych zasobów. W takim przypadku zobaczysz:

    * &lt;Nazwa konta Media Services&gt;
    * &lt;Nazwa konta programu Media Services nazwa &gt; / &lt; punktu końcowego przesyłania strumieniowego&gt;
    * &lt;Nazwa konta magazynu&gt;

    Odfiltruj, a następnie wybierz zasób i naciśnij przycisk **Zastosuj**. Aby uzyskać szczegółowe informacje o obsługiwanych zasobach i metrykach, zobacz [monitorowanie Media Services metryki](media-services-metrics-diagnostic-logs.md).

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

- [Metryki usługi Azure Monitor](../../azure-monitor/data-platform.md)
- [Twórz i wyświetlaj alerty metryk i zarządzaj nimi za pomocą Azure monitor](../../azure-monitor/alerts/alerts-metric.md).

## <a name="next-steps"></a>Następne kroki

[Dzienniki diagnostyczne](media-services-diagnostic-logs-howto.md)
