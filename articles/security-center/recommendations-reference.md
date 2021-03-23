---
title: Tabela referencyjna dla wszystkich zaleceń Azure Security Center
description: Ten artykuł zawiera listę zaleceń dotyczących zabezpieczeń Azure Security Center, które pomagają w zabezpieczaniu i ochronie zasobów.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 03/22/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 2d9f76bd3c6ad0060a90f52abba1c7378b310e67
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801480"
---
# <a name="security-recommendations---a-reference-guide"></a>Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny

Ten artykuł zawiera listę zaleceń, które mogą być widoczne w Azure Security Center. Zalecenia przedstawione w danym środowisku zależą od chronionych zasobów i dostosowanej konfiguracji.

Zalecenia dotyczące Security Center są oparte na [teście zabezpieczeń platformy Azure](../security/benchmarks/introduction.md). Usługa Azure Security test to zestaw wytycznych dotyczących zabezpieczeń i zgodności opartych na platformie Azure, które są stosowane do najlepszych rozwiązań w zakresie bezpieczeństwa i zapewniających zgodność. Ten powszechnie przestrzegany test porównawczy jest oparty na kontrolkach z [centrum na potrzeby zabezpieczeń internetowych (CIS)](https://www.cisecurity.org/benchmark/azure/) i [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) , które koncentrują się na zabezpieczeniach skoncentrowanych na chmurze.

Aby dowiedzieć się, jak odpowiedzieć na te zalecenia, zobacz temat [korygowanie zaleceń w Azure Security Center](security-center-remediate-recommendations.md).

Twój bezpieczny wynik jest oparty na liczbie Security Center zaleceń, które zostały ukończone. Aby zdecydować, które zalecenia należy rozwiązać w pierwszej kolejności, należy zapoznać się z ważnością każdego z nich i potencjalnym wpływem na bezpieczny wynik.

> [!TIP]
> Jeśli opis zalecenia brzmi "Brak powiązanych zasad", zazwyczaj jest to spowodowane tym, że zalecenie jest zależne od innego zalecenia i _jego_ zasad. Na przykład zalecenie "błędy kondycji programu Endpoint Protection należy skorygować...", zależy od zalecenia, które sprawdza, czy rozwiązanie Endpoint Protection jest _zainstalowane_ nawet ("rozwiązanie Endpoint Protection powinno być zainstalowane..."). Odpowiednie _zalecenie ma_ zasady.
> Ograniczenie zasad wyłącznie do zalecenia podstawy upraszcza zarządzanie zasadami.

## <a name="appservices-recommendations"></a><a name='recs-appservices'></a>Zalecenia dotyczące usługi aplikacji

[!INCLUDE [asc-recs-appservices](../../includes/asc-recs-appservices.md)]

## <a name="compute-recommendations"></a><a name='recs-compute'></a>Zalecenia dotyczące obliczeń

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="container-recommendations"></a><a name='recs-container'></a>Rekomendacje dotyczące kontenerów

[!INCLUDE [asc-recs-container](../../includes/asc-recs-container.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>Zalecenia dotyczące danych

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>Zalecenia dotyczące IdentityAndAccess

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="iot-recommendations"></a><a name='recs-iot'></a>Zalecenia dotyczące IoT

[!INCLUDE [asc-recs-iot](../../includes/asc-recs-iot.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>Zalecenia dotyczące sieci

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>Przestarzałe zalecenia

|Zalecenie|Opis powiązanych zasad &|Ważność|Włączono szybką poprawkę? ([Dowiedz się więcej](security-center-remediate-recommendations.md#quick-fix-remediation))|Typ zasobu|
|----|----|----|----|----|
|**Dostęp do App Services powinien być ograniczony**|Ogranicz dostęp do App Services, zmieniając konfigurację sieci, aby odmówić ruchu przychodzącego z zakresów, które są zbyt szerokie.<br>(Powiązane zasady: [wersja zapoznawcza]: dostęp do App Services powinien być ograniczony)|Wys.|N|App Service|
|**Reguły dla aplikacji sieci Web na IaaS sieciowych grup zabezpieczeń powinny być zaostrzone**|Zwiększanie funkcjonalności sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) dla maszyn wirtualnych, na których działają aplikacje sieci Web, z regułami sieciowej grupy zabezpieczeń, które są nadmiernie ograniczające w odniesieniu do portów aplikacji sieci Web.<br>(Powiązane zasady: reguły sieciowych grup zabezpieczeń dla aplikacji sieci Web na IaaS powinny być zaostrzone)|Wys.|N|Maszyna wirtualna|
|**Należy zdefiniować zasady zabezpieczeń pod kątem zmniejszenia możliwości ataku przez usunięcie niepotrzebnych uprawnień aplikacji (wersja zapoznawcza)**|Zdefiniuj zasady zabezpieczeń pod kątem ograniczenia ataku przez usunięcie niepotrzebnych uprawnień aplikacji. Zalecane jest skonfigurowanie zasad zabezpieczeń pod warunkiem, że w celu uzyskania dostępu do zasobów, do których mogą uzyskać dostęp.<br>(Powiązane zasady: [wersja zapoznawcza]: należy zdefiniować zasady zabezpieczeń pod kątem usług Kubernetes Services)|Śred.|N|Zasoby obliczeniowe (kontenery)|
|**Zainstaluj Azure Security Center usługi IoT Security module, aby uzyskać lepszą widoczność urządzeń IoT**|Zainstaluj Azure Security Center dla usługi IoT Security module, aby uzyskać lepszą widoczność urządzeń IoT.|Niski|N|Urządzenie IoT|

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat zaleceń, zobacz następujące tematy:

- [Co to są zasady zabezpieczeń, inicjatywy i zalecenia?](security-policy-concept.md)
- [Zapoznaj się z zaleceniami dotyczącymi zabezpieczeń](security-center-recommendations.md)
