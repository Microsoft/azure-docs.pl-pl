---
title: Przekształcanie danych w projektancie
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak importować i przekształcać dane w projektancie Azure Machine Learning, aby tworzyć własne zestawy danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: peterclu
ms.author: peterlu
ms.date: 06/28/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 70f5e17c4cc42201e9aa3d36c9937f6ceb9527d0
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880207"
---
# <a name="transform-data-in-azure-machine-learning-designer"></a>Przekształcanie danych w projektancie Azure Machine Learning


W tym artykule przedstawiono sposób przekształcania i zapisywania zestawów danych w projektancie Azure Machine Learning, dzięki czemu można przygotować własne dane do uczenia maszynowego.

Do przygotowania dwóch zestawów danych zostanie użyty przykładowy wynikowy [plik klasyfikacji dochodu dla "](./samples-designer.md) dane osobowe dla dorosłych": jeden zestaw danych, który zawiera informacje dotyczące spisu dla dorosłych tylko z Stany Zjednoczone i innego zestawu danych, który zawiera informacje o spisie od osób trzecich.

W tym artykule omówiono sposób wykonywania następujących zadań:

1. Przekształcanie zestawu danych w celu przygotowania go do szkoleń.
1. Wyeksportuj wyniki zestawów danych do magazynu danych.
1. Wyświetl wyniki.

Ta procedura jest warunkiem wstępnym artykułu [jak ponownie nauczenie modeli projektanta](how-to-retrain-designer.md) . W tym artykule dowiesz się, jak używać przekształconych zestawów danych do uczenia wielu modeli przy użyciu parametrów potoku.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="transform-a-dataset"></a>Przekształcanie zestawu danych

W tej sekcji dowiesz się, jak zaimportować przykładowy zestaw danych i podzielić dane na US and non-US DataSets. Aby uzyskać więcej informacji na temat importowania własnych danych do projektanta, zobacz [How to import Data](how-to-designer-import-data.md).

### <a name="import-data"></a>Importowanie danych

Aby zaimportować przykładowy zestaw danych, wykonaj następujące kroki.

1. Zaloguj się do <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.Azure.com</a>i wybierz obszar roboczy, z którym chcesz współpracować.

1. Przejdź do projektanta. Wybierz **łatwe do użycia moduły prekompilacji** , aby utworzyć nowy potok.

1. Wybierz domyślny obiekt docelowy obliczeń do uruchomienia potoku.

1. Na lewo od kanwy potoku jest paletą zestawów danych i modułów. Wybierz pozycję **zestawy danych**. Następnie zapoznaj się z sekcją **Samples** .

1. Przeciągnij i upuść **binarny zestaw danych klasyfikacji dochodów dla dorosłych** na kanwę.

1. Kliknij prawym przyciskiem myszy moduł zestawu danych **dochodów z spisu dla dorosłych** , a następnie wybierz polecenie **Wizualizuj**  >  **zestaw danych wyjściowych**

1. Użyj okna podglądu danych, aby eksplorować zestaw danych. Zwróć szczególną uwagę na wartości kolumny "rodzime kraje".

### <a name="split-the-data"></a>Dzielenie danych

W tej sekcji użyjesz [modułu Split Data](algorithm-module-reference/split-data.md) , aby identyfikować i dzielić wiersze, które zawierają "Stany Zjednoczone" w kolumnie "rodzime kraje". 

1. W palecie modułów z lewej strony kanwy rozwiń sekcję **transformacja danych** i Znajdź moduł **Split Data** .

1. Przeciągnij moduł **Split Data** na kanwę i upuść moduł poniżej modułu DataSet.

1. Połącz moduł DataSet z modułem **Split Data** .

1. Wybierz moduł **Split Data** .

1. W okienku Szczegóły modułu z prawej strony kanwy Ustaw **tryb dzielenia** na **wyrażenie regularne**.

1. Wprowadź **wyrażenie regularne**: `\"native-country" United-States` .

    Tryb **wyrażenia regularnego** testuje pojedynczą kolumnę dla wartości. Aby uzyskać więcej informacji na temat modułu Split Data (podział danych), zobacz [stronę referencyjną modułu algorytmu](algorithm-module-reference/split-data.md)powiązanego.

Potok powinien wyglądać następująco:

:::image type="content" source="./media/how-to-designer-transform-data/split-data.png"alt-text="Zrzut ekranu przedstawiający sposób konfigurowania potoku i modułu Split Data":::


## <a name="save-the-datasets"></a>Zapisz zestawy danych

Teraz, gdy potok został skonfigurowany tak, aby podzielił dane, musisz określić miejsce, w którym mają być utrwalane zestawy danych. Na potrzeby tego przykładu należy użyć modułu **eksportu danych** do zapisania zestawu danych w magazynie obiektów. Aby uzyskać więcej informacji na temat magazynów danych, zobacz [nawiązywanie połączenia z usługami Azure Storage](how-to-access-data.md)

1. W palecie modułów z lewej strony kanwy rozwiń sekcję **dane wejściowe i wyjściowe** i Znajdź moduł **Eksportowanie danych** .

1. Przeciągnij i upuść dwa moduły **eksportu danych** poniżej modułu **Split Data** .

1. Podłącz każdy port wyjściowy modułu **Split Data** do innego modułu **eksportowania** danych.

    Potok powinien wyglądać następująco:

    ![Zrzut ekranu przedstawiający sposób łączenia modułów eksportu danych](media/how-to-designer-transform-data/export-data-pipeline.png).

1. Wybierz moduł **eksportu danych** , który jest *połączony z* największym portem modułu **Split Data** .

    Kolejność portów wyjściowych dla modułu **Split Data** . Pierwszy port wyjściowy zawiera wiersze, w których wyrażenie regularne ma wartość true. W takim przypadku pierwszy port zawiera wiersze dla dochodów na podstawie Stanów Zjednoczonych, a drugi port zawiera wiersze dla dochodów niezwiązanych z stanem USA.

1. W okienku Szczegóły modułu z prawej strony kanwy ustaw następujące opcje:
    
    **Typ magazynu** danych: BLOB Storage platformy Azure

    **Magazyn** danych: wybierz istniejący magazyn danych lub wybierz pozycję "nowy magazyn danych", aby utworzyć go teraz.

    **Ścieżka**: `/data/us-income`

    **Format pliku**: CSV

    > [!NOTE]
    > W tym artykule przyjęto założenie, że masz dostęp do magazynu danych zarejestrowanego w bieżącym obszarze roboczym Azure Machine Learning. Aby uzyskać instrukcje dotyczące sposobu konfigurowania magazynu danych, zobacz [nawiązywanie połączenia z usługami Azure Storage](how-to-connect-data-ui.md#create-datastores).

    Jeśli nie masz magazynu danych, możesz utworzyć go teraz. Na przykład w tym artykule zostaną zapisane zestawy danych na domyślnym koncie usługi BLOB Storage skojarzonym z obszarem roboczym. Spowoduje to zapisanie zestawów danych do `azureml` kontenera w nowym folderze o nazwie `data` .

1.  Wybierz moduł **eksportu danych** połączony z najbardziej *odpowiednim* portem modułu **Split Data** .

1. W okienku Szczegóły modułu z prawej strony kanwy ustaw następujące opcje:
    
    **Typ magazynu** danych: BLOB Storage platformy Azure

    **Magazyn** danych: Wybierz ten sam magazyn danych, jak powyżej

    **Ścieżka**: `/data/non-us-income`

    **Format pliku**: CSV

1. Upewnij się, że moduł **eksportu danych** połączony z lewym portem **danych z podziałem** ma **ścieżkę** `/data/us-income` .

1. Upewnij się, że moduł **eksportu danych** połączony z właściwym portem ma **ścieżkę** `/data/non-us-income` .

    Potok i ustawienia powinny wyglądać następująco:
    
    ![Zrzut ekranu przedstawiający sposób konfigurowania modułów eksportu danych](media/how-to-designer-transform-data/us-income-export-data.png).

### <a name="submit-the-run"></a>Prześlij przebieg

Teraz, gdy potok jest skonfigurowany do dzielenia i eksportowania danych, Prześlij uruchomienie potoku.

1. W górnej części kanwy wybierz pozycję **Prześlij**.

1. W oknie dialogowym **Konfigurowanie uruchomienia potoku** wybierz pozycję **Utwórz nowy** , aby utworzyć eksperyment.

    Eksperymenty logicznie grupują pokrewne uruchomienia potoku. Jeśli ten potok zostanie uruchomiony w przyszłości, należy użyć tego samego eksperymentu na potrzeby rejestrowania i śledzenia.

1. Podaj nazwę eksperymentu opisowego, taką jak "Split-spis-Data".

1. Wybierz pozycję **Prześlij**.

## <a name="view-results"></a>Wyświetlanie wyników

Po zakończeniu potoku można wyświetlić wyniki, przechodząc do magazynu obiektów BLOB w Azure Portal. Możesz również wyświetlić pośrednicy wynik modułu **Split Data** , aby upewnić się, że dane zostały prawidłowo podzielone.

1. Wybierz moduł **Split Data** .

1. W okienku Szczegóły modułu z prawej strony kanwy wybierz pozycję dane **wyjściowe + dzienniki**. 

1. Wybierz ikonę wizualizacji ikona ![ Wizualizacja ](media/how-to-designer-transform-data/visualize-icon.png) obok pozycji **wyniki pozycję DataSet1**. 

1. Sprawdź, czy kolumna "kraj macierzysty" zawiera tylko wartość "Stany Zjednoczone".

1. Wybierz ikonę wizualizacji ikona ![ Wizualizacja ](media/how-to-designer-transform-data/visualize-icon.png) obok pozycji **wyniki DataSet2**. 

1. Sprawdź, czy kolumna "kraj macierzysty" nie zawiera wartości "Stany Zjednoczone".

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Pomiń tę sekcję, jeśli chcesz kontynuować pracę z częścią 2 tej metody, aby ponownie przeprowadzić [uczenie modeli przy użyciu programu Azure Machine Learning Designer](how-to-retrain-designer.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób przekształcania zestawu danych i zapisywania go w zarejestrowanej usłudze datastore.

Przejdź do następnej części tej serii How with z [modelami reciąganie z Azure Machine Learning Designer](how-to-retrain-designer.md) , aby używać przekształconych zestawów danych i parametrów potoku do uczenia modeli uczenia maszynowego.