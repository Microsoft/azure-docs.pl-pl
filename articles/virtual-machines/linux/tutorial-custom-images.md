---
title: Samouczek — tworzenie niestandardowych obrazów maszyn wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure
description: Z tego samouczka dowiesz się, jak utworzyć niestandardowy obraz maszyny wirtualnej na platformie Azure za pomocą interfejsu wiersza polecenia platformy Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: 81bbf0b49919db68407a85b9ea2f731c5f8e1d91
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769881"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Samouczek: tworzenie niestandardowego obrazu maszyny wirtualnej na platformie Azure za pomocą interfejsu wiersza polecenia platformy Azure

Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe mogą służyć do ładowania początkowego konfiguracji, na przykład do wstępnego ładowania aplikacji, konfiguracji aplikacji i innych konfiguracji systemu operacyjnego. W tym samouczku utworzysz własny niestandardowy obraz maszyny wirtualnej platformy Azure. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie galerii obrazów udostępnionych
> * Tworzenie definicji obrazu
> * Tworzenie wersji obrazu
> * Tworzenie maszyny wirtualnej na pomocą obrazu 
> * Udostępnianie galerii obrazów


W tym samouczku używany jest interfejs wiersza [polecenia w Azure Cloud Shell](../../cloud-shell/overview.md), który jest stale aktualizowany do najnowszej wersji. Aby otworzyć Cloud Shell, wybierz pozycję **Wypróbuj** w górnej części dowolnego bloku kodu.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i używać go lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.4.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="overview"></a>Omówienie

Zestaw [Shared Image Gallery](../shared-image-galleries.md) upraszcza udostępnianie obrazów niestandardowych w całej organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe mogą służyć do ładowania początkowego konfiguracji, na przykład do wstępnego ładowania aplikacji, konfiguracji aplikacji i innych konfiguracji systemu operacyjnego. 

Interfejs Shared Image Gallery umożliwia udostępnianie niestandardowych obrazów maszyn wirtualnych innym osobom. Wybierz, które obrazy chcesz udostępnić, w których regionach chcesz je udostępnić i komu chcesz je udostępnić. 

Ta Shared Image Gallery ma wiele typów zasobów:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W poniższych krokach wyjaśniono szczegółowo, jak na podstawie istniejącej maszyny wirtualnej utworzyć obraz niestandardowy do ponownego użycia, za pomocą którego można tworzyć nowe wystąpienia maszyn wirtualnych.

Do utworzenia przykładu przedstawionego w tym samouczku potrzebna jest istniejąca maszyna wirtualna. W razie potrzeby możesz zobaczyć przewodnik Szybki start dotyczący [interfejsu wiersza](quick-create-cli.md) polecenia, aby utworzyć maszynę wirtualną do użycia na potrzeby tego samouczka. Podczas pracy z samouczkiem w razie potrzeby zastąp nazwy zasobów.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić aplikację Cloud Shell osobnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

## <a name="create-an-image-gallery"></a>Tworzenie galerii obrazów 

Galeria obrazów jest podstawowym zasobem używanym do włączania udostępniania obrazów. 

Dozwolone znaki nazwy galerii to wielkie lub małe litery, cyfry, kropki i kropki. Nazwa galerii nie może zawierać łączników.   Nazwy galerii muszą być unikatowe w ramach subskrypcji. 

