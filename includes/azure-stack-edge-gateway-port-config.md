---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/16/2019
ms.author: alkohli
ms.openlocfilehash: 417fbdea3f46dfb3e90ab4890cec5e88c5aa4e07
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523887"
---
| Numer portu.| Do lub do zewnątrz | Zakres portów| Wymagane | Uwagi |
|---------|-----------|-----------|----------|-------|
| TCP 80 (HTTP)|Out|Sieć WAN |Nie|Port wychodzący jest używany na potrzeby dostępu do Internetu w celu pobierania aktualizacji. <br>Wychodzący serwer proxy sieci Web jest konfigurowany przez użytkownika. |
| TCP 443 (HTTPS)|Out|Sieć WAN|Tak|Port wychodzący służy do uzyskiwania dostępu do danych w chmurze.<br>Wychodzący serwer proxy sieci Web jest konfigurowany przez użytkownika.|
| UDP 123 (NTP)|Out|Sieć WAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy jest używany internetowy serwer NTP.  |   
| UDP 53 (DNS)|Out|Sieć WAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy jest używany internetowy serwer DNS.<br>Zalecamy korzystanie z lokalnego serwera DNS. |
| TCP 5985 (WinRM)|Out/in|Sieć LAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany do nawiązania połączenia z urządzeniem za pośrednictwem zdalnego programu PowerShell za pośrednictwem protokołu HTTP.  |
| UDP 67 (DHCP)|Out|Sieć LAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy używany jest lokalny serwer DHCP.  |
| TCP 80 (HTTP)|Out/in|Sieć LAN|Tak|Ten port jest portem wejściowym lokalnego interfejsu użytkownika na urządzeniu na potrzeby lokalnego zarządzania. <br>Dostęp do lokalnego interfejsu użytkownika za pośrednictwem protokołu HTTP zostanie automatycznie przekierowany do protokołu HTTPS.  |
| TCP 443 (HTTPS)|Out/in|Sieć LAN|Tak|Ten port jest portem wejściowym lokalnego interfejsu użytkownika na urządzeniu na potrzeby lokalnego zarządzania. Ten port służy również do łączenia Azure Resource Manager z lokalnymi interfejsami API urządzenia, do łączenia usługi BLOB Storage za pośrednictwem interfejsów API REST oraz z usługą tokenu zabezpieczającego (STS) w celu uwierzytelniania za pomocą tokenów dostępu i odświeżania.|
| TCP 445 (SMB)|W|Sieć LAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko w przypadku łączenia się za pośrednictwem protokołu SMB. |
| TCP 2049 (NFS)|W|Sieć LAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko w przypadku łączenia się za pośrednictwem systemu plików NFS. |


