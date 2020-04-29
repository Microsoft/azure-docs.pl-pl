---
title: Samouczek — Tworzenie niestandardowych obrazów maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure
description: Z tego samouczka dowiesz się, jak utworzyć niestandardowy obraz maszyny wirtualnej na platformie Azure za pomocą interfejsu wiersza polecenia platformy Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: dc7b395d46fd28cde9ccbbda8a8a55447efa61c9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81460056"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Samouczek: tworzenie niestandardowego obrazu maszyny wirtualnej na platformie Azure za pomocą interfejsu wiersza polecenia platformy Azure

Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe mogą służyć do ładowania początkowego konfiguracji, na przykład do wstępnego ładowania aplikacji, konfiguracji aplikacji i innych konfiguracji systemu operacyjnego. W tym samouczku utworzysz własny niestandardowy obraz maszyny wirtualnej platformy Azure. Omawiane kwestie:

> [!div class="checklist"]
> * Anulowanie aprowizacji i uogólnianie maszyn wirtualnych
> * Tworzenie obrazu niestandardowego
> * Tworzenie maszyny wirtualnej na podstawie obrazu niestandardowego
> * Wyświetlanie listy wszystkich obrazów w subskrypcji
> * Usuwanie obrazu

W tym samouczku jest używany interfejs wiersza polecenia w [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), który jest stale aktualizowany do najnowszej wersji. Aby otworzyć Cloud Shell, wybierz opcję **Wypróbuj** z góry dowolnego bloku kodu.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poniższych krokach wyjaśniono szczegółowo, jak na podstawie istniejącej maszyny wirtualnej utworzyć obraz niestandardowy do ponownego użycia, za pomocą którego można tworzyć nowe wystąpienia maszyn wirtualnych.

Do utworzenia przykładu przedstawionego w tym samouczku potrzebna jest istniejąca maszyna wirtualna. W razie potrzeby ten [przykładowy skrypt](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md) może go utworzyć. Podczas pracy z tym samouczkiem zamień w odpowiednich przypadkach nazwy maszyn wirtualnych i grup zasobów.

## <a name="create-a-custom-image"></a>Tworzenie obrazu niestandardowego

Aby utworzyć obraz maszyny wirtualnej, należy przygotować maszynę wirtualną przez anulowanie aprowizacji, cofnięcie przydziału, a następnie oznaczenie źródłowej maszyny wirtualnej jako uogólnionej. Po przygotowaniu maszyny wirtualnej można utworzyć obraz.

### <a name="deprovision-the-vm"></a>Anulowanie aprowizacji maszyny wirtualnej 

Anulowanie aprowizacji powoduje uogólnienie maszyny wirtualnej przez usunięcie informacji charakterystycznych dla danego komputera. To uogólnienie sprawia, że można wdrożyć wiele maszyn wirtualnych za pomocą pojedynczego obrazu. Podczas anulowania aprowizacji nazwa hosta jest resetowana do *localhost.localdomain*. Usuwane są również klucze hosta SSH, konfiguracje „nameserver”, hasło administratora (root) i buforowane dzierżawy DHCP.

> [!WARNING]
> Cofnięcie aprowizacji i oznaczenie maszyny wirtualnej jako uogólnionej spowoduje, że źródłowa maszyna wirtualna stanie się bezużyteczny i nie będzie można jej ponownie uruchomić. 

Aby anulować aprowizację maszyny wirtualnej, użyj agenta maszyny wirtualnej platformy Azure (waagent). Agent maszyny wirtualnej platformy Azure jest zainstalowany na maszynie wirtualnej i zarządza aprowizowaniem oraz interakcjami z kontrolerem sieci szkieletowej Azure. Aby uzyskać więcej informacji, zobacz [Przewodnik użytkownika Agenta platformy Azure dla systemu Linux](../extensions/agent-linux.md).

