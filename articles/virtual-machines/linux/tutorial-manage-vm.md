---
title: Samouczek — tworzenie maszyn wirtualnych z systemem Linux i zarządzanie nimi za pomocą interfejsu wiersza polecenia platformy Azure
description: Z tego samouczka dowiesz się, jak tworzyć maszyny wirtualne z systemem Linux i zarządzać nimi za pomocą interfejsu wiersza polecenia platformy Azure
services: virtual-machines
documentationcenter: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.date: 03/23/2018
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: fb70f11e8815b078bccd4ed1cd294c961904579d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816606"
---
# <a name="tutorial-create-and-manage-linux-vms-with-the-azure-cli"></a>Samouczek: tworzenie maszyn wirtualnych z systemem Linux i zarządzanie nimi za pomocą interfejsu wiersza polecenia platformy Azure

Maszyny wirtualne platformy Azure oferują w pełni konfigurowalne i elastyczne środowiska obliczeniowe. W tym samouczku opisano podstawowe elementy wdrożenia maszyny wirtualnej platformy Azure, takie jak wybieranie rozmiaru i obrazu maszyny wirtualnej oraz wdrażanie maszyny wirtualnej. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej i łączenie z nią
> * Wybieranie i używanie obrazów maszyn wirtualnych
> * Wyświetlanie i używanie określonych rozmiarów maszyn wirtualnych
> * Zmienianie rozmiaru maszyny wirtualnej
> * Wyświetlanie stanu maszyny wirtualnej i jego omówienie

W tym samouczku używany jest interfejs wiersza [polecenia Azure Cloud Shell](../../cloud-shell/overview.md), który jest stale aktualizowany do najnowszej wersji. Aby otworzyć Cloud Shell, wybierz pozycję **Wypróbuj** w górnej części bloku kodu.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). 

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Grupę zasobów należy utworzyć przed maszyną wirtualną. W tym przykładzie grupa zasobów o nazwie *myResourceGroupVM* zostanie utworzona w regionie *eastus*. 

```azurecli-interactive
az group create --name myResourceGroupVM --location eastus
```

Grupa zasobów jest określana podczas tworzenia lub modyfikowania maszyn wirtualnych, co zostało przedstawione w tym samouczku.

## <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). 

Podczas tworzenia maszyny wirtualnej masz dostęp do kilku opcji, takich jak obraz systemu operacyjnego, ustalanie rozmiaru dysku i poświadczenia administracyjne. W poniższym przykładzie zostanie utworzona maszyna wirtualna o nazwie *myVM*, na której działa system Ubuntu Server. Na maszynie wirtualnej zostanie utworzone konto użytkownika o nazwie *azureuser* i zostaną wygenerowane klucze SSH, jeśli nie istnieją w domyślnej lokalizacji kluczy (*~/.ssh*):

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Tworzenie maszyny wirtualnej może potrwać kilka minut. Po utworzeniu maszyny wirtualnej interfejs wiersza polecenia platformy Azure wyświetla informacje o maszynie wirtualnej. Zanotuj adres `publicIpAddress`; przy użyciu tego adresu można uzyskać dostęp do maszyny wirtualnej. 

```output
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM"
}
```

## <a name="connect-to-vm"></a>Łączenie z maszyną wirtualną

Możesz teraz nawiązać połączenie z maszyną wirtualną przy użyciu protokołu SSH w usłudze Azure Cloud Shell lub z poziomu komputera lokalnego. Zastąp przykładowy adres IP adresem `publicIpAddress` zanotowanym w poprzednim kroku.

```bash
ssh azureuser@52.174.34.95
```

Po zalogowaniu się do maszyny wirtualnej możesz zainstalować i skonfigurować aplikacje. Gdy skończysz, zamknij sesję SSH jak zwykle:

```bash
exit
```

## <a name="understand-vm-images"></a>Omówienie obrazów maszyny wirtualnej

Witryna Azure Marketplace zawiera wiele obrazów, za pomocą których można tworzyć maszyny wirtualne. W poprzednich krokach utworzono maszynę wirtualną przy użyciu obrazu systemu Ubuntu. W tym kroku interfejs wiersza polecenia platformy Azure jest używany do wyszukania w portalu Marketplace obrazu systemu CentOS, za pomocą którego zostanie wdrożona druga maszyna wirtualna. 

