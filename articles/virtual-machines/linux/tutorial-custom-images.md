---
title: Samouczek — Tworzenie niestandardowych obrazów maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure
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
ms.openlocfilehash: 40ec86030e136a06fe240e473a469681dcfa7c4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557765"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Samouczek: tworzenie niestandardowego obrazu maszyny wirtualnej na platformie Azure za pomocą interfejsu wiersza polecenia platformy Azure

Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe mogą służyć do ładowania początkowego konfiguracji, na przykład do wstępnego ładowania aplikacji, konfiguracji aplikacji i innych konfiguracji systemu operacyjnego. W tym samouczku utworzysz własny niestandardowy obraz maszyny wirtualnej platformy Azure. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie galerii obrazów udostępnionych
> * Tworzenie definicji obrazu
> * Tworzenie wersji obrazu
> * Tworzenie maszyny wirtualnej na podstawie obrazu 
> * Udostępnianie galerii obrazów


W tym samouczku jest używany interfejs wiersza polecenia w [Azure Cloud Shell](../../cloud-shell/overview.md), który jest stale aktualizowany do najnowszej wersji. Aby otworzyć Cloud Shell, wybierz opcję **Wypróbuj** z góry dowolnego bloku kodu.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.4.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="overview"></a>Omówienie

[Galeria obrazów udostępnionych](../shared-image-galleries.md) upraszcza udostępnianie obrazów niestandardowych w całej organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe mogą służyć do ładowania początkowego konfiguracji, na przykład do wstępnego ładowania aplikacji, konfiguracji aplikacji i innych konfiguracji systemu operacyjnego. 

Galeria obrazów udostępnionych umożliwia udostępnianie niestandardowych obrazów maszyn wirtualnych innym osobom. Wybierz obrazy, które chcesz udostępnić, które regiony mają być dostępne w programie, oraz użytkowników, którym chcesz je udostępnić. 

Funkcja galerii obrazów udostępnionych ma wiele typów zasobów:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W poniższych krokach wyjaśniono szczegółowo, jak na podstawie istniejącej maszyny wirtualnej utworzyć obraz niestandardowy do ponownego użycia, za pomocą którego można tworzyć nowe wystąpienia maszyn wirtualnych.

Do utworzenia przykładu przedstawionego w tym samouczku potrzebna jest istniejąca maszyna wirtualna. W razie potrzeby można wyświetlić [Przewodnik Szybki start interfejsu wiersza polecenia](quick-create-cli.md) , aby utworzyć maszynę wirtualną do użycia w tym samouczku. Podczas pracy z samouczkiem Zastąp nazwy zasobów, w razie konieczności.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

## <a name="create-an-image-gallery"></a>Tworzenie galerii obrazów 

Galeria obrazów jest podstawowym zasobem używanym do włączania udostępniania obrazu. 

Dozwolone znaki w nazwie galerii to wielkie lub małe litery, cyfry, kropki i kropki. Nazwa galerii nie może zawierać kresek.   Nazwy galerii muszą być unikatowe w ramach subskrypcji. 

