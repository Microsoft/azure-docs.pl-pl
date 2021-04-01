---
title: 'Wybieranie kolumn w zestawie danych: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu SELECT Columns in DataSet w Azure Machine Learning, aby wybrać podzbiór kolumn do użycia w operacjach podrzędnych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 7926d5dd33df5538713f8de152dbbace2bc29864
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90890300"
---
# <a name="select-columns-in-dataset-module"></a>Wybierz kolumny w module DataSet

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Użyj tego modułu, aby wybrać podzbiór kolumn do użycia w operacjach podrzędnych. Moduł nie usuwa fizycznie kolumn ze źródłowego zestawu danych; Zamiast tego tworzy podzestaw kolumn, podobnie jak w przypadku *widoku* bazy danych lub *projekcji*.

Ten moduł jest przydatny, gdy konieczne jest ograniczenie liczby kolumn dostępnych dla operacji podrzędnej, lub jeśli chcesz zmniejszyć rozmiar zestawu danych przez usunięcie niepotrzebnych kolumn.

Kolumny w zestawie danych są wyprowadzane w tej samej kolejności, w jakiej znajdują się w oryginalnych danych, nawet jeśli zostaną określone w innej kolejności.

## <a name="how-to-use"></a>Sposób użycia

Ten moduł nie ma parametrów. Selektor kolumny służy do wybierania kolumn do dołączenia lub wykluczenia.

### <a name="choose-columns-by-name"></a>Wybierz kolumny według nazwy

W module istnieje wiele opcji umożliwiających wybranie kolumn według nazwy: 

+ Filtrowanie i wyszukiwanie

    Kliknij opcję **według nazwy** .

    Jeśli masz połączony zestaw danych, który został już wypełniony, powinna zostać wyświetlona lista dostępnych kolumn. Jeśli żadna kolumna nie zostanie wyświetlona, może być konieczne uruchomienie modułów nadrzędnych w celu wyświetlenia listy kolumn.

    Aby odfiltrować listę, wpisz w polu wyszukiwania. Na przykład, jeśli wpiszesz literę `w` w polu wyszukiwania, lista zostanie przefiltrowana w celu wyświetlenia nazw kolumn zawierających literę `w` .

    Wybierz kolumny, a następnie kliknij przycisk strzałki w prawo, aby przenieść wybrane kolumny do listy w okienku po prawej stronie.

    + Aby wybrać ciągły zakres nazw kolumn, naciśnij klawisze **Shift + kliknięcie**.
    + Aby dodać pojedyncze kolumny do zaznaczenia, naciśnij **klawisze CTRL + kliknięcie**.

    Kliknij przycisk znacznik wyboru, aby zapisać i zamknąć.

+ Używanie nazw w połączeniu z innymi regułami

    Kliknij opcję **with Rules** .
    
    Wybierz regułę, taką jak wyświetlanie kolumn o określonym typie danych.

    Następnie kliknij poszczególne kolumny tego typu według nazwy, aby dodać je do listy wyboru.

+ Wpisz lub wklej rozdzieloną przecinkami listę nazw kolumn

    Jeśli zestaw danych jest szeroki, może być łatwiejsze używanie indeksów lub wygenerowanych list nazw zamiast wybierania kolumn pojedynczo. Przy założeniu, że lista została przygotowana z góry:

    1. Kliknij opcję **with Rules** . 
    2. Wybierz pozycję **brak kolumn**, wybierz pozycję  **Dołącz**, a następnie kliknij wewnątrz pola tekstowego z czerwonym wykrzyknikiem. 
    3. Wklej lub wpisz rozdzieloną przecinkami listę nazw kolumn, które zostały wcześniej zweryfikowane. Nie można zapisać modułu, jeśli dowolna kolumna ma nieprawidłową nazwę. Upewnij się, że nazwy zostały wcześniej sprawdzone.
    
    Możesz również użyć tej metody, aby określić listę kolumn przy użyciu ich wartości indeksu. 

### <a name="choose-by-type"></a>Wybierz według typu

Jeśli używasz opcji **with Rules** , możesz zastosować wiele warunków w wybranych kolumnach. Na przykład może być konieczne uzyskanie tylko kolumn funkcji typu danych liczbowych.

Opcja **BEGIN with** określa Twój punkt początkowy i jest ważna w przypadku interpretacji wyników. 

+ W przypadku wybrania opcji **wszystkie kolumny** wszystkie kolumny zostaną dodane do listy. Następnie należy użyć opcji **Wyklucz** , aby *usunąć* kolumny, które spełniają określone warunki. 

    Na przykład możesz zacząć od wszystkich kolumn, a następnie usunąć kolumny według nazwy lub typu.

+ W przypadku wybrania opcji **brak kolumn** lista kolumn zaczyna się pusta. Następnie należy określić warunki, aby *dodać* kolumny do listy. 

    W przypadku zastosowania wielu reguł każdy warunek jest **dodatkiem**. Załóżmy na przykład, że zaczynasz od brak kolumn, a następnie dodasz regułę w celu pobrania wszystkich kolumn liczbowych. W zestawie danych cen samochodów, które powoduje 16 kolumn. Następnie kliknij **+** znak, aby dodać nowy warunek, a następnie wybierz pozycję **Dołącz wszystkie funkcje**. Uzyskany zestaw danych zawiera wszystkie kolumny liczbowe oraz wszystkie kolumny funkcji, w tym niektóre kolumny funkcji ciągów.

### <a name="choose-by-column-index"></a>Wybierz według indeksu kolumny

Indeks kolumny odwołuje się do kolejności kolumny w oryginalnym zestawie danych.

+ Kolumny są numerowane sekwencyjnie, począwszy od 1.  
+ Aby uzyskać zakres kolumn, użyj łącznika. 
+ Specyfikacje typu Open-zakończony, takie jak `1-` lub, `-3` są niedozwolone.
+ Zduplikowane wartości indeksu (lub nazwy kolumn) nie są dozwolone i mogą spowodować wystąpienie błędu.

Na przykład, zakładając, że zestaw danych ma co najmniej osiem kolumn, można wkleić w jednym z poniższych przykładów, aby zwrócić wiele kolumn nieciągłych: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

Ostatni przykład nie powoduje błędu; zwraca jednak pojedyncze wystąpienie kolumny `4` .



### <a name="change-order-of-columns"></a>Zmień kolejność kolumn

Opcja **Zezwalaj na duplikowanie i zachowywanie kolejności kolumn w zaznaczeniu** rozpoczyna się od pustej listy i dodaje kolumny określone przez nazwę lub według indeksu. W przeciwieństwie do innych opcji, które zawsze zwracają kolumny w ich "porządku naturalnym", ta opcja wyprowadza kolumny w kolejności, w jakiej się znajdują lub wyświetlają listę. 

Na przykład, w zestawie danych z kolumnami Kol1, Col2, Col3 i Col4, można odwrócić kolejność kolumn i opuścić kolumnę 2, określając jedną z następujących list:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 