---
title: 'Szybki Start: wysyłanie i odbieranie zdarzeń przy użyciu języka go-Azure Event Hubs'
description: 'Szybki Start: Ten artykuł zawiera Przewodnik dotyczący tworzenia aplikacji języka go, która wysyła zdarzenia z usługi Azure Event Hubs.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 59cd0d757108e7579ce389d216b0ee4d569e12fd
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87002458"
---
# <a name="quickstart-send-events-to-or-receive-events-from-event-hubs-using-go"></a>Szybki Start: wysyłanie zdarzeń do i odbieranie zdarzeń z Event Hubs przy użyciu języka go
Azure Event Hubs to platforma do pozyskiwania i strumieniowego przesyłania danych, która umożliwia odbieranie i przetwarzanie milionów zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md).

W tym samouczku opisano, jak zapisywać aplikacje w programie w celu wysyłania zdarzeń do zdarzeń lub odbierania ich z centrum zdarzeń. 

> [!NOTE]
> Ten przewodnik Szybki start możesz pobrać jako przykład z witryny [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), zastąpić ciągi `EventHubConnectionString` i `EventHubName` wartościami swojego centrum zdarzeń, a następnie uruchomić go. Alternatywnie możesz utworzyć własne rozwiązanie, wykonując kroki opisane w tym samouczku.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

