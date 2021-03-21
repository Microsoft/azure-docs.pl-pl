---
title: Przetwarzanie danych z modeli zautomatyzowanych uczenia maszynowego (AutoML) przy użyciu przepływów danych
description: Dowiedz się, jak używać przepływów danych Azure Data Factory do przetwarzania danych z modeli zautomatyzowanych uczenia maszynowego (AutoML).
services: data-factory
author: amberz
co-author: ATLArcht
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/31/2021
ms.author: amberz
ms.co-author: Donnana
ms.openlocfilehash: 45cd44cc0678b7f3a006a88bf66be2bca091af76
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595383"
---
# <a name="process-data-from-automated-machine-learning-models-by-using-data-flows"></a>Przetwarzanie danych z automatycznych modeli uczenia maszynowego przy użyciu przepływów danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Automatyczne Uczenie maszynowe (AutoML) jest przyjęte przez projekty uczenia maszynowego do uczenia, dostrajania i uzyskiwania najlepszych modeli automatycznie przy użyciu metryk docelowych określonych dla klasyfikacji, regresji i prognozowania szeregów czasowych.

Jednym z wyzwań dla AutoML jest to, że pierwotne dane z magazynu danych lub transakcyjnej bazy danych byłyby ogromnym zestawem danych, prawdopodobnie 10 GB. Duży zestaw danych wymaga dłuższego czasu do uczenia modeli, dlatego zalecamy zoptymalizowanie przetwarzania danych przed przeszkoleniem Azure Machine Learning modeli. W tym samouczku przedstawiono sposób użycia Azure Data Factory do partycjonowania zestawu danych w plikach AutoML dla Machine Learning zestawu danych.

Projekt AutoML zawiera następujące trzy scenariusze przetwarzania danych:

* Podziel duże ilości danych na pliki AutoML przed przeszkoleniem modeli.

     [Ramka danych Pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) jest często używana do przetwarzania danych przed przeszkoleniem modeli. Ramka danych Pandas działa prawidłowo w przypadku rozmiarów danych mniejszych niż 1 GB, ale jeśli dane są większe niż 1 GB, ramka danych Pandas spowalnia się w celu przetwarzania danych. Czasami możesz nawet otrzymać komunikat o błędzie braku pamięci. Zalecamy używanie formatu [pliku Parquet](https://parquet.apache.org/) na potrzeby uczenia maszynowego, ponieważ jest to format binarny kolumnowy.
    
     Data Factory mapowanie przepływów danych to wizualnie zaprojektowane przekształcenia danych, które uwalniają inżynierów danych od pisania kodu. Mapowanie przepływów danych to zaawansowany sposób przetwarzania dużych ilości danych, ponieważ potok używa skalowanych klastrów Spark.

* Podziel zestaw danych szkoleniowych i zestaw danych testowych.
    
    Zestaw danych szkoleniowych zostanie użyty dla modelu szkoleniowego. Testowy zestaw danych zostanie użyty do oszacowania modeli w projekcie uczenia maszynowego. Działanie podziału warunkowego na potrzeby mapowania przepływów danych będzie dzielić dane szkoleniowe i dane testowe.

* Usuń niekwalifikowane dane.

    Możesz chcieć usunąć niekwalifikowane dane, takie jak plik Parquet z zerowymi wierszami. W tym samouczku użyjemy zagregowanego działania, aby uzyskać liczbę wierszy. Liczba wierszy będzie warunkiem usunięcia niekwalifikowanych danych.

## <a name="preparation"></a>Przygotowanie

Skorzystaj z poniższej tabeli Azure SQL Database.

```
CREATE TABLE [dbo].[MyProducts](
    [ID] [int] NULL,
    [Col1] [char](124) NULL,
    [Col2] [char](124) NULL,
    [Col3] datetime NULL,
    [Col4] int NULL

) 

```

## <a name="convert-data-format-to-parquet"></a>Konwertuj format danych na Parquet

Następujący przepływ danych spowoduje przekonwertowanie tabeli SQL Database na format pliku Parquet:

- **Źródłowy zestaw danych**: tabela transakcji SQL Database.
- **Zestaw danych ujścia**: Magazyn obiektów BLOB w formacie Parquet.

## <a name="remove-unqualified-data-based-on-row-count"></a>Usuń niekwalifikowane dane na podstawie liczby wierszy

Załóżmy, że musimy usunąć liczbę wierszy mniejszą niż dwa.

1. Użyj zagregowanego działania, aby uzyskać liczbę wierszy. Użyj **grupowania według** Col2 i **agregacji** z `count(1)` dla liczby wierszy.

    ![Zrzut ekranu pokazujący Konfigurowanie działania zagregowanego w celu uzyskania liczby wierszy.](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. Za pomocą działania ujścia wybierz **Typ ujścia** jako **pamięć podręczna** na karcie **sink** . Następnie wybierz żądaną kolumnę z listy rozwijanej **kolumny kluczy** na karcie **Ustawienia** .

    ![Zrzut ekranu pokazujący Konfigurowanie działania CacheSink w celu uzyskania liczby wierszy w buforze pamięci podręcznej.](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. Użyj działania kolumna pochodna, aby dodać kolumnę liczba wierszy w strumieniu źródłowym. Na karcie **Ustawienia kolumny pochodnej** Użyj `CacheSink#lookup` wyrażenia, aby uzyskać liczbę wierszy z CacheSink.

    ![Zrzut ekranu pokazujący Konfigurowanie działania kolumny pochodnej w celu dodania liczby wierszy w Source1.](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. Użyj działania podziału warunkowego, aby usunąć niekwalifikowane dane. W tym przykładzie liczba wierszy jest określana na podstawie kolumny Col2. Warunek polega na usunięciu liczby wierszy mniejszych niż dwa, więc zostaną usunięte dwa wiersze (ID = 2 i ID = 7). Dane niekwalifikowane można zapisać do magazynu obiektów BLOB w celu zarządzania danymi.

    ![Zrzut ekranu pokazujący Konfigurowanie działania podziału warunkowego w celu pobrania danych, które są większe lub równe dwóm.](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    * Utwórz nowe źródło do pobierania liczby wierszy, które będą używane w oryginalnym źródle w dalszych krokach.
>    * Użyj CacheSink z punktu widzenia wydajności.

## <a name="split-training-data-and-test-data"></a>Podziel dane szkoleniowe i dane testowe

Chcemy podzielić dane szkoleniowe i dane testowe dla każdej partycji. W tym przykładzie dla tej samej wartości Col2 Pobierz pierwsze dwa wiersze jako dane testowe i resztę wierszy jako dane szkoleniowe.

1. Użyj działania okna, aby dodać jeden wiersz dla każdej partycji. Na karcie **ponad** wybierz kolumnę partycji. W tym samouczku zajmiemy się partycją for Col2. Nadaj kolejności na karcie **Sortuj** , która w tym samouczku będzie oparta na identyfikatorze. Nadaj kolejności na karcie **kolumny okna** , aby dodać jedną kolumnę jako numer wiersza dla każdej partycji.

    ![Zrzut ekranu pokazujący Konfigurowanie działania okna do dodawania jednej nowej kolumny z numerem wiersza.](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. Użyj działania podziału warunkowego, aby podzielić dwa pierwsze wiersze partycji na zestaw danych testowych i resztę wierszy w zestawie danych szkoleniowych. Na karcie **Ustawienia podziału warunkowego** Użyj wyrażenia `lesserOrEqual(RowNum,2)` jako warunku.

    ![Zrzut ekranu pokazujący Konfigurowanie działania podziału warunkowego w celu podzielenia bieżącego zestawu danych z zestawem danych szkoleniowych i zestawem danych testowych.](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-the-training-and-test-datasets-with-parquet-format"></a>Partycjonowanie szkoleń i testów zestawów danych przy użyciu formatu Parquet

Za pomocą działania ujścia na karcie **Optymalizacja** Użyj **unikatowej wartości na partycję** , aby ustawić kolumnę jako klucz kolumny dla partycji.

![Zrzut ekranu pokazujący Konfigurowanie działania ujścia w celu ustawienia partycji zestawu danych szkoleniowych.](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

Przyjrzyjmy się całej logice potoku.

![Zrzut ekranu pokazujący logikę całego potoku.](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)

## <a name="next-steps"></a>Następne kroki

Kompiluj resztę logiki przepływu danych przy użyciu mapowania [przekształceń](concepts-data-flow-overview.md)przepływu danych.
