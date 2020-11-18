---
title: Aktualizowanie polecenia z poziomu aplikacji klienckiej
titleSuffix: Azure Cognitive Services
description: Aktualizowanie polecenia z poziomu aplikacji klienckiej
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 1bffb09d0f49bbd0059e8a528d67bfe215f0650d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654351"
---
# <a name="update-a-command-from-the-client"></a>Aktualizowanie polecenia z poziomu klienta

W tym artykule dowiesz się, jak zaktualizować bieżące polecenie z aplikacji klienckiej.

## <a name="prerequisites"></a>Wymagania wstępne
> [!div class = "checklist"]
> * [Utworzona wcześniej aplikacja Polecenia niestandardowe](quickstart-custom-commands-application.md)

## <a name="update-the-state-of-a-command"></a>Aktualizowanie stanu polecenia

Jeśli aplikacja kliencka wymaga zaktualizowania stanu trwającego polecenia bez wprowadzania głosu, można wysłać zdarzenie, aby zaktualizować polecenie.

Aby zilustrować ten scenariusz, zaktualizuj stan trwającego polecenia (TurnOnOff), możemy wysłać następujące działania dotyczące zdarzenia. 

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "updatedCommand": {
      "name": "TurnOnOff",
      "updatedParameters": {
        "OnOff": "on"
      },
      "cancel": false
    },
    "updatedGlobalParameters": {},
    "processTurn": true
  }
}
```

Umożliwia przejrzenie kluczowych atrybutów tego działania.

| Atrybut | Wyjaśnienie |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Wprowadź** | Działanie jest typu "Event", a nazwa zdarzenia musi mieć wartość "RemoteUpdate". |
| **wartość** | Atrybut "value" zawiera atrybuty wymagane do zaktualizowania bieżącego polecenia. |
| **updatedCommand** | Atrybut "updatedCommand" zawiera nazwę polecenia, "updatedParameters" jest mapą z nazwą parametrów i ich zaktualizowanymi wartościami. |
| **Anuluj** | Jeśli bieżące polecenie musi zostać anulowane, ustaw dla atrybutu "Cancel" wartość true. |
| **updatedGlobalParameters** | Atrybut "updatedGlobalParameters" jest również mapą, podobnie jak "updatedParameters", ale używany jako parametry globalne. |
| **processTurn** | Jeśli trzeba przetworzyć po wysłaniu działania, ustaw atrybut "processTurn" na wartość true. |

Możesz przetestować ten scenariusz w portalu poleceń niestandardowych.

1. Otwórz utworzoną wcześniej aplikację Polecenia niestandardowe. 
1. Kliknij pozycję uczenie i przetestuj.
1. Wyślij "Włącz".
1. Otwórz Panel boczny, a następnie kliknij pozycję Edytor działań.
1. Wpisz i Wyślij zdarzenie RemoteCommand określone w poprzedniej sekcji.
    > [!div class="mx-imgBorder"]
    > ![Wyślij polecenie zdalne](media/custom-commands/send-remote-command-activity.png)

Zwróć uwagę, jak wartość parametru "OnOff" została ustawiona na "on" przy użyciu działania z klienta zamiast mowy lub tekstu.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>Aktualizowanie katalogu parametru dla polecenia

Po skonfigurowaniu listy prawidłowych opcji dla parametru, wartości dla parametru są definiowane globalnie dla aplikacji. 

W naszym przykładzie parametr SubjectDevice będzie miał stałą listę obsługiwanych wartości bez względu na konwersację.

W przypadku konieczności dodania nowych wpisów do wykazu parametrów na konwersację można wysłać następujące działanie.

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "catalogUpdate": {
      "commandParameterCatalogs": {
        "TurnOnOff": [
          {
            "name": "SubjectDevice",
            "values": {
              "stereo": [
                "cd player"
              ]
            }
          }
        ]
      }
    },
    "processTurn": false
  }
}
```
W przypadku tego działania dodaliśmy wpis "stereo" do wykazu parametru "SubjectDevice" w poleceniu "TurnOnOff".

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="Katalog aktualizacji":::

Zanotuj kilka rzeczy.
1. Wystarczy wysłać to działanie tylko raz (najlepiej po rozpoczęciu połączenia).
1. Po wysłaniu tego działania należy poczekać na wysłanie usługi Event ParameterCatalogsUpdated z powrotem do klienta.

## <a name="add-additional-context-from-the-client-application"></a>Dodawanie dodatkowego kontekstu z aplikacji klienckiej

Można ustawić dodatkowy kontekst z aplikacji klienckiej na konwersację, która może zostać później użyta w aplikacji poleceń niestandardowych. 

Na przykład Pomyśl o scenariuszu, w którym chcesz wysłać identyfikator i nazwę urządzenia połączonego z aplikacją poleceń niestandardowych.

Aby przetestować ten scenariusz, Utwórzmy nowe polecenie w naszej bieżącej aplikacji.
1. Utwórz nowe polecenie o nazwie GetDeviceInfo.
1. Dodaj przykładowe zdanie z "Pobierz informacje o urządzeniu".
1. W regule ukończenia "gotowe" Dodaj akcję Wyślij odpowiedź na mowę, która zawiera atrybuty Klasa ClientContext.
    > ![Wysyłanie odpowiedzi mowy z kontekstem](media/custom-commands/send-speech-response-context.png)
1. Zapisz, Przeszkol i przetestuj aplikację.
1. W oknie testowanie Wyślij działanie, aby zaktualizować kontekst klienta.
    > ```json
    >{
    >   "type": "event",
    >   "name": "RemoteUpdate",
    >   "value": {
    >     "clientContext": {
    >       "deviceId": "12345",
    >       "deviceName": "My device"
    >     },
    >     "processTurn": false
    >   }
    >}
    > ```
1. Wyślij tekst "Pobierz informacje o urządzeniu".
    > ![Wyślij działanie kontekstu klienta](media/custom-commands/send-client-context-activity.png)

Zanotuj kilka rzeczy.
1. Wystarczy wysłać to działanie tylko raz (najlepiej po rozpoczęciu połączenia).
1. Można używać obiektów złożonych dla Klasa ClientContext.
1. W odpowiedziach na mowę można używać Klasa ClientContext do wysyłania działań i podczas wywoływania punktów końcowych sieci Web.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aktualizowanie polecenia z poziomu internetowego punktu końcowego](./how-to-custom-commands-update-command-from-web-endpoint.md)
