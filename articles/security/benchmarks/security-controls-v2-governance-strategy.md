---
title: Test porównawczy zabezpieczeń platformy Azure v2 — zarządzanie i strategia
description: Azure Security test — zarządzanie i strategia
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e8a5196bf71712caae1218933ed13345f4cecd99
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059275"
---
# <a name="security-control-governance-and-strategy"></a>Kontrola zabezpieczeń: zarządzanie i strategia

Tworzenie kopii zapasowych i odzyskiwanie obejmuje kontrolki, aby zapewnić, że kopie zapasowe danych i konfiguracji w różnych warstwach usług są wykonywane, weryfikowane i chronione.

## <a name="gs-1-define-asset-management-and-protection-strategy"></a>GS-1: Zdefiniuj zarządzanie zasobami i strategię ochrony

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| GS-1 | 2, 13 | SC, AC |

Zadbaj o to, aby udokumentować i komunikować się jasno strategię monitorowania i ochrony systemów i danych. Ustalanie priorytetów odnajdywania, oceny, ochrony i monitorowania o krytycznym znaczeniu dla firmy. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Standard klasyfikacji danych zgodny z ryzykiem biznesowym

-   Organizacja zabezpieczeń wgląd w czynniki ryzyka i spis zasobów 

-   Zatwierdzenie przez organizację zabezpieczeń usług platformy Azure do użycia 

-   Bezpieczeństwo zasobów w ramach cyklu życia

-   Wymagana strategia kontroli dostępu zgodnie z klasyfikacją danych organizacji

-   Korzystanie z funkcji ochrony danych natywnych i firmowych platformy Azure

-   Wymagania dotyczące szyfrowania danych dla przypadków użycia w trakcie tranzytu i w czasie spoczynku

-   Odpowiednie standardy kryptograficzne

Uwaga: podejście do zarządzania zasobami i ochrony dla chmury i lokalnego może się różnić w zależności od wielu czynników, takich jak Usługa aplikacji/model hostingu, zagrożenia biznesowe i wymagania dotyczące zgodności. 

- [Zalecenie dotyczące architektury zabezpieczeń platformy Azure — magazyn, dane i szyfrowanie](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Podstawy zabezpieczeń platformy Azure — zabezpieczenia, szyfrowanie i magazyn danych platformy Azure](../fundamentals/encryption-overview.md)

- [Środowisko wdrażania chmury — najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych platformy Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Test wydajności Azure Security — zarządzanie zasobami](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Test porównawczy zabezpieczeń platformy Azure — Ochrona danych](/azure/security/benchmarks/security-controls-v2-data-protection)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-security-posture-management-strategy"></a>GS-2: Definiowanie strategii zarządzania Stanami zabezpieczeń

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| GS-2 | 20, 3, 5 | RA, CM, SC |

Nieustannie mierz i ograniczaj ryzyko dla poszczególnych zasobów i środowiska, w którym są hostowane. Ustalanie priorytetów zasobów o wysokiej wartości i wysoce narażonych obszarów ataków, takich jak opublikowane aplikacje, punkty danych przychodzących i wychodzących sieci, punkty końcowe użytkowników i administratorów itp.

