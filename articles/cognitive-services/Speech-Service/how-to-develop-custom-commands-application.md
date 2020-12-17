---
title: 'Instrukcje: opracowywanie aplikacji poleceń niestandardowych — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym instruktażu dowiesz się, jak tworzyć i dostosowywać aplikacje poleceń niestandardowych. Polecenia niestandardowe ułatwiają tworzenie zaawansowanych poleceń głosowych, zoptymalizowanych pod kątem interakcji głosowych, i najlepiej odpowiadają za wykonywanie zadań lub scenariusze kontroli poleceń i kontrolek, szczególnie dopasowane do urządzeń Internet rzeczy (IoT), otoczenia i bezobsługowego.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: 98c0459e0b67102458169147b1d39e98e2b3e2b1
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632958"
---
# <a name="develop-custom-commands-applications"></a>Opracowywanie aplikacji poleceń niestandardowych

W tym instruktażu dowiesz się, jak opracowywać i konfigurować niestandardowe aplikacje poleceń. Polecenia niestandardowe ułatwiają tworzenie zaawansowanych poleceń głosowych, zoptymalizowanych pod kątem interakcji głosowych, i najlepiej odpowiadają za wykonywanie zadań lub scenariusze kontroli poleceń i kontrolek, szczególnie dopasowane do urządzeń Internet rzeczy (IoT), otoczenia i bezobsługowego.

W tym artykule opisano tworzenie aplikacji, która może włączać i wyłączać telewizor, ustawiać temperaturę i ustawić alarm. Po utworzeniu tych podstawowych poleceń są uwzględniane następujące opcje dostosowywania poleceń:

* Dodawanie parametrów do poleceń
* Dodawanie konfiguracji do parametrów polecenia
* Tworzenie reguł interakcji
* Tworzenie szablonów generacji języka dla odpowiedzi na mowę
* Korzystanie z niestandardowego głosu 

## <a name="create-application-with-simple-commands"></a>Tworzenie aplikacji przy użyciu prostych poleceń

Najpierw Zacznij od utworzenia pustej aplikacji poleceń niestandardowych. Aby uzyskać szczegółowe informacje, zapoznaj się z [przewodnikiem Szybki Start](quickstart-custom-commands-application.md). Tym razem zamiast importowania projektu, należy utworzyć pusty projekt.

