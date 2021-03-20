---
title: Integralność i bezpieczeństwo platformy Azure — zabezpieczenia platformy Azure
description: Omówienie techniczne integralności i zabezpieczeń platformy Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 4755bc19a645d196487f0b8e0f4d1ef2120723ca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94557983"
---
# <a name="platform-integrity-and-security-overview"></a>Omówienie integralności i zabezpieczeń platformy
Flota Azure składa się z milionów serwerów (hostów) o tysiącach dodawanych codziennie. Tysiące hostów również regularnie przeprowadza konserwację dzięki ponownym uruchomieniu, odświeżaniu i naprawianiu systemu operacyjnego. Zanim host będzie mógł dołączyć do floty i rozpocząć akceptowanie obciążeń klientów, firma Microsoft sprawdza, czy host jest bezpieczny i godny zaufania. Ta weryfikacja gwarantuje, że złośliwe lub przypadkowe zmiany nie wystąpią na składnikach sekwencji rozruchu podczas łańcucha dostaw lub przepływów pracy konserwacji.

## <a name="securing-azure-hardware-and-firmware"></a>Zabezpieczanie sprzętu i oprogramowania układowego platformy Azure
W tej serii artykułów opisano, jak firma Microsoft zapewnia integralność i bezpieczeństwo hostów za pośrednictwem różnych etapów cyklu życia, od produkcji do zachód. Adresy artykułów:
 
- [Zabezpieczenia oprogramowania układowego](firmware.md)
- [Bezpieczny rozruch z interfejsem UEFI](secure-boot.md)
- [Mierzony zaświadczanie rozruchu i hosta](measured-boot-host-attestation.md)
- [Projekt Cerberus](project-cerberus.md)
- [Szyfrowanie danych magazynowanych](encryption-atrest.md)
- [Zabezpieczenia funkcji hypervisor](hypervisor.md)
 
## <a name="next-steps"></a>Następne kroki

- Dowiedz się, w jaki sposób firma Microsoft aktywnie współpracuje z ekosystemem sprzętowym w chmurze, aby zapewnić [lepsze zabezpieczenia oprogramowania układowego](firmware.md).

- Zapoznaj się z Twoją [wspólną odpowiedzialnością w chmurze](shared-responsibility.md).