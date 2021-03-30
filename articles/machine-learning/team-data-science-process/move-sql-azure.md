---
title: Przenoszenie danych do procesu nauki o danych Azure SQL Database zespołowej
description: Przenoszenie danych z plików prostych (format CSV lub TSV) lub z danych przechowywanych w SQL Server do Azure SQL Database.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 42bac2df7abe00be8c0e6ffddcc9bef7ef28ba9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93309522"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Przenoszenie danych do usługi Azure SQL Database dla usługi Azure Machine Learning

W tym artykule opisano opcje przeniesienia danych z plików prostych (format CSV lub TSV) lub z danych przechowywanych w SQL Server do Azure SQL Database. Te zadania dotyczące przeniesienia danych do chmury są częścią procesu nauki o danych zespołowych.

Temat przedstawiający opcje przenoszenia danych do SQL Server dla Machine Learning można znaleźć [w temacie Przenoszenie danych do SQL Server na maszynie wirtualnej platformy Azure](move-sql-server-virtual-machine.md).

Poniższa tabela zawiera podsumowanie opcji przeniesienia danych do Azure SQL Database.

| <b>ZEWNĘTRZ</b> | <b>MIEJSCE docelowe: Azure SQL Database</b> |
| --- | --- |
| <b>Plik prosty (format CSV lub TSV)</b> |[Wstaw zbiorczo zapytanie SQL](#bulk-insert-sql-query) |
| <b>SQL Server lokalne</b> |1.[Eksportuj do pliku prostego](#export-flat-file)<br> 2. [Kreator migracji SQL Database](#insert-tables-bcp)<br> 3. [Tworzenie kopii zapasowej i przywracanie bazy danych](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prerequisites"></a><a name="prereqs"></a>Wymagania wstępne
Procedury opisane w tym miejscu wymagają:

* **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Storage**. Używasz konta usługi Azure Storage do przechowywania danych w tym samouczku. Jeśli nie masz konta usługi Azure Storage, zobacz artykuł [Tworzenie konta magazynu](../../storage/common/storage-account-create.md) . Po utworzeniu konta magazynu należy uzyskać klucz konta używany do uzyskiwania dostępu do magazynu. Zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md).
* Dostęp do **Azure SQL Database**. Jeśli konieczne jest skonfigurowanie Azure SQL Database, [wprowadzenie z Microsoft Azure SQL Database](../../azure-sql/database/single-database-create-quickstart.md) zawiera informacje na temat udostępniania nowego wystąpienia Azure SQL Database.
* Zainstalowano i skonfigurowano **Azure PowerShell** lokalnie. Aby uzyskać instrukcje, zobacz [How to Install and configure Azure PowerShell](/powershell/azure/).

**Dane**: procesy migracji są przedstawiane za pomocą [zestawu danych NYC taksówki](https://chriswhong.com/open-data/foil_nyc_taxi/). Zestaw danych NYC taksówki zawiera informacje na temat danych o podróży i ich Targi i jest dostępny w usłudze Azure Blob Storage: [NYCe dane z taksówką](https://www.andresmh.com/nyctaxitrips/). Przykład i opis tych plików znajdują się w [opisie zestawu danych NYC taksówki](sql-walkthrough.md#dataset).

Można dostosować procedury opisane w tym miejscu do zestawu własnych danych lub postępować zgodnie z instrukcjami opisanymi przy użyciu zestawu danych NYC taksówki. Aby przekazać zestaw danych NYC taksówki do bazy danych SQL Server, wykonaj procedurę opisaną w temacie [zbiorcze Importowanie danych do SQL Server Database](sql-walkthrough.md#dbload).

## <a name="moving-data-from-a-flat-file-source-to-an-azure-sql-database"></a><a name="file-to-azure-sql-database"></a> Przeniesienie danych z prostego źródła pliku do Azure SQL Database
Dane w plikach prostych (CSV lub TSV) można przenieść do Azure SQL Database przy użyciu zbiorczego zapytania SQL.

### <a name="bulk-insert-sql-query"></a><a name="bulk-insert-sql-query"></a> Wstaw zbiorczo zapytanie SQL
Kroki procedury korzystającej z zapytania BULK INSERT SQL są podobne do wskazówek dotyczących przeniesienia danych z prostego źródła pliku do SQL Server na maszynie wirtualnej platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bulk Wstaw zapytanie SQL](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="moving-data-from-sql-server-to-an-azure-sql-database"></a><a name="sql-on-prem-to-sazure-sql-database"></a> Przeniesienie danych z SQL Server do Azure SQL Database
Jeśli dane źródłowe są przechowywane w SQL Server, istnieją różne możliwości przeniesienia danych do Azure SQL Database:

1. [Eksportuj do pliku prostego](#export-flat-file)
2. [Kreator migracji SQL Database](#insert-tables-bcp)
3. [Tworzenie kopii zapasowej i przywracanie bazy danych](#db-migration)
4. [Azure Data Factory](#adf)

Kroki dla pierwszych trzech są podobne do tych w sekcjach [Przenieś dane do SQL Server na maszynie wirtualnej platformy Azure](move-sql-server-virtual-machine.md) , która obejmuje te same procedury. Linki do odpowiednich sekcji w tym temacie znajdują się w poniższych instrukcjach.

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Eksportuj do pliku prostego
Kroki związane z tym eksportowaniem do pliku prostego są podobne do tych wskazówek objętych [eksportowaniem do pliku prostego](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="sql-database-migration-wizard"></a><a name="insert-tables-bcp"></a>Kreator migracji SQL Database
Kroki opisane w Kreatorze migracji SQL Database są podobne do tych, które opisano w [Kreatorze migracji SQL Database](move-sql-server-virtual-machine.md#sql-migration).

### <a name="database-back-up-and-restore"></a><a name="db-migration"></a>Tworzenie kopii zapasowej i przywracanie bazy danych
Procedura tworzenia kopii zapasowej i przywracania bazy danych przypomina te wskazówki wymienione w temacie [Tworzenie kopii zapasowej i przywracanie bazy danych](move-sql-server-virtual-machine.md#sql-backup).

### <a name="azure-data-factory"></a><a name="adf"></a>Azure Data Factory
Dowiedz się, jak przenieść dane do Azure SQL Database z Azure Data Factory (ADF) w tym temacie. [Przenieś dane z SQL Server do usługi SQL Azure z Azure Data Factory](move-sql-azure-adf.md). W tym temacie pokazano, jak za pomocą funkcji ADF przenieść dane z bazy danych SQL Server do Azure SQL Database za pośrednictwem usługi Azure Blob Storage.

Należy rozważyć użycie funkcji ADF, gdy dane wymagają ciągłego migrowania przy użyciu hybrydowych źródeł lokalnych i chmurowych.  Moduł ADF również pomaga, gdy dane wymagają przekształceń lub wymagają nowej logiki biznesowej podczas migracji. ADF umożliwia planowanie i monitorowanie zadań przy użyciu prostych skryptów JSON, które regularnie zarządzają przenoszeniem danych. Funkcja ADF oferuje również inne funkcje, takie jak obsługa złożonych operacji.