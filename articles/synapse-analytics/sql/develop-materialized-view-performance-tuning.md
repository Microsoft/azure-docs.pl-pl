---
title: Strojenie wydajności za pomocą zmaterializowanych widoków
description: Zalecenia i zagadnienia, które należy znać podczas używania zmaterializowanych widoków w celu zwiększenia wydajności kwerendy.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: 30ca03633b9b0788235439204a3c1926fe6b6a6b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429982"
---
# <a name="performance-tuning-with-materialized-views"></a>Strojenie wydajności za pomocą zmaterializowanych widoków

W puli SQL Synapse zmaterializowane widoki zapewniają metodę niskiej konserwacji dla złożonych zapytań analitycznych, aby uzyskać szybką wydajność bez zmiany zapytania. W tym artykule omówiono ogólne wskazówki dotyczące korzystania ze zmaterializowanych widoków.

## <a name="materialized-views-vs-standard-views"></a>Widoki zmaterializowane a widoki standardowe

Pula SQL obsługuje zarówno widoki standardowe, jak i zmaterializowane.  Obie są tabelami wirtualnymi utworzonymi za pomocą wyrażeń SELECT i prezentowanymi kwerendom jako tabele logiczne.  Widoki ujawniają złożoność obliczania wspólnych danych i dodają warstwę abstrakcji do zmian obliczeniowych, dzięki czemu nie ma potrzeby przepisywania kwerend.  

Widok standardowy oblicza swoje dane za każdym razem, gdy widok jest używany.  Na dysku nie są przechowywane żadne dane. Ludzie zazwyczaj używają widoków standardowych jako narzędzia, które pomaga organizować obiekty logiczne i kwerendy w bazie danych.  Aby użyć widoku standardowego, kwerenda musi bezpośrednio odwoływać się do niego.

Zmaterializowany widok wstępnie oblicza, przechowuje i przechowuje swoje dane w puli SQL, podobnie jak w tabeli.  Ponowne przeliczanie nie jest potrzebne za każdym razem, gdy używany jest zmaterializowany widok.  Dlatego kwerendy, które używają wszystkich lub podzbiór danych w widokach materializowanych można uzyskać większą wydajność.  Co więcej, kwerendy można użyć zmaterializowanego widoku bez bezpośredniego odwoływania się do niego, więc nie ma potrzeby zmiany kodu aplikacji.  

