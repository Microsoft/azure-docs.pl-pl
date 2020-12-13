---
title: 'Samouczek: Tworzenie modelu predykcyjnego za pomocą zautomatyzowanej ML (część 1 z 2)'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak tworzyć i wdrażać zautomatyzowane modele ML, dzięki czemu możesz korzystać z najlepszego modelu do przewidywania wyników w programie Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 897f493edf6ccdebb25c201e8e4f9babfb0754c5
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370282"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-using-automated-machine-learning-part-1-of-2"></a>Samouczek: integracja Power BI — Tworzenie modelu predykcyjnego za pomocą zautomatyzowanej uczenia maszynowego (część 1 z 2)

W pierwszej części tego samouczka nauczysz się i wdrożyć model uczenia maszynowego przy użyciu funkcji automatycznego uczenia maszynowego w programie Azure Machine Learning Studio.  W części 2 zostanie następnie użyty model najlepszego działania do przewidywania wyników w programie Microsoft Power BI.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Tworzenie klastra obliczeniowego Azure Machine Learning
> * Utwórz zestaw danych
> * Tworzenie zautomatyzowanego przebiegu ML
> * Wdróż najlepszy model do punktu końcowego oceniania w czasie rzeczywistym


Istnieją trzy różne sposoby tworzenia i wdrażania modelu, który będzie używany w Power BI.  W tym artykule omówiono opcję C: uczenie i wdrażanie modeli przy użyciu zautomatyzowanej ML w Studio.  Ta opcja przedstawia środowisko tworzenia bez kodu, które w pełni automatyzuje przygotowanie danych i szkolenia modeli. 

Zamiast tego można użyć:

