---
title: Usuwanie nietrwałe dla kontenerów (wersja zapoznawcza)
titleSuffix: Azure Storage
description: Nietrwałe usuwanie kontenerów (wersja zapoznawcza) zapewnia ochronę danych, dzięki czemu można łatwiej odzyskać dane, gdy są one błędnie modyfikowane lub usuwane przez aplikację lub przez innego użytkownika konta magazynu.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 54e703b096ea4e3572a6fc00aa6b7b2b99c4bcad
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800748"
---
# <a name="soft-delete-for-containers-preview"></a>Usuwanie nietrwałe dla kontenerów (wersja zapoznawcza)

Nietrwałe usuwanie kontenerów (wersja zapoznawcza) chroni dane przed przypadkowym lub złośliwym usunięciem. Po włączeniu usuwania nietrwałego kontenerów dla konta magazynu wszystkie usunięte kontenery i ich zawartość są przechowywane w usłudze Azure Storage przez określony przez Ciebie okres. W okresie przechowywania można przywrócić wcześniej usunięte kontenery. Przywrócenie kontenera przywraca wszystkie obiekty blob znajdujące się w tym kontenerze w momencie jego usunięcia.

Aby kompleksowo chronić dane obiektów blob, firma Microsoft zaleca włączenie następujących funkcji ochrony danych:

- Usuwanie nietrwałe kontenera w celu przywrócenia kontenera, który został usunięty. Aby dowiedzieć się, jak włączyć usuwanie nietrwałe kontenera, zobacz [Włączanie i zarządzanie usuwaniem nietrwałym dla kontenerów](soft-delete-container-enable.md).
- Przechowywanie wersji obiektów BLOB w celu automatycznego zachowywania poprzednich wersji obiektu BLOB. Po włączeniu obsługi wersji obiektów BLOB można przywrócić wcześniejszą wersję obiektu BLOB, aby odzyskać dane, jeśli są one błędnie modyfikowane lub usuwane. Aby dowiedzieć się, jak włączyć obsługę wersji obiektów blob, zobacz [Włączanie obsługi wersji obiektów blob i zarządzanie nimi](versioning-enable.md).
- Usuwanie nietrwałego obiektu BLOB w celu przywrócenia usuniętego obiektu BLOB lub wersji. Aby dowiedzieć się, jak włączyć usuwanie nietrwałe obiektów blob, zobacz [Włączanie i zarządzanie nietrwałego usuwania dla obiektów BLOB](soft-delete-blob-enable.md).

> [!IMPORTANT]
> Usuwanie nietrwałe kontenera jest obecnie w **wersji zapoznawczej**. Zapoznaj się z [dodatkowymi postanowieniami dotyczącymi](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych w Microsoft Azure wersjach zapoznawczych, które mają zastosowanie do funkcji platformy Azure w wersjach beta, Preview lub innych, które nie zostały jeszcze ogólnie udostępnione.

## <a name="how-container-soft-delete-works"></a>Jak działa usuwanie nietrwałe kontenera

Po włączeniu usuwania nietrwałego kontenera można określić okres przechowywania usuniętych kontenerów z przedziału od 1 do 365 dni. Domyślny okres przechowywania wynosi 7 dni. W okresie przechowywania można odzyskać usunięty kontener, wywołując operację **przywracania kontenera** .

Podczas przywracania kontenera są również przywracane obiekty blob kontenera i wszystkie wersje obiektów BLOB. Można jednak używać tylko nietrwałego usuwania kontenera do przywracania obiektów blob, jeśli kontener został usunięty. Aby przywrócić usunięty obiekt BLOB, gdy jego kontener nadrzędny nie został usunięty, należy użyć usuwania nietrwałego obiektu BLOB lub przechowywania wersji obiektów BLOB.

> [!WARNING]
> Usuwanie nietrwałe kontenera może przywrócić tylko całe kontenery i obiekty blob, które znajdują się w czasie usuwania. Nie można przywrócić usuniętego obiektu BLOB w kontenerze za pomocą usuwania nietrwałego kontenera.

Na poniższym diagramie przedstawiono sposób przywracania usuniętego kontenera, gdy jest włączone usuwanie nietrwałe kontenera:

:::image type="content" source="media/soft-delete-container-overview/container-soft-delete-diagram.png" alt-text="Diagram przedstawiający sposób przywrócenia kontenera usuwania nietrwałego":::

Po przywróceniu kontenera można przywrócić jego oryginalną nazwę, jeśli ta nazwa nie została ponownie użyta. Jeśli nazwa oryginalnego kontenera została użyta, można przywrócić kontener z nową nazwą.

Po upływie okresu przechowywania kontener zostanie trwale usunięty z usługi Azure Storage i nie będzie można go odzyskać. Zegar jest uruchamiany w okresie przechowywania w momencie usunięcia kontenera. Okres przechowywania można zmienić w dowolnym momencie, ale należy pamiętać, że zaktualizowany okres przechowywania dotyczy tylko nowo usuniętych kontenerów. Usunięte wcześniej kontenery zostaną trwale usunięte na podstawie okresu przechowywania, który obowiązywał w momencie usunięcia kontenera.

Wyłączenie usuwania nietrwałego kontenera nie powoduje trwałego usunięcia kontenerów, które zostały wcześniej usunięte. Wszystkie nietrwałe kontenery usunięte zostaną trwale usunięte po upływie okresu przechowywania, który obowiązywał w momencie usunięcia kontenera.

> [!IMPORTANT]
> Usuwanie nietrwałe kontenera nie chroni przed usunięciem konta magazynu, ale tylko w odniesieniu do usuwania kontenerów na tym koncie. Aby chronić konto magazynu przed usunięciem, należy skonfigurować blokadę zasobu konta magazynu. Aby uzyskać więcej informacji na temat blokowania zasobów Azure Resource Manager, zobacz [Zablokuj zasoby, aby zapobiec nieoczekiwanym zmianom](../../azure-resource-manager/management/lock-resources.md).

## <a name="about-the-preview"></a>Informacje o wersji zapoznawczej

Usuwanie nietrwałe kontenera jest dostępne w wersji zapoznawczej we wszystkich regionach świadczenia usługi Azure.

Wersja 2019-12-12 lub nowsza interfejsu API REST usługi Azure Storage obsługuje usuwanie nietrwałe kontenera.

### <a name="storage-account-support"></a>Obsługa kont magazynu

Usuwanie nietrwałe kontenera jest dostępne dla następujących typów kont magazynu:

- Konto magazynu ogólnego przeznaczenia w wersji 2 i w wersji 1
- Blokuj konta magazynu obiektów BLOB
- Konta usługi Blob Storage

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
