---
title: SQL na żądanie (wersja zapoznawcza)
description: Dowiedz się więcej na temat Synapse SQL na żądanie w usłudze Azure Synapse Analytics.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: dc47bf73f39d73861c166674a692932d51064e6d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91288532"
---
# <a name="sql-on-demand-preview-in-azure-synapse-analytics"></a>SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics 

Każdy obszar roboczy usługi Azure Synapse Analytics (wersja zapoznawcza) zawiera punkty końcowe SQL na żądanie (wersja zapoznawcza), których można użyć do wykonywania zapytań dotyczących danych w usłudze Lake.

SQL na żądanie to usługa zapytania na dane w usłudze Data Lake. Umożliwia ona dostęp do danych za pomocą następujących funkcji:
 
- Znana składnia T-SQL służąca do wykonywania zapytań dotyczących danych bez konieczności kopiowania lub ładowania danych do wyspecjalizowanego magazynu. 
- Zintegrowana łączność za pośrednictwem interfejsu T-SQL, który oferuje szeroką gamę narzędzi do analizy biznesowej i usług zapytań ad hoc, w tym najpopularniejszych sterowników. 

SQL na żądanie to system rozproszonego przetwarzania danych, oparty na danych o dużej skali i funkcjach obliczeniowych. SQL na żądanie umożliwia analizowanie danych Big Data w ciągu kilku sekund, w zależności od obciążenia. Dzięki wbudowanej odporności na błędy wykonywania zapytania system zapewnia wysoką niezawodność i sukces, nawet w przypadku długotrwałych zapytań obejmujących duże zbiory danych.

SQL na żądanie jest bezserwerowy, dlatego nie istnieje infrastruktura do konfigurowania lub klastrów do obsługi. Domyślny punkt końcowy dla tej usługi jest udostępniany w każdym obszarze roboczym usługi Azure Synapse, więc możesz zacząć wysyłać zapytania o dane zaraz po utworzeniu obszaru roboczego. 

Za zarezerwowane zasoby nie jest naliczana opłata za dane przetworzone przez uruchomione zapytania, dlatego ten model jest rzeczywistym modelem płatnym za użycie.  

W przypadku korzystania z Apache Spark platformy Azure Synapse w potoku danych w celu przygotowania, oczyszczenia lub wzbogacania danych można [wykonywać zapytania dotyczące zewnętrznych tabel platformy Spark](develop-storage-files-spark-tables.md) utworzonych w procesie bezpośrednio z poziomu bazy danych SQL na żądanie. Użyj [linku prywatnego](../security/how-to-connect-to-workspace-with-private-links.md) , aby przenieść punkt końcowy na żądanie SQL do [zarządzanej sieci wirtualnej obszaru roboczego](../security/synapse-workspace-managed-vnet.md).  

## <a name="sql-on-demand-benefits"></a>Korzyści związane z programem SQL na żądanie

Jeśli zachodzi potrzeba eksplorowania danych w usłudze Data Lake, uzyskania szczegółowych informacji z niej lub optymalizacji istniejącego potoku transformacji danych, można skorzystać z funkcji SQL na żądanie. Jest to odpowiednie dla następujących scenariuszy:

- Podstawowe możliwości odnajdywania i eksploracji — szybko Wykorzystaj dane w różnych formatach (Parquet, CSV, JSON) w usłudze Data Lake, aby móc planować sposób wyodrębniania z niego szczegółowych informacji.
- Logiczny magazyn danych — zapewniają relacyjne streszczenie na podstawie nieprzetworzonych lub różnych danych bez konieczności ponownego lokalizowania i przekształcania danych, co pozwala na zawsze aktualny widok danych.
- Przekształcanie danych — prosta, skalowalna i wydajna metoda przekształcania danych w usłudze Lake przy użyciu języka T-SQL, dzięki czemu może być podawana do analizy biznesowej i innych narzędzi lub załadowana do relacyjnego magazynu danych (bazy danych SQL Synapse, Azure SQL Database itd.).

Różne role profesjonalne mogą korzystać z usługi SQL na żądanie:

- Inżynierowie danych mogą eksplorować usługi Lake, przekształcać i przygotowywać dane przy użyciu tych usług oraz uprościć potoki przekształcania danych. Aby uzyskać więcej informacji, zapoznaj się z tym [samouczkiem](tutorial-data-analyst.md).
- Naukowcy danych mogą szybko przyczynić się do zawartości i struktury danych w Lake, dzięki funkcjom, takim jak OPENROWSET i automatycznym wnioskom schematu.
- Analitycy danych mogą [eksplorować dane i bazy danych platformy Spark](develop-storage-files-spark-tables.md) utworzone przez analityków danych lub inżynierów danych przy użyciu znanego języka T-SQL lub ich ulubionych narzędzi, które mogą łączyć się z serwerem SQL na żądanie.
- Specjaliści analizy biznesowej mogą szybko [tworzyć Power BI raporty na podstawie danych w tabelach Lake](tutorial-connect-power-bi-desktop.md) i Spark.

## <a name="how-to-start-using-sql-on-demand"></a>Jak zacząć korzystać z programu SQL na żądanie

Punkt końcowy SQL na żądanie jest udostępniany w każdym obszarze roboczym usługi Azure Synapse. Możesz utworzyć obszar roboczy i szybko zacząć wysyłać zapytania o dane za pomocą narzędzi, które znasz.

## <a name="client-tools"></a>Narzędzia klienta

SQL na żądanie umożliwia korzystanie z istniejących narzędzi SQL ad hoc do wykonywania zapytań i analiz biznesowej w celu wybrania usługi Data Lake. Ponieważ zapewnia znaną składnię T-SQL, dowolne narzędzie mogące nawiązywać połączenia z usługą SQL dla połączeń TDS może łączyć się z [programem SQL Server na żądanie i wysyłać do nich zapytania Synapse](connect-overview.md) . Możesz łączyć się z Azure Data Studio i uruchamiać zapytania ad hoc lub łączyć się z Power BI, aby uzyskać wgląd w kilka minut.

## <a name="t-sql-support"></a>Obsługa języka T-SQL

SQL na żądanie oferuje obszar powierzchni zapytania T-SQL, który jest nieco ulepszony/rozszerzony w niektórych aspektach, aby pomieścić środowiska związane z wykonywaniem zapytań dotyczących danych z częściową strukturą i bez struktury. Ponadto niektóre aspekty języka T-SQL nie są obsługiwane ze względu na projekt SQL na żądanie, na przykład funkcja DML nie jest obecnie obsługiwana.

- Obciążenie można organizować przy użyciu znanych koncepcji:
- Bazy danych — punkt końcowy na żądanie SQL może mieć wiele baz danych.
- Schematy — w bazie danych może istnieć jedna lub wiele grup własności obiektów o nazwie schematy.
- Widoki
- Zasoby zewnętrzne — źródła danych, formaty plików i tabele

Zabezpieczenia można wymusić przy użyciu:

- Nazwy logowania i użytkownicy
- Poświadczenia umożliwiające kontrolę dostępu do kont magazynu
- Przyznaj, Odmów i Odwołaj uprawnienia na poziomie obiektu
- Integracja z usługą Azure Active Directory

Obsługiwane T-SQL:

- Jest obsługiwany pełny [wybór](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) obszaru powierzchni, w tym większość funkcji SQL
- CETAS — UTWÓRZ TABELĘ ZEWNĘTRZNĄ JAKO WYBRANĄ
- Instrukcje języka DDL powiązane tylko z widokami i zabezpieczeniami

SQL na żądanie nie ma magazynu lokalnego. w bazach danych są przechowywane tylko obiekty metadanych. W związku z tym język T-SQL dotyczący następujących pojęć nie jest obsługiwany:

- Tabele
- Wyzwalacze
- Zmaterializowane widoki
- Instrukcje języka DDL inne niż powiązane z widokami i zabezpieczeniami
- Instrukcje języka DML

### <a name="extensions"></a>Rozszerzenia

Aby zapewnić bezproblemowe środowisko tworzenia zapytań dotyczących danych znajdujących się w plikach w usłudze Data Lake, funkcja SQL na żądanie rozszerza istniejącą funkcję [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) , dodając następujące możliwości:

