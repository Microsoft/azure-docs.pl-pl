---
title: 'Zarządzanie zasobem obliczeniowym dla dedykowanej puli SQL (dawniej: SQL DW)'
description: Dowiedz się więcej o możliwościach skalowania wydajności dla dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics. Skalowanie w poziomie przez dostosowanie jednostek dwu lub obniżenie kosztów przez wstrzymanie dedykowanej puli SQL.
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
ms.openlocfilehash: 4a2ce18f4a02ccaadf66a96049b00f3837b927e4
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677041"
---
# <a name="manage-compute-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Zarządzanie obliczeniami dla dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics

Dowiedz się więcej o zarządzaniu dedykowaną pulą SQL zasobów obliczeniowych (dawniej SQL DW) w usłudze Azure Synapse Analytics. Niższe koszty dzięki wstrzymaniu dedykowanej puli SQL lub skalowaniu dedykowanej puli SQL w celu spełnienia wymagań dotyczących wydajności.

## <a name="what-is-compute-management"></a>Co to jest zarządzanie obliczeniami?

Architektura dedykowanej puli SQL (dawniej SQL DW) oddziela magazyn i obliczeniowe, umożliwiając każdemu skalowanie niezależnie. W rezultacie można skalować zasoby obliczeniowe w celu spełnienia wymagań związanych z wydajnością niezależnie od magazynu danych. Można również wstrzymywać i wznawiać działanie zasobów obliczeniowych. Naturalna konsekwencja tej architektury polega na tym, że [rozliczenia](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) na potrzeby obliczeniowe i magazyny są oddzielone. Jeśli nie musisz używać dedykowanej puli SQL (dawniej SQL DW) przez pewien czas, możesz zaoszczędzić koszty obliczeń, zatrzymując obliczenia.

## <a name="scaling-compute"></a>Skalowanie obliczeniowe

Obliczenia można skalować w poziomie lub skali z powrotem przez dostosowanie ustawienia [jednostki magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md) dla dedykowanej puli SQL (dawniej SQL DW). Wydajność ładowania i zapytań można zwiększać liniowo w miarę dodawania większej liczby jednostek magazynu danych.

