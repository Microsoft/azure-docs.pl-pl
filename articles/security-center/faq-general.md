---
title: Często zadawane pytania dotyczące Azure Security Center — pytania ogólne
description: Często zadawane pytania ogólne dotyczące Azure Security Center, produktu, który pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 5695f9fa090419d803f4f3603b45b771321e5ce9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301452"
---
# <a name="faq---general-questions-about-azure-security-center"></a>Często zadawane pytania — ogólne pytania dotyczące Azure Security Center

## <a name="what-is-azure-security-center"></a>Co to jest usługa Azure Security Center?
Azure Security Center pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie dzięki zwiększonej widoczności i kontroli nad bezpieczeństwem zasobów. Umożliwia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami dla wszystkich subskrypcji, pomaga wykrywać zagrożenia, które w przeciwnym razie mogłyby pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

Security Center używa agenta Log Analytics do zbierania i przechowywania danych. Szczegółowe informacje znajdują się w temacie [zbieranie danych w Azure Security Center](security-center-enable-data-collection.md).


## <a name="how-do-i-get-azure-security-center"></a>Jak mogę uzyskać Azure Security Center?
Azure Security Center jest włączona z subskrypcją Microsoft Azure i jest dostępna z [Azure Portal](https://azure.microsoft.com/features/azure-portal/). Aby uzyskać do niego dostęp, [Zaloguj się do portalu](https://portal.azure.com), wybierz pozycję **Przeglądaj**i przewiń do **Security Center**.


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Które zasoby platformy Azure są monitorowane przez Azure Security Center?
Azure Security Center monitoruje następujące zasoby platformy Azure:

* Maszyny wirtualne (z uwzględnieniem [Cloud Services](../cloud-services/cloud-services-choose-me.md))
* Zestawy skalowania maszyn wirtualnych
* Rozwiązania partnerskie zintegrowane z subskrypcją platformy Azure, taką jak Zapora aplikacji sieci Web na maszynach wirtualnych i na App Service Environment
* [Wiele usług PaaS platformy Azure wymienionych w przeglądzie produktu](features-paas.md)


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Jak można zobaczyć bieżący stan zabezpieczeń moich zasobów platformy Azure?
Na stronie **przegląd Security Centeru** są wyświetlane ogólne stan zabezpieczeń środowiska, podzielone na dane dotyczące zasobów obliczeniowych, sieci, magazynu & i aplikacji. Każdy typ zasobu ma wskaźnik pokazujący zidentyfikowane luki w zabezpieczeniach. Kliknięcie każdego kafelka powoduje wyświetlenie listy problemów dotyczących zabezpieczeń zidentyfikowanych przez Security Center oraz spisu zasobów w ramach subskrypcji.



## <a name="what-is-a-security-policy"></a>Co to są zasady zabezpieczeń?
Zasady zabezpieczeń definiują zestaw formantów, które są zalecane dla zasobów w ramach określonej subskrypcji. W Azure Security Center definiujesz zasady dla subskrypcji platformy Azure zgodnie z wymaganiami firmy dotyczącymi zabezpieczeń oraz typem aplikacji lub poufnością danych w poszczególnych subskrypcjach.

Zasady zabezpieczeń włączone w Azure Security Center na dysku zalecenia dotyczące zabezpieczeń i monitorowania. Aby dowiedzieć się więcej na temat zasad zabezpieczeń, zobacz [monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md).


## <a name="who-can-modify-a-security-policy"></a>Kto może zmodyfikować zasady zabezpieczeń?
Aby zmodyfikować zasady zabezpieczeń, musisz być **administratorem zabezpieczeń** lub **właścicielem** tej subskrypcji.

Aby dowiedzieć się, jak skonfigurować zasady zabezpieczeń, zobacz [Ustawianie zasad zabezpieczeń w Azure Security Center](tutorial-security-policy.md).


## <a name="what-is-a-security-recommendation"></a>Co to jest zalecenie dotyczące zabezpieczeń?
Usługa Azure Security Center analizuje stan zabezpieczeń zasobów platformy Azure. Po zidentyfikowaniu potencjalnych luk w zabezpieczeniach są tworzone zalecenia. Zalecenia przeprowadzą Cię przez proces konfigurowania wymaganego formantu. Oto przykłady:

* Inicjowanie obsługi programu chroniącego przed złośliwym oprogramowaniem w celu identyfikowania i usuwania złośliwego oprogramowania
* [Sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md) i reguły sterujące ruchem do maszyn wirtualnych
* Inicjowanie obsługi zapory aplikacji sieci Web w celu ułatwienia obrony przed atakami ukierunkowanymi na aplikacje sieci Web
* Wdrażanie brakujących aktualizacji systemu
* Modyfikowanie konfiguracji systemu operacyjnego, które odbiegają od zalecanych standardów

Tutaj są wyświetlane tylko zalecenia włączone w zasadach zabezpieczeń.


## <a name="what-triggers-a-security-alert"></a>Co wyzwala alert zabezpieczeń?
Azure Security Center automatycznie gromadzi, analizuje i odrzuca dane dzienników z zasobów platformy Azure, sieci i rozwiązań partnerskich, takich jak oprogramowanie chroniące przed złośliwym kodem i zapory. Po wykryciu zagrożenia tworzony jest alert zabezpieczeń. Przykłady obejmują wykrywanie:

* Zagrożonych maszyn wirtualnych komunikujących się ze znanymi złośliwymi adresami IP
* Wykryto zaawansowane złośliwe oprogramowanie korzystające z raportowania błędów systemu Windows
* Ataków siłowych wobec maszyn wirtualnych
* Alerty zabezpieczeń z zintegrowanych rozwiązań zabezpieczeń partnerów, takich jak zapory chroniące przed złośliwym oprogramowaniem lub aplikacje sieci Web


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Jaka jest różnica między zagrożeniami wykrytymi przez centrum Microsoft Security Response Center a Azure Security Centerymi
Centrum Microsoft Security Response Center (MSRC) wykonuje wybór opcji monitorowanie zabezpieczeń sieci i infrastruktury platformy Azure, a następnie odbiera analizę zagrożeń i reklamacji nadużycia od stron trzecich. Gdy centrum MSRC uzyska dostęp do danych klienta przez nielegalną lub nieautoryzowaną stronę albo że klient korzystający z systemu Azure nie spełnia warunków akceptowalnego użycia, Menedżer zdarzeń zabezpieczeń powiadamia klienta. Powiadomienie zwykle odbywa się przez wysłanie wiadomości e-mail do kontaktów zabezpieczeń określonych w Azure Security Center lub właściciela subskrypcji platformy Azure, jeśli nie określono kontaktu zabezpieczeń.

Security Center to usługa platformy Azure, która stale monitoruje środowisko platformy Azure klienta i stosuje analizę w celu automatycznego wykrywania szerokiego zakresu potencjalnie złośliwych działań. Te wykrycia są rozłączone jako alerty zabezpieczeń na pulpicie nawigacyjnym Security Center.