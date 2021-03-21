---
title: Dryf schematu w mapowaniu przepływu danych
description: Twórz odporne przepływy danych w Azure Data Factory z dryfem schematu
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: 11ddb2f40ee56b51c5ecbae11465093abb8e4feb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93027486"
---
# <a name="schema-drift-in-mapping-data-flow"></a>Dryf schematu w mapowaniu przepływu danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dryfowanie schematu to przypadek, w którym źródła często zmieniają metadane. Pola, kolumny i typy można dodawać, usuwać lub zmieniać na bieżąco. Bez obsługi dryfowania schematu przepływ danych jest narażony na zmiany nadrzędnego źródła danych. Typowe wzorce ETL kończą się niepowodzeniem, gdy przychodzące kolumny i pola są zmieniane, ponieważ mają one być powiązane z tymi nazwami źródłowymi.

Aby chronić przed dryfem ze schematami, ważne jest, aby w narzędziu przepływu danych były dostępne obiekty, dzięki którym inżynier danych może:

* Zdefiniuj źródła, które mają modyfikowalne nazwy pól, typy danych, wartości i rozmiary
* Zdefiniuj parametry przekształcenia, które mogą współpracowały ze wzorcami danych, a nie zakodowane pola i wartości
* Zdefiniuj wyrażenia, które opisują wzorce, aby dopasować pola przychodzące zamiast używać nazwanych pól

Azure Data Factory natywnie obsługuje elastyczne schematy, które zmieniają się po wykonaniu do wykonania, aby można było utworzyć logikę transformacji danych ogólnych bez potrzeby ponownego kompilowania przepływów danych.

Należy podjąć decyzję dotyczącą architektury w przepływie danych, aby akceptować dryf schematu w ramach przepływu. Gdy to zrobisz, możesz chronić przed zmianami schematu ze źródeł. Jednak w ramach przepływu danych utracisz wczesne powiązania kolumn i typów. Azure Data Factory traktuje przepływy napływu schematu jako przepływy późnego wiązania, dlatego podczas kompilowania przekształceń nie będą dostępne nazwy kolumn z przełożeniami w widoku schematu w całym przepływie.

To wideo zawiera wprowadzenie do niektórych złożonych rozwiązań, które można łatwo kompilować w usłudze ADF przy użyciu funkcji dryfowania schematu przepływu danych. W tym przykładzie tworzymy wzorce wielokrotnego użytku na podstawie elastycznych schematów bazy danych:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tyx7]

## <a name="schema-drift-in-source"></a>Dryf schematu w źródle

Kolumny przesyłane do przepływu danych z definicji źródłowej są zdefiniowane jako "przedzielone", gdy nie są obecne w projekcji źródłowej. Projekcję źródła można wyświetlić na karcie projekcja w transformacji źródłowej. Po wybraniu zestawu danych dla źródła ADF automatycznie przejdzie schemat z zestawu danych i utworzysz projekcję z tej definicji schematu zestawu danych.

W transformacji źródłowej, dryfowanie schematu jest zdefiniowane jako odczytane kolumny, które nie są zdefiniowane w schemacie zestawu danych. Aby włączyć dryfowanie schematu, zaznacz pole wyboru **Zezwalaj na dryfowanie schematu** w transformacji źródłowej.

![Źródło dryfu schematu](media/data-flow/schemadrift001.png "Źródło dryfu schematu")

Po włączeniu dryfowania schematu wszystkie pola przychodzące są odczytywane ze źródła podczas wykonywania i przesyłane przez cały przepływ do ujścia. Domyślnie wszystkie nowo wykryte kolumny, znane jako *kolumny* przedzielone, docierają jako dane typu String. Jeśli chcesz, aby przepływ danych automatycznie wywnioskować typy danych z przewidzianymi kolumnami, zaznacz pole wyboru **wywnioskowanie typów kolumn** w ustawieniach źródłowych.

## <a name="schema-drift-in-sink"></a>Dryfowanie schematu w zlewie

W transformacji ujścia, dryfowanie schematu jest zapisywane w przypadku zapisywania dodatkowych kolumn na podstawie elementów zdefiniowanych w schemacie danych ujścia. Aby włączyć dryfowanie schematu, zaznacz pole wyboru **Zezwalaj na dryfowanie** obiektów w transformację ujścia.

![Ujścia schematu dryfu](media/data-flow/schemadrift002.png "Ujścia schematu dryfu")

Jeśli funkcja dryfowania schematu jest włączona, upewnij się, że suwak **automapowanie** na karcie Mapowanie jest włączony. Po tym suwaku wszystkie kolumny przychodzące są zapisywane w miejscu docelowym. W przeciwnym razie musisz użyć mapowania opartego na regułach, aby napisać kolumny przeznaczone.

![Automapowanie ujścia](media/data-flow/automap.png "Automapowanie ujścia")

## <a name="transforming-drifted-columns"></a>Przekształcanie kolumn z dryfem

Gdy przepływ danych ma przelany kolumny, możesz uzyskać do nich dostęp przy użyciu następujących metod:

* Użyj `byPosition` wyrażeń i, `byName` Aby jawnie odwoływać się do kolumny według nazwy lub numeru pozycji.
* Dodaj wzorzec kolumny w kolumnie pochodnej lub transformację agregacji, aby dopasować ją do dowolnej kombinacji nazw, strumienia, pozycji, pochodzenia lub typu
* Dodaj mapowanie oparte na regułach w transformację SELECT lub ujścia, aby dopasować kolumny przeznaczone do aliasów kolumn za pośrednictwem wzorca

Aby uzyskać więcej informacji na temat implementowania wzorców kolumn, zobacz [wzorce kolumn w mapowaniu przepływu danych](concepts-data-flow-column-pattern.md).

### <a name="map-drifted-columns-quick-action"></a>Szybka akcja mapowania kolumn z dryfem

Aby jawnie odwoływać się do kolumn z dryfem, można szybko generować mapowania dla tych kolumn za pośrednictwem szybkiej akcji podglądu danych. Gdy [tryb debugowania](concepts-data-flow-debug-mode.md) jest włączony, przejdź do karty Podgląd danych, a następnie kliknij przycisk **Odśwież** , aby pobrać Podgląd danych. Jeśli Fabryka danych wykryje, że istnieją kolumny mające Przedziały, można kliknąć pozycję **Mapuj** i wygenerować kolumnę pochodną, która pozwala na odwoływanie się do wszystkich kolumn w widoku schematu podrzędnych.

![Zrzut ekranu przedstawia kartę Podgląd danych z zamapowanej mapy o nazwie wychodzącej.](media/data-flow/mapdrifted1.png "Przedryfowanie mapy")

W wygenerowanej transformacji kolumn pochodnych każda przeprowadzona kolumna jest mapowana na wykrytą nazwę i typ danych. W powyższym podglądzie danych kolumna "movieId" jest wykryta jako liczba całkowita. Po kliknięciu **mapowanej mapy** movieId jest zdefiniowany w kolumnie pochodnej jako `toInteger(byName('movieId'))` i uwzględniony w widokach schematu w transformacjach podrzędnych.

![Zrzut ekranu przedstawia kartę Ustawienia kolumny pochodnej.](media/data-flow/mapdrifted2.png "Przedryfowanie mapy")

## <a name="next-steps"></a>Następne kroki
W [języku wyrażeń przepływu danych](data-flow-expression-functions.md)znajdziesz dodatkowe możliwości dla wzorców kolumn i dryfu schematu, w tym "byName" i "byPosition".
