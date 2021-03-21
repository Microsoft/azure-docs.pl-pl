---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 338332eb90fda9afe7655f9f2ad19ed726419b6c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95561711"
---
### <a name="to-configure-and-register-the-device"></a>Konfigurowanie i rejestrowanie urządzenia
1. Przejdź do interfejsu programu Windows PowerShell na konsoli szeregowej urządzenia StorSimple. Instrukcje można znaleźć w temacie [Nawiązywanie połączenia z konsolą szeregową urządzenia przy użyciu programu PuTTY](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#use-putty-to-connect-to-the-device-serial-console). **Pamiętaj, aby dokładnie wykonać procedurę, w przeciwnym wypadku nie uzyskasz dostępu do konsoli.**
2. W otwartej sesji naciśnij raz klawisz **Enter**, aby wyświetlić wiersz polecenia.
3. Zostanie wyświetlony monit o wybranie języka dla danego urządzenia. Wybierz język, a następnie naciśnij klawisz **Enter**.
   
    ![Konfigurowanie i rejestrowanie urządzenia StorSimple 1](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice1-gov-include.png)
4. W przedstawionym menu konsoli szeregowej wybierz opcję 1: **Zaloguj z pełnymi uprawnieniami dostępu**.
   
    ![Rejestrowanie urządzenia StorSimple 2](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice2-gov-include.png)
5. Wykonaj poniższe kroki, aby skonfigurować minimalne wymagane ustawienia sieci dla urządzenia.
   
   > [!IMPORTANT]
   > Te kroki konfiguracji należy wykonać na aktywnym kontrolerze urządzenia. Menu konsoli szeregowej wskazuje stan kontrolera w komunikacie transparentu. Jeśli nie masz połączenia z aktywnym kontrolerem, odłącz się, a następnie połącz się z aktywnym kontrolerem.
   
   1. W wierszu polecenia wpisz hasło. Domyślne hasło urządzenia to **Password1**.
   2. Wpisz następujące polecenie:
      
        `Invoke-HcsSetupWizard`
   3. Zostanie uruchomiony Kreator instalacji, który ułatwi konfigurowanie ustawień sieciowych urządzenia. Wprowadź następujące informacje:
      
      * Adres IP dla interfejsu sieciowego danych 0
      * Maska podsieci
      * Brama
      * Adres IP podstawowego serwera DNS
      * Adres IP podstawowego serwera NTP
      
      > [!NOTE]
      > Być może trzeba będzie poczekać kilka minut na zastosowanie ustawień maski podsieci i DNS.
    
   4. Opcjonalnie Skonfiguruj serwer proxy sieci Web.
      
      > [!IMPORTANT]
      > Konfiguracja serwera proxy sieci Web jest opcjonalna, jednak warto wiedzieć, że w przypadku korzystania z serwera proxy sieci Web można go skonfigurować tylko w tym miejscu. Aby uzyskać więcej informacji, zobacz temat [Konfigurowanie serwera proxy sieci Web dla urządzenia](../articles/storsimple/storsimple-8000-configure-web-proxy.md).
     
6. Naciśnij klawisze CTRL + C, aby zamknąć kreatora instalacji.
8. Uruchom następujące polecenie cmdlet, aby wskazać urządzenie w portalu Microsoft Azure Government (ponieważ domyślnie wskazuje on publiczny klasyczny portal platformy Azure). Spowoduje to ponowne uruchomienie obu kontrolerów. Zaleca się, aby do obu kontrolerów połączyć się jednocześnie z obydwoma sesjami, dzięki czemu można zobaczyć, kiedy każdy kontroler zostanie uruchomiony ponownie.
   
    `Set-CloudPlatform -AzureGovt_US`
   
   Zostanie wyświetlony komunikat z potwierdzeniem. Zaakceptuj wartość domyślną (**Y**).
9. Uruchom następujące polecenie cmdlet, aby wznowić instalację:
   
    `Invoke-HcsSetupWizard`
   
    ![Kreator wznawiania instalacji](./media/storsimple-configure-and-register-device-gov-u2/HCS_ResumeSetup-gov-include.png)
   
10. Zaakceptuj ustawienia sieci. Po zaakceptowaniu poszczególnych ustawień zobaczysz komunikat z walidacją.
11. Ze względów bezpieczeństwa hasło administratora urządzenia wygasa po pierwszej sesji i należy je teraz zmienić. Po wyświetleniu monitu podaj hasło administratora urządzenia. Prawidłowe hasło administratora urządzenia musi zawierać od 8 do 15 znaków. Hasło musi zawierać trzy z wymienionych elementów: małe litery, wielkie litery, cyfry i znaki specjalne.
    
    <br/>![Rejestrowanie urządzenia StorSimple 5](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice5_gov-include.png)
12. W ostatnim kroku Kreatora instalacji wykonywana jest rejestracja urządzenia w usłudze Menedżer urządzeń StorSimple. W takim przypadku wymagany jest klucz rejestracji usługi uzyskany w [kroku 2: Pobieranie klucza rejestracji usługi](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#step-2-get-the-service-registration-key). Po podaniu klucza rejestracji konieczne może być zaczekanie 2-3 minut, zanim urządzenie zostanie zarejestrowane.
    
    > [!NOTE]
    > Możesz nacisnąć klawisze Ctrl + C i zakończyć działanie Kreatora instalacji w dowolnym momencie. Jeżeli wprowadzono wszystkie ustawienia sieciowe (adres IP dla protokołu Data 0, maskę podsieci i bramę), wpisy zostaną zachowane.
    
    ![Postęp rejestracji StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegistrationProgress-gov-include.png)
13. Po zarejestrowaniu urządzenia zostanie wyświetlony klucz szyfrowania danych usługi. Skopiuj ten klucz i zapisz go w bezpiecznym miejscu. **Ten klucz jest wymagany w przypadku klucza rejestracji usługi w celu zarejestrowania dodatkowych urządzeń w usłudze StorSimple Device Manager.** Więcej informacji na temat tego klucza znajduje się w temacie [Zabezpieczenia usługi StorSimple](../articles/storsimple/storsimple-8000-security.md).
    
    ![Rejestrowanie urządzenia StorSimple 7](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice7_gov-include.png)
    > [!IMPORTANT]
    > Aby skopiować tekst z okna konsoli szeregowej, po prostu zaznacz tekst. Następnie możesz wkleić go do schowka lub w dowolnym edytorze tekstów.
    > 
    > NIE używaj **kombinacji klawiszy CTRL + C** , aby skopiować klucz szyfrowania danych usługi. **Naciśnięcie klawiszy CTRL + C** spowoduje zamknięcie kreatora instalacji. W efekcie hasło administratora urządzenia nie zostanie zmienione, a na urządzeniu zostanie przywrócone hasło domyślne.
    
14. Zakończ działanie konsoli szeregowej.
15. Wróć do portalu Azure Government i wykonaj następujące czynności:
    
    1. Przejdź do usługi Menedżer urządzeń StorSimple.
    2. Kliknij pozycję **Urządzenia**. Z listy urządzeń Zidentyfikuj urządzenie, które chcesz wdrożyć. Sprawdź, czy urządzenie pomyślnie nawiązał połączenie z usługą, sprawdzając jego stan. Urządzenie powinno mieć stan **Online**.
            
        Jeśli urządzenie ma stan **Offline**, zaczekaj kilka minut, aż urządzenie przejdzie do trybu online.
       
        Jeśli po kilku minutach urządzenie jest wciąż w trybie offline, sprawdź, czy sieć zapory została skonfigurowana zgodnie z opisem [wymagań sieciowych dotyczących urządzenia StorSimple](../articles/storsimple/storsimple-8000-system-requirements.md).
       
        Sprawdź, czy port 9354 jest otwarty dla komunikacji wychodzącej, ponieważ jest on używany przez usługę Service Bus do komunikacji między usługą i urządzeniem w usłudze Menedżer urządzeń StorSimple.