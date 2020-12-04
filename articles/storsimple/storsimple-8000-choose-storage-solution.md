---
title: Opcje transferu danych na platformę Azure przy użyciu urządzenia | Microsoft Docs
description: Dowiedz się, jak wybrać odpowiednie urządzenie do lokalnego transferu danych na platformę Azure między Data Box Edge, Azure File Sync i StorSimple 8000.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: f2de6dbba2548f0c281df35edf2883d0c00af667
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571917"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>Compare StorSimple with Azure File Sync and Data Box Edge data transfer options (Porównanie usługi StorSimple z usługami Azure File Sync i opcjami transferu danych usługi Data Box Edge) 

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]
 
Ten dokument zawiera omówienie opcji lokalnego transferu danych na platformę Azure, porównując: Data Box Edge a Azure File Sync a 8000.

- **[Data Box Edge](../databox-online/azure-stack-edge-overview.md)** — Data Box Edge to lokalne urządzenie sieciowe, które przenosi dane na platformę Azure i poza nią i ma funkcję obliczeniową z obsługą systemu AI na potrzeby wstępnego przetwarzania danych podczas przekazywania. Data Box Gateway jest wirtualną wersją urządzenia z takimi samymi możliwościami transferu danych.
- **[Azure File Sync](../storage/files/storage-sync-files-deployment-guide.md)** — Azure File Sync może służyć do scentralizowania udziałów plików w organizacji w Azure Files, przy zachowaniu elastyczności, wydajności i zgodności lokalnego serwera plików. Funkcja Azure File Sync przekształca system Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Ogólna dostępność Azure File Sync została ogłoszona wcześniej w 2018.
- **[StorSimple](./storsimple-overview.md)** – StorSimple to urządzenie hybrydowe, które ułatwia przedsiębiorstwom skonsolidowanie infrastruktury magazynu na potrzeby magazynu podstawowego, ochrony danych, archiwizacji i odzyskiwania po awarii na jednym rozwiązaniu przez ścisłe integrację z usługą Azure Storage. Cykl życia produktu dla programu StorSimple można znaleźć [tutaj](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).

## <a name="comparison-summary"></a>Podsumowanie porównania

|                           |StorSimple 8000   |Azure File Sync   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|**Omówienie**     |Magazyn i archiwum hybrydowe warstwowe|Ogólny magazyn serwera plików z obsługą warstw chmury i synchronizacji z obsługą wiele lokacji.  |Rozwiązanie magazynu do wstępnego przetwarzania danych i wysyłania ich przez sieć do platformy Azure.        |
|**Scenariusze**    |Serwer plików, archiwizowanie, miejsce docelowe kopii zapasowej |Serwer plików, archiwizowanie (wiele lokacji)   |Transfer danych, przetwarzanie wstępne danych, w tym inferencing ML, IoT, archiwalny    |
|**Funkcja obliczeniowa Edge** |Niedostępne |Niedostępne |Obsługuje uruchamianie kontenerów za pomocą Azure IoT Edge    |
|**Współczynnik postaci**  |Urządzenie fizyczne   |Agent zainstalowany w systemie Windows Server |Urządzenie fizyczne   |
|**Sprzęt**     |Urządzenie fizyczne udostępniane przez firmę Microsoft jako część usługi | Dostarczone przez klienta |Urządzenie fizyczne udostępniane przez firmę Microsoft jako część usługi  |
|**Format danych**  |Format niestandardowy   |Pliki         |Obiekty blob lub pliki    |
|**Obsługa protokołu** |iSCSI          |SMB, NFS    | SMB lub NFS      |
|**Cennik**      |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [Azure Data Box Edge](../databox-online/azure-stack-edge-overview.md) i [Azure Data Box Gateway](../databox-online/data-box-gateway-overview.md)
- Dowiedz się więcej o [Azure File Sync](../storage/files/storage-sync-files-deployment-guide.md)
