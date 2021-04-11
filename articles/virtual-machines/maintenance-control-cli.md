---
title: Kontrola konserwacji maszyn wirtualnych platformy Azure przy użyciu interfejsu wiersza polecenia
description: Dowiedz się, jak kontrolować, kiedy konserwacja jest stosowana do maszyn wirtualnych platformy Azure przy użyciu funkcji sterowania konserwacją i interfejsu wiersza polecenia.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/20/2020
ms.author: cynthn
ms.openlocfilehash: 9425759de1e08bc83cac80cd1b56c602edb59fb1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562966"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-cli"></a>Sterowanie aktualizacjami przy użyciu sterowania konserwacją i interfejsu wiersza polecenia platformy Azure

Kontrola konserwacji umożliwia określenie, kiedy należy zastosować aktualizacje platformy do infrastruktury hosta izolowanych maszyn wirtualnych i hostów dedykowanych platformy Azure. W tym temacie omówiono opcje interfejsu wiersza polecenia platformy Azure służące do sterowania konserwacją. Aby uzyskać więcej informacji na temat korzyści z używania kontroli konserwacji, jej ograniczeń i innych opcji zarządzania, zobacz [Zarządzanie aktualizacjami platformy przy użyciu kontroli konserwacji](maintenance-control.md).

## <a name="create-a-maintenance-configuration"></a>Utworzenie konfiguracji konserwacji

Użyj `az maintenance configuration create` , aby utworzyć konfigurację konserwacji. W tym przykładzie zostanie utworzona konfiguracja konserwacji o nazwie Moja *config* objęta zakresem hosta. 

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

Skopiuj identyfikator konfiguracji z danych wyjściowych do użycia później.

Użycie `--maintenance-scope host` gwarantuje, że konfiguracja konserwacji służy do kontrolowania aktualizacji infrastruktury hosta.

Jeśli spróbujesz utworzyć konfigurację o tej samej nazwie, ale w innej lokalizacji, zostanie wyświetlony komunikat o błędzie. Nazwy konfiguracji muszą być unikatowe dla grupy zasobów.

Można wykonać zapytanie o dostępne konfiguracje konserwacji przy użyciu programu `az maintenance configuration list` .

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>Utwórz konfigurację konserwacji z zaplanowanym oknem
Możesz również zadeklarować zaplanowane okno, gdy platforma Azure zastosuje aktualizacje do zasobów. W tym przykładzie zostanie utworzona konfiguracja konserwacji o nazwie Moja config z zaplanowanym oknem 5 godzin w czwartym poniedziałek każdego miesiąca. Po utworzeniu zaplanowanego okna nie trzeba już ręcznie stosować aktualizacji.

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
> **Czas trwania** konserwacji nie może być dłuższy niż *2 godziny* . **Cykl** konserwacji musi być ustawiony na co najmniej raz w ciągu 35 dni.

Cykl konserwacji można wyrazić jako codziennie, co tydzień lub co miesiąc. Przykłady to:
- **codzienne**— konserwacja — powtarzanie okna — co: "dzień" **lub** "3Days"
- **cotygodniowe**— konserwacja — powtarzanie okna — co: "3Weeks" **lub** "tydzień Sobota, Niedziela"
- **miesięczne**-konserwacyjne — powtarzanie okna — co: "miesiąc day23, day24" **lub** "miesiąc ostatni Niedziela" **lub** "miesiąc czwarty poniedziałek"


## <a name="assign-the-configuration"></a>Przypisz konfigurację

Służy `az maintenance assignment create` do przypisywania konfiguracji do izolowanej maszyny wirtualnej lub dedykowanego hosta platformy Azure.

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Zastosuj konfigurację do maszyny wirtualnej przy użyciu identyfikatora konfiguracji. Określ `--resource-type virtualMachines` i podaj nazwę maszyny wirtualnej dla programu `--resource-name` oraz grupę zasobów dla maszyny wirtualnej w programie `--resource-group` oraz lokalizację maszyny wirtualnej dla programu `--location` . 

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

Aby zastosować konfigurację do dedykowanego hosta, należy dołączyć `--resource-type hosts` , `--resource-parent-name` z nazwą grupy hostów, i `--resource-parent-type hostGroups` . 

Parametr `--resource-id` jest identyfikatorem hosta. Aby uzyskać identyfikator dedykowanego hosta, można użyć [AZ VM Host Get-instance-View](/cli/azure/vm/host#az-vm-host-get-instance-view) .

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

## <a name="check-configuration"></a>Sprawdź konfigurację

Można sprawdzić, czy konfiguracja została zastosowana prawidłowo, lub sprawdzić, jaka konfiguracja jest aktualnie stosowana `az maintenance assignment list` .

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


## <a name="check-for-pending-updates"></a>Wyszukaj oczekujące aktualizacje

Użyj `az maintenance update list` , aby sprawdzić, czy istnieją oczekujące aktualizacje. Update--Subscription to identyfikator subskrypcji zawierającej maszynę wirtualną.

Jeśli nie ma żadnych aktualizacji, polecenie zwróci komunikat o błędzie, który będzie zawierać tekst: `Resource not found...StatusCode: 404` .

Jeśli są dostępne aktualizacje, zostanie zwrócony tylko jeden, nawet jeśli istnieje wiele oczekujących aktualizacji. Dane dla tej aktualizacji zostaną zwrócone w obiekcie:

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

Sprawdź, czy istnieją oczekujące aktualizacje dla izolowanej maszyny wirtualnej. W tym przykładzie dane wyjściowe są sformatowane jako tabela na potrzeby czytelności.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Dedykowany host

Aby sprawdzić oczekujące aktualizacje dla dedykowanego hosta. W tym przykładzie dane wyjściowe są sformatowane jako tabela na potrzeby czytelności. Zamień wartości dla zasobów własnymi.

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

Użyj `az maintenance apply update` , aby zastosować oczekujące aktualizacje. W przypadku powodzenia to polecenie zwróci kod JSON zawierający szczegóły aktualizacji.

### <a name="isolated-vm"></a>Izolowana maszyna wirtualna

Utwórz żądanie zastosowania aktualizacji dla izolowanej maszyny wirtualnej.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Dedykowany host

Zastosuj aktualizacje do dedykowanego hosta.

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

## <a name="check-the-status-of-applying-updates"></a>Sprawdź stan zastosowania aktualizacji 

Postęp aktualizacji można sprawdzić za pomocą programu `az maintenance applyupdate get` . 

Możesz użyć `default` jako nazwy aktualizacji, aby zobaczyć wyniki dla ostatniej aktualizacji lub zamienić na `myUpdateName` nazwę aktualizacji, która została zwrócona podczas uruchomienia `az maintenance applyupdate create` .

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
LastUpdateTime będzie czas, w którym aktualizacja została ukończona, inicjowana przez użytkownika lub przez platformę w przypadku nieużycia okna samoobsługowego. Jeśli aktualizacja nie została zastosowana przez kontrolę konserwacji, zostanie wyświetlona wartość domyślna.

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


## <a name="delete-a-maintenance-configuration"></a>Usuń konfigurację konserwacji

Służy `az maintenance configuration delete` do usuwania konfiguracji konserwacji. Usunięcie konfiguracji spowoduje usunięcie kontroli konserwacji ze skojarzonych zasobów.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --resource-name myConfig
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej, zobacz temat [konserwacja i aktualizacje](maintenance-and-updates.md).
