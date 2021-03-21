---
title: 'Instrukcje: opracowywanie aplikacji poleceń niestandardowych — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak tworzyć i dostosowywać aplikacje poleceń niestandardowych. Te aplikacje polecenia głosowego najlepiej nadają się do realizacji zadań lub scenariuszy kontroli i sterowania.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: 1a002b6efbe2603ae254c19f9e3cc7377198cea2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97935822"
---
# <a name="develop-custom-commands-applications"></a>Opracowywanie aplikacji poleceń niestandardowych

W tym artykule z tego artykułu dowiesz się, jak opracowywać i konfigurować niestandardowe aplikacje poleceń. Funkcja poleceń niestandardowych ułatwia tworzenie zaawansowanych aplikacji poleceń głosowych, które są zoptymalizowane pod kątem interakcji głosowych. Funkcja najlepiej nadaje się do wykonywania zadań lub scenariuszy kontroli i sterowania. Jest to szczególnie odpowiednie dla urządzeń Internet rzeczy (IoT) oraz dla urządzeń w otoczeniu i bezobsługowych.

W tym artykule opisano tworzenie aplikacji, która może włączać i wyłączać telewizor, ustawiać temperaturę i ustawić alarm. Po utworzeniu tych podstawowych poleceń należy zapoznać się z następującymi opcjami dostosowywania poleceń:

* Dodawanie parametrów do poleceń
* Dodawanie konfiguracji do parametrów polecenia
* Tworzenie reguł interakcji
* Tworzenie szablonów generacji języka na potrzeby odpowiedzi na mowę
* Korzystanie z niestandardowych narzędzi głosowych

## <a name="create-an-application-by-using-simple-commands"></a>Tworzenie aplikacji przy użyciu prostych poleceń

Zacznij od utworzenia pustej aplikacji poleceń niestandardowych. Aby uzyskać szczegółowe informacje, zapoznaj się z [przewodnikiem Szybki Start](quickstart-custom-commands-application.md). W tej aplikacji zamiast zaimportować projekt, należy utworzyć pusty projekt.

1. W polu **Nazwa** wprowadź nazwę projektu *Smart-Pokój — w wersji uproszczonej* (lub inną wybraną nazwę).
1. Na liście **Język** wybierz pozycję **angielski (Stany Zjednoczone)**.
1. Wybierz lub utwórz zasób LUIS.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający okno "nowy projekt".](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>Aktualizowanie zasobów LUIS (opcjonalnie)

Możesz zaktualizować zasób tworzenia wybrany w oknie **Nowy projekt** . Można również ustawić zasób przewidywania. 

Zasób predykcyjny jest używany do rozpoznawania podczas publikowania aplikacji poleceń niestandardowych. Nie potrzebujesz zasobów predykcyjnych w fazach tworzenia i testowania.

### <a name="add-a-turnon-command"></a>Dodaj polecenie wlaczania

W pustej utworzonej aplikacji poleceń niestandardowych w języku Smart-The-Lite można dodać polecenie. Polecenie przetworzy wypowiedź, `Turn on the tv` . Odpowiedź będzie odpowiadać na wiadomość `Ok, turning the tv on` .

1. Utwórz nowe polecenie, wybierając pozycję **nowe** w górnej części okienka po lewej stronie. Zostanie otwarte **nowe okno polecenia** .
1. Podaj wartość w polu **Nazwa** `TurnOn` .
1. Wybierz przycisk **Utwórz**.

Środkowe okienko zawiera listę właściwości polecenia. 

W poniższej tabeli opisano właściwości konfiguracji polecenia. Aby uzyskać więcej informacji, zobacz [pojęcia i definicje poleceń niestandardowych](./custom-commands-references.md).

| Konfiguracja            | Opis                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Przykładowe zdania | Przykład wyrażenia długości, że użytkownik może powiedzieć, aby wyzwolić to polecenie.                                                                 |
| Parametry       | Informacje wymagane do ukończenia tego polecenia.                                                                                |
| Reguły uzupełniania | Akcje, które należy wykonać, aby zrealizować polecenie. Przykłady: reagowanie na użytkownika lub komunikowanie się z usługą sieci Web. |
| Reguły interakcji   | Inne reguły do obsługi bardziej szczegółowych lub złożonych sytuacji.                                                              |


> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający miejsce utworzenia polecenia.](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>Dodaj przykładowe zdania

W sekcji **przykładowe zdania** przedstawiono przykład tego, co użytkownik może powiedzieć.

1. W środkowym okienku wybierz **przykładowe zdania**.
1. W okienku po prawej stronie Dodaj przykłady:

    ```
    Turn on the tv
    ```

1.  W górnej części okienka wybierz pozycję **Zapisz**.

Nie masz jeszcze parametrów, więc możesz przejść do sekcji **reguł ukończenia** .

#### <a name="add-a-completion-rule"></a>Dodawanie reguły uzupełniania

Następnie polecenie wymaga reguły uzupełniania. Ta reguła instruuje użytkownika, że jest wykonywana akcja realizacji. 

Aby uzyskać więcej informacji o regułach i regułach uzupełniania, zobacz [pojęcia i definicje poleceń niestandardowych](./custom-commands-references.md).

1. Wybierz **Zakończono** domyślną regułę uzupełniania. Następnie edytuj go w następujący sposób:

    
    | Ustawienie    | Sugerowana wartość                          | Opis                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Nazwa**       | `ConfirmationResponse`                  | Nazwa opisująca przeznaczenie reguły          |
    | **Warunki** | Brak                                     | Warunki określające, kiedy można uruchomić regułę    |
    | **Akcje**    | **Wyślij odpowiedź**  >  na mowę **Edytor prosty**  >  **Pierwsza odmiana** > `Ok, turning the tv on` | Akcja, która ma zostać podjęta po spełnieniu warunku reguły |

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający miejsce utworzenia odpowiedzi na mowę.](media/custom-commands/create-speech-response-action.png)

