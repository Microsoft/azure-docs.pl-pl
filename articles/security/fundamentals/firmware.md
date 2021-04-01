---
title: Zabezpieczenia oprogramowania układowego — zabezpieczenia platformy Azure
description: Dowiedz się, w jaki sposób firma Microsoft zabezpiecza sprzęt i oprogramowanie układowe platformy Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 8233cc714d977083f4d55716d35c0b7094a069ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94557959"
---
# <a name="firmware-security"></a>Zabezpieczenia oprogramowania układowego
W tym artykule opisano, jak firma Microsoft zabezpiecza ekosystem sprzętu w chmurze i łańcuchy dostaw.

## <a name="securing-the-cloud-hardware-ecosystem"></a>Zabezpieczanie ekosystemu sprzętu chmury
Firma Microsoft aktywnie współpracuje z ekosystemem sprzętowym w chmurze w celu zapewnienia ciągłego ulepszania zabezpieczeń:

- Współpraca z partnerami sprzętu i oprogramowania układowego platformy Azure (na przykład producentami składników i integratorami systemów) w celu spełnienia wymagań w zakresie zabezpieczeń sprzętu i oprogramowania układowego platformy Azure.

- Umożliwienie partnerom ciągłego oceniania i ulepszania swoich produktów stan zabezpieczeń, korzystając z wymagań zdefiniowanych przez firmę Microsoft w obszarach takich jak:

  - Bezpieczny rozruch oprogramowania układowego
  - Bezpieczne odzyskiwanie oprogramowania układowego
  - Bezpieczna aktualizacja oprogramowania układowego
  - Kryptografia oprogramowania układowego
  - Sprzęt zablokowany
  - Szczegółowe dane telemetryczne debugowania
  - Obsługa sprzętu TPM 2,0 do włączania mierzonego rozruchu

- Zaangażowanie w i Współtworzenie projektu zabezpieczeń [Open COMPUTE Project (OCP)](https://www.opencompute.org/wiki/Security) przez opracowywanie specyfikacji. Specyfikacje mają na celu podwyższenie poziomu spójności i przejrzystości dla bezpiecznego projektowania i architektury w ekosystemie.

   > [!NOTE]
   > Przykładem naszego udziału w projekcie w języku OCP Security jest specyfikacja [bezpiecznego rozruchu sprzętowego](https://docs.google.com/document/d/1Se1Dd-raIZhl_xV3MnECeuu_I0nF-keg4kqXyK4k4Wc/edit#heading=h.5z2d7x9gbhk0) .

## <a name="securing-hardware-and-firmware-supply-chains"></a>Zabezpieczanie łańcuchów zaopatrzenia sprzętowego i oprogramowania układowego
Dostawcy sprzętu w chmurze i dostawcy platformy Azure są również zobowiązani do zapewnienia bezpieczeństwa łańcucha i wymagań opracowanych przez firmę Microsoft. Proces tworzenia i wdrażania sprzętu i oprogramowania układowego jest wymagany do przestrzegania procesów Microsoft [Security Development](https://www.microsoft.com/securityengineering/sdl) Process (SDL), takich jak:

- Threat Modeling
- Bezpieczne przeglądy projektu
- Recenzje oprogramowania układowego i testowanie penetracji
- Bezpieczne środowiska kompilowania i testowania
- Zarządzanie lukami w zabezpieczeniach i reagowanie na zdarzenia

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat tego, co robimy, aby zwiększyć integralność i bezpieczeństwo platformy, zobacz:

- [Bezpieczny rozruch](secure-boot.md)
- [Mierzony zaświadczanie rozruchu i hosta](measured-boot-host-attestation.md)
- [Projekt Cerberus](project-cerberus.md)
- [Szyfrowanie danych magazynowanych](encryption-atrest.md)
- [Zabezpieczenia funkcji hypervisor](hypervisor.md)