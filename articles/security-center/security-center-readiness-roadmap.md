---
title: Mapa gotowości usługi Azure Security Center | Microsoft Docs
description: Ten dokument zawiera mapę gotowości umożliwiającą zwiększenie możliwości usługi Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: memildin
ms.openlocfilehash: e4f905b7a08564eadb20d65d651c137ef3741376
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100325"
---
# <a name="azure-security-center-readiness-roadmap"></a>Plan gotowości Azure Security Center
Ten dokument zapewnia mapę gotowości, która będzie pomocna w rozpoczynaniu pracy z usługą Azure Security Center.

## <a name="understanding-security-center"></a>Opis usługi Security Center
Usługa Azure Security Center zapewnia ujednolicone zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami na potrzeby obciążeń uruchamianych na platformie Azure, lokalnie i w innych chmurach. 

Użyj następujących zasobów, aby rozpocząć pracę z usługą Security Center.

Artykuły
- [Wprowadzenie do usługi Azure Security Center](security-center-introduction.md)
- [Przewodnik Szybki start dotyczący usługi Azure Security Center](security-center-get-started.md)

Filmy wideo
- [Wideo z szybkim wprowadzeniem](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
- [Omówienie możliwości związanych z zapobieganiem, wykrywaniem i reagowaniem usługi Security Center](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>Planowanie i operacje

Aby w pełni wykorzystać zalety usługi Security Center, trzeba zrozumieć, w jak różny sposób będą używać usługi poszczególne osoby i zespoły w organizacji, gdyż umożliwi to spełnienie wymagań związanych z bezpieczeństwem obsługi, monitorowaniem, zarządzaniem i reagowaniem na zdarzenia.

Użyj następujących zasobów, aby uzyskać pomoc w procesach planowania i obsługi.

- [Przewodnik planowania i obsługi usługi Azure Security Center](security-center-planning-and-operations-guide.md)


### <a name="onboarding-computers-to-security-center"></a>Dołączanie komputerów do usługi Security Center
Security Center automatycznie wykrywa wszelkie subskrypcje lub obszary robocze platformy Azure, które nie są chronione przez usługę Azure Defender. Obejmuje to subskrypcje platformy Azure korzystające z Security Center bezpłatnych i obszarów roboczych, dla których nie włączono rozwiązania zabezpieczeń.

Użyj następujących zasobów, aby uzyskać pomoc w procesach dołączania.

- [Dołączanie komputerów spoza platformy Azure](quickstart-onboard-machines.md)
- [Hybrydowa usługa Azure Security Center — omówienie](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Zmniejszenie zagrożeń dla bezpieczeństwa przy użyciu usługi Security Center
Usługa Security Center automatycznie gromadzi, analizuje i integruje dane dzienników z zasobów platformy Azure, sieci oraz połączonych rozwiązań partnerskich, takich jak rozwiązania zapory i ochrony punktów końcowych, aby wykrywać prawdziwe zagrożenia i redukować liczbę fałszywych alarmów.

Użyj następujących zasobów, aby uzyskać pomoc w zarządzaniu alertami zabezpieczeń i zabezpieczaniu swoich zasobów.

Artykuły    
- [Ochrona sieci w usłudze Azure Security Center](./security-center-network-recommendations.md)
- [Ochrona usługi Azure SQL i danych w usłudze Azure Security Center](./security-center-remediate-recommendations.md)


Wideo    
- [Zmniejszenie zagrożeń dla bezpieczeństwa przy użyciu usługi Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>Korzystanie z usługi Security Center w celu reagowania na zdarzenia
Aby zmniejszyć koszty i szkody, ważne jest, aby przed atakiem uzyskać plan reagowania na zdarzenia. Usługi Azure Security Center można używać na różnych etapach reagowania na zdarzenia.

Użyj następujących zasobów, aby zrozumieć sposób dołączenia usługi Security Center do procesu reagowania na zdarzenia.

Filmy wideo    
* [Usługa Azure Security Center na potrzeby reagowania na zdarzenia](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Szybkie reagowanie na zagrożenia przy użyciu zabezpieczeń, obsługi i badań nowej generacji](https://youtu.be/e8iFCz5RM4g)

Artykuły    
* [Używanie usługi Azure Security Center do reagowania na zdarzenia](./tutorial-security-incident.md)
* [Reagowanie na wyzwalacze Security Center przy użyciu usługi Automation](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>Zaawansowana ochrona w chmurze

Maszyny wirtualne platformy Azure mogą wykorzystywać zaawansowane możliwości ochrony w chmurze w usłudze Security Center. Te możliwości obejmują dostęp do maszyn wirtualnych just-in-Time i adaptacyjnych kontroli aplikacji.

Użyj następujących zasobów, aby dowiedzieć się, jak korzystać z tych możliwości w usłudze Security Center.

Filmy wideo    
* [Azure Security Center — dostęp just in Time do maszyny wirtualnej](https://youtu.be/UOQb2FcdQnU)
* [Azure Security Center — funkcje adaptacyjnego sterowania aplikacjami](https://youtu.be/wWWekI1Y9ck)

Artykuły    
* [Zarządzanie dostępem do maszyny wirtualnej przy użyciu funkcji just in Time](./security-center-just-in-time.md)
* [Funkcje adaptacyjnego sterowania aplikacjami w usłudze Azure Security Center](./security-center-adaptive-application.md)

## <a name="hands-on-activities"></a>Praktyczne działania

* [Praktyczne laboratorium usługi Security Center](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Podręcznik dotyczący zaleceń zapory aplikacji internetowej (WAF) w usłudze Security Center](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Podręcznik dotyczący usługi Azure Security Center: alerty zabezpieczeń](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Strona dokumentacji usługi Security Center](./index.yml)
* [Strona dokumentacji interfejsu API REST usługi Security Center](/previous-versions/azure/reference/mt704034(v=azure.100))
* [Często zadawane pytania dotyczące usługi Azure Security Center](./faq-general.md)
* [Strona cennika usługi Security Center](https://azure.microsoft.com/pricing/details/security-center/)
* [Najlepsze rozwiązania z zakresu zabezpieczeń tożsamości](../security/fundamentals/identity-management-best-practices.md)
* [Najlepsze rozwiązania z zakresu zabezpieczeń sieci](../security/fundamentals/network-best-practices.md)
* [Zalecenia PaaS](../security/fundamentals/paas-deployments.md)
* [Zgodność](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [Klienci usługi log Analytics mogą teraz używać Azure Security Center do ochrony obciążeń chmury hybrydowej](/archive/blogs/msoms/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads)

## <a name="community-resources"></a>Zasoby społeczności

* [Usługa Security Center UserVoice](https://feedback.azure.com/forums/347535-azure-security-center)
* [P&stronę dla Security Center](/answers/topics/azure-security-center.html)