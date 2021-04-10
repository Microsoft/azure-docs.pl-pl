---
title: Wyłącz geograficznie kopie zapasowe
description: Przewodnik dotyczący wyłączania geograficznego tworzenia kopii zapasowych dla dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 01/06/2021
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 395d5f0697138155b0bb0c629461aada9e9c18c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98739119"
---
# <a name="disable-geo-backups-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Wyłącz geograficznie kopie zapasowe dla dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics

W tym artykule dowiesz się, jak wyłączyć geograficznie kopie zapasowe dla dedykowanej puli SQL (dawniej SQL DW) Azure Portal.

## <a name="disable-geo-backups-through-azure-portal"></a>Wyłącz geograficznie kopie zapasowe za Azure Portal

Wykonaj następujące kroki, aby wyłączyć geograficznie kopie zapasowe dla dedykowanej puli SQL (dawniej SQL DW):

> [!NOTE]
> Jeśli wyłączysz geograficzną kopię zapasową, nie będzie już możliwe odzyskanie dedykowanej puli SQL (dawniej SQL DW) do innego regionu platformy Azure. 
>

1. Zaloguj się do konta [Azure Portal](https://portal.azure.com/) .
1. Wybierz dedykowaną pulę SQL (dawniej SQL DW), dla której chcesz wyłączyć geograficznie kopie zapasowe. 
1. W obszarze **Ustawienia** w panelu nawigacyjnym po lewej stronie wybierz pozycję **zasady geograficznej kopii zapasowej**.

   ![Wyłącz geograficzną kopię zapasową](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-1.png)

1. Aby wyłączyć tworzenie kopii zapasowych, wybierz pozycję **wyłączone**. 

   ![Wyłączone geograficznie kopia zapasowa](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-2.png)

1. Wybierz pozycję *Zapisz* , aby upewnić się, że Twoje ustawienia zostały zapisane. 

   ![Zapisz ustawienia geograficznej kopii zapasowej](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-3.png)

## <a name="next-steps"></a>Następne kroki

- [Przywracanie istniejącej dedykowanej puli SQL (dawniej SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Przywracanie usuniętej dedykowanej puli SQL (dawniej SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
