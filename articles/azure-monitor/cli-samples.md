---
title: Przykłady interfejsu wiersza polecenia Azure Monitor
description: Przykłady poleceń interfejsu wiersza polecenia dla Azure Monitor funkcji. Azure Monitor to usługa Microsoft Azure, która umożliwia wysyłanie powiadomień o alertach, wywoływanie internetowych adresów URL na podstawie wartości skonfigurowanych danych telemetrycznych oraz automatyczne skalowanie Cloud Services, Virtual Machines i Web Apps.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/16/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8eaf8c2e140f0b323db0c20a2e9946884c51df04
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039176"
---
# <a name="azure-monitor-cli-samples"></a>Przykłady interfejsu wiersza polecenia Azure Monitor
W tym artykule przedstawiono przykładowe polecenia interfejsu wiersza polecenia (CLI) ułatwiające dostęp do funkcji Azure Monitor. Azure Monitor pozwala na automatyczne skalowanie Cloud Services, Virtual Machines i Web Apps oraz wysyłanie powiadomień o alertach lub wywoływanie adresów URL sieci Web na podstawie wartości skonfigurowanych danych telemetrycznych.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie zainstalowano jeszcze interfejsu wiersza polecenia platformy Azure, postępuj zgodnie z instrukcjami dotyczącymi [instalacji interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Możesz również użyć [Azure Cloud Shell](/azure/cloud-shell) , aby uruchomić interfejs wiersza polecenia jako interaktywny w przeglądarce. Pełne odwołanie do wszystkich dostępnych poleceń znajduje się w [dokumentacji interfejsu wiersza polecenia Azure monitor](/cli/azure/monitor). 

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Pierwszym krokiem jest zalogowanie się do konta platformy Azure.

```azurecli
az login
```

Po uruchomieniu tego polecenia należy zalogować się za pomocą instrukcji na ekranie. Wszystkie polecenia działają w kontekście domyślnej subskrypcji.

Wyświetl szczegóły bieżącej subskrypcji.

```azurecli
az account show
```

Zmień kontekst roboczy na inną subskrypcję.

```azurecli
az account set -s <Subscription ID or name>
```

Wyświetl listę wszystkich obsługiwanych poleceń Azure Monitor.

```azurecli
az monitor -h
```

## <a name="view-activity-log"></a>Wyświetl dziennik aktywności

Wyświetl listę zdarzeń dziennika aktywności.

```azurecli
az monitor activity-log list
```

Wyświetl wszystkie dostępne opcje.

```azurecli
az monitor activity-log list -h
```

Wyświetl listę dzienników według źródła.

```azurecli
az monitor activity-log list --resource-group <group name>
```

Wyświetl listę dzienników według obiektu wywołującego.

```azurecli
az monitor activity-log list --caller myname@company.com
```

Wyświetl listę dzienników według obiektu wywołującego dla typu zasobu w zakresie dat.

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Pracuj z alertami 
> [!NOTE]
> W tym momencie w interfejsie wiersza polecenia są obsługiwane tylko alerty (klasyczne). 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Pobieranie reguł alertów (klasycznych) w grupie zasobów

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Tworzenie reguły alertu metryki (klasycznej)

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Usuwanie reguły alertu (klasycznego)

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Profile dziennika

Informacje przedstawione w tej sekcji służą do pracy z profilami dzienników.

### <a name="get-a-log-profile"></a>Pobierz profil dziennika

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Dodawanie profilu dziennika z przechowywaniem

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Dodawanie profilu dziennika z przechowywaniem i EventHub

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Usuwanie profilu dziennika

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnostyka

Skorzystaj z informacji w tej sekcji, aby korzystać z ustawień diagnostycznych.

### <a name="get-a-diagnostic-setting"></a>Pobierz ustawienie diagnostyczne

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-setting"></a>Utwórz ustawienie diagnostyczne 

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <storage account ID> \
    --resource <target resource object ID> \
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

### <a name="delete-a-diagnostic-setting"></a>Usuń ustawienie diagnostyczne

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Automatyczne skalowanie

Skorzystaj z informacji w tej sekcji, aby korzystać z ustawień skalowania automatycznego. Należy zmodyfikować te przykłady.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Pobieranie ustawień automatycznego skalowania dla grupy zasobów

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Pobieranie ustawień automatycznego skalowania według nazwy w grupie zasobów

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Ustawianie ustawień skalowania automatycznego

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```
