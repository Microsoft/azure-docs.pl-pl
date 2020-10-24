---
title: 'Szybki Start: Tworzenie aplikacji w języku Python przy użyciu Azure Cosmos DB konta interfejsu API SQL'
description: Przykładowy kod w języku Python, którego można użyć do nawiązywania połączenia z interfejsem API SQL usługi Azure Cosmos DB i do wykonywania w niej zapytań
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: anfeldma
ms.custom:
- seodec18
- seo-javascript-september2019
- seo-python-october2019
- devx-track-python
ms.openlocfilehash: aa23f6d5d7b81e93145fd7db3fb5b45e46918d21
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487589"
---
# <a name="quickstart-build-a-python-application-using-an-azure-cosmos-db-sql-api-account"></a>Szybki Start: Tworzenie aplikacji w języku Python przy użyciu Azure Cosmos DB konta interfejsu API SQL

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK 4](create-sql-api-java.md)
> * [Spring Data 3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

W tym przewodniku szybki start utworzysz konto Azure Cosmos DB interfejsu API SQL i zarządzasz nim z poziomu Azure Portal oraz Visual Studio Code z aplikacją w języku Python sklonowaną z usługi GitHub. Azure Cosmos DB to wielomodelowa usługa bazy danych, która pozwala szybko tworzyć i wysyłać zapytania dotyczące dokumentów, tabel, kluczy i wartościowych baz danych przy użyciu dystrybucji globalnej i możliwości skalowania w poziomie.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto Cosmos DB. Dostępne są następujące opcje:
    * W ramach aktywnej subskrypcji platformy Azure:
        * [Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free) lub Użyj istniejącej subskrypcji 
        * [Kredyty miesięczne programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers)
        * [Azure Cosmos DB warstwy Bezpłatna](./optimize-dev-test.md#azure-cosmos-db-free-tier)
    * Bez aktywnej subskrypcji platformy Azure:
        * [Wypróbuj bezpłatnie Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)w środowisku testowym trwającym przez 30 dni.
        * [Emulator usługi Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) 
- [Python 2,7 lub 3.5.3 +](https://www.python.org/downloads/), z `python` plikiem wykonywalnym w `PATH` .
- [Program Visual Studio Code](https://code.visualstudio.com/)
- [Rozszerzenie Python dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview).
- Usługi [git](https://www.git-scm.com/downloads). 
- [Azure Cosmos DB SQL API SDK dla języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Dodawanie kontenera

Teraz można użyć narzędzia Eksplorator danych w Azure Portal, aby utworzyć bazę danych i kontener. 

1. Wybierz pozycję **Eksplorator danych**  >  **nowy kontener**. 
    
    Obszar **Dodaj kontener** jest wyświetlany po prawej stronie, może być konieczne przewinięcie w prawo w celu wyświetlenia go.

    :::image type="content" source="./media/create-sql-api-python/azure-cosmosdb-data-explorer.png" alt-text="Eksplorator danych w witrynie Azure Portal, okienko Dodawanie kontenera":::

2. Na stronie **Dodawanie kontenera** wprowadź ustawienia dla nowego kontenera.

    |Ustawienie|Sugerowana wartość|Opis
    |---|---|---|
    |**Identyfikator bazy danych**|Zadania|Wprowadź *Zadania* jako nazwę nowej bazy danych. Nazwy baz danych muszą zawierać od 1 do 255 znaków i nie mogą zawierać znaków `/, \\, #, ?` ani mieć spacji na końcu. Sprawdź opcję **zainicjuj przepływność bazy danych** , która umożliwia udostępnianie przepływności dla bazy danych we wszystkich kontenerach w bazie danych. Ta opcja pomaga również w obniżyć kosztów. |
    |**Przepływność**|400|Pozostaw przepływność na 400 jednostek żądań na sekundę (RU/s). Jeśli chcesz zmniejszyć opóźnienie, możesz później skalować przepływność w górę.| 
    |**Identyfikator kontenera**|Elementy|Wprowadź *elementy* jako nazwę nowego kontenera. W przypadku identyfikatorów kontenerów obowiązują takie same wymagania dotyczące znaków jak dla nazw baz danych.|
    |**Klucz partycji**| /category| W przykładzie opisanym w tym artykule jest stosowany */Category* jako klucz partycji.|
    
    Oprócz powyższych ustawień można opcjonalnie dodać **unikatowe klucze** dla kontenera. W tym przykładzie pozostawmy pole puste. Unikatowe klucze umożliwiają deweloperom dodanie warstwy integralności danych do bazy danych. Tworząc unikatowe Zasady kluczy podczas tworzenia kontenera, należy zapewnić unikatowość jednej lub więcej wartości na klucz partycji. Aby dowiedzieć się więcej, zapoznaj się z artykułem [Unique keys in Azure Cosmos DB (Unikatowe klucze w usłudze Azure Cosmos DB)](unique-keys.md).
    
    Wybierz przycisk **OK**. W Eksploratorze danych zostanie wyświetlona nowa baza danych i nowy kontener.

## <a name="add-sample-data"></a>Dodawanie danych przykładowych

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Wysyłanie zapytań dotyczących danych

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujemy aplikację interfejsu API SQL z serwisu GitHub, ustawimy parametry połączenia i uruchomimy ją. Ten przewodnik Szybki Start używa wersji 4 [zestawu SDK języka Python](https://pypi.org/project/azure-cosmos/#history).

1. Otwórz wiersz polecenia, utwórz nowy folder o nazwie git-samples, a następnie zamknij wiersz polecenia.

    ```cmd
    md "git-samples"
    ```
   Jeśli używasz monitu bash, zamiast tego użyj następującego polecenia:

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

1. Na koncie Azure Cosmos DB w [Azure Portal](https://portal.azure.com/)wybierz pozycję **klucze** w lewym okienku nawigacji. Użyj przycisków kopiowania po prawej stronie ekranu, aby skopiować **Identyfikator URI** i **klucz podstawowy** do pliku *cosmos_get_started. PR* w następnym kroku.

    :::image type="content" source="./media/create-sql-api-dotnet/access-key-and-uri-in-keys-settings-in-the-azure-portal.png" alt-text="Eksplorator danych w witrynie Azure Portal, okienko Dodawanie kontenera":::

2. W Visual Studio Code Otwórz plik *cosmos_get_started. PR* w *\git-samples\azure-Cosmos-DB-Python-Getting-Started*.

3. Skopiuj wartość **identyfikatora URI** z portalu (przy użyciu przycisku kopiowania) i ustaw ją jako wartość zmiennej **punktu końcowego** w *cosmos_get_started. PR*. 

    `endpoint = 'https://FILLME.documents.azure.com',`

4. Następnie skopiuj wartość **klucza podstawowego** z portalu i ustaw ją jako wartość **klucza** w *cosmos_get_started. PR*. Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do nawiązania komunikacji z usługą Azure Cosmos DB. 

    `key = 'FILLME'`

5. Zapisz plik *cosmos_get_started. PR* .

## <a name="review-the-code"></a>Przeglądanie kodu

Ta czynność jest opcjonalna. Dowiedz się więcej o zasobach bazy danych utworzonych w kodzie lub przejdź z wyprzedzeniem, aby [zaktualizować parametry połączenia](#update-your-connection-string).

Wszystkie poniższe fragmenty kodu są pobierane z pliku *cosmos_get_started. PR* .

* Inicjowanie klienta CosmosClient. Upewnij się, że Zaktualizowano wartości "Endpoint" i "Key" zgodnie z opisem w sekcji [aktualizowanie parametrów połączenia](#update-your-connection-string) . 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_cosmos_client)]

* Tworzenie nowej bazy danych.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_database_if_not_exists)]

* Tworzony jest nowy kontener z 400 RU/s [zainicjowaną przepływność](request-units.md). Wybieramy `lastName` jako [klucz partycji](partitioning-overview.md#choose-partitionkey), który umożliwia nam wykonywanie wydajnych zapytań filtrujących tę właściwość. 

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_container_if_not_exists)]

* Dodawanie niektórych elementów do kontenera. Kontenery to kolekcja elementów (Dokumenty JSON), które mogą mieć zróżnicowany schemat. Metody pomocnika ```get_[name]_family_item``` zwracają reprezentacje rodziny, które są przechowywane w Azure Cosmos dB jako dokumenty JSON.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=create_item)]

* Odczyty punktów (wyszukiwania wartości klucza) są wykonywane przy użyciu `read_item` metody. Przypisujemy [opłaty za usługę ru](request-units.md) dla każdej operacji.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=read_item)]

* Zapytanie jest wykonywane przy użyciu składni zapytania SQL. Ponieważ korzystamy z wartości klucza partycji ```lastName``` w klauzuli WHERE, Azure Cosmos DB wydajnie kieruje to zapytanie do odpowiednich partycji, zwiększając wydajność.

    [!code-python[](~/azure-cosmos-db-python-getting-started/cosmos_get_started.py?name=query_items)]
   
## <a name="run-the-app"></a>Uruchamianie aplikacji

1. W Visual Studio Code wybierz pozycję **Widok**  >  **paleta poleceń**. 

2. W wierszu polecenia wprowadź **Python: Select Interpreter** (Python: wybierz interpreter), a następnie wybierz wersję języka Python do użycia.

    Stopka w programie Visual Studio Code jest aktualizowana w celu wskazania wybranego interpretera. 

3. Wybierz pozycję **Wyświetl**  >  **zintegrowany terminal** , aby otworzyć zintegrowany terminal Visual Studio Code.

4. W oknie zintegrowanych terminali upewnij się, że jesteś w folderze *Azure-Cosmos-DB-Python-The-Started* . Jeśli nie, uruchom poniższe polecenie, aby przejść do folderu przykładu. 

    ```cmd
    cd "\git-samples\azure-cosmos-db-python-getting-started"`
    ```

5. Uruchom następujące polecenie, aby zainstalować pakiet azure-cosmos. 

    ```python
    pip install --pre azure-cosmos
    ```

    Jeśli podczas próby zainstalowania pakietu azure-cosmos zostanie wyświetlony błąd odmowy dostępu, będzie trzeba [uruchomić program VS Code jako administrator](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Uruchom następujące polecenie, aby uruchomić przykład i utworzyć i zapisać nowe dokumenty w Azure Cosmos DB.

    ```python
    python cosmos_get_started.py
    ```

7. Aby potwierdzić, że nowe elementy zostały utworzone i zapisane, w Azure Portal wybierz pozycję **Eksplorator danych**  >  **AzureSampleFamilyDatabase**  >  **elementy**AzureSampleFamilyDatabase. Wyświetl elementy, które zostały utworzone. Oto przykład przykładowego dokumentu JSON dla rodziny Andersen:
   
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

W tym przewodniku szybki start przedstawiono sposób tworzenia konta Azure Cosmos DB, tworzenia kontenera przy użyciu Eksplorator danych i uruchamiania aplikacji w języku Python w Visual Studio Code. Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importuj dane do usługi Azure Cosmos DB na potrzeby interfejsu API SQL](import-data.md)