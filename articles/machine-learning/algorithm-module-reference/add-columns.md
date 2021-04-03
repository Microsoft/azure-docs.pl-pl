---
title: 'Dodawanie kolumn: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą modułu Dodaj kolumny w projektancie Azure Machine Learning przeciągnij i upuść, aby połączyć dwa zestawy danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 36de827dff239dbeebc66e330a76b7a65fefb909
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421961"
---
# <a name="add-columns-module"></a>Dodawanie modułu kolumn

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Ten moduł służy do łączenia dwóch zestawów danych. Wszystkie kolumny z dwóch zestawów danych, które można określić jako dane wejściowe, są łączone w celu utworzenia jednego elementu DataSet. Jeśli musisz połączyć więcej niż dwa zestawy danych, użyj kilku wystąpień **Dodaj kolumny**.



## <a name="how-to-configure-add-columns"></a>Jak skonfigurować Dodawanie kolumn
1. Dodaj moduł **Dodaj kolumny** do potoku.

2. Połącz dwa zestawy danych, które chcesz połączyć. Jeśli chcesz połączyć więcej niż dwa zestawy danych, możesz utworzyć łańcuch ze sobą kilka kombinacji **dodawania kolumn**.

    - Istnieje możliwość połączenia dwóch kolumn, które mają różną liczbę wierszy. Wyjściowy zestaw danych jest uzupełniony o brakujące wartości dla każdego wiersza w mniejszej kolumnie źródłowej.

    - Nie można wybrać pojedynczych kolumn do dodania. Wszystkie kolumny z każdego zestawu danych są łączone podczas korzystania z **dodawania kolumn**. W związku z tym, jeśli chcesz dodać tylko podzestaw kolumn, użyj opcji wybierz kolumny w zestawie danych, aby utworzyć zestaw danych z kolumnami, które chcesz.

3. Prześlij potok.

### <a name="results"></a>Wyniki
Po uruchomieniu potoku:

- Aby wyświetlić pierwsze wiersze nowego zestawu danych, kliknij prawym przyciskiem myszy moduł **Dodaj kolumny** i wybierz polecenie Wizualizuj. Lub wybierz moduł i przejdź do karty dane **wyjściowe** w prawym panelu, kliknij ikonę histogramu w danych **wyjściowych portu** , aby wizualizować wynik.

Liczba kolumn w nowym zestawie danych jest równa sumie kolumn obu wejściowych zestawów danych.

Jeśli istnieją dwie kolumny o tej samej nazwie w wejściowych zestawach danych, do nazwy kolumny zostanie dodany sufiks liczbowy. Na przykład jeśli istnieją dwa wystąpienia kolumny o nazwie TargetOutcome, po lewej kolumnie zostanie zmieniona nazwa TargetOutcome_1 a prawa kolumna zostanie zmieniona TargetOutcome_2.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 