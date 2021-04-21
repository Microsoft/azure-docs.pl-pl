---
title: Samouczek — używanie niestandardowego obrazu maszyny wirtualnej w zestawie skalowania przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć niestandardowy obraz maszyny wirtualnej, którego można użyć do wdrożenia zestawu skalowania maszyny wirtualnej
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: a9a4abe550da4f0438f875127b3b689045c06e6f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763005"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Samouczek: tworzenie niestandardowego obrazu i używanie go dla zestawów skalowania maszyn wirtualnych za pośrednictwem interfejsu wiersza polecenia platformy Azure
Podczas tworzenia zestawu skalowania należy wskazać obraz używany do wdrożenia wystąpień maszyn wirtualnych. Aby zmniejszyć liczbę zadań wykonywanych po wdrożeniu wystąpień maszyn wirtualnych, można użyć niestandardowego obrazu maszyny wirtualnej. Niestandardowy obraz maszyny wirtualnej obejmuje wszystkie wymagane instalacje i konfiguracje aplikacji. Wszystkie wystąpienia maszyn wirtualnych utworzone w zestawie skalowania używają niestandardowego obrazu maszyny wirtualnej i są gotowe do obsługi ruchu aplikacji. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie galerii obrazów udostępnionych
> * Tworzenie wyspecjalizowanej definicji obrazu
> * Tworzenie wersji obrazu
> * Tworzenie zestawu skalowania na pomocą wyspecjalizowanego obrazu
> * Udostępnianie galerii obrazów


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.4.0 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="overview"></a>Omówienie

Zestaw [Shared Image Gallery](../virtual-machines/shared-image-galleries.md) upraszcza udostępnianie obrazów niestandardowych w całej organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe mogą służyć do ładowania początkowego konfiguracji, na przykład do wstępnego ładowania aplikacji, konfiguracji aplikacji i innych konfiguracji systemu operacyjnego. 

Interfejs Shared Image Gallery umożliwia udostępnianie niestandardowych obrazów maszyn wirtualnych innym osobom. Wybierz, które obrazy chcesz udostępnić, w których regionach chcesz je udostępnić i komu chcesz je udostępnić. 

## <a name="create-and-configure-a-source-vm"></a>Tworzenie i konfigurowanie źródłowej maszyny wirtualnej

Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group), a następnie utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Ta maszyna wirtualna jest następnie używana jako źródło obrazu. Poniższy przykład obejmuje tworzenie maszyny wirtualnej *myVM* w grupie zasobów *myResourceGroup*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

> [!IMPORTANT]
> Identyfikator **maszyny** wirtualnej jest wyświetlany w danych wyjściowych polecenia [az vm create.](/cli/azure/vm) Skopiuj to bezpieczne miejsce, aby można było z niego korzystać w dalszej części tego samouczka.

Publiczny adres IP maszyny wirtualnej jest również wyświetlany w danych wyjściowych polecenia [az vm create.](/cli/azure/vm) Za pośrednictwem protokołu SSH połącz się z publicznym adresem IP maszyny wirtualnej w następujący sposób:

```console
ssh azureuser@<publicIpAddress>
```

Aby dostosować maszynę wirtualną, należy zainstalować podstawowy serwer internetowy. Dzięki temu wdrożona maszyna wirtualna będzie miała wszystkie pakiety wymagane do uruchamiania aplikacji internetowej. Zainstaluj serwer *NGINX* za pomocą polecenia `apt-get` w następujący sposób:

```bash
sudo apt-get install -y nginx
```

Gdy wszystko będzie gotowe, wpisz , `exit` aby odłączyć połączenie SSH.

## <a name="create-an-image-gallery"></a>Tworzenie galerii obrazów 

Galeria obrazów jest podstawowym zasobem używanym do włączania udostępniania obrazów. 

Dozwolone znaki nazwy galerii to wielkie lub małe litery, cyfry, kropki i kropki. Nazwa galerii nie może zawierać łączników.   Nazwy galerii muszą być unikatowe w ramach subskrypcji. 

