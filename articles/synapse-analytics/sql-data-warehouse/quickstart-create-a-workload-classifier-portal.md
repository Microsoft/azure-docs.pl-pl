---
title: 'Szybki start: tworzenie klasyfikatora obciążenia — portal'
description: Użyj Azure Portal, aby utworzyć klasyfikator obciążenia o wysokiej ważności.
services: synapse-analytics
author: ronortloff
ms.author: rortloff
manager: craigg
ms.reviewer: jrasnick
ms.date: 05/04/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- azure-synapse
- mode-portal
ms.openlocfilehash: 6f1997a80e66d1ca7928afc02acf92f376fc3376
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534278"
---
# <a name="quickstart-create-a-dedicated-sql-pool-workload-classifier-using-the-azure-portal"></a>Szybki start: tworzenie klasyfikatora obciążenia dedykowanej puli SQL przy użyciu Azure Portal

W tym przewodniku Szybki start utworzysz klasyfikator [obciążenia](sql-data-warehouse-workload-classification.md) na potrzeby przypisywania zapytań do grupy obciążeń.  Klasyfikator przypisze żądania od `ELTLogin` użytkownika SQL do grupy `DataLoads` obciążeń.   Postępuj zgodnie z [samouczkiem Szybki start: konfigurowanie izolacji obciążeń,](quickstart-configure-workload-isolation-portal.md) aby utworzyć `DataLoads` grupę obciążeń.  W tym samouczku utworzysz klasyfikator obciążenia z opcją WLM_LABEL, aby ułatwić poprawne klasyfikowanie żądań.  Klasyfikator przypisze również `HIGH` [ważność](sql-data-warehouse-workload-importance.md) obciążenia do tych żądań.


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.


## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

