---
title: Samopomoc sql on-demand)
description: Ta sekcja zawiera informacje, które mogą pomóc w rozwiązywaniu problemów z SQL na żądanie (wersja zapoznawcza).
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: e2c262915c928cf487cb84aeb3423d67e7a96e97
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424832"
---
# <a name="self-help-for-sql-on-demand-preview"></a>Samopomoc dla sql na żądanie (wersja zapoznawcza)

Ten artykuł zawiera informacje dotyczące rozwiązywania najczęstszych problemów z sql na żądanie (wersja zapoznawcza) w usłudze Azure Synapse Analytics.

## <a name="sql-on-demand-is-grayed-out-in-synapse-studio"></a>SQL na żądanie jest wyszarzony w Synapse Studio

Jeśli Synapse Studio nie może nawiązać połączenia z SQL na żądanie, można zauważyć, że SQL na żądanie jest wyszarzony lub pokazuje stan "Offline". Zwykle ten problem występuje, gdy jeden z następujących przypadków się dzieje:

1) Twoja sieć uniemożliwia komunikację z zapleczem usługi Azure Synapse. Najczęstszym przypadkiem jest to, że port 1443 jest zablokowany. Aby uzyskać SQL na żądanie do pracy odblokować ten port. Inne problemy mogą uniemożliwić sql na żądanie do pracy, jak również, [odwiedź pełny przewodnik rozwiązywania problemów, aby uzyskać więcej informacji](../troubleshoot/troubleshoot-synapse-studio.md).
2) Nie masz uprawnień do logowania się do sql na żądanie. Aby uzyskać dostęp, jeden z administratorów obszaru roboczego Usługi Azure Synapse powinien dodać Cię do administratora obszaru roboczego lub roli administratora SQL. [Więcej informacji można znaleźć w pełnym przewodniku dotyczącym kontroli dostępu.](access-control.md)

## <a name="query-fails-because-file-cannot-be-opened"></a>Kwerenda kończy się niepowodzeniem, ponieważ nie można otworzyć pliku

Jeśli zapytanie nie powiedzie się z błędem z informacją "Plik nie może być otwarty, ponieważ nie istnieje lub jest używany przez inny proces" i masz pewność, że oba pliki istnieją i nie jest używany przez inny proces oznacza SQL na żądanie nie może uzyskać dostępu do pliku. Ten problem zwykle występuje, ponieważ tożsamość usługi Azure Active Directory nie ma praw dostępu do pliku. Domyślnie sql na żądanie próbuje uzyskać dostęp do pliku przy użyciu tożsamości usługi Azure Active Directory. Aby rozwiązać ten problem, musisz mieć odpowiednie prawa dostępu do pliku. Najprostszym sposobem jest przyznanie sobie roli "Współautor danych obiektów blob magazynu" na koncie magazynu, który próbujesz zbadać. [Więcej informacji można znaleźć w pełnym przewodniku dotyczącym kontroli dostępu usługi Azure Active Directory, aby uzyskać więcej informacji.](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>Kwerenda kończy się niepowodzeniem, ponieważ nie można jej wykonać z powodu bieżących ograniczeń zasobów 

Jeśli kwerenda nie powiedzie się z komunikatem o błędzie "Ta kwerenda nie może być wykonana z powodu bieżących ograniczeń zasobów", oznacza to, że SQL OD nie jest w stanie wykonać go w tej chwili z powodu ograniczeń zasobów: 

- Upewnij się, że używane są typy danych o rozsądnych rozmiarach. Należy również określić schemat plików parkietu dla kolumn ciągów, ponieważ będą one domyślnie wywrzeć varchar(8000). 

- Jeśli zapytanie jest przeznaczone dla plików CSV, należy rozważyć [utworzenie statystyk](develop-tables-statistics.md#statistics-in-sql-on-demand-preview). 

- Odwiedź [najlepsze rozwiązania dotyczące wydajności dla języka SQL na żądanie,](best-practices-sql-on-demand.md) aby zoptymalizować kwerendę.  

## <a name="next-steps"></a>Następne kroki

Przejrzyj następujące artykuły, aby dowiedzieć się więcej o używaniu języka SQL na żądanie:

- [Zapytanie o pojedynczy plik CSV](query-single-csv-file.md)

- [Foldery kwerend i wiele plików CSV](query-folders-multiple-csv-files.md)

- [Kwerenda określonych plików](query-specific-files.md)

- [Pliki parkietu kwerendy](query-parquet-files.md)

- [Kwerenda Parkiet zagnieżdżone typy](query-parquet-nested-types.md)

- [Kwerenda plików JSON](query-json-files.md)

- [Tworzenie widoków i korzystanie z ich używania](create-use-views.md)

- [Tworzenie tabel zewnętrznych i korzystanie z ich używania](create-use-external-tables.md)

- [Przechowywanie wyników kwerend do magazynu](create-external-table-as-select.md)