1. Wybierz pozycję **Zapisz** , aby zapisać akcję.
1. Wróć do sekcji **reguły uzupełniania** , wybierz pozycję **Zapisz** , aby zapisać wszystkie zmiany. 

    > [!NOTE]
    > Nie musisz używać domyślnej reguły uzupełniania, która jest dostarczana z poleceniem. Można usunąć domyślną regułę uzupełniania i dodać własną regułę.

### <a name="add-a-settemperature-command"></a>Dodaj polecenie settemperaturę

Teraz Dodaj jeszcze jedno polecenie `SetTemperature` . To polecenie zajmie pojedynczy wypowiedź `Set the temperature to 40 degrees` i odpowie na komunikat `Ok, setting temperature to 40 degrees` .

Aby utworzyć nowe polecenie, wykonaj kroki, które zostały użyte dla `TurnOn` polecenia, ale Użyj przykładowego zdania `Set the temperature to 40 degrees` .

Następnie edytuj istniejące **gotowe** reguły ukończenia w następujący sposób:

| Ustawienie    | Sugerowana wartość                          |
| ---------- | ---------------------------------------- |
| **Nazwa**  | `ConfirmationResponse`                  |
| **Warunki** | Brak                                     |
| **Akcje**    | **Wyślij odpowiedź**  >  na mowę **Edytor prosty**  >  **Pierwsza odmiana** > `Ok, setting temperature to 40 degrees` |

Wybierz pozycję **Zapisz** , aby zapisać wszystkie zmiany w poleceniu.

### <a name="add-a-setalarm-command"></a>Dodaj polecenie setAlarm

Utwórz nowe `SetAlarm` polecenie. Użyj przykładowego zdania `Set an alarm for 9 am tomorrow` . Następnie edytuj istniejące **gotowe** reguły ukończenia w następujący sposób:

| Ustawienie    | Sugerowana wartość                          |
| ---------- | ---------------------------------------- |
| **Nazwa**  | `ConfirmationResponse`                  |
| **Warunki** | Brak                                     |
| **Akcje**    | **Wyślij odpowiedź**  >  na mowę **Edytor prosty**  >  **Pierwsza odmiana** > `Ok, setting an alarm for 9 am tomorrow` |

Wybierz pozycję **Zapisz** , aby zapisać wszystkie zmiany w poleceniu.

### <a name="try-it-out"></a>Czas to wypróbować

Przetestuj zachowanie aplikacji, korzystając z okienka test: 

1. W prawym górnym rogu okienka wybierz ikonę **szkolenie** . 
1. Po zakończeniu szkolenia wybierz pozycję **Testuj**. 

Wypróbuj następujące przykłady wypowiedź przy użyciu głosu lub tekstu:

