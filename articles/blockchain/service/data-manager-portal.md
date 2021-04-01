---
title: Konfigurowanie Data Manager łańcucha bloków przy użyciu usługi Azure Portal — Azure łańcucha bloków Service
description: Twórz Data Manager łańcucha bloków dla usługi Azure łańcucha bloków i zarządzaj nimi przy użyciu Azure Portal.
ms.date: 03/30/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: a151c28d31bf0bb7f21185fb161315d42f9563d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85200685"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Konfigurowanie menedżera danych łańcucha bloków przy użyciu witryny Azure Portal

Skonfiguruj Data Manager łańcucha bloków dla usługi Azure łańcucha bloków, aby przechwytywać dane łańcucha bloków i wysyłać je do tematu Azure Event Grid.

Aby skonfigurować wystąpienie Data Manager łańcucha bloków:

* Utwórz wystąpienie Data Manager łańcucha bloków dla węzła transakcji usługi Azure łańcucha bloków Service
* Dodawanie aplikacji łańcucha bloków

## <a name="prerequisites"></a>Wymagania wstępne

* Pełny [Przewodnik Szybki Start: Tworzenie elementu członkowskiego łańcucha bloków przy użyciu Azure Portal](create-member.md) lub [szybkiego startu: Tworzenie elementu członkowskiego łańcucha bloków usługi Azure łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure](create-member-cli.md). W przypadku korzystania z usługi łańcucha bloków Data Manager zaleca się użycie warstwy *Standard* usług Azure łańcucha bloków.
* Tworzenie [tematu Event Grid](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Informacje o [obsłudze zdarzeń w Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="create-instance"></a>Utwórz wystąpienie

Wystąpienie Data Manager łańcucha bloków nawiązuje połączenie i monitoruje węzeł transakcji usługi Azure łańcucha bloków. Połączenie może utworzyć tylko użytkownicy z dostępem do węzła transakcji. Wystąpienie przechwytuje wszystkie nieprzetworzone i nieprzetworzone dane transakcji z węzła transakcji. Łańcucha bloków Data Manager publikuje komunikat **RawBlockAndTransactionMsg** , który jest nadzbiorem informacji zwracanych z web3. ETH [getblock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) i [gettransaction](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction) zapytania.

Połączenie wychodzące wysyła dane łańcucha bloków do Azure Event Grid. Podczas tworzenia wystąpienia można skonfigurować pojedyncze połączenie wychodzące. Łańcucha bloków Data Manager obsługuje wiele połączeń wychodzących tematu Event Grid dla danego wystąpienia Data Manager łańcucha bloków. Dane łańcucha bloków można wysyłać do jednego miejsca docelowego lub wysyłać dane łańcucha bloków do wielu miejsc docelowych. Aby dodać kolejne miejsce docelowe, po prostu Dodaj dodatkowe połączenia wychodzące do wystąpienia.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do elementu członkowskiego usługi Azure łańcucha bloków, który chcesz połączyć z łańcucha bloków Data Manager. Wybierz pozycję **łańcucha bloków Data Manager**.
1. Wybierz pozycję **Dodaj**.

    ![Dodaj Data Manager łańcucha bloków](./media/data-manager-portal/add-instance.png)

    Wprowadź następujące informacje:

    Ustawienie | Opis
    --------|------------
    Nazwa | Wprowadź unikatową nazwę połączonego Data Manager łańcucha bloków. Nazwa Data Manager łańcucha bloków może zawierać małe litery i cyfry, a maksymalna długość wynosi 20 znaków.
    Węzeł transakcji | Wybierz węzeł transakcji. Wyświetlane są tylko węzły transakcji, do których masz dostęp do odczytu.
    Nazwa połączenia | Wprowadź unikatową nazwę połączenia wychodzącego, w którym są wysyłane dane transakcji łańcucha bloków.
    Punkt końcowy siatki zdarzeń | Wybierz temat usługi Event Grid w tej samej subskrypcji co wystąpienie Data Manager łańcucha bloków.

1. Wybierz przycisk **OK**.

    Utworzenie wystąpienia Data Manager łańcucha bloków może zająć mniej niż minutę. Po wdrożeniu wystąpienia zostanie ono automatycznie uruchomione. Uruchomione wystąpienie Data Manager łańcucha bloków przechwytuje zdarzenia łańcucha bloków z węzła Transaction i wysyła dane do połączeń wychodzących.

    Nowe wystąpienie zostanie wyświetlone na liście wystąpień łańcucha bloków Data Manager dla elementu członkowskiego usługi Azure łańcucha bloków.

    ![Lista wystąpień elementu członkowskiego danych łańcucha bloków](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Dodawanie aplikacji łańcucha bloków

Jeśli dodasz aplikację łańcucha bloków, łańcucha bloków Data Manager dekoduje zdarzenie i stan właściwości aplikacji. W przeciwnym razie wysyłane są tylko nieprzetworzone dane transakcji blokowych i nieprzetworzonych. Łańcucha bloków Data Manager również wykrywa adresy kontraktu po wdrożeniu kontraktu. Można dodać wiele aplikacji łańcucha bloków do wystąpienia Data Manager łańcucha bloków.

> [!IMPORTANT]
> Obecnie aplikacje łańcucha bloków, które deklarują [Typy tablic](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) stałych lub [typy mapowania](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) , nie są w pełni obsługiwane. Właściwości zadeklarowane jako tablica lub typy mapowania nie będą zdekodowane w wiadomościach *ContractPropertiesMsg* lub *DecodedContractEventsMsg* .

Aby można było dodać aplikację, Data Manager łańcucha bloków wymaga inteligentnego ABI kontraktu i wdrożonego pliku kodu.

### <a name="get-contract-abi-and-bytecode"></a>Pobierz ABI kontraktu i kod bajtowy

ABI kontraktu definiuje inteligentne interfejsy kontraktu. Opisuje sposób korzystania z kontraktu inteligentnego. Aby skopiować ABI kontraktu do schowka, możesz użyć [rozszerzenia Azure łańcucha bloków Development Kit for Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) .

1. W okienku Eksploratora Visual Studio Code rozwiń folder **kompilacja/kontrakty** w projekcie o trwałym stanie.
1. Kliknij prawym przyciskiem myszy plik JSON metadanych kontraktu. Nazwa pliku jest nazwą kontraktu inteligentnego, po której następuje rozszerzenie **. JSON** .
1. Wybierz pozycję **Kopiuj kontrakt ABI**.

    ![Visual Studio Code okienku z opcją Kopiuj kontrakt ABI](./media/data-manager-portal/abi-devkit.png)

    ABI kontraktu jest kopiowany do Schowka.

1. Zapisz tablicę **ABI** jako plik JSON. Na przykład *abi.js*. Ten plik jest używany w późniejszym kroku.

Łańcucha bloków Data Manager wymaga wdrożonego kodu bajtowego dla kontraktu inteligentnego. Wdrożony kod bajtowy jest inny niż kod bajtowy kontraktu inteligentnego. Użyj rozszerzenia Azure łańcucha bloków Development Kit, aby skopiować kod bajtowy do Schowka.

1. W okienku Eksploratora Visual Studio Code rozwiń folder **kompilacja/kontrakty** w projekcie o trwałym stanie.
1. Kliknij prawym przyciskiem myszy plik JSON metadanych kontraktu. Nazwa pliku jest nazwą kontraktu inteligentnego, po której następuje rozszerzenie **. JSON** .
1. Wybierz pozycję **Kopiuj transakcję kod bajtowy**.

    ![Visual Studio Code okienku z opcją kopiowania kodu bajtowego transakcji](./media/data-manager-portal/bytecode-devkit.png)

    Kod bajtowy jest kopiowany do Schowka.

1. Zapisz wartość **kodu bajtowego** jako plik JSON. Na przykład *bytecode.js*. Ten plik jest używany w późniejszym kroku.

Poniższy przykład pokazuje *abi.json* i *bytecode.jsna* plikach otwartych w edytorze vs Code. Pliki powinny wyglądać podobnie.

![Przykład abi.jsi bytecode.jsdla plików](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Utwórz ABI kontraktu i adres URL kodu bajtowego

Łańcucha bloków Data Manager wymaga dostępności plików ABI i kodu bajtowego kontraktu przez adres URL podczas dodawania aplikacji. Możesz użyć konta usługi Azure Storage, aby podać adres URL dostępny prywatnie.

#### <a name="create-storage-account"></a>Tworzenie konta magazynu

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Przekazywanie plików kontraktu

1. Utwórz nowy kontener dla konta magazynu. Wybierz kontenery **> kontenerze**.

    ![Tworzenie kontenera konta magazynu](./media/data-manager-portal/create-container.png)

    | Pole | Opis |
    |-------|-------------|
    | Nazwa  | Nazwij kontener. Na przykład *smartcontract* |
    | Poziom dostępu publicznego | Wybierz pozycję *prywatny (brak dostępu anonimowego)* |

1. Wybierz przycisk **OK**, aby utworzyć kontener.
1. Wybierz kontener, a następnie wybierz pozycję **Przekaż**.
1. Wybierz pliki JSON, które zostały utworzone w sekcji [Pobierz kontrakt ABI i kod bajtowy](#get-contract-abi-and-bytecode) .

    ![Przekaż obiekt BLOB](./media/data-manager-portal/upload-blobs.png)

    Wybierz pozycję **Przekaż**.

#### <a name="generate-url"></a>Generuj adres URL

Dla każdego obiektu BLOB Wygeneruj sygnaturę dostępu współdzielonego.

1. Wybierz obiekt BLOB ABI JSON.
1. Wybierz pozycję **Generuj SAS**
1. Ustaw odpowiednie wygaśnięcie sygnatury dostępu, a następnie wybierz pozycję **Generuj token SAS i adres URL**.

    ![Generuj token SAS](./media/data-manager-portal/generate-sas.png)

1. Skopiuj **adres URL sygnatury dostępu współdzielonego obiektu BLOB** i Zapisz go w następnej sekcji.
1. Powtórz kroki [Generuj adres URL](#generate-url) dla obiektu BLOB JSON.

### <a name="add-application-to-instance"></a>Dodaj aplikację do wystąpienia

1. Wybierz wystąpienie Data Manager łańcucha bloków z listy wystąpień.
1. Wybierz pozycję **aplikacje łańcucha bloków**.
1. Wybierz pozycję **Dodaj**.

    ![Dodawanie aplikacji łańcucha bloków](./media/data-manager-portal/add-application.png)

    Wprowadź nazwę aplikacji łańcucha bloków oraz adresy URL ABI i kodu bajtowego.

    Ustawienie | Opis
    --------|------------
    Nazwa | Wprowadź unikatową nazwę aplikacji łańcucha bloków, która ma być śledzona.
    ABI kontraktu | Ścieżka URL do pliku ABI kontraktu. Aby uzyskać więcej informacji, zobacz temat [Tworzenie kontraktu ABI i adresu URL kodu bajtowego](#create-contract-abi-and-bytecode-url).
    Kod bajtowy kontraktu | Ścieżka adresu URL do pliku kodu bajtowego. Aby uzyskać więcej informacji, zobacz temat [Tworzenie kontraktu ABI i adresu URL kodu bajtowego](#create-contract-abi-and-bytecode-url).

1. Wybierz przycisk **OK**.

    Po utworzeniu aplikacji aplikacja zostanie wyświetlona na liście aplikacji łańcucha bloków.

    ![Lista aplikacji łańcucha bloków](./media/data-manager-portal/artifact-list.png)

Możesz usunąć konto usługi Azure Storage lub użyć go do skonfigurowania większej liczby aplikacji łańcucha bloków. Jeśli chcesz usunąć konto usługi Azure Storage, możesz usunąć grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego konta magazynu i wszystkich innych zasobów skojarzonych z tą grupą zasobów.

## <a name="stop-instance"></a>Zatrzymaj wystąpienie

Zatrzymaj wystąpienie Menedżera łańcucha bloków, jeśli chcesz zatrzymać przechwytywanie zdarzeń łańcucha bloków i wysyłanie danych do połączeń wychodzących. Po zatrzymaniu wystąpienia nie są naliczane opłaty za łańcucha bloków Data Manager. Aby uzyskać więcej informacji, zobacz [cennik](https://azure.microsoft.com/pricing/details/blockchain-service).

1. Przejdź do **omówienia** i wybierz pozycję **Zatrzymaj**.

    ![Zatrzymaj wystąpienie](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>Następne kroki

Wypróbuj następny samouczek tworzenia Eksploratora komunikatów transakcji łańcucha bloków za pomocą łańcucha bloków Data Manager i Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Wysyłanie danych do usługi Azure Cosmos DB za pomocą menedżera danych łańcucha bloków](data-manager-cosmosdb.md)