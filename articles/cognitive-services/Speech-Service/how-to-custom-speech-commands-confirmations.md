---
title: 'Instrukcje: Dodawanie potwierdzenia do polecenia niestandardowego (wersja zapoznawcza)'
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano sposób implementowania potwierdzeń dla polecenia w poleceniach niestandardowych.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: afa197c83b4f66f12863de4185ef7763447f3ed9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75456499"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Instrukcje: Dodawanie potwierdzenia do polecenia niestandardowego (wersja zapoznawcza)

W tym artykule dowiesz się, jak dodać potwierdzenie do polecenia.

## <a name="prerequisites"></a>Wymagania wstępne

Należy wykonać czynności opisane w następujących artykułach:

- [Szybki Start: Tworzenie polecenia niestandardowego (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-new.md)
- [Szybki Start: Tworzenie polecenia niestandardowego z parametrami (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Utwórz polecenie setAlarm

Aby zademonstrować walidacje, Utwórzmy nowe polecenie umożliwiające użytkownikowi ustawienie alarmu.

1. Otwieranie wcześniej utworzonych aplikacji poleceń niestandardowych w programie [Speech Studio](https://speech.microsoft.com/)
1. Utwórz nowe polecenie **setAlarm**
1. Dodaj parametr o nazwie DateTime

   | Ustawienie           | Sugerowana wartość                                          | Opis                                                                                      |
   | ----------------- | ---------------------------------------------------------| ------------------------------------------------------------------------------------------------ |
   | Nazwa              | DateTime                                                 | Opisowa nazwa parametru polecenia                                                    |
   | Wymagany          | true                                                     | Pole wyboru wskazujące, czy przed ukończeniem polecenia jest wymagana wartość tego parametru |
   | Szablon odpowiedzi | "— Co godzinę?"                                           | Monit o podanie wartości tego parametru, gdy nie jest on znany                              |
   | Typ              | DateTime                                                 | Typ parametru, taki jak Number, String lub Data Time                                      |
   | Wartości domyślne daty     | Jeśli brakuje daty, użyj dzisiaj                             |                                                                                                  |
   | Ustawienia domyślne czasu     | W przypadku braku czasu Użyj początku dnia                      |                                                                                                  | 

1. Dodaj przykładowe zdania
   
   ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Dodawanie reguły uzupełniania w celu potwierdzenia wyniku

   | Ustawienie    | Sugerowana wartość                                         | Opis                                        |
   | ---------- | ------------------------------------------------------- | -------------------------------------------------- |
   | Nazwa reguły  | Ustaw alarm                                               | Nazwa opisująca przeznaczenie reguły          |
   | Akcje    | SpeechResponse-"-OK, zestaw alarmowy dla {DateTime}"       | Akcja, która ma zostać podjęta po spełnieniu warunku reguły |

## <a name="try-it-out"></a>Wypróbowywanie działania

Wybierz panel testowania i wypróbuj kilka interakcji.

- Wejście: ustaw alarm dla jutro o południe
- Wynik: "OK, ustawiony alarm dla 12/06/2019 12:00:00"

- Dane wejściowe: ustaw alarm
- Wynik: "co godzinę?"
- Dane wejściowe: 17:00
- Wynik: "OK, ustawiony alarm dla 12/05/2019 17:00:00"

## <a name="add-the-advanced-rules-for-confirmation"></a>Dodaj zaawansowane reguły dla potwierdzenia

1. Dodaj zaawansowaną regułę do potwierdzenia. 

    Ta reguła poprosiła użytkownika o potwierdzenie daty i godziny alarmu i oczekuje potwierdzenia (tak/nie) przy następnym włączeniu.

   | Ustawienie               | Sugerowana wartość                                                                  | Opis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nazwa reguły             | Potwierdź datę i godzinę                                                                | Nazwa opisująca przeznaczenie reguły          |
   | Warunki            | Wymagany parametr-DateTime                                                    | Warunki określające, kiedy można uruchomić regułę    |   
   | Akcje               | SpeechResponse-"-czy na pewno chcesz ustawić alarm dla elementu {DateTime}?"       | Akcja, która ma zostać podjęta po spełnieniu warunku reguły |
   | Stan po wykonaniu | Czekaj na dane wejściowe                                                                   | Stan dla użytkownika po włączeniu                  |
   | Oczekiwania          | Potwierdzenie                                                                     | Oczekiwanie na następne włączenie                      |

1. Dodaj zaawansowaną regułę, która będzie obsługiwać pomyślne potwierdzenie (użytkownik ten ma wartość tak)

   | Ustawienie               | Sugerowana wartość                                                                  | Opis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nazwa reguły             | Zaakceptowano potwierdzenie                                                            | Nazwa opisująca przeznaczenie reguły          |
   | Warunki            | SuccessfulConfirmation & wymagany parametr-DateTime                           | Warunki określające, kiedy można uruchomić regułę    |   
   | Stan po wykonaniu | Gotowe do ukończenia                                                             | Stan użytkownika po włączeniu                   |

1. Dodaj zaawansowaną regułę, aby obsłużyć potwierdzenie (nie dotyczy użytkownika)

   | Ustawienie               | Sugerowana wartość                                                                  | Opis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nazwa reguły             | Potwierdzenie odrzucenia                                                                   | Nazwa opisująca przeznaczenie reguły          |
   | Warunki            | DeniedConfirmation & wymagany parametr-DateTime                               | Warunki określające, kiedy można uruchomić regułę    |   
   | Akcje               | ClearParameter-DateTime & SpeechResponse-"-Brak problemu, co oznacza czas?"     | Akcja, która ma zostać podjęta po spełnieniu warunku reguły |
   | Stan po wykonaniu | Czekaj na dane wejściowe                                                                   | Stan użytkownika po włączeniu                   |
   | Oczekiwania          | ElicitParameters — Data i godzina                                                      | Oczekiwanie na następne włączenie                      |

## <a name="try-it-out"></a>Wypróbowywanie działania

Wybierz panel testowania i wypróbuj kilka interakcji.

- Wejście: ustaw alarm dla jutro o południe
- Wynik: "czy na pewno chcesz ustawić alarm dla 12/07/2019 12:00:00?"
- Dane wejściowe: nie
- Wynik: "nie ma problemu, co oznacza czas?"
- Dane wejściowe: 17:00
- Wynik: "czy na pewno chcesz ustawić alarm dla 12/06/2019 17:00:00?"
- Dane wejściowe: tak
- Wynik: "OK, ustawiony alarm dla 12/06/2019 17:00:00"

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Instrukcje: Dodawanie korekty jednoetapowej do polecenia niestandardowego (wersja zapoznawcza)](./how-to-custom-speech-commands-one-step-correction.md)