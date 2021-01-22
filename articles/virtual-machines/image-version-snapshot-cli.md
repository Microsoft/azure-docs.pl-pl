---
title: Interfejs wiersza polecenia — Tworzenie obrazu na podstawie migawki lub dysku zarządzanego w galerii obrazów udostępnionych
description: Dowiedz się, jak utworzyć obraz na podstawie migawki lub dysku zarządzanego w galerii obrazów udostępnionych przy użyciu interfejsu wiersza polecenia platformy Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 9cfb1fe6d2050a63070e9c21e4b8c3ef59efcb15
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682685"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-the-azure-cli"></a>Tworzenie obrazu z dysku zarządzanego lub migawki w udostępnionej galerii obrazów przy użyciu interfejsu wiersza polecenia platformy Azure

Jeśli masz istniejącą migawkę lub dysk zarządzany, który chcesz migrować do galerii obrazów udostępnionych, możesz utworzyć obraz udostępnionej galerii obrazów bezpośrednio z dysku zarządzanego lub migawki. Po przetestowaniu nowego obrazu można usunąć źródłowy dysk zarządzany lub migawkę. Możesz również utworzyć obraz z dysku zarządzanego lub migawki w udostępnionej galerii obrazów przy użyciu [Azure PowerShell](image-version-snapshot-powershell.md).

Obrazy w galerii obrazów mają dwa składniki, które zostaną utworzone w tym przykładzie:
- **Definicja obrazu** przenosi informacje o obrazie i wymaganiach dotyczących korzystania z niego. Obejmuje to zarówno system Windows, jak i Linux, wyspecjalizowane lub uogólnione informacje o wersji oraz minimalne i maksymalne wymagania dotyczące pamięci. Jest to definicja typu obrazu. 
- **Wersja obrazu** jest używana do tworzenia maszyny wirtualnej w przypadku korzystania z galerii obrazów udostępnionych. Dla danego środowiska można mieć wiele wersji obrazu. Podczas tworzenia maszyny wirtualnej wersja obrazu jest używana do tworzenia nowych dysków dla maszyny wirtualnej. Wersje obrazów można wielokrotnie używać.


## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby ukończyć ten artykuł, musisz mieć migawkę lub dysk zarządzany. 

Jeśli chcesz dołączyć dysk danych, rozmiar dysku danych nie może być większy niż 1 TB.

W trakcie pracy z tym artykułem Zastąp nazwy zasobów, w razie konieczności.

## <a name="find-the-snapshot-or-managed-disk"></a>Znajdowanie migawki lub dysku zarządzanego 

