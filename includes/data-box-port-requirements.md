---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 948f5399ceb931d701341c2cf378482ede94979d
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200302"
---
| Numer portu.| Do lub do zewnątrz | Zakres portów| Wymagane| Uwagi |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|W|Sieć LAN|Tak|Ten port służy do nawiązywania połączenia z interfejsami API REST magazynu w blogu urządzenie Data Box za pośrednictwem protokołu HTTP. W przypadku braku połączenia z interfejsami API REST spowoduje to automatyczne przekierowanie do lokalnego interfejsu użytkownika sieci Web ponad 8443. |
| TCP 443 (HTTPS)|W|Sieć LAN|Tak|Ten port służy do nawiązywania połączenia z interfejsami API REST magazynu w blogu urządzenie Data Box za pośrednictwem protokołu HTTPS. W przypadku braku połączenia z interfejsami API REST spowoduje to automatyczne przekierowanie do lokalnego interfejsu użytkownika sieci Web ponad 8443. |
| TCP 8443 (HTTPS-Alt)|W|Sieć LAN|Tak|Jest to alternatywny port dla protokołu HTTPS i jest używany podczas nawiązywania połączenia z lokalnym interfejsem użytkownika sieci Web w celu zarządzania urządzeniami. |
| TCP 445 (SMB)|Out/in|Sieć LAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy połączenie jest nawiązywane za pośrednictwem protokołu SMB. |
| TCP 2049 (NFS)|Out/in|Sieć LAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest wymagany tylko wtedy, gdy połączenie jest nawiązywane za pośrednictwem systemu plików NFS. |
| TCP 111 (NFS)|Out/in|Sieć LAN|W niektórych przypadkach<br>Zobacz uwagi|Ten port jest używany na potrzeby mapowania rpcbind/portów i jest wymagany tylko w przypadku łączenia się za pośrednictwem systemu plików NFS.  |
