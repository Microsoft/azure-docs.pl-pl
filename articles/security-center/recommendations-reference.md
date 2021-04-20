---
title: Tabela odwoływek dla wszystkich Azure Security Center rekomendacji
description: W tym artykule Azure Security Center zalecenia dotyczące zabezpieczeń, które ułatwiają wzmacnianie i ochronę zasobów.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 04/06/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: e994aead1840fd3ef9b57e92cf95e94837608d7a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719137"
---
# <a name="security-recommendations---a-reference-guide"></a>Zalecenia dotyczące zabezpieczeń — przewodnik referencyjny

W tym artykule wymieniono zalecenia, które mogą być Azure Security Center. Rekomendacje wyświetlane w twoim środowisku zależą od zasobów, które chronisz, i niestandardowej konfiguracji.

Security Center są oparte na testach porównawczych [zabezpieczeń platformy Azure.](https://docs.microsoft.com/security/benchmark/azure/introduction)
Test porównawczy zabezpieczeń platformy Azure to oparty na wspólnych platformach zgodności, zestaw wytycznych dotyczących najlepszych rozwiązań dotyczących zabezpieczeń i zgodności, który został przez firmę Microsoft specyficzny dla platformy Azure. Ten szeroko przestrzegany test porównawczy opiera się na mechanizmach kontroli nist [(Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) i [National Institute of Standards and Technology (NIST),](https://www.nist.gov/) które koncentrują się na zabezpieczeniach skoncentrowanych na chmurze.

Aby dowiedzieć się, jak reagować na te zalecenia, zobacz Rekomendacje dotyczące [korygowania w Azure Security Center](security-center-remediate-recommendations.md).

Ocena bezpieczeństwa jest oparta na liczbie Security Center, które zostały wykonane. Aby zdecydować, które zalecenia należy najpierw rozwiązać, przyjrzyj się ważności każdego z nich i ich potencjalnemu wpływowi na wskaźnik bezpieczeństwa.

> [!TIP]
> Jeśli w opisie zalecenia znajduje się tekst "No related policy" (Brak powiązanych zasad), zwykle jest to zależne od innego zalecenia i _jego_ zasad. Na przykład zalecenie "Błędy kondycji programu Endpoint Protection powinny zostać skorygowane...", opiera się na rekomendacji, która sprawdza, czy rozwiązanie ochrony punktu końcowego jest nawet zainstalowane _("Należy_ zainstalować rozwiązanie ochrony punktu końcowego..."). Zalecenie _bazowe_ ma zasady.
> Ograniczenie zasad tylko do zalecenia foundational upraszcza zarządzanie zasadami.

## <a name="appservices-recommendations"></a><a name='recs-appservices'></a>Zalecenia dotyczące usługi AppServices

[!INCLUDE [asc-recs-appservices](../../includes/asc-recs-appservices.md)]

## <a name="compute-recommendations"></a><a name='recs-compute'></a>Zalecenia dotyczące obliczeń

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="container-recommendations"></a><a name='recs-container'></a>Rekomendacje dotyczące kontenerów

[!INCLUDE [asc-recs-container](../../includes/asc-recs-container.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>Zalecenia dotyczące danych

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>Zalecenia dotyczące funkcji IdentityAndAccess

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="iot-recommendations"></a><a name='recs-iot'></a>Zalecenia dotyczące IoT

[!INCLUDE [asc-recs-iot](../../includes/asc-recs-iot.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>Zalecenia dotyczące sieci

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>Przestarzałe zalecenia

|Zalecenie|Opis & powiązanych zasad|Ważność|
|----|----|----|
|Dostęp do App Services powinien być ograniczony|Ogranicz dostęp do App Services, zmieniając konfigurację sieci, aby uniemożliwić ruch przychodzący z zbyt szerokich zakresów.<br>(Powiązane zasady: [wersja zapoznawcza]: dostęp do App Services powinien być ograniczony)|Wys.|
|Reguły dotyczące aplikacji internetowych w sieciowych sieciach IaaS powinny być wzmocnione|Wzmacnianie sieciowej grupy zabezpieczeń (NSG) maszyn wirtualnych, na których są uruchomione aplikacje internetowe, przy użyciu reguł sieciowej grupy zabezpieczeń, które są zbyt permisywne w odniesieniu do portów aplikacji internetowych.<br>(Powiązane zasady: Reguły sieciowych grupy zabezpieczeń dla aplikacji internetowych w Ujściu IaaS powinny być wzmocnione)|Wys.|
|Należy zdefiniować zasady zabezpieczeń zasobników, aby zmniejszyć wektor ataku przez usunięcie niepotrzebnych uprawnień aplikacji (wersja zapoznawcza)|Zdefiniuj zasady zabezpieczeń zasobników, aby zmniejszyć wektor ataku przez usunięcie niepotrzebnych uprawnień aplikacji. Zaleca się skonfigurowanie zasad zabezpieczeń zasobników, aby zasobniki mogły uzyskać dostęp tylko do zasobów, do których mają dostęp.<br>(Powiązane zasady: [wersja zapoznawcza]: Zasady zabezpieczeń zasobników powinny być zdefiniowane w usługach Kubernetes)|Śred.|
|Instalowanie Azure Security Center zabezpieczeń IoT w celu uzyskania większej widoczności urządzeń IoT|Zainstaluj Azure Security Center zabezpieczeń IoT, aby uzyskać większy wgląd w urządzenia IoT.|Niski|
|Aby zastosować aktualizacje systemu, należy ponownie uruchomić maszyny|Uruchom ponownie maszyny, aby zastosować aktualizacje systemu i zabezpieczyć maszynę przed lukami w zabezpieczeniach. (Powiązane zasady: Aktualizacje systemu powinny być zainstalowane na maszynach)|Śred.|
|Na maszynach powinien być zainstalowany agent monitorowania|Ta akcja instaluje agenta monitorowania na wybranych maszynach wirtualnych. Wybierz obszar roboczy dla agenta, do który ma być zgłaszany raport. (Brak powiązanych zasad)|Wys.|
||||

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zaleceniach, zobacz następujące informacje:

- [Co to są zasady zabezpieczeń, inicjatywy i zalecenia?](security-policy-concept.md)
- [Przeglądanie zaleceń dotyczących zabezpieczeń](security-center-recommendations.md)
