---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008489"
---
1. Nawiąż Podłączanie pulpitu zdalnego z maszyną z uruchomionym serwerem przetwarzania. 
2. Uruchom cspsconfigtool.exe, aby uruchomić narzędzie konfiguracji serwera przetwarzania Azure Site Recovery.
    - Narzędzie jest uruchamiane automatycznie przy pierwszym zalogowaniu się do serwera przetwarzania.
    - Jeśli plik nie zostanie otwarty automatycznie, kliknij jego skrót na pulpicie.

3. W polu Nazwa **FQDN lub adres IP serwera konfiguracji** Określ nazwę lub serwer konfiguracji, za pomocą którego ma zostać zarejestrowany serwer przetwarzania.
4. Upewnij się, że w polu **port serwera konfiguracji** określono 443. Jest to port, na którym serwer konfiguracji nasłuchuje żądań.
5. W polu **hasło połączenia** określ hasło określone podczas konfigurowania serwera konfiguracji. Aby znaleźć hasło:
    -  Na serwerze konfiguracji przejdź do folderu instalacji Site Recovery **\home\svssystems\bin \**:
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Uruchom poniższe polecenie, aby wydrukować bieżące hasło:
    ```
    genpassphrase.exe -n
    ```

6. W obszarze **transfer danych port** pozostaw wartość domyślną, chyba że określono port niestandardowy.

7. Kliknij przycisk **Zapisz** Zapisz ustawienia i zarejestruj serwer przetwarzania.

    
    ![Rejestrowanie serwera przetwarzania](./media/site-recovery-vmware-register-process-server/register-ps.png)
