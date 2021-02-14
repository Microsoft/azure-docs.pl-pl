---
title: Używanie przepływu danych do przetwarzania danych z modeli zautomatyzowanych uczenia maszynowego (AutoML)
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
ms.openlocfilehash: 494fc2c227b6fe855e96a780d43cc6702722fa94
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100521018"
---
# <a name="process-data-from-automated-machine-learningautoml-models-using-data-flow"></a>Przetwarzanie danych z modeli zautomatyzowanych uczenia maszynowego (AutoML) przy użyciu przepływu danych

Automatyczne Uczenie maszynowe (AutoML) jest wdrażane przez projekty uczenia maszynowego do uczenia, dostrajania i uzyskiwania najlepszych modeli przy użyciu metryki docelowej określonej dla klasyfikacji, regresji i prognozowania szeregów czasowych. 

Jednym wyzwaniem jest niepierwotne dane z magazynu danych lub transakcyjnej bazy danych, na przykład: 10GB, duży zestaw danych wymaga dłuższego czasu do uczenia modeli, więc optymalizacja przetwarzania danych jest zalecana przed szkoleniem Azure Machine Learning modeli. W tym samouczku przedstawiono sposób użycia narzędzia ADF do partycjonowania zestawu danych do Parquet plików dla zestawu danych Azure Machine Learning. 

W programie zautomatyzowany projekt uczenia maszynowego (AutoML) stosuje się następujące trzy scenariusze przetwarzania danych:

* Podziel duże ilości danych na pliki Parquet przed modelami szkoleniowymi. 

     [Ramka danych Pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) jest często używana do przetwarzania danych przed modelami szkoleniowymi. Ramka danych Pandas dobrze sprawdza się w przypadku rozmiarów danych mniejszych niż 1 GB, ale jeśli dane są duże niż 1 GB, to Pandasa ramka danych spowalnia przetwarzanie danych, nawet jeśli zostanie wyświetlony komunikat o błędzie braku pamięci. Formaty [plików Parquet](https://parquet.apache.org/) są zalecane dla uczenia maszynowego, ponieważ jest to format binarny kolumnowy.
    
    Usługa Azure Data Factors mapuje przepływy danych, które są wizualnie zaprojektowane z użyciem kodu do inżynierów danych. Przetwarzanie dużych ilości danych jest wydajne, ponieważ potok używa skalowanych klastrów Spark w poziomie.

* Podziel zestaw danych szkolenia i zestaw danych testowych.
    
    Zestaw danych szkoleniowych zostanie użyty dla modelu szkoleniowego. testowy zestaw danych zostanie użyty do oszacowania modeli w projekcie uczenia maszynowego. Mapowanie operacji podziału warunkowego dla przepływów danych spowoduje rozdzielenie danych szkoleniowych i danych testowych. 

* Usuń niekwalifikowane dane.

    Może być konieczne usunięcie niekwalifikowanych danych, takich jak plik Parquet z zerowym wierszem. W tym samouczku użyjemy zagregowanego działania, aby pobrać liczbę wierszy, a liczba wierszy będzie warunkiem usunięcia niekwalifikowanych danych. 


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

Przepływ danych spowoduje przekonwertowanie tabeli Azure SQL Database na format pliku Parquet. 

**Źródłowy zestaw danych**: tabela transakcji Azure SQL Database

**Zestaw danych ujścia**: Magazyn obiektów BLOB w formacie Parquet


## <a name="remove-unqualified-data-based-on-row-count"></a>Usuń niekwalifikowane dane na podstawie liczby wierszy

Przypuśćmy do usunięcia liczby wierszy mniejszej niż 2. 

1. Użyj działania agregacji, aby pobrać liczbę wierszy: **Grupuj według** na podstawie Col2 i **agregacji** z liczbą (1) dla liczby wierszy. 

    ![Skonfiguruj działanie agregujące, aby uzyskać liczbę wierszy](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. Użyj działania ujścia, wybierz pozycję **Typ ujścia** jako pamięć podręczna na karcie **ujścia** , a następnie wybierz żądaną kolumnę z listy rozwijanej **kolumny kluczy** na karcie **Ustawienia** . 

    ![Konfigurowanie działania CacheSink w celu pobrania liczby wierszy w buforowanym ujścia](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. Użyj działania kolumna pochodna, aby dodać kolumnę liczba wierszy w strumieniu źródłowym. Na karcie **Ustawienia kolumny pochodnej** Użyj wyrażenia wyszukiwania CacheSink # pobierającego liczbę wierszy z SinkCache.
    ![Skonfiguruj działanie kolumny pochodnej, aby dodać liczbę wierszy w źródle 1](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. Użyj działania podziału warunkowego, aby usunąć niekwalifikowane dane. W tym przykładzie liczba wierszy oparta na kolumnie Col2, a warunek polega na usunięciu liczby wierszy mniejszej niż 2, więc zostaną usunięte dwa wiersze (ID = 2 i ID = 7). Dane niekwalifikowane można zapisać do magazynu obiektów BLOB w celu zarządzania danymi. 

    ![Konfigurowanie działania podziału warunkowego w celu pobierania danych o wartości większej lub równej 2](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    *    Utwórz nowe źródło do pobierania liczby wierszy, które będą używane w oryginalnym źródle w dalszych krokach. 
>    *    Użyj CacheSink z punktu widzenia wydajności. 

## <a name="split-training-data-and-test-data"></a>Podziel dane szkoleniowe i dane testowe 

1. Chcemy podzielić dane szkoleniowe i dane testowe dla każdej partycji. W tym przykładzie dla tej samej wartości Col2 Pobierz 2 pierwszych wierszy jako dane testowe i wiersze REST jako dane szkoleniowe. 

    Użyj działania okna, aby dodać jeden wiersz dla każdej partycji. W obszarze **za pośrednictwem** karty wybierz pozycję kolumna dla partycji (w tym samouczku zostanie utworzona partycja dla Col2), nadawanie kolejności na karcie **Sortowanie** (w tym samouczku na podstawie identyfikatora do zamówienia) i na karcie **kolumny okna** do dodania jednej kolumny jako numeru wiersza dla każdej partycji. 
    ![Konfigurowanie działania okna w celu dodania jednej nowej kolumny z numerem wiersza](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. Użyj działania podziału warunkowego, aby podzielić każdą z pierwszych 2 wierszy partycji na zestaw danych testowych, a następnie wiersze REST do szkolenia zestawu danych. Na karcie **Ustawienia podziału warunkowego** Użyj wyrażenia LesserOrEqual (rownum, 2) jako warunek. 

    ![Skonfiguruj działanie podziału warunkowego, aby podzielić bieżący zestaw danych na zestaw danych szkoleniowych i zestaw danych testów](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-training-dataset-and-test-dataset-with-parquet-format"></a>Zestaw danych szkolenia partycji i zestaw danych testowych z formatem Parquet

1. Użyj działania ujścia na karcie **Optymalizacja** przy użyciu **unikatowej wartości na partycję** , aby ustawić kolumnę jako klucz kolumny dla partycji. 
    ![Konfigurowanie działania ujścia w celu ustawienia partycji zestawu danych szkoleniowych](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

    Poszukajmy całej logiki potoku.
    ![Logika całego potoku](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)


## <a name="next-steps"></a>Następne kroki

* Utwórz resztę logiki przepływu danych, korzystając z mapowania [przekształceń](concepts-data-flow-overview.md)przepływów danych.
