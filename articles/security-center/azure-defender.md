---
title: Omówienie usługi Azure Defender i dostępnych planów
description: Dowiedz się więcej na temat planów, ochrony i alertów usługi Azure Defender. Następnie Włącz usługę Azure Defender w Twoich subskrypcjach.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 5a5b96d5a9ea6aa05da30238690b8f5fa745b3f3
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448422"
---
# <a name="introduction-to-azure-defender"></a>Wprowadzenie do usługi Azure Defender

Funkcje Azure Security Center obejmują dwa szerokie filary zabezpieczeń w chmurze:

- **Cloud Security stan Management (CSPM)**
- **Ochrona obciążenia w chmurze (CWP)**

Funkcje CSPM Security Center, takie jak bezpieczeństwo oceny, wykrywanie niezwiązanych z konfiguracją zabezpieczeń na maszynach z systemem Windows i Linux, są częścią bezpłatnego środowiska Security Center dostępnego dla wszystkich użytkowników platformy Azure. Korzystaj z tych funkcji CSPM, aby wzmocnić stan i zapewnić zgodność z przepisami.

**Usługa Azure Defender** to platforma ochrony obciążeń w chmurze (CWPP) zintegrowana w ramach Security Center na potrzeby zaawansowanej, inteligentnej, chronionej platformy Azure i obciążeń hybrydowych.

Ten pulpit nawigacyjny usługi Azure Defender znajduje się w Azure Security Center:

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Przykład pulpitu nawigacyjnego usługi Azure Defender" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Jakie typy zasobów można zabezpieczyć za pomocą usługi Azure Defender?

Usługa Azure Defender udostępnia alerty zabezpieczeń i zaawansowaną ochronę przed zagrożeniami dla maszyn wirtualnych, baz danych SQL, kontenerów, aplikacji sieci Web, sieci i innych.

Po włączeniu usługi Azure Defender w obszarze **cennika i ustawienia** Azure Security Center następujące plany usługi Defender są dostępne jednocześnie i zapewniają kompleksowe zabezpieczenia dla warstw obliczeniowych, danych i usług w środowisku:

- [Usługa Azure Defender dla serwerów](defender-for-servers-introduction.md)
- [Usługa Azure Defender dla usługi App Service](defender-for-app-service-introduction.md)
- [Usługa Azure Defender dla usługi Storage](defender-for-storage-introduction.md)
- [Azure Defender for SQL](defender-for-sql-introduction.md)
- [Usługa Azure Defender dla IoT](defender-for-iot-introduction.md)
- [Usługa Azure Defender dla platformy Kubernetes](defender-for-kubernetes-introduction.md)
- [Usługa Azure Defender dla rejestrów kontenerów](defender-for-container-registries-introduction.md)
- [Usługa Azure Defender dla usługi Key Vault](defender-for-key-vault-introduction.md)

Każdy z tych planów został opisany osobno w dokumentacji Security Center.


## <a name="hybrid-cloud-protection"></a>Ochrona chmury hybrydowej

Oprócz obrony środowiska platformy Azure można dodać możliwości usługi Azure Defender do środowiska chmury hybrydowej:

- Ochrona serwerów spoza platformy Azure
- Ochrona maszyn wirtualnych w innych chmurach (takich jak AWS i GCP)
- Ochrona urządzeń IoT

Będziesz otrzymywać dostosowane analizy zagrożeń i alerty z priorytetami zgodnie z określonym środowiskiem, dzięki czemu możesz skupić się na tym, co najważniejsze

Wdróż [usługę Azure Arc](https://azure.microsoft.com/services/azure-arc/) i Włącz usługę Azure Defender, aby zwiększyć ochronę do lokalnych i wielochmurowych maszyn wirtualnych i baz danych SQL. Usługa Azure ARC dla serwerów jest bezpłatną usługą, ale usługi, które są używane na serwerach z obsługą łuku, na przykład Azure Defender, będą obciążane opłatami za ceny za usługę.

[Dowiedz się więcej o usłudze Azure Arc](https://docs.microsoft.com/azure/azure-arc/overview).


## <a name="azure-defender-alerts"></a>Alerty usługi Azure Defender 

Gdy usługa Azure Defender wykryje zagrożenie w dowolnym obszarze środowiska, generuje alert. Te alerty opisują szczegóły zasobów, których to dotyczy, sugerowane kroki zaradcze, a w niektórych przypadkach opcja wyzwalająca aplikację logiki w odpowiedzi.

Czy alert jest generowany przez Security Center, czy odbierany przez Security Center z zintegrowanego produktu zabezpieczeń, można go wyeksportować. Aby wyeksportować alerty do usługi Azure wskaźnikowej, wszelkich SIEM innych firm lub dowolnego innego narzędzia zewnętrznego, postępuj zgodnie z instrukcjami w temacie [Eksportowanie alertów do Siem](continuous-export.md).

> [!NOTE]
> Alerty z różnych źródeł mogą mieć różne ilości czasu. Na przykład alerty wymagające analizy ruchu sieciowego mogą trwać dłużej niż alerty związane z podejrzanymi procesami uruchomionymi na maszynach wirtualnych.


## <a name="azure-defender-advanced-protection-capabilities"></a>Zaawansowane możliwości ochrony usługi Azure Defender

Usługa Azure Defender używa zaawansowanej analizy na potrzeby dostosowanych zaleceń dotyczących Twoich zasobów. 

Ochrona obejmuje Zabezpieczanie portów zarządzania na maszynach wirtualnych za pomocą dostępu just in Time i adaptacyjnych kontrolek aplikacji w celu tworzenia list dozwolonych aplikacji, które nie powinny być uruchamiane na maszynach. 

Użyj kafelków ochrony zaawansowanej na pulpicie nawigacyjnym usługi Azure Defender, aby monitorować i konfigurować każdą z tych ochrony. 

## <a name="vulnerability-assessment-and-management"></a>Ocena luk w zabezpieczeniach i zarządzanie nimi

Usługa Azure Defender obejmuje skanowanie w poszukiwaniu luk w zabezpieczeniach maszyn wirtualnych i rejestrów kontenerów bez dodatkowych kosztów. Skanery są obsługiwane przez Qualys, ale nie jest potrzebna licencja Qualys, a nawet konto Qualys — wszystko, co jest obsługiwane bezproblemowo w Security Center. 

Zapoznaj się z wynikami tych skanerów luk w zabezpieczeniach i zareaguj na nie w Security Center. Pozwala to Security Center bliżej jednego z okien szklanych dla wszystkich wysiłków związanych z bezpieczeństwem w chmurze.

Dowiedz się więcej na następujących stronach:

- [Security Center rozwiązanie do oceny luk w zabezpieczeniach dla usługi Azure Virtual Machines](deploy-vulnerability-assessment-vm.md)
- [Identyfikowanie luk w zabezpieczeniach obrazów w rejestrach kontenerów platformy Azure](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o korzyściach płynących z usługi Azure Defender. 

> [!div class="nextstepaction"]
> [Włączanie usługi Azure Defender](security-center-pricing.md)