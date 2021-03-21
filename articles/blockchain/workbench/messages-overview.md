---
title: Używanie komunikatów do integrowania z usługą Azure łańcucha bloków Workbench
description: Omówienie korzystania z komunikatów w celu zintegrowania usługi Azure łańcucha bloków Workbench w wersji zapoznawczej z innymi systemami.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 14bd0f84bc9490d95d3dbe0b9f122882f0d2059d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74324506"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Integracja z usługą Azure łańcucha bloków Workbench Messaging

Oprócz udostępniania interfejsu API REST usługa Azure łańcucha bloków Workbench zapewnia także integrację opartą na komunikacji. Workbench publikuje zdarzenia zorientowane na finanse za pośrednictwem Azure Event Grid, co umożliwia konsumentom podrzędnym pozyskiwanie danych lub podejmowanie działań na podstawie tych zdarzeń. Dla tych klientów, którzy wymagają niezawodnej obsługi komunikatów, usługa Azure łańcucha bloków Workbench również dostarcza komunikaty do punktu końcowego Azure Service Bus.

## <a name="input-apis"></a>Wejściowe interfejsy API

Jeśli chcesz zainicjować transakcje z systemów zewnętrznych w celu tworzenia użytkowników, tworzenia kontraktów i aktualizowania kontraktów, możesz użyć wejściowych interfejsów API komunikatów do wykonywania transakcji w księdze. Zobacz [przykłady integracji z obsługą wiadomości](https://aka.ms/blockchain-workbench-integration-sample) , aby uzyskać przykład demonstrujący wejściowe interfejsy API.

Poniżej znajdują się obecnie dostępne wejściowe interfejsy API.

### <a name="create-user"></a>Tworzenie użytkownika

Tworzy nowego użytkownika.

Żądanie wymaga następujących pól:

| **Nazwa**             | **Opis**                                      |
|----------------------|------------------------------------------------------|
| IdentyfikatorŻądania            | Identyfikator GUID dostarczony przez klienta                                |
| firstName            | Imię użytkownika                              |
| lastName             | Nazwisko użytkownika                               |
| emailAddress         | Adres e-mail użytkownika                           |
| externalId           | Identyfikator obiektu użytkownika usługi Azure AD                      |
| connectionId         | Unikatowy identyfikator połączenia usługi łańcucha bloków |
| messageSchemaVersion | Wersja schematu obsługi komunikatów                            |
| komunikatname          | **CreateUserRequest**                               |

Przykład:

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Łańcucha bloków Workbench zwraca odpowiedź z następującymi polami:

| **Nazwa**              | **Opis**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| IdentyfikatorŻądania             | Identyfikator GUID dostarczony przez klienta |
| userId                | Identyfikator użytkownika, który został utworzony |
| userChainIdentifier   | Adres użytkownika, który został utworzony w sieci łańcucha bloków. W Ethereum adres jest adresem **w łańcuchu** . |
| connectionId          | Unikatowy identyfikator połączenia usługi łańcucha bloków|
| messageSchemaVersion  | Wersja schematu obsługi komunikatów |
| komunikatname           | **CreateUserUpdate** |
| status                | Stan żądania utworzenia użytkownika.  Jeśli to się powiedzie, wartość jest **sukces**. W przypadku niepowodzenia wartość jest **Niepowodzenie**.     |
| additionalInformation | Dodatkowe informacje na podstawie stanu |

Przykład pomyślnego **utworzenia odpowiedzi użytkownika** z łańcucha bloków Workbench:

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

Jeśli żądanie nie powiodło się, szczegóły dotyczące błędu znajdują się w dodatkowych informacjach.

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>Tworzenie kontraktu

Tworzy nowy kontrakt.

Żądanie wymaga następujących pól:

| **Nazwa**             | **Opis**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| IdentyfikatorŻądania            | Identyfikator GUID dostarczony przez klienta |
| userChainIdentifier  | Adres użytkownika, który został utworzony w sieci łańcucha bloków. W Ethereum ten adres jest adresem **łańcucha** użytkownika. |
| applicationName      | Nazwa aplikacji |
| Wersja              | Wersja aplikacji. Wymagane, jeśli masz włączoną wiele wersji aplikacji. W przeciwnym razie wersja jest opcjonalna. Aby uzyskać więcej informacji na temat przechowywania wersji aplikacji, zobacz [przechowywanie wersji aplikacji Azure łańcucha bloków Workbench](version-app.md). |
| workflowName         | Nazwa przepływu pracy |
| parameters           | Parametry wejściowe dla tworzenia kontraktu |
| connectionId         | Unikatowy identyfikator połączenia usługi łańcucha bloków |
| messageSchemaVersion | Wersja schematu obsługi komunikatów |
| komunikatname          | **CreateContractRequest** |

Przykład:

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer",
    "version": "1.0",
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Łańcucha bloków Workbench zwraca odpowiedź z następującymi polami:

| **Nazwa**                 | **Opis**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| IdentyfikatorŻądania                | Identyfikator GUID dostarczony przez klienta                                                             |
| contractId               | Unikatowy identyfikator kontraktu w usłudze Azure łańcucha bloków Workbench |
| contractLedgerIdentifier | Adres kontraktu w księdze                                            |
| connectionId             | Unikatowy identyfikator połączenia usługi łańcucha bloków                               |
| messageSchemaVersion     | Wersja schematu obsługi komunikatów                                                         |
| komunikatname              | **CreateContractUpdate**                                                      |
| status                   | Stan żądania utworzenia kontraktu.  Możliwe wartości: **przesłane**, **zatwierdzone**, **Niepowodzenie**.  |
| additionalInformation    | Dodatkowe informacje na podstawie stanu                              |

Przykład przesłanej odpowiedzi **dotyczącej tworzenia kontraktu** z łańcucha bloków Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Przykład zatwierdzonej odpowiedzi **dotyczącej tworzenia kontraktu** z łańcucha bloków Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Jeśli żądanie nie powiodło się, szczegóły dotyczące błędu znajdują się w dodatkowych informacjach.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>Akcja tworzenia kontraktu

Tworzy nową akcję kontraktu.

Żądanie wymaga następujących pól:

| **Nazwa**                 | **Opis**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| IdentyfikatorŻądania                | Identyfikator GUID dostarczony przez klienta |
| userChainIdentifier      | Adres użytkownika, który został utworzony w sieci łańcucha bloków. W Ethereum ten adres jest adresem **łańcucha** użytkownika. |
| contractLedgerIdentifier | Adres kontraktu w księdze |
| Wersja                  | Wersja aplikacji. Wymagane, jeśli masz włączoną wiele wersji aplikacji. W przeciwnym razie wersja jest opcjonalna. Aby uzyskać więcej informacji na temat przechowywania wersji aplikacji, zobacz [przechowywanie wersji aplikacji Azure łańcucha bloków Workbench](version-app.md). |
| workflowFunctionName     | Nazwa funkcji przepływu pracy |
| parameters               | Parametry wejściowe dla tworzenia kontraktu |
| connectionId             | Unikatowy identyfikator połączenia usługi łańcucha bloków |
| messageSchemaVersion     | Wersja schematu obsługi komunikatów |
| komunikatname              | **CreateContractActionRequest** |

Przykład:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "version": "1.0",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Łańcucha bloków Workbench zwraca odpowiedź z następującymi polami:

| **Nazwa**              | **Opis**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| IdentyfikatorŻądania             | Identyfikator GUID dostarczony przez klienta|
| contractId            | Unikatowy identyfikator kontraktu w usłudze Azure łańcucha bloków Workbench |
| connectionId          | Unikatowy identyfikator połączenia usługi łańcucha bloków |
| messageSchemaVersion  | Wersja schematu obsługi komunikatów |
| komunikatname           | **CreateContractActionUpdate** |
| status                | Stan żądania akcji kontraktu. Możliwe wartości: **przesłane**, **zatwierdzone**, **Niepowodzenie**.                         |
| additionalInformation | Dodatkowe informacje na podstawie stanu |

Przykład przesłanej odpowiedzi **akcji tworzenia kontraktu** z łańcucha bloków Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Przykład zatwierdzoną **akcję tworzenia kontraktu** z łańcucha bloków Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Jeśli żądanie nie powiodło się, szczegóły dotyczące błędu znajdują się w dodatkowych informacjach.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>Kody błędów i komunikaty wejściowych interfejsów API

**Kod błędu 4000: zły błąd żądania**
- Nieprawidłowy connectionId
- Deserializacja CreateUserRequest nie powiodła się
- Deserializacja CreateContractRequest nie powiodła się
- Deserializacja CreateContractActionRequest nie powiodła się
- Aplikacja {zidentyfikowana przez nazwę aplikacji} nie istnieje
- Aplikacja {identyfikowana przez nazwę aplikacji} nie ma przepływu pracy
- UserChainIdentifier nie istnieje
- Kontrakt {zidentyfikowany przez identyfikator księgi} nie istnieje
- Kontrakt {zidentyfikowany przez identyfikator księgi} nie ma funkcji {nazwa funkcji przepływu pracy}
- UserChainIdentifier nie istnieje

**Kod błędu 4090: błąd konfliktu**
- Użytkownik już istnieje
- Kontrakt już istnieje
- Akcja kontraktu już istnieje

**Kod błędu 5000: wewnętrzny błąd serwera**
- Komunikaty o wyjątkach

## <a name="event-notifications"></a>Powiadomienia o zdarzeniach

Powiadomienia o zdarzeniach mogą służyć do powiadamiania użytkowników i systemów podrzędnych zdarzeń, które wystąpiły w łańcucha bloków Workbench i sieci łańcucha bloków, z którą jest połączona. Powiadomienia o zdarzeniach mogą być używane bezpośrednio w kodzie lub używane z narzędziami, takimi jak Logic Apps i Flow, aby wyzwolić przepływ danych do systemów podrzędnych.

Aby uzyskać szczegółowe informacje o różnych komunikatach, które można odbierać, zobacz informacje dotyczące [komunikatów powiadomień](#notification-message-reference) .

### <a name="consuming-event-grid-events-with-azure-functions"></a>Zużywanie zdarzeń Event Grid z Azure Functions

Jeśli użytkownik chce używać Event Grid do powiadamiania o zdarzeniach, które zachodzą w łańcucha bloków Workbench, można używać zdarzeń z Event Grid przy użyciu Azure Functions.

1. Utwórz **aplikacja funkcji platformy Azure** w Azure Portal.
2. Utwórz nową funkcję.
3. Znajdź szablon Event Grid. Wyświetlany jest podstawowy kod szablonu służący do odczytywania wiadomości. Zmodyfikuj kod w razie konieczności.
4. Zapisz funkcję. 
5. Wybierz Event Grid z grupy zasobów łańcucha bloków Workbench.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Zużywanie zdarzeń Event Grid z Logic Apps

1. Utwórz nową **aplikację logiki platformy Azure** w Azure Portal.
2. Podczas otwierania aplikacji logiki platformy Azure w portalu zostanie wyświetlony monit o wybranie wyzwalacza. Wybierz **Azure Event Grid — w przypadku wystąpienia zdarzenia zasobu**.
3. Po wyświetleniu projektanta przepływu pracy zostanie wyświetlony monit o zalogowanie się.
4. Wybierz subskrypcję. Zasób jako **Microsoft. EventGrid. temats**. Wybierz **nazwę zasobu** z nazwy zasobu z grupy zasobów usługi Azure łańcucha bloków Workbench.
5. Wybierz Event Grid z grupy zasobów łańcucha bloków Workbench.

## <a name="using-service-bus-topics-for-notifications"></a>Korzystanie z Service Bus tematów na potrzeby powiadomień

Tematy Service Bus mogą służyć do powiadamiania użytkowników o zdarzeniach, które zachodzą w łańcucha bloków Workbench. 

1. Przejdź do Service Bus w grupie zasobów Workbench.
2. Wybierz kartę **Tematy**.
3. Wybierz pozycję **ruch wychodzący — temat**.
4. Utwórz nową subskrypcję w tym temacie. Uzyskaj dla niego klucz.
5. Utwórz program, który subskrybuje zdarzenia z tej subskrypcji.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Zużywanie Service Bus komunikatów z Logic Apps

1. Utwórz nową **aplikację logiki platformy Azure** w Azure Portal.
2. Podczas otwierania aplikacji logiki platformy Azure w portalu zostanie wyświetlony monit o wybranie wyzwalacza. Wpisz **Service Bus** w polu wyszukiwania i wybierz wyzwalacz odpowiedni dla typu interakcji, która ma być dostępna z Service Bus. Na przykład **Service Bus — po odebraniu komunikatu w subskrypcji tematu (Autouzupełnianie)**.
3. Gdy zostanie wyświetlony Projektant przepływu pracy, Określ informacje o połączeniu dla Service Bus.
4. Wybierz swoją subskrypcję i określ temat **Workbench-External**.
5. Utwórz logikę dla aplikacji, która wykorzystuje komunikat z tego wyzwalacza.

## <a name="notification-message-reference"></a>Dokumentacja komunikatów powiadomień

W zależności od **komunikatu**, komunikaty powiadomień mają jeden z następujących typów komunikatów.

### <a name="block-message"></a>Blokuj komunikat

Zawiera informacje o poszczególnych blokach. *BlockMessage* zawiera sekcję z informacjami o poziomie bloku i sekcją z informacjami o transakcji.

| Nazwa | Opis |
|------|-------------|
| blokowanie | Zawiera [Informacje o blokowaniu](#block-information) |
| transakcje | Zawiera [Informacje o transakcji](#transaction-information) kolekcji dla bloku |
| connectionId | Unikatowy identyfikator połączenia |
| messageSchemaVersion | Wersja schematu obsługi komunikatów |
| komunikatname | **BlockMessage** |
| additionalInformation | Podano dodatkowe informacje |

#### <a name="block-information"></a>Informacje o blokowaniu

| Nazwa              | Opis |
|-------------------|-------------|
| blockId           | Unikatowy identyfikator bloku w usłudze Azure łańcucha bloków Workbench |
| blockNumber       | Unikatowy identyfikator dla bloku w księdze |
| blockHash         | Skrót bloku |
| previousBlockHash | Skrót poprzedniego bloku |
| blockTimestamp    | Sygnatura czasowa bloku |

#### <a name="transaction-information"></a>Informacje o transakcji

| Nazwa               | Opis |
|--------------------|-------------|
| transactionId      | Unikatowy identyfikator transakcji wewnątrz usługi Azure łańcucha bloków Workbench |
| transactionHash    | Skrót transakcji w księdze |
| wniosek               | Unikatowy identyfikator w księdze dla źródła transakcji |
| na wartość                 | Unikatowy identyfikator w księdze dla miejsca docelowego transakcji |
| provisioningStatus | Określa bieżący stan procesu aprowizacji transakcji. Możliwe wartości: </br>0 — transakcja została utworzona przez interfejs API w bazie danych</br>1 — transakcja została wysłana do księgi</br>2 — transakcja została pomyślnie przekazana do księgi</br>3 lub 4 — nie można zatwierdzić transakcji do księgi</br>5 — transakcja została pomyślnie przekazana do księgi |

Przykład *BlockMessage* z łańcucha bloków Workbench:

``` json
{
    "block": {
        "blockId": 123,
        "blockNumber": 1738312,
        "blockHash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
        "previousBlockHash": null,
        "blockTimestamp": "2018-10-09T23:35:58Z",
    },
    "transactions": [
        {
            "transactionId": 234,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": null,
            "provisioningStatus": 1
        },
        {
            "transactionId": 235,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xadd97e1e595916e29ea94fda894941574000ffff",
            "to": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff",
            "provisioningStatus": 2
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "BlockMessage",
    "additionalInformation": {}
}
```

### <a name="contract-message"></a>Komunikat dotyczący kontraktu

Zawiera informacje o kontrakcie. Komunikat zawiera sekcję z właściwościami kontraktu i sekcją z informacjami o transakcji. Wszystkie transakcje, które zmodyfikowano kontrakt dla danego bloku, znajdują się w sekcji transakcji.

| Nazwa | Opis |
|------|-------------|
| blockId | Unikatowy identyfikator bloku w usłudze Azure łańcucha bloków Workbench |
| blockHash | Skrót bloku |
| modifyingTransactions | [Transakcje, które modyfikują](#modifying-transaction-information) kontrakt |
| contractId | Unikatowy identyfikator kontraktu w usłudze Azure łańcucha bloków Workbench |
| contractLedgerIdentifier | Unikatowy identyfikator kontraktu w księdze |
| contractProperties | [Właściwości kontraktu](#contract-properties) |
| isNewContract | Wskazuje, czy ten kontrakt został nowo utworzony. Możliwe wartości to: true: ten kontrakt został utworzony przez nowy kontrakt. FAŁSZ: ten kontrakt jest aktualizacją kontraktu. |
| connectionId | Unikatowy identyfikator połączenia |
| messageSchemaVersion | Wersja schematu obsługi komunikatów |
| komunikatname | **ContractMessage** |
| additionalInformation | Podano dodatkowe informacje |

#### <a name="modifying-transaction-information"></a>Modyfikowanie informacji o transakcji

| Nazwa               | Opis |
|--------------------|-------------|
| transactionId | Unikatowy identyfikator transakcji wewnątrz usługi Azure łańcucha bloków Workbench |
| transactionHash | Skrót transakcji w księdze |
| wniosek | Unikatowy identyfikator w księdze dla źródła transakcji |
| na wartość | Unikatowy identyfikator w księdze dla miejsca docelowego transakcji |

#### <a name="contract-properties"></a>Właściwości kontraktu

| Nazwa               | Opis |
|--------------------|-------------|
| workflowPropertyId | Unikatowy identyfikator właściwości przepływu pracy w usłudze Azure łańcucha bloków Workbench |
| name | Nazwa właściwości przepływu pracy |
| wartość | Wartość właściwości przepływu pracy |

Przykład *ContractMessage* z łańcucha bloków Workbench:

``` json
{
    "blockId": 123,
    "blockhash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
    "modifyingTransactions": [
        {
            "transactionId": 234,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07ffff"
        },
        {
            "transactionId": 235,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
        }
    ],
    "contractId": 111,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "contractProperties": [
        {
            "workflowPropertyId": 1,
            "name": "State",
            "value": "0"
        },
        {
            "workflowPropertyId": 2,
            "name": "Description",
            "value": "1969 Dodge Charger"
        },
        {
            "workflowPropertyId": 3,
            "name": "AskingPrice",
            "value": "30000"
        },
        {
            "workflowPropertyId": 4,
            "name": "OfferPrice",
            "value": "0"
        },
        {
            "workflowPropertyId": 5,
            "name": "InstanceAppraiser",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 6,
            "name": "InstanceBuyer",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 7,
            "name": "InstanceInspector",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 8,
            "name": "InstanceOwner",
            "value": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
        },
        {
            "workflowPropertyId": 9,
            "name": "ClosingDayOptions",
            "value": "[21,48,69]"
        }
    ],
    "isNewContract": false,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "ContractMessage",
    "additionalInformation": {}
}
```

### <a name="event-message-contract-function-invocation"></a>Komunikat zdarzenia: wywołanie funkcji kontraktu

Zawiera informacje, gdy wywoływana jest funkcja kontraktu, taka jak nazwa funkcji, dane wejściowe parametrów i obiekt wywołujący funkcji.

| Nazwa | Opis |
|------|-------------|
| eventName                   | **ContractFunctionInvocation** |
| Obiekt wywołujący                      | [Informacje o wywołującym](#caller-information) |
| contractId                  | Unikatowy identyfikator kontraktu w usłudze Azure łańcucha bloków Workbench |
| contractLedgerIdentifier    | Unikatowy identyfikator kontraktu w księdze |
| functionName                | Nazwa funkcji |
| parameters                  | [Informacje o parametrach](#parameter-information) |
| Transaction                 | Informacje o transakcji |
| inTransactionSequenceNumber | Numer sekwencyjny transakcji w bloku |
| connectionId                | Unikatowy identyfikator połączenia |
| messageSchemaVersion        | Wersja schematu obsługi komunikatów |
| komunikatname                 | **EventMessage** |
| additionalInformation       | Podano dodatkowe informacje |

#### <a name="caller-information"></a>Informacje o wywołującym

| Nazwa | Opis |
|------|-------------|
| typ | Typ obiektu wywołującego, na przykład użytkownika lub kontraktu |
| identyfikator | Unikatowy identyfikator obiektu wywołującego w usłudze Azure łańcucha bloków Workbench |
| ledgerIdentifier | Unikatowy identyfikator obiektu wywołującego w księdze |

#### <a name="parameter-information"></a>Informacje o parametrach

| Nazwa | Opis |
|------|-------------|
| name | Nazwa parametru |
| wartość | Wartość parametru |

#### <a name="event-message-transaction-information"></a>Informacje o transakcji komunikatu o zdarzeniu

| Nazwa               | Opis |
|--------------------|-------------|
| transactionId      | Unikatowy identyfikator transakcji wewnątrz usługi Azure łańcucha bloków Workbench |
| transactionHash    | Skrót transakcji w księdze |
| wniosek               | Unikatowy identyfikator w księdze dla źródła transakcji |
| na wartość                 | Unikatowy identyfikator w księdze dla miejsca docelowego transakcji |

Przykład *EventMessage ContractFunctionInvocation* z łańcucha bloków Workbench:

``` json
{
    "eventName": "ContractFunctionInvocation",
    "caller": {
        "type": "User",
        "id": 21,
        "ledgerIdentifier": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60ffff"
    },
    "contractId": 34,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "functionName": "Modify",
    "parameters": [
        {
            "name": "description",
            "value": "a new description"
        },
        {
            "name": "price",
            "value": "4567"
        }
    ],
    "transaction": {
        "transactionId": 234,
        "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
        "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
        "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
    },
    "inTransactionSequenceNumber": 1,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

### <a name="event-message-application-ingestion"></a>Komunikat zdarzenia: pozyskiwanie aplikacji

Zawiera informacje o przekazywaniu aplikacji do Workbench, takie jak nazwa i wersja przekazanej aplikacji.

| Nazwa | Opis |
|------|-------------|
| eventName | **ApplicationIngestion** |
| applicationId | Unikatowy identyfikator aplikacji w usłudze Azure łańcucha bloków Workbench |
| applicationName | Nazwa aplikacji |
| applicationDisplayName | Nazwa wyświetlana aplikacji |
| applicationVersion | Wersja aplikacji |
| applicationDefinitionLocation | Adres URL, pod którym znajduje się plik konfiguracji aplikacji |
| contractCodes | Kolekcja [kodów kontraktu](#contract-code-information) dla aplikacji |
| applicationRoles | Kolekcja [ról aplikacji](#application-role-information) dla aplikacji |
| applicationWorkflows | Kolekcja [przepływów pracy aplikacji](#application-workflow-information) dla aplikacji |
| connectionId | Unikatowy identyfikator połączenia |
| messageSchemaVersion | Wersja schematu obsługi komunikatów |
| komunikatname | **EventMessage** |
| additionalInformation | Informacje dodatkowe podane tutaj zawierają informacje o stanach i przejściach przepływu pracy aplikacji. |

#### <a name="contract-code-information"></a>Informacje o kodzie kontraktu

| Nazwa | Opis |
|------|-------------|
| identyfikator | Unikatowy identyfikator pliku kodu kontraktu w usłudze Azure łańcucha bloków Workbench |
| ledgerId | Unikatowy identyfikator księgi w usłudze Azure łańcucha bloków Workbench |
| location | Adres URL, pod którym znajduje się plik kodu kontraktu |

#### <a name="application-role-information"></a>Informacje o roli aplikacji

| Nazwa | Opis |
|------|-------------|
| identyfikator | Unikatowy identyfikator roli aplikacji w usłudze Azure łańcucha bloków Workbench |
| name | Nazwa roli aplikacji |

#### <a name="application-workflow-information"></a>Informacje o przepływie pracy aplikacji

| Nazwa | Opis |
|------|-------------|
| identyfikator | Unikatowy identyfikator przepływu pracy aplikacji wewnątrz usługi Azure łańcucha bloków Workbench |
| name | Nazwa przepływu pracy aplikacji |
| displayName | Nazwa wyświetlana przepływu pracy aplikacji |
|  — funkcje | Kolekcja [funkcji dla przepływu pracy aplikacji](#workflow-function-information)|
| amerykański | Kolekcja [Stanów dla przepływu pracy aplikacji](#workflow-state-information) |
| properties | [Informacje o właściwościach przepływu pracy](#workflow-property-information) aplikacji |

##### <a name="workflow-function-information"></a>Informacje o funkcji przepływu pracy

| Nazwa | Opis |
|------|-------------|
| identyfikator | Unikatowy identyfikator funkcji przepływu pracy aplikacji wewnątrz usługi Azure łańcucha bloków Workbench |
| name | Nazwa funkcji |
| parameters | Parametry dla funkcji |

##### <a name="workflow-state-information"></a>Informacje o stanie przepływu pracy

| Nazwa | Opis |
|------|-------------|
| name | Nazwa stanu |
| displayName | Nazwa wyświetlana stanu |
| styl | Styl stanu (powodzenie lub niepowodzenie) |

##### <a name="workflow-property-information"></a>Informacje o właściwościach przepływu pracy

| Nazwa | Opis |
|------|-------------|
| identyfikator | Unikatowy identyfikator właściwości przepływu pracy aplikacji wewnątrz usługi Azure łańcucha bloków Workbench |
| name | Nazwa właściwości |
| typ | Typ właściwości |

Przykład *EventMessage ApplicationIngestion* z łańcucha bloków Workbench:

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
    "applicationDefinitionLocation": "http://url",
    "contractCodes": [
        {
            "id": 23,
            "ledgerId": 1,
            "location": "http://url"
        }
    ],
    "applicationRoles": [
            {
                "id": 134,
                "name": "Buyer"
            },
            {
                "id": 135,
                "name": "Seller"
            }
       ],
    "applicationWorkflows": [
        {
            "id": 89,
            "name": "AssetTransfer",
            "displayName": "Asset Transfer",
            "functions": [
                {
                    "id": 912,
                    "name": "",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                },
                {
                    "id": 913,
                    "name": "modify",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                }
            ],
            "states": [ 
                 {
                      "name": "Created",
                      "displayName": "Created",
                      "style" : "Success"
                 },
                 {
                      "name": "Terminated",
                      "displayName": "Terminated",
                      "style" : "Failure"
                 }
            ],
            "properties": [
                {
                    "id": 879,
                    "name": "Description",
                    "type": {
                        "name": "string"
                     }
                },
                {
                    "id": 880,
                    "name": "Price",
                    "type": {
                        "name": "int"
                     }
                }
            ]
        }
    ],
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation":
        {
            "states" :
            [
                {
                    "Name": "BuyerAccepted",
                    "Transitions": [
                        {
                            "DisplayName": "Accept",
                            "AllowedRoles": [ ],
                            "AllowedInstanceRoles": [ "InstanceOwner" ],
                            "Function": "Accept",
                            "NextStates": [ "SellerAccepted" ]
                        }
                    ]
                }
            ]
        }
}
```

### <a name="event-message-role-assignment"></a>Komunikat zdarzenia: przypisanie roli

Zawiera informacje o tym, kiedy użytkownik ma przypisaną rolę w Workbench, na przykład, kto wykonał przypisanie roli oraz nazwę roli i odpowiednią aplikację.

| Nazwa | Opis |
|------|-------------|
| eventName | **RoleAssignment** |
| applicationId | Unikatowy identyfikator aplikacji w usłudze Azure łańcucha bloków Workbench |
| applicationName | Nazwa aplikacji |
| applicationDisplayName | Nazwa wyświetlana aplikacji |
| applicationVersion | Wersja aplikacji |
| applicationRole        | Informacje o [roli aplikacji](#roleassignment-application-role) |
| użytkownika przypisującego               | Informacje o [przypisaniu](#roleassignment-assigner) |
| osoby przydzielonej               | Informacje dotyczące osoby [przydzielonej](#roleassignment-assignee) |
| connectionId           | Unikatowy identyfikator połączenia |
| messageSchemaVersion   | Wersja schematu obsługi komunikatów |
| komunikatname            | **EventMessage** |
| additionalInformation  | Podano dodatkowe informacje |

#### <a name="roleassignment-application-role"></a>Rola aplikacji RoleAssignment

| Nazwa | Opis |
|------|-------------|
| identyfikator | Unikatowy identyfikator roli aplikacji w usłudze Azure łańcucha bloków Workbench |
| name | Nazwa roli aplikacji |

#### <a name="roleassignment-assigner"></a>RoleAssignment, przypisujący

| Nazwa | Opis |
|------|-------------|
| identyfikator | Unikatowy identyfikator użytkownika w usłudze Azure łańcucha bloków Workbench |
| typ | Typ przypisania |
| chainIdentifier | Unikatowy identyfikator użytkownika w księdze |

#### <a name="roleassignment-assignee"></a>RoleAssignment

| Nazwa | Opis |
|------|-------------|
| identyfikator | Unikatowy identyfikator użytkownika w usłudze Azure łańcucha bloków Workbench |
| typ | Typ osoby przydzielonej |
| chainIdentifier | Unikatowy identyfikator użytkownika w księdze |

Przykład *EventMessage RoleAssignment* z łańcucha bloków Workbench:

``` json
{
    "eventName": "RoleAssignment",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
    "applicationRole": {
        "id": 134,
        "name": "Buyer"
    },
    "assigner": {
        "id": 1,
        "type": null,
        "chainIdentifier": "0xeFFC7766d38aC862d79706c3C5CEEf089564ffff"
    },
    "assignee": {
        "id": 3,
        "type": null,
        "chainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
    },
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

## <a name="next-steps"></a>Następne kroki

- [Wzorce integracji kontraktów inteligentnych](integration-patterns.md)
