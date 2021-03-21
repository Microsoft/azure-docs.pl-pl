---
title: Samouczek — Tworzenie sieci wirtualnych platformy Azure i zarządzanie nimi dla maszyn wirtualnych z systemem Linux
description: Z tego samouczka dowiesz się, jak tworzyć sieci wirtualne platformy Azure i zarządzać nimi za pomocą interfejsu wiersza polecenia platformy Azure na maszynach wirtualnych z systemem Linux
services: virtual-machines
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
ms.collection: linux
tags: azure-resource-manager
ms.subservice: networking
ms.assetid: ''
ms.service: virtual-machines
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 6356d9b587847ff2b49574b970109ada0d785660
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564632"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-linux-virtual-machines-with-the-azure-cli"></a>Samouczek: tworzenie sieci wirtualnych platformy Azure i zarządzanie nimi za pomocą interfejsu wiersza polecenia platformy Azure na maszynach wirtualnych z systemem Linux

Maszyny wirtualne platformy Azure korzystają z sieci platformy Azure do wewnętrznej i zewnętrznej komunikacji sieciowej. Ten samouczek przedstawia proces wdrażania dwóch maszyn wirtualnych i konfigurowania dla nich sieci platformy Azure. W przykładach w tym samouczku założono, że maszyny wirtualne hostują aplikację internetową z zapleczem bazy danych, jednak wdrożenie aplikacji nie jest omówione w samouczku. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej i podsieci
> * Tworzenie publicznego adresu IP
> * Tworzenie maszyny wirtualnej frontonu
> * Zabezpieczanie ruchu sieciowego
> * Tworzenie maszyny wirtualnej zaplecza

W tym samouczku jest używany interfejs wiersza polecenia w [Azure Cloud Shell](../../cloud-shell/overview.md), który jest stale aktualizowany do najnowszej wersji. Aby otworzyć Cloud Shell, wybierz opcję **Wypróbuj** z góry dowolnego bloku kodu.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="vm-networking-overview"></a>Omówienie sieci maszyn wirtualnych

Sieci wirtualne platformy Azure umożliwiają bezpieczne połączenia sieciowe między maszynami wirtualnymi, Internetem i innymi usługami platformy Azure, takimi jak Azure SQL Database. Sieci wirtualne są podzielone na logiczne segmenty nazywane podsieciami. Podsieci są używane do sterowania przepływem sieciowym oraz pełnią funkcję granicy zabezpieczeń. Wdrażana maszyna wirtualna zwykle zawiera wirtualny interfejs sieciowy dołączony do podsieci.

Po ukończeniu samouczka zostaną utworzone następujące zasoby sieci wirtualnej:

