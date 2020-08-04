---
title: Azure Security Center i Azure Container Registry
description: Dowiedz się więcej na temat skanowania rejestrów kontenerów za pomocą Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2020
ms.author: memildin
ms.openlocfilehash: b66969b26a801e6bd9aacf999c1c1ef9179ef1bd
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534672"
---
# <a name="azure-container-registry-image-scanning-by-security-center"></a>Azure Container Registry skanowania obrazów przez Security Center

Azure Container Registry (ACR) to zarządzana usługa rejestru prywatnego platformy Docker, która przechowuje obrazy kontenerów dla wdrożeń platformy Azure i zarządza nimi w rejestrze centralnym. Jest ona oparta na rejestrze platformy Docker open source 2,0.

Jeśli jesteś w warstwie Standardowa Azure Security Center, możesz dodać pakiet rejestrów kontenerów. Ta opcjonalna funkcja zapewnia dokładniejszy wgląd w luki w zabezpieczeniach obrazów w rejestrach opartych na Azure Resource Manager. Włącz lub Wyłącz pakiet na poziomie subskrypcji, aby uwzględnić wszystkie rejestry w subskrypcji. Ta funkcja jest naliczana za obraz, jak pokazano na [stronie z cennikiem](security-center-pricing.md). Włączenie pakietu rejestrów kontenerów zapewnia, że Security Center jest gotowy do skanowania obrazów, które są przekazywane do rejestru. 

## <a name="availability"></a>Dostępność

- Stan wydania: **Ogólna dostępność**
- Wymagane role: **czytelnik zabezpieczeń** i [rola czytnika Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-roles)
- Obsługiwane rejestry i obrazy:
    - ✔ Hostowane w systemie Linux rejestry ACR, które są dostępne z publicznego Internetu i zapewniają dostęp do powłoki.
    - ✘ Rejestry ACR hostowane w systemie Windows.
    - Rejestry prywatne ✘ — Security Center wymaga, aby rejestry były dostępne z publicznego Internetu. Security Center nie może obecnie nawiązać połączenia z usługą, ani skanować rejestrów z dostępem ograniczonym za pomocą zapory, punktu końcowego usługi lub prywatnych punktów końcowych, takich jak link prywatny platformy Azure.
    - ✘ Minimalistyczny obrazy, takie jak obrazy wyłuskane [platformy Docker](https://hub.docker.com/_/scratch/) lub obrazy typu "Distroless", które zawierają tylko aplikację i jej zależności środowiska uruchomieniowego bez Menedżera pakietów, powłoki lub systemu operacyjnego.
- Połączeń 
    - ✔ Chmury komercyjne
    - ✘ W chmurze dla instytucji rządowych USA
    - ✘ W chmurze dla instytucji rządowych, inne chmury dla instytucji rządowych


## <a name="when-are-images-scanned"></a>Kiedy są skanowane obrazy?

Za każdym razem, gdy obraz jest wypychany do rejestru, Security Center automatycznie skanuje ten obraz. Aby wyzwolić skanowanie obrazu, wypchnij go do repozytorium.

Po zakończeniu skanowania (zwykle po około 2 minutach, ale może to być maksymalnie 15 minut), ustalenia są dostępne jako Security Center zalecenia:

[![Przykładowe Azure Security Center zalecenia dotyczące luk w zabezpieczeniach odnalezionych w obrazie hostowanym Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Zalety integracji

Security Center identyfikuje Azure Resource Manager oparte na usłudze ACR w ramach subskrypcji i bezproblemowo zapewnia:

* **Skanowanie w poszukiwaniu luk w zabezpieczeniach platformy Azure** w przypadku wszystkich wypychanych obrazów systemu Linux. Security Center skanuje obraz przy użyciu skanera z wiodącej w branży skanowania dostawcy, Qualys. To rozwiązanie natywne jest w sposób ciągły zintegrowane.

* **Zalecenia dotyczące zabezpieczeń** obrazów systemu Linux z znanymi lukami w zabezpieczeniach. Security Center zawiera szczegółowe informacje o każdej raportowanej luce w zabezpieczeniach i klasyfikacji ważności. Ponadto zawiera wskazówki dotyczące sposobu korygowania określonych luk w zabezpieczeniach w każdym obrazie wypychanym do rejestru.

![Ogólne omówienie Azure Security Center i Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Często zadawane pytania dotyczące skanowania obrazu Azure Container Registry

### <a name="how-does-security-center-scan-an-image"></a>Jak Security Center skanować obraz?
Obraz zostanie pobrany z rejestru. Następnie uruchamia się w izolowanej piaskownicy za pomocą skanera Qualys, który wyodrębnia listę znanych luk w zabezpieczeniach.

Security Center filtrów i klasyfikuje wyniki ze skanera. Gdy obraz jest w dobrej kondycji, Security Center oznacza go jako taki. Security Center generuje zalecenia dotyczące zabezpieczeń tylko dla obrazów, które mają problemy, które mają zostać rozwiązane. Powiadamiając tylko w przypadku problemów, Security Center zmniejsza możliwości niechcianych alertów informacyjnych.

### <a name="how-often-does-security-center-scan-my-images"></a>Jak często Security Center skanować moje obrazy?
Podczas każdego wypychania są wyzwalane skanowania obrazu.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Czy mogę uzyskać wyniki skanowania za pośrednictwem interfejsu API REST?
Tak. Wyniki są poniżej [interfejsu API REST podocen](/rest/api/securitycenter/subassessments/list/). Ponadto można użyć usługi Azure Resource Graph (ARG), interfejsu API podobnej do Kusto dla wszystkich zasobów: zapytanie może pobrać określone skanowanie.
 
### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Jakie typy rejestrów są skanowane? Jakie typy są rozliczane?
[Sekcja dostępność](#availability) zawiera listę typów rejestrów kontenerów obsługiwanych przez pakiet rejestrów kontenerów. 

Jeśli rejestry, które nie są obsługiwane, są połączone z subskrypcją platformy Azure, nie zostaną przeskanowane i nie zostaną naliczone opłaty.


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o funkcjach zabezpieczeń kontenera Security Center, zobacz:

* [Zabezpieczenia Azure Security Center i kontenera](container-security.md)

* [Integracja z usługą Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* [Ochrona maszyny wirtualnej](security-center-virtual-machine-protection.md) — opis zaleceń dotyczących Security Center
