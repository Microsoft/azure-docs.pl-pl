---
title: Indeksy hiperprzestrzeni dla Apache Spark
description: Optymalizacja wydajności platformy Apache Spark przy użyciu indeksów Hyperspace
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 08/12/2020
ms.author: euang
ms.reviewer: euang
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 1833a606f3dbbc9826858bac4f3ba056b5b7ad8a
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369866"
---
# <a name="hyperspace-an-indexing-subsystem-for-apache-spark"></a>Hiperprzestrzeni: podsystem indeksowania dla Apache Spark

Hiperprzestrzeni wprowadza możliwość Apache Spark użytkownikom na tworzenie indeksów w ich zestawach danych, takich jak CSV, JSON i Parquet, i używanie ich do potencjalnego przyspieszania zapytań i obciążeń.

W tym artykule wykorzystamy podstawy hiperprzestrzeni, podkreślamy jego prostotę i pokazują, jak może on być używany przez same osoby.

Zastrzeżenie: hiperprzestrzeni umożliwia przyspieszenie obciążeń lub zapytań w dwóch okolicznościach:

* Zapytania zawierają filtry dotyczące predykatów o wysokiej wartości selektywnej. Na przykład możesz chcieć wybrać 100 pasujących wierszy z miliona wierszy kandydujących.
* Zapytania zawierają sprzężenie wymagające dużych losowo. Na przykład możesz chcieć dołączyć zestaw danych 100 GB z zestawem danych o pojemności 10 GB.

Warto uważnie monitorować obciążenia i określać, czy indeksowanie pomaga w poszczególnych przypadkach.

