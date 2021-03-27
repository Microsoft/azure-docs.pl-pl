---
ms.openlocfilehash: 23c64cc91378be605481eb554af2178100df3508
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629415"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktywne wersje LTS LTS i Maintenance (zalecane 8.11.1 i 10.14.1).
- Zasób i parametry połączenia aktywnego usługi komunikacyjnej. [Utwórz zasób usług komunikacyjnych](../../create-communication-resource.md).

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

- W terminalu lub oknie poleceń Uruchom polecenie, `node --version` Aby sprawdzić, czy Node.js jest zainstalowany.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-nodejs-application"></a>Utwórz nową aplikację Node.js

Najpierw Otwórz terminal lub okno poleceń, Utwórz nowy katalog dla aplikacji i przejdź do niego.

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

Uruchom `npm init -y` , aby utworzyć **package.jsw** pliku z ustawieniami domyślnymi.

```console
npm init -y
```

Utwórz plik o nazwie **phone-numbers-quickstart.js** w katalogu głównym właśnie utworzonego katalogu. Dodaj następujący fragment kodu:

```javascript
async function main() {
    // quickstart code will here
}

main();
```

### <a name="install-the-package"></a>Zainstaluj pakiet

Użyj `npm install` polecenia, aby zainstalować bibliotekę klienta usługi Azure Communications Services dla języka JavaScript.

```console
npm install @azure/communication-phone-numbers --save
```

`--save`Opcja dodaje bibliotekę jako zależność w **package.js** pliku.

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Zaimportuj **PhoneNumbersClient** z biblioteki klienta i Utwórz wystąpienie go przy użyciu parametrów połączenia. Poniższy kod pobiera parametry połączenia dla zasobu ze zmiennej środowiskowej o nazwie `COMMUNICATION_SERVICES_CONNECTION_STRING` . Dowiedz się, jak [zarządzać parametrami połączenia zasobu](../../create-communication-resource.md#store-your-connection-string).

Dodaj następujący kod na początku **phone-numbers-quickstart.js**:

```javascript
const { PhoneNumbersClient } = require('@azure/communication-phone-numbers');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the phone numbers client
const phoneNumbersClient = new PhoneNumbersClient(connectionString);
```

## <a name="manage-phone-numbers"></a>Zarządzanie numerami telefonów

### <a name="search-for-available-phone-numbers"></a>Wyszukaj dostępne numery telefonów

Aby kupić numery telefonów, należy najpierw wyszukać dostępne numery telefonów. Aby wyszukać numery telefonów, podaj numer kierunkowy, typ przypisania, [możliwości numeru](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)telefonu, [typ numeru telefonu](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)i ilość. Należy pamiętać, że dla bezpłatnego typu numeru telefonu, dostarczając kod obszaru jest opcjonalny.

Dodaj następujący fragment kodu do `main` funkcji:

```javascript
/**
 * Search for Available Phone Number
 */

// Create search request
const searchRequest = {
    countryCode: "US",
    phoneNumberType: "tollFree",
    assignmentType: "application",
    capabilities: {
      sms: "outbound",
      calling: "none"
    },
    areaCode: "833",
    quantity: 1
  };

const searchPoller = await phoneNumbersClient.beginSearchAvailablePhoneNumbers(searchRequest);

// The search is underway. Wait to receive searchId.
const { searchId, phoneNumbers } = searchPoller.pollUntilDone();
const phoneNumber = phoneNumbers[0];

console.log(`Found phone number: ${phoneNumber}`);
console.log(`searchId: ${searchId}`);
```

### <a name="purchase-phone-number"></a>Numer telefonu zakupu

Wynikiem wyszukiwania numerów telefonów jest `PhoneNumberSearchResult` . Zawiera to, `searchId` które można przesłać do interfejsu API numerów zakupów w celu uzyskania liczb w wyszukiwaniu. Należy pamiętać, że wywołanie interfejsu API numerów telefonów zakupów spowoduje naliczenie opłaty za Twoje konto platformy Azure.

Dodaj następujący fragment kodu do `main` funkcji:

```javascript
/**
 * Purchase Phone Number
 */

const purchasePoller = await phoneNumbersClient.beginPurchasePhoneNumbers(searchId);

// Purchase is underway.
await purchasePoller.pollUntilDone();
console.log(`Successfully purchased ${phoneNumber}`);
```

### <a name="update-phone-number-capabilities"></a>Aktualizowanie możliwości numeru telefonu

Po zakupie numeru telefonu Dodaj następujący kod, aby zaktualizować jego możliwości:

```javascript
/**
 * Update Phone Number Capabilities
 */

// Create update request.
// This will update phone number to send and receive sms, but only send calls.
const updateRequest = {
  sms: "inbound+outbound",
  calling: "outbound"
};

const updatePoller = await phoneNumbersClient.beginUpdatePhoneNumberCapabilities(
  phoneNumber,
  updateRequest
);

// Update is underway.
await updatePoller.pollUntilDone();
console.log("Phone number updated successfully.");
```

### <a name="get-purchased-phone-numbers"></a>Pobierz zakupione numery telefonów

Po wybraniu numeru zakupu można pobrać go z klienta programu. Dodaj następujący kod do funkcji, `main` Aby uzyskać kupiony numer telefonu:

```javascript
/**
 * Get Purchased Phone Number
 */

const { capabilities } = await phoneNumbersClient.getPurchasedPhoneNumber(phoneNumber);
console.log(`These capabilities: ${capabilities}, should be the same as these: ${updateRequest}.`);
```

Możesz również pobrać wszystkie zakupione numery telefonów.

```javascript
const phoneNumbers = await phoneNumbersClient.listPurchasedPhoneNumbers();

for await (const purchasedPhoneNumber of phoneNumbers) {
  console.log(`Phone number: ${purchasedPhoneNumber.phoneNumber}, country code: ${purchasedPhoneNumber.countryCode}.`);
}
```

### <a name="release-phone-number"></a>Numer telefonu wydania

Możesz teraz zwolnić zakupiony numer telefonu. Dodaj poniższy fragment kodu do `main` funkcji:

```javascript
/**
 * Release Purchased Phone Number
 */

const releasePoller = await client.beginReleasePhoneNumber(phoneNumber);

// Release is underway.
await releasePoller.pollUntilDone();
console.log("Successfully release phone number.");
```

## <a name="run-the-code"></a>Uruchamianie kodu

Użyj `node` polecenia, aby uruchomić kod, który został dodany do pliku **phone-numbers-quickstart.js** .

```console
node phone-numbers-quickstart.js
```