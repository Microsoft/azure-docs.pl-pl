---
title: Projektowanie magazynu tabel platformy Azure pod kątem modyfikacji danych | Microsoft Docs
description: Projektowanie tabel do modyfikacji danych w usłudze Azure Table Storage. Optymalizuj operacje wstawiania, aktualizowania i usuwania. Zapewnij spójność przechowywanych jednostek.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 25785bc4b945f469e67f2a71eb6676940e091d56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88236764"
---
# <a name="design-for-data-modification"></a>Projektowanie pod kątem modyfikacji danych
Ten artykuł koncentruje się na zagadnieniach projektowych dotyczących optymalizacji operacji wstawiania, aktualizacji i usuwania. W niektórych przypadkach należy oszacować różnice między projektami, które optymalizują się w celu wykonywania zapytań dotyczących projektów, które optymalizują się pod kątem modyfikacji danych, tak samo jak w przypadku projektów relacyjnych baz danych (chociaż techniki zarządzania projektami są inne w relacyjnej bazie danych). W sekcjach wzorów projektowania tabeli opisano niektóre szczegółowe wzorce projektowe dla Table service i wyróżniają niektóre z nich. W ramach tego problemu okaże się, że wiele projektów zoptymalizowanych pod kątem wykonywania zapytań dotyczących jednostek również dobrze sprawdza się w przypadku modyfikowania jednostek.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Optymalizowanie wydajności operacji wstawiania, aktualizowania i usuwania
Aby zaktualizować lub usunąć jednostkę, musi być możliwe jej zidentyfikowanie przy użyciu wartości **PartitionKey** i **RowKey** . W związku z tym wybór opcji **PartitionKey** i **RowKey** w celu zmodyfikowania jednostek powinien spełniać podobne kryteria, aby obsługiwały zapytania dotyczące punktów, ponieważ chcemy identyfikować jednostki tak efektywnie, jak to możliwe. Nie chcesz korzystać z niewydajnej partycji lub skanowania tabeli, aby zlokalizować jednostkę w celu odnalezienia wartości **PartitionKey** i **RowKey** , które należy zaktualizować lub usunąć.  

Następujące wzorce w wzorach projektu tabeli sekcji dotyczą optymalizacji wydajności lub operacji wstawiania, aktualizowania i usuwania:  

* [Duże Usuwanie woluminu wzorzec](table-storage-design-patterns.md#high-volume-delete-pattern) — umożliwia usunięcie dużej liczby jednostek przez zapisanie wszystkich jednostek do jednoczesnego usunięcia w oddzielnej tabeli. obiekty można usunąć, usuwając tabelę.  
* [Wzorzec serii danych](table-storage-design-patterns.md#data-series-pattern) — Przechowaj kompletne serie danych w pojedynczej jednostce, aby zminimalizować liczbę wysyłanych żądań.  
* [Wzorzec szerokiej jednostki](table-storage-design-patterns.md#wide-entities-pattern) — używanie wielu jednostek fizycznych do przechowywania jednostek logicznych z więcej niż 252 właściwościami.  
* [Wzorzec dużych jednostek](table-storage-design-patterns.md#large-entities-pattern) — Użyj magazynu obiektów BLOB do przechowywania dużych wartości właściwości.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Zapewnianie spójności przechowywanych jednostek
Drugim czynnikiem, który ma wpływ na wybór kluczy do optymalizacji modyfikacji danych, jest sposób zapewnienia spójności przy użyciu transakcji niepodzielnych. Elementu EGT można używać tylko do obsługi jednostek przechowywanych w tej samej partycji.  

W poniższych wzorcach [wzorców projektu tabeli](table-storage-design-patterns.md) artykułów adres zarządzania spójnością:  

* [Wzorzec indeksu pomocniczego wewnątrz partycji](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) — przechowywanie wielu kopii każdej jednostki przy użyciu różnych wartości **RowKey** (w tej samej partycji), aby umożliwić szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych wartości **RowKey** .  
* [Wzorzec indeksu pomocniczego między partycjami](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) — przechowywanie wielu kopii każdej jednostki przy użyciu różnych wartości RowKey w oddzielnych partycjach lub w oddzielnych tabelach, aby umożliwić szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych wartości **RowKey** .  
* [Wzorzec ostatecznie spójnych transakcji](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) — Włącz ostatecznie spójne zachowanie między granicami partycji lub granicami systemu magazynu za pomocą kolejek platformy Azure.
* [Indeks jednostek indeksu](table-storage-design-patterns.md#index-entities-pattern) — utrzymuje jednostki indeksu, aby umożliwić efektywne wyszukiwanie, które zwracają listy jednostek.  
* [Wzorzec denormalizacji](table-storage-design-patterns.md#denormalization-pattern) — połącz powiązane dane ze sobą w pojedynczej jednostce, aby umożliwić pobieranie wszystkich potrzebnych danych przy użyciu pojedynczego punktu zapytania.  
* [Wzorzec serii danych](table-storage-design-patterns.md#data-series-pattern) — Przechowaj kompletne serie danych w pojedynczej jednostce, aby zminimalizować liczbę wysyłanych żądań.  

Informacje o transakcjach grupy jednostek można znaleźć w sekcji dotyczącej [transakcji grupy jednostek](table-storage-design.md#entity-group-transactions).  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Upewnij się, że projekt do wydajnej modyfikacji ułatwia wykonywanie wydajnych zapytań
W wielu przypadkach projekt do wydajnego wykonywania zapytań umożliwia wydajne modyfikacje, ale zawsze należy sprawdzić, czy jest to przypadek dla konkretnego scenariusza. Niektóre wzorce w [wzorcach projektowych tabeli](table-storage-design-patterns.md) artykułów wyraźnie ocenią różnice między kwerendami i modyfikowaniem jednostek, a zawsze należy wziąć pod uwagę liczbę poszczególnych typów operacji.  

Poniższe wzorce w [projekcie tabeli](table-storage-design-patterns.md) artykułów stanowią kompromis między projektowaniem dla wydajnych zapytań i projektowaniem do wydajnej modyfikacji danych:  

* [Wzorzec klucza złożonego](table-storage-design-patterns.md#compound-key-pattern) — Użyj złożonych wartości **RowKey** , aby umożliwić klientowi wyszukiwanie powiązanych danych za pomocą pojedynczego punktu zapytania.  
* [Wzorzec końca dziennika](table-storage-design-patterns.md#log-tail-pattern) — pobranie *n* jednostek, które zostały ostatnio dodane do partycji przy użyciu wartości **RowKey** , która jest sortowana w odwrotnej kolejności daty i godziny.  
