---
title: Przechwytywanie zarządzanego obrazu maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
description: Przechwyć zarządzany obraz maszyny wirtualnej platformy Azure, który ma być używany na potrzeby wdrożeń masowych przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 10/08/2018
ms.author: cynthn
ms.custom: legacy, devx-track-azurecli
ms.collection: linux
ms.openlocfilehash: 8e81c204c1f05b7fc6bdf1efc7060e2094c648e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102630626"
---
# <a name="how-to-create-a-managed-image-of-a-virtual-machine-or-vhd"></a>Jak utworzyć zarządzany obraz maszyny wirtualnej lub wirtualnego dysku twardego

Aby utworzyć wiele kopii maszyny wirtualnej do użycia na platformie Azure na potrzeby tworzenia i testowania, Przechwyć zarządzany obraz maszyny wirtualnej lub wirtualnego dysku twardego systemu operacyjnego. Aby tworzyć, przechowywać i udostępniać obrazy na dużą skalę, zobacz [udostępnione Galerie obrazów](../shared-images-cli.md).

Jeden zarządzany obraz obsługuje maksymalnie 20 równoczesnych wdrożeń. Próba utworzenia więcej niż 20 maszyn wirtualnych współbieżnie z tego samego obrazu zarządzanego może spowodować przekroczenie limitu czasu oczekiwania z ograniczeniami wydajności magazynu pojedynczego wirtualnego dysku twardego. Aby jednocześnie utworzyć więcej niż 20 maszyn wirtualnych, Użyj obrazu [udostępnionego galerii obrazów](../shared-image-galleries.md) skonfigurowanego z 1 repliką dla każdego 20 współbieżnych wdrożeń maszyn wirtualnych.

Aby utworzyć obraz zarządzany, należy usunąć informacje o koncie osobistym. W poniższych krokach wycofasz obsługę istniejącej maszyny wirtualnej, zwolnisz ją i utworzysz obraz. Ten obraz służy do tworzenia maszyn wirtualnych w ramach dowolnej grupy zasobów w ramach subskrypcji.

Aby utworzyć kopię istniejącej maszyny wirtualnej z systemem Linux na potrzeby tworzenia kopii zapasowej lub debugowania albo przesłać wyspecjalizowany wirtualny dysk twardy z systemem Linux z lokalnej maszyny wirtualnej, zobacz temat [przekazywanie i Tworzenie maszyny wirtualnej z systemem Linux z niestandardowego obrazu dysku](upload-vhd.md).  

Możesz użyć usługi **Azure VM Image Builder (publiczna wersja zapoznawcza)** , aby utworzyć niestandardowy obraz, nie trzeba uczyć się żadnych narzędzi ani skonfigurować potoków kompilacji, po prostu dostarczając konfigurację obrazu, a Konstruktor obrazów utworzy obraz. Aby uzyskać więcej informacji, zobacz [wprowadzenie z konstruktorem obrazów maszyn wirtualnych platformy Azure](../image-builder-overview.md).

Przed utworzeniem obrazu potrzebne są następujące elementy:

* Maszyna wirtualna platformy Azure utworzona w modelu wdrażania Menedżer zasobów, który używa dysków zarządzanych. Jeśli maszyna wirtualna z systemem Linux nie została jeszcze utworzona, możesz użyć [portalu](quick-create-portal.md), [interfejsu wiersza polecenia platformy Azure](quick-create-cli.md)lub [szablonów Menedżer zasobów](create-ssh-secured-vm-from-template.md). Skonfiguruj maszynę wirtualną zgodnie z wymaganiami. Na przykład [Dodaj dyski danych](add-disk.md), Zastosuj aktualizacje i zainstaluj aplikacje. 

* Najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) został zainstalowany i być zalogowany do konta platformy Azure przy użyciu [AZ login](/cli/azure/reference-index#az-login).

## <a name="prefer-a-tutorial-instead"></a>Wolisz korzystać z samouczka?

Aby uzyskać uproszczoną wersję tego artykułu oraz testowanie, ocenianie lub uczenie maszyn wirtualnych na platformie Azure, zobacz [Tworzenie niestandardowego obrazu maszyny wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia](tutorial-custom-images.md).  W przeciwnym razie należy przeczytać tutaj, aby uzyskać pełny obraz.


## <a name="step-1-deprovision-the-vm"></a>Krok 1. Anulowanie aprowizacji maszyny wirtualnej
Najpierw należy anulować obsługę administracyjną maszyny wirtualnej przy użyciu agenta maszyny wirtualnej platformy Azure w celu usunięcia plików i danych specyficznych dla maszyny. Użyj `waagent` polecenia z `-deprovision+user` parametrem na ŹRÓDŁOWEJ maszynie wirtualnej z systemem Linux. Aby uzyskać więcej informacji, zobacz [Przewodnik użytkownika Agenta platformy Azure dla systemu Linux](../extensions/agent-linux.md). Nie można odwrócić tego procesu.

1. Nawiązywanie połączenia z maszyną wirtualną z systemem Linux przy użyciu klienta SSH.
2. W oknie SSH wprowadź następujące polecenie:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > To polecenie można uruchomić tylko na maszynie wirtualnej, która zostanie przechwycona jako obraz. To polecenie nie gwarantuje, że obraz jest czyszczony dla wszystkich poufnych informacji lub jest odpowiedni do ponownej dystrybucji. Ten `+user` parametr usuwa także ostatnio zainicjowane konto użytkownika. Aby zachować poświadczenia konta użytkownika na maszynie wirtualnej, użyj tylko `-deprovision` .
 
3. Wprowadź **y** , aby kontynuować. Można dodać parametr, `-force` Aby uniknąć tego kroku potwierdzenia.
4. Po zakończeniu wykonywania polecenia wpisz **Exit** , aby zamknąć klienta SSH.  Maszyna wirtualna będzie nadal działać w tym momencie.

## <a name="step-2-create-vm-image"></a>Krok 2. Tworzenie obrazu maszyny wirtualnej
Użyj interfejsu wiersza polecenia platformy Azure, aby oznaczyć maszynę wirtualną jako uogólnioną i przechwycić obraz. W poniższych przykładach Zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy *parametrów obejmują:* *MyVnet*, i *myVM*.

1. Cofnij przydział maszyny wirtualnej, która została wstrzymana za pomocą [AZ VM deallocate](/cli/azure/vm). Poniższy przykład powoduje cofnięcie przydziału maszyny wirtualnej o nazwie *myVM* w grupie zasobów o nazwie Moja *resourceName*.  
   
    ```azurecli
    az vm deallocate \
        --resource-group myResourceGroup \
        --name myVM
    ```
    
    Poczekaj na całkowite cofnięcie przydziału maszyny wirtualnej przed przejściem dalej. Może to potrwać kilka minut.  Maszyna wirtualna jest zamykana podczas cofania alokacji.

2. Oznacz maszynę wirtualną jako uogólnioną za pomocą [AZ VM generalize](/cli/azure/vm). Poniższy przykład oznacza maszynę wirtualną o nazwie *myVM* w grupie zasobów o nazwie Moja *resourceName* jako uogólniona.
   
    ```azurecli
    az vm generalize \
        --resource-group myResourceGroup \
        --name myVM
    ```

    Nie można już ponownie uruchomić maszyny wirtualnej, która została uogólniona.

3. Utwórz obraz zasobu maszyny wirtualnej za pomocą elementu [AZ Image Create](/cli/azure/image#az-image-create). Poniższy przykład umożliwia utworzenie obrazu o nazwie "Moja *ilustracja* " w grupie zasobów o nazwie Moja *resourceName* przy użyciu zasobu maszyny wirtualnej o nazwie *myVM*.
   
    ```azurecli
    az image create \
        --resource-group myResourceGroup \
        --name myImage --source myVM
    ```
   
   > [!NOTE]
   > Obraz jest tworzony w tej samej grupie zasobów co źródłowa maszyna wirtualna. Możesz tworzyć maszyny wirtualne w dowolnej grupie zasobów w ramach subskrypcji z tego obrazu. Z perspektywy zarządzania można utworzyć określoną grupę zasobów dla zasobów i obrazów maszyn wirtualnych.
   >
   > Jeśli chcesz przechowywać obraz w magazynie odpornym na strefy, musisz go utworzyć w regionie, który obsługuje [strefy dostępności](../../availability-zones/az-overview.md) i uwzględnić `--zone-resilient true` parametr.
   
To polecenie zwraca kod JSON opisujący obraz maszyny wirtualnej. Zapisz dane wyjściowe do późniejszego odwołania.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Krok 3. Tworzenie maszyny wirtualnej na podstawie przechwyconego obrazu
Utwórz maszynę wirtualną przy użyciu obrazu utworzonego za pomocą polecenia [AZ VM Create](/cli/azure/vm). Poniższy przykład tworzy maszynę wirtualną o nazwie *myVMDeployed* z obrazu o nazwie "Moja *Image*".

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Tworzenie maszyny wirtualnej w innej grupie zasobów 

Maszyny wirtualne można tworzyć na podstawie obrazu w dowolnej grupie zasobów w ramach subskrypcji. Aby utworzyć maszynę wirtualną w innej grupie zasobów niż obraz, podaj pełny identyfikator zasobu na obrazie. Użyj [AZ Image list](/cli/azure/image#az-image-list) , aby wyświetlić listę obrazów. Dane wyjściowe będą podobne do poniższego przykładu.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

Poniższy przykład używa [AZ VM Create](/cli/azure/vm#az-vm-create) , aby utworzyć maszynę wirtualną w grupie zasobów innej niż obraz źródłowy, określając identyfikator zasobu obrazu.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Krok 4. Weryfikowanie wdrożenia

Użyj protokołu SSH do utworzonej maszyny wirtualnej w celu zweryfikowania wdrożenia i rozpoczęcia korzystania z nowej maszyny wirtualnej. Aby nawiązać połączenie za pośrednictwem protokołu SSH, Znajdź adres IP lub nazwę FQDN maszyny wirtualnej za pomocą polecenie [AZ VM show](/cli/azure/vm#az-vm-show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Następne kroki
Aby tworzyć, przechowywać i udostępniać obrazy na dużą skalę, zobacz [udostępnione Galerie obrazów](../shared-images-cli.md).
