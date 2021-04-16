---
title: Nawiązywanie połączenia Synapse SQL pomocą Azure Data Studio
description: Użyj Azure Data Studio, aby nawiązać połączenie z Synapse SQL i Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 1529e45bd7f799a727a29c8c2e26f7ed77c4e2a0
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565597"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio"></a>Nawiązywanie połączenia Synapse SQL pomocą Azure Data Studio

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Za pomocą Azure Data Studio [nawiązać](/sql/azure-data-studio/download-azure-data-studio?view=azure-sqldw-latest&preserve-view=true) połączenie i odpyt Synapse SQL w Azure Synapse Analytics. 

## <a name="connect"></a>Nawiązywanie połączenia

Aby nawiązać połączenie z Synapse SQL, otwórz Azure Data Studio wybierz **pozycję Nowe połączenie.**

![Otwórz Azure Data Studio](./media/get-started-azure-data-studio/1-start.png)

Wybierz **Microsoft SQL Server** jako **typ połączenia**.

Połączenie wymaga następujących parametrów:

* **Serwer:** Serwer w postaci `<Azure Synapse workspace name>` -ondemand.sql.azuresynapse.net
* **Baza danych:** Nazwa bazy danych

> [!NOTE]
> Jeśli chcesz użyć bez serwera puli **SQL,** adres URL powinien wyglądać tak:
>
> - `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net.
>
> Jeśli chcesz użyć dedykowanej **puli SQL,** adres URL powinien wyglądać tak:
>
> - `<Azure Synapse workspace name>`.sql.azuresynapse.net

Wybierz **opcję Uwierzytelnianie** systemu **Windows, Azure Active Directory** lub **Identyfikator logowania SQL** jako **typ uwierzytelniania.**

Aby użyć **nazwy logowania SQL** jako typu uwierzytelniania, dodaj parametry nazwy użytkownika/hasła:

* **Użytkownik:** Użytkownik serwera w formularzu `<User>`
* **Hasło:** Hasło skojarzone z użytkownikiem

Aby użyć Azure Active Directory, należy wybrać wymagany typ uwierzytelniania.

![Uwierzytelnianie za pomocą usługi AAD](./media/get-started-azure-data-studio/3-aad-auth.png)

Poniższy zrzut ekranu przedstawia **szczegóły połączenia dla** **uwierzytelniania systemu Windows:**

![Uwierzytelnianie systemu Windows](./media/get-started-azure-data-studio/3-windows-auth.png)

Poniższy zrzut ekranu przedstawia szczegóły **połączenia przy użyciu** logowania **SQL:**

![Identyfikator logowania SQL](./media/get-started-azure-data-studio/2-database-details.png)

Po pomyślnym zalogowaniu powinien zostać wyświetlony pulpit nawigacyjny podobny do tego: ![ Pulpit nawigacyjny](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>Zapytanie

Po nasyłaniu połączenia można Synapse SQL za pomocą obsługiwanych instrukcji [Języka Transact-SQL (T-SQL)](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) względem wystąpienia. Wybierz **pozycję Nowe zapytanie** w widoku pulpitu nawigacyjnego, aby rozpocząć pracę.

![Nowe zapytanie](./media/get-started-azure-data-studio/5-new-query.png)

Na przykład można użyć następującej instrukcji Transact-SQL do wykonywania zapytań o pliki [Parquet](query-parquet-files.md) przy użyciu bez serwera puli SQL:

```sql
SELECT COUNT(*)
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
    FORMAT='PARQUET'
)
```
## <a name="next-steps"></a>Następne kroki 
Poznaj inne sposoby nawiązywania połączenia z Synapse SQL: 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Sqlcmd](get-started-connect-sqlcmd.md)

Aby [uzyskać więcej informacji,](/sql/azure-data-studio/quickstart-sql-dw)Azure Data Studio use Azure Data Studio to connect and query data using a dedicated SQL pool in Azure Synapse Analytics , (Nawiązywanie połączeń i wykonywanie zapytań dotyczących danych przy użyciu dedykowanej puli SQL w programie Azure Synapse Analytics ).
