---
title: Usługa Azure Defender dla rejestrów kontenerów — korzyści i funkcje
description: Dowiedz się więcej o zaletach i funkcjach usługi Azure Defender dla rejestrów kontenerów.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ad880b7c23c687530a79ca1123474e94c923e150
ms.sourcegitcommit: b849ecdc8aa97337299b0f09970b7810c59cd044
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96310395"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>Wprowadzenie do usługi Azure Defender dla rejestrów kontenerów

Azure Container Registry (ACR) to zarządzana usługa rejestru prywatnego platformy Docker, która przechowuje obrazy kontenerów dla wdrożeń platformy Azure i zarządza nimi w rejestrze centralnym. Jest ona oparta na rejestrze platformy Docker open source 2,0.

Aby chronić wszystkie Azure Resource Manager rejestry oparte na danych w subskrypcji, Włącz **usługę Azure Defender dla rejestrów kontenerów** na poziomie subskrypcji. Security Center następnie skanuje obrazy, które są przekazywane do rejestru, importowane do rejestru lub wszystkie obrazy pobrane w ciągu ostatnich 30 dni. Ta funkcja jest naliczana za obraz.

[!INCLUDE [Defender for container registries availability info](../../includes/security-center-availability-defender-for-container-registries.md)]

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>Jakie są korzyści z używania usługi Azure Defender na potrzeby rejestrów kontenerów?

Security Center identyfikuje oparte na Azure Resource Manager rejestry ACR w ramach subskrypcji i bezproblemowo udostępniają ocenę luk w zabezpieczeniach platformy Azure i zarządzanie nimi dla obrazów w rejestrze.

**Usługa Azure Defender dla rejestrów kontenerów** zawiera skaner luk w zabezpieczeniach służący do skanowania obrazów w rejestrach Azure Container Registry opartych na Azure Resource managerach i zapewniają lepszy wgląd w luki w zabezpieczeniach obrazów. Zintegrowany skaner jest obsługiwany przez Qualys, wiodące w branży skanowania dostawcy.

Po znalezieniu problemów — według Qualys lub Security Center — otrzymasz powiadomienie na pulpicie nawigacyjnym Security Center. W przypadku każdej luki w zabezpieczeniach Security Center zawiera zalecenia z możliwością wykonania akcji, a także klasyfikację ważności oraz wskazówki dotyczące sposobu korygowania problemu. Aby uzyskać szczegółowe informacje na temat zaleceń dotyczących Security Center kontenerów, zobacz [listę referencyjną zaleceń](recommendations-reference.md#recs-containers).

Security Center filtrów i klasyfikuje wyniki ze skanera. Gdy obraz jest w dobrej kondycji, Security Center oznacza go jako taki. Security Center generuje zalecenia dotyczące zabezpieczeń tylko dla obrazów, które mają problemy, które mają zostać rozwiązane. Security Center zawiera szczegółowe informacje o każdej raportowanej luce w zabezpieczeniach i klasyfikacji ważności. Ponadto zawiera wskazówki dotyczące sposobu korygowania konkretnych luk w zabezpieczeniach w każdym obrazie.

Powiadamiając tylko w przypadku problemów, Security Center zmniejsza możliwości niechcianych alertów informacyjnych.


> [!TIP]
> Aby dowiedzieć się więcej o funkcjach zabezpieczeń kontenera Security Center, zobacz:
>
> - [Zabezpieczenia Azure Security Center i kontenera](container-security.md)
> - [Wprowadzenie do usługi Azure Defender dla Kubernetes](defender-for-kubernetes-introduction.md)

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
Tak. Wyniki są dostępne w [interfejsie API REST ocen podrzędnych](/rest/api/securitycenter/subassessments/list/). Ponadto można użyć usługi Azure Resource Graph (ARG), interfejsu API podobnej do Kusto dla wszystkich zasobów: zapytanie może pobrać określone skanowanie.

### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Jakie typy rejestrów są skanowane? Jakie typy są rozliczane?
Listę typów rejestrów kontenerów obsługiwanych przez usługę Azure Defender dla rejestrów kontenerów można znaleźć w temacie [Availability (dostępność](defender-for-container-registries-usage.md#availability)).

W przypadku łączenia nieobsługiwanych rejestrów z subskrypcją platformy Azure nie będą one przeszukiwane i nie będą naliczane opłaty.

### <a name="can-i-customize-the-findings-from-the-vulnerability-scanner"></a>Czy mogę dostosować wyniki ze skanera luk w zabezpieczeniach?
Tak. Jeśli istnieje organizacja, która ma być ignorowana, zamiast skorygować ją, możesz ją wyłączyć. Wyłączone wyniki nie wpływają na swój Bezpieczny wynik ani nie generują niechcianych szumów.

[Dowiedz się więcej na temat tworzenia reguł w celu wyłączenia wyników z narzędzia zintegrowanej oceny luk w zabezpieczeniach](defender-for-container-registries-usage.md#disable-specific-findings-preview).

### <a name="why-is-security-center-alerting-me-to-vulnerabilities-about-an-image-that-isnt-in-my-registry"></a>Dlaczego Security Center ostrzega mnie o lukach w zabezpieczeniach dotyczących obrazu, który nie znajduje się w rejestrze?
Security Center zapewnia oceny luk w zabezpieczeniach dla każdego obrazu wypchnięte lub ściągniętego w rejestrze. Niektóre obrazy mogą ponownie używać tagów z obrazu, który został już zeskanowany. Na przykład możesz ponownie przypisać tag "Najnowsza" przy każdym dodawaniu obrazu do skrótu. W takich przypadkach obraz "stary" nadal istnieje w rejestrze i nadal może zostać pobrany przez jego skrót. Jeśli obraz zawiera wyniki dotyczące zabezpieczeń i zostanie pobrany, spowoduje to ujawnienie luk w zabezpieczeniach.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Skanowanie obrazów pod kątem luk w zabezpieczeniach](defender-for-container-registries-usage.md)