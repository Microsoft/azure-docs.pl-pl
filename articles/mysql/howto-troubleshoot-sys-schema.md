---
title: Użyj sys_schema-Azure Database for MySQL
description: Dowiedz się, jak używać sys_schema do znajdowania problemów z wydajnością i konserwowania bazy danych w Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/30/2020
ms.openlocfilehash: a20510ee2800a54f9a51a2f498ee8ae8a3e51d55
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94543153"
---
# <a name="how-to-use-sys_schema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Jak używać sys_schema na potrzeby dostrajania wydajności i konserwacji bazy danych w programie Azure Database for MySQL

Baza danych MySQL performance_schema, najpierw dostępna w programie MySQL 5,5, oferuje instrumentację dla wielu najważniejszych zasobów serwera, takich jak alokacja pamięci, programy przechowywane, blokowanie metadanych itp. Jednak performance_schema zawiera więcej niż 80 tabel i uzyskanie niezbędnych informacji często wymaga sprzęgania tabel w performance_schema, a także tabel z information_schema. Kompilowanie na performance_schema i information_schema sys_schema zapewnia zaawansowaną kolekcję [widoków przyjaznych dla użytkownika](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) w bazie danych tylko do odczytu i jest w pełni włączona w Azure Database for MySQL wersji 5,7.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/sys-schema-views.png" alt-text="widoki sys_schema":::

W sys_schema istnieją widoki 52, a każdy widok ma jedną z następujących prefiksów:

- Host_summary lub we/wy: powiązane opóźnienia we/wy.
- InnoDB: InnoDB buforu stanu i blokady.
- Pamięć: użycie pamięci przez hosta i użytkowników.
- Schemat: informacje dotyczące schematu, takie jak autoprzyrost, indeksy itd.
- Instrukcja: informacje na temat instrukcji SQL; może to być instrukcja, która spowodowała pełne skanowanie tabeli lub długi czas wykonywania zapytań.
- Użytkownik: zasoby używane i pogrupowane według użytkowników. Przykłady to plik I/OS, połączenia i pamięć.
- Czekaj: zdarzenia oczekiwania pogrupowane według hosta lub użytkownika.

Teraz przyjrzyjmy się typowym wzorcem użycia sys_schema. Aby zacząć od, będziemy grupować wzorce użycia do dwóch kategorii: **dostrajania wydajności** i **konserwacji bazy danych**.

## <a name="performance-tuning"></a>Dostosowywanie wydajności

### <a name="sysuser_summary_by_file_io"></a>*sys.user_summary_by_file_io*

We/wy jest najtańszą operacją w bazie danych. Średni czas oczekiwania operacji we/wy można sprawdzić, badając widok *sys.user_summary_by_file_io* . Przy domyślnym 125 GB magazynu z zainicjowaną obsługą operacji we/wy trwa około 15 sekund.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/io-latency-125GB.png" alt-text="opóźnienie we/wy: 125 GB":::

Ponieważ Azure Database for MySQL skaluje we/wy w odniesieniu do magazynu, po zwiększeniu miejsca do magazynowania na 1 TB, opóźnienie operacji we/wy zmniejsza się do 571 MS.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/io-latency-1TB.png" alt-text="opóźnienie we/wy: 1 TB":::

### <a name="sysschema_tables_with_full_table_scans"></a>*sys.schema_tables_with_full_table_scans*

Pomimo starannego planowania wiele zapytań nadal może prowadzić do pełnego skanowania tabel. Aby uzyskać dodatkowe informacje o typach indeksów i sposobach ich optymalizacji, można zapoznać się z tym artykułem: [Jak rozwiązywać problemy z wydajnością zapytań](./howto-troubleshoot-query-performance.md). Pełne skanowanie tabeli polega na wielu zasobach i obniżeniu wydajności bazy danych. Najszybszym sposobem znajdowania tabel za pomocą pełnego skanowania tabeli jest zapytanie w widoku *sys.schema_tables_with_full_table_scans* .

:::image type="content" source="./media/howto-troubleshoot-sys-schema/full-table-scans.png" alt-text="pełne skanowanie tabeli":::

### <a name="sysuser_summary_by_statement_type"></a>*sys.user_summary_by_statement_type*

Aby rozwiązać problemy z wydajnością bazy danych, może być korzystne zidentyfikowanie zdarzeń występujących w bazie danych i użycie widoku *sys.user_summary_by_statement_type* może wystarczyć do wzięcia końca.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/summary-by-statement.png" alt-text="Podsumowanie według instrukcji":::

W tym przykładzie Azure Database for MySQL poświęcają 53 minut na opróżnianie 44579 dziennika zapytań slog. To długi czas i wiele systemów IOs. To działanie można ograniczyć, wyłączając dziennik wolnych zapytań lub zmniejszając częstotliwość powolnych logowań do Azure Portal.

## <a name="database-maintenance"></a>Konserwacja bazy danych

### <a name="sysinnodb_buffer_stats_by_table"></a>*sys.innodb_buffer_stats_by_table*

[!IMPORTANT]
> Wykonanie zapytania dotyczącego tego widoku może wpłynąć na wydajność. Zaleca się przeprowadzenie tego rozwiązywania problemów w godzinach pracy poza szczytem.

Pula buforów InnoDB znajduje się w pamięci i jest głównym mechanizmem pamięci podręcznej między systemem DBMS i magazynem. Rozmiar puli buforów InnoDB jest powiązany z warstwą wydajności i nie można jej zmienić, jeśli nie wybrano innej jednostki SKU produktu. Podobnie jak w przypadku pamięci w systemie operacyjnym, stare strony są wymieniane w celu zapewnienia pokoju na potrzeby świeżych danych. Aby dowiedzieć się, które tabele zużywają większość pamięci puli buforów InnoDB, można zbadać widok *sys.innodb_buffer_stats_by_table* .

:::image type="content" source="./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png" alt-text="Stan buforu InnoDB":::

Na grafice powyżej widać, że inne niż tabele i widoki systemowe, każda tabela w bazie danych mysqldatabase033, która hostuje jedną z witryn WordPress, zajmuje 16 KB lub 1 stronę danych w pamięci.

### <a name="sysschema_unused_indexes--sysschema_redundant_indexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Indeksy to doskonałe narzędzia pozwalające zwiększyć wydajność odczytu, ale wiążą się z dodatkowymi kosztami operacji wstawiania i przechowywania. *Sys.schema_unused_indexes* i *sys.schema_redundant_indexes* zapewniają wgląd w nieużywane lub zduplikowane indeksy.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/unused-indexes.png" alt-text="nieużywane indeksy":::

:::image type="content" source="./media/howto-troubleshoot-sys-schema/redundant-indexes.png" alt-text="nadmiarowe indeksy":::

## <a name="conclusion"></a>Podsumowanie

Podsumowując, sys_schema jest doskonałym narzędziem do dostrajania wydajności i konserwacji bazy danych. Upewnij się, że korzystasz z tej funkcji w Azure Database for MySQL. 

## <a name="next-steps"></a>Następne kroki
- Aby znaleźć odpowiedzi na najczęściej zadawane pytania lub opublikować nowe pytanie/odpowiedź, odwiedź stronę pytania lub [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql)w [witrynie Microsoft Q&](/answers/topics/azure-database-mysql.html) .