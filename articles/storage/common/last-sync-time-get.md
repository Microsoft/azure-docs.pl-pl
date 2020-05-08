---
title: Sprawdź Właściwość godzina ostatniej synchronizacji dla konta magazynu
titleSuffix: Azure Storage
description: Dowiedz się, jak sprawdzić Właściwość godzina ostatniej synchronizacji dla konta magazynu z replikacją geograficzną. Właściwość czas ostatniej synchronizacji wskazuje czas, w którym wszystkie zapisy z regionu podstawowego zostały pomyślnie wprowadzone do regionu pomocniczego.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: afcadd55e87579b25f03176fa3227024863b90fb
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858508"
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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aby uzyskać ostatnią godzinę synchronizacji dla konta magazynu za pomocą programu PowerShell, Zainstaluj wersję modułu AZ. Storage, która obsługuje pobieranie statystyki replikacji geograficznej. Na przykład:

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion ??? –AllowPrerelease –AllowClobber –Force
```

Następnie sprawdź Właściwość **GeoReplicationStats. LastSyncTime** konta magazynu. Pamiętaj, aby zastąpić wartości zastępcze własnymi wartościami:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uzyskać ostatnią godzinę synchronizacji dla konta magazynu za pomocą interfejsu wiersza polecenia platformy Azure, sprawdź Właściwość **geoReplicationStats. lastSyncTime** konta magazynu. Użyj parametru `--expand` , aby zwrócić wartości właściwości zagnieżdżonych w obszarze **geoReplicationStats**. Pamiętaj, aby zastąpić wartości zastępcze własnymi wartościami:

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