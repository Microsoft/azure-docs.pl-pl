---
title: Współpraca z usługą Azure DDoS Protection Standard
description: Informacje o możliwościach partnerskich włączonych przez Azure DDoS Protection Standard.
ms.service: virtual-network
documentationcenter: na
author: anupamvi
mms.devlang: na
ms.topic: how-to
ms.date: 01/28/2020
ms.author: kumud
ms.openlocfilehash: 8136ea2a567ac823f03941ebd4a67bed043ada07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84689355"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Współpraca z usługą Azure DDoS Protection Standard
W tym artykule opisano możliwości partnerskie włączane przez Standard Azure DDoS Protection. Ten artykuł ma na celu ułatwienie menedżerom produktów i rolom biznesowym zrozumienia ścieżki inwestycyjnej i zapewnienie wglądu w propozycję wartości partnerskiej.

## <a name="background"></a>Tło
Rozproszone ataki typu "odmowa usługi" (DDoS) to jedna z najważniejszych problemów dotyczących dostępności i zabezpieczeń, które klienci przenoszą aplikacje do chmury. Dzięki extortion i Hacktivism są typowymi motywacjami związanymi z atakami na DDoS, są one ciągle coraz bardziej skalowane w rodzaju, skali i częstotliwości występowania, ponieważ są stosunkowo proste i tanie do uruchomienia.

Azure DDoS Protection zapewnia środki zaradcze dotyczące najbardziej zaawansowanych zagrożeń DDoS, wykorzystując globalną skalę sieci platformy Azure. Usługa zapewnia ulepszone możliwości ograniczania DDoS dla aplikacji i zasobów wdrożonych w sieciach wirtualnych.

Partnerzy technologiczni mogą chronić swoje zasoby klientów natywnie przy użyciu Azure DDoS Protection Standard, aby rozwiązać problemy z dostępnością i niezawodnością spowodowaną atakami DDoS.

## <a name="introduction-to-azure-ddos-protection-standard"></a>Wprowadzenie do Azure DDoS Protection Standard
Azure DDoS Protection Standard zapewnia ulepszone możliwości ograniczania DDoS do ataków warstwy 3 i 4 DDoS. Poniżej przedstawiono kluczowe funkcje usługi DDoS Protection Standard.

### <a name="adaptive-real-time-tuning"></a>Adaptacyjne dostrajanie w czasie rzeczywistym
Dla każdej chronionej aplikacji Azure DDoS Protection Standard automatycznie dostosowuje progi zasad łagodzenia DDoS na podstawie wzorców profilu ruchu aplikacji. Usługa wykonuje to dostosowanie przy użyciu dwóch szczegółowych informacji:

- Automatyczna nauka wzorców ruchu dla poszczególnych klientów (na adres IP) dla warstwy 3 i 4.
- Minimalizacja fałszywych wartości dodatnich, biorąc pod uwagę, że skalowanie systemu Azure pozwala na pochłanianie znacznego natężenia ruchu.

