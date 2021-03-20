---
title: Bezpieczne rozwiązania deweloperskie na Microsoft Azure
description: Najlepsze rozwiązania ułatwiające tworzenie bezpieczniejszego kodu i wdrażanie bezpieczniejszej aplikacji w chmurze.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: baaa311620f5c10948aa3494002ce359cc5dab28
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94517176"
---
# <a name="secure-development-best-practices-on-azure"></a>Bezpieczne programowanie najlepszych rozwiązań na platformie Azure
W tej serii artykułów przedstawiono działania i mechanizmy kontroli zabezpieczeń, które należy wziąć pod uwagę podczas opracowywania aplikacji dla chmury. Fazy Microsoft Security Development Lifecycle (SDL) i pytania dotyczące zabezpieczeń oraz pojęcia, które należy wziąć pod uwagę podczas każdej fazy cyklu życia, zostały omówione. Celem jest ułatwienie zdefiniowania działań i usług platformy Azure, których można używać w każdej fazie cyklu życia w celu projektowania, opracowywania i wdrażania bezpieczniejszej aplikacji.

Zalecenia zawarte w artykułach pochodzą z naszego doświadczenia związanego z bezpieczeństwem platformy Azure i z naszych klientów. Możesz użyć tych artykułów jako odniesienia do tego, co należy wziąć pod uwagę podczas konkretnej fazy projektu deweloperskiego, ale sugerujemy również zapoznanie się ze wszystkimi artykułami od początku do końca. W przypadku odczytywania wszystkich artykułów wprowadzono koncepcje, które mogły zostać pominięte we wcześniejszych fazach projektu. Wdrożenie tych koncepcji przed udostępnieniem produktu może pomóc w tworzeniu bezpiecznego oprogramowania, wymaganiach dotyczących zgodności z zabezpieczeniami i zmniejszeniu kosztów rozwoju.

Te artykuły są przeznaczone dla projektantów oprogramowania, deweloperów i testerów na wszystkich poziomach, którzy tworzą i wdrażają bezpieczne aplikacje platformy Azure.

## <a name="overview"></a>Omówienie

Bezpieczeństwo jest jednym z najważniejszych aspektów dowolnych aplikacji i nie jest prostą kwestią, aby uzyskać prawo. Na szczęście platforma Azure oferuje wiele usług, które mogą pomóc w zabezpieczeniu aplikacji w chmurze. Te artykuły zawierają adresy i usługi platformy Azure, które można wdrożyć na każdym etapie cyklu projektowania oprogramowania, aby ułatwić tworzenie bezpieczniejszego kodu i wdrażanie bezpieczniejszej aplikacji w chmurze.

## <a name="security-development-lifecycle"></a>Cykl projektowania zabezpieczeń

