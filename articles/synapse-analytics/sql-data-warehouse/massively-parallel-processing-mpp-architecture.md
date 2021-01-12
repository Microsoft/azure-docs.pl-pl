---
title: Architektura dedykowanej puli SQL (dawniej SQL DW)
description: Dowiedz się, jak dedykowana Pula SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics łączy rozproszone możliwości przetwarzania zapytań z usługą Azure Storage w celu uzyskania wysokiej wydajności i skalowalności.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: c537d3109f770c1ce77be6495bdacd24270dad5f
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98119598"
---
# <a name="dedicated-sql-pool-formerly-sql-dw-architecture-in-azure-synapse-analytics"></a>Architektura dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics

Usługa Azure Synapse Analytics to usługa analizy, która łączy magazynowanie danych przedsiębiorstwa z analizą danych big data. Zapewnia to swobodę wykonywania zapytań dotyczących danych na Twoich warunkach.

> [!NOTE]
>Zapoznaj się z [dokumentacją usługi Azure Synapse Analytics](../overview-what-is.md).
>


> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-architecture-components"></a>Składniki architektury SQL Synapse

[Dedykowana Pula SQL (dawniej SQL DW)](sql-data-warehouse-overview-what-is.md) wykorzystuje architekturę skalowalną w poziomie do dystrybucji obliczeniowego przetwarzania danych w wielu węzłach. Jednostka skali jest abstrakcją mocy obliczeniowej, która jest znana jako [Jednostka magazynu danych](what-is-a-data-warehouse-unit-dwu-cdwu.md). Obliczenia są niezależne od magazynu, co umożliwia skalowanie obliczeniowe niezależnie od danych w systemie.

