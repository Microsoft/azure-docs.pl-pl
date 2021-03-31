---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8c60e0275853f3c879db22f5414f0fbbbdb47b85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86050434"
---
## <a name="preparing-for-updates"></a>Przygotowywanie do aktualizacji
Przed przeskanowaniem i zastosowaniem aktualizacji należy wykonać następujące czynności:

1. Wykonaj migawkę w chmurze danych urządzenia.
2. Upewnij się, że stałe adresy IP kontrolera są rutowane i mogą łączyć się z Internetem. Te stałe adresy IP będą używane do obsługi aktualizacji urządzenia. Można to sprawdzić, uruchamiając następujące polecenie cmdlet na każdym kontrolerze z interfejsu programu Windows PowerShell urządzenia:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network>`
   
    **Przykładowe dane wyjściowe dla Test-Connection, gdy stałe adresy IP mogą łączyć się z Internetem**

    ```output
    Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com

    Source      Destination     IPV4Address      IPV6Address
    ----------------- -----------  -----------
    HCSNODE0  bing.com        204.79.197.200
    HCSNODE0  bing.com        204.79.197.200
    HCSNODE0  bing.com        204.79.197.200
    HCSNODE0  bing.com        204.79.197.200

    Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

    Source      Destination       IPV4Address    IPV6Address
    ----------------- -----------  -----------
    HCSNODE0  204.79.197.200  204.79.197.200
    HCSNODE0  204.79.197.200  204.79.197.200
    HCSNODE0  204.79.197.200  204.79.197.200
    HCSNODE0  204.79.197.200  204.79.197.200
    ```

Po pomyślnym wykonaniu tych czynności ręcznych wstępne sprawdzanie można przeprowadzić skanowanie i zainstalowanie aktualizacji.

