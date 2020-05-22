---
title: Samouczek — implementowanie Azure Databricks przy użyciu punktu końcowego Cosmos DB
description: W tym samouczku opisano sposób implementacji Azure Databricks w sieci wirtualnej z punktem końcowym usługi włączonym dla Cosmos DB.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: e18c2b0f03f9ac2155c441580d62d6085581de12
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779593"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>Samouczek: implementowanie Azure Databricks przy użyciu punktu końcowego Cosmos DB

W tym samouczku opisano sposób implementacji środowiska sieci wirtualnej wstrzykniętego z włączonym punktem końcowym usługi dla Cosmos DB.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie obszaru roboczego Azure Databricks w sieci wirtualnej
> * Utwórz punkt końcowy usługi Cosmos DB
> * Utwórz konto Cosmos DB i Importuj dane
> * Tworzenie klastra Azure Databricks
> * Cosmos DB zapytań z notesu Azure Databricks

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem wykonaj następujące czynności:

* Utwórz [obszar roboczy Azure Databricks w sieci wirtualnej](quickstart-create-databricks-workspace-vnet-injection.md).

* Pobierz [Łącznik Spark](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar).

* Pobierz przykładowe dane z [centrów narodowych NOAA, aby uzyskać informacje o środowisku](https://www.ncdc.noaa.gov/stormevents/). Wybierz stan lub obszar i wybierz pozycję **Wyszukaj**. Na następnej stronie zaakceptuj wartości domyślne i wybierz pozycję **Wyszukaj**. Następnie wybierz pozycję **Pobierz woluminy CSV** po lewej stronie, aby pobrać wyniki.

* Pobierz [wstępnie skompilowany plik binarny](https://aka.ms/csdmtool) narzędzia do migracji danych Azure Cosmos DB.

## <a name="create-a-cosmos-db-service-endpoint"></a>Utwórz punkt końcowy usługi Cosmos DB

1. Po wdrożeniu obszaru roboczego Azure Databricks w sieci wirtualnej przejdź do sieci wirtualnej w [Azure Portal](https://portal.azure.com). Zwróć uwagę na podsieć publiczną i prywatną utworzoną za pomocą wdrożenia datakostki.

   ![Podsieci sieci wirtualnej](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. Wybierz *podsieć publiczną* i Utwórz punkt końcowy usługi Cosmos DB. Następnie **Zapisz**.
   
   ![Dodaj punkt końcowy usługi Cosmos DB](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Tworzenie konta usługi Cosmos DB

1. Otwórz witrynę Azure Portal. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > bazy danych > Azure Cosmos DB**.

2. Wypełnij **szczegóły wystąpienia** na karcie **podstawy** z następującymi ustawieniami:

   |Ustawienie|Wartość|
   |-------|-----|
   |Subskrypcja|*Twoja subskrypcja*|
   |Resource Group|*Twoja grupa zasobów*|
   |Nazwa konta|DB-VNET-Service-Endpoint|
   |Interfejs API|Core (SQL)|
   |Lokalizacja|Zachodnie stany USA|
   |Nadmiarowość geograficzna|Wyłącz|
   |Moduły zapisujące obsługujące wiele regionów|Włącz|

   ![Dodaj punkt końcowy usługi Cosmos DB](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. Wybierz kartę **Sieć** i skonfiguruj sieć wirtualną. 

   a. Wybierz sieć wirtualną utworzoną jako warunek wstępny, a następnie wybierz opcję *podsieć publiczna*. Zwróć uwagę, że w *podsieci prywatnej* *brakuje punktu końcowego "Microsoft AzureCosmosDB"*. Dzieje się tak dlatego, że punkt końcowy usługi Cosmos DB jest włączony tylko w *podsieci publicznej*.

   b. Upewnij się, że włączono opcję **zezwalania na dostęp z Azure Portal** . To ustawienie umożliwia dostęp do konta Cosmos DB z Azure Portal. Jeśli ta opcja jest ustawiona na **Odmów**, podczas próby uzyskania dostępu do konta otrzymasz błędy. 

   > [!NOTE]
   > Nie jest to konieczne w tym samouczku, ale możesz również włączyć *dostęp z adresu IP* , jeśli chcesz mieć możliwość dostępu do konta Cosmos DB z komputera lokalnego. Na przykład, jeśli łączysz się z kontem przy użyciu zestawu SDK Cosmos DB, musisz włączyć to ustawienie. Jeśli jest wyłączona, zostaną wyświetlone błędy "odmowa dostępu".

   ![Ustawienia sieci Cosmos DB konta](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. Wybierz pozycję **Przegląd + Utwórz**, a następnie **Utwórz** , aby utworzyć konto Cosmos DB w sieci wirtualnej.

5. Po utworzeniu konta Cosmos DB przejdź do pozycji **klucze** w obszarze **Ustawienia**. Skopiuj podstawowe parametry połączenia i Zapisz je w edytorze tekstu do późniejszego użycia.

    ![Strona kluczy konta Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. Wybierz **Eksplorator danych** i **nową kolekcję** , aby dodać nową bazę danych i kolekcję do konta Cosmos DB.

    ![Cosmos DB nową kolekcję](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Przekaż dane do Cosmos DB

1. Otwórz wersję interfejsu graficznego [Narzędzia do migracji danych dla Cosmos DB](https://aka.ms/csdmtool) **Dtui. exe**.

    ![Narzędzie do migracji danych usługi Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. Na karcie **informacje źródłowe** wybierz pozycję **pliki CSV** w polu **Importuj z** listy rozwijanej. Następnie wybierz pozycję **Dodaj pliki** i Dodaj CSV dane burzy, który został pobrany jako warunek wstępny.

    ![Informacje o źródle narzędzia do migracji danych Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. Na karcie **informacje docelowe** wprowadź parametry połączenia. Format ciągu połączenia to `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>` . AccountEndpoint i AccountKey są zawarte w podstawowych parametrach połączenia, które zostały zapisane w poprzedniej sekcji. Dołącz `Database=<your database name>` na końcu ciągu połączenia i wybierz pozycję **Weryfikuj**. Następnie Dodaj nazwę kolekcji i klucz partycji.

    ![Informacje o miejscu docelowym narzędzia do migracji danych Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. Wybierz pozycję **dalej** , aż uzyskasz dostęp do strony podsumowania. Następnie wybierz pozycję **Importuj**.

## <a name="create-a-cluster-and-add-library"></a>Tworzenie klastra i Dodawanie biblioteki

1. Przejdź do usługi Azure Databricks w obszarze [Azure Portal](https://portal.azure.com) i wybierz pozycję **Uruchom obszar roboczy**.

2. Utwórz nowy klaster. Wybierz nazwę klastra i zaakceptuj pozostałe ustawienia domyślne.

   ![Nowe ustawienia klastra](./media/service-endpoint-cosmosdb/create-cluster.png)

3. Po utworzeniu klastra przejdź do strony klaster i wybierz kartę **biblioteki** . Wybierz pozycję **Zainstaluj nowe** i Przekaż plik JAR łącznika platformy Spark, aby zainstalować bibliotekę.

    ![Zainstaluj bibliotekę łączników platformy Spark](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    Można sprawdzić, czy biblioteka została zainstalowana na karcie **biblioteki** .

    ![Karta biblioteki klastrów datakostek](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Cosmos DB zapytań z notesu datacegły

1. Przejdź do obszaru roboczego Azure Databricks i Utwórz nowy Notes w języku Python.

    ![Tworzenie nowego notesu datacegły](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. Uruchom następujący kod w języku Python, aby ustawić konfigurację połączenia Cosmos DB. Odpowiednio zmień **punkt końcowy**, **MasterKey**, **bazę danych**i **kolekcję** .

    ```python
    connectionConfig = {
      "Endpoint" : "https://<your Cosmos DB account name.documents.azure.com:443/",
      "Masterkey" : "<your Cosmos DB primary key>",
      "Database" : "<your database name>",
      "preferredRegions" : "West US 2",
      "Collection": "<your collection name>",
      "schema_samplesize" : "1000",
      "query_pagesize" : "200000",
      "query_custom" : "SELECT * FROM c"
    }
    ```

3. Użyj poniższego kodu w języku Python, aby załadować dane i utworzyć widok tymczasowy.

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. Użyj następującego polecenia Magic, aby wykonać instrukcję SQL, która zwraca dane.

    ```python
    %sql
    select * from storm
    ```

    Pomyślnie nawiązano połączenie z obszarem roboczym z wstrzykiwanymi sieciami wirtualnymi z włączonym Cosmos DB zasobem usługi. Aby dowiedzieć się więcej na temat nawiązywania połączenia z usługą Cosmos DB, zobacz [Azure Cosmos DB Connector for Apache Spark](https://github.com/Azure/azure-cosmosdb-spark).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, obszar roboczy Azure Databricks i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Usunięcie zadania pozwala uniknąć niepotrzebnych rozliczeń. Jeśli planujesz użyć obszaru roboczego Azure Databricks w przyszłości, możesz zatrzymać klaster i ponownie uruchomić go później. Jeśli nie chcesz nadal korzystać z tego obszaru roboczego Azure Databricks, Usuń wszystkie zasoby utworzone w tym samouczku, wykonując następujące czynności:

1. W menu po lewej stronie w Azure Portal kliknij pozycję **grupy zasobów** , a następnie kliknij nazwę utworzonej grupy zasobów.

2. Na stronie grupy zasobów wybierz pozycję **Usuń**, wpisz nazwę zasobu do usunięcia w polu tekstowym, a następnie ponownie wybierz pozycję **Usuń** .

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono obszar roboczy Azure Databricks w sieci wirtualnej i użyto łącznika Cosmos DB Spark do wykonywania zapytań dotyczących danych Cosmos DB z kostek. Aby dowiedzieć się więcej na temat pracy z Azure Databricks w sieci wirtualnej, przejdź do samouczka dotyczącego używania SQL Server z Azure Databricks.

> [!div class="nextstepaction"]
> [Samouczek: wykonywanie zapytania dotyczącego kontenera Docker SQL Server Linux w sieci wirtualnej z notesu Azure Databricks](vnet-injection-sql-server.md)