* [Opcja A: uczenie i wdrażanie modeli przy użyciu notesów](tutorial-power-bi-custom-model.md) — środowisko tworzenia po raz pierwszy przy użyciu notesów Jupyter hostowanych w programie Azure Machine Learning Studio.
* [Opcja B: uczenie i wdrażanie modeli przy użyciu narzędzia Projektant](tutorial-power-bi-designer-model.md)— środowisko tworzenia z niską ilością kodu przy użyciu narzędzia Projektant (interfejs użytkownika typu "przeciągnij i upuść").

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure ([dostępna jest bezpłatna wersja próbna](https://aka.ms/AMLFree)). 
- Obszar roboczy usługi Azure Machine Learning. Jeśli jeszcze nie masz obszaru roboczego, postępuj zgodnie z instrukcjami [tworzenia obszar roboczy usługi Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).

## <a name="create-compute-cluster"></a>Tworzenie klastra obliczeniowego

Automatyczna ML pociąga za siebie wiele różnych modeli uczenia maszynowego, aby znaleźć "najlepszy" algorytm i parametry. Azure Machine Learning parallelizes wykonywanie szkolenia modelu przez klaster obliczeniowy.

W [Azure Machine Learning Studio](https://ml.azure.com)wybierz pozycję **obliczenia** w menu po lewej stronie, a następnie kartę **Klastry obliczeniowe** . Wybierz pozycję **Nowy**:

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia klastra obliczeniowego":::

Na ekranie **Tworzenie klastra obliczeniowego** :

1. Wybierz rozmiar maszyny wirtualnej (na potrzeby tego samouczka `Standard_D11_v2` komputer jest prawidłowo).
1. Wybierz pozycję **Dalej**
1. Podaj prawidłową nazwę obliczeniową
1. Zachowaj **minimalną liczbę węzłów** w 0
1. Zmień **maksymalną liczbę węzłów** na 4
1. Wybierz pozycję **Utwórz**

Można zobaczyć, że stan klastra został zmieniony na **Tworzenie**.

>[!NOTE]
> Po utworzeniu klastra będzie on zawierał 0 węzłów, co oznacza, że nie są naliczane żadne koszty obliczeniowe. Opłaty są naliczane tylko wtedy, gdy uruchamiane jest zadanie zautomatyzowanej ML. Po upływie 120 sekund czasu bezczynności klaster będzie skalowany z powrotem do zera.


## <a name="create-dataset"></a>Utworzenie zestawu danych

W tym samouczku użyjesz [zestawu danych cukrzycą](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html), który jest dostępny na [platformie Azure Open DataSets](https://azure.microsoft.com/services/open-datasets/).

Aby utworzyć zestaw danych, wybierz opcję **zestawy** danych po lewej stronie, a następnie pozycję **Utwórz zestaw danych** — zostaną wyświetlone następujące opcje:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia nowego zestawu danych":::

Wybierz pozycję **z otwartych zestawów** danych, a następnie w oknie **Tworzenie DataSet z otwartej grupy zestawów** :

1. Wyszukaj *cukrzycą* przy użyciu paska wyszukiwania
1. Wybierz **przykład: cukrzycą**
1. Wybierz pozycję **Dalej**
1. Podaj nazwę zestawu danych — *cukrzycą*
1. Wybierz pozycję **Utwórz**

Możesz eksplorować dane, wybierając zestaw danych, a następnie **Eksploruj**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Zrzut ekranu przedstawiający sposób eksplorowania zestawu danych":::

Dane zawierają 10 zmiennych wejściowych linii bazowej (takich jak wiek, płeć, indeks masy ciała, średnie ciśnienie krwi oraz sześć pomiarów surowicy krwi) i jedna zmienna docelowa o nazwie **Y** (miara ilościowa cukrzycą z postępem roku po linii bazowej).

## <a name="create-automated-ml-run"></a>Utwórz zautomatyzowany przebieg ML

W [Azure Machine Learning Studio](https://ml.azure.com) wybierz opcję **zautomatyzowany ml** w menu po lewej stronie, a następnie **nowe automatyczne uruchomienie ml**:

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia nowego zautomatyzowanego przebiegu ML":::

Następnie wybierz utworzony wcześniej zestaw danych **cukrzycą** , a następnie wybierz pozycję **Next (dalej**):

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania zestawu danych":::
 
Na ekranie **Konfigurowanie uruchamiania** :

1. W obszarze **Nazwa eksperymentu** wybierz pozycję **Utwórz nowy** .
1. Podaj nazwę eksperymentu
1. W polu kolumna docelowa wybierz pozycję **Y** .
1. W polu **Wybierz klaster obliczeniowy** wybierz utworzony wcześniej klaster obliczeniowy. 

Ukończony formularz powinien wyglądać podobnie do:

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="Zrzut ekranu przedstawiający sposób konfigurowania zautomatyzowanej ML":::

Na koniec należy wybrać zadanie uczenia maszynowego, które jest **regresją**:

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="Zrzut ekranu przedstawiający sposób konfigurowania zadania":::

Wybierz pozycję **Zakończ**.

> [!IMPORTANT]
> Ukończenie szkolenia do 100 różnych modeli zajmie około 30 minut.

## <a name="deploy-the-best-model"></a>Wdróż najlepszy model

Po zakończeniu zautomatyzowanego uruchomienia ML można zobaczyć listę wszystkich modeli uczenia maszynowego, które zostały wypróbowane, wybierając kartę **modele** . Modele są uporządkowane w kolejności wydajności — najpierw będzie widoczny model najlepiej wykonujący. Po wybraniu najlepszego modelu przycisk **Wdróż** zostanie włączony:

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="Zrzut ekranu przedstawiający listę modeli":::

Wybranie opcji **Wdróż** spowoduje wyświetlenie ekranu **wdrażania modelu** :

1. Podaj nazwę usługi modelu — Użyj **cukrzycą-model**
1. Wybierz **Azure Container Service**
1. Wybierz pozycję **Wdróż**

Powinien zostać wyświetlony komunikat z informacją o tym, że model został pomyślnie wdrożony.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób uczenia i wdrażania modelu uczenia maszynowego przy użyciu zautomatyzowanej ML. W następnym samouczku przedstawiono sposób użycia (wyniku) tego modelu z Power BI.

> [!div class="nextstepaction"]
> [Samouczek: korzystanie z modelu w Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
