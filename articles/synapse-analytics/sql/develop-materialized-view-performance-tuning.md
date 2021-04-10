---
title: Strojenie wydajności za pomocą zmaterializowanych widoków
description: Zalecenia i zagadnienia dotyczące widoków przeznaczonych do poprawienia wydajności zapytań.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: e6c3987e2de7f9592a1f7f6086657592e1bf0c16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676597"
---
# <a name="performance-tuning-with-materialized-views-using-dedicated-sql-pool-in-azure-synapse-analytics"></a>Dostrajanie wydajności w przypadku widoków z materiałami przy użyciu dedykowanej puli SQL w usłudze Azure Synapse Analytics

W dedykowanej puli SQL widoki z materiałami zapewniają niską metodę konserwacji dla złożonych zapytań analitycznych w celu uzyskania szybkiej wydajności bez żadnej zmiany w zapytaniu. W tym artykule omówiono ogólne wskazówki dotyczące korzystania z widoków z materiałami.

## <a name="materialized-views-vs-standard-views"></a>Widoki z materiałami i widoki standardowe

Pula SQL obsługuje widoki standardowe i materiałowe.  Obie są tabelami wirtualnymi utworzonymi za pomocą SELECT Expressions i prezentowanych jako tabele logiczne.  Widoki przedstawiają złożoność wspólnych obliczeń danych i dodają warstwę abstrakcji do obliczeń zmian, aby nie trzeba było ponownie pisać zapytań.  

Widok standardowy oblicza swoje dane za każdym razem, gdy widok jest używany.  Brak danych przechowywanych na dysku. Użytkownicy zazwyczaj używają widoków standardowych jako narzędzia, które ułatwiają organizowanie obiektów logicznych i zapytań w bazie danych.  Aby użyć widoku standardowego, zapytanie musi nawiązać bezpośrednie odwołanie do niego.

Wyspecjalizowany widok wstępnie oblicza, przechowuje i utrzymuje dane w dedykowanej puli SQL, podobnie jak w przypadku tabeli.  Obliczenia nie są wymagane za każdym razem, gdy jest używany widok z materiałami.  Dlatego, że zapytania wykorzystujące wszystkie lub podzbiór danych w widokach w postaci większej wydajności mogą zwiększyć wydajność.  Jeszcze lepsze zapytania mogą korzystać z widoku z materiałami bez bezpośredniego odniesienia do niego, dlatego nie trzeba zmieniać kodu aplikacji.  

Większość standardowych wymagań widoku nadal ma zastosowanie do widoku z materiałami. Aby uzyskać szczegółowe informacje na temat składniowe widoku i innych wymagań, zapoznaj się z tematem [Tworzenie przykładowego widoku jako wyboru](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true).

| Porównanie                     | Widok                                         | Zmaterializowany widok
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|Definicja widoku                 | Przechowywane w usłudze Azure Data Warehouse.              | Przechowywane w usłudze Azure Data Warehouse.
|Wyświetlanie zawartości                    | Generowane za każdym razem, gdy widok jest używany.   | Wstępnie przetworzone i przechowywane w usłudze Azure Data Warehouse podczas tworzenia widoku. Zaktualizowano w miarę dodawania danych do tabel bazowych.
|Odświeżanie danych                    | Zawsze aktualizowane                               | Zawsze aktualizowane
|Szybkość pobierania danych widoku z złożonych zapytań     | Mała                                         | Duża  
|Dodatkowy magazyn                   | Nie                                           | Tak
|Składnia                          | CREATE VIEW                                  | UTWÓRZ WIDOK Z MATERIAŁAMI JAKO WYBRANY

## <a name="benefits-of-materialized-views"></a>Zalety widoków z materiałami

Właściwie zaprojektowany widok z materiałami zapewnia następujące korzyści:

- Krótszy czas wykonywania złożonych zapytań z sprzężeniami i funkcjami agregującymi. Im bardziej skomplikowane jest zapytanie, tym większy potencjał do zapisu w czasie wykonywania. Jest to najbardziej korzystne, gdy koszt obliczeń zapytania jest wysoki, a wynikający z nich zestaw danych jest mały.  

- Optymalizator zapytań w dedykowanej puli SQL może automatycznie używać wdrożonych widoków w celu usprawnienia planów wykonywania zapytań.  Ten proces jest niewidoczny dla użytkowników, którzy zapewniają szybszą wydajność zapytań i nie wymagają zapytań, aby skierować bezpośrednie odwołanie do widoków z materiałami.

- Wymaga niskiej konserwacji w widokach.  Widok z materiałami przechowuje dane w dwóch miejscach, klastrowany indeks magazynu kolumn dla danych początkowych w czasie tworzenia widoku oraz magazyn zmian danych przyrostowych.  Wszystkie zmiany danych z tabel podstawowych są automatycznie dodawane do magazynu różnicowego w sposób synchroniczny.  Proces w tle (w ramach krotki) okresowo przenosi dane z magazynu różnicowego do indeksu magazynu kolumn widoku.  Ten projekt umożliwia wykonywanie zapytań dotyczących danych w postaci materiałów, które zwracają te same dane, co bezpośrednio zapytania dotyczące tabel podstawowych.
- Dane w widoku z materiałami mogą być dystrybuowane inaczej od tabel podstawowych.  
- Dane w widokach z materiałami uzyskują takie same korzyści wysokiej dostępności i odporności jak dane w regularnych tabelach.  

W porównaniu z innymi dostawcami magazynu danych, wyspecjalizowane widoki zaimplementowane w dedykowanej puli SQL oferują również następujące dodatkowe korzyści:

- Automatyczne i synchroniczne odświeżanie danych ze zmianami danych w tabelach podstawowych. Nie jest wymagane wykonanie jakiejkolwiek czynności przez użytkownika.
- Szeroka Obsługa funkcji agregujących. Zobacz [Tworzenie widoku z materiałami jako Select (Transact-SQL)](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true).
- Wsparcie dla zalecenia dotyczącego widoku z materiałami specyficznymi dla zapytań.  Zobacz [wyjaśnienie (Transact-SQL)](/sql/t-sql/queries/explain-transact-sql?view=azure-sqldw-latest&preserve-view=true).

## <a name="common-scenarios"></a>Typowe scenariusze  

Widoki z materiałami są zwykle używane w następujących scenariuszach:

**Trzeba poprawić wydajność złożonych zapytań analitycznych na dużą ilość danych**

Złożone zapytania analityczne zwykle używają większej liczby funkcji agregacji i sprzężeń tabel, co sprawia, że wykonywanie większej liczby operacji obliczeniowych, takich jak losowe i sprzężenie w trakcie wykonywania zapytania.  To dlatego, że te zapytania trwają dłużej, szczególnie w przypadku dużych tabel.  

Użytkownicy mogą tworzyć widoki z danymi zwracanymi ze wspólnych obliczeń zapytań, więc nie jest wymagane ponowne obliczenie, gdy te dane są wymagane przez zapytania, co umożliwia obniżenie kosztów obliczeniowych i szybsze reagowanie na zapytania.

**Potrzeba szybszej wydajności bez ani minimalnej zmiany zapytania**

Zmiany schematu i zapytania w magazynach danych zwykle są ograniczone do obsługi zwykłych operacji ETL i raportowania.  Osoby mogą używać widoków z materiałami dla dostrajania wydajności zapytań, jeśli koszty związane z widokami mogą być przesunięte przez zysk w ramach wydajności zapytań.

W porównaniu z innymi opcjami dostrajania, takimi jak skalowanie i Zarządzanie statystykami, jest to znacznie mniej wpływające na tworzenie i konserwowanie widoku z materiałami oraz jego potencjalne zwiększenie wydajności.

- Tworzenie lub obsługa widoków z materiałami nie ma wpływu na zapytania działające względem tabel podstawowych.
- Optymalizator zapytań może automatycznie używać wdrożonych widoków, bez bezpośredniego odwołania do widoku w zapytaniu. Ta funkcja zmniejsza konieczność zmiany zapytania w dostrajania wydajności.

**Potrzebna inna strategia dystrybucji danych w celu przyspieszenia wykonywania zapytań**

Usługa Azure Data Warehouse to system dystrybuowany i wysoce Parallel Processing (MPP).  

Synapse SQL to rozproszony system zapytań, który umożliwia przedsiębiorstwom wdrażanie scenariuszy magazynowania danych i wirtualizacji danych przy użyciu standardowych środowisk T-SQL, które są znane dla inżynierów danych. Rozszerza również możliwości programu SQL na potrzeby obsługi przesyłania strumieniowego i scenariuszy uczenia maszynowego. Dane w tabeli magazynu danych są dystrybuowane między 60 węzłami przy użyciu jednej z trzech [strategii dystrybucji](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (mieszanie, round_robin lub zreplikowane).  

Dystrybucja danych jest określana w czasie tworzenia tabeli i pozostaje niezmieniona, dopóki tabela nie zostanie porzucona. Widok z materiałami jest tabelą wirtualną na dysku obsługującym dystrybucję danych i round_robin.  Użytkownicy mogą wybrać dystrybucję danych inną niż tabele podstawowe, ale optymalną dla wydajności zapytań, które często korzystają z widoków.  

## <a name="design-guidance"></a>Wskazówki dotyczące projektowania

Poniżej przedstawiono ogólne wskazówki dotyczące korzystania z widoków z materiałami w celu zwiększenia wydajności zapytań:

**Projektowanie dla obciążenia**

Przed rozpoczęciem tworzenia wyspecjalizowanych widoków należy mieć dogłębne zrozumienie obciążeń związanych ze wzorcami zapytań, ważnością, częstotliwością i rozmiarem danych uzyskanych.  

Użytkownicy mogą uruchamiać WITH_RECOMMENDATIONS WYJAŚNIj <SQL_statement> dla widoków z materiałami zalecanymi przez optymalizator zapytań.  Ponieważ te zalecenia są specyficzne dla zapytań, widok z materiałami, który korzysta z pojedynczego zapytania, może nie być optymalny dla innych zapytań w tym samym obciążeniu.  

Oceń te zalecenia z uwzględnieniem potrzeb związanych z obciążeniem.  Idealnymi widokami materiałowymi są te, które korzystają z wydajności obciążeń.  

**Weź pod uwagę kompromis między szybszymi zapytaniami i kosztem**

Dla każdego widoku z materiałami istnieje koszt magazynowania danych i koszt utrzymania widoku.  Wraz ze zmianami danych w tabelach podstawowych rozmiar widoku z materiałami wzrasta i jego struktura fizyczna również ulega zmianie.  

Aby uniknąć obniżenia wydajności zapytań, każdy widok z materiałami jest obsługiwany oddzielnie przez aparat magazynu danych, w tym przeniesienie wierszy z magazynu różnicowego do segmentów indeksu magazynu kolumn i konsolidowanie zmian danych.  

Obciążenie pracą konserwacyjną zwiększa się, gdy liczba widoków z materiałami i zmian tabeli bazowej rośnie.   Użytkownicy powinni sprawdzić, czy koszt ponoszony ze wszystkich widoków z materiałami może być przesunięty przez wzrost wydajności zapytania.  

To zapytanie można uruchomić, aby wyświetlić listę przykładowego widoku w bazie danych:

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

Opcje zmniejszania liczby widoków z materiałami:

- Identyfikuj typowe zestawy danych często używane przez złożone zapytania w obciążeniu.  Utwórz widoki z materiałami do przechowywania tych zestawów danych, aby Optymalizator mógł używać ich jako bloków konstrukcyjnych podczas tworzenia planów wykonywania.  

- Porzuć widoki z materiałami, które mają niskie użycie lub nie są już potrzebne.  Wyłączony widok materiałowy nie jest obsługiwany, ale nadal wiąże się z nim koszt magazynu.  

- Połącz widoki z materiałami utworzone w tej samej lub podobnej tabeli podstawowej nawet wtedy, gdy ich dane nie nakładają się na siebie.  Łączenie widoków z materiałami może skutkować większym widokiem w rozmiarze niż suma oddzielnych widoków, ale koszt konserwacji widoku powinien zostać zredukowany.  Na przykład:

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

Optymalizator magazynu danych może automatycznie używać wdrożonych widoków w celu zwiększenia wydajności zapytań.  Ta obsługa jest zastosowana w sposób przezroczysty w przypadku zapytań, które nie odwołują się do widoków i zapytań, które używają wartości zagregowanych nieobsługiwanych w przypadku tworzenia widoków w materiałach.  Nie jest wymagana żadna zmiana zapytania. Można sprawdzić szacowany plan wykonywania zapytania, aby potwierdzić, czy jest używany widok z materiałami.  

**Monitorowanie widoków z materiałami**

Widok z materiałami jest przechowywany w magazynie danych, podobnie jak tabela z klastrowanym indeksem magazynu kolumn (WIK).  Odczytywanie danych z widoku z materiałami obejmuje skanowanie indeksu i stosowanie zmian z magazynu różnicowego.  Gdy liczba wierszy w magazynie różnic jest zbyt duża, rozpoznawanie zapytania z widoku z materiałami może trwać dłużej niż bezpośrednio zapytania w tabelach bazowych.  

Aby uniknąć obniżenia wydajności zapytań, dobrym sposobem jest uruchomienie [polecenia DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest&preserve-view=true) w celu monitorowania overhead_ratio widoku (total_rows/base_view_row).  Jeśli overhead_ratio jest zbyt wysoka, rozważ odbudowanie widoku z materiałami, aby wszystkie wiersze w magazynie różnicowym były przenoszone do indeksu magazynu kolumn.  

**Widok materiałowy i buforowanie zestawu wyników**

Te dwie funkcje są wprowadzane w dedykowanej puli SQL w tym samym czasie na potrzeby dostrajania wydajności zapytań. Buforowanie zestawu wyników służy do osiągania dużej współbieżności i krótszych czasów odpowiedzi z powtarzalnych zapytań dotyczących danych statycznych.  

Aby można było użyć buforowanego wyniku, formularz żądania pamięci podręcznej musi pasować do zapytania, które spowodowało wytworzenie pamięci podręcznej.  Ponadto, buforowany wynik musi dotyczyć całego zapytania.  

Widoki z materiałami umożliwiają wprowadzanie zmian w tabelach podstawowych.  Dane w widokach z materiałami mogą być stosowane do fragmentu zapytania.  Obsługuje to te same widoki, które mogą być używane przez różne zapytania, które udostępniają pewne obliczenia w celu zwiększenia wydajności.

## <a name="example"></a>Przykład

W tym przykładzie używamy zapytania przypominającego TPCDS, które umożliwia znalezienie klientów, którzy spędzają więcej pieniędzy za pośrednictwem wykazu niż w sklepach. Identyfikuje również preferowanych klientów i ich kraj/region pochodzenia.   Zapytanie obejmuje wybranie pierwszych 100 rekordów z Unii trzech instrukcji SELECT sub obejmujących SUM () i GROUP BY.

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

Sprawdź szacowany plan wykonywania zapytania.  Istnieje 18 operacji wylosowania i 17 sprzężeń, co zajmuje więcej czasu. Teraz Utwórzmy jeden pojedynczy widok dla każdej z trzech instrukcji podrzędnych SELECT.

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

Sprawdź ponownie plan wykonania oryginalnego zapytania.  Teraz liczba sprzężeń zmienia się z 17 na 5 i nie ma już trybu losowego.  Wybierz ikonę operacji filtrowania w planie. Jego lista wyjściowa pokazuje, że dane są odczytywane z widoków z materiałami, a nie z tabel podstawowych.  

 ![Plan_Output_List_with_Materialized_Views](./media/develop-materialized-view-performance-tuning/output-list.png)

W przypadku widoków z materiałami to samo zapytanie działa znacznie szybciej bez zmiany kodu.  

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej porad programistycznych, zobacz [Synapse Development SQL — Omówienie](develop-overview.md).
 