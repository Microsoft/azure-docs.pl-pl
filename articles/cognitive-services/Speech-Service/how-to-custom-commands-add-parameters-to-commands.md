---
title: 'Instrukcje: Dodawanie prostych poleceń do poleceń niestandardowych Application-Speech Service'
titleSuffix: Azure Cognitive Services
description: W tym artykule dowiesz się, jak dodać parametry do poleceń niestandardowych
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 0ed237debc2395ed307658b2d57a541574f9478a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87284153"
---
# <a name="add-parameters-to-commands"></a>Dodawanie parametrów do poleceń

W tym artykule dowiesz się, jak dodać parametry do poleceń niestandardowych. Parametry są wymagane przez polecenia do wykonania zadania. W scenariuszach złożonych parametry można także użyć do zdefiniowania warunków, które wyzwalają akcje niestandardowe.

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * [Instrukcje: Tworzenie aplikacji przy użyciu prostych poleceń](./how-to-custom-commands-create-application-with-simple-commands.md)

## <a name="configure-parameters-for-turnon-command"></a>Konfigurowanie parametrów dla polecenia wlacza

Edytuj istniejące polecenie **wlaczanie** , aby włączyć i wyłączyć wiele urządzeń.

1. Ponieważ polecenie zostanie teraz obsłużone i wyłączone, Zmień nazwę polecenia na **TurnOnOff**.
   1. W lewym okienku wybierz polecenie **wlaczaj** , a następnie wybierz przycisk wielokropka (...) obok **polecenia nowe** w górnej części okienka.
   
   1. Wybierz pozycję **Zmień nazwę**. W oknie **Zmień** nazwę systemu Windows Zmień **nazwę** na **TurnOnOff**.

1. Następnie Dodaj nowy parametr do tego polecenia, który określa, czy użytkownik chce włączyć lub wyłączyć urządzenie.
   1. Wybierz pozycję  **Dodaj** obecność znajdującą się u góry środkowego okienka. Z listy rozwijanej wybierz opcję **parametr**.
   1. W okienku po prawej stronie w sekcji **Parametry** Dodaj wartość w polu **Nazwa** jako **OnOff**.
   1. Wybierz pozycję **wymagane**. W oknie **Dodawanie odpowiedzi dla wymaganego parametru** wybierz opcję **prosty edytor**. W **pierwszej wariacji**Dodaj
        ```
        On or Off?
        ```
   1. Wybierz pozycję **Aktualizuj**.

       > [!div class="mx-imgBorder"]
       > ![Utwórz wymaganą odpowiedź parametru](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. Teraz skonfigurujemy właściwości parametrów. Aby uzyskać wyjaśnienie wszystkich właściwości konfiguracji polecenia, przejdź do pozycji [odwołania](./custom-commands-references.md). Skonfiguruj właściwości parametru w następujący sposób:
      

       | Konfiguracja      | Sugerowana wartość     | Opis                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Nazwa               | `OnOff`           | Nazwa opisowa parametru                                                                           |
       | Jest globalny          | unchecked       | Pole wyboru wskazujące, czy wartość tego parametru jest stosowana globalnie do wszystkich poleceń w aplikacji|
       | Wymagane           | checked         | Pole wyboru wskazujące, czy przed ukończeniem polecenia jest wymagana wartość tego parametru |
       | Odpowiedź dotycząca wymaganego parametru      |> edytora prostego `On or Off?`      | Monit o podanie wartości tego parametru, gdy nie jest on znany |
       | Type               | Ciąg          | Typ parametru, taki jak Number, String, Data Time lub geography   |
       | Konfiguracja      | Akceptowanie wstępnie zdefiniowanych wartości wejściowych z wykazu wewnętrznego | W przypadku ciągów, to ogranicza dane wejściowe do zestawu możliwych wartości |
       | Wstępnie zdefiniowane wartości wejściowe     | `on`, `off`           | Zestaw możliwych wartości i ich aliasów         |
       
        
   1. Aby dodać wstępnie zdefiniowane wartości wejściowe, wybierz opcję **Dodaj wstępnie zdefiniowane dane wejściowe** i w oknie **nowy element**  , wpisz **nazwę** w powyższej tabeli. W tym przypadku nie używamy aliasów, więc można pozostawić to pole puste.
   
      > [!div class="mx-imgBorder"]
      > ![Utwórz parametr](media/custom-commands/create-on-off-parameter.png)

   1. Wybierz pozycję **Zapisz** , aby zapisać wszystkie konfiguracje parametru.
 
 ### <a name="add-subjectdevice-parameter"></a>Dodaj parametr SubjectDevice 

   1. Następnie ponownie wybierz pozycję **Dodaj** , aby dodać drugi parametr reprezentujący nazwę urządzeń, które mogą być kontrolowane za pomocą tego polecenia. Użyj poniższej konfiguracji.
   

       | Ustawienie            | Sugerowana wartość       |
       | ------------------ | --------------------- |
       | Nazwa               | `SubjectDevice`         |
       | Jest globalny          | unchecked             |
       | Wymagane           | checked               |
       | Odpowiedź dotycząca wymaganego parametru     | > edytora prostego `Which device do you want to control?`    | 
       | Type               | Ciąg                |          |
       | Konfiguracja      | Akceptowanie wstępnie zdefiniowanych wartości wejściowych z wykazu wewnętrznego | 
       | Wstępnie zdefiniowane wartości wejściowe | `tv`, `fan`               |
       | Aliasy ( `tv` )      | `television`, `telly`     |

   1. Wybierz pozycję **Zapisz**

### <a name="modify-example-sentences"></a>Modyfikuj przykładowe zdania

W przypadku poleceń z parametrami warto dodać przykładowe zdania, które obejmują wszystkie możliwe kombinacje. Na przykład:

* Pełne informacje o parametrach — `turn {OnOff} the {SubjectDevice}`
* Informacje o części parametru- `turn it {OnOff}`
* Brak informacji o parametrach- `turn something`

Przykładowe zdania o różnym stopniu informacji umożliwiają aplikacji poleceń niestandardowych rozpoznawanie zarówno rozdzielczości jednego zrzutu, jak i rozwiązywanie wielu przełączania przy użyciu częściowych informacji.

Mając to na uwadze, Edytuj przykładowe zdania, aby użyć parametrów zgodnie z sugerowaną poniżej:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Wybierz pozycję **Zapisz**.

> [!TIP]
> W przykładowym edytorze zdań Użyj nawiasów klamrowych, aby odwołać się do parametrów. - `turn {OnOff} the {SubjectDevice}` Użyj karty do automatycznego uzupełniania z kopii zapasowej za pomocą wcześniej utworzonych parametrów.

### <a name="modify-completion-rules-to-include-parameters"></a>Modyfikuj reguły uzupełniania w celu uwzględnienia parametrów

Zmodyfikuj istniejącą regułę ukończenia **ConfirmationResponse**.

1. W sekcji **warunki** wybierz pozycję **Dodaj warunek**.
1. W oknie **Nowy warunek** na liście **Typ** wybierz pozycję **wymagane parametry**. Na poniższej liście kontrolnej Sprawdź zarówno **OnOff** , jak i **SubjectDevice**.
1. Pozostaw **Isglobalną** jako niesprawdzoną.
1. Wybierz przycisk **Utwórz**.
1. W sekcji **Akcje** Edytuj istniejącą akcję **Wyślij odpowiedź na mowę** , umieszczając kursor nad akcją i wybierając przycisk Edytuj. Tym razem użyj nowo utworzonych parametrów **OnOff** i **SubjectDevice**

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Wybierz pozycję **Zapisz**.

### <a name="try-it-out"></a>Wypróbowywanie działania
1. Wybierz ikonę **szkolenia** w górnej części okienka po prawej stronie.

1. Po zakończeniu szkolenia wybierz pozycję **Testuj**. Zostanie wyświetlone okno **Test aplikacji** .
 Wypróbuj kilka interakcji.

    - Dane wejściowe: wyłączanie telewizora
    - Wynik: OK, wyłączanie telewizora
    - Wejście: wyłączanie telewizji
    - Wynik: OK, wyłączanie telewizora
    - Wejście: wyłączanie
    - Dane wyjściowe: urządzenie, które chcesz kontrolować?
    - Dane wejściowe: telewizja
    - Wynik: OK, wyłączanie telewizora

## <a name="configure-parameters-for-settemperature-command"></a>Konfigurowanie parametrów dla polecenia settemperatur

Zmodyfikuj polecenie **Settemperaturę** , aby umożliwić jej ustawienie temperatury w sposób pokierowany przez użytkownika.

Dodaj nową **temperaturę** parametru z następującą konfiguracją

| Konfiguracja      | Sugerowana wartość     |
| ------------------ | ----------------|
| Nazwa               | `Temperature`           |
| Wymagane           | checked         |
| Odpowiedź dotycząca wymaganego parametru      | > edytora prostego `What temperature would you like?`
| Type               | Liczba          |


Edytuj przykład wyrażenia długości na następujących wartościach.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

Edytuj istniejące reguły uzupełniania zgodnie z następującą konfiguracją.

| Konfiguracja      | Sugerowana wartość     |
| ------------------ | ----------------|
| Warunki         | Wymagana > temperatura parametru           |
| Akcje           | Wysyłanie odpowiedzi na mowę > `Ok, setting temperature to {Temperature} degrees` |

### <a name="try-it-out"></a>Wypróbowywanie działania

**Uczenie** i **testowanie** zmian przy użyciu kilku interakcji.

- Dane wejściowe: Ustaw temperaturę
- Wynik: jakiej temperatury chcesz?
- Dane wejściowe: 50 stopni
- Wynik: OK, ustawianie temperatury na 50 stopni

## <a name="configure-parameters-to-the-setalarm-command"></a>Konfigurowanie parametrów dla polecenia setAlarm

Dodaj parametr o nazwie **DateTime** z następującą konfiguracją.

   | Ustawienie                           | Sugerowana wartość                     | 
   | --------------------------------- | ----------------------------------------|
   | Nazwa                              | `DateTime`                               |
   | Wymagane                          | checked                                 |
   | Odpowiedź dotycząca wymaganego parametru   | > edytora prostego `For what time?`            | 
   | Type                              | DateTime                                |
   | Wartości domyślne daty                     | Jeśli brakuje daty, użyj dzisiaj            |
   | Ustawienia domyślne czasu                     | W przypadku braku czasu Użyj początku dnia     |


> [!NOTE]
> W tym artykule użyto głównie typów parametrów String, Number i DateTime. Aby wyświetlić listę wszystkich obsługiwanych typów parametrów i ich właściwości, przejdź do pozycji [odwołania](./custom-commands-references.md).


Edytuj przykład wyrażenia długości na następujących wartościach.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Edytuj istniejące reguły uzupełniania zgodnie z następującą konfiguracją.

   | Ustawienie    | Sugerowana wartość                               |
   | ---------- | ------------------------------------------------------- |
   | Akcje    | Wyślij odpowiedź na mowę — `Ok, alarm set for {DateTime}`  |


### <a name="try-it-out"></a>Wypróbowywanie działania

**Uczenie** i **testowanie** zmian.
- Wejście: ustaw alarm dla jutro o południe
- Wynik: OK, ustawiony alarm dla 2020-05-02 12:00:00
- Dane wejściowe: ustaw alarm
- Wynik: co godzinę?
- Dane wejściowe: 17:00
- Wynik: OK, ustawiony alarm dla 2020-05-01 17:00:00


## <a name="try-out-all-the-commands"></a>Wypróbuj wszystkie polecenia

Przetestuj wszystkie trzy polecenia za pomocą wyrażenia długości związanych z różnymi poleceniami. Należy pamiętać, że można przełączać się między różnymi poleceniami.

- Dane wejściowe: ustaw alarm
- Wynik: na jakim czasie?
- Wejście: Włączanie telewizora
- Wynik: OK, Włączanie telewizora
- Dane wejściowe: ustaw alarm
- Wynik: na jakim czasie?
- Dane wejściowe: 17:00
- Wynik: OK, ustawiony alarm dla 2020-05-01 17:00:00

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Instrukcje: Dodawanie konfiguracji do parametrów poleceń](./how-to-custom-commands-add-parameter-configuration.md)
