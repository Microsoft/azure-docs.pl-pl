---
title: 'Dodawanie wierszy: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak połączyć dwa zestawy danych przy użyciu modułu Dodaj wiersze w Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: cd9b5f8f182c4deab746d2c41e516a6ac23fb7aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79477735"
---
# <a name="add-rows-module"></a>Dodaj moduł wierszy

W tym artykule opisano moduł w programie Azure Machine Learning Designer (wersja zapoznawcza).

Ten moduł służy do łączenia dwóch zestawów danych. W trakcie łączenia wiersze drugiego zestawu danych są dodawane na końcu pierwszego zestawu danych.  
  
Łączenie wierszy jest przydatne w scenariuszach takich jak:  
  
+ Wygenerowałeś serię statystyk oceny i chcesz połączyć je w jedną tabelę, aby ułatwić raportowanie.  
  
+ Pracujesz z różnymi zestawami danych i chcesz połączyć zestawy danych w celu utworzenia końcowego zestawu.  

## <a name="how-to-use-add-rows"></a>Jak używać Dodawanie wierszy  

Aby połączyć wiersze z dwóch zestawów danych, wiersze muszą mieć dokładnie ten sam schemat. Oznacza to, że ta sama liczba kolumn i ten sam typ danych w kolumnach.

1.  Przeciągnij moduł **Dodaj wiersze** do potoku, aby znaleźć go w obszarze **Przekształcanie danych**.

2. Połącz zestawy danych z dwoma portami wejściowymi. Zestaw danych, który chcesz dołączyć, powinien być połączony z drugim (prawym) portem. 
  
3.  Prześlij potok. Liczba wierszy w wyjściowym zestawie danych powinna być równa sumie wierszy wejściowych zestawów danych.

    Jeśli dodasz ten sam zestaw danych do obu wejść modułu **Dodaj wiersze** , zestaw danych zostanie zduplikowany. 

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 