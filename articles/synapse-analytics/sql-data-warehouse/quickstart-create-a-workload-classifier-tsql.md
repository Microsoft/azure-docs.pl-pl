---
title: 'Szybki Start: Tworzenie klasyfikatora obciążeń — T-SQL'
description: Użyj języka T-SQL, aby utworzyć klasyfikator obciążeń o wysokiej ważności.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: a63cd95fdcee7c9ed0c49ba41b4d7e7e6de8f4bf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458726"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>Szybki Start: Tworzenie klasyfikatora obciążeń przy użyciu języka T-SQL

W tym przewodniku szybki start utworzysz klasyfikator obciążeń o wysokiej ważności dla DYREKTORa w organizacji. Ten klasyfikator obciążeń umożliwi zajęcie przez dyrektorów naczelnych pierwszeństwa przed innymi zapytaniami o niższej ważności kolejki.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

> [!NOTE]
> Tworzenie dedykowanego wystąpienia puli SQL w usłudze Azure Synapse Analytics może spowodować powstanie nowej usługi do obciążania.  Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku szybki start założono, że masz już zainicjowaną dedykowaną pulę SQL w usłudze Azure Synapse Analytics i że masz uprawnienia sterujące bazą danych. Jeśli trzeba ją utworzyć, użyj instrukcji [Create i Connect-Portal](create-data-warehouse-portal.md) , aby utworzyć dedykowaną pulę SQL o nazwie **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-login-for-theceo"></a>Utwórz nazwę logowania dla TheCEO

Utwórz nazwę logowania SQL Server uwierzytelnienia w `master` bazie danych przy użyciu polecenia [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) for "TheCEO".

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Tworzenie użytkownika

[Tworzenie użytkownika](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), "TheCEO" w mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>Tworzenie klasyfikatora obciążeń

Utwórz [klasyfikator obciążeń](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) dla "TheCEO" o wysokiej ważności.

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>Wyświetl istniejące klasyfikatory

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

Opłaty są naliczane za jednostki magazynu danych i dane przechowywane w dedykowanej puli SQL. Opłaty za te zasoby obliczeniowe i magazynowe są naliczane osobno.

- Jeśli chcesz zachować dane w magazynie, możesz wstrzymać obliczenia, gdy nie używasz dedykowanej puli SQL. Przez wstrzymywanie obliczeń opłata jest naliczana tylko za magazyn danych. Gdy wszystko będzie gotowe do pracy z danymi, Wznów obliczenia.
- Jeśli chcesz usunąć przyszłe opłaty, możesz usunąć dedykowaną pulę SQL.

Wykonaj następujące kroki, aby wyczyścić zasoby.

1. Zaloguj się do [Azure Portal](https://portal.azure.com), wybierz dedykowaną pulę SQL.

    ![Czyszczenie zasobów](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Aby wstrzymać obliczenia, wybierz przycisk **Wstrzymaj** . Gdy dedykowana Pula SQL jest wstrzymana, zobaczysz przycisk **Start** .  Aby wznowić obliczenia, wybierz pozycję **Uruchom**.

3. Aby usunąć dedykowaną pulę SQL, aby nie naliczać opłat za zasoby obliczeniowe i magazynowanie, wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

- Klasyfikator obciążeń został utworzony. Uruchom kilka zapytań jako TheCEO, aby zobaczyć, jak są one wykonywane. Zobacz [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) , aby wyświetlać zapytania i przypisane znaczenie.
- Aby uzyskać więcej informacji na temat dedykowanego zarządzania obciążeniem puli SQL, zobacz temat [ważność obciążeń](sql-data-warehouse-workload-importance.md) i [Klasyfikacja obciążeń](sql-data-warehouse-workload-classification.md).
- Zapoznaj się z artykułami z artykułu jak, aby [skonfigurować ważność obciążenia](sql-data-warehouse-how-to-configure-workload-importance.md) oraz jak [zarządzać i monitorować zarządzanie obciążeniami](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
