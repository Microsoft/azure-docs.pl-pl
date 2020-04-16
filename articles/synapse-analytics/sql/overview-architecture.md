---
title: Architektura SQL usługi Azure Synapse
description: Dowiedz się, jak usługa Azure Synapse SQL łączy w sobie przetwarzanie równoległe (MPP) z usługą Azure Storage w celu osiągnięcia wysokiej wydajności i skalowalności.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 570e84b3a545736aad6983c7f0d8c0f0296ca589
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431815"
---
# <a name="azure-synapse-sql-architecture"></a>Architektura SQL usługi Azure Synapse 

W tym artykule opisano składniki architektury Synapse SQL.

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="synapse-sql-architecture-components"></a>Składniki architektury SYNAPSE SQL

Synapse SQL wykorzystuje architekturę skalowania w poziomie do dystrybucji obliczeniowego przetwarzania danych w wielu węzłach. Obliczeń jest oddzielona od magazynu, co umożliwia skalowanie obliczeń niezależnie od danych w systemie. 

W przypadku puli SQL jednostka skali jest abstrakcją mocy obliczeniowej, która jest znana jako [jednostka magazynu danych](resource-consumption-models.md). 

W przypadku sql na żądanie, będąc bez użycia serwera, skalowanie odbywa się automatycznie, aby dostosować się do wymagań zasobów kwerendy. Jak topologia zmienia się w czasie, dodając, usuwając węzły lub pracy awaryjnej, dostosowuje się do zmian i upewnia się, że zapytanie ma wystarczającą ilość zasobów i kończy się pomyślnie. Na przykład poniższy obraz przedstawia SQL na żądanie przy użyciu 4 węzłów obliczeniowych do wykonania kwerendy.

