---
title: Jak używać poleceń urządzeń w rozwiązaniu IoT Central platformy Azure
description: Jak używać poleceń urządzenia w usłudze Azure IoT Central rozwiązanie. W tym samouczku pokazano, jak deweloper urządzenia używa poleceń urządzenia w aplikacji klienckiej do aplikacji IoT Central platformy Azure.
author: dominicbetts
ms.author: dobett
ms.date: 01/07/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: e53bf377a7ef8f2293debd288ba25ef8f04ff4fc
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611002"
---
# <a name="how-to-use-commands-in-an-azure-iot-central-solution"></a>Jak używać poleceń w rozwiązaniu IoT Central platformy Azure

W tym przewodniku opisano sposób, w jaki deweloper urządzenia używa poleceń, które są zdefiniowane w szablonie urządzenia.

Operator może użyć interfejsu użytkownika IoT Central do wywołania polecenia na urządzeniu. Polecenia sterują zachowaniem urządzenia. Na przykład operator może wywoływać polecenie w celu ponownego uruchomienia urządzenia lub zbierania danych diagnostycznych.

Urządzenie może:

* Natychmiastowe Odpowiedz na polecenie.
* Odpowiedz na IoT Central, gdy odbierze polecenie, a następnie Powiadom IoT Central o zakończeniu *długotrwałego polecenia* .

Domyślnie polecenia oczekują, że urządzenie ma być połączone i kończy się niepowodzeniem, jeśli nie można nawiązać połączenia z urządzeniem. W przypadku wybrania opcji **Kolejka w trybie offline** w interfejsie użytkownika szablonu urządzenia polecenie może zostać umieszczone w kolejce, dopóki urządzenie nie przejdzie do trybu online. Te *polecenia w trybie offline* są opisane w oddzielnej sekcji w dalszej części tego artykułu.

## <a name="define-your-commands"></a>Definiowanie poleceń

Polecenia standardowe są wysyłane do urządzenia, aby nakazać urządzeniu wykonywanie czegoś. Polecenie może zawierać parametry z dodatkowymi informacjami. Na przykład polecenie otwarcia zaworu na urządzeniu może mieć parametr określający, jak dużo należy otworzyć zawór. Polecenia mogą również otrzymać wartość zwracaną, gdy urządzenie ukończy polecenie. Na przykład polecenie, które prosi urządzenie o uruchomienie niektórych diagnostyki, może otrzymać raport diagnostyczny jako wartość zwrotną.

Polecenia są definiowane jako część szablonu urządzenia. Poniższy zrzut ekranu przedstawia definicję polecenia **pobierz Max-Min raport** w szablonie urządzenia z **termostatem** . To polecenie ma zarówno parametry żądania, jak i odpowiedzi: 

:::image type="content" source="media/howto-use-commands/command-definition.png" alt-text="Zrzut ekranu przedstawiający polecenie Pobierz maks. minimalny raport w szablonie urządzenia z termostatem":::

W poniższej tabeli przedstawiono ustawienia konfiguracji dla funkcji polecenia:

| Pole             |Opis|
|-------------------|-----------|
|Nazwa wyświetlana       |Wartość polecenia używana na pulpitach nawigacyjnych i formularzach.|
| Nazwa            | Nazwa polecenia. IoT Central generuje wartość dla tego pola z nazwy wyświetlanej, ale w razie potrzeby można wybrać własną wartość. To pole musi być alfanumeryczne. Kod urządzenia używa tej wartości **nazwy** .|
| Typ możliwości | Dotyczące.|
| Kolejka w trybie offline | Określa, czy polecenie to należy wykonać w *trybie offline* . |
| Opis     | Opis możliwości polecenia.|
| Komentarz     | Wszelkie komentarze dotyczące funkcji polecenia.|
| Żądanie     | Ładunek dla polecenia urządzenia.|
| Reakcja     | Ładunek odpowiedzi na polecenie urządzenia.|

Poniższy fragment kodu przedstawia reprezentację w formacie JSON polecenia w modelu urządzenia. W tym przykładzie wartość odpowiedzi jest złożonym typem **obiektu** z wieloma polami:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

> [!TIP]
> Model urządzenia można wyeksportować ze strony szablon urządzenia.

Tę definicję polecenia można powiązać z zrzutem ekranu interfejsu użytkownika przy użyciu następujących pól:

* `@type` Aby określić typ możliwości: `Command`
* `name` dla wartości polecenia.

Opcjonalne pola, takie jak nazwa wyświetlana i opis, umożliwiają dodanie więcej szczegółów do interfejsu i możliwości.

## <a name="standard-commands"></a>Polecenia standardowe

W tej sekcji pokazano, w jaki sposób urządzenie wysyła wartość odpowiedzi zaraz po odebraniu polecenia.

Poniższy fragment kodu przedstawia sposób, w jaki urządzenie może odpowiedzieć na polecenie natychmiast, wysyłając kod sukcesu:

> [!NOTE]
> W tym artykule użyto Node.js dla uproszczenia. Aby zapoznać się z innymi przykładami języka, zobacz samouczek [Tworzenie i łączenie aplikacji klienckiej z aplikacją platformy Azure IoT Central](tutorial-connect-device.md) .

