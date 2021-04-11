---
ms.openlocfilehash: 0bfb23977f6553568da24df614621bdf1eb9d06d
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113309"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Najnowsza wersja [programu .NET Core Client Library](https://dotnet.microsoft.com/download/dotnet-core) dla danego systemu operacyjnego.
- Zasób i parametry połączenia aktywnego usługi komunikacyjnej. [Utwórz zasób usług komunikacyjnych](../../create-communication-resource.md).

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

- W terminalu lub oknie poleceń Uruchom `dotnet` polecenie, aby sprawdzić, czy Biblioteka klienta .NET jest zainstalowana.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-c-application"></a>Utwórz nową aplikację w języku C#

W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową o nazwie `PhoneNumbersQuickstart` . To polecenie tworzy prosty projekt C# "Hello world" z pojedynczym plikiem źródłowym: **program. cs**.

```console
dotnet new console -o PhoneNumbersQuickstart
```

Zmień katalog na nowo utworzony folder aplikacji i Użyj `dotnet build` polecenia, aby skompilować aplikację.

```console
cd PhoneNumbersQuickstart
dotnet build
```

### <a name="install-the-package"></a>Zainstaluj pakiet

Mimo że w katalogu aplikacji, zainstaluj bibliotekę klienta usługi Azure Communicationname dla pakietu .NET za pomocą `dotnet add package` polecenia.

```console
dotnet add package Azure.Communication.PhoneNumbers --version 1.0.0-beta.6
```

Dodaj `using` dyrektywę w górnej części **programu program. cs** , aby uwzględnić przestrzenie nazw.

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Azure.Communication.PhoneNumbers;
```

Aktualizowanie `Main` sygnatury funkcji jako asynchronicznej.

```csharp
static async Task Main(string[] args)
{
  ...
}
```

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Klientów numeru telefonu można uwierzytelniać przy użyciu parametrów połączenia uzyskanych z zasobów komunikacji platformy Azure w [Azure Portal] [azure_portal].

```csharp
// Get a connection string to our Azure Communication resource.
var connectionString = "<connection_string>";
var client = new PhoneNumbersClient(connectionString);
```

W przypadku klientów z numerem telefonu dostępna jest również opcja uwierzytelniania przy użyciu Azure Active Directory uwierzytelniania. W przypadku tej opcji `AZURE_CLIENT_SECRET` , `AZURE_CLIENT_ID` i należy `AZURE_TENANT_ID` skonfigurować zmienne środowiskowe do uwierzytelniania.

```csharp
// Get an endpoint to our Azure Communication resource.
var endpoint = new Uri("<endpoint_url>");
TokenCredential tokenCredential = new DefaultAzureCredential();
client = new PhoneNumbersClient(endpoint, tokenCredential);
```

## <a name="manage-phone-numbers"></a>Zarządzanie numerami telefonów

### <a name="search-for-available-phone-numbers"></a>Wyszukaj dostępne numery telefonów

Aby kupić numery telefonów, należy najpierw wyszukać dostępne numery telefonów. Aby wyszukać numery telefonów, podaj numer kierunkowy, typ przypisania, [możliwości numeru](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)telefonu, [typ numeru telefonu](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)i ilość. Należy pamiętać, że dla bezpłatnego typu numeru telefonu, dostarczając kod obszaru jest opcjonalny.

```csharp
var capabilities = new PhoneNumberCapabilities(calling:PhoneNumberCapabilityType.None, sms:PhoneNumberCapabilityType.Outbound);
var searchOptions = new PhoneNumberSearchOptions { AreaCode = "833", Quantity = 1 };

var searchOperation = await client.StartSearchAvailablePhoneNumbersAsync("US", PhoneNumberType.TollFree, PhoneNumberAssignmentType.Application, capabilities, searchOptions);
await searchOperation.WaitForCompletionAsync();
```

### <a name="purchase-phone-numbers"></a>Zakupy numerów telefonów

Wynikiem wyszukiwania numerów telefonów jest `PhoneNumberSearchResult` . Zawiera to, `SearchId` które można przesłać do interfejsu API numerów zakupów w celu uzyskania liczb w wyszukiwaniu. Należy pamiętać, że wywołanie interfejsu API numerów telefonów zakupów spowoduje naliczenie opłaty za Twoje konto platformy Azure.

```csharp
var purchaseOperation = await client.StartPurchasePhoneNumbersAsync(searchOperation.Value.SearchId);
await purchaseOperation.WaitForCompletionResponseAsync();
```

### <a name="get-phone-numbers"></a>Uzyskaj numery telefonów

Po wybraniu numeru zakupu można pobrać go z klienta programu.
```csharp
var getPhoneNumberResponse = await client.GetPurchasedPhoneNumberAsync("+14255550123");
Console.WriteLine($"Phone number: {getPhoneNumberResponse.Value.PhoneNumber}, country code: {getPhoneNumberResponse.Value.CountryCode}");
```

Możesz również pobrać wszystkie zakupione numery telefonów.
``` csharp
var purchasedPhoneNumbers = client.GetPurchasedPhoneNumbersAsync();
await foreach (var purchasedPhoneNumber in purchasedPhoneNumbers)
{
    Console.WriteLine($"Phone number: {purchasedPhoneNumber.PhoneNumber}, country code: {purchasedPhoneNumber.CountryCode}");
}
```

### <a name="update-phone-number-capabilities"></a>Aktualizowanie możliwości numeru telefonu

Za pomocą numeru zakupionego można zaktualizować możliwości.

````csharp
var updateCapabilitiesOperation = await client.StartUpdateCapabilitiesAsync("+14255550123", calling: PhoneNumberCapabilityType.Outbound, sms: PhoneNumberCapabilityType.InboundOutbound);
await updateCapabilitiesOperation.WaitForCompletionAsync();
````


### <a name="release-phone-number"></a>Numer telefonu wydania

Możesz zwolnić zakupiony numer telefonu.

````csharp
var releaseOperation = await client.StartReleasePhoneNumberAsync("+14255550123");
await releaseOperation.WaitForCompletionResponseAsync();
````

## <a name="run-the-code"></a>Uruchamianie kodu

Uruchom aplikację z katalogu aplikacji za pomocą `dotnet run` polecenia.

```console
dotnet run
```

## <a name="sample-code"></a>Przykładowy kod

Możesz pobrać przykładową aplikację z usługi [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/PhoneNumbers)