Aby wyświetlić listę najczęściej używanych obrazów, użyj polecenia [az vm image list](/cli/azure/vm/image).

```azurecli-interactive 
az vm image list --output table
```

W danych wyjściowych tego polecenia wyświetlana jest lista najpopularniejszych obrazów maszyn wirtualnych na platformie Azure.

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

Pełną listę można wyświetlić, dodając argument `--all`. Listę obrazów można także filtrować za pomocą argumentów `--publisher` lub `–-offer`. W tym przykładzie lista jest filtrowana dla wszystkich obrazów z ofertą zgodną z systemem *CentOS*. 

```azurecli-interactive 
az vm image list --offer CentOS --all --output table
```

Częściowe dane wyjściowe:

```output
Offer             Publisher         Sku   Urn                                     Version
----------------  ----------------  ----  --------------------------------------  -----------
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201501         6.5.201501
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201503         6.5.201503
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201506         6.5.201506
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20150904       6.5.20150904
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20160309       6.5.20160309
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20170207       6.5.20170207
```

W celu wdrożenia maszyny wirtualnej przy użyciu wybranego obrazu zanotuj wartość z kolumny *Urn*, która składa się z wydawcy, oferty, jednostki SKU i opcjonalnie numeru wersji, aby [zidentyfikować](cli-ps-findimage.md#terminology) obraz. Podczas określania obrazu numer wersji można zastąpić wartością „latest”, co spowoduje wybranie najnowszej wersji dystrybucji. W tym przykładzie argument `--image` służy do określenia najnowszej wersji obrazu systemu CentOS 6.5.  

```azurecli-interactive
az vm create --resource-group myResourceGroupVM --name myVM2 --image OpenLogic:CentOS:6.5:latest --generate-ssh-keys
```

## <a name="understand-vm-sizes"></a>Omówienie rozmiarów maszyn wirtualnych

Rozmiar maszyny wirtualnej określa ilość zasobów obliczeniowych, takich jak procesor CPU, procesor GPU i pamięć, które są dostępne dla maszyny wirtualnej. Maszyny wirtualne muszą mieć rozmiary stosowne do oczekiwanych obciążeń. Jeśli obciążenie zwiększy się, można zmienić rozmiar istniejącej maszyny wirtualnej.

### <a name="vm-sizes"></a>Rozmiary maszyn wirtualnych

W poniższej tabeli przedstawiono kategorie rozmiarów podzielone według przypadków użycia.  

| Typ                     | Typowe rozmiary           |    Opis       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Ogólnego przeznaczenia](../sizes-general.md)         |B, Dsv3, Dv3, DSv2, Dv2, Av2, DC| Zrównoważona moc procesora CPU w stosunku do pamięci. Opcja idealna w przypadku tworzenia i testowania, małych i średnich aplikacji oraz rozwiązań dotyczących danych.  |
| [Optymalizacja pod kątem obliczeń](../sizes-compute.md)   | Fsv2          | Duża moc procesora CPU w stosunku do pamięci. Opcja dobra w przypadku aplikacji o średnim ruchu, urządzeń sieciowych i procesów wsadowych.        |
| [Optymalizacja pod kątem pamięci](../sizes-memory.md)    | Esv3, Ev3, M, DSv2, Dv2  | Duża ilość pamięci na rdzeń. Opcja bardzo dobra w przypadku relacyjnych baz danych, średnich i dużych pamięci podręcznych oraz analizowania w pamięci.                 |
| [Optymalizacja pod kątem magazynu](../sizes-storage.md)      | Lsv2, Ls              | Wysoka przepływność dysku i duża liczba operacji we/wy. Opcja idealna w przypadku danych big data oraz baz danych SQL i NoSQL.                                                         |
| [Procesor GPU](../sizes-gpu.md)          | NV, NVv2, NC, NCv2, NCv3, ND            | Maszyny wirtualne wyspecjalizowane pod kątem intensywnego renderowania grafiki i edytowania materiałów wideo.       |
| [Wysoka wydajność](../sizes-hpc.md) | H        | Maszyny wirtualne z najbardziej wydajnymi procesorami CPU oraz, opcjonalnie, interfejsami sieciowymi zapewniającymi wysoką przepływność (RDMA). |


