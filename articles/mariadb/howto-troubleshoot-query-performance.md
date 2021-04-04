---
title: Rozwiązywanie problemów z wydajnością zapytań — Azure Database for MariaDB
description: Informacje na temat rozwiązywania problemów z wydajnością zapytań w Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: 2b7491723ffcff73e4b243fe54ef18608167d636
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664719"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mariadb"></a>Jak użyć WYJAŚNIj, aby uzyskać informacje o wydajności zapytań dotyczących profilów w Azure Database for MariaDB
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

Jak widać na podstawie tego przykładu, wartość *klucza* jest równa null. To wyjście oznacza, że MariaDB nie może znaleźć żadnych indeksów zoptymalizowanych pod kątem zapytania i wykonuje pełne skanowanie tabeli. Zoptymalizujmy to zapytanie, dodając indeks w kolumnie **ID** .

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

Nowe wyjaśnienie pokazuje, że MariaDB teraz używa indeksu, aby ograniczyć liczbę wierszy do 1, co z kolei znacznie skraca czas wyszukiwania.
 
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

Jak widać na podstawie danych wyjściowych, MariaDB nie używa żadnych indeksów, ponieważ nie są dostępne żadne prawidłowe indeksy. Pokazuje także *czas użycia tymczasowego; Użycie sortowania plików*, co oznacza, że MariaDB tworzy tabelę tymczasową w celu spełnienia klauzuli **Group by** .
 
Utworzenie indeksu dla samego kolumny **C2** nie powoduje żadnych różnic, a MariaDB nadal musi utworzyć tabelę tymczasową:

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

W takim przypadku można utworzyć **pokryty indeks** zarówno **C1** , jak i **C2** , dodając wartość **C2**"bezpośrednio w indeksie, aby wyeliminować dalsze wyszukiwanie danych.

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

Zgodnie z powyższym WYJAŚNIENIEm, MariaDB używa teraz indeksu pokrytego i nie należy tworzyć tabeli tymczasowej. 

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

MariaDB wykonuje operację *sortowania plików* , która jest stosunkowo niska, zwłaszcza gdy musi sortować wiele wierszy. Aby zoptymalizować to zapytanie, połączony indeks można utworzyć dla obu kolumn, które są sortowane.

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

W WYJAŚNIeniu są teraz widoczne, że MariaDB jest w stanie użyć połączonego indeksu, aby uniknąć dodatkowego sortowania, ponieważ indeks jest już posortowany.
 
## <a name="conclusion"></a>Podsumowanie
 
Użycie WYJAŚNIeń i różnych typów indeksów może znacząco zwiększyć wydajność. Indeks tabeli nie musi oznaczać, że MariaDB będzie mógł użyć jej do wykonywania zapytań. Zawsze Weryfikuj założenia przy użyciu WYJAŚNIeń i Optymalizuj zapytania przy użyciu indeksów.

## <a name="next-steps"></a>Następne kroki
- Aby znaleźć odpowiedzi na najczęściej zadawane pytania lub opublikować nowe pytanie/odpowiedź, odwiedź stronę pytania lub [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mariadb)w [witrynie Microsoft Q&](/answers/topics/azure-database-mariadb.html) .