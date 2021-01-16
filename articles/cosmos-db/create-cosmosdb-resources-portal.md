---
title: Szybki Start — tworzenie zasobów Azure Cosmos DB z poziomu Azure Portal
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia bazy danych, kontenera i elementów usługi Azure Cosmos przy użyciu Azure Portal.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/10/2020
ms.openlocfilehash: 731a1fdf9520efc43c954cba79602ed84f983b3d
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247254"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Szybki Start: Tworzenie konta, bazy danych, kontenera i elementów usługi Azure Cosmos w Azure Portal
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Za pomocą Azure Cosmos DB można szybko tworzyć i wykonywać zapytania dotyczące baz danych typu klucz/wartość, baz danych dokumentów i baz danych grafów, a wszystkie korzyści wynikające z dystrybucji globalnej i możliwości skalowania poziomego w ramach rdzenia Azure Cosmos DB. 

W tym przewodniku szybki start pokazano, jak za pomocą Azure Portal utworzyć konto [interfejsu API SQL](./introduction.md) Azure Cosmos DB, utworzyć bazę danych dokumentów i kontener oraz dodać dane do kontenera. 

## <a name="prerequisites"></a>Wymagania wstępne

Subskrypcja platformy Azure lub bezpłatne Azure Cosmos DB konto wersji próbnej
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

