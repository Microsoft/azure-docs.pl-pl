---
title: Dryft schematu w przepływie danych mapowania
description: Tworzenie odpornych przepływów danych w fabryce danych platformy Azure za pomocą dryfu schematu
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: 4c510cae7ba5a952e6dc7f7cb7ae7591fe801560
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461297"
---
# <a name="schema-drift-in-mapping-data-flow"></a>Dryft schematu w przepływie danych mapowania

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Dryf schematu jest w przypadku, gdy źródła często zmieniają metadane. Pola, kolumny i typy mogą być dodawane, usuwane lub zmieniane na bieżąco. Bez obsługi dla dryfu schematu przepływu danych staje się narażony na zmiany źródła danych nadrzędnych. Typowe wzorce ETL nie po awarii, gdy przychodzące kolumny i pola zmieniają się, ponieważ są one zwykle powiązane z tymi nazwami źródłowymi.

Aby chronić przed dryftem schematu, ważne jest, aby mieć obiekty w narzędziu przepływu danych, aby umożliwić, jako inżynier danych, do:

* Definiowanie źródeł, które mają zmienne nazwy pól, typy danych, wartości i rozmiary
* Definiowanie parametrów transformacji, które mogą pracować z wzorcami danych zamiast zakodowanych pól i wartości
* Definiowanie wyrażeń, które rozumieją wzorce w celu dopasowania pól przychodzących, zamiast używać nazwanych pól

Usługa Azure Data Factory natywnie obsługuje elastyczne schematy, które zmieniają się z wykonywania na wykonanie, dzięki czemu można tworzyć logikę transformacji danych ogólnych bez konieczności ponownego kompilacji przepływów danych.

Należy podjąć decyzję architektury w przepływie danych, aby zaakceptować dryf schematu w całym przepływie. Po wykonaniu tej tej pracy można chronić przed zmianami schematu ze źródeł. Jednak utracisz wczesne powiązanie kolumn i typów w całym przepływie danych. Usługa Azure Data Factory traktuje przepływy dryfów schematu jako przepływy późnego wiązania, więc podczas tworzenia przekształceń, nazwy kolumn dryfował nie będą dostępne w widokach schematu w całym przepływie.

Ten klip wideo zawiera wprowadzenie do niektórych złożonych rozwiązań, które można łatwo zbudować w ujmiju ADF z funkcją dryfu schematu przepływu danych. W tym przykładzie tworzymy wzorce wielokrotnego wykorzystania na podstawie elastycznych schematów bazy danych:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tyx7]

## <a name="schema-drift-in-source"></a>Dryf schematu w źródle

Kolumny wchodzące do przepływu danych z definicji źródła są definiowane jako "dryfowane", gdy nie są obecne w projekcji źródłowej. Projekcję źródłą można wyświetlić na karcie rzutowania w transformacji źródłowej. Po wybraniu zestawu danych dla źródła podajnik ADF automatycznie przejmie schemat z zestawu danych i utworzy projekt z tej definicji schematu zestawu danych.

W transformacji źródłowej dryft schematu jest zdefiniowany jako odczyt kolumn, które nie są zdefiniowane schematu zestawu danych. Aby włączyć dryft schematu, zaznacz opcję **Zezwalaj na dryft schematu** w transformacji źródła.

![Źródło dryfu schematu](media/data-flow/schemadrift001.png "Źródło dryfu schematu")

Gdy dryf schematu jest włączona, wszystkie pola przychodzące są odczytywane ze źródła podczas wykonywania i przekazywane przez cały przepływ do sink. Domyślnie wszystkie nowo wykryte kolumny, znane jako *kolumny dryfowane,* są dostarczane jako typ danych ciągu. Jeśli chcesz, aby przepływ danych automatycznie wywnioskował typy danych z dryfowanych kolumn, zaznacz **typy kolumn dryfujących infer** w ustawieniach źródła.

## <a name="schema-drift-in-sink"></a>Dryf schematu w zlewie

W transformacji ujścia dryf schematu jest podczas pisania dodatkowych kolumn na górze, co jest zdefiniowane w schemacie danych ujścia. Aby włączyć dryft schematu, sprawdź **Zezwalaj na dryf schematu** w transformacji ujścia.

![Zlew dryfu schematu](media/data-flow/schemadrift002.png "Zlew dryfu schematu")

Jeśli dryft schematu jest włączony, upewnij się, że suwak **automatycznego mapowania** na karcie Mapowanie jest włączony. Po wł. W przeciwnym razie należy użyć mapowania opartego na regułach do zapisu kolumn dryfowanych.

![Automatyczne mapowanie umywalki](media/data-flow/automap.png "Automatyczne mapowanie umywalki")

## <a name="transforming-drifted-columns"></a>Przekształcanie kolumn dryfowanych

Gdy przepływ danych ma dryfowane kolumny, można uzyskać do nich dostęp w przekształceniach za pomocą następujących metod:

* Użyj `byPosition` i `byName` wyrażenia jawnie odwołać się do kolumny według nazwy lub numeru pozycji.
* Dodawanie wzorca kolumny w kolumnie pochodnej lub transformacji agregacji w celu dopasowania do dowolnej kombinacji nazwy, strumienia, położenia lub typu
* Dodawanie mapowania opartego na regułach w transformacji Wyboru lub Zlewu w celu dopasowania kolumn dryfowanych do aliasów kolumn za pomocą wzorca

Aby uzyskać więcej informacji na temat implementowania wzorców kolumn, zobacz [Wzorce kolumn w przepływie danych mapowania](concepts-data-flow-column-pattern.md).

### <a name="map-drifted-columns-quick-action"></a>Mapa dryfował kolumny szybkie działanie

Aby jawnie odwoływać się do kolumn dryfowanych, można szybko wygenerować mapowania dla tych kolumn za pomocą szybkiej akcji podglądu danych. Po włączeniu [trybu debugowania](concepts-data-flow-debug-mode.md) przejdź do karty Podgląd danych i kliknij przycisk **Odśwież,** aby pobrać podgląd danych. Jeśli fabryka danych wykryje, że istnieją kolumny dryfowane, można kliknąć **mapę dryfował** i wygenerować kolumnę pochodną, która pozwala odwoływać się do wszystkich dryfował kolumn w widokach schematu w dół.

![Mapa dryfowała](media/data-flow/mapdrifted1.png "Mapa dryfowała")

W wygenerowanej transformacji kolumny pochodnej każda kolumna dryfowana jest mapowana na wykrytą nazwę i typ danych. W powyższym podglądzie danych kolumna "movieId" jest wykrywana jako liczba całkowita. Po **kliknięciu map drifted,** movieId jest zdefiniowany `toInteger(byName('movieId'))` w kolumnie pochodnej jako i uwzględnione w widokach schematu w dalszych przekształceniach.

![Mapa dryfowała](media/data-flow/mapdrifted2.png "Mapa dryfowała")

## <a name="next-steps"></a>Następne kroki
W [języku wyrażenia przepływu danych](data-flow-expression-functions.md)znajdziesz dodatkowe udogodnienia dla wzorców kolumn i dryfu schematu, w tym "byName" i "byPosition".
