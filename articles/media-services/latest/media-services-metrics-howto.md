---
title: Wyświetl metryki z Azure Monitor
description: W tym artykule przedstawiono sposób monitorowania metryk przy użyciu wykresów Azure Portal i interfejsu wiersza polecenia platformy Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: c230e1e950bb924631032940642a6202acf4ade8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80382940"
---
# <a name="monitor-media-services-metrics"></a>Monitorowanie metryk usług Media Services

[Azure monitor](../../azure-monitor/overview.md) umożliwia monitorowanie metryk i dzienników diagnostycznych, które ułatwiają zrozumienie sposobu działania aplikacji. Aby uzyskać szczegółowy opis tej funkcji oraz sprawdzić, dlaczego warto używać Azure Media Services metryki i dzienniki diagnostyczne, zobacz [monitorowanie Media Services metryk i dzienników diagnostycznych](media-services-metrics-diagnostic-logs.md).

Azure Monitor oferuje kilka sposobów współpracy z metrykami, w tym wykresów w portalu, uzyskiwania do nich dostępu za pośrednictwem interfejsu API REST lub wykonywania zapytań przy użyciu wiersza polecenia platformy Azure. W tym artykule przedstawiono sposób monitorowania metryk przy użyciu wykresów Azure Portal i interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- [Tworzenie konta usługi Media Services](create-account-cli-how-to.md)
- Przegląd [metryk Media Services monitora i dzienników diagnostycznych](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Wyświetlanie metryk w Azure Portal

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
1. Przejdź do konta Azure Media Services i wybierz pozycję **metryki**.
1. Kliknij pole **zasób** i wybierz zasób, dla którego chcesz monitorować metryki.

    Po prawej stronie zostanie wyświetlone okno **Wybierz zasób** z listą dostępnych zasobów. W takim przypadku zobaczysz:

    * &lt;Nazwa konta Media Services&gt;
    * &lt;Nazwa konta&gt;/&lt;programu Media Services nazwa punktu końcowego przesyłania strumieniowego&gt;
    * &lt;Nazwa konta magazynu&gt;

    Wybierz zasób i naciśnij przycisk **Zastosuj**. Aby uzyskać szczegółowe informacje o obsługiwanych zasobach i metrykach, zobacz [monitorowanie Media Services metryki](media-services-metrics-diagnostic-logs.md).

    ![Metryki](media/media-services-metrics/metrics02.png)

    > [!NOTE]
    > Aby przełączać się między zasobami, dla których chcesz monitorować metryki, kliknij ponownie pole **zasobu** i powtórz ten krok.
1. (Opcjonalnie) Nadaj wykresowi nazwę (Edytuj nazwę, naciskając ołówek u góry).
1. Dodaj metryki, które chcesz wyświetlić.

    ![Metryki](media/media-services-metrics/metrics03.png)
1. Możesz przypiąć wykres do pulpitu nawigacyjnego.

## <a name="view-metrics-with-azure-cli"></a>Wyświetlanie metryk przy użyciu interfejsu wiersza polecenia platformy Azure

Aby uzyskać metryki "wychodzące" przy użyciu interfejsu wiersza polecenia platformy Azure `az monitor metrics` , uruchom następujące polecenie:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Aby uzyskać inne metryki, Zastąp ciąg "wychodzące" dla nazwy metryki, która Cię interesuje.

## <a name="see-also"></a>Zobacz także

* [Metryki Azure Monitor](../../azure-monitor/platform/data-platform.md)
* [Twórz i wyświetlaj alerty metryk i zarządzaj nimi za pomocą Azure monitor](../../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Następne kroki

[Dzienniki diagnostyczne](media-services-diagnostic-logs-howto.md)
