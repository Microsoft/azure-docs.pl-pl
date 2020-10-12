---
title: Znane problemy i ograniczenia dotyczące migracji online do wystąpienia zarządzanego Azure SQL
description: Informacje o znanych problemach/ograniczeniach migracji skojarzonych z migracją online do wystąpienia zarządzanego usługi Azure SQL.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 6393624cb4520ccd28dff41dd97746873bc13f59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91330302"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-managed-instance"></a>Znane problemy/ograniczenia migracji z migracją online do wystąpienia zarządzanego Azure SQL

Poniżej opisano znane problemy i ograniczenia związane z migracją online z programu SQL Server do wystąpienia zarządzanego usługi Azure SQL.

> [!IMPORTANT]
> W przypadku migracji w trybie online SQL Server do Azure SQL Database migracja SQL_variant typów danych nie jest obsługiwana.

## <a name="backup-requirements"></a>Wymagania dotyczące kopii zapasowych

- **Kopie zapasowe z sumą kontrolną**

    Azure Database Migration Service używa metody Backup i Restore do migrowania lokalnych baz danych do wystąpienia zarządzanego SQL. Azure Database Migration Service obsługuje tylko kopie zapasowe utworzone przy użyciu sum kontrolnych.

    [Włącz lub Wyłącz sumy kontrolne kopii zapasowych podczas wykonywania kopii zapasowej lub przywracania (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)

    > [!NOTE]
    > Jeśli kopie zapasowe bazy danych są wykonywane przy użyciu kompresji, suma kontrolna jest zachowaniem domyślnym, chyba że zostanie jawnie wyłączona.

    W przypadku migracji w trybie offline w przypadku wybrania opcji **Zezwól na Azure Database Migration Service...**, a następnie Azure Database Migration Service będzie korzystać z kopii zapasowej bazy danych z włączoną opcją sum kontrolnych.

- **Nośnik kopii zapasowej**

    Upewnij się, że wszystkie kopie zapasowe są wykonywane na osobnym nośniku kopii zapasowych (pliki kopii zapasowej). Azure Database Migration Service nie obsługuje kopii zapasowych, które są dołączane do pojedynczego pliku kopii zapasowej. Utwórz pełną kopię zapasową i Rejestruj kopie zapasowe w oddzielnych plikach kopii zapasowej.

## <a name="data-and-log-file-layout"></a>Układ danych i plików dziennika

- **Liczba plików dziennika**

    Azure Database Migration Service nie obsługuje baz danych z wieloma plikami dziennika. Jeśli masz wiele plików dziennika, Zmniejsz je i uporządkuj je w jeden plik dziennika transakcji. Ponieważ nie można zdalnie rejestrować plików, które nie są puste, należy najpierw utworzyć kopię zapasową pliku dziennika.

## <a name="sql-server-features"></a>Funkcje SQL Server

- **FileStream/FileTable**

    Wystąpienie zarządzane SQL obecnie nie obsługuje plików FileStream i FileTable. W przypadku obciążeń zależnych od tych funkcji Zalecamy wybór serwerów SQL działających na maszynach wirtualnych platformy Azure jako element docelowy platformy Azure.

- **Tabele w pamięci**

    Pakiet OLTP w pamięci jest dostępny w warstwach Premium i Krytyczne dla działania firmy dla wystąpienia zarządzanego SQL; warstwa Ogólnego przeznaczenia nie obsługuje przetwarzania OLTP w pamięci.

## <a name="migration-resets"></a>Resetowanie migracji

- **Wdrożenia**

    Wystąpienie zarządzane SQL to usługa PaaS z automatyczną poprawką poprawek i aktualizacjami wersji. Podczas migracji wystąpienia zarządzanego SQL aktualizacje niekrytyczne są przechowywane przez maksymalnie 36 godzin. Następnie (i w przypadku aktualizacji krytycznych), jeśli migracja zostanie zakłócona, proces resetuje do stanu pełnego przywracania.

    Uruchomienie produkcyjne migracji można wywołać tylko po przywróceniu pełnej kopii zapasowej i przejściu do wszystkich kopii zapasowych dziennika. Jeśli dotyczy to jednorazowe migracji produkcyjnej, skontaktuj się z [aliasem opinii platformy Azure](mailto:dmsfeedback@microsoft.com).
