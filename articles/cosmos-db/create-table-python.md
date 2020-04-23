---
title: 'Szybki start: interfejs API tabel z językiem Python — Azure Cosmos DB'
description: Ten przewodnik Szybki start przedstawia sposób używania interfejsu API tabel usługi Azure Cosmos DB do tworzenia aplikacji przy użyciu witryny Azure Portal i języka Python
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: quickstart
ms.date: 04/10/2018
ms.author: sngun
ms.custom: seo-python-october2019
ms.openlocfilehash: 673155706c3b7d44b41562c2dc8ce24a94ac6a58
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083635"
---
# <a name="quickstart-build-a-table-api-app-with-python-and-azure-cosmos-db"></a>Szybki start: tworzenie aplikacji interfejsu API tabel przy użyciu języka Python i usługi Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

W tym przewodniku Szybki start można utworzyć konto interfejsu API tabeli usługi Azure Cosmos DB i zarządzać nim z witryny Azure Portal i visual studio za pomocą aplikacji języka Python sklonowanej z usługi GitHub. Usługa Azure Cosmos DB to wielomodelowa usługa bazy danych, która umożliwia szybkie tworzenie i wykonywanie zapytań o bazy danych dokumentów, tabeli, wartości klucza i wykresów z możliwościami dystrybucji globalnej i skali poziomej.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz jeden za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Możesz [też bezpłatnie wypróbować usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure. Można również użyć [emulatora bazy danych usługi Azure Cosmos](https://aka.ms/cosmosdb-emulator) z identyfikatorem URI `https://localhost:8081` i kluczem. `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`
- [Visual Studio 2019](https://www.visualstudio.com/downloads/), z **deweloperami platformy Azure** i obciążeń **programistów Python** wybrany podczas instalacji. 
- [Git](https://git-scm.com/downloads).

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

> [!IMPORTANT] 
> Musisz utworzyć nowe konto interfejsu API tabeli, aby pracować z ogólnie dostępnymi zestawami SDK interfejsu API tabeli. Konta interfejsu API tabeli utworzone w okresie obowiązywania wersji zapoznawczej nie są obsługiwane przez ogólnie dostępne zestawy SDK.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Dodawanie tabeli

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Dodawanie danych przykładowych

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujemy aplikację Tabela z serwisu GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi. 

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
    git clone https://github.com/Azure-Samples/storage-python-getting-started.git
    ```

3. Następnie otwórz plik rozwiązania w programie Visual Studio. 

> ! [PORADA] Aby uzyskać bardziej szczegółowe wskazówki dotyczące podobnego kodu, zobacz przykładowy artykuł [interfejsu API tabeli usługi Cosmos DB.](table-storage-how-to-use-python.md)

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i skopiować je do aplikacji. Umożliwia to aplikacji komunikację z hostowaną bazą danych. 

1. Na swoim koncie usługi Azure Cosmos DB w [portalu Azure](https://portal.azure.com/)wybierz pozycję **Parametry połączenia**. 

    ![Wyświetlanie i kopiowanie ciągu połączenia w ustawieniach parametry połączenia](./media/create-table-python/view-and-copy-connection-string-in-connection-string-settings.png)

2. Skopiuj NAZWĘ KONTA przy użyciu przycisku po prawej stronie pola.

3. Otwórz plik *config.py* i wklej nazwę konta z portalu do wartości STORAGE_ACCOUNT_NAME w wierszu 19.

4. Wróć do portalu i skopiuj KLUCZ PODSTAWOWY.

5. Wklej KLUCZ PODSTAWOWY z portalu do wartości STORAGE_ACCOUNT_KEY w wierszu 20.

6. Zapisz plik *config.py.*

## <a name="run-the-app"></a>Uruchomienie aplikacji

1. W programie Visual Studio kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań**.

2. Wybierz bieżące środowisko Języka Python, a następnie kliknij prawym przyciskiem myszy.

2. Wybierz **pozycję Zainstaluj pakiet Python**, a następnie wprowadź *tabelę azure-storage*.

3. Naciśnij klawisz F5, aby uruchomić aplikację. Aplikacja zostanie wyświetlona w przeglądarce. 

Teraz możesz wrócić do Eksploratora danych i zobaczyć, jak się pracuje z nowymi danymi, modyfikuje je i tworzy zapytania względem nich. 

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przedstawiono sposób tworzenia konta usługi Azure Cosmos DB, tworzenia tabeli przy użyciu Eksploratora danych i uruchamiania aplikacji języka Python w programie Visual Studio w celu dodania danych tabeli.  Teraz można tworzyć zapytania do danych przy użyciu interfejsu API tabel.  

> [!div class="nextstepaction"]
> [Importowanie danych tabeli do interfejsu API tabeli](table-import.md)
