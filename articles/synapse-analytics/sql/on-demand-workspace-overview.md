---
title: Bezserwerowa pula SQL
description: Dowiedz się więcej na temat bez serwera puli SQL w Azure Synapse Analytics.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: f0abef835e406b442239cecd81fed5751f1c9a92
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378163"
---
# <a name="serverless-sql-pool-in-azure-synapse-analytics"></a>Bez serwera pula SQL w Azure Synapse Analytics 

Każdy Azure Synapse Analytics jest dostarczany z bez serwera punktami końcowymi puli SQL, których można użyć do wykonywania zapytań o dane w użytce lake.

Bez serwerowa pula SQL to usługa zapytań o dane w data lake. Umożliwia ona dostęp do danych za pomocą następujących funkcji:
 
- Znajoma składnia języka T-SQL do wykonywania zapytań o dane bez konieczności kopiowania lub ładowania danych do wyspecjalizowanego magazynu. 
- Zintegrowana łączność za pośrednictwem interfejsu T-SQL, który oferuje szeroką gamę narzędzi do analizy biznesowej i zapytań ad hoc, w tym najpopularniejsze sterowniki. 

Bez serwera pula SQL to rozproszony system przetwarzania danych, który jest zbudowany z myślą o dużych funkcjach obliczeniowych i danych. Bez serwera pula SQL umożliwia analizowanie danych big data w ciągu kilku sekund do minut, w zależności od obciążenia. Dzięki wbudowanej odporności na uszkodzenia wykonywania zapytań system zapewnia wysoką niezawodność i powodzenie nawet w przypadku długotrwałych zapytań obejmujących duże zestawy danych.

Bez serwera pula SQL jest bez serwera, dlatego nie ma infrastruktury do instalacji ani klastrów do konserwacji. Domyślny punkt końcowy dla tej usługi jest dostarczany w każdym Azure Synapse roboczym, dzięki czemu możesz rozpocząć wykonywanie zapytań o dane natychmiast po utworzeniu obszaru roboczego. 

Za zarezerwowane zasoby nie są naliczane opłaty. Opłaty są naliczane tylko za dane przetwarzane przez uruchamiane zapytania, dlatego ten model jest prawdziwym modelem płatności za użycie.  

Jeśli używasz usługi Apache Spark for Azure Synapse w potoku danych w celu przygotowywania, czyszczenia lub wzbogacania danych, możesz odpytować zewnętrzne tabele [platformy Spark](develop-storage-files-spark-tables.md) utworzone w procesie bezpośrednio z bez serwera puli SQL. Użyj [Private Link,](../security/how-to-connect-to-workspace-with-private-links.md) aby wprowadzić bez serwera punkt końcowy puli SQL do sieci wirtualnej obszaru [roboczego zarządzanego.](../security/synapse-workspace-managed-vnet.md)  

## <a name="serverless-sql-pool-benefits"></a>Korzyści z puli SQL bez serwera

Jeśli musisz eksplorować dane w data lake, uzyskać na ich temat szczegółowe informacje lub zoptymalizować istniejący potok przekształcania danych, możesz skorzystać z bez serwera puli SQL. Jest to odpowiednie dla następujących scenariuszy:

- Podstawowe odnajdywanie i eksploracja — szybkie uzasadnienie danych w różnych formatach (Parquet, CSV, JSON) w twoim data lake, dzięki czemu możesz zaplanować sposób wyodrębniania z nich szczegółowych informacji.
- Magazyn danych logicznych — zapewnia abstrakcję relacyjną na podstawie danych pierwotnych lub różnych bez przenoszenia i przekształcania danych, umożliwiając zawsze aktualny widok danych.
- Przekształcanie danych — prosty, skalowalny i performantowy sposób przekształcania danych w magazynie typu lake przy użyciu języka T-SQL, dzięki czemu mogą być podawane do analizy danych i innych narzędzi lub ładowane do relacyjnych magazynów danych (bazy danych Synapse SQL, Azure SQL Database itp.).

Z bez serwera puli SQL mogą korzystać różne role zawodowe:

- Inżynierowie danych mogą eksplorować usługę Lake, przekształcać i przygotowywać dane przy użyciu tej usługi oraz upraszczać potoki przekształcania danych. Aby uzyskać więcej informacji, zapoznaj się z tym [samouczkiem.](tutorial-data-analyst.md)
- Badacze danych mogą szybko wnioskować o zawartości i strukturze danych w układzie lake dzięki takim cechom jak OPENROWSET i automatyczne wnioskowanie schematu.
- Analitycy danych mogą eksplorować dane i tabele zewnętrzne [platformy Spark](develop-storage-files-spark-tables.md) utworzone przez analityków danych lub inżynierów danych przy użyciu znanego języka T-SQL lub ich ulubionych narzędzi, które mogą łączyć się z bez serwera pulą SQL.
- Specjaliści ds. Power BI bi mogą szybko [tworzyć raporty na podstawie danych w tabelach lake](tutorial-connect-power-bi-desktop.md) i Spark.

