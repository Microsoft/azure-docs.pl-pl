---
title: Użyj Data Manager łańcucha bloków, aby zaktualizować Azure Cosmos DB — usługa Azure łańcucha bloków Service
description: Wysyłanie danych łańcucha bloków do Azure Cosmos DB za pomocą łańcucha bloków Data Manager dla usługi Azure łańcucha bloków
ms.date: 03/08/2020
ms.topic: tutorial
ms.reviewer: chroyal
ms.openlocfilehash: 69790787bc888448f2f40178bd12ee7058cc5892
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91283461"
---
# <a name="tutorial-use-blockchain-data-manager-to-send-data-to-azure-cosmos-db"></a>Samouczek: wysyłanie danych do Azure Cosmos DB za pomocą łańcucha bloków Data Manager

W tym samouczku użyjesz łańcucha bloków Data Manager dla usługi Azure łańcucha bloków, aby zarejestrować dane transakcji łańcucha bloków w Azure Cosmos DB. Łańcucha bloków Data Manager przechwytuje, przekształca i dostarcza dane finansów łańcucha bloków do tematów Azure Event Grid. Z poziomu Azure Event Grid używasz łącznika aplikacji logiki platformy Azure do tworzenia dokumentów w Azure Cosmos DB bazie danych. Po zakończeniu pracy z samouczkiem możesz eksplorować dane transakcji łańcucha bloków w Eksplorator danych Azure Cosmos DB.

