---
title: Test porównawczy zabezpieczeń platformy Azure v2 — zarządzanie i strategia
description: Azure Security test — zarządzanie i strategia
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 277033e41ec7e02b89eca8cf74fe6854acb51cc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727026"
---
# <a name="security-control-v2-governance-and-strategy"></a>Kontrola zabezpieczeń v2: ładu i strategia

Kierownictwo i strategia zapewniają wskazówki dotyczące zapewnienia spójnej strategii zabezpieczeń i udokumentowanego podejścia do nadzoru w zakresie zabezpieczeń i zapewnienia bezpieczeństwa, w tym ustanawiania ról i obowiązków związanych z różnymi funkcjami zabezpieczeń w chmurze, ujednoliconą strategią techniczną oraz obsługą zasad i standardów.

## <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definiowanie strategii zarządzania elementami zawartości i ochrony danych

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| GS-1 | 2, 13 | SC, AC |

Zadbaj o to, aby udokumentować i komunikować się jasno strategię monitorowania i ochrony systemów i danych. Ustalaj priorytety odnajdywania, oceny, ochrony i monitorowania danych oraz systemów o krytycznym znaczeniu dla firmy.

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

- Standard klasyfikacji danych zgodny z ryzykiem biznesowym

- Wgląd organizacji zabezpieczeń w czynniki ryzyka i spis elementów zawartości

- Zatwierdzenie przez organizację zabezpieczeń usług platformy Azure do użycia

- Bezpieczeństwo elementów zawartości w całym cyklu życia

- Wymagana strategia kontroli dostępu zgodnie z klasyfikacją danych organizacji

- Korzystanie z natywnych dla platformy Azure oraz oferowanych przez inne firmy funkcji ochrony danych

- Wymagania w zakresie szyfrowania danych dla przypadków użycia dotyczących danych przesyłanych i danych magazynowanych

- Odpowiednie standardy kryptograficzne

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:
- [Zalecenie dotyczące architektury zabezpieczeń platformy Azure — magazyn, dane i szyfrowanie](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Podstawy zabezpieczeń platformy Azure — zabezpieczenia, szyfrowanie i magazynowanie danych platformy Azure](../fundamentals/encryption-overview.md)

- [Cloud Adoption Framework — najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych platformy Azure](../fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Test porównawczy zabezpieczeń platformy Azure — zarządzanie elementami zawartości](security-controls-v2-asset-management.md)

- [Test porównawczy zabezpieczeń platformy Azure — ochrona danych](security-controls-v2-data-protection.md)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definiowanie strategii segmentacji przedsiębiorstwa

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| GS-2 | 4, 9, 16 | AC, CA, SC |

Ustanów strategię całego przedsiębiorstwa do segmentacji dostępu do zasobów przy użyciu kombinacji tożsamości, sieci, aplikacji, subskrypcji, grupy zarządzania i innych kontrolek.

Należy starannie zrównoważyć potrzebę rozdzielania zabezpieczeń, aby umożliwić codzienne działanie systemów, które muszą komunikować się ze sobą i uzyskiwać dostęp do danych.

Upewnij się, że strategia segmentacji jest zaimplementowana spójnie dla różnych typów kontroli, w tym zabezpieczeń sieci, modeli tożsamości i dostępu, a także modeli uprawnień/dostępu i procesów ludzkich.

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (wideo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Wskazówki dotyczące strategii segmentacji na platformie Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Wyrównywanie segmentacji sieci przy użyciu strategii segmentacji przedsiębiorstwa](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definiowanie strategii zarządzania stanem zabezpieczeń

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| GS-3 | 20, 3, 5 | RA, CM, SC |

Nieustannie mierz i ograniczaj ryzyko dla poszczególnych zasobów i środowiska, w którym są hostowane. Ustalaj priorytety elementów zawartości o wysokiej wartości i wysoce narażonych na ataki obszarów, takich jak opublikowane aplikacje, punkty danych przychodzących i wychodzących sieci, punkty końcowe użytkowników i administratorów itp.

- [Test porównawczy zabezpieczeń platformy Azure — stan i zarządzanie lukami w zabezpieczeniach](security-controls-v2-posture-vulnerability-management.md)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Dopasuj role organizacji, obowiązki i odpowiedzialność

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| GS-4 | Nie dotyczy | PL, PM |

Upewnij się, że masz udokumentowaną strategię dotyczącą ról i obowiązków w organizacji zabezpieczeń. Ustalaj priorytety w celu wyraźnego określenia odpowiedzialności za decyzje dotyczące zabezpieczeń, informując wszystkie osoby o współużytkowanym modelu odpowiedzialności i informując zespoły techniczne o technologii do zabezpieczania chmury.

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 1 — ludzie: informowanie zespołów o podróży zabezpieczeń w chmurze](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 2 — ludzie: informowanie zespołów o technologii zabezpieczeń w chmurze](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 3 — proces: przypisywanie odpowiedzialności za decyzje dotyczące zabezpieczeń chmury](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-network-security-strategy"></a>GS-5: Definiowanie strategii zabezpieczeń sieci

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| GS-5 | 9 | URZĄD CERTYFIKACJI, SC |

Ustanów podejście zabezpieczeń sieci platformy Azure jako część ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów:

- Scentralizowane zarządzanie siecią i odpowiedzialność w zakresie zabezpieczeń

- Model segmentacji sieci wirtualnej zgodny ze strategią segmentacji przedsiębiorstwa

- Strategia korygowania w różnych scenariuszach zagrożeń i ataków

- Strategia internetowa i dotycząca brzegowego ruchu przychodzącego i wychodzącego

- Strategia międzyłączności w chmurze hybrydowej i środowisku lokalnym

- Aktualne artefakty zabezpieczeń sieci (takie jak diagramy sieciowe, Architektura sieci odniesienia)

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Test porównawczy zabezpieczeń platformy Azure — zabezpieczenia sieci](security-controls-v2-network-security.md)

