---
title: Wersja zapoznawcza na żądanie SQL) Samopomoc
description: Ta sekcja zawiera informacje, które mogą pomóc w rozwiązywaniu problemów z programem SQL na żądanie (wersja zapoznawcza).
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: e2c262915c928cf487cb84aeb3423d67e7a96e97
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424832"
---
# <a name="self-help-for-sql-on-demand-preview"></a>Samoobsługowa pomoc dla SQL na żądanie (wersja zapoznawcza)

Ten artykuł zawiera informacje o sposobie rozwiązywania problemów z większością częstych problemów związanych z programem SQL na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics.

## <a name="sql-on-demand-is-grayed-out-in-synapse-studio"></a>Program SQL on-Demand jest wyszarzony w programie Synapse Studio

Jeśli program Synapse Studio nie może nawiązać połączenia z usługą SQL na żądanie, zobaczysz, że wartość SQL na żądanie jest wyszarzona lub zawiera stan "offline". Zazwyczaj ten problem występuje, gdy wystąpi jedno z następujących przypadków:

1) Twoja sieć uniemożliwia komunikację z zapleczem usługi Azure Synapse. W większości przypadków jest blokowany port 1443. Aby uzyskać dostęp do programu SQL Server na żądanie w celu odblokowania tego portu. Inne problemy mogą uniemożliwić działanie programu SQL na żądanie. Aby uzyskać [więcej informacji, odwiedź pełny Przewodnik rozwiązywania problemów](../troubleshoot/troubleshoot-synapse-studio.md).
2) Nie masz uprawnień do logowania się do programu SQL na żądanie. Aby uzyskać dostęp, jedna z administratorów obszaru roboczego usługi Azure Synapse powinna dodać użytkownika do roli administratora obszaru roboczego lub administratora SQL. [Aby uzyskać więcej informacji, odwiedź pełny Przewodnik po kontroli dostępu](access-control.md).

## <a name="query-fails-because-file-cannot-be-opened"></a>Zapytanie nie powiodło się, ponieważ nie można otworzyć pliku

Jeśli zapytanie nie powiedzie się z powodu błędu "nie można otworzyć pliku, ponieważ nie istnieje lub jest używany przez inny proces" i masz pewność, że oba pliki istnieją i nie są używane przez inny proces, oznacza to, że usługa SQL na żądanie nie może uzyskać dostępu do pliku. Ten problem jest zwykle spowodowany tym, że tożsamość Azure Active Directory nie ma uprawnień dostępu do pliku. Domyślnie SQL na żądanie próbuje uzyskać dostęp do pliku przy użyciu tożsamości Azure Active Directory. Aby rozwiązać ten problem, musisz mieć odpowiednie prawa dostępu do pliku. Najprostszym sposobem jest przyznanie sobie roli współautor danych obiektu blob magazynu na koncie magazynu, które próbujesz zbadać. [Aby uzyskać więcej informacji, odwiedź pełny Przewodnik po Azure Active Directory kontroli dostępu do magazynu](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). 

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>Zapytanie nie powiodło się, ponieważ nie można go wykonać ze względu na bieżące ograniczenia zasobów 

Jeśli zapytanie nie powiedzie się i zostanie wyświetlony komunikat o błędzie "to zapytanie nie może zostać wykonane z powodu bieżących ograniczeń zasobów", oznacza to, że w tej chwili nie można wykonać tego zapytania z powodu ograniczeń zasobów: 

- Upewnij się, że są używane typy danych o rozsądnych rozmiarach. Ponadto Określ schemat dla plików Parquet dla kolumn ciągów, ponieważ będą one domyślnie VARCHAR (8000). 

- Jeśli zapytanie wskazuje pliki CSV, należy rozważyć [utworzenie statystyk](develop-tables-statistics.md#statistics-in-sql-on-demand-preview). 

- Zapoznaj się [z najlepszymi rozwiązaniami dotyczącymi wydajności dla programu SQL na żądanie](best-practices-sql-on-demand.md) , aby zoptymalizować zapytanie.  

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej na temat korzystania z programu SQL na żądanie:

- [Kwerenda pojedynczego pliku CSV](query-single-csv-file.md)

- [Foldery zapytań i wiele plików CSV](query-folders-multiple-csv-files.md)

- [Kwerenda określonych plików](query-specific-files.md)

- [Wykonywanie zapytań względem plików Parquet](query-parquet-files.md)

- [Wykonywanie zapytań względem typów zagnieżdżonych Parquet](query-parquet-nested-types.md)

- [Wykonywanie zapytań względem plików JSON](query-json-files.md)

- [Tworzenie widoków i korzystanie z nich](create-use-views.md)

- [Tworzenie i używanie tabel zewnętrznych](create-use-external-tables.md)

- [Przechowywanie wyników zapytania w magazynie](create-external-table-as-select.md)
