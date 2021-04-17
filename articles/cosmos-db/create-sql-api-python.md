---
title: 'Szybki start: tworzenie aplikacji w języku Python przy użyciu Azure Cosmos DB interfejsu API SQL'
description: Przykładowy kod w języku Python, którego można użyć do nawiązywania połączenia z interfejsem API SQL usługi Azure Cosmos DB i do wykonywania w niej zapytań
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 04/06/2021
ms.author: rosouz
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
- devx-track-python
ms.openlocfilehash: de05fdc110e653dd63cf098a8b31ffb81b13b1c5
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365687"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Szybki start: tworzenie aplikacji w języku Python przy użyciu Azure Cosmos DB interfejsu API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK 4](create-sql-api-java.md)
> * [Spring Data 3](create-sql-api-spring-data.md)
> * [Łącznik platformy Spark w wersji 3](create-sql-api-spark.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

W tym przewodniku Szybki start utworzysz konto interfejsu API SQL Azure Cosmos DB i zarządzasz nim z usługi Azure Portal oraz z usługi Visual Studio Code przy użyciu aplikacji języka Python sklonowanej z usługi GitHub. Azure Cosmos DB to wielo modelowa usługa bazy danych, która umożliwia szybkie tworzenie dokumentów, tabel, tabel, klucz-wartość i grafowych baz danych z możliwościami globalnej dystrybucji i skalowania w poziomie.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto Cosmos DB. Dostępne są następujące opcje:
    * W ramach aktywnej subskrypcji platformy Azure:
        * [Utwórz bezpłatne konto platformy Azure lub](https://azure.microsoft.com/free) użyj istniejącej subskrypcji 
        * [Visual Studio miesięczne środki](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers)
        * [Azure Cosmos DB Bezpłatna](./optimize-dev-test.md#azure-cosmos-db-free-tier)
    * Bez aktywnej subskrypcji platformy Azure:
        * [Wypróbuj Azure Cosmos DB bezpłatnie](https://azure.microsoft.com/try/cosmosdb/), środowisko testowe, które trwa 30 dni.
        * [Emulator usługi Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) 
- [Python 2.7 lub 3.6+](https://www.python.org/downloads/)z `python` plikiem wykonywalnym w pliku `PATH` .
- [Program Visual Studio Code](https://code.visualstudio.com/)
- Rozszerzenie [języka Python dla języka Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview).
- [Git](https://www.git-scm.com/downloads). 
- [Azure Cosmos DB SDK interfejsu API SQL dla języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Dodawanie kontenera

Teraz możesz użyć narzędzia Eksplorator danych w Azure Portal, aby utworzyć bazę danych i kontener. 

1. Wybierz **Eksplorator danych**  >  **nowy kontener.** 
    
    Obszar **Dodaj** kontener jest wyświetlany po prawej stronie. Aby go wyświetlić, może być konieczne przewinięcie w prawo.

    :::image type="content" source="./media/create-sql-api-python/azure-cosmosdb-data-explorer.png" alt-text="Eksplorator danych w witrynie Azure Portal, okienko Dodawanie kontenera":::

2. Na stronie **Dodawanie kontenera** wprowadź ustawienia dla nowego kontenera.

    |Ustawienie|Sugerowana wartość|Opis
    |---|---|---|
    |**Identyfikator bazy danych**|Zadania|Wprowadź *Zadania* jako nazwę nowej bazy danych. Nazwy baz danych muszą zawierać od 1 do 255 znaków i nie mogą zawierać znaków `/, \\, #, ?` ani mieć spacji na końcu. Zaznacz opcję **Aprowizuj** przepływność bazy danych, aby udostępnić przepływność aprowizowana w bazie danych we wszystkich kontenerach w bazie danych. Ta opcja pomaga również w oszczędzaniu kosztów. |
    |**Przepływność**|400|Pozostaw przepływność na poziomie 400 jednostek żądania na sekundę (RU/s). Jeśli chcesz zmniejszyć opóźnienie, możesz później skalować przepływność w górę.| 
    |**Identyfikator kontenera**|Elementy|Wprowadź *Items* jako nazwę nowego kontenera. W przypadku identyfikatorów kontenerów obowiązują takie same wymagania dotyczące znaków jak dla nazw baz danych.|
    |**Klucz partycji**| /category| W przykładzie opisanym w tym artykule *użyto /category* jako klucza partycji.|
    
    Oprócz powyższych ustawień możesz opcjonalnie dodać unikatowe **klucze** dla kontenera. W tym przykładzie pozostawmy pole puste. Unikatowe klucze umożliwiają deweloperom dodanie warstwy integralności danych do bazy danych. Tworząc zasady unikatowych kluczy podczas tworzenia kontenera, zapewniasz unikatowość co najmniej jednej wartości na klucz partycji. Aby dowiedzieć się więcej, zapoznaj się z artykułem [Unique keys in Azure Cosmos DB (Unikatowe klucze w usłudze Azure Cosmos DB)](unique-keys.md).
    
    Wybierz przycisk **OK**. W Eksploratorze danych zostanie wyświetlona nowa baza danych i nowy kontener.

## <a name="add-sample-data"></a>Dodawanie danych przykładowych

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Wysyłanie zapytań dotyczących danych

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujemy aplikację interfejsu API SQL z serwisu GitHub, ustawimy parametry połączenia i uruchomimy ją. W tym przewodniku Szybki start jest używana wersja 4 [zestawu SDK języka Python.](https://pypi.org/project/azure-cosmos/#history)

1. Otwórz wiersz polecenia, utwórz nowy folder o nazwie git-samples, a następnie zamknij wiersz polecenia.

    ```cmd
    md "git-samples"
    ```
   Jeśli używasz wiersza polecenia powłoki Bash, zamiast tego użyj następującego polecenia:

   ```bash
   mkdir "git-samples"
   ```

2. Otwórz okno terminala usługi Git, np. git bash, i użyj polecenia `cd`, aby przejść do nowego folderu instalacji aplikacji przykładowej.

    ```bash
    cd "git-samples"
    ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-python-getting-started.git
    ```  

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i skopiować je do aplikacji.

1. Na koncie Azure Cosmos DB w witrynie [Azure Portal](https://portal.azure.com/)wybierz pozycję **Klucze w** lewym panelu nawigacyjnym. Użyj przycisków kopiowania po prawej stronie ekranu,  aby skopiować adres **URI** i klucz podstawowy do *pliku cosmos_get_started.py* w następnym kroku.

    :::image type="content" source="./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png" alt-text="Uzyskaj klucz dostępu i URI w ustawieniach Klucze w Azure Portal":::

2. W Visual Studio Code otwórz plik *cosmos_get_started.py* w folderze *\git-samples\azure-cosmos-db-python-getting-started.*

3. Skopiuj wartość **URI** z portalu (przy użyciu przycisku kopiowania) i nadaj jej wartość zmiennej **punktu** końcowego w pliku *cosmos_get_started.py.* 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Następnie skopiuj **wartość KLUCZ PODSTAWOWY** z portalu i nadaj jej wartość klucza **w** *pliku cosmos_get_started.py.* Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do nawiązania komunikacji z usługą Azure Cosmos DB. 

    `key = 'FILLME'`

5. Zapisz *plik cosmos_get_started.py.*

## <a name="review-the-code"></a>Przeglądanie kodu

Ta czynność jest opcjonalna. Dowiedz się więcej o zasobach bazy danych utworzonych w kodzie lub przejdź do [tematu Aktualizowanie parametrów połączenia.](#update-your-connection-string)

Wszystkie poniższe fragmenty kodu są brane z *pliku cosmos_get_started.py.*

* Inicjowanie klienta CosmosClient. Pamiętaj, aby zaktualizować wartości "punkt końcowy" i "klucz", zgodnie z opisem w sekcji [Aktualizowanie parametrów](#update-your-connection-string) połączenia. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* Tworzenie nowej bazy danych.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* Zostanie utworzony nowy kontener z aprowizowanej [](request-units.md)przepływności 400 RU/s. Jako klucz `lastName` [partycji wybieramy](partitioning-overview.md#choose-partitionkey)klucz , który pozwala nam na wydajne zapytania filtrowane według tej właściwości. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Dodawanie niektórych elementów do kontenera. Kontenery są kolekcją elementów (dokumentów JSON), które mogą mieć zróżnicowany schemat. Metody pomocnika zwracają reprezentacje rodziny, które są przechowywane w Azure Cosmos DB jako dokumenty ```get_[name]_family_item``` JSON.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* Odczyty punktu (wyszukiwania wartości klucza) są wykonywane przy użyciu `read_item` metody . Drukujemy opłatę [za ru za](request-units.md) każdą operację.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* Zapytanie jest wykonywane przy użyciu składni zapytania SQL. Ponieważ używamy wartości klucza partycji w klauzuli WHERE, Azure Cosmos DB efektywnie przekieruje to zapytanie do odpowiednich ```lastName``` partycji, co poprawi wydajność.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>Uruchamianie aplikacji

1. W Visual Studio Code wybierz pozycję **Wyświetl**  >  **paletę poleceń.** 

2. W wierszu polecenia wprowadź **Python: Select Interpreter** (Python: wybierz interpreter), a następnie wybierz wersję języka Python do użycia.

    Stopka w programie Visual Studio Code jest aktualizowana w celu wskazania wybranego interpretera. 

3. Wybierz **pozycję View**  >  **Integrated Terminal (Wyświetl zintegrowany terminal),** aby Visual Studio Code zintegrowany terminal.

4. W oknie zintegrowanego terminalu upewnij się, że jesteś w folderze *azure-cosmos-db-python-getting-started.* Jeśli nie, uruchom poniższe polecenie, aby przejść do folderu przykładu. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Uruchom następujące polecenie, aby zainstalować pakiet azure-cosmos. 

    ```python
    pip install --pre azure-cosmos
    ```

    Jeśli podczas próby zainstalowania pakietu azure-cosmos zostanie wyświetlony błąd odmowy dostępu, będzie trzeba [uruchomić program VS Code jako administrator](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Uruchom następujące polecenie, aby uruchomić przykład oraz utworzyć i zapisać nowe dokumenty w Azure Cosmos DB.

    ```python
    python cosmos_get_started.py
    ```

7. Aby potwierdzić, że nowe elementy zostały utworzone i zapisane, w Azure Portal wybierz pozycję Eksplorator danych  >  **AzureSampleFamilyDatabase.**  >   Wyświetl elementy, które zostały utworzone. Oto przykładowy dokument JSON dla rodziny Andersen:
   
   ```json
   {
       "id": "Andersen-1569479288379",
       "lastName": "Andersen",
       "district": "WA5",
       "parents": [
           {
               "familyName": null,
               "firstName": "Thomas"
           },
           {
               "familyName": null,
               "firstName": "Mary Kay"
           }
       ],
       "children": null,
       "address": {
           "state": "WA",
           "county": "King",
           "city": "Seattle"
       },
       "registered": true,
       "_rid": "8K5qAIYtZXeBhB4AAAAAAA==",
       "_self": "dbs/8K5qAA==/colls/8K5qAIYtZXc=/docs/8K5qAIYtZXeBhB4AAAAAAA==/",
       "_etag": "\"a3004d78-0000-0800-0000-5d8c5a780000\"",
       "_attachments": "attachments/",
       "_ts": 1569479288
   }
   ```

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wiesz już, jak utworzyć konto usługi Azure Cosmos DB, utworzyć kontener przy użyciu usługi Eksplorator danych i uruchomić aplikację w języku Python w Visual Studio Code. Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importuj dane do usługi Azure Cosmos DB na potrzeby interfejsu API SQL](import-data.md)