Większość wymagań dotyczących widoku standardowego nadal ma zastosowanie do widoku zmaterializowanego. Szczegółowe informacje na temat składni widoku materializowanego i innych wymagań można znaleźć w [1944 R. Tworzenie zmaterializowanego widoku jako wybierz](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

| Porównanie                     | Widok                                         | Zmaterializowany widok
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|Definicja widoku                 | Przechowywane w magazynie danych platformy Azure.              | Przechowywane w magazynie danych platformy Azure.
|Wyświetlanie zawartości                    | Generowane za każdym razem, gdy widok jest używany.   | Wstępnie przetworzone i przechowywane w magazynie danych platformy Azure podczas tworzenia widoku. Aktualizowane w miarę dodawania danych do tabel bazowych.
|Odświeżanie danych                    | Zawsze aktualizowane                               | Zawsze aktualizowane
|Szybkość pobierania danych widoku ze złożonych zapytań     | Powolny                                         | Szybko  
|Dodatkowa pamięć masowa                   | Nie                                           | Tak
|Składnia                          | TWORZENIE WIDOKU                                  | TWORZENIE ZMATERIALIZOWANEGO WIDOKU JAKO ZAZNACZANIE

## <a name="benefits-of-using-materialized-views"></a>Korzyści z używania zmaterializowanych widoków

Prawidłowo zaprojektowany zmaterializowany widok zapewnia następujące korzyści:

- Skrócono czas wykonywania złożonych zapytań z sieciami JON i funkcjami agregucyjnymi. Im bardziej złożona kwerenda, tym wyższy potencjał oszczędzania czasu wykonywania. Największa korzyść uzyskuje się, gdy koszt obliczeń kwerendy jest wysoki, a wynikowy zestaw danych jest mały.  

- Optymalizator w puli SQL może automatycznie używać wdrożonych zmaterializowanych widoków w celu poprawy planów wykonywania kwerend.  Ten proces jest przejrzysty dla użytkowników zapewniających szybszą wydajność zapytań i nie wymaga od kwerend bezpośredniego odwoływania się do zmaterializowanych widoków.

- Wymaga niskiej konserwacji widoków.  Zmaterializowany widok przechowuje dane w dwóch miejscach, indeks klastrowanego magazynu kolumn dla danych początkowych w czasie tworzenia widoku i magazyn delta dla zmian danych przyrostowych.  Wszystkie zmiany danych z tabel podstawowych są automatycznie dodawane do magazynu delta w sposób synchroniczne.  Proces w tle (przechodzenie do krotki) okresowo przenosi dane z magazynu delta do indeksu magazynu kolumn widoku.  Ten projekt umożliwia wykonywanie zapytań zmaterializowanych widoków w celu zwrócenia tych samych danych, co bezpośrednie wykonywanie zapytań o tabele podstawowe.
- Dane w zmaterializowanym widoku mogą być rozłożone inaczej niż w tabelach bazowych.  
- Dane w zmaterializowanych widokach uzyskuje takie same korzyści wysokiej dostępności i odporności jak dane w zwykłych tabelach.  

W porównaniu z innymi dostawcami magazynu danych zmaterializowane widoki zaimplementowane w puli SQL zapewniają również następujące dodatkowe korzyści:

- Automatyczne i synchroniczne odświeżanie danych ze zmianami danych w tabelach bazowych. Nie jest wymagana żadna akcja użytkownika.
- Szeroka obsługa funkcji agregujących. Zobacz [TWORZENIE ZMATERIALIZOWANEGO WIDOKU JAKO SELECT (Transact-SQL)](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).
- Obsługa zalecenia zmaterializowanego widoku specyficzne dla kwerendy.  Zobacz [OBJAŚNIENIE (Transact-SQL)](/sql/t-sql/queries/explain-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="common-scenarios"></a>Typowe scenariusze  

Widoki materializowane są zwykle używane w następujących scenariuszach:

**Potrzeba poprawy wydajności złożonych zapytań analitycznych w stosunku do dużych ilości danych**

Złożone zapytania analityczne zazwyczaj używają więcej funkcji agregacji i sprzężeń tabeli, powodując więcej operacji ciężkich obliczeniowych, takich jak przetasowania i sprzężenia w wykonywaniu kwerend.  Dlatego te zapytania trwać dłużej, szczególnie na dużych tabelach.  

Użytkownicy mogą tworzyć zmaterializowane widoki dla danych zwracanych ze wspólnych obliczeń zapytań, więc nie jest wymagana ponowna obliczenie, gdy te dane są potrzebne przez kwerendy, co pozwala na niższy koszt obliczeń i szybszą odpowiedź na kwerendy.

**Potrzebujesz szybszej wydajności bez zmian w kwerendach lub minimalnych**

Zmiany schematu i kwerend w magazynach danych są zazwyczaj ograniczone do minimum w celu obsługi regularnych operacji ETL i raportowania.  Użytkownicy mogą używać zmaterializowanych widoków do dostrajania wydajności kwerendy, jeśli koszt ponoszony przez widoki może zostać przesunięty przez wzrost wydajności kwerendy.

W porównaniu do innych opcji strojenia, takich jak skalowanie i zarządzanie statystykami, jest to znacznie mniej istotna zmiana produkcji w celu utworzenia i utrzymania zmaterializowanego widoku, a jego potencjalny przyrost wydajności jest również wyższy.

- Tworzenie lub utrzymywanie zmaterializowanych widoków nie ma wpływu na kwerendy uruchomione względem tabel podstawowych.
- Optymalizator kwerendy może automatycznie używać wdrożonych widoków materialowanych bez bezpośredniego odwołania do widoku w kwerendzie. Ta funkcja zmniejsza potrzebę zmiany zapytania w dostrajaniu wydajności.

**Potrzebujesz innej strategii dystrybucji danych, aby uzyskać szybszą wydajność zapytań**

Magazyn danych platformy Azure to rozproszony i masowo równoległy system przetwarzania (MPP).   Dane w tabeli magazynu danych są dystrybuowane w 60 węzłach przy użyciu jednej z trzech [strategii dystrybucji](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (skrótu, round_robin lub replikacji).  

Dystrybucja danych jest określona w czasie tworzenia tabeli i pozostaje niezmieniona, dopóki tabela nie zostanie porzucona. Zmaterializowany widok będący tabelą wirtualną na dysku obsługuje dystrybucje skrótów i round_robin danych.  Użytkownicy mogą wybrać dystrybucję danych, która różni się od tabel podstawowych, ale optymalna dla wydajności zapytań, które często używają widoków.  

## <a name="design-guidance"></a>Wskazówki dotyczące projektu

Oto ogólne wskazówki dotyczące korzystania ze zmaterializowanych widoków w celu zwiększenia wydajności kwerendy:

**Projektowanie dla obciążenia**

Przed rozpoczęciem tworzenia zmaterializowanych widoków, ważne jest, aby mieć głębokie zrozumienie obciążenia pod względem wzorców zapytań, ważność, częstotliwość i rozmiar danych wynikowych.  

Użytkownicy mogą uruchamiać WITH_RECOMMENDATIONS EXPLAIN <SQL_statement> dla zmaterializowanych widoków zalecanych przez optymalizator kwerend.  Ponieważ te zalecenia są specyficzne dla kwerendy, zmaterializowany widok, który przynosi korzyści pojedynczej kwerendzie, może nie być optymalny dla innych zapytań w tym samym obciążeniu.  

Oceń te zalecenia, mając na uwadze potrzeby związane z obciążeniem pracą.  Idealne zmaterializowane widoki to te, które przynoszą korzyści wydajności obciążenia.  

**Należy pamiętać o kompromisie między szybszymi zapytaniami a kosztami**

Dla każdego zmaterializowanego widoku jest koszt przechowywania danych i koszt utrzymania widoku.  Wraz ze zmianami danych w tabelach bazowych zwiększa się rozmiar zmaterializowanego widoku, a jego struktura fizyczna również się zmienia.  

Aby uniknąć pogorszenia wydajności kwerendy, każdy zmaterializowany widok jest obsługiwany oddzielnie przez aparat magazynu danych, w tym przenoszenie wierszy z magazynu delta do segmentów indeksu magazynu kolumn i konsolidowanie zmian danych.  

Obciążenie związane z konserwacją wzrasta, gdy zwiększa się liczba zmaterializowanych widoków i zmian w tabeli bazowej.   Użytkownicy powinni sprawdzić, czy koszt poniesiony ze wszystkich zmaterializowanych widoków może być równoważony przez wzrost wydajności kwerendy.  

Tę kwerendę można uruchomić dla listy zmaterializowanych widoków w bazie danych:

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

Opcje zmniejszania liczby zmaterializowanych widoków:

- Identyfikowanie typowych zestawów danych często używanych przez złożone zapytania w obciążeniu.  Utwórz zmaterializowane widoki do przechowywania tych zestawów danych, dzięki czemu optymalizator może używać ich jako bloków konstrukcyjnych podczas tworzenia planów wykonania.  

- Upuść zmaterializowane widoki, które mają niskie użycie lub nie są już potrzebne.  Wyłączony widok materialized nie jest zachowywany, ale nadal ponosi koszty magazynu.  

- Łączenie zmaterializowanych widoków utworzonych w tych samych lub podobnych tabelach bazowych, nawet jeśli ich dane nie nakładają się na siebie.  Połączenie zmaterializowanych widoków może spowodować większy rozmiar niż suma oddzielnych widoków, jednak koszt konserwacji widoku powinien zostać zmniejszony.  Przykład:

```sql
-- Query 1 would benefit from having a materialized view created with this SELECT statement
SELECT A, SUM(B)
FROM T
GROUP BY A

-- Query 2 would benefit from having a materialized view created with this SELECT statement
SELECT C, SUM(D)
FROM T
GROUP BY C

-- You could create a single mateiralized view of this form
SELECT A, C, SUM(B), SUM(D)
FROM T
GROUP BY A, C

```

**Nie wszystkie dostrajanie wydajności wymaga zmiany zapytania**

Optymalizator magazynu danych może automatycznie używać wdrożonych zmaterializowanych widoków w celu zwiększenia wydajności kwerend.  Ta obsługa jest stosowana w sposób przejrzysty do kwerend, które nie odwołują się do widoków i do kwerend, które używają agregatów nieobsługiwał w tworzeniu zmaterializowanych widoków.  Nie jest wymagana żadna zmiana kwerendy. Można sprawdzić plan szacowanego wykonania kwerendy, aby potwierdzić, czy używany jest widok zmaterializowany.  

**Monitoruj zmaterializowane widoki**

Zmaterializowany widok jest przechowywany w magazynie danych, podobnie jak tabela z indeksem klastrowanego magazynu kolumn (CCI).  Odczyt danych z zmaterializowanego widoku obejmuje skanowanie indeksu i stosowanie zmian ze sklepu delta.  Gdy liczba wierszy w magazynie delta jest zbyt wysoka, rozwiązywanie kwerendy z zmaterializowanego widoku może trwać dłużej niż bezpośrednie wykonywanie zapytań do tabel podstawowych.  Aby uniknąć pogorszenia wydajności kwerendy, dobrą praktyką jest uruchomienie [PDW_SHOWMATERIALIZEDVIEWOVERHEAD DBCC](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) w celu monitorowania overhead_ratio widoku (total_rows / base_view_row).  Jeśli overhead_ratio jest zbyt wysoka, należy rozważyć odbudowanie zmaterializowanego widoku, więc wszystkie wiersze w magazynie delta są przenoszone do indeksu magazynu kolumn.  

**Zmaterializowane buforowanie widoku i zestawu wyników**

Te dwie funkcje są wprowadzane w puli SQL w tym samym czasie dostrajania wydajności kwerendy. Buforowanie zestawu wyników służy do osiągnięcia wysokiej współbieżności i szybkiego czasu odpowiedzi z powtarzających się zapytań względem danych statycznych.  

Aby użyć buforowanego wyniku, formularz kwerendy żądającej pamięci podręcznej musi być zgodny z kwerendą, która wyprodukowała pamięć podręczną.  Ponadto wynik buforowane musi mieć zastosowanie do całej kwerendy.  Zmaterializowane widoki umożliwiają zmiany danych w tabelach bazowych.  Dane w zmaterializowanych widokach można zastosować do fragmentu kwerendy.  Ta obsługa umożliwia te same zmaterializowane widoki, które mają być używane przez różne kwerendy, które współużytkuje niektóre obliczenia dla szybszej wydajności.

## <a name="example"></a>Przykład

W tym przykładzie użyto zapytania podobnego do TPCDS, które znajduje klientów, którzy wydają więcej pieniędzy za pośrednictwem katalogu niż w sklepach. Identyfikuje również preferowanych klientów i ich kraj pochodzenia.   Kwerenda polega na wybraniu TOP 100 rekordów z unii trzech instrukcji sub-SELECT obejmujących SUM() i GROUP BY.

```sql
WITH year_total AS (
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
       ,'s' sale_type
FROM customer
     ,store_sales
     ,date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
       ,'c' sale_type
FROM customer
     ,catalog_sales
     ,date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
       ,'w' sale_type
FROM customer
     ,web_sales
     ,date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
         )
  SELECT TOP 100
                  t_s_secyear.customer_id
                 ,t_s_secyear.customer_first_name
                 ,t_s_secyear.customer_last_name
                 ,t_s_secyear.customer_birth_country
FROM year_total t_s_firstyear
     ,year_total t_s_secyear
     ,year_total t_c_firstyear
     ,year_total t_c_secyear
     ,year_total t_w_firstyear
     ,year_total t_w_secyear
WHERE t_s_secyear.customer_id = t_s_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_c_secyear.customer_id
   AND t_s_firstyear.customer_id = t_c_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_secyear.customer_id
   AND t_s_firstyear.sale_type = 's'
   AND t_c_firstyear.sale_type = 'c'
   AND t_w_firstyear.sale_type = 'w'
   AND t_s_secyear.sale_type = 's'
   AND t_c_secyear.sale_type = 'c'
   AND t_w_secyear.sale_type = 'w'
   AND t_s_firstyear.dyear+0 =  1999
   AND t_s_secyear.dyear+0 = 1999+1
   AND t_c_firstyear.dyear+0 =  1999
   AND t_c_secyear.dyear+0 =  1999+1
   AND t_w_firstyear.dyear+0 = 1999
   AND t_w_secyear.dyear+0 = 1999+1
   AND t_s_firstyear.year_total > 0
   AND t_c_firstyear.year_total > 0
   AND t_w_firstyear.year_total > 0
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_s_firstyear.year_total > 0 THEN t_s_secyear.year_total / t_s_firstyear.year_total ELSE NULL END
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_w_firstyear.year_total > 0 THEN t_w_secyear.year_total / t_w_firstyear.year_total ELSE NULL END
ORDER BY t_s_secyear.customer_id
         ,t_s_secyear.customer_first_name
         ,t_s_secyear.customer_last_name
         ,t_s_secyear.customer_birth_country
OPTION ( LABEL = 'Query04-af359846-253-3');
```

Sprawdź plan szacowanego wykonania kwerendy.  Istnieje 18 przetasowań i 17 łączy operacji, które zajmują więcej czasu, aby wykonać. Teraz utwórzmy jeden zmaterializowany widok dla każdej z trzech instrukcji sub-SELECT.

```sql
CREATE materialized view nbViewSS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.store_sales
     ,dbo.date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
GO
CREATE materialized view nbViewCS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
          , count_big(*) as cb
FROM dbo.customer
     ,dbo.catalog_sales
     ,dbo.date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

GO
CREATE materialized view nbViewWS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.web_sales
     ,dbo.date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

```

Sprawdź plan wykonania oryginalnej kwerendy ponownie.  Teraz liczba łączy zmienia się z 17 na 5 i nie ma już shuffle.  Kliknij ikonę Operacji filtrowania w planie. Jego lista danych wyjściowych pokazuje dane są odczytywane z zmaterializowanych widoków zamiast tabel bazowych.  

 ![Plan_Output_List_with_Materialized_Views](./media/develop-materialized-view-performance-tuning/output-list.png)

Z zmaterializowanych widoków, to samo zapytanie działa znacznie szybciej bez zmiany kodu.  

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej wskazówek dotyczących programowania, zobacz [Omówienie rozwoju języka SQL Synapse](develop-overview.md).
 