1. W polu **Nazwa** wprowadź nazwę projektu jako `Smart-Room-Lite` (lub coś innego do wyboru).
1. Na liście **Język** wybierz pozycję **angielski (Stany Zjednoczone)**.
1. Wybierz lub Utwórz wybrany zasób LUIS.

   > [!div class="mx-imgBorder"]
   > ![Tworzenie projektu](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>Aktualizowanie zasobów LUIS (opcjonalnie)

Możesz zaktualizować zasób tworzenia wybrany w oknie **Nowy projekt** , a następnie ustawić zasób przewidywania. Zasób predykcyjny jest używany do rozpoznawania podczas publikowania aplikacji poleceń niestandardowych. Nie potrzebujesz zasobów predykcyjnych w fazach tworzenia i testowania.

### <a name="add-turnon-command"></a>Dodaj polecenie wlaczania

W pustej aplikacji poleceń niestandardowych w języku **Smart-The-Lite** , która została właśnie utworzona, Dodaj proste polecenie, które przetwarza wypowiedź, `turn on the tv` i reaguje na wiadomość `Ok, turning the tv on` .

1. Utwórz nowe polecenie, wybierając pozycję **nowe** w górnej części okienka po lewej stronie. Zostanie otwarte **nowe okno polecenia** .
1. Podaj wartość dla pola **Nazwa** jako **wlaczac**.
1. Wybierz przycisk **Utwórz**.

W środkowym okienku wyświetlane są różne właściwości polecenia. Należy skonfigurować następujące właściwości polecenia. Aby uzyskać wyjaśnienie wszystkich właściwości konfiguracji polecenia, przejdź do pozycji [odwołania](./custom-commands-references.md).

| Konfiguracja            | Opis                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Przykładowe zdania** | Przykład wyrażenia długości, że użytkownik może powiedzieć, aby wyzwolić to polecenie                                                                 |
| **Parametry**       | Informacje wymagane do wykonania polecenia                                                                                |
| **Reguły uzupełniania** | Akcje, które należy wykonać, aby zrealizować polecenie. Na przykład, aby odpowiedzieć użytkownikowi lub komunikować się z inną usługą sieci Web. |
| **Reguły interakcji**   | Dodatkowe reguły do obsługi bardziej szczegółowych lub złożonych sytuacji                                                              |


> [!div class="mx-imgBorder"]
> ![Utwórz polecenie](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>Dodaj przykładowe zdania

Zacznijmy od sekcji **przykładowe zdania** i podaj przykład, co użytkownik może powiedzieć.

1. Wybierz sekcję **przykładowe zdania** w środkowym okienku.
1. W najbardziej prawym okienku Dodaj przykłady:

    ```
    turn on the tv
    ```

1.  Wybierz pozycję **Zapisz** w górnej części okienka.

Na razie nie mamy parametrów, więc można przejść do sekcji **reguł ukończenia** .

#### <a name="add-a-completion-rule"></a>Dodawanie reguły uzupełniania

Następnie polecenie musi mieć regułę uzupełniania. Ta reguła instruuje użytkownika, że jest wykonywana akcja realizacji. Aby dowiedzieć się więcej na temat reguł i reguł ukończenia, przejdź do pozycji [odwołania](./custom-commands-references.md).

1. Wybierz opcję **domyślna reguła** uzupełniania i edytuj ją w następujący sposób:

    
    | Ustawienie    | Sugerowana wartość                          | Opis                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Nazwa**       | ConfirmationResponse                  | Nazwa opisująca przeznaczenie reguły          |
    | **Warunki** | Brak                                     | Warunki określające, kiedy można uruchomić regułę    |
    | **Akcje**    | Wysyłaj odpowiedzi na mowę > prosty edytor > pierwszej wariacji > `Ok, turning the tv on` | Akcja, która ma zostać podjęta po spełnieniu warunku reguły |

   > [!div class="mx-imgBorder"]
   > ![Tworzenie odpowiedzi na mowę](media/custom-commands/create-speech-response-action.png)

1. Wybierz pozycję **Zapisz** , aby zapisać akcję.
1. Wróć do sekcji **reguły uzupełniania** , wybierz pozycję **Zapisz** , aby zapisać wszystkie zmiany. 

    > [!NOTE]
    > Nie jest konieczne użycie domyślnej reguły uzupełniania, która jest dostarczana z poleceniem. W razie konieczności można usunąć istniejącą domyślną regułę uzupełniania i dodać własną regułę.

### <a name="add-settemperature-command"></a>Dodaj polecenie settemperaturę

Teraz Dodaj jeszcze jedno polecenie **Settemperaturę** , które będzie miało pojedynczy wypowiedź `set the temperature to 40 degrees` i odpowiedzieć na komunikat `Ok, setting temperature to 40 degrees` .

Postępuj zgodnie z instrukcjami dla polecenia **wlaczaj** , aby utworzyć nowe polecenie za pomocą przykładowego zdania "**Ustaw temperaturę na 40 stopni**".

Następnie edytuj istniejące **gotowe** reguły ukończenia w następujący sposób:

| Ustawienie    | Sugerowana wartość                          |
| ---------- | ---------------------------------------- |
| Nazwa  | ConfirmationResponse                  |
| Warunki | Brak                                     |
| Akcje    | Wysyłaj odpowiedzi na mowę > prosty edytor > pierwszej wariacji > `Ok, setting temperature to 40 degrees` |

Wybierz pozycję **Zapisz** , aby zapisać wszystkie zmiany w poleceniu.

### <a name="add-setalarm-command"></a>Dodaj polecenie setAlarm

Utwórz nowe polecenie **setAlarm** przy użyciu przykładowego zdania "**Ustaw alarm dla 9 am**". Następnie edytuj istniejące **gotowe** reguły ukończenia w następujący sposób:

| Ustawienie    | Sugerowana wartość                          |
| ---------- | ---------------------------------------- |
| Nazwa reguły  | ConfirmationResponse                  |
| Warunki | Brak                                     |
| Akcje    | Wysyłaj odpowiedzi na mowę > prosty edytor > pierwszej wariacji >`Ok, setting an alarm for 9 am tomorrow` |

Wybierz pozycję **Zapisz** , aby zapisać wszystkie zmiany w poleceniu.

### <a name="try-it-out"></a>Czas to wypróbować

Przetestuj zachowanie przy użyciu panelu rozmowa testowa. Wybierz ikonę **szkolenia** znajdującą się u góry okienka po prawej stronie. Po zakończeniu szkolenia wybierz pozycję **Testuj**. Wypróbuj następujące przykłady wypowiedź za pośrednictwem głosu lub tekstu:

- Wpisz: Ustaw temperaturę na 40 stopni
- Oczekiwana odpowiedź: OK, ustawianie temperatury na 40 stopni
- Wpisywanie: Włączanie telewizora
- Oczekiwana odpowiedź: OK, Włączanie telewizora
- Wpisz: ustaw alarm dla 9 am/jutro
- Oczekiwana odpowiedź: OK, Ustawianie alarmu dla 9 am/jutro

> [!div class="mx-imgBorder"]
> ![Testowanie za pomocą rozmowy w sieci Web](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> W panelu test można wybrać pozycję **Zmień szczegóły** , aby uzyskać informacje dotyczące sposobu przetwarzania danych wejściowych dźwięku/tekstu.

## <a name="add-parameters-to-commands"></a>Dodawanie parametrów do poleceń

W tej sekcji dowiesz się, jak dodać parametry do poleceń. Parametry są wymagane przez polecenia do wykonania zadania. W scenariuszach złożonych parametry można także użyć do zdefiniowania warunków, które wyzwalają akcje niestandardowe.

### <a name="configure-parameters-for-turnon-command"></a>Konfigurowanie parametrów dla polecenia wlacza

Zacznij od edycji istniejącego polecenia **wlaczania** , aby włączyć i wyłączyć wiele urządzeń.

1. Teraz, gdy polecenie zostanie teraz obsłużone i wyłączone, Zmień nazwę polecenia na **TurnOnOff**.
   1. W lewym okienku wybierz polecenie **wlaczaj** , a następnie wybierz przycisk wielokropka (...) obok **polecenia nowe** w górnej części okienka.
   
   1. Wybierz pozycję **Zmień nazwę**. W oknie **Zmień** nazwę systemu Windows Zmień **nazwę** na **TurnOnOff**.

1. Następnie Dodaj nowy parametr do tego polecenia, który określa, czy użytkownik chce włączyć lub wyłączyć urządzenie.
   1. Wybierz pozycję  **Dodaj** obecność znajdującą się u góry środkowego okienka. Z listy rozwijanej wybierz opcję **parametr**.
   1. W okienku po prawej stronie w sekcji **Parametry** Dodaj wartość w polu **Nazwa** jako **OnOff**.
   1. Wybierz pozycję **wymagane**. W oknie **Dodawanie odpowiedzi dla wymaganego parametru** wybierz opcję **prosty edytor**. W **pierwszej wariacji** Dodaj
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
       | Typ               | String          | Typ parametru, taki jak Number, String, Data Time lub geography   |
       | Konfiguracja      | Akceptowanie wstępnie zdefiniowanych wartości wejściowych z wykazu wewnętrznego | W przypadku ciągów, to ogranicza dane wejściowe do zestawu możliwych wartości |
       | Wstępnie zdefiniowane wartości wejściowe     | `on`, `off`           | Zestaw możliwych wartości i ich aliasów         |
       
        
   1. Aby dodać wstępnie zdefiniowane wartości wejściowe, wybierz opcję **Dodaj wstępnie zdefiniowane dane wejściowe** i w oknie **nowy element**  , wpisz **nazwę** w powyższej tabeli. W tym przypadku nie używamy aliasów, więc można pozostawić to pole puste.
   
      > [!div class="mx-imgBorder"]
      > ![Utwórz parametr](media/custom-commands/create-on-off-parameter.png)

   1. Wybierz pozycję **Zapisz** , aby zapisać wszystkie konfiguracje parametru.
 
#### <a name="add-subjectdevice-parameter"></a>Dodaj parametr SubjectDevice 

1. Następnie ponownie wybierz pozycję **Dodaj** , aby dodać drugi parametr reprezentujący nazwę urządzeń, które mogą być kontrolowane za pomocą tego polecenia. Użyj poniższej konfiguracji.


    | Ustawienie            | Sugerowana wartość       |
    | ------------------ | --------------------- |
    | Nazwa               | `SubjectDevice`         |
    | Jest globalny          | unchecked             |
    | Wymagane           | checked               |
    | Odpowiedź dotycząca wymaganego parametru     | > edytora prostego `Which device do you want to control?`    | 
    | Typ               | String                |          |
    | Konfiguracja      | Akceptowanie wstępnie zdefiniowanych wartości wejściowych z wykazu wewnętrznego | 
    | Wstępnie zdefiniowane wartości wejściowe | `tv`, `fan`               |
    | Aliasy ( `tv` )      | `television`, `telly`     |

1. Wybierz pozycję **Zapisz**

#### <a name="modify-example-sentences"></a>Modyfikuj przykładowe zdania

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

#### <a name="modify-completion-rules-to-include-parameters"></a>Modyfikuj reguły uzupełniania w celu uwzględnienia parametrów

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

Wypróbuj zmiany, wybierając ikonę **szkolenia** znajdującą się u góry okienka po prawej stronie. Po zakończeniu szkolenia wybierz pozycję **Testuj**. Zostanie wyświetlone okno **Test aplikacji** . Wypróbuj poniższe interakcje.

- Dane wejściowe: wyłączanie telewizora
- Wynik: OK, wyłączanie telewizora
- Wejście: wyłączanie telewizji
- Wynik: OK, wyłączanie telewizora
- Wejście: wyłączanie
- Dane wyjściowe: urządzenie, które chcesz kontrolować?
- Dane wejściowe: telewizja
- Wynik: OK, wyłączanie telewizora

### <a name="configure-parameters-for-settemperature-command"></a>Konfigurowanie parametrów dla polecenia settemperatur

Zmodyfikuj polecenie **Settemperaturę** , aby umożliwić jej ustawienie temperatury w sposób pokierowany przez użytkownika.

Dodaj nową **temperaturę** parametru z następującą konfiguracją

| Konfiguracja      | Sugerowana wartość     |
| ------------------ | ----------------|
| Nazwa               | `Temperature`           |
| Wymagane           | checked         |
| Odpowiedź dotycząca wymaganego parametru      | > edytora prostego `What temperature would you like?`
| Typ               | Liczba          |


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

### <a name="configure-parameters-to-the-setalarm-command"></a>Konfigurowanie parametrów dla polecenia setAlarm

Dodaj parametr o nazwie **DateTime** z następującą konfiguracją.

   | Ustawienie                           | Sugerowana wartość                     | 
   | --------------------------------- | ----------------------------------------|
   | Nazwa                              | `DateTime`                               |
   | Wymagane                          | checked                                 |
   | Odpowiedź dotycząca wymaganego parametru   | > edytora prostego `For what time?`            | 
   | Typ                              | Data/godzina                                |
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

Przetestuj wszystkie trzy polecenia za pomocą wyrażenia długości związanych z różnymi poleceniami. Należy pamiętać, że można przełączać się między różnymi poleceniami.

- Dane wejściowe: ustaw alarm
- Wynik: na jakim czasie?
- Wejście: Włączanie telewizora
- Wynik: OK, Włączanie telewizora
- Dane wejściowe: ustaw alarm
- Wynik: na jakim czasie?
- Dane wejściowe: 17:00
- Wynik: OK, ustawiony alarm dla 2020-05-01 17:00:00

## <a name="add-configurations-to-commands-parameters"></a>Dodawanie konfiguracji do parametrów poleceń

W tej sekcji dowiesz się więcej na temat zaawansowanej konfiguracji parametrów, w tym:

 - Jak wartości parametrów mogą należeć do zestawu zdefiniowanego zewnętrznie do aplikacji poleceń niestandardowych
 - Jak dodać klauzule walidacji do wartości parametrów

### <a name="configure-parameter-as-external-catalog-entity"></a>Konfigurowanie parametru jako jednostki zewnętrznego katalogu

Polecenia niestandardowe umożliwiają skonfigurowanie parametrów typu ciąg w celu odwoływania się do katalogów zewnętrznych hostowanych przez punkt końcowy sieci Web. Umożliwia to niezależne aktualizowanie wykazu zewnętrznego bez wprowadzania zmian w aplikacji poleceń niestandardowych. Takie podejście jest przydatne w przypadkach, w których wpisy katalogu mogą być duże w liczbie.

Ponownie Użyj parametru **SubjectDevice** z polecenia **TurnOnOff** . Bieżąca konfiguracja tego parametru **akceptuje wstępnie zdefiniowane dane wejściowe z wykazu wewnętrznego**. Odnosi się to do statycznej listy urządzeń, zgodnie z definicją w konfiguracji parametrów. Chcemy przenieść tę zawartość do zewnętrznego źródła danych, które można aktualizować niezależnie.

Aby to zrobić, Zacznij od dodania nowego punktu końcowego sieci Web. Przejdź do sekcji **punkty końcowe sieci Web** w lewym okienku i Dodaj nowy punkt końcowy sieci Web z następującą konfiguracją.

| Ustawienie | Sugerowana wartość |
|----|----|
| Nazwa | `getDevices` |
| Adres URL | `https://aka.ms/speech/cc-sampledevices` |
| Metoda | GET |


Jeśli Sugerowana wartość dla adresu URL nie działa prawidłowo, należy skonfigurować i hostować prosty internetowy punkt końcowy, który zwraca kod JSON zawierający listę urządzeń, które mogą być kontrolowane. Punkt końcowy sieci Web powinien zwrócić kod JSON sformatowany w następujący sposób:
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```

Następnie przejdź do strony ustawień parametrów **SubjectDevice** i zmień właściwości na następujące.

| Ustawienie | Sugerowana wartość |
| ----| ---- |
| Konfiguracja | Zaakceptuj wstępnie zdefiniowane dane wejściowe z wykazu zewnętrznego |                               
| Punkt końcowy katalogu | GetDevices |
| Metoda | GET |

Następnie wybierz pozycję **Zapisz**.

> [!IMPORTANT]
> Nie zostanie wyświetlona opcja konfigurowania parametru w celu akceptowania danych wejściowych z wykazu zewnętrznego, chyba że w sekcji **punkt końcowy sieci Web** w okienku po lewej stronie jest ustawiony punkt końcowy sieci Web.

Wypróbuj ją, wybierając pozycję **uczenie** i poczekaj na zakończenie szkolenia. Po zakończeniu szkolenia wybierz pozycję **Testuj** i wypróbuj kilka interakcji.

* Dane wejściowe: Włącz
* Dane wyjściowe: urządzenie, które chcesz kontrolować?
* Dane wejściowe: sygnalizatory
* Wynik: OK, Włączanie świateł

> [!NOTE]
> Zwróć uwagę na to, jak można teraz kontrolować wszystkie urządzenia hostowane w punkcie końcowym sieci Web. Nadal konieczne jest nauczenie aplikacji do testowania nowych zmian i ponowne opublikowanie aplikacji.

### <a name="add-validation-to-parameters"></a>Dodawanie walidacji do parametrów

**Walidacje** to konstrukcje mające zastosowanie do niektórych typów parametrów, które umożliwiają konfigurowanie ograniczeń dotyczących wartości parametru i monitują o korektę, jeśli wartości nie mieszczą się w ograniczeniach. Aby uzyskać pełną listę typów parametrów rozszerzających konstrukcję walidacji, przejdź do pozycji [odwołania](./custom-commands-references.md)

Sprawdzanie poprawności przy użyciu polecenia **Settemperatura** . Aby dodać weryfikację parametru **temperatury** , wykonaj następujące czynności.

1. W lewym okienku wybierz polecenie **Settemperature** .
1. Wybierz pozycję  **temperatura** w środkowym okienku.
1. Wybierz pozycję **Dodaj weryfikację** obecną w okienku po prawej stronie.
1. W oknie **Nowa Walidacja** Skonfiguruj weryfikację w następujący sposób, a następnie wybierz pozycję **Utwórz**.


    | Konfiguracja parametrów | Sugerowana wartość | Opis |
    | ---- | ---- | ---- |
    | Wartość minimalna | `60` | Dla parametrów liczbowych minimalna wartość, którą może przyjąć ten parametr |
    | Wartość maksymalna | `80` | Dla parametrów liczbowych wartość maksymalna, którą może przyjąć ten parametr |
    | Niepowodzenie odpowiedzi |  Edytor prosty > pierwszej odmiany > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Monituj o podanie nowej wartości, Jeśli weryfikacja nie powiedzie się |

    > [!div class="mx-imgBorder"]
    > ![Dodawanie walidacji zakresu](media/custom-commands/add-validations-temperature.png)

Wypróbuj ją, wybierając ikonę **szkolenia** znajdującą się w prawym okienku. Po zakończeniu szkolenia wybierz pozycję **Testuj** i spróbuj wykonać kilka działań:

- Dane wejściowe: Ustaw temperaturę na 72 stopni
- Wynik: OK, ustawianie temperatury na 72 stopni
- Dane wejściowe: Ustaw temperaturę na 45 stopni
- Wynik: Niestety, można ustawić tylko temperaturę między 60 i 80 stopni
- Dane wejściowe: Zwiększ do 72 stopni.
- Wynik: OK, ustawianie temperatury na 72 stopni

## <a name="add-interaction-rules"></a>Dodawanie reguł interakcji

Reguły interakcji są *dodatkowymi regułami* , które obsługują konkretne lub złożone sytuacje. Chociaż możesz tworzyć własne niestandardowe reguły interakcji, w tym przykładzie korzystasz z reguł interakcji dla następujących scenariuszy strategicznych:

* Potwierdzanie poleceń
* Dodawanie korekty jednoetapowej do poleceń

Aby dowiedzieć się więcej o regułach interakcji, przejdź do sekcji [odwołania](./custom-commands-references.md) .

### <a name="add-confirmations-to-a-command"></a>Dodawanie potwierdzeń do polecenia

Aby dodać potwierdzenie, użyj polecenia **Settemperatura** . Aby uzyskać potwierdzenie, należy utworzyć reguły interakcji przy użyciu następujących kroków.

1. Wybierz polecenie **Settemperaturę** w lewym okienku.
1. Dodaj reguły interakcji, wybierając pozycję **Dodaj** w środkowym okienku. Następnie wybierz pozycję polecenia **reguły interakcji**  >  **Potwierdź polecenie**.

    Ta akcja dodaje trzy reguły interakcji, które poproszą użytkownika o potwierdzenie daty i godziny alarmu i oczekuje potwierdzenia (tak/nie) dla następnego wyłączenia.

    1. Zmodyfikuj regułę interakcji z **poleceniem Potwierdź** zgodnie z następującą konfiguracją:
        1. Zmień **nazwę nazwy** , aby **potwierdzić temperaturę**.
        1. Dodaj nowy warunek jako   >  **temperaturę** wymaganych parametrów.
        1. Dodać nową akcję jako **Typ**  >  **Wyślij odpowiedź na mowę** czy  >  **na pewno chcesz ustawić temperaturę jako wartość {temperatura}?**
        1. Pozostaw wartość domyślną **oczekiwanego potwierdzenia od użytkownika** w sekcji **oczekiwania** .
      
         > [!div class="mx-imgBorder"]
         > ![Utwórz wymaganą odpowiedź parametru](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Zmodyfikuj regułę interakcji **pomyślnie** , aby obsłużyć pomyślne potwierdzenie (użytkownik ten ma wartość tak).
      
          1. Modyfikowanie **nazwy** do **temperatury potwierdzenia powiodło się**.
          1. Pozostaw już istniejące **potwierdzenie zostało zakończone pomyślnie** .
          1. Dodaj nowy **warunek jako**  >  **parametry wymagane**  >  **temperatury**.
          1. Pozostaw wartość domyślną **stanu po wykonaniu** jako **reguły ukończenia wykonywania**.

    1. Zmodyfikuj regułę interakcji **odmowa dla potwierdzenia** , aby obsługiwać scenariusze w przypadku odmowy potwierdzenia (użytkownik nie został wskazany).

          1. Zmień **nazwę** na **odmowa temperatury potwierdzenia**.
          1. Pozostaw już istniejące **potwierdzenie zostało odrzucone** .
          1. Dodaj nowy **warunek jako**  >  **parametry wymagane**  >  **temperatury**.
          1. Dodaj nową akcję jako **Typ**  >  **wysyłanie odpowiedzi na mowę**  >  **nie ma problemu. Jakiej temperatury potem?**
          1. Pozostaw wartość domyślną **stanu po wykonaniu** jako **oczekiwanie na dane wejściowe użytkownika**.

> [!IMPORTANT]
> W tym artykule użyto wbudowanej funkcji potwierdzania. Możesz również ręcznie dodać reguły interakcji po jednej.
   
Wypróbuj zmiany, wybierając pozycję **uczenie**, poczekaj na zakończenie szkolenia, a następnie wybierz pozycję **Testuj**.

- **Dane wejściowe**: Ustaw temperaturę na 80 stopni
- **Wynik**: czy na pewno chcesz ustawić temperaturę jako 80 stopni?
- **Dane wejściowe**: nie
- **Dane wyjściowe**: brak problemu. Jakiej temperatury potem?
- **Dane wejściowe**: 72 stopni
- **Wynik**: czy na pewno chcesz ustawić temperaturę jako 72 stopni?
- **Dane wejściowe**: tak
- **Wynik**: OK, ustawianie temperatury na 83 stopni

### <a name="implement-corrections-in-a-command"></a>Implementowanie poprawek w poleceniu

W tej sekcji należy skonfigurować poprawkę jednoetapową, która jest używana po wykonaniu akcji realizacji. Zobaczysz również przykład sposobu, w jaki poprawka jest włączona domyślnie, jeśli polecenie nie zostało jeszcze spełnione. Aby dodać poprawkę, gdy polecenie nie zostało zakończone, Dodaj nowy parametr **AlarmTone**.

Wybierz polecenie **setAlarm** z okienka po lewej stronie, a następnie Dodaj nowy parametr **AlarmTone**.
        
- **Nazwa**  >  **AlarmTone**
- **Typ**  >  **Ciąg**
- **Wartość domyślna**  >  **CHIMES**
- **Konfiguracja**  >  **Akceptowanie wstępnie zdefiniowanych wartości wejściowych z wykazu wewnętrznego**
- **Wstępnie zdefiniowane wartości wejściowe**  >  **CHIMES**, **Jingle** i **echo** jako indywidualne wstępnie zdefiniowane dane wejściowe


Następnie zaktualizuj odpowiedź dla parametru **DateTime** , aby **ustawić alarm z sygnałem {AlarmTone}. Na jakim czasie?**. Następnie zmodyfikuj regułę uzupełniania w następujący sposób:

1. Wybierz istniejącą regułę ukończenia **ConfirmationResponse**.
1. W prawym okienku Umieść kursor nad istniejącą akcją i wybierz pozycję **Edytuj**.
1. Zaktualizuj odpowiedź mowy na **OK, zestaw alarmowy dla {DateTime}. Dźwięk ma wartość {AlarmTone}.**

> [!IMPORTANT]
> Dźwięk można zmienić bez żadnej konfiguracji jawnej w trwającym poleceniu, na przykład wtedy, gdy polecenie nie zostało jeszcze zakończone. *Korekta jest domyślnie włączona dla wszystkich parametrów poleceń, niezależnie od liczby wyłączanej, jeśli polecenie nie jest jeszcze spełnione.*

#### <a name="correction-when-command-is-completed"></a>Korekta po zakończeniu polecenia

Platforma poleceń niestandardowych oferuje również możliwość korekty jednoetapowej, nawet gdy polecenie zostało ukończone. Ta funkcja nie jest domyślnie włączona. Musi być jawnie skonfigurowana. Aby skonfigurować poprawkę jednoetapową, wykonaj następujące czynności.

1. W przypadku polecenia **setAlarm** Dodaj regułę interakcji **polecenia typu Update Previous** , aby zaktualizować wcześniej ustawiony alarm. Zmień **nazwę domyślnej reguły interakcji, aby** **zaktualizować poprzedni alarm**.
1. Pozostaw **polecenie domyślne poprzedni** warunek należy zaktualizować, jeśli jest to.
1. Dodaj nowy warunek jako **Typ**  >  **wymagany parametr**  >  **DateTime**.
1. Dodaj nową akcję jako **Typ**  >  **Wyślij odpowiedź mowy**  >  **prosty edytor**  >  **Aktualizowanie poprzedniego czasu alarmu do {DateTime}.**
1. Pozostaw wartość domyślną **stanu po wykonaniu** , gdy **polecenie zostało ukończone**.

Wypróbuj zmiany, wybierając pozycję **uczenie**, poczekaj na zakończenie szkolenia, a następnie wybierz pozycję **Testuj**.

- **Dane wejściowe**: ustaw alarm.
- **Dane wyjściowe**: gotowe do ustawienia alarmu z tonowego jako Chimes. Na jakim czasie?
- **Wejście**: ustaw alarm z odcieniem Jingle przez 9 jutro.
- **Wynik**: OK, ustawiony alarm dla 2020-05-21 09:00:00. Tonu alarmu to Jingle.
- **Dane wejściowe**: nie, 8 am.
- **Dane wyjściowe**: aktualizowanie poprzedniego czasu alarmu do 2020-05-29 08:00.

> [!NOTE]
> W prawdziwej aplikacji w sekcji **działania** tej reguły korekcji należy również wysłać do klienta działanie lub wywołać punkt końcowy HTTP, aby zaktualizować czas alarmu w systemie. Ta akcja powinna być odpowiedzialna wyłącznie za aktualizowanie czasu alarmu, a nie innych atrybutów polecenia. W tym przypadku jest to sygnał alarmu.

## <a name="add-language-generation-templates-for-speech-responses"></a>Dodawanie szablonów generowania języka na potrzeby odpowiedzi mowy

Szablony generowania języka umożliwiają dostosowanie odpowiedzi wysyłanych do klienta i wprowadzenie wariancji w odpowiedziach. Dostosowanie generowania języka można osiągnąć przez:

* Korzystanie z szablonów generacji języka
* Użycie wyrażeń adaptacyjnych

Szablony poleceń niestandardowych są oparte na [szablonach LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)BotFramework. Ponieważ polecenia niestandardowe tworzą nowy szablon LG w razie potrzeby (czyli w przypadku odpowiedzi na mowę w parametrach lub akcjach), nie trzeba określać nazwy szablonu LG. Dlatego zamiast definiować szablon jako:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

Wystarczy zdefiniować treść szablonu bez nazwy w następujący sposób.

> [!div class="mx-imgBorder"]
> ![przykład edytora szablonów](./media/custom-commands/template-editor-example.png)


Ta zmiana wprowadza zmiany do odpowiedzi na mowę wysyłanych do klienta. Tak więc w przypadku tego samego wypowiedź, odpowiednia odpowiedź mowy byłaby losowo wybierana z dostępnych opcji.

Korzystanie z szablonów LG pozwala także definiować złożone odpowiedzi na mowę dla poleceń przy użyciu wyrażeń adaptacyjnych. Aby uzyskać więcej informacji, można zapoznać się z [formatem szablonów LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates) . Polecenia niestandardowe domyślnie obsługują wszystkie funkcje z następującymi niewielkimi różnicami:

* W jednostkach szablonów LG są reprezentowane jako $ {EntityName}. W poleceniach niestandardowych nie używamy jednostek, ale parametry mogą być używane jako zmienne z jedną z tych reprezentacji $ {ParameterName} lub {ParameterName}
* Tworzenie i rozszerzanie szablonu nie jest obsługiwane w poleceniach niestandardowych. Dzieje się tak dlatego, że nigdy nie edytujesz `.lg` pliku bezpośrednio, ale tylko odpowiedzi automatycznie utworzonych szablonów.
* Funkcje niestandardowe wstrzykiwane przez element LG nie są obsługiwane w poleceniach niestandardowych. Wstępnie zdefiniowane funkcje są nadal obsługiwane.
* Opcje (Strict, replaceNull & lineBreakStyle) nie są obsługiwane w poleceniach niestandardowych.

### <a name="add-template-responses-to-turnonoff-command"></a>Dodawanie odpowiedzi szablonu do polecenia TurnOnOff

Zmodyfikuj polecenie **TurnOnOff** , aby dodać nowy parametr z następującą konfiguracją:

| Ustawienie            | Sugerowana wartość       | 
| ------------------ | --------------------- | 
| Nazwa               | `SubjectContext`         | 
| Jest globalny          | unchecked             | 
| Wymagane           | unchecked               | 
| Typ               | String                |
| Wartość domyślna      | `all` |
| Konfiguracja      | Akceptowanie wstępnie zdefiniowanych wartości wejściowych z wykazu wewnętrznego | 
| Wstępnie zdefiniowane wartości wejściowe | `room`, `bathroom`, `all`|

#### <a name="modify-completion-rule"></a>Modyfikuj regułę uzupełniania

Edytuj sekcję **Actions** istniejącej reguły ukończenia **ConfirmationResponse**. W oknie podręcznym **Edytowanie akcji** przejdź do **edytora szablonów** i Zastąp tekst następującym przykładem.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

**Przeszkol** i **Przetestuj** swoją aplikację w następujący sposób. Zwróć uwagę na odmianę odpowiedzi z powodu użycia wielu alternatyw wartości szablonu, a także wykorzystano wyrażenia adaptacyjne.

* Wejście: Włączanie telewizora
* Wynik: OK, Włączanie telewizora
* Wejście: Włączanie telewizora
* Wynik: gotowe, włączona telewizja
* Dane wejściowe: Wyłącz światła
* Wynik: OK, wyłączanie wszystkich lamp
* Dane wejściowe: Wyłącz światła pokojowe
* Wynik: OK, wyłączanie sygnalizatorów pokoju

## <a name="use-custom-voice"></a>Używanie głosu niestandardowego

Innym sposobem dostosowania odpowiedzi na polecenia niestandardowe jest wybranie niestandardowego głosu wyjściowego. Wykonaj następujące kroki, aby przełączyć domyślny głos na niestandardowy.

1. W aplikacji polecenia niestandardowe wybierz pozycję **Ustawienia** w okienku po lewej stronie.
1. Wybierz opcję **niestandardowy głos** w środkowym okienku.
1. Wybierz żądany niestandardowy lub publiczny głos z tabeli.
1. Wybierz pozycję **Zapisz**.

> [!div class="mx-imgBorder"]
> ![Przykładowe zdania z parametrami](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - Dla **publicznych głosów** **typy neuronowych** są dostępne tylko dla określonych regionów. Aby sprawdzić dostępność, zobacz [głosy standardowe i neuronowych według regionu/punktu końcowego](./regions.md#standard-and-neural-voices).
> - W przypadku **niestandardowych głosów** można je utworzyć na stronie niestandardowego projektu głosu. Zobacz Rozpoczynanie [pracy z niestandardowym głosem](./how-to-custom-voice.md).

Teraz aplikacja będzie odpowiadać w wybranym głosowaniu zamiast domyślnego głosu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [zintegrować swoją aplikację z poleceniami niestandardowymi](how-to-custom-commands-setup-speech-sdk.md) za pomocą aplikacji klienckiej przy użyciu zestawu Speech SDK.
* [Skonfiguruj ciągłe wdrażanie](how-to-custom-commands-deploy-cicd.md) dla aplikacji poleceń niestandardowych za pomocą usługi Azure DevOps. 
                      