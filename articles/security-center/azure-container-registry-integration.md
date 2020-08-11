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
ms.openlocfilehash: 718f9a29b70dab34269c959ccd62452e56a32d72
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056605"
---
# <a name="azure-container-registry-image-scanning-by-security-center"></a>Azure Container Registry skanowania obrazów przez Security Center

Azure Container Registry (ACR) to zarządzana usługa rejestru prywatnego platformy Docker, która przechowuje obrazy kontenerów dla wdrożeń platformy Azure i zarządza nimi w rejestrze centralnym. Jest ona oparta na rejestrze platformy Docker open source 2,0.

Jeśli jesteś w warstwie Standardowa Azure Security Center, możesz dodać pakiet rejestrów kontenerów. Ta opcjonalna funkcja zapewnia dokładniejszy wgląd w luki w zabezpieczeniach obrazów w rejestrach opartych na Azure Resource Manager. Włącz lub Wyłącz pakiet na poziomie subskrypcji, aby uwzględnić wszystkie rejestry w subskrypcji. Ta funkcja jest naliczana za obraz, jak pokazano na [stronie z cennikiem](security-center-pricing.md). Włączenie pakietu rejestrów kontenerów zapewnia, że Security Center jest gotowy do skanowania obrazów, które są przekazywane do rejestru. 

## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólna dostępność|
|Wpisaną|Warstwa Standardowa|
|Obsługiwane rejestry i obrazy:|![Tak ](./media/icons/yes-icon.png) hostowane w systemie Linux rejestry ACR, które są dostępne z publicznego Internetu i zapewniają dostęp do powłoki.<br>![Brak ](./media/icons/no-icon.png) rejestrów ACR hostowanych przez system Windows.<br>![Brak ](./media/icons/no-icon.png) rejestrów prywatnych — Security Center wymaga dostępu do Twoich rejestrów z publicznego Internetu. Security Center nie może obecnie nawiązać połączenia z usługą, ani skanować rejestrów z dostępem ograniczonym za pomocą zapory, punktu końcowego usługi lub prywatnych punktów końcowych, takich jak link prywatny platformy Azure.<br>![Nie ](./media/icons/no-icon.png) są to obrazy minimalistyczny, takie jak obrazy wyłuskane [platformy Docker](https://hub.docker.com/_/scratch/) ani obrazy "Distroless", które zawierają tylko aplikacje i ich zależności środowiska uruchomieniowego bez Menedżera pakietów, powłoki lub systemu operacyjnego.|
|Wymagane role i uprawnienia:|Rola **czytelnik zabezpieczeń** i [Azure Container Registry czytelnik](https://docs.microsoft.com/azure/container-registry/container-registry-roles)|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Nie](./media/icons/no-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)|
|||




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
