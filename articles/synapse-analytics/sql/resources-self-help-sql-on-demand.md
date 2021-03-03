---
title: Bezserwerowa Pula SQL w samoobsługowej pomocy
description: Ta sekcja zawiera informacje, które mogą pomóc w rozwiązywaniu problemów z pulą SQL bez serwera.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: f94134b2a06155b1b1f390175578e501a840038b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669425"
---
# <a name="self-help-for-serverless-sql-pool"></a>Samoobsługowa pomoc dla puli SQL bezserwerowej

Ten artykuł zawiera informacje o sposobie rozwiązywania problemów z większością częstych problemów dotyczących puli SQL bez użycia serwera w usłudze Azure Synapse Analytics.

## <a name="serverless-sql-pool-is-grayed-out-in-synapse-studio"></a>Pula SQL bezserwerowa jest wyszarzona w programie Synapse Studio

Jeśli program Synapse Studio nie może nawiązać połączenia z pulą SQL bezserwerową, Zauważ, że bezserwerowa Pula SQL jest wyszarzona lub zawiera stan "offline". Zazwyczaj ten problem występuje, gdy wystąpi jedno z następujących przypadków:

1) Twoja sieć uniemożliwia komunikację z zapleczem usługi Azure Synapse. W większości przypadków przyczyną jest zablokowany port 1443. Aby uzyskać bezserwerową pulę SQL do pracy, Odblokuj ten port. Inne problemy mogą uniemożliwić działanie puli SQL bezserwerowej. Aby uzyskać [więcej informacji, odwiedź pełny Przewodnik rozwiązywania problemów](../troubleshoot/troubleshoot-synapse-studio.md).
2) Nie masz uprawnień do logowania się do bezserwerowej puli SQL. Aby udzielić Ci dostępu, administrator obszaru roboczego usługi Azure Synapse powinien dodać Cię do roli administratora obszaru roboczego lub administratora SQL. [Aby uzyskać więcej informacji, odwiedź pełny przewodnik dotyczący kontroli dostępu](../security/synapse-workspace-access-control-overview.md).

## <a name="query-fails-because-file-cannot-be-opened"></a>Zapytanie nie powiodło się, ponieważ nie można otworzyć pliku

Jeśli zapytanie nie powiedzie się z powodu błędu "nie można otworzyć pliku, ponieważ nie istnieje lub jest używany przez inny proces" i masz pewność, że oba pliki istnieją i nie są używane przez inny proces, oznacza to, że bezserwerowa Pula SQL nie może uzyskać dostępu do pliku. Ten problem jest zwykle spowodowany tym, że tożsamość Azure Active Directory nie ma uprawnień dostępu do pliku. Domyślnie Pula SQL bezserwerowa próbuje uzyskać dostęp do pliku przy użyciu tożsamości Azure Active Directory. Aby rozwiązać ten problem, musisz mieć odpowiednie prawa dostępu do pliku. Najprostszym sposobem jest udzielenie sobie roli „Współautor danych obiektu blob usługi Storage” w odniesieniu do konta magazynu, którego dotyczy zapytanie. 
- [Aby uzyskać więcej informacji, zobacz pełny przewodnik dotyczący kontroli dostępu do magazynu w usłudze Azure Active Directory](../../storage/common/storage-auth-aad-rbac-portal.md). 
- [Odwiedź stronę kontrola dostępu do konta magazynu dla bezserwerowej puli SQL w usłudze Azure Synapse Analytics](develop-storage-files-storage-access-control.md)

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>Zapytanie nie powiodło się, ponieważ nie można go wykonać ze względu na bieżące ograniczenia zasobów 

Jeśli zapytanie nie powiedzie się i zostanie wyświetlony komunikat o błędzie "nie można wykonać tego zapytania z powodu bieżących ograniczeń zasobów", oznacza to, że bezserwerowa Pula SQL nie może wykonać tej operacji w tym momencie ze względu na ograniczenia zasobów: 

- Upewnij się, że są używane typy danych o rozsądnych rozmiarach. Ponadto Określ schemat dla plików Parquet dla kolumn ciągów, ponieważ będą one domyślnie VARCHAR (8000). 

- Jeśli zapytanie wskazuje pliki CSV, należy rozważyć [utworzenie statystyk](develop-tables-statistics.md#statistics-in-serverless-sql-pool). 

- Odwiedź [najlepsze rozwiązania w zakresie wydajności dla puli SQL bezserwerowej](best-practices-sql-on-demand.md) , aby zoptymalizować zapytanie.  

## <a name="create-statement-is-not-supported-in-master-database"></a>INSTRUKCJA CREATE nie jest obsługiwana w bazie danych Master

Jeśli zapytanie nie powiedzie się, zostanie wyświetlony komunikat o błędzie:

> "Nie można wykonać zapytania. Błąd: w bazie danych Master nie jest obsługiwana funkcja CREATE EXTERNAL TABLE/źródło danych/plik z ZAKRESem bazy danych. 

oznacza to, że baza danych Master w puli SQL bezserwerowej nie obsługuje tworzenia:
  - Tabele zewnętrzne
  - Zewnętrzne źródła danych
  - Poświadczenia w zakresie bazy danych
  - Zewnętrzne formaty plików

Rozwiązanie:

  1. Utwórz bazę danych użytkownika:

```sql
CREATE DATABASE <DATABASE_NAME>
```

  2. Wykonanie instrukcji CREATE w kontekście <DATABASE_NAME>, która nie powiodła się wcześniej dla bazy danych Master. 
  
  Przykład tworzenia zewnętrznego formatu pliku:
    
```sql
USE <DATABASE_NAME>
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] 
WITH ( FORMAT_TYPE = PARQUET)
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej na temat używania puli SQL bezserwerowej:

- [Kwerenda pojedynczego pliku CSV](query-single-csv-file.md)

- [Foldery zapytań i wiele plików CSV](query-folders-multiple-csv-files.md)

- [Kwerenda określonych plików](query-specific-files.md)

- [Wykonywanie zapytań względem plików Parquet](query-parquet-files.md)

- [Wykonywanie zapytań względem typów zagnieżdżonych Parquet](query-parquet-nested-types.md)

- [Wykonywanie zapytań względem plików JSON](query-json-files.md)

- [Tworzenie widoków i korzystanie z nich](create-use-views.md)

- [Tworzenie i używanie tabel zewnętrznych](create-use-external-tables.md)

- [Przechowywanie wyników zapytania w magazynie](create-external-table-as-select.md)
