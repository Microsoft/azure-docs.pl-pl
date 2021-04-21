---
title: Przechwytywanie obrazu zarządzanego maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
description: Przechwyć zarządzany obraz maszyny wirtualnej platformy Azure do użycia na masową skalę wdrożeń przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 10/08/2018
ms.author: cynthn
ms.custom: legacy, devx-track-azurecli
ms.collection: linux
ms.openlocfilehash: dddbad2403734bc749497a7acca16b2a5b6076f4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792259"
---
# <a name="how-to-create-a-managed-image-of-a-virtual-machine-or-vhd"></a>Jak utworzyć obraz zarządzany maszyny wirtualnej lub wirtualnego dysku twardego

Aby utworzyć wiele kopii maszyny wirtualnej do użycia na platformie Azure w celu tworzenia i testowania, przechwyć zarządzany obraz maszyny wirtualnej lub dysku VHD systemu operacyjnego. Aby tworzyć, przechowywać i udostępniać obrazy na dużą skalę, zobacz [Galerie obrazów udostępnionych.](../shared-images-cli.md)

Jeden obraz zarządzany obsługuje maksymalnie 20 równoczesnych wdrożeń. Próba równoczesnego utworzenia więcej niż 20 maszyn wirtualnych z tego samego obrazu zarządzanego może spowodować przechowanie limitów czasu aprowrowi ze względu na ograniczenia wydajności magazynu pojedynczego wirtualnego dysku twardego. Aby utworzyć więcej niż 20 maszyn wirtualnych [](../shared-image-galleries.md) jednocześnie, użyj obrazu Galerie obrazów udostępnionych skonfigurowanego z 1 repliką dla każdego 20 współbieżnych wdrożeń maszyn wirtualnych.

Aby utworzyć obraz zarządzany, musisz usunąć informacje o koncie osobistym. W poniższych krokach cofniesz aprowizowanie istniejącej maszyny wirtualnej, cofniesz jej alokację i utworzysz obraz. Ten obraz umożliwia tworzenie maszyn wirtualnych w dowolnej grupie zasobów w ramach subskrypcji.

Aby utworzyć kopię istniejącej maszyny wirtualnej z systemem Linux na potrzeby tworzenia kopii zapasowej lub debugowania lub przekazać wyspecjalizowany wirtualny dysk twardy z systemem Linux z lokalnej maszyny wirtualnej, zobacz Upload and create a Linux VM from custom disk image (Przekazywanie i tworzenie maszyny wirtualnej z systemem Linux na podstawie niestandardowego obrazu [dysku).](upload-vhd.md)  

Usługa Azure VM Image Builder **(publiczna** wersja zapoznawcza) umożliwia tworzenie niestandardowego obrazu bez konieczności uczenia się żadnych narzędzi ani konfigurowania potoków kompilacji, po prostu dostarczając konfigurację obrazu, Image Builder utworzy obraz. Aby uzyskać więcej informacji, zobacz [Wprowadzenie z Azure VM Image Builder](../image-builder-overview.md).

Przed utworzeniem obrazu potrzebne są następujące elementy:

* Maszyna wirtualna platformy Azure utworzona w Resource Manager wdrażania, która używa dysków zarządzanych. Jeśli jeszcze nie utworzono maszyny wirtualnej z systemem Linux, możesz użyć [portalu,](quick-create-portal.md)interfejsu wiersza polecenia platformy [Azure](quick-create-cli.md)lub [Resource Manager szablonów.](create-ssh-secured-vm-from-template.md) Skonfiguruj maszynę wirtualną zgodnie z potrzebami. Na przykład można [dodawać dyski danych,](add-disk.md)stosować aktualizacje i instalować aplikacje. 

* Zainstalowano najnowszy [interfejs wiersza](/cli/azure/install-az-cli2) polecenia platformy Azure i zalogowano się do konta platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az_login).

## <a name="prefer-a-tutorial-instead"></a>Wolisz korzystać z samouczka?

Aby uzyskać uproszczoną wersję tego artykułu oraz informacje na temat testowania, oceniania lub poznawania maszyn wirtualnych na platformie Azure, zobacz Tworzenie niestandardowego obrazu maszyny wirtualnej platformy Azure przy użyciu interfejsu wiersza [polecenia](tutorial-custom-images.md).  W przeciwnym razie czytaj dalej tutaj, aby uzyskać pełny obraz.


## <a name="step-1-deprovision-the-vm"></a>Krok 1. Coprowizuje maszynę wirtualną
Najpierw cofniesz aprowizę maszyny wirtualnej za pomocą agenta maszyny wirtualnej platformy Azure, aby usunąć dane i pliki specyficzne dla maszyny. Użyj polecenia `waagent` z parametrem `-deprovision+user` na źródłowej maszynie wirtualnej z systemem Linux. Aby uzyskać więcej informacji, zobacz [Przewodnik użytkownika Agenta platformy Azure dla systemu Linux](../extensions/agent-linux.md). Nie można cofnąć tego procesu.

