---
title: Test porównawczy zabezpieczeń platformy Azure v2 — zarządzanie i strategia
description: Azure Security test — zarządzanie i strategia
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 420ab87591e230592ec8f728c6acb155266b5912
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91403530"
---
# <a name="security-control-v2-governance-and-strategy"></a>Kontrola zabezpieczeń v2: ładu i strategia

Kierownictwo i strategia zapewniają wskazówki dotyczące zapewnienia spójnej strategii zabezpieczeń i udokumentowanego podejścia do nadzoru w zakresie zabezpieczeń i zapewnienia bezpieczeństwa, w tym ustanawiania ról i obowiązków związanych z różnymi funkcjami zabezpieczeń w chmurze, ujednoliconą strategią techniczną oraz obsługą zasad i standardów.

## <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definiowanie strategii zarządzania zasobami i ochrony danych

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| GS-1 | 2, 13 | SC, AC |

Zadbaj o to, aby udokumentować i komunikować się jasno strategię monitorowania i ochrony systemów i danych. Ustalanie priorytetów odnajdywania, oceny, ochrony i monitorowania o krytycznym znaczeniu dla firmy. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

- Standard klasyfikacji danych zgodny z ryzykiem biznesowym

- Organizacja zabezpieczeń wgląd w czynniki ryzyka i spis zasobów 

- Zatwierdzenie przez organizację zabezpieczeń usług platformy Azure do użycia 

- Bezpieczeństwo zasobów w ramach cyklu życia

- Wymagana strategia kontroli dostępu zgodnie z klasyfikacją danych organizacji

- Korzystanie z funkcji ochrony danych natywnych i firmowych platformy Azure

- Wymagania dotyczące szyfrowania danych dla przypadków użycia w trakcie tranzytu i w czasie spoczynku

- Odpowiednie standardy kryptograficzne

Aby uzyskać więcej informacji, zobacz następujące odwołania:
- [Zalecenie dotyczące architektury zabezpieczeń platformy Azure — magazyn, dane i szyfrowanie](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Podstawy zabezpieczeń platformy Azure — zabezpieczenia, szyfrowanie i magazyn danych platformy Azure](../fundamentals/encryption-overview.md)

- [Środowisko wdrażania chmury — najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych platformy Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Test wydajności Azure Security — zarządzanie zasobami](security-controls-v2-asset-management.md)

- [Test porównawczy zabezpieczeń platformy Azure — Ochrona danych](security-controls-v2-data-protection.md)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definiowanie strategii segmentacji przedsiębiorstwa

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| GS-2 | 4, 9, 16 | AC, CA, SC |

Ustanów strategię całego przedsiębiorstwa do segmentacji dostępu do zasobów przy użyciu kombinacji tożsamości, sieci, aplikacji, subskrypcji, grupy zarządzania i innych kontrolek.

Należy starannie zrównoważyć potrzebę rozdzielania zabezpieczeń, aby umożliwić codzienne działanie systemów, które muszą komunikować się ze sobą i uzyskiwać dostęp do danych.

Upewnij się, że strategia segmentacji jest zaimplementowana spójnie między typami formantów, w tym zabezpieczeniami sieci, modelami tożsamości i dostępu, a także z kontrolkami dostępu i dostępem do aplikacji.

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (wideo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Wyrównaj segmentację sieci przy użyciu strategii segmentacji przedsiębiorstwa](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definiowanie strategii zarządzania stan zabezpieczeń

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| GS-3 | 20, 3, 5 | RA, CM, SC |

Nieustannie mierz i ograniczaj ryzyko dla poszczególnych zasobów i środowiska, w którym są hostowane. Ustalanie priorytetów zasobów o wysokiej wartości i wysoce narażonych obszarów ataków, takich jak opublikowane aplikacje, punkty danych przychodzących i wychodzących sieci, punkty końcowe użytkowników i administratorów itp.

- [Azure Security test — stan i zarządzanie lukami w zabezpieczeniach](security-controls-v2-posture-vulnerability-management.md)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Wyrównaj role organizacji, obowiązki i accountabilities

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| GS-4 | Nie dotyczy | PL, PM |

Zadbaj o to, aby udokumentować i komunikować się z przejrzystą strategią dotyczącą ról i obowiązków w organizacji zabezpieczeń. Ustalaj priorytety w celu zapewnienia jasnej odpowiedzialności za decyzje dotyczące zabezpieczeń, przenosząc wszystkie osoby na współużytkowany model odpowiedzialności i przeszkolej zespoły techniczne dotyczące technologii, aby zabezpieczyć chmurę.

- [Najlepsze rozwiązanie w zakresie zabezpieczeń Azure 1 — ludzie: Edukacja zespołów w podróży w zakresie zabezpieczeń w chmurze](https://aka.ms/AzSec1)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 2 — ludzie: Edukacja zespołów w technologii zabezpieczeń chmury](https://aka.ms/AzSec2)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 3 — proces: Przypisywanie odpowiedzialności za decyzje dotyczące zabezpieczeń chmury](https://aka.ms/AzSec3)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-network-security-strategy"></a>GS-5: Definiowanie strategii zabezpieczeń sieci

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| GS-5 | 9 | URZĄD CERTYFIKACJI, SC |

Ustanów podejście zabezpieczeń sieci platformy Azure jako część ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

- Scentralizowane zarządzanie siecią i odpowiedzialność w zakresie zabezpieczeń

- Model segmentacji sieci wirtualnej wyrównany do strategii segmentacji przedsiębiorstwa

- Strategia korygowania w różnych scenariuszach zagrożeń i ataków

- Strategia internetowa i dotycząca ruchu przychodzącego i wychodzącego

- Strategia międzyłączności w chmurze hybrydowej i lokalnej

- Aktualne artefakty zabezpieczeń sieci (np. diagramy sieciowe, Architektura sieci odniesienia)

Aby uzyskać więcej informacji, zobacz następujące odwołania:

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](https://aka.ms/AzSec11)

- [Test porównawczy zabezpieczeń platformy Azure — Zabezpieczenia sieci](security-controls-v2-network-security.md)

- [Omówienie zabezpieczeń sieci platformy Azure](../fundamentals/network-overview.md)

- [Strategia architektury sieci przedsiębiorstwa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definiowanie strategii dostępu Identity i Privileged Access

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| GS-6 | 16, 4 | AC, AU, SC |

Ustanów rolę platformy Azure i dostęp uprzywilejowany jako część ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.  

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

- Scentralizowany system tożsamości i uwierzytelniania oraz połączenie z innymi wewnętrznymi i zewnętrznymi systemami tożsamości

- Metody silnego uwierzytelniania w różnych przypadkach użycia i warunkach

- Ochrona użytkowników z wysokim poziomem uprawnień

- Monitorowanie i obsługa nietypowych działań użytkowników  

- Przegląd tożsamości i dostępu użytkownika oraz proces uzgadniania

Aby uzyskać więcej informacji, zobacz następujące odwołania:

- [Test porównawczy zabezpieczeń Azure — Zarządzanie tożsamościami](security-controls-v2-identity-management.md)

- [Test porównawczy zabezpieczeń platformy Azure — uprzywilejowany dostęp](security-controls-v2-privileged-access.md)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](https://aka.ms/AzSec11)

- [Omówienie zabezpieczeń usługi Azure Identity Management](../fundamentals/identity-management-overview.md)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definiowanie strategii rejestrowania i reagowania na zagrożenia

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| GS-7 | 19 | IR, AU, RA, SC |

Ustanów strategię rejestrowania i reagowania na zagrożenia, aby szybko wykrywać i rozwiązywać zagrożenia oraz spełnić wymagania dotyczące zgodności. Ustalaj priorytety, dostarczając analityków z alertami o wysokiej jakości i bezproblemowe środowisko, aby umożliwić im skoncentrowanie się na zagrożeniach, a nie na poziomie integracji i ręcznej. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

- Rola i obowiązki organizacji operacji zabezpieczeń (SecOP) 

- Dobrze zdefiniowany proces reagowania na zdarzenia z Instytutem NIST lub inną platformą branżową 

- Przechwytywanie i przechowywanie dzienników w celu zapewnienia obsługi wykrywania zagrożeń, reagowania na zdarzenia i zgodności

- Scentralizowana widoczność i korelacja informacji dotyczących zagrożeń przy użyciu SIEM, natywnych możliwości platformy Azure i innych źródeł 

- Plan komunikacji i powiadomień z klientami, dostawcami i zainteresowanymi stronami

- Używanie platform macierzystych i innych firm platformy Azure do obsługi zdarzeń, takich jak rejestrowanie i wykrywanie zagrożeń, dowodowych oraz korygowanie i eliminowanie ataków

- Procesy obsługi zdarzeń i działań po zdarzeniu, takie jak zdobyte doświadczenia i przechowywanie dowodów

Aby uzyskać więcej informacji, zobacz następujące odwołania:
- [Test porównawczy zabezpieczeń platformy Azure — rejestrowanie i wykrywanie zagrożeń](security-controls-v2-logging-threat-detection.md)

- [Test porównawczy zabezpieczeń platformy Azure — odpowiedź na zdarzenie](security-controls-v2-incident-response.md)

- [Azure Security Best Practice 4 — proces. Aktualizowanie procesów odpowiedzi na zdarzenia dla chmury](https://aka.ms/AzSec4)

- [Przewodnik wdrażania platformy Azure, rejestrowania i podejmowania decyzji dotyczących raportowania](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Skalowanie, zarządzanie i monitorowanie w przedsiębiorstwie platformy Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-8-define-backup-and-recovery-strategy"></a>GS-8: Definiowanie strategii tworzenia kopii zapasowych i odzyskiwania

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| GS-8 | 10 | CP |

Utwórz strategię tworzenia kopii zapasowych i odzyskiwania na platformie Azure dla swojej organizacji. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

- Cel czasu odzyskiwania (RTO) i definicje celu punktu odzyskiwania (RPO) zgodnie z celami odporności firmy

- Projektowanie nadmiarowości w aplikacjach i ustawieniach infrastruktury

- Ochrona kopii zapasowych przy użyciu funkcji kontroli dostępu i szyfrowania danych

Aby uzyskać więcej informacji, zobacz następujące odwołania:
- [Azure Security test — tworzenie kopii zapasowych i odzyskiwanie](security-controls-v2-backup-recovery.md)

- [Azure Well-Architecture Framework — tworzenie kopii zapasowych i odzyskiwanie po awarii dla aplikacji platformy Azure](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Wdrażanie platformy Azure — ciągłość działania i odzyskiwanie po awarii](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**Odpowiedzialność**: klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

