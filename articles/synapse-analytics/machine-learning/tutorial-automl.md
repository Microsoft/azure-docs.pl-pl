---
title: 'Samouczek: szkolenie modelu uczenia maszynowego przy użyciu AutoML'
description: Samouczek dotyczący sposobu uczenia modelu uczenia maszynowego w usłudze Azure Synapse przy użyciu AutoML.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 8dd99b60a548e3c392bbe468ddde484081e6eb8b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468410"
---
# <a name="tutorial-code-free-machine-learning-model-training-in-azure-synapse-with-automl-preview"></a>Samouczek: bezpłatne uczenie modelu uczenia maszynowego w usłudze Azure Synapse z AutoML (wersja zapoznawcza)

Dowiedz się, jak łatwo wzbogacać dane w tabelach platformy Spark przy użyciu nowych modeli uczenia maszynowego, które są pouczeni przy użyciu [AutoML w Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml).  Użytkownik w Synapse może po prostu wybrać tabelę Spark w obszarze roboczym usługi Azure Synapse, aby użyć jako zestawu danych szkoleniowych do tworzenia modeli uczenia maszynowego w niezawodnym kodzie.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> - Uczenie modeli uczenia maszynowego przy użyciu bezpłatnego środowiska w usłudze Azure Synapse Studio, które korzysta z zautomatyzowanej ML w środowisku Azure ML. Typ pociągu modelu zależy od problemu, który próbujesz rozwiązać.

