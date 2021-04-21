---
title: Usuwanie nie softne dla kontenerów (wersja zapoznawcza)
titleSuffix: Azure Storage
description: Usuwanie nie softne dla kontenerów (wersja zapoznawcza) chroni dane, dzięki czemu można łatwiej odzyskać dane, gdy zostaną błędnie zmodyfikowane lub usunięte przez aplikację lub innego użytkownika konta magazynu.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 2efd673d26231e83d820f7971a740d06e9b2a1d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785419"
---
# <a name="soft-delete-for-containers-preview"></a>Usuwanie nie softne dla kontenerów (wersja zapoznawcza)

Usuwanie nieumyślne dla kontenerów (wersja zapoznawcza) chroni dane przed przypadkowym lub złośliwym usunięciem. Po włączeniu usuwania nie softowego kontenera dla konta magazynu usunięty kontener i jego zawartość są przechowywane w usłudze Azure Storage przez określony okres. W okresie przechowywania można przywrócić wcześniej usunięte kontenery. Przywrócenie kontenera przywraca wszystkie obiekty blob znajdujące się w tym kontenerze w momencie jego usunięcia.

Aby uzyskać end-to-end protection dla danych obiektów blob, firma Microsoft zaleca włączenie następujących funkcji ochrony danych:

- Usuwanie nie softne kontenera w celu przywrócenia usuniętego kontenera. Aby dowiedzieć się, jak włączyć usuwanie nie softowe kontenera, zobacz Włączanie usuwania [nie softowego dla kontenerów](soft-delete-container-enable.md)i zarządzanie nim.
- Obsługa wersji obiektów blob w celu automatycznego zachowania poprzednich wersji obiektu blob. Po włączeniu obsługi wersji obiektów blob można przywrócić wcześniejszą wersję obiektu blob, aby odzyskać dane, jeśli zostały błędnie zmodyfikowane lub usunięte. Aby dowiedzieć się, jak włączyć obsługę wersji obiektów blob, zobacz Włączanie obsługi wersji obiektów [blob i zarządzanie nimi.](versioning-enable.md)
- Usuwanie nie softowe obiektu blob w celu przywrócenia usuniętego obiektu blob lub wersji. Aby dowiedzieć się, jak włączyć usuwanie nie softowe obiektów blob, zobacz Włączanie usuwania nie softowego dla obiektów blob i [zarządzanie nim.](soft-delete-blob-enable.md)

> [!IMPORTANT]
> Usuwanie nie softowe kontenera jest obecnie dostępne w WERSJI **ZAPOZNAWCZEJ.** Aby uzyskać informacje o postanowieniach prawnych, [które dotyczą](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) funkcji platformy Azure, które są dostępne w wersji beta, wersji zapoznawczej lub nie są jeszcze ogólnie dostępne, zobacz Uzupełniające warunki użytkowania dla wersji zapoznawczych usługi Microsoft Azure Preview.

## <a name="how-container-soft-delete-works"></a>Jak działa usuwanie nie softne kontenera

Po włączeniu usuwania nie softowego kontenera można określić okres przechowywania usuniętych kontenerów, który wynosi od 1 do 365 dni. Domyślny okres przechowywania wynosi 7 dni. W okresie przechowywania można odzyskać usunięty kontener, wywołując **operację przywracania kontenera.**

Po przywróceniu kontenera zostaną również przywrócone obiekty blob kontenera i wszystkie jego wersje. Można jednak użyć usuwania nie softowego kontenera tylko w celu przywrócenia obiektów blob, jeśli sam kontener został usunięty. Aby przywrócić usunięty obiekt blob, gdy jego kontener nadrzędny nie został usunięty, należy użyć usuwania nie soft delete lub wersji obiektu blob.

> [!WARNING]
> Usuwanie nie softowe kontenera może przywrócić tylko całe kontenery i ich zawartość w momencie usunięcia. Nie można przywrócić usuniętego obiektu blob w kontenerze przy użyciu usuwania nie softu kontenera. Firma Microsoft zaleca również włączenie usuwania nie softowego obiektów blob i obsługi wersji obiektów blob w celu ochrony poszczególnych obiektów blob w kontenerze.

Na poniższym diagramie przedstawiono sposób przywracania usuniętego kontenera po włączeniu usuwania nie softu kontenera:

:::image type="content" source="media/soft-delete-container-overview/container-soft-delete-diagram.png" alt-text="Diagram przedstawiający sposób przywracania nieukreślanych usuniętych kontenerów":::

