---
title: Authentication
description: Dowiedz się, jak uwierzytelniać się w usłudze Azure Synapse Analytics przy użyciu usługi Azure Active Directory (Azure AD) lub SQL Server Authentication.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: 29709dc03ee3a06bdf2aec2587909a08ee13504e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85206734"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>Uwierzytelnianie w usłudze Azure Synapse Analytics

Dowiedz się, jak uwierzytelniać się w usłudze SQL Synapse w usłudze Azure Synapse przy użyciu usług Azure Active Directory (AAD) lub SQL Server Authentication.

Aby nawiązać połączenie z pulą SQL, należy przekazać poświadczenia zabezpieczeń w celu uwierzytelnienia. Podczas ustanawiania połączenia niektóre ustawienia połączeń są konfigurowane w ramach ustanawiania sesji zapytań.  

Aby uzyskać więcej informacji na temat zabezpieczeń i sposobu włączania połączeń z magazynem danych, zobacz [Zabezpieczanie dokumentacji bazy danych](sql-data-warehouse-overview-manage-security.md).

## <a name="sql-authentication"></a>Uwierzytelnianie SQL

Aby połączyć się z pulą SQL, należy podać następujące informacje:

* W pełni kwalifikowana ServerName
* Określanie uwierzytelniania SQL
* Nazwa użytkownika
* Hasło
* Domyślna baza danych (opcjonalnie)

Domyślnie połączenie jest nawiązywane z bazą danych *Master* , a nie z bazą danych użytkownika. Aby nawiązać połączenie z bazą danych użytkownika, możesz wykonać jedną z dwóch czynności:

* Określ domyślną bazę danych podczas rejestrowania serwera przy użyciu Eksplorator obiektów SQL Server w SSDT, SSMS lub w parametrach połączenia aplikacji. Na przykład Dołącz parametr InitialCatalog dla połączenia ODBC.
* Wyróżnij bazę danych użytkownika przed utworzeniem sesji w programie SSDT.

> [!NOTE]
> Instrukcja języka Transact-SQL **Użyj elementu webdatabase;** nie jest obsługiwana w przypadku zmiany bazy danych dla połączenia. Aby uzyskać wskazówki dotyczące łączenia się z pulą SQL za pomocą SSDT, zobacz [zapytanie z programem Visual Studio](sql-data-warehouse-query-visual-studio.md) .

## <a name="azure-active-directory-aad-authentication"></a>Uwierzytelnianie Azure Active Directory (AAD)

Uwierzytelnianie [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) jest mechanizmem łączenia się z pulą SQL przy użyciu tożsamości w usłudze Azure Active Directory (Azure AD). Przy użyciu uwierzytelniania Azure Active Directory można centralnie zarządzać tożsamościami użytkowników bazy danych i innych usług firmy Microsoft w jednej centralnej lokalizacji. Centralne zarządzanie IDENTYFIKATORami oferuje jedno miejsce do zarządzania użytkownikami usługi Azure Synapse i upraszcza zarządzanie uprawnieniami.

### <a name="benefits"></a>Korzyści

Korzyści Azure Active Directory obejmują:

* Stanowi alternatywę dla SQL Server uwierzytelniania.
* Pomaga zatrzymać rozprzestrzenianie tożsamości użytkowników między serwerami.
* Umożliwia rotację haseł w jednym miejscu
* Zarządzanie uprawnieniami bazy danych przy użyciu zewnętrznych grup (Azure AD).
* Eliminuje przechowywanie haseł, włączając zintegrowane uwierzytelnianie systemu Windows i formy uwierzytelniania obsługiwane przez Azure Active Directory.
* Używa użytkowników zawartej bazy danych do uwierzytelniania tożsamości na poziomie bazy danych.
* Obsługuje uwierzytelnianie oparte na tokenach dla aplikacji łączących się z pulą SQL.
* Obsługuje uwierzytelnianie wieloskładnikowe za Active Directory uniwersalnego uwierzytelniania dla różnych narzędzi, w tym [SQL Server Management Studio](../../azure-sql/database/authentication-mfa-ssms-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) i [SQL Server narzędzi](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)do obsługi danych.

> [!NOTE]
> Azure Active Directory nadal jest stosunkowo nowy i ma pewne ograniczenia. Aby upewnić się, że Azure Active Directory jest dobrze dopasowane do danego środowiska, zobacz [funkcje i ograniczenia usługi Azure AD](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#azure-ad-features-and-limitations), w tym dodatkowe zagadnienia.

### <a name="configuration-steps"></a>Kroki konfiguracji

Wykonaj następujące kroki, aby skonfigurować uwierzytelnianie Azure Active Directory.

1. Tworzenie i wypełnianie Azure Active Directory
2. Opcjonalnie: Skojarz lub Zmień usługę Active Directory, która jest aktualnie skojarzona z subskrypcją platformy Azure
3. Tworzenie Azure Active Directory administratora dla usługi Azure Synapse
4. Konfigurowanie komputerów klienckich
5. Utwórz użytkowników zawartej bazy danych w bazie danych zamapowanej na tożsamości usługi Azure AD
6. Nawiązywanie połączenia z pulą SQL przy użyciu tożsamości usługi Azure AD

Obecnie Azure Active Directory użytkownicy nie są wyświetlani w Eksplorator obiektów SSDT. Aby obejść ten sposób, Wyświetl użytkowników w [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="find-the-details"></a>Znajdź szczegóły

* Kroki konfigurowania i używania uwierzytelniania Azure Active Directory są niemal identyczne dla Azure SQL Database i Synapse SQL na platformie Azure Synapse. Postępuj zgodnie ze szczegółowymi instrukcjami w temacie [nawiązywanie połączenia z usługą SQL Database lub pulą SQL przy użyciu uwierzytelniania Azure Active Directory](../../azure-sql/database/authentication-aad-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
* Utwórz niestandardowe role bazy danych i Dodaj użytkowników do ról. Następnie przyznaj uprawnienia szczegółowe do ról. Aby uzyskać więcej informacji, zobacz [wprowadzenie z uprawnieniami aparatu bazy danych](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć wykonywanie zapytań za pomocą programu Visual Studio i innych aplikacji, zobacz [zapytanie w programie Visual Studio](sql-data-warehouse-query-visual-studio.md).
