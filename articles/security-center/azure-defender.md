---
title: Omówienie Azure Defender i dostępnych planów
description: Dowiedz się Azure Defender o planach, zabezpieczeniach i alertach firmy. Następnie włącz Azure Defender subskrypcji w celu zabezpieczenia zaawansowanego.
author: memildin
ms.author: memildin
ms.date: 9/30/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 0bd0d9c2230b8400aa3197044f944daceb93c715
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718525"
---
# <a name="introduction-to-azure-defender"></a>Wprowadzenie do usługi Azure Defender

Azure Security Center te funkcje obejmują dwa szerokie filary zabezpieczeń w chmurze:

- **Zarządzanie poziomem zabezpieczeń w chmurze (CSPM)** — Security Center jest dostępna **bezpłatnie dla** wszystkich użytkowników platformy Azure. Bezpłatne środowisko obejmuje funkcje CSPM, takie jak ocena bezpieczeństwa, wykrywanie błędów konfiguracji zabezpieczeń na maszynach platformy Azure, spis zasobów i inne. Użyj tych funkcji CSPM, aby zwiększyć swoje możliwości chmury hybrydowej i śledzić zgodność z wbudowanymi zasadami.

- Ochrona obciążeń w chmurze **(CWP)** — zintegrowana platforma ochrony obciążeń w chmurze (CWPP) firmy **Security Center, Azure Defender , zapewnia** zaawansowaną, inteligentną ochronę platformy Azure oraz hybrydowych zasobów i obciążeń. Włączenie Azure Defender zapewnia szereg dodatkowych funkcji zabezpieczeń zgodnie z opisem na tej stronie. Oprócz wbudowanych zasad po włączeniu dowolnego planu Azure Defender można dodawać niestandardowe zasady i inicjatywy. Możesz dodać standardy prawne — takie jak NIST i Azure CIS — a także test porównawczy zabezpieczeń platformy [Azure,](https://docs.microsoft.com/security/benchmark/azure/introduction) aby uzyskać prawdziwie dostosowany widok zgodności.

Pulpit Azure Defender nawigacyjny w Security Center zapewnia widoczność i kontrolę nad funkcjami platformy CWP w twoim środowisku:

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Przykład pulpitu nawigacyjnego Azure Defender nawigacyjnym" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Jakie typy zasobów można Azure Defender zabezpieczać?

Azure Defender alerty zabezpieczeń i zaawansowaną ochronę przed zagrożeniami dla maszyn wirtualnych, baz danych SQL, kontenerów, aplikacji internetowych, sieci i innych.

Po włączeniu usługi Azure Defender  z obszaru Ceny i ustawienia usługi Azure Security Center wszystkie następujące plany usługi Defender są włączane jednocześnie i zapewniają kompleksową ochronę warstw obliczeniowych, danych i usług środowiska:

- [Usługa Azure Defender dla serwerów](defender-for-servers-introduction.md)
- [Usługa Azure Defender dla usługi App Service](defender-for-app-service-introduction.md)
- [Usługa Azure Defender dla usługi Storage](defender-for-storage-introduction.md)
- [Azure Defender for SQL](defender-for-sql-introduction.md)
- [Usługa Azure Defender dla platformy Kubernetes](defender-for-kubernetes-introduction.md)
- [Usługa Azure Defender dla rejestrów kontenerów](defender-for-container-registries-introduction.md)
- [Usługa Azure Defender dla usługi Key Vault](defender-for-key-vault-introduction.md)
- [Usługa Azure Defender dla usługi Resource Manager](defender-for-resource-manager-introduction.md)
- [Usługa Azure Defender dla usługi DNS](defender-for-dns-introduction.md)

Każdy z tych planów został szczegółowo wyjaśniony w Security Center dokumentacji.

> [!TIP]
> Azure Defender dla IoT (wersja zapoznawcza) to oddzielny produkt. Wszystkie szczegóły znajdziesz w te tematze Wprowadzenie do Azure Defender dla IoT [(wersja zapoznawcza).](../defender-for-iot/overview.md) 

## <a name="hybrid-cloud-protection"></a>Ochrona chmury hybrydowej

Możesz nie tylko chronić środowisko platformy Azure, ale także dodawać Azure Defender do środowiska chmury hybrydowej:

- Ochrona serwerów spoza platformy Azure
- Ochrona maszyn wirtualnych w innych chmurach (takich jak AWS i GCP)

Otrzymasz niestandardową analizę zagrożeń i alerty z określonymi priorytetami w zależności od środowiska, dzięki czemu możesz skupić się na tym, co ma największe znaczenie.

Aby rozszerzyć ochronę na maszyny wirtualne i bazy danych SQL, które znajdują się w innych chmurach lub lokalnie, należy [wdrożyć](https://azure.microsoft.com/services/azure-arc/) Azure Arc i włączyć Azure Defender. Azure Arc dla serwerów jest bezpłatną usługą, ale usługi używane na serwerach z usługą Arc, na przykład Azure Defender, będą naliczane zgodnie z cennikiem tej usługi. Aby dowiedzieć się [więcej, zobacz Dodawanie maszyn spoza platformy Azure za pomocą Azure Arc](quickstart-onboard-machines.md#add-non-azure-machines-with-azure-arc).

> [!TIP]
> Łącznik natywny dla usług AWS w sposób przezroczysty obsługuje Azure Arc wdrożenia. Aby dowiedzieć się [więcej, zobacz Łączenie kont usług AWS z Azure Security Center](quickstart-onboard-aws.md).



## <a name="azure-defender-security-alerts"></a>Azure Defender alerty zabezpieczeń 

Gdy Azure Defender wykryje zagrożenie w dowolnym obszarze środowiska, generuje alert zabezpieczeń. Te alerty opisują szczegóły zasobów, których dotyczy problem, sugerowane kroki korygowania, a w niektórych przypadkach opcję wyzwalania aplikacji logiki w odpowiedzi.

Niezależnie od tego, czy alert jest generowany przez Security Center, czy odbierany przez Security Center od zintegrowanego produktu zabezpieczającego, można go wyeksportować. Aby wyeksportować alerty do rozwiązania Azure Sentinel, dowolnego rozwiązania SIEM innej firmy lub dowolnego innego narzędzia zewnętrznego, postępuj zgodnie z instrukcjami w tesłudze Stream [alerts to a SIEM, SOAR,](export-to-siem.md)or IT Service Management solution (Alerty usługi Stream do rozwiązania SIEM, SOAR lub zarządzania usługami IT).

> [!NOTE]
> Alerty z różnych źródeł mogą pojawiać się w różnych ilościach czasu. Na przykład alerty wymagające analizy ruchu sieciowego mogą być wyświetlane dłużej niż alerty związane z podejrzanymi procesami uruchomionymi na maszynach wirtualnych.


## <a name="azure-defender-advanced-protection-capabilities"></a>Azure Defender zaawansowane funkcje ochrony

Azure Defender korzysta z zaawansowanej analizy w celu przygotowania dostosowanych zaleceń związanych z zasobami. 

Zabezpieczenia obejmują zabezpieczanie portów zarządzania maszyn wirtualnych za pomocą dostępu just in time i adaptacyjnych kontrolek aplikacji w celu tworzenia list zezwalania dla aplikacji, które powinny i nie powinny być uruchamiane na maszynach. 

Kafelki zaawansowanej ochrony na pulpicie nawigacyjnym Azure Defender do monitorowania i konfigurowania każdej z tych ochrony. 

## <a name="vulnerability-assessment-and-management"></a>Ocena luk w zabezpieczeniach i zarządzanie nimi

Azure Defender obejmuje skanowanie luk w zabezpieczeniach maszyn wirtualnych i rejestrów kontenerów bez dodatkowych kosztów. Skanery są obsługiwane przez firmę Qualys, ale nie potrzebujesz licencji firmy Qualys ani nawet konta Qualys — wszystko jest bezproblemowo obsługiwane w Security Center. 

Przejrzyj wyniki tych skanerów luk w zabezpieczeniach i odpowiedz na nie z poziomu Security Center. To przybliża Security Center do tego, że jest to pojedyncze okienko dla wszystkich wysiłków w zakresie zabezpieczeń w chmurze.

Dowiedz się więcej na następujących stronach:

- [Security Center zintegrowane rozwiązanie do oceny luk w zabezpieczeniach dla maszyn wirtualnych platformy Azure](deploy-vulnerability-assessment-vm.md)
- [Identyfikowanie luk w zabezpieczeniach obrazów w rejestrach kontenerów platformy Azure](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>Następne kroki

W tym artykule o wiesz już, jakie są zalety Azure Defender. 

> [!div class="nextstepaction"]
> [Włączanie usługi Azure Defender](enable-azure-defender.md)