---
title: 'Samouczek: uczenie modelu przy użyciu funkcji automatycznego uczenia maszynowego'
description: Samouczek dotyczący sposobu uczenia modelu uczenia maszynowego bez kodu w usłudze Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: f3b0c5f1487951d05bc83973e5b4b9f3634a694b
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222262"
---
# <a name="tutorial-train-a-machine-learning-model-without-code"></a>Samouczek: uczenie modelu uczenia maszynowego bez kodu

Możesz wzbogacić dane w tabelach platformy Spark przy użyciu nowych modeli uczenia maszynowego, które są pouczenie przy użyciu funkcji [automatycznego uczenia maszynowego](../../machine-learning/concept-automated-ml.md). W usłudze Azure Synapse Analytics można wybrać tabelę platformy Spark w obszarze roboczym do użycia jako zestaw danych szkoleniowych do tworzenia modeli uczenia maszynowego i można to zrobić w środowisku bez kodu.

W tym samouczku dowiesz się, jak uczenie modeli uczenia maszynowego przy użyciu bezpłatnego środowiska usługi Azure Synapse Analytics Studio. Funkcja automatycznego uczenia maszynowego jest używana w Azure Machine Learning, zamiast ręcznego kodowania środowiska. Typ pociągu modelu zależy od problemu, który próbujesz rozwiązać.