Jeśli nie masz subskrypcji platformy Azure, [przed rozpoczęciem utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

- [Obszar roboczy Synapse Analytics](../get-started-create-workspace.md) z kontem magazynu ADLS Gen2 skonfigurowanym jako magazyn domyślny. Musisz być **współautorem danych obiektów blob magazynu** dla systemu plików ADLS Gen2, z którym pracujesz.
- Pula platformy Spark w obszarze roboczym usługi Azure Synapse Analytics. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie puli platformy Spark w usłudze Azure Synapse](../quickstart-create-sql-pool-studio.md).
- Azure Machine Learning połączoną usługę w obszarze roboczym analizy usługi Azure Synapse. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie połączonej usługi Azure Machine Learning w usłudze Azure Synapse](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

## <a name="create-a-spark-table-for-training-dataset"></a>Tworzenie tabeli platformy Spark na potrzeby zestawu danych szkoleniowych

W tym samouczku będzie potrzebna tabela platformy Spark. Poniższy Notes utworzy tabelę Spark.

1. Pobierz Notes [Create-Spark-Table-NYCTaxi-Data. ipynb](https://go.microsoft.com/fwlink/?linkid=2149229)

1. Zaimportuj Notes do usługi Azure Synapse Studio.
![Importuj Notes](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Wybierz pulę platformy Spark, której chcesz użyć, a następnie kliknij przycisk `Run all` . Uruchomienie tego notesu spowoduje uzyskanie nowych danych o taksówkach z otwartego zestawu danych i zapisanie ich w domyślnej bazie danych platformy Spark.
![Uruchom wszystko](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. Po zakończeniu działania notesu zostanie utworzona nowa tabela platformy Spark z domyślną bazą danych Spark. Przejdź do centrum danych i Znajdź tabelę o nazwie za pomocą `nyc_taxi` .
![Tabela platformy Spark](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="launch-automl-wizard-to-train-a-model"></a>Uruchom Kreatora AutoML, aby nauczyć model

Kliknij prawym przyciskiem myszy tabelę Spark utworzoną w poprzednim kroku. Wybierz pozycję "Machine Learning > Wzbogacaj przy użyciu nowego modelu", aby otworzyć kreatora.
![Uruchom Kreatora AutoML](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

Zostanie wyświetlony panel konfiguracja i zostanie wyświetlony monit o podanie szczegółowych informacji konfiguracyjnych dotyczących tworzenia eksperymentu AutoML w Azure Machine Learning. Ten przebieg spowoduje nauczenie wielu modeli, a najlepszy model od pomyślnego przebiegu zostanie zarejestrowany w rejestrze modelu usługi Azure ML:

![Skonfiguruj przebieg krok 1](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

- **Obszar roboczy usługi Azure ml**: obszar roboczy Azure ml jest wymagany do utworzenia AutoML eksperymentu. Musisz również połączyć obszar roboczy usługi Azure Synapse z obszarem roboczym Azure ML przy użyciu [połączonej usługi](quickstart-integrate-azure-machine-learning.md). Gdy masz wszystkie wymagania wstępne, możesz określić obszar roboczy Azure ML, który ma być używany dla tego uruchomienia AutoML.

- **Nazwa eksperymentu**: Określ nazwę eksperymentu. Po przesłaniu AutoML przebiegu należy podać nazwę eksperymentu. Informacje dotyczące przebiegu są przechowywane w ramach tego eksperymentu w obszarze roboczym usługi Azure ML. To środowisko spowoduje domyślnie utworzenie nowego eksperymentu i wygenerowanie proponowanej nazwy, ale można również podać nazwę istniejącego eksperymentu.

- **Najlepszy model**: Określ nazwę najlepszego modelu z przebiegu AutoML. Najlepszy model otrzymuje tę nazwę i zostanie zapisany w rejestrze modelu usługi Azure ML automatycznie po tym przebiegu. AutoML uruchomienie spowoduje utworzenie wielu modeli uczenia maszynowego. W oparciu o podstawową metrykę wybraną w późniejszym kroku można porównać te modele i wybrać najlepszy model.

- **Kolumna docelowa**: to, co model jest przeszkolony do przewidywania. Wybierz kolumnę, która ma zostać przewidywalna.

- **Pula platformy Spark**: Pula platformy Spark, która ma być używana do uruchamiania eksperymentu AutoML. Obliczenia będą wykonywane w określonej puli.

- **Szczegóły konfiguracji platformy Spark**: oprócz puli platformy Spark można także podać szczegóły konfiguracji sesji.

W tym samouczku wybieramy kolumnę liczbową `fareAmount` jako kolumnę docelową.

Kliknij przycisk "Kontynuuj".

## <a name="choose-task-type"></a>Wybierz typ zadania

Wybierz typ modelu uczenia maszynowego na podstawie pytania, na które próbujesz odpowiedzieć. Ponieważ została wybrana `fareAmount` jako kolumna docelowa i jest to wartość liczbowa, wybieramy *regresję*.

Kliknij przycisk "Kontynuuj", aby przeprowadzić konfigurację dodatkowych ustawień.

![Wybór typu zadania](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>Dodatkowe konfiguracje

W przypadku wybrania opcji *Klasyfikacja* lub typ *regresji* dodatkowe konfiguracje są następujące:

- **Metryka podstawowa**: Metryka używana do mierzenia, jak dobrze robi model. Jest to metryka, która będzie używana do porównywania różnych modeli utworzonych w ramach przebiegu AutoML i określania modelu, który najlepiej sprawdza się.

- **Czas zadania szkoleniowego (godziny)**: maksymalny czas (w godzinach) dla eksperymentu do uruchamiania i uczenia modeli. Należy pamiętać, że można również podać wartości mniejsze niż 1. Na przykład: `0.5`.

- **Maksymalna liczba współbieżnych iteracji**: reprezentuje maksymalną liczbę iteracji, które będą wykonywane równolegle.

- **Zgodność modelu ONNX**: Jeśli ta funkcja jest włączona, modele przeszkolone przez AutoML zostaną przekonwertowane na format ONNX. Jest to szczególnie istotne, jeśli chcesz użyć modelu do oceniania w pulach SQL Synapse platformy Azure.

Wszystkie te ustawienia mają wartość domyślną, którą można dostosować.
![dodatkowe konfiguracje](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

> Należy zauważyć, że po wybraniu opcji "prognozowanie szeregów czasowych" jest wymagana większa liczba konfiguracji. Prognozowanie nie obsługuje również zgodności modelu ONNX.

Po zakończeniu wszystkich wymaganych konfiguracji można rozpocząć Uruchamianie AutoML.

Istnieją dwa sposoby uruchamiania AutoML w usłudze Azure Azure Synapse. Aby skorzystać z bezpłatnego kodu, możesz wybrać opcję bezpośredniego **uruchamiania** . Jeśli wolisz korzystać z kodu, możesz wybrać opcję **Otwórz w notesie**, która umożliwia wyświetlenie kodu, który tworzy przebieg i uruchamia Notes.

### <a name="create-run-directly"></a>Utwórz przebieg bezpośrednio

Kliknij pozycję "Uruchom Uruchom", aby uruchomić AutoML uruchomione bezpośrednio. Zostanie powiadomienie wskazujące, że uruchomienie AutoML przebiega.

Po pomyślnym uruchomieniu AutoML zostanie wyświetlone inne pomyślne powiadomienie. Możesz również kliknąć przycisk powiadomień, aby sprawdzić stan przesłania przebiegu.
Azure ML przez kliknięcie linku w pomyślnym powiadomieniu.
![Pomyślne powiadomienie](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>Utwórz przebieg z notesem

Wybierz pozycję *Otwórz w notesie* , aby wygenerować Notes. Kliknij pozycję *Uruchom wszystkie* , aby wykonać Notes.
Umożliwia to również dodanie dodatkowych ustawień do przebiegu AutoML.

![Otwórz Notes](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

Po pomyślnym przesłaniu przebiegu z notesu będzie można utworzyć link do przebiegu eksperymentu w obszarze roboczym usługi Azure ML w danych wyjściowych notesu. Możesz kliknąć link, aby monitorować AutoML przebiegu w usłudze Azure ML.
![Wszystkie uruchomienia notesu ](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png) )

## <a name="next-steps"></a>Następne kroki

- [Samouczek: ocenianie modelu uczenia maszynowego w ramach dedykowanych pul SQL usługi Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md).
- [Szybki Start: Tworzenie nowej Azure Machine Learning połączonej usługi w usłudze Azure Synapse](quickstart-integrate-azure-machine-learning.md)
- [Możliwości Machine Learning w usłudze Azure Synapse Analytics](what-is-machine-learning.md)