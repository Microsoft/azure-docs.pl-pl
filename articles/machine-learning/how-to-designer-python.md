---
title: Wykonaj skrypt języka Python w projektancie
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modelu skryptów Execute języka Python w programie Azure Machine Learning Designer do uruchamiania operacji niestandardowych utworzonych w języku Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 09/09/2020
ms.topic: conceptual
ms.custom: how-to, designer, devx-track-python
ms.openlocfilehash: dcc28d98efbc82079586de8cfbecd35effc93d6e
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556237"
---
# <a name="run-python-code-in-azure-machine-learning-designer"></a>Uruchamianie kodu w języku Python w programie Azure Machine Learning Designer

W tym artykule dowiesz się, jak dodać logikę niestandardową do programu Azure Machine Learning Designer przy użyciu modułu [uruchamiania skryptu języka Python](algorithm-module-reference/execute-python-script.md) . Korzystając z poniższej procedury, można użyć biblioteki Pandas do wykonania prostej inżynierii funkcji.

Można użyć wbudowanego edytora kodu, aby szybko dodać prostą logikę języka Python. Jeśli chcesz dodać bardziej złożony kod lub przekazać dodatkowe biblioteki języka Python, należy użyć metody pliku zip.

Domyślne środowisko wykonawcze używa dystrybucji Anacondas języka Python. Aby zapoznać się z pełną listą wstępnie zainstalowanych pakietów, zapoznaj się ze stroną [wykonywanie modułu skryptu języka Python](algorithm-module-reference/execute-python-script.md) .

![Wykonaj mapę wejściową języka Python](media/how-to-designer-python/execute-python-map.png)

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="execute-python-written-in-the-designer"></a>Wykonywanie kodu Python zapisaną w projektancie

### <a name="add-the-execute-python-script-module"></a>Dodaj moduł wykonywania skryptu języka Python

1. Znajdź moduł **skryptu języka Python** w palecie projektanta. Można je znaleźć w sekcji **języka Python** .

1. Przeciągnij i upuść moduł na kanwę potoku.

### <a name="connect-input-datasets"></a>Połącz wejściowe zestawy danych

W tym artykule jest wykorzystywany przykładowy zestaw **danych z danymi cen samochodów (RAW)**. 

1. Przeciągnij i upuść zestaw danych do kanwy potoku.

1. Połącz port wyjściowy zestawu danych z górnym lewym portem wejściowym modułu skryptu języka **Python** . Projektant udostępnia dane wejściowe jako parametr do skryptu punktu wejścia.
    
    Prawidłowy port wejściowy jest zarezerwowany dla spakowanych bibliotek języka Python.

    ![Połącz zestawy danych](media/how-to-designer-python/connect-dataset.png)
        

1. Zanotuj, który port wejściowy jest używany. Projektant przypisuje lewy port wejściowy do zmiennej `dataset1` i środkowy port wejściowy do `dataset2` . 

Moduły wejściowe są opcjonalne, ponieważ można generować lub importować dane bezpośrednio w module **wykonywania skryptu języka Python** .

### <a name="write-your-python-code"></a>Napisz kod w języku Python

Projektant udostępnia początkowy skrypt punktu wejścia, który umożliwia edytowanie i wprowadzanie własnego kodu w języku Python. 

W tym przykładzie używasz Pandas do łączenia dwóch kolumn znajdujących się w zestawie danych samochodów, **cenie** i **mocy** , aby utworzyć nową kolumnę, **dolary na minutę**. Ta kolumna przedstawia, jak bardzo płacisz za każdą próbkę, która może być przydatną funkcją do podjęcia decyzji o tym, czy samochód jest dobrym rozwiązaniem dla pieniędzy. 

1. Wybierz moduł **skrypt języka Python** .

1. W okienku, które pojawia się po prawej stronie kanwy, zaznacz pole tekstowe **skrypt języka Python** .

1. Skopiuj i wklej następujący kod do pola tekstowego.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    Potok powinien wyglądać na poniższym obrazie:
    
    ![Wykonaj potok Python](media/how-to-designer-python/execute-python-pipeline.png)

    Skrypt punktu wejścia musi zawierać funkcję `azureml_main` . Istnieją dwa parametry funkcji, które są mapowane na dwa porty wejściowe dla modułu **uruchamiania skryptów języka Python** .

    Zwracana wartość musi być Pandas Dataframe. Można zwrócić do dwóch ramek dataframes jako dane wyjściowe modułu.
    
1. Prześlij potok.

Teraz masz zestaw danych z nową funkcją **dolarów/HP** , co może być przydatne w szkoleniu zalecenia dotyczącego samochodu. Jest to przykładowa Ekstrakcja funkcji i redukcja liczby wymiarów. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [zaimportować własne dane](how-to-designer-import-data.md) za Azure Machine Learning projektanta.
