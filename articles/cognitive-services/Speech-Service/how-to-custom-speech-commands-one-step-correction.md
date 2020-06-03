---
title: Dodawanie korekty jednoetapowej w przypadku poleceń niestandardowych wersja zapoznawcza — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak dodać poprawkę jednoetapową dla polecenia w aplikacji polecenia niestandardowe w wersji zapoznawczej.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 05f63ba4e70f649df33905f1e92fb1fab866a86c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310431"
---
# <a name="add-a-one-step-correction-to-a-custom-command-in-a-custom-commands-preview-application"></a>Dodawanie jednoetapowej korekty do polecenia niestandardowego w aplikacji w wersji zapoznawczej poleceń niestandardowych

W tym artykule dowiesz się, jak dodać potwierdzenie jednoetapowe do polecenia w aplikacji polecenia niestandardowe w wersji zapoznawczej.

Poprawka jednoetapowa służy do aktualizowania polecenia, które zostało właśnie zakończone. Po dodaniu korekty jednoetapowej do polecenia alarmu można zmienić zdanie i zaktualizować czas alarmu. Na przykład:

- Wejście: ustaw alarm dla jutro o południe
- Wynik: OK, ustawiony alarm dla 2020-05-02 12:00:00
- Dane wejściowe: nie, jutro o 1pm
- Wynik: ok

> [!NOTE]
> W rzeczywistym scenariuszu klient wykonuje akcję w wyniku wykonania polecenia. W tym artykule przyjęto założenie, że masz mechanizm aktualizowania alarmu w aplikacji zaplecza.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w następujących artykułach:
> [!div class="checklist"]

> * [Szybki Start: Tworzenie aplikacji niestandardowych poleceń w wersji zapoznawczej](./quickstart-custom-speech-commands-create-new.md)
> * [Szybki Start: Tworzenie poleceń niestandardowych dla aplikacji w wersji zapoznawczej przy użyciu parametrów](./quickstart-custom-speech-commands-create-parameters.md)
> * [Instrukcje: Dodawanie potwierdzeń do polecenia w aplikacji polecenia niestandardowe w wersji zapoznawczej](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-interaction-rules-for-one-step-correction"></a>Dodawanie reguł interakcji dla korekty jednoetapowej

Aby przedstawić korekcję jednoetapową, należy zwiększyć polecenie **setAlarm** , które zostało utworzone w [sposób: Dodawanie potwierdzenia do polecenia w podglądzie poleceń niestandardowych](./how-to-custom-speech-commands-confirmations.md).

1. Dodaj regułę interakcji, aby zaktualizować polecenie **setAlarm** .

    Ta reguła monituje użytkownika o potwierdzenie daty i godziny alarmu i oczekuje potwierdzenia (tak/nie) w następnym przypadku.

   | Ustawienie               | Sugerowana wartość                                                  | Opis                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | **Nazwa reguły**             | **Aktualizuj poprzedni alarm**                                            | Nazwa opisująca przeznaczenie reguły          |
   | **Warunki**            | **Poprzednie polecenie należy zaktualizować & wymaganego parametru — > DateTime**                | Warunki określające, kiedy można uruchomić regułę    |   
   | **Akcje**               | **Wyślij odpowiedź na mowę — > prostych edytorów > aktualizowanie poprzedniego alarmu do wartości {DateTime}**      | Akcja do wykonania, gdy warunki reguły są spełnione |
   | **Stan po wykonaniu** | **Ukończono polecenie**        | Stan użytkownika po włączeniu                   |

1. W okienku wybierz właśnie utworzoną regułę interakcji. Wybierz przycisk wielokropka (**...**) w lewym górnym rogu okienka. Następnie użyj strzałki **Przenieś w górę** , aby przenieść regułę do góry listy **reguły interakcji** .
   > [!div class="mx-imgBorder"]
   > ![Dodawanie walidacji zakresu](media/custom-speech-commands/one-step-correction-rules.png)

    > [!NOTE]
    > W rzeczywistej aplikacji należy użyć sekcji **Actions** , aby wysłać do klienta działanie z powrotem, lub wywołać punkt końcowy HTTP w celu zaktualizowania alarmu w systemie.

## <a name="try-it-out"></a>Wypróbowywanie działania

1. Wybierz pozycję **uczenie**.

1. Po zakończeniu szkolenia wybierz pozycję **test**, a następnie spróbuj wykonać następujące interakcje:

   - Wejście: ustaw alarm dla jutro o południe
   - Wynik: czy na pewno chcesz ustawić alarm dla 2020-05-02 12:00:00
   - Dane wejściowe: tak
   - Wynik: OK, ustawiony alarm dla 2020-05-02 12:00:00
   - Dane wejściowe: nie, jutro o 2pm
   - Dane wyjściowe: aktualizowanie poprzedniego alarmu do 2020-05-02 14:00:00
