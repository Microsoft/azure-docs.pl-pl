---
title: Zarządzanie danymi historycznymi przy użyciu zasad przechowywania — Azure SQL Edge
description: Dowiedz się, jak zarządzać danymi historycznymi przy użyciu zasad przechowywania w usłudze Azure SQL Edge
keywords: SQL Edge, przechowywanie danych
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: 45ce874ffb626f63b2239c66afdefd091114cbd2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90888138"
---
# <a name="manage-historical-data-with-retention-policy"></a>Zarządzanie danymi historycznymi przy użyciu zasad przechowywania

Przechowywanie danych można włączyć w bazie danych i dowolnych tabelach bazowych, co pozwala użytkownikom na tworzenie elastycznych zasad przedawniania dla ich tabel i baz danych. Stosowanie przechowywania danych jest proste: wymaga ustawienia tylko jednego parametru podczas tworzenia tabeli lub w ramach operacji ALTER TABLE. 

Gdy zasady przechowywania danych są zdefiniowanych przez dla bazy danych i tabeli źródłowej, uruchamiane jest zadanie czasomierza czasu w tle w celu usunięcia wszelkich przestarzałych rekordów z tabeli obsługujących przechowywanie danych. Identyfikacja pasujących wierszy i ich usunięcie z tabeli odbywa się w sposób przezroczysty, w tle zadania, które zostało zaplanowane i uruchomione przez system. Warunek wieku dla wierszy tabeli jest sprawdzany na podstawie kolumny użytej `filter_column` w definicji tabeli. Jeśli na przykład okres przechowywania jest ustawiony na jeden tydzień, wiersze tabeli, które kwalifikują się do oczyszczenia, spełniają jeden z następujących warunków: 

- Jeśli kolumna filtru używa typu danych DATETIMEOFFSET, warunek jest `filter_column < DATEADD(WEEK, -1, SYSUTCDATETIME())`
- W przeciwnym razie warunek jest `filter_column < DATEADD(WEEK, -1, SYSDATETIME())`

## <a name="data-retention-cleanup-phases"></a>Fazy oczyszczania przechowywania danych

Operacja oczyszczania przechowywania danych składa się z dwóch faz. 
- Faza odnajdywania — w tej fazie operacja oczyszczania identyfikuje wszystkie tabele w bazach danych użytkowników, aby utworzyć listę do oczyszczenia. Odnajdywanie jest uruchamiane raz dziennie.
- Faza oczyszczania — w tej fazie oczyszczanie jest uruchamiane dla wszystkich tabel z ograniczonym przechowywaniem danych, które zostały zidentyfikowane w fazie odnajdywania. Jeśli nie można wykonać operacji czyszczenia w tabeli, ta tabela jest pomijana w bieżącym przebiegu i zostanie ponowiona w następnej iteracji. Podczas czyszczenia są używane następujące zasady
    - Jeśli przestarzały wiersz jest zablokowany przez inną transakcję, ten wiersz jest pomijany. 
    - Oczyszczanie przebiega przy użyciu domyślnego ustawienia limitu czasu blokady 5 sekund. Jeśli nie można uzyskać blokad w tabelach w przedziale czasu, tabela jest pomijana w bieżącym przebiegu i zostanie ponowiona w następnej iteracji.
    - Jeśli wystąpi błąd podczas czyszczenia tabeli, ta tabela jest pomijana i zostanie pobrana w następnej iteracji.

## <a name="manual-cleanup"></a>Ręczne czyszczenie

W zależności od ustawień przechowywania danych w tabeli i charakteru obciążenia bazy danych istnieje możliwość, że wątek automatycznego oczyszczania może nie usunąć całkowicie wszystkich przestarzałych wierszy w trakcie jego uruchamiania. Aby pomóc Ci w tym i umożliwić użytkownikom ręczne usuwanie przestarzałych wierszy, `sys.sp_cleanup_data_retention` procedura składowana została wprowadzona w usłudze Azure SQL Edge. 

Ta procedura składowana pobiera trzy parametry. 
    - Nazwa schematu — nazwa schematu będącego właścicielem dla tabeli. Jest to parametr wymagany. 
    - Nazwa tabeli — Nazwa tabeli, dla której jest uruchamiane Oczyszczanie ręczne. Jest to parametr wymagany. 
    - rowcount (Output) — zmienna wyjściowa. Zwraca liczbę wierszy oczyszczonych przez Oczyszczanie ręczne Sp. Jest to opcjonalny parametr. 

