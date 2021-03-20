---
title: Generowanie i eksportowanie certyfikatów dla połączeń sieci VPN użytkownika | Wirtualna sieć WAN platformy Azure
description: Utwórz certyfikat główny z podpisem własnym, wyeksportuj klucz publiczny i Wygeneruj certyfikaty klienta dla połączeń sieci VPN użytkowników przy użyciu programu PowerShell w systemie Windows 10 lub Windows Server 2016.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 2205f170ee846d4db94db7f524a1c424cfbc8f7b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91328042"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>Generowanie i eksportowanie certyfikatów dla połączeń sieci VPN użytkownika

Połączenia sieci VPN użytkownika (punkt-lokacja) używają certyfikatów do uwierzytelniania. W tym artykule pokazano, jak utworzyć certyfikat główny z podpisem własnym i wygenerować certyfikaty klienta przy użyciu programu PowerShell w systemie Windows 10 lub Windows Server 2016.

Kroki opisane w tym artykule należy wykonać na komputerze z systemem Windows 10 lub Windows Server 2016. Polecenia cmdlet programu PowerShell używane do generowania certyfikatów są częścią systemu operacyjnego i nie działają w innych wersjach systemu Windows. Komputer z systemem Windows 10 lub Windows Server 2016 jest wymagany tylko do wygenerowania certyfikatów. Po wygenerowaniu certyfikatów można je przekazać lub zainstalować w dowolnym obsługiwanym systemie operacyjnym klienta.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Następne kroki

Kontynuuj wykonywanie [wirtualnych kroków sieci WAN dla połączenia sieci VPN użytkownika](virtual-wan-about.md)