Listę migawek, które są dostępne w grupie zasobów, można wyświetlić za pomocą polecenia [AZ Snapshot list](/cli/azure/snapshot#az-snapshot-list). 

```azurecli-interactive
az snapshot list --query "[].[name, id]" -o tsv
```

Można również użyć dysku zarządzanego zamiast migawki. Aby uzyskać dysk zarządzany, użyj polecenie [AZ Disk list](/cli/azure/disk#az-disk-list). 

```azurecli-interactive
az disk list --query "[].[name, id]" -o tsv
```

Po umieszczeniu identyfikatora migawki lub dysku zarządzanego i przypisaniu go do zmiennej o nazwie `$source` do użycia później.

Możesz użyć tego samego procesu do pobrania dowolnego dysku z danymi, który ma zostać uwzględniony w obrazie. Należy przypisać je do zmiennych, a następnie użyć tych zmiennych później podczas tworzenia wersji obrazu.


## <a name="find-the-gallery"></a>Znajdź galerię

Aby można było utworzyć definicję obrazu, potrzebne będą informacje o galerii obrazów.

Wyświetl informacje o dostępnych galeriach obrazów przy użyciu polecenia [AZ SIG list](/cli/azure/sig#az-sig-list). Zanotuj nazwę galerii, w której Grupa zasobów jest używana przez galerię do późniejszego użycia.

```azurecli-interactive 
az sig list -o table
```


## <a name="create-an-image-definition"></a>Tworzenie definicji obrazu

Definicje obrazów tworzą logiczne grupowanie dla obrazów. Są one używane do zarządzania informacjami o obrazie. Nazwy definicji obrazów mogą składać się z wielkich lub małych liter, cyfr, kropek, kresek i kropek. 

Podczas tworzenia definicji obrazu upewnij się, że zawiera on wszystkie prawidłowe informacje. W tym przykładzie zakładamy, że migawka lub dysk zarządzany pochodzą z maszyny wirtualnej, która jest używana, i nie został uogólniony. Jeśli na dysku zarządzanym lub migawek utworzono uogólniony system operacyjny (po uruchomieniu programu Sysprep dla systemu Windows lub [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` lub `-deprovision+user` Linux), Zmień wartość `-OsState` na `generalized` . 

Aby uzyskać więcej informacji na temat wartości, które można określić dla definicji obrazu, zobacz [definicje obrazu](./shared-image-galleries.md#image-definitions).

Utwórz definicję obrazu w galerii za pomocą polecenia [AZ SIG Image-Definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

W tym przykładzie definicja obrazu ma nazwę *myImageDefinition* i jest dla [WYSPECJALIZOWANEGO](./shared-image-galleries.md#generalized-and-specialized-images) obrazu systemu operacyjnego Linux. Aby utworzyć definicję dla obrazów przy użyciu systemu operacyjnego Windows, użyj polecenia `--os-type Windows` . 

W tym przykładzie Galeria nosi nazwę "Moja *Galeria*", znajduje się w grupie zasobów *myGalleryRG* , a nazwa definicji obrazu będzie *mImageDefinition*.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>Utwórz wersję obrazu

Utwórz wersję obrazu za pomocą polecenia [AZ Image Gallery Create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create). 

Dozwolone znaki wersji obrazu to liczby i kropki. Liczba musi należeć do zakresu 32-bitowej liczby całkowitej. Format: *MajorVersion*. *MinorVersion*. *Poprawka*.

W tym przykładzie wersja naszego obrazu to *1.0.0* , a firma Microsoft chce utworzyć 1 replikę w regionie *Południowo-środkowe stany USA* i 1 replikę w regionie *Wschodnie stany USA 2* , korzystając z magazynu Strefowo nadmiarowego. Podczas wybierania regionów docelowych na potrzeby replikacji należy pamiętać, że należy również uwzględnić region *źródłowy* dysku zarządzanego lub migawki jako cel replikacji.

Przekaż identyfikator migawki lub dysku zarządzanego w `--os-snapshot` parametrze.


```azurecli-interactive 
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --os-snapshot $source
```

Jeśli chcesz uwzględnić dyski danych w obrazie, musisz uwzględnić zarówno `--data-snapshot-luns` parametr ustawiony jako numer LUN, jak i `--data-snapshots` zestaw jako identyfikator dysku lub migawki danych.

> [!NOTE]
> Musisz poczekać na zakończenie kompilowania i replikowania wersji obrazu, aby można było użyć tego samego obrazu zarządzanego do utworzenia innej wersji obrazu.
>
> Możesz również przechowywać wszystkie repliki wersji obrazu w [magazynie strefowo nadmiarowy](../storage/common/storage-redundancy.md) przez dodanie `--storage-account-type standard_zrs` podczas tworzenia wersji obrazu.
>

## <a name="next-steps"></a>Następne kroki

Utwórz maszynę wirtualną na podstawie [wyspecjalizowanej wersji obrazu](vm-specialized-image-version-cli.md).

Aby uzyskać informacje o sposobach dostarczania informacji o planie zakupu, zobacz temat [dostarczanie informacji o planie zakupu portalu Azure Marketplace podczas tworzenia obrazów](marketplace-images.md).