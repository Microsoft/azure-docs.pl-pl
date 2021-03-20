---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 71460af42b4da97a578ae5a3e23a714577e71867
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86218306"
---
| Numer portu.| Do lub do zewnątrz | Zakres portów| Wymagane|   Uwagi |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|Out|Sieć WAN |Nie|Port wychodzący jest używany na potrzeby dostępu do Internetu w celu pobierania aktualizacji. <br>Wychodzący serwer proxy sieci Web jest konfigurowany przez użytkownika. |
| TCP 443 (HTTPS)|Out|Sieć WAN|Tak|Port wychodzący służy do uzyskiwania dostępu do danych w chmurze.<br>Wychodzący serwer proxy sieci Web jest konfigurowany przez użytkownika.|
| UDP 123 (NTP)|Out|Sieć WAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy jest używany internetowy serwer NTP.  |   
| UDP 53 (DNS)|Out|Sieć WAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy jest używany internetowy serwer DNS.<br>Zalecamy korzystanie z lokalnego serwera DNS. |
| TCP 5985 (WinRM)|Out/in|Sieć LAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany do nawiązania połączenia z urządzeniem za pośrednictwem zdalnego programu PowerShell za pośrednictwem protokołu HTTP.  |
| UDP 67 (DHCP)|Out|Sieć LAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy używany jest lokalny serwer DHCP.  |
| TCP 80 (HTTP)|Out/in|Sieć LAN|Tak|Ten port jest portem wejściowym lokalnego interfejsu użytkownika na urządzeniu na potrzeby lokalnego zarządzania. <br>Dostęp do lokalnego interfejsu użytkownika za pośrednictwem protokołu HTTP zostanie automatycznie przekierowany do protokołu HTTPS.  |
| TCP 443 (HTTPS)|Out/in|Sieć LAN|Tak|Ten port jest portem wejściowym lokalnego interfejsu użytkownika na urządzeniu na potrzeby lokalnego zarządzania. |
| TCP 445 (SMB)|W|Sieć LAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko w przypadku łączenia się za pośrednictwem protokołu SMB. |
| TCP 2049 (NFS)|W|Sieć LAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko w przypadku łączenia się za pośrednictwem systemu plików NFS. |
