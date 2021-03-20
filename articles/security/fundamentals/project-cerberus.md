---
title: Integralność oprogramowania układowego — zabezpieczenia platformy Azure
description: Zapoznaj się z miarami kryptograficznymi w celu zapewnienia integralności oprogramowania układowego.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: f085858a9d550623704efd4f051ed525e55a37e0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94557854"
---
# <a name="project-cerberus"></a>Projekt Cerberus

Cerberus jest zgodnym z certyfikatem głównym sprzętu NIST 800-193 z tożsamością, której nie można sklonować. Usługa Cerberus została zaprojektowana w celu dokładniejszego podniesienia poziomu zabezpieczeń stan infrastruktury platformy Azure, zapewniając mocną kotwicę zaufania dla integralności oprogramowania układowego.

## <a name="enabling-an-anchor-of-trust"></a>Włączanie kotwicy zaufania
Każdy mikroukład Cerberus ma unikatową tożsamość kryptograficzną, która została ustanowiona przy użyciu podpisanego łańcucha certyfikatów do urzędu certyfikacji (CA) firmy Microsoft. Pomiary uzyskane z Cerberus mogą służyć do weryfikowania integralności składników, takich jak:

- Host
- Kontroler zarządzania płytą główną (BMC)
- Wszystkie urządzenia peryferyjne, w tym karta sieciowa i [system-on-Chip](https://en.wikipedia.org/wiki/System_on_a_chip) (SOC)

To zakotwiczenie zaufania pomaga chronić oprogramowanie układowe platformy przed:

- Naruszone pliki binarne oprogramowania układowego uruchomione na platformie
- Złośliwe oprogramowanie i hakerzy wykorzystujący usterki w systemie operacyjnym, aplikacji lub funkcji hypervisor
- Niektóre typy ataków łańcucha dostaw (produkcja, zestaw, tranzyt)
- Złośliwe testerzy z uprawnieniami administracyjnymi lub dostępem do sprzętu

## <a name="cerberus-attestation"></a>Zaświadczanie Cerberus
Cerberus uwierzytelnia integralność oprogramowania układowego dla składników serwera za pomocą manifestu oprogramowania układowego platformy (PFM). PFM definiuje listę autoryzowanych wersji oprogramowania układowego i zapewnia pomiar platformy dla [usługi zaświadczania hosta](measured-boot-host-attestation.md)platformy Azure. Usługa zaświadczania hosta weryfikuje pomiary i dokonuje wyznaczania tylko zaufanych hostów do przyłączenia floty platformy Azure i obsługi obciążeń klientów.

W połączeniu z usługą zaświadczania hosta usługi Cerberus "rozszerzają i promują wysoce bezpieczną infrastrukturę produkcyjną platformy Azure.

> [!NOTE]
> Aby dowiedzieć się więcej, zobacz informacje o [projekcie Cerberus](https://github.com/opencomputeproject/Project_Olympus/tree/master/Project_Cerberus) w witrynie GitHub.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat tego, co robimy, aby zwiększyć integralność i bezpieczeństwo platformy, zobacz:

- [Zabezpieczenia oprogramowania układowego](firmware.md)
- [Bezpieczny rozruch](secure-boot.md)
- [Mierzony zaświadczanie rozruchu i hosta](measured-boot-host-attestation.md)
- [Szyfrowanie danych magazynowanych](encryption-atrest.md)
- [Zabezpieczenia funkcji hypervisor](hypervisor.md)