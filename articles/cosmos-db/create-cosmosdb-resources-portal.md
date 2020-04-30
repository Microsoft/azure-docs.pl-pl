---
title: Szybki Start — tworzenie zasobów Azure Cosmos DB z poziomu Azure Portal
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia bazy danych, kontenera i elementów usługi Azure Cosmos przy użyciu Azure Portal.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/05/2020
ms.openlocfilehash: 79deb2f33a11e8ccb6f059bde7590b7cc0fe20c0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80521141"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Szybki Start: Tworzenie konta, bazy danych, kontenera i elementów usługi Azure Cosmos w Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Za pomocą Azure Cosmos DB można szybko tworzyć i wykonywać zapytania dotyczące baz danych typu klucz/wartość, baz danych dokumentów i baz danych grafów, a wszystkie korzyści wynikające z dystrybucji globalnej i możliwości skalowania poziomego w ramach rdzenia Azure Cosmos DB. 

W tym przewodniku szybki start pokazano, jak używać Azure Portal do tworzenia Azure Cosmos DB konta [interfejsu API SQL](sql-api-introduction.md) , tworzenia bazy danych dokumentów i kontenera oraz dodawania danych do kontenera. 

## <a name="prerequisites"></a>Wymagania wstępne

Subskrypcja platformy Azure lub bezpłatne Azure Cosmos DB konto wersji próbnej
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

