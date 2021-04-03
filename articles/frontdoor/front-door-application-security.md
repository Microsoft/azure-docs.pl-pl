---
title: Azure front-drzwi — zabezpieczenia warstwy aplikacji | Microsoft Docs
description: Ten artykuł pomaga zrozumieć, w jaki sposób usługa Azure Front Drzwiczks umożliwia ochronę i zabezpieczanie zastrzeżonych aplikacji
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 4ee50b4c7da27df3630c1b4d263f076da44189bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89399943"
---
# <a name="application-layer-security-with-front-door"></a>Zabezpieczenia warstwy aplikacji z przednimi drzwiami
Drzwi frontonu platformy Azure udostępniają funkcje ochrony aplikacji sieci Web, które chronią aplikacje sieci Web przed atakami sieciowymi i typowymi lukami w zabezpieczeniach sieci Web, takimi jak iniekcja SQL lub obsługa skryptów między lokacjami Włączone dla frontonów http (s), zabezpieczenia warstwy aplikacji dla drzwi przednich są globalnie dystrybuowane i zawsze włączone, zatrzymując złośliwe ataki na granicy sieci platformy Azure, daleko od Twoich założeń. Po dodaniu optymalizacji zabezpieczeń i wydajności, przód drzwi zapewnia szybkie i bezpieczne środowisko sieci Web dla użytkowników końcowych.

## <a name="application-protection"></a>Ochrona aplikacji
Ochrona aplikacji dla drzwi przednich jest konfigurowana w każdym środowisku brzegowym na całym świecie, w linii z aplikacjami, i automatycznie blokuje ruch inny niż http (s), który dociera do aplikacji sieci Web. Nasza Architektura rozproszona z wieloma dzierżawcami umożliwia globalną ochronę na dużą skalę bez obniżania wydajności. W przypadku obciążeń http (s) usługa Ochrona aplikacji sieci Web na początku drzwi udostępnia rozbudowany aparat reguł dla reguł niestandardowych, wstępnie skonfigurowany zestaw reguł przed typowymi atakami i szczegółowe informacje o rejestrowaniu wszystkich żądań zgodnych z regułą. Obsługiwane są elastyczne akcje, takie jak Zezwalanie, blokowanie i rejestrowanie.

## <a name="custom-access-control-rules"></a>Niestandardowe reguły kontroli dostępu
- **Lista dozwolonych adresów IP i lista zablokowanych:** Można skonfigurować reguły niestandardowe, aby kontrolować dostęp do aplikacji sieci Web na podstawie listy adresów IP klientów. Obsługiwane są zarówno protokół IP v4, jak i protokół IPv6 V6
- **Kontrola dostępu oparta na geograficznym:** Można skonfigurować reguły niestandardowe, aby kontrolować dostęp do aplikacji sieci Web w oparciu o kod kraju, z adresu IP klienta
- **Filtrowanie parametrów http:** Można skonfigurować niestandardowe reguły dostępu na podstawie pasujących parametrów żądania HTTP (s), w tym nagłówków, adresów URL i ciągów zapytań

## <a name="azure-managed-rules"></a>Reguły zarządzane przez platformę Azure
- Wstępnie skonfigurowany zestaw reguł dla typowych najważniejszych luk w zabezpieczeniach OWASP jest domyślnie włączony. W wersji zapoznawczej zestaw reguł obejmuje sprawdzanie SQLi i żądań XSS. Zostaną dodane dodatkowe reguły. Aby sprawdzić, czy wstępnie skonfigurowane reguły działają zgodnie z oczekiwaniami dla aplikacji, możesz zacząć od akcji tylko do rejestrowania. 

## <a name="rate-limiting"></a>Rate limiting (Ograniczanie szybkości)
- Reguła kontroli częstotliwości polega na ograniczeniu nietypowego natężenia ruchu z dowolnego adresu IP klienta.  Można ustawić wartość progową liczby żądań sieci Web dozwolonych przez adres IP klienta w czasie trwania jednej minuty.

## <a name="centralized-protection-policy"></a>Zasady centralnej ochrony
- Można zdefiniować kilka reguł ochrony i dodać je do zasad w kolejności priorytetu. Reguły niestandardowe mają wyższy priorytet niż zarządzany zestaw reguł, aby zezwalać na wyjątki. Pojedyncze zasady są skojarzone z aplikacją sieci Web.  Te same zasady ochrony aplikacji sieci Web są replikowane do wszystkich serwerów brzegowych we wszystkich lokalizacjach, zapewniając spójne zasady zabezpieczeń we wszystkich regionach.

## <a name="configuration"></a>Konfigurowanie
- W trakcie korzystania z wersji zapoznawczej można używać interfejsów API REST, programu PowerShell lub interfejsu wiersza polecenia do tworzenia i wdrażania zasad ochrony aplikacji i zasad dotyczących drzwi przednich. Dostęp do portalu będzie obsługiwany, zanim usługa będzie ogólnie dostępna. 


## <a name="monitoring"></a>Monitorowanie
Drzwi z przodu zapewniają możliwość monitorowania aplikacji sieci Web pod kątem ataków przy użyciu metryk w czasie rzeczywistym, które są zintegrowane z Azure Monitor do śledzenia alertów i łatwego monitorowania trendów.

## <a name="pricing"></a>Ceny
Zabezpieczenia warstwy aplikacji dla drzwi przednich są bezpłatne w wersji zapoznawczej.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
