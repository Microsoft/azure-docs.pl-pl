---
title: 'Samouczek: Tworzenie modelu predykcyjnego za pomocą zautomatyzowanej ML (część 1 z 2)'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak tworzyć i wdrażać zautomatyzowane modele uczenia maszynowego, aby można było użyć najlepszego modelu do przewidywania wyników w programie Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 6dc99d58f15653e9d3f991622de3bb3388690459
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814809"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-by-using-automated-machine-learning-part-1-of-2"></a>Samouczek: integracja Power BI — Tworzenie modelu predykcyjnego przy użyciu funkcji automatycznego uczenia maszynowego (część 1 z 2)

W części 1 tego samouczka nauczysz się i wdrożyć model uczenia maszynowego. Używasz automatycznej uczenia maszynowego (ML) w Azure Machine Learning Studio.  W części 2 zostanie użyty model najlepszego wykonywania do przewidywania wyników w programie Microsoft Power BI.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Utwórz klaster obliczeniowy Azure Machine Learning.
> * Utwórz zestaw danych.
> * Utwórz zautomatyzowany przebieg uczenia maszynowego.
> * Wdróż najlepszy model do punktu końcowego oceniania w czasie rzeczywistym.


Istnieją trzy sposoby tworzenia i wdrażania modelu, który będzie używany w Power BI.  W tym artykule opisano "opcja C: uczenie i wdrażanie modeli przy użyciu funkcji automatycznego uczenia maszynowego w programie Studio".  Ta opcja to środowisko tworzenia bez kodu. Całkowicie automatyzuje przygotowanie danych i uczenie modeli. 

Zamiast tego można użyć jednej z innych opcji:

