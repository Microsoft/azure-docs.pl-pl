---
title: 'Samouczek: Tworzenie & zarządzanie zestawem skalowania maszyn wirtualnych — interfejs wiersza polecenia platformy Azure'
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć zestaw skalowania maszyn wirtualnych oraz wykonywać niektóre typowe zadania zarządzania, takie jak uruchamianie i zatrzymywanie wystąpienia lub zmienianie pojemności zestawu skalowania.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 0f94823b958ae5f95789dd4ef9a62057bdf764a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94517470"
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Samouczek: tworzenie zestawu skalowania maszyn wirtualnych i zarządzanie nim przy użyciu interfejsu wiersza polecenia platformy Azure
Zestaw skalowania maszyn wirtualnych umożliwia wdrożenie zestawu identycznych, automatycznie skalowanych maszyn wirtualnych, oraz zarządzanie nimi. W całym cyklu życia zestawu skalowania maszyn wirtualnych konieczne może być uruchomienie jednego lub większej liczby zadań zarządzania. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zestawu skalowania maszyn wirtualnych i nawiązywanie połączenia z nim
> * Wybieranie i używanie obrazów maszyn wirtualnych
> * Wyświetlanie i używanie określonych rozmiarów wystąpień maszyn wirtualnych
> * Ręczne skalowanie zestawu skalowania
> * Wykonywanie typowych zadań zarządzania zestawem skalowania

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2.0.29 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana. 


## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Grupę zasobów należy utworzyć przed utworzeniem zestawu skalowania maszyn wirtualnych. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). W tym przykładzie grupa zasobów o nazwie *myResourceGroup* zostanie utworzona w regionie *eastus*. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Nazwa grupy zasobów jest podawana podczas tworzenia lub modyfikowania zestawu skalowania w różnych miejscach tego samouczka.


## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania
Utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [az vmss create](/cli/azure/vmss). Poniższy przykład tworzy zestaw skalowania o nazwie *myScaleSet* i GENERUJE klucze SSH, jeśli nie istnieją:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania oraz wystąpień maszyn wirtualnych trwa kilka minut. Musisz również utworzyć moduł równoważenia obciążenia, który umożliwia kierowanie ruchu do poszczególnych wystąpień maszyn wirtualnych.


## <a name="view-the-vm-instances-in-a-scale-set"></a>Wyświetlanie wystąpień maszyn wirtualnych w zestawie skalowania
Aby wyświetlić listę wystąpień maszyn wirtualnych w zestawie skalowania, użyj polecenia [az vmss list-instances](/cli/azure/vmss) w następujący sposób:

```azurecli-interactive
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

Następujące przykładowe dane wyjściowe zawierają dwa wystąpienia maszyn wirtualnych w zestawie skalowania:

```output
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  c059be0c-37a2-497a-b111-41272641533c
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUP  ec19e7a7-a4cd-4b24-9670-438f4876c1f9
```


W pierwszej kolumnie danych wyjściowych — *InstanceId* — widać identyfikator wystąpienia. Aby wyświetlić dodatkowe informacje na temat określonego wystąpienia maszyny wirtualnej, dodaj parametr `--instance-id` do polecenia [az vmss get-instance-view](/cli/azure/vmss). W poniższym przykładzie są widoczne informacje o wystąpieniu maszyny wirtualnej *1*:

```azurecli-interactive
az vmss get-instance-view \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --instance-id 1
```


## <a name="list-connection-information"></a>Wyświetlanie informacji o połączeniu
Do modułu równoważenia obciążenia, który kieruje ruch do poszczególnych wystąpień maszyn wirtualnych, jest przypisany publiczny adres IP. Domyślnie do modułu równoważenia obciążenia platformy Azure, który przesyła dalej ruch połączenia zdalnego na danym porcie do poszczególnych maszyn wirtualnych, są dodawane reguły translatora adresów sieciowych (NAT). Aby nawiązać połączenie z wystąpieniami maszyn wirtualnych w zestawie skalowania, musisz utworzyć połączenie zdalne z przypisanym publicznym adresem IP za pośrednictwem określonego numeru portu.

Za pomocą polecenia [az vmss list-instance-connection-info](/cli/azure/vmss) podaj listę adresów i portów służących do nawiązania połączenia z wystąpieniami maszyn wirtualnych w zestawie skalowania:

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

Następujące przykładowe dane wyjściowe zawierają nazwę wystąpienia, publiczny adres IP modułu równoważenia obciążenia oraz numer portu, do którego reguły NAT kierują ruch:

```output
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

