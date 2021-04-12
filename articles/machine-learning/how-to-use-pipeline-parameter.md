---
title: Używanie parametrów potoku w projektancie do tworzenia uniwersalnych potoków
titleSuffix: Azure Machine Learning
description: Jak używać parametrów potoku w programie Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 04/09/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 30ae737a170c337fe6be51521aeb358cdcebd44b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107344"
---
# <a name="use-pipeline-parameters-in-the-designer-to-build-versatile-pipelines"></a>Używanie parametrów potoku w projektancie do tworzenia uniwersalnych potoków

Użyj parametrów potoku do kompilowania elastycznych potoków w projektancie. Parametry potoku umożliwiają dynamiczne ustawianie wartości w czasie wykonywania w celu hermetyzacji logiki potoku i ponownego użycia zasobów.

Parametry potoku są szczególnie przydatne podczas ponownego przesyłania potoku, [modeli szkoleń](how-to-retrain-designer.md)lub [wykonywania prognoz wsadowych](how-to-run-batch-predictions-designer.md).

W tym artykule dowiesz się, jak wykonać następujące czynności:

> [!div class="checklist"]
> * Tworzenie parametrów potoku
> * Usuwanie parametrów potoku i zarządzanie nimi
> * Wyzwalanie uruchomienia potoku podczas dopasowywania parametrów potoku

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

* Aby zapoznać się z przewodnikiem po wprowadzeniu do projektanta, Ukończ [samouczek projektanta](tutorial-designer-automobile-price-train-score.md). 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-pipeline-parameter"></a>Utwórz parametr potoku

Istnieją trzy sposoby tworzenia parametru potoku w projektancie:
- Utwórz parametr potoku w panelu Ustawienia i powiąż go z modułem.
- Podnieś poziom parametru modułu do parametru potoku.
- Podnieś poziom zestawu danych do parametru potoku

> [!NOTE]
> Parametry potoku obsługują tylko podstawowe typy danych `int` , takie jak, `float` i `string` .

### <a name="option-1-create-a-pipeline-parameter-in-the-settings-panel"></a>Opcja 1. Tworzenie parametru potoku w panelu ustawień

W tej sekcji utworzysz parametr potoku w panelu ustawień.

W tym przykładzie utworzysz parametr potoku, który definiuje, jak potok wypełnia brakujące dane przy użyciu **nieoczyszczonego modułu danych** .

1. Obok nazwy roboczej potoku wybierz **ikonę koła zębatego** , aby otworzyć panel **Ustawienia** .

1. W sekcji **Parametry potoku** wybierz **+** ikonę.

1.  Wprowadź nazwę parametru i wartości domyślnej. 

    Na przykład wprowadź `replace-missing-value` nazwę parametru, a `0` jako wartość domyślną.

   ![Zrzut ekranu pokazujący sposób tworzenia parametru potoku](media/how-to-use-pipeline-parameter/create-pipeline-parameter.png)


