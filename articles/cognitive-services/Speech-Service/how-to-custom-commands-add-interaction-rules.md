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
ms.openlocfilehash: 7d6c0928196c9e8e1abf6aa7f724a58753ce3d2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87289041"
---
# <a name="add-interaction-rules"></a>Dodawanie reguł interakcji

W tym artykule omówiono **reguły interakcji**. Są to dodatkowe reguły umożliwiające obsługę bardziej szczegółowych lub złożonych sytuacji. W tym artykule możesz tworzyć własne niestandardowe reguły interakcji, korzystając z reguł interakcji w następujących scenariuszach:

* Potwierdzanie poleceń
* Dodawanie korekty jednoetapowej do poleceń

Aby dowiedzieć się więcej o regułach interakcji, przejdź do sekcji [odwołania](./custom-commands-references.md) .

## <a name="prerequisites"></a>Wymagania wstępne

Należy wykonać czynności opisane w następujących artykułach:
> [!div class="checklist"]
> * [Instrukcje: Tworzenie aplikacji przy użyciu prostych poleceń](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Instrukcje: Dodawanie parametrów do poleceń](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="add-confirmations-to-a-command"></a>Dodawanie potwierdzeń do polecenia

Aby dodać potwierdzenie, użyj polecenia **Settemperatura** . Aby uzyskać potwierdzenie, należy utworzyć reguły interakcji przy użyciu następujących kroków.

1. Wybierz polecenie **Settemperaturę** w lewym okienku.
1. Dodaj reguły interakcji, wybierając pozycję **Dodaj** w środkowym okienku. Następnie wybierz pozycję polecenia **reguły interakcji**  >  **Potwierdź polecenie**.

    Ta akcja dodaje trzy reguły interakcji, które poproszą użytkownika o potwierdzenie daty i godziny alarmu i oczekuje potwierdzenia (tak/nie) dla następnego wyłączenia.

    1. Zmodyfikuj regułę interakcji z **poleceniem Potwierdź** zgodnie z następującą konfiguracją:
        1. Zmień **nazwę nazwy** , aby **potwierdzić temperaturę**.
        1. Dodaj nowy warunek jako **Required parameters**  >  **temperaturę**wymaganych parametrów.
        1. Dodać nową akcję jako **Typ**  >  **Wyślij odpowiedź na mowę**czy  >  **na pewno chcesz ustawić temperaturę jako wartość {temperatura}?**
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
   

### <a name="try-out-the-changes"></a>Wypróbuj zmiany

Wybierz pozycję **szkolenie**, poczekaj na zakończenie szkolenia, a następnie wybierz pozycję **Testuj**.

- **Dane wejściowe**: Ustaw temperaturę na 80 stopni
- **Wynik**: czy na pewno chcesz ustawić temperaturę jako 80 stopni?
- **Dane wejściowe**: nie
- **Dane wyjściowe**: brak problemu. Jakiej temperatury potem?
- **Dane wejściowe**: 72 stopni
- **Wynik**: czy na pewno chcesz ustawić temperaturę jako 72 stopni?
- **Dane wejściowe**: tak
- **Wynik**: OK, ustawianie temperatury na 83 stopni


## <a name="implement-corrections-in-a-command"></a>Implementowanie poprawek w poleceniu

W tej sekcji należy skonfigurować poprawkę jednoetapową, która jest używana po wykonaniu akcji realizacji. Zobaczysz również przykład sposobu, w jaki poprawka jest włączona domyślnie, jeśli polecenie nie zostało jeszcze spełnione. Aby dodać poprawkę, gdy polecenie nie zostało zakończone, Dodaj nowy parametr **AlarmTone**.

Wybierz polecenie **setAlarm** z okienka po lewej stronie, a następnie Dodaj nowy parametr **AlarmTone**.
        
- **Nazwa**  >  **AlarmTone**
- **Typ**  >  **Ciąg**
- **Wartość domyślna**  >  **CHIMES**
- **Konfiguracja**  >  **Akceptowanie wstępnie zdefiniowanych wartości wejściowych z wykazu wewnętrznego**
- **Wstępnie zdefiniowane wartości wejściowe**  >  **CHIMES**, **Jingle**i **echo** jako indywidualne wstępnie zdefiniowane dane wejściowe


Następnie zaktualizuj odpowiedź dla parametru **DateTime** , aby **ustawić alarm z sygnałem {AlarmTone}. Na jakim czasie?**. Następnie zmodyfikuj regułę uzupełniania w następujący sposób:

1. Wybierz istniejącą regułę ukończenia **ConfirmationResponse**.
1. W prawym okienku Umieść kursor nad istniejącą akcją i wybierz pozycję **Edytuj**.
1. Zaktualizuj odpowiedź mowy na **OK, zestaw alarmowy dla {DateTime}. Dźwięk ma wartość {AlarmTone}.**

### <a name="try-out-the-changes"></a>Wypróbuj zmiany

Wybierz pozycję **szkolenie**, poczekaj na zakończenie szkolenia, a następnie wybierz pozycję **Testuj**.
Wypróbuj następujące wyrażenia długości:

- **Dane wejściowe**: ustaw alarm.
- **Dane wyjściowe**: gotowe do ustawienia alarmu z tonowego jako Chimes. Na jakim czasie?
- **Wejście**: ustaw alarm z odcieniem Jingle przez 9 jutro.
- **Wynik**: OK, ustawiony alarm dla 2020-05-30 09:00:00. Tonu alarmu to Jingle.

> [!IMPORTANT]
> Dźwięk można zmienić bez żadnej konfiguracji jawnej w trwającym poleceniu, na przykład wtedy, gdy polecenie nie zostało jeszcze zakończone. *Korekta jest domyślnie włączona dla wszystkich parametrów poleceń, niezależnie od liczby wyłączanej, jeśli polecenie nie jest jeszcze spełnione.*

### <a name="correction-when-command-is-completed"></a>Korekta po zakończeniu polecenia

Platforma poleceń niestandardowych oferuje również możliwość korekty jednoetapowej, nawet gdy polecenie zostało ukończone. Ta funkcja nie jest domyślnie włączona. Musi być jawnie skonfigurowana. Aby skonfigurować poprawkę jednoetapową, wykonaj następujące czynności.

1. W przypadku polecenia **setAlarm** Dodaj regułę interakcji **polecenia typu Update Previous** , aby zaktualizować wcześniej ustawiony alarm. Zmień **nazwę domyślnej reguły interakcji, aby** **zaktualizować poprzedni alarm**.
1. Pozostaw **polecenie domyślne poprzedni** warunek należy zaktualizować, jeśli jest to.
1. Dodaj nowy warunek jako **Typ**  >  **wymagany parametr**  >  **DateTime**.
1. Dodaj nową akcję jako **Typ**  >  **Wyślij odpowiedź mowy**  >  **prosty edytor**  >  **Aktualizowanie poprzedniego czasu alarmu do {DateTime}.**
1. Pozostaw wartość domyślną **stanu po wykonaniu** , gdy **polecenie zostało ukończone**.

### <a name="try-out-the-changes"></a>Wypróbuj zmiany

Wybierz pozycję **szkolenie**, poczekaj na zakończenie szkolenia, a następnie wybierz pozycję **Testuj**.

- **Dane wejściowe**: ustaw alarm.
- **Dane wyjściowe**: gotowe do ustawienia alarmu z tonowego jako Chimes. Na jakim czasie?
- **Wejście**: ustaw alarm z odcieniem Jingle przez 9 jutro.
- **Wynik**: OK, ustawiony alarm dla 2020-05-21 09:00:00. Tonu alarmu to Jingle.
- **Dane wejściowe**: nie, 8 am.
- **Dane wyjściowe**: aktualizowanie poprzedniego czasu alarmu do 2020-05-29 08:00.

> [!NOTE]
> W prawdziwej aplikacji w sekcji **działania** tej reguły korekcji należy również wysłać do klienta działanie lub wywołać punkt końcowy HTTP, aby zaktualizować czas alarmu w systemie. Ta akcja powinna być odpowiedzialna wyłącznie za aktualizowanie czasu alarmu, a nie innych atrybutów polecenia. W tym przypadku jest to sygnał alarmu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Instrukcje: Dodawanie szablonów generowania języka dla odpowiedzi na mowę](./how-to-custom-commands-add-language-generation-templates.md)
