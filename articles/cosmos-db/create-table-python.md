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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82083635"
---
# <a name="quickstart-build-a-table-api-app-with-python-and-azure-cosmos-db"></a>Szybki start: tworzenie aplikacji interfejsu API tabel przy użyciu języka Python i usługi Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

W tym przewodniku szybki start utworzysz konto Azure Cosmos DB interfejs API tabel i zarządzasz nim z Azure Portal oraz z programu Visual Studio z aplikacją w języku Python sklonowaną z usługi GitHub. Azure Cosmos DB to wielomodelowa usługa bazy danych, która pozwala szybko tworzyć i wysyłać zapytania dotyczące dokumentów, tabel, kluczy i wartościowych baz danych przy użyciu dystrybucji globalnej i możliwości skalowania w poziomie.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Lub [Wypróbuj bezpłatnie Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure. Można również użyć [emulatora Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) z identyfikatorem URI `https://localhost:8081` i kluczem `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Program Visual Studio 2019](https://www.visualstudio.com/downloads/)z obciążeniami programistycznymi **platformy Azure** i **programowaniem** w języku Python wybranym podczas instalacji. 
- Usługi [git](https://git-scm.com/downloads).

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

> ! WYOWIETLON Aby zapoznać się z bardziej szczegółowym omówieniem podobnego kodu, zobacz artykuł [Cosmos DB interfejs API tabel Sample](table-storage-how-to-use-python.md) .

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i skopiować je do aplikacji. Umożliwia to aplikacji komunikację z hostowaną bazą danych. 

1. Na koncie Azure Cosmos DB w [Azure Portal](https://portal.azure.com/)wybierz pozycję **Parametry połączenia**. 

    ![Wyświetlanie i kopiowanie parametrów połączenia w ustawieniach parametrów połączenia](./media/create-table-python/view-and-copy-connection-string-in-connection-string-settings.png)

2. Skopiuj NAZWĘ KONTA przy użyciu przycisku po prawej stronie pola.

3. Otwórz plik *config.py* i wklej nazwę konta z portalu do wartości STORAGE_ACCOUNT_NAME w wierszu 19.

4. Wróć do portalu i skopiuj KLUCZ PODSTAWOWY.

5. Wklej KLUCZ PODSTAWOWY z portalu do wartości STORAGE_ACCOUNT_KEY w wierszu 20.

6. Zapisz plik *config.py* .

## <a name="run-the-app"></a>Uruchomienie aplikacji

1. W programie Visual Studio kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań**.

2. Wybierz bieżące środowisko języka Python, a następnie kliknij prawym przyciskiem myszy.

2. Wybierz pozycję **Zainstaluj pakiet języka Python**, a następnie wprowadź pozycję *Azure-Storage-Table*.

3. Naciśnij klawisz F5, aby uruchomić aplikację. Aplikacja zostanie wyświetlona w przeglądarce. 

Teraz możesz wrócić do Eksploratora danych i zobaczyć, jak się pracuje z nowymi danymi, modyfikuje je i tworzy zapytania względem nich. 

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start dowiesz się, jak utworzyć konto Azure Cosmos DB, utworzyć tabelę przy użyciu Eksplorator danych i uruchomić aplikację w języku Python w programie Visual Studio, aby dodać dane tabeli.  Teraz można tworzyć zapytania do danych przy użyciu interfejsu API tabel.  

> [!div class="nextstepaction"]
> [Importowanie danych tabeli do interfejsu API tabeli](table-import.md)