Poniższy przykład pokazuje wykonywanie ręcznego oczyszczania SP dla tabeli `dbo.data_retention_table` .

```sql
declare @rowcnt bigint 
EXEC sys.sp_cleanup_data_retention 'dbo', 'data_retention_table', @rowcnt output 
select @rowcnt 
```

## <a name="how-obsolete-rows-are-deleted"></a>Jak są usuwane przestarzałe wiersze

Proces oczyszczania zależy od układu indeksu tabeli. Zadanie w tle jest tworzone w celu przeprowadzenia nieaktualnego oczyszczania danych dla wszystkich tabel z ograniczonym okresem przechowywania. Czyszczenie logiki dla indeksu magazynu wierszy (B-Tree lub sterty) usuwa przestarzały wiersz w mniejszych fragmentach (do 10 000), minimalizując nacisk na dziennik bazy danych i podsystem we/wy. Chociaż logika oczyszczania wykorzystuje wymagany indeks B-drzewa, kolejność usuwania wierszy starszych niż okres przechowywania nie może być gwarantowana. W związku z tym nie należy podejmować żadnych zależności od kolejności oczyszczania w aplikacjach.

Zadanie oczyszczania dla klastrowanej usługi magazynu kolumn usuwa jednocześnie wszystkie grupy wierszy (zazwyczaj zawierają 1 000 000 wierszy każdego), co jest bardzo wydajne, szczególnie w przypadku generowania danych i w dużym tempie.

![Czyszczenie przechowywania danych](./media/data-retention-cleanup/data-retention-cleanup.png)

Doskonałej kompresji danych i wydajne oczyszczanie przechowywania sprawia, że klastrowany indeks magazynu kolumn jest idealnym wyborem dla scenariuszy, gdy obciążenie szybko generuje dużą ilość danych.

> [!Note]
> W przypadku indeksów i sterty drzewa B przechowywanie danych powoduje uruchomienie zapytania usuwania w tabelach bazowych, co może powodować konflikt z wyzwalaczami usuwania w tabelach. Zalecane jest, aby usunąć wyzwalacze usuwania z tabel lub aby nie włączać przechowywania danych w tabelach, które mają usuwanie wyzwalacza DML.

## <a name="monitoring-data-retention-cleanup"></a>Monitorowanie oczyszczania przechowywania danych

Operacje czyszczenia zasad przechowywania danych można monitorować przy użyciu zdarzeń rozszerzonych (XEvents) w usłudze Azure SQL Edge. Aby uzyskać więcej informacji na temat zdarzeń rozszerzonych, zobacz [Omówienie XEvents](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events). 

Następujące sześć zdarzeń rozszerzonych ułatwia śledzenie stanu operacji oczyszczania. 

| Nazwa | Opis |
|------| ------------|
| data_retention_task_started  | Występuje, gdy rozpocznie się zadanie w tle do czyszczenia tabel z zasadami przechowywania. |
| data_retention_task_completed  | Występuje, gdy zadanie w tle do czyszczenia tabel ma zakończenia zasad przechowywania. |
| data_retention_task_exception  | Występuje, gdy zadanie w tle do czyszczenia tabel z zasadami przechowywania zakończy się niepowodzeniem poza procesem oczyszczania przechowywania specyficznym dla tabeli. |
| data_retention_cleanup_started  | Występuje po rozpoczęciu procesu oczyszczania tabeli z zasadami przechowywania danych. |
| data_retention_cleanup_exception  | Występuje proces oczyszczania tabeli z zasadami przechowywania kończy się niepowodzeniem. |
| data_retention_cleanup_completed  | Występuje, gdy trwa czyszczenie procesu tabeli z zasadami przechowywania danych. |  

Ponadto `RING_BUFFER_DATA_RETENTION_CLEANUP` sys.dm_os_ring_buffers do dynamicznego widoku zarządzania został dodany nowy typ buforu pierścieniowego o nazwie. Ten widok może służyć do monitorowania operacji czyszczenia danych. 


## <a name="next-steps"></a>Następne kroki
- [Zasady przechowywania danych](data-retention-overview.md)
- [Włączanie i wyłączanie zasad przechowywania danych](data-retention-enable-disable.md)
