---
title: Kontrola zabezpieczeń Azure — reagowanie na zdarzenia
description: Odpowiedź na zdarzenia kontroli zabezpieczeń platformy Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fb4c4c5a0cf6610af17aabc562c42d2e0eb4e6a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94409097"
---
# <a name="security-control-incident-response"></a>Kontrola zabezpieczeń: odpowiedź na zdarzenie

Ochrona informacji organizacji, a także jej reputacja, poprzez opracowywanie i wdrażanie infrastruktury odpowiedzi na zdarzenia (np. planów, zdefiniowanych ról, szkoleń, komunikacji, nadzoru nad zarządzaniem) w celu szybkiego wykrywania ataku, a następnie skutecznego zaistnienia szkody, wyeliminowania obecności osoby atakującej i przywrócenia integralności sieci i systemów.

## <a name="101-create-an-incident-response-guide"></a>10,1: Tworzenie przewodnika odpowiedzi na zdarzenia

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 10.1 | 19,1, 19,2, 19,3 | Customer |

Utwórz Przewodnik po odpowiedzi na zdarzenia dla swojej organizacji. Upewnij się, że istnieją zarejestrowane plany reakcji na zdarzenia, które definiują wszystkie role pracowników, a także etapy obsługi zdarzeń/zarządzania od wykrywania do oceny po zdarzeniu.  

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na zdarzenia zabezpieczeń](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia incydentu centrum Microsoft Security Response](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Skorzystaj z przewodnika obsługi zdarzeń związanych z bezpieczeństwem programu NIST, aby pomóc w tworzeniu własnego planu reagowania na zdarzenia](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: Tworzenie oceny incydentu i procedury priorytetyzacji

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 10,2 | 19,8 | Customer |

Security Center przypisuje ważność do każdego alertu, aby pomóc w ustaleniu, które alerty należy najpierw zbadać. Ważność jest oparta na tym, jak dobrze Security Center znajduje się w wyszukiwaniu lub analitycznym używanym do wystawiania alertu, a także poziom pewności, że istniało złośliwy wpływ na działanie, które prowadziło do alertu. 

Dodatkowo jasno Oznacz subskrypcje (na przykład Produkcja, inne niż prod) przy użyciu tagów i Utwórz system nazewnictwa, aby jasno identyfikować i klasyfikować zasoby platformy Azure, szczególnie te, które przetwarzają dane poufne.  Odpowiedzialność za korygowanie alertów w oparciu o krytyczne znaczenie zasobów platformy Azure i środowisko, w którym wystąpiło zdarzenie, leży po stronie użytkownika.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](../../security-center/security-center-alerts-overview.md)

- [Organizowanie zasobów platformy Azure przy użyciu tagów](../../azure-resource-manager/management/tag-resources.md)

## <a name="103-test-security-response-procedures"></a>10,3: procedury odpowiedzi na zabezpieczenia testowe

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 10,3 | 19 | Customer |

Przeprowadzaj ćwiczenia w celu przetestowania możliwości reagowania na zdarzenia systemu w ramach regularnego erze, aby pomóc w ochronie zasobów platformy Azure. Zidentyfikuj słabe punkty i przerwy oraz popraw plan zgodnie z wymaganiami.

- [Przewodnik po publikacji NIST, który umożliwia testowanie, uczenie i wykonywanie programów dla planów i możliwości IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Podaj szczegóły kontaktu dotyczącego zabezpieczeń i Skonfiguruj powiadomienia dotyczące alertów dotyczących zdarzeń związanych z zabezpieczeniami

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 10,4 | 19,5 | Customer |

Firma Microsoft będzie używać informacji kontaktowych o zdarzeniach dotyczących zabezpieczeń, aby skontaktować się z Tobą, jeśli firma Microsoft Security Response Center (MSRC) wykryje, że dostęp do danych zostały nadane przez nielegalną lub nieautoryzowaną stronę. Przejrzyj zdarzenia po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt z zabezpieczeniami Azure Security Center](../../security-center/security-center-provide-security-contact-details.md)

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: Uwzględnij alerty zabezpieczeń w systemie odpowiedzi na zdarzenia

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 10.5 | 19,6 | Customer |

Eksportuj alerty i zalecenia dotyczące Azure Security Center przy użyciu funkcji eksportu ciągłego, aby pomóc identyfikować zagrożenia dla zasobów platformy Azure. Eksport ciągły umożliwia wyeksportowanie alertów i zaleceń ręcznie lub w stały sposób ciągły. Możesz użyć łącznika danych Azure Security Center do przesyłania strumieniowego alertów do usługi Azure wskaźnikowej.

- [Jak skonfigurować eksport ciągły](../../security-center/continuous-export.md)

- [Jak przesłać strumieniowo alerty do usługi Azure Sentinel](../../sentinel/connect-azure-security-center.md)

## <a name="106-automate-the-response-to-security-alerts"></a>10,6: Automatyzowanie odpowiedzi na alerty zabezpieczeń

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność za |
|--|--|--|
| 10,6 | 19 | Customer |

Funkcja automatyzacji przepływu pracy w programie Azure Security Center umożliwia automatyczne wyzwalanie odpowiedzi w Logic Apps ramach alertów zabezpieczeń i zaleceń dotyczących ochrony zasobów platformy Azure.

- [Jak skonfigurować automatyzację przepływu pracy i Logic Apps](../../security-center/workflow-automation.md)


## <a name="next-steps"></a>Następne kroki

- Zobacz następną kontrolę zabezpieczeń: [testy penetracji i czerwone ćwiczenia zespołu](security-control-penetration-tests-red-team-exercises.md)