### <a name="find-available-vm-sizes"></a>Wyszukiwanie dostępnych rozmiarów maszyn wirtualnych

Aby wyświetlić listę dostępnych rozmiarów maszyn wirtualnych w danym regionie, użyj polecenia [az vm list-sizes](/cli/azure/vm). 

```azurecli-interactive 
az vm list-sizes --location eastus --output table
```

Częściowe dane wyjściowe:

```output
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 2          3584  Standard_DS1                          1           1047552                    7168
                 4          7168  Standard_DS2                          2           1047552                   14336
                 8         14336  Standard_DS3                          4           1047552                   28672
                16         28672  Standard_DS4                          8           1047552                   57344
                 4         14336  Standard_DS11                         2           1047552                   28672
                 8         28672  Standard_DS12                         4           1047552                   57344
                16         57344  Standard_DS13                         8           1047552                  114688
                32        114688  Standard_DS14                        16           1047552                  229376
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
                 4          3584  Standard_A2                           2           1047552                  138240
                 8          7168  Standard_A3                           4           1047552                  291840
                 4         14336  Standard_A5                           2           1047552                  138240
                16         14336  Standard_A4                           8           1047552                  619520
                 8         28672  Standard_A6                           4           1047552                  291840
                16         57344  Standard_A7                           8           1047552                  619520
```

### <a name="create-vm-with-specific-size"></a>Tworzenie maszyny wirtualnej o określonym rozmiarze

W poprzednim przykładzie tworzenia maszyny wirtualnej nie podano rozmiaru, co spowodowało użycie rozmiaru domyślnego. Rozmiar maszyny wirtualnej można wybrać podczas jej tworzenia, używając polecenia [az vm create](/cli/azure/vm) i argumentu `--size`. 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM3 \
    --image UbuntuLTS \
    --size Standard_F4s \
    --generate-ssh-keys
```

### <a name="resize-a-vm"></a>Zmienianie rozmiaru maszyny wirtualnej

Po wdrożeniu maszyny wirtualnej można zmienić jej rozmiar w celu zwiększenia lub zmniejszenia alokacji zasobów. Bieżący rozmiar maszyny wirtualnej można wyświetlić za pomocą polecenia [az vm show](/cli/azure/vm):

```azurecli-interactive
az vm show --resource-group myResourceGroupVM --name myVM --query hardwareProfile.vmSize
```

Przed zmianą rozmiaru maszyny wirtualnej sprawdź, czy żądany rozmiar jest dostępny w bieżącym klastrze platformy Azure. Polecenie [az vm list-vm-resize-options](/cli/azure/vm) wyświetla listę rozmiarów. 

```azurecli-interactive 
az vm list-vm-resize-options --resource-group myResourceGroupVM --name myVM --query [].name
```

Jeśli żądany rozmiar maszyny wirtualnej jest dostępny, można go zmienić dla włączonej maszyny, ale zostanie ona ponownie uruchomiona w trakcie tej operacji. Wykonaj zmianę rozmiaru za pomocą polecenia [az vm resize]( /cli/azure/vm).

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_DS4_v2
```

