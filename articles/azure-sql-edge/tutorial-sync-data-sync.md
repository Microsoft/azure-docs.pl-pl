---
title: Synchronizuj dane z usługi Azure SQL Edge (wersja zapoznawcza) za pomocą SQL Data Sync
description: Informacje o synchronizowaniu danych z usługi Azure SQL Edge (wersja zapoznawcza) przy użyciu usługi Azure SQL Data Sync
keywords: SQL Edge, synchronizacja danych z programu SQL Edge, synchronizacja danych programu SQL Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7971681c3f0c99a11567e6a30e61167c5d42348c
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680512"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Samouczek: synchronizacja danych z programu SQL Edge do Azure SQL Database przy użyciu SQL Data Sync

W tym samouczku dowiesz się, jak używać *grupy synchronizacji* SQL Data Sync Azure do przyrostowej synchronizacji danych z usługi Azure SQL Edge do Azure SQL Database. SQL Data Sync to usługa oparta na Azure SQL Database, która pozwala synchronizować dane, które są wybierane dwukierunkowo w wielu bazach danych SQL i wystąpieniach SQL Server. Aby uzyskać więcej informacji na temat SQL Data Sync, zobacz [Azure SQL Data Sync](../sql-database/sql-database-sync-data.md).

Ponieważ usługa SQL Edge jest oparta na najnowszych wersjach [aparatu bazy danych SQL Server](/sql/sql-server/sql-server-technical-documentation/), można także użyć dowolnego mechanizmu synchronizacji danych, który jest stosowany do lokalnego wystąpienia SQL Server, do synchronizowania danych z wystąpieniem programu SQL Edge działającym na urządzeniu brzegowym lub z niego.

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek wymaga komputera z systemem Windows, który został skonfigurowany przy użyciu [agenta synchronizacji danych dla platformy Azure SQL Data Sync](../sql-database/sql-database-data-sync-agent.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Utworzenie bazy danych Azure SQL Database. Aby uzyskać informacje na temat sposobu tworzenia bazy danych Azure SQL Database przy użyciu Azure Portal, zobacz [Tworzenie pojedynczej bazy danych w programie Azure SQL Database](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* Utwórz tabele i inne niezbędne obiekty we wdrożeniu Azure SQL Database.

* Utwórz niezbędne tabele i obiekty w wdrożeniu usługi Azure SQL Edge. Aby uzyskać więcej informacji, zobacz [Używanie pakietów DAC SQL Database z programem SQL Edge](deploy-dacpac.md).

* Zarejestruj wystąpienie usługi Azure SQL Edge przy użyciu agenta synchronizacji danych dla platformy Azure SQL Data Sync. Aby uzyskać więcej informacji, zobacz [Dodawanie lokalnej bazy danych SQL Server](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-edge"></a>Synchronizowanie danych między bazą danych SQL Azure a programem SQL Edge

Konfigurowanie synchronizacji między bazą danych Azure SQL Database i wystąpieniem programu SQL Edge przy użyciu SQL Data Sync obejmuje trzy kluczowe kroki:  

1. Użyj Azure Portal, aby utworzyć grupę synchronizacji. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy synchronizacji](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). Pojedynczej bazy danych można użyć do utworzenia wielu grup synchronizacji w celu synchronizowania danych z różnych wystąpień programu SQL Edge do co najmniej *jednej bazy danych* SQL na platformie Azure.

2. Dodaj elementy członkowskie synchronizacji do grupy synchronizacji. Aby uzyskać więcej informacji, zobacz [Dodawanie elementów członkowskich synchronizacji](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Skonfiguruj grupę synchronizacji, aby wybrać tabele, które będą częścią synchronizacji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie grupy synchronizacji](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

Po wykonaniu powyższych kroków będziesz mieć grupę synchronizacji obejmującą bazę danych SQL Azure i wystąpienie programu SQL Edge.

Aby uzyskać więcej informacji na temat SQL Data Sync, zobacz następujące artykuły:

* [Agent synchronizacji danych dla platformy Azure SQL Data Sync](../sql-database/sql-database-data-sync-agent.md)

* [Najlepsze rozwiązania](../sql-database/sql-database-best-practices-data-sync.md) i [Rozwiązywanie problemów z usługą Azure SQL Data Sync](../sql-database/sql-database-troubleshoot-data-sync.md)

* [Monitorowanie SQL Data Sync przy użyciu dzienników Azure Monitor](../sql-database/sql-database-sync-monitor-oms.md)

* [Aktualizowanie schematu synchronizacji przy użyciu języka Transact-SQL](../sql-database/sql-database-update-sync-schema.md) lub [programu PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Następne kroki

* [Użyj programu PowerShell, aby synchronizować między Azure SQL Database i Azure SQL Edge](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). W tym samouczku Zastąp `OnPremiseServer` szczegóły bazy danych informacjami o usłudze Azure SQL Edge.
