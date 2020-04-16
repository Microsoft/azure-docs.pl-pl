---
title: Wykresy przepływu danych
description: Jak pracować z wykresami przepływu danych fabryki danych
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/04/2019
ms.openlocfilehash: 1618011dc3c281cb2ef06ef80cfc7c8e69c4e091
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415506"
---
# <a name="mapping-data-flow-graphs"></a>Mapowanie wykresów przepływu danych

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Powierzchnia projektowa przepływów danych mapowania jest powierzchnią "konstrukcyjną", na której przepływy danych są budowane od góry do do do góry, od lewej do prawej. Do każdego przekształcenia dołączony jest przybornik z symbolem plus (+). Skoncentruj się na logice biznesowej zamiast łączenia węzłów za pomocą krawędzi w środowisku DAG o swobodnej formie.

Poniżej znajdują się wbudowane mechanizmy do zarządzania wykresem przepływu danych.

## <a name="move-nodes"></a>Przenoszenie węzłów

![Opcje transformacji agregującej](media/data-flow/agghead.png "nagłówek agregatora")

Bez paradygmatu przeciągania i upuszczania sposobem "przenoszenia" węzła transformacji jest zmiana strumienia przychodzącego. Zamiast tego można przenieść przekształcenia wokół zmieniając "przychodzące strumienia".

## <a name="streams-of-data-inside-of-data-flow"></a>Strumienie danych wewnątrz przepływu danych

W usłudze Azure Data Factory Data Flow strumienie reprezentują przepływ danych. W okienku ustawień transformacji zostanie wyświetlene pole "Strumień przychodzący". Informuje o tym, który przychodzący strumień danych jest zasilanie tej transformacji. Można zmienić fizyczną lokalizację węzła transformacji na wykresie, klikając nazwę strumienia przychodzącego i wybierając inny strumień danych. Bieżącej transformacji wraz ze wszystkimi kolejnymi przekształcenia na tym strumieniu zostanie następnie przeniesione do nowej lokalizacji.

Jeśli przenosisz transformację z co najmniej jednym przekształcenia po nim, nowa lokalizacja w przepływie danych zostanie połączona za pośrednictwem nowej gałęzi.

Jeśli po wybranym węźle nie ma kolejnych przekształceń, tylko to przekształcenie zostanie przeniesione do nowej lokalizacji.

## <a name="hide-graph-and-show-graph"></a>Ukrywanie wykresu i pokazy wykresu

Po prawej stronie dolnego okienka konfiguracji znajduje się przycisk, w którym można rozwinąć dolne okienko do pełnego ekranu podczas pracy z konfiguracjami transformacji. Pozwoli to na korzystanie z przycisków "poprzedni" i "następny", aby poruszać się po konfiguracjach wykresu. Aby wrócić do widoku wykresu, kliknij przycisk w dół i wróć do podzielonego ekranu.

## <a name="search-graph"></a>Wykres wyszukiwania

Można przeszukiwać wykres za pomocą przycisku wyszukiwania na powierzchni projektowej.

![Wyszukaj](media/data-flow/search001.png "Wykres wyszukiwania")

## <a name="next-steps"></a>Następne kroki

Po zakończeniu projektu przepływu danych włącz przycisk debugowania i przetestuj go w trybie debugowania bezpośrednio w [projektancie przepływu danych](concepts-data-flow-debug-mode.md) lub [debugowaniu potoku.](control-flow-execute-data-flow-activity.md)
