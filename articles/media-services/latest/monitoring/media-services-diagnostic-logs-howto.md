---
title: Monitorowanie dzienników diagnostycznych za pomocą Azure Monitor
description: W tym artykule przedstawiono sposób kierowania i wyświetlania dzienników diagnostycznych za pośrednictwem Azure Monitor.
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
ms.openlocfilehash: 13dadd9665f2d66861e00d769086fb23a4dea115
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961697"
---
# <a name="monitor-media-services-diagnostic-logs"></a>Monitorowanie dzienników diagnostycznych Media Services

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

[Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview.md) umożliwia monitorowanie metryk i dzienników diagnostycznych, które ułatwiają zrozumienie sposobu działania aplikacji. Aby uzyskać szczegółowy opis tej funkcji oraz sprawdzić, dlaczego warto używać Azure Media Services metryki i dzienniki diagnostyczne, zobacz [monitorowanie Media Services metryk i dzienników diagnostycznych](monitor-media-services.md).

W tym artykule pokazano, jak skierować dane do konta magazynu, a następnie wyświetlić dane.

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz konto Media Services](../account-create-how-to.md).
- Przejrzyj  [Media Services monitora](monitor-media-services.md).

## <a name="route-data-to-the-storage-account-using-the-portal"></a>Kierowanie danych do konta magazynu przy użyciu portalu

1. Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com.
1. Przejdź do konta Media Services w programie, a następnie kliknij pozycję **Ustawienia diagnostyczne** w obszarze **monitorowanie**. Zostanie wyświetlona lista wszystkich zasobów w subskrypcji, które generują dane monitorowania za pośrednictwem usługi Azure Monitor.

    ![Zrzut ekranu, który wyróżnia ustawienia diagnostyczne w sekcji monitorowanie.](../media/media-services-diagnostic-logs/logs01.png)

1. Kliknij pozycję **Dodaj ustawienie diagnostyczne**.

   Ustawienie diagnostyczne zasobu definiuje, *jakie* dane monitorowania powinny być przekierowywane z określonego zasobu i *gdzie* powinny one trafiać.

1. W wyświetlonej sekcji podaj **nazwę** ustawienia i zaznacz pole wyboru **Zarchiwizuj na koncie magazynu**.

    Wybierz konto magazynu, do którego chcesz wysłać dzienniki, a następnie naciśnij przycisk **OK**.
1. Zaznacz wszystkie pola w obszarach **Dziennik** i **Metryka**. W zależności od typu zasobu może być widoczna tylko jedna z tych opcji. Za pomocą tych pól wyboru można wybrać kategorie danych dzienników i metryk, dostępne dla tego typu zasobu, które mają być wysyłane do wybranego miejsca docelowego — w tym przypadku konta magazynu.

   ![Sekcja ustawień diagnostycznych](../media/media-services-diagnostic-logs/logs02.png)
1. Ustaw suwak **Okres przechowywania (w dniach)** na wartość 30. Suwak umożliwia ustawienie liczby dni przechowywania danych monitorowania na koncie magazynu. Starsze dane są automatycznie usuwane przez usługę Azure Monitor. Wpisanie wartości zero powoduje, że dane są przechowywane przez nieograniczony czas.
1. Kliknij pozycję **Zapisz**.

Dane monitorowania z zasobu będą teraz przepływać do konta magazynu.

## <a name="route-data-to-the-storage-account-using-the-azure-cli"></a>Kierowanie danych do konta magazynu przy użyciu interfejsu wiersza polecenia platformy Azure

Aby włączyć magazyn dzienników diagnostycznych na koncie magazynu, uruchom następujące `az monitor diagnostic-settings` polecenie interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Na przykład:

```azurecli-interactive
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforams  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount" \
    --resource-group "amsResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="view-data-in-the-storage-account-using-the-portal"></a>Wyświetlanie danych na koncie magazynu przy użyciu portalu

Jeśli wykonano powyższe kroki, dane zaczęły już przepływać do konta magazynu.

Może upłynąć nawet pięć minut, zanim zdarzenie pojawi się na koncie magazynu.

1. W portalu przejdź do sekcji **Konta magazynu** widocznej na pasku nawigacyjnym po lewej stronie.
1. Znajdź konto magazynu utworzone w poprzedniej sekcji i kliknij je.
1. Kliknij **obiekty blob**, a następnie na kontenerze z etykietą **Insights-Logs-keydeliveryrequests**. Jest to kontener, w którym znajdują się Twoje dzienniki. Dane monitorowania są podzielone na kontenery według identyfikatora zasobu, a następnie według daty i godziny.
1. Przejdź do pliku PT1H.json, klikając identyfikator zasobu, datę i godzinę w obrębie kontenerów. Kliknij plik PT1H.json, a następnie kliknij przycisk **Pobierz**.

 Możesz teraz wyświetlić zdarzenie JSON przechowywane na koncie magazynu.

### <a name="examples-of-pt1hjson"></a>Przykłady PT1H.jsna

#### <a name="clear-key-delivery-log"></a>Wyczyść dziennik dostarczania klucza

```json
{
  "time": "2019-05-21T00:07:33.2820450Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 253,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "e4276e1d-c012-40b1-80d0-ac15808b9277",
      "nbf": "1558396914",
      "exp": "1558400814",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "fb5c2b3a-bffa-4434-9c6f-73d689649add",
    "keyType": "Clear",
    "keyId": "e4276e1d-c012-40b1-80d0-ac15808b9277",
    "policyName": "SharedContentKeyPolicyUsedByAllAssets",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

#### <a name="widevine-encrypted-key-delivery-log"></a>Dziennik dostarczania zaszyfrowanego klucza Widevine

```json
{
  "time": "2019-05-20T23:15:22.7088747Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 69,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
      "nbf": "1558392430",
      "exp": "1558396330",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "49613dd2-16aa-4595-a6e0-4e68beae6d37",
    "keyType": "Widevine",
    "keyId": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
    "policyName": "DRMContentKeyPolicy",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="see-also"></a>Zobacz też

* [Metryki usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/data-platform.md)
* [Azure Monitor dzienników diagnostycznych](https://docs.microsoft.com/azure/azure-monitor/essentials/platform-logs-overview.md)
* [Jak zbierać i zużywać dane dzienników z zasobów platformy Azure](https://docs.microsoft.com/azure/azure-monitor/essentials/platform-logs-overview.md)

## <a name="next-steps"></a>Następne kroki

[Monitorowanie metryk](media-services-metrics-howto.md)
