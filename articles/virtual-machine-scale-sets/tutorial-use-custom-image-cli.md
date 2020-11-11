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
ms.openlocfilehash: 62cf7c979be83454ae2433befcdbf4f5d8e5524f
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516547"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Samouczek: tworzenie niestandardowego obrazu i używanie go dla zestawów skalowania maszyn wirtualnych za pośrednictwem interfejsu wiersza polecenia platformy Azure
Podczas tworzenia zestawu skalowania należy wskazać obraz używany do wdrożenia wystąpień maszyn wirtualnych. Aby zmniejszyć liczbę zadań wykonywanych po wdrożeniu wystąpień maszyn wirtualnych, można użyć niestandardowego obrazu maszyny wirtualnej. Niestandardowy obraz maszyny wirtualnej obejmuje wszystkie wymagane instalacje i konfiguracje aplikacji. Wszystkie wystąpienia maszyn wirtualnych utworzone w zestawie skalowania używają niestandardowego obrazu maszyny wirtualnej i są gotowe do obsługi ruchu aplikacji. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie galerii obrazów udostępnionych
> * Tworzenie wyspecjalizowanej definicji obrazu
> * Tworzenie wersji obrazu
> * Tworzenie zestawu skalowania na podstawie obrazu specjalistycznego
> * Udostępnianie galerii obrazów


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2.4.0 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="overview"></a>Omówienie

[Galeria obrazów udostępnionych](shared-image-galleries.md) upraszcza udostępnianie obrazów niestandardowych w całej organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe mogą służyć do ładowania początkowego konfiguracji, na przykład do wstępnego ładowania aplikacji, konfiguracji aplikacji i innych konfiguracji systemu operacyjnego. 

Galeria obrazów udostępnionych umożliwia udostępnianie niestandardowych obrazów maszyn wirtualnych innym osobom. Wybierz obrazy, które chcesz udostępnić, które regiony mają być dostępne w programie, oraz użytkowników, którym chcesz je udostępnić. 

## <a name="create-and-configure-a-source-vm"></a>Tworzenie i konfigurowanie źródłowej maszyny wirtualnej

Najpierw utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group), a następnie utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Ta maszyna wirtualna jest następnie używana jako źródło obrazu. Poniższy przykład obejmuje tworzenie maszyny wirtualnej *myVM* w grupie zasobów *myResourceGroup* :

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
> **Identyfikator** maszyny wirtualnej jest wyświetlany w danych wyjściowych polecenia [AZ VM Create](/cli/azure/vm) . Skopiuj ten zwrócono komunikatu bezpiecznie, aby można było go użyć w dalszej części tego samouczka.

Publiczny adres IP maszyny wirtualnej jest również pokazywany w danych wyjściowych polecenia [AZ VM Create](/cli/azure/vm) . Za pośrednictwem protokołu SSH połącz się z publicznym adresem IP maszyny wirtualnej w następujący sposób:

```console
ssh azureuser@<publicIpAddress>
```

Aby dostosować maszynę wirtualną, należy zainstalować podstawowy serwer internetowy. Dzięki temu wdrożona maszyna wirtualna będzie miała wszystkie pakiety wymagane do uruchamiania aplikacji internetowej. Zainstaluj serwer *NGINX* za pomocą polecenia `apt-get` w następujący sposób:

```bash
sudo apt-get install -y nginx
```

Gdy skończysz, wpisz polecenie, `exit` Aby rozłączyć połączenie SSH.

## <a name="create-an-image-gallery"></a>Tworzenie galerii obrazów 

Galeria obrazów jest podstawowym zasobem używanym do włączania udostępniania obrazu. 

Dozwolone znaki w nazwie galerii to wielkie lub małe litery, cyfry, kropki i kropki. Nazwa galerii nie może zawierać kresek.   Nazwy galerii muszą być unikatowe w ramach subskrypcji. 

