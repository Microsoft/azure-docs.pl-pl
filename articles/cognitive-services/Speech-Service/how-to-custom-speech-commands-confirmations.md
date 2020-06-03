---
title: Dodawanie potwierdzeń w poleceniach niestandardowych podgląd aplikacji App-Speech Service
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak dodawać potwierdzenia do poleceń w aplikacji poleceń niestandardowych w wersji zapoznawczej.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 1cb0624012b22b6cae2c98bfa6ddc9495e09615d
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310472"
---
# <a name="add-confirmations-to-a-command-in-a-custom-commands-preview-application"></a>Dodawanie potwierdzeń do polecenia w aplikacji polecenia niestandardowe w wersji zapoznawczej

Ten artykuł zawiera informacje na temat tworzenia potwierdzeń dla poleceń w aplikacji poleceń niestandardowych w wersji zapoznawczej.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w następujących artykułach:
> [!div class="checklist"]
> * [Szybki Start: Tworzenie aplikacji niestandardowych poleceń w wersji zapoznawczej](./quickstart-custom-speech-commands-create-new.md)
> * [Szybki Start: Tworzenie poleceń niestandardowych dla aplikacji w wersji zapoznawczej przy użyciu parametrów](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Utwórz polecenie setAlarm

Aby przedstawić potwierdzenia, Utwórz nowe polecenie, które ustawia alarm.

1. W programie [Speech Studio](https://speech.microsoft.com/)Otwórz utworzoną przez siebie aplikację z poleceniami Custom Preview.
1. Utwórz nowe polecenie **setAlarm** .
1. Dodaj parametr **DateTime** o następującej konfiguracji:

   | Ustawienie                           | Sugerowana wartość                     |  Opis                 |
   | --------------------------------- | -----------------------------------------------------| ------------|
   | **Nazwa**                              | **Data/godzina**                                | Nazwa opisowa parametru                                |
   | **Wymagane**                          | Zaznaczono                                 | Pole wyboru wskazujące, czy wartość tego parametru jest wymagana przed ukończeniem polecenia |
   | **Odpowiedź na wymagany parametr**   | **Prosty edytor — > jaki czas?**                              | Monit o podanie wartości tego parametru, gdy nie jest on znany |
   | **Typ**                              | **Data/godzina**                                | Typ parametru, taki jak Number, String, DateTime lub geography   |
   | **Wartości domyślne daty**                     | Jeśli brakuje daty, użyj dzisiejszej daty            | Wartość domyślna zmiennej, która ma zostać użyta, jeśli nie została dostarczona przez użytkownika  |  
   | **Ustawienia domyślne czasu**                     | Jeśli brakuje czasu, użyj początku dnia     |  Wartość domyślna zmiennej, która ma zostać użyta, jeśli nie została podana przez użytkownika|

1. Dodaj przykładowe zdania.
   
    ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Dodaj regułę uzupełniania, aby potwierdzić wynik. Użyj następującej konfiguracji:

   | Ustawienie    | Sugerowana wartość                               |Opis                                     |
   | ---------- | ------------------------------------------------------- |-----|
   | **Nazwa reguły**  | **Ustaw alarm**                                               |    Nazwa opisująca przeznaczenie reguły |
   | **Akcje**    | **Wyślij odpowiedź na mowę — > OK, ustawiono alarm dla {DateTime}**    |Akcja, która ma zostać podjęta po spełnieniu warunku reguły

## <a name="try-it-out"></a>Wypróbowywanie działania

1. Wybierz pozycję **uczenie** w górnej części okienka po prawej stronie.

1. Po zakończeniu szkolenia wybierz pozycję **test**, a następnie spróbuj wykonać następujące interakcje:
    - Wejście: ustaw alarm dla jutro o południe
    - Wynik: OK, ustawiony alarm dla 2020-05-02 12:00:00
    - Dane wejściowe: ustaw alarm
    - Wynik: co godzinę?
    - Dane wejściowe: 17:00
    - Wynik: OK, ustawiony alarm dla 2020-05-01 17:00:00

## <a name="add-interaction-rules-for-the-confirmation"></a>Dodaj reguły interakcji dla potwierdzenia

Utwórz potwierdzenia, dodając reguły interakcji.

1. W poleceniu **setAlarm** wybierz pozycję **Dodaj** w środkowym okienku, a następnie wybierz pozycję Potwierdź **reguły interakcji**  >  **polecenie**.

    Ta reguła prosi użytkownika o potwierdzenie daty i godziny alarmu. Użyj następujących ustawień:

   | Ustawienie               | Sugerowana wartość                                                                  | Opis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Nazwa reguły**             | **Potwierdź datę i godzinę**                                                                | Nazwa opisująca przeznaczenie reguły          |
   | **Warunki**            | **Wymagany parametr — > DateTime**                                                    | Warunki określające, kiedy można uruchomić regułę    |   
   | **Akcje**               | **Wyślij odpowiedź na mowę — > czy na pewno chcesz ustawić alarm dla elementu {DateTime}?**     | Akcja, która ma zostać podjęta po spełnieniu warunku reguły |
   | **Oczekiwania**          | **Oczekiwanie na potwierdzenie od użytkownika**                                                 | Oczekiwanie na następne włączenie                      |
   | **Stan po wykonaniu**  | **Zaczekaj na dane wejściowe użytkownika**                                                            | Stan dla użytkownika po włączeniu                  |
  
      > [!div class="mx-imgBorder"]
      > ![Utwórz wymaganą odpowiedź parametru](media/custom-speech-commands/add-validation-set-temperature.png)

1. Dodaj regułę interakcji dla zaakceptowanego potwierdzenia (użytkownik powiedział "yes"). Użyj następującej konfiguracji:

   | Ustawienie               | Sugerowana wartość                                                                  | Opis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Nazwa reguły**             | **Zaakceptowano potwierdzenie**                                                            | Nazwa opisująca przeznaczenie reguły          |
   | **Warunki**            | **Potwierdzenie zakończyło się pomyślnie & wymaganego parametru — > DateTime**                      | Warunki określające, kiedy można uruchomić regułę    |   
   | **Stan po wykonaniu** | **Wykonaj reguły uzupełniania**                                                          | Stan użytkownika po włączeniu                   |

1. Dodaj regułę interakcji dla potwierdzenia odmowy (użytkownik powiedział "No"). Użyj następującej konfiguracji:

   | Ustawienie               | Sugerowana wartość                                                                  | Opis                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Nazwa reguły**             | **Potwierdzenie odrzucenia**                                                                   | Nazwa opisująca przeznaczenie reguły          |
   | **Warunki**            | **Odmowa potwierdzenia & wymaganego parametru — > DateTime**                               | Warunki określające, kiedy można uruchomić regułę    |   
   | **Akcje**               | **Wyczyść wartość parametru-> DateTime & wysyłanie odpowiedzi na mowę — > nie masz problemu, co oznacza, że czas?**  | Akcja, która ma zostać podjęta po spełnieniu warunku reguły |
   | **Stan po wykonaniu** | **Czekaj na dane wejściowe**                                                                   | Stan użytkownika po włączeniu                   |
   | **Oczekiwania**          | **Oczekiwane dane wejściowe parametrów od użytkownika > DateTime**                           | Oczekiwanie na następne włączenie                      |

## <a name="try-out-the-changes"></a>Wypróbuj zmiany

1. Wybierz pozycję **uczenie**.

1. Po zakończeniu szkolenia wybierz pozycję **test**, a następnie spróbuj wykonać następujące interakcje:

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
> [Dodawanie jednoetapowej korekty do polecenia w aplikacji polecenia niestandardowe w wersji zapoznawczej](./how-to-custom-speech-commands-one-step-correction.md)
