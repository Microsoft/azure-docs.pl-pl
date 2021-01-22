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
ms.openlocfilehash: 46c5f5995c7a1d4eb074f6c1b25ecaad7e2da37e
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695541"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-managed-instance"></a>Znane problemy/ograniczenia migracji z migracją online do wystąpienia zarządzanego Azure SQL

Poniżej opisano znane problemy i ograniczenia związane z migracją online z programu SQL Server do wystąpienia zarządzanego usługi Azure SQL.

> [!IMPORTANT]
> W przypadku migracji w trybie online SQL Server do Azure SQL Database migracja SQL_variant typów danych nie jest obsługiwana.

## <a name="backup-requirements"></a>Wymagania dotyczące kopii zapasowych

- **Kopie zapasowe z sumą kontrolną**

    Azure Database Migration Service używa metody Backup i Restore do migrowania lokalnych baz danych do wystąpienia zarządzanego SQL. Azure Database Migration Service obsługuje tylko kopie zapasowe utworzone przy użyciu sum kontrolnych.

    [Włącza lub wyłącza sumy kontrolne kopii zapasowych podczas wykonywania kopii zapasowej lub przywracania (SQL Server)](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server).

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

## <a name="smb-file-share-connectivity"></a>Łączność z udziałem plików SMB

Problemy z nawiązywaniem połączenia z udziałem plików SMB prawdopodobnie są spowodowane problemem z uprawnieniami. 

Aby przetestować łączność udziału plików SMB, wykonaj następujące kroki: 

1. Zapisz kopię zapasową w udziale plików SMB. 
1. Sprawdź łączność sieciową między podsiecią Azure Database Migration Service i SQL Server źródłowej. Najprostszym sposobem, aby to zrobić, wdrożyć maszynę wirtualną SQL Server w podsieci DMS i połączyć się ze źródłem SQL Server przy użyciu SQL Server Management Studio. 
1. Przywróć nagłówek w SQL Server źródłowym z kopii zapasowej w ramach udziału plików: 

   ```sql
   RESTORE HEADERONLY   
   FROM DISK = N'\\<SMB file share path>\full.bak'
   ```

Jeśli nie możesz połączyć się z udziałem plików, Skonfiguruj uprawnienia, wykonując następujące czynności: 

1. Przejdź do udziału plików za pomocą Eksploratora plików. 
1. Kliknij prawym przyciskiem myszy udział plików, a następnie wybierz polecenie Właściwości. 
1. Wybierz kartę **udostępnianie** i wybierz opcję **Udostępnianie zaawansowane**. 
1. Dodaj konto systemu Windows używane na potrzeby migracji i przypisz do niego pełny dostęp z możliwością kontroli. 
1. Dodaj konto usługi SQL Server i przypisz do niego pełny dostęp z możliwością kontroli. Jeśli nie masz pewności, które konto jest używane, sprawdź **Configuration Manager SQL Server** dla konta usługi SQL Server. 

   :::image type="content" source="media/known-issues-azure-sql-db-managed-instance-online/assign-fileshare-permissions.png" alt-text="Przyznaj pełną kontrolę dostępu do kont systemu Windows używanych do migracji oraz dla konta usługi SQL Server. ":::