- Wpisz: *Ustaw temperaturę na 40 stopni*
- Oczekiwana odpowiedź: OK, ustawianie temperatury na 40 stopni
- Wpisywanie: *Włączanie telewizora*
- Oczekiwana odpowiedź: OK, Włączanie telewizora
- Wpisz: *Ustaw alarm dla 9 am/jutro*
- Oczekiwana odpowiedź: OK, Ustawianie alarmu dla 9 am/jutro

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający test w interfejsie czatu sieci Web.](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> W okienku test można wybrać pozycję **Zmień szczegóły** , aby uzyskać informacje na temat sposobu przetwarzania tego głosu lub wprowadzania tekstu.

## <a name="add-parameters-to-commands"></a>Dodawanie parametrów do poleceń

W tej sekcji dowiesz się, jak dodać parametry do poleceń. Polecenia wymagają parametrów do ukończenia zadania. W złożonych scenariuszach parametry mogą służyć do definiowania warunków, które wyzwalają akcje niestandardowe.

### <a name="configure-parameters-for-a-turnon-command"></a>Konfigurowanie parametrów dla polecenia wlaczania

Zacznij od edycji istniejącego `TurnOn` polecenia, aby włączyć i wyłączyć wiele urządzeń.

1. Teraz, gdy polecenie będzie obsługiwało i wyłączać scenariusze, Zmień nazwę polecenia na *TurnOnOff*.
   1. W okienku po lewej stronie wybierz polecenie **wlaczaj** . Następnie obok **polecenia nowe** w górnej części okienka wybierz przycisk wielokropka (**...**).
   
   1. Wybierz pozycję **Zmień nazwę**. W oknie **polecenia Zmień** nazwę na *TurnOnOff*.

1. Dodaj nowy parametr do polecenia. Ten parametr określa, czy użytkownik chce włączyć lub wyłączyć urządzenie.
   1. W górnej części środkowego okienka wybierz pozycję  **Dodaj**. Z menu rozwijanego wybierz opcję **parametr**.
   1. W okienku po prawej stronie w sekcji **Parametry** , w polu **Nazwa** Dodaj `OnOff` .
   1. Wybierz pozycję **wymagane**. W oknie **Dodawanie odpowiedzi dla wymaganego parametru** wybierz opcję **prosty edytor**. W **pierwszym polu odmiany** , Dodaj *lub Wyłącz?*.
   1. Wybierz pozycję **Aktualizuj**.

       > [!div class="mx-imgBorder"]
       > ![Zrzut ekranu pokazujący sekcję "Dodawanie odpowiedzi dla wymaganego parametru" z wybraną kartą "prosty edytor".](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. Skonfiguruj właściwości parametru, korzystając z poniższej tabeli. Aby uzyskać informacje na temat wszystkich właściwości konfiguracji polecenia, zobacz [pojęcia i definicje poleceń niestandardowych](./custom-commands-references.md).
      

       | Konfigurowanie      | Sugerowana wartość     | Opis                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | **Nazwa**               | `OnOff`           | Opisowa nazwa parametru                                                                           |
       | **Jest globalny**          | Niezaznaczone       | Pole wyboru wskazujące, czy wartość tego parametru jest stosowana globalnie do wszystkich poleceń w aplikacji.|
       | **Wymagane**           | Wybrane         | Pole wyboru wskazujące, czy wartość tego parametru jest wymagana przed ukończeniem polecenia. |
       | **Odpowiedź dotycząca wymaganego parametru**      |**Edytor prosty** > `On or Off?`      | Monit o podanie wartości tego parametru, gdy nie jest znany. |
       | **Typ**               | **Ciąg**          | Typ parametru, taki jak Number, String, Data Time lub Geografia.   |
       | **Konfiguracja**      | **Akceptowanie wstępnie zdefiniowanych wartości wejściowych z wykazu wewnętrznego** | W przypadku ciągów, to ustawienie ogranicza dane wejściowe do zestawu możliwych wartości. |
       | **Wstępnie zdefiniowane wartości wejściowe**     | `on`, `off`           | Zbiór możliwych wartości i ich aliasów.         |
       
        
   1. Aby dodać wstępnie zdefiniowane wartości wejściowe, wybierz opcję **Dodaj wstępnie zdefiniowane dane wejściowe**. W oknie **nowy element**  wpisz *nazwę* , jak pokazano w powyższej tabeli. W tym przypadku nie używasz aliasów, więc możesz pozostawić to pole puste.
   
      > [!div class="mx-imgBorder"]
      > ![Zrzut ekranu przedstawiający sposób tworzenia parametru.](media/custom-commands/create-on-off-parameter.png)

   1. Wybierz pozycję **Zapisz** , aby zapisać wszystkie konfiguracje parametru.
 
#### <a name="add-a-subjectdevice-parameter"></a>Dodaj parametr SubjectDevice

1. Aby dodać drugi parametr reprezentujący nazwę urządzeń, które mogą być kontrolowane za pomocą tego polecenia, wybierz pozycję **Dodaj**. Użyj poniższej konfiguracji.


    | Ustawienie            | Sugerowana wartość       |
    | ------------------ | --------------------- |
    | **Nazwa**               | `SubjectDevice`         |
    | **Jest globalny**          | Niezaznaczone             |
    | **Wymagane**           | Wybrane               |
    | **Odpowiedź dotycząca wymaganego parametru**     | **Edytor prosty** > `Which device do you want to control?`    | 
    | **Typ**               | **Ciąg**                |          |
    | **Konfiguracja**      | **Akceptowanie wstępnie zdefiniowanych wartości wejściowych z wykazu wewnętrznego** | 
    | **Wstępnie zdefiniowane wartości wejściowe** | `tv`, `fan`               |
    | **Aliasy** ( `tv` )      | `television`, `telly`     |

1. Wybierz pozycję **Zapisz**.

#### <a name="modify-example-sentences"></a>Modyfikuj przykładowe zdania

W przypadku poleceń korzystających z parametrów warto dodać przykładowe zdania, które obejmują wszystkie możliwe kombinacje. Na przykład:

* Pełne informacje o parametrach: `turn {OnOff} the {SubjectDevice}`
* Informacje o części parametru: `turn it {OnOff}`
* Brak informacji o parametrach: `turn something`

Przykładowe zdania wykorzystujące różne stopnie informacji umożliwiają aplikacji poleceń niestandardowych rozpoznawanie zarówno rozdzielczości jednego zrzutu, jak i różnych rozdzielczości przy użyciu częściowych informacji.

Korzystając z tych informacji, Edytuj przykładowe zdania, aby użyć tych sugerowanych parametrów:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Wybierz pozycję **Zapisz**.

> [!TIP]
> W edytorze zdania przykładowe Użyj nawiasów klamrowych, aby odwołać się do parametrów. Na przykład `turn {OnOff} the {SubjectDevice}`.
> Użyj karty na potrzeby automatycznego uzupełniania, które zostały wcześniej utworzone za pomocą parametrów.

#### <a name="modify-completion-rules-to-include-parameters"></a>Modyfikuj reguły uzupełniania w celu uwzględnienia parametrów

Zmodyfikuj istniejącą regułę uzupełniania `ConfirmationResponse` .

1. W sekcji **warunki** wybierz pozycję **Dodaj warunek**.
1. W oknie **Nowy warunek** na liście **Typ** wybierz pozycję **wymagane parametry**. Na poniższej liście Wybierz zarówno **OnOff** , jak i **SubjectDevice**.
1. Pozostaw niewybrane pole **Isglobally** .
1. Wybierz przycisk **Utwórz**.
1. W sekcji **Akcje** Edytuj akcję **Wyślij odpowiedź na mowę** , aktywując ją i wybierając przycisk Edytuj. Tym razem użyj nowo utworzonych `OnOff` i `SubjectDevice` parametrów:

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Wybierz pozycję **Zapisz**.

Wypróbuj zmiany, wybierając ikonę **szkolenia** w górnej części okienka po prawej stronie. 

Po zakończeniu szkolenia wybierz pozycję **Testuj**. Zostanie wyświetlone okno **Testuj aplikację** . Wypróbuj następujące interakcje:

- Dane wejściowe: wyłączanie *telewizora*
- Wynik: OK, wyłączanie telewizora
- Wejście: wyłączanie *telewizji*
- Wynik: OK, wyłączanie telewizora
- Wejście: *wyłączanie*
- Dane wyjściowe: urządzenie, które chcesz kontrolować?
- Dane wejściowe: *telewizja*
- Wynik: OK, wyłączanie telewizora

### <a name="configure-parameters-for-a-settemperature-command"></a>Konfigurowanie parametrów dla polecenia settemperatur

Zmodyfikuj `SetTemperature` polecenie, aby umożliwić mu ustawienie temperatury jako przekierowanej przez użytkownika.

Dodaj `Temperature` parametr. Użyj następującej konfiguracji:

| Konfigurowanie      | Sugerowana wartość     |
| ------------------ | ----------------|
| **Nazwa**               | `Temperature`           |
| **Wymagane**           | Wybrane         |
| **Odpowiedź dotycząca wymaganego parametru**      | **Edytor prosty** > `What temperature would you like?`
| **Typ**               | `Number`          |


Edytuj przykład wyrażenia długości, aby użyć następujących wartości.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

Edytuj istniejące reguły uzupełniania. Użyj poniższej konfiguracji.

| Konfigurowanie      | Sugerowana wartość     |
| ------------------ | ----------------|
| **Warunki**         | **Wymagany parametr**  >  **Temperatura**           |
| **Akcje**           | **Wyślij odpowiedź na mowę** > `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-for-a-setalarm-command"></a>Konfigurowanie parametrów dla polecenia setAlarm

Dodaj wywołany parametr `DateTime` . Użyj poniższej konfiguracji.

   | Ustawienie                           | Sugerowana wartość                     | 
   | --------------------------------- | ----------------------------------------|
   | **Nazwa**                              | `DateTime`                               |
   | **Wymagane**                          | Wybrane                                 |
   | **Odpowiedź dotycząca wymaganego parametru**   | **Edytor prosty** > `For what time?`            | 
   | **Typ**                              | **Data/godzina**                                |
   | **Wartości domyślne daty**                     | Jeśli brakuje daty, użyj dzisiaj.            |
   | **Ustawienia domyślne czasu**                     | Jeśli brakuje czasu, użyj początku dnia.     |


> [!NOTE]
> W tym artykule najczęściej są stosowane typy parametrów String, Number i DateTime. Aby uzyskać listę wszystkich obsługiwanych typów parametrów i ich właściwości, zobacz [pojęcia i definicje poleceń niestandardowych](./custom-commands-references.md).


Edytuj przykład wyrażenia długości. Użyj następujących wartości.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Edytuj istniejące reguły uzupełniania. Użyj poniższej konfiguracji.

   | Ustawienie    | Sugerowana wartość                               |
   | ---------- | ------------------------------------------------------- |
   | **Akcje**    | **Wyślij odpowiedź na mowę** > `Ok, alarm set for {DateTime}`  |

Przetestuj trzy polecenia za pomocą wyrażenia długości związanych z różnymi poleceniami. (Można przełączać się między różnymi poleceniami).

- Dane wejściowe: *Ustaw alarm*
- Wynik: na jakim czasie?
- Wejście: *Włączanie telewizora*
- Wynik: OK, Włączanie telewizora
- Dane wejściowe: *Ustaw alarm*
- Wynik: na jakim czasie?
- Dane wejściowe: *5 PM*
- Wynik: OK, ustawiony alarm dla 2020-05-01 17:00:00

## <a name="add-configurations-to-command-parameters"></a>Dodawanie konfiguracji do parametrów polecenia

W tej sekcji dowiesz się więcej na temat zaawansowanej konfiguracji parametrów, w tym:

 - Jak wartości parametrów mogą należeć do zestawu, który jest zdefiniowany poza aplikacją poleceń niestandardowych.
 - Jak dodać klauzule walidacji do wartości parametrów.

### <a name="configure-a-parameter-as-an-external-catalog-entity"></a>Konfigurowanie parametru jako jednostki zewnętrznego katalogu

Funkcja poleceń niestandardowych umożliwia skonfigurowanie parametrów typu ciąg w celu odwoływania się do katalogów zewnętrznych hostowanych przez punkt końcowy sieci Web. Umożliwia to niezależne aktualizowanie wykazu zewnętrznego bez konieczności edytowania aplikacji poleceń niestandardowych. Takie podejście jest przydatne w przypadkach, w których wpisy wykazu są liczne.

Ponownie Użyj `SubjectDevice` parametru z `TurnOnOff` polecenia. Bieżąca konfiguracja tego parametru **akceptuje wstępnie zdefiniowane dane wejściowe z wykazu wewnętrznego**. Ta konfiguracja odwołuje się do statycznej listy urządzeń w konfiguracji parametrów. Przenieś tę zawartość do zewnętrznego źródła danych, które można aktualizować niezależnie.

Aby przenieść zawartość, Zacznij od dodania nowego internetowego punktu końcowego. W okienku po lewej stronie przejdź do sekcji **punkty końcowe sieci Web** . W tym miejscu Dodaj nowy internetowy punkt końcowy. Użyj poniższej konfiguracji.

| Ustawienie | Sugerowana wartość |
|----|----|
| **Nazwa** | `getDevices` |
| **Adres URL** | `https://aka.ms/speech/cc-sampledevices` |
| **Metoda** | **Pobierz** |


Jeśli Sugerowana wartość dla tego adresu URL nie będzie działać, należy skonfigurować i hostować punkt końcowy sieci Web, który zwraca plik JSON zawierający listę urządzeń, które mogą być kontrolowane. Punkt końcowy sieci Web powinien zwrócić plik JSON sformatowany w następujący sposób:
    
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

Następnie przejdź do strony ustawień parametrów **SubjectDevice** . Skonfiguruj następujące właściwości.

| Ustawienie | Sugerowana wartość |
| ----| ---- |
| **Konfiguracja** | **Zaakceptuj wstępnie zdefiniowane dane wejściowe z wykazu zewnętrznego** |                               
| **Punkt końcowy katalogu** | `getDevices` |
| **Metoda** | **Pobierz** |

Następnie wybierz pozycję **Zapisz**.

> [!IMPORTANT]
> Nie zostanie wyświetlona opcja konfigurowania parametru w celu akceptowania danych wejściowych z zewnętrznego wykazu, chyba że punkt końcowy sieci Web ustawiony w sekcji **punkt końcowy sieci Web** w okienku po lewej stronie.

Wypróbuj go, wybierając pozycję **uczenie**. Po zakończeniu szkolenia wybierz pozycję **Testuj** i wypróbuj kilka interakcji.

* Dane wejściowe: *Włącz*
* Dane wyjściowe: urządzenie, które chcesz kontrolować?
* Dane wejściowe: *sygnalizatory*
* Wynik: OK, Włączanie świateł

> [!NOTE]
> Teraz można kontrolować wszystkie urządzenia hostowane w punkcie końcowym sieci Web. Jednak nadal musisz przeprowadzić uczenie aplikacji, aby przetestować nowe zmiany, a następnie ponownie opublikować aplikację.

### <a name="add-validation-to-parameters"></a>Dodawanie walidacji do parametrów

*Walidacje* to konstrukcje, które są stosowane do niektórych typów parametrów, które umożliwiają konfigurowanie ograniczeń dotyczących wartości parametru. Monitują o poprawki, jeśli wartości nie mieszczą się w ograniczeniach. Aby uzyskać listę typów parametrów, które zwiększają konstrukcję walidacji, zobacz temat [pojęcia i definicje poleceń niestandardowych](./custom-commands-references.md).

Sprawdzanie poprawności przy użyciu `SetTemperature` polecenia. Wykonaj poniższe kroki, aby dodać weryfikację dla `Temperature` parametru.

1. W okienku po lewej stronie wybierz polecenie **Settemperaturę** .
1. W środkowym okienku wybierz pozycję **temperatura**.
1. W okienku po prawej stronie wybierz pozycję **Dodaj weryfikację**.
1. W oknie **Nowa Walidacja** Skonfiguruj sprawdzanie poprawności, jak pokazano w poniższej tabeli. Następnie wybierz pozycję **Utwórz**.


    | Konfiguracja parametrów | Sugerowana wartość | Opis |
    | ---- | ---- | ---- |
    | **Wartość minimalna** | `60` | Dla parametrów liczbowych minimalna wartość, którą może przyjąć ten parametr |
    | **Wartość maksymalna** | `80` | Dla parametrów liczbowych wartość maksymalna, którą może przyjąć ten parametr |
    | **Niepowodzenie odpowiedzi** |  **Edytor prosty**  >  **Pierwsza odmiana** > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Monit o podanie nowej wartości, Jeśli weryfikacja nie powiedzie się |

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający sposób dodawania walidacji zakresu.](media/custom-commands/add-validations-temperature.png)

Wypróbuj ją, wybierając ikonę **szkolenia** w górnej części okienka po prawej stronie. Po zakończeniu szkolenia wybierz pozycję **test**. Wypróbuj kilka interakcji:

- Dane wejściowe: *Ustaw temperaturę na 72 stopni*
- Wynik: OK, ustawianie temperatury na 72 stopni
- Dane wejściowe: *Ustaw temperaturę na 45 stopni*
- Wynik: Niestety, można ustawić tylko temperaturę między 60 i 80 stopni
- Dane wejściowe: *Zwiększ do 72 stopni* .
- Wynik: OK, ustawianie temperatury na 72 stopni

## <a name="add-interaction-rules"></a>Dodawanie reguł interakcji

Reguły interakcji są *dodatkowymi* regułami, które obsługują konkretne lub złożone sytuacje. Chociaż możesz tworzyć własne reguły interakcji, w tym przykładzie używasz reguł interakcji dla następujących scenariuszy:

* Potwierdzanie poleceń
* Dodawanie korekty jednoetapowej do poleceń

Aby uzyskać więcej informacji na temat reguł interakcji, zobacz temat [pojęcia i definicje poleceń niestandardowych](./custom-commands-references.md).

### <a name="add-confirmations-to-a-command"></a>Dodawanie potwierdzeń do polecenia

Aby dodać potwierdzenie, użyj `SetTemperature` polecenia. Aby uzyskać potwierdzenie, utwórz reguły interakcji, wykonując następujące czynności:

1. W okienku po lewej stronie wybierz polecenie **Settemperaturę** .
1. W środkowym okienku Dodaj reguły interakcji, wybierając pozycję **Dodaj**. Następnie wybierz pozycję polecenia **reguły interakcji**  >  **Potwierdź polecenie**.

    Ta akcja dodaje trzy reguły interakcji. Reguły zwracają użytkownikowi potwierdzenie daty i godziny alarmu. Po kolejnym włączeniu oczekiwane jest potwierdzenie (tak lub nie).

    1. Zmodyfikuj regułę interakcji z **poleceniem Confirm** przy użyciu następującej konfiguracji:
        1. Zmień nazwę, aby **potwierdzić temperaturę**.
        1. Dodaj nowy warunek: temperatura **wymaganych parametrów**  >  .
        1. Dodaj nową akcję: **wpisz tekst**  >  **Wyślij odpowiedź na mowę** czy  >  **na pewno chcesz ustawić temperaturę jako {temperatura} stopni?**
        1. W sekcji **oczekiwania** pozostaw wartość domyślną **oczekiwaną przez użytkownika**.
      
         > [!div class="mx-imgBorder"]
         > ![Zrzut ekranu przedstawiający sposób tworzenia wymaganej odpowiedzi parametru.](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Zmodyfikuj regułę interakcji **zakończyła się pomyślnie** , aby obsłużyć pomyślne potwierdzenie (użytkownik ten ma wartość tak).
      
          1. Zmień nazwę na " **potwierdzenie temperatury" powiodło się**.
          1. Pozostaw istniejące **potwierdzenie zostało zakończone pomyślnie** .
          1. Dodaj nowy warunek: **wpisz**  >  **wymagane parametry**  >  **temperatura**.
          1. Pozostaw domyślną wartość **stanu po wykonaniu** jako **reguły ukończenia wykonania**.

    1. Zmodyfikuj regułę interakcji **odmowa dla potwierdzenia** , aby obsługiwać scenariusze w przypadku odmowy potwierdzenia (użytkownik nie został wskazany).

          1. Zmień nazwę na **odmowa temperatury potwierdzenia**.
          1. Pozostaw istniejące **potwierdzenie o nieoczekiwanym** stanie.
          1. Dodaj nowy warunek: **wpisz**  >  **wymagane parametry**  >  **temperatura**.
          1. Dodaj nową akcję: **wpisz**  >  **wysyłanie odpowiedzi na mowę**  >  **nie ma problemu. Jakiej temperatury potem?**.
          1. Zmień domyślną wartość **stanu po wykonaniu** , aby **czekać na dane wejściowe użytkownika**.

> [!IMPORTANT]
> W tym artykule opisano użycie wbudowanej funkcji potwierdzania. Możesz również ręcznie dodać reguły interakcji po jednej.
   
Wypróbuj zmiany, wybierając pozycję **uczenie**. Po zakończeniu szkolenia wybierz pozycję **Testuj**.

- **Dane wejściowe**: *Ustaw temperaturę na 80 stopni*
- **Wynik**: czy na pewno chcesz ustawić temperaturę jako 80 stopni?
- **Dane wejściowe**: *nie*
- **Dane wyjściowe**: brak problemu. Jakiej temperatury potem?
- **Dane wejściowe**: *72 stopni*
- **Wynik**: czy na pewno chcesz ustawić temperaturę jako 72 stopni?
- **Dane wejściowe**: *tak*
- **Wynik**: OK, ustawianie temperatury na 72 stopni

### <a name="implement-corrections-in-a-command"></a>Implementowanie poprawek w poleceniu

Ta sekcja umożliwia skonfigurowanie korekty jednoetapowej. Ta korekta jest używana po uruchomieniu akcji realizacji. Zobaczysz również przykład sposobu, w jaki poprawka jest włączona domyślnie, jeśli polecenie nie zostało jeszcze spełnione. Aby dodać poprawkę, gdy polecenie nie zostało zakończone, Dodaj nowy parametr `AlarmTone` .

W lewym okienku wybierz polecenie **setAlarm** . Następnie Dodaj nowy parametr **AlarmTone**.
        
- **Nazwij** > `AlarmTone`
- **Typ**  >  **Ciąg**
- **Wartość domyślna**  >  **CHIMES**
- **Konfiguracja**  >  **Akceptowanie wstępnie zdefiniowanych wartości wejściowych z wykazu wewnętrznego**
- **Wstępnie zdefiniowane wartości wejściowe**  >  **CHIMES**, **Jingle** i **echo** (te wartości są pojedynczymi wstępnie zdefiniowanymi danymi wejściowymi).


Następnie zaktualizuj odpowiedź dla parametru **DateTime** , aby **ustawić alarm z sygnałem {AlarmTone}. Na jakim czasie?**. Następnie zmodyfikuj regułę uzupełniania w następujący sposób:

1. Wybierz istniejącą regułę ukończenia **ConfirmationResponse**.
1. W okienku po prawej stronie Umieść kursor nad istniejącą akcją i wybierz pozycję **Edytuj**.
1. Zaktualizuj odpowiedź mowy na `OK, alarm set for {DateTime}. The alarm tone is {AlarmTone}` .

> [!IMPORTANT]
> Dźwięk można zmienić bez żadnej konfiguracji jawnej w trwającym poleceniu. Na przykład może ulec zmianie, gdy polecenie nie zostało jeszcze zakończone. Korekta jest *Domyślnie* włączona dla wszystkich parametrów poleceń, niezależnie od liczby wyłączanej, jeśli polecenie nie jest jeszcze spełnione.

#### <a name="implement-a-correction-when-a-command-is-finished"></a>Zaimplementuj korektę po zakończeniu polecenia

Platforma poleceń niestandardowych umożliwia korekcję jednoetapową, nawet gdy polecenie zostało zakończone. Ta funkcja nie jest domyślnie włączona. Musi być jawnie skonfigurowana. 

Wykonaj następujące kroki, aby skonfigurować poprawkę jednoetapową:

1. W przypadku polecenia **setAlarm** Dodaj regułę interakcji **polecenia typu Update Previous** , aby zaktualizować wcześniej ustawiony alarm. Zmień nazwę reguły interakcji jako **aktualizację poprzedniego alarmu**.
1. Pozostaw warunek domyślny: **poprzednie polecenie należy zaktualizować**.
1. Dodaj nowy warunek: **wpisz**  >  **wymaganą**  >  **wartość parametru DateTime**.
1. Dodaj nową akcję: **wpisz**  >  **Wyślij odpowiedź mowy**  >  **Simple Editor**  >  **, aby zaktualizować poprzedni czas alarmu do {DateTime}**.
1. Pozostaw domyślną wartość **stanu po wykonaniu** jako **wykonaną polecenie**.

Wypróbuj zmiany, wybierając pozycję **uczenie**. Poczekaj na zakończenie szkolenia, a następnie wybierz pozycję **Testuj**.

- **Dane wejściowe**: *Ustaw alarm.*
- **Dane wyjściowe**: gotowe do ustawienia alarmu z tonowego jako Chimes. Na jakim czasie?
- **Wejście**: *Ustaw alarm z odcieniem Jingle przez 9 jutro.*
- **Wynik**: OK, ustawiony alarm dla 2020-05-21 09:00:00. Tonu alarmu to Jingle.
- **Dane wejściowe**: *nie, 8 am.*
- **Dane wyjściowe**: aktualizowanie poprzedniego czasu alarmu do 2020-05-29 08:00.

> [!NOTE]
> W prawdziwej aplikacji w sekcji **działania** tej reguły korekcji należy również wysłać do klienta działanie lub wywołać punkt końcowy HTTP, aby zaktualizować czas alarmu w systemie. Ta akcja powinna być odpowiedzialna wyłącznie za aktualizowanie czasu alarmu. Nie powinien być odpowiedzialny za jakikolwiek inny atrybut polecenia. W takim przypadku ten atrybut będzie dźwiękiem.

## <a name="add-language-generation-templates-for-speech-responses"></a>Dodawanie szablonów generacji języka na potrzeby odpowiedzi na mowę

Szablony generowania języka (LG) umożliwiają dostosowywanie odpowiedzi wysyłanych do klienta. Wprowadzają wariancję do odpowiedzi. Generowanie języka można osiągnąć przy użyciu:

* Szablony generowania języka.
* Wyrażenia adaptacyjne.

Szablony poleceń niestandardowych są oparte na [szablonach LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)bot Framework. Ponieważ funkcja poleceń niestandardowych tworzy nowy szablon LG w razie potrzeby (w przypadku odpowiedzi na mowę w parametrach lub akcjach), nie trzeba określać nazwy szablonu LG. 

Dlatego nie musisz definiować szablonu w następujący sposób:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

Zamiast tego można zdefiniować treść szablonu bez nazwy, takie jak:

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający przykład edytora szablonów.](./media/custom-commands/template-editor-example.png)


Ta zmiana wprowadza zmiany do odpowiedzi na mowę, które są wysyłane do klienta programu. W przypadku elementu wypowiedź odpowiednia odpowiedź mowy jest losowo wybierana z podanych opcji.

Korzystając z szablonów LG, można także definiować złożone odpowiedzi mowy dla poleceń przy użyciu wyrażeń adaptacyjnych. Aby uzyskać więcej informacji, zobacz [Format szablonów gł](/azure/bot-service/file-format/bot-builder-lg-file-format#templates). 

Domyślnie funkcja poleceń niestandardowych obsługuje wszystkie funkcje z następującymi niewielkimi różnicami:

* W szablonach LG jednostki są reprezentowane jako `${entityName}` . Funkcja poleceń niestandardowych nie używa jednostek. Można jednak używać parametrów jako zmiennych z `${parameterName}` reprezentacją lub `{parameterName}` reprezentacją.
* Funkcja poleceń niestandardowych nie obsługuje kompozycji i rozwinięcia szablonu, ponieważ nie można bezpośrednio edytować pliku *. LG* . Edytujesz tylko odpowiedzi automatycznie utworzonych szablonów.
* Funkcja poleceń niestandardowych nie obsługuje funkcji niestandardowych, które są wstrzykiwane. Obsługiwane są wstępnie zdefiniowane funkcje.
* Funkcja poleceń niestandardowych nie obsługuje opcji, takich jak `strict` , `replaceNull` , i `lineBreakStyle` .

### <a name="add-template-responses-to-a-turnonoff-command"></a>Dodawanie odpowiedzi szablonu do polecenia TurnOnOff

Zmodyfikuj `TurnOnOff` polecenie, aby dodać nowy parametr. Użyj poniższej konfiguracji.

| Ustawienie            | Sugerowana wartość       | 
| ------------------ | --------------------- | 
| **Nazwa**               | `SubjectContext`         | 
| **Jest globalny**          | Niezaznaczone             | 
| **Wymagane**           | Niezaznaczone               | 
| **Typ**               | **Ciąg**                |
| **Wartość domyślna**      | `all` |
| **Konfiguracja**      | **Akceptowanie wstępnie zdefiniowanych wartości wejściowych z wykazu wewnętrznego** | 
| **Wstępnie zdefiniowane wartości wejściowe** | `room`, `bathroom`, `all`|

#### <a name="modify-a-completion-rule"></a>Modyfikowanie reguły uzupełniania

Edytuj sekcję **Actions** istniejącej reguły ukończenia **ConfirmationResponse**. W oknie **Edycja akcji** przejdź do **edytora szablonów**. Następnie zastąp tekst następującym przykładem.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

Uczenie i testowanie aplikacji przy użyciu następujących danych wejściowych i wyjściowych. Zwróć uwagę na odmianę odpowiedzi. Odmiana jest tworzona przez wiele wariantów wartości szablonu, a także za pomocą wyrażeń adaptacyjnych.

* Wejście: *Włączanie telewizora*
* Wynik: OK, Włączanie telewizora
* Wejście: *Włączanie telewizora*
* Wynik: gotowe, włączona telewizja
* Dane wejściowe: *Wyłącz światła*
* Wynik: OK, wyłączanie wszystkich lamp
* Dane wejściowe: Wyłącz *światła pokojowe*
* Wynik: OK, wyłączanie sygnalizatorów pokoju

## <a name="use-a-custom-voice"></a>Korzystanie z niestandardowego głosu

Innym sposobem dostosowywania odpowiedzi na polecenia niestandardowe jest wybranie głosu wyjściowego. Wykonaj następujące kroki, aby przełączyć domyślny głos na niestandardowego użytkownika:

1. W aplikacji polecenia niestandardowe, w okienku po lewej stronie, wybierz pozycję **Ustawienia**.
1. W środkowym okienku wybierz opcję **niestandardowa komunikacja głosowa**.
1. W tabeli wybierz niestandardowy głos lub głos publiczny.
1. Wybierz pozycję **Zapisz**.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający przykładowe zdania i parametry.](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> W przypadku głosów publicznych typy neuronowych są dostępne tylko dla określonych regionów. Aby uzyskać więcej informacji, zobacz sekcję [Obsługiwane regiony usługi Speech Service](./regions.md#standard-and-neural-voices).
>
> Możesz tworzyć niestandardowe głosy na stronie **niestandardowego projektu głosowego** . Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z niestandardowym głosem](./how-to-custom-voice.md).

Teraz aplikacja będzie odpowiadać w wybranym głosowaniu zamiast domyślnego głosu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [zintegrować swoją aplikację z poleceniami niestandardowymi](how-to-custom-commands-setup-speech-sdk.md) za pomocą aplikacji klienckiej przy użyciu zestawu Speech SDK.
* [Skonfiguruj ciągłe wdrażanie](how-to-custom-commands-deploy-cicd.md) dla aplikacji poleceń niestandardowych za pomocą usługi Azure DevOps. 
                      