Utwórz galerię obrazów za pomocą [narzędzia az sig create](/cli/azure/sig#az_sig_create). Poniższy przykład tworzy grupę zasobów o nazwie galeria o nazwie *myGalleryRG* w regionach *Wschodnie* usa oraz galerię o nazwie *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="get-information-about-the-vm"></a>Uzyskiwanie informacji o maszynie wirtualnej

Możesz wyświetlić listę maszyn wirtualnych dostępnych za pomocą narzędzia [az vm list](/cli/azure/vm#az_vm_list). 

```azurecli-interactive
az vm list --output table
```

Gdy znasz nazwę maszyny wirtualnej i grupę zasobów, w której się znajduje, uzyskaj identyfikator maszyny wirtualnej przy użyciu narzędzia [az vm get-instance-view.](/cli/azure/vm#az_vm_get_instance_view) 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```

Skopiuj identyfikator maszyny wirtualnej do późniejszego użycia.

## <a name="create-an-image-definition"></a>Tworzenie definicji obrazu

Definicje obrazów tworzą logiczne grupowanie obrazów. Są one używane do zarządzania informacjami o wersjach obrazów, które są w nich tworzone. 

Nazwy definicji obrazów mogą składać się z wielkich lub małych liter, cyfr, kropek, łączników i kropek. 

Aby uzyskać więcej informacji na temat wartości, które można określić dla definicji obrazu, zobacz [Definicje obrazów](../shared-image-galleries.md#image-definitions).

Utwórz definicję obrazu w galerii za pomocą [narzędzia az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create). 

W tym przykładzie definicja obrazu nosi nazwę *myImageDefinition* i jest dla [wyspecjalizowanego](../shared-image-galleries.md#generalized-and-specialized-images) obrazu systemu operacyjnego Linux. 

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

Skopiuj identyfikator definicji obrazu z danych wyjściowych do późniejszego użycia.

## <a name="create-the-image-version"></a>Tworzenie wersji obrazu

Utwórz wersję obrazu z maszyny wirtualnej przy użyciu [narzędzia az image gallery create-image-version.](/cli/azure/sig/image-version#az_sig_image_version_create)  

Dozwolone znaki dla wersji obrazu to cyfry i kropki. Liczby muszą znajdować się w zakresie 32-bitowej liczby całkowitej. Format: *MajorVersion*. *MinorVersion*. *Popraw .*

W tym przykładzie wersja naszego obrazu to *1.0.0* i zamierzamy utworzyć 2 repliki w regionie  Zachodnio-środkowe stany *USA,* 1 replikę w regionie Południowo-środkowe stany USA i 1 replikę w regionie Wschodnie stany USA *2* przy użyciu magazynu strefowo nadmiarowego. Regiony replikacji muszą zawierać region, w których znajduje się źródłowa maszyna wirtualna.

Zastąp wartość w `--managed-image` tym przykładzie identyfikatorem maszyny wirtualnej z poprzedniego kroku.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Zanim będzie można użyć tego samego obrazu zarządzanego do utworzenia innej wersji obrazu, należy poczekać na całkowite zakończenie tworzenia i replikowania wersji obrazu.
>
> Obraz można również przechowywać w magazynie lokalnym przez dodanie magazynu , lub magazynu strefowo nadmiarowego, dodając podczas `--storage-account-type  premium_lrs` tworzenia wersji [](../../storage/common/storage-redundancy.md) `--storage-account-type  standard_zrs` obrazu.
>

 
## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną przy użyciu [narzędzia az vm create,](/cli/azure/vm#az_vm_create) używając parametru --specialized, aby wskazać, że obraz jest wyspecjalizowanym obrazem. 

Użyj identyfikatora definicji obrazu dla , `--image` aby utworzyć maszynę wirtualną z najnowszej dostępnej wersji obrazu. Możesz również utworzyć maszynę wirtualną z określonej wersji, podając identyfikator wersji obrazu dla `--image` . 

W tym przykładzie tworzymy maszynę wirtualną z najnowszej wersji obrazu *myImageDefinition.*

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```

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

Aby uzyskać więcej informacji na temat udostępniania zasobów przy użyciu kontroli RBAC platformy Azure, zobacz Dodawanie lub usuwanie przypisań ról platformy [Azure przy użyciu interfejsu wiersza polecenia platformy Azure.](../../role-based-access-control/role-assignments-cli.md)

## <a name="azure-image-builder"></a>Konstruktor obrazów platformy Azure

Platforma Azure oferuje również usługę, zbudowaną na platformie Packer, [Azure VM Image Builder](../image-builder-overview.md). Po prostu opisz dostosowania w szablonie i obsłuży on tworzenie obrazu. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony obraz niestandardowy maszyny wirtualnej. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie galerii obrazów udostępnionych
> * Tworzenie definicji obrazu
> * Tworzenie wersji obrazu
> * Tworzenie maszyny wirtualnej na pomocą obrazu 
> * Udostępnianie galerii obrazów

Przejdź do następnego samouczka, aby dowiedzieć się więcej o maszynach wirtualnych o wysokiej dostępności.

> [!div class="nextstepaction"]
> [Tworzenie maszyn wirtualnych o wysokiej dostępności](tutorial-availability-sets.md)