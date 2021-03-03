---
title: Uruchamianie prognoz wsadowych za pomocą narzędzia Azure Machine Learning Designer
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć potok przewidywania wsadowego. Wdróż potok jako sparametryzowanej usługi sieci Web i Wyzwól ją z dowolnej biblioteki HTTP.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: dda47d3ff561d4d57045dbb28f8c411e193086d5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657378"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer"></a>Uruchamianie prognoz wsadowych za pomocą narzędzia Azure Machine Learning Designer


W tym artykule dowiesz się, jak utworzyć potok prognozowania wsadowego za pomocą projektanta. Funkcja prognozowanie wsadowe umożliwia ciągłe tworzenie dużych zestawów danych na żądanie przy użyciu usługi sieci Web, która może być wyzwalana z dowolnej biblioteki HTTP.

W tym instruktażu pouczysz się wykonywać następujące zadania:

> [!div class="checklist"]
> * Tworzenie i publikowanie potoku wnioskowania partii
> * Korzystanie z punktu końcowego potoku
> * Zarządzanie wersjami punktów końcowych

Aby dowiedzieć się, jak skonfigurować usługi wsadowe oceniania przy użyciu zestawu SDK, zobacz towarzyszące [instrukcje](./tutorial-pipeline-batch-scoring-classification.md).

## <a name="prerequisites"></a>Wymagania wstępne

Ta procedura polega na tym, że masz już potok szkoleniowy. Aby zapoznać się z przewodnikiem po stronie projektanta, wykonaj [jedną z części samouczka projektanta](tutorial-designer-automobile-price-train-score.md). 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-batch-inference-pipeline"></a>Tworzenie potoku wnioskowania partii

Potok szkoleniowy musi być uruchamiany co najmniej raz, aby można było utworzyć potok inferencing.

1. Przejdź do karty **Projektant** w obszarze roboczym.

1. Wybierz potok szkoleniowy łączący model, który ma być używany do prognozowania.

1. **Prześlij** potok.

    ![Prześlij potok](./media/how-to-run-batch-predictions-designer/run-training-pipeline.png)

Teraz, gdy potok szkoleniowy został uruchomiony, można utworzyć potok wnioskowania o partie.

1. Obok pozycji **Prześlij** wybierz nową listę rozwijaną **Utwórz potok wnioskowania**.

1. Wybierz **potok wnioskowania partii**.

    ![Tworzenie potoku wnioskowania partii](./media/how-to-run-batch-predictions-designer/create-batch-inference.png)
    
Wynik to domyślny potok wnioskowania o partię. 

### <a name="add-a-pipeline-parameter"></a>Dodaj parametr potoku

Aby utworzyć prognozy dotyczące nowych danych, można ręcznie połączyć inny zestaw danych w tym widoku roboczym potoku lub utworzyć parametr dla zestawu danych. Parametry umożliwiają zmianę zachowania procesu wsadowego inferencing w czasie wykonywania.

W tej sekcji utworzysz parametr dataset, aby określić inny zestaw danych do tworzenia prognoz.

1. Wybierz moduł DataSet.

1. Zostanie wyświetlone okienko po prawej stronie kanwy. W dolnej części okienka wybierz pozycję **Ustaw jako parametr potoku**.
   
    Wprowadź nazwę parametru lub Zaakceptuj wartość domyślną.

    > [!div class="mx-imgBorder"]
    > ![Ustaw zestaw danych jako parametr potoku](./media/how-to-run-batch-predictions-designer/set-dataset-as-pipeline-parameter.png)

## <a name="publish-your-batch-inference-pipeline"></a>Publikowanie potoku wnioskowania partii

Teraz wszystko jest gotowe do wdrożenia potoku wnioskowania. Spowoduje to wdrożenie potoku i udostępnienie go innym osobom.

1. Wybierz przycisk **Publikuj**.

1. W wyświetlonym oknie dialogowym Rozwiń listę rozwijaną dla **PipelineEndpoint** i wybierz pozycję **Nowy PipelineEndpoint**.

1. Podaj nazwę punktu końcowego i opcjonalny opis.

    W dolnej części okna dialogowego można zobaczyć skonfigurowany parametr z wartością domyślną identyfikatora zestawu danych używanego podczas szkolenia.

1. Kliknij pozycję **Opublikuj**.

![Publikowanie potoku](./media/how-to-run-batch-predictions-designer/publish-inference-pipeline.png)


## <a name="consume-an-endpoint"></a>Korzystanie z punktu końcowego

