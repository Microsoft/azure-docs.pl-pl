---
title: Zabezpieczenia funkcji hypervisor na platformie Azure — zabezpieczenia platformy Azure
description: Omówienie techniczne zabezpieczeń funkcji hypervisor w floty platformy Azure.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 766266edd663b75b893a5883e30bb48eed7bbfdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94696119"
---
# <a name="hypervisor-security-on-the-azure-fleet"></a>Zabezpieczenia funkcji hypervisor w flotie platformy Azure

System funkcji hypervisor platformy Azure jest oparty na funkcji Windows Hyper-V. System funkcji hypervisor pozwala administratorowi komputera określić partycje gościa, które mają oddzielne przestrzenie adresowe. Oddzielne przestrzenie adresowe umożliwiają załadowanie systemu operacyjnego i aplikacji działających równolegle z systemem operacyjnym (hosta), który jest wykonywany w partycji głównej komputera. System operacyjny hosta (znany również jako uprzywilejowana partycja główna) ma bezpośredni dostęp do wszystkich urządzeń fizycznych i urządzeń peryferyjnych w systemie (Kontrolery magazynu, adaptacje sieciowe). System operacyjny hosta umożliwia partycjom gościa współużytkowanie tych urządzeń fizycznych przez udostępnienie "urządzeń wirtualnych" każdej partycji gościa. W rezultacie system operacyjny działający na partycji gościa ma dostęp do zwirtualizowanych urządzeń peryferyjnych udostępnianych przez usługi wirtualizacji wykonywane na partycji głównej.

Funkcja hypervisor platformy Azure jest zbudowana z myślą o następujących celach zabezpieczeń:

| Cel | Element źródłowy |
|--|--|
| Izolacja | Zasada zabezpieczeń nie zezwala na przesyłanie informacji między maszynami wirtualnymi. To ograniczenie wymaga możliwości w Virtual Machine Manager (VMM) i sprzętu do izolacji pamięci, urządzeń, sieci i zasobów zarządzanych, takich jak utrwalone dane. |
| Integralność programu VMM | Aby uzyskać ogólną integralność systemu, integralność poszczególnych składników funkcji hypervisor jest ustanawiana i utrzymywana. |
| Integralność platformy | Integralność funkcji hypervisor zależy od integralności sprzętu i oprogramowania, z którego korzysta. Mimo że funkcja hypervisor nie ma bezpośredniej kontroli nad integralnością platformy, platforma Azure opiera się na mechanizmach sprzętowych i oprogramowania układowego, takich jak mikroukład [Cerberus](project-cerberus.md) , aby chronić i wykrywać integralną platformę. Program VMM i Goście nie mogą działać, jeśli zostanie naruszona integralność platformy. |
| Ograniczony dostęp | Funkcje zarządzania są wykonywane tylko przez autoryzowanych administratorów połączonych za pośrednictwem bezpiecznych połączeń. Zasada najniższych uprawnień jest wymuszana przez mechanizmy kontroli dostępu opartej na rolach (RBAC) na platformie Azure. |
| Inspekcja | Platforma Azure umożliwia inspekcję i ochronę danych o tym, co się dzieje w systemie, aby można było je później sprawdzić. |

Podejście firmy Microsoft do ograniczania funkcjonalności funkcji hypervisor platformy Azure i podsystemu wirtualizacji można podzielić na następujące trzy kategorie.

## <a name="strongly-defined-security-boundaries-enforced-by-the-hypervisor"></a>Silnie zdefiniowane granice zabezpieczeń wymuszane przez funkcję hypervisor

Funkcja hypervisor platformy Azure wymusza stosowanie wielu granic zabezpieczeń między:

- Zwirtualizowane partycje "Gość" i partycja uprzywilejowana ("host")
- Wielu Gości
- Samego siebie i hosta
- Sama i wszyscy Goście

Zapewniona jest poufność, integralność i dostępność dla granic zabezpieczeń funkcji hypervisor. Granice obrony przed zakresem ataków, w tym wycieki informacji kanału bocznego, odmowa usługi i podniesienie uprawnień.

Granica zabezpieczeń funkcji hypervisor zapewnia również segmentację między dzierżawcami dla ruchu sieciowego, urządzeń wirtualnych, magazynu, zasobów obliczeniowych i wszystkich innych zasobów maszyn wirtualnych.

## <a name="defense-in-depth-exploit-mitigations"></a>Środki zaradcze w ramach ochrony szczegółowej

W przypadku wystąpienia najprawdopodobniej luki w zabezpieczeniach, funkcja hypervisor platformy Azure zawiera wiele warstw środków zaradczych, takich jak:

- Izolacja procesów opartych na hoście obsługujących składniki między MASZYNami wirtualnymi
- Zabezpieczenia oparte na wirtualizacji (VBS), aby zapewnić integralność składników trybu użytkownika i jądra z bezpiecznego świata
- Wiele poziomów rozwiązywania problemów z wykorzystaniem. Środki zaradcze obejmują generowanie losowe układu przestrzeni adresowej (ASLR), zapobieganie wykonywaniu danych (DEP), arbitralną ochronę kodu, integralność przepływu sterowania i zapobieganie uszkodzeniem danych
- Automatyczne Inicjowanie zmiennych stosu na poziomie kompilatora
- Interfejsy API jądra, które automatycznie inicjują od zera alokacje sterty jądra wykonane przez funkcję Hyper-V

Te środki zaradcze zostały zaprojektowane w celu zapewnienia, że wykorzystanie luki w zabezpieczeniach między MASZYNami wirtualnymi nie jest możliwe.

## <a name="strong-security-assurance-processes"></a>Silne procesy zapewniania bezpieczeństwa

Obszar ataków związanych z funkcją hypervisor obejmuje oprogramowanie sieciowe, urządzenia wirtualne i wszystkie powierzchnie między MASZYNami wirtualnymi. Obszar narażony na ataki jest śledzony przez zautomatyzowaną integrację kompilacji, która wyzwala okresowe przeglądy zabezpieczeń.

Wszystkie powierzchnie ataków na maszynę wirtualną są modelem zagrożeń, poddawanym przeglądowi kodu, rozmytym i testowanym przez nasz czerwony zespół na potrzeby naruszeń granicy zabezpieczeń. Firma Microsoft ma [program Bounty usterki](https://www.microsoft.com/msrc/bounty-hyper-v) , który płaci nagrody dotyczące istotnych luk w zabezpieczeniach w wersjach kwalifikujących się produktów dla Microsoft Hyper-V.

> [!NOTE]
> Dowiedz się więcej o [silnych procesach zapewniania bezpieczeństwa](../../azure-government/azure-secure-isolation-guidance.md#strong-security-assurance-processes) w funkcji Hyper-V.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat tego, co robimy, aby zwiększyć integralność i bezpieczeństwo platformy, zobacz:

- [Zabezpieczenia oprogramowania układowego](firmware.md)
- [Bezpieczny rozruch](secure-boot.md)
- [Mierzony zaświadczanie rozruchu i hosta](measured-boot-host-attestation.md)
- [Projekt Cerberus](project-cerberus.md)
- [Szyfrowanie danych magazynowanych](encryption-atrest.md)