---
title: 'Szybki Start: odczytywanie danych z ADLS Gen2 do Pandas Dataframe'
description: Odczytywanie danych z konta Azure Data Lake Storage Gen2 w ramce dataPandas przy użyciu języka Python w programie Synapse Studio w usłudze Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye, negust
ms.date: 03/23/2021
author: garyericson
ms.author: garye
ms.openlocfilehash: b7358c522cf12e7856496ad71fda393394e7ceab
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969876"
---
# <a name="quickstart-read-data-from-adls-gen2-to-pandas-dataframe-in-azure-synapse-analytics"></a>Szybki Start: odczytywanie danych z ADLS Gen2 do Pandas Dataframe w usłudze Azure Synapse Analytics

W tym przewodniku szybki start dowiesz się, jak łatwo używać języka Python do odczytywania danych z Azure Data Lake Storage (ADLS) Gen2 do Pandas Dataframe w usłudze Azure Synapse Analytics.

Z notesu programu Synapse Studio:

- Nawiązywanie połączenia z kontenerem w Data Lake Storage Gen2 połączonym z obszarem roboczym usługi Azure Synapse Analytics
- Odczytaj dane z notesu PySpark przy użyciu polecenia `spark.read.load`
- Konwertuj dane na Pandasą ramkę danych przy użyciu `.toPandas()`

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/).
- Obszar roboczy Synapse Analytics z Data Lake Storage Gen2 skonfigurowany jako magazyn domyślny — musi to być **współautor danych obiektów blob magazynu** systemu Data Lake Storage Gen2 systemu plików, z którym pracujesz. Aby uzyskać szczegółowe informacje na temat tworzenia obszaru roboczego, zobacz [Tworzenie obszaru roboczego Synapse](get-started-create-workspace.md).
- Apache Spark pulę w obszarze roboczym — zobacz [Tworzenie puli Apache Spark bezserwerowej](get-started-analyze-spark.md#create-a-serverless-apache-spark-pool).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="upload-sample-data-to-adls-gen2"></a>Przekaż przykładowe dane do ADLS Gen2

1. W Azure Portal Utwórz kontener w tym samym Data Lake Storage Gen2 używanym przez program Synapse Studio. Możesz pominąć ten krok, jeśli chcesz użyć domyślnego konta połączonego magazynu w obszarze roboczym usługi Azure Synapse Analytics.

1. W programie Synapse Studio kliknij pozycję **dane**, wybierz kartę **połączone** i wybierz kontener w obszarze **Azure Data Lake Storage Gen2**.

1. Pobierz przykładowy plik [RetailSales.csv](https://github.com/Azure-Samples/Synapse/blob/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData/RetailSales.csv) i przekaż go do kontenera.

1. Wybierz przekazany plik, kliknij pozycję **Właściwości** i skopiuj wartość **ścieżka ABFSS** .

## <a name="read-data-from-adls-gen2-into-a-pandas-dataframe"></a>Odczytaj dane z ADLS Gen2 do Pandas Dataframe

1. W lewym okienku kliknij pozycję **programowanie**.

1. Kliknij **+** i wybierz "Notes", aby utworzyć nowy Notes.

1. W polu **Dołącz do** wybierz pulę Apache Spark. Jeśli go nie masz, kliknij pozycję **Utwórz pulę Apache Spark**.

1. W komórce kod notesu wklej następujący kod w języku Python, wstawiając wcześniej skopiowaną ścieżkę ABFSS:

   ```python
   %%pyspark
   data_path = spark.read.load('<ABFSS Path to RetailSales.csv>', format='csv', header=True)
   data_path.show(10)
   
   print('Converting to Pandas.')
   
   pdf = data_path.toPandas()
   print(pdf)
   ```

1. Uruchom komórkę.

Po kilku minutach tekst wyświetlany powinien wyglądać podobnie do poniższego.

```text
Command executed in 25s 324ms by gary on 03-23-2021 17:40:23.481 -07:00

Job execution Succeeded Spark 2 executors 8 cores

+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
|storeId|productCode|quantity|logQuantity|advertising|price|weekStarting|                  id|
+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
|      2| surface.go|     105|9.264828557|          1|  159|   6/15/2017|d6bd47a7-2ad6-4f0...|
|      2| surface.go|      80|8.987196821|          0|  269|   7/27/2017|64cc74c2-c7da-4e1...|
|      2| surface.go|      68|8.831711918|          1|  209|    8/3/2017|9a2d164b-5e44-44d...|
|      2| surface.go|      28|7.965545573|          0|  209|   8/10/2017|b8cd9987-1d5a-4f4...|
|      2| surface.go|      16|7.377758908|          0|  209|   8/24/2017|ac0ec099-e102-4bf...|
|      2| surface.go|     253| 10.1402973|          1|  189|   8/31/2017|3d22c002-b04c-409...|
|      2| surface.go|     107|9.282847063|          0|  189|    9/7/2017|b6e19699-d684-449...|
|      2| surface.go|      66|8.803273983|          0|  189|   9/14/2017|e89a5838-fb8f-413...|
|      2| surface.go|      65|8.793612072|          0|  179|   9/21/2017|c3278682-16c0-483...|
|      2| surface.go|      17|7.454719949|          0|  269|  10/12/2017|f40190c1-b2ed-46f...|
+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
only showing top 10 rows

Converting to Pandas.
      storeId  ...                                    id
0           2  ...  d6bd47a7-2ad6-4f0a-b8de-ed1386cae5ea
1           2  ...  64cc74c2-c7da-4e12-af64-c95bdf429934
2           2  ...  9a2d164b-5e44-44d7-9837-cf9ae6566c99
3           2  ...  b8cd9987-1d5a-4f4f-9346-719d73b1f7f0
4           2  ...  ac0ec099-e102-4bfc-9775-983b151dcd03
...       ...  ...                                   ...
28942     137  ...  6af00133-7015-415d-831b-ddf05bb5828c
28943     137  ...  1e0d3a21-ab43-49c4-89e2-49d202821807
28944     137  ...  5cc7e50a-6aa4-419b-a933-905a667aa2df
28945     137  ...  650ca506-7a4f-46f8-b2e1-e52ceffadf16
28946     137  ...  9bb216f6-04ec-4b61-9e68-34772b814c44

[28947 rows x 8 columns]
```

## <a name="next-steps"></a>Następne kroki

- [Co to jest usługa Azure Synapse Analytics?](overview-what-is.md)
- [Rozpoczynanie pracy z usługą Azure Synapse Analytics](get-started.md)
- [Utwórz bezserwerową pulę Apache Spark](get-started-analyze-spark.md#create-a-serverless-apache-spark-pool)
