---
title: Tabela referencyjna dla wszystkich zaleceń Azure Security Center
description: Ten artykuł zawiera listę zaleceń dotyczących zabezpieczeń Azure Security Center, które pomagają w ochronie Twoich zasobów.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 01/12/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 11d4830908b4e86da12cd5e40cc26b1c1b1aecbd
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133051"
---
# <a name="security-recommendations---a-reference-guide"></a>Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny

Ten artykuł zawiera listę zaleceń, które mogą być widoczne w Azure Security Center. Zalecenia przedstawione w danym środowisku zależą od chronionych zasobów i dostosowanej konfiguracji.

Zalecenia dotyczące usługi Security Center są oparte na najlepszych rozwiązaniach. Niektóre są zgodne z **testem porównawczym zabezpieczeń platformy Azure**, czyli opracowanymi przez firmę Microsoft specyficznymi dla platformy Azure wytycznymi dotyczącymi najlepszych rozwiązań w zakresie zabezpieczeń i zgodności opartymi na popularnych strukturach zapewnienia zgodności.
[Dowiedz się więcej o teście porównawczym zabezpieczeń platformy Azure](../security/benchmarks/introduction.md).

Aby dowiedzieć się, jak odpowiedzieć na te zalecenia, zobacz temat [korygowanie zaleceń w Azure Security Center](security-center-remediate-recommendations.md).

Twój bezpieczny wynik jest oparty na liczbie Security Center zaleceń, które zostały ukończone. Aby zdecydować, które zalecenia należy rozwiązać w pierwszej kolejności, należy zapoznać się z ważnością każdego z nich i potencjalnym wpływem na bezpieczny wynik.

> [!TIP]
> Jeśli opis zalecenia brzmi "Brak powiązanych zasad", zazwyczaj jest to spowodowane tym, że zalecenie jest zależne od innego zalecenia i _jego_ zasad. Na przykład zalecenie "błędy kondycji programu Endpoint Protection należy skorygować...", zależy od zalecenia, które sprawdza, czy rozwiązanie Endpoint Protection jest _zainstalowane_ nawet ("rozwiązanie Endpoint Protection powinno być zainstalowane..."). Odpowiednie _zalecenie ma_ zasady.
> Ograniczenie zasad wyłącznie do zalecenia podstawy upraszcza zarządzanie zasadami.

## <a name="compute-recommendations"></a><a name='recs-compute'></a>Zalecenia dotyczące obliczeń

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>Zalecenia dotyczące danych

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>Zalecenia dotyczące IdentityAndAccess

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>Zalecenia dotyczące sieci

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>Przestarzałe zalecenia

|Zalecenie|Opis powiązanych zasad &|Ważność|Włączono szybką poprawkę? ([Dowiedz się więcej](security-center-remediate-recommendations.md#quick-fix-remediation))|Typ zasobu|
|----|----|----|----|----|
|**Dostęp do App Services powinien być ograniczony**|Ogranicz dostęp do App Services, zmieniając konfigurację sieci, aby odmówić ruchu przychodzącego z zakresów, które są zbyt szerokie.<br>(Powiązane zasady: [wersja zapoznawcza]: dostęp do App Services powinien być ograniczony)|Wys.|N|App Service|
|**Reguły dla aplikacji sieci Web na IaaS sieciowych grup zabezpieczeń powinny być zaostrzone**|Zawzmacniaj grupę zabezpieczeń sieci (sieciowej grupy zabezpieczeń) dla maszyn wirtualnych, na których działają aplikacje sieci Web, z regułami sieciowej grupy zabezpieczeń, które są nadmiernie ograniczające w odniesieniu do portów aplikacji sieci Web.<br>(Powiązane zasady: reguły sieciowych grup zabezpieczeń dla aplikacji sieci Web na IaaS powinny być zaostrzone)|Wys.|N|Maszyna wirtualna|
|**Należy zdefiniować zasady zabezpieczeń pod kątem zmniejszenia możliwości ataku przez usunięcie niepotrzebnych uprawnień aplikacji (wersja zapoznawcza)**|Zdefiniuj zasady zabezpieczeń pod kątem ograniczenia ataku przez usunięcie niepotrzebnych uprawnień aplikacji. Zalecane jest skonfigurowanie zasad zabezpieczeń pod warunkiem, że w celu uzyskania dostępu do zasobów, do których mogą uzyskać dostęp.<br>(Powiązane zasady: [wersja zapoznawcza]: należy zdefiniować zasady zabezpieczeń pod kątem usług Kubernetes Services)|Śred.|N|Zasoby obliczeniowe (kontenery)|
|**Zainstaluj Azure Security Center usługi IoT Security module, aby uzyskać lepszą widoczność urządzeń IoT**|Zainstaluj Azure Security Center dla usługi IoT Security module, aby uzyskać lepszą widoczność urządzeń IoT.|Niski|N|Urządzenie IoT|

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat zaleceń, zobacz następujące tematy:

- [Zalecenia dotyczące zabezpieczeń w Centrum zabezpieczeń Azure](security-center-recommendations.md)
- [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)
