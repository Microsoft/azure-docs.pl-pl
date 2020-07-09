---
title: Azure Security Center i Azure Container Registry
description: Dowiedz się więcej o integracji Azure Security Center z usługą Azure Container Registry
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: f3ef633ff0271d74eea7320faadf17685976d3b6
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970471"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Azure Container Registry integrację z usługą Security Center

Azure Container Registry (ACR) to zarządzana usługa rejestru prywatnego platformy Docker, która przechowuje obrazy kontenerów dla wdrożeń platformy Azure i zarządza nimi w rejestrze centralnym. Jest ona oparta na rejestrze platformy Docker open source 2,0.

Jeśli jesteś w warstwie Standardowa Azure Security Center, możesz dodać pakiet rejestrów kontenerów. Ta opcjonalna funkcja zapewnia dokładniejszy wgląd w luki w zabezpieczeniach obrazów w rejestrach opartych na usłudze ARM. Włącz lub Wyłącz pakiet na poziomie subskrypcji, aby uwzględnić wszystkie rejestry w subskrypcji. Ta funkcja jest naliczana za obraz, jak pokazano na [stronie z cennikiem](security-center-pricing.md). Włączenie pakietu rejestrów kontenerów zapewnia, że Security Center jest gotowy do skanowania obrazów, które są przekazywane do rejestru. 


## <a name="availability"></a>Dostępność

- Stan wydania: **Ogólna dostępność**
- Wymagane role: **czytelnik zabezpieczeń** i [rola czytnika Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-roles)
- Połączeń 
    - ✔ Chmury komercyjne
    - ✘ W chmurze dla instytucji rządowych USA
    - Chmura ✘ Chińska dla instytucji rządowych, inne chmury gov


## <a name="when-are-images-scanned"></a>Kiedy są skanowane obrazy?

Za każdym razem, gdy obraz jest wypychany do rejestru, Security Center automatycznie skanuje ten obraz. Aby wyzwolić skanowanie obrazu, wypchnij go do repozytorium.

Po zakończeniu skanowania (zwykle po około 10 minutach, ale może to być do 40 minut), ustalenia są dostępne jako Security Center zalecenia:

[![Przykładowe Azure Security Center zalecenia dotyczące luk w zabezpieczeniach odnalezionych w obrazie hostowanym Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Zalety integracji

Security Center identyfikuje rejestry ACR oparte na architekturze ARM w Twojej subskrypcji i bezproblemowo udostępnia:

* **Skanowanie w poszukiwaniu luk w zabezpieczeniach platformy Azure** w przypadku wszystkich wypychanych obrazów systemu Linux. Security Center skanuje obraz przy użyciu skanera z wiodącej w branży skanowania dostawcy, Qualys. To rozwiązanie natywne jest w sposób ciągły zintegrowane.

* **Zalecenia dotyczące zabezpieczeń** obrazów systemu Linux z znanymi lukami w zabezpieczeniach. Security Center zawiera szczegółowe informacje o każdej raportowanej luce w zabezpieczeniach i klasyfikacji ważności. Ponadto zawiera wskazówki dotyczące sposobu korygowania określonych luk w zabezpieczeniach w każdym obrazie wypychanym do rejestru.

![Ogólne omówienie Azure Security Center i Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="acr-with-security-center-faq"></a>ACR z Security Center często zadawane pytania

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Jakie typy obrazów mogą Azure Security Center skanowanie?
Security Center skanuje obrazy systemu operacyjnego Linux, które zapewniają dostęp do powłoki. 

Skaner Qualys nie obsługuje obrazów minimalistyczny, takich jak obrazy wyłuskane [platformy Docker](https://hub.docker.com/_/scratch/) lub obrazy typu "Distroless", które zawierają tylko aplikację i jej zależności środowiska uruchomieniowego bez Menedżera pakietów, powłoki lub systemu operacyjnego.

### <a name="how-does-azure-security-center-scan-an-image"></a>Jak Azure Security Center skanować obraz?
Obraz zostanie pobrany z rejestru. Następnie uruchamia się w izolowanej piaskownicy za pomocą skanera Qualys, który wyodrębnia listę znanych luk w zabezpieczeniach.

Security Center filtrów i klasyfikuje wyniki ze skanera. Gdy obraz jest w dobrej kondycji, Security Center oznacza go jako taki. Security Center generuje zalecenia dotyczące zabezpieczeń tylko dla obrazów, które mają problemy, które mają zostać rozwiązane. Powiadamiając tylko w przypadku problemów, Security Center zmniejsza możliwości niechcianych alertów informacyjnych.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Jak często Azure Security Center skanować moje obrazy?
Podczas każdego wypychania są wyzwalane skanowania obrazu.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Czy mogę uzyskać wyniki skanowania za pośrednictwem interfejsu API REST?
Tak. Wyniki są poniżej [interfejsu API REST podocen](/rest/api/securitycenter/subassessments/list/). Ponadto można użyć usługi Azure Resource Graph (ARG), interfejsu API podobnej do Kusto dla wszystkich zasobów: zapytanie może pobrać określone skanowanie.
 



## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o funkcjach zabezpieczeń kontenera Security Center, zobacz:

* [Zabezpieczenia Azure Security Center i kontenera](container-security.md)

* [Integracja z usługą Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* [Ochrona maszyny wirtualnej](security-center-virtual-machine-protection.md) — opis zaleceń dotyczących Security Center
