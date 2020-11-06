---
title: 'Konwertuj na CSV: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą modułu Konwertuj do woluminów CSV w programie Azure Machine Learning Designer skonwertować zestaw danych do pliku CSV, który może być ponownie używany później.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: cc58689e30e9b03e490c0871f3decd50372371fa
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421927"
---
# <a name="convert-to-csv-module"></a>Konwertuj na moduł CSV

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Ten moduł służy do konwertowania zestawu danych w formacie CSV, który można pobrać, wyeksportować lub udostępnić za pomocą modułów skryptów języka R lub Python.

### <a name="more-about-the-csv-format"></a>Więcej informacji o formacie CSV 

Format CSV, który oznacza "wartości rozdzielane przecinkami", jest formatem pliku używanym przez wiele zewnętrznych narzędzi uczenia maszynowego. Plik CSV jest typowym formatem wymiany podczas pracy z językami typu open source, takimi jak R lub Python.

Nawet w przypadku wykonywania większości zadań w Azure Machine Learning mogą wystąpić sytuacje, w których może być przydatne przekonwertowanie zestawu danych do pliku CSV w celu użycia w zewnętrznych narzędziach. Na przykład:

+ Pobierz plik CSV, aby otworzyć go w programie Excel, lub zaimportuj go do relacyjnej bazy danych.  
+ Zapisz plik CSV w magazynie w chmurze i Połącz się z nim z Power BI, aby tworzyć wizualizacje.  
+ Użyj formatu CSV, aby przygotować dane do użycia w języku R i Python. 

Podczas konwertowania zestawu danych do formatu CSV plik CSV jest zapisywany w obszarze roboczym usługi Azure ML. Możesz użyć narzędzia usługi Azure Storage, aby otworzyć plik i korzystać z niego bezpośrednio. Możesz również uzyskać dostęp do woluminu CSV w projektancie, wybierając moduł **Konwertuj na CSV** , a następnie wybierz ikonę histogramu **na karcie dane wyjściowe w** prawym panelu, aby wyświetlić dane wyjściowe. Wolumin CSV można pobrać z folderu Results do katalogu lokalnego.  

## <a name="how-to-configure-convert-to-csv"></a>Jak skonfigurować konwertowanie do formatu CSV


1.  Dodaj moduł konwersji do woluminu CSV do potoku. Ten moduł można znaleźć w grupie **Przekształcanie danych** w projektancie. 

2. Połącz ją z dowolnym modułem, który wyprowadza zestaw danych.   
  
3.  Prześlij potok.

### <a name="results"></a>Wyniki
  

Wybierz kartę dane **wyjściowe** w prawym panelu **Konwertuj do formatu CSV** , a następnie wybierz jedną z tych ikon w obszarze **wyjście portu**.  

+ **Zarejestruj zestaw danych** : wybierz ikonę i Zapisz plik CSV z powrotem w obszarze roboczym Azure ml jako oddzielny zestaw danych. Zestaw danych można znaleźć jako moduł w drzewie modułów w sekcji **moje zbiory** danych.

 + **Wyświetl dane wyjściowe** : wybierz ikonę oka i postępuj zgodnie z instrukcjami, aby przejrzeć folder **Results_dataset** i pobrać plik data.csv.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 