![Sieć wirtualna z dwiema podsieciami](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* — sieć wirtualna, której maszyny wirtualne używają do komunikacji między sobą i z Internetem.
- *myFrontendSubnet* — podsieć w sieci *myVNet* używana przez zasoby frontonu.
- *myPublicIPAddress* — publiczny adres IP używany do uzyskania dostępu do maszyny *myFrontendVM* za pośrednictwem Internetu.
- *myFrontentNic* — interfejs sieciowy używany przez maszynę wirtualną *myFrontendVM* do komunikacji z maszyną *myBackendVM*.
- *myFrontendVM* — maszyna wirtualna używana do komunikacji pomiędzy Internetem a maszyną *myBackendVM*.
- *myBackendNSG* — sieciowa grupa zabezpieczeń, która kontroluje komunikację między maszyną *myFrontendVM* a maszyną *myBackendVM*.
- *myBackendSubnet* — podsieć skojarzona z grupą *myBackendNSG* i używana przez zasoby zaplecza.
- *myBackendNic* — interfejs sieciowy używany przez maszynę wirtualną *myBackendVM* do komunikacji z maszyną *myFrontendVM*.
- *myBackendVM* — maszyna wirtualna, która korzysta z portów 22 i 3306 do komunikacji z maszyną *myFrontendVM*.

## <a name="create-a-virtual-network-and-subnet"></a>Tworzenie sieci wirtualnej i podsieci

W tym samouczku zostanie utworzona jedna sieć wirtualna z dwoma podsieciami. Zostanie utworzona podsieć frontonu do hostowania aplikacji internetowej oraz podsieć zaplecza do hostowania serwera bazy danych.

Przed utworzeniem sieci wirtualnej należy utworzyć grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie *myRGNetwork* w lokalizacji Wschodnie.

```azurecli-interactive 
az group create --name myRGNetwork --location eastus
```

### <a name="create-virtual-network"></a>Tworzenie sieci wirtualnej

Użyj polecenia [az network vnet create](/cli/azure/network/vnet), aby utworzyć sieć wirtualną. W tym przykładzie sieć nazywa się *mvVNet*, a jej prefiks adresu to *10.0.0.0/16*. Tworzona jest również podsieć o nazwie *myFrontendSubnet* z prefiksem *10.0.1.0/24*. W dalszej części tego samouczka maszyna wirtualna frontonu zostanie połączona z tą podsiecią. 

```azurecli-interactive 
az network vnet create \
  --resource-group myRGNetwork \
  --name myVNet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myFrontendSubnet \
  --subnet-prefix 10.0.1.0/24
```

### <a name="create-subnet"></a>Tworzenie podsieci

Nowa podsieć jest dodawana do maszyny wirtualnej przy użyciu polecenia [az network vnet subnet create](/cli/azure/network/vnet/subnet). W tym przykładzie podsieć nazywa się *myBackendSubnet*, a jej prefiks adresu to *10.0.2.0/24*. Ta podsieć jest używana ze wszystkimi usługami zaplecza.

```azurecli-interactive 
az network vnet subnet create \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --address-prefix 10.0.2.0/24
```

W ten sposób utworzono sieć i podzielono ją na dwie podsieci — jedną dla usług frontonu i jedną dla usług zaplecza. W kolejnej sekcji zostaną utworzone maszyny wirtualne połączone z tymi podsieciami.

## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Publiczny adres IP umożliwia dostęp do zasobów platformy Azure w Internecie. Można skonfigurować dynamiczną lub statyczną alokację publicznego adresu IP. Domyślnie publiczny adres IP jest przydzielany dynamicznie. Dynamiczne adresy IP są zwalniane, gdy przydział maszyny wirtualnej zostaje cofnięty. Dlatego adres IP zmienia się podczas każdej operacji, która obejmuje cofnięcie przydziału maszyny wirtualnej.

Można też ustawić statyczną metodę alokacji, co gwarantuje, że adres IP pozostanie przydzielony do maszyny wirtualnej nawet wówczas, gdy jej przydział zostanie cofnięty. Podczas korzystania ze statycznie przydzielonych adresów IP nie można określić adresu IP. Zamiast tego jest on przydzielany z puli dostępnych adresów.

```azurecli-interactive
az network public-ip create --resource-group myRGNetwork --name myPublicIPAddress
```

Podczas tworzenia maszyny wirtualnej za pomocą polecenia [az vm create](/cli/azure/vm) domyślnie stosowana jest dynamiczna metoda alokacji publicznego adresu IP. Aby przypisać statyczny publiczny adres IP do maszyny wirtualnej, należy podczas tworzenia jej za pomocą polecenia [az vm create](/cli/azure/vm) dołączyć argument `--public-ip-address-allocation static`. Ta operacja nie jest pokazana w samouczku, ale w kolejnej sekcji dynamicznie przypisany adres IP zostanie zmieniony na adres przypisany statycznie. 

### <a name="change-allocation-method"></a>Zmiana metody alokacji

Metodę alokacji adresu IP można zmienić za pomocą polecenia [az network public-ip update](/cli/azure/network/public-ip). W tym przykładzie metoda alokacji adresu IP maszyny wirtualnej frontonu zostanie zmieniona na statyczną.

Najpierw cofnij przydział maszyny wirtualnej.

```azurecli-interactive 
az vm deallocate --resource-group myRGNetwork --name myFrontendVM
```

Użyj polecenia [az network public-ip update](/cli/azure/network/public-ip), aby zaktualizować metodę alokacji. W tym przypadku parametr `--allocation-method` zostanie zmieniony na *static*.

```azurecli-interactive 
az network public-ip update --resource-group myRGNetwork --name myPublicIPAddress --allocation-method static
```

Uruchom maszynę wirtualną.

```azurecli-interactive 
az vm start --resource-group myRGNetwork --name myFrontendVM --no-wait
```

### <a name="no-public-ip-address"></a>Brak publicznego adresu IP

Maszyna wirtualna często nie musi być dostępna przez Internet. Aby utworzyć maszynę wirtualną bez publicznego adresu IP, należy użyć argumentu `--public-ip-address ""` z pustą parą podwójnych cudzysłowów. Ta konfiguracja jest przedstawiona w dalszej części tego samouczka.

## <a name="create-a-front-end-vm"></a>Tworzenie maszyny wirtualnej frontonu

Użyj polecenia [az vm create](/cli/azure/vm), aby utworzyć maszynę wirtualną o nazwie *myFrontendVM*, korzystającą z adresu *myPublicIPAddress*.

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --vnet-name myVNet \
  --subnet myFrontendSubnet \
  --nsg myFrontendNSG \
  --public-ip-address myPublicIPAddress \
  --image UbuntuLTS \
  --generate-ssh-keys
```

## <a name="secure-network-traffic"></a>Zabezpieczanie ruchu sieciowego

Sieciowa grupa zabezpieczeń (NSG, network security group) zawiera listę reguł zabezpieczeń, które blokują lub zezwalają na ruch sieciowy do zasobów połączonych z usługami Azure Virtual Network (VNet). Sieciowe grupy zabezpieczeń można skojarzyć z podsieciami lub pojedynczymi interfejsami sieciowymi. Jeśli sieciowa grupa zabezpieczeń jest skojarzona z interfejsem sieciowym, jest stosowana wyłącznie do powiązanej maszyny wirtualnej. Jeśli sieciowa grupa zabezpieczeń jest skojarzona z podsiecią, te reguły są stosowane do wszystkich zasobów połączonych z tą podsiecią. 

### <a name="network-security-group-rules"></a>Reguły sieciowych grup zabezpieczeń

Reguły sieciowych grup zabezpieczeń określają porty sieciowe, dla których ruch jest dozwolony lub niedozwolony. Reguły mogą zawierać zakresy źródłowych lub docelowych adresów IP, umożliwiając kontrolę ruchu pomiędzy określonymi systemami lub podsieciami. Reguły sieciowych grup zabezpieczeń mają również priorytet (od 1 do 4096). Priorytet określa kolejność oceny reguł. Reguła z priorytetem 100 jest oceniana przed regułą z priorytetem 200.

Wszystkie sieciowe grupy zabezpieczeń zawierają zestaw reguł domyślnych. Reguł domyślnych nie można usunąć, ale ponieważ mają przypisany najniższy priorytet, mogą być zastąpione przez tworzone zasady.

Domyślne reguły dla sieciowych grup zabezpieczeń to:

- **Sieć wirtualna** — ruch pochodzący z sieci wirtualnej i kończący się w niej jest dozwolony zarówno w kierunku przychodzącym, jak i wychodzącym.
- **Internet** — ruch wychodzący jest dozwolony, ale ruch przychodzący jest blokowany.
- **Moduł równoważenia obciążenia** — umożliwia modułowi równoważenia obciążenia platformy Azure badanie kondycji maszyn wirtualnych i wystąpień ról. Jeśli nie używasz zestawu z równoważeniem obciążenia, możesz przesłonić tę regułę.

### <a name="create-network-security-groups"></a>Tworzenie sieciowych grup zabezpieczeń

Sieciową grupę zabezpieczeń można utworzyć podczas tworzenia maszyny wirtualnej przy użyciu polecenia [az vm create](/cli/azure/vm). W takim przypadku sieciowa grupa zabezpieczeń jest skojarzona z interfejsem sieciowym tej maszyny wirtualnej, z automatycznie utworzoną regułą zezwalającej na ruch z dowolnego źródła przez port *22*. Wcześniej w tym samouczku sieciowa grupa zabezpieczeń frontonu została automatycznie utworzona wraz z maszyną wirtualną frontonu. Została również automatycznie utworzona reguła sieciowej grupy zabezpieczeń dla portu 22. 

W niektórych przypadkach pomocne może być wcześniejsze utworzenie sieciowej grupy zabezpieczeń, na przykład wtedy, gdy nie powinny być tworzone domyślne reguły protokołu SSH, lub jeśli sieciowa grupa zabezpieczeń ma być połączona z podsiecią. 

Użyj polecenia [az network nsg create](/cli/azure/network/nsg), aby utworzyć sieciową grupę zabezpieczeń.

```azurecli-interactive 
az network nsg create --resource-group myRGNetwork --name myBackendNSG
```

Sieciowa grupa zabezpieczeń nie zostanie skojarzona z interfejsem sieciowym, ale z podsiecią. W tej konfiguracji każda maszyna wirtualna połączona z podsiecią dziedziczy reguły sieciowej grupy zabezpieczeń.

Zaktualizuj istniejącą podsieć o nazwie *myBackendSubnet*, używając nowej sieciowej grupy zabezpieczeń.

```azurecli-interactive 
az network vnet subnet update \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --network-security-group myBackendNSG
```

### <a name="secure-incoming-traffic"></a>Zabezpieczanie ruchu przychodzącego

Podczas tworzenia maszyny wirtualnej frontonu utworzona została reguła sieciowej grupy zabezpieczeń, zezwalająca na ruch przychodzący na porcie 22. Ta reguła umożliwia nawiązywanie połączeń SSH z maszyną wirtualną. W tym przykładzie ruch powinien być również dozwolony na porcie *80*. Ta konfiguracja umożliwia uzyskanie dostępu do aplikacji internetowej na maszynie wirtualnej.

Użyj polecenia [az network nsg rule create](/cli/azure/network/nsg/rule), aby utworzyć regułę dla portu *80*.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myFrontendNSG \
  --name http \
  --access allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 80
```

Maszyna wirtualna frontonu jest dostępna tylko na porcie *22* i porcie *80*. Pozostały ruch przychodzący jest blokowany w sieciowej grupie zabezpieczeń. Warto zwizualizować konfiguracje reguł sieciowej grupy zabezpieczeń. Polecenie [az network rule list](/cli/azure/network/nsg/rule) zwraca konfigurację reguł sieciowej grupy zabezpieczeń. 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myFrontendNSG --output table
```

### <a name="secure-vm-to-vm-traffic"></a>Zabezpieczanie ruchu między maszynami wirtualnymi

Reguły sieciowych grup zabezpieczeń można także stosować pomiędzy maszynami wirtualnymi. W tym przykładzie maszyna wirtualna frontonu musi połączyć się z maszyną wirtualną zaplecza na porcie *22* i *3306*. Ta konfiguracja umożliwia nawiązywanie połączeń SSH wychodzących z maszyny wirtualnej frontonu, a także umożliwia aplikacji na maszynie wirtualnej frontonu komunikację z bazą danych MySQL zaplecza. Cały pozostały ruch pomiędzy maszynami wirtualnymi frontonu i zaplecza powinien zostać zablokowany.

Użyj polecenia [az network nsg rule create](/cli/azure/network/nsg/rule), aby utworzyć regułę dla portu 22. Zwróć uwagę, że argument `--source-address-prefix` ma wartość *10.0.1.0/24*. Ta konfiguracja gwarantuje, że ruch z podsieci frontonu jest dozwolony wyłącznie za pośrednictwem sieciowej grupy zabezpieczeń.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name SSH \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "22"
```

Teraz dodaj regułę dla ruchu bazy MySQL na porcie 3306.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name MySQL \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "3306"
```

Ponieważ sieciowe grupy zabezpieczeń mają domyślną regułę zezwalającą na dowolny ruch pomiędzy maszynami wirtualnymi w tej samej sieci wirtualnej, można utworzyć regułę blokowania całego ruchu w sieciowych grupach zabezpieczeń zaplecza. Zwróć uwagę, że parametr `--priority` ma wartość *300*, czyli niższą niż priorytet reguł sieciowej grupy zabezpieczeń i reguł bazy danych MySQL. Ta konfiguracja gwarantuje, że ruch SSH i MySQL będzie nadal dozwolony w sieciowej grupie zabezpieczeń.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name denyAll \
  --access Deny \
  --protocol Tcp \
  --direction Inbound \
  --priority 300 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "*"
```

## <a name="create-back-end-vm"></a>Tworzenie maszyny wirtualnej zaplecza

Teraz należy utworzyć maszynę wirtualną połączoną z podsiecią *myBackendSubnet*. Należy zwrócić uwagę, że wartość argumentu `--nsg` to pusta para cudzysłowów. Z tą maszyną wirtualną nie trzeba tworzyć sieciowej grupy zabezpieczeń. Maszyna wirtualna jest połączona z podsiecią zaplecza, która jest chroniona za pomocą utworzonej wcześniej sieciowej grupy zabezpieczeń. Ta sieciowa grupa zabezpieczeń jest stosowana do maszyny wirtualnej. Należy również zauważyć, że wartość argumentu `--public-ip-address` ma to pusta para cudzysłowów. Ta konfiguracja tworzy maszynę wirtualną bez publicznego adresu IP. 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --public-ip-address "" \
  --nsg "" \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Maszyna wirtualna zaplecza jest dostępna dla podsieci frontonu tylko na porcie *22* i porcie *3306*. Pozostały ruch przychodzący jest blokowany w sieciowej grupie zabezpieczeń. Warto zwizualizować konfiguracje reguł sieciowej grupy zabezpieczeń. Polecenie [az network rule list](/cli/azure/network/nsg/rule) zwraca konfigurację reguł sieciowej grupy zabezpieczeń. 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myBackendNSG --output table
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono i zabezpieczono sieci platformy Azure na potrzeby maszyn wirtualnych. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej i podsieci
> * Tworzenie publicznego adresu IP
> * Tworzenie maszyny wirtualnej frontonu
> * Zabezpieczanie ruchu sieciowego
> * Tworzenie maszyny wirtualnej zaplecza

Aby dowiedzieć się więcej o ochronie dysków maszyn wirtualnych, zobacz [kopia zapasowa i odzyskiwanie po awarii dla dysków](../backup-and-disaster-recovery-for-azure-iaas-disks.md).