---
title: Sterowanie konserwacją maszyn wirtualnych platformy Azure przy użyciu interfejsu wiersza polecenia
description: Dowiedz się, jak kontrolować, kiedy konserwacja jest stosowana do maszyn wirtualnych platformy Azure przy użyciu kontrolki konserwacji i interfejsu wiersza polecenia.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/20/2020
ms.author: cynthn
ms.openlocfilehash: c57f66eca5d15024c6b10e8fad12ddb575b9f894
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765903"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-cli"></a>Kontrolowanie aktualizacji za pomocą kontrolki konserwacji i interfejsu wiersza polecenia platformy Azure

Kontrola konserwacji umożliwia podjęcie decyzji o tym, kiedy zastosować aktualizacje platformy do hostowania infrastruktury izolowanych maszyn wirtualnych i dedykowanych hostów platformy Azure. W tym temacie omykamy opcje interfejsu wiersza polecenia platformy Azure dotyczące kontroli konserwacji. Aby uzyskać więcej informacji o korzyściach z używania kontroli konserwacji, jej ograniczeniach i innych opcjach zarządzania, zobacz Zarządzanie aktualizacjami platformy [za pomocą kontrolki konserwacji.](maintenance-control.md)

## <a name="create-a-maintenance-configuration"></a>Utworzenie konfiguracji konserwacji

Użyj `az maintenance configuration create` , aby utworzyć konfigurację konserwacji. Ten przykład tworzy konfigurację konserwacji o nazwie *myConfig* w zakresie hosta. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host\
   --location eastus
```

Skopiuj identyfikator konfiguracji z danych wyjściowych do późniejszego użycia.

Użycie `--maintenance-scope host` opcji zapewnia, że konfiguracja konserwacji jest używana do kontrolowania aktualizacji infrastruktury hosta.

Jeśli spróbujesz utworzyć konfigurację o tej samej nazwie, ale w innej lokalizacji, wystąpi błąd. Nazwy konfiguracji muszą być unikatowe dla grupy zasobów.

Zapytania dotyczące dostępnych konfiguracji konserwacji można uzyskać przy użyciu programu `az maintenance configuration list` .

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>Tworzenie konfiguracji konserwacji za pomocą zaplanowanego okna
Możesz również zadeklarować zaplanowane okno, w którym platforma Azure zastosuje aktualizacje do zasobów. Ten przykład tworzy konfigurację konserwacji o nazwie myConfig z zaplanowanym oknem 5 godzin w czwarty poniedziałek każdego miesiąca. Po utworzeniu zaplanowanego okna nie trzeba już ręcznie stosować aktualizacji.

```azurecli-interactive
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host \
   --location eastus \
   --maintenance-window-duration "05:00" \
   --maintenance-window-recur-every "Month Fourth Monday" \
   --maintenance-window-start-date-time "2020-12-30 08:00" \
   --maintenance-window-time-zone "Pacific Standard Time"
```

> [!IMPORTANT]
> Czas **trwania** konserwacji musi być *co najmniej 2* godziny. Cykl **konserwacji** musi być ustawiony na co najmniej raz w ciągu 35 dni.

Cykl konserwacji może być wyrażony jako codziennie, co tydzień lub co miesiąc. Przykłady to:
- **daily**— maintenance-window-recur-every: "Day" **lub** "3Days"
- **weekly**- maintenance-window-recur-every: "3Weeks" **lub** "Week Saturday,Sunday"
- **monthly**- maintenance-window-recur-every: "Month day23,day24" **or** "Month Last Sunday" **or** "Month Fourth Monday"


## <a name="assign-the-configuration"></a>Przypisywanie konfiguracji

Użyj `az maintenance assignment create` , aby przypisać konfigurację do izolowanej maszyny wirtualnej lub Azure Dedicated Host.

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Zastosuj konfigurację do maszyny wirtualnej przy użyciu identyfikatora konfiguracji. Określ i podaj nazwę maszyny wirtualnej dla programu oraz grupę zasobów dla maszyny wirtualnej w programie oraz lokalizację maszyny `--resource-type virtualMachines` `--resource-name` `--resource-group` wirtualnej dla programu `--location` . 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>Dedykowany host

Aby zastosować konfigurację do dedykowanego hosta, należy dołączyć , `--resource-type hosts` `--resource-parent-name` z nazwą grupy hostów i `--resource-parent-type hostGroups` . 

Parametr `--resource-id` jest identyfikatorem hosta. Aby uzyskać identyfikator dedykowanego hosta, możesz użyć az [vm host get-instance-view.](/cli/azure/vm/host#az_vm_host_get_instance_view)

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>Sprawdzanie konfiguracji

Możesz sprawdzić, czy konfiguracja została zastosowana poprawnie, lub sprawdzić, jaka konfiguracja jest obecnie stosowana przy użyciu programu `az maintenance assignment list` .

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>Dedykowany host 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>Sprawdzanie oczekujących aktualizacji

Użyj `az maintenance update list` , aby sprawdzić, czy istnieją oczekujące aktualizacje. Zaktualizuj --subscription, aby był identyfikatorem subskrypcji zawierającej maszynę wirtualną.

Jeśli nie ma żadnych aktualizacji, polecenie zwróci komunikat o błędzie, który będzie zawierać tekst: `Resource not found...StatusCode: 404` .

Jeśli istnieją aktualizacje, zostanie zwrócona tylko jedna, nawet jeśli istnieje wiele oczekujących aktualizacji. Dane dla tej aktualizacji zostaną zwrócone w obiekcie :

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Sprawdź oczekujące aktualizacje izolowanej maszyny wirtualnej. W tym przykładzie dane wyjściowe są formatowane jako tabela w celu ich czytelności.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Dedykowany host

Aby sprawdzić oczekujące aktualizacje dla dedykowanego hosta. W tym przykładzie dane wyjściowe są formatowane jako tabela w celu ich czytelności. Zastąp wartości zasobów własnymi.

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>Stosowanie aktualizacji

Użyj `az maintenance apply update` , aby zastosować oczekujące aktualizacje. W przypadku powodzenia to polecenie zwróci dane JSON zawierające szczegóły aktualizacji.

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Utwórz żądanie zastosowania aktualizacji do izolowanej maszyny wirtualnej.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Dedykowany host

Stosowanie aktualizacji na dedykowanym hoście.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>Sprawdzanie stanu stosowania aktualizacji 

Możesz sprawdzić postęp aktualizacji przy użyciu narzędzia `az maintenance applyupdate get` . 

Możesz użyć nazwy aktualizacji, aby wyświetlić wyniki ostatniej aktualizacji, lub zastąpić nazwą aktualizacji zwróconą podczas `default` `myUpdateName` korzystania z programu `az maintenance applyupdate create` .

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime będzie godziną ukończenia aktualizacji zainicjowaną przez Użytkownika lub przez platformę w przypadku, gdy nie zostało użyte okno samodzielnej konserwacji. Jeśli aktualizacja nie została nigdy zastosowana za pośrednictwem kontroli konserwacji, zostanie pokazana wartość domyślna.

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>Dedykowany host

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>Usuwanie konfiguracji konserwacji

Użyj `az maintenance configuration delete` , aby usunąć konfigurację konserwacji. Usunięcie konfiguracji powoduje usunięcie kontrolki konserwacji ze skojarzonych zasobów.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --resource-name myConfig
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej, zobacz [Konserwacja i aktualizacje.](maintenance-and-updates.md)
