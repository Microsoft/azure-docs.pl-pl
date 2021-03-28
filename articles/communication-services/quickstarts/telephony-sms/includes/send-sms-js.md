---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-communication-services
author: bertong
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: bertong
ms.openlocfilehash: 8fe8b853fe07af40603950a61c0dd2a1df74d14e
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644324"
---
Rozpocznij pracę z usługami Azure Communications Services przy użyciu zestawu SDK SMS usługi komunikacyjnej do wysyłania wiadomości SMS.

W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-js-pr/tree/feature/communication/sdk/communication/communication-sms) | [Package (NPM)](https://www.npmjs.com/package/@azure/communication-sms) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktywne wersje LTS LTS i Maintenance (zalecane 8.11.1 i 10.14.1).
- Zasób i parametry połączenia aktywnego usługi komunikacyjnej. [Utwórz zasób usług komunikacyjnych](../../create-communication-resource.md).
- Numer telefonu z włączoną funkcją SMS. [Pobierz numer telefonu](../get-phone-number.md).

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

- W terminalu lub oknie poleceń Uruchom polecenie, `node --version` Aby sprawdzić, czy Node.js jest zainstalowany.
- Aby wyświetlić numery telefonów skojarzone z zasobem usług komunikacyjnych, zaloguj się do [Azure Portal](https://portal.azure.com/), Znajdź zasób usługi komunikacyjnej i Otwórz kartę **numery telefonów** w okienku nawigacji po lewej stronie.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-nodejs-application"></a>Utwórz nową aplikację Node.js

Najpierw Otwórz terminal lub okno poleceń, Utwórz nowy katalog dla aplikacji i przejdź do niego.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Uruchom `npm init -y` , aby utworzyć **package.jsw** pliku z ustawieniami domyślnymi.

```console
npm init -y
```

Użyj edytora tekstów, aby utworzyć plik o nazwie **send-sms.js** w katalogu głównym projektu. Do tego pliku zostanie dodany kod źródłowy dla tego przewodnika Szybki Start w poniższych sekcjach.

### <a name="install-the-package"></a>Zainstaluj pakiet

Użyj `npm install` polecenia, aby zainstalować zestaw SMS SDK usług Azure Communications Services dla języka JavaScript.

```console
npm install @azure/communication-sms --save
```

`--save`Opcja zawiera listę bibliotek jako zależność w **package.js** pliku.

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje zestawu SMS SDK usługi Azure Communication Services dla Node.js.

| Nazwa                                  | Opis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Ta klasa jest wymagana dla wszystkich funkcji programu SMS. Utwórz wystąpienie go przy użyciu informacji o subskrypcji i użyj go do wysyłania wiadomości SMS. |
| SmsSendRequest | Ten interfejs jest modelem do kompilowania żądania programu SMS (np. Skonfiguruj do i numery telefonów oraz zawartość SMS). |
| SmsSendOptions | Ten interfejs zapewnia opcje konfigurowania raportowania dostarczania. Jeśli `enableDeliveryReport` jest ustawiona na `true` , zdarzenie będzie emitowane po pomyślnym dostarczeniu. |
| SmsSendResult               | Ta klasa zawiera wynik z usługi SMS.                                          |

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Zaimportuj **SmsClient** z zestawu SDK i Utwórz wystąpienie go przy użyciu parametrów połączenia. Poniższy kod pobiera parametry połączenia dla zasobu ze zmiennej środowiskowej o nazwie `COMMUNICATION_SERVICES_CONNECTION_STRING` . Dowiedz się, jak [zarządzać parametrami połączenia zasobu](../../create-communication-resource.md#store-your-connection-string).

Utwórz i Otwórz plik o nazwie **send-sms.js** i Dodaj następujący kod:

```javascript
const { SmsClient } = require('@azure/communication-sms');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
```

## <a name="send-a-1n-sms-message"></a>Wyślij wiadomość SMS z 1: N

Aby wysłać wiadomość SMS do listy adresatów, wywołaj `send` funkcję z SmsClient z listą numerów telefonów adresatów (Jeśli chcesz wysłać wiadomość do pojedynczego odbiorcy, Uwzględnij tylko jedną cyfrę na liście). Dodaj ten kod na końcu **send-sms.js**:

```javascript
async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Hello World 👋🏻 via SMS"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```
Należy zamienić `<from-phone-number>` na numer telefonu z włączonym programem SMS skojarzony z zasobem usługi komunikacyjnej oraz `<to-phone-number-1>` `<to-phone-number-2>` z numerami telefonów, do których chcesz wysłać wiadomość.

> [!WARNING]
> Należy zauważyć, że numery telefonów powinny być podane w standardowym formacie E. 164. (np.: + 14255550123).

## <a name="send-a-1n-sms-message-with-options"></a>Wyślij wiadomość SMS z opcją 1: N z opcjami

Możesz również przekazać obiekt Options, aby określić, czy raport dostarczania powinien być włączony, i ustawić Tagi niestandardowe.

```javascript

async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Weekly Promotion!"
  }, {
    //Optional parameters
    enableDeliveryReport: true,
    tag: "marketing"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```

Należy zamienić `<from-phone-number>` na numer telefonu z włączoną funkcją SMS skojarzony z zasobem usług komunikacyjnych `<to-phone-number-1>` i `<to-phone-number-2>` z numerami telefonów, do których chcesz wysłać wiadomość.

> [!WARNING]
> Należy zauważyć, że numery telefonów powinny być podane w standardowym formacie E. 164. (np.: + 14255550123).

`enableDeliveryReport`Parametr jest opcjonalnym parametrem, którego można użyć w celu skonfigurowania raportowania dostarczania. Jest to przydatne w scenariuszach, w których chcesz emitować zdarzenia podczas dostarczania wiadomości SMS. Zapoznaj się z tematem [Obsługa zdarzeń programu SMS](../handle-sms-events.md) — Szybki Start, aby skonfigurować dostarczanie raportów dla wiadomości SMS.
`tag` jest opcjonalnym parametrem, za pomocą którego można zastosować tag do raportu dostarczania.

## <a name="run-the-code"></a>Uruchamianie kodu

Użyj `node` polecenia, aby uruchomić kod, który został dodany do pliku **send-sms.js** .

```console

node ./send-sms.js

```