* [Opcja A: uczenie i wdrażanie modeli przy użyciu notesów Jupyter](tutorial-power-bi-custom-model.md). W tym środowisku tworzenia kodu używane są notesy Jupyter hostowane w Azure Machine Learning Studio.
* [Opcja B: uczenie i wdrażanie modeli przy użyciu narzędzia Azure Machine Learning Designer](tutorial-power-bi-designer-model.md). W tym środowisku tworzenia kodu jest wykorzystywany interfejs użytkownika typu "przeciągnij i upuść".

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji, możesz skorzystać z [bezpłatnej wersji próbnej](https://aka.ms/AMLFree). 
- Obszar roboczy usługi Azure Machine Learning. Jeśli nie masz jeszcze obszaru roboczego, zapoznaj się z tematem [tworzenie Azure Machine Learning obszarów roboczych i zarządzanie nimi](./how-to-manage-workspace.md#create-a-workspace).

## <a name="create-a-compute-cluster"></a>Tworzenie klastra obliczeniowego

Automatyczne Uczenie maszynowe pociąga za dużo modeli uczenia maszynowego, aby znaleźć "najlepszy" algorytm i parametry. Azure Machine Learning parallelizes na uruchomienie szkolenia modelu w ramach klastra obliczeniowego.

Aby rozpocząć, w [Azure Machine Learning Studio](https://ml.azure.com), w menu po lewej stronie wybierz pozycję **Oblicz**. Otwórz kartę **Klastry obliczeniowe** . Następnie wybierz pozycję **Nowy**:

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia klastra obliczeniowego.":::

Na stronie **Tworzenie klastra obliczeniowego** :

1. Wybierz rozmiar maszyny wirtualnej. W tym samouczku maszyna **Standard_D11_v2a** jest w prawidłowym zakresie.
1. Wybierz pozycję **Dalej**.
1. Podaj prawidłową nazwę obliczeniową.
1. Zachowaj **minimalną liczbę węzłów** w `0` .
1. Zmień **maksymalną liczbę węzłów** na `4` .
1. Wybierz przycisk **Utwórz**.

Stan klastra zmieni się na **Tworzenie**.

>[!NOTE]
> Nowy klaster ma 0 węzłów, więc nie są naliczane żadne koszty obliczeniowe. Opłaty są naliczane tylko wtedy, gdy uruchamiane jest zadanie automatycznego uczenia maszynowego. Klaster jest skalowany z powrotem do 0 automatycznie po 120 sekund czasu bezczynności.


## <a name="create-a-dataset"></a>Utwórz zestaw danych

W tym samouczku użyjesz [zestawu danych cukrzycą](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Ten zestaw danych jest dostępny w [usłudze Azure Open DataSets](https://azure.microsoft.com/services/open-datasets/).

Aby utworzyć zestaw danych, w menu po lewej stronie wybierz pozycję **zestawy** danych. Następnie wybierz pozycję **Utwórz zestaw danych**. Zobaczysz następujące opcje:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia nowego zestawu danych.":::

Wybierz pozycję **z otwartych zestawów danych**. Następnie na stronie **Tworzenie zestawu danych na podstawie otwartych zestawów DataSets** :

1. Użyj paska wyszukiwania, aby znaleźć *cukrzycą*.
1. Wybierz **przykład: cukrzycą**.
1. Wybierz pozycję **Dalej**.
1. Nazwij zestaw danych *cukrzycą*.
1. Wybierz przycisk **Utwórz**.

Aby eksplorować dane, wybierz zestaw danych, a następnie wybierz polecenie **Eksploruj**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Zrzut ekranu przedstawiający sposób eksplorowania zestawu danych.":::

Dane zawierają 10 bazowych zmiennych wejściowych, takich jak wiek, płeć, indeks masy ciała, średnie ciśnienie krwi oraz sześć pomiarów surowicy krwi. Ma także jedną zmienną docelową o nazwie **Y**. Ta zmienna docelowa jest miarą ilościową cukrzycą w jednym roku po linii bazowej.

## <a name="create-an-automated-machine-learning-run"></a>Tworzenie automatycznego przebiegu uczenia maszynowego

W [Azure Machine Learning Studio](https://ml.azure.com), w menu po lewej stronie wybierz pozycję **zautomatyzowany ml**. Następnie wybierz kolejno pozycje **Nowy zautomatyzowany przebiegu ml**:

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia nowego automatycznego przebiegu uczenia maszynowego.":::

Następnie wybierz utworzony wcześniej zestaw danych **cukrzycą** . Następnie wybierz pozycję **dalej**:

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania zestawu danych.":::
 
Na stronie **Konfigurowanie przebiegu** :

1. W obszarze **Nazwa eksperymentu** wybierz pozycję **Utwórz nowy**.
1. Nazwij eksperyment.
1. W polu **kolumna docelowa** wybierz wartość **Y**.
1. W polu **Wybierz klaster obliczeniowy** wybierz utworzony wcześniej klaster obliczeniowy. 

Ukończony formularz powinien wyglądać następująco:

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="Zrzut ekranu przedstawiający sposób konfigurowania automatycznej uczenia maszynowego.":::

Na koniec wybierz zadanie uczenia maszynowego. W tym przypadku zadanie jest **regresją**:

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="Zrzut ekranu przedstawiający sposób konfigurowania zadania.":::

Wybierz pozycję **Zakończ**.

> [!IMPORTANT]
> Automatyczne Uczenie maszynowe zajmie około 30 minut na zakończenie szkolenia modeli 100.

## <a name="deploy-the-best-model"></a>Wdróż najlepszy model

Po zakończeniu automatycznego uczenia maszynowego można zobaczyć wszystkie modele uczenia maszynowego, które zostały wypróbowane, wybierając kartę **modele** . Modele są uporządkowane według wydajności; najpierw jest pokazywany model najlepiej wykonujący. Po wybraniu najlepszego modelu przycisk **Wdróż** jest włączony:

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="Zrzut ekranu przedstawiający listę modeli.":::

Wybierz pozycję **Wdróż** , aby otworzyć okno **Wdróż model** :

1. Nazwij model Service *cukrzycą-model*.
1. Wybierz **Azure Container Service**.
1. Wybierz pozycję **Wdróż**.

Powinien zostać wyświetlony komunikat informujący o pomyślnym wdrożeniu modelu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób uczenia i wdrażania modelu uczenia maszynowego przy użyciu funkcji automatycznego uczenia maszynowego. W następnym samouczku dowiesz się, jak używać tego modelu (SCORE) w Power BI.

> [!div class="nextstepaction"]
> [Samouczek: korzystanie z modelu w Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