- [Omówienie zabezpieczeń sieci platformy Azure](../fundamentals/network-overview.md)

- [Strategia architektury sieci przedsiębiorstwa](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definiowanie strategii dotyczącej tożsamości i dostępu uprzywilejowanego

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| GS-6 | 16, 4 | AC, AU, SC |

Ustanów rolę platformy Azure i dostęp uprzywilejowany jako część ogólnej strategii kontroli dostępu zabezpieczeń w organizacji.

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów:

- Scentralizowany system tożsamości i uwierzytelniania oraz połączenie z innymi wewnętrznymi i zewnętrznymi systemami tożsamości

- Metody silnego uwierzytelniania w różnych przypadkach użycia i warunkach

- Ochrona użytkowników z wysokim poziomem uprawnień

- Monitorowanie i obsługa nietypowych działań użytkowników

- Przegląd tożsamości i dostępu użytkownika oraz proces uzgadniania

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:

- [Test porównawczy zabezpieczeń platformy Azure — zarządzanie tożsamością](security-controls-v2-identity-management.md)

- [Test porównawczy zabezpieczeń platformy Azure — dostęp uprzywilejowany](security-controls-v2-privileged-access.md)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 11 — architektura. Pojedyncza ujednolicona strategia zabezpieczeń](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Omówienie zabezpieczeń zarządzania tożsamościami platformy Azure](../fundamentals/identity-management-overview.md)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definiowanie strategii rejestrowania i reagowania na zagrożenia

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| GS-7 | 19 | IR, AU, RA, SC |

Ustanów strategię rejestrowania i reagowania na zagrożenia, aby szybko wykrywać i rozwiązywać zagrożenia oraz spełnić wymagania dotyczące zgodności. Potraktuj priorytetowo kwestię udostępnienia analitykom alertów o wysokiej jakości i bezproblemowego środowiska, tak aby mogli skupić się na zagrożeniach, a nie na wdrażaniu i ręcznych krokach. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

- Rola i obowiązki organizacji operacji zabezpieczeń (SecOP) 

- Dobrze zdefiniowany proces reagowania na zdarzenia zgodnie z NIST lub inną platformą branżową 

- Przechwytywanie i przechowywanie dzienników w celu zapewnienia obsługi wykrywania zagrożeń, reagowania na zdarzenia i zgodności

- Scentralizowana widoczność i korelacja informacji dotyczących zagrożeń przy użyciu rozwiązania SIEM, natywnych możliwości platformy Azure i innych źródeł 

- Plan komunikacji i powiadomień z klientami, dostawcami i publicznymi zainteresowanymi stronami

- Używanie natywnych i zewnętrznych platform Azure do obsługi zdarzeń, takich jak rejestrowanie i wykrywanie zagrożeń, postępowania dowodowe oraz korygowanie i eliminowanie ataków

- Procesy obsługi zdarzeń i działań po zdarzeniu, takie jak zdobyte doświadczenia i przechowywanie dowodów

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:
- [Test porównawczy zabezpieczeń platformy Azure — rejestrowanie i wykrywanie zagrożeń](security-controls-v2-logging-threat-detection.md)

- [Test porównawczy zabezpieczeń platformy Azure — reagowanie na zdarzenia](security-controls-v2-incident-response.md)

- [Najlepsze rozwiązanie dotyczące zabezpieczeń platformy Azure 4 — proces. Aktualizowanie procesów reagowania na zdarzenia dla chmury](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [Przewodnik dotyczący decyzji w zakresie platformy wdrażania Azure, rejestrowania i raportowania](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Skalowanie, zarządzanie i monitorowanie w przedsiębiorstwie na platformie Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-8-define-backup-and-recovery-strategy"></a>GS-8: Definiowanie strategii tworzenia kopii zapasowych i odzyskiwania

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| GS-8 | 10 | CP |

Utwórz strategię tworzenia kopii zapasowych i odzyskiwania na platformie Azure dla swojej organizacji. 

Ta strategia powinna obejmować udokumentowane wskazówki, zasady i standardy dla następujących elementów: 

- Cel czasu odzyskiwania (RTO) i definicje celu punktu odzyskiwania (RPO) zgodnie z celami odporności firmy

- Projektowanie nadmiarowości w aplikacjach i ustawieniach infrastruktury

- Ochrona kopii zapasowych przy użyciu funkcji kontroli dostępu i szyfrowania danych

Aby uzyskać więcej informacji, zapoznaj się z następującymi dokumentami:
- [Azure Security test — tworzenie kopii zapasowych i odzyskiwanie](security-controls-v2-backup-recovery.md)

- [Azure Well-Architecture Framework — tworzenie kopii zapasowych i odzyskiwanie po awarii dla aplikacji platformy Azure](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Wdrażanie platformy Azure — ciągłość działania i odzyskiwanie po awarii](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**Odpowiedzialność**: Klient

**Uczestnicy zabezpieczeń klientów** ([Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Wszyscy uczestnicy projektu](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)