> [!NOTE]
> Utworzenie dedykowanego wystąpienia puli SQL w Azure Synapse Analytics może spowodować utworzenie nowej rozliczanej usługi.  Aby uzyskać więcej informacji, [zobacz Azure Synapse Analytics cennika](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku Szybki start założono, że masz już dedykowane wystąpienie puli SQL z uprawnieniami CONTROL DATABASE. Jeśli musisz ją utworzyć, użyj polecenia Utwórz i połącz — [portal,](create-data-warehouse-portal.md) aby utworzyć dedykowaną pulę SQL o nazwie **mySampleDataWarehouse.**
<br><br>
Istnieje grupa `DataLoads` obciążeń.  Zobacz [samouczek Szybki start: konfigurowanie izolacji obciążeń,](quickstart-configure-workload-isolation-portal.md) aby utworzyć grupę obciążeń.
<br><br>
>[!IMPORTANT] 
>Dedykowana pula SQL musi być w trybie online, aby skonfigurować zarządzanie obciążeniami. 


## <a name="create-a-login-for-eltlogin"></a>Tworzenie identyfikatora logowania dla ELTLogin

Utwórz identyfikator SQL Server logowania do uwierzytelniania w bazie `master` danych przy użyciu funkcji CREATE [LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) dla . `ELTLogin`

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user-and-grant-permissions"></a>Tworzenie użytkownika i udzielanie uprawnień

Po utworzeniu identyfikatora logowania użytkownik musi zostać utworzony w bazie danych.  Użyj [polecenia CREATE USER,](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) aby utworzyć użytkownika SQL `ELTRole` w bazie danych **mySampleDataWarehouse.**  Ponieważ podczas pracy z tym samouczkiem będziemy testować klasyfikację, `ELTLogin` przyznaj uprawnienia do **usługi mySampleDataWarehouse.** 

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
GRANT CONTROL ON DATABASE::mySampleDataWarehouse TO ELTLogin 
END
;
```

## <a name="configure-workload-classification"></a>Konfigurowanie klasyfikacji obciążeń
Klasyfikacja umożliwia przekierowanie żądań na podstawie zestawu reguł do grupy obciążeń.  W [samouczku Szybki start: konfigurowanie izolacji obciążeń](quickstart-configure-workload-isolation-portal.md) została utworzona `DataLoads` grupa obciążeń.  Teraz utworzysz klasyfikator obciążenia w celu przekierowania zapytań do `DataLoads` grupy obciążeń.


1.  Przejdź do strony dedykowanej puli SQL **mySampleDataWarehouse.**
3.  Wybierz **pozycję Zarządzanie obciążeniami.**

    ![Kliknij menu](./media/quickstart-create-a-workload-classifier-portal/menu.png)

4.  Wybierz **pozycję & klasyfikatory** po prawej stronie `DataLoads` grupy obciążeń.

    ![Kliknięcie pozycji Utwórz](./media/quickstart-create-a-workload-classifier-portal/settings-classifiers.png)

5. Wybierz  **pozycję Nieskonfigurowane** w kolumnie Klasyfikatory.
6. Wybierz **pozycję + Dodaj klasyfikator**.

    ![Kliknij pozycję Dodaj.](./media/quickstart-create-a-workload-classifier-portal/add-wc.png)

7.  Wprowadź `ELTLoginDataLoads` wartość w **nazwie**.
8.  Wprowadź `ELTLogin` wartość w przypadku **member**.
9.  Wybierz `High` dla **opcji Ważność żądania**.  *Opcjonalne*, normalna ważność jest domyślna.
10. Wprowadź `fact_loads` wartość w przypadku **etykiety**.
11. Wybierz pozycję **Dodaj**.
12. Wybierz pozycję **Zapisz**.

    ![Kliknij pozycję Config (Konfiguracja)](./media/quickstart-create-a-workload-classifier-portal/config-wc.png)

## <a name="verify-and-test-classification"></a>Weryfikowanie i testowanie klasyfikacji
Sprawdź widok [sys.workload_management_workload_classifiers](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql?view=azure-sqldw-latest&preserve-view=true) katalogu, aby zweryfikować istnienie `ELTLoginDataLoads` klasyfikatora.

```sql
SELECT * FROM sys.workload_management_workload_classifiers WHERE name = 'ELTLoginDataLoads'
```

Sprawdź widok [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest&preserve-view=true) wykazu, aby zweryfikować szczegóły klasyfikatora.

```sql
SELECT c.[name], c.group_name, c.importance, cd.classifier_type, cd.classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ELTLoginDataLoads'
```

Uruchom następujące instrukcje, aby przetestować klasyfikację.  Upewnij się, że nałączono połączenie ``ELTLogin`` jako i jest używane w ``Label`` zapytaniu.
```sql
CREATE TABLE factstaging (ColA int)
INSERT INTO factstaging VALUES(0)
INSERT INTO factstaging VALUES(1)
INSERT INTO factstaging VALUES(2)
GO

CREATE TABLE testclassifierfact WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT * FROM factstaging
OPTION (LABEL='fact_loads')
```

Sprawdź, `CREATE TABLE` czy instrukcja została `DataLoads` sklasyfikowana do grupy obciążeń przy użyciu `ELTLoginDataLoads` klasyfikatora obciążenia.
```sql 
SELECT TOP 1 request_id, classifier_name, group_name, resource_allocation_percentage, submit_time, [status], [label], command 
FROM sys.dm_pdw_exec_requests 
WHERE [label] = 'fact_loads'
ORDER BY submit_time DESC
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć `ELTLoginDataLoads` klasyfikator obciążenia utworzony w tym samouczku:

1. Kliknij pozycję **1 Classifier** (Klasyfikator 1) po prawej stronie `DataLoads` grupy obciążeń.

    ![Kliknij pozycję Usuń.](./media/quickstart-create-a-workload-classifier-portal/delete-wc.png)

2. Kliknij pozycję **Klasyfikatory.**
3. Kliknij pozycję **`...`** po prawej stronie `ELTLoginDataLoads` klasyfikatora obciążenia.
4. Kliknij pozycję **Usuń.**
5. Kliknij pozycję **Zapisz**.

    ![Klikanie pozycji Zapisz.](./media/quickstart-create-a-workload-classifier-portal/delete-save-wc.png)

Opłaty są naliczane za jednostki magazynu danych i dane przechowywane w dedykowanej puli SQL. Opłaty za te zasoby obliczeniowe i magazynowe są naliczane osobno.

- Jeśli chcesz przechowywać dane w magazynie, możesz wstrzymać obliczenia, gdy nie używasz dedykowanej puli SQL. Wstrzymanie obliczeń pozwala na naliczanie opłat tylko za magazyn danych. Gdy wszystko będzie gotowe do pracy z danymi, wznów obliczenia.
- Jeśli chcesz usunąć przyszłe opłaty, możesz usunąć dedykowaną pulę SQL.

Wykonaj następujące kroki, aby wyczyścić zasoby.

1. Zaloguj się do [Azure Portal](https://portal.azure.com), wybierz dedykowaną pulę SQL.

    ![Czyszczenie zasobów](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Aby wstrzymać obliczenia, wybierz przycisk **Wstrzymaj.** Po wstrzymaniu dedykowanej puli SQL zostanie wyświetlony **przycisk** Uruchom.  Aby wznowić obliczenia, wybierz pozycję **Uruchom.**

3. Aby usunąć dedykowaną pulę SQL, aby nie były naliczane opłaty za zasoby obliczeniowe lub magazyn, wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Monitoruj obciążenie przy użyciu Azure Portal monitorowania.  Aby [uzyskać szczegółowe informacje, zobacz Monitorowanie zarządzania obciążeniami](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) i zarządzanie nimi.
