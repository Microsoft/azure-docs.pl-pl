---
title: Analizowanie pliku JSON procesu & przy użyciu Apache Hive — usługa Azure HDInsight
description: Dowiedz się, jak używać dokumentów JSON i analizować je za pomocą Apache Hive w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: d3c5077450dd4ec59f5ea7bc8f37879f9aa775bf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868940"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Przetwarzanie i analizowanie dokumentów JSON przy użyciu Apache Hive w usłudze Azure HDInsight

Dowiedz się, jak przetwarzać i analizować pliki JavaScript Object Notation (JSON) za pomocą Apache Hive w usłudze Azure HDInsight. W tym artykule jest stosowany następujący dokument JSON:

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

Plik można znaleźć pod adresem `wasb://processjson@hditutorialdata.blob.core.windows.net/` . Aby uzyskać więcej informacji na temat korzystania z usługi Azure Blob Storage z usługą HDInsight, zobacz [Korzystanie z usługi Azure Blob Storage zgodnej z systemem plików HDFS przy użyciu Apache Hadoop w usłudze HDInsight](../hdinsight-hadoop-use-blob-storage.md). Plik można skopiować do domyślnego kontenera klastra.

W tym artykule użyto konsoli Apache Hive. Aby uzyskać instrukcje dotyczące sposobu otwierania konsoli programu Hive, zobacz temat [Korzystanie z programu Apache Ambari Hive View z Apache Hadoop w usłudze HDInsight](apache-hadoop-use-hive-ambari-view.md).

> [!NOTE]  
> Widok Hive nie jest już dostępny w usłudze HDInsight 4,0.

## <a name="flatten-json-documents"></a>Spłaszcz dokumenty JSON

Metody wymienione w następnej sekcji wymagają, aby dokument JSON był złożony z pojedynczego wiersza. Dlatego należy spłaszczyć dokument JSON do ciągu. Jeśli dokument JSON został już spłaszczony, możesz pominąć ten krok i przejść bezpośrednio do następnej sekcji w temacie Analizowanie danych JSON. Aby spłaszczyć dokument JSON, uruchom następujący skrypt:

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

Plik RAW JSON znajduje się w lokalizacji `wasb://processjson@hditutorialdata.blob.core.windows.net/` . Tabela **StudentsRaw** Hive wskazuje nieprzetworzony dokument JSON, który nie jest spłaszczony.

W tabeli Hive **StudentsOneLine** są przechowywane dane w domyślnym systemie plików usługi HDInsight pod ścieżką **/JSON/Students/** .

Instrukcja **INSERT** wypełnia tabelę **StudentOneLine** za pomocą spłaszczonych danych JSON.

Instrukcja **SELECT** zwraca tylko jeden wiersz.

Oto dane wyjściowe instrukcji **SELECT** :

:::image type="content" source="./media/using-json-in-hive/hdinsight-flatten-json.png" alt-text="Spłaszczanie dokumentu JSON przy użyciu usługi HDInsight" border="true":::

## <a name="analyze-json-documents-in-hive"></a>Analizowanie dokumentów JSON w usłudze Hive

Program Hive oferuje trzy różne mechanizmy uruchamiania zapytań w dokumentach JSON lub można napisać własne:

* Użyj get_json_object funkcji zdefiniowanej przez użytkownika (UDF).
* Użyj json_tuple UDF.
* Użyj niestandardowego serializatora/deserializacji (elementu SERDE).
* Napisz własny format UDF przy użyciu języka Python lub innych języków. Aby uzyskać więcej informacji na temat sposobu uruchamiania własnego kodu w języku Python za pomocą usługi Hive, zobacz [Python UDF with Apache Hive i Apache świni](./python-udf-hdinsight.md).

### <a name="use-the-get_json_object-udf"></a>Użyj get_json_object UDF

Program Hive zawiera wbudowaną funkcję UDF o nazwie [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) , która wysyła zapytania JSON podczas środowiska uruchomieniowego. Ta metoda przyjmuje dwa argumenty: nazwę tabeli i nazwę metody. Nazwa metody ma spłaszczony dokument JSON i pole JSON, które należy przeanalizować. Spójrzmy na przykład, aby zobaczyć, jak działa ta metoda UDF.

Następujące zapytanie zwraca imię i nazwisko dla każdego ucznia:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Oto dane wyjściowe po uruchomieniu tego zapytania w oknie konsoli:

:::image type="content" source="./media/using-json-in-hive/hdinsight-get-json-object.png" alt-text="Apache Hive pobiera funkcję UDF obiektu JSON" border="true":::

Istnieją ograniczenia dotyczące get_json_object UDF:

* Ponieważ każde pole w zapytaniu wymaga przeanalizowania zapytania, ma wpływ na wydajność.
* **Pobierz \_ JSON_OBJECT ()** zwraca ciąg reprezentujący tablicę. Aby przekonwertować tę tablicę na tablicę programu Hive, należy użyć wyrażeń regularnych w celu zastąpienia nawiasów kwadratowych "[" i "]", a następnie należy wywołać metodę Split, aby pobrać tablicę.

Ta konwersja polega na tym, że witryna typu wiki programu Hive zaleca korzystanie z **json_tuple**.  

### <a name="use-the-json_tuple-udf"></a>Użyj json_tuple UDF

Inna usługa UDF udostępniana przez program Hive jest nazywana [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), która jest większa niż [get_ _object JSON](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Ta metoda pobiera zestaw kluczy i ciąg JSON. Następnie zwraca spójną krotkę wartości. Następujące zapytanie zwraca identyfikator ucznia i ocenę klasy z dokumentu JSON:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Dane wyjściowe tego skryptu w konsoli programu Hive:

:::image type="content" source="./media/using-json-in-hive/hdinsight-json-tuple.png" alt-text="Apache Hive wyniki zapytania JSON" border="true":::

W formacie `json_tuple` UDF użyto składni [widoku bocznego](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) w gałęzi, która umożliwia \_ krotkom JSON Tworzenie tabeli wirtualnej przez zastosowanie funkcji UDT do każdego wiersza oryginalnej tabeli. Złożone dane JSON stają się zbyt nieporęczny z powodu wielokrotnego użycia **widoku bocznego**. Ponadto **JSON_TUPLE** nie może obsłużyć ZAGNIEŻDŻONych notacji JSON.

### <a name="use-a-custom-serde"></a>Użyj niestandardowego elementu SERDE

Elementu SERDE jest najlepszym wyborem do analizowania zagnieżdżonych dokumentów JSON. Pozwala on definiować schemat JSON, a następnie można użyć schematu do analizy dokumentów. Aby uzyskać instrukcje, zobacz [How to use a Custom JSON elementu SERDE with Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Podsumowanie

Typ operatora JSON w wyborze gałęzi zależy od danego scenariusza. Korzystając z prostego dokumentu JSON i jednego pola do wyszukania, wybierz opcję UDF **get_json_object** platformy Hive. Jeśli masz więcej niż jeden klucz do wyszukania, możesz użyć **json_tuple**. W przypadku dokumentów zagnieżdżonych Użyj **elementu SERDE JSON**.

## <a name="next-steps"></a>Następne kroki

Aby poznać pokrewne artykuły, zobacz:

* [Analizowanie przykładowego pliku Apache Log4J przy użyciu Apache Hive i HiveQL z Apache Hadoop w usłudze HDInsight](./hdinsight-use-hive.md)
* [Analizowanie danych dotyczących opóźnień lotów przy użyciu interakcyjnych zapytań w usłudze HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)