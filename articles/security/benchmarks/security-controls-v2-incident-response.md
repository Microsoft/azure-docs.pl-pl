---
title: Azure Security test — wersja 2 — odpowiedź na zdarzenie
description: Odpowiedź na zdarzenie usługi Azure Security test w wersji 2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 1ba7391b3b524f7532101083a444aab6059e8a10
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408759"
---
# <a name="security-control-v2-incident-response"></a>Kontrola zabezpieczeń v2: odpowiedź na zdarzenie

Reagowanie na zdarzenia obejmuje kontrolę w cyklu życia odpowiedzi na zdarzenia — przygotowanie, wykrywanie i analiza, zawieranie i działania po zdarzeniu. Obejmuje to korzystanie z usług platformy Azure, takich jak Azure Security Center i wskaźnik kontrolny, w celu zautomatyzowania procesu reagowania na zdarzenia.

## <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: przygotowanie — aktualizacja procesu reagowania na incydenty dla platformy Azure

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| IR — 1 | 19 | IR-4, IR-8 |

Upewnij się, że organizacja ma procesy reagowania na zdarzenia związane z bezpieczeństwem, Zaktualizowano te procesy na platformie Azure i regularnie wykonuje je w celu zapewnienia gotowości.

- [Implementowanie zabezpieczeń w środowisku przedsiębiorstwa](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [Przewodnik odwołuje się do odpowiedzi na zdarzenia](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Odpowiedzialność** : klient

**Uczestnicy zabezpieczeń klientów** ( [Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operacje zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Przygotowywanie zdarzeń](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Analiza zagrożeń](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: przygotowanie — powiadomienie o zdarzeniu instalacji

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| IR — 2 | 19,5 | IR-4, IR-5, IR-6, IR-8 |

Skonfiguruj informacje kontaktowe dotyczące zdarzenia zabezpieczeń w Azure Security Center. Informacje kontaktowe są używane przez firmę Microsoft do skontaktowania się z Tobą, jeśli centrum Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych jest uzyskiwany przez nielegalną lub nieautoryzowaną osobę. Dostępne są również opcje dostosowywania alertów dotyczących zdarzeń i powiadomień w różnych usługach platformy Azure w zależności od potrzeb związanych z odpowiedzią na zdarzenia. 

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../../security-center/security-center-provide-security-contact-details.md)

**Odpowiedzialność** : klient

**Uczestnicy zabezpieczeń klientów** ( [Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operacje zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Przygotowywanie zdarzeń](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: wykrywanie i analiza — Tworzenie zdarzeń na podstawie alertów o wysokiej jakości

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| IR-3 | 19,6 | IR-4, IR-5 |

Upewnij się, że masz proces tworzenia alertów o wysokiej jakości i mierzenia jakości alertów. Dzięki temu można uczyć się lekcji od przeszłych zdarzeń i określić priorytety alertów dla analityków, dzięki czemu nie będą one tracić czasu na fałszywie dodatnich. 

Alerty o wysokiej jakości mogą być tworzone na podstawie doświadczeń z przeszłych zdarzeń, sprawdzonych źródeł społeczności i narzędzi przeznaczonych do generowania i czyszczenia alertów przez odmowę i skorelowanie różnorodnych źródeł sygnałów. 

Azure Security Center oferuje alerty o wysokiej jakości dla wielu zasobów platformy Azure. Łącznika danych ASC można użyć do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej. Wskaźnik oceny platformy Azure umożliwia tworzenie zaawansowanych reguł alertów w celu automatycznego generowania zdarzeń na potrzeby badania. 

Eksportuj alerty i zalecenia dotyczące Azure Security Center przy użyciu funkcji eksportowania, aby pomóc identyfikować zagrożenia dla zasobów platformy Azure. Eksportuj alerty i zalecenia ręcznie lub w stały sposób ciągły.

- [Jak skonfigurować eksportowanie](../../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure wskaźnikowego](../../sentinel/connect-azure-security-center.md)

**Odpowiedzialność** : klient

**Uczestnicy zabezpieczeń klientów** ( [Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operacje zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Przygotowywanie zdarzeń](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Analiza zagrożeń](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: wykrywanie i analiza — Zbadaj zdarzenie

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| IR — 4 | 19 | IR — 4 |

Upewnij się, że analitycy mogą wykonywać zapytania i używać różnorodnych źródeł danych podczas badania potencjalnych zdarzeń, aby zbudować pełny wgląd w to, co się stało. Należy zebrać różne dzienniki, aby śledzić działania potencjalnej osoby atakującej w łańcuchu kasowania, aby uniknąć niewidomych plamek.  Należy również upewnić się, że szczegółowe informacje i uczenia są przechwytywane dla innych analityków i w przyszłości.  

Źródła danych do zbadania obejmują scentralizowane źródła rejestrowania, które są już zbierane z usług w zakresie i działających systemów, ale mogą również obejmować:

- Dane sieci — używanie dzienników przepływów sieciowych grup zabezpieczeń, platformy Azure Network Watcher i Azure Monitor do przechwytywania dzienników przepływu sieci i innych informacji analitycznych. 

- Migawki uruchomionych systemów: 

    - Użyj funkcji migawek maszyny wirtualnej platformy Azure, aby utworzyć migawkę dysku działającego systemu. 

    - Użyj możliwości natywnego zrzutu pamięci systemu operacyjnego, aby utworzyć migawkę pamięci uruchomionego systemu.

    - Użyj funkcji Snapshot w ramach usług platformy Azure lub własnej funkcji oprogramowania, aby utworzyć migawki uruchomionych systemów.

Platforma Azure — wskaźnik przedstawia szeroką analizę danych w praktycznie dowolnym źródle dziennika i portalu zarządzania przypadkami do zarządzania pełnym cyklem życia zdarzeń. Informacje o analizie podczas badania mogą być powiązane ze zdarzeniami w celu śledzenia i raportowania. 

- [Tworzenie migawek dysku maszyny z systemem Windows](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Tworzenie migawek dysku maszyny z systemem Linux](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure obsługi informacji diagnostycznych i kolekcji zrzutów pamięci](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Zbadaj zdarzenia za pomocą platformy Azure — wskaźnik](../../sentinel/tutorial-investigate-cases.md)

**Odpowiedzialność** : klient

**Uczestnicy zabezpieczeń klientów** ( [Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operacje zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Przygotowywanie zdarzeń](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Analiza zagrożeń](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: wykrywanie i analiza — określanie priorytetów zdarzeń

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| IR-5 | 19,8 | CA-2, IR-4 |

Zapewnienie kontekstu analitykom, na których zdarzenia mają być skoncentrowane na początku, na podstawie ważności alertów i poufności zasobów. 

Azure Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które spowodowało wygenerowanie alertu.

Ponadto należy oznaczyć zasoby przy użyciu tagów i utworzyć system nazewnictwa, aby identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne.  Odpowiedzialność za korygowanie alertów zależy od zagrożenia dla zasobów platformy Azure i środowiska, w którym wystąpiło zdarzenie.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](../../azure-resource-manager/management/tag-resources.md)

**Odpowiedzialność** : klient

**Uczestnicy zabezpieczeń klientów** ( [Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operacje zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Przygotowywanie zdarzeń](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Analiza zagrożeń](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: zawieranie, likwidowanie i odzyskiwanie — Automatyzacja obsługi zdarzeń

| Identyfikator platformy Azure | IDENTYFIKATORY formantów usługi CIS: v 7.1 | NIST SP800-53 identyfikatory () |
|--|--|--|--|
| IR-6 | 19 | IR-4, IR-5, IR-6 |

Automatyzuj ręczne powtarzające się zadania w celu przyspieszenia czasu reakcji i zmniejszenia obciążenia dla analityków. Wykonanie ręcznych zadań trwa dłużej, spowalnia każde zdarzenie i zmniejsza liczbę incydentów, które może obsłużyć analityk. Zadania ręczne zwiększają również zmęczenie analityków, które zwiększają ryzyko wystąpienia błędu ludzkiego powodującego opóźnienia i obniżają możliwości skoncentrowania się na skomplikowanych zadaniach analityków. Korzystając z funkcji automatyzacji przepływu pracy w Azure Security Center i wskaźnik platformy Azure, można automatycznie wyzwalać akcje lub uruchamiać element PlayBook w celu reagowania na przychodzące alerty zabezpieczeń. Element PlayBook wykonuje akcje, takie jak wysyłanie powiadomień, wyłączanie kont i izolowanie problematycznych sieci. 

- [Konfigurowanie automatyzacji przepływu pracy w Security Center](../../security-center/workflow-automation.md)

- [Konfigurowanie zautomatyzowanych odpowiedzi na zagrożenia w Azure Security Center](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurowanie zautomatyzowanych odpowiedzi na zagrożenia na platformie Azure — wskaźnik](../../sentinel/tutorial-respond-threats-playbook.md)

**Odpowiedzialność** : klient

**Uczestnicy zabezpieczeń klientów** ( [Dowiedz się więcej](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Operacje zabezpieczeń](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Przygotowywanie zdarzeń](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Analiza zagrożeń](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)