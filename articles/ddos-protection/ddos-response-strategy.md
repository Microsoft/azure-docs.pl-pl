---
title: Składniki strategii reagowania na ataki DDoS
description: Dowiedz się, jak używać standardu Azure DDoS Protection, aby reagować na ataki DDoS.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 2b31a8aa8b126c228ac7e9c3ca182300c710b098
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97814061"
---
# <a name="components-of-a-ddos-response-strategy"></a>Składniki strategii reagowania na ataki DDoS

Atak DDoS, który jest przeznaczony dla zasobów platformy Azure, zazwyczaj wymaga minimalnej interwencji z punktu widzenia użytkownika. Nadal, uwzględnianie środków zaradczych DDoS w ramach strategii reagowania na zdarzenia pomaga zminimalizować wpływ na ciągłość działania.

## <a name="microsoft-threat-intelligence"></a>Analiza zagrożeń firmy Microsoft

Firma Microsoft ma rozbudowaną sieć analizy zagrożeń. Ta sieć korzysta z zbiorczej wiedzy o rozszerzonej społeczności zabezpieczeń, która obsługuje firmę Microsoft Usługi online, partnerów firmy Microsoft i relacje w społeczności Internet Security. 

Jako dostawca infrastruktury krytycznej firma Microsoft otrzymuje wczesne ostrzeżenia o zagrożeniach. Firma Microsoft zbiera informacje o analizie zagrożeń z jej Usługi online i od jej globalnej bazy klientów. Firma Microsoft obejmuje wszystkie te analizy zagrożeń z powrotem w Azure DDoS Protection produkty.

Ponadto firma Microsoft Digital Zbrodnis (DCU) wykonuje obraźliwe strategie przeciwko botnetami. Botnetami są typowym źródłem poleceń i kontroli dla ataków DDoS.

## <a name="risk-evaluation-of-your-azure-resources"></a>Ocena ryzyka zasobów platformy Azure

Konieczna jest znajomość zakresu ryzyka od ataku DDoS na bieżąco. Okresowo zadawaj pytania:

- Jakie nowe publicznie dostępne zasoby platformy Azure wymagają ochrony?

- Czy istnieje single point of failure w usłudze? 

- Jak usługi mogą być izolowane w celu ograniczenia wpływu ataku, a w dalszym ciągu udostępniają usługi prawidłowym klientom?

- Czy istnieją sieci wirtualne, w których należy włączyć DDoS Protection Standard, ale nie są? 

- Czy moje usługi są aktywne/aktywne z trybem failover w wielu regionach?

Ważne jest, aby zrozumieć normalne zachowanie aplikacji i przygotować się do działania, jeśli aplikacja nie zachowuje się w oczekiwany sposób podczas ataku DDoS. Mają skonfigurowane monitory dla aplikacji o znaczeniu krytycznym, które naśladią zachowanie klienta i powiadamiają o wykryciu odpowiednich anomalii. Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi monitorowania i diagnostyki](/azure/architecture/best-practices/monitoring#monitoring-and-diagnostics-scenarios) , aby uzyskać wgląd w kondycję aplikacji.

[Azure Application Insights](../azure-monitor/app/app-insights-overview.md) to rozszerzalna usługa zarządzania wydajnością aplikacji (APM) dla deweloperów sieci Web na wielu platformach. Użyj Application Insights do monitorowania działającej aplikacji sieci Web. Automatycznie wykrywa anomalie wydajności. Zawiera narzędzia analityczne, które ułatwiają diagnozowanie problemów i zrozumienie, co użytkownicy robią z Twoją aplikacją. Usługa ta pomaga w ciągłym udoskonalaniu wydajności i użyteczności tworzonych rozwiązań.

## <a name="customer-ddos-response-team"></a>Zespół odpowiedzi DDoS klienta

Tworzenie zespołu odpowiedzi DDoS to kluczowy krok w celu szybkiego i efektywnego ataku. Zidentyfikuj kontakty w organizacji, które będą nadzorowały planowanie i wykonywanie. Ten zespół odpowiedzi DDoS powinien starannie zrozumieć usługę Azure DDoS Protection Standard. Upewnij się, że zespół może identyfikować i ograniczać ataki przez koordynowanie z klientami wewnętrznymi i zewnętrznymi, w tym z zespołem pomocy technicznej firmy Microsoft. 

Zalecamy używanie ćwiczeń symulacji jako zwykłych części planowania dostępności i ciągłości usług, a ćwiczenia te powinny obejmować testowanie skali. Zobacz [testowanie za pomocą symulacji](test-through-simulations.md) , aby dowiedzieć się, jak symulować ruch testowy DDoS do publicznych punktów końcowych platformy Azure.

## <a name="alerts-during-an-attack"></a>Alerty w trakcie ataku

Azure DDoS Protection Standard identyfikuje i zmniejsza ataki DDoS bez żadnej interwencji użytkownika. Aby otrzymywać powiadomienia, gdy istnieje aktywne środki zaradcze dla chronionego publicznego adresu IP, można [skonfigurować alerty](alerts.md).

### <a name="when-to-contact-microsoft-support"></a>Kiedy należy skontaktować się z pomocą techniczną firmy Microsoft

Azure DDoS Protection klienci standardowi mają dostęp do zespołu szybkiego reagowania na DDoS (DRR), który może pomóc w zbadaniu ataku w trakcie ataku, a także analizy po ataku. Zobacz [DDoS szybką odpowiedź](ddos-rapid-response.md) , aby uzyskać więcej informacji, w tym o tym, kiedy należy skontaktować się z zespołem DRR.

## <a name="post-attack-steps"></a>Kroki po ataku

Jest to zawsze dobrą strategią wykonywania postmortem po ataku i dostosowania strategii odpowiedzi DDoS zgodnie z wymaganiami. Zagadnienia, które należy wziąć pod uwagę:

- Czy w przypadku braku skalowalnej architektury występuje zakłócenie w działaniu usługi lub użytkownika?

- Które aplikacje lub usługi były największe?

- Jak skutecznie była strategia odpowiedzi DDoS i jak można ją ulepszyć?

Jeśli podejrzewasz, że jesteś w trakcie ataku DDoS, przeprowadzenie eskalacji zwykłych kanałów pomocy technicznej platformy Azure.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć plan ochrony DDoS](manage-ddos-protection.md).