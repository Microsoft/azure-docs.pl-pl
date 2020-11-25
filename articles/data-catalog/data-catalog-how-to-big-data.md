---
title: Jak wykazać dane Big Data w Azure Data Catalog
description: Instrukcje dotyczące wyróżniania artykułu dotyczącego używania Azure Data Catalog ze źródłami danych Big Data, w tym Azure Blob Storage, Azure Data Lake i Hadoop HDFS.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: e79e5e16b300fd02b9c9124f9677747834f22813
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009436"
---
# <a name="how-to-catalog-big-data-in-azure-data-catalog"></a>Jak wykazać dane Big Data w Azure Data Catalog

## <a name="introduction"></a>Wprowadzenie

**Microsoft Azure Data Catalog** to w pełni zarządzana usługa w chmurze, która służy jako system rejestracji i odnajdywania źródeł danych w przedsiębiorstwie. Wszystko to ułatwia osobom odkrywanie, poznawanie i używanie źródeł danych oraz ułatwianie organizacjom uzyskiwania większej wartości z istniejących źródeł danych, w tym danych Big Data.

**Azure Data Catalog** obsługuje rejestrację obiektów blob i katalogów usługi Azure Storage, a także plików i katalogów Hadoop HDFS. Ogólny charakter tych źródeł danych zapewnia dużą elastyczność. Aby jednak uzyskać największą wartość rejestrowania ich w **Azure Data Catalog**, użytkownicy muszą rozważyć, jak są zorganizowane źródła danych.

## <a name="directories-as-logical-data-sets"></a>Katalogi jako logiczne zestawy danych

Typowym wzorcem organizowania źródeł danych Big Data jest traktowanie katalogów jako logicznych zestawów danych. Katalogi najwyższego poziomu są używane do definiowania zestawu danych, a podfoldery definiują partycje i pliki, które zawierają same dane.

Przykładem tego wzorca może być:

```text
    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...
```

W tym przykładzie vehicle_maintenance_events i location_tracking_events reprezentują logiczne zestawy danych. Każdy z tych folderów zawiera pliki danych uporządkowane według roku i miesiąca w podfolderach. Każdy z tych folderów może potencjalnie zawierać setki lub tysiące plików.

W tym wzorcu Rejestracja pojedynczych plików z **Azure Data Catalog** prawdopodobnie nie ma sensu. Zamiast tego należy zarejestrować katalogi, które reprezentują zestawy danych, które mają znaczenie dla użytkowników pracujących z danymi.

## <a name="reference-data-files"></a>Pliki danych referencyjnych

Wzorzec uzupełniający służy do przechowywania zestawów danych referencyjnych jako pojedynczych plików. Te zestawy danych mogą być uważane za małą część danych Big Data i są często podobne do wymiarów w modelu danych analitycznych. Pliki danych referencyjnych zawierają rekordy, które są używane do zapewnienia kontekstu na potrzeby zbiorczych plików danych przechowywanych w innym miejscu w magazynie danych Big Data.

Przykładem tego wzorca może być:

```text
    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv
```

Gdy analityk lub naukowc danych pracuje z danymi zawartymi w większych strukturach katalogów, dane w tych plikach referencyjnych mogą służyć do zapewnienia bardziej szczegółowych informacji o jednostkach, które są nazywane tylko nazwami lub IDENTYFIKATORami w większym zestawie danych.

W tym wzorcu warto zarejestrować pojedyncze pliki danych referencyjnych za pomocą **Azure Data Catalog**. Każdy plik reprezentuje zestaw danych, a każdy z nich może być oznaczony jako adnotacja i odnaleziony indywidualnie.

## <a name="alternate-patterns"></a>Wzorce alternatywne

Wzorce opisane w poprzedniej sekcji są tylko dwoma możliwymi sposobami organizowania magazynu danych Big Data, ale każda implementacja jest inna. Niezależnie od tego, jak źródła danych są strukturalne, podczas rejestrowania źródeł danych Big Data przy użyciu **Azure Data Catalog** należy skoncentrować się na zarejestrowaniu plików i katalogów, które reprezentują zestawy danych, które są wartościami innych w organizacji. Zarejestrowanie wszystkich plików i katalogów może obsłużyć katalog, co utrudnia użytkownikom znalezienie potrzebnych informacji.

## <a name="summary"></a>Podsumowanie

Zarejestrowanie źródeł danych za pomocą **Azure Data Catalog** ułatwia ich odnajdywanie i zrozumienie. Rejestrując pliki i katalogi danych Big Data, które reprezentują logiczne zestawy danych, można ułatwić użytkownikom znajdowanie i używanie potrzebnych źródeł danych Big Data.