![Architektura dedykowanej puli SQL (dawniej SQL DW)](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

Dedykowana Pula SQL (wcześniej SQL DW) korzysta z architektury opartej na węźle. Aplikacje nawiązują połączenie i wydają polecenia T-SQL do węzła kontrolki. Węzeł kontrolny obsługuje aparat zapytań rozproszonych, który optymalizuje zapytania do przetwarzania równoległego, a następnie przekazuje operacje do węzłów obliczeniowych w celu wykonywania równoległych zadań.

Węzły obliczeniowe przechowują wszystkie dane użytkowników w usłudze Azure Storage i wykonują zapytania równoległe. Usługa przenoszenia danych (ang. Data Movement Service, DMS) to wewnętrzna usługa działająca na poziomie systemu, która przenosi dane pomiędzy węzłami w sposób wymagany do równoległego wykonywania zapytań i zwracania prawidłowych wyników.

Z rozdzielonym magazynem i funkcją obliczeniową w przypadku korzystania z dedykowanej puli SQL (dawniej SQL DW) jeden z nich może:

- Niezależnie od potrzeb związanych z magazynem bez względu na wielkość mocy obliczeniowej.
- Zwiększ lub Zmniejsz moc obliczeniową w ramach dedykowanej puli SQL (dawniej SQL DW) bez przeniesienia danych.
- Wstrzymywać zasoby obliczeniowe bez wpływu na dane, płacąc tylko za przestrzeń dyskową.
- Wznawiać zasoby obliczeniowe w godzinach pracy.

### <a name="azure-storage"></a>Azure Storage

Dedykowana Pula SQL (dawniej SQL DW) korzysta z usługi Azure Storage, aby zapewnić bezpieczeństwo danych użytkownika.  Ponieważ dane są przechowywane i zarządzane przez usługę Azure Storage, istnieje oddzielna opłata za użycie magazynu. Dane są podzielonej na fragmenty do **dystrybucji** w celu zoptymalizowania wydajności systemu. Podczas definiowania tabeli możesz wybrać wzorzec dzielenia na fragmenty używany do dystrybucji danych. Te wzorce fragmentowania są obsługiwane:

- Skrót
- Działanie okrężne
- Replikowanie

### <a name="control-node"></a>Węzeł kontrolny

Węzeł kontrolny to mózg całej architektury. Jest to fronton współdziałający ze wszystkimi aplikacjami i połączeniami. Aparat zapytań rozproszonych działa w węźle kontroli w celu optymalizowania i koordynowania zapytań równoległych. Po przesłaniu zapytania T-SQL węzeł kontrolny przekształca go w zapytania, które są wykonywane równolegle do każdej dystrybucji.

### <a name="compute-nodes"></a>Węzły obliczeniowe

Węzły obliczeniowe zapewniają moc obliczeniową. Dystrybucje są mapowane na węzły obliczeniowe do przetwarzania. Ponieważ płacisz za więcej zasobów obliczeniowych, dystrybucje są ponownie mapowane na dostępne węzły obliczeniowe. Liczba węzłów obliczeniowych z zakresu od 1 do 60 i jest określana na podstawie poziomu usługi dla Synapse SQL.

Każdy węzeł obliczeniowy ma identyfikator węzła, który jest widoczny w widokach systemu. IDENTYFIKATOR węzła obliczeniowego można zobaczyć, szukając kolumny node_id w widokach systemowych, których nazwy rozpoczynają się od sys.pdw_nodes. Listę tych widoków systemowych można znaleźć w temacie [Synapse SQL system viewss](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="data-movement-service"></a>Usługa przenoszenia danych

Usługa przenoszenia danych (DMS) to technologia transportu danych, która koordynuje przenoszenie danych między węzłami obliczeniowymi. Niektóre zapytania wymagają przeniesienia danych, aby upewnić się, że zapytania równoległe zwracają dokładne wyniki. Gdy przenoszenie danych jest wymagane, usługa DMS gwarantuje, że odpowiednie dane będą pobierane do odpowiedniej lokalizacji.

## <a name="distributions"></a>Dystrybucji

Dystrybucja to podstawowa jednostka magazynowania i przetwarzania zapytań równoległych wykonywanych na danych rozproszonych. Gdy Synapse SQL uruchamia kwerendę, pracy jest podzielony na 60 mniejszych zapytań, które są uruchamiane równolegle.

Każda z 60 mniejszych zapytań jest uruchamiana na jednym z dystrybucji danych. Każdy węzeł obliczeniowy zarządza jedną lub większą liczbą dystrybucji 60. Dedykowana Pula SQL (wcześniej SQL DW) z maksymalną ilość zasobów obliczeniowych ma jedną dystrybucję na każdy węzeł obliczeniowy. Dedykowana Pula SQL (wcześniej SQL DW) o minimalnych zasobach obliczeniowych ma wszystkie dystrybucje w jednym węźle obliczeniowym.  

## <a name="hash-distributed-tables"></a>Tabele dystrybuowane przy użyciu skrótu

Tabela dystrybuowana przy użyciu skrótu może zapewniać najwyższą wydajność zapytań w przypadku sprzężeń i agregacji w dużych tabelach.

Aby fragmentu dane do tabeli rozproszonej przez funkcję mieszania, funkcja skrótu jest używana do niejednoznacznego przypisywania każdego wiersza do jednej dystrybucji. W definicji tabeli jedna kolumna zostaje wyznaczona jako kolumna dystrybucji. Funkcja skrótu używa wartości z kolumny dystrybucji, aby przypisać każdy wiersz do określonej dystrybucji.

Na poniższym diagramie przedstawiono sposób przechowywania pełnej (nierozproszonej tabeli) jako tabeli rozproszonej przez funkcję tworzenia skrótów.

![Tabela rozproszona](./media/massively-parallel-processing-mpp-architecture/hash-distributed-table.png "Tabela rozproszona")  

- Każdy wiersz należy do jednej dystrybucji.  
- Algorytm wyznaczania wartości skrótu przypisuje każdy wiersz do jednej dystrybucji.  
- Liczba wierszy tabeli na dystrybucję jest różna, jak pokazano w różnych rozmiarach tabel.

Istnieją zagadnienia dotyczące wydajności dla wyboru kolumny dystrybucji, takie jak odrębność, pochylenie danych i typy zapytań, które są uruchamiane w systemie.

## <a name="round-robin-distributed-tables"></a>Tabele dystrybuowane przy użyciu działania okrężnego

Tabela okrężna jest najprostszą tabelą umożliwiającą tworzenie i dostarczanie szybkiej wydajności, gdy jest używana jako tabela przejściowa dla obciążeń.

W tabeli dystrybuowanej przy użyciu działania okrężnego dane są dystrybuowane równomiernie w całej tabeli, bez dodatkowej optymalizacji. Najpierw wybierana jest dystrybucja, a następnie bufory wierszy są przypisywane sekwencyjnie. Ładowanie danych do tabeli z działaniem okrężnym jest szybkie, ale wydajność zapytań jest często lepsza w tabelach dystrybuowanych przy użyciu skrótu. Sprzężenia w tabelach działania okrężnego wymagają danych reshuffling, które pobierają dodatkowy czas.

## <a name="replicated-tables"></a>Tabele replikowane

Tabela replikowana zapewnia najszybsze wykonywanie zapytań w przypadku niewielkich tabel.

Replikowana pamięć podręczna zawiera pełną kopię tabeli w każdym węźle obliczeniowym. Dlatego replikowanie tabeli eliminuje konieczność przesyłania danych między węzłami obliczeniowymi przed operacją sprzężenia lub agregacji. Replikacja sprawdza się najlepiej w przypadku małych tabel. Dodatkowy magazyn jest wymagany i występuje dodatkowe obciążenie związane z zapisywaniem danych, co sprawia, że duże tabele nie są praktyczne.  

Na poniższym diagramie przedstawiono zreplikowane tabele, które są buforowane przy pierwszej dystrybucji w każdym węźle obliczeniowym.  

![Tabela replikowana](./media/massively-parallel-processing-mpp-architecture/replicated-table.png "Tabela replikowana")

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już usługę Azure Synapse, Dowiedz się, jak szybko [utworzyć dedykowaną pulę SQL (dawniej SQL DW)](create-data-warehouse-portal.md) i [załadować przykładowe dane](./load-data-from-azure-blob-storage-using-copy.md). Jeśli dopiero zaczynasz korzystać z platformy Azure, [słownik platformy Azure](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) może pomóc Ci zaznajomić się z nową terminologią. Lub zapoznaj się z innymi zasobami usługi Azure Synapse.  

- [Historie sukcesu klientów](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Blogi](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Żądania funkcji](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Filmy wideo](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Tworzenie biletu pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md)
- [Strona pytania&pytań i odpowiedzi](/answers/topics/azure-synapse-analytics.html)
- [Forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)