```javascript
client.onDeviceMethod('getMaxMinReport', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'getMaxMinReport': {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

Wywołanie w celu `onDeviceMethod` skonfigurowania `commandHandler` metody. Ta procedura obsługi poleceń:

1. Sprawdza nazwę polecenia.
1. Dla `getMaxMinReport` polecenia jest wywoływana w `getMaxMinReportObject` celu pobrania wartości do uwzględnienia w zwracanym obiekcie.
1. Wywołania `sendCommandResponse` do wysłania odpowiedzi z powrotem do IoT Central. Ta odpowiedź zawiera `200` kod odpowiedzi wskazujący powodzenie.

Poniższy zrzut ekranu pokazuje, jak wyświetlana jest pomyślna odpowiedź polecenia w interfejsie użytkownika IoT Central:

:::image type="content" source="media/howto-use-commands/simple-command-ui.png" alt-text="Zrzut ekranu przedstawiający sposób wyświetlania ładunku poleceń dla standardowego polecenia":::

## <a name="long-running-commands"></a>Długotrwałe polecenia

W tej sekcji pokazano, jak urządzenie może opóźnić wysyłanie potwierdzenia przez polecenie.

Poniższy fragment kodu przedstawia sposób, w jaki urządzenie może zaimplementować długotrwałe polecenie:

> [!NOTE]
> W tym artykule użyto Node.js dla uproszczenia. Aby zapoznać się z innymi przykładami języka, zobacz samouczek [Tworzenie i łączenie aplikacji klienckiej z aplikacją platformy Azure IoT Central](tutorial-connect-device.md) .

```javascript
client.onDeviceMethod('rundiagnostics', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'rundiagnostics': {
    console.log('Starting long-running diagnostics run ' + request.payload);
    await sendCommandResponse(request, response, 202, 'Diagnostics run started');

    // Long-running operation here
    // ...

    const patch = {
      rundiagnostics: {
        value: 'Diagnostics run complete at ' + new Date().toLocaleString()
      }
    };

    deviceTwin.properties.reported.update(patch, function (err) {
      if (err) throw err;
      console.log('Properties have been reported for component');
    });
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};
```

Wywołanie w celu `onDeviceMethod` skonfigurowania `commandHandler` metody. Ta procedura obsługi poleceń:

1. Sprawdza nazwę polecenia.
1. Wywołania `sendCommandResponse` do wysłania odpowiedzi z powrotem do IoT Central. Ta odpowiedź zawiera `202` kod odpowiedzi wskazujący, że oczekiwane wyniki.
1. Kończy wykonywanie długotrwałej operacji.
1. Używa raportowanej właściwości o takiej samej nazwie jak polecenie, aby poinformować IoT Central, że polecenie zostało ukończone.

Poniższy zrzut ekranu przedstawia sposób wyświetlania odpowiedzi polecenia w interfejsie użytkownika IoT Central po odebraniu kodu odpowiedzi 202:

:::image type="content" source="media/howto-use-commands/long-running-start.png" alt-text="Zrzut ekranu przedstawiający natychmiastowe odpowiedzi z urządzenia":::

Poniższy zrzut ekranu przedstawia IoT Central interfejs użytkownika po odebraniu aktualizacji właściwości, która wskazuje, że polecenie zostało ukończone:

:::image type="content" source="media/howto-use-commands/long-running-finish.png" alt-text="Zrzut ekranu pokazujący długotrwałe polecenie zakończone":::

## <a name="offline-commands"></a>Polecenia w trybie offline

W tej sekcji pokazano, jak urządzenie obsługuje polecenie w trybie offline. Jeśli urządzenie jest w trybie online, może obsłużyć polecenie w trybie offline zaraz po odebraniu. Jeśli urządzenie jest w trybie offline, obsługuje polecenie offline po kolejnym nawiązaniu połączenia z IoT Central. Urządzenia nie mogą wysyłać wartości zwracanej w odpowiedzi na polecenie w trybie offline.

> [!NOTE]
> W tym artykule użyto Node.js dla uproszczenia.

Poniższy zrzut ekranu przedstawia polecenie offline o nazwie **GenerateDiagnostics**. Parametr request jest obiektem z właściwością DateTime o nazwie **StartTime** i właściwością wyliczenia Integer o nazwie **Bank**:

:::image type="content" source="media/howto-use-commands/offline-command.png" alt-text="Zrzut ekranu pokazujący interfejs użytkownika dla polecenia w trybie offline":::

Poniższy fragment kodu przedstawia sposób, w jaki klient może nasłuchiwać poleceń w trybie offline i wyświetlać zawartość komunikatu:

```javascript
client.on('message', function (msg) {
  console.log('Body: ' + msg.data);
  console.log('Properties: ' + JSON.stringify(msg.properties));
  client.complete(msg, function (err) {
    if (err) {
      console.error('complete error: ' + err.toString());
    } else {
      console.log('complete sent');
    }
  });
});
```

Dane wyjściowe poprzedniego fragmentu kodu przedstawiają ładunek z wartościami **StartTime** i **bankowymi** . Lista właściwości zawiera nazwę polecenia w elemencie listy **Nazwa metody** :

```output
Body: {"StartTime":"2021-01-06T06:00:00.000Z","Bank":2}
Properties: {"propertyList":[{"key":"iothub-ack","value":"none"},{"key":"method-name","value":"GenerateDiagnostics"}]}
```

> [!NOTE]
> Domyślny czas wygaśnięcia polecenia w trybie offline wynosi 24 godziny, po upływie którego wiadomość wygaśnie.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak używać poleceń w aplikacji IoT Central platformy Azure, zobacz [ładunki](concepts-telemetry-properties-commands.md) , aby dowiedzieć się więcej na temat parametrów poleceń i [utworzyć i połączyć aplikację kliencką z aplikacją IoT Central platformy Azure](tutorial-connect-device.md) , aby zobaczyć kompletne przykłady kodu w różnych językach.
