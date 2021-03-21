---
title: 'Samouczek: Azure Data Lake Storage Gen2, Azure Databricks & Spark | Microsoft Docs'
description: W tym samouczku pokazano, jak uruchamiać zapytania Spark w klastrze Azure Databricks, aby uzyskać dostęp do danych na koncie magazynu Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.custom: devx-track-python
ms.openlocfilehash: 232e28d3cc8b0bc7427dd035d51743f623e54259
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564307"
---
# <a name="tutorial-azure-data-lake-storage-gen2-azure-databricks--spark"></a>Samouczek: Azure Data Lake Storage Gen2, Azure Databricks & Spark

W tym samouczku pokazano, jak połączyć klaster usługi Azure Databricks z danymi przechowywanymi na koncie magazynu platformy Azure z włączoną usługą Azure Data Lake Storage Gen2. Takie połączenie umożliwia natywne wykonywanie w klastrze zapytań i analiz dotyczących tych danych.

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie klastra usługi Databricks
> * Pozyskiwanie danych bez struktury na koncie magazynu
> * Uruchamianie analiz dotyczących danych w magazynie obiektów blob

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz konto usługi Azure Data Lake Storage Gen2.

  Zobacz [Tworzenie konta magazynu, które ma być używane z Azure Data Lake Storage Gen2](create-data-lake-storage-account.md).

* Upewnij się, że Twoje konto użytkownika ma przypisaną [rolę Współautor danych obiektu blob magazynu](../common/storage-auth-aad-rbac-portal.md).

