---
title: Analizowanie danych przy użyciu usługi Azure Machine Learning
description: Użyj Azure Machine Learning, aby utworzyć predykcyjny model uczenia maszynowego na podstawie danych przechowywanych w usłudze Azure Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 76a154d3a137017f374247308a3980d598698246
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678663"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Analizowanie danych przy użyciu usługi Azure Machine Learning

W tym samouczku do tworzenia predykcyjnego modelu uczenia maszynowego służy [Azure Machine Learning projektanta](../../machine-learning/concept-designer.md) . Model jest oparty na danych przechowywanych w usłudze Azure Synapse. Scenariusz dla tego samouczka polega na przewidywaniu, czy klient może kupić rower, czy nie ma to firmy Adventure Works, sklepu rowerowego w celu utworzenia dostosowanej kampanii marketingowej.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym samouczku potrzebne są:

* Pula SQL wstępnie załadowana z przykładowymi danymi AdventureWorksDW. Aby zainicjować obsługę administracyjną tej puli SQL, zobacz [Tworzenie puli SQL](create-data-warehouse-portal.md) i wybieranie ładowania przykładowych danych. Jeśli masz już magazyn danych, ale nie masz przykładowych danych, możesz [ręcznie załadować przykładowe dane](./load-data-from-azure-blob-storage-using-copy.md).
* obszar roboczy usługi Azure Machine Learning. Postępuj zgodnie z [tym samouczkiem](../../machine-learning/how-to-manage-workspace.md) , aby utworzyć nowy.

## <a name="get-the-data"></a>Pobieranie danych

Używane dane są w widoku dbo. vTargetMail w AdventureWorksDW. Aby korzystać ze sklepu datastore w tym samouczku, dane są najpierw eksportowane do konta Azure Data Lake Storage, ponieważ usługa Azure Synapse nie obsługuje obecnie zestawów danych. Azure Data Factory może służyć do eksportowania danych z magazynu danych w celu Azure Data Lake Storage za pomocą [działania kopiowania](../../data-factory/copy-activity-overview.md). Użyj następującego zapytania do importowania:

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Gdy dane będą dostępne w Azure Data Lake Storage, magazyny danych w Azure Machine Learning są używane do [nawiązywania połączenia z usługami Azure Storage](../../machine-learning/how-to-access-data.md). Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć magazyn danych i odpowiadający mu element dataset:

