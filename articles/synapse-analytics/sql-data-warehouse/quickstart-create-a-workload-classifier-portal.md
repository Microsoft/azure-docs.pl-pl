---
title: 'Szybki Start: Tworzenie klasyfikatora obciążeń — Portal'
description: Użyj Azure Portal, aby utworzyć klasyfikator obciążeń o wysokiej ważności.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 05/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 1f4d113f3bc6add67dd34a7ef5e3f8cdc08cecf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98677525"
---
# <a name="quickstart-create-a-dedicated-sql-pool-workload-classifier-using-the-azure-portal"></a>Szybki Start: tworzenie dedykowanego klasyfikatora obciążeń puli SQL przy użyciu Azure Portal

W tym przewodniku szybki start utworzysz [klasyfikator obciążeń](sql-data-warehouse-workload-classification.md) służący do przypisywania zapytań do grupy obciążeń.  Klasyfikator przypisze żądania od `ELTLogin` użytkownika SQL do `DataLoads` grupy obciążeń.   Postępuj zgodnie z samouczkiem [Szybki Start: Konfigurowanie izolacji obciążenia](quickstart-configure-workload-isolation-portal.md) , aby utworzyć `DataLoads` grupę obciążeń.  W tym samouczku zostanie utworzony klasyfikator obciążeń z opcją WLM_LABEL, aby ułatwić dalsze klasyfikowanie żądań.  Klasyfikator przypisze `HIGH` również [ważność obciążeń](sql-data-warehouse-workload-importance.md) do tych żądań.


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.


## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

> [!NOTE]
> Tworzenie dedykowanego wystąpienia puli SQL w usłudze Azure Synapse Analytics może spowodować powstanie nowej usługi do obciążania.  Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku szybki start założono, że masz już dedykowane wystąpienie puli SQL, które ma uprawnienia do kontroli bazy danych. Jeśli trzeba ją utworzyć, użyj instrukcji [Create i Connect-Portal](create-data-warehouse-portal.md) , aby utworzyć dedykowaną pulę SQL o nazwie **mySampleDataWarehouse**.
<br><br>
Grupa obciążenia `DataLoads` istnieje.  Zobacz samouczek [Szybki Start: Konfigurowanie izolacji obciążenia](quickstart-configure-workload-isolation-portal.md) , aby utworzyć grupę obciążeń.
<br><br>
>[!IMPORTANT] 
>Dedykowana Pula SQL musi być w trybie online, aby można było skonfigurować zarządzanie obciążeniami. 


## <a name="create-a-login-for-eltlogin"></a>Utwórz nazwę logowania dla ELTLogin

Utwórz SQL Server Logowanie do uwierzytelniania w `master` bazie danych przy użyciu polecenia [Utwórz login](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) dla `ELTLogin` .

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user-and-grant-permissions"></a>Utwórz użytkownika i Udziel uprawnień