Jeśli nie masz subskrypcji platformy Azure, [przed rozpoczęciem utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

- [Obszar roboczy analizy usługi Azure Synapse](../get-started-create-workspace.md). Upewnij się, że ma ono następujące konto magazynu skonfigurowane jako magazyn domyślny: Azure Data Lake Storage Gen2. W przypadku systemu plików Data Lake Storage Gen2, z którym pracujesz, upewnij się, że jesteś **współautorem danych magazynu obiektów BLOB**.
- Pula Apache Spark w obszarze roboczym usługi Azure Synapse Analytics. Aby uzyskać szczegółowe informacje, zobacz [Szybki Start: Tworzenie dedykowanej puli SQL za pomocą usługi Azure Synapse Analytics Studio](../quickstart-create-sql-pool-studio.md).
- Azure Machine Learning połączona usługa w obszarze roboczym analizy usługi Azure Synapse. Aby uzyskać szczegółowe informacje, zobacz [Szybki Start: Tworzenie nowej Azure Machine Learning połączonej usługi w usłudze Azure Synapse Analytics](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-spark-table-for-training-dataset"></a>Tworzenie tabeli platformy Spark na potrzeby zestawu danych szkoleniowych

W tym samouczku potrzebna jest tabela platformy Spark. Poniższy Notes tworzy.

1. Pobierz Notes [Create-Spark-Table-NYCTaxi-Data. ipynb](https://go.microsoft.com/fwlink/?linkid=2149229).

1. Zaimportuj Notes do usługi Azure Synapse Analytics Studio.
![Zrzut ekranu usługi Azure Synapse Analytics z wyróżnioną opcją importu.](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Wybierz pulę platformy Spark, której chcesz użyć, a następnie wybierz pozycję **Uruchom wszystkie**. Spowoduje to pobranie danych z Nowego Jorku z otwartego zestawu danych, a następnie zapisanie go do domyślnej bazy danych platformy Spark.
![Zrzut ekranu usługi Azure Synapse Analytics z wyróżnioną funkcją Run All i Database Spark.](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. Po zakończeniu działania notesu zostanie wyświetlona nowa tabela platformy Spark z domyślną bazą danych platformy Spark. Z **danych** Znajdź tabelę o nazwie **nyc_taxi**.
![Zrzut ekranu karty dane analizy usługi Azure Synapse z wyróżnioną nową tabelą.](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="launch-automated-machine-learning-wizard"></a>Uruchom Kreatora automatycznego uczenia maszynowego

Oto kroki tej procedury:

1. Kliknij prawym przyciskiem myszy tabelę Spark utworzoną w poprzednim kroku. Aby otworzyć kreatora, wybierz opcję **Machine Learning**  >  **wzbogacania z nowym modelem**.
![Zrzut ekranu przedstawiający tabelę Spark z użyciem Machine Learning i wzbogacania z wyróżnionym nowym modelem.](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

1. Następnie można podać szczegóły konfiguracji dotyczące tworzenia zautomatyzowanego eksperymentu uczenia maszynowego w Azure Machine Learning. Ten przebieg powoduje pociąganie wielu modeli, a najlepszy model z pomyślnego przebiegu jest rejestrowany w rejestrze modelu Azure Machine Learning.

   ![Zrzut ekranu przedstawiający wzbogacanie z nowymi specyfikacjami konfiguracji modelu.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

    - **Obszar roboczy Azure Machine Learning**: Aby utworzyć zautomatyzowany przebieg uczenia maszynowego, jest wymagany obszar roboczy Azure Machine Learning. Musisz również połączyć obszar roboczy usługi Azure Synapse Analytics z obszarem roboczym Azure Machine Learning przy użyciu [połączonej usługi](quickstart-integrate-azure-machine-learning.md). Po spełnieniu wszystkich wymagań wstępnych możesz określić obszar roboczy Azure Machine Learning, który ma być używany dla tego zautomatyzowanego przebiegu.

    - **Nazwa eksperymentu**: Określ nazwę eksperymentu. Po przesłaniu automatycznego przebiegu uczenia maszynowego należy podać nazwę eksperymentu. Informacje dotyczące przebiegu są przechowywane w ramach tego eksperymentu w obszarze roboczym Azure Machine Learning. To środowisko domyślnie tworzy nowy eksperyment i generuje proponowaną nazwę, ale można również podać nazwę istniejącego eksperymentu.

    - **Najlepszy model**: Określ nazwę najlepszego modelu z automatycznego uruchamiania. Najlepszy model otrzymuje tę nazwę i Zapisano w Azure Machine Learning rejestru modelu automatycznie po tym przebiegu. Zautomatyzowany przebieg uczenia maszynowego tworzy wiele modeli uczenia maszynowego. W oparciu o podstawową metrykę wybraną w późniejszym kroku można porównać te modele i wybrać najlepszy model.

    - **Kolumna docelowa**: to, co model jest przeszkolony do przewidywania. Wybierz kolumnę, która ma zostać przewidywalna. (W tym samouczku wybieramy kolumnę liczbową `fareAmount` jako kolumnę docelową).

    - **Pula platformy Spark**: Pula platformy Spark, która ma być używana na potrzeby automatycznego uruchamiania eksperymentu. Obliczenia są uruchamiane w określonej puli.

    - **Szczegóły konfiguracji platformy Spark**: oprócz puli platformy Spark można także podać szczegóły konfiguracji sesji.

1. Wybierz opcję **Kontynuuj**.

## <a name="choose-task-type"></a>Wybierz typ zadania

Wybierz typ modelu uczenia maszynowego dla eksperymentu na podstawie pytania, na które próbujesz odpowiedzieć. Ponieważ `fareAmount` jest kolumną docelową i jest wartością liczbową, wybierz pozycję **regresja** tutaj. Następnie wybierz pozycję **Kontynuuj**.

![Zrzut ekranu przedstawiający wzbogacanie przy użyciu nowego modelu z wyróżnioną regresją.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>Dodatkowe konfiguracje

W przypadku wybrania opcji **regresja** lub **Klasyfikacja** jako typu modelu w poprzedniej sekcji dostępne są następujące konfiguracje:

- **Metryka podstawowa**: Metryka używana do mierzenia, jak dobrze robi model. Jest to metryka używana do porównywania różnych modeli utworzonych w zautomatyzowanym przebiegu i określania modelu, który najlepiej sprawdza się.

- **Czas zadania szkoleniowego (godziny)**: maksymalny czas (w godzinach) dla eksperymentu do uruchamiania i uczenia modeli. Należy pamiętać, że można również podać wartości mniejsze niż 1 (na przykład `0.5` ).

- **Maksymalna liczba współbieżnych iteracji**: reprezentuje maksymalną liczbę iteracji uruchomionych równolegle.

- **Zgodność modelu ONNX**: po włączeniu tej opcji modele przeszkolone przez automatyczne Uczenie maszynowe są konwertowane do formatu ONNX. Jest to szczególnie istotne, jeśli chcesz użyć modelu oceniania w pulach SQL usługi Azure Synapse Analytics.

Wszystkie te ustawienia mają wartość domyślną, którą można dostosować.
![Zrzut ekranu przedstawiający wzbogacanie z nowym modelem dodatkowe konfiguracje.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

Po zakończeniu wszystkich wymaganych konfiguracji można uruchomić automatyczne uruchomienie. Możesz wybrać pozycję **Utwórz przebieg**, która uruchamia przebieg uruchomiony bezpośrednio, bez kodu. Alternatywnie, jeśli preferujesz kod, możesz wybrać pozycję **Otwórz w notesie**. Ta opcja umożliwia wyświetlenie kodu, który tworzy przebieg i uruchamia Notes.

>[!NOTE]
>W przypadku wybrania **prognozowania szeregów czasowych** jako typu modelu w poprzedniej sekcji należy wykonać dodatkowe konfiguracje. Prognozowanie nie obsługuje także zgodności modelu ONNX.

### <a name="create-run-directly"></a>Utwórz przebieg bezpośrednio

Aby uruchomić automatyczne uruchamianie uczenia maszynowego, wybierz pozycję **Rozpocznij uruchamianie**. Zobaczysz powiadomienie wskazujące, że przebieg jest uruchamiany. Następnie zobaczysz inne powiadomienie informujące o powodzeniu. Możesz również sprawdzić stan w Azure Machine Learning, wybierając link w powiadomieniu.
![Zrzut ekranu przedstawiający pomyślne powiadomienie.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>Utwórz przebieg z notesem

Aby wygenerować Notes, wybierz pozycję **Otwórz w notesie**. Następnie wybierz pozycję **Uruchom wszystkie**. Umożliwia to również dodanie dodatkowych ustawień do zautomatyzowanego przebiegu uczenia maszynowego.

![Zrzut ekranu przedstawiający Notes z wyróżnionym poleceniem Uruchom wszystkie.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

Po pomyślnym przesłaniu przebiegu zostanie wyświetlony link do przebiegu eksperymentu w obszarze roboczym Azure Machine Learning w danych wyjściowych notesu. Wybierz łącze, aby monitorować automatyczne uruchamianie w Azure Machine Learning.
![Zrzut ekranu usługi Azure Synapse Analytics z wyróżnionym ](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png) linkiem.

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Kreator oceniania modelu uczenia maszynowego (wersja zapoznawcza) dla dedykowanych pul SQL](tutorial-sql-pool-model-scoring-wizard.md)
- [Szybki Start: Tworzenie nowej Azure Machine Learning połączonej usługi w usłudze Azure Synapse Analytics](quickstart-integrate-azure-machine-learning.md)
- [Możliwości uczenia maszynowego w usłudze Azure Synapse Analytics](what-is-machine-learning.md)