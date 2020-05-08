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
ms.openlocfilehash: bf1b79c1b5d7b9dfd93b354c6b6ff5a512bb74a5
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858246"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Instrukcje: Dodawanie potwierdzenia do polecenia niestandardowego (wersja zapoznawcza)

W tym artykule dowiesz się, jak dodać potwierdzenie do polecenia.

## <a name="prerequisites"></a>Wymagania wstępne

Należy wykonać czynności opisane w następujących artykułach:
> [!div class="checklist"]
> *  [Szybki Start: Tworzenie polecenia niestandardowego (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-new.md)
> * [Szybki Start: Tworzenie polecenia niestandardowego z parametrami (wersja zapoznawcza)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Utwórz polecenie setAlarm

Aby przedstawić potwierdzenia, Utwórzmy nowe polecenie umożliwiające użytkownikowi ustawienie alarmu.

1. Otwórz wcześniej utworzoną aplikację poleceń niestandardowych w programie [Speech Studio](https://speech.microsoft.com/).
1. Utwórz nowe polecenie `SetAlarm`.
1. Dodaj parametr o nazwie `DateTime` z następującą konfiguracją.

   | Ustawienie                           | Sugerowana wartość                     |  Opis                 |
   | --------------------------------- | -----------------------------------------------------| ------------|
   | Nazwa                              | DateTime                                | Nazwa opisowa parametru                                |
   | Wymagany                          | checked                                 | Pole wyboru wskazujące, czy przed ukończeniem polecenia jest wymagana wartość tego parametru |
   | Odpowiedź dotycząca wymaganego parametru   | Prosty edytor — > jaki czas?                              | Monit o podanie wartości tego parametru, gdy nie jest on znany |
   | Typ                              | DateTime                                | Typ parametru, taki jak Number, String, Data Time lub geography   |
   | Wartości domyślne daty                     | Jeśli brakuje daty, użyj dzisiaj            | Wartość domyślna zmiennej, która ma zostać użyta, jeśli nie została dostarczona przez użytkownika.  |  
   | Ustawienia domyślne czasu                     | W przypadku braku czasu Użyj początku dnia     |  Wartość domyślna zmiennej, która ma zostać użyta, jeśli nie została dostarczona przez użytkownika.|

1. Dodaj przykładowe zdania.
   
    ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Dodaj regułę uzupełniania, aby potwierdzić wynik.

   | Ustawienie    | Sugerowana wartość                               |Opis                                     |
   | ---------- | ------------------------------------------------------- |-----|
   | Nazwa reguły  | Ustaw alarm                                               |    Nazwa opisująca przeznaczenie reguły |
   | Akcje    | Wyślij odpowiedź na mowę — OK, ustaw alarm dla {DateTime}    |Akcja, która ma zostać podjęta po spełnieniu warunku reguły

## <a name="try-it-out"></a>Wypróbowywanie działania

1. Wybierz `Train` ikonę znajdującą się w prawym okienku.

1. Po zakończeniu szkolenia wybierz pozycję `Test`.
    - Wejście: ustaw alarm dla jutro o południe
    - Wynik: OK, ustawiony alarm dla 2020-05-02 12:00:00
    - Dane wejściowe: ustaw alarm
    - Wynik: co godzinę?
    - Dane wejściowe: 17:00
    - Wynik: OK, ustawiony alarm dla 2020-05-01 17:00:00

## <a name="add-the-advanced-rules-for-confirmation"></a>Dodaj zaawansowane reguły dla potwierdzenia

Potwierdzenia są osiągane przez dodanie reguły interakcji.

1. W istniejącym `SetAlarm` poleceniu Dodaj **regułę interakcji** według ikony wyboru `+Add` w środkowym okienku, a następnie wybierając **pozycję Zasady** -> interakcji**Potwierdź polecenie**.

    Ta reguła poprosiła użytkownika o potwierdzenie daty i godziny alarmu i oczekuje potwierdzenia (tak/nie) przy następnym włączeniu.

   | Ustawienie               | Sugerowana wartość                                                                  | Opis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nazwa reguły             | Potwierdź datę i godzinę                                                                | Nazwa opisująca przeznaczenie reguły          |
   | Warunki            | Wymagany parametr — > DateTime                                                    | Warunki określające, kiedy można uruchomić regułę    |   
   | Akcje               | Wyślij odpowiedź na mowę — > czy na pewno chcesz ustawić alarm dla elementu {DateTime}?     | Akcja, która ma zostać podjęta po spełnieniu warunku reguły |
   | Oczekiwania          | Oczekiwanie na potwierdzenie od użytkownika                                                 | Oczekiwanie na następne włączenie                      |
   | Stan po wykonaniu  | Zaczekaj na dane wejściowe użytkownika                                                            | Stan dla użytkownika po włączeniu                  |
  
      > [!div class="mx-imgBorder"]
      > ![Utwórz wymaganą odpowiedź parametru](media/custom-speech-commands/add-validation-set-temperature.png)

1. Dodaj kolejną regułę interakcji, aby obsłużyć pomyślne potwierdzenie (użytkownik ten ma wartość tak)

   | Ustawienie               | Sugerowana wartość                                                                  | Opis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nazwa reguły             | Zaakceptowano potwierdzenie                                                            | Nazwa opisująca przeznaczenie reguły          |
   | Warunki            | Potwierdzenie zakończyło się pomyślnie & wymaganego parametru — > DateTime                      | Warunki określające, kiedy można uruchomić regułę    |   
   | Stan po wykonaniu | Wykonaj reguły uzupełniania                                                          | Stan użytkownika po włączeniu                   |

1. Dodaj zaawansowaną regułę, aby obsłużyć potwierdzenie (nie dotyczy użytkownika)

   | Ustawienie               | Sugerowana wartość                                                                  | Opis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nazwa reguły             | Potwierdzenie odrzucenia                                                                   | Nazwa opisująca przeznaczenie reguły          |
   | Warunki            | Odmowa potwierdzenia & wymaganego parametru — > DateTime                               | Warunki określające, kiedy można uruchomić regułę    |   
   | Akcje               | Wyczyść wartość parametru-> DateTime & wysyłanie odpowiedzi na mowę — > nie masz problemu, co oznacza, że czas?  | Akcja, która ma zostać podjęta po spełnieniu warunku reguły |
   | Stan po wykonaniu | Czekaj na dane wejściowe                                                                   | Stan użytkownika po włączeniu                   |
   | Oczekiwania          | Oczekiwano parametrów wejściowych od użytkownika > DateTime                           | Oczekiwanie na następne włączenie                      |

## <a name="try-out-the-changes"></a>Wypróbuj zmiany

Wybierz `Train`pozycję Zaczekaj na zakończenie szkolenia `Test`i wybierz opcję.

- Wejście: ustaw alarm dla jutro o południe
- Wynik: czy na pewno chcesz ustawić alarm dla 2020-05-02 12:00:00?
- Dane wejściowe: nie
- Dane wyjściowe: nie ma problemu, co oznacza czas?
- Dane wejściowe: 17:00
- Wynik: "czy na pewno chcesz ustawić alarm dla 2020-05-01 17:00:00?"
- Dane wejściowe: tak
- Wynik: OK, ustawiony alarm dla 2020-05-01 17:00:00

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Instrukcje: Dodawanie korekty jednoetapowej do polecenia niestandardowego (wersja zapoznawcza)](./how-to-custom-speech-commands-one-step-correction.md)