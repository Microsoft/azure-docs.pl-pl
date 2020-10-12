---
title: Użyj zaleceń Azure Security Center, aby zwiększyć bezpieczeństwo | Microsoft Docs
description: " Dowiedz się, jak korzystać z zasad zabezpieczeń i zaleceń w Azure Security Center, aby pomóc w ograniczeniu ataku zabezpieczeń. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 4c6b8b0bfa78dca5ca32c8c72edcc463ebb9057a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322142"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Używanie zaleceń usługi Azure Security Center w celu ulepszania zabezpieczeń

Aby zmniejszyć prawdopodobieństwo wystąpienia poważnych zdarzeń zabezpieczeń, można skonfigurować zasady zabezpieczeń, a następnie zaimplementować zalecenia udostępniane przez Azure Security Center. W tym artykule pokazano, jak używać zasad zabezpieczeń i zaleceń w Security Center, aby pomóc w ograniczeniu ataku zabezpieczeń. 

Security Center automatycznie uruchamia ciągłego skanowania w celu przeanalizowania stanu zabezpieczeń zasobów platformy Azure. Gdy Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy zalecenia, które przeprowadzą Cię przez proces konfigurowania wymaganych kontroli zabezpieczeń. Security Center aktualizuje zalecenia w ciągu 24 godzin, z następującymi wyjątkami:

- Zalecenia dotyczące konfiguracji zabezpieczeń systemu operacyjnego zostały zaktualizowane w ciągu 48 godzin
- Zalecenia dotyczące Endpoint Protection problemów są aktualizowane w ciągu 8 godzin

## <a name="scenario"></a>Scenariusz
W tym scenariuszu przedstawiono sposób użycia Security Center, aby zmniejszyć prawdopodobieństwo wystąpienia zdarzenia związanego z zabezpieczeniami przez monitorowanie Security Center zaleceń i podejmowanie działań. W scenariuszu jest stosowana fikcyjna firma, firma Contoso i role przedstawione w [przewodniku planowania i](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)obsługi Security Center. W tym scenariuszu koncentrujemy się na rolach następujących osób:

![Role scenariusza](./media/security-center-using-recommendations/scenario-roles.png)

Firma Contoso niedawno przeprowadzono migrację niektórych zasobów lokalnych na platformę Azure. Firma Contoso chce chronić swoje zasoby i ograniczyć liczbę luk w zabezpieczeniach w chmurze.

## <a name="use-azure-security-center"></a>Korzystanie z usługi Azure Security Center
David, od zabezpieczeń IT firmy Contoso, został już wybrany do dołączenia Security Center w subskrypcjach firmy Contoso w celu Azure Security Center zapobiegania i wykrywania luk w zabezpieczeniach. 

Security Center automatycznie analizuje stan zabezpieczeń zasobów platformy Azure firmy Contoso i stosuje domyślne zasady zabezpieczeń. Gdy Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy **zalecenia** na podstawie kontrolek ustawionych w zasadach zabezpieczeń. 

David uruchamia zabezpieczenia platformy Azure z włączonym usługą Azure Defender, we wszystkich swoich subskrypcjach, aby uzyskać pełny pakiet zaleceń i funkcji zabezpieczeń. Jan również dołącza wszystkie istniejące serwery lokalne, które nie zostały jeszcze poddane migracji do chmury, dzięki czemu mogą korzystać z pomocy technicznej hybrydowej Security Center na [serwerach z systemami Windows i Linux](quickstart-onboard-machines.md).

Jan jest właścicielem obciążenia w chmurze. Jan jest odpowiedzialny za stosowanie kontroli zabezpieczeń zgodnie z zasadami zabezpieczeń firmy Contoso. 

Jan wykonuje następujące zadania:

- Monitoruj zalecenia dotyczące zabezpieczeń udostępniane przez Security Center
- Oceń zalecenia dotyczące zabezpieczeń i zdecyduj, czy mają być stosowane lub odrzucane zalecenia.
- Zastosuj zalecenia dotyczące zabezpieczeń

### <a name="remediate-threats-using-recommendations"></a>Koryguj zagrożenia przy użyciu rekomendacji
W ramach codziennych działań monitorowania Jan loguje się do platformy Azure i otwiera Security Center. 

1. Jan wybiera subskrypcje obciążeń.

2. Jan sprawdza **bezpieczny wynik** , aby uzyskać ogólny obraz tego, jak zabezpieczy się subskrypcje i widzi, że wynik to 548.

3. Jan musi zdecydować, które zalecenia należy obsłużyć w pierwszej kolejności. Dlatego Jan klika polecenie Secure Score i zaczyna obsługiwać zalecenia w zależności od tego, jak dużo poprawia swój [bezpieczny wynik](secure-score-security-controls.md).

4. Ponieważ Jan ma wiele połączonych maszyn wirtualnych, Marcin decyduje się skupić na jego maszynach w [spisie zasobów](asset-inventory.md).

5. Gdy Jan otworzy spis zasobów, zostanie wyświetlona lista zaleceń. Jan obsługuje je zgodnie z bezpiecznym wpływem na ocenę.

6. Marcin ma wiele maszyn wirtualnych połączonych z Internetem, a ponieważ ich porty są ujawniane, są martwisz się, że osoba atakująca może przejąć kontrolę nad serwerami. Dlatego Jan wybiera, aby korzystać z [**dostępu just in Time do maszyny wirtualnej**](security-center-just-in-time.md).

Marcin kontynuuje przechodzenie między zaleceniami o wysokim priorytecie i o średnim priorytecie i podejmuje decyzje dotyczące wdrożenia. W przypadku każdego zalecenia firma Jan przegląda szczegółowe informacje udostępniane przez Security Center, aby zrozumieć, jakie zasoby mają wpływ, jaki jest wpływ na to, jakie są te zalecenia, co oznacza, jakie są środki zaradcze w zakresie rozwiązywania problemów.

### <a name="enforce-recommendations-to-prevent-security-misconfigurations"></a>Wymuś zalecenia, aby zapobiec błędom konfiguracji zabezpieczeń

Aby upewnić się, że użytkownicy nie tworzą zasobów, które negatywnie wpływają na ocenę Jan, konfigurują opcje Wymuszaj i Odmów na zaleceniach, które są dla nich najważniejsze. Dowiedz się więcej w temacie [zapobieganie błędom konfiguracji z zaleceń Wymuszaj/Odmów](prevent-misconfigurations.md).


## <a name="conclusion"></a>Podsumowanie
Zalecenia dotyczące monitorowania w Security Center pomagają wyeliminować luki w zabezpieczeniach przed wystąpieniem ataku. W przypadku korygowania zaleceń Twój bezpieczny wynik i stan zabezpieczenia są ulepszane. Security Center automatycznie odnajduje nowe wdrożone zasoby, ocenia je przed zasadami zabezpieczeń i udostępnia nowe zalecenia dotyczące zabezpieczania tych zasobów.


## <a name="next-steps"></a>Następne kroki
Upewnij się, że masz proces monitorowania, w którym regularnie sprawdzasz zalecenia w Security Center, aby upewnić się, że zasoby są bezpieczne z upływem czasu.

W tym scenariuszu pokazano, jak używać zasad zabezpieczeń i zaleceń w Security Center, aby pomóc w ograniczeniu ataku zabezpieczeń.

Dowiedz się, jak reagować na zagrożenia [, aby zarządzać alertami zabezpieczeń i odpowiadać na](security-center-managing-and-responding-alerts.md)nie.
