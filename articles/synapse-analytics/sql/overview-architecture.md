---
title: Architektura usługi
description: Dowiedz się, jak usługa Azure Synapse SQL łączy masowe przetwarzanie równoległe (MPP) z usługą Azure Storage w celu uzyskania wysokiej wydajności i skalowalności.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 9f2f3eee12bb8741f6d079f6f081a08f4e2db9b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87046869"
---
# <a name="azure-synapse-sql-architecture"></a>Architektura SQL usługi Azure Synapse 

W tym artykule opisano składniki architektury programu SQL Synapse.

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="synapse-sql-architecture-components"></a>Składniki architektury SQL Synapse

Synapse SQL wykorzystuje architekturę skalowania w poziomie do dystrybucji obliczeniowego przetwarzania danych w wielu węzłach. Obliczenia są niezależne od magazynu, co umożliwia skalowanie obliczeniowe niezależnie od danych w systemie. 

W przypadku puli SQL jednostka skali jest abstrakcją mocy obliczeniowej, która jest znana jako [Jednostka magazynu danych](resource-consumption-models.md). 

W przypadku serwera SQL na żądanie, skalowanie jest wykonywane automatycznie, aby uwzględnić wymagania dotyczące zasobów zapytań. Ponieważ topologia zmienia się wraz z upływem czasu poprzez dodanie, usunięcie węzłów lub przełączenia w tryb failover, dostosowuje się do zmian i sprawdza, czy zapytanie ma wystarczającą ilość zasobów i pomyślnie kończy pracę. Na przykład na poniższym obrazie przedstawiono instrukcję SQL na żądanie wykorzystującą 4 węzły obliczeniowe do wykonania zapytania.

