---
title: Wdróż bezpieczne aplikacje na Microsoft Azure
description: W tym artykule opisano najlepsze rozwiązania, które należy wziąć pod uwagę podczas fazy wydania i odpowiedzi projektu aplikacji sieci Web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 4c71ddbf1d2b435697b2707acf0b1262f2c5dc31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94517193"
---
# <a name="deploy-secure-applications-on-azure"></a>Wdrażanie bezpiecznych aplikacji na platformie Azure
W tym artykule opisano działania związane z bezpieczeństwem i kontrolki, które należy wziąć pod uwagę podczas wdrażania aplikacji w chmurze. Pytania zabezpieczające i pojęcia, które należy wziąć pod uwagę podczas fazy wydania i reakcji [cyklu życia programu Microsoft Security Development (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) . Celem jest ułatwienie zdefiniowania działań i usług platformy Azure, których można użyć do wdrożenia bezpieczniejszej aplikacji.

Następujące fazy SDL zostały omówione w tym artykule:

- Release
- Reakcja

## <a name="release"></a>Release
Fokus fazy wydania jest gotowy do projektu dla wydania publicznego.
Obejmuje to planowanie sposobów efektywnego wykonywania zadań obsługi po wydaniu i rozwiązywania luk w zabezpieczeniach, które mogą wystąpić później.

### <a name="check-your-applications-performance-before-you-launch"></a>Sprawdź wydajność aplikacji przed uruchomieniem

Sprawdź wydajność aplikacji przed jej uruchomieniem lub Wdróż aktualizacje w środowisku produkcyjnym. Uruchamiaj [testy obciążenia](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) oparte na chmurze za pomocą programu Visual Studio, aby znaleźć problemy z wydajnością w aplikacji, zwiększyć jakość wdrożenia, upewnić się, że aplikacja jest zawsze dostępna, i że aplikacja może obsługiwać ruch dla danego uruchomienia.

### <a name="install-a-web-application-firewall"></a>Zainstaluj zaporę aplikacji sieci Web

Aplikacje internetowe coraz częściej stają się obiektami złośliwych ataków wykorzystujących znane luki w zabezpieczeniach. Często te luki w zabezpieczeniach są atakami polegającymi na iniekcji SQL i atakami na skrypty między lokacjami. Zapobieganie atakom w kodzie aplikacji może być trudne. Może to wymagać rygorystycznej konserwacji, poprawek i monitorowania w wielu warstwach topologii aplikacji. Scentralizowany WAF pomaga uprościć zarządzanie zabezpieczeniami. Rozwiązanie WAF może także reagować na zagrożenia bezpieczeństwa przez zastosowanie poprawki znanej luki w centralnej lokalizacji, a następnie zabezpieczenie każdej indywidualnej aplikacji sieci Web.

[Usługa Azure Application Gateway WAF](../../web-application-firewall/ag/ag-overview.md) zapewnia scentralizowaną ochronę aplikacji sieci Web przed typowymi atakami i lukami w zabezpieczeniach. WAF opiera się na regułach z [OWASP podstawowych zestawów reguł](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,0 lub 2.2.9.

### <a name="create-an-incident-response-plan"></a>Tworzenie planu reagowania na zdarzenia

Przygotowanie planu reagowania na zdarzenia jest kluczowe, aby pomóc w rozwiązywaniu nowych zagrożeń, które mogą się pojawić w czasie. Przygotowanie planu reagowania na incydenty obejmuje zidentyfikowanie odpowiednich kontaktów awaryjnych zabezpieczeń i ustanowienie planów obsługi zabezpieczeń dla kodu dziedziczonego z innych grup w organizacji oraz do licencjonowania kodu innej firmy.

### <a name="conduct-a-final-security-review"></a>Przeprowadzenie końcowego przeglądu zabezpieczeń

Świadome przejrzenie wszystkich wykonanych działań związanych z bezpieczeństwem pomaga zapewnić gotowość do wydania oprogramowania lub aplikacji. Ostateczny Przegląd zabezpieczeń (FRS) zazwyczaj obejmuje badanie modeli zagrożeń, danych wyjściowych narzędzi i wydajności przed bramami jakości i paskami błędów, które zostały zdefiniowane w fazie wymagań.

### <a name="certify-release-and-archive"></a>Certyfikowanie wersji i archiwum

Certyfikowanie oprogramowania przed wydaniem pomaga upewnić się, że spełnione są wymagania dotyczące zabezpieczeń i ochrony prywatności. Archiwizowanie wszystkich odpowiednich danych jest niezbędne do wykonywania zadań obsługi po wydaniu. Archiwizowanie pomaga również w obniżeniu długoterminowych kosztów związanych z ciągłym inżynierią oprogramowania.

## <a name="response"></a>Reakcja
Centra fazy końcowej odpowiedzi są dostępne w zespole programistycznym, które są w stanie reagować na odpowiednie raporty dotyczące nowych zagrożeń oprogramowania i luk w zabezpieczeniach.

### <a name="execute-the-incident-response-plan"></a>Wykonaj plan reagowania na zdarzenia

Możliwość wdrażania planu reagowania na incydenty w fazie wydania ma kluczowe znaczenie dla ochrony klientów przed zagrożeniami dotyczącymi zabezpieczeń oprogramowania lub ochrony prywatności.

### <a name="monitor-application-performance"></a>Monitorowanie wydajności aplikacji

Ciągłe monitorowanie aplikacji po jej wdrożeniu może pomóc w wykrywaniu problemów z wydajnością oraz lukami w zabezpieczeniach.
Usługi platformy Azure, które pomagają w monitorowaniu aplikacji, to:

  - Azure Application Insights
  - Azure Security Center

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) to rozszerzalna usługa zarządzania wydajnością aplikacji (APM) dla deweloperów sieci Web na wielu platformach. Użyj tej usługi do monitorowania aplikacji internetowej na żywo. Application Insights automatycznie wykrywa anomalie wydajności. Zawiera ona zaawansowane narzędzia analityczne ułatwiające diagnozowanie problemów i zrozumienie, jakie użytkownicy faktycznie robią z Twoją aplikacją. Usługa ta pomaga w ciągłym udoskonalaniu wydajności i użyteczności tworzonych rozwiązań.

#### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../../security-center/security-center-introduction.md) pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie dzięki zwiększonej widoczności (i kontrolowania) zabezpieczeń zasobów platformy Azure, w tym aplikacji sieci Web. Azure Security Center pomaga wykrywać zagrożenia, które w przeciwnym razie mogą być niezauważalne. Współpracuje z różnymi rozwiązaniami dotyczącymi zabezpieczeń.

Bezpłatna warstwa Security Center oferuje ograniczone zabezpieczenia tylko dla zasobów platformy Azure. [Warstwa standardowa Security Center](../../security-center/security-center-get-started.md) rozszerza te możliwości do zasobów lokalnych i innych chmur.
Security Center Standard pomaga:

  - Znajdowanie i rozwiązywanie luk w zabezpieczeniach.
  - Stosowanie kontroli dostępu i aplikacji w celu blokowania złośliwych działań.
  - Wykrywaj zagrożenia przy użyciu funkcji analizy i analizy.
  - Szybko reaguj na ataki.

## <a name="next-steps"></a>Następne kroki
W poniższych artykułach zalecamy mechanizmy kontroli zabezpieczeń i działania, które ułatwiają projektowanie i opracowywanie bezpiecznych aplikacji.

- [Projektowanie bezpiecznych aplikacji](secure-design.md)
- [Tworzenie bezpiecznych aplikacji](secure-develop.md)