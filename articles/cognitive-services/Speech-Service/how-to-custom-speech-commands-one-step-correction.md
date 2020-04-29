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
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75456455"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Instrukcje: Dodawanie korekty jednoetapowej do polecenia niestandardowego (wersja zapoznawcza)

W tym artykule dowiesz się, jak dodać potwierdzenie jednoetapowe do polecenia.

Poprawka jednoetapowa służy do aktualizowania polecenia, które zostało właśnie zakończone.

Oznacza to, że w przypadku skonfigurowania alarmu można zmienić zdanie i zaktualizować czas alarmu.

- Wejście: ustaw alarm dla jutro o południe
- Wynik: "OK, ustawiony alarm dla 12/06/2019 12:00:00"
- Dane wejściowe: nie, jutro o 1pm
- Wynik: "OK

Należy pamiętać, że jest to oznacza, że deweloper ma mechanizm aktualizowania alarmu w aplikacji zaplecza.

## <a name="prerequisites"></a>Wymagania wstępne

Należy wykonać czynności opisane w następujących artykułach:

- [Szybki Start: Tworzenie polecenia niestandardowego (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-new.md)
- [Szybki Start: Tworzenie polecenia niestandardowego z parametrami (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-parameters.md)
- [Instrukcje: Dodawanie potwierdzenia do polecenia niestandardowego (wersja zapoznawcza)](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>Dodaj zaawansowane reguły dla korekty jednoetapowej 

Aby zademonstrować korektę jednoetapową, przyciągnijmy polecenie **setAlarm** , które zostało utworzone w sekcji [potwierdzenia](./how-to-custom-speech-commands-confirmations.md).
 
1. Dodaj zaawansowaną regułę, aby zaktualizować poprzedni alarm. 

    Ta reguła poprosiła użytkownika o potwierdzenie daty i godziny alarmu i oczekuje potwierdzenia (tak/nie) przy następnym włączeniu.

   | Ustawienie               | Sugerowana wartość                                                  | Opis                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Nazwa reguły             | Aktualizuj poprzedni alarm                                            | Nazwa opisująca przeznaczenie reguły          |
   | Warunki            | UpdateLastCommand & wymagany parametr-DateTime                | Warunki określające, kiedy można uruchomić regułę    |   
   | Akcje               | SpeechResponse-"-Aktualizowanie poprzedniego alarmu do {DateTime}"       | Akcja, która ma zostać podjęta po spełnieniu warunku reguły |
   | Stan po wykonaniu | Ukończ polecenie                                                 | Stan użytkownika po włączeniu                   |

1. Przenieś utworzoną właśnie regułę na początek reguł zaawansowanych (przewiń regułę w panelu i kliknij strzałkę w górę).
   > [!div class="mx-imgBorder"]
   > ![Dodawanie walidacji zakresu](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> W rzeczywistej aplikacji w sekcji działania tej reguły zostanie wysłane również działanie do klienta lub wywołanie punktu końcowego HTTP w celu zaktualizowania alarmu w systemie.

## <a name="try-it-out"></a>Wypróbowywanie działania

Wybierz panel testowania i wypróbuj kilka interakcji.

- Wejście: ustaw alarm dla jutro o południe
- Wynik: "czy na pewno chcesz ustawić alarm dla 12/07/2019 12:00:00?"
- Dane wejściowe: tak
- Wynik: "OK, ustawiony alarm dla 12/07/2019 12:00:00"
- Dane wejściowe: nie, jutro o 1pm
- Dane wyjściowe: "Aktualizowanie poprzedniego alarmu do 12/07/2019 13:00:00"
