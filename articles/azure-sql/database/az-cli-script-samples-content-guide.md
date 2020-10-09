---
title: Przykłady skryptów interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Przykłady skryptów interfejsu wiersza polecenia platformy Azure służące do tworzenia i zarządzania Azure SQL Database i wystąpieniem zarządzanym usługi Azure SQL
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: overview-samples, mvc, sqldbrb=2, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 893ea607b322c2f9b2d8a4e8b5b087e21c476d98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87497175"
---
# <a name="azure-cli-samples-for-azure-sql-database-and-sql-managed-instance"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla Azure SQL Database i wystąpienia zarządzanego SQL 
 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Za pomocą <a href="/cli/azure">interfejsu wiersza polecenia platformy Azure</a>można konfigurować wystąpienia zarządzane Azure SQL Database i SQL.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

# <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

Poniższa tabela zawiera linki do przykładów skryptów interfejsu wiersza polecenia platformy Azure do zarządzania pojedynczymi bazami danych w puli w Azure SQL Database. 

|Obszar|Opis|
|---|---|
|**Tworzenie baz danych w Azure SQL Database**||
| [Tworzenie pojedynczej bazy danych i konfigurowanie reguły zapory](scripts/create-and-configure-database-cli.md) | Tworzy SQL Database i konfiguruje regułę zapory na poziomie serwera. |
| [Tworzenie pul elastycznych i przenoszenie baz danych w puli](scripts/move-database-between-elastic-pools-cli.md) | Tworzy elastyczne pule, przenosi bazy danych w puli i zmienia rozmiary obliczeniowe. |
|**Skalowanie baz danych w Azure SQL Database**||
| [Skalowanie pojedynczej bazy danych](scripts/monitor-and-scale-database-cli.md) | Skaluje bazę danych w SQL Database do innego rozmiaru obliczeniowego po wykonaniu zapytania o informacje o rozmiarze dla bazy danych. |
| [Skalowanie puli elastycznej](scripts/scale-pool-cli.md) | Skaluje pulę elastyczną SQL do innego rozmiaru obliczeniowego. |
|**Konfigurowanie replikacji geograficznej i trybu failover**||
| [Dodawanie pojedynczej bazy danych do grupy trybu failover](scripts/add-database-to-failover-group-cli.md)| Tworzy bazę danych i grupę trybu failover, dodaje bazę danych do grupy trybu failover, a następnie testuje tryb failover na serwerze pomocniczym. |
| [Konfigurowanie grupy trybu failover dla puli elastycznej](../../sql-database/scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Tworzy bazę danych, dodaje ją do puli elastycznej, dodaje pulę elastyczną do grupy trybu failover, a następnie testuje tryb failover na serwerze pomocniczym. |
| [Konfigurowanie i przełączanie pojedynczej bazy danych za pomocą funkcji aktywnej replikacji geograficznej](../../sql-database/scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Umożliwia skonfigurowanie aktywnej replikacji geograficznej dla bazy danych w Azure SQL Database i przełączenie jej w tryb failover do repliki pomocniczej. |
| [Konfigurowanie i przełączanie w tryb failover bazy danych w puli przy użyciu aktywnej replikacji geograficznej](../../sql-database/scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Konfiguruje aktywną replikację geograficzną dla bazy danych w puli elastycznej, a następnie przełącza ją w tryb failover do repliki pomocniczej. |
| **Inspekcja i wykrywanie zagrożeń** |
| [Konfigurowanie inspekcji i wykrywanie zagrożeń](../../sql-database/scripts/sql-database-auditing-and-threat-detection-cli.md)| Konfiguruje zasady inspekcji i wykrywania zagrożeń dla bazy danych w Azure SQL Database. |
| **Tworzenie kopii zapasowej, przywracanie, kopiowanie i Importowanie bazy danych**||
| [Tworzenie kopii zapasowej bazy danych](../../sql-database/scripts/sql-database-backup-database-cli.md)| Tworzy kopię zapasową bazy danych w SQL Database w usłudze Azure Storage. |
| [Przywracanie bazy danych](../../sql-database/scripts/sql-database-restore-database-cli.md)| Przywraca bazę danych w SQL Database z geograficznie nadmiarowej kopii zapasowej i przywraca usuniętą bazę danych do najnowszej kopii zapasowej. |
| [Kopiowanie bazy danych na nowy serwer](../../sql-database/scripts/sql-database-copy-database-to-new-server-cli.md) | Tworzy kopię istniejącej bazy danych w SQL Database na nowym serwerze. |
| [Importowanie bazy danych z pliku BACPAC](../../sql-database/scripts/sql-database-import-from-bacpac-cli.md)| Importuje bazę danych do SQL Database z pliku BACPAC. |
|||

Dowiedz się więcej o [interfejsie API interfejsu wiersza polecenia platformy Azure o pojedynczej bazie danych](single-database-manage.md#the-azure-cli).

# <a name="azure-sql-managed-instance"></a>[Wystąpienie zarządzane Azure SQL](#tab/managed-instance)

Poniższa tabela zawiera linki do przykładów skryptów interfejsu wiersza polecenia platformy Azure dla wystąpienia zarządzanego Azure SQL.

|Obszar|Opis|
|---|---|
| **Tworzenie wystąpienia zarządzanego SQL**||
| [Tworzenie wystąpienia zarządzanego SQL](../../sql-database/scripts/sql-database-create-configure-managed-instance-cli.md)| Tworzy wystąpienie zarządzane SQL. |
| **Konfigurowanie funkcji Transparent Data Encryption (TDE)**||
| [Zarządzanie Transparent Data Encryption w wystąpieniu zarządzanym SQL przy użyciu Azure Key Vault](../../sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Konfiguruje Transparent Data Encryption (TDE) w wystąpieniu zarządzanym SQL, używając Azure Key Vault z różnymi kluczowymi scenariuszami. |
|**Konfigurowanie grupy trybu failover**||
| [Konfigurowanie grupy trybu failover dla wystąpienia zarządzanego SQL](../../sql-database/scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Tworzy dwa wystąpienia wystąpienia zarządzanego SQL, dodaje je do grupy trybu failover, a następnie testuje tryb failover z podstawowego wystąpienia zarządzanego SQL do pomocniczego wystąpienia zarządzanego SQL. |
|||

Dodatkowe przykłady wystąpienia zarządzanego SQL można znaleźć w temacie [Tworzenie](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/), [Aktualizowanie](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/), [przenoszenie bazy danych](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)i [Praca ze](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) skryptami.

Dowiedz się więcej o [interfejsie API interfejsu wiersza polecenia platformy Azure o wystąpieniu zarządzanym SQL](../managed-instance/api-references-create-manage-instance.md#azure-cli-create-and-configure-managed-instances).

---
