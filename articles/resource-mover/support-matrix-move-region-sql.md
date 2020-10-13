---
title: Obsługa przenoszenia zasobów usługi Azure SQL między regionami przy użyciu funkcji przenoszenia zasobów platformy Azure.
description: Zapoznaj się z obsługą przenoszenia zasobów usługi Azure SQL między regionami przy użyciu usługi Azure Resource przenoszącej.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 4925f6ffd2383c21f8ff9b0e3196d44fc15bb657
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89652997"
---
# <a name="support-for-moving-azure-sql-resources-between-azure-regions"></a>Obsługa przeniesienia zasobów usługi Azure SQL między regionami platformy Azure

W tym artykule podsumowano obsługę i wymagania wstępne dotyczące przenoszenia zasobów usługi Azure SQL między regionami platformy Azure za pomocą usługi Azure Resource przenoszącej.

## <a name="requirements"></a>Wymagania

Wymagania zostały podsumowane w poniższej tabeli.

**Funkcja** | **Obsługiwane/nieobsługiwane** | **Szczegóły**
--- | --- | ---
**Skalowanie Azure SQL Database** | Nieobsługiwane | Nie można przenieść baz danych w warstwie usługi Azure SQL — skalowanie przy użyciu funkcji przenoszenia zasobów.
**Nadmiarowość stref** | Obsługiwane |  Obsługiwane opcje przenoszenia:<br/><br/> -Między regionami, które obsługują nadmiarowość stref.<br/><br/> -Między regionami, które nie obsługują nadmiarowości stref.<br/><br/> — Między regionem, który obsługuje nadmiarowość strefy, do regionu, który nie obsługuje nadmiarowości strefy.<br/><br/> — Między regionem, który nie obsługuje nadmiarowości strefy w regionie, który obsługuje nadmiarowość strefy. 
**Synchronizacja danych** | Baza danych centrum/Synchronizacja: nieobsługiwane<br/><br/> Element członkowski synchronizacji: obsługiwane. | Jeśli element członkowski synchronizacji jest przenoszony, należy skonfigurować synchronizację danych z nową docelową bazą danych.
**Istniejąca replikacja geograficzna** | Obsługiwane | Istniejące repliki geograficzne są ponownie mapowane na nowe podstawowe w regionie docelowym.<br/><br/> Po przeniesieniu należy zainicjować rozmieszczanie. [Dowiedz się więcej](/azure/sql-database/sql-database-active-geo-replication-portal)
**Transparent Data Encryption (TDE) z Bring Your Own Key (BYOK)** | Obsługiwane | [Dowiedz się więcej](../key-vault/general/move-region.md) o przenoszeniu magazynów kluczy między regionami.
**TDE z kluczem zarządzanym przez usługę** | Obsługiwane. |  [Dowiedz się więcej](../key-vault/general/move-region.md) o przenoszeniu magazynów kluczy między regionami.
**Dynamiczne reguły maskowania danych** | Obsługiwane. | Reguły są automatycznie kopiowane do regionu docelowego w ramach przenoszenia. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started-portal).
**Zaawansowane zabezpieczenia danych** | Nieobsługiwane. | Obejście: Skonfiguruj na poziomie SQL Server w regionie docelowym. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).
**Reguły zapory** | Nieobsługiwane. | Obejście: Skonfiguruj reguły zapory dla SQL Server w regionie docelowym. Reguły zapory na poziomie bazy danych są kopiowane z serwera źródłowego na serwer docelowy. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-server-level-firewall-rule).
**Zasady inspekcji** | Nieobsługiwane. | Zasady zostaną zresetowane do domyślnych po przeniesieniu. [Dowiedz się](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) , jak zresetować.
**Przechowywanie kopii zapasowej** | Obsługiwane. | Zasady przechowywania kopii zapasowej dla źródłowej bazy danych są przenoszone do docelowej bazy danych. [Dowiedz się](/azure/sql-database/sql-database-long-term-backup-retention-configure) , jak modyfikować ustawienia po przeniesieniu.
**Dostrajanie automatycznego** | Nieobsługiwane. | Obejście: Ustaw ustawienia automatycznego dostrajania po przeniesieniu. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-automatic-tuning-enable).
**Alerty bazy danych** | Nieobsługiwane. | Obejście: Ustaw alerty po przeniesieniu. [Dowiedz się więcej](https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal).
**Usługa Azure SQL Server rozciągana baza danych** | Nieobsługiwane | Nie można przenieść rozproszonych baz danych programu SQL Server za pomocą przenoszenia zasobów.
**Azure Synapse Analytics** | Nieobsługiwane | Nie można przenieść Synapse Analytics (dawniej Azure SQL Data Warehouse) za pomocą przenoszenia zasobów.
## <a name="next-steps"></a>Następne kroki

Wypróbuj [zasoby SQL platformy Azure](tutorial-move-region-sql.md) w innym regionie przy użyciu obszaru przenoszenia zasobów.