---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4e262c9e5bb88e77bc9c09853c06f4cdb41eedaa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67183424"
---
## <a name="preparing-for-updates"></a>Przygotowywanie do aktualizacji
Przed przeskanowaniem i zastosowaniem aktualizacji należy wykonać następujące czynności:

1. Wykonaj migawkę w chmurze danych urządzenia.
2. Upewnij się, że stałe adresy IP kontrolera są rutowane i mogą łączyć się z Internetem. Te stałe adresy IP będą używane do obsługi aktualizacji urządzenia. Można to sprawdzić, uruchamiając następujące polecenie cmdlet na każdym kontrolerze z interfejsu programu Windows PowerShell urządzenia:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network>`
   
    **Przykładowe dane wyjściowe dla połączenia testowego, gdy stałe adresy IP mogą łączyć się z Internetem**

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

Po pomyślnym wykonaniu tych czynności ręcznych wstępne sprawdzanie można przeprowadzić skanowanie i zainstalowanie aktualizacji.