Połącz się przez protokół SSH z pierwszym wystąpieniem maszyny wirtualnej. Podaj własny publiczny adres IP i numer portu za pomocą parametru `-p`, jak pokazano w poprzednim poleceniu:

```console
ssh azureuser@13.92.224.66 -p 50001
```

Po zalogowaniu do wystąpienia maszyny wirtualnej możesz ręcznie wprowadzić zmiany w konfiguracji zgodnie z potrzebami. Na razie zamknij sesję SSH w zwykły sposób:

```console
exit
```


## <a name="understand-vm-instance-images"></a>Opis obrazów wystąpień maszyn wirtualnych
Podczas tworzenia zestawu skalowania na początku tego samouczka dla wystąpień maszyn wirtualnych został użyty parametr `--image` obrazu *UbuntuLTS*. Portal Azure Marketplace zawiera wiele obrazów, których można użyć do tworzenia wystąpień maszyn wirtualnych. Aby wyświetlić listę najczęściej używanych obrazów, użyj polecenia [az vm image list](/cli/azure/vm/image).

```azurecli-interactive
az vm image list --output table
```

Następujące przykładowe dane wyjściowe zawierają najbardziej typowe obrazy maszyn wirtualnych na platformie Azure. Podczas tworzenia zestawu skalowania można wskazać obraz za pomocą wartości *UrnAlias*.

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

Aby wyświetlić pełną listę, dodaj argument `--all`. Listę obrazów można także filtrować za pomocą argumentów `--publisher` lub `–-offer`. W tym przykładzie lista została odfiltrowana pod kątem obrazów z ofertą zgodną z systemem *CentOS*:

```azurecli-interactive
az vm image list --offer CentOS --all --output table
```

Następujące skrócone dane wyjściowe zawierają niektóre dostępne obrazy z systemem CentOS 7.3:

```output
Offer    Publisher   Sku   Urn                                 Version
-------  ----------  ----  ----------------------------------  -------------
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20161221   7.3.20161221
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170421   7.3.20170421
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170517   7.3.20170517
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170612   7.3.20170612
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170707   7.3.20170707
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170925   7.3.20170925
```

Aby wdrożyć zestaw skalowania z konkretnym obrazem, użyj wartości z kolumny *Urn*. Podczas określania obrazu numer wersji można zastąpić wartością *latest*, co spowoduje wybranie najnowszej wersji dystrybucji. W tym przykładzie argument `--image` służy do określenia najnowszej wersji obrazu z systemem CentOS 7.3.

> [!IMPORTANT]
> Zalecamy użycie *najnowszej* wersji obrazu. Określ wartość "Najnowsza", aby użyć najnowszej wersji obrazu dostępnej w czasie wdrażania. Uwaga nawet jeśli używasz "Najnowsza", obraz maszyny wirtualnej nie zostanie automatycznie zaktualizowany po wdrożeniu, nawet jeśli zostanie udostępniona nowa wersja.

Nie trzeba wdrażać następującego zestawu skalowania, ponieważ utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania oraz wystąpień maszyn wirtualnych trwa kilka minut:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetCentOS \
  --image OpenLogic:CentOS:7.3:latest \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="understand-vm-instance-sizes"></a>Opis rozmiarów wystąpień maszyn wirtualnych
Rozmiar wystąpienia maszyny wirtualnej, czyli *jednostka SKU*, określa ilość zasobów obliczeniowych, takich jak procesor CPU, procesor GPU i pamięć, które są dostępne dla wystąpienia maszyny wirtualnej. Wystąpienia maszyn wirtualnych w zestawie skalowania muszą mieć rozmiary odpowiednie do oczekiwanych obciążeń.

### <a name="vm-instance-sizes"></a>Rozmiary wystąpień maszyn wirtualnych
W poniższej tabeli przedstawiono typowe kategorie rozmiarów maszyn wirtualnych podzielone według przypadków użycia.