Jeśli żądany rozmiar jest niedostępny w bieżącym klastrze, przed rozpoczęciem operacji zmiany rozmiaru należy cofnąć przydział maszyny wirtualnej. Za pomocą polecenia [az vm deallocate]( /cli/azure/vm) zatrzymaj maszynę wirtualną i cofnij jej przydział. Pamiętaj, że kiedy maszyna wirtualna zostanie ponownie włączona, wszelkie dane na dysku tymczasowym mogą zostać usunięte. Publiczny adres IP również się zmieni, chyba że używany jest statyczny adres IP. 

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupVM --name myVM
```

Po cofnięciu przydziału może zostać wykonana zmiana rozmiaru. 

```azurecli-interactive
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_GS1
```

Po zmienieniu rozmiaru można ponownie uruchomić maszynę wirtualną.

```azurecli-interactive
az vm start --resource-group myResourceGroupVM --name myVM
```

## <a name="vm-power-states"></a>Stany zasilania maszyny wirtualnej

Maszyna wirtualna platformy Azure może znajdować się w jednym z wielu stanów zasilania. Ten stan reprezentuje bieżący stan maszyny wirtualnej z punktu widzenia funkcji hypervisor. 

### <a name="power-states"></a>Stany zasilania

| Stan zasilania | Opis
|----|----|
| Uruchamianie | Wskazuje, że maszyna wirtualna jest uruchamiana. |
| Uruchomienie | Wskazuje, że maszyna wirtualna działa. |
| Zatrzymywanie | Wskazuje, że maszyna wirtualna jest zatrzymywana. | 
| Zatrzymano | Wskazuje, że maszyna wirtualna została zatrzymana. Opłaty za operacje obliczeniowe są także naliczane w przypadku maszyn wirtualnych w stanie Zatrzymano.  |
| Cofanie przydziału | Wskazuje, że przydział maszyny wirtualnej jest cofany. |
| Cofnięto przydział | Wskazuje, że maszyna wirtualna została usunięta z funkcji hypervisor, ale jest nadal dostępna na płaszczyźnie kontroli. Opłaty za operacje obliczeniowe nie są naliczane w przypadku maszyn wirtualnych w stanie Cofnięto przydział. |
| - | Wskazuje, że stan zasilania maszyny wirtualnej jest nieznany. |

### <a name="find-the-power-state"></a>Znajdowanie stanu zasilania

Aby pobrać stan określonej maszyny wirtualnej, użyj polecenia [az vm get-instance-view](/cli/azure/vm). Pamiętaj, aby określić prawidłową nazwę maszyny wirtualnej i grupy zasobów. 

```azurecli-interactive
az vm get-instance-view \
    --name myVM \
    --resource-group myResourceGroupVM \
    --query instanceView.statuses[1] --output table
```

Dane wyjściowe:

```output
ode                DisplayStatus    Level
------------------  ---------------  -------
PowerState/running  VM running       Info
```

Aby pobrać stan zasilania wszystkich maszyn wirtualnych w subskrypcji, użyj interfejsu [API Virtual Machines —](/rest/api/compute/virtualmachines/listall) lista wszystkich z parametrem **statusOnly ustawionym** na *wartość true.*

## <a name="management-tasks"></a>Zadania zarządzania

W trakcie cyklu życia maszyny wirtualnej można uruchamiać zadania zarządzania, takie jak uruchamianie, zatrzymywanie lub usuwanie maszyny wirtualnej. Ponadto można tworzyć skrypty służące do automatyzowania zadań powtarzających się lub złożonych. Używając interfejsu wiersza polecenia platformy Azure, wiele typowych zadań zarządzania można uruchamiać z poziomu wiersza polecenia lub w skryptach. 

### <a name="get-ip-address"></a>Uzyskiwanie adresu IP

To polecenie wyświetla prywatny i publiczny adres IP maszyny wirtualnej.  

```azurecli-interactive
az vm list-ip-addresses --resource-group myResourceGroupVM --name myVM --output table
```

### <a name="stop-virtual-machine"></a>Zatrzymywanie maszyny wirtualnej

```azurecli-interactive
az vm stop --resource-group myResourceGroupVM --name myVM
```

### <a name="start-virtual-machine"></a>Uruchamianie maszyny wirtualnej

```azurecli-interactive
az vm start --resource-group myResourceGroupVM --name myVM
```

### <a name="delete-resource-group"></a>Usuwanie grupy zasobów

Usunięcie grupy zasobów spowoduje również usunięcie wszystkich znajdujących się w niej zasobów, takich jak maszyna wirtualna, sieć wirtualna i dysk. Parametr `--no-wait` zwraca kontrolę do wiersza polecenia bez oczekiwania na zakończenie operacji. Parametr `--yes` potwierdza, że chcesz usunąć zasoby bez wyświetlania dodatkowego monitu.

```azurecli-interactive
az group delete --name myResourceGroupVM --no-wait --yes
```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje dotyczące podstawowych zadań tworzenia maszyny wirtualnej i zarządzania nią, takie jak:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej i łączenie z nią
> * Wybieranie i używanie obrazów maszyn wirtualnych
> * Wyświetlanie i używanie określonych rozmiarów maszyn wirtualnych
> * Zmienianie rozmiaru maszyny wirtualnej
> * Wyświetlanie stanu maszyny wirtualnej i jego omówienie

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat dysków maszyn wirtualnych.  

> [!div class="nextstepaction"]
> [Tworzenie dysków maszyn wirtualnych i zarządzanie nimi](./tutorial-manage-disks.md)