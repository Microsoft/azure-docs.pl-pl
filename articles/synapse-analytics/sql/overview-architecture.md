---
title: Architektura usługi
description: Dowiedz się, Azure Synapse SQL łączy możliwości rozproszonego przetwarzania zapytań z usługą Azure Storage w celu osiągnięcia wysokiej wydajności i skalowalności.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: f342f39b62956cd85f269918e8e1ef1a2478a3d8
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566362"
---
# <a name="azure-synapse-sql-architecture"></a>Architektura usługi Azure Synapse SQL 

W tym artykule opisano składniki architektury Synapse SQL.

## <a name="synapse-sql-architecture-components"></a>Synapse SQL składników architektury

Synapse SQL wykorzystuje architekturę skalowania w celu dystrybucji przetwarzania obliczeniowego danych między wiele węzłów. Obliczenia są oddzielone od magazynu, co umożliwia skalowanie obliczeń niezależnie od danych w systemie. 

W przypadku dedykowanej puli SQL jednostka skalowania jest abstrakcją mocy obliczeniowej, która jest znana jako [jednostka magazynu danych](resource-consumption-models.md). 

W przypadku bez serwera bez serwera puli SQL skalowanie jest wykonywane automatycznie w celu uwzględnienia wymagań dotyczących zasobów zapytań. Gdy topologia zmienia się wraz z dodawaniem, usuwaniem węzłów lub trybu failover, dostosowuje się do zmian i upewnia się, że zapytanie ma wystarczającą ilość zasobów i zakończy się pomyślnie. Na przykład na poniższej ilustracji przedstawiono bez serwera bez serwera pulę SQL korzystającą z 4 węzłów obliczeniowych w celu wykonania zapytania.