![Adaptacyjne dostosowanie czasu rzeczywistego](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>Ataki, analiza, dane telemetryczne, monitorowanie i alerty
Azure DDoS Protection identyfikuje i zmniejsza ataki DDoS bez żadnej interwencji użytkownika.

- Jeśli chroniony zasób znajduje się w subskrypcji objętej Azure Security Center, DDoS Protection Standard automatycznie wyśle alert, aby Security Center za każdym razem, gdy zostanie wykryty atak DDoS i zostanie on skorygowany do chronionej aplikacji.
- Alternatywnie, aby otrzymywać powiadomienia, gdy istnieje aktywne środki zaradcze dla chronionego publicznego adresu IP, można [skonfigurować alert](manage-ddos-protection.md#configure-alerts-for-ddos-protection-metrics) dotyczący metryki w obszarze atak DDoS.
- Ponadto możesz utworzyć alerty dla innych metryk DDoS i [skonfigurować ataki analizy](manage-ddos-protection.md#configure-ddos-attack-analytics) , aby zrozumieć skalę ataku, porzuconego ruchu, wektorów ataków, najważniejszych współautorów i inne szczegóły.

![Metryki DDoS](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>DDoS szybka odpowiedź (DRR)
Klienci standardowi DDoS Protection mogą uzyskać dostęp do [szybkiego zespołu odpowiedzi](https://azure.microsoft.com/blog/ddos-protection-attack-analytics-rapid-response/) podczas aktywnego ataku. DRR może pomóc w badaniu ataków, niestandardowych ograniczeniach podczas ataku i analizie po ataku.

### <a name="sla-guarantee-and-cost-protection"></a>Gwarancja SLA i ochrona kosztów
Usługa DDoS Protection Standard jest objęta umową SLA na 99,99%, a ochrona kosztów zapewnia środki na korzystanie z zasobów w celu skalowania w poziomie podczas udokumentowanego ataku. Aby uzyskać więcej informacji, zobacz [Umowa SLA dla Azure DDoS Protection](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/).

## <a name="featured-partner-scenarios"></a>Polecane scenariusze partnerskie
Poniżej przedstawiono kluczowe korzyści, które można wykorzystać w ramach integracji ze standardem Azure DDoS Protection:

- W zapleczu są automatycznie chronione oferowane Azure DDoS Protection przez partnerów usługi równoważenia obciążenia, zapory aplikacji sieci Web, zapory itp.) dla klientów.
- Partnerzy mają dostęp do Azure DDoS Protection standardowej analizy ataków i telemetrii, którą mogą zintegrować z własnymi produktami, oferując ujednolicone środowisko klienta.  
- Partnerzy mają dostęp do DDoS szybkiej odpowiedzi nawet w przypadku braku szybkiej odpowiedzi na platformę Azure w przypadku problemów związanych z DDoS.
- Chronione aplikacje partnerów są obsługiwane przez gwarancję SLA DDoS i ochronę kosztów w przypadku ataków DDoS.

## <a name="technical-integration-overview"></a>Omówienie integracji technicznej
Dostęp do Azure DDoS Protection standardowych możliwości partnerskich są udostępniane za pośrednictwem Azure Portal, interfejsów API i interfejsu wiersza polecenia/PS.

### <a name="integrate-with-ddos-protection-standard"></a>Integracja z usługą DDoS Protection Standard
Aby partnerzy mogli skonfigurować integrację ze standardem Azure DDoS Protection, wymagane są następujące kroki:
1. Utwórz plan DDoS Protection w żądanej subskrypcji (partnerskiej). Aby uzyskać instrukcje krok po kroku, zobacz temat [Tworzenie planu ochrony DDoS Standard](manage-ddos-protection.md#create-a-ddos-protection-plan).
   > [!NOTE]
   > Dla danej dzierżawy należy utworzyć tylko 1 plan DDoS Protection. 
2. Wdróż usługę z publicznym punktem końcowym w ramach subskrypcji (partnerskich), na przykład modułu równoważenia obciążenia, zapór i zapory aplikacji sieci Web. 
3. Włącz Standard Azure DDoS Protection w sieci wirtualnej usługi z publicznymi punktami końcowymi przy użyciu planu DDoS Protection utworzonego w pierwszym kroku. Aby uzyskać szczegółowe instrukcje, zobacz [Włączanie planu ochrony standardowego](manage-ddos-protection.md#enable-ddos-for-an-existing-virtual-network) (Stpe) DDoS
   > [!IMPORTANT] 
   > Po włączeniu standardu Azure DDoS Protection w sieci wirtualnej wszystkie publiczne adresy IP w ramach tej sieci wirtualnej są automatycznie chronione. Te publiczne adresy IP mogą znajdować się na platformie Azure (subskrypcja klienta) lub poza platformą Azure. 
4. Opcjonalnie można zintegrować Azure DDoS Protection standardowe dane telemetryczne i analizę ataków na pulpicie nawigacyjnym dostępnym dla klienta. Aby uzyskać więcej informacji o korzystaniu z telemetrii, zobacz [korzystanie DDoS Protection telemetrii](manage-ddos-protection.md#use-ddos-protection-telemetry). Aby uzyskać więcej informacji na temat konfigurowania usługi ataków, zobacz [Konfigurowanie DDoS ataków](manage-ddos-protection.md#configure-ddos-attack-analytics)

### <a name="onboarding-guides-and-technical-documentation"></a>Przewodniki dołączania i dokumentacja techniczna

- [Strona produktu Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/)
- [Dokumentacja Azure DDoS Protection](ddos-protection-overview.md)
- [Dokumentacja interfejsu API Azure DDoS Protection](https://docs.microsoft.com/rest/api/virtualnetwork/ddosprotectionplans)
- [Dokumentacja interfejsu API usługi Azure Virtual Network](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>Uzyskaj pomoc

- Jeśli masz pytania dotyczące integracji aplikacji, usługi lub produktu ze standardem Azure DDoS Protection, skontaktuj się z [społecznością usługi Azure Security](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security).
- Obserwuj dyskusje na [Stack Overflow](https://stackoverflow.com/tags/azure-ddos/).

### <a name="get-to-market"></a>Zyskaj na rynek

- Podstawowym programem do współpracy z firmą Microsoft jest [Microsoft Partner Network](https://partner.microsoft.com/). — Microsoft Graph integracje zabezpieczeń należą do ścieżki [niezależnego dostawcy oprogramowania (ISV) MPN](https://partner.microsoft.com/saas-solution-guide) .
- [Microsoft Intelligent Security Association](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) to program przeznaczony dla partnerów zabezpieczeń firmy Microsoft, który ułatwia wzbogacanie produktów zabezpieczeń i zwiększa możliwości wykrywania klientów w zakresie integracji z produktami firmy Microsoft.

## <a name="next-steps"></a>Następne kroki
Wyświetl istniejące integracje partnerów:

- [Barracuda WAF jako usługa](https://www.barracuda.com/waf-as-a-service)
- [Azure Cloud WAF z Radware](https://www.radware.com/resources/microsoft-azure/)