Po przywróceniu kontenera można przywrócić jego oryginalną nazwę, jeśli ta nazwa nie została ponownie użyta. Jeśli oryginalna nazwa kontenera została użyta, możesz przywrócić kontener z nową nazwą.

Po upływie okresu przechowywania kontener zostanie trwale usunięty z usługi Azure Storage i nie będzie można go odzyskać. Zegar rozpoczyna się w okresie przechowywania w momencie usunięcia kontenera. Okres przechowywania można zmienić w dowolnym momencie, ale należy pamiętać, że zaktualizowany okres przechowywania dotyczy tylko nowo usuniętych kontenerów. Wcześniej usunięte kontenery zostaną trwale usunięte na podstawie okresu przechowywania, który obowiązywał w momencie usunięcia kontenera.

Wyłączenie usuwania nietrwałych kontenerów nie powoduje trwałego usunięcia kontenerów, które wcześniej zostały usunięte nietrwałych. Wszystkie nie soft-deleted kontenery zostaną trwale usunięte po upływie okresu przechowywania, który obowiązywał w momencie usunięcia kontenera.

> [!IMPORTANT]
> Usuwanie nie softowe kontenera nie chroni przed usunięciem konta magazynu. Chroni tylko przed usunięciem kontenerów na tym koncie. Aby chronić konto magazynu przed usunięciem, skonfiguruj blokadę zasobu konta magazynu. Aby uzyskać więcej informacji na temat blokowania konta magazynu, zobacz Apply an Azure Resource Manager lock to a storage account (Stosowanie blokady Azure Resource Manager [do konta magazynu).](../common/lock-account-resource.md)

## <a name="about-the-preview"></a>Informacje o wersji zapoznawczej

Usuwanie nie softowe kontenera jest dostępne w wersji zapoznawczej we wszystkich regionach świadczenia usługi Azure.

Wersja 2019-12-12 lub nowsza interfejsu API REST usługi Azure Storage obsługuje usuwanie nie softowe kontenera.

### <a name="storage-account-support"></a>Obsługa konta magazynu

Usuwanie nie softowe kontenera jest dostępne dla następujących typów kont magazynu:

- Konta magazynu ogólnego przeznaczenia w wersji 2 i 1
- Konta magazynu blokowych obiektów blob
- Konta usługi Blob Storage

Konta magazynu z hierarchiczną przestrzenią nazw włączoną do użytku Azure Data Lake Storage Gen2 są również obsługiwane.

### <a name="register-for-the-preview"></a>Zarejestruj się, aby uzyskać podgląd

Aby zarejestrować się w wersji zapoznawczej na użytek usuwania nie softowego kontenera, użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby przesłać żądanie zarejestrowania funkcji w ramach subskrypcji. Po zatwierdzeniu żądania możesz włączyć usuwanie nieblokowe kontenera przy użyciu nowych lub istniejących kont magazynu blokowych obiektów blob, magazynu obiektów blob lub magazynu obiektów blob ogólnego przeznaczenia w wersji 2 lub premium.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby zarejestrować się w programie PowerShell, [wywołaj polecenie Register-AzProviderFeature.](/powershell/module/az.resources/register-azproviderfeature)

```powershell
# Register for container soft delete (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName ContainerSoftDelete

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby zarejestrować się za pomocą interfejsu wiersza polecenia platformy Azure, [wywołaj polecenie az feature register.](/cli/azure/feature#az_feature_register)

```azurecli
az feature register --namespace Microsoft.Storage --name ContainerSoftDelete
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>Sprawdzanie stanu rejestracji

Aby sprawdzić stan rejestracji, użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby sprawdzić stan rejestracji w programie PowerShell, wywołaj polecenie [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName ContainerSoftDelete
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby sprawdzić stan rejestracji w interfejsie wiersza polecenia platformy Azure, wywołaj [polecenie az feature.](/cli/azure/feature#az_feature_show)

```azurecli
az feature show --namespace Microsoft.Storage --name ContainerSoftDelete
```

---

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Włączenie usuwania nie softowego kontenera nie jest naliczane dodatkowe opłaty. Dane w nieukońcowanych kontenerach są rozliczane z taką samą szybkością jak aktywne dane.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie usuwania nie softowego kontenera](soft-delete-container-enable.md)
- [Usuwanie nietrwałe dla obiektów blob](soft-delete-blob-overview.md)
- [Wersjonarowanie obiektów blob](versioning-overview.md)