Aby zapoznać się z krokami skalowania, zobacz Przewodniki Szybki Start dotyczące [Azure Portal](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md)lub [T-SQL](quickstart-scale-compute-tsql.md) . Można również wykonywać operacje skalowania w poziomie za pomocą [interfejsu API REST](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Aby wykonać operację skalowania, dedykowana Pula SQL (dawniej SQL DW) najpierw kasuje wszystkie zapytania przychodzące, a następnie wycofuje transakcje, aby zapewnić spójny stan. Skalowanie jest realizowane dopiero po ukończeniu wycofywania transakcji. W przypadku operacji skalowania system odłącza warstwę magazynu od węzłów obliczeniowych, dodaje węzły obliczeniowe, a następnie ponownie dołącza warstwę magazynu do warstwy obliczeniowej. Każda dedykowana Pula SQL (wcześniej SQL DW) jest przechowywana jako dystrybucja 60, które są równomiernie dystrybuowane do węzłów obliczeniowych. Dodanie większej liczby węzłów obliczeniowych zwiększa moc obliczeniową. Wraz ze wzrostem liczby węzłów obliczeniowych liczba rozkładów na węzeł obliczeniowy zmniejsza się, co zapewnia większą moc obliczeniową dla zapytań. Podobnie, zmniejszenie jednostek magazynu danych zmniejsza liczbę węzłów obliczeniowych, co zmniejsza zasoby obliczeniowe dla zapytań.

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

Aby zobaczyć korzyści wynikające z wydajności skalowania w poziomie, szczególnie w przypadku większych jednostek magazynu danych, należy użyć co najmniej jednego zestawu danych o rozmiarze 1 TB. Aby znaleźć najlepszą liczbę jednostek magazynu danych dla dedykowanej puli SQL (dawniej SQL DW), spróbuj skalować w górę i w dół. Po załadowaniu danych Uruchom kilka zapytań z różnymi liczbami jednostek magazynu danych. Ze względu na to, że skalowanie odbywa się szybko, możesz wypróbować różne poziomy wydajności w ciągu godziny lub mniej.

Zalecenia dotyczące wyszukiwania najlepszej liczby jednostek magazynu danych:

- W przypadku dedykowanej puli SQL (dawniej SQL DW) w opracowaniu Zacznij od wybrania mniejszej liczby jednostek magazynu danych.  Dobrym punktem początkowym jest DW400c lub DW200c.
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
W przypadku wstrzymania dedykowanej puli SQL (dawniej SQL DW):

- Zasoby obliczeniowe i pamięci są zwracane do puli dostępnych zasobów w centrum danych.
- Koszt jednostkowy magazynu danych jest równy zero przez czas wstrzymania.
- Nie dotyczy to przechowywania danych, a Twoje dane pozostają nienaruszone.
- Wszystkie operacje uruchomione lub w kolejce zostały anulowane.

Po wznowieniu dedykowanej puli SQL (dawniej SQL DW):

- Dedykowana Pula SQL (wcześniej SQL DW) uzyskuje zasoby obliczeniowe i pamięci dla ustawienia jednostek magazynu danych.
- Opłaty za usługę obliczeniową dla jednostek magazynu danych są wznawiane.
- Twoje dane staną się dostępne.
- Gdy dedykowana Pula SQL (wcześniej SQL DW) jest w trybie online, należy ponownie uruchomić zapytania dotyczące obciążenia.

Jeśli zawsze chcesz uzyskać dostęp do dedykowanej puli SQL (dawniej SQL DW), rozważ przeskalowanie jej w dół do najmniejszego rozmiaru, a nie wstrzymanie.

Aby uzyskać instrukcje dotyczące wstrzymywania i wznawiania, zobacz [Azure Portal](pause-and-resume-compute-portal.md)lub Przewodniki Szybki Start dla [programu PowerShell](pause-and-resume-compute-powershell.md) . Można również użyć [interfejsu API REST](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) lub [wznowić interfejs API REST](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Opróżnianie transakcji przed wstrzymywaniem i skalowaniem

Zalecamy zezwolenie na zakończenie istniejących transakcji przed zainicjowaniem operacji wstrzymywania lub skalowania.

W przypadku wstrzymania lub skalowania dedykowanej puli SQL (dawniej SQL DW) w tle, gdy zainicjujesz żądanie wstrzymania lub skalowania, zostaną anulowane zapytania. Anulowanie prostego zapytania typu SELECT to szybka operacja, która nie ma prawie żadnego wpływu na czas wstrzymywania lub skalowania wystąpienia.  Może jednak nie być możliwe szybkie zatrzymanie zapytań transakcyjnych, które modyfikują dane lub ich strukturę. **Zapytania transakcyjne należy z założenia wykonać w całości lub wycofać ich zmiany.** Całkowite cofnięcie wyników działania zapytania transakcyjnego może trwać równie długo lub nawet dłużej niż pierwotna zmiana wprowadzona przez zapytanie. Na przykład w przypadku anulowania zapytania, którego zadaniem było usunięcie wierszy i które było uruchomione przez godzinę, może upłynąć kolejna godzina, zanim system z powrotem wstawi wiersze, które zostały usunięte. W przypadku uruchomienia procedury wstrzymywania lub skalowania w toku transakcji operacja wstrzymywania lub skalowania może zająć dużo czasu, ponieważ zanim będzie możliwe jej wykonanie, zmiany muszą zostać w pełni cofnięte.

Zobacz też [Omówienie transakcji](sql-data-warehouse-develop-transactions.md)i [Optymalizowanie transakcji](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatyzowanie zarządzania obliczeniami

Aby zautomatyzować operacje zarządzania obliczeniami, zobacz [Zarządzanie obliczeniami przy użyciu usługi Azure Functions](manage-compute-with-azure-functions.md).

Każda operacja skalowania w poziomie, Wstrzymaj i Wznów może potrwać kilka minut. W przypadku automatycznego skalowania, wstrzymywania lub wznawiania zalecamy wdrożenie logiki, aby upewnić się, że niektóre operacje zostały zakończone przed przejściem do innej akcji. Sprawdzanie dedykowanej puli SQL (dawniej SQL DW) za pomocą różnych punktów końcowych pozwala na prawidłowe wdrożenie automatyzacji takich operacji.

Aby sprawdzić dedykowany stan puli SQL (dawniej SQL DW), zobacz Przewodnik Szybki Start dotyczący programu [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) lub [języka T-SQL](quickstart-scale-compute-tsql.md#check-dedicated-sql-pool-formerly-sql-dw-state) . Możesz również sprawdzić dedykowaną pulę SQL (dawniej SQL DW) za pomocą [interfejsu API REST](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).

## <a name="permissions"></a>Uprawnienia

Skalowanie dedykowanej puli SQL (dawniej SQL DW) wymaga uprawnień opisanych w temacie [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Polecenie Wstrzymaj i Wznów wymaga uprawnienia [współautora bazy danych SQL](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#sql-db-contributor) , w tym Microsoft. SQL/serwery/bazy danych/działanie.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem Przewodnik dotyczący [zarządzania obliczeniami](manage-compute-with-azure-functions.md) inny aspekt zarządzania zasobami obliczeniowymi polega na przydzielaniu różnych zasobów obliczeniowych dla poszczególnych zapytań. Aby uzyskać więcej informacji, zobacz [klasy zasobów dla zarządzania obciążeniami](resource-classes-for-workload-management.md).
