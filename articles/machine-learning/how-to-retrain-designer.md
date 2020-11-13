---
title: Pouczenie modeli przy użyciu narzędzia Azure Machine Learning Designer
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak ponownie przeszkolić modele uczenia maszynowego z opublikowanymi potokami w programie Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 04/06/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: d8ef4d9f768d6fdcf976c9317d1abec3d4533824
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94554805"
---
# <a name="retrain-models-with-azure-machine-learning-designer"></a>Ponowne uczenie modeli za pomocą narzędzia Azure Machine Learning Designer


W tym artykule z tego artykułu dowiesz się, jak za pomocą narzędzia Azure Machine Learning Designer ponownie przeprowadzić uczenie modelu uczenia maszynowego. Przy użyciu opublikowanych potoków można zautomatyzować przepływ pracy i ustawić parametry w celu uczenia modelu na nowych danych. 

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Uczenie modelu uczenia maszynowego.
> * Utwórz parametr potoku.
> * Opublikuj potok szkoleniowy.
> * Ponownie przeszkol model przy użyciu nowych parametrów.

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning
* Ukończ część 1 tej serii How to-to-to-to-the [-to-](how-to-designer-transform-data.md) Series

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

W tym artykule założono również, że masz pewną wiedzę na temat kompilowania potoków w projektancie. Aby zapoznać się z przewodnikiem, Ukończ [samouczek](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Przykładowy potok

Potok użyty w tym artykule to zmieniona wersja przykładowego [przewidywania dochodu](samples-designer.md#classification) potoku na stronie głównej projektanta. Potok używa modułu [Importuj dane](algorithm-module-reference/import-data.md) zamiast przykładowego zestawu danych, aby pokazać, jak szkolić modele przy użyciu własnych danych.

![Zrzut ekranu pokazujący zmodyfikowany potok przykładu z polem wyróżniania modułu Importuj dane](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Tworzenie parametru potoku

Utwórz parametry potoku, aby dynamicznie ustawiać zmienne w czasie wykonywania. Na potrzeby tego przykładu zmienisz ścieżkę danych szkolenia z ustalonej wartości na parametr, aby można było ponownie nauczyć model na różnych danych.

1. Wybierz moduł **Importuj dane** .

    > [!NOTE]
    > W tym przykładzie do uzyskiwania dostępu do danych w zarejestrowaniu datastore jest wykorzystywany moduł import danych. Można jednak wykonać podobne kroki, jeśli używasz alternatywnych wzorców dostępu do danych.

1. W okienku szczegółów modułu z prawej strony kanwy wybierz źródło danych.

1. Wprowadź ścieżkę do danych. Możesz również wybrać pozycję **Przeglądaj ścieżkę** , aby przeglądać drzewo plików. 

1. MouseOver pole **ścieżki** i wybierz wielokropek powyżej wyświetlonego pola **ścieżki** .

    ![Zrzut ekranu pokazujący sposób tworzenia parametru potoku](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. Wybierz pozycję **Dodaj do parametru potoku**.

1. Podaj nazwę parametru i wartość domyślną.

   > [!NOTE]
   > Parametry potoku można sprawdzić i **edytować, wybierając ikonę koła** zębatego obok tytułu wersji roboczej potoku. 

1. Wybierz pozycję **Zapisz**.

1. Prześlij uruchomienie potoku.

## <a name="find-a-trained-model"></a>Znajdź model przeszkolony

Projektant zapisuje wszystkie dane wyjściowe potoku, w tym modele przeszkolone, do domyślnego konta magazynu obszaru roboczego. Możesz również uzyskać dostęp do przeszkolonych modeli bezpośrednio w projektancie:

1. Poczekaj na zakończenie działania potoku.
1. Wybierz moduł **Train Model** (Trenowanie modelu).
1. W okienku Szczegóły modułu z prawej strony kanwy wybierz pozycję dane **wyjściowe + dzienniki**.
1. Model można znaleźć w innych danych **wyjściowych** wraz z uruchamianiem dzienników.
1. Alternatywnie wybierz ikonę **Wyświetl dane wyjściowe** . W tym miejscu możesz wykonać instrukcje w oknie dialogowym, aby przejść bezpośrednio do magazynu danych. 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu pokazujący sposób pobierania przeszkolonego modelu](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="publish-a-training-pipeline"></a>Publikowanie potoku szkoleniowego

Opublikuj potok w punkcie końcowym potoku, aby łatwo ponownie wykorzystać potoki w przyszłości. Punkt końcowy potoku tworzy punkt końcowy REST w celu wywołania potoku w przyszłości. W tym przykładzie punkt końcowy potoku umożliwia ponowne wykorzystanie potoku w celu ponownego nauczenia modelu na różnych danych.

1. Wybierz pozycję **Publikuj** powyżej kanwy projektanta.
1. Wybierz lub Utwórz punkt końcowy potoku.

   > [!NOTE]
   > Można opublikować wiele potoków w jednym punkcie końcowym. Każdy potok w danym punkcie końcowym ma numer wersji, który można określić podczas wywoływania punktu końcowego potoku.

1. Kliknij pozycję **Opublikuj**.

## <a name="retrain-your-model"></a>Ponowne uczenie modelu

Teraz, gdy masz opublikowany potok szkoleniowy, możesz go użyć do ponownego nauczenia modelu pod kątem nowych danych. Można przesyłać przebiegi z punktu końcowego potoku w obszarze roboczym Studio lub programowo.

### <a name="submit-runs-by-using-the-studio-portal"></a>Przesyłanie przebiegów przy użyciu portalu Studio

Wykonaj następujące kroki, aby przesłać sparametryzowany punkt końcowy potoku z portalu Studio:

1. Przejdź do strony **punkty końcowe** w obszarze roboczym Studio.
1. Wybierz kartę **punkty końcowe potoku** . Następnie wybierz punkt końcowy potoku.
1. Wybierz kartę **opublikowane potoki** . Następnie wybierz wersję potoku, która ma zostać uruchomiona.
1. Wybierz pozycję **Prześlij**.
1. W oknie dialogowym Konfiguracja można określić wartości parametrów dla uruchomienia. W tym przykładzie należy zaktualizować ścieżkę danych, aby szkolić model przy użyciu zestawu danych innego niż US.

![Zrzut ekranu pokazujący sposób skonfigurowania sparametryzowanego uruchomienia potoku w projektancie](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Przesyłanie przebiegów przy użyciu kodu

Punkt końcowy REST opublikowanego potoku można znaleźć w panelu przegląd. Wywołując punkt końcowy, możesz ponownie przeprowadzić uczenie opublikowanego potoku.

Aby można było wywołać metodę REST, potrzebny jest nagłówek uwierzytelniania OAuth 2,0 typu okaziciela. Aby uzyskać informacje o konfigurowaniu uwierzytelniania do obszaru roboczego i tworzeniu sparametryzowanego wywołania REST, zobacz [Tworzenie potoku Azure Machine Learning na potrzeby oceniania partii](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób tworzenia sparametryzowanego punktu końcowego potoku szkolenia przy użyciu narzędzia Projektant.

Aby zapoznać się z kompletnym przewodnikiem dotyczącym wdrażania modelu w celu wykonywania prognoz, zobacz [samouczek projektanta](tutorial-designer-automobile-price-train-score.md) , aby nauczyć i wdrożyć model regresji.
