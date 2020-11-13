---
title: Aktualizowanie polecenia z punktu końcowego sieci Web
titleSuffix: Azure Cognitive Services
description: Aktualizowanie polecenia z punktu końcowego sieci Web
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 4432843ac93002bc92068db191706352234d76e6
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94571252"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Aktualizowanie polecenia z punktu końcowego sieci Web

Jeśli aplikacja kliencka wymaga zaktualizowania stanu trwającego polecenia bez wprowadzania głosu, można użyć połączenia z punktem końcowym sieci Web, aby zaktualizować polecenie.

W tym artykule dowiesz się, jak zaktualizować bieżące polecenie z punktu końcowego sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne
> [!div class = "checklist"]
> * [Utworzona wcześniej aplikacja Polecenia niestandardowe](quickstart-custom-commands-application.md)

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure 

Na potrzeby tego przykładu będziemy potrzebować HTTP-Triggered [funkcji platformy Azure](https://docs.microsoft.com/azure/azure-functions/) , która obsługuje następujące dane wejściowe (lub podzestaw tego wejścia).

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

Umożliwia przejrzenie kluczowych atrybutów tych danych wejściowych.

| Atrybut | Wyjaśnienie |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | "conversationId" jest unikatowym identyfikatorem konwersacji, pamiętaj, że ten identyfikator może być generowany z poziomu aplikacji klienckiej. |
| **currentCommand** | "currentCommand" to polecenie aktualnie aktywne w konwersacji. |
| **Nazwij** | "name" to nazwa polecenia, a parametr "Parameters" to mapa z bieżącymi wartościami parametrów. |
| **currentGlobalParameters** | "currentGlobalParameters" to również mapa, taka jak "Parameters", ale jest używana dla parametrów globalnych. |

Dane wyjściowe funkcji platformy Azure muszą obsługiwać następujący format.

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

Ten format można rozpoznać, ponieważ jest to ten sam, który był używany podczas [aktualizowania polecenia z klienta](./how-to-custom-commands-update-command-from-client.md)programu. 

Teraz Utwórz funkcję platformy Azure na podstawie NodeJS i skopiuj ten kod

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

Gdy wywołamy tę funkcję platformy Azure z poleceń niestandardowych, wyślemy bieżące wartości rozmowy i będziemy zwracały parametry, które chcemy zaktualizować, lub jeśli chcemy anulować bieżące polecenie.

## <a name="update-the-existing-custom-commands-app"></a>Aktualizowanie istniejącej aplikacji poleceń niestandardowych

Teraz przejdźmy do funkcji platformy Azure za pomocą istniejącej aplikacji poleceń niestandardowych.

1. Dodaj nowe polecenie o nazwie IncrementCounter.
1. Dodaj tylko jedno przykładowe zdanie z wartością "Zwiększ".
1. Dodaj nowy parametr o nazwie Counter (o nazwie określonej w funkcji platformy Azure powyżej) typu Number z wartością domyślną 0.
1. Dodaj nowy internetowy punkt końcowy o nazwie IncrementEndpoint z adresem URL funkcji platformy Azure i z włączonymi aktualizacjami zdalnymi.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="Ustawianie punktu końcowego sieci Web przy użyciu aktualizacji zdalnych":::
1. Utwórz nową regułę interakcji o nazwie "IncrementRule" i Dodaj akcję wywołania punktu końcowego sieci Web.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="Reguła przyrostu":::
1. W obszarze Konfiguracja akcji wybierz IncrementEndpoint, skonfiguruj na pomyślne wysłanie odpowiedzi mowy z wartością licznika i w przypadku niepowodzenia z komunikatem o błędzie.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="Ustaw punkt końcowy wywołania licznika przyrostowego":::
1. Ustawianie stanu po wykonaniu reguły w celu zaczekania na dane wejściowe użytkownika

## <a name="test-it"></a>Testowanie

1. Zapisywanie i uczenie aplikacji
1. Kliknij przycisk Testuj
1. Wyślij kilka razy "przyrostka" (jest to przykładowe zdanie dla polecenia IncrementCounter)
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="Przykład licznika przyrostu":::

Zwróć uwagę, jak wartość parametru licznika jest zwiększana przy każdym włączeniu funkcji platformy Azure.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Włączanie procesu ciągłej integracji/ciągłego wdrażania dla aplikacji Polecenia niestandardowe](./how-to-custom-commands-deploy-cicd.md)