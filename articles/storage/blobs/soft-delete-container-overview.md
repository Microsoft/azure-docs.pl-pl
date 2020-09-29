---
title: Usuwanie nietrwałe dla kontenerów (wersja zapoznawcza)
titleSuffix: Azure Storage
description: Nietrwałe usuwanie kontenerów (wersja zapoznawcza) zapewnia ochronę danych, dzięki czemu można łatwiej odzyskać dane, gdy są one błędnie modyfikowane lub usuwane przez aplikację lub przez innego użytkownika konta magazynu.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/25/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 3c6aa408ab3e89c367fca0701773b35fb9b41bb8
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91460802"
---
# <a name="soft-delete-for-containers-preview"></a>Usuwanie nietrwałe dla kontenerów (wersja zapoznawcza)

Nietrwałe usuwanie kontenerów (wersja zapoznawcza) chroni dane przed przypadkowym lub błędnym modyfikacją lub usunięciem. Po włączeniu usuwania nietrwałego kontenera dla konta magazynu wszystkie usunięte kontenery i ich zawartość są przechowywane w usłudze Azure Storage przez określony okres. W okresie przechowywania można przywrócić wcześniej usunięte kontenery i wszystkie obiekty blob w nich.

Aby kompleksowo chronić dane obiektów blob, firma Microsoft zaleca włączenie następujących funkcji ochrony danych:

- Usuwanie nietrwałe kontenera, aby chronić przed przypadkowym usunięciem lub zastąpieniem kontenera. Aby dowiedzieć się, jak włączyć usuwanie nietrwałe kontenera, zobacz [Włączanie i zarządzanie usuwaniem nietrwałym dla kontenerów](soft-delete-container-enable.md).
- Usuwanie nietrwałe obiektów blob, aby chronić przed przypadkowym usunięciem lub zastępowaniem pojedynczego obiektu BLOB. Aby dowiedzieć się, jak włączyć usuwanie nietrwałe obiektów blob, zobacz [usuwanie nietrwałe dla obiektów BLOB](soft-delete-blob-overview.md).
- Przechowywanie wersji obiektów BLOB w celu automatycznego zachowywania poprzednich wersji obiektu BLOB. Po włączeniu obsługi wersji obiektów BLOB można przywrócić wcześniejszą wersję obiektu BLOB, aby odzyskać dane, jeśli są one błędnie modyfikowane lub usuwane. Aby dowiedzieć się, jak włączyć obsługę wersji obiektów blob, zobacz [Włączanie obsługi wersji obiektów blob i zarządzanie nimi](versioning-enable.md).

> [!WARNING]
> Nie można cofnąć usunięcia konta magazynu. Usuwanie nietrwałe nie chroni przed usunięciem konta magazynu. Aby zapobiec przypadkowemu usunięciu konta magazynu, Skonfiguruj blokadę **CannotDelete** dla zasobu konta magazynu. Aby uzyskać więcej informacji na temat blokowania zasobów platformy Azure, zobacz [blokowanie zasobów, aby zapobiec nieoczekiwanym zmianom](../../azure-resource-manager/management/lock-resources.md).

## <a name="how-container-soft-delete-works"></a>Jak działa usuwanie nietrwałe kontenera

Po włączeniu usuwania nietrwałego kontenera można określić okres przechowywania usuniętych kontenerów z przedziału od 1 do 365 dni. Domyślny okres przechowywania wynosi 7 dni. W okresie przechowywania można odzyskać usunięty kontener, wywołując operację **usuwania kontenera** .

Po przywróceniu kontenera można przywrócić jego oryginalną nazwę, jeśli ta nazwa nie została ponownie użyta. Jeśli nazwa oryginalnego kontenera została użyta, można przywrócić kontener z nową nazwą.