* Zainstaluj narzędzie AzCopy w wersji 10. Zobacz [Transferowanie danych za pomocą narzędzia AzCopy w wersji 10](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

* Tworzenie jednostki usługi. Zobacz [jak: korzystanie z portalu do tworzenia aplikacji usługi Azure AD i nazwy głównej usługi, która może uzyskiwać dostęp do zasobów](../../active-directory/develop/howto-create-service-principal-portal.md).

  Jest kilka rzeczy, o których należy pamiętać podczas wykonywania kroków przedstawionych w tym artykule.

  : heavy_check_mark: podczas wykonywania kroków opisanych w sekcji [przypisywanie aplikacji do roli](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) w artykule, należy się upewnić, że rola **współautor danych obiektów blob magazynu** jest przypisana do jednostki usługi.

  > [!IMPORTANT]
  > Upewnij się, że przypisano rolę w zakresie konta magazynu usługi Data Lake Storage Gen2. Możesz przypisać rolę do nadrzędnej grupy zasobów lub subskrypcji, ale będzie zgłaszany błąd dotyczący uprawnień do momentu rozpropagowania przypisań roli do konta magazynu.

  : heavy_check_mark: podczas wykonywania kroków z sekcji [pobieranie wartości dla logowania w](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) artykule wklej identyfikator dzierżawy, identyfikator aplikacji i wartości klucza tajnego klienta do pliku tekstowego. Wkrótce będą potrzebne.

### <a name="download-the-flight-data"></a>Pobieranie danych lotów

W tym samouczku w celu zademonstrowania sposobu wykonywania operacji ETL są używane dane dotyczące lotów opracowane przez agencję Bureau of Transportation Statistics. Aby ukończyć samouczek, musisz pobrać te dane.

1. Przejdź do strony [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?gnoyr_VQ=FGJ).

2. Zaznacz pole wyboru **Prezipped file** (Wstępnie spakowany plik), aby zaznaczyć wszystkie pola danych.

3. Wybierz przycisk **Download** (Pobierz) i zapisz wyniki na swoim komputerze. 

4. Rozpakuj zawartość pliku zip i zanotuj nazwę pliku oraz jego ścieżkę. Te informacje będą potrzebne w późniejszym kroku.

## <a name="create-an-azure-databricks-service"></a>Tworzenie usługi Azure Databricks

W tej sekcji utworzysz usługę Azure Databricks przy użyciu witryny Azure Portal.

1. W Azure Portal wybierz pozycję **Utwórz**  >    >  **Azure Databricks** analizy zasobów.

    ![Datakostki na Azure Portal](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Datakostki na Azure Portal")

2. W obszarze **Usługa Azure Databricks** podaj następujące wartości, aby utworzyć usługę Databricks:

    |Właściwość  |Opis  |
    |---------|---------|
    |**Nazwa obszaru roboczego**     | Podaj nazwę obszaru roboczego usługi Databricks.  |
    |**Subskrypcja**     | Z listy rozwijanej wybierz subskrypcję platformy Azure.        |
    |**Grupa zasobów**     | Określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej grupy. Grupa zasobów to kontener zawierający powiązane zasoby dla rozwiązania platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/management/overview.md). |
    |**Lokalizacja**     | Wybierz pozycję **Zachodnie stany USA 2**. Inne dostępne regiony podano na stronie [dostępności usług platformy Azure według regionów](https://azure.microsoft.com/regions/services/).       |
    |**Warstwa cenowa**     |  Wybierz pozycję **Standardowy**.     |

    ![Tworzenie obszaru roboczego usługi Azure Databricks](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "Tworzenie usługi Azure Databricks")

3. Tworzenie konta potrwa kilka minut. Stan operacji można monitorować za pomocą paska postępu znajdującego się u góry.

4. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie pozycję **Utwórz**.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Tworzenie klastra Spark w usłudze Azure Databricks

1. W witrynie Azure Portal przejdź do utworzonej usługi Databricks i wybierz pozycję **Uruchom obszar roboczy**.

2. Nastąpi przekierowanie do portalu usługi Azure Databricks. W portalu wybierz pozycję **Klaster**.

    ![Datakostki na platformie Azure](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Datakostki na platformie Azure")

3. Na stronie **Nowy klaster** podaj wartości, aby utworzyć klaster.

    ![Tworzenie klastra usługi datakosteks Spark na platformie Azure](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Tworzenie klastra usługi datakosteks Spark na platformie Azure")

    Uzupełnij wartości następujących pól i zaakceptuj wartości domyślne w pozostałych polach:

    - Wprowadź nazwę klastra.
     
    - Upewnij się, że pole wyboru **Zakończ po 120 min aktywności** zostało zaznaczone. Podaj czas (w minutach), po jakim działanie klastra ma zostać zakończone, jeśli nie jest używany.

4. Wybierz pozycję **Utwórz klaster**. Po uruchomieniu klastra możesz dołączać do niego notesy i uruchamiać zadania Spark.

## <a name="ingest-data"></a>Pozyskiwanie danych

### <a name="copy-source-data-into-the-storage-account"></a>Kopiowanie danych źródłowych na konto magazynu

Korzystanie z narzędzia AzCopy do kopiowania danych z pliku *csv* na konto usługi Data Lake Storage Gen2.

1. Otwórz okno wiersza polecenia, a następnie wprowadź następujące polecenie, aby zalogować się na swoje konto magazynu.

   ```bash
   azcopy login
   ```

   Postępuj zgodnie z instrukcjami, które pojawiają się w oknie wiersza polecenia w celu uwierzytelnienia konta użytkownika.

2. Aby skopiować dane z pliku *csv*, wprowadź następujące polecenie.

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<container-name>/folder1/On_Time.csv
   ```

   * Zastąp `<csv-folder-path>` wartość symbolu zastępczego ścieżką do pliku *CSV* .

   * Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.

   * Zastąp `<container-name>` symbol zastępczy nazwą kontenera na koncie magazynu.

## <a name="create-a-container-and-mount-it"></a>Utwórz kontener i zainstaluj go

W tej sekcji utworzysz kontener i folder na koncie magazynu.

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do utworzonej usługi Azure Databricks i wybierz pozycję **Uruchom obszar roboczy**.

2. Po lewej stronie wybierz pozycję **Obszar roboczy**. Z listy rozwijanej **Obszar roboczy** wybierz pozycję **Utwórz** > **Notes**.

    ![Tworzenie notesu w kostkach](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "Tworzenie notesu w kostkach")

3. W oknie dialogowym **Tworzenie notesu** wprowadź nazwę notesu. Jako język wybierz pozycję **Python**, a następnie wybierz utworzony wcześniej klaster Spark.

4. Wybierz przycisk **Utwórz**.

5. Skopiuj i wklej następujący blok kodu do pierwszej komórki, ale jeszcze nie uruchamiaj kodu.

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<appId>",
           "fs.azure.account.oauth2.client.secret": "<clientSecret>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```

18. W tym bloku kodu zamień symbole zastępcze `appId`, `clientSecret`, `tenant` i `storage-account-name` na wartości zebrane podczas wykonywania kroków wymagań wstępnych. Zastąp `container-name` wartość symbolu zastępczego nazwą kontenera.

19. Naciśnij klawisze **SHIFT+ENTER**, aby uruchomić kod w tym bloku.

   Nie zamykaj tego notesu, ponieważ później będziesz jeszcze dodawać do niego polecenia.

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Konwertowanie formatu CSV na format Parquet za pomocą notesu usługi Databricks

We wcześniej utworzonym notesie dodaj nową komórkę i wklej do niej następujący kod. 

```python
# Use the previously established DBFS mount point to read the data.
# create a data frame to read data.

flightDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/*.csv")

# read the airline csv file and write the output to parquet format for easy query.
flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
print("Done")
```

## <a name="explore-data"></a>Eksplorowanie danych

W nowej komórce wklej następujący kod, aby uzyskać listę plików CSV przekazanych za pomocą narzędzia AzCopy.

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata"))
```

Aby utworzyć nowy plik i uzyskać listę plików w folderze *parquet/flights*, uruchom następujący skrypt:

```python
dbutils.fs.put("/mnt/flightdata/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/parquet/flights")
```

Przy użyciu tego przykładowego kodu możesz eksplorować hierarchię systemu plików HDFS na przykładzie danych przechowywanych na koncie magazynu z włączoną usługą Azure Data Lake Storage Gen2.

## <a name="query-the-data"></a>Wykonywanie zapytań na danych

Następnie możesz rozpocząć wykonywanie zapytań dotyczących danych przekazanych na swoje konto magazynu. Wprowadź każdy z poniższych bloków kodu w komórce **Cmd 1** i naciśnij klawisze **Cmd + Enter**, aby uruchomić skrypt języka Python.

Aby utworzyć ramki danych dla źródeł danych, uruchom następujący skrypt:

* Zastąp `<csv-folder-path>` wartość symbolu zastępczego ścieżką do pliku *CSV* .

```python
# Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/On_Time.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

# read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(
    header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

# print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

# print the flight database size
print("Number of flights in the database: ", flightDF.count())

# show the first 20 rows (20 is the default)
# to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

# Display to run visualizations
# preferably run this in a separate cmd cell
display(flightDF)
```

Wprowadź ten skrypt, aby uruchomić pewne podstawowe zapytania analizy na danych.

```python
# Run each of these queries, preferably in a separate cmd cell for separate analysis
# create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

# create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

# using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights = out2.count()
print("Jan 2016: ", NumJan2016Flights, " Feb 2016: ", NumFeb2016Flights)
Total = NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql(
    "SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'")
print('Airports in Texas: ', out.show(100))

# find all airlines that fly from Texas
out1 = spark.sql(
    "SELECT distinct(Reporting_Airline) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. W tym celu zaznacz grupę zasobów konta magazynu i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu oprogramowania Apache Hive w usłudze Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)