[![Zrzut ekranu przedstawia szczegóły transakcji łańcucha bloków.](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Utwórz wystąpienie Data Manager łańcucha bloków
> * Dodawanie aplikacji łańcucha bloków do dekodowania właściwości transakcji i zdarzeń
> * Tworzenie konta Azure Cosmos DB i bazy danych do przechowywania danych transakcji
> * Utwórz aplikację logiki platformy Azure, aby połączyć temat Azure Event Grid z Azure Cosmos DB
> * Wyślij transakcję do księgi łańcucha bloków
> * Wyświetl zdekodowane dane transakcji w Azure Cosmos DB

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Kończenie [szybkiego startu: Tworzenie elementu członkowskiego łańcucha bloków przy użyciu Azure Portal](create-member.md) lub [szybkiego startu: Tworzenie elementu członkowskiego łańcucha bloków usługi Azure łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure](create-member-cli.md)
* Kończenie [szybkiego startu: użyj Visual Studio Code, aby nawiązać połączenie z siecią konsorcjum usługi Azure łańcucha bloków](connect-vscode.md). Przewodnik Szybki Start przeprowadzi Cię przez instalację [usługi Azure łańcucha bloków Development Kit dla Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) i skonfigurowanie środowiska deweloperskiego łańcucha bloków.
* Pełny [Samouczek: Tworzenie, kompilowanie i wdrażanie inteligentnych kontraktów przy użyciu Visual Studio Code](send-transaction.md). Samouczek przeprowadzi Cię przez proces tworzenia przykładowego kontraktu inteligentnego.
* Tworzenie [tematu Event Grid](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Informacje o [obsłudze zdarzeń w Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Utwórz wystąpienie

Wystąpienie Data Manager łańcucha bloków nawiązuje połączenie i monitoruje węzeł transakcji usługi Azure łańcucha bloków. Wystąpienie przechwytuje wszystkie nieprzetworzone i nieprzetworzone dane transakcji z węzła transakcji. Połączenie wychodzące wysyła dane łańcucha bloków do Azure Event Grid. Podczas tworzenia wystąpienia można skonfigurować pojedyncze połączenie wychodzące.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do elementu członkowskiego usługi Azure łańcucha bloków, który został utworzony w ramach wymagania wstępnego [przewodnika Szybki Start: Tworzenie elementu członkowskiego łańcucha bloków przy użyciu Azure Portal](create-member.md). Wybierz pozycję **łańcucha bloków Data Manager**.
1. Wybierz pozycję **Dodaj**.

    ![Dodaj Data Manager łańcucha bloków](./media/data-manager-cosmosdb/add-instance.png)

    Wprowadź następujące wartości:

    Ustawienie | Przykład | Opis
    --------|---------|------------
    Nazwa | Czujka | Wprowadź unikatową nazwę połączonego Data Manager łańcucha bloków.
    Węzeł transakcji | myblockchainmember | Wybierz domyślny węzeł transakcji dla elementu członkowskiego usługi Azure łańcucha bloków, który został utworzony w ramach wymagań wstępnych.
    Nazwa połączenia | cosmosdb | Wprowadź unikatową nazwę połączenia wychodzącego, w którym są wysyłane dane transakcji łańcucha bloków.
    Punkt końcowy siatki zdarzeń | Temat | Wybierz temat dotyczący siatki zdarzeń utworzony w ramach wymagania wstępnego. Uwaga: wystąpienie Data Manager łańcucha bloków i temat usługi Event Grid muszą znajdować się w tej samej subskrypcji.

1. Kliknij przycisk **OK**.

    Utworzenie wystąpienia Data Manager łańcucha bloków może zająć mniej niż minutę. Po wdrożeniu wystąpienia zostanie ono automatycznie uruchomione. Uruchomione wystąpienie Data Manager łańcucha bloków przechwytuje zdarzenia łańcucha bloków z węzła Transaction i wysyła dane do usługi Event Grid.

## <a name="add-application"></a>Dodawanie aplikacji

Dodaj aplikację łańcucha bloków **helloblockchain** , aby łańcucha bloków Data Manager dekoduje zdarzenie i stan właściwości. Aby można było dodać aplikację, łańcucha bloków Data Manager wymaga pliku ABI i kodu bajtowego kontraktu inteligentnego.

### <a name="get-contract-abi-and-bytecode"></a>Pobierz ABI kontraktu i kod bajtowy

ABI kontraktu definiuje inteligentne interfejsy kontraktu. Opisuje sposób korzystania z kontraktu inteligentnego. Aby skopiować ABI kontraktu do schowka, możesz użyć [rozszerzenia Azure łańcucha bloków Development Kit for Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) .

1. W okienku Eksploratora Visual Studio Code rozwiń folder **Build/Contract (kompilacja/kontrakty** w projekcie **helloblockchain** Solid) utworzonym w samouczku wymagań wstępnych [: Użyj Visual Studio Code, aby utworzyć, skompilować i wdrożyć inteligentne kontrakty](send-transaction.md).
1. Kliknij prawym przyciskiem myszy plik JSON metadanych kontraktu. Nazwa pliku jest nazwą kontraktu inteligentnego, po której następuje rozszerzenie **. JSON** .
1. Wybierz pozycję **Kopiuj kontrakt ABI**.

    ![Visual Studio Code okienku z opcją Kopiuj kontrakt ABI](./media/data-manager-cosmosdb/abi-devkit.png)

    ABI kontraktu jest kopiowany do Schowka.

1. Zapisz tablicę **ABI** jako plik JSON. Na przykład *abi.js*. Ten plik jest używany w późniejszym kroku.

Łańcucha bloków Data Manager wymaga wdrożonego kodu bajtowego dla kontraktu inteligentnego. Wdrożony kod bajtowy jest inny niż kod bajtowy kontraktu inteligentnego. Użyj rozszerzenia Azure łańcucha bloków Development Kit, aby skopiować kod bajtowy do Schowka.

1. W okienku Eksploratora Visual Studio Code rozwiń folder **kompilacja/kontrakty** w projekcie o trwałym stanie.
1. Kliknij prawym przyciskiem myszy plik JSON metadanych kontraktu. Nazwa pliku jest nazwą kontraktu inteligentnego, po której następuje rozszerzenie **. JSON** .
1. Wybierz pozycję **Kopiuj transakcję kod bajtowy**.

    ![Visual Studio Code okienku z opcją kopiowania kodu bajtowego transakcji](./media/data-manager-cosmosdb/bytecode-devkit.png)

    Kod bajtowy jest kopiowany do Schowka.

1. Zapisz wartość **kodu bajtowego** jako plik JSON. Na przykład *bytecode.js*. Ten plik jest używany w późniejszym kroku.

Poniższy przykład pokazuje *abi.json* i *bytecode.jsna* plikach otwartych w edytorze vs Code. Pliki powinny wyglądać podobnie.

![Przykład abi.jsi bytecode.jsdla plików](./media/data-manager-cosmosdb/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Utwórz ABI kontraktu i adres URL kodu bajtowego

Łańcucha bloków Data Manager wymaga dostępności plików ABI i kodu bajtowego kontraktu przez adres URL podczas dodawania aplikacji. Możesz użyć konta usługi Azure Storage, aby podać adres URL dostępny prywatnie.

#### <a name="create-storage-account"></a>Tworzenie konta magazynu

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Przekazywanie plików kontraktu

1. Utwórz nowy kontener dla konta magazynu. Wybierz kontenery **> kontenerze**.

    ![Tworzenie kontenera konta magazynu](./media/data-manager-cosmosdb/create-container.png)

    | Ustawienie | Opis |
    |---------|-------------|
    | Nazwa  | Nazwij kontener. Na przykład *smartcontract* |
    | Poziom dostępu publicznego | Wybierz pozycję *prywatny (brak dostępu anonimowego)* |

1. Wybierz przycisk **OK**, aby utworzyć kontener.
1. Wybierz kontener, a następnie wybierz pozycję **Przekaż**.
1. Wybierz pliki JSON, które zostały utworzone w sekcji [Pobierz kontrakt ABI i kod bajtowy](#get-contract-abi-and-bytecode) .

    ![Przekaż obiekt BLOB](./media/data-manager-cosmosdb/upload-blobs.png)

    Wybierz pozycję **Przekaż**.

#### <a name="generate-url"></a>Generuj adres URL

Dla każdego obiektu BLOB Wygeneruj sygnaturę dostępu współdzielonego.

1. Wybierz obiekt BLOB ABI JSON.
1. Wybierz pozycję **Generuj SAS**
1. Ustaw odpowiednie wygaśnięcie sygnatury dostępu, a następnie wybierz pozycję **Generuj token SAS i adres URL**.

    ![Generuj token SAS](./media/data-manager-cosmosdb/generate-sas.png)

1. Skopiuj **adres URL sygnatury dostępu współdzielonego obiektu BLOB** i Zapisz go w następnej sekcji.
1. Powtórz kroki [Generuj adres URL](#generate-url) dla obiektu BLOB JSON.

### <a name="add-helloblockchain-application-to-instance"></a>Dodaj aplikację helloblockchain do wystąpienia

1. Wybierz wystąpienie Data Manager łańcucha bloków z listy wystąpień.
1. Wybierz pozycję **aplikacje łańcucha bloków**.
1. Wybierz pozycję **Dodaj**.

    ![Dodawanie aplikacji łańcucha bloków](./media/data-manager-cosmosdb/add-application.png)

    Wprowadź nazwę aplikacji łańcucha bloków oraz adresy URL ABI i kodu bajtowego.

    Ustawienie | Opis
    --------|------------
    Nazwa | Wprowadź unikatową nazwę aplikacji łańcucha bloków, która ma być śledzona.
    ABI kontraktu | Ścieżka URL do pliku ABI kontraktu. Aby uzyskać więcej informacji, zobacz temat [Tworzenie kontraktu ABI i adresu URL kodu bajtowego](#create-contract-abi-and-bytecode-url).
    Kod bajtowy kontraktu | Ścieżka adresu URL do pliku kodu bajtowego. Aby uzyskać więcej informacji, zobacz temat [Tworzenie kontraktu ABI i adresu URL kodu bajtowego](#create-contract-abi-and-bytecode-url).

1. Kliknij przycisk **OK**.

    Po utworzeniu aplikacji aplikacja zostanie wyświetlona na liście aplikacji łańcucha bloków.

    ![Lista aplikacji łańcucha bloków](./media/data-manager-cosmosdb/artifact-list.png)

Możesz usunąć konto usługi Azure Storage lub użyć go do skonfigurowania większej liczby aplikacji łańcucha bloków. Jeśli chcesz usunąć konto usługi Azure Storage, możesz usunąć grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego konta magazynu i wszystkich innych zasobów skojarzonych z tą grupą zasobów.

## <a name="create-azure-cosmos-db"></a>Tworzenie bazy danych usługi Azure Cosmos DB

[!INCLUDE [cosmos-db-create-storage-account](../../../includes/cosmos-db-create-dbaccount.md)]

### <a name="add-a-database-and-container"></a>Dodawanie bazy danych i kontenera

Możesz użyć Eksplorator danych w Azure Portal, aby utworzyć bazę danych i kontener.

1. Wybierz pozycję **Eksplorator danych** w lewym obszarze nawigacji na stronie konta Azure Cosmos DB, a następnie wybierz pozycję **nowy kontener**.
1. W okienku **Dodaj kontener** wprowadź ustawienia dla nowego kontenera.

    ![Dodaj ustawienia kontenera](./media/data-manager-cosmosdb/add-container.png)

    | Ustawienie | Opis
    |---------|-------------|
    | Identyfikator bazy danych | Wprowadź **łańcucha bloków-Data** jako nazwę nowej bazy danych. |
    | Przepływność | Pozostaw przepływność na **400** jednostek żądań na sekundę (ru/s). Jeśli chcesz zmniejszyć opóźnienie, możesz później skalować przepływność w górę.|
    | Identyfikator kontenera | Wprowadź **komunikaty** jako nazwę nowego kontenera. |
    | Klucz partycji | Użyj **/MessageType** jako klucza partycji. |

1. Kliknij przycisk **OK**. W Eksplorator danych zostanie wyświetlona nowa baza danych i kontener, który został utworzony.

## <a name="create-logic-app"></a>Tworzenie aplikacji logiki

Azure Logic Apps pomaga zaplanować i zautomatyzować procesy biznesowe i przepływy pracy, gdy trzeba zintegrować systemy i usługi. Za pomocą aplikacji logiki można łączyć Event Grid z Azure Cosmos DB.

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób** > **Integracja** > **Aplikacja logiki**.
1. Podaj szczegółowe informacje o tym, gdzie utworzyć aplikację logiki. Po zakończeniu wybierz pozycję **Utwórz**.

    Aby uzyskać więcej informacji na temat tworzenia aplikacji logiki, zobacz [Tworzenie zautomatyzowanych przepływów pracy za pomocą Azure Logic Apps](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Po wdrożeniu aplikacji przez platformę Azure wybierz zasób aplikacji logiki.
1. W projektancie Logic Apps w obszarze **Szablony**wybierz pozycję **pusta aplikacja logiki**.

### <a name="add-event-grid-trigger"></a>Dodaj wyzwalacz Event Grid

Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest aktywowany w momencie wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Po każdym aktywowaniu wyzwalacza aparat usługi Logic Apps tworzy wystąpienie aplikacji logiki uruchamiającej i wykonującej przepływ pracy. Użyj wyzwalacza Azure Event Grid, aby wysyłać dane transakcji łańcucha bloków z Event Grid do Cosmos DB.

1. W projektancie Logic Apps Wyszukaj i wybierz łącznik **Azure Event Grid** .
1. Na karcie **wyzwalacze** wybierz opcję **gdy występuje zdarzenie zasobu**.
1. Utwórz połączenie interfejsu API z tematem Event Grid.

    ![Ustawienia wyzwalacza siatki zdarzeń](./media/data-manager-cosmosdb/event-grid-trigger.png)

    | Ustawienie | Opis
    |---------|-------------|
    | Subskrypcja | Wybierz subskrypcję zawierającą Event Grid temat. |
    | Typ zasobu | Wybierz pozycję **Microsoft. EventGrid. temats**. |
    | Nazwa zasobu | Wybierz nazwę tematu Event Grid, w którym łańcucha bloków Data Manager wysyła wiadomości danych transakcji. |

### <a name="add-cosmos-db-action"></a>Dodaj akcję Cosmos DB

Dodaj akcję w celu utworzenia dokumentu w Cosmos DB dla każdej transakcji. Użyj typu komunikatu Transaction jako klucza partycji do kategoryzacji komunikatów.

1. Wybierz pozycję **Nowy krok**.
1. W obszarze **Wybierz akcję**Wyszukaj **Azure Cosmos DB**.
1. Wybierz **Azure Cosmos DB akcje > > Utwórz lub zaktualizuj dokument**.
1. Utwórz połączenie interfejsu API z bazą danych Cosmos DB.

    ![Ustawienia połączenia Cosmos DB](./media/data-manager-cosmosdb/cosmosdb-connection.png)

    | Ustawienie | Opis
    |---------|-------------|
    | Nazwa połączenia | Wybierz subskrypcję zawierającą Event Grid temat. |
    | Konto DocumentDB | Wybierz konto DocumentDB utworzone w sekcji [Tworzenie konta Azure Cosmos DB](#create-azure-cosmos-db) . |

1. Wprowadź **Identyfikator bazy danych** i **identyfikator kolekcji** dla Azure Cosmos DB utworzonego wcześniej w sekcji [Dodawanie bazy danych i kontenera](#add-a-database-and-container) .

1. Wybierz ustawienie **dokumentu** . W okienku *Dodawanie zawartości dynamicznej* wybierz pozycję **wyrażenie** i skopiuj i wklej następujące wyrażenie:

    ```
    addProperty(triggerBody()?['data'], 'id', utcNow())
    ```

    Wyrażenie Pobiera część danych komunikatu i ustawia identyfikator na wartość sygnatury czasowej.

1. Wybierz pozycję **Dodaj nowy parametr** i wybierz pozycję **wartość klucza partycji**.
1. Ustaw **wartość klucza partycji** `"@{triggerBody()['data']['MessageType']}"` . Wartość musi być ujęta w podwójne cudzysłowy.

    ![Projektant Logic Apps z ustawieniami Cosmos DB](./media/data-manager-cosmosdb/create-action.png)

    Wartość ustawia klucz partycji typu komunikat transakcji.

1. Wybierz pozycję **Zapisz**.

Aplikacja logiki monitoruje temat Event Grid. Gdy nowa transakcja jest wysyłana z łańcucha bloków Data Manager, aplikacja logiki tworzy dokument w Cosmos DB.

## <a name="send-a-transaction"></a>Wyślij transakcję

Następnie Wyślij transakcję do księgi łańcucha bloków, aby przetestować utworzone elementy. Użyj funkcji **SendRequest** kontraktu **HelloBlockchain** , która została utworzona w samouczku wymagań wstępnych [: Użyj Visual Studio Code, aby tworzyć, kompilować i wdrażać kontrakty inteligentne](send-transaction.md).

1. Na stronie interakcji z kontraktem inteligentnym usługi Azure łańcucha bloków Development Kit można używać funkcji **SendRequest** . Kliknij prawym przyciskiem myszy **HelloBlockchain. peruwiański** i wybierz polecenie **Pokaż stronę interakcji z kontraktem inteligentnym** z menu.

    ![Wybierz pozycję Pokaż stronę interakcji z kontraktem inteligentnym z menu](./media/data-manager-cosmosdb/contract-interaction.png)

1. Wybierz akcję kontraktu **SendRequest** i wprowadź **Hello, łańcucha bloków!** dla parametru **RequestMessage** . Wybierz polecenie **Wykonaj** , aby wywołać funkcję **SendRequest** za pośrednictwem transakcji.

    ![Wykonaj akcję SendRequest](./media/data-manager-cosmosdb/sendrequest-action.png)

Funkcja SendRequest ustawia pola **RequestMessage** i **State** . Bieżący stan dla **RequestMessage** jest argumentem, który przeszedł do **Hello, łańcucha bloków**. Wartość pola **stan** jest **niepożądana**.

## <a name="view-transaction-data"></a>Wyświetl dane transakcji

Po powiązaniu Data Manager z usługą łańcucha bloków w celu Azure Cosmos DB można wyświetlić komunikaty łańcucha bloków Transaction w Cosmos DB Eksplorator danych.

1. Przejdź do widoku Cosmos DB Eksplorator danych. Na przykład **cosmosdb-łańcucha bloków > Eksplorator danych > łańcucha bloków-Data > messages > Items**.

    ![Cosmos DB Eksplorator danych](./media/data-manager-cosmosdb/data-explorer.png)

    Eksplorator danych wyświetla listę łańcucha blokówch komunikatów danych, które zostały utworzone w Cosmos DB bazie danych.

1. Przejrzyj komunikaty, wybierając pozycję Identyfikator elementu i Znajdź komunikat ze skrótem pasującej transakcji.

    [![Zrzut ekranu przedstawia szczegóły transakcji łańcucha bloków wybranego elementu.](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

    Komunikat nieprzetworzonych transakcji zawiera szczegółowe informacje o transakcji. Jednak informacje o właściwościach są szyfrowane.

    Ze względu na to, że dodano kontrakt HelloBlockchain Smart do wystąpienia Data Manager łańcucha bloków, zostanie również wysłany typ komunikatu **ContractProperties** , który zawiera dekodowane informacje o właściwościach.

1. Znajdź komunikat **ContractProperties** dla transakcji. Powinien być następnym komunikatem na liście.

    [![Szczegóły transakcji łańcucha bloków](./media/data-manager-cosmosdb/properties-msg.png)](./media/data-manager-cosmosdb/properties-msg.png#lightbox)

    Tablica **DecodedProperties** zawiera właściwości transakcji.

Gratulacje! Eksplorator komunikatów transakcji został pomyślnie utworzony za pomocą łańcucha bloków Data Manager i Azure Cosmos DB.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, możesz usunąć zasoby i grupy zasobów użyte w tym samouczku. Aby usunąć grupę zasobów:

1. W Azure Portal przejdź do **grupy zasobów** w okienku nawigacji po lewej stronie i wybierz grupę zasobów, którą chcesz usunąć.
1. Wybierz pozycję **Usuń grupę zasobów**. Sprawdź usuwanie, wprowadzając nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o integrowaniu z księgami łańcucha bloków.

> [!div class="nextstepaction"]
> [Używanie łącznika łańcucha bloków Ethereum z Azure Logic Apps](ethereum-logic-app.md)