## <a name="how-to-start-using-serverless-sql-pool"></a>Jak rozpocząć korzystanie z bez serwera puli SQL

Bez serwera punkt końcowy puli SQL jest dostarczany w każdym Azure Synapse roboczym. Obszar roboczy można utworzyć i natychmiast rozpocząć wykonywanie zapytań o dane przy użyciu znanych narzędzi.

## <a name="client-tools"></a>Narzędzia klienckie

Bez serwera pula SQL umożliwia istniejącym narzędziom sql do wykonywania zapytań ad hoc i analizy biznesowej korzystanie z data lake. Ponieważ udostępnia znajomą składnię języka T-SQL, każde narzędzie, które może nawiązać połączenie TDS z ofertami SQL, może nawiązywać połączenia z ofertami SQL i Synapse SQL [.](connect-overview.md) Możesz połączyć się za pomocą Azure Data Studio i uruchamiać zapytania ad hoc lub połączyć się z Power BI, aby uzyskać szczegółowe informacje w ciągu kilku minut.

## <a name="t-sql-support"></a>Obsługa języka T-SQL

Bez serwerowa pula SQL oferuje powierzchnię zapytań T-SQL, która jest nieco rozszerzona/rozszerzona w niektórych aspektach w celu uwzględnienia doświadczeń związanych z wykonywaniem zapytań dotyczących częściowo ustrukturyzowanych i nieustrukturyzowanych danych. Ponadto niektóre aspekty języka T-SQL nie są obsługiwane ze względu na projekt bez serwera puli SQL, na przykład funkcja DML nie jest obecnie obsługiwana.

- Obciążenie można uporządkować przy użyciu znanych pojęć:
- Bazy danych — bez serwera punkt końcowy puli SQL może mieć wiele baz danych.
- Schematy — w bazie danych może być jedna lub wiele grup własności obiektów nazywanych schematami.
- Widoki
- Zasoby zewnętrzne — źródła danych, formaty plików i tabele

Zabezpieczenia można wymusić przy użyciu:

- Nazwy logowania i użytkownicy
- Poświadczenia do kontrolowania dostępu do kont magazynu
- Udzielanie, odrzucanie i odwoływanie uprawnień na poziomie obiektu
- Integracja z usługą Azure Active Directory

Obsługiwany język T-SQL:

- Obsługiwany [jest pełny](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) obszar powierzchni SELECT, w tym większość funkcji SQL
- CETAS — CREATE EXTERNAL TABLE AS SELECT
- Instrukcje języka DDL dotyczące tylko widoków i zabezpieczeń

Bez serwera pula SQL nie ma magazynu lokalnego, tylko obiekty metadanych są przechowywane w bazach danych. W związku z tym język T-SQL związany z następującymi pojęciami nie jest obsługiwany:

- tabelami
- Wyzwalacze
- Zmaterializowane widoki
- Instrukcje języka DDL inne niż związane z widokami i zabezpieczeniami
- Instrukcje języka DML

### <a name="extensions"></a>Rozszerzenia

Aby umożliwić bezproblemowe wykonywanie zapytań dotyczących danych znajdujących się w plikach w programie data lake, bez serwera pula SQL rozszerza istniejącą funkcję [OPENROWSET,](/sql/t-sql/functions/openrowset-transact-sql?view=azure-sqldw-latest&preserve-view=true) dodając następujące możliwości:

[Wykonywanie zapytań o wiele plików lub folderów](query-data-storage.md#query-multiple-files-or-folders)

[Format pliku PARQUET](query-data-storage.md#query-parquet-files)

[Dodatkowe opcje pracy z tekstem rozdzielanych (terminator pola, terminator wiersza, znak ucieczki)](query-data-storage.md#query-csv-files)

[Odczytywanie wybranego podzestawu kolumn](query-data-storage.md#read-a-chosen-subset-of-columns)

[Wnioskowanie schematu](query-data-storage.md#schema-inference)

[filename, funkcja](query-data-storage.md#filename-function)

[filepath, funkcja](query-data-storage.md#filepath-function)

[Praca z typami złożonymi oraz zagnieżdżone lub powtarzane struktury danych](query-data-storage.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Zabezpieczenia

Bez serwera pula SQL oferuje mechanizmy zabezpieczania dostępu do danych.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integracja usługi Azure Active Directory z uwierzytelnianiem wieloskładnikowym

Bez serwera pula SQL umożliwia centralne zarządzanie tożsamościami użytkowników bazy danych i innych serwerów usługi firmy Microsoft z Azure Active Directory [integracji.](../../azure-sql/database/authentication-aad-configure.md) Ta funkcja upraszcza zarządzanie uprawnieniami i zwiększa bezpieczeństwo. Azure Active Directory (Azure AD) [](../../azure-sql/database/authentication-mfa-ssms-configure.md) obsługuje uwierzytelnianie wieloskładnikowe (MFA), aby zwiększyć bezpieczeństwo danych i aplikacji przy jednoczesnym wspieraniu procesu logowania pojedynczego.

#### <a name="authentication"></a>Authentication

Uwierzytelnianie bez serwera puli SQL odnosi się do sposobu, w jaki użytkownicy potwierdzają swoją tożsamość podczas nawiązywania połączenia z punktem końcowym. Obsługiwane są dwa typy uwierzytelniania:

- **Uwierzytelnianie SQL**

  Ta metoda uwierzytelniania używa nazwy użytkownika i hasła.

- **Azure Active Directory uwierzytelniania:**

  Ta metoda uwierzytelniania używa tożsamości zarządzanych przez Azure Active Directory. W przypadku użytkowników usługi Azure AD można włączyć uwierzytelnianie wieloskładnikowe. Używaj uwierzytelniania usługi Active Directory (zabezpieczeń zintegrowanych), [gdy tylko jest to możliwe](/sql/relational-databases/security/choose-an-authentication-mode?view=azure-sqldw-latest&preserve-view=true).

#### <a name="authorization"></a>Autoryzacja

Autoryzacja odnosi się do tego, co użytkownik może zrobić w bezużytecznej bazie danych puli SQL i jest kontrolowana przez członkostwo w roli bazy danych konta użytkownika i uprawnienia na poziomie obiektu.

Jeśli jest używane uwierzytelnianie SQL, użytkownik SQL istnieje tylko w bez serverless puli SQL i uprawnienia są ograniczony do obiektów w puli SQL bez serwera. Dostępu do zabezpieczanych obiektów w innych usługach (takich jak Azure Storage) nie można przyznać bezpośrednio użytkownikowi SQL, ponieważ istnieje on tylko w zakresie bez serwera puli SQL. Użytkownik SQL musi użyć jednego z obsługiwanych [typów autoryzacji,](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) aby uzyskać dostęp do plików.

Jeśli jest używane uwierzytelnianie usługi Azure AD, użytkownik może zalogować się do bez serwera w puli SQL i innych usługach, takich jak Azure Storage, i może udzielić uprawnień użytkownikowi usługi Azure AD.

### <a name="access-to-storage-accounts"></a>Dostęp do kont magazynu

Użytkownik zalogowany do bez serwera w usłudze puli SQL musi być autoryzowany do uzyskiwania dostępu do plików i wykonywania zapytań o nie w usłudze Azure Storage. bez serwera pula SQL obsługuje następujące typy autoryzacji:

- **Sygnatura dostępu współdzielonego (SAS)** zapewnia delegowany dostęp do zasobów na koncie magazynu. Dzięki sygnaturze dostępu współdzielonego można udzielić klientom dostępu do zasobów na koncie magazynu bez udostępniania kluczy kont. Sygnatura dostępu współdzielonego zapewnia szczegółową kontrolę nad typem dostępu udzielanych klientom, którzy mają sygnaturę dostępu współdzielonego: interwał ważności, przyznane uprawnienia, dopuszczalny zakres adresów IP, akceptowalny protokół (https/http).

- **Tożsamość użytkownika** (znana także jako "pass-through") to typ autoryzacji, w którym tożsamość użytkownika usługi Azure AD, który zalogował się do bez serwera w puli SQL, jest używana do autoryzowania dostępu do danych. Przed uzyskaniem dostępu do danych administrator usługi Azure Storage musi udzielić użytkownikowi usługi Azure AD uprawnień dostępu do danych. Ten typ autoryzacji używa użytkownika usługi Azure AD, który zalogował się do bez serwera w puli SQL, dlatego nie jest obsługiwany w przypadku typów użytkowników SQL.

## <a name="next-steps"></a>Następne kroki
Dodatkowe informacje na temat połączenia z punktem końcowym i wykonywania zapytań dotyczących plików można znaleźć w następujących artykułach: 
- [Nawiązywanie połączenia z punktem końcowym](connect-overview.md)
- [Wykonywanie zapytań o pliki](develop-storage-files-overview.md)