- [Azure Security test — stan i zarządzanie lukami w zabezpieczeniach](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-align-organization-roles-responsibilities-and-accountabilities"></a>GS-3: Wyrównaj role organizacji, obowiązki i accountabilities

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| GS-3 | Nie dotyczy | PL, PM |

Zadbaj o to, aby udokumentować i komunikować się z przejrzystą strategią dotyczącą ról i obowiązków w organizacji zabezpieczeń. Ustalanie priorytetów w celu zapewnienia jasnej odpowiedzialności za decyzje dotyczące zabezpieczeń, edukację na potrzeby wspólnego modelu odpowiedzialności oraz edukację techniczną dla bezpieczeństwa chmury. 

- [Najlepsze rozwiązanie w zakresie zabezpieczeń Azure 1 — ludzie: Edukacja zespołów w podróży w zakresie zabezpieczeń w chmurze](https://aka.ms/AzSec1)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 2 — ludzie: Edukacja zespołów w technologii zabezpieczeń chmury](https://aka.ms/AzSec2)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 3 — proces: Przypisywanie odpowiedzialności za decyzje dotyczące zabezpieczeń chmury](https://aka.ms/AzSec3)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-define-network-security-strategy"></a>GS-4: Definiowanie strategii zabezpieczeń sieci

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| GS-4 | 9 | URZĄD CERTYFIKACJI, SC |

Ustanów podejście zabezpieczeń sieci platformy Azure jako część ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowane zarządzanie siecią i odpowiedzialność w zakresie zabezpieczeń

-   Model segmentacji sieci wirtualnej wyrównany do strategii segmentacji przedsiębiorstwa

-   Strategia korygowania w różnych scenariuszach zagrożeń i ataków

-   Strategia internetowa i dotycząca ruchu przychodzącego i wychodzącego

-   Strategia międzyłączności w chmurze hybrydowej i lokalnej

-   Aktualne artefakty zabezpieczeń sieci (np. diagramy sieciowe, Architektura sieci odniesienia)

Uwaga: podejście zabezpieczeń sieci dla chmury i lokalnego może się różnić w zależności od wielu czynników, takich jak model usługi aplikacji, ekspozycja na zagrożenia i konfiguracja sieci hybrydowej.

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](https://aka.ms/AzSec11)

- [Test porównawczy zabezpieczeń platformy Azure — Zabezpieczenia sieci](/azure/security/benchmarks/security-controls-v2-network-security)

- [Omówienie zabezpieczeń sieci platformy Azure](../fundamentals/network-overview.md)

- [Strategia architektury sieci przedsiębiorstwa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-identity-and-privileged-access-strategy"></a>GS-5: Definiowanie strategii dostępu Identity i Privileged Access

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| GS-5 | 16, 4 | AC, AU, SC |

Ustanów rolę platformy Azure i dostęp uprzywilejowany jako część ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Scentralizowany system tożsamości i uwierzytelniania oraz połączenie z innymi wewnętrznymi i zewnętrznymi systemami tożsamości

-   Metody silnego uwierzytelniania w różnych przypadkach użycia i warunkach

-   Ochrona użytkowników z wysokim poziomem uprawnień

-   Monitorowanie i obsługa nietypowych działań użytkowników  

-   Przegląd tożsamości i dostępu użytkownika oraz proces uzgadniania

Uwaga: podejście do Twojej tożsamości i uprzywilejowanego dostępu do chmury i lokalnego może się różnić w zależności od wielu czynników, takich jak ścieżka dostępu do danych/aplikacji, model usługi i strategia dostępu klientów/partnerów.

- [Test porównawczy zabezpieczeń Azure — Zarządzanie tożsamościami](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Test porównawczy zabezpieczeń platformy Azure — uprzywilejowany dostęp](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](https://aka.ms/AzSec11)

- [Omówienie zabezpieczeń usługi Azure Identity Management](../fundamentals/identity-management-overview.md) 

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-logging-and-threat-response-strategy"></a>GS-6: Definiowanie strategii rejestrowania i reagowania na zagrożenia

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| GS-6 | 19 | IR, AU, RA, SC |

Ustanów strategię rejestrowania i reagowania na zagrożenia, aby szybko wykrywać i rozwiązywać zagrożenia oraz spełnić wymagania dotyczące zgodności. Ustalaj priorytety, dostarczając analityków z alertami o wysokiej jakości i bezproblemowe środowisko, aby umożliwić im skoncentrowanie się na zagrożeniach, a nie na poziomie integracji i ręcznej. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Rola i obowiązki organizacji operacji zabezpieczeń (SecOP) 

-   Dobrze zdefiniowany proces reagowania na zdarzenia z Instytutem NIST lub inną platformą branżową 

-   Przechwytywanie i przechowywanie dzienników w celu zapewnienia obsługi wykrywania zagrożeń, reagowania na zdarzenia i zgodności

-   Scentralizowana widoczność i korelacja informacji dotyczących zagrożeń przy użyciu SIEM, natywnych możliwości platformy Azure i innych źródeł 

-   Plan komunikacji i powiadomień z klientami, dostawcami i zainteresowanymi stronami

-   Używanie platform macierzystych i innych firm platformy Azure do obsługi zdarzeń, takich jak rejestrowanie i wykrywanie zagrożeń, dowodowych oraz korygowanie i eliminowanie ataków

-   Procesy obsługi zdarzeń i działań po zdarzeniu, takie jak zdobyte doświadczenia i przechowywanie dowodów

Uwaga: sposób rejestrowania i wykrywania zagrożeń w chmurze i lokalnie może się różnić w zależności od wielu czynników, takich jak wymagania dotyczące zgodności, poziom zagrożenia oraz możliwość wykrywania i korygowania. 

- [Test porównawczy zabezpieczeń platformy Azure — rejestrowanie i wykrywanie zagrożeń](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Test porównawczy zabezpieczeń platformy Azure — odpowiedź na zdarzenie](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Azure Security Best Practice 4 — proces. Aktualizowanie procesów odpowiedzi na zdarzenia dla chmury](https://aka.ms/AzSec11)

- [Przewodnik wdrażania platformy Azure, rejestrowania i podejmowania decyzji dotyczących raportowania](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Skalowanie, zarządzanie i monitorowanie w przedsiębiorstwie platformy Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-backup-and-recovery-strategy"></a>GS-7: Definiowanie strategii tworzenia kopii zapasowych i odzyskiwania

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| GS-7 | 10 | CP |

Utwórz strategię tworzenia kopii zapasowych i odzyskiwania na platformie Azure dla swojej organizacji. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

-   Cel czasu odzyskiwania (RTO) i definicje celu punktu odzyskiwania (RPO) zgodnie z celami odporności firmy

-   Projektowanie nadmiarowości w aplikacjach i ustawieniach infrastruktury

-   Ochrona kopii zapasowych przy użyciu funkcji kontroli dostępu i szyfrowania danych

Uwaga: podejście do tworzenia kopii zapasowych i odzyskiwania w chmurze i lokalnie może się różnić w zależności od wielu czynników, takich jak nadmiarowość infrastruktury, Usługa aplikacji/model hostingu i wymagania dotyczące zgodności.

- [Azure Security test — tworzenie kopii zapasowych i odzyskiwanie](/azure/security/benchmarks/security-controls-v2-backup-recovery)

- [Środowisko platformy Azure — tworzenie kopii zapasowych i odzyskiwanie po awarii dla aplikacji platformy Azure](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Wdrażanie platformy Azure — ciągłość działania i odzyskiwanie po awarii](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów**:

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