Teraz masz opublikowany potok z parametrem DataSet. Potok będzie używać nauczonego modelu utworzonego w potoku szkoleniowego do oceny zestawu danych, który jest udostępniany jako parametr.

### <a name="submit-a-pipeline-run"></a>Przesyłanie uruchomienia potoku 

W tej sekcji skonfigurujesz ręczny przebieg potoku i zmieniasz parametr potoku w celu oceny nowych danych. 

1. Po zakończeniu wdrażania przejdź do sekcji **punkty końcowe** .

1. Wybierz **punkty końcowe potoku**.

1. Wybierz nazwę utworzonego punktu końcowego.

![Łącze punktu końcowego](./media/how-to-run-batch-predictions-designer/manage-endpoints.png)

1. Wybierz pozycję **opublikowane potoki**.

    Na tym ekranie są wyświetlane wszystkie opublikowane potoki opublikowane w tym punkcie końcowym.

1. Wybierz opublikowany potok.

    Na stronie Szczegóły potoku zostanie wyświetlona Szczegółowa historia uruchamiania i informacje o parametrach połączenia dla potoku. 
    
1. Wybierz pozycję **Prześlij** , aby utworzyć ręczny przebieg potoku.

    ![Szczegóły potoku](./media/how-to-run-batch-predictions-designer/submit-manual-run.png)
    
1. Zmień parametr, aby użyć innego zestawu danych.
    
1. Wybierz pozycję **Prześlij** , aby uruchomić potok.

### <a name="use-the-rest-endpoint"></a>Korzystanie z punktu końcowego REST

Informacje o sposobach korzystania z punktów końcowych potoku i opublikowanego potoku znajdują się w sekcji **punkty końcowe** .

Punkt końcowy REST punktu końcowego potoku można znaleźć w panelu przegląd przebiegu. Wywołując punkt końcowy, korzystasz z domyślnego opublikowanego potoku.

Możesz również wykorzystać opublikowany potok na stronie **opublikowane potoki** . Wybierz opublikowany potok i możesz znaleźć punkt końcowy REST w panelu **Przegląd opublikowanych potoków** z prawej strony wykresu. 

Aby można było wywołać metodę REST, potrzebny jest nagłówek uwierzytelniania OAuth 2,0 typu okaziciela. Zapoznaj się z poniższą [sekcją samouczka](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) , aby uzyskać więcej szczegółów na temat konfigurowania uwierzytelniania do obszaru roboczego i wykonywania sparametryzowanych wywołań REST.

## <a name="versioning-endpoints"></a>Punkty końcowe wersji

Projektant przypisuje wersję do każdego kolejnego potoku, który publikuje w punkcie końcowym. Możesz określić wersję potoku, która ma zostać wykonana jako parametr w wywołaniu REST. Jeśli nie określisz numeru wersji, Projektant użyje domyślnego potoku.

Podczas publikowania potoku możesz wybrać opcję nowego domyślnego potoku dla tego punktu końcowego.

![Ustawianie potoku domyślnego](./media/how-to-run-batch-predictions-designer/set-default-pipeline.png)

Możesz również ustawić nowy potok domyślny na karcie **opublikowane potoki** w punkcie końcowym.

![Ustaw domyślny potok na stronie opublikowanego potoku](./media/how-to-run-batch-predictions-designer/set-new-default-pipeline.png)

## <a name="limitations"></a>Ograniczenia

Jeśli wprowadzisz modyfikacje potoku szkoleniowego, należy ponownie przesłać potok szkoleniowy, **zaktualizować**  potok wnioskowania i ponownie uruchomić potok wnioskowania.

Należy pamiętać, że tylko modele zostaną zaktualizowane w potoku wnioskowania, podczas gdy transformacja danych nie zostanie zaktualizowana.

Aby użyć zaktualizowanej transformacji w potoku wnioskowania, należy zarejestrować dane wyjściowe transformacji modułu transformacji jako zestaw danych.

![Zrzut ekranu przedstawiający sposób rejestrowania zestawu danych transformacji](./media/how-to-run-batch-predictions-designer/register-transformation-dataset.png)

Następnie ręcznie Zastąp **element TD-** module w potoku wnioskowania z zarejestrowanym zestawem danych.

![Zrzut ekranu przedstawiający sposób zastąpienia modułu transformacji](./media/how-to-run-batch-predictions-designer/replace-td-module-batch-inference-pipeline.png)

Następnie można przesłać potok wnioskowania ze zaktualizowanym modelem i transformację oraz opublikować.

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z [samouczkiem](tutorial-designer-automobile-price-train-score.md) projektanta, aby nauczyć i wdrożyć model regresji.
''
