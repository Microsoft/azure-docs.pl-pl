---
title: Synchronizuj dane z usługi Azure SQL Edge przy użyciu SQL Data Sync
description: Informacje o synchronizowaniu danych z usługi Azure SQL Edge przy użyciu usługi Azure SQL Data Sync
keywords: SQL Edge, synchronizacja danych z programu SQL Edge, synchronizacja danych programu SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5659ce5fa6f99463f58a33662563d768248fd8cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93394904"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Samouczek: synchronizacja danych z programu SQL Edge do Azure SQL Database przy użyciu SQL Data Sync

W tym samouczku dowiesz się, jak używać *grupy synchronizacji* SQL Data Sync Azure do przyrostowej synchronizacji danych z usługi Azure SQL Edge do Azure SQL Database. SQL Data Sync to usługa oparta na Azure SQL Database, która pozwala synchronizować dane, które są wybierane dwukierunkowo dla wielu baz danych w Azure SQL Database i SQL Server wystąpieniach. Aby uzyskać więcej informacji na temat SQL Data Sync, zobacz [Azure SQL Data Sync](../azure-sql/database/sql-data-sync-data-sql-server-sql-database.md).

Ponieważ usługa SQL Edge jest oparta na najnowszych wersjach [aparatu bazy danych SQL Server](/sql/sql-server/sql-server-technical-documentation/), można także użyć dowolnego mechanizmu synchronizacji danych, który ma zastosowanie do wystąpienia SQL Server, do synchronizowania danych z wystąpieniem programu SQL Edge działającym na urządzeniu brzegowym lub z niego.

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek wymaga komputera z systemem Windows, który został skonfigurowany przy użyciu [agenta synchronizacji danych dla platformy Azure SQL Data Sync](../azure-sql/database/sql-data-sync-agent-overview.md).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

* Utwórz bazę danych w Azure SQL Database. Aby uzyskać informacje na temat sposobu tworzenia bazy danych przy użyciu Azure Portal, zobacz [Tworzenie pojedynczej bazy danych w Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal).

* Utwórz tabele i inne niezbędne obiekty we wdrożeniu Azure SQL Database.

* Utwórz niezbędne tabele i obiekty w wdrożeniu usługi Azure SQL Edge. Aby uzyskać więcej informacji, zobacz [Używanie pakietów DAC SQL Database z programem SQL Edge](deploy-dacpac.md).

* Zarejestruj wystąpienie usługi Azure SQL Edge przy użyciu agenta synchronizacji danych dla platformy Azure SQL Data Sync. Aby uzyskać więcej informacji, zobacz [Dodawanie bazy danych SQL Server](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-on-prem).

## <a name="sync-data-between-a-database-in-azure-sql-database-and-sql-edge"></a>Synchronizowanie danych między bazą danych w Azure SQL Database i SQL Edge

Konfigurowanie synchronizacji między bazą danych w Azure SQL Database i wystąpieniem programu SQL Edge przy użyciu SQL Data Sync obejmuje trzy kluczowe kroki:  


1. Użyj Azure Portal, aby utworzyć grupę synchronizacji. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy synchronizacji](../azure-sql/database/sql-data-sync-sql-server-configure.md#create-sync-group). Pojedynczej bazy danych można użyć do utworzenia wielu grup synchronizacji w celu synchronizowania danych z różnych wystąpień programu SQL Edge do co najmniej jednej *bazy danych w* Azure SQL Database. 

2. Dodaj elementy członkowskie synchronizacji do grupy synchronizacji. Aby uzyskać więcej informacji, zobacz [Dodawanie elementów członkowskich synchronizacji](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members).

3. Skonfiguruj grupę synchronizacji, aby wybrać tabele, które będą częścią synchronizacji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie grupy synchronizacji](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members).

Po wykonaniu powyższych kroków będziesz mieć grupę synchronizacji zawierającą bazę danych w Azure SQL Database i wystąpieniem programu SQL Edge.

Aby uzyskać więcej informacji na temat SQL Data Sync, zobacz następujące artykuły:

* [Agent synchronizacji danych dla platformy Azure SQL Data Sync](../azure-sql/database/sql-data-sync-agent-overview.md)

* [Najlepsze rozwiązania](../azure-sql/database/sql-data-sync-best-practices.md) i [Rozwiązywanie problemów z usługą Azure SQL Data Sync](../azure-sql/database/sql-data-sync-troubleshoot.md)

* [Monitorowanie SQL Data Sync przy użyciu dzienników Azure Monitor](../azure-sql/database/monitor-tune-overview.md)

* [Aktualizowanie schematu synchronizacji przy użyciu języka Transact-SQL](../azure-sql/database/sql-data-sync-update-sync-schema.md) lub [programu PowerShell](../azure-sql/database/scripts/update-sync-schema-in-sync-group.md)

## <a name="next-steps"></a>Następne kroki


* [Użyj programu PowerShell, aby synchronizować między Azure SQL Database i Azure SQL Edge](../azure-sql/database/scripts/sql-data-sync-sync-data-between-azure-onprem.md). W tym samouczku Zastąp `OnPremiseServer` szczegóły bazy danych informacjami o usłudze Azure SQL Edge.