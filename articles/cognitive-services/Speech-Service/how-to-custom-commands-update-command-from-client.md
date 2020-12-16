---
title: Aktualizowanie polecenia z poziomu aplikacji klienckiej
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zaktualizować polecenie z aplikacji klienckiej.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: nitinme
ms.openlocfilehash: 08c674a7a7ec060a4273836064cb1c21e979e725
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560291"
---
# <a name="update-a-command-from-a-client-app"></a>Aktualizowanie polecenia z poziomu aplikacji klienckiej

W tym artykule dowiesz się, jak zaktualizować bieżące polecenie z aplikacji klienckiej.

## <a name="prerequisites"></a>Wymagania wstępne
> [!div class = "checklist"]
> * [Utworzona wcześniej aplikacja Polecenia niestandardowe](quickstart-custom-commands-application.md)

## <a name="update-the-state-of-a-command"></a>Aktualizowanie stanu polecenia

Jeśli aplikacja kliencka wymaga zaktualizowania stanu trwającego polecenia bez wprowadzania głosu, można wysłać zdarzenie, aby zaktualizować polecenie.

Aby zilustrować ten scenariusz, wyślij następujące działanie zdarzenia w celu zaktualizowania stanu trwającego polecenia ( `TurnOnOff` ): 

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

Przejrzyjmy kluczowe atrybuty tego działania:

| Atrybut | Wyjaśnienie |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Wprowadź** | Działanie jest typu `"event"` , a nazwa zdarzenia musi być `"RemoteUpdate"` . |
| **wartość** | Ten atrybut `"value"` zawiera atrybuty wymagane do zaktualizowania bieżącego polecenia. |
| **updatedCommand** | Ten atrybut `"updatedCommand"` zawiera nazwę polecenia. W tym atrybucie `"updatedParameters"` jest mapą z nazwami parametrów i ich zaktualizowanymi wartościami. |
| **Anuluj** | Jeśli bieżące polecenie wymaga anulowania, należy ustawić atrybut `"cancel"` na `true` . |
| **updatedGlobalParameters** | Ten atrybut `"updatedGlobalParameters"` jest mapą tak samo `"updatedParameters"` , ale jest używany dla parametrów globalnych. |
| **processTurn** | Jeśli przekształcenie ma być przetwarzane po wysłaniu działania, należy ustawić atrybut `"processTurn"` na `true` . |

Możesz przetestować ten scenariusz w portalu poleceń niestandardowych:

1. Otwórz wcześniej utworzoną aplikację poleceń niestandardowych. 
1. Wybierz pozycję **uczenie** i **Przetestuj**.
1. Wyślij `turn` .
1. Otwórz Panel boczny i wybierz pozycję **Edytor działań**.
1. Wpisz i Wyślij `RemoteCommand` Zdarzenie określone w poprzedniej sekcji.
    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu, który pokazuje zdarzenie dla polecenia zdalnego.](media/custom-commands/send-remote-command-activity.png)

Należy zauważyć, jak wartość parametru `"OnOff"` została ustawiona na `"on"` za pomocą działania z klienta zamiast mowy lub tekstu.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>Aktualizowanie katalogu parametru dla polecenia

Po skonfigurowaniu listy prawidłowych opcji dla parametru, wartości dla parametru są definiowane globalnie dla aplikacji. 

W naszym przykładzie `SubjectDevice` parametr będzie miał stałą listę obsługiwanych wartości niezależnie od rozmowy.

Jeśli chcesz dodać nowe wpisy do wykazu parametrów dla każdej konwersacji, możesz wysłać następujące działanie:

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
W przypadku tego działania dodano wpis `"stereo"` do katalogu parametru `"SubjectDevice"` w poleceniu `"TurnOnOff"` .

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="Zrzut ekranu przedstawiający aktualizację katalogu.":::

Zanotuj kilka rzeczy:
- To działanie należy wysłać tylko raz (najlepiej po rozpoczęciu połączenia).
- Po wysłaniu tego działania należy poczekać na `ParameterCatalogsUpdated` wysłanie zdarzenia z powrotem do klienta.

## <a name="add-more-context-from-the-client-application"></a>Dodaj więcej kontekstu z aplikacji klienckiej

Można ustawić dodatkowy kontekst z aplikacji klienckiej na konwersację, która może zostać później użyta w aplikacji poleceń niestandardowych. 

Na przykład Pomyśl o scenariuszu, w którym chcesz wysłać identyfikator i nazwę urządzenia połączonego z aplikacją poleceń niestandardowych.

Aby przetestować ten scenariusz, Utwórzmy nowe polecenie w bieżącej aplikacji:
1. Utwórz nowe polecenie o nazwie `GetDeviceInfo` .
1. Dodaj przykładowe zdanie `get device info` .
1. W **gotowej** regule ukończenia Dodaj akcję **Wyślij odpowiedź na mowę** , która zawiera atrybuty `clientContext` .
   ![Zrzut ekranu pokazujący odpowiedź na wysyłanie mowy z kontekstem.](media/custom-commands/send-speech-response-context.png)
1. Zapisz, Wyszkol i przetestuj aplikację.
1. W oknie testowanie Wyślij działanie, aby zaktualizować kontekst klienta.

    ```json
    {
       "type": "event",
       "name": "RemoteUpdate",
       "value": {
         "clientContext": {
           "deviceId": "12345",
           "deviceName": "My device"
         },
         "processTurn": false
       }
    }
    ```
1. Wyślij tekst `get device info` .
   ![Zrzut ekranu pokazujący działanie wysyłania kontekstu klienta.](media/custom-commands/send-client-context-activity.png)

Zanotuj kilka rzeczy:
- To działanie należy wysłać tylko raz (najlepiej po rozpoczęciu połączenia).
- W programie można używać obiektów złożonych `clientContext` .
- `clientContext`W odpowiedzi na mowę można używać do wysyłania działań i wywoływania punktów końcowych sieci Web.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aktualizowanie polecenia z poziomu internetowego punktu końcowego](./how-to-custom-commands-update-command-from-web-endpoint.md)