| Typ                     | Typowe rozmiary           |    Opis       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Ogólnego przeznaczenia](../virtual-machines/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0–7| Zrównoważona moc procesora CPU w stosunku do pamięci. Opcja idealna w przypadku tworzenia i testowania, małych i średnich aplikacji oraz rozwiązań dotyczących danych.  |
| [Optymalizacja pod kątem obliczeń](../virtual-machines/sizes-compute.md)   | Fs, F             | Duża moc procesora CPU w stosunku do pamięci. Opcja dobra w przypadku aplikacji o średnim ruchu, urządzeń sieciowych i procesów wsadowych.        |
| [Optymalizacja pod kątem pamięci](../virtual-machines/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Duża ilość pamięci na rdzeń. Opcja bardzo dobra w przypadku relacyjnych baz danych, średnich i dużych pamięci podręcznych oraz analizowania w pamięci.                 |
| [Optymalizacja pod kątem magazynu](../virtual-machines/sizes-storage.md)      | Ls                | Wysoka przepływność dysku i duża liczba operacji we/wy. Opcja idealna w przypadku danych big data oraz baz danych SQL i NoSQL.                                                         |
| [Procesor GPU](../virtual-machines/sizes-gpu.md)          | NV, NC            | Maszyny wirtualne wyspecjalizowane pod kątem intensywnego renderowania grafiki i edytowania materiałów wideo.       |
| [Wysoka wydajność](../virtual-machines/sizes-hpc.md) | H, A8-11          | Maszyny wirtualne z najbardziej wydajnymi procesorami CPU oraz, opcjonalnie, interfejsami sieciowymi zapewniającymi wysoką przepływność (RDMA). 

### <a name="find-available-vm-instance-sizes"></a>Znajdowanie dostępnych rozmiarów wystąpień maszyn wirtualnych
Aby wyświetlić listę dostępnych rozmiarów wystąpień maszyn wirtualnych w danym regionie, użyj polecenia [az vm list-sizes](/cli/azure/vm).

```azurecli-interactive
az vm list-sizes --location eastus --output table
```

Rezultat jest podobny do poniższego, skróconego przykładu, który pokazuje zasoby przypisane do każdego rozmiaru maszyny wirtualnej:

```output
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 4          3584  Standard_DS1_v2                       1           1047552                    7168
                 8          7168  Standard_DS2_v2                       2           1047552                   14336
[...]
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
[...]
                 4          2048  Standard_F1                           1           1047552                   16384
                 8          4096  Standard_F2                           2           1047552                   32768
[...]
                24         57344  Standard_NV6                          6           1047552                   38912
                48        114688  Standard_NV12                        12           1047552                  696320
```

### <a name="create-a-scale-set-with-a-specific-vm-instance-size"></a>Tworzenie zestawu skalowania o określonym rozmiarze wystąpienia maszyny wirtualnej
Podczas tworzenia zestawu skalowania na początku tego samouczka dla wystąpień maszyn wirtualnych została użyta domyślna jednostka SKU maszyny wirtualnej *Standard_D1_v2*. Można wskazać inny rozmiar wystąpienia maszyny wirtualnej na podstawie danych wyjściowych polecenia [az vm list-sizes](/cli/azure/vm). W poniższym przykładzie zostanie utworzony zestaw skalowania z parametrem `--vm-sku` umożliwiającym wskazanie rozmiaru wystąpienia maszyny wirtualnej *Standard_F1*. Nie trzeba wdrażać następującego zestawu skalowania, ponieważ utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania oraz wystąpień maszyn wirtualnych trwa kilka minut:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetF1Sku \
  --image UbuntuLTS \
  --vm-sku Standard_F1 \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="change-the-capacity-of-a-scale-set"></a>Zmienianie pojemności zestawu skalowania
Podczas tworzenia zestawu skalowania na początku tego samouczka domyślnie zostały wdrożone dwa wystąpienia maszyn wirtualnych. Polecenie [az vmss create](/cli/azure/vmss) przyjmuje parametr `--instance-count`, który pozwala zmienić liczbę wystąpień tworzonych w zestawie skalowania. Aby zwiększyć lub zmniejszyć liczbę wystąpień maszyn wirtualnych w istniejącym zestawie skalowania, można ręcznie zmienić pojemność. Zestaw skalowania tworzy lub usuwa wymaganą liczbę wystąpień maszyn wirtualnych, a następnie konfiguruje moduł równoważenia obciążenia w celu dystrybucji ruchu.

Aby ręcznie zwiększyć lub zmniejszyć liczbę wystąpień maszyn wirtualnych w zestawie skalowania, użyj polecenia [az vmss scale](/cli/azure/vmss). W poniższym przykładzie liczba wystąpień maszyn wirtualnych w zestawie skalowania jest ustawiana na *3*:

```azurecli-interactive
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 3
```

Aktualizacja pojemności zestawu skalowania trwa kilka minut. Aby wyświetlić liczbę bieżących wystąpień w zestawie skalowania, użyj polecenia [az vmss show](/cli/azure/vmss) i zapytania *sku.capacity*:

```azurecli-interactive
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```


## <a name="common-management-tasks"></a>Typowe zadania zarządzania
Potrafisz już utworzyć zestaw skalowania, wyświetlić informacje o połączeniu i połączyć się z wystąpieniami maszyn wirtualnych. W tym samouczku omówiliśmy używanie innego obrazu systemu operacyjnego dla wystąpień maszyn wirtualnych, wybieranie innego rozmiaru maszyny wirtualnej oraz ręczne skalowanie liczby wystąpień. Typowe operacje zarządzania obejmują uruchamianie, zatrzymywanie i ponowne uruchamianie wystąpień maszyn wirtualnych w zestawie skalowania.

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>Zatrzymywanie i cofanie przydziału wystąpień maszyn wirtualnych w zestawie skalowania
Aby zatrzymać co najmniej jedno wystąpienie maszyny wirtualnej w zestawie skalowania, użyj polecenia [az vmss stop](/cli/azure/vmss). Parametr `--instance-ids` umożliwia wskazanie wystąpień maszyn wirtualnych, które mają zostać zatrzymane. Jeśli nie podasz identyfikatora wystąpienia, zostaną zatrzymane wszystkie wystąpienia maszyn wirtualnych w zestawie skalowania. W następującym przykładzie zostaje zatrzymane wystąpienie *1*:

```azurecli-interactive
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

Zatrzymane wystąpienia maszyn wirtualnych wciąż mają przydział i generują koszty operacji obliczeniowych. Jeśli chcesz cofnąć przydział wystąpień maszyn wirtualnych i ponosić tylko koszty magazynowania, użyj polecenia [az vmss deallocate](/cli/azure/vmss). Następujący przykład przedstawia zatrzymanie i cofnięcie przydziału wystąpienia *1*:

```azurecli-interactive
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="start-vm-instances-in-a-scale-set"></a>Uruchamianie wystąpień maszyn wirtualnych w zestawie skalowania
Aby uruchomić co najmniej jedno wystąpienie maszyny wirtualnej w zestawie skalowania, użyj polecenia [az vmss start](/cli/azure/vmss). Parametr `--instance-ids` umożliwia wskazanie wystąpień maszyn wirtualnych, które mają zostać uruchomione. Jeśli nie podasz identyfikatora wystąpienia, zostaną uruchomione wszystkie wystąpienia maszyn wirtualnych w zestawie skalowania. W następującym przykładzie zostaje uruchomione wystąpienie *1*:

```azurecli-interactive
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="restart-vm-instances-in-a-scale-set"></a>Ponowne uruchamianie wystąpień maszyn wirtualnych w zestawie skalowania
Aby uruchomić ponownie co najmniej jedno wystąpienie maszyny wirtualnej w zestawie skalowania, użyj polecenia [az vmss restart](/cli/azure/vmss). Parametr `--instance-ids` umożliwia wskazanie wystąpień maszyn wirtualnych, które mają zostać uruchomione ponownie. Jeśli nie podasz identyfikatora wystąpienia, zostaną uruchomione ponownie wszystkie wystąpienia maszyn wirtualnych w zestawie skalowania. W następującym przykładzie zostaje uruchomione ponownie wystąpienie *1*:

```azurecli-interactive
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```


## <a name="clean-up-resources"></a>Czyszczenie zasobów
Usunięcie grupy zasobów powoduje również usunięcie wszystkich znajdujących się w niej zasobów, takich jak wystąpienia maszyn wirtualnych, sieć wirtualna i dyski. Parametr `--no-wait` zwraca kontrolę do wiersza polecenia bez oczekiwania na zakończenie operacji. Parametr `--yes` potwierdza, że chcesz usunąć zasoby bez wyświetlania dodatkowego monitu.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Następne kroki
W tym samouczku omówiono wykonywanie niektórych podstawowych zadań w zakresie tworzenia zestawu skalowania i zarządzania nim przy użyciu interfejsu wiersza polecenia platformy Azure:

> [!div class="checklist"]
> * Tworzenie zestawu skalowania maszyn wirtualnych i nawiązywanie połączenia z nim
> * Wybieranie i używanie obrazów maszyn wirtualnych
> * Wyświetlanie i używanie określonych rozmiarów maszyn wirtualnych
> * Ręczne skalowanie zestawu skalowania
> * Wykonywanie typowych zadań zarządzania zestawem skalowania

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat dysków zestawów skalowania.

> [!div class="nextstepaction"]
> [Korzystanie z dysków danych z zestawami skalowania](tutorial-use-disks-cli.md)
