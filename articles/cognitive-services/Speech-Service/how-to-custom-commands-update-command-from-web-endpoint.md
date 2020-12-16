---
title: Aktualizowanie polecenia z poziomu internetowego punktu końcowego
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zaktualizować stan polecenia, używając wywołania do punktu końcowego w sieci Web.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: nitinme
ms.openlocfilehash: d0b77e6af36f0a71405f6c032bfdd121abeb0071
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560274"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Aktualizowanie polecenia z poziomu internetowego punktu końcowego

Jeśli aplikacja kliencka wymaga aktualizacji stanu trwającego polecenia bez wprowadzania głosu, można użyć wywołania do punktu końcowego sieci Web, aby zaktualizować polecenie.

W tym artykule dowiesz się, jak zaktualizować bieżące polecenie z punktu końcowego sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne
> [!div class = "checklist"]
> * [Utworzona wcześniej aplikacja Polecenia niestandardowe](quickstart-custom-commands-application.md)

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure 

Na potrzeby tego przykładu wymagana jest [Funkcja platformy Azure](../../azure-functions/index.yml) wyzwalana przez protokół http, która obsługuje następujące dane wejściowe (lub podzestaw tego wejścia):

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

Przejrzyjmy kluczowe atrybuty tego danych wejściowych:

| Atrybut | Wyjaśnienie |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | Unikatowy identyfikator konwersacji. Należy pamiętać, że ten identyfikator może być generowany z poziomu aplikacji klienckiej. |
| **currentCommand** | Polecenie, które jest aktualnie aktywne w konwersacji. |
| **Nazwij** | Nazwa polecenia. Ten `parameters` atrybut jest mapą z bieżącymi wartościami parametrów. |
| **currentGlobalParameters** | Mapa, taka jak `parameters` , ale używana dla parametrów globalnych. |

Dane wyjściowe funkcji platformy Azure muszą obsługiwać następujący format:

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

Teraz Utwórz funkcję platformy Azure na podstawie Node.js. Kopiuj/wklej ten kod:

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

Gdy wywołasz tę funkcję platformy Azure z poleceń niestandardowych, będziesz wysyłać bieżące wartości w konwersacji. Zwracasz parametry, które chcesz zaktualizować, lub jeśli chcesz anulować bieżące polecenie.

## <a name="update-the-existing-custom-commands-app"></a>Aktualizowanie istniejącej aplikacji poleceń niestandardowych

Przejdźmy do funkcji platformy Azure za pomocą istniejącej aplikacji poleceń niestandardowych:

1. Dodaj nowe polecenie o nazwie `IncrementCounter` .
1. Dodaj tylko jedno przykładowe zdanie z wartością `increment` .
1. Dodaj nowy parametr o nazwie `Counter` (o nazwie określonej w funkcji platformy Azure) typu `Number` z wartością domyślną `0` .
1. Dodaj nowy internetowy punkt końcowy o nazwie `IncrementEndpoint` przy użyciu adresu URL funkcji platformy Azure z **włączonymi** **aktualizacjami zdalnymi** .
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="Zrzut ekranu przedstawiający Ustawianie punktu końcowego sieci Web przy użyciu aktualizacji zdalnych.":::
1. Utwórz nową regułę interakcji o nazwie **IncrementRule** i Dodaj akcję **wywołania punktu końcowego dla sieci Web** .
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="Zrzut ekranu przedstawiający tworzenie reguły interakcji.":::
1. W obszarze Konfiguracja akcji wybierz opcję `IncrementEndpoint` . Skonfiguruj **na sukces** , aby **wysłać odpowiedź mowy** o wartości `Counter` i skonfigurować w przypadku **niepowodzenia** z komunikatem o błędzie.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="Zrzut ekranu przedstawiający Ustawianie licznika przyrostu do wywoływania punktu końcowego sieci Web.":::
1. Ustaw stan po wykonaniu reguły, aby **czekać na dane wejściowe użytkownika**.

## <a name="test-it"></a>Testowanie

1. Zapisz i Wyszkol aplikację.
1. Kliknij przycisk **Testuj**.
1. Wyślij `increment` kilka razy (czyli przykładowe zdanie dla `IncrementCounter` polecenia).
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="Zrzut ekranu pokazujący przykład licznika przyrostu.":::

Zwróć uwagę, jak funkcja platformy Azure zwiększa wartość `Counter` parametru przy każdym włączeniu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Włączanie procesu ciągłej integracji/ciągłego wdrażania dla aplikacji Polecenia niestandardowe](./how-to-custom-commands-deploy-cicd.md)