Utwórz galerię obrazów przy użyciu polecenia [AZ SIG Create](/cli/azure/sig#az-sig-create). Poniższy przykład tworzy grupę zasobów o nazwie Galeria o nazwie *myGalleryRG* w *regionie Wschodnie stany USA* i galerię o nazwie Moja *Gallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Tworzenie definicji obrazu

Definicje obrazów tworzą logiczne grupowanie dla obrazów. Są one używane do zarządzania informacjami o wersjach obrazu, które są w nich tworzone. 

Nazwy definicji obrazów mogą składać się z wielkich lub małych liter, cyfr, kropek, kresek i kropek. 

Upewnij się, że definicja obrazu jest odpowiednim typem. W przypadku uogólnionej maszyny wirtualnej (przy użyciu programu Sysprep dla systemu Windows lub waagent-anulowania aprowizacji w systemie Linux) należy utworzyć uogólnioną definicję obrazu przy użyciu programu `--os-state generalized` . Jeśli chcesz użyć maszyny wirtualnej bez usuwania istniejących kont użytkowników, Utwórz wyspecjalizowaną definicję obrazu za pomocą polecenia `--os-state specialized` .

Aby uzyskać więcej informacji na temat wartości, które można określić dla definicji obrazu, zobacz [definicje obrazu](../virtual-machines/linux/shared-image-galleries.md#image-definitions).

Utwórz definicję obrazu w galerii za pomocą polecenia [AZ SIG Image-Definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

W tym przykładzie definicja obrazu ma nazwę *myImageDefinition* i jest dla [WYSPECJALIZOWANEGO](../virtual-machines/linux/shared-image-galleries.md#generalized-and-specialized-images) obrazu systemu operacyjnego Linux. Aby utworzyć definicję dla obrazów przy użyciu systemu operacyjnego Windows, użyj polecenia `--os-type Windows` . 

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
> **Identyfikator** definicji obrazu jest pokazywany w danych wyjściowych polecenia. Skopiuj ten zwrócono komunikatu bezpiecznie, aby można było go użyć w dalszej części tego samouczka.


## <a name="create-the-image-version"></a>Utwórz wersję obrazu

Utwórz wersję obrazu z maszyny wirtualnej za pomocą polecenia [AZ Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create).  

Dozwolone znaki wersji obrazu to liczby i kropki. Liczba musi należeć do zakresu 32-bitowej liczby całkowitej. Format: *MajorVersion*. *MinorVersion*. *Poprawka*.

W tym przykładzie wersja naszego obrazu to *1.0.0* , a firma Microsoft chce utworzyć 1 replikę w regionie *Południowo-środkowe stany USA* i 1 replikę w regionie *Wschodnie stany USA 2* . Regiony replikacji muszą zawierać region, w którym znajduje się źródłowa maszyna wirtualna.

Zastąp wartość `--managed-image` w tym przykładzie identyfikatorem maszyny wirtualnej z poprzedniego kroku.

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
> Musisz poczekać na zakończenie kompilowania i replikowania wersji obrazu, aby można było użyć tego samego obrazu zarządzanego do utworzenia innej wersji obrazu.
>
> Możesz również przechowywać obraz w usłudze Premium Storage przez dodanie `--storage-account-type  premium_lrs` lub [nadmiarowy magazyn stref](../storage/common/storage-redundancy.md) przez dodanie `--storage-account-type  standard_zrs` go podczas tworzenia wersji obrazu.
>




## <a name="create-a-scale-set-from-the-image"></a>Tworzenie zestawu skalowania na podstawie obrazu
Utwórz zestaw skalowania z wyspecjalizowanego obrazu przy użyciu [`az vmss create`](/cli/azure/vmss#az-vmss-create) . 

Utwórz zestaw skalowania przy użyciu [`az vmss create`](/cli/azure/vmss#az-vmss-create) parametru--wyspecjalizowanego w celu wskazania, że obraz jest wyspecjalizowanym obrazem. 

Użyj identyfikatora definicji obrazu do, `--image` Aby utworzyć wystąpienia zestawu skalowania z najnowszej wersji dostępnego obrazu. Możesz również utworzyć wystąpienia zestawu skalowania na podstawie określonej wersji, podając identyfikator wersji obrazu dla `--image` . 

Utwórz zestaw skalowania o nazwie *myScaleSet* Najnowsza wersja utworzonego wcześniej obrazu *myImageDefinition* .

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
Aby zezwolić na ruch internetowy w zestawie skalowania oraz upewnić się, że serwer internetowy działa prawidłowo, utwórz regułę modułu równoważenia obciążenia za pomocą polecenia [az network lb rule create](/cli/azure/network/lb/rule). W poniższym przykładzie pokazano tworzenie reguły o nazwie *myLoadBalancerRuleWeb* , która zezwala na ruch na porcie *TCP**80* :

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

Możesz udostępniać obrazy między subskrypcjami przy użyciu funkcji kontroli dostępu opartej na rolach (Azure RBAC). Możesz udostępniać obrazy w galerii, definicji obrazu lub wersji obrazu. Każdy użytkownik, który ma uprawnienia do odczytu wersji obrazu, nawet między subskrypcjami, będzie mógł wdrożyć maszynę wirtualną przy użyciu wersji obrazu.

Zalecamy udostępnianie innym użytkownikom na poziomie galerii. Aby uzyskać identyfikator obiektu galerii, użyj [AZ SIG show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Użyj identyfikatora obiektu jako zakresu wraz z adresem e-mail i [AZ role przypisanie Create](/cli/azure/role/assignment#az-role-assignment-create) , aby dać użytkownikowi dostęp do galerii obrazów udostępnionych. Zastąp `<email-address>` i `<gallery iD>` własnymi informacjami.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Aby uzyskać więcej informacji o sposobach udostępniania zasobów przy użyciu usługi Azure RBAC, zobacz [Dodawanie i usuwanie przypisań ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md).


## <a name="clean-up-resources"></a>Czyszczenie zasobów
Aby usunąć zestaw skalowania i dodatkowe zasoby, Usuń grupę zasobów i wszystkie jej zasoby za pomocą polecenie [AZ Group Delete](/cli/azure/group). Parametr `--no-wait` zwraca kontrolę do wiersza polecenia bez oczekiwania na zakończenie operacji. Parametr `--yes` potwierdza, że chcesz usunąć zasoby bez wyświetlania dodatkowego monitu.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Następne kroki
W tym samouczku omówiono tworzenie niestandardowego obrazu maszyny wirtualnej i używanie go z zestawami skalowania za pośrednictwem interfejsu wiersza polecenia platformy Azure:

> [!div class="checklist"]
> * Tworzenie galerii obrazów udostępnionych
> * Tworzenie wyspecjalizowanej definicji obrazu
> * Tworzenie wersji obrazu
> * Tworzenie zestawu skalowania na podstawie obrazu specjalistycznego
> * Udostępnianie galerii obrazów

Przejdź do następnego samouczka, aby dowiedzieć się, jak wdrożyć aplikacje w zestawie skalowania.

> [!div class="nextstepaction"]
> [Deploy applications to your scale sets (Wdrażanie aplikacji w zestawach skalowania)](tutorial-install-apps-cli.md)