1. Uruchom program Azure Machine Learning Studio z Azure Portal lub Zaloguj się na [Azure Machine Learning Studio](https://ml.azure.com/).

1. Kliknij pozycję **magazyny** danych w okienku po lewej stronie w sekcji **Zarządzanie** , a następnie kliknij pozycję **nowy magazyn** danych.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/datastores-tab.png" alt-text="Zrzut ekranu przedstawiający lewe okienko interfejsu Azure Machine Learning":::

1. Podaj nazwę magazynu danych, wybierz typ "Azure Blob Storage", podaj lokalizację i poświadczenia. Następnie kliknij pozycję **Utwórz**.

1. Następnie kliknij pozycję **zestawy danych** w okienku po lewej stronie w sekcji **elementy zawartości** . Wybierz pozycję **Utwórz zestaw danych** z opcją **z magazynu** danych.

1. Określ nazwę zestawu danych i wybierz typ, który ma być **Tabelaryczny**. Następnie kliknij przycisk **dalej** , aby przejść do przodu.

1. W **sekcji Wybierz lub Utwórz magazyn** danych wybierz opcję, która została **wcześniej utworzona**. Wybierz magazyn danych, który został utworzony wcześniej. Kliknij przycisk Dalej i określ ustawienia ścieżki i pliku. Upewnij się, że jako plik zawiera nagłówek kolumny.

1. Na koniec kliknij pozycję **Utwórz** , aby utworzyć zestaw danych.

## <a name="configure-designer-experiment"></a>Konfigurowanie eksperymentu projektanta

Następnie wykonaj kroki opisane poniżej, aby skonfigurować projektanta:

1. Kliknij kartę **Projektant** w okienku po lewej stronie w sekcji **autor** .

1. Wybierz **łatwe w użyciu wstępnie skompilowane moduły** , aby utworzyć nowy potok.

1. W okienku ustawienia po prawej stronie Określ nazwę potoku.

1. Ponadto Wybierz docelowy klaster obliczeniowy dla całego eksperymentu w obszarze Ustawienia do klastra, który został wcześniej zainicjowany. Zamknij okienko ustawienia.

## <a name="import-the-data"></a>Importowanie danych

1. Wybierz subtab **zestawy danych** w lewym okienku poniżej pola wyszukiwania.

1. Przeciągnij utworzony wcześniej zestaw danych na kanwę.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/import-dataset.png" alt-text="Zrzut ekranu modułu DataSet na kanwie.":::

## <a name="clean-the-data"></a>Czyszczenie danych

Aby wyczyścić dane, Porzuć kolumny, które nie są istotne dla modelu. Wykonaj następujące czynności:

1. Wybierz **moduły** subtab w lewym okienku.

1. Przeciągnij **pozycję Wybierz kolumny w zestawie danych** w obszarze **przekształcanie danych < manipulowanie** na kanwę. Połącz ten moduł z modułem **DataSet** .

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-in.png" alt-text="Zrzut ekranu modułu wyboru kolumny na kanwie." lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-out.png":::

1. Kliknij moduł, aby otworzyć okienko właściwości. Kliknij pozycję Edytuj kolumnę, aby określić kolumny, które chcesz usunąć.

1. Wyklucz dwie kolumny: CustomerAlternateKey i GeographyKey. Kliknij pozycję **Zapisz**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/drop-columns.png" alt-text="Zrzut ekranu przedstawiający kolumny, które zostały porzucone.":::

## <a name="build-the-model"></a>Tworzenie modelu

Dane są podzielone na 80-20:80%, aby szkolić model uczenia maszynowego i 20% do testowania modelu. Algorytmy "dwie klasy" są używane w tym rozwiązaniu klasyfikacji binarnej.

1. Przeciągnij moduł **Split Data (podział danych** ) na kanwę.

1. W okienku właściwości wprowadź 0,8 dla **części wierszy w pierwszym wyjściowym zestawie danych**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/split-data.png" alt-text="Zrzut ekranu przedstawiający współczynnik podziału 0,8.":::

1. Przeciągnij moduł **Two-Class Boosted Decision Tree** (Dwuklasowe wzmocnione drzewo decyzyjne) na kanwę.

1. Przeciągnij moduł **uczenie modelu** do kanwy. Określ dane wejściowe, łącząc je z **Dwuklasowym drzewem decyzyjnym** (algorytm ml) i **dzieląc dane** (dane do uczenia algorytmu).

1. W przypadku modelu uczenia model, w opcji **kolumna etykiety** w okienku właściwości wybierz pozycję Edytuj kolumnę. Wybierz kolumnę **BikeBuyer** jako kolumnę do przewidywania i wybierz pozycję **Zapisz**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/label-column.png" alt-text="Zrzut ekranu przedstawiający kolumnę Label, BikeBuyer, Selected.":::

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/train-model.png" alt-text="Zrzut ekranu przedstawiający moduł uczenia modeli podłączony do Two-Class drzewa decyzyjne i podział modułów danych.":::

## <a name="score-the-model"></a>Ocenianie modelu

Teraz Przetestuj, jak model wykonuje na danych testowych. Dwa różne algorytmy zostaną porównane, aby zobaczyć, który z nich wykonuje lepsze. Wykonaj następujące czynności:

1. Przeciągnij moduł **modelu oceny** na kanwę i połącz go, aby **nauczyć model** i **podzielić moduły danych** .

1. Przeciągnij **średnią bayesaą Perceptron** do kanwy eksperymentu. Porównano, jak ten algorytm wykonuje w porównaniu do Two-Classego drzewa decyzyjnego.

1. Skopiuj i wklej **model uczenia** modułów i **model oceny** na kanwie.

1. Przeciągnij moduł **Evaluate Model** (Ocena modelu) do kanwy, aby porównać oba algorytmy.

1. Kliknij pozycję **Prześlij** , aby skonfigurować uruchomienie potoku.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-in.png" alt-text="Zrzut ekranu przedstawiający wszystkie pozostałe moduły na kanwie." lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-out.png":::

1. Po zakończeniu przebiegu kliknij prawym przyciskiem myszy moduł **oceny modelu** i kliknij polecenie **Wizualizuj wyniki oceny**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/result-visualize-zoomed-out.png" alt-text="Zrzut ekranu przedstawiający wyniki.":::

Dostarczone metryki to krzywa ROC, diagram odwołań dokładności i krzywa podnoszenia. Przyjrzyj się tym metrykom, aby zobaczyć, że pierwszy model działa lepiej niż drugi. Aby dowiedzieć się, jaki jest pierwszy model przewidywany, kliknij prawym przyciskiem myszy moduł model oceny i kliknij polecenie Wizualizuj wynikowy zestaw danych, aby zobaczyć przewidywane wyniki.

Zobaczysz dwie więcej kolumn dodanych do testu zestawu danych.

* Scored Probabilities (Sklasyfikowane prawdopodobieństwo): prawdopodobieństwo, że klient jest nabywcą roweru.
* Scored Labels (Sklasyfikowane etykiety): klasyfikacja dokonana przez model — nabywca roweru (1) lub nie (0). Ustawiony próg prawdopodobieństwa etykietowania wynosi 50% i można go dostosować.

Porównaj kolumnę BikeBuyer (rzeczywista) z etykietami z oceną (przewidywania), aby zobaczyć, jak dobrze został wykonany model. Następnie można użyć tego modelu do prognozowania dla nowych klientów. Możesz [opublikować ten model jako usługę sieci Web](../../machine-learning/tutorial-designer-automobile-price-deploy.md) lub zapisać wyniki z powrotem do usługi Azure Synapse.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Azure Machine Learning, zapoznaj się z artykułem [wprowadzenie do Machine Learning na platformie Azure](../../machine-learning/overview-what-is-azure-ml.md).

Dowiedz się więcej na temat wbudowanej oceny w hurtowni [danych.](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)