1. Połącz się z maszyną wirtualną z systemem Linux przy użyciu klienta SSH.
2. W oknie SSH wprowadź następujące polecenie:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > To polecenie należy uruchomić tylko na maszynie wirtualnej, która zostanie przechwycona jako obraz. To polecenie nie gwarantuje, że obraz zostanie wyczyszony ze wszystkich poufnych informacji lub jest odpowiedni do redystrybucji. Parametr `+user` usuwa również ostatnie aprowizowane konto użytkownika. Aby zachować poświadczenia konta użytkownika na maszynie wirtualnej, użyj tylko nazwy `-deprovision` .
 
3. Wprowadź **y,** aby kontynuować. Aby uniknąć tego `-force` kroku potwierdzenia, możesz dodać parametr .
4. Po zakończeniu polecenia wprowadź polecenie **exit,** aby zamknąć klienta SSH.  Maszyna wirtualna nadal będzie działać w tym momencie.

## <a name="step-2-create-vm-image"></a>Krok 2. Tworzenie obrazu maszyny wirtualnej
Użyj interfejsu wiersza polecenia platformy Azure, aby oznaczyć maszynę wirtualną jako uogólniony i przechwycić obraz. W poniższych przykładach zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy parametrów *to myResourceGroup,* *myVnet* i *myVM.*

1. Cofniesz aprowizowanie maszyny wirtualnej za pomocą az [vm deallocate](/cli/azure/vm). Poniższy przykład cofa przydział maszyny wirtualnej o *nazwie myVM* w grupie zasobów *o nazwie myResourceGroup.*  
   
    ```azurecli
    az vm deallocate \
        --resource-group myResourceGroup \
        --name myVM
    ```
    
    Przed przejściem zaczekaj na całkowite cofnięcia alokacji maszyny wirtualnej. Może to potrwać kilka minut.  Maszyna wirtualna jest zamykana podczas co deallocation.

2. Oznacz maszynę wirtualną jako uogólnione za pomocą [az vm generalize](/cli/azure/vm). W poniższym przykładzie maszyna wirtualna o nazwie *myVM* jest oznaczana jako uogólniona w grupie zasobów o nazwie *myResourceGroup.*
   
    ```azurecli
    az vm generalize \
        --resource-group myResourceGroup \
        --name myVM
    ```

    Maszyny wirtualnej, która została uogólniona, nie można już uruchomić ponownie.

3. Utwórz obraz zasobu maszyny wirtualnej za pomocą [az image create](/cli/azure/image#az_image_create). Poniższy przykład tworzy obraz o nazwie *myImage* w grupie zasobów o nazwie *myResourceGroup* przy użyciu zasobu maszyny wirtualnej *o nazwie myVM.*
   
    ```azurecli
    az image create \
        --resource-group myResourceGroup \
        --name myImage --source myVM
    ```
   
   > [!NOTE]
   > Obraz zostanie utworzony w tej samej grupie zasobów co źródłową maszynę wirtualną. Maszyny wirtualne można tworzyć w dowolnej grupie zasobów w ramach subskrypcji na tej ilustracji. Z perspektywy zarządzania możesz utworzyć konkretną grupę zasobów dla zasobów i obrazów maszyny wirtualnej.
   >
   > Jeśli chcesz przechowywać obraz w magazynie odpornym na strefy, musisz utworzyć [](../../availability-zones/az-overview.md) go w regionie, który obsługuje strefy dostępności i uwzględnia parametr `--zone-resilient true` .
   
To polecenie zwraca dane JSON opisujące obraz maszyny wirtualnej. Zapisz te dane wyjściowe do późniejszego odwołania.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Krok 3. Tworzenie maszyny wirtualnej na pomocą przechwyconego obrazu
Utwórz maszynę wirtualną przy użyciu obrazu utworzonego za pomocą [narzędzia az vm create.](/cli/azure/vm) Poniższy przykład tworzy maszynę wirtualną *o nazwie myVMDeployed* na obrazie *o nazwie myImage*.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Tworzenie maszyny wirtualnej w innej grupie zasobów 

Maszyny wirtualne można tworzyć na pomocą obrazu w dowolnej grupie zasobów w ramach subskrypcji. Aby utworzyć maszynę wirtualną w innej grupie zasobów niż obraz, określ pełny identyfikator zasobu dla obrazu. Użyj [az image list,](/cli/azure/image#az_image_list) aby wyświetlić listę obrazów. Dane wyjściowe będą podobne do poniższego przykładu.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

W poniższym przykładzie użyto [az vm create,](/cli/azure/vm#az_vm_create) aby utworzyć maszynę wirtualną w grupie zasobów innej niż obraz źródłowy, określając identyfikator zasobu obrazu.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Krok 4. Weryfikowanie wdrożenia

Na maszynie wirtualnej utworzonej przez Ciebie za pomocą programu SSH zweryfikuj wdrożenie i zacznij korzystać z nowej maszyny wirtualnej. Aby nawiązać połączenie za pośrednictwem protokołu SSH, znajdź adres IP lub WQDN maszyny wirtualnej za pomocą [az vm show](/cli/azure/vm#az_vm_show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Następne kroki
Aby tworzyć, przechowywać i udostępniać obrazy na dużą skalę, zobacz [Galerie obrazów udostępnionych.](../shared-images-cli.md)