Po utworzeniu nazwy logowania należy utworzyć użytkownika w bazie danych programu.  Użyj instrukcji [Create User](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) , aby utworzyć użytkownika SQL `ELTRole` w **mySampleDataWarehouse**.  Ponieważ będziemy testować klasyfikację w trakcie tego samouczka, udziel `ELTLogin` uprawnień **mySampleDataWarehouse**. 

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
GRANT CONTROL ON DATABASE::mySampleDataWarehouse TO ELTLogin 
END
;
```

## <a name="configure-workload-classification"></a>Konfigurowanie klasyfikacji obciążenia
Klasyfikacja pozwala na kierowanie żądań na podstawie zestawu reguł do grupy obciążeń.  W samouczku [Szybki Start: Konfigurowanie izolacji obciążeń](quickstart-configure-workload-isolation-portal.md) utworzyliśmy `DataLoads` grupę obciążeń.  Teraz utworzysz klasyfikator obciążeń, aby kierować zapytania do `DataLoads` grupy obciążeń.


1.  Przejdź do strony dedykowanej puli SQL **mySampleDataWarehouse** .
3.  Wybierz pozycję **zarządzanie obciążeniami**.

    ![Menu kliknij](./media/quickstart-create-a-workload-classifier-portal/menu.png)

4.  Wybierz pozycję **ustawienia & klasyfikatory** po prawej stronie `DataLoads` grupy obciążenia.

    ![Kliknięcie pozycji Utwórz](./media/quickstart-create-a-workload-classifier-portal/settings-classifiers.png)

5. W kolumnie klasyfikatory wybierz pozycję  **Nieskonfigurowane** .
6. Wybierz pozycję **+ Dodaj klasyfikator**.

    ![Kliknij pozycję Dodaj.](./media/quickstart-create-a-workload-classifier-portal/add-wc.png)

7.  Wprowadź `ELTLoginDataLoads` **nazwę**.
8.  Wprowadź `ELTLogin` dla **elementu członkowskiego**.
9.  Wybierz `High` opcję **ważności żądania**.  *Opcjonalna*, normalna ważność jest domyślna.
10. Wprowadź `fact_loads` dla **etykiety**.
11. Wybierz pozycję **Dodaj**.
12. Wybierz pozycję **Zapisz**.

    ![Kliknij pozycję Konfiguracja](./media/quickstart-create-a-workload-classifier-portal/config-wc.png)

## <a name="verify-and-test-classification"></a>Weryfikuj i Testuj klasyfikację
Sprawdź widok wykazu [sys.workload_management_workload_classifiers](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql?view=azure-sqldw-latest&preserve-view=true) , aby sprawdzić istnienie `ELTLoginDataLoads` klasyfikatora.

```sql
SELECT * FROM sys.workload_management_workload_classifiers WHERE name = 'ELTLoginDataLoads'
```

Sprawdź widok wykazu [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest&preserve-view=true) , aby zweryfikować szczegóły klasyfikatora.

```sql
SELECT c.[name], c.group_name, c.importance, cd.classifier_type, cd.classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ELTLoginDataLoads'
```

Uruchom następujące instrukcje, aby przetestować klasyfikację.  Upewnij się, że nawiązano połączenie z usługą ``ELTLogin`` i ``Label`` jest używany w zapytaniu.
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

Sprawdź `CREATE TABLE` instrukcję zaklasyfikowaną do `DataLoads` grupy obciążeń przy użyciu `ELTLoginDataLoads` klasyfikatora obciążeń.
```sql 
SELECT TOP 1 request_id, classifier_name, group_name, resource_allocation_percentage, submit_time, [status], [label], command 
FROM sys.dm_pdw_exec_requests 
WHERE [label] = 'fact_loads'
ORDER BY submit_time DESC
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć `ELTLoginDataLoads` klasyfikator obciążeń utworzony w tym samouczku:

1. Kliknij **1 klasyfikator** po prawej stronie `DataLoads` grupy obciążenia.

    ![Kliknij pozycję Usuń.](./media/quickstart-create-a-workload-classifier-portal/delete-wc.png)

2. Kliknij **klasyfikatora**.
3. Kliknij z **`...`** prawej strony `ELTLoginDataLoads` klasyfikatora obciążenia.
4. Kliknij pozycję **Usuń**.
5. Kliknij pozycję **Zapisz**.

    ![Klikanie pozycji Zapisz.](./media/quickstart-create-a-workload-classifier-portal/delete-save-wc.png)

Opłaty są naliczane za jednostki magazynu danych i dane przechowywane w dedykowanej puli SQL. Opłaty za te zasoby obliczeniowe i magazynowe są naliczane osobno.

- Jeśli chcesz zachować dane w magazynie, możesz wstrzymać obliczenia, gdy nie korzystasz z dedykowanej puli SQL. Przez wstrzymywanie obliczeń opłata jest naliczana tylko za magazyn danych. Gdy wszystko będzie gotowe do pracy z danymi, Wznów obliczenia.
- Jeśli chcesz usunąć przyszłe opłaty, możesz usunąć dedykowaną pulę SQL.

Wykonaj następujące kroki, aby wyczyścić zasoby.

1. Zaloguj się do [Azure Portal](https://portal.azure.com), wybierz dedykowaną pulę SQL.

    ![Czyszczenie zasobów](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Aby wstrzymać obliczenia, wybierz przycisk **Wstrzymaj** . Gdy dedykowana Pula SQL jest wstrzymana, zobaczysz przycisk **Start** .  Aby wznowić obliczenia, wybierz pozycję **Uruchom**.

3. Aby usunąć dedykowaną pulę SQL, aby nie naliczać opłat za zasoby obliczeniowe i magazynowanie, wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Monitoruj obciążenie przy użyciu metryk monitorowania Azure Portal.  Aby uzyskać szczegółowe informacje [, zobacz zarządzanie obciążeniami i monitorowanie](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) .
