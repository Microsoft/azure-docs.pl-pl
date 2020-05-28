---
title: Samouczek — wykonywanie operacji ETL przy użyciu Azure Databricks
description: W tym samouczku dowiesz się, jak wyodrębnić dane z Data Lake Storage Gen2 do Azure Databricks, Przekształć dane i załadować je do usługi Azure Synapse Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: d97fae777b06625488234eb6d48a74a60a83f4d6
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84018722"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu Azure Databricks

W ramach tego samouczka wykonasz operację ETL (wyodrębnianie, przekształcanie i ładowanie danych) przy użyciu usługi Azure Databricks. Dane z Azure Data Lake Storage Gen2 można wyodrębnić do Azure Databricks, uruchamiać przekształcenia danych w Azure Databricks i ładować przekształcone dane do usługi Azure Synapse Analytics.

Kroki opisane w tym samouczku umożliwiają Azure Databricks transferu danych do Azure Databricks za pomocą łącznika usługi Azure Synapse. Ten łącznik z kolei używa platformy Azure Blob Storage jako magazynu tymczasowego do przesyłania danych między klastrem Azure Databricks i usługą Azure Synapse.

Poniższa ilustracja przedstawia przepływ aplikacji:

![Azure Databricks z usługami Data Lake Store i Azure Synapse](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Azure Databricks z usługami Data Lake Store i Azure Synapse")

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Tworzenie usługi Azure Databricks.
> * Tworzenie klastra Spark w usłudze Azure Databricks.
> * Tworzenie systemu plików na koncie usługi Data Lake Storage Gen2.
> * Przekazywanie danych przykładowych na konto usługi Azure Data Lake Storage Gen2.
> * Tworzenie jednostki usługi.
> * Wyodrębnianie danych z konta usługi Azure Data Lake Storage Gen2.
> * Przekształcanie danych w usłudze Azure Databricks.
> * Ładowanie danych do usługi Azure Synapse.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!Note]
> Tego samouczka nie można przeprowadzić za pomocą **subskrypcji bezpłatnej wersji próbnej platformy Azure**.
> Jeśli masz bezpłatne konto, przejdź do swojego profilu i Zmień subskrypcję na **płatność zgodnie z rzeczywistym**użyciem. Aby uzyskać więcej informacji, zobacz [Bezpłatne konto platformy Azure](https://azure.microsoft.com/free/). Następnie [Usuń limit wydatków](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)i [Poproś o zwiększenie limitu przydziału](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) dla procesorów wirtualnych vCPU w Twoim regionie. Podczas tworzenia obszaru roboczego Azure Databricks możesz wybrać warstwę cenową **wersji próbnej (Premium-14-Days Free dBu)** , aby umożliwić dostęp do obszaru roboczego bezpłatnie Azure Databricks DBU przez 14 dni.
     
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka wykonaj następujące zadania:

* Utwórz usługę Azure Synapse, Utwórz regułę zapory na poziomie serwera i nawiąż połączenie z serwerem jako administrator serwera. Zobacz [Szybki Start: Tworzenie puli SQL Synapse i wykonywanie na niej zapytań przy użyciu Azure Portal](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md).

* Utwórz klucz główny dla usługi Azure Synapse. Zobacz [Tworzenie klucza głównego bazy danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).

* Utwórz konto usługi Azure Blob Storage i zawarty w nim kontener. Ponadto pobierz klucz dostępu, aby uzyskać dostęp do konta magazynu. Zobacz [Szybki Start: przekazywanie, pobieranie i wyświetlanie listy obiektów BLOB za pomocą Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

* Utwórz konto usługi Azure Data Lake Storage Gen2. Zobacz [Szybki Start: Tworzenie konta magazynu Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

* Tworzenie jednostki usługi. Zobacz [jak: korzystanie z portalu do tworzenia aplikacji usługi Azure AD i nazwy głównej usługi, która może uzyskiwać dostęp do zasobów](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

   Jest kilka rzeczy, o których należy pamiętać podczas wykonywania kroków przedstawionych w tym artykule.

   * Wykonując kroki opisane w sekcji [przypisywanie aplikacji do roli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) w artykule, należy się upewnić, że rola **współautor danych obiektów blob magazynu** jest przypisana do jednostki usługi w zakresie konta Data Lake Storage Gen2. Jeśli przypiszesz rolę do nadrzędnej grupy zasobów lub subskrypcji, będziesz otrzymywać błędy związane z uprawnieniami, dopóki te przydziały roli nie zostaną rozpropagowane do konta magazynu.

      Jeśli wolisz używać listy kontroli dostępu (ACL) do kojarzenia jednostki usługi z określonym plikiem lub katalogiem, kontrola dostępu referencyjnego [w Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

   * Podczas wykonywania kroków opisanych w sekcji [pobieranie wartości dla logowania w](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) artykule wklej identyfikator dzierżawy, identyfikator aplikacji i wartości klucza tajnego do pliku tekstowego.

* Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="gather-the-information-that-you-need"></a>Zbieranie potrzebnych informacji

Upewnij się, że zostały spełnione wszystkie wymagania wstępne tego samouczka.

   Przed rozpoczęciem należy zebrać następujące informacje:

   : heavy_check_mark: Nazwa bazy danych, nazwa serwera bazy danych, nazwa użytkownika i hasło do usługi Azure Synapse.

   : heavy_check_mark: klucz dostępu konta magazynu obiektów BLOB.

   : heavy_check_mark: nazwa konta magazynu Data Lake Storage Gen2.

   : heavy_check_mark: Identyfikator dzierżawy subskrypcji.

   : heavy_check_mark: Identyfikator aplikacji dla aplikacji, która została zarejestrowana w usłudze Azure Active Directory (Azure AD).

   : heavy_check_mark: klucz uwierzytelniania dla aplikacji, która została zarejestrowana w usłudze Azure AD.

## <a name="create-an-azure-databricks-service"></a>Tworzenie usługi Azure Databricks

W tej sekcji utworzysz usługę Azure Databricks przy użyciu witryny Azure Portal.

1. Z menu Azure Portal wybierz pozycję **Utwórz zasób**.

    ![Utwórz zasób na Azure Portal](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png)

    Następnie wybierz pozycję **Analytics**  >  **Azure Databricks**.

    ![Utwórz Azure Databricks na Azure Portal](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-resource-create.png)



2. W obszarze **Usługa Azure Databricks** podaj następujące wartości, aby utworzyć usługę Databricks:

    |Właściwość  |Opis  |
    |---------|---------|
    |**Nazwa obszaru roboczego**     | Podaj nazwę obszaru roboczego usługi Databricks.        |
    |**Subskrypcja**     | Z listy rozwijanej wybierz subskrypcję platformy Azure.        |
    |**Grupa zasobów**     | Określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej grupy. Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/management/overview.md). |
    |**Lokalizacja**     | Wybierz pozycję **Zachodnie stany USA 2**.  Inne dostępne regiony podano na stronie [dostępności usług platformy Azure według regionów](https://azure.microsoft.com/regions/services/).      |
    |**Warstwa cenowa**     |  Wybierz pozycję **Standardowy**.     |

3. Tworzenie konta potrwa kilka minut. Stan operacji można monitorować za pomocą paska postępu znajdującego się u góry.

4. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie pozycję **Utwórz**.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Tworzenie klastra Spark w usłudze Azure Databricks

1. W witrynie Azure Portal przejdź do utworzonej usługi Databricks i wybierz pozycję **Uruchom obszar roboczy**.

2. Nastąpi przekierowanie do portalu usługi Azure Databricks. W portalu wybierz pozycję **Klaster**.

    ![Datakostki na platformie Azure](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Datakostki na platformie Azure")

3. Na stronie **Nowy klaster** podaj wartości, aby utworzyć klaster.

    ![Tworzenie klastra usługi datakosteks Spark na platformie Azure](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Tworzenie klastra usługi datakosteks Spark na platformie Azure")

4. Uzupełnij wartości następujących pól i zaakceptuj wartości domyślne w pozostałych polach:

    * Wprowadź nazwę klastra.

    * Upewnij się, że jest zaznaczone pole wyboru **Zakończ po \_ \_ minutach braku aktywności** . Podaj czas (w minutach), po jakim działanie klastra ma zostać zakończone, jeśli nie jest on używany.

    * Wybierz pozycję **Utwórz klaster**. Po uruchomieniu klastra możesz dołączać do niego notesy i uruchamiać zadania Spark.

## <a name="create-a-file-system-in-the-azure-data-lake-storage-gen2-account"></a>Tworzenie systemu plików na koncie usługi Azure Data Lake Storage Gen2

W tej sekcji utworzysz notes w obszarze roboczym usługi Azure Databricks, a następnie uruchomisz fragmenty kodu, aby skonfigurować konto magazynu.

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do utworzonej usługi Azure Databricks i wybierz pozycję **Uruchom obszar roboczy**.

2. Po lewej stronie wybierz pozycję **Obszar roboczy**. Z listy rozwijanej **Obszar roboczy** wybierz pozycję **Utwórz** > **Notes**.

    ![Tworzenie notesu w kostkach](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Tworzenie notesu w kostkach")

3. W oknie dialogowym**Tworzenie notesu** wprowadź nazwę notesu. Jako język wybierz pozycję **Scala**, a następnie wybierz utworzony wcześniej klaster Spark.

    ![Podaj szczegóły dla notesu w kostkach](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Podaj szczegóły dla notesu w kostkach")

4. Wybierz przycisk **Utwórz**.

5. Poniższy blok kodu ustawia domyślne poświadczenia nazwy głównej usługi dla każdego konta ADLS generacji 2, do którego można uzyskać dostęp w sesji platformy Spark. Drugi blok kodu dołącza nazwę konta do ustawienia, aby określić poświadczenia dla określonego konta ADLS generacji 2.  Skopiuj i wklej blok kodu w pierwszej komórce notesu Azure Databricks.

   **Konfiguracja sesji**

   ```scala
   val appID = "<appID>"
   val secret = "<secret>"
   val tenantID = "<tenant-id>"

   spark.conf.set("fs.azure.account.auth.type", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret", "<secret>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   ```

   **Konfiguracja konta**

   ```scala
   val storageAccountName = "<storage-account-name>"
   val appID = "<app-id>"
   val secret = "<secret>"
   val fileSystemName = "<file-system-name>"
   val tenantID = "<tenant-id>"

   spark.conf.set("fs.azure.account.auth.type." + storageAccountName + ".dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type." + storageAccountName + ".dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id." + storageAccountName + ".dfs.core.windows.net", "" + appID + "")
   spark.conf.set("fs.azure.account.oauth2.client.secret." + storageAccountName + ".dfs.core.windows.net", "" + secret + "")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint." + storageAccountName + ".dfs.core.windows.net", "https://login.microsoftonline.com/" + tenantID + "/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://" + fileSystemName  + "@" + storageAccountName + ".dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
   ```

6. W tym bloku kodu zamień symbole zastępcze `<app-id>`, `<secret>`, `<tenant-id>` i `<storage-account-name>` na wartości zebrane podczas wykonywania kroków wymagań wstępnych. Zastąp symbol zastępczy `<file-system-name>` dowolną nazwą, którą chcesz nadać systemowi plików.

   * Parametry `<app-id>` i `<secret>` pochodzą z aplikacji zarejestrowanej w usłudze Active Directory podczas tworzenia jednostki usługi.

   * Parametr `<tenant-id>` pochodzi z subskrypcji.

   * Parametr `<storage-account-name>` to nazwa konta magazynu usługi Azure Data Lake Storage Gen2.

7. Naciśnij klawisze **SHIFT+ENTER**, aby uruchomić kod w tym bloku.

## <a name="ingest-sample-data-into-the-azure-data-lake-storage-gen2-account"></a>Pozyskiwanie danych przykładowych na konto usługi Azure Data Lake Storage Gen2

Przed przystąpieniem do pracy z tą sekcją musisz zapewnić spełnienie następujących wymagań wstępnych:

Wprowadź następujący kod w komórce notesu:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

W tej komórce naciśnij klawisze **SHIFT + ENTER**, aby uruchomić kod.

Teraz w nowej komórce pod tą komórką wprowadź następujący kod, zastępując wartości w nawiasach tymi samymi wartościami, których użyto wcześniej:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://" + fileSystemName + "@" + storageAccountName + ".dfs.core.windows.net/")

W tej komórce naciśnij klawisze **SHIFT + ENTER**, aby uruchomić kod.

## <a name="extract-data-from-the-azure-data-lake-storage-gen2-account"></a>Wyodrębnianie danych z konta usługi Azure Data Lake Storage Gen2

1. Teraz możesz załadować przykładowy plik json jako ramkę danych w usłudze Azure Databricks. Wklej następujący kod w nowej komórce. Zastąp symbole zastępcze umieszczone w nawiasach ostrokątnych własnymi wartościami.

   ```scala
   val df = spark.read.json("abfss://" + fileSystemName + "@" + storageAccountName + ".dfs.core.windows.net/small_radio_json.json")
   ```
2. Naciśnij klawisze **SHIFT+ENTER**, aby uruchomić kod w tym bloku.

3. Uruchom poniższy kod, aby wyświetlić zawartość ramki danych:

    ```scala
    df.show()
    ```
   Zostaną wyświetlone dane wyjściowe podobne do następującego fragmentu kodu:

   ```output
   +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
   |               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
   +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
   | El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
   | Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
   | Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
   ...
   ...
   ```

   Masz teraz dane wyodrębnione z usługi Azure Data Lake Storage 2. generacji do usługi Azure Databricks.

## <a name="transform-data-in-azure-databricks"></a>Przekształcanie danych w usłudze Azure Databricks

Plik przykładowych danych pierwotnych, **small_radio_json.json**, przechwytuje odbiorców stacji radiowej i ma wiele kolumn. W tej sekcji przekształcisz dane tak, aby z zestawu danych były pobierane tylko określone kolumny.

1. Zacznij od pobrania tylko kolumn **firstName**, **lastName**, **gender**, **location** i **level** z utworzonej ramki danych.

   ```scala
   val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
   specificColumnsDf.show()
   ```

   Otrzymasz dane wyjściowe pokazane w poniższym fragmencie kodu:

   ```output
   +---------+----------+------+--------------------+-----+
   |firstname|  lastname|gender|            location|level|
   +---------+----------+------+--------------------+-----+
   | Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |     Tess|  Townsend|     F|Nashville-Davidso...| free|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
   |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Tess|  Townsend|     F|Nashville-Davidso...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   +---------+----------+------+--------------------+-----+
   ```

2. Możesz dalej przekształcać te dane, aby zmienić nazwę kolumny **level** na **subscription_type**.

   ```scala
   val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
   renamedColumnsDF.show()
   ```

   Otrzymasz dane wyjściowe pokazane w poniższym fragmencie kodu.

   ```output
   +---------+----------+------+--------------------+-----------------+
   |firstname|  lastname|gender|            location|subscription_type|
   +---------+----------+------+--------------------+-----------------+
   | Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
   |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   +---------+----------+------+--------------------+-----------------+
   ```

## <a name="load-data-into-azure-synapse"></a>Ładowanie danych do usługi Azure Synapse

W tej sekcji przekażesz przekształcone dane do usługi Azure Synapse. Łącznik usługi Azure Synapse służy do Azure Databricks bezpośredniego przekazywania ramki danych jako tabeli w puli Synapse Spark.

Jak wspomniano wcześniej, łącznik usługi Azure Synapse używa usługi Azure Blob Storage jako magazynu tymczasowego do przekazywania danych między Azure Databricks i Azure Synapse. Możesz rozpocząć od podania konfiguracji umożliwiającej nawiązanie połączenia z kontem magazynu. Musisz już mieć utworzone konto w ramach wymagań wstępnych dotyczących tego artykułu.

1. Podaj konfigurację umożliwiającą uzyskanie dostępu do konta usługi Azure Storage z usługi Azure Databricks.

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val blobAccessKey =  "<access-key>"
   ```

2. Określ folder tymczasowy, który ma być używany podczas przesuwania danych między Azure Databricks i Azure Synapse.

   ```scala
   val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"
   ```

3. Uruchom poniższy fragment kodu, aby zapisać klucze dostępu usługi Azure Blob Storage w konfiguracji. Dzięki tej akcji nie będzie trzeba przechowywać klucza dostępu w notesie w postaci zwykłego tekstu.

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, blobAccessKey)
   ```

4. Podaj wartości, aby połączyć się z wystąpieniem usługi Azure Synapse. Należy utworzyć usługę Azure Synapse Analytics jako wymaganie wstępne. Użyj w pełni kwalifikowanej nazwy serwera dla **dwServer**. Na przykład `<servername>.database.windows.net`.

   ```scala
   //Azure Synapse related settings
   val dwDatabase = "<database-name>"
   val dwServer = "<database-server-name>"
   val dwUser = "<user-name>"
   val dwPass = "<password>"
   val dwJdbcPort =  "1433"
   val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
   val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
   val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
   ```

5. Uruchom Poniższy fragment kodu, aby załadować przekształconą ramkę danych **renamedColumnsDF**, jako tabelę w usłudze Azure Synapse. Ten fragment kodu tworzy tabelę o nazwie **SampleTable** w bazie danych SQL.

   ```scala
   spark.conf.set(
       "spark.sql.parquet.writeLegacyFormat",
       "true")

   renamedColumnsDF.write.format("com.databricks.spark.sqldw").option("url", sqlDwUrlSmall).option("dbtable", "SampleTable")       .option( "forward_spark_azure_storage_credentials","True").option("tempdir", tempDir).mode("overwrite").save()
   ```

   > [!NOTE]
   > W tym przykładzie użyto `forward_spark_azure_storage_credentials` flagi, która powoduje, że usługa Azure Synapse uzyskuje dostęp do danych z usługi BLOB Storage przy użyciu klucza dostępu. Jest to jedyna obsługiwana metoda uwierzytelniania.
   >
   > Jeśli Blob Storage platformy Azure jest ograniczone do wybranych sieci wirtualnych, usługa Azure Synapse wymaga [tożsamość usługi zarządzanej zamiast kluczy dostępu](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Spowoduje to wystąpienie błędu "to żądanie nie ma autoryzacji do wykonania tej operacji".

6. Połącz się z usługą SQL Database i sprawdź, czy jest widoczna baza danych o nazwie **SampleTable**.

   ![Weryfikowanie tabeli przykładowej](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Weryfikowanie tabeli przykładowej")

7. Uruchom wybrane zapytanie, aby sprawdzić zawartość tabeli. Tabela powinna zawierać takie same dane jak ramka danych **renamedColumnsDF**.

    ![Sprawdzanie zawartości przykładowej tabeli](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "Sprawdzanie zawartości przykładowej tabeli")

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po ukończeniu tego samouczka możesz przerwać działanie klastra. W obszarze roboczym usługi Azure Databricks wybierz pozycję **Klastry** po lewej stronie. Aby przerwać działanie klastra, w obszarze **Akcje** wskaż wielokropek (...) i wybierz ikonę **Przerwij**.

![Zatrzymaj klaster datakostki](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Zatrzymaj klaster datakostki")

Jeśli klaster nie zostanie ręcznie zakończony, zostanie automatycznie zatrzymany, pod warunkiem, że zaznaczono pole wyboru **Przerwij po \_ \_ minutach braku aktywności** podczas tworzenia klastra. W takim przypadku nieaktywny klaster automatycznie zatrzymuje się po określonym czasie.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie usługi Azure Databricks
> * Tworzenie klastra Spark w usłudze Azure Databricks
> * Tworzenie notesu w usłudze Azure Databricks
> * Wyodrębnianie danych z konta usługi Data Lake Storage Gen2
> * Przekształcanie danych w usłudze Azure Databricks
> * Ładowanie danych do usługi Azure Synapse

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat przesyłania strumieniowego danych w czasie rzeczywistym do usługi Azure Databricks przy użyciu usługi Azure Event Hubs.

> [!div class="nextstepaction"]
>[Przesyłanie strumieniowe danych do usługi Azure Databricks przy użyciu usługi Event Hubs](databricks-stream-from-eventhubs.md)