- Zainstalowano lokalnie. W razie potrzeby postępuj zgodnie z [tymi instrukcjami](https://golang.org/doc/install) .
- Aktywne konto platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][].
- **Utwórz przestrzeń nazw Event Hubs i centrum zdarzeń**. Użyj [Azure Portal](https://portal.azure.com) , aby utworzyć przestrzeń nazw typu Event Hubs i uzyskać poświadczenia zarządzania wymagane przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md).

## <a name="send-events"></a>Wysyłanie zdarzeń
W tej sekcji przedstawiono sposób tworzenia aplikacji języka go w celu wysyłania zdarzeń do centrum zdarzeń. 

### <a name="install-go-package"></a>Zainstaluj pakiet języka go

Pobierz pakiet języka go dla Event Hubs z `go get` lub `dep` . Na przykład:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>Importuj pakiety w pliku kodu

Aby zaimportować pakiety języka go, należy użyć następującego przykładu kodu:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>Tworzenie jednostki usługi

Utwórz nową nazwę główną usługi, postępując zgodnie z instrukcjami w temacie [Tworzenie jednostki usługi platformy Azure przy użyciu interfejsu wiersza polecenia platformy azure 2,0](/cli/azure/create-an-azure-service-principal-azure-cli). Zapisz podane poświadczenia w środowisku z następującymi nazwami. Zarówno Azure SDK dla języka Go, jak i pakiety Event Hubs są wstępnie skonfigurowane do wyszukiwania tych nazw zmiennych:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Teraz Utwórz dostawcę autoryzacji dla klienta Event Hubs, który używa tych poświadczeń:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="create-event-hubs-client"></a>Utwórz klienta Event Hubs

Poniższy kod tworzy klienta Event Hubs:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>Pisanie kodu w celu wysyłania komunikatów

W poniższym fragmencie kodu Użyj (1), aby interakcyjnie wysyłać wiadomości z terminalu, lub (2), aby wysyłać komunikaty w ramach programu:

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!"))
```

### <a name="extras"></a>Dodatki

Pobierz identyfikatory partycji w centrum zdarzeń:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

Uruchom aplikację, aby wysłać zdarzenia do centrum zdarzeń. 

Gratulacje! Wysłano komunikaty do centrum zdarzeń.

## <a name="receive-events"></a>Odbieranie zdarzeń

### <a name="create-a-storage-account-and-container"></a>Tworzenie konta magazynu i kontenera

Stan, taki jak dzierżawy na partycjach i punkty kontrolne w strumieniu zdarzeń, jest współużytkowany przez odbiorniki przy użyciu kontenera usługi Azure Storage. Można utworzyć konto magazynu i kontener za pomocą zestawu SDK języka go, ale można go również utworzyć, wykonując instrukcje zawarte w [temacie Informacje o kontach usługi Azure Storage](../storage/common/storage-account-create.md).

Przykłady tworzenia artefaktów magazynu za pomocą zestawu SDK języka go są dostępne w [repozytorium przykłady](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) i w przykładach odpowiadających temu samouczkowi.

### <a name="go-packages"></a>Pakiety języka go

Aby odbierać komunikaty, Pobierz pakiety go dla Event Hubs z `go get` lub `dep` :

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

### <a name="import-packages-in-your-code-file"></a>Importuj pakiety w pliku kodu

Aby zaimportować pakiety języka go, należy użyć następującego przykładu kodu:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

### <a name="create-service-principal"></a>Tworzenie jednostki usługi

Utwórz nową nazwę główną usługi, postępując zgodnie z instrukcjami w temacie [Tworzenie jednostki usługi platformy Azure przy użyciu interfejsu wiersza polecenia platformy azure 2,0](/cli/azure/create-an-azure-service-principal-azure-cli). Zapisz podane poświadczenia w danym środowisku przy użyciu następujących nazw: wstępnie skonfigurowany pakiet Azure SDK dla języka Go i Event Hubs do wyszukiwania tych nazw zmiennych.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Następnie utwórz dostawcę autoryzacji dla klienta Event Hubs, który używa tych poświadczeń:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>Pobierz strukturę metadanych

Pobierz strukturę z metadanymi o środowisku platformy Azure przy użyciu zestawu Azure go SDK. Późniejsze operacje używają tej struktury do znajdowania prawidłowych punktów końcowych.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>Utwórz pomocnika poświadczeń 

Utwórz pomocnika poświadczeń korzystającego z poprzednich poświadczeń usługi Azure Active Directory (AAD), aby utworzyć poświadczenia sygnatury dostępu współdzielonego (SAS) dla magazynu. Ostatni parametr nakazuje temu konstruktorowi używanie tych samych zmiennych środowiskowych, jak używane wcześniej:

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

### <a name="create-a-check-pointer-and-a-leaser"></a>Tworzenie wskaźnika Check i dzierżawy 

Utwórz **dzierżawę**, odpowiedzialną za dzierżawę określonej odbiorcy, a także **wskaźnik kontroli**odpowiedzialny za pisanie punktów kontrolnych strumienia wiadomości, dzięki czemu inni odbiorcy mogą zacząć odczytywanie od poprawnego przesunięcia.

Obecnie dostępna jest jedna **StorageLeaserCheckpointer** , która używa tego samego kontenera magazynu do zarządzania dzierżawami i punktami kontrolnymi. Oprócz nazw kont magazynu i kontenerów **StorageLeaserCheckpointer** wymaga podania poświadczeń utworzonych w poprzednim kroku i struktury środowiska platformy Azure w celu prawidłowego dostępu do kontenera.

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

### <a name="construct-event-processor-host"></a>Konstruowanie hosta procesora zdarzeń

Masz teraz elementy potrzebne do skonstruowania klasy eventprocessorhost w następujący sposób. Ten sam **StorageLeaserCheckpointer** jest używany zarówno jako dzierżawa, jak i wskaźnik sprawdzania, jak opisano wcześniej:

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

### <a name="create-handler"></a>Utwórz procedurę obsługi 

Teraz Utwórz procedurę obsługi i zarejestruj ją za pomocą hosta procesora zdarzeń. Gdy host zostanie uruchomiony, ma zastosowanie do wszystkich innych określonych programów obsługi komunikatów przychodzących:

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

### <a name="write-code-to-receive-messages"></a>Pisanie kodu w celu odbierania komunikatów

Za pomocą wszystkich konfiguracji można uruchomić hosta procesora zdarzeń za pomocą programu `Start(context)` , aby zachować jego trwałą pracę, lub z systemem, `StartNonBlocking(context)` tak długo, jak są dostępne komunikaty.

Ten samouczek jest uruchamiany i uruchamiany w następujący sposób: Zapoznaj się z przykładem usługi GitHub, korzystając z `StartNonBlocking` :

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="next-steps"></a>Następne kroki
Przeczytaj następujące artykuły:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkcje i terminologia w usłudze Azure Event Hubs](event-hubs-features.md)
- [Event Hubs — często zadawane pytania](event-hubs-faq.md)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[bezpłatne konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
