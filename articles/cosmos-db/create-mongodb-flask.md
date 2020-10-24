---
title: Utwórz aplikację sieci Web z kolbą Python przy użyciu interfejsu API Azure Cosmos DB dla MongoDB
description: Przykładowy kod Python Flask, którego można używać do nawiązywania połączeń i wykonywania zapytań przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.custom: devx-track-python
ms.openlocfilehash: a0545ea61879b30349403e019ead69ba14f75cb9
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486501"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Szybki Start: Tworzenie aplikacji w języku Python przy użyciu interfejsu API Azure Cosmos DB dla MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

W tym przewodniku szybki start użyjesz Azure Cosmos DB konta interfejsu API usługi Mongo DB lub emulatora Azure Cosmos DB do uruchamiania kolby Python To-Do aplikacji internetowej sklonowanej z usługi GitHub. Azure Cosmos DB to wielomodelowa usługa bazy danych, która pozwala szybko tworzyć i wysyłać zapytania dotyczące dokumentów, tabel, kluczy i wartościowych baz danych przy użyciu dystrybucji globalnej i możliwości skalowania w poziomie.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Lub [Wypróbuj bezpłatnie Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure. Można też użyć [emulatora Azure Cosmos DB](local-emulator.md). 
- [Python 3.6+](https://www.python.org/downloads/)
- [Visual Studio Code](https://code.visualstudio.com/Download) z [rozszerzeniem języka Python](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python).

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujemy aplikację Flask bazy danych MongoDB z repozytorium GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi.

1. Otwórz wiersz polecenia, utwórz nowy folder o nazwie git-samples, a następnie zamknij wiersz polecenia.

    ```bash
    md "C:\git-samples"
    ```

2. Otwórz okno terminala usługi Git, np. git bash, i użyj polecenia `cd`, aby przejść do nowego folderu instalacji aplikacji przykładowej.

    ```bash
    cd "C:\git-samples"
    ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

    ```bash
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. Uruchom następujące polecenie, aby zainstalować moduły języka Python.

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Otwórz folder w programie Visual Studio Code.

## <a name="review-the-code"></a>Przeglądanie kodu

Ta czynność jest opcjonalna. Jeśli chcesz się dowiedzieć, jak zasoby bazy danych są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Uruchamianie aplikacji internetowej](#run-the-web-app). 

Wszystkie poniższe fragmenty kodu pochodzą z pliku *App.py* i używają parametrów połączenia dla lokalnego emulatora Azure Cosmos DB. Hasło musi zostać podzielone, jak pokazano poniżej, aby uwzględnić ukośniki, których w przeciwnym razie nie można by było przeanalizować.

* Inicjujemy klienta bazy danych MongoDB, pobieramy bazę danych i uwierzytelniamy.

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* Pobieramy kolekcję lub tworzymy ją, jeśli jeszcze nie istnieje.

    ```python
    todos = db.todo #Select the collection
    ```

* Tworzenie aplikacji

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>Uruchom aplikację internetową

1. Upewnij się, że emulator usługi Azure Cosmos DB działa.

2. Otwórz okno terminalu i za pomocą polecenia `cd` przejdź do katalogu, w którym jest zapisana aplikacja.

3. Następnie Ustaw zmienną środowiskową dla aplikacji do kolby `set FLASK_APP=app.py` dla `$env:FLASK_APP = app.py` edytorów programu PowerShell lub w `export FLASK_APP=app.py` przypadku korzystania z komputera Mac. 

4. Uruchom aplikację za pomocą programu `flask run` i przejdź do *protokołu http: \/ /127.0.0.1:5000/*.

5. Dodawaj oraz usuwaj zadania i sprawdzaj, jak są dodawane i zmieniane w kolekcji.

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

Jeśli chcesz przetestować kod na koncie usługi Live Azure Cosmos DB, przejdź do Azure Portal, aby utworzyć konto.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Aby przetestować kod na koncie usługi Live Azure Cosmos DB, Pobierz informacje o parametrach połączenia. Następnie skopiuj je do aplikacji.

1. Na koncie Azure Cosmos DB w Azure Portal, w lewym okienku nawigacji wybierz pozycję **Parametry połączenia**, a następnie wybierz pozycję **klucze odczytu i zapisu**. W celu skopiowania nazwy użytkownika, parametrów połączenia i hasła będziesz używać przycisków kopiowania po prawej stronie ekranu. 

2. Otwórz plik *app.py* w katalogu głównym.

3. Skopiuj wartość **username** z portalu (przy użyciu przycisku kopiowania) i przypisz ją do klucza **name** w pliku *app.py*.

4. Następnie skopiuj wartość **parametrów połączenia** z portalu i ustaw ją jako wartość **MongoClient** w pliku *App.py* .

5. Na końcu skopiuj wartość **password** z portalu i przypisz ją do klucza **password** w pliku *app.py*.

Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do nawiązania komunikacji z usługą Azure Cosmos DB. Możesz ją uruchomić tak jak wcześniej.

## <a name="deploy-to-azure"></a>Wdróż na platformie Azure

Aby wdrożyć tę aplikację, możesz utworzyć nową aplikację internetową na platformie Azure i włączyć ciągłe wdrażanie z rozwidleniem tego repozytorium GitHub. Postępuj zgodnie z tym [samouczkiem](../app-service/deploy-continuous-deployment.md) , aby skonfigurować ciągłe wdrażanie za pomocą usługi GitHub na platformie Azure.

W przypadku wdrażania na platformie Azure należy usunąć klucze aplikacji i upewnić się, że poniższa sekcja nie jest wyłączona przez oznaczenie jej jako komentarza:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Następnie należy dodać do ustawień aplikacji odpowiednie wartości MONGOURL, MONGO_PASSWORD i MONGO_USERNAME. Aby dowiedzieć się więcej o ustawieniach aplikacji w usłudze Azure Web Apps, skorzystaj z tego [samouczka](../app-service/configure-common.md#configure-app-settings).

Jeśli nie chcesz tworzyć rozwidlenia tego repozytorium, możesz również wybrać poniższy przycisk **Wdróż na platformie Azure** . Następnie należy przejść do platformy Azure i skonfigurować ustawienia aplikacji przy użyciu informacji o koncie Azure Cosmos DB.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Jeśli planujesz przechowywanie kodu w usłudze GitHub lub w innych rozwiązaniach kontroli źródła, pamiętaj o usunięciu z kodu parametrów połączenia. Można je zamiast tego konfigurować za pomocą ustawień aplikacji internetowej.

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia Azure Cosmos DB dla konta interfejsu API usługi Mongo DB i używania emulatora Azure Cosmos DB do uruchamiania kolby Python To-Do aplikacji internetowej sklonowanej z usługi GitHub. Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importowanie danych z bazy danych MongoDB do usługi Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%252fazure%252fcosmos-db%252ftoc.json%253ftoc%253d%252fazure%252fcosmos-db%252ftoc.json)