Po utworzeniu parametru potoku należy [go dołączyć do parametru modułu](#attach-module-parameter-to-pipeline-parameter) , który ma zostać dynamicznie ustawiony.

### <a name="option-2-promote-a-module-parameter"></a>Opcja 2: podwyższanie poziomu parametru modułu

Najprostszym sposobem utworzenia parametru potoku dla wartości modułu jest podwyższenie poziomu parametru modułu. Wykonaj następujące kroki, aby podwyższyć poziom parametru modułu do parametru potoku:

1. Wybierz moduł, do którego chcesz dołączyć parametr potoku.
1. W okienku szczegółów modułu MouseOver parametr, który chcesz określić.
1. Wybierz przycisk wielokropka (**...**), który zostanie wyświetlony.
1. Wybierz pozycję **Dodaj do parametru potoku**.

    ![Zrzut ekranu pokazujący sposób promowania parametru modułu w potoku parametr1](media/how-to-use-pipeline-parameter/promote-module-para-to-pipeline-para.png)

1. Wprowadź nazwę parametru i wartość domyślną.
1. Wybierz pozycję **Zapisz**

Teraz możesz określić nowe wartości dla tego parametru w dowolnym momencie przesyłania tego potoku.

### <a name="option-3-promote-a-dataset-to-a-pipeline-parameter"></a>Opcja 3: podwyższanie poziomu zestawu danych do parametru potoku

Jeśli chcesz przesłać potok z zmiennymi zestawami danych, musisz podwyższyć poziom zestawu danych do parametru potoku:

1. Wybierz zestaw danych, który ma zostać przekształcony w parametr potoku.

1. W panelu Szczegóły zestawu danych zaznacz pozycję **Ustaw jako parametr potoku**.

   ![Zrzut ekranu pokazujący sposób ustawiania zestawu danych jako parametru potoku](media/how-to-use-pipeline-parameter/set-dataset-as-pipeline-parameter.png)

Teraz można określić inny zestaw danych przy użyciu parametru potoku przy następnym uruchomieniu potoku.

## <a name="attach-and-detach-module-parameter-to-pipeline-parameter"></a>Dołączanie i odłączanie parametru modułu do parametru potoku 

W tej sekcji dowiesz się, jak dołączać i odłączać parametr modułu do parametru potoku.

### <a name="attach-module-parameter-to-pipeline-parameter"></a>Dołącz parametr modułu do parametru potoku

Możesz dołączyć te same parametry modułu zduplikowanych modułów do tego samego parametru potoku, jeśli chcesz zmienić wartość w tym samym czasie podczas wyzwalania uruchomienia potoku.

W poniższym przykładzie użyto zduplikowanego nieprawidłowego modułu **danych** . Dla każdego **nieczystego modułu danych** Dołącz **wartość zastępczą** do parametru potoku **Zastąp niebrakującą wartość**:

1. Wybierz **czysty moduł danych** .

1. W okienku szczegółów modułu z prawej strony kanwy Ustaw **Tryb czyszczenia** na "Niestandardowa wartość podstawiania".

1. MouseOver pole **wartości zastępczej** .

1. Wybierz przycisk wielokropka (**...**), który zostanie wyświetlony.

1. Wybierz parametr potoku `replace-missing-value` .

   ![Zrzut ekranu pokazujący sposób dołączania parametru potoku](media/how-to-use-pipeline-parameter/attach-replace-value-to-pipeline-parameter.png)

Pole **wartości zastępczej** zostało pomyślnie dołączone do parametru potoku. 


### <a name="detach-module-parameter-to-pipeline-parameter"></a>Odłączanie parametru modułu do parametru potoku

Po dołączeniu **wartości zastępczej** do parametru potoku nie można wykonać akcji.

Parametr modułu można odłączyć do parametru potoku, klikając wielokropek (**...**) obok parametru modułu, a następnie wybrać opcję **Odłącz od parametru potoku**.

 ![Zrzut ekranu pokazujący, że nie można wykonać akcji po dołączeniu do parametru potoku](media/how-to-use-pipeline-parameter/non-actionable-module-parameter.png)

## <a name="update-and-delete-pipeline-parameters"></a>Aktualizowanie i usuwanie parametrów potoku

W tej sekcji dowiesz się, jak aktualizować i usuwać parametry potoku.

### <a name="update-pipeline-parameters"></a>Aktualizowanie parametrów potoku

Wykonaj następujące kroki, aby zaktualizować parametr potoku modułu:

1. W górnej części kanwy wybierz ikonę koła zębatego.
1. W sekcji **Parametry potoku** można wyświetlić i zaktualizować nazwę oraz wartość domyślną dla wszystkich parametrów potoku.

### <a name="delete-a-dataset-pipeline-parameter"></a>Usuń parametr potoku DataSet

Aby usunąć parametr potoku zestawu danych, wykonaj następujące kroki:

1. Wybierz moduł DataSet.
1. Usuń zaznaczenie opcji **Ustaw jako parametr potoku**.


### <a name="delete-module-pipeline-parameters"></a>Usuń parametry potoku modułu

Wykonaj następujące kroki, aby usunąć parametr potoku modułu:

1. W górnej części kanwy wybierz ikonę koła zębatego.

1. Wybierz wielokropek (**...**) obok parametru potoku.

    Ten widok przedstawia moduły, do których jest dołączony parametr potoku.

    ![Zrzut ekranu pokazujący bieżący parametr potoku zastosowany do modułu](media/how-to-use-pipeline-parameter/delete-pipeline-parameter2.png)

1. Wybierz pozycję **Usuń parametr** , aby usunąć parametr potoku.

    > [!NOTE]
    > Usunięcie parametru potoku spowoduje odłączenie wszystkich dołączonych parametrów modułu, a wartość odłączonych parametrów modułu zachowuje bieżącą wartość parametru potoku.     

## <a name="trigger-a-pipeline-run-with-pipeline-parameters"></a>Wyzwalanie uruchomienia potoku przy użyciu parametrów potoku 

W tej sekcji dowiesz się, jak przesłać uruchomienie potoku podczas ustawiania parametrów potoku.

### <a name="resubmit-a-pipeline-run"></a>Prześlij ponownie uruchomienie potoku

Po przesłaniu potoku z parametrami potoku można ponownie przesłać potok z innymi parametrami:

1. Przejdź do strony szczegółów potoku. W oknie **Przegląd uruchomienia potoku** można sprawdzić bieżące parametry i wartości potoku.

1. Wybierz pozycję **Prześlij ponownie**.
1. W **uruchomieniu potoku instalacji** Określ nowe parametry potoku. 

![Zrzut ekranu pokazujący ponowne przesyłanie potoku z parametrami potoku](media/how-to-use-pipeline-parameter/resubmit-pipeline-run.png)

### <a name="use-published-pipelines"></a>Użyj opublikowanych potoków

Możesz również opublikować potok, aby użyć jego parametrów potoku. **Opublikowany potok** to potok, który został wdrożony w zasobie obliczeniowym, które mogą być wywoływane przez aplikacje klienckie za pośrednictwem punktu końcowego REST.

Opublikowane punkty końcowe są szczególnie przydatne w scenariuszach przeszkolenia i przewidywania wsadowych. Aby uzyskać więcej informacji, zobacz [jak ponownie szkolić modele w projektancie](how-to-retrain-designer.md) lub [uruchamiać przewidywania wsadowe w projektancie](how-to-run-batch-predictions-designer.md).

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób tworzenia parametrów potoku w projektancie. Następnie zapoznaj się z tematem jak używać parametrów potoku do ponownego [uczenia modeli](how-to-retrain-designer.md) lub wykonywania [prognoz wsadowych](how-to-run-batch-predictions-designer.md).

Możesz też dowiedzieć się, jak [programowo używać potoków z zestawem SDK](how-to-deploy-pipelines.md).
