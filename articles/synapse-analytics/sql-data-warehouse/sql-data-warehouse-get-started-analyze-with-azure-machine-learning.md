---
title: Analizowanie danych przy użyciu usługi Azure Machine Learning
description: Usługa Azure Machine Learning umożliwia tworzenie modelu uczenia maszynowego predykcyjnego na podstawie danych przechowywanych w usłudze Azure Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 74a6d1aecfc83ea68b9e30453056d231f4bf3e65
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416187"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Analizowanie danych przy użyciu usługi Azure Machine Learning
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Program Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

W tym samouczku użyto usługi Azure Machine Learning do utworzenia modelu uczenia maszynowego predykcyjnego na podstawie danych przechowywanych w usłudze Azure Synapse. W szczególności ten samouczek omawia tworzenie ukierunkowanej kampanii marketingowej dla sklepu rowerowego Adventure Works przez prognozowanie prawdopodobieństwa zakupu roweru przez klienta.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków opisanych w tym samouczku potrzebne są:

* Pula SQL wstępnie załadowana przy próbkowaniu danych AdventureWorksDW. Aby udostępnić ten przepis, zobacz [Tworzenie puli SQL](create-data-warehouse-portal.md) i wybierz załadować przykładowe dane. Jeśli masz już magazyn danych, ale bez przykładowych danych, możesz [ręcznie załadować przykładowe dane](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="1-get-the-data"></a>1. Pobierz dane
Dane znajdują się w widoku dbo.vTargetMail w bazie danych AdventureWorksDW. Aby odczytać te dane:

1. Zaloguj się do programu [Azure Machine Learning Studio](https://studio.azureml.net/) i kliknij eksperymenty.
2. Kliknij **przycisk +NOWOŚĆ** w lewym dolnym rogu ekranu i wybierz **pusty eksperyment**.
3. Wprowadź nazwę swojego eksperymentu: Targeted Marketing (Marketing docelowy).
4. Przeciągnij moduł **Importuj dane** w obszarze **Wprowadzanie danych i wyjmij** z okienka modułów do obszaru roboczego.
5. Określ szczegóły puli SQL w okienku Właściwości.
6. Określ **zapytanie** do bazy danych, aby odczytać potrzebne dane.

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

Aby uruchomić eksperyment, kliknij pozycję **Run** (Uruchom) na kanwie eksperymentu.

![Uruchamianie eksperymentu](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png)

Po pomyślnym wykonaniu eksperymentu kliknij port wyjściowy na dole modułu Reader (Czytnik) i wybierz opcję **Visualize** (Wizualizacja), aby wyświetlić zaimportowane danych.

![Wyświetlanie zaimportowanych danych](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png)

## <a name="2-clean-the-data"></a>2. Wyczyść dane
Aby wyczyścić dane, usuń kilka kolumn, które nie są istotne dla modelu. W tym celu:

1. Przeciągnij moduł **Wybierz kolumny w** module Zestaw danych w obszarze **Transformacja danych < Manipulacja** na kanwę. Podłącz ten moduł do modułu **Importuj dane.**
2. Kliknij pozycję **Launch column selector** (Uruchom selektor kolumn) w okienku Properties (Właściwości), aby wskazać kolumny do usunięcia.

   ![Kolumny projektu](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png)
3. Wyklucz dwie kolumny: CustomerAlternateKey i GeographyKey.

   ![Usuń zbędne kolumny](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png)

## <a name="3-build-the-model"></a>3. Zbuduj model
Podzielimy dane w proporcji 80–20: 80% do trenowania tworzenia modelu uczenia maszynowego i 20% do testowania modelu. Użyjemy algorytmów "Dwuklasowych" dla tego problemu klasyfikacji binarnej.

1. Przeciągnij moduł **Split** (Podział) na kanwę.
2. W okienku właściwości wprowadź 0,8 dla ułamka wierszy w pierwszym wyjściowym zestawie danych.

   ![Podział danych na zestaw szkoleniowy i zestaw testowy](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png)
3. Przeciągnij moduł **Two-Class Boosted Decision Tree** (Dwuklasowe wzmocnione drzewo decyzyjne) na kanwę.
4. Przeciągnij **moduł Modelu pociągu** do kanwy i określ dane wejściowe, łącząc go z modułami **dwuklasowego drzewa wzmocnionego (algorytmu** ML) i **Split** (dane do trenowania algorytmu). 

     ![Łączenie modułu Train Model (Model szkoleniowy)](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png)
5. Następnie kliknij przycisk **Launch column selector** (Uruchom selektor kolumn) w okienku Properties (Właściwości). Wybierz kolumnę **BikeBuyer** (Nabywca roweru) jako kolumnę do prognozowania.

   ![Wybór kolumny do prognozowania](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png)

## <a name="4-score-the-model"></a>4. Ocena modelu
Teraz przetestujemy działanie modelu na danych testowych. Porównamy wybrany algorytm z innym algorytmem, aby zobaczyć, który działa lepiej.

1. Przeciągnij **moduł Score Model** do kanwy i połącz go z modułami Modelu **pociągu** i **Podziel dane.**

   ![Ocena modelu](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png)
2. Przeciągnij moduł **Two-Class Bayes Point Machine** (Dwuklasowa maszyna punktu Bayesa) do kanwy eksperymentu. Porównamy działanie tego algorytmu z algorytmem Two-Class Boosted Decision Tree (Dwuklasowe wzmocnione drzewo decyzyjne).
3. Skopiuj i wklej moduły Train Model (Model szkoleniowy) i Score Model (Model klasyfikacyjny) do kanwy.
4. Przeciągnij moduł **Evaluate Model** (Ocena modelu) do kanwy, aby porównać oba algorytmy.
5. **Uruchom** eksperyment.

   ![Uruchamianie eksperymentu](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png)
6. Kliknij port wyjściowy w dolnej części modułu Evaluate Model (Ocena modelu), a następnie kliknij pozycję Visualize (Wizualizacja).

   ![Wizualizacja wyników oceny](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png)

Podane metryki to krzywa ROC, diagram precyzyjnego przypomnienia i krzywa podnoszenia. Na podstawie tych metryk możemy stwierdzić, że pierwszy model działa lepiej niż drugi. Aby przyjrzeć się przewidywanemu pierwszeu modelowi, kliknij port wyjściowy modelu wyników i kliknij pozycję Wizualizuj.

![Wizualizacja wyników klasyfikacji](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png)

Zostaną wyświetlone dwie kolumny dodane do zestawu danych testowych.

* Scored Probabilities (Sklasyfikowane prawdopodobieństwo): prawdopodobieństwo, że klient jest nabywcą roweru.
* Scored Labels (Sklasyfikowane etykiety): klasyfikacja dokonana przez model — nabywca roweru (1) lub nie (0). Ustawiony próg prawdopodobieństwa etykietowania wynosi 50% i można go dostosować.

Porównując wartości w kolumnach BikeBuyer (Nabywca roweru) (rzeczywiste) oraz Scored Labels (Sklasyfikowane etykiety) (prognozowane), można określić prawidłowość działania modelu. Następnie można użyć tego modelu, aby prognoz dla nowych klientów i opublikować ten model jako usługę sieci web lub zapisać wyniki z powrotem do platformy Azure Synapse.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o tworzeniu predykcyjnych modeli uczenia maszynowego, zapoznaj się z artykułem [Wprowadzenie do usługi Machine Learning na platformie Azure](https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/).