---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "68361546"
---
### <a name="bastion-tier"></a>Warstwa bastionu

Host bastionu jest opcjonalnym składnikiem, który można wykorzystać jako serwer skoku, aby uzyskać dostęp do wystąpień aplikacji i bazy danych. Z maszyną wirtualną hosta bastionu może być przypisany publiczny adres IP, mimo że zalecane jest skonfigurowanie połączenia ExpressRoute lub sieci VPN typu lokacja-lokacja w sieci lokalnej w celu zapewnienia bezpiecznego dostępu. W przypadku ruchu przychodzącego należy również otworzyć tylko protokół SSH (port 22, Linux) lub RDP (Port 3389, Windows Server). Aby zapewnić wysoką dostępność, należy wdrożyć hosta bastionu w dwóch strefach dostępności lub w jednym zestawie dostępności.

Na maszynach wirtualnych można również włączyć przekazywanie przez agenta SSH, co umożliwia dostęp do innych maszyn wirtualnych w sieci wirtualnej przez przekazanie poświadczeń z hosta bastionu. Można też użyć tunelowania SSH, aby uzyskać dostęp do innych wystąpień.

Oto przykład przekazywania agentów:

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

To polecenie nawiązuje połączenie z bastionu, a następnie natychmiast `ssh` ponownie działa, aby uzyskać terminal w wystąpieniu docelowym. Jeśli klaster jest skonfigurowany inaczej, może być konieczne określenie użytkownika innego niż root w wystąpieniu docelowym. `-A`Argument przekazuje połączenie z agentem, aby Twój klucz prywatny na komputerze lokalnym był używany automatycznie. Należy zauważyć, że przekazywanie agentów jest łańcuchem, więc drugie `ssh` polecenie zawiera również, `-A` tak aby wszystkie kolejne połączenia SSH inicjowane z wystąpienia docelowego również używały lokalnego klucza prywatnego.