Utwórz galerię obrazów przy użyciu polecenia [AZ SIG Create](/cli/azure/sig#az-sig-create). Poniższy przykład tworzy grupę zasobów o nazwie Galeria o nazwie *myGalleryRG* w *regionie Wschodnie stany USA* i galerię o nazwie Moja *Gallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="get-information-about-the-vm"></a>Pobierz informacje o maszynie wirtualnej

Możesz wyświetlić listę maszyn wirtualnych, które są dostępne za pomocą polecenia [AZ VM list](/cli/azure/vm#az-vm-list). 

```azurecli-interactive
az vm list --output table
```

Gdy znasz nazwę maszyny wirtualnej i grupę zasobów, w której znajduje się, Pobierz identyfikator maszyny wirtualnej za pomocą polecenia [AZ VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view). 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```

Skopiuj identyfikator maszyny wirtualnej do użycia później.

## <a name="create-an-image-definition"></a>Tworzenie definicji obrazu

Definicje obrazów tworzą logiczne grupowanie dla obrazów. Są one używane do zarządzania informacjami o wersjach obrazu, które są w nich tworzone. 

Nazwy definicji obrazów mogą składać się z wielkich lub małych liter, cyfr, kropek, kresek i kropek. 

Aby uzyskać więcej informacji na temat wartości, które można określić dla definicji obrazu, zobacz [definicje obrazu](../shared-image-galleries.md#image-definitions).

Utwórz definicję obrazu w galerii za pomocą polecenia [AZ SIG Image-Definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create). 

W tym przykładzie definicja obrazu ma nazwę *myImageDefinition* i jest dla [WYSPECJALIZOWANEGO](../shared-image-galleries.md#generalized-and-specialized-images) obrazu systemu operacyjnego Linux. 

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

Skopiuj identyfikator definicji obrazu z danych wyjściowych do użycia później.

## <a name="create-the-image-version"></a>Utwórz wersję obrazu

Utwórz wersję obrazu z maszyny wirtualnej za pomocą polecenia [AZ Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create).  

Dozwolone znaki wersji obrazu to liczby i kropki. Liczba musi należeć do zakresu 32-bitowej liczby całkowitej. Format: *MajorVersion*. *MinorVersion*. *Poprawka*.

W tym przykładzie wersja naszego obrazu to *1.0.0* , a firma Microsoft tworzy 2 repliki w regionie *zachodnie Stany* USA, 1 replikę w regionie *Południowo-środkowe stany USA* i 1 replikę w regionie *Wschodnie stany USA 2* , korzystając z magazynu Strefowo nadmiarowego. Regiony replikacji muszą zawierać region, w którym znajduje się źródłowa maszyna wirtualna.

Zastąp wartość `--managed-image` w tym przykładzie identyfikatorem maszyny wirtualnej z poprzedniego kroku.

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
> Musisz poczekać na zakończenie kompilowania i replikowania wersji obrazu, aby można było użyć tego samego obrazu zarządzanego do utworzenia innej wersji obrazu.
>
> Możesz również przechowywać obraz w magazynie Premiun przez dodanie `--storage-account-type  premium_lrs` lub [nadmiarowy magazyn stref](../../storage/common/storage-redundancy.md) przez dodanie `--storage-account-type  standard_zrs` go podczas tworzenia wersji obrazu.
>

 
## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [AZ VM Create](/cli/azure/vm#az-vm-create) przy użyciu--wyspecjalizowanego parametru, aby wskazać obraz, jest to wyspecjalizowany obraz. 

Użyj identyfikatora definicji obrazu dla programu, `--image` Aby utworzyć maszynę wirtualną na podstawie najnowszej wersji dostępnego obrazu. Możesz również utworzyć maszynę wirtualną na podstawie określonej wersji, podając identyfikator wersji obrazu dla `--image` . 

W tym przykładzie tworzymy maszynę wirtualną na podstawie najnowszej wersji obrazu *myImageDefinition* .

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```

## <a name="share-the-gallery"></a>Udostępnianie galerii

Możesz udostępniać obrazy między subskrypcjami przy użyciu funkcji kontroli dostępu opartej na rolach (Azure RBAC). Możesz udostępniać obrazy w galerii, definicji obrazu lub wersji obrazu Leve. Każdy użytkownik, który ma uprawnienia do odczytu wersji obrazu, nawet między subskrypcjami, będzie mógł wdrożyć maszynę wirtualną przy użyciu wersji obrazu.

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

Aby uzyskać więcej informacji o sposobach udostępniania zasobów przy użyciu usługi Azure RBAC, zobacz [Dodawanie i usuwanie przypisań ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md).

## <a name="azure-image-builder"></a>Konstruktor obrazów platformy Azure

Platforma Azure oferuje również usługę, która jest oparta na pakiecie [Konstruktor obrazów maszyn wirtualnych platformy Azure](../image-builder-overview.md). Wystarczy opisać dostosowania w szablonie i obsłużyć Tworzenie obrazu. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku został utworzony obraz niestandardowy maszyny wirtualnej. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie galerii obrazów udostępnionych
> * Tworzenie definicji obrazu
> * Tworzenie wersji obrazu
> * Tworzenie maszyny wirtualnej na podstawie obrazu 
> * Udostępnianie galerii obrazów

Przejdź do następnego samouczka, aby dowiedzieć się więcej o maszynach wirtualnych o wysokiej dostępności.

> [!div class="nextstepaction"]
> [Tworzenie maszyn wirtualnych o wysokiej dostępności](tutorial-availability-sets.md)