---
title: SQL na żądanie (wersja zapoznawcza)
description: Dowiedz się więcej o synapse SQL na żądanie w usłudze Azure Synapse Analytics.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 8d4de424d5d4d6da1ee80e04b35e63ae29df57c8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424909"
---
# <a name="sql-on-demand-preview-in-azure-synapse-analytics"></a>SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics 

Każdy obszar roboczy usługi Azure Synapse Analytics (wersja zapoznawcza) jest dostarczany z punktami końcowymi SQL na żądanie (wersja zapoznawcza), których można użyć do wykonywania zapytań o dane w aplikacji.

SQL na żądanie jest usługą kwerend za pośrednictwem danych w usłudze data lake. Umożliwia dostęp do danych za pośrednictwem następujących funkcji:
 
- Znana składnia T-SQL do wykonywania zapytań o dane w miejscu bez konieczności kopiowania lub ładowania danych do specjalistycznego magazynu. 
- Zintegrowana łączność za pośrednictwem interfejsu T-SQL, który oferuje szeroką gamę narzędzi do analizy biznesowej i zapytań ad hoc, w tym najpopularniejszych sterowników. 

SQL na żądanie to rozproszony system przetwarzania danych, stworzony z myślą o dużej skali danych i obliczeń. SQL na żądanie umożliwia analizowanie dużych zbiorów danych w ciągu kilku sekund do minut, w zależności od obciążenia. Dzięki wbudowanej odporności na uszkodzenia wykonywania zapytań system zapewnia wysoką niezawodność i współczynnik sukcesu nawet w przypadku długotrwałych zapytań obejmujących duże zestawy danych.

SQL na żądanie jest bezserwerowy, dlatego nie ma infrastruktury do instalacji lub klastrów do utrzymania. Domyślny punkt końcowy dla tej usługi jest dostarczany w każdym obszarze roboczym usługi Azure Synapse, dzięki czemu można rozpocząć wykonywanie zapytań o dane zaraz po utworzeniu obszaru roboczego. Nie ma żadnych opłat za zarezerwowane zasoby, opłaty są naliczane tylko za dane skanowane przez uruchomione kwerendy, dlatego ten model jest prawdziwym modelem płatności za użycie.  

Jeśli używasz platformy Spark w potoku danych, do przygotowania danych, oczyszczania lub wzbogacania, można [zbadać wszystkie tabele platformy Spark](develop-storage-files-spark-tables.md) utworzone w procesie, bezpośrednio z SQL na żądanie. Użyj [łącza prywatnego,](../security/how-to-connect-to-workspace-with-private-links.md) aby przenieść punkt końcowy SQL na żądanie do [zarządzanej sieci wirtualnej obszaru roboczego](../security/synapse-workspace-managed-vnet.md).  

## <a name="who-is-sql-on-demand-for"></a>Kto jest SQL na żądanie

Jeśli chcesz eksplorować dane w usłudze data lake, uzyskać wgląd w niego lub zoptymalizować istniejący potok transformacji danych, możesz korzystać z korzystania z sql na żądanie. Nadaje się do następujących scenariuszy:

- Podstawowe wykrywanie i eksploracja — szybko porozumieć dane w różnych formatach (Parkiet, CSV, JSON) w jeziorze danych, dzięki czemu można zaplanować, jak wyodrębnić z niego szczegółowe informacje.
- Magazyn danych logicznych — zapewnij abstrakcję relacyjną na podstawie surowych lub rozbieżnych danych bez przenoszenia i przekształcania danych, umożliwiając zawsze aktualny widok danych.
- Transformacja danych — prosty, skalowalny i wydajny sposób przekształcania danych w jeziorze przy użyciu języka T-SQL, dzięki czemu można je zasilać bi i inne narzędzia lub ładować do relacyjnego magazynu danych (bazy danych Synapse SQL, usługa Azure SQL Database itp.).

Różne role zawodowe mogą korzystać z SQL na żądanie:

- Inżynierowie danych mogą eksplorować jezioro, przekształcać i przygotowywać dane za pomocą tej usługi i upraszczać potoki transformacji danych. Aby uzyskać więcej informacji, sprawdź ten [samouczek](tutorial-data-analyst.md).
- Data Scientists może szybko przyczyny o zawartości i strukturze danych w jeziorze, dzięki funkcjom, takich jak OPENROWSET i automatyczne wnioskowanie schematu.
- Analitycy danych mogą [eksplorować tabele danych i platformy Spark](develop-storage-files-spark-tables.md) utworzone przez analityków danych lub inżynierów danych przy użyciu znanego języka T-SQL lub ich ulubionych narzędzi, które mogą łączyć się z sql na żądanie.
- Specjaliści analizy biznesowej mogą szybko [tworzyć raporty usługi Power BI na podstawie danych w](tutorial-connect-power-bi-desktop.md) tabelach Lake i Spark.

## <a name="what-do-i-need-to-do-to-start-using-it"></a>Co muszę zrobić, aby zacząć z niego korzystać?

Punkt końcowy SQL na żądanie jest dostarczany w każdym obszarze roboczym usługi Azure Synapse. Można utworzyć obszar roboczy i natychmiast rozpocząć wykonywanie zapytań o dane za pomocą narzędzi, które znasz.

## <a name="client-tools"></a>Narzędzia klienckie

SQL na żądanie umożliwia istniejące sql ad-hoc zapytań i narzędzi analizy biznesowej, aby wykorzystać do jeziora danych. Ponieważ zapewnia znane składni T-SQL, każde narzędzie zdolne do ustanowienia tds połączenia oferty SQL można [połączyć i kwerendy Synapse SQL](connect-overview.md) na żądanie. Możesz połączyć się z usługą Azure Data Studio i uruchamiać zapytania ad hoc lub połączyć się z usługą Power BI, aby uzyskać szczegółowe informacje w ciągu kilku minut.

## <a name="is-full-t-sql-supported"></a>Czy pełna obsługa T-SQL jest obsługiwana?

SQL na żądanie oferuje T-SQL kwerendy powierzchni, która jest nieco rozszerzone / rozszerzone w niektórych aspektach, aby pomieścić dla środowisk wokół wykonywania zapytań danych półstrukturalnych i niestrukturalnych. Ponadto niektóre aspekty języka T-SQL nie są obsługiwane ze względu na projekt SQL na żądanie, na przykład funkcja DML nie jest obecnie obsługiwana.

- Obciążenie można zorganizować przy użyciu znanych pojęć:
- Bazy danych — punkt końcowy SQL na żądanie może mieć wiele baz danych.
- Schematy — w bazie danych może istnieć jedna lub wiele grup własności obiektu o nazwie schematy.
- Widoki
- Zasoby zewnętrzne — źródła danych, formaty plików i tabele

Zabezpieczenia można wymuszać za pomocą:

- Nazwy logowania i użytkownicy
- Poświadczenia do kontrolowania dostępu do kont magazynu
- Udzielanie, odmawianie i odwoływanie uprawnień na poziomie obiektu
- Integracja z usługą Azure Active Directory

Obsługiwany T-SQL:

- Pełna [powierzchnia SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) jest obsługiwana, w tym większość funkcji SQL
- CETAS - TWORZENIE TABELI ZEWNĘTRZNEJ JAKO WYBIERZ
- Instrukcje DDL związane tylko z widokami i zabezpieczeniami

SQL na żądanie nie ma magazynu lokalnego, tylko obiekty metadanych są przechowywane w bazach danych. W związku z tym T-SQL związane z następujących pojęć nie jest obsługiwany:

- Tabele
- Wyzwalacze
- Zmaterializowane widoki
- Oświadczenia DDL inne niż te związane z widokami i bezpieczeństwem
- Instrukcje języka DML

### <a name="extensions"></a>Rozszerzenia

Aby umożliwić płynne działanie w miejscu wykonywania zapytań o dane znajdujących się w plikach w u źródła danych, SQL na żądanie rozszerza istniejącą funkcję [OPENROWSET,](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) dodając następujące możliwości:

[Kwerenda z wieloma plikami lub folderami](develop-storage-files-overview.md#query-multiple-files-or-folders)

[Format pliku PARKIET](develop-storage-files-overview.md#parquet-file-format)

[Dodatkowe opcje pracy z tekstem rozdzielanym (terminator pól, terminator wierszy, znak ucieczki)](develop-storage-files-overview.md#additional-options-for-working-with-delimited-text)

[Odczytywanie wybranego podzbioru kolumn](develop-storage-files-overview.md#read-a-chosen-subset-of-columns)

[Wnioskowanie ze schematu](develop-storage-files-overview.md#schema-inference)

[nazwa pliku, funkcja](develop-storage-files-overview.md#filename-function)

[filepath, funkcja](develop-storage-files-overview.md#filepath-function)

[Praca ze złożonymi typami i zagnieżdżonymi lub powtarzającymi się strukturami danych](develop-storage-files-overview.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Zabezpieczenia

SQL na żądanie oferuje mechanizmy, aby zabezpieczyć dostęp do danych.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integracja usługi Azure Active Directory z uwierzytelnianiem wieloskładnikowym

Sql na żądanie umożliwia centralne zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft za pomocą [integracji usługi Azure Active Directory.](../../sql-database/sql-database-Azure AD-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) Ta funkcja upraszcza zarządzanie uprawnieniami i zwiększa bezpieczeństwo. Usługa Azure Active Directory (Azure AD) obsługuje [uwierzytelnianie wieloskładnikowe](../../sql-database/sql-database-ssms-mfa-authentication-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (MFA) w celu zwiększenia bezpieczeństwa danych i aplikacji, jednocześnie obsługując proces logowania jednokrotnego.

#### <a name="authentication"></a>Authentication

Uwierzytelnianie SQL na żądanie odnosi się do sposobu, w jaki użytkownicy potwierdzają swoją tożsamość podczas łączenia się z punktem końcowym. Obsługiwane są dwa typy uwierzytelniania:

- **Uwierzytelnianie SQL**

  Ta metoda uwierzytelniania używa nazwy użytkownika i hasła.

- **Uwierzytelnianie usługi Azure Active Directory:**

  Ta metoda uwierzytelniania używa tożsamości zarządzanych przez usługę Azure Active Directory. W przypadku użytkowników usługi Azure AD można włączyć uwierzytelnianie wieloskładnikowe. Używaj uwierzytelniania usługi Active Directory (zabezpieczeń zintegrowanych), [gdy tylko jest to możliwe](/sql/relational-databases/security/choose-an-authentication-mode?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

#### <a name="authorization"></a>Autoryzacja

Autoryzacja odnosi się do tego, co użytkownik może zrobić w bazie danych SQL na żądanie i jest kontrolowana przez członkostwo roli bazy danych konta użytkownika i uprawnienia na poziomie obiektu.

Jeśli jest używane uwierzytelnianie SQL, użytkownik SQL istnieje tylko w SQL na żądanie i uprawnienia są ograniczone do obiektów w SQL na żądanie. Dostęp do zabezpieczalnych obiektów w innych usługach (takich jak usługa Azure Storage) nie może być przyznany użytkownikowi SQL bezpośrednio, ponieważ istnieje tylko w zakresie SQL na żądanie. Użytkownik SQL musi użyć jednego z [obsługiwanych typów autoryzacji,](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) aby uzyskać dostęp do plików.

Jeśli jest używane uwierzytelnianie usługi Azure AD, użytkownik może zalogować się do sql na żądanie i innych usług, takich jak usługa Azure Storage, i może udzielić uprawnień użytkownikowi usługi Azure AD.

### <a name="access-to-storage-accounts"></a>Dostęp do kont magazynu

Użytkownik, który jest zalogowany do usługi SQL na żądanie musi być autoryzowany do uzyskiwania dostępu i wykonywania zapytań o pliki w usłudze Azure Storage. SQL na żądanie obsługuje następujące typy autoryzacji:

- **Sygnatura dostępu współdzielonego (SAS)** zapewnia delegowany dostęp do zasobów na koncie magazynu. Za pomocą sygnatury dostępu Współdzielonego można udzielić klientom dostępu do zasobów na koncie magazynu bez udostępniania kluczy konta. Sygnatura dostępu współdzielonego zapewnia szczegółową nad typem dostępu udzielonego klientom, którzy mają sygnatury dostępu Współdzielonego: interwał ważności, przyznane uprawnienia, dopuszczalny zakres adresów IP, dopuszczalny protokół (https/http).

- **Tożsamość użytkownika** (znana również jako "przekazywanie") to typ autoryzacji, w którym tożsamość użytkownika usługi Azure AD, który zalogował się do sql na żądanie, jest używana do autoryzowania dostępu do danych. Przed uzyskaniem dostępu do danych administrator usługi Azure Storage musi udzielić użytkownikowi usługi Azure AD uprawnień dostępu do danych. Ten typ autoryzacji używa użytkownika usługi Azure AD, który zalogował się do języka SQL na żądanie, dlatego nie jest obsługiwany dla typów użytkowników SQL.

## <a name="next-steps"></a>Następne kroki
Dodatkowe informacje na temat połączenia punktu końcowego i plików zapytań można znaleźć w następujących artykułach: 
- [Łączenie się z punktem końcowym](connect-overview.md)
- [Kwerenda plików](develop-storage-files-overview.md)