Przejdź do witryny [Azure Portal](https://portal.azure.com/), aby utworzyć konto usługi Azure Cosmos DB. Wyszukaj i wybierz usługę **Azure Cosmos DB**.

   ![Okienko Bazy danych w witrynie Azure Portal](./media/create-cosmosdb-resources-portal/find-nosql-cosmosdb-marketplace.png)

1. Wybierz pozycję **Dodaj**.
1. Na stronie **Tworzenie konta usługi Azure Cosmos DB** wprowadź podstawowe ustawienia nowego konta usługi Azure Cosmos. 

    |Ustawienie|Wartość|Opis |
    |---|---|---|
    |Subskrypcja|Nazwa subskrypcji|Wybierz subskrypcję platformy Azure, której chcesz użyć dla tego konta usługi Azure Cosmos. |
    |Grupa zasobów|Nazwa grupy zasobów|Wybierz grupę zasobów lub wybierz pozycję **Utwórz nową**, a następnie wprowadź unikatową nazwę nowej grupy zasobów. |
    |Nazwa konta|Unikatowa nazwa|Wprowadź nazwę, która będzie identyfikować konto usługi Azure Cosmos. Ponieważ adres *documents.azure.com* jest dołączany do podanej nazwy w celu utworzenia identyfikatora URI, użyj unikatowej nazwy.<br><br>Nazwa może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi mieć długość od 3 do 31 znaków.|
    |Interfejs API|Typ konta do utworzenia|Wybierz pozycję **Core (SQL)**, aby utworzyć bazę danych dokumentów i wykonać zapytanie przy użyciu składni języka SQL. <br><br>Interfejs API określa typ konta do utworzenia. Azure Cosmos DB oferuje pięć interfejsów API: Core (SQL) i MongoDB dla danych dokumentu, Gremlin dla danych grafów, Azure Table i Cassandra. Obecnie dla każdego interfejsu API należy utworzyć oddzielne konto. <br><br>[Dowiedz się więcej o interfejsie API SQL](introduction.md).|
    |Zastosuj rabat w warstwie Bezpłatna|Zastosuj lub nie stosuj|Dzięki Azure Cosmos DB warstwy Bezpłatna uzyskasz bezpłatnie 400 RU/s i 5 GB miejsca na koncie. Dowiedz się więcej o [warstwie Bezpłatna](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Lokalizacja|Region najbliżej Twoich użytkowników|Wybierz lokalizację geograficzną, w której będzie hostowane konto usługi Azure Cosmos DB. Użyj lokalizacji znajdującej się najbliżej Twoich użytkowników, aby zapewnić im najszybszy dostęp do danych.|
    |Typ konta|Produkcja lub nieprodukcyjna|Wybierz pozycję **produkcja** , jeśli konto będzie używane w obciążeniu produkcyjnym. Wybierz opcję **nieprodukcyjne** , jeśli konto będzie używane dla nieprodukcyjnego, na przykład programowanie, testowanie, pytania i przemieszczenie. Jest to ustawienie tagu zasobu platformy Azure, które dostosowuje środowisko portalu, ale nie wpływa na bazowe konto Azure Cosmos DB. Tę wartość można zmienić w dowolnym momencie.|


> [!NOTE]
> W ramach subskrypcji platformy Azure można korzystać z maksymalnie jednej bezpłatnej warstwy Azure Cosmos DB i musimy zadecydować, aby utworzyć konto. Jeśli opcja zastosowania rabatu warstwy Bezpłatna nie jest widoczna, oznacza to, że inne konto w ramach subskrypcji zostało już włączone w ramach warstwy Bezpłatna.
   
   ![Strona nowego konta usługi Azure Cosmos DB](./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail.png)

1. Wybierz pozycję **Przegląd + utwórz**. Sekcje **Sieć** i **Tagi** możesz pominąć.

1. Przejrzyj ustawienia konta, a następnie wybierz pozycję **Utwórz**. Utworzenie konta trwa kilka minut. Poczekaj na wyświetlenie komunikatu **Wdrożenie zostało ukończone** na stronie portalu. 

    ![Okienko Powiadomienia w witrynie Azure Portal](./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png)

1. Wybierz pozycję **Przejdź do zasobu**, aby przejść do strony konta usługi Azure Cosmos DB. 

    ![Strona konta usługi Azure Cosmos DB](./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-quickstart-pane.png)

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Dodawanie bazy danych i kontenera 

Możesz użyć Eksplorator danych w Azure Portal, aby utworzyć bazę danych i kontener. 

1.  Wybierz pozycję **Eksplorator danych** w lewym obszarze nawigacji na stronie konta Azure Cosmos DB, a następnie wybierz pozycję **nowy kontener**. 
    
    Może być konieczne przewinięcie w prawo, aby wyświetlić okno **Dodawanie kontenera** .
    
    ![Eksplorator danych w witrynie Azure Portal, okienko Dodawanie kontenera](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  W okienku **Dodaj kontener** wprowadź ustawienia dla nowego kontenera.
    
    |Ustawienie|Sugerowana wartość|Opis
    |---|---|---|
    |**Identyfikator bazy danych**|ToDoList|Wprowadź *todolist* jako nazwę nowej bazy danych. Nazwy baz danych muszą zawierać od 1 do 255 znaków i nie mogą zawierać znaków `/, \\, #, ?` ani mieć spacji na końcu. Sprawdź opcję **zainicjuj przepływność bazy danych** , która umożliwia udostępnianie przepływności dla bazy danych we wszystkich kontenerach w bazie danych. Ta opcja pomaga również w obniżyć kosztów. |
    |**Przepływność**|400|Pozostaw przepływność na 400 jednostek żądań na sekundę (RU/s). Jeśli chcesz zmniejszyć opóźnienie, możesz później przeskalować przepływność w górę.| 
    |**Identyfikator kontenera**|Items|Wprowadź *elementy* jako nazwę nowego kontenera. W przypadku identyfikatorów kontenerów obowiązują takie same wymagania dotyczące znaków jak dla nazw baz danych.|
    |**Klucz partycji**| /category| W przykładzie opisanym w tym artykule jest stosowany */Category* jako klucz partycji.|

    
    Nie dodawaj **unikatowych kluczy** dla tego przykładu. Klucze unikatowe umożliwiają dodanie warstwy integralności danych do bazy danych, zapewniając unikatowość jednej lub więcej wartości na klucz partycji. Aby uzyskać więcej informacji, zobacz [unikalne klucze w Azure Cosmos DB](unique-keys.md).
    
1.  Wybierz przycisk **OK**. W Eksplorator danych zostanie wyświetlona nowa baza danych i kontener, który został utworzony.

## <a name="add-data-to-your-database"></a>Dodawanie danych do bazy danych

Dodaj dane do nowej bazy danych przy użyciu Eksplorator danych.

1. W **Eksplorator danych**rozwiń bazę danych **todolist** i rozwiń kontener **elementów** . Następnie wybierz pozycję **elementy**, a następnie wybierz pozycję **nowy element**. 
   
   ![Tworzenie nowych dokumentów w Eksploratorze danych w witrynie Azure Portal](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
   
1. Dodaj następującą strukturę do dokumentu po prawej stronie okienka **dokumenty** :

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. Wybierz pozycję **Zapisz**.
   
   ![Kopiuj w danych JSON i wybierz pozycję Zapisz w Eksplorator danych w Azure Portal](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)
   
1. Ponownie wybierz pozycję **Nowy dokument** , a następnie utwórz i Zapisz inny dokument z `id`unikatowymi wartościami i innymi wybranymi właściwościami. Dokumenty mogą mieć dowolną strukturę, ponieważ Azure Cosmos DB nie nakłada żadnych schematów na dane.

## <a name="query-your-data"></a>Wysyłanie zapytań dotyczących danych

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

Jeśli chcesz usunąć tylko bazę danych i korzystać z konta usługi Azure Cosmos w przyszłości, możesz usunąć tę bazę danych, wykonując następujące czynności:

* Masz konto usługi Azure Cosmos.
* Otwórz **Eksplorator danych**, kliknij prawym przyciskiem myszy bazę danych, którą chcesz usunąć, a następnie wybierz polecenie **Usuń bazę danych**.
* Wprowadź identyfikator bazy danych/nazwę bazy danych, aby potwierdzić operację usuwania. 

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia konta Azure Cosmos DB, tworzenia bazy danych i kontenera przy użyciu Eksplorator danych. Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)