Utwórz galerię obrazów za pomocą [narzędzia az sig create](/cli/azure/sig#az_sig_create). Poniższy przykład tworzy grupę zasobów o nazwie galeria o nazwie *myGalleryRG* w regionach *Wschodnie* usa oraz galerię *o nazwie myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Tworzenie definicji obrazu

Definicje obrazów tworzą logiczne grupowanie obrazów. Są one używane do zarządzania informacjami o wersjach obrazów, które są w nich tworzone. 

Nazwy definicji obrazów mogą składać się z wielkich lub małych liter, cyfr, kropek, łączników i kropek. 

Upewnij się, że definicja obrazu jest właściwym typem. Jeśli maszyna wirtualna jest uogólniona (przy użyciu narzędzia Sysprep dla systemu Windows lub waagent -deprovision dla systemu Linux), należy utworzyć uogólnione definicje obrazu przy użyciu narzędzia `--os-state generalized` . Jeśli chcesz użyć maszyny wirtualnej bez usuwania istniejących kont użytkowników, utwórz wyspecjalizowaną definicję obrazu przy `--os-state specialized` użyciu .

Aby uzyskać więcej informacji na temat wartości, które można określić dla definicji obrazu, zobacz [Definicje obrazów](../virtual-machines/shared-image-galleries.md#image-definitions).

Utwórz definicję obrazu w galerii za pomocą [az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create).

W tym przykładzie definicja obrazu nosi nazwę *myImageDefinition* i jest dla [wyspecjalizowanego](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) obrazu systemu operacyjnego Linux. Aby utworzyć definicję obrazów przy użyciu systemu operacyjnego Windows, `--os-type Windows` użyj . 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

> [!IMPORTANT]
> Identyfikator **definicji** obrazu jest wyświetlany w danych wyjściowych polecenia . Skopiuj to bezpieczne miejsce, aby można było z niego korzystać w dalszej części tego samouczka.


## <a name="create-the-image-version"></a>Tworzenie wersji obrazu

Utwórz wersję obrazu z maszyny wirtualnej za pomocą [narzędzia az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create).  

Dozwolone znaki dla wersji obrazu to cyfry i kropki. Liczby muszą być w zakresie 32-bitowej liczby całkowitej. Format: *MajorVersion*. *MinorVersion*. *Popraw .*

W tym przykładzie wersja naszego obrazu to *1.0.0,* a my  utworzymy 1 replikę w regionie Południowo-środkowe usa i 1 replikę w regionie Wschodnie usa *2.* Regiony replikacji muszą zawierać region, w których znajduje się źródłowa maszyna wirtualna.

Zastąp wartość w `--managed-image` tym przykładzie identyfikatorem maszyny wirtualnej z poprzedniego kroku.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1" \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Zanim będzie można użyć tego samego obrazu zarządzanego do utworzenia innej wersji obrazu, należy poczekać na całkowite zakończenie tworzenia i replikowania wersji obrazu.
>
> Obraz można również przechowywać w magazynie Premium, dodając magazyn strefowo nadmiarowy lub , dodając go podczas `--storage-account-type  premium_lrs` tworzenia wersji [](../storage/common/storage-redundancy.md) `--storage-account-type  standard_zrs` obrazu.
>




## <a name="create-a-scale-set-from-the-image"></a>Tworzenie zestawu skalowania na obrazie
Utwórz zestaw skalowania na pomocą wyspecjalizowanego obrazu przy [`az vmss create`](/cli/azure/vmss#az_vmss_create) użyciu . 

Utwórz zestaw skalowania przy [`az vmss create`](/cli/azure/vmss#az_vmss_create) użyciu parametru --specialized, aby wskazać, że obraz jest wyspecjalizowanym obrazem. 

Użyj identyfikatora definicji obrazu dla , aby utworzyć wystąpienia zestawu skalowania z `--image` najnowszej dostępnej wersji obrazu. Wystąpienia zestawu skalowania można również utworzyć z określonej wersji, podając identyfikator wersji obrazu dla `--image` . 

Utwórz zestaw skalowania *o nazwie myScaleSet* jako najnowszą wersję utworzonego wcześniej obrazu *myImageDefinition.*

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.


## <a name="test-your-scale-set"></a>Testowanie zestawu skalowania
Aby zezwolić na ruch internetowy w zestawie skalowania oraz upewnić się, że serwer internetowy działa prawidłowo, utwórz regułę modułu równoważenia obciążenia za pomocą polecenia [az network lb rule create](/cli/azure/network/lb/rule). W poniższym przykładzie pokazano tworzenie reguły o nazwie *myLoadBalancerRuleWeb*, która zezwala na ruch na porcie *TCP**80*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Aby zobaczyć, jak działa zestaw skalowania, uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [az network public-ip show](/cli/azure/network/public-ip). W poniższym przykładzie pokazano uzyskiwanie adresu IP dla modułu *myScaleSetLBPublicIP* utworzonego w ramach zestawu skalowania:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Wprowadź publiczny adres IP w przeglądarce internetowej. Zostanie wyświetlona domyślna strona serwera NGINX, jak pokazano w poniższym przykładzie:

![Serwer Nginx uruchomiony za pomocą niestandardowego obrazu maszyny wirtualnej](media/tutorial-use-custom-image-cli/default-nginx-website.png)



## <a name="share-the-gallery"></a>Udostępnianie galerii

Obrazy można udostępniać między subskrypcjami przy użyciu kontroli dostępu na podstawie ról (RBAC) platformy Azure. Obrazy można udostępniać w galerii, definicji obrazu lub wersji obrazu. Każdy użytkownik, który ma uprawnienia do odczytu do wersji obrazu, nawet w różnych subskrypcjach, będzie mógł wdrożyć maszynę wirtualną przy użyciu wersji obrazu.

Zalecamy udostępnianie innym użytkownikom na poziomie galerii. Aby uzyskać identyfikator obiektu galerii, użyj [az sig show](/cli/azure/sig#az_sig_show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Użyj identyfikatora obiektu jako zakresu wraz z adresem e-mail i az [role assignment create,](/cli/azure/role/assignment#az_role_assignment_create) aby udzielić użytkownikowi dostępu do galerii obrazów udostępnionych. Zastąp `<email-address>` i `<gallery iD>` własnymi informacjami.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Aby uzyskać więcej informacji na temat udostępniania zasobów przy użyciu kontroli RBAC platformy Azure, zobacz Dodawanie lub usuwanie przypisań ról platformy [Azure przy użyciu interfejsu wiersza polecenia platformy Azure.](../role-based-access-control/role-assignments-cli.md)


## <a name="clean-up-resources"></a>Czyszczenie zasobów
Aby usunąć zestaw skalowania i dodatkowe zasoby, usuń grupę zasobów i wszystkie jej zasoby za pomocą [az group delete](/cli/azure/group). Parametr `--no-wait` zwraca kontrolę do wiersza polecenia bez oczekiwania na zakończenie operacji. Parametr `--yes` potwierdza, że chcesz usunąć zasoby bez wyświetlania dodatkowego monitu.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Następne kroki
W tym samouczku omówiono tworzenie niestandardowego obrazu maszyny wirtualnej i używanie go z zestawami skalowania za pośrednictwem interfejsu wiersza polecenia platformy Azure:

> [!div class="checklist"]
> * Tworzenie galerii obrazów udostępnionych
> * Tworzenie wyspecjalizowanej definicji obrazu
> * Tworzenie wersji obrazu
> * Tworzenie zestawu skalowania na pomocą wyspecjalizowanego obrazu
> * Udostępnianie galerii obrazów

Przejdź do następnego samouczka, aby dowiedzieć się, jak wdrożyć aplikacje w zestawie skalowania.

> [!div class="nextstepaction"]
> [Deploy applications to your scale sets (Wdrażanie aplikacji w zestawach skalowania)](tutorial-install-apps-cli.md)