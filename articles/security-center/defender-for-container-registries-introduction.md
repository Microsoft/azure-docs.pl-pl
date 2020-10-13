---
title: Usługa Azure Defender dla rejestrów kontenerów — korzyści i funkcje
description: Dowiedz się więcej o zaletach i funkcjach usługi Azure Defender dla rejestrów kontenerów.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 12264a79ee5428e98d6cf7d37bef6706295e68dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448381"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>Wprowadzenie do usługi Azure Defender dla rejestrów kontenerów

Azure Container Registry (ACR) to zarządzana usługa rejestru prywatnego platformy Docker, która przechowuje obrazy kontenerów dla wdrożeń platformy Azure i zarządza nimi w rejestrze centralnym. Jest ona oparta na rejestrze platformy Docker open source 2,0.

Aby chronić wszystkie Azure Resource Manager rejestry oparte na danych w subskrypcji, Włącz **usługę Azure Defender dla rejestrów kontenerów** na poziomie subskrypcji. Security Center następnie skanuje obrazy, które są przekazywane do rejestru, importowane do rejestru lub wszystkie obrazy pobrane w ciągu ostatnich 30 dni. Ta funkcja jest naliczana za obraz.

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>Jakie są korzyści z używania usługi Azure Defender na potrzeby rejestrów kontenerów?

Security Center identyfikuje oparte na Azure Resource Manager rejestry ACR w ramach subskrypcji i bezproblemowo udostępniają ocenę luk w zabezpieczeniach platformy Azure i zarządzanie nimi dla obrazów w rejestrze.

**Usługa Azure Defender dla rejestrów kontenerów** zawiera skaner luk w zabezpieczeniach służący do skanowania obrazów w rejestrach Azure Container Registry opartych na Azure Resource managerach i zapewniają lepszy wgląd w luki w zabezpieczeniach obrazów. Zintegrowany skaner jest obsługiwany przez Qualys, wiodące w branży skanowania dostawcy.

Po znalezieniu problemów — według Qualys lub Security Center — otrzymasz powiadomienie na pulpicie nawigacyjnym Security Center. W przypadku każdej luki w zabezpieczeniach Security Center zawiera zalecenia z możliwością wykonania akcji, a także klasyfikację ważności oraz wskazówki dotyczące sposobu korygowania problemu. Aby uzyskać szczegółowe informacje na temat zaleceń dotyczących Security Center kontenerów, zobacz [listę referencyjną zaleceń](recommendations-reference.md#recs-containers).

Security Center filtrów i klasyfikuje wyniki ze skanera. Gdy obraz jest w dobrej kondycji, Security Center oznacza go jako taki. Security Center generuje zalecenia dotyczące zabezpieczeń tylko dla obrazów, które mają problemy, które mają zostać rozwiązane. Security Center zawiera szczegółowe informacje o każdej raportowanej luce w zabezpieczeniach i klasyfikacji ważności. Ponadto zawiera wskazówki dotyczące sposobu korygowania konkretnych luk w zabezpieczeniach w każdym obrazie.

Powiadamiając tylko w przypadku problemów, Security Center zmniejsza możliwości niechcianych alertów informacyjnych.


## <a name="when-are-images-scanned"></a>Kiedy są skanowane obrazy?

Istnieją trzy wyzwalacze do skanowania obrazu:

- **W przypadku wypychania** — za każdym razem, gdy obraz jest wypychany do rejestru, Security Center automatycznie skanuje ten obraz. Aby wyzwolić skanowanie obrazu, wypchnij go do repozytorium.

- **Ostatnio ściągane** — ponieważ nowe luki są wykrywane codziennie, **usługa Azure Defender dla rejestrów kontenerów** również skanuje wszystkie obrazy, które zostały pobrane w ciągu ostatnich 30 dni. Nie ma dodatkowej opłaty za ponowne skanowanie; Jak wspomniano powyżej, opłaty są naliczane raz na obraz.

- **Przy imporcie** Azure Container Registry zawiera narzędzia do importowania, które umożliwiają przenoszenie obrazów do rejestru z usługi Docker Hub, Microsoft Container Registry lub innego rejestru kontenerów platformy Azure. **Usługa Azure Defender dla rejestrów kontenerów** skanuje wszystkie obsługiwane obrazy, które zaimportowano. Dowiedz się więcej w temacie [Importowanie obrazów kontenera do rejestru kontenerów](../container-registry/container-registry-import-images.md).
 
Skanowanie kończy się zwykle w ciągu 2 minut, ale może potrwać do 15 minut. Ustalenia są udostępniane jako Security Center zalecenia, takie jak:

[![Przykładowe Azure Security Center zalecenia dotyczące luk w zabezpieczeniach odnalezionych w obrazie hostowanym Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-security-center-work-with-azure-container-registry"></a>Jak działa Security Center z Azure Container Registry

Poniżej znajduje się ogólny diagram składników i korzyści z ochrony rejestrów przy użyciu Security Center.

![Ogólne omówienie Azure Security Center i Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Często zadawane pytania dotyczące skanowania obrazu Azure Container Registry

### <a name="how-does-security-center-scan-an-image"></a>Jak Security Center skanować obraz?
Obraz zostanie pobrany z rejestru. Następnie uruchamia się w izolowanej piaskownicy za pomocą skanera Qualys, który wyodrębnia listę znanych luk w zabezpieczeniach.

Security Center filtrów i klasyfikuje wyniki ze skanera. Gdy obraz jest w dobrej kondycji, Security Center oznacza go jako taki. Security Center generuje zalecenia dotyczące zabezpieczeń tylko dla obrazów, które mają problemy, które mają zostać rozwiązane. Powiadamiając tylko w przypadku problemów, Security Center zmniejsza możliwości niechcianych alertów informacyjnych.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Czy mogę uzyskać wyniki skanowania za pośrednictwem interfejsu API REST?
Tak. Wyniki są poniżej [interfejsu API REST podocen](/rest/api/securitycenter/subassessments/list/). Ponadto można użyć usługi Azure Resource Graph (ARG), interfejsu API podobnej do Kusto dla wszystkich zasobów: zapytanie może pobrać określone skanowanie.
 
### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Jakie typy rejestrów są skanowane? Jakie typy są rozliczane?
Listę typów rejestrów kontenerów obsługiwanych przez usługę Azure Defender dla rejestrów kontenerów można znaleźć w temacie [Availability (dostępność](defender-for-container-registries-usage.md#availability)).

W przypadku łączenia nieobsługiwanych rejestrów z subskrypcją platformy Azure nie będą one przeszukiwane i nie będą naliczane opłaty.


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o funkcjach zabezpieczeń kontenera Security Center, zobacz:

- [Zabezpieczenia Azure Security Center i kontenera](container-security.md)

- [Wprowadzenie do usługi Azure Defender dla Kubernetes](defender-for-kubernetes-introduction.md)


