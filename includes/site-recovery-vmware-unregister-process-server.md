---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: bff9457220efbb6a6b318550a5b2dd9af4ae230f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361397"
---
Wykonaj kroki opisane w określonych okolicznościach.

### <a name="unregister-a-connected-process-server"></a>Wyrejestruj podłączony serwer przetwarzania

1. Nawiąż połączenie zdalne z serwerem przetwarzania jako administrator.
2. W **Panelu sterowania**Otwórz aplet **programy > Odinstaluj program**.
3. Odinstaluj program **Microsoft Azure Site Recovery usługa mobilności/główny serwer docelowy**.
4. Odinstaluj program **Microsoft Azure Site Recovery Configuration/Process Server**.
5. Po odinstalowaniu programów w krokach 3 i 4 Odinstaluj **Microsoft Azure Site Recovery zależności serwera konfiguracji/procesu**.

### <a name="unregister-a-disconnected-process-server"></a>Wyrejestrowywanie odłączonego serwera przetwarzania

Te kroki należy wykonać tylko wtedy, gdy nie istnieje sposób, aby przywrócić maszynę, na której zainstalowano serwer przetwarzania.

1. Zaloguj się na serwerze konfiguracji jako administrator.
2. Otwórz wiersz polecenia z uprawnieniami administracyjnymi i przejdź do `%ProgramData%\ASR\home\svsystems\bin` .
3. Uruchom to polecenie, aby uzyskać listę jednego lub większej liczby serwerów przetwarzania.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. Nie: numer seryjny serwera przetwarzania.
    - IP/Nazwa: adres IP i nazwa komputera, na którym uruchomiono serwer przetwarzania.
    - Puls: ostatni puls z maszyny serwera przetwarzania.
    ! [Zrzut ekranu pokazuje wyświetlany w postaci zwykłego tekstu informacje o serwerach przetwarzania i tekście wybierz jeden z powyższych serwerów, aby wyrejestrować. (Media/Site-Recovery-VMware-Unregister-Process-Server/Unregister-cmd.PNG)

4. Określ numer seryjny serwera przetwarzania, który chcesz wyrejestrować.
5. Wyrejestrowanie serwera przetwarzania Usuń wszystkie jego szczegóły z systemu i wyświetli komunikat: **pomyślnie wyrejestrowano nazwę serwera> (serwer-IP-adres)**