Po upływie okresu przechowywania kontener zostanie trwale usunięty z usługi Azure Storage i nie będzie można go odzyskać. Zegar jest uruchamiany w okresie przechowywania w momencie usunięcia kontenera. Okres przechowywania można zmienić w dowolnym momencie, ale należy pamiętać, że zaktualizowany okres przechowywania dotyczy tylko nowo usuniętych kontenerów. Usunięte wcześniej kontenery zostaną trwale usunięte na podstawie okresu przechowywania, który obowiązywał w momencie usunięcia kontenera.

Wyłączenie usuwania nietrwałego kontenera nie powoduje trwałego usunięcia kontenerów, które zostały wcześniej usunięte. Wszystkie nietrwałe kontenery usunięte zostaną trwale usunięte po upływie okresu przechowywania, który obowiązywał w momencie usunięcia kontenera.

## <a name="about-the-preview"></a>Informacje o wersji zapoznawczej

Usuwanie nietrwałe kontenera jest dostępne w wersji zapoznawczej we wszystkich publicznych regionach świadczenia usługi Azure.

> [!IMPORTANT]
> Wersja zapoznawcza usuwania nietrwałego kontenera jest przeznaczona wyłącznie do użytku nieprodukcyjnego. Umowy dotyczące poziomu usług produkcyjnych (umowy SLA) nie są obecnie dostępne.

W wersji 2019-12-12 i nowszej interfejsu API REST usługi Azure Storage obsługiwane jest usuwanie nietrwałe kontenera.

### <a name="storage-account-support"></a>Obsługa kont magazynu

Usuwanie nietrwałe kontenera jest dostępne dla następujących typów kont magazynu:

- Konta magazynu ogólnego przeznaczenia w wersji 2
- Blokuj konta magazynu obiektów BLOB
- Konta usługi Blob Storage

Jeśli konto magazynu jest kontem ogólnego przeznaczenia w wersji 1, użyj Azure Portal, aby przeprowadzić uaktualnienie do konta ogólnego przeznaczenia w wersji 2. Aby uzyskać więcej informacji na temat kont magazynu, zobacz [Omówienie konta usługi Azure Storage](../common/storage-account-overview.md).

Obsługiwane są również konta magazynu z hierarchiczną przestrzenią nazw włączoną do użycia z Azure Data Lake Storage Gen2.

### <a name="register-for-the-preview"></a>Zarejestruj się w wersji zapoznawczej

Aby zarejestrować się w wersji zapoznawczej usuwania nietrwałego kontenera, użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure w celu przesłania żądania zarejestrowania tej funkcji w ramach subskrypcji. Po zatwierdzeniu żądania można włączyć funkcję usuwania nietrwałego kontenera przy użyciu dowolnego nowego lub istniejącego konta programu ogólnego przeznaczenia w wersji 2, BLOB Storage lub Premium Storage.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby zarejestrować się w programie PowerShell, wywołaj polecenie [register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

```powershell
# Register for container soft delete (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName ContainerSoftDelete

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zarejestrować się w interfejsie wiersza polecenia platformy Azure, wywołaj polecenie [AZ Feature Register](/cli/azure/feature#az-feature-register) .

```azurecli
az feature register --namespace Microsoft.Storage --name ContainerSoftDelete
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>Sprawdź stan rejestracji

Aby sprawdzić stan rejestracji, użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby sprawdzić stan rejestracji w programie PowerShell, wywołaj polecenie [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName ContainerSoftDelete
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby sprawdzić stan rejestracji w interfejsie wiersza polecenia platformy Azure, wywołaj polecenie [AZ Feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage --name ContainerSoftDelete
```

---

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Nie ma dodatkowej opłaty za włączenie usuwania nietrwałego kontenera. Opłaty za dane w nietrwałe usunięte kontenery są naliczane według stawki za aktywne dane.

## <a name="next-steps"></a>Następne kroki

- [Konfiguruj usuwanie nietrwałe kontenera](soft-delete-container-enable.md)
- [Usuwanie nietrwałe dla obiektów blob](soft-delete-blob-overview.md)
- [Przechowywanie wersji obiektów BLOB](versioning-overview.md)