Połącz się z maszyną wirtualną przy użyciu protokołu SSH, a następnie uruchom polecenie anulowania aprowizacji maszyny wirtualnej. Dzięki użyciu argumentu `+user` zostaną również usunięte ostatnie aprowizowane konto użytkownika i wszelkie powiązane dane. Zastąp przykładowy adres IP publicznym adresem IP swojej maszyny wirtualnej.

Połącz się przez protokół SSH z maszyną wirtualną.
```bash
ssh azureuser@52.174.34.95
```
Anuluj aprowizację maszyny wirtualnej.

```bash
sudo waagent -deprovision+user -force
```
Zamknij sesję SSH.

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Cofanie przydziału i oznaczanie maszyny wirtualnej jako uogólnionej

Aby można było utworzyć obraz, należy cofnąć przydział maszyny wirtualnej. Cofnij przydział maszyny wirtualnej przy użyciu polecenia [az vm deallocate](/cli//azure/vm). 
   
```azurecli-interactive 
az vm deallocate --resource-group myResourceGroup --name myVM
```

Na koniec ustaw dla maszyny wirtualnej stan uogólniony za pomocą polecenia [az vm generalize](/cli//azure/vm), aby poinformować platformę Azure, że maszyna została uogólniona. Obraz można utworzyć tylko za pomocą uogólnionej maszyny wirtualnej.
   
```azurecli-interactive 
az vm generalize --resource-group myResourceGroup --name myVM
```

### <a name="create-the-image"></a>Tworzenie obrazu

Teraz można utworzyć obraz maszyny wirtualnej za pomocą polecenia [az image create](/cli//azure/image). W poniższym przykładzie utworzono obraz o nazwie *myImage* za pomocą maszyny wirtualnej o nazwie *myVM*.
   
```azurecli-interactive 
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --source myVM
```
 
## <a name="create-vms-from-the-image"></a>Tworzenie maszyn wirtualnych z obrazu

Obraz już istnieje, można więc za jego pomocą utworzyć jedną lub więcej nowych maszyn wirtualnych przy użyciu polecenia [az vm create](/cli/azure/vm). W poniższym przykładzie utworzono maszynę wirtualną o nazwie *myVMfromImage* za pomocą obrazu o nazwie *myImage*.

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVMfromImage \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Zalecamy ograniczenie liczby współbieżnych wdrożeń do 20 maszyn wirtualnych z jednego obrazu. Jeśli planujesz współbieżne wdrożenia z ponad 20 maszyn wirtualnych z tego samego obrazu niestandardowego, użyj [udostępnionej galerii obrazów](shared-image-galleries.md) z wieloma replikami obrazu. 

## <a name="image-management"></a>Zarządzanie obrazami 

Oto kilka przykładów typowych zadań zarządzania obrazami i tego, jak można je wykonać za pomocą wiersza polecenia platformy Azure.

Wyświetlanie listy wszystkich obrazów posortowanej według nazw w formacie tabeli.

```azurecli-interactive 
az image list \
    --resource-group myResourceGroup
```

Usuwanie obrazu. W tym przykładzie obraz o nazwie *myOldImage* jest usuwany z grupy *myResourceGroup*.

```azurecli-interactive 
az image delete \
    --name myOldImage \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony obraz niestandardowy maszyny wirtualnej. W tym samouczku omówiono:

> [!div class="checklist"]
> * Anulowanie aprowizacji i uogólnianie maszyn wirtualnych
> * Tworzenie obrazu niestandardowego
> * Tworzenie maszyny wirtualnej na podstawie obrazu niestandardowego
> * Wyświetlanie listy wszystkich obrazów w subskrypcji
> * Usuwanie obrazu

Przejdź do następnego samouczka, aby dowiedzieć się więcej o maszynach wirtualnych o wysokiej dostępności.

> [!div class="nextstepaction"]
> [Utwórz maszyny wirtualne o wysokiej](tutorial-availability-sets.md)dostępności.

