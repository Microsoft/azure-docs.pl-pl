---
title: 'Instrukcje: Dodawanie potwierdzenia do polecenia niestandardowego'
titleSuffix: Azure Cognitive Services
description: W tym artykule dowiesz się, jak zaimplementować potwierdzenia dla polecenia w poleceniach niestandardowych.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 5f66e29e4c1bc85981202251e0de8288f4baee4e
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307839"
---
# <a name="add-interaction-rules"></a>Dodawanie reguł interakcji

W tym artykule omówiono **reguły interakcji**. Są to dodatkowe reguły umożliwiające obsługę bardziej szczegółowych lub złożonych sytuacji. W tym artykule możesz tworzyć własne niestandardowe reguły interakcji, korzystając z reguł interakcji w następujących scenariuszach:

* Potwierdzanie poleceń
* Dodawanie jednoetapowych poprawek do poleceń

Przejdź do sekcji [odwołania](./custom-commands-references.md) , aby dowiedzieć się więcej na temat reguł interakcji.

## <a name="prerequisites"></a>Wymagania wstępne

Należy wykonać czynności opisane w następujących artykułach:
> [!div class="checklist"]
> * [Instrukcje: Tworzenie aplikacji przy użyciu prostych poleceń](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Instrukcje: Dodawanie parametrów do poleceń](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="add-confirmations-to-a-command"></a>Dodawanie potwierdzeń do polecenia

Aby dodać potwierdzenie, użyj polecenia **Settemperatura** . W celu uzyskania potwierdzenia można utworzyć reguły interakcji, wykonując poniższe kroki.

1. W okienku po lewej stronie wybierz polecenie **Settemperaturę** .
2. Dodaj reguły interakcji, wybierając pozycję **Dodaj** w środkowym okienku, a następnie wybierając pozycję **zasady interakcji**  >  **Potwierdź polecenie**.

    Spowoduje to dodanie 3 reguł interakcji, że ta reguła poprosiła użytkownika o potwierdzenie daty i godziny alarmu i oczekuje na potwierdzenie (tak/nie) w następnym przypadku.

    1. Zmodyfikuj regułę interakcji z **poleceniem Potwierdź** zgodnie z następującą konfiguracją
        1. Zmień **nazwę nazwy** na **`Confirm Temperature`** .
        1. Dodaj nowy warunek jako **parametry wymagane > temperatury**
        1. Dodaj nową akcję jako **typ > wysyłanie odpowiedzi na mowę > `Are you sure you want to set the temperature as {Temperature} degrees?` **
        1. Pozostaw wartość domyślną **oczekiwanego potwierdzenia od użytkownika** w sekcji oczekiwania.
      
         > [!div class="mx-imgBorder"]
         > ![Utwórz wymaganą odpowiedź parametru](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Zmodyfikuj regułę interakcji **pomyślnie** , aby obsłużyć pomyślne potwierdzenie (użytkownik ten ma wartość tak).
      
          1. Zmodyfikuj **nazwę** na **`Confirmation temperature succeeded`** .
          1. Pozostaw już istniejące **potwierdzenie zostało zakończone pomyślnie** .
          1. Dodaj nowy warunek jako **typ > wymaganych parametrów > temperatury**
          1. Pozostaw wartość domyślną **stanu po wykonaniu** jako **reguły ukończenia wykonywania**.

    1. Zmodyfikuj **odmowę** (użytkownik nie), aby obsłużyć scenariusze w przypadku odmowy potwierdzenia.

          1. Zmodyfikuj **nazwę** na **`Confirmation temperature denied`** .
          1. Pozostaw już istniejące **potwierdzenie zostało odrzucone** .
          1. Dodaj nowy warunek jako **typ > wymaganych parametrów > temperatury**
          1. Dodaj nową akcję jako **typ > wysyłanie odpowiedzi na mowę > `No problem. What temperature then?` **
          1. Pozostaw wartość domyślną **stanu po wykonaniu** jako **oczekiwanie na dane wejściowe użytkownika**.

> [!IMPORTANT]
> W tym artykule użyto wbudowanej funkcji zatwierdzania. Alternatywnie można również osiągnąć ten sam sposób, dodając reguły interakcji po jednym, ręcznie.
   

### <a name="try-out-the-changes"></a>Wypróbuj zmiany

Wybierz pozycję **szkolenie**, zaczekaj na zakończenie szkolenia i wybierz pozycję **Testuj**.

- Dane wejściowe: Ustaw temperaturę na 80 stopni
- Wynik: ok 80?
- Dane wejściowe: nie
- Dane wyjściowe: brak problemu. jakiej temperatury potem?
- Dane wejściowe: 83 stopni
- Wynik: ok 83?
- Dane wejściowe: tak
- Wynik: OK, ustawianie temperatury na 83 stopni


## <a name="implementing-corrections-in-a-command"></a>Implementowanie poprawek w poleceniu

W tej sekcji należy skonfigurować korekcję jednoetapową, która jest używana po wykonaniu akcji realizacji. Zobaczysz również przykład sposobu, w jaki poprawka jest włączona domyślnie na wypadek, gdyby polecenie nie zostało jeszcze spełnione. Aby dodać poprawkę, gdy polecenie nie zostało zakończone, Dodaj nowy parametr **AlarmTone**.

Wybierz pozycję **setAlarm** Command w lewym okienku i Dodaj nowy parametr **AlarmTone**.
        
- **Nazwij** > `AlarmTone`
- **Wpisz** ciąg >
- **Wartość domyślna** > `Chimes`
- > **konfiguracji** akceptują wstępnie zdefiniowane wartości wejściowe z wykazu wewnętrznego
- **Wstępnie zdefiniowane wartości wejściowe**  >  `Chimes` , `Jingle` i `Echo` . Każdy jako indywidualny wstępnie zdefiniowany dane wejściowe.


Następnie zaktualizuj odpowiedź dla parametru DateTime na `Ready to set alarm with tone as {AlarmTone}. For what time?` . Następnie zmodyfikuj regułę uzupełniania w następujący sposób.

1. Wybierz istniejącą regułę ukończenia **ConfirmationResponse**.
1. W prawym panelu Umieść kursor nad istniejącą akcją i wybierz przycisk **Edytuj** .
1. Aktualizuj odpowiedź mowy na`Ok, alarm set for {DateTime}. The alarm tone is {AlarmTone}.`

### <a name="try-out-the-changes"></a>Wypróbuj zmiany

Wybierz pozycję **szkolenie**, zaczekaj na zakończenie szkoleń i wybierz pozycję **Testuj**.
Wypróbuj następujące wyrażenia długości:

- Dane wejściowe: ustaw alarm
- Dane wyjściowe: gotowe do ustawienia alarmu z tonowego jako Chimes. Na jakim czasie?
- Wejście: ustaw alarm z tonowego jako jingle dla 9 am/jutro
- Wynik: OK, ustawiony alarm dla 2020-05-30 09:00:00. Tonu alarmu to Jingle.

> [!IMPORTANT]
> Należy zauważyć, jak dźwięk można zmienić bez żadnej konfiguracji jawnej w trwającym poleceniu, tj. gdy polecenie nie zostało jeszcze ukończone. **Korekta jest domyślnie włączona dla wszystkich parametrów poleceń, niezależnie od liczby wyłączanej, jeśli polecenie nie jest jeszcze spełnione.**

### <a name="correction-when-command-is-completed"></a>Korekta po zakończeniu polecenia

Platforma poleceń niestandardowych oferuje również możliwość korekty jednoetapowej, nawet gdy polecenie zostało ukończone. Ta funkcja nie jest domyślnie włączona i musi być jawnie skonfigurowana. Aby skonfigurować poprawkę jednoetapową, wykonaj następujące czynności.

1. W przypadku polecenia **setAlarm** Dodaj regułę interakcji typu **Update Previous polecenie** , aby zaktualizować wcześniej ustawiony alarm. Zmień **nazwę domyślnej reguły interakcji, aby** **zaktualizować poprzedni alarm**.
1. Pozostaw **polecenie domyślne poprzedni** warunek należy zaktualizować, jeśli jest to.
1.  Dodaj nowy warunek jako **typ > wymaganego parametru > DateTime**.
1. Dodaj nową akcję jako **typ > wysyłanie odpowiedzi na mowę > prostego edytora > `Updating previous alarm time to {DateTime}.` **
1. Pozostaw wartość domyślną stanu wykonywania po wykonaniu **polecenia**.

### <a name="try-out-the-changes"></a>Wypróbuj zmiany

Wybierz pozycję **szkolenie**, zaczekaj na zakończenie szkolenia i wybierz pozycję **Testuj**.

- Dane wejściowe: ustaw alarm
- Dane wyjściowe: gotowe do ustawienia alarmu z tonowego jako Chimes. O której?
- Wejście: ustaw alarm z tonowego jako jingle dla 9 am/jutro
- Wynik: OK, ustawiony alarm dla 2020-05-21 09:00:00. Tonu alarmu to Jingle.
- Dane wejściowe: nie, 8 am
- Dane wyjściowe: aktualizowanie poprzedniego czasu alarmu do 2020-05-29 08:00.

> [!NOTE]
> W prawdziwej aplikacji w sekcji działania tej reguły korekcji należy również wysłać do klienta działanie lub wywołać punkt końcowy HTTP, aby zaktualizować czas alarmu w systemie. Ta akcja powinna być pojedynczo odpowiedzialna za tylko aktualizację czasu alarmu, a nie innych atrybutów polecenia, w tym przypadku alarmu alarmowego.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Instrukcje: Dodawanie szablonów generowania języka dla odpowiedzi na mowę](./how-to-custom-commands-add-language-generation-templates.md)