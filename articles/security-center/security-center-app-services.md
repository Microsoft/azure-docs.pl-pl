---
title: Chronienie aplikacji internetowych i interfejsów API w usłudze Azure App Service
description: Ten artykuł pomaga rozpocząć ochronę Azure App Service aplikacji sieci Web i interfejsów API w programie Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: ec3fb8609612f3920e330da7922fdd1eb8883305
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2020
ms.locfileid: "89459780"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Chronienie aplikacji internetowych i interfejsów API w usłudze Azure App Service

Azure App Service to w pełni zarządzana platforma do tworzenia i hostowania aplikacji i interfejsów API sieci Web bez obaw o zarządzanie infrastrukturą. Umożliwia zarządzanie, monitorowanie i wgląd w dane operacyjne w celu spełnienia wymagań dotyczących wydajności, zabezpieczeń i zgodności klasy korporacyjnej. Aby uzyskać więcej informacji, zobacz [Azure App Service](https://azure.microsoft.com/services/app-service/).

Aby włączyć zaawansowaną ochronę przed zagrożeniami dla planu Azure App Service, musisz:

* Subskrybuj standardową warstwę cenową Azure Security Center
* Włącz plan App Service, jak pokazano poniżej. Security Center jest natywnie zintegrowana z App Service, eliminując konieczność wdrażania i dołączania — integracja jest niewidoczna.
* Mają plan App Service skojarzony z dedykowanymi maszynami. Obsługiwane są następujące plany: Basic, standard, Premium, izolowane lub Linux. Security Center nie obsługuje planów bezpłatna, współdzielona ani zużycie. Aby uzyskać więcej informacji, zobacz [plany App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).

Po włączeniu planu App Service Security Center ocenia zasoby objęte planem App Service i generuje zalecenia dotyczące zabezpieczeń na podstawie ich wyników. Security Center chroni wystąpienie maszyny wirtualnej, w której jest uruchomiony App Service i interfejs zarządzania. Monitoruje również żądania i odpowiedzi wysyłane do i z aplikacji uruchamianych w App Service.

Security Center wykorzystuje skalę chmury oraz widoczność, którą platforma Azure ma jako dostawcę chmury, aby monitorować typowe ataki aplikacji sieci Web. Security Center mogą wykrywać ataki w aplikacjach i identyfikować ataki, nawet gdy osoby atakujące znajdują się w fazie rekonesansego skanowania w celu identyfikowania luk w zabezpieczeniach w wielu aplikacjach hostowanych przez platformę Azure. Jako usługa natywna platformy Azure Security Center jest również w unikatowym miejscu, aby oferować analizę zabezpieczeń opartą na hoście obejmującą bazowe węzły obliczeniowe dla tego PaaS, umożliwiając Security Center wykrywające ataki na aplikacje sieci Web, które zostały już wykorzystane. Aby uzyskać więcej informacji, zobacz [Ochrona przed zagrożeniami dla Azure App Service](threat-protection.md#app-services).


## <a name="enable-monitoring-and-protection-of-app-service"></a>Włącz monitorowanie i ochronę App Service

1. W Azure Portal wybierz pozycję Security Center.
2. Przejdź do pozycji **cennik & ustawienia** i wybierz subskrypcję.
3. W obszarze **warstwa cenowa**w wierszu **usługi App Service** Przełącz swój plan na **włączony**.

    [![Włączanie usług App Services w ramach subskrypcji warstwy standardowej](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> Liczba wystąpień wymienionych dla danej **liczby zasobów** reprezentuje łączną liczbę wystąpień obliczeniowych w ramach wszystkich App Service planów w ramach tej subskrypcji, uruchomionych w momencie otwarcia bloku warstwy cenowej.
>
> Azure App Service oferuje różne plany. Plan App Service definiuje zestaw zasobów obliczeniowych dla aplikacji sieci Web do uruchomienia. Są one równoważne farmom serwerów w konwencjonalnym hostingu w sieci Web. Co najmniej jedna aplikacja może być skonfigurowana do uruchamiania w tych samych zasobach obliczeniowych (lub w tym samym planie App Service).
>
>Aby sprawdzić poprawność liczby, przejdź do obszaru "App Service plany" w witrynie Azure Portal, gdzie możesz zobaczyć liczbę wystąpień obliczeniowych używanych przez poszczególne plany. 






Aby wyłączyć monitorowanie i zalecenia dotyczące App Service, Powtórz ten proces i Przełącz plan **App Service** na **wyłączony**.



## <a name="see-also"></a>Zobacz także
W tym artykule przedstawiono sposób korzystania z funkcji monitorowania w usłudze Azure Security Center. Aby dowiedzieć się więcej na temat Azure Security Center, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania ustawień zabezpieczeń w usłudze Azure Security Center.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [App Services](security-center-virtual-machine-protection.md#app-services): Wyświetl listę środowisk usługi App Service za pomocą podsumowań kondycji.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Blog Azure Security](https://docs.microsoft.com/archive/blogs/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