![Architektura usługi](./media//overview-architecture/sql-architecture.png)

Synapse SQL korzysta z architektury opartej na węzłach. Aplikacje łączą się z węzłem kontrolnym i wydajeją polecenia T-SQL, które są pojedynczym punktem wejścia dla Synapse SQL. 

Węzeł Azure Synapse SQL korzysta z rozproszonego aparatu zapytań do optymalizowania zapytań pod kątem przetwarzania równoległego, a następnie przekazuje operacje do węzłów obliczeniowych w celu równoległego wykonywania pracy. 

Węzeł kontrolny bez serwera puli SQL korzysta z aparatu rozproszonego przetwarzania zapytań (DQP) w celu optymalizacji i organizowania rozproszonego wykonywania zapytań użytkowników przez podzielenie go na mniejsze zapytania, które będą wykonywane w węzłach obliczeniowych. Każde małe zapytanie jest nazywane zadaniem i reprezentuje jednostkę wykonywania rozproszonego. Odczytuje pliki z magazynu, łączy wyniki z innych zadań, grup lub zamówień danych pobranych z innych zadań. 

Węzły obliczeniowe przechowują wszystkie dane użytkowników w usłudze Azure Storage i wykonują zapytania równoległe. Usługa przenoszenia danych (ang. Data Movement Service, DMS) to wewnętrzna usługa działająca na poziomie systemu, która przenosi dane pomiędzy węzłami w sposób wymagany do równoległego wykonywania zapytań i zwracania prawidłowych wyników. 

W przypadku korzystania z magazynu i zasobów obliczeniowych Synapse SQL niezależnej wielkości mocy obliczeniowej niezależnie od potrzeb magazynu. W przypadku bez serwera skalowanie puli SQL odbywa się automatycznie, natomiast w przypadku dedykowanej puli SQL można:

* Powiększaj lub zmniejszaj moc obliczeniową w dedykowanej puli SQL bez przenoszenia danych.
* Wstrzymywać zasoby obliczeniowe bez wpływu na dane, płacąc tylko za przestrzeń dyskową.
* Wznawiać zasoby obliczeniowe w godzinach pracy.

## <a name="azure-storage"></a>Azure Storage

Synapse SQL korzysta z usługi Azure Storage, aby zapewnić bezpieczeństwo danych użytkownika. Ponieważ dane są przechowywane i zarządzane przez usługę Azure Storage, za zużycie magazynu jest naliczana oddzielna opłata. 

Bez serwera pula SQL umożliwia wykonywanie zapytań dotyczących plików data lake, a dedykowana pula SQL umożliwia wykonywanie zapytań i pozysowanie danych z data lake plików. Po pozysłaniu danych do dedykowanej puli SQL  dane są podzielone na dystrybucje, aby zoptymalizować wydajność systemu. Podczas definiowania tabeli możesz wybrać wzorzec dzielenia na fragmenty używany do dystrybucji danych. Obsługiwane są następujące wzorce fragmentowania:

* Skrót
* Działanie okrężne
* Replikowanie

## <a name="control-node"></a>Węzeł kontrolny

Węzeł kontrolny to mózg całej architektury. Jest to fronton współdziałający ze wszystkimi aplikacjami i połączeniami. 

W Synapse SQL rozproszony aparat zapytań działa w węźle kontrolnym w celu optymalizacji i koordynowania zapytań równoległych. Po przesłaniu zapytania T-SQL do dedykowanej puli SQL węzeł kontrolny przekształca je w zapytania, które są uruchamiane równolegle dla każdej dystrybucji.

W bezużyteowej puli SQL aparat DQP działa w węźle kontrolnym, aby zoptymalizować i skoordynować rozproszone wykonywanie zapytania użytkownika, dzieląc je na mniejsze zapytania, które będą wykonywane w węzłach obliczeniowych. Przypisuje również zestawy plików do przetworzenia przez każdy węzeł.

## <a name="compute-nodes"></a>Węzły obliczeniowe

Węzły obliczeniowe zapewniają moc obliczeniową. 

W dedykowanej puli SQL dystrybucje są mapowe na węzły obliczeniowe w celu przetworzenia. Gdy płacisz za więcej zasobów obliczeniowych, pula ponownie mapuje dystrybucje na dostępne węzły obliczeniowe. Liczba węzłów obliczeniowych wynosi od 1 do 60 i jest określana na podstawie poziomu usługi dla dedykowanej puli SQL. Każdy węzeł obliczeniowy ma identyfikator węzła, który jest widoczny w widokach systemowych. Identyfikator węzła obliczeniowego można wyświetlić, szukając kolumny node_id w widokach systemowych, których nazwy zaczynają się od sys.pdw_nodes. Aby uzyskać listę tych widoków systemowych, [zobacz Synapse SQL widoków systemu](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest&preserve-view=true).

W bezserowej puli SQL do każdego węzła obliczeniowego jest przypisywane zadanie i zestaw plików do wykonania zadania. Zadanie to jednostka wykonywania zapytania rozproszonego, która w rzeczywistości jest częścią przesłanego przez użytkownika zapytania. Automatyczne skalowanie jest stosowane w celu upewniania się, że do wykonania zapytania użytkownika jest wystarczająca ilość węzłów obliczeniowych.

## <a name="data-movement-service"></a>Usługa przenoszenia danych

Usługa Data Movement Service (DMS) to technologia transportu danych w dedykowanej puli SQL, która koordynuje ruch danych między węzłami obliczeniowymi. Niektóre zapytania wymagają zmiany danych w celu zapewnienia, że zapytania równoległe zwracają dokładne wyniki. Gdy jest wymagane przemieszczenia danych, usługi DMS zapewniają, że odpowiednie dane dojdą do właściwej lokalizacji.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Dystrybucji

Dystrybucja to podstawowa jednostka magazynowania i przetwarzania zapytań równoległych uruchamianych na danych rozproszonych w dedykowanej puli SQL. Gdy dedykowana pula SQL uruchamia zapytanie, praca jest dzielona na 60 mniejszych zapytań, które są uruchamiane równolegle. 

Każde z 60 mniejszych zapytań jest uruchamiane w jednej z dystrybucji danych. Każdy węzeł obliczeniowy zarządza co najmniej jedną z 60 dystrybucji. Dedykowana pula SQL z maksymalnymi zasobami obliczeniowymi ma jedną dystrybucję na węzeł obliczeniowy. Dedykowana pula SQL z minimalnymi zasobami obliczeniowymi ma wszystkie dystrybucje w jednym węźle obliczeniowym. 

## <a name="hash-distributed-tables"></a>Tabele dystrybuowane przy użyciu skrótu
Tabela dystrybuowana przy użyciu skrótu może zapewniać najwyższą wydajność zapytań w przypadku sprzężeń i agregacji w dużych tabelach. 

Aby dzielić dane na tabelę rozproszoną przy użyciu skrótów, dedykowana pula SQL używa funkcji skrótu w celu deterministycznego przypisania każdego wiersza do jednej dystrybucji. W definicji tabeli jedna kolumna zostaje wyznaczona jako kolumna dystrybucji. Funkcja skrótu używa wartości z kolumny dystrybucji, aby przypisać każdy wiersz do określonej dystrybucji.

Na poniższym diagramie pokazano, w jaki sposób pełna (niepoproszona tabela) jest przechowywana jako tabela rozproszona przy wyznaczaniu wartości skrótu. 

![Tabela rozproszona](media//overview-architecture/hash-distributed-table.png "Tabela rozproszona") 

* Każdy wiersz należy do jednej dystrybucji. 
* Deterministyczny algorytm wyznaczania wartości skrótu przypisuje każdy wiersz do jednego rozkładu. 
* Liczba wierszy tabeli na dystrybucję różni się, jak pokazano w różnych rozmiarach tabel.

Istnieją zagadnienia dotyczące wydajności związane z wyborem kolumny dystrybucji, takie jak odrębność, niesyć danych i typy zapytań uruchamianych w systemie.

## <a name="round-robin-distributed-tables"></a>Tabele dystrybuowane przy użyciu działania okrężnego

Tabela z działaniem okrężnym jest najprostszą tabelą do utworzenia i zapewnia wysoką wydajność, gdy jest używana jako tabela przemieszczania do ładowania.

W tabeli dystrybuowanej przy użyciu działania okrężnego dane są dystrybuowane równomiernie w całej tabeli, bez dodatkowej optymalizacji. Rozkład jest najpierw wybierany losowo, a następnie bufory wierszy są sekwencyjnie przypisywane do rozkładów. Ładowanie danych do tabeli z działaniem okrężnym jest szybkie, ale wydajność zapytań jest często lepsza w tabelach dystrybuowanych przy użyciu skrótu. Sprzężenia w tabelach z okrężną wymagają ponownego dufowania danych, co zajmuje dodatkowy czas.

## <a name="replicated-tables"></a>Zreplikowane tabele
Tabela replikowana zapewnia najszybsze wykonywanie zapytań w przypadku niewielkich tabel.

Tabela, która jest replikowana, buforuje pełną kopię tabeli w każdym węźle obliczeniowym. Dlatego replikowanie tabeli usuwa konieczność przesyłania danych między węzłami obliczeniowymi przed przyłączeniem lub agregacją. Replikacja sprawdza się najlepiej w przypadku małych tabel. Wymagany jest dodatkowy magazyn oraz dodatkowe obciążenie związane z zapisywaniem danych, które sprawiają, że duże tabele są niepraktyczne. 

Na poniższym diagramie przedstawiono zreplikowane tabeli, które są buforowane w pierwszej dystrybucji w każdym węźle obliczeniowym. 

![Tabela replikowana](media/overview-architecture/replicated-table.png "Tabela replikowana") 

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już nieco o Synapse SQL, dowiedz się, jak szybko utworzyć dedykowaną pulę [SQL](../quickstart-create-sql-pool-portal.md) i załadować przykładowe dane (./sql-data-warehouse-load-sample-databases.md). [](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) Lub zacznij [korzystać z bez serwera puli SQL.](../quickstart-sql-on-demand.md) Jeśli dopiero zaczynasz korzystać z platformy Azure, [słownik platformy Azure](../../azure-glossary-cloud-terminology.md) może pomóc Ci zaznajomić się z nową terminologią. 
