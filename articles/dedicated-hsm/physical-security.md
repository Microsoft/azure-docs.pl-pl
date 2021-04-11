---
title: Zabezpieczenia fizyczne modułu HSM — dedykowany moduł HSM platformy Azure | Microsoft Docs
description: Informacje o zabezpieczeniach fizycznych urządzeń z dedykowanym modułem HSM platformy Azure w centrach danych
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 8437d12075a148a3e8062f62e195bc019d89a85b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606967"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Dedykowane zabezpieczenia fizyczne modułu HSM platformy Azure

Dedykowany moduł HSM platformy Azure pomaga spełnić zaawansowane wymagania dotyczące zabezpieczeń dla magazynu kluczy. Jest on zarządzany zgodnie ze ścisłymi praktykami w zakresie zabezpieczeń w całym cyklu życia, aby zaspokoić potrzeby klientów.

## <a name="security-through-procurement"></a>Zabezpieczenia za poorednictwem zakupów

Firma Microsoft postępuje zgodnie z bezpiecznym procesem zaopatrzenia. Zarządzamy łańcuchem opieki i upewnij się, że określone urządzenie zostało uporządkowane i dostarczone, gdy urządzenie dociera do naszych centrów danych. Urządzenia znajdują się w serializowanych torbach i kontenerach z niezabezpieczonymi zdarzeniami. Są one przechowywane w bezpiecznym obszarze magazynu, dopóki nie zostaną przełączone do galerii danych centrum danych.  Stojaki zawierające urządzenia HSM są uważane za duże znaczenie biznesowe (HBI). Urządzenia są zablokowane i pod nadzorem wideo przez cały czas i z tyłu.

## <a name="security-through-deployment"></a>Zabezpieczenia przy użyciu wdrożenia

Sprzętowych modułów zabezpieczeń są instalowane w stojakach wraz ze skojarzonymi składnikami sieciowymi. Po zainstalowaniu należy je skonfigurować przed udostępnieniem ich jako części dedykowanej usługi HSM platformy Azure. To działanie konfiguracyjne jest wykonywane przez pracowników firmy Microsoft, którzy zostali poddaną kontroli w tle. Administracja "just in Time" (JIT) służy do ograniczania dostępu tylko do odpowiednich pracowników i tylko wtedy, gdy wymagany jest dostęp. Używane procedury i systemy zapewniają również, że wszystkie działania związane z urządzeniami HSM są rejestrowane.

## <a name="security-in-operations"></a>Zabezpieczenia w operacjach

Sprzętowych modułów zabezpieczeń są urządzeniami sprzętowymi (rzeczywistym modułem HSM w urządzeniu), dzięki czemu możliwe jest, że mogą wystąpić problemy na poziomie składnika. Potencjalne problemy obejmują między innymi awarie wentylatorów i zasilaczy. Ten typ zdarzenia wymaga konserwacji lub przerwania/naprawy, aby zastąpić wszelkie wymieniane składniki.

### <a name="component-replacement"></a>Zastąpienie składnika

Po zainicjowaniu obsługi urządzenia i w obszarze zarządzania klientami można wymienić tylko te składniki. Ten składnik znajduje się poza granicą zabezpieczeń i nie powoduje naruszenia zdarzenia. System obsługi biletów służy do autoryzowania inżyniera firmy Microsoft w celu uzyskania dostępu do tyłu stojaka HBI. Po przetworzeniu biletu zostanie wystawiony tymczasowy klucz fizyczny. Ten klucz zapewnia inżynierowi dostęp do urządzenia i umożliwia im wymianę składnika, którego to dotyczy. Każdy inny dostęp (to znaczy spowodowanie naruszenia zdarzenia) zostałby wykonany, gdy urządzenie nie zostanie przydzielono klientowi, co minimalizuje ryzyko związane z bezpieczeństwem i dostępnością.  

### <a name="device-replacement"></a>Zastępowanie urządzenia

W przypadku łącznego błędu urządzenia następuje proces podobny do tego, który jest używany podczas awarii składnika. Jeśli klient nie może zeroize urządzenia lub urządzenie jest w nieznanym stanie, urządzenia z danymi zostaną usunięte i umieszczone w pojemniku niszczenia w stojaku. Urządzenia umieszczone w pojemniku zostaną zniszczone w sposób kontrolowany i bezpieczny. Żadne urządzenia z danymi z stojaka HBI nie opuszczą centrum danych firmy Microsoft.

### <a name="other-rack-access-activities"></a>Inne działania dotyczące dostępu do stojaka

Jeśli inżynier firmy Microsoft musi uzyskać dostęp do stojaka używanego przez urządzenia HSM (na przykład konserwacja urządzenia sieciowego), w celu uzyskania dostępu do HBI bezpiecznego stojaka zostaną użyte standardowe procedury zabezpieczeń. Cały dostęp będzie objęty nadzorem wideo. Urządzenia HSM są weryfikowane na [poziomie standardu FIPS 140-2](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) , dzięki czemu każdy nieautoryzowany dostęp do urządzeń HSM zostanie zasygnalizowane klientowi, a dane zostaną wyzerowane.

## <a name="logical-level-security-considerations"></a>Zagadnienia dotyczące zabezpieczeń na poziomie logicznym

Sprzętowych modułów zabezpieczeń są obsługiwane w sieci wirtualnej utworzonej przez klienta w ramach prywatnego obszaru adresów IP klienta.  Ta konfiguracja zapewnia cenną izolację poziomu sieci logicznej i zapewnia dostęp tylko dla klienta. Wynika to z tego, że wszystkie kontrole zabezpieczeń na poziomie logicznym są odpowiedzialne za klienta.

## <a name="next-steps"></a>Następne kroki

Zaleca się, aby wszystkie kluczowe pojęcia dotyczące usługi, takie jak wysoka dostępność i bezpieczeństwo i możliwości obsługi, były dobrze zrozumiałe przed zainicjowaniem obsługi urządzeń, projektem lub wdrożeniem aplikacji.

* [Wysoka dostępność](high-availability.md)
* [Sieć](networking.md)
* [Możliwości obsługi](supportability.md)
* [Monitorowanie](monitoring.md)
* [Architektura wdrożenia](deployment-architecture.md)
