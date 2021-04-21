---
title: Opcje transferu danych na platformę Azure przy użyciu urządzenia | Microsoft Docs
description: Dowiedz się, jak wybrać odpowiednie urządzenie do lokalnego transferu danych na platformę Azure między urządzenie Data Box Edge, Azure File Sync i StorSimple 8000.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 1345486e6bda7501a862612652b722b0075e190f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791184"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>Compare StorSimple with Azure File Sync and Data Box Edge data transfer options (Porównanie usługi StorSimple z usługami Azure File Sync i opcjami transferu danych usługi Data Box Edge) 

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]
 
Ten dokument zawiera omówienie opcji lokalnego transferu danych na platformę Azure, porównując: urządzenie Data Box Edge a Azure File Sync z serią StorSimple 8000.

- **[urządzenie Data Box Edge](../databox-online/azure-stack-edge-overview.md)** — urządzenie Data Box Edge to lokalne urządzenie sieciowe, które przenosi dane na platformę Azure i z platformy Azure oraz ma możliwości obliczeniowe usługi Edge z obsługą AI do wstępnego przetwarzania danych podczas przekazywania. Data Box Gateway jest wirtualną wersją urządzenia z takimi samymi możliwościami transferu danych.
- **[Azure File Sync](../storage/file-sync/file-sync-deployment-guide.md)** — Azure File Sync może służyć do scentralizować udziały plików organizacji w programie Azure Files przy zachowaniu elastyczności, wydajności i zgodności lokalnego serwera plików. Funkcja Azure File Sync przekształca system Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Ogólna dostępność Azure File Sync została ogłoszona wcześniej w 2018 r.
- **[StorSimple](./storsimple-overview.md)** — StorSimple to urządzenie hybrydowe, które pomaga przedsiębiorstwom skonsolidować infrastrukturę magazynu dla magazynu podstawowego, ochrony danych, archiwizowania i odzyskiwania po awarii w jednym rozwiązaniu dzięki ścisłej integracji z usługą Azure Storage. Cykl życia produktu StorSimple można znaleźć [tutaj.](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)

## <a name="comparison-summary"></a>Podsumowanie porównania

|                           |StorSimple 8000   |Azure File Sync   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|**Omówienie**     |Warstwowy magazyn hybrydowy i archiwum|Ogólny magazyn serwera plików z warstwami w chmurze i synchronizacją wielu lokacji.  |Rozwiązanie magazynu do wstępnego przetwarzania danych i wysyłania ich przez sieć na platformę Azure.        |
|**Scenariusze**    |Serwer plików, archiwum, cel kopii zapasowej |Serwer plików, archiwizacja (wiele lokacji)   |Transfer danych, przetwarzanie wstępne danych, w tym wnioskowanie uczenia maszynowego, IoT, archiwizacja    |
|**Obliczenia brzegowe** |Niedostępne |Niedostępne |Obsługuje uruchamianie kontenerów przy użyciu Azure IoT Edge    |
|**Faktor**  |Urządzenie fizyczne   |Agent zainstalowany w systemie Windows Server |Urządzenie fizyczne   |
|**Sprzęt**     |Urządzenie fizyczne dostarczane przez firmę Microsoft w ramach usługi | Podany przez klienta |Urządzenie fizyczne dostarczane przez firmę Microsoft w ramach usługi  |
|**Format danych**  |Format niestandardowy   |Pliki         |Obiekty blob lub pliki    |
|**Obsługa protokołów** |iSCSI          |SMB, NFS    | SMB lub NFS      |
|**Cennik**      |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [więcej Azure Data Box Edge](../databox-online/azure-stack-edge-overview.md) i [Azure Data Box Gateway](../databox-gateway/data-box-gateway-overview.md)
- Dowiedz się [więcej o Azure File Sync](../storage/file-sync/file-sync-deployment-guide.md)