![Architektura SQL synapsy](./media//overview-architecture/sql-architecture.png)

Synapse SQL używa architektury opartej na węźle. Aplikacje łączą i wydają polecenia T-SQL do węzła Control, który jest pojedynczym punktem wejścia dla Synapse SQL. 

Węzeł kontroli puli SQL wykorzystuje aparat MPP do optymalizacji zapytań do przetwarzania równoległego, a następnie przekazuje operacje do węzłów obliczeniowych, aby wykonywać swoją pracę równolegle. 

Węzeł kontroli SQL na żądanie wykorzystuje aparat przetwarzania zapytań rozproszonych (DQP) do optymalizacji i organizowania rozproszonego wykonywania kwerendy użytkownika przez podzielenie go na mniejsze zapytania, które będą wykonywane w węzłach obliczeniowych. Każda mała kwerenda jest nazywana zadaniem i reprezentuje rozproszoną jednostkę wykonywania. Odczytuje pliki z magazynu, łączy wyniki z innych zadań, grup lub zamówień danych pobranych z innych zadań. 

Węzły obliczeniowe przechowują wszystkie dane użytkowników w usłudze Azure Storage i wykonują zapytania równoległe. Usługa przenoszenia danych (ang. Data Movement Service, DMS) to wewnętrzna usługa działająca na poziomie systemu, która przenosi dane pomiędzy węzłami w sposób wymagany do równoległego wykonywania zapytań i zwracania prawidłowych wyników. 

Z oddzielonych pamięci masowej i obliczeń, podczas korzystania z Synapse SQL można korzystać z niezależnego rozmiaru mocy obliczeniowej, niezależnie od potrzeb magazynu. Dla SQL na żądanie skalowanie odbywa się automatycznie, podczas gdy dla puli SQL można:

* Zwiększanie lub zmniejszanie mocy obliczeniowej w puli SQL (magazyn danych) bez przenoszenia danych.
* Wstrzymywać zasoby obliczeniowe bez wpływu na dane, płacąc tylko za przestrzeń dyskową.
* Wznawiać moc obliczeniową w godzinach działania.

## <a name="azure-storage"></a>Azure Storage

Synapse SQL wykorzystuje usługę Azure Storage, aby zapewnić bezpieczeństwo danych użytkownika. Ponieważ dane są przechowywane i zarządzane przez usługę Azure Storage, istnieje osobna opłata za zużycie magazynu. 

SQL na żądanie umożliwia wykonywanie zapytań o pliki w ułowieniu danych w sposób tylko do odczytu, podczas gdy pula SQL umożliwia również pozyskiwania danych. Gdy dane są ponajętowane do puli SQL, dane są podzielone na **dystrybucje** w celu optymalizacji wydajności systemu. Można wybrać wzorzec dzielenia na fragmenty, który ma być używany do dystrybucji danych podczas definiowania tabeli. Te wzorce dzielenia na fragmenty są obsługiwane:

* Skrót
* Działanie okrężne
* Replikowanie

## <a name="control-node"></a>Węzeł kontrolny

Węzeł Control jest mózgiem architektury. Jest to fronton współdziałający ze wszystkimi aplikacjami i połączeniami. 

W puli SQL aparat MPP działa w węźle Control w celu optymalizacji i koordynowania zapytań równoległych. Podczas przesyłania kwerendy T-SQL do puli SQL, węzeł Control przekształca go w kwerendy, które są uruchamiane względem każdej dystrybucji równolegle.

W sql na żądanie aparat DQP działa w węźle Control w celu optymalizacji i koordynowania rozproszonego wykonywania kwerendy użytkownika przez podzielenie go na mniejsze zapytania, które będą wykonywane w węzłach obliczeniowych. Przypisuje również zestawy plików do przetworzenia przez każdy węzeł.

## <a name="compute-nodes"></a>Węzły obliczeniowe

Węzły obliczeniowe zapewniają moc obliczeniową. 

W puli SQL dystrybucje są mapowane do węzłów obliczeniowych do przetwarzania. Gdy płacisz za więcej zasobów obliczeniowych, pula ponownie mapuje dystrybucje do dostępnych węzłów obliczeniowych. Liczba węzłów obliczeniowych waha się od 1 do 60 i jest określana przez poziom usługi dla puli SQL. Każdy węzeł obliczeniowy ma identyfikator węzła, który jest widoczny w widokach systemu. Identyfikator węzła obliczeń można wyświetlić, wyszukując kolumnę node_id w widokach systemowych, których nazwy zaczynają się od pliku sys.pdw_nodes. Aby uzyskać listę tych widoków systemu, zobacz [Widoki systemu MPP](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest).

W języku SQL na żądanie każdemu węzłowi obliczeniowemu jest przypisane zadanie i zestaw plików do wykonania zadania. Zadanie jest rozproszoną jednostką wykonywania kwerendy, która jest w rzeczywistości częścią przesłanej przez użytkownika kwerendy. Automatyczne skalowanie jest w życie, aby upewnić się, że wystarczająco dużo węzłów obliczeniowych są wykorzystywane do wykonywania kwerendy użytkownika.

## <a name="data-movement-service"></a>Usługa przenoszenia danych

Usługa przenoszenia danych (DMS) to technologia transportu danych w puli SQL, która koordynuje przenoszenie danych między węzłami obliczeniowymi. Niektóre zapytania wymagają przenoszenia danych, aby upewnić się, że zapytania równoległe zwracają dokładne wyniki. Gdy wymagany jest ruch danych, DMS zapewnia, że odpowiednie dane trafią do właściwej lokalizacji.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Dystrybucji

Dystrybucja jest podstawową jednostką magazynu i przetwarzania dla zapytań równoległych, które są uruchamiane na rozproszonych danych w puli SQL. Gdy pula SQL uruchamia kwerendę, praca jest podzielona na 60 mniejszych zapytań, które są uruchamiane równolegle. 

Każdy z 60 mniejszych zapytań jest uruchamiany w jednej z dystrybucji danych. Każdy węzeł obliczeniowy zarządza jedną lub kilkoma z 60 dystrybucji. Pula SQL z maksymalnymi zasobami obliczeniowymi ma jedną dystrybucję na węzeł obliczeniowy. Pula SQL z minimalnymi zasobami obliczeniowymi ma wszystkie dystrybucje w jednym węźle obliczeniowym. 

## <a name="hash-distributed-tables"></a>Tabele dystrybuowane przy użyciu skrótu
Tabela dystrybuowana przy użyciu skrótu może zapewniać najwyższą wydajność zapytań w przypadku sprzężeń i agregacji w dużych tabelach. 

Aby podzielić dane do tabeli rozproszonej mieszania, pula SQL używa funkcji mieszania do deterministycznie przypisać każdy wiersz do jednej dystrybucji. W definicji tabeli jedna kolumna zostaje wyznaczona jako kolumna dystrybucji. Funkcja skrótu używa wartości z kolumny dystrybucji, aby przypisać każdy wiersz do określonej dystrybucji.

Na poniższym diagramie przedstawiono sposób, w jaki pełna (tabela nie rozproszona) jest przechowywana jako tabela rozproszona mieszania. 

![Tabela rozproszona](media//overview-architecture/hash-distributed-table.png "Tabela rozproszona") 

* Każdy wiersz należy do jednej dystrybucji. 
* Deterministyczny algorytm mieszania przypisuje każdy wiersz do jednej dystrybucji. 
* Liczba wierszy tabeli na dystrybucję różni się w zależności od różnych rozmiarów tabel.

Istnieją zagadnienia dotyczące wydajności dla wyboru kolumny dystrybucji, takie jak odrębność, pochylenie danych i typy zapytań uruchamianych w systemie.

## <a name="round-robin-distributed-tables"></a>Tabele dystrybuowane przy użyciu działania okrężnego

Tabela okrężna jest najprostszą tabelą do tworzenia i zapewnia szybką wydajność, gdy jest używana jako tabela przemieszczania dla obciążeń.

W tabeli dystrybuowanej przy użyciu działania okrężnego dane są dystrybuowane równomiernie w całej tabeli, bez dodatkowej optymalizacji. Dystrybucja jest najpierw wybierana losowo, a następnie bufory wierszy są przypisywane do dystrybucji sekwencyjnie. Ładowanie danych do tabeli z działaniem okrężnym jest szybkie, ale wydajność zapytań jest często lepsza w tabelach dystrybuowanych przy użyciu skrótu. Sprzężenia w tabelach okrężnych wymagają przetasowania danych, co zajmuje więcej czasu.

## <a name="replicated-tables"></a>Zreplikowane tabele
Tabela replikowana zapewnia najszybsze wykonywanie zapytań w przypadku niewielkich tabel.

Tabela, która jest replikowana buforuje pełną kopię tabeli w każdym węźle obliczeniowym. Dlatego replikowanie tabeli eliminuje konieczność przesyłania danych między węzłami obliczeniowymi przed operacją sprzężenia lub agregacji. Replikacja sprawdza się najlepiej w przypadku małych tabel. Dodatkowe miejsce do magazynowania jest wymagane i istnieje dodatkowe obciążenie, które jest ponoszone podczas zapisywania danych, które sprawiają, że duże tabele niepraktyczne. 

Poniższy diagram przedstawia replikowaną tabelę, która jest buforowana w pierwszej dystrybucji w każdym węźle obliczeniowym. 

![Tabela replikowana](media/overview-architecture/replicated-table.png "Tabela replikowana") 

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz trochę o Synapse SQL, dowiedz się, jak szybko [utworzyć pulę SQL](../quickstart-create-sql-pool.md) i [załadować przykładowe dane](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) (./sql-data-warehouse-load-sample-databases.md). Możesz też zacząć [używać programu SQL na żądanie](../quickstart-sql-on-demand.md). Jeśli dopiero zaczynasz korzystać z platformy Azure, [słownik platformy Azure](../../azure-glossary-cloud-terminology.md) może pomóc Ci zaznajomić się z nową terminologią. 