Zgodnie z najlepszymi rozwiązaniami dotyczącymi bezpiecznego opracowywania oprogramowania należy zintegrować zabezpieczenia w każdej fazie cyklu tworzenia oprogramowania, od analizy wymagań do konserwacji, niezależnie od metodologii projektu ([kaskadowego](https://en.wikipedia.org/wiki/Waterfall_model), [Agile](https://en.wikipedia.org/wiki/Agile_software_development)lub [DevOps](https://en.wikipedia.org/wiki/DevOps)). W przypadku wzbudzania naruszeń danych wysokiego profilu oraz wykorzystania luk w zabezpieczeniach operacyjnych więcej deweloperów ma świadomość, że zabezpieczenia muszą być rozkierowane w całym procesie tworzenia oprogramowania.

Im później nawiążesz problem z cyklem rozwoju, tym większa opłata zostanie naprawiona. Problemy z zabezpieczeniami nie są wyjątkiem. W przypadku pominięcia problemów z zabezpieczeniami we wczesnych fazach tworzenia oprogramowania każda z poniższych faz może odziedziczyć luki w powyższej fazie. Końcowy produkt będzie zbierać wiele problemów z zabezpieczeniami i możliwość naruszenia. Tworzenie zabezpieczeń w każdej fazie cyklu projektowania pomaga w wczesnym wykorzystaniu problemów i pomaga zmniejszyć koszty tworzenia oprogramowania.

Postępuj zgodnie z etapami [cyklu projektowania zabezpieczeń firmy Microsoft (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) , aby wprowadzić działania i usługi platformy Azure, których można użyć do spełnienia praktyk bezpiecznego tworzenia oprogramowania w każdej fazie cyklu życia.

Fazy SDL to:

  - Szkolenia
  - Wymagania
  - Projekt
  - Implementacja
  - Weryfikacja
  - Release
  - Reakcja

![Cykl projektowania zabezpieczeń](./media/secure-dev-overview/01-sdl-phase.png)

W tych artykułach są grupowane fazy SDL w celu projektowania, opracowywania i wdrażania.

## <a name="engage-your-organizations-security-team"></a>Zaangażuj zespół ds. zabezpieczeń w organizacji

Organizacja może mieć formalny program zabezpieczeń aplikacji, który ułatwia wykonywanie działań związanych z bezpieczeństwem. Jeśli Twoja organizacja ma zespoły ds. zabezpieczeń i zgodności, pamiętaj o tym przed rozpoczęciem tworzenia aplikacji. Zadawaj je w każdej fazie SDL, niezależnie od tego, czy istnieją jakieś zadania, które nie zostały pominięte.

Zdajemy sobie sprawę, że wielu czytelników może nie mieć zespołu ds. zabezpieczeń lub zgodności. Te artykuły mogą pomóc w zalogowaniu się do pytań zabezpieczających i decyzji, które należy wziąć pod uwagę w każdej fazie SDL.

## <a name="resources"></a>Zasoby

Skorzystaj z następujących zasobów, aby dowiedzieć się więcej na temat tworzenia bezpiecznych aplikacji i zabezpieczania aplikacji na platformie Azure:

[Microsoft Security Development Lifecycle (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) — jest to proces tworzenia oprogramowania od firmy Microsoft, który pomaga deweloperom w tworzeniu bezpieczniejszego oprogramowania. Ułatwia ona Rozwiązywanie wymagań dotyczących zgodności z zabezpieczeniami przy jednoczesnym zmniejszeniu kosztów tworzenia oprogramowania.

[Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) — OWASP to społeczność online, która dostarcza bezpłatnie dostępne artykuły, metodologie, dokumentację, narzędzia i technologie w polu zabezpieczenia aplikacji sieci Web.

[Wypychanie z lewej strony, jak piasta](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) — szereg artykułów online, które przedstawiają różne typy działań zabezpieczeń aplikacji, które deweloperzy powinni zakończyć, aby utworzyć bardziej bezpieczny kod.

[Platforma tożsamości firmy Microsoft](../../active-directory/develop/index.yml) — platforma tożsamości firmy Microsoft to ewolucja usługi tożsamości usługi Azure AD i platformy deweloperskiej. Jest to w pełni funkcjonalna platforma, która składa się z usługi uwierzytelniania, bibliotek typu open source, rejestracji aplikacji i konfiguracji, pełnej dokumentacji dla deweloperów, przykładów kodu i innej zawartości dla deweloperów. Platforma tożsamości firmy Microsoft obsługuje protokoły standardowe branżowe, takie jak OAuth 2,0 i OpenID Connect Connect.

[Najlepsze rozwiązania w zakresie zabezpieczeń dla rozwiązań platformy Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) — zbiór najlepszych rozwiązań dotyczących zabezpieczeń do użycia podczas projektowania, wdrażania i zarządzania rozwiązaniami w chmurze przy użyciu platformy Azure. Ten dokument jest przeznaczony dla informatyków. Może to obejmować projektantów, architektów, deweloperów i testerów, którzy tworzą i wdrażają bezpieczne rozwiązania platformy Azure.

[Plany zabezpieczeń i zgodności na platformie Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) — plany zabezpieczeń i zgodności platformy Azure to zasoby, które mogą pomóc w tworzeniu i uruchamianiu aplikacji opartych na chmurze, które są zgodne z rygorystycznymi przepisami i standardami.

## <a name="next-steps"></a>Następne kroki
W poniższych artykułach zalecamy mechanizmy kontroli zabezpieczeń i działania, które mogą pomóc w projektowaniu, projektowaniu i wdrażaniu bezpiecznych aplikacji.

- [Projektowanie bezpiecznych aplikacji](secure-design.md)
- [Tworzenie bezpiecznych aplikacji](secure-develop.md)
- [Wdrażanie bezpiecznych aplikacji](secure-deploy.md)