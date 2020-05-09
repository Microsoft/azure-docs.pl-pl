---
title: 'Szybki Start: Tworzenie polecenia niestandardowego z parametrami (wersja zapoznawcza) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano Dodawanie parametrów do aplikacji poleceń niestandardowych.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: bf77616123f9311f7384fea515f250e47b354c8c
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853608"
---
# <a name="quickstart-create-a-custom-commands-application-with-parameters-preview"></a>Szybki Start: Tworzenie aplikacji poleceń niestandardowych z parametrami (wersja zapoznawcza)

W [poprzednim artykule](./quickstart-custom-speech-commands-create-new.md)utworzono prostą aplikację poleceń niestandardowych bez parametrów.

W tym artykule rozszerzasz tę aplikację w celu użycia parametrów, aby umożliwić obsługę włączania i wyłączania wielu urządzeń.

## <a name="create-parameters"></a>Utwórz parametry

1. Otwórz [utworzony wcześniej](./quickstart-custom-speech-commands-create-new.md) projekt
1. Zmodyfikujmy istniejące polecenie, aby włączyć i wyłączyć wiele urządzeń.
1. Ponieważ polecenie zostanie teraz obsłużone i wyłączone, Zmień nazwę polecenia na `TurnOnOff`.
   - W lewym okienku wybierz `TurnOn` polecenie, a następnie kliknij `...` ikonę obok pozycji `+ New command` w górnej części okienka.
   
   - Wybierz `Rename` ikonę. W oknie podręcznym **polecenie zmiany** nazwy Zmień **nazwę** na `TurOnOff`. Następnie wybierz pozycję **Zapisz**.

1. Następnie utworzysz nowy parametr, aby określić, czy użytkownik chce włączyć lub wyłączyć urządzenie.
   - Wybierz `+ Add` ikonę znajdującą się w górnej części środkowego okienka. Z listy rozwijanej wybierz opcję **parametr**.
   - W najbardziej prawym okienku można zobaczyć sekcję Konfiguracja **parametrów** .
   - Dodaj wartość dla **nazwy**.
   - Zaznacz **wymagane** pole wyboru. W oknie **Dodawanie odpowiedzi dla wymaganego parametru** wybierz kolejno pozycje **prosty edytor** i do **pierwszej zmiany**, Dodaj
        ```
        On or Off?
        ```
   - Wybierz pozycję **Update** (Aktualizuj).

       > [!div class="mx-imgBorder"]
       > ![Utwórz wymaganą odpowiedź parametru](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
   - Następnie skonfiguruj pozostałe właściwości parametru w następujący sposób i wybierz opcję `Save` zapisania konfiguracji wszystkie konfiguracje do parametru.
       

       | Konfigurowanie      | Sugerowana wartość     | Opis                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Nazwa               | OnOff           | Nazwa opisowa parametru                                                                           |
       | Jest globalny          | unchecked       | Pole wyboru wskazujące, czy wartość tego parametru jest stosowana globalnie do wszystkich poleceń w aplikacji|
       | Wymagany           | checked         | Pole wyboru wskazujące, czy przed ukończeniem polecenia jest wymagana wartość tego parametru |
       | Odpowiedź dotycząca wymaganego parametru      |Prosty edytor — > włączony lub wyłączony?      | Monit o podanie wartości tego parametru, gdy nie jest on znany |
       | Typ               | String          | Typ parametru, taki jak Number, String, Data Time lub geography   |
       | Konfigurowanie      | Akceptowanie wstępnie zdefiniowanych wartości wejściowych z wykazu wewnętrznego | W przypadku ciągów, to ogranicza dane wejściowe do zestawu możliwych wartości |
       | Wstępnie zdefiniowane wartości wejściowe     | włączone, wyłączone             | Zestaw możliwych wartości i ich aliasów         |
       
        > [!div class="mx-imgBorder"]
        > ![Utwórz parametr](media/custom-speech-commands/create-on-off-parameter.png)

   - Następnie ponownie wybierz `+ Add` ikonę, aby dodać drugi parametr reprezentujący nazwę urządzeń z następującą konfiguracją.
   

       | Ustawienie            | Sugerowana wartość       | Opis                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | Nazwa               | SubjectDevice         | Nazwa opisowa parametru                                                                     |
       | Jest globalny          | unchecked             | Pole wyboru wskazujące, czy wartość tego parametru jest stosowana globalnie do wszystkich poleceń w aplikacji |
       | Wymagany           | checked               | Pole wyboru wskazujące, czy przed ukończeniem polecenia jest wymagana wartość tego parametru          |
       | Edytor prosty      | Które urządzenie?    | Monit o podanie wartości tego parametru, gdy nie jest on znany                                       |
       | Typ               | String                | Typ parametru, taki jak Number, String, Data Time lub geography                                                |
       | Konfigurowanie      | Akceptowanie wstępnie zdefiniowanych wartości wejściowych z wykazu wewnętrznego | W przypadku ciągów lista ciągów ogranicza dane wejściowe do zestawu możliwych wartości       |
       | Wstępnie zdefiniowane wartości wejściowe | Telewizja, wentylator               | Zestaw możliwych wartości i ich aliasów                               |
       | Aliasy (telewizja)      | Telewizja, informowanie     | Opcjonalne aliasy dla każdej możliwej wartości wstępnie zdefiniowanych wartości wejściowych                                 |

## <a name="add-example-sentences"></a>Dodaj przykładowe zdania

Za pomocą poleceń z parametrami warto dodać przykładowe zdania, które obejmują wszystkie możliwe kombinacje. Przykład:

1. Pełne informacje o parametrach —`turn {OnOff} the {SubjectDevice}`
1. Informacje o części parametru-`turn it {OnOff}`
1. Brak informacji o parametrach-`turn something`

Przykładowe zdania o różnym stopniu informacji umożliwiają aplikacji poleceń niestandardowych rozpoznawanie zarówno rozdzielczości jednego zrzutu, jak i rozwiązywanie wielu przełączania przy użyciu częściowych informacji.

Mając to na uwadze, Edytuj przykładowe zdania, aby użyć parametrów zgodnie z sugerowaną poniżej.

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> W przykładowym edytorze zdań Użyj nawiasów klamrowych, aby odwołać się do parametrów. - `turn {OnOff} the {SubjectDevice}`Użyj karty do automatycznego uzupełniania z kopii zapasowej za pomocą wcześniej utworzonych parametrów.

## <a name="add-parameters-to-completion-rules"></a>Dodawanie parametrów do reguł ukończenia

Zmodyfikuj regułę ukończenia utworzoną w [poprzednim przewodniku szybki start](./quickstart-custom-speech-commands-create-new.md).

1. W sekcji **warunki** Dodaj nowy warunek, wybierając pozycję **+ Dodaj warunek** .
1. W nowym wyskakującym okienku **Nowy warunek**wybierz `Required parameters` z listy rozwijanej **Typ** . Na poniższej liście kontrolnej Sprawdź zarówno `OnOff` , jak i `SubjectDevice`.
1. Kliknij pozycję **Utwórz**.
1. W sekcji **Akcje** Edytuj istniejącą akcję Wyślij odpowiedź na mowę, umieszczając kursor nad akcją i klikając ikonę edycji. Tym razem Użyjmy niedawno utworzonych `OnOff` i parametrów `SubjectDevice`

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>Wypróbowywanie działania
1. Wybierz `Train` ikonę znajdującą się w prawym okienku.

1. Po zakończeniu szkolenia wybierz pozycję `Test`.
    - Zostanie wyświetlony nowy **test okna aplikacji** .
    - Wypróbuj kilka interakcji.

        - Dane wejściowe: wyłączanie telewizora
        - Wynik: OK, wyłączanie telewizora        
        - Wejście: wyłączanie telewizji
        - Wynik: OK, wyłączanie telewizora
        - Wejście: wyłączanie
        - Dane wyjściowe: które urządzenie?
        - Dane wejściowe: telewizja
        - Wynik: OK, wyłączanie telewizora

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Szybki Start: używanie poleceń niestandardowych z niestandardowym głosem (wersja zapoznawcza)](./quickstart-custom-speech-commands-select-custom-voice.md)