[Kwerenda wielu plików lub folderów](query-data-storage.md#query-multiple-files-or-folders)

[Format pliku PARQUET](query-data-storage.md#query-parquet-files)

[Dodatkowe opcje pracy z rozdzielonym tekstem (terminator pola, terminator wiersza, znak ucieczki)](query-data-storage.md#query-csv-files)

[Odczytaj wybrany podzestaw kolumn](query-data-storage.md#read-a-chosen-subset-of-columns)

[Wnioskowanie schematu](query-data-storage.md#schema-inference)

[Funkcja filename](query-data-storage.md#filename-function)

[FilePath — funkcja](query-data-storage.md#filepath-function)

[Pracuj z typami złożonymi i zagnieżdżonymi lub powtarzanymi strukturami danych](query-data-storage.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Zabezpieczenia

Program SQL on-Demand oferuje mechanizmy zabezpieczania dostępu do danych.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integracja usługi Azure Active Directory z uwierzytelnianiem wieloskładnikowym

Usługa SQL na żądanie umożliwia centralne zarządzanie tożsamościami użytkowników bazy danych i innych usług firmy Microsoft z [integracją Azure Active Directory](../../azure-sql/database/authentication-aad-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Ta funkcja upraszcza zarządzanie uprawnieniami i zwiększa bezpieczeństwo. Azure Active Directory (Azure AD) obsługuje [uwierzytelnianie wieloskładnikowe](../../azure-sql/database/authentication-mfa-ssms-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (MFA) w celu zwiększenia bezpieczeństwa danych i aplikacji podczas obsługi logowania jednokrotnego.

#### <a name="authentication"></a>Authentication

Uwierzytelnianie na żądanie SQL dotyczy sposobu, w jaki użytkownicy udowadniają swoją tożsamość podczas nawiązywania połączenia z punktem końcowym. Obsługiwane są dwa typy uwierzytelniania:

- **Uwierzytelnianie SQL**

  Ta metoda uwierzytelniania używa nazwy użytkownika i hasła.

- **Uwierzytelnianie Azure Active Directory**:

  Ta metoda uwierzytelniania używa tożsamości zarządzanych przez Azure Active Directory. W przypadku użytkowników usługi Azure AD można włączyć uwierzytelnianie wieloskładnikowe. Używaj uwierzytelniania usługi Active Directory (zabezpieczeń zintegrowanych), [gdy tylko jest to możliwe](/sql/relational-databases/security/choose-an-authentication-mode?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

#### <a name="authorization"></a>Autoryzacja

Autoryzacja odnosi się do tego, co użytkownik może zrobić w bazie danych SQL na żądanie, i jest kontrolowane przez członkostwo roli bazy danych i uprawnienia na poziomie obiektów.

Jeśli używane jest uwierzytelnianie SQL, użytkownik SQL istnieje tylko na żądanie SQL, a uprawnienia do zakresu obiektów w SQL na żądanie. Dostęp do zabezpieczanych obiektów w innych usługach (takich jak Azure Storage) nie może zostać udzielony bezpośrednio użytkownikowi SQL, ponieważ istnieje on tylko w zakresie SQL na żądanie. Użytkownik SQL musi użyć jednego z [obsługiwanych typów autoryzacji](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) , aby uzyskać dostęp do plików.

Jeśli jest używane uwierzytelnianie usługi Azure AD, użytkownik może zalogować się do usługi SQL na żądanie i innych usług, takich jak usługa Azure Storage, i może udzielić uprawnień użytkownikowi usługi Azure AD.

### <a name="access-to-storage-accounts"></a>Dostęp do kont magazynu

Użytkownik zalogowany do usługi SQL na żądanie musi mieć autoryzację, aby uzyskać dostęp do plików w usłudze Azure Storage i wysyłać do nich zapytania. SQL na żądanie obsługuje następujące typy autoryzacji:

- **Sygnatura dostępu współdzielonego (SAS)** zapewnia delegowany dostęp do zasobów na koncie magazynu. Za pomocą sygnatury dostępu współdzielonego można przyznać klientom dostęp do zasobów na koncie magazynu bez udostępniania kluczy konta. Sygnatura dostępu współdzielonego zapewnia szczegółową kontrolę nad dostępem udzielonym klientom, którzy mają interwał ważności SAS:, przyznane uprawnienia, akceptowalny zakres adresów IP, akceptowalny protokół (https/http).

- **Tożsamość użytkownika** (nazywana również "przekazywaniem") jest typem autoryzacji, w którym tożsamość użytkownika usługi Azure AD zalogowanym na żądanie SQL jest używana do autoryzacji dostępu do danych. Przed uzyskaniem dostępu do danych administrator usługi Azure Storage musi udzielić uprawnień użytkownikowi usługi Azure AD na potrzeby uzyskiwania dostępu do danych. Ten typ autoryzacji używa użytkownika usługi Azure AD, który zalogował się na żądanie SQL, dlatego nie jest obsługiwany w przypadku typów użytkowników SQL.

## <a name="next-steps"></a>Następne kroki
Dodatkowe informacje na temat połączenia punktu końcowego i plików zapytań można znaleźć w następujących artykułach: 
- [Nawiązywanie połączenia z punktem końcowym](connect-overview.md)
- [Tworzenie zapytań dotyczących plików](develop-storage-files-overview.md)
