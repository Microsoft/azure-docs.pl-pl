---
title: 'Szybki Start: Tworzenie poleceń niestandardowych dla aplikacji w wersji zapoznawczej przy użyciu parametrów — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano Dodawanie parametrów do aplikacji poleceń niestandardowych, dzięki czemu można włączać i wyłączać wiele urządzeń.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: nitinme
ms.openlocfilehash: 940b3604487fbef7736c0d8f4f9299563b0b86d5
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509308"
---
# <a name="quickstart-create-a-custom-commands-preview-application-with-parameters"></a>Szybki Start: Tworzenie poleceń niestandardowych aplikacji w wersji zapoznawczej przy użyciu parametrów

W [poprzednim artykule](./quickstart-custom-speech-commands-create-new.md)utworzono prostą aplikację poleceń niestandardowych bez parametrów.

W tym artykule zostanie rozszerzona ta aplikacja o parametry, aby umożliwić i wyłączyć wiele urządzeń.

## <a name="create-parameters"></a>Tworzenie parametrów

1. Otwórz projekt utworzony w [poprzednim artykule](./quickstart-custom-speech-commands-create-new.md).

   Edytujemy istniejące polecenie, aby można było go używać do włączania i wyłączania wielu urządzeń.
1. Ponieważ polecenie zostanie teraz obsłużone i wyłączone, zmień jego nazwę na **TurnOnOff**.
   1. W lewym okienku wybierz polecenie **wlaczaj** , a następnie wybierz przycisk wielokropka (**...**) obok **polecenia nowe** w górnej części okienka.
   
   1. Wybierz pozycję **Zmień nazwę**. W oknie **polecenia Zmień** **nazwę** na **TurOnOff**. Wybierz pozycję **Zapisz**.

1. Utwórz parametr, aby określić, czy użytkownik chce włączyć lub wyłączyć urządzenie.
   1. Wybierz pozycję **Dodaj** w górnej części środkowego okienka. Z listy rozwijanej wybierz opcję **parametr**.
   1. W okienku po prawej stronie w sekcji **Parametry** Dodaj wartość w polu **Nazwa** .
   1. Wybierz pozycję **wymagane**. W oknie **Dodawanie odpowiedzi dla wymaganego parametru** wybierz opcję **prosty edytor**. W **pierwszym polu wariacja** wprowadź następujący tekst:
        ```
        On or Off?
        ```
   1. Wybierz pozycję **Update** (Aktualizuj).

       > [!div class="mx-imgBorder"]
       > ![Utwórz wymaganą odpowiedź parametru](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
1. Skonfiguruj pozostałe właściwości parametru w następujący sposób:
       

    | Konfigurowanie      | Sugerowana wartość     | Opis                                                      |
    | ------------------ | ----------------| ---------------------------------------------------------------------|
    | **Nazwa**               | **OnOff**           | Opisowa nazwa parametru.                                                                  |
    | **Jest globalny**          | Zaznaczenia       | Pole wyboru wskazujące, czy wartość parametru jest stosowana globalnie do wszystkich poleceń w aplikacji.|
    | **Wymagane**           | Wybrano         | Pole wyboru wskazujące, czy wartość parametru jest wymagana.  |
    | **Odpowiedź dotycząca wymaganego parametru**      |**Prosty edytor — > włączony lub wyłączony?**      | Monit o podanie wartości parametru, gdy nie jest on znany. |
    | **Typ**               | **Ciąg**          | Typ parametru. Na przykład Number, String, Data Time, geography.   |
    | **Konfiguracja**      | **Akceptowanie wstępnie zdefiniowanych wartości wejściowych z wykazu wewnętrznego** | W przypadku ciągów, to ustawienie ogranicza dane wejściowe do zestawu możliwych wartości. |
    | **Wstępnie zdefiniowane wartości wejściowe**     | **włączone**, **wyłączone**             | Zestaw możliwych wartości i ich aliasów.         |
       


    > [!div class="mx-imgBorder"]
    > ![Utwórz parametr](media/custom-speech-commands/create-on-off-parameter.png)

1. Wybierz pozycję **Zapisz**, aby zapisać ustawienia.

 1. Ponownie wybierz pozycję **Dodaj** , aby dodać drugi parametr. Ten parametr reprezentuje nazwę urządzenia. Użyj następujących ustawień:
   

       | Ustawienie            | Sugerowana wartość       | Opis                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | **Nazwa**               | **SubjectDevice**         | Opisowa nazwa parametru.                                                                     |
       | **Jest globalny**          | Zaznaczenia             | Pole wyboru wskazujące, czy wartość parametru jest stosowana globalnie do wszystkich poleceń w aplikacji. |
       | **Wymagane**           | Wybrano               | Pole wyboru wskazujące, czy wartość parametru jest wymagana.          |
       | **Edytor prosty**      | **Które urządzenie?**    | Monit o podanie wartości parametru, gdy nie jest on znany.                                       |
       | **Typ**               | **Ciąg**                | Typ parametru. Na przykład Number, String, Data Time, geography.                                                |
       | **Konfiguracja**      | **Akceptowanie wstępnie zdefiniowanych wartości wejściowych z wykazu wewnętrznego** | W przypadku ciągów, to ustawienie ogranicza dane wejściowe do zestawu możliwych wartości.       |
       | **Wstępnie zdefiniowane wartości wejściowe** | **telewizja**, **wentylator**               | Zestaw możliwych wartości i ich aliasów.                               |
       | **Aliasy** (telewizja)      | **telewizja**, **informowanie**     | Opcjonalne aliasy dla każdej ze wstępnie zdefiniowanych wartości wejściowych.                                 |

## <a name="add-example-sentences"></a>Dodaj przykładowe zdania

W przypadku poleceń z parametrami warto dodać przykładowe zdania, które obejmują wszystkie możliwe kombinacje. Przykład:

- Pełne informacje o parametrach:`turn {OnOff} the {SubjectDevice}`
- Informacje o części parametru:`turn it {OnOff}`
- Brak informacji o parametrach:`turn something`

Przykładowe zdania, które mają różne ilości informacji, umożliwiają aplikacji poleceń niestandardowych rozpoznawanie zarówno rozdzielczości jednego zrzutu, jak i rozdzielczości wielostopniowych, które mają częściowe informacje.

Z tego względu należy edytować przykładowe zdania, aby użyć parametrów zgodnie z sugerowanymi tutaj:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> W edytorze przykładowe zdania należy użyć nawiasów klamrowych, aby odwołać się do parametrów: `turn {OnOff} the {SubjectDevice}` .
>
> Użyj karty do automatycznego uzupełniania zdefiniowanej przez wcześniej utworzone parametry.

## <a name="add-parameters-to-completion-rules"></a>Dodawanie parametrów do reguł ukończenia

Zmodyfikuj regułę uzupełniania utworzoną w [poprzednim przewodniku szybki start](./quickstart-custom-speech-commands-create-new.md).

1. W sekcji **warunki** wybierz pozycję **Dodaj warunek**.
1. W oknie **Nowy warunek** na liście **Typ** wybierz pozycję **wymagane parametry**. Z listy wybierz zarówno **OnOff** , jak i **SubjectDevice**.
1. Wybierz przycisk **Utwórz**.
1. W sekcji **Akcje** Edytuj istniejącą akcję **Wyślij odpowiedź na mowę** , umieszczając kursor nad akcją i wybierając przycisk Edytuj. Tym razem użyj nowych `OnOff` i `SubjectDevice` parametrów:

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>Wypróbowywanie działania
1. Wybierz pozycję **uczenie** w górnej części okienka po prawej stronie.

1. Gdy szkolenie jest gotowe, wybierz pozycję **Testuj**.
    
    Zostanie wyświetlone okno **Test aplikacji** .

1. Wypróbuj kilka interakcji.

        - Input: turn off the tv
        - Output: Ok, turning off the tv        
        - Input: turn off the television
        - Output: Ok, turning off the tv
        - Input: turn it off
        - Output: Which device?
        - Input: the tv
        - Output: Ok, turning off the tv

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Szybki Start: używanie poleceń niestandardowych z niestandardowym głosem (wersja zapoznawcza)](./quickstart-custom-speech-commands-select-custom-voice.md)