Przejdź do witryny [Azure Portal](https://portal.azure.com/), aby utworzyć konto usługi Azure Cosmos DB. Wyszukaj i wybierz pozycję **Azure Cosmos DB**.

   :::image type="content" source="./media/create-cosmosdb-resources-portal/find-nosql-cosmosdb-marketplace.png" alt-text="Okienko Bazy danych w witrynie Azure Portal":::

1. Wybierz pozycję **Dodaj**.
1. Na stronie **Tworzenie konta usługi Azure Cosmos DB** wprowadź podstawowe ustawienia nowego konta usługi Azure Cosmos. 

    |Ustawienie|Wartość|Opis |
    |---|---|---|
    |Subskrypcja|Nazwa subskrypcji|Wybierz subskrypcję platformy Azure, której chcesz użyć dla tego konta usługi Azure Cosmos. |
    |Grupa zasobów|Nazwa grupy zasobów|Wybierz grupę zasobów lub wybierz pozycję **Utwórz nową**, a następnie wprowadź unikatową nazwę nowej grupy zasobów. |
    |Nazwa konta|Unikatowa nazwa|Wprowadź nazwę, która będzie identyfikować konto usługi Azure Cosmos. Ponieważ adres *documents.azure.com* jest dołączany do podanej nazwy w celu utworzenia identyfikatora URI, użyj unikatowej nazwy.<br><br>Nazwa może zawierać tylko małe litery, cyfry i znaki łącznika (-). Musi mieć długość od 3 do 31 znaków.|
    |Interfejs API|Typ konta do utworzenia|Wybierz pozycję **Core (SQL)** , aby utworzyć bazę danych dokumentów i wykonać zapytanie przy użyciu składni języka SQL. <br><br>Interfejs API określa typ konta do utworzenia. Usługa Azure Cosmos DB oferuje pięć interfejsów API: Core (SQL) i MongoDB dla danych dokumentów, Gremlin dla danych grafów, Azure Table i Cassandra. Obecnie dla każdego interfejsu API należy utworzyć oddzielne konto. <br><br>[Dowiedz się więcej o interfejsie API SQL](introduction.md).|
    |Tryb wydajności|Nieobsługiwana przepływność lub bezserwerowy|Wybierz opcję **zainicjowana przepływność** , aby utworzyć konto w trybie [przepływności aprowizacji](set-throughput.md) . Wybierz opcję **Bezserwerowa** , aby utworzyć konto w trybie [bezserwerowym](serverless.md) .|
    |Zastosuj rabat Azure Cosmos DB warstwy Bezpłatna|Zastosuj lub nie stosuj|W ramach warstwy Bezpłatna usługi Azure Cosmos DB uzyskasz bezpłatnie pierwsze 400 jednostek RU/s i 5 GB magazynu na koncie. Dowiedz się więcej o [warstwie Bezpłatna](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Lokalizacja|Region najbliżej Twoich użytkowników|Wybierz lokalizację geograficzną, w której będzie hostowane konto usługi Azure Cosmos DB. Użyj lokalizacji znajdującej się najbliżej Twoich użytkowników, aby zapewnić im najszybszy dostęp do danych.|
    |Typ konta|Produkcyjne lub nieprodukcyjne|Wybierz pozycję **Produkcyjne**, jeśli konto będzie używane w obciążeniu produkcyjnym. Wybierz pozycję **Nieprodukcyjne**, jeśli konto będzie używane na potrzeby nieprodukcyjne, na przykład programowania, testowania, kontroli jakości lub wdrażania przejściowego. Jest to ustawienie tagu zasobu platformy Azure, które dostosowuje środowisko portalu, ale nie wpływa na bazowe konto usługi Azure Cosmos DB. Tę wartość można zmienić w dowolnym momencie.|
    |Nadmiarowość geograficzna|Włącz lub Wyłącz|Włącz lub Wyłącz dystrybucję globalną na swoim koncie przez parowanie regionu z obszarem pary. Później możesz dodać więcej regionów do swojego konta.|
    |Moduły zapisujące obsługujące wiele regionów|Włącz lub Wyłącz|Funkcja zapisów obejmujących wiele regionów pozwala korzystać z zalet elastyczności baz danych i kontenerów na całym świecie.|
    |Strefy dostępności|Włącz lub Wyłącz|Strefy dostępności pomóc w dalszej poprawie dostępności i odporności aplikacji.|

> [!NOTE]
> W ramach jednej subskrypcji platformy Azure można korzystać z maksymalnie jednego konta usługi Azure Cosmos DB w warstwie Bezpłatna. Tę opcję należy wybrać podczas tworzenia konta. Jeśli opcja zastosowania rabatu na podstawie warstwy Bezpłatna nie jest widoczna, inne konto w subskrypcji już korzysta z warstwy Bezpłatna.

> [!NOTE]
> Następujące opcje są niedostępne, jeśli wybierzesz opcję **bezserwerowe** jako **tryb pojemności**:
> - Zastosuj rabat na podstawie warstwy Bezpłatna
> - Nadmiarowość geograficzna
> - Moduły zapisujące obsługujące wiele regionów
   
   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail.png" alt-text="Strona nowego konta usługi Azure Cosmos DB":::

1. Wybierz pozycję **Przegląd + utwórz**. Sekcje **Sieć** i **Tagi** możesz pominąć.

1. Przejrzyj ustawienia konta, a następnie wybierz pozycję **Utwórz**. Utworzenie konta trwa kilka minut. Poczekaj na wyświetlenie komunikatu **Wdrożenie zostało ukończone** na stronie portalu. 

    :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png" alt-text="Okienko Powiadomienia w witrynie Azure Portal":::

1. Wybierz pozycję **Przejdź do zasobu**, aby przejść do strony konta usługi Azure Cosmos DB. 

    :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-quickstart-pane.png" alt-text="Strona konta usługi Azure Cosmos DB":::

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Dodawanie bazy danych i kontenera 

Możesz użyć Eksplorator danych w Azure Portal, aby utworzyć bazę danych i kontener. 

1.  Wybierz pozycję **Eksplorator danych** w lewym obszarze nawigacji na stronie konta Azure Cosmos DB, a następnie wybierz pozycję **nowy kontener**. 
    
    Może być konieczne przewinięcie w prawo, aby wyświetlić okno **Dodawanie kontenera** .
    
    :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png" alt-text="Eksplorator danych w witrynie Azure Portal, okienko Dodawanie kontenera":::
    
1.  W okienku **Dodaj kontener** wprowadź ustawienia dla nowego kontenera.
    
    |Ustawienie|Sugerowana wartość|Opis
    |---|---|---|
    |**Identyfikator bazy danych**|ToDoList|Wprowadź *todolist* jako nazwę nowej bazy danych. Nazwy baz danych muszą zawierać od 1 do 255 znaków i nie mogą zawierać znaków `/, \\, #, ?` ani mieć spacji na końcu. Sprawdź opcję **zainicjuj przepływność bazy danych** , która umożliwia udostępnianie przepływności dla bazy danych we wszystkich kontenerach w bazie danych. Ta opcja pomaga również w obniżyć kosztów. |
    |**Przepływność**|400|Pozostaw przepływność na 400 jednostek żądań na sekundę (RU/s). Jeśli chcesz zmniejszyć opóźnienie, możesz później skalować przepływność w górę.<br><br>**Uwaga**: to ustawienie nie jest dostępne podczas tworzenia nowego kontenera na koncie bezserwerowym.| 
    |**Identyfikator kontenera**|Elementy|Wprowadź *elementy* jako nazwę nowego kontenera. W przypadku identyfikatorów kontenerów obowiązują takie same wymagania dotyczące znaków jak dla nazw baz danych.|
    |**Klucz partycji**| /category| W przykładzie opisanym w tym artykule jest stosowany */Category* jako klucz partycji.|

    
    Nie dodawaj **unikatowych kluczy** dla tego przykładu. Klucze unikatowe umożliwiają dodanie warstwy integralności danych do bazy danych, zapewniając unikatowość jednej lub więcej wartości na klucz partycji. Aby uzyskać więcej informacji, zobacz [unikalne klucze w Azure Cosmos DB](unique-keys.md).
    
1.  Wybierz przycisk **OK**. W Eksplorator danych zostanie wyświetlona nowa baza danych i kontener, który został utworzony.

## <a name="add-data-to-your-database"></a>Dodawanie danych do bazy danych

Dodaj dane do nowej bazy danych przy użyciu Eksplorator danych.

1. W **Eksplorator danych** rozwiń bazę danych **todolist** i rozwiń kontener **elementów** . Następnie wybierz pozycję **elementy**, a następnie wybierz pozycję **nowy element**. 
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png" alt-text="Tworzenie nowych dokumentów w Eksploratorze danych w witrynie Azure Portal":::
   
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
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png" alt-text="Kopiuj w danych JSON i wybierz pozycję Zapisz w Eksplorator danych w Azure Portal":::
   
1. Ponownie wybierz pozycję **Nowy dokument** , a następnie utwórz i Zapisz inny dokument z unikatowymi wartościami i innymi wybranymi `id` właściwościami. Dokumenty mogą mieć dowolną strukturę, ponieważ Azure Cosmos DB nie nakłada żadnych schematów na dane.

## <a name="query-your-data"></a>Wysyłanie zapytań dotyczących danych

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

Jeśli chcesz usunąć tylko bazę danych i korzystać z konta usługi Azure Cosmos w przyszłości, możesz usunąć tę bazę danych, wykonując następujące czynności:

* Masz konto usługi Azure Cosmos.
* Otwórz **Eksplorator danych**, kliknij prawym przyciskiem myszy bazę danych, którą chcesz usunąć, a następnie wybierz polecenie **Usuń bazę danych**.
* Wprowadź identyfikator bazy danych/nazwę bazy danych, aby potwierdzić operację usuwania. 

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia konta Azure Cosmos DB, tworzenia bazy danych i kontenera przy użyciu Eksplorator danych. Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)