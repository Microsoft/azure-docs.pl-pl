---
title: Zarządzanie zasobem obliczeniowym dla puli SQL
description: Dowiedz się więcej o możliwościach skalowania wydajności w puli SQL usługi Azure Synapse Analytics. Skalowanie w poziomie przez dostosowanie jednostek dwu lub niższych kosztów przez wstrzymanie magazynu danych.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 90815d52e6884efe6cff9a7860c093b4b5c1bc94
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85204545"
---
# <a name="manage-compute-in-azure-synapse-analytics-data-warehouse"></a>Zarządzanie obliczeniami w magazynie danych usługi Azure Synapse Analytics

Dowiedz się więcej na temat zarządzania zasobami obliczeniowymi w puli SQL usługi Azure Synapse Analytics. Niższe koszty dzięki wstrzymaniu puli SQL lub skalowaniu magazynu danych w celu spełnienia wymagań dotyczących wydajności.

## <a name="what-is-compute-management"></a>Co to jest zarządzanie obliczeniami?

Architektura magazynu danych polega na rozdzieleniu magazynu i obliczeń, co pozwala na niezależne skalowanie. W rezultacie można skalować zasoby obliczeniowe w celu spełnienia wymagań związanych z wydajnością niezależnie od magazynu danych. Można również wstrzymywać i wznawiać działanie zasobów obliczeniowych. Naturalna konsekwencja tej architektury polega na tym, że [rozliczenia](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) na potrzeby obliczeniowe i magazyny są oddzielone. Jeśli przez pewien czas nie musisz używać magazynu, możesz zaoszczędzić na kosztach zasobów obliczeniowych, wstrzymując obliczenia.

## <a name="scaling-compute"></a>Skalowanie obliczeniowe

Obliczenia można skalować w poziomie lub skali z powrotem przez dostosowanie ustawienia [jednostki magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md) dla puli SQL. Wydajność ładowania i zapytań można zwiększać liniowo w miarę dodawania większej liczby jednostek magazynu danych.

Aby zapoznać się z krokami skalowania, zobacz Przewodniki Szybki Start dotyczące [Azure Portal](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md)lub [T-SQL](quickstart-scale-compute-tsql.md) . Można również wykonywać operacje skalowania w poziomie za pomocą [interfejsu API REST](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Aby wykonać operację skalowania, usługa SQL Pool najpierw kasuje wszystkie zapytania przychodzące, a następnie wycofuje transakcje, aby zapewnić spójny stan. Skalowanie jest realizowane dopiero po ukończeniu wycofywania transakcji. W przypadku operacji skalowania system odłącza warstwę magazynu od węzłów obliczeniowych, dodaje węzły obliczeniowe, a następnie ponownie dołącza warstwę magazynu do warstwy obliczeniowej. Każda pula SQL jest przechowywana jako dystrybucje 60, które są równomiernie dystrybuowane do węzłów obliczeniowych. Dodanie większej liczby węzłów obliczeniowych zwiększa moc obliczeniową. Wraz ze wzrostem liczby węzłów obliczeniowych liczba rozkładów na węzeł obliczeniowy zmniejsza się, co zapewnia większą moc obliczeniową dla zapytań. Podobnie, zmniejszenie jednostek magazynu danych zmniejsza liczbę węzłów obliczeniowych, co zmniejsza zasoby obliczeniowe dla zapytań.

W poniższej tabeli przedstawiono, w jaki sposób liczba dystrybucji na węzeł obliczeniowy zmienia się w miarę zmiany jednostek magazynu danych.  DW30000c udostępnia 60 węzłów obliczeniowych i osiąga znacznie wyższą wydajność zapytań niż DW100c.

| Jednostki magazynu danych  | \# węzłów obliczeniowych | \# z dystrybucji na węzeł |
| -------- | ---------------- | -------------------------- |
| DW100c   | 1                | 60                         |
| DW200c   | 1                | 60                         |
| DW300c   | 1                | 60                         |
| DW400c   | 1                | 60                         |
| DW500c.   | 1                | 60                         |
| DW1000c  | 2                | 30                         |
| DW1500c  | 3                | 20                         |
| DW2000c  | 4                | 15                         |
| DW2500c  | 5                | 12                         |
| DW3000c  | 6                | 10                         |
| DW5000c  | 10               | 6                          |
| DW6000c  | 12               | 5                          |
| DW7500c  | 15               | 4                          |
| DW10000c | 20               | 3                          |
| DW15000c | 30               | 2                          |
| DW30000c | 60               | 1                          |

## <a name="finding-the-right-size-of-data-warehouse-units"></a>Znajdowanie odpowiedniego rozmiaru jednostek magazynu danych

Aby zobaczyć korzyści wynikające z wydajności skalowania w poziomie, szczególnie w przypadku większych jednostek magazynu danych, należy użyć co najmniej jednego zestawu danych o rozmiarze 1 TB. Aby znaleźć najlepszą liczbę jednostek magazynu danych dla puli SQL, spróbuj skalować w górę i w dół. Po załadowaniu danych Uruchom kilka zapytań z różnymi liczbami jednostek magazynu danych. Ze względu na to, że skalowanie odbywa się szybko, możesz wypróbować różne poziomy wydajności w ciągu godziny lub mniej.

Zalecenia dotyczące wyszukiwania najlepszej liczby jednostek magazynu danych:

- W przypadku puli SQL w trakcie tworzenia Zacznij od wybrania mniejszej liczby jednostek magazynu danych.  Dobrym punktem początkowym jest DW400c lub DW200c.
- Monitoruj wydajność aplikacji, obserwując liczbę wybranych jednostek magazynu danych w porównaniu do podanej wydajności.
- Założono skalę liniową i określić, ile potrzeba do zwiększenia lub zmniejszenia jednostek magazynu danych.
- Kontynuuj wprowadzanie zmian, dopóki nie osiągniesz optymalnego poziomu wydajności dla wymagań firmy.

## <a name="when-to-scale-out"></a>Kiedy skalować w poziomie

Skalowanie jednostek magazynu danych wpływa na następujące aspekty wydajności:

- Liniowie poprawia wydajność systemu na potrzeby skanów, agregacji i instrukcji CTAS.
- Zwiększa liczbę czytników i składników zapisywania do ładowania danych.
- Maksymalna liczba współbieżnych zapytań i miejsc współbieżności.

Zalecenia dotyczące skalowania jednostek magazynu danych w poziomie:

- Zanim zaczniesz wykonywać duże operacje ładowania lub przekształcania danych, Skaluj w poziomie, aby zapewnić szybsze dostęp do danych.
- W czasie szczytu godzin pracy można skalować w poziomie, aby pomieścić większą liczbę współbieżnych zapytań.

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Co w przypadku skalowania w dół nie poprawia wydajności?

Dodanie jednostek magazynu danych zwiększa równoległość. Jeśli prace są równo podzielone między węzłami obliczeniowymi, dodatkowa równoległość zwiększa wydajność zapytań. Jeśli skalowanie w dół nie zmienia wydajności, istnieje kilka powodów, dla których może się to zdarzyć. Dane mogą być pochylone w ramach dystrybucji lub mogą wprowadzać duże ilości danych. Aby zbadać problemy z wydajnością zapytań, zobacz [Rozwiązywanie problemów z wydajnością](sql-data-warehouse-troubleshoot.md#performance).

## <a name="pausing-and-resuming-compute"></a>Wstrzymywanie i wznawianie procesów obliczeniowych

Wstrzymywanie obliczeń powoduje odłączenie warstwy magazynu od węzłów obliczeniowych. Zasoby obliczeniowe są wydawane z Twojego konta. Nie jest naliczana opłata za obliczenia, gdy obliczenie jest wstrzymane. Wznawianie obliczeń ponownie dołącza magazyn do węzłów obliczeniowych i wznawia opłaty za obliczenia.
W przypadku wstrzymania puli SQL:

- Zasoby obliczeniowe i pamięci są zwracane do puli dostępnych zasobów w centrum danych.
- Koszt jednostkowy magazynu danych jest równy zero przez czas wstrzymania.
- Nie dotyczy to przechowywania danych, a Twoje dane pozostają nienaruszone.
- Wszystkie operacje uruchomione lub w kolejce zostały anulowane.

Po wznowieniu puli SQL:

- Pula SQL uzyskuje zasoby obliczeniowe i pamięci dla ustawienia jednostek magazynu danych.
- Opłaty za usługę obliczeniową dla jednostek magazynu danych są wznawiane.
- Twoje dane staną się dostępne.
- Gdy pula SQL jest w trybie online, należy ponownie uruchomić zapytania dotyczące obciążenia.

Jeśli zawsze chcesz uzyskać dostęp do puli SQL, rozważ przeskalowanie jej w dół do najmniejszego rozmiaru, a nie wstrzymanie.

Aby uzyskać instrukcje dotyczące wstrzymywania i wznawiania, zobacz [Azure Portal](pause-and-resume-compute-portal.md)lub Przewodniki Szybki Start dla [programu PowerShell](pause-and-resume-compute-powershell.md) . Można również użyć [interfejsu API REST](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) lub [wznowić interfejs API REST](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Opróżnianie transakcji przed wstrzymywaniem i skalowaniem

Zalecamy zezwolenie na zakończenie istniejących transakcji przed zainicjowaniem operacji wstrzymywania lub skalowania.

W przypadku wstrzymania lub skalowania puli SQL w tle są anulowane zapytania po zainicjowaniu żądania wstrzymania lub skalowania. Anulowanie prostego zapytania typu SELECT to szybka operacja, która nie ma prawie żadnego wpływu na czas wstrzymywania lub skalowania wystąpienia.  Może jednak nie być możliwe szybkie zatrzymanie zapytań transakcyjnych, które modyfikują dane lub ich strukturę. **Zapytania transakcyjne należy z założenia wykonać w całości lub wycofać ich zmiany.** Całkowite cofnięcie wyników działania zapytania transakcyjnego może trwać równie długo lub nawet dłużej niż pierwotna zmiana wprowadzona przez zapytanie. Na przykład w przypadku anulowania zapytania, którego zadaniem było usunięcie wierszy i które było uruchomione przez godzinę, może upłynąć kolejna godzina, zanim system z powrotem wstawi wiersze, które zostały usunięte. W przypadku uruchomienia procedury wstrzymywania lub skalowania w toku transakcji operacja wstrzymywania lub skalowania może zająć dużo czasu, ponieważ zanim będzie możliwe jej wykonanie, zmiany muszą zostać w pełni cofnięte.

Zobacz też [Omówienie transakcji](sql-data-warehouse-develop-transactions.md)i [Optymalizowanie transakcji](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatyzowanie zarządzania obliczeniami

Aby zautomatyzować operacje zarządzania obliczeniami, zobacz [Zarządzanie obliczeniami przy użyciu usługi Azure Functions](manage-compute-with-azure-functions.md).

Każda operacja skalowania w poziomie, Wstrzymaj i Wznów może potrwać kilka minut. W przypadku automatycznego skalowania, wstrzymywania lub wznawiania zalecamy wdrożenie logiki, aby upewnić się, że niektóre operacje zostały zakończone przed przejściem do innej akcji. Sprawdzenie stanu puli SQL za pomocą różnych punktów końcowych pozwala na prawidłowe wdrożenie automatyzacji takich operacji.

Aby sprawdzić stan puli SQL, zobacz Przewodnik Szybki Start dotyczący programu [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) lub [języka T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) . Stan puli SQL można także sprawdzić za pomocą [interfejsu API REST](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).

## <a name="permissions"></a>Uprawnienia

Skalowanie puli SQL wymaga uprawnień opisanych w [instrukcji ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  Polecenie Wstrzymaj i Wznów wymaga uprawnienia [współautora bazy danych SQL](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#sql-db-contributor) , w tym Microsoft. SQL/serwery/bazy danych/działanie.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem Przewodnik dotyczący [zarządzania obliczeniami](manage-compute-with-azure-functions.md) inny aspekt zarządzania zasobami obliczeniowymi polega na przydzielaniu różnych zasobów obliczeniowych dla poszczególnych zapytań. Aby uzyskać więcej informacji, zobacz [klasy zasobów dla zarządzania obciążeniami](resource-classes-for-workload-management.md).