![Architektura usługi](./media//overview-architecture/sql-architecture.png)

Synapse SQL używa architektury opartej na węźle. Aplikacje nawiązują połączenie i wydają polecenia T-SQL do węzła kontrolki, który jest pojedynczym punktem wejścia dla Synapse SQL. 

Węzeł kontroli puli SQL wykorzystuje aparat MPP do optymalizowania zapytań do przetwarzania równoległego, a następnie przekazuje operacje do węzłów obliczeniowych w celu wykonywania równoległych zadań. 

Węzeł kontroli na żądanie SQL korzysta z aparatu przetwarzania zapytań rozproszonych (DQP) w celu optymalizacji i organizowania rozproszonego wykonywania zapytań użytkownika przez dzielenie go na mniejsze zapytania, które będą wykonywane w węzłach obliczeniowych. Każde małe zapytanie jest nazywane zadaniem i reprezentuje jednostkę wykonawczą. Odczytuje pliki z magazynu, łączy wyniki innych zadań, grup lub zamówień z danymi pobranymi z innych zadań. 

Węzły obliczeniowe przechowują wszystkie dane użytkowników w usłudze Azure Storage i wykonują zapytania równoległe. Usługa przenoszenia danych (ang. Data Movement Service, DMS) to wewnętrzna usługa działająca na poziomie systemu, która przenosi dane pomiędzy węzłami w sposób wymagany do równoległego wykonywania zapytań i zwracania prawidłowych wyników. 

Dzięki rozdzieleniu magazynu i obliczeń użycie usługi Synapse SQL one może mieć możliwość skorzystania z niezależnego określania wielkości mocy obliczeniowej niezależnie od potrzeb związanych z magazynem. Skalowanie na żądanie SQL jest wykonywane automatycznie, podczas gdy dla puli SQL jedna może:

* Zwiększ lub Zmniejsz moc obliczeniową w puli SQL (hurtowni danych) bez przeniesienia danych.
* Wstrzymywać zasoby obliczeniowe bez wpływu na dane, płacąc tylko za przestrzeń dyskową.
* Wznawiać zasoby obliczeniowe w godzinach pracy.

## <a name="azure-storage"></a>Azure Storage

Synapse SQL wykorzystuje usługę Azure Storage, aby zapewnić bezpieczeństwo danych użytkownika. Ponieważ dane są przechowywane i zarządzane przez usługę Azure Storage, istnieje oddzielna opłata za użycie magazynu. 

Usługa SQL na żądanie umożliwia wykonywanie zapytań dotyczących plików w usłudze Data Lake w trybie tylko do odczytu, natomiast Pula SQL umożliwia również pozyskiwanie danych. Gdy dane są pozyskiwane w puli SQL, dane są podzielonej na fragmenty do **dystrybucji** w celu zoptymalizowania wydajności systemu. Można wybrać, który wzorzec fragmentowania ma być używany do dystrybucji danych podczas definiowania tabeli. Te wzorce fragmentowania są obsługiwane:

* Skrót
* Działanie okrężne
* Replikowanie

## <a name="control-node"></a>Węzeł kontrolny

Węzeł kontrolny to mózg całej architektury. Jest to fronton współdziałający ze wszystkimi aplikacjami i połączeniami. 

W puli SQL aparat MPP jest uruchamiany w węźle kontroli w celu optymalizacji i koordynowania zapytań równoległych. Po przesłaniu zapytania T-SQL do puli SQL, węzeł kontrolny przekształca go w zapytania, które są wykonywane równolegle do każdej dystrybucji.

W przypadku programu SQL na żądanie aparat DQP jest uruchamiany w węźle kontroli w celu optymalizacji i koordynowania rozproszonego wykonania zapytania użytkownika przez podzielenie go na mniejsze zapytania, które będą wykonywane w węzłach obliczeniowych. Przypisuje również zestawy plików do przetworzenia przez każdy węzeł.

## <a name="compute-nodes"></a>Węzły obliczeniowe

Węzły obliczeniowe zapewniają moc obliczeniową. 

W puli SQL dystrybucja jest mapowana na węzły obliczeniowe do przetworzenia. W miarę płacenia za więcej zasobów obliczeniowych Pula ponownie mapuje dystrybucje do dostępnych węzłów obliczeniowych. Liczba węzłów obliczeniowych z zakresu od 1 do 60 i jest określana na podstawie poziomu usługi dla puli SQL. Każdy węzeł obliczeniowy ma identyfikator węzła, który jest widoczny w widokach systemu. IDENTYFIKATOR węzła obliczeniowego można zobaczyć, szukając kolumny node_id w widokach systemowych, których nazwy rozpoczynają się od sys.pdw_nodes. Aby zapoznać się z listą tych widoków systemowych, zobacz [widoki systemowe MPP](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest).

W przypadku programu SQL na żądanie każdy węzeł obliczeniowy jest przypisywany do zadania i zestawu plików, na których ma zostać wykonane zadanie. Zadanie jest jednostką wykonawczą zapytania rozproszonego, która jest w rzeczywistości częścią kwerendy przesłanej przez użytkownika. Automatyczne skalowanie jest stosowane w celu zapewnienia wystarczającej liczby węzłów obliczeniowych do wykonywania zapytań użytkownika.

## <a name="data-movement-service"></a>Usługa przenoszenia danych

Usługa przenoszenia danych (DMS) to technologia transportu danych w puli SQL, która koordynuje przenoszenie danych między węzłami obliczeniowymi. Niektóre zapytania wymagają przeniesienia danych, aby upewnić się, że zapytania równoległe zwracają dokładne wyniki. Gdy przenoszenie danych jest wymagane, usługa DMS gwarantuje, że odpowiednie dane będą pobierane do odpowiedniej lokalizacji.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Dystrybucji

Dystrybucja to podstawowa jednostka magazynu i przetwarzania dla zapytań równoległych, które są uruchamiane na danych rozproszonych w puli SQL. Gdy w puli SQL działa zapytanie, prace są podzielone na 60 mniejsze zapytania, które są uruchamiane równolegle. 

Każda z 60 mniejszych zapytań jest uruchamiana na jednym z dystrybucji danych. Każdy węzeł obliczeniowy zarządza jedną lub większą liczbą dystrybucji 60. Pula SQL z maksymalną ilość zasobów obliczeniowych ma jedną dystrybucję na każdy węzeł obliczeniowy. Pula SQL o minimalnych zasobach obliczeniowych ma wszystkie dystrybucje w jednym węźle obliczeniowym. 

## <a name="hash-distributed-tables"></a>Tabele dystrybuowane przy użyciu skrótu
Tabela dystrybuowana przy użyciu skrótu może zapewniać najwyższą wydajność zapytań w przypadku sprzężeń i agregacji w dużych tabelach. 

Aby fragmentu dane do tabeli rozproszonej przez funkcję mieszania, w puli SQL jest stosowana funkcja skrótu, która umożliwia jednoznaczne przypisanie każdego wiersza do jednej dystrybucji. W definicji tabeli jedna kolumna zostaje wyznaczona jako kolumna dystrybucji. Funkcja skrótu używa wartości z kolumny dystrybucji, aby przypisać każdy wiersz do określonej dystrybucji.

Na poniższym diagramie przedstawiono sposób przechowywania pełnej (nierozproszonej tabeli) jako tabeli rozproszonej przez funkcję tworzenia skrótów. 

![Tabela rozproszona](media//overview-architecture/hash-distributed-table.png "Tabela rozproszona") 

* Każdy wiersz należy do jednej dystrybucji. 
* Algorytm wyznaczania wartości skrótu przypisuje każdy wiersz do jednej dystrybucji. 
* Liczba wierszy tabeli na dystrybucję jest różna, jak pokazano w różnych rozmiarach tabel.

Istnieją zagadnienia dotyczące wydajności dla wyboru kolumny dystrybucji, takie jak odrębność, pochylenie danych i typy zapytań, które są uruchamiane w systemie.

## <a name="round-robin-distributed-tables"></a>Tabele dystrybuowane przy użyciu działania okrężnego

Tabela okrężna jest najprostszą tabelą umożliwiającą tworzenie i dostarczanie szybkiej wydajności, gdy jest używana jako tabela przejściowa dla obciążeń.

W tabeli dystrybuowanej przy użyciu działania okrężnego dane są dystrybuowane równomiernie w całej tabeli, bez dodatkowej optymalizacji. Najpierw wybierana jest dystrybucja, a następnie bufory wierszy są przypisywane sekwencyjnie. Ładowanie danych do tabeli z działaniem okrężnym jest szybkie, ale wydajność zapytań jest często lepsza w tabelach dystrybuowanych przy użyciu skrótu. Sprzężenia w tabelach działania okrężnego wymagają danych reshuffling, które pobierają dodatkowy czas.

## <a name="replicated-tables"></a>Zreplikowane tabele
Tabela replikowana zapewnia najszybsze wykonywanie zapytań w przypadku niewielkich tabel.

Replikowana pamięć podręczna zawiera pełną kopię tabeli w każdym węźle obliczeniowym. Dlatego replikowanie tabeli eliminuje konieczność przesyłania danych między węzłami obliczeniowymi przed operacją sprzężenia lub agregacji. Replikacja sprawdza się najlepiej w przypadku małych tabel. Dodatkowy magazyn jest wymagany i występuje dodatkowe obciążenie związane z zapisywaniem danych, co sprawia, że duże tabele nie są praktyczne. 

Na poniższym diagramie przedstawiono zreplikowane tabele, które są buforowane przy pierwszej dystrybucji w każdym węźle obliczeniowym. 

![Tabela replikowana](media/overview-architecture/replicated-table.png "Tabela replikowana") 

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już wersję Synapse SQL, Dowiedz się, jak szybko [utworzyć pulę SQL](../quickstart-create-sql-pool-portal.md) i [załadować dane przykładowe](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) (./SQL-Data-Warehouse-Load-Sample-Databases.MD). Lub zacznij [korzystać z programu SQL na żądanie](../quickstart-sql-on-demand.md). Jeśli dopiero zaczynasz korzystać z platformy Azure, [słownik platformy Azure](../../azure-glossary-cloud-terminology.md) może pomóc Ci zaznajomić się z nową terminologią. 
