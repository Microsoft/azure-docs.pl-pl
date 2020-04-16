---
title: 'Usługa Azure Data Studio (wersja zapoznawcza): łączenie i wykonywanie zapytań dotyczących programu Synapse SQL'
description: Użyj usługi Azure Data Studio (wersja zapoznawcza), aby połączyć się z programem Synapse SQL i wysyłać kwerendy w usłudze Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: edf5a6a6a0f17c21abb818a0d41d0d0b1c39949c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423803"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio-preview"></a>Łączenie się z programem Synapse SQL za pomocą usługi Azure Data Studio (wersja zapoznawcza)

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Program Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Usługi [Azure Data Studio (wersja zapoznawcza)](/sql/azure-data-studio/download-azure-data-studio?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) można używać do łączenia się z programem Synapse SQL i wykonywania zapytań w usłudze Azure Synapse Analytics. 

## <a name="connect"></a>Połącz

Aby połączyć się z programem Synapse SQL, otwórz usługę Azure Data Studio i wybierz pozycję **Nowe połączenie**.

![Otwórz program Azure Data Studio](./media/get-started-azure-data-studio/1-start.png)

Wybierz **program Microsoft SQL Server** jako typ **połączenia**.

Połączenie wymaga następujących parametrów:

* **Serwer:** Serwer w `<Azure Synapse workspace name>`postaci -ondemand.sql.azuresynapse.net
* **Baza danych:** Nazwa bazy danych

> [!NOTE]
> Jeśli chcesz używać **SQL na żądanie (wersja zapoznawcza),** adres URL powinien wyglądać następująco:
>
> - `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net.
>
> Jeśli chcesz użyć **puli SQL** adres URL powinien wyglądać następująco:
>
> - `<Azure Synapse workspace name>`sql.azuresynapse.net .sql.azuresynapse.net

Wybierz **uwierzytelnianie systemu Windows,** **usługę Azure Active Directory**lub sql **login** jako **typ uwierzytelniania**.

Aby użyć **logowania SQL** jako typu uwierzytelniania, dodaj parametry nazwy użytkownika/hasła:

* **Użytkownik:** Użytkownik serwera w formularzu`<User>`
* **Hasło:** Hasło skojarzone z użytkownikiem

Aby korzystać z usługi Azure Active Directory, należy wybrać typ uwierzytelniania wymagane.

![Uwierzytelnianie za pomocą usługi AAD](./media/get-started-azure-data-studio/3-aad-auth.png)

Ten zrzut ekranu przedstawia **szczegóły połączenia** dla **uwierzytelniania systemu Windows:**

![Uwierzytelnianie systemu Windows](./media/get-started-azure-data-studio/3-windows-auth.png)

Ten zrzut ekranu przedstawia **szczegóły połączenia** przy użyciu **logowania SQL:**

![Identyfikator logowania SQL](./media/get-started-azure-data-studio/2-database-details.png)

Po pomyślnym zalogowaniu powinien zostać ![wyświetlony pulpit nawigacyjny w ten sposób: Pulpit nawigacyjny](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>Zapytanie

Po nawiązaniu połączenia można wysyłać zapytania do synapse SQL przy użyciu obsługiwanych instrukcji [Transact-SQL (T-SQL)](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) względem wystąpienia. Wybierz **pozycję Nowa kwerenda** z widoku pulpitu nawigacyjnego, aby rozpocząć.

![Nowe zapytanie](./media/get-started-azure-data-studio/5-new-query.png)

Na przykład można użyć następującej instrukcji Transact-SQL do wykonywania zapytań o [pliki parkietu](query-parquet-files.md) przy użyciu języka SQL na żądanie:

```sql
SELECT COUNT(*)
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
    FORMAT='PARQUET'
)
```
## <a name="next-steps"></a>Następne kroki 
Poznaj inne sposoby łączenia się z synapse SQL: 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Program Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Sqlcmd](get-started-connect-sqlcmd.md)
 
