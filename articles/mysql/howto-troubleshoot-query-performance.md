---
title: Rozwiązywanie problemów z wydajnością zapytań — Azure Database for MySQL
description: Informacje na temat rozwiązywania problemów z wydajnością zapytań w Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: 0725de878836e415695d307b68db43802d9b5c2f
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545858"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mysql"></a>Jak użyć WYJAŚNIj, aby uzyskać informacje o wydajności zapytań dotyczących profilów w Azure Database for MySQL
**Wyjaśnienie** to przydatne narzędzie do optymalizowania zapytań. Instrukcji WYJAŚNIj można użyć do uzyskania informacji o sposobie wykonywania instrukcji SQL. Poniższe dane wyjściowe przedstawiają przykład wykonania instrukcji WYJAŚNIeń.

```sql
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 10.00
        Extra: Using where
```

Jak widać na podstawie tego przykładu, wartość *klucza* jest równa null. To wyjście oznacza, że MySQL nie może znaleźć żadnych indeksów zoptymalizowanych pod kątem zapytania i wykonuje pełne skanowanie tabeli. Zoptymalizujmy to zapytanie, dodając indeks w kolumnie **ID** .

```sql
mysql> ALTER TABLE tb1 ADD KEY (id);
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ref
possible_keys: id
          key: id
      key_len: 4
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
```

Nowe wyjaśnienie pokazuje, że program MySQL teraz używa indeksu w celu ograniczenia liczby wierszy do 1, co z kolei znacznie skraca czas wyszukiwania.
 
## <a name="covering-index"></a>Indeks obejmujący
Indeks obejmujący zawiera wszystkie kolumny zapytania w indeksie, aby zmniejszyć liczbę pobieranych wartości z tabel danych. Oto ilustracja w następującej instrukcji **Group by** .
 
```sql
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

Jak widać na podstawie danych wyjściowych, program MySQL nie używa żadnych indeksów, ponieważ nie są dostępne żadne prawidłowe indeksy. Pokazuje także *czas użycia tymczasowego; Przy użyciu sortowania plików* , co oznacza, że baza danych MySQL tworzy tabelę tymczasową w celu spełnienia klauzuli **Group by** .
 
Utworzenie indeksu dla samego kolumny **C2** nie powoduje żadnych różnic, a baza danych MySQL musi utworzyć tabelę tymczasową:

```sql 
mysql> ALTER TABLE tb1 ADD KEY (c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

W takim przypadku można utworzyć **pokryty indeks** zarówno **C1** , jak i **C2** , dodając wartość **C2** "bezpośrednio w indeksie, aby wyeliminować dalsze wyszukiwanie danych.

```sql 
mysql> ALTER TABLE tb1 ADD KEY covered(c1,c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: covered
          key: covered
      key_len: 108
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using index
```

Zgodnie z powyższym WYJAŚNIENIEm program MySQL używa teraz indeksu pokrytego i nie należy tworzyć tabeli tymczasowej. 

## <a name="combined-index"></a>Połączony indeks
Połączony indeks zawiera wartości z wielu kolumn i może być traktowany jako tablica wierszy, które są posortowane przez konkatenację wartości indeksowanych kolumn. Ta metoda może być przydatna w instrukcji **Group by** .

```sql
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using filesort
```

Baza danych MySQL wykonuje operację *sortowania plików* , która jest stosunkowo niska, zwłaszcza gdy musi sortować wiele wierszy. Aby zoptymalizować to zapytanie, połączony indeks można utworzyć dla obu kolumn, które są sortowane.

```sql 
mysql> ALTER TABLE tb1 ADD KEY my_sort2 (c1, c2);
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: NULL
          key: my_sort2
      key_len: 108
          ref: NULL
         rows: 10
     filtered: 11.11
        Extra: Using where; Using index
```

W WYJAŚNIeniu widać teraz, że program MySQL jest w stanie użyć połączonego indeksu, aby uniknąć dodatkowego sortowania, ponieważ indeks jest już posortowany.
 
## <a name="conclusion"></a>Podsumowanie
 
Użycie WYJAŚNIeń i różnych typów indeksów może znacząco zwiększyć wydajność. Indeksowanie tabeli nie musi oznaczać, że program MySQL będzie mógł użyć jej do wykonywania zapytań. Zawsze Weryfikuj założenia przy użyciu WYJAŚNIeń i Optymalizuj zapytania przy użyciu indeksów.


## <a name="next-steps"></a>Następne kroki
- Aby znaleźć odpowiedzi na najczęściej zadawane pytania lub opublikować nowe pytanie/odpowiedź, odwiedź stronę pytania lub [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql)w [witrynie Microsoft Q&](/answers/topics/azure-database-mysql.html) .