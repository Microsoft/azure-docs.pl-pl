---
title: Sprawdź Właściwość godzina ostatniej synchronizacji dla konta magazynu
titleSuffix: Azure Storage
description: Dowiedz się, jak sprawdzić Właściwość godzina ostatniej synchronizacji dla konta magazynu z replikacją geograficzną. Właściwość czas ostatniej synchronizacji wskazuje czas, w którym wszystkie zapisy z regionu podstawowego zostały pomyślnie wprowadzone do regionu pomocniczego.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 242700c05053aa9d07e3a561a21986c8451a46c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91612448"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>Sprawdź Właściwość godzina ostatniej synchronizacji dla konta magazynu

Podczas konfigurowania konta magazynu można określić, że dane są kopiowane do regionu pomocniczego, który ma setki kilometrów od regionu podstawowego. Replikacja geograficzna zapewnia trwałość danych w przypadku znacznej awarii w regionie podstawowym, na przykład klęski żywiołowej. Jeśli dodatkowo włączysz dostęp do odczytu do regionu pomocniczego, dane pozostają dostępne dla operacji odczytu, jeśli region podstawowy stał się niedostępny. Możesz zaprojektować aplikację, aby bezproblemowo przełączać się do odczytu z regionu pomocniczego, jeśli region podstawowy nie odpowiada.

Magazyn Geograficznie nadmiarowy (GRS) i strefa Geograficznie nadmiarowy (GZRS) obie te dane są replikowane asynchronicznie do regionu pomocniczego. Aby uzyskać dostęp do odczytu do regionu pomocniczego, Włącz magazyn Geograficznie nadmiarowy z dostępem do odczytu (RA-GRS) lub strefę geograficzną z dostępem do odczytu (RA-GZRS). Aby uzyskać więcej informacji na temat różnych opcji nadmiarowości oferowanych przez usługę Azure Storage, zobacz [nadmiarowość usługi Azure Storage](storage-redundancy.md).

W tym artykule opisano, jak sprawdzić Właściwość **godzina ostatniej synchronizacji** dla konta magazynu, aby można było oszacować rozbieżność między regionem podstawowym i pomocniczym.

## <a name="about-the-last-sync-time-property"></a>Informacje o ostatniej właściwości czasu synchronizacji

Ponieważ replikacja geograficzna jest asynchroniczna, istnieje możliwość, że dane zapisywane w regionie podstawowym nie zostały jeszcze zapisaną do regionu pomocniczego w momencie wystąpienia awarii. Właściwość **godzina ostatniej synchronizacji** wskazuje czas ostatniego zapisania danych z regionu podstawowego w regionie pomocniczym. Wszystkie zapisy dokonane w regionie podstawowym przed upływem czasu ostatniej synchronizacji są dostępne do odczytania z lokalizacji dodatkowej. Zapisy dokonane w regionie podstawowym po ostatniej właściwości czasu synchronizacji mogą być niedostępne dla operacji odczytu.

Właściwość **czas ostatniej synchronizacji** jest wartością daty/godziny GMT.

## <a name="get-the-last-sync-time-property"></a>Pobierz właściwość czasu ostatniej synchronizacji

Możesz użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby pobrać wartość właściwości **Data ostatniej synchronizacji** .

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby uzyskać ostatnią godzinę synchronizacji dla konta magazynu za pomocą programu PowerShell, należy zainstalować w wersji 1.11.0 lub nowszej modułu [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) . Następnie sprawdź Właściwość **GeoReplicationStats. LastSyncTime** konta magazynu. Pamiętaj, aby zastąpić wartości zastępcze własnymi wartościami:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uzyskać ostatnią godzinę synchronizacji dla konta magazynu za pomocą interfejsu wiersza polecenia platformy Azure, sprawdź Właściwość **geoReplicationStats. lastSyncTime** konta magazynu. Użyj `--expand` parametru, aby zwrócić wartości właściwości zagnieżdżonych w obszarze **geoReplicationStats**. Pamiętaj, aby zastąpić wartości zastępcze własnymi wartościami:

```azurecli-interactive
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

---

## <a name="see-also"></a>Zobacz też

- [Nadmiarowość usługi Azure Storage](storage-redundancy.md)
- [Zmiana opcji nadmiarowości dla konta magazynu](redundancy-migration.md)
- [Projektowanie aplikacji o wysokiej dostępności przy użyciu nadmiarowości geograficznej](geo-redundant-design.md)
