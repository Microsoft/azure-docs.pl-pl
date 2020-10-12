---
title: Eksplorowanie danych w tabelach programu Hive za pomocą zapytań Hive — proces nauki danych zespołu
description: Użyj przykładowych skryptów Hive, które są używane do eksplorowania danych w tabelach programu Hive w klastrze usługi HDInsight Hadoop.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c0dfa06e2ece2ba4631c0d5681b066ab0134daba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86085671"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Eksplorowanie danych tabel programu Hive za pomocą zapytań Hive

Ten artykuł zawiera przykładowe skrypty Hive, które są używane do eksplorowania danych w tabelach programu Hive w klastrze usługi HDInsight Hadoop.

To zadanie jest krokiem w [procesie nauki danych zespołu](overview.md).

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz:

* Utworzono konto usługi Azure Storage. Jeśli potrzebujesz instrukcji, zobacz [Tworzenie konta usługi Azure Storage](../../storage/common/storage-account-create.md)
* Zainicjowano obsługę niestandardowego klastra Hadoop z usługą HDInsight. Jeśli potrzebujesz instrukcji, zobacz [Dostosowywanie klastrów Azure HDInsight Hadoop na potrzeby zaawansowanej analizy](customize-hadoop-cluster.md).
* Dane zostały przekazane do tabel programu Hive w klastrach Azure HDInsight Hadoop. Jeśli tak nie jest, postępuj zgodnie z instrukcjami w temacie [Tworzenie i ładowanie danych do tabel programu Hive](move-hive-tables.md) , aby najpierw przekazać dane do tabel programu Hive.
* Włączono dostęp zdalny do klastra. Jeśli potrzebujesz instrukcji, zobacz [dostęp do węzła głównego klastra usługi Hadoop](customize-hadoop-cluster.md).
* Jeśli potrzebujesz instrukcji dotyczących przesyłania zapytań Hive, zobacz [Jak przesłać zapytania Hive](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Przykładowe skrypty zapytań Hive do eksploracji danych
1. Pobierz liczbę obserwacji na partycję  `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Pobierz liczbę obserwacji dziennie  `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Pobieranie poziomów w kolumnie kategorii  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Pobierz liczbę poziomów w połączeniu dwóch kolumn kategorii  `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Pobieranie dystrybucji dla kolumn liczbowych  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Wyodrębnij rekordy z sprzęgania dwóch tabel
   
    ```hiveql
    SELECT
        a.<common_columnname1> as <new_name1>,
        a.<common_columnname2> as <new_name2>,
        a.<a_column_name1> as <new_name3>,
        a.<a_column_name2> as <new_name4>,
        b.<b_column_name1> as <new_name5>,
        b.<b_column_name2> as <new_name6>
    FROM
        (
        SELECT <common_columnname1>,
            <common_columnname2>,
            <a_column_name1>,
            <a_column_name2>,
        FROM <databasename>.<tablename1>
        ) a
        join
        (
        SELECT <common_columnname1>,
            <common_columnname2>,
            <b_column_name1>,
            <b_column_name2>,
        FROM <databasename>.<tablename2>
        ) b
        ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>
    ```

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Dodatkowe skrypty zapytań dla scenariuszy danych podróży z taksówką
Przykłady zapytań, które są specyficzne dla scenariuszy [danych dotyczących rejsów z NYCem](https://chriswhong.com/open-data/foil_nyc_taxi/) , są również dostępne w [repozytorium GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Te zapytania mają już określony schemat danych i są gotowe do przesłania do uruchomienia.