Ten dokument jest również dostępny w formie notesu dla języków [Python](https://github.com/microsoft/hyperspace/blob/master/notebooks/python/Hitchhikers%20Guide%20to%20Hyperspace.ipynb), [C#](https://github.com/microsoft/hyperspace/blob/master/notebooks/csharp/Hitchhikers%20Guide%20to%20Hyperspace.ipynb)i [Scala](https://github.com/microsoft/hyperspace/blob/master/notebooks/scala/Hitchhikers%20Guide%20to%20Hyperspace.ipynb)

## <a name="setup"></a>Konfigurowanie

Aby rozpocząć od, Rozpocznij nową sesję platformy Spark. Ponieważ ten dokument jest samouczkiem tylko do zilustrowania, jakie miejsce może zaoferować, należy wprowadzić zmianę konfiguracji, która pozwala na wyróżnienie tego, jakie miejsce jest wykonywane na małych zestawach danych. 

Domyślnie platforma Spark używa sprzężenia emisji, aby zoptymalizować zapytania sprzężenia, gdy rozmiar danych jednej ze stron sprzężenia jest mały (czyli w przypadku przykładowych danych używanych w tym samouczku). W związku z tym wyłączamy sprzężenia emisji, tak aby później, gdy uruchomimy zapytania sprzężenia, platforma Spark używa sprzężenia Sort-merge. Jest to głównie pokazuje, jak indeksy hiperprzestrzeni byłyby stosowane na dużą skalę w celu przyspieszenia zapytań do sprzężeń.

Dane wyjściowe w następującej komórce zawierają odwołanie do pomyślnie utworzonej sesji platformy Spark i drukuje "-1" jako wartość zmodyfikowanej konfiguracji dołączania, która wskazuje, że sprzężenie emisji zostało pomyślnie wyłączone.

:::zone pivot = "programming-language-scala"

```scala
// Start your Spark session
spark

// Disable BroadcastHashJoin, so Spark will use standard SortMergeJoin. Currently, Hyperspace indexes utilize SortMergeJoin to speed up query.
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", -1)

// Verify that BroadcastHashJoin is set correctly
println(spark.conf.get("spark.sql.autoBroadcastJoinThreshold"))

```

::: zone-end

:::zone pivot = "programming-language-python"

```python
# Start your Spark session.
spark

# Disable BroadcastHashJoin, so Spark will use standard SortMergeJoin. Currently, Hyperspace indexes utilize SortMergeJoin to speed up query.
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", -1)

# Verify that BroadcastHashJoin is set correctly 
print(spark.conf.get("spark.sql.autoBroadcastJoinThreshold"))
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
// Disable BroadcastHashJoin, so Spark will use standard SortMergeJoin. Currently, Hyperspace indexes utilize SortMergeJoin to speed up query.
spark.Conf().Set("spark.sql.autoBroadcastJoinThreshold", -1);

// Verify that BroadcastHashJoin is set correctly.
Console.WriteLine(spark.Conf().Get("spark.sql.autoBroadcastJoinThreshold"));
```

::: zone-end

Wyniki w:

```console
res3: org.apache.spark.sql.SparkSession = org.apache.spark.sql.SparkSession@297e957d
-1
```

## <a name="data-preparation"></a>Przygotowywanie danych

Aby przygotować środowisko, utworzysz przykładowe rekordy danych i Zapisz je jako pliki danych Parquet. Parquet jest używany na potrzeby ilustracji, ale można również użyć innych formatów, takich jak CSV. W kolejnych komórkach zobaczysz, jak utworzyć kilka indeksów hiperprzestrzeni dla tego przykładowego zestawu danych i użyć ich przy uruchamianiu zapytań.

Przykładowe rekordy odnoszą się do dwóch zestawów danych: działu i pracownika. Należy skonfigurować ścieżki "empLocation" i "deptLocation", aby na koncie magazynu wskazywały żądaną lokalizację w celu zapisania wygenerowanych plików danych.

W danych wyjściowych w poniższej komórce przedstawiono zawartość naszych zestawów danych jako listy Triplets, a następnie odwołania do ramek dataframes utworzonych w celu zapisania zawartości każdego zestawu danych w naszym preferowanej lokalizacji.

:::zone pivot = "programming-language-scala"

```scala
import org.apache.spark.sql.DataFrame

// Sample department records
val departments = Seq(
      (10, "Accounting", "New York"),
      (20, "Research", "Dallas"),
      (30, "Sales", "Chicago"),
      (40, "Operations", "Boston"))

// Sample employee records
val employees = Seq(
      (7369, "SMITH", 20),
      (7499, "ALLEN", 30),
      (7521, "WARD", 30),
      (7566, "JONES", 20),
      (7698, "BLAKE", 30),
      (7782, "CLARK", 10),
      (7788, "SCOTT", 20),
      (7839, "KING", 10),
      (7844, "TURNER", 30),
      (7876, "ADAMS", 20),
      (7900, "JAMES", 30),
      (7934, "MILLER", 10),
      (7902, "FORD", 20),
      (7654, "MARTIN", 30))

// Save sample data in the Parquet format
import spark.implicits._
val empData: DataFrame = employees.toDF("empId", "empName", "deptId")
val deptData: DataFrame = departments.toDF("deptId", "deptName", "location")

val empLocation: String = "/<yourpath>/employees.parquet"       //TODO ** customize this location path **
val deptLocation: String = "/<yourpath>/departments.parquet"     //TODO ** customize this location path **
empData.write.mode("overwrite").parquet(empLocation)
deptData.write.mode("overwrite").parquet(deptLocation)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

from pyspark.sql.types import StructField, StructType, StringType, IntegerType

# Sample department records
departments = [(10, "Accounting", "New York"), (20, "Research", "Dallas"), (30, "Sales", "Chicago"), (40, "Operations", "Boston")]

# Sample employee records
employees = [(7369, "SMITH", 20), (7499, "ALLEN", 30), (7521, "WARD", 30), (7566, "JONES", 20), (7698, "BLAKE", 30)]

# Create a schema for the dataframe
dept_schema = StructType([StructField('deptId', IntegerType(), True), StructField('deptName', StringType(), True), StructField('location', StringType(), True)])
emp_schema = StructType([StructField('empId', IntegerType(), True), StructField('empName', StringType(), True), StructField('deptId', IntegerType(), True)])

departments_df = spark.createDataFrame(departments, dept_schema)
employees_df = spark.createDataFrame(employees, emp_schema)

#TODO ** customize this location path **
emp_Location = "/<yourpath>/employees.parquet"
dept_Location = "/<yourpath>/departments.parquet"

employees_df.write.mode("overwrite").parquet(emp_Location)
departments_df.write.mode("overwrite").parquet(dept_Location)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

using Microsoft.Spark.Sql.Types;

// Sample department records
var departments = new List<GenericRow>()
{
    new GenericRow(new object[] {10, "Accounting", "New York"}),
    new GenericRow(new object[] {20, "Research", "Dallas"}),
    new GenericRow(new object[] {30, "Sales", "Chicago"}),
    new GenericRow(new object[] {40, "Operations", "Boston"})
};

// Sample employee records
var employees = new List<GenericRow>() {
      new GenericRow(new object[] {7369, "SMITH", 20}),
      new GenericRow(new object[] {7499, "ALLEN", 30}),
      new GenericRow(new object[] {7521, "WARD", 30}),
      new GenericRow(new object[] {7566, "JONES", 20}),
      new GenericRow(new object[] {7698, "BLAKE", 30}),
      new GenericRow(new object[] {7782, "CLARK", 10}),
      new GenericRow(new object[] {7788, "SCOTT", 20}),
      new GenericRow(new object[] {7839, "KING", 10}),
      new GenericRow(new object[] {7844, "TURNER", 30}),
      new GenericRow(new object[] {7876, "ADAMS", 20}),
      new GenericRow(new object[] {7900, "JAMES", 30}),
      new GenericRow(new object[] {7934, "MILLER", 10}),
      new GenericRow(new object[] {7902, "FORD", 20}),
      new GenericRow(new object[] {7654, "MARTIN", 30})
};

// Save sample data in the Parquet format
var departmentSchema = new StructType(new List<StructField>()
{
    new StructField("deptId", new IntegerType()),
    new StructField("deptName", new StringType()),
    new StructField("location", new StringType())
});
var employeeSchema = new StructType(new List<StructField>()
{
    new StructField("empId", new IntegerType()),
    new StructField("empName", new StringType()),
    new StructField("deptId", new IntegerType())
});

DataFrame empData = spark.CreateDataFrame(employees, employeeSchema); 
DataFrame deptData = spark.CreateDataFrame(departments, departmentSchema); 

string empLocation = "/<yourpath>/employees.parquet";       //TODO ** customize this location path **
string deptLocation = "/<yourpath>/departments.parquet";     //TODO ** customize this location path **
empData.Write().Mode("overwrite").Parquet(empLocation);
deptData.Write().Mode("overwrite").Parquet(deptLocation);

```

::: zone-end

Wyniki w:

```console
import org.apache.spark.sql.DataFrame  
departments: Seq[(Int, String, String)] = List((10,Accounting,New York), (20,Research,Dallas), (30,Sales,Chicago), (40,Operations,Boston))  
employees: Seq[(Int, String, Int)] = List((7369,SMITH,20), (7499,ALLEN,30), (7521,WARD,30), (7566,JONES,20), (7698,BLAKE,30), (7782,CLARK,10), (7788,SCOTT,20), (7839,KING,10), (7844,TURNER,30), (7876,ADAMS,20), (7900,JAMES,30), (7934,MILLER,10), (7902,FORD,20), (7654,MARTIN,30))  
import spark.implicits._  
empData: org.apache.spark.sql.DataFrame = [empId: int, empName: string ... 1 more field]  
deptData: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]  
empLocation: String = /your-path/employees.parquet  
deptLocation: String = /your-path/departments.parquet  
```

Sprawdźmy zawartość utworzonych przez siebie plików Parquet, aby upewnić się, że zawierają one oczekiwane rekordy w poprawnym formacie. Później będziemy używać tych plików danych do tworzenia indeksów hiperprzestrzeni i uruchamiania przykładowych zapytań.

Uruchomienie następującej komórki powoduje utworzenie i wyjście, które wyświetla wiersze w ramkach danych pracownika i działu w formie tabelarycznej. Powinna istnieć 14 pracowników i 4 działy, każdy pasujący do jednego z Triplets utworzonych w poprzedniej komórce.

:::zone pivot = "programming-language-scala"

```scala
// empLocation and deptLocation are the user defined locations above to save parquet files
val empDF: DataFrame = spark.read.parquet(empLocation)
val deptDF: DataFrame = spark.read.parquet(deptLocation)

// Verify the data is available and correct
empDF.show()
deptDF.show()
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# emp_Location and dept_Location are the user-defined locations above to save parquet files
emp_DF = spark.read.parquet(emp_Location)
dept_DF = spark.read.parquet(dept_Location)

# Verify the data is available and correct
emp_DF.show()
dept_DF.show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// empLocation and deptLocation are the user-defined locations above to save parquet files
DataFrame empDF = spark.Read().Parquet(empLocation);
DataFrame deptDF = spark.Read().Parquet(deptLocation);

// Verify the data is available and correct
empDF.Show();
deptDF.Show();

```

::: zone-end

Wyniki w:

```console
empDF: org.apache.spark.sql.DataFrame = [empId: int, empName: string ... 1 more field]  
deptDF: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]
```

```console
|EmpId|EmpName|DeptId|
|-----|-------|------|
| 7499|  ALLEN|    30|
| 7521|   WARD|    30|
| 7369|  SMITH|    20|
| 7844| TURNER|    30|
| 7876|  ADAMS|    20|
| 7900|  JAMES|    30|
| 7934| MILLER|    10|
| 7839|   KING|    10|
| 7566|  JONES|    20|
| 7698|  BLAKE|    30|
| 7782|  CLARK|    10|
| 7788|  SCOTT|    20|
| 7902|   FORD|    20|
| 7654| MARTIN|    30|  
```

&nbsp; &nbsp;

```console
|DeptId|  DeptName|Location|
|------|----------|--------|
|    10|Accounting|New York|
|    40|Operations|  Boston|
|    20|  Research|  Dallas|
|    30|     Sales| Chicago|
```

## <a name="indexes"></a>Indeksy

Hiperprzestrzeni umożliwia tworzenie indeksów dla rekordów skanowanych z utrwalonych plików danych. Po ich pomyślnym utworzeniu wpis, który odpowiada indeksowi, zostanie dodany do metadanych hiperprzestrzeni. Te metadane są później używane przez optymalizator Apache Spark (z naszymi rozszerzeniami) podczas przetwarzania zapytań w celu znalezienia i użycia właściwych indeksów.

Po utworzeniu indeksów można wykonać kilka akcji:

* **Odśwież, jeśli dane bazowe zmienią się.** Można odświeżyć istniejący indeks, aby przechwycić zmiany.
* **Usuń, jeśli indeks nie jest wymagany.** Można wykonać nietrwałe usunięcie, czyli indeks nie został fizycznie usunięty, ale jest oznaczony jako "usunięty", aby nie był już używany w obciążeniach.
* **Próżniowy, jeśli indeks nie jest już wymagany.** Można próżniowo indeks, co wymusza fizyczne usunięcie zawartości indeksu i skojarzonych metadanych całkowicie z metadanych hiperprzestrzeni.

Odświeżanie, jeśli dane bazowe zmienią się, można odświeżyć istniejący indeks w celu przechwycenia.
Usuń Jeśli indeks nie jest wymagany, można wykonać usuwanie nietrwałe, czyli indeks nie został fizycznie usunięty, ale jest oznaczony jako "usunięty", więc nie jest już używany w obciążeniach.

W poniższych sekcjach pokazano, jak takie operacje zarządzania indeksami można wykonać w miejscu.

Najpierw należy zaimportować wymagane biblioteki i utworzyć wystąpienie hiperprzestrzeni. Później użyjesz tego wystąpienia, aby wywoływać różne interfejsy API hiperprzestrzeni do tworzenia indeksów na przykładowych danych i modyfikować te indeksy.

Wyjście z uruchamiania następującej komórki pokazuje odwołanie do utworzonego wystąpienia hiperprzestrzeni.

:::zone pivot = "programming-language-scala"

```scala
// Create an instance of Hyperspace
import com.microsoft.hyperspace._

val hyperspace: Hyperspace = Hyperspace()
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Create an instance of Hyperspace
hyperspace = Hyperspace(spark)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Create an instance of Hyperspace
using Microsoft.Spark.Extensions.Hyperspace;

Hyperspace hyperspace = new Hyperspace(spark);

```

::: zone-end

Wyniki w:

```console
import com.microsoft.hyperspace._  
hyperspace: com.microsoft.hyperspace.Hyperspace = com.microsoft.hyperspace.Hyperspace@1432f740
```

## <a name="create-indexes"></a>Tworzenie indeksów

Aby utworzyć indeks hiperprzestrzeni, należy podać dwie informacje:

* Ramka Dataframe, która odwołuje się do danych, które mają być indeksowane.
* Obiekt konfiguracji indeksu, IndexConfig, który określa nazwę indeksu i indeksowane i uwzględnione kolumny indeksu.

Zacznij od tworzenia trzech indeksów hiperprzestrzeni na naszych przykładowych danych: dwa indeksy w zestawie danych działu o nazwie "deptIndex1" i "deptIndex2" oraz jeden indeks w zestawie danych pracownika o nazwie "empIndex". Dla każdego indeksu potrzebna jest odpowiednia IndexConfig do przechwycenia nazwy wraz z listami kolumn dla indeksowanych i uwzględnionych kolumn. Uruchomienie następującej komórki powoduje utworzenie tych IndexConfigs i wyświetlenie ich w danych wyjściowych.

> [!Note]
> Kolumna indeksu to kolumna, która pojawia się w filtrach lub w warunkach sprzężenia. Kolumna uwzględniona jest kolumną wyświetlaną w ramach wyboru/projektu.

Na przykład, w następującym zapytaniu:

```sql
SELECT X
FROM T
WHERE Y = 2
```

T może być kolumną indeksu, a X może być kolumną uwzględnioną.

:::zone pivot = "programming-language-scala"

```scala
// Create index configurations
import com.microsoft.hyperspace.index.IndexConfig

val empIndexConfig: IndexConfig = IndexConfig("empIndex", Seq("deptId"), Seq("empName"))
val deptIndexConfig1: IndexConfig = IndexConfig("deptIndex1", Seq("deptId"), Seq("deptName"))
val deptIndexConfig2: IndexConfig = IndexConfig("deptIndex2", Seq("location"), Seq("deptName"))
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Create index configurations

emp_IndexConfig = IndexConfig("empIndex1", ["deptId"], ["empName"])
dept_IndexConfig1 = IndexConfig("deptIndex1", ["deptId"], ["deptName"])
dept_IndexConfig2 = IndexConfig("deptIndex2", ["location"], ["deptName"])

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

using Microsoft.Spark.Extensions.Hyperspace.Index;

var empIndexConfig = new IndexConfig("empIndex", new string[] {"deptId"}, new string[] {"empName"});
var deptIndexConfig1 = new IndexConfig("deptIndex1", new string[] {"deptId"}, new string[] {"deptName"});
var deptIndexConfig2 = new IndexConfig("deptIndex2", new string[] {"location"}, new string[] {"deptName"});

```

::: zone-end

Wyniki w:

```console
import com.microsoft.hyperspace.index.IndexConfig  
empIndexConfig: com.microsoft.hyperspace.index.IndexConfig = [indexName: empIndex; indexedColumns: deptid; includedColumns: empname]  
deptIndexConfig1: com.microsoft.hyperspace.index.IndexConfig = [indexName: deptIndex1; indexedColumns: deptid; includedColumns: deptname]  
deptIndexConfig2: com.microsoft.hyperspace.index.IndexConfig = [indexName: deptIndex2; indexedColumns: location; includedColumns: deptname]  
```
Teraz utworzysz trzy indeksy przy użyciu konfiguracji indeksu. W tym celu należy wywołać polecenie "CreateInstance" w naszym wystąpieniu hiperprzestrzeni. To polecenie wymaga konfiguracji indeksu i ramki danych zawierającej wiersze do indeksowania. Uruchomienie następującej komórki powoduje utworzenie trzech indeksów.

:::zone pivot = "programming-language-scala"

```scala
// Create indexes from configurations
import com.microsoft.hyperspace.index.Index

hyperspace.createIndex(empDF, empIndexConfig)
hyperspace.createIndex(deptDF, deptIndexConfig1)
hyperspace.createIndex(deptDF, deptIndexConfig2)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Create indexes from configurations

hyperspace.createIndex(emp_DF, emp_IndexConfig)
hyperspace.createIndex(dept_DF, dept_IndexConfig1)
hyperspace.createIndex(dept_DF, dept_IndexConfig2)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Create indexes from configurations
hyperspace.CreateIndex(empDF, empIndexConfig);
hyperspace.CreateIndex(deptDF, deptIndexConfig1);
hyperspace.CreateIndex(deptDF, deptIndexConfig2);

```

::: zone-end

Wyniki w:

```console
import com.microsoft.hyperspace.index.Index
```

## <a name="list-indexes"></a>Indeksy listy

Poniższy kod pokazuje, jak można wyświetlić listę wszystkich dostępnych indeksów w wystąpieniu hiperprzestrzeni. Używa interfejsu API "Indexs", który zwraca informacje o istniejących indeksach jako element Dataframe, aby można było wykonać dodatkowe operacje. 

Na przykład można wywołać prawidłowe operacje dla tej ramki danych w celu sprawdzenia jej zawartości lub przeanalizowania jej w dalszej części (na przykład filtrowanie określonych indeksów lub grupowanie ich zgodnie z określoną żądaną właściwością).

W poniższej komórce jest stosowana akcja "Pokaż" ramki danych w celu pełnego wydrukowania wierszy i wyświetlenia szczegółów naszych indeksów w formie tabelarycznej. Dla każdego indeksu można zobaczyć wszystkie miejsca, w których zapisano wszystkie informacje w metadanych. Natychmiast Zwróć uwagę na następujące kwestie:

* config. IndexName, config. indexedColumns, config. includedColumns i status. status są polami, do których użytkownik zwykle się odwołuje.
* dfSignature jest generowany automatycznie przez hiperprzestrzeni i jest unikatowy dla każdego indeksu. Hiperprzestrzeni używa tej sygnatury wewnętrznie, aby zachować indeks i wykorzystać go w czasie wykonywania zapytania.


W poniższych danych wyjściowych wszystkie trzy indeksy powinny mieć wartość "ACTIVE" jako stan, a ich nazwy, kolumny indeksowane i uwzględnione kolumny powinny być zgodne z informacjami zdefiniowanymi w powyższych konfiguracjach indeksu.

:::zone pivot = "programming-language-scala"

```scala
hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

hyperspace.Indexes().Show();

```

::: zone-end

Wyniki w:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="delete-indexes"></a>Usuwanie indeksów

Istniejący indeks można usunąć za pomocą interfejsu API "deleteIndex" i podając nazwę indeksu. Usuwanie indeksu wykonuje usuwanie nietrwałe: polega głównie na aktualizowaniu stanu indeksu w metadanych hiperprzestrzeni z "aktywne" do "usunięte". Spowoduje to wykluczenie usuniętego indeksu z jakichkolwiek przyszłych optymalizacji zapytań i hiperprzestrzeni nie będą już wybierane tego indeksu dla jakichkolwiek zapytań. 

Jednak pliki indeksów dla usuniętego indeksu nadal pozostają dostępne (ponieważ jest to usuwanie nietrwałe), dzięki czemu można przywrócić indeks, jeśli zostanie wyświetlony monit użytkownika.

Poniższa komórka usuwa indeks o nazwie "deptIndex2" i wyświetla listę metadanych hiperprzestrzeni po tym elemencie. Dane wyjściowe powinny być podobne do powyższej komórki dla "indeksy list" z wyjątkiem "deptIndex2", który teraz powinien zmienić jego stan na "usunięty".

:::zone pivot = "programming-language-scala"

```scala
hyperspace.deleteIndex("deptIndex2")

hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.deleteIndex("deptIndex2")
hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
hyperspace.DeleteIndex("deptIndex2");

hyperspace.Indexes().Show();

```

::: zone-end

Wyniki w:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="restore-indexes"></a>Przywróć indeksy

Aby przywrócić usunięty indeks, można użyć interfejsu API "restoreIndex". Spowoduje to przywrócenie najnowszej wersji indeksu do stanu aktywnego i umożliwi ponowne użycie go dla zapytań. W poniższej komórce przedstawiono przykład użycia "restoreIndex". Usuwasz "deptIndex1" i przywracamy go. Po wywołaniu "restoreIndex" w danych wyjściowych zostanie najpierw wyświetlony stan "deptIndex1" ("usunięty"), po odwołaniu polecenia "deleteIndex" i powrocie do stanu "aktywny".

:::zone pivot = "programming-language-scala"

```scala
hyperspace.deleteIndex("deptIndex1")

hyperspace.indexes.show

hyperspace.restoreIndex("deptIndex1")

hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.deleteIndex("deptIndex1")
hyperspace.indexes().show()
hyperspace.restoreIndex("deptIndex1")
hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

hyperspace.DeleteIndex("deptIndex1");
hyperspace.Indexes().Show();
hyperspace.RestoreIndex("deptIndex1");
hyperspace.Indexes().Show();

```

::: zone-end

Wyniki w:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.indexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

&nbsp; &nbsp;

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="vacuum-indexes"></a>Indeksy próżniowe

Można wykonać twarde usuwanie, czyli całkowicie usunąć pliki i wpis metadanych dla usuniętego indeksu przy użyciu polecenia **vacuumIndex** . Ta akcja jest nieodwracalna. Fizycznie usuwa wszystkie pliki indeksu, co oznacza, że jest to trudne do usunięcia.

W poniższej komórce przepróżniuje indeks "deptIndex2" i wyświetla metadane hiperprzestrzeni po podciśnieniu. Należy wyświetlić wpisy metadanych dla dwóch indeksów "deptIndex1" i "empIndex", zarówno w stanie "ACTIVE", jak i bez wpisu dla "deptIndex2".

:::zone pivot = "programming-language-scala"

```scala
hyperspace.vacuumIndex("deptIndex2")

hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.vacuumIndex("deptIndex2")
hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

hyperspace.VacuumIndex("deptIndex2");
hyperspace.Indexes().Show();

```

::: zone-end

Wyniki w:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="enable-or-disable-hyperspace"></a>Włącz lub Wyłącz hiperprzestrzeni

Hiperprzestrzeni udostępnia interfejsy API umożliwiające Włączanie lub wyłączanie użycia indeksów przy użyciu platformy Spark.

* Za pomocą polecenia **enableHyperspace** reguły optymalizacji hiperprzestrzeni stają się widoczne dla Optymalizatora Spark i wykorzystują istniejące indeksy hiperprzestrzeni do optymalizowania zapytań użytkowników.
* Przy użyciu polecenia **disableHyperspace** reguły hiperprzestrzeni nie są już stosowane podczas optymalizacji zapytania. Wyłączenie obszaru hiperprzestrzeni nie ma wpływu na utworzone indeksy, ponieważ pozostają nienaruszone.

W poniższej komórce pokazano, jak można użyć tych poleceń do włączenia lub wyłączenia hiperprzestrzeni. Dane wyjściowe zawierają odwołanie do istniejącej sesji platformy Spark, której konfiguracja została zaktualizowana.

:::zone pivot = "programming-language-scala"

```scala
// Enable Hyperspace
spark.enableHyperspace

// Disable Hyperspace
spark.disableHyperspace
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Enable Hyperspace
Hyperspace.enable(spark)

# Disable Hyperspace
Hyperspace.disable(spark)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Enable Hyperspace
spark.EnableHyperspace();

// Disable Hyperspace
spark.DisableHyperspace();

```

::: zone-end

Wyniki w:

```console
res48: org.apache.spark.sql.Spark™Session = org.apache.spark.sql.SparkSession@39fe1ddb  
res51: org.apache.spark.sql.Spark™Session = org.apache.spark.sql.SparkSession@39fe1ddb
```

## <a name="index-usage"></a>Użycie indeksu

Aby zapewnić, że platforma Spark użyje indeksów hiperprzestrzeni podczas przetwarzania zapytania, należy upewnić się, że funkcja hiperprzestrzeni jest włączona.

W poniższej komórce jest włączona funkcja hiperprzestrzeni i tworzone są dwie ramki danych zawierające dane przykładowe rekordy, które służą do uruchamiania przykładowych zapytań. Dla każdej ramki danych drukowane są kilka przykładowych wierszy.

:::zone pivot = "programming-language-scala"

```scala
// Enable Hyperspace
spark.enableHyperspace

val empDFrame: DataFrame = spark.read.parquet(empLocation)
val deptDFrame: DataFrame = spark.read.parquet(deptLocation)

empDFrame.show(5)
deptDFrame.show(5)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Enable Hyperspace
Hyperspace.enable(spark)

emp_DF = spark.read.parquet(emp_Location)
dept_DF = spark.read.parquet(dept_Location)

emp_DF.show(5)
dept_DF.show(5)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Enable Hyperspace
spark.EnableHyperspace();

DataFrame empDFrame = spark.Read().Parquet(empLocation);
DataFrame deptDFrame = spark.Read().Parquet(deptLocation);

empDFrame.Show(5);
deptDFrame.Show(5);

```

::: zone-end

Wyniki w:

```console
res53: org.apache.spark.sql.Spark™Session = org.apache.spark.sql.Spark™Session@39fe1ddb  
empDFrame: org.apache.spark.sql.DataFrame = [empId: int, empName: string ... 1 more field]  
deptDFrame: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]  
```

&nbsp; &nbsp;

```console
|empId|empName|deptId|
|-----|-------|------|
| 7499|  ALLEN|    30|
| 7521|   WARD|    30|
| 7369|  SMITH|    20|
| 7844| TURNER|    30|
| 7876|  ADAMS|    20|
```

&nbsp;&nbsp;Spowoduje to wyświetlenie tylko 5 pierwszych wierszy &nbsp;&nbsp;

```console
|deptId|  deptName|location|
|------|----------|--------|
|    10|Accounting|New York|
|    40|Operations|  Boston|
|    20|  Research|  Dallas|
|    30|     Sales| Chicago|
```

## <a name="index-types"></a>Typy indeksów

Obecnie hiperprzestrzeni ma reguły umożliwiające wykorzystanie indeksów dla dwóch grup zapytań:

* Zapytania wyboru z predykatami filtrowania wyboru zakresu
* Dołącz zapytania z predykatem sprzężenia równości (czyli equijoins).

## <a name="indexes-for-accelerating-filters"></a>Indeksy na potrzeby przyspieszania filtrów

Pierwsze przykładowe zapytanie wykonuje wyszukiwanie rekordów działu, jak pokazano w poniższej komórce. W programie SQL to zapytanie wygląda podobnie do poniższego przykładu:

```sql
SELECT deptName
FROM departments
WHERE deptId = 20
```

W danych wyjściowych w poniższej komórce przedstawiono następujące elementy:

* Wynik zapytania, który jest nazwą pojedynczego działu.
* Plan zapytania, który jest używany do uruchamiania zapytania przez platformę Spark.

W planie zapytania operator **FileScan** w dolnej części planu wskazuje źródło danych, z którego zostały odczytane rekordy. Lokalizacja tego pliku wskazuje ścieżkę do najnowszej wersji indeksu "deptIndex1". Te informacje pokazują, że zgodnie z kwerendą i przy użyciu reguł optymalizacji hiperprzestrzeni, platforma Spark zdecydowała się wykorzystać właściwy indeks w czasie wykonywania.

:::zone pivot = "programming-language-scala"

```scala
// Filter with equality predicate

val eqFilter: DataFrame = deptDFrame.filter("deptId = 20").select("deptName")
eqFilter.show()

eqFilter.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Filter with equality predicate

eqFilter = dept_DF.filter("""deptId = 20""").select("""deptName""")
eqFilter.show()

eqFilter.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

DataFrame eqFilter = deptDFrame.Filter("deptId = 20").Select("deptName");
eqFilter.Show();

eqFilter.Explain(true);

```

::: zone-end

Wyniki w:

```console
eqFilter: org.apache.spark.sql.DataFrame = [deptName: string]
```

```console
|DeptName|
|--------|
|Research|
```

&nbsp; &nbsp;

```console
== Parsed Logical Plan ==
'Project [unresolvedalias('deptName, None)]
+- Filter (deptId#533 = 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Analyzed Logical Plan ==
deptName: string
Project [deptName#534]
+- Filter (deptId#533 = 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [deptName#534]
+- Filter (isnotnull(deptId#533) && (deptId#533 = 20))
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Physical Plan ==
*(1) Project [deptName#534]
+- *(1) Filter (isnotnull(deptId#533) && (deptId#533 = 20))
   +- *(1) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId), EqualTo(deptId,20)], ReadSchema: struct<deptId:int,deptName:string>
```

Drugim przykładem jest zapytanie wyboru zakresu dla rekordów działu. W programie SQL to zapytanie wygląda podobnie do poniższego przykładu:

```sql
SELECT deptName
FROM departments
WHERE deptId > 20
```

Podobnie jak w przypadku pierwszego przykładu, dane wyjściowe następującej komórki przedstawiają wyniki zapytania (nazwy dwóch działów) i plan zapytania. Lokalizacja pliku danych w operatorze **FileScan** pokazuje, że do uruchomienia zapytania użyto elementu "deptIndex1".

:::zone pivot = "programming-language-scala"

```scala
// Filter with range selection predicate

val rangeFilter: DataFrame = deptDFrame.filter("deptId > 20").select("deptName")
rangeFilter.show()

rangeFilter.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Filter with range selection predicate

rangeFilter = dept_DF.filter("""deptId > 20""").select("deptName")
rangeFilter.show()

rangeFilter.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Filter with range selection predicate
DataFrame rangeFilter = deptDFrame.Filter("deptId > 20").Select("deptName");
rangeFilter.Show();

rangeFilter.Explain(true);

```

::: zone-end

Wyniki w:

```console
rangeFilter: org.apache.spark.sql.DataFrame = [deptName: string]
```

```console
|  DeptName|
|----------|
|Operations|
|     Sales|
```

```console
== Parsed Logical Plan ==
'Project [unresolvedalias('deptName, None)]
+- Filter (deptId#533 > 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Analyzed Logical Plan ==
deptName: string
Project [deptName#534]
+- Filter (deptId#533 > 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [deptName#534]
+- Filter (isnotnull(deptId#533) && (deptId#533 > 20))
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Physical Plan ==
*(1) Project [deptName#534]
+- *(1) Filter (isnotnull(deptId#533) && (deptId#533 > 20))
   +- *(1) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId), GreaterThan(deptId,20)], ReadSchema: struct<deptId:int,deptName:string>
```
Trzeci przykład to zapytanie łączące dział i rekordy pracowników na identyfikator działu. Równoważna instrukcja SQL jest pokazana w następujący sposób:

```sql
SELECT employees.deptId, empName, departments.deptId, deptName
FROM   employees, departments
WHERE  employees.deptId = departments.deptId
```
Wyjście z następującej komórki pokazuje wyniki zapytania, czyli nazwy 14 pracowników i nazwę działu, w którym pracuje każdy pracownik. Plan zapytania jest również uwzględniony w danych wyjściowych. Zwróć uwagę, jak lokalizacje plików dwóch operatorów **FileScan** pokazują, że platforma Spark użyła indeksów "empIndex" i "deptIndex1", aby uruchomić zapytanie.

:::zone pivot = "programming-language-scala"

```scala
// Join

val eqJoin: DataFrame =
      empDFrame.
      join(deptDFrame, empDFrame("deptId") === deptDFrame("deptId")).
      select(empDFrame("empName"), deptDFrame("deptName"))

eqJoin.show()

eqJoin.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Join

eqJoin = emp_DF.join(dept_DF, emp_DF.deptId == dept_DF.deptId).select(emp_DF.empName, dept_DF.deptName)

eqJoin.show()

eqJoin.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Join
DataFrame eqJoin =
      empDFrame
      .Join(deptDFrame, empDFrame.Col("deptId") == deptDFrame.Col("deptId"))
      .Select(empDFrame.Col("empName"), deptDFrame.Col("deptName"));

eqJoin.Show();

eqJoin.Explain(true);

```

::: zone-end

Wyniki w:

```console
eqJoin: org.apache.spark.sql.DataFrame = [empName: string, deptName: string]
```

```console
|empName|  deptName|
|-------|----------|
|  SMITH|  Research|
|  JONES|  Research|
|   FORD|  Research|
|  ADAMS|  Research|
|  SCOTT|  Research|
|   KING|Accounting|
|  CLARK|Accounting|
| MILLER|Accounting|
|  JAMES|     Sales|
|  BLAKE|     Sales|
| MARTIN|     Sales|
|  ALLEN|     Sales|
|   WARD|     Sales|
| TURNER|     Sales|
```

```console
== Parsed Logical Plan ==
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Relation[empId#527,empName#528,deptId#529] parquet
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Analyzed Logical Plan ==
empName: string, deptName: string
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Relation[empId#527,empName#528,deptId#529] parquet
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Project [empName#528, deptId#529]
   :  +- Filter isnotnull(deptId#529)
   :     +- Relation[empName#528,deptId#529] parquet
   +- Project [deptId#533, deptName#534]
      +- Filter isnotnull(deptId#533)
         +- Relation[deptId#533,deptName#534] parquet

== Physical Plan ==
*(3) Project [empName#528, deptName#534]
+- *(3) SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(1) Project [empName#528, deptId#529]
   :  +- *(1) Filter isnotnull(deptId#529)
   :     +- *(1) FileScan parquet [deptId#529,empName#528] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,empName:string>, SelectedBucketsCount: 200 out of 200
   +- *(2) Project [deptId#533, deptName#534]
      +- *(2) Filter isnotnull(deptId#533)
         +- *(2) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,deptName:string>, SelectedBucketsCount: 200 out of 200
```

## <a name="support-for-sql-semantics"></a>Obsługa semantyki SQL

Użycie indeksu jest niewidoczne, jeśli używasz interfejsu API Dataframe lub Spark SQL. Poniższy przykład pokazuje ten sam przykład sprzężenia tak jak wcześniej, w formularzu SQL, pokazując użycie indeksów, jeśli ma to zastosowanie.

:::zone pivot = "programming-language-scala"

```scala
empDFrame.createOrReplaceTempView("EMP")
deptDFrame.createOrReplaceTempView("DEPT")

val joinQuery = spark.sql("SELECT EMP.empName, DEPT.deptName FROM EMP, DEPT WHERE EMP.deptId = DEPT.deptId")

joinQuery.show()
joinQuery.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

from pyspark.sql import SparkSession

emp_DF.createOrReplaceTempView("EMP")
dept_DF.createOrReplaceTempView("DEPT")

joinQuery = spark.sql("SELECT EMP.empName, DEPT.deptName FROM EMP, DEPT WHERE EMP.deptId = DEPT.deptId")

joinQuery.show()
joinQuery.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

empDFrame.CreateOrReplaceTempView("EMP");
deptDFrame.CreateOrReplaceTempView("DEPT");

var joinQuery = spark.Sql("SELECT EMP.empName, DEPT.deptName FROM EMP, DEPT WHERE EMP.deptId = DEPT.deptId");

joinQuery.Show();
joinQuery.Explain(true);

```

::: zone-end

Wyniki w:

```console

joinQuery: org.apache.spark.sql.DataFrame = [empName: string, deptName: string]
```

```console
|empName|  deptName|
|-------|----------|
|  SMITH|  Research|
|  JONES|  Research|
|   FORD|  Research|
|  ADAMS|  Research|
|  SCOTT|  Research|
|   KING|Accounting|
|  CLARK|Accounting|
| MILLER|Accounting|
|  JAMES|     Sales|
|  BLAKE|     Sales|
| MARTIN|     Sales|
|  ALLEN|     Sales|
|   WARD|     Sales|
| TURNER|     Sales|
```

```console
== Parsed Logical Plan ==
'Project ['EMP.empName, 'DEPT.deptName]
+- 'Filter ('EMP.deptId = 'DEPT.deptId)
   +- 'Join Inner
      :- 'UnresolvedRelation `EMP`
      +- 'UnresolvedRelation `DEPT`

== Analyzed Logical Plan ==
empName: string, deptName: string
Project [empName#528, deptName#534]
+- Filter (deptId#529 = deptId#533)
   +- Join Inner
      :- SubqueryAlias `emp`
      :  +- Relation[empId#527,empName#528,deptId#529] parquet
      +- SubqueryAlias `dept`
         +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Project [empName#528, deptId#529]
   :  +- Filter isnotnull(deptId#529)
   :     +- Relation[empId#527,empName#528,deptId#529] parquet
   +- Project [deptId#533, deptName#534]
      +- Filter isnotnull(deptId#533)
         +- Relation[deptId#533,deptName#534,location#535] parquet

== Physical Plan ==
*(5) Project [empName#528, deptName#534]
+- *(5) SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(2) Sort [deptId#529 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(deptId#529, 200)
   :     +- *(1) Project [empName#528, deptId#529]
   :        +- *(1) Filter isnotnull(deptId#529)
   :           +- *(1) FileScan parquet [deptId#529,empName#528] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,empName:string>
   +- *(4) Sort [deptId#533 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(deptId#533, 200)
         +- *(3) Project [deptId#533, deptName#534]
            +- *(3) Filter isnotnull(deptId#533)
               +- *(3) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/your-path/departments.parquet], PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,deptName:string>
```

## <a name="explain-api"></a>Wyjaśnij interfejs API

Indeksy są doskonałe, ale jak wiesz, czy są one używane? Hiperprzestrzeni umożliwia użytkownikom porównanie ich oryginalnego planu z zaktualizowanym planem zależnym od indeksu przed uruchomieniem zapytania. Istnieje możliwość wyboru z trybu HTML, zwykłego tekstu lub konsoli, aby wyświetlić dane wyjściowe polecenia.

W poniższej komórce przedstawiono przykład o kodzie HTML. Wyróżniona sekcja reprezentuje różnicę między oryginalnymi i zaktualizowanymi planami wraz z używanymi indeksami.

:::zone pivot = "programming-language-scala"

```scala
spark.conf.set("spark.hyperspace.explain.displayMode", "html")
hyperspace.explain(eqJoin) { displayHTML }
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

eqJoin = emp_DF.join(dept_DF, emp_DF.deptId == dept_DF.deptId).select(emp_DF.empName, dept_DF.deptName)

spark.conf.set("spark.hyperspace.explain.displayMode", "html")
hyperspace.explain(eqJoin, True, displayHTML)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

spark.Conf().Set("spark.hyperspace.explain.displayMode", "html");
spark.Conf().Set("spark.hyperspace.explain.displayMode.highlight.beginTag", "<b style=\"background:LightGreen\">");
spark.Conf().Set("spark.hyperspace.explain.displayMode.highlight.endTag", "</b>");

hyperspace.Explain(eqJoin, false, input => DisplayHTML(input));

```

::: zone-end

Wyniki w:

### <a name="plan-with-indexes"></a>Planowanie przy użyciu indeksów

```console
Project [empName#528, deptName#534]
+- SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(1) Project [empName#528, deptId#529]
   :  +- *(1) Filter isnotnull(deptId#529)
   :     +- *(1) FileScan parquet [deptId#529,empName#528] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
   +- *(2) Project [deptId#533, deptName#534]
      +- *(2) Filter isnotnull(deptId#533)
         +- *(2) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
```

### <a name="plan-without-indexes"></a>Planowanie bez indeksów

```console
Project [empName#528, deptName#534]
+- SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(2) Sort [deptId#529 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(deptId#529, 200)
   :     +- *(1) Project [empName#528, deptId#529]
   :        +- *(1) Filter isnotnull(deptId#529)
   :           +- *(1) FileScan parquet [empName#528,deptId#529] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/your-path/employees.parquet], PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
   +- *(4) Sort [deptId#533 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(deptId#533, 200)
         +- *(3) Project [deptId#533, deptName#534]
            +- *(3) Filter isnotnull(deptId#533)
               +- *(3) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/your-path/departments.parquet], PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
```

### <a name="indexes-used"></a>Używane indeksy

```console
deptIndex1:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/<container>/indexes/public/deptIndex1/v__=0
empIndex:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/<container>/indexes/public/empIndex/v__=0
```

## <a name="refresh-indexes"></a>Odśwież indeksy

Jeśli oryginalne dane, na których został utworzony indeks, nie będą już przechwytywać najnowszego stanu danych. Można odświeżyć stary indeks przy użyciu polecenia **refreshIndex** . To polecenie powoduje, że indeks zostanie całkowicie odbudowany i zaktualizowany zgodnie z najnowszymi rekordami danych. Pokażemy, jak stopniowo odświeżyć indeks w innych notesach.

W poniższych dwóch komórkach przedstawiono przykład dla tego scenariusza:

* Pierwsza komórka dodaje do danych pierwotnych działów dwa więcej działów. Odczytuje i drukuje listę działów, aby sprawdzić, czy nowe działy są prawidłowo dodawane. Dane wyjściowe przedstawiają sześć wydziałów łącznie: cztery stare i dwa nowe. Wywoływanie aktualizacji **refreshIndex** "deptIndex1", aby indeks przechwytł nowe działy.
* W drugiej komórce jest uruchamiany nasz przykład zapytania wyboru zakresu. Wyniki powinny teraz zawierać cztery działy: dwa są tymi, które były widoczne przed uruchomieniem powyższego zapytania, a dwa to nowe dodane działy.

### <a name="specific-index-refresh"></a>Odświeżenie indeksu

:::zone pivot = "programming-language-scala"

```scala
val extraDepartments = Seq(
      (50, "Inovation", "Seattle"),
      (60, "Human Resources", "San Francisco"))

val extraDeptData: DataFrame = extraDepartments.toDF("deptId", "deptName", "location")
extraDeptData.write.mode("Append").parquet(deptLocation)

val deptDFrameUpdated: DataFrame = spark.read.parquet(deptLocation)

deptDFrameUpdated.show(10)

hyperspace.refreshIndex("deptIndex1")
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

extra_Departments = [(50, "Inovation", "Seattle"), (60, "Human Resources", "San Francisco")]

extra_departments_df = spark.createDataFrame(extra_Departments, dept_schema)
extra_departments_df.write.mode("Append").parquet(dept_Location)


dept_DFrame_Updated = spark.read.parquet(dept_Location)

dept_DFrame_Updated.show(10)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

var extraDepartments = new List<GenericRow>()
{
    new GenericRow(new object[] {50, "Inovation", "Seattle"}),
    new GenericRow(new object[] {60, "Human Resources", "San Francisco"})
};
    
DataFrame extraDeptData = spark.CreateDataFrame(extraDepartments, departmentSchema);
extraDeptData.Write().Mode("Append").Parquet(deptLocation);

DataFrame deptDFrameUpdated = spark.Read().Parquet(deptLocation);

deptDFrameUpdated.Show(10);

hyperspace.RefreshIndex("deptIndex1");

```

::: zone-end

Wyniki w:

```console
extraDepartments: Seq[(Int, String, String)] = List((50,Inovation,Seattle), (60,Human Resources,San Francisco))  
extraDeptData: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]  
deptDFrameUpdated: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]
```

&nbsp; &nbsp;

```console  
|deptId|       deptName|     location|
|------|---------------|-------------|
|    60|Human Resources|San Francisco|
|    10|     Accounting|     New York|
|    50|      Inovation|      Seattle|
|    40|     Operations|       Boston|
|    20|       Research|       Dallas|
|    30|          Sales|      Chicago|
```

### <a name="range-selection"></a>Wybór zakresu

:::zone pivot = "programming-language-scala"

```scala
val newRangeFilter: DataFrame = deptDFrameUpdated.filter("deptId > 20").select("deptName")
newRangeFilter.show()

newRangeFilter.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

newRangeFilter = dept_DFrame_Updated.filter("deptId > 20").select("deptName")
newRangeFilter.show()

newRangeFilter.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

DataFrame newRangeFilter = deptDFrameUpdated.Filter("deptId > 20").Select("deptName");
newRangeFilter.Show();

newRangeFilter.Explain(true);

```

::: zone-end

Wyniki w:

```console
newRangeFilter: org.apache.spark.sql.DataFrame = [deptName: string]
```

```console
|       DeptName|
|---------------|
|Human Resources|
|      Inovation|
|     Operations|
|          Sales|
```

```console
== Parsed Logical Plan ==
'Project [unresolvedalias('deptName, None)]
+- Filter (deptId#674 > 20)
   +- Relation[deptId#674,deptName#675,location#676] parquet

== Analyzed Logical Plan ==
deptName: string
Project [deptName#675]
+- Filter (deptId#674 > 20)
   +- Relation[deptId#674,deptName#675,location#676] parquet

== Optimized Logical Plan ==
Project [deptName#675]
+- Filter (isnotnull(deptId#674) && (deptId#674 > 20))
   +- Relation[deptId#674,deptName#675,location#676] parquet

== Physical Plan ==
*(1) Project [deptName#675]
+- *(1) Filter (isnotnull(deptId#674) && (deptId#674 > 20))
   +- *(1) FileScan parquet [deptId#674,deptName#675] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId), GreaterThan(deptId,20)], ReadSchema: struct<deptId:int,deptName:string>
```

## <a name="next-steps"></a>Następne kroki

* [Hiperprzestrzeni w projekcie](https://microsoft.github.io/hyperspace/)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
