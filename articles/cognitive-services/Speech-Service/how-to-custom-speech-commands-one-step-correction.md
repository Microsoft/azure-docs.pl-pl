---
title: 'Instrukcje: Dodawanie korekty jednoetapowej do polecenia niestandardowego (wersja zapoznawcza) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak zaimplementować jednoetapowe poprawki dla polecenia w poleceniach niestandardowych.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: f43c28d314cb8a0211496664cd20d2c380e4d5b0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858276"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Instrukcje: Dodawanie korekty jednoetapowej do polecenia niestandardowego (wersja zapoznawcza)

W tym artykule dowiesz się, jak dodać potwierdzenie jednoetapowe do polecenia.

Poprawka jednoetapowa służy do aktualizowania polecenia, które zostało właśnie zakończone. Oznacza to, że jeśli po prostu skonfigurujesz alarm, możesz zmienić zdanie i zaktualizować czas alarmu. Przykładem takiej sytuacji jest:

- Wejście: ustaw alarm dla jutro o południe
- Wynik: OK, ustawiony alarm dla 2020-05-02 12:00:00
- Dane wejściowe: nie, jutro o 1pm
- Wynik: ok

Prawdziwy scenariusz, który jest ogólnie dołączony przez klienta wykonującego akcję w wyniku wykonania polecenia — w tym artykule przyjęto założenie, że deweloper ma mechanizm aktualizowania alarmu w aplikacji zaplecza.

## <a name="prerequisites"></a>Wymagania wstępne

Należy wykonać czynności opisane w następujących artykułach:
> [!div class="checklist"]

> * [Szybki Start: Tworzenie polecenia niestandardowego (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-new.md)
> * [Szybki Start: Tworzenie polecenia niestandardowego z parametrami (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-parameters.md)
> * [Instrukcje: Dodawanie potwierdzenia do polecenia niestandardowego (wersja zapoznawcza)](./how-to-custom-speech-commands-confirmations.md)


## <a name="add-interaction-rules-for-one-step-correction"></a>Dodawanie reguł interakcji dla korekty jednoetapowej 

Aby zademonstrować korektę jednoetapową, należy przyciągnąćmy polecenie **setAlarm** , które zostało utworzone w [sposób: Dodawanie potwierdzenia do polecenia niestandardowego (wersja zapoznawcza)](./how-to-custom-speech-commands-confirmations.md).
1. Dodaj regułę interakcji, aby zaktualizować wcześniej ustawiony alarm. 

    Ta reguła poprosiła użytkownika o potwierdzenie daty i godziny alarmu i oczekuje potwierdzenia (tak/nie) przy następnym włączeniu.

   | Ustawienie               | Sugerowana wartość                                                  | Opis                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Nazwa reguły             | Aktualizuj poprzedni alarm                                            | Nazwa opisująca przeznaczenie reguły          |
   | Warunki            | Poprzednie polecenie należy zaktualizować & wymaganego parametru — > DateTime                | Warunki określające, kiedy można uruchomić regułę    |   
   | Akcje               | Wyślij odpowiedź na mowę — > prostych edytorów > aktualizowanie poprzedniego alarmu do wartości {DateTime}      | Akcja do wykonania, gdy warunki reguły są spełnione |
   | Stan po wykonaniu | Ukończono polecenie        | Stan użytkownika po włączeniu                   |

1. Przenieś utworzoną właśnie regułę na górę reguł interakcji (Wybierz regułę w panelu i kliknij strzałkę w górę w obszarze `...` ikona w górnej części środkowego okienka).
   > [!div class="mx-imgBorder"]
   > ![Dodawanie walidacji zakresu](media/custom-speech-commands/one-step-correction-rules.png)
    > [!NOTE]
    > W rzeczywistej aplikacji w sekcji działania tej reguły zostanie wysłane również działanie do klienta lub wywołanie punktu końcowego HTTP w celu zaktualizowania alarmu w systemie.

## <a name="try-it-out"></a>Wypróbowywanie działania

Wybierz `Train`pozycję, poczekaj na zakończenie szkolenia `Test`i wybierz pozycję.

- Wejście: ustaw alarm dla jutro o południe
- Wynik: czy na pewno chcesz ustawić alarm dla 2020-05-02 12:00:00
- Dane wejściowe: tak
- Wynik: OK, ustawiony alarm dla 2020-05-02 12:00:00
- Dane wejściowe: nie, jutro o 2pm
- Dane wyjściowe: aktualizowanie poprzedniego alarmu do 2020-05-02 14:00:00
