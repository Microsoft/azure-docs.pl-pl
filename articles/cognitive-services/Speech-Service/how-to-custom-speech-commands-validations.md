---
title: 'Instrukcje: Dodawanie walidacji do parametrów polecenia niestandardowego'
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak dodać walidacje do parametru w poleceniach niestandardowych.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 2b7fd608156ab269cfc0c85c6c508fa9d5eebc83
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857193"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Instrukcje: Dodawanie walidacji do parametrów polecenia niestandardowego (wersja zapoznawcza)

W tym artykule opisano Dodawanie walidacji do parametrów i wyświetlanie wierszy poprawek.

## <a name="prerequisites"></a>Wymagania wstępne

Należy wykonać czynności opisane w następujących artykułach:

> [!div class="checklist"]
> * [Szybki Start: Tworzenie polecenia niestandardowego](./quickstart-custom-speech-commands-create-new.md)
> * [Szybki Start: Tworzenie polecenia niestandardowego z parametrami](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Utwórz polecenie settemperaturę

Aby zademonstrować walidacje, Utwórzmy nowe polecenie umożliwiające użytkownikom ustawianie temperatury.

1. Otwieranie wcześniej utworzonych aplikacji poleceń niestandardowych w programie [Speech Studio](https://speech.microsoft.com/)
1. Utwórz nowe polecenie`SetTemperature`
1. Dodaj parametr dla temperatury docelowej.

   | Konfiguracja parametrów           | Sugerowana wartość    |Opis                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | Nazwa              | Temperatura                       | Nazwa opisowa parametru                                |
   | Wymagany          | checked                           | Pole wyboru wskazujące, czy przed ukończeniem polecenia jest wymagana wartość tego parametru |
   | Odpowiedź dotycząca wymaganego parametru     | Prosty edytor — > jakiej temperatury chcesz?  | Monit o podanie wartości tego parametru, gdy nie jest on znany |
   | Typ              | Liczba                            | Typ parametru, taki jak Number, String, Data Time lub geography   |

1. Dodaj weryfikację parametru temperatury.

    - Na stronie Konfiguracja **parametrów** dla `Temperature` parametru wybierz pozycję `Add a validation` z sekcji walidacji.
    - Wypełnij wartości w oknie podręcznym **nowej walidacji** , a następnie wybierz pozycję **Utwórz**.

  
       | Konfiguracja parametrów         | Sugerowana wartość                                          | Opis                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | Wartość minimalna        | 60               | Dla parametrów liczbowych minimalna wartość, którą może przyjąć ten parametr |
       | Wartość maksymalna        | 80               | Dla parametrów liczbowych wartość maksymalna, którą może przyjąć ten parametr |
       | Niepowodzenie — prosty edytor| Pierwsza odmiana — Niestety, można ustawić tylko między 60 i 80 stopni      | Monituj o podanie nowej wartości, Jeśli weryfikacja nie powiedzie się                                       |

       > [!div class="mx-imgBorder"]
       > ![Dodawanie walidacji zakresu](media/custom-speech-commands/validations-add-temperature.png)

1. Dodaj przykładowe zdania

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Dodawanie reguły uzupełniania w celu potwierdzenia wyniku

   | Ustawienie    | Sugerowana wartość                                           |Opis                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | Nazwa       | Komunikat z potwierdzeniem                                      |Nazwa opisująca przeznaczenie reguły |
   | Warunki | Wymagane parametry —`Temperature`                       |Warunki określające, kiedy można uruchomić regułę    |   
   | Akcje    | Wyślij odpowiedź na mowę —`Ok, setting temperature to {Temperature} degrees` | Akcja, która ma zostać podjęta po spełnieniu warunku reguły |

> [!TIP]
> Ten przykład używa odpowiedzi mowy, aby potwierdzić wynik. Aby zapoznać się z przykładami dotyczącymi kończenia polecenia z akcją klienta, zobacz: [jak: zrealizować polecenia na kliencie przy użyciu zestawu Speech SDK](./how-to-custom-speech-commands-fulfill-sdk.md)


## <a name="try-it-out"></a>Wypróbowywanie działania
1. Wybierz `Train` ikonę znajdującą się w prawym okienku.

1. Po zakończeniu szkolenia wybierz `Test` i wypróbuj kilka interakcji.

    - Dane wejściowe: Ustaw temperaturę na 72 stopni
    - Wynik: OK, ustawianie temperatury na 72 stopni
    - Dane wejściowe: Ustaw temperaturę na 45 stopni
    - Wynik: Niestety, można ustawić tylko między 60 i 80 stopni
    - Dane wejściowe: Zwiększ do 72 stopni.
    - Wynik: OK, ustawianie temperatury na 72 stopni

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Instrukcje: Dodawanie potwierdzenia do polecenia niestandardowego (wersja zapoznawcza)](./how-to-custom-speech-commands-confirmations.md)
