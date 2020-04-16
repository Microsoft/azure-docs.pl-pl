---
title: Kontrola zabezpieczeń platformy Azure — reagowanie na incydenty
description: Reakcja zdarzenia kontroli zabezpieczeń platformy Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 993793d21e6253188dfc199d8701cbe117503517
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408430"
---
# <a name="security-control-incident-response"></a>Kontrola bezpieczeństwa: reagowanie na incydenty

Chroń informacje o organizacji, a także jej reputację, opracowując i wdrażając infrastrukturę reagowania na incydenty (np. plany, określone role, szkolenia, komunikację, nadzór nad zarządzaniem), aby szybko odkryć atak, a następnie skutecznie ograniczyć szkody, wyeliminować obecność atakującego i przywrócić integralność sieci i systemów.

## <a name="101-create-an-incident-response-guide"></a>10.1: Tworzenie przewodnika po zdarzeniach

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | Klient |

Skompiluj przewodnik po zdarzeniach dla swojej organizacji. Upewnij się, że istnieją pisemne plany reagowania na incydenty, które definiują wszystkie role personelu, a także fazy obsługi incydentów/zarządzania od wykrywania do przeglądu po incydencie.  

- [Wskazówki dotyczące tworzenia własnego procesu reagowania na incydenty bezpieczeństwa](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia incydentu w centrum reagowania na zabezpieczenia firmy Microsoft](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Wykorzystaj przewodnik obsługi incydentów związanych z bezpieczeństwem komputerowym NIST, aby pomóc w stworzeniu własnego planu reagowania na incydenty](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Tworzenie procedury oceniania incydentów i ustalania priorytetów

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 10.2 | 19.8 | Klient |

Usługa Security Center przypisuje ważność do każdego alertu, aby ułatwić ustalanie priorytetów alertów, które powinny być najpierw zbadane. Ważność opiera się na tym, jak pewny jest usługa Security Center w znalezieniu lub analityczna używana do wystawiania alertu, a także poziom zaufania, że za działaniem, które doprowadziło do alertu, było złośliwe intencje. 

Dodatkowo wyraźnie zaznacz subskrypcje (np. produkcji, non-prod) przy użyciu tagów i utworzyć system nazewnictwa, aby wyraźnie zidentyfikować i kategoryzować zasoby platformy Azure, zwłaszcza tych przetwarzania poufnych danych.  Twoim obowiązkiem jest nadanie priorytetu korygowania alertów na podstawie krytyczności zasobów platformy Azure i środowiska, w którym wystąpił incydent.

- [Alerty zabezpieczeń w Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [Organizowanie zasobów platformy Azure za pomocą tagów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="103-test-security-response-procedures"></a>10.3: Testowanie procedur reagowania na zabezpieczenia

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 10.3 | 19 | Klient |

Przeprowadzaj ćwiczenia, aby przetestować możliwości reagowania na zdarzenia w systemach w regularnym rytmie, aby chronić zasoby platformy Azure. Zidentyfikować słabe punkty i luki oraz w razie potrzeby dokonać przeglądu planu.

- [Publikacja NIST - Przewodnik po testach, szkoleniach i programach ćwiczeń dla planów i możliwości IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Podaj dane kontaktowe zdarzeń bezpieczeństwa i konfiguruj powiadomienia o alertach dla incydentów bezpieczeństwa

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 10.4 | 19.5 | Klient |

Informacje kontaktowe dotyczące zdarzeń związanych z bezpieczeństwem będą używane przez firmę Microsoft do kontaktu się z Użytkownikiem, jeśli Centrum Microsoft Security Response Center (MSRC) odkryje, że dostęp do twoich danych uzyskała strona niezgodna z prawem lub nieautoryzowana. Przejrzyj incydenty po fakcie, aby upewnić się, że problemy zostały rozwiązane.

- [Jak ustawić kontakt zabezpieczeń usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Włącz alerty bezpieczeństwa do systemu reagowania na incydenty

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 10.5 | 19.6 | Klient |

Eksportuj alerty i zalecenia usługi Azure Security Center za pomocą funkcji ciągłego eksportowania w celu identyfikowania zagrożeń związanych z zasobami platformy Azure. Ciągły eksport umożliwia eksportowanie alertów i zaleceń ręcznie lub w sposób ciągły. Możesz użyć łącznika danych usługi Azure Security Center do przesyłania strumieniowego alertów do usługi Azure Sentinel.

- [Jak skonfigurować eksport ciągły](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Jak przesyłać strumieniowo alerty do usługi Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

## <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatyzacja reakcji na alerty bezpieczeństwa

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 10.6 | 19 | Klient |

Użyj funkcji Automatyzacja przepływu pracy w usłudze Azure Security Center, aby automatycznie wyzwalać odpowiedzi za pośrednictwem "Aplikacji logiki" w alertach i zaleceniach dotyczących zabezpieczeń w celu ochrony zasobów platformy Azure.

- [Jak skonfigurować automatyzację przepływu pracy i aplikacje logiki](https://docs.microsoft.com/azure/security-center/workflow-automation)


## <a name="next-steps"></a>Następne kroki

- Zobacz następną kontrolę bezpieczeństwa: [testy penetracyjne i ćwiczenia czerwonej drużyny](security-control-penetration-tests-red-team-exercises.md)