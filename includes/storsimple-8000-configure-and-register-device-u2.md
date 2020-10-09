---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 56514f5dcf4bfe205ef46ee64dcf4dcf638d4f62
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183436"
---
#### <a name="to-configure-and-register-the-device"></a>Konfigurowanie i rejestrowanie urządzenia

1. Przejdź do interfejsu programu Windows PowerShell na konsoli szeregowej urządzenia StorSimple. Instrukcje można znaleźć w temacie [Nawiązywanie połączenia z konsolą szeregową urządzenia przy użyciu programu PuTTY](#use-putty-to-connect-to-the-device-serial-console). **Pamiętaj, aby dokładnie wykonać procedurę, w przeciwnym wypadku nie uzyskasz dostępu do konsoli.**

2. W otwartej sesji naciśnij raz klawisz **Enter**, aby wyświetlić wiersz polecenia.

3. Zostanie wyświetlony monit o wybranie języka dla danego urządzenia. Wybierz język, a następnie naciśnij klawisz **Enter**.

4. W przedstawionym menu konsoli szeregowej wybierz opcję 1: **Zaloguj z pełnymi uprawnieniami dostępu**.
     Wykonaj kroki 5–12, aby skonfigurować minimalne wymagane ustawienia sieciowe urządzenia. **Te kroki konfiguracji należy wykonać na aktywnym kontrolerze urządzenia.** Menu konsoli szeregowej wskazuje stan kontrolera w komunikacie transparentu. Jeśli nie masz połączenia z aktywnym kontrolerem, zakończ połączenie, a następnie nawiąż połączenie z aktywnym kontrolerem.

5. W wierszu polecenia wpisz hasło. Domyślne hasło urządzenia to **Password1**.

6. Wpisz następujące polecenie: `Invoke-HcsSetupWizard`.

7. Zostanie uruchomiony Kreator instalacji, który ułatwi konfigurowanie ustawień sieciowych urządzenia. Wprowadź następujące informacje:
   
   * Adres IP dla interfejsu sieciowego protokołu DATA 0
   * Maska podsieci
   * Brama
   * Adres IP podstawowego serwera DNS

   Poniżej przedstawiono przykładowe dane wyjściowe.

    ```
        ---------------------------------------------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: 8100-SHX0991003G44MT
        Software Version: 6.3.9600.17759
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Active
        ---------------------------------------------------------------

        Your device needs to be registered with the Microsoft Azure StorSimple Manager service. Please run 'Invoke-HcsSetupWizard' to set up your device.

        Controller0>Invoke-HcsSetupWizard

        Which IP address family would you like to configure on interface Data0?
        [4] IPv4 [6] IPv6 [B] Both (Default is "4"): 4

        Data0 IPv4 address:10.111.111.00
        Data0 IPv4 subnet: 255.255.252.0
        Data0 IPv4 gateway: 10.111.111.11

        IPv4 primary DNS server [10.222.118.154]:10.222.222.111
    ```

    <br>
    W powyższym przykładzie danych wyjściowych system weryfikuje ustawienia sieciowe po każdym kroku procesu.

     > [!NOTE]
     > Być może trzeba będzie poczekać kilka minut, aż ustawienia maski podsieci i serwera DNS zostaną zastosowane. Jeśli zostanie wyświetlony komunikat o błędzie „Check the network connectivity to Data 0” (Sprawdź łączność sieciową z protokołem Data 0), sprawdź połączenie z siecią fizyczną w interfejsie sieciowym protokołu DATA 0 na aktywnym kontrolerze.

8. Opcjonalnie skonfiguruj serwer proxy sieci Web. Konfiguracja serwera proxy sieci Web jest opcjonalna, jednak **warto wiedzieć, że w przypadku korzystania z serwera proxy sieci Web można go skonfigurować tylko w tym miejscu**. Aby uzyskać więcej informacji, zobacz temat [Konfigurowanie serwera proxy sieci Web dla urządzenia](../articles/storsimple/storsimple-8000-configure-web-proxy.md).
9. Skonfiguruj podstawowy serwer NTP dla urządzenia. Serwery NTP są wymagane, ponieważ urządzenie musi synchronizować czas do celów uwierzytelniania dostawców usługi w chmurze. Upewnij się, że sieć zezwala na ruch NTP z centrum danych do Internetu. Jeśli nie jest to możliwe, określ wewnętrzny serwer NTP.

    Poniżej pokazano przykładowe dane wyjściowe.

    ```
        Would you like to configure a web proxy?
        [Y] Yes [N] No (Default is "N"):N

        Primary NTP server [time.windows.com]:time.windows.com

    ```

10. Ze względów bezpieczeństwa hasło administratora urządzenia wygasa po pierwszej sesji i należy je teraz zmienić. Po wyświetleniu monitu podaj hasło administratora urządzenia. Prawidłowe hasło administratora urządzenia musi zawierać od 8 do 15 znaków. Hasło musi zawierać trzy z wymienionych elementów: małe litery, wielkie litery, cyfry i znaki specjalne.

    ```
        The device administrator password must be between 8 and 15 characters. The password must contain a combination of uppercase letters, lowercase letters, numbers and special characters.
        Administrator Password:********
        Confirm Administrator Password:********
    ```
11. W ostatnim kroku Kreatora instalacji wykonywana jest rejestracja urządzenia w usłudze Menedżer urządzeń StorSimple. Do tego celu potrzebny jest klucz rejestracji usługi uzyskany w kroku 2. Po podaniu klucza rejestracji konieczne może być zaczekanie 2-3 minut, zanim urządzenie zostanie zarejestrowane.
    
    > [!NOTE]
    > Możesz nacisnąć klawisze Ctrl + C i zakończyć działanie Kreatora instalacji w dowolnym momencie. Jeżeli wprowadzono wszystkie ustawienia sieciowe (adres IP dla protokołu Data 0, maskę podsieci i bramę), wpisy zostaną zachowane.
    
    Poniżej pokazano przykładowe dane wyjściowe.

    ```
        The service registration key is available in the StorSimple Manager service.
        Enter service registration key:**************************************
        Device registration is in progress. Please wait.

    ```

12. Po zarejestrowaniu urządzenia zostanie wyświetlony klucz szyfrowania danych usługi. Skopiuj ten klucz i zapisz go w bezpiecznym miejscu. **Ten klucz będzie wymagany razem z kluczem rejestracji usługi w celu rejestracji dodatkowych urządzeń w usłudze Menedżer urządzeń StorSimple.** Więcej informacji na temat tego klucza znajduje się w temacie [Zabezpieczenia usługi StorSimple](../articles/storsimple/storsimple-security.md).
    
    ![Rejestrowanie urządzenia StorSimple 7](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup1.png)
    
    > [!NOTE]
    > Aby skopiować tekst z okna konsoli szeregowej, po prostu zaznacz tekst. Następnie możesz wkleić go do schowka lub w dowolnym edytorze tekstów. NIE używaj klawiszy Ctrl + C do kopiowania klucza szyfrowania danych usługi. Użycie klawiszy Ctrl + C spowoduje zakończenie działania Kreatora instalacji. W efekcie hasło administratora urządzenia nie zostanie zmienione, a na urządzeniu zostanie przywrócone hasło domyślne.
    
13. Zakończ działanie konsoli szeregowej.
14. Wróć do witryny Azure Portal i wykonaj następujące czynności:
    
    1. Przejdź do usługi Menedżer urządzeń StorSimple.
    2. Kliknij pozycję **Urządzenia**.
    3. Na tabelarycznej liście urządzeń zweryfikuj, czy urządzenie pomyślnie nawiązało połączenie z usługą, sprawdzając jego stan. Urządzenie powinno mieć stan **Gotowe do skonfigurowania**.
       
        ![Strona Urządzenia StorSimple](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup2.png)
       
        Być może trzeba będzie poczekać kilka minut na zmianę stanu urządzenia na **Gotowe do skonfigurowania**.
       
        Jeśli urządzenie nie jest wyświetlane na tej liście, musisz sprawdzić, czy sieć zapory została skonfigurowana zgodnie z opisem [wymagań sieciowych dotyczących urządzenia StorSimple](../articles/storsimple/storsimple-8000-system-requirements.md). Sprawdź, czy port 9354 jest otwarty dla komunikacji wychodzącej, ponieważ jest on używany przez usługę Service Bus do komunikacji między usługą i urządzeniem w usłudze Menedżer urządzeń StorSimple.

