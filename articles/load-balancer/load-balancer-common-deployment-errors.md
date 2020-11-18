---
title: Rozwiązywanie typowych błędów związanych z wdrażaniem
titleSuffix: Azure Load Balancer
description: Opisuje sposób rozwiązywania typowych błędów podczas wdrażania modułów równoważenia obciążenia platformy Azure
services: load-balancer
documentationcenter: na
tags: top-support-issue
author: anavinahar
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.author: anavin
ms.openlocfilehash: ae3899a61e942695fed1e3da5fc543b298a42e24
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695558"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-load-balancer"></a>Rozwiązywanie typowych błędów wdrażania platformy Azure za pomocą Azure Load Balancer

W tym artykule opisano niektóre typowe błędy wdrażania Azure Load Balancer i przedstawiono informacje umożliwiające rozwiązanie tych błędów. Jeśli szukasz informacji o kodzie błędu i te informacje nie zostały podane w tym artykule, powiadom nas o tym. W dolnej części tej strony możesz opuścić opinię. Opinie są śledzone za pomocą usługi GitHub.

## <a name="error-codes"></a>Kody błędów

| Kod błędu | Szczegóły i środki zaradcze |
| ------- | ---------- |
|DifferentSkuLoadBalancersAndPublicIPAddressNotAllowed| Jednostka SKU publicznego adresu IP i jednostka SKU Load Balancer muszą być zgodne. Upewnij się, że Azure Load Balancer i jednostki SKU publicznego adresu IP są zgodne. Standardowa jednostka SKU jest zalecana w przypadku obciążeń produkcyjnych. Dowiedz się więcej o [różnicach w](./skus.md) jednostkach SKU  |
|DifferentSkuLoadBalancerAndPublicIPAddressNotAllowedInVMSS | Usługa Virtual Machine Scale Sets domyślnie ma podstawowe usługi równoważenia obciążenia, gdy jednostka SKU nie została określona lub wdrożona bez standardowych publicznych adresów IP. Wdróż ponownie zestaw skalowania maszyn wirtualnych z lokalnymi publicznymi adresami IP w poszczególnych wystąpieniach, aby upewnić się, że usługa Load Balancer w warstwie Standardowa jest zaznaczone, lub po prostu wybierz Standard LB podczas wdrażania zestawu skalowania maszyn wirtualnych z Azure Portal. |
|MaxAvailabilitySetsInLoadBalancerReached | Pula zaplecza Load Balancer może zawierać maksymalnie 150 zestawów dostępności. Jeśli nie masz jawnie zdefiniowanych zestawów dostępności dla maszyn wirtualnych w puli zaplecza, każda pojedyncza maszyna wirtualna przejdzie w swój własny zestaw dostępności. Dlatego wdrożenie autonomicznych maszyn wirtualnych 150 będzie oznaczać, że mają one zestaw dostępności 150, co spowoduje przekroczenie limitu. W ramach obejścia można wdrożyć zestaw dostępności i dodać do niego dodatkowe maszyny wirtualne. |
|NetworkInterfaceAndLoadBalancerAreInDifferentAvailabilitySets | W przypadku usługi równoważenia obciążenia w warstwie Podstawowa SKU interfejs sieciowy i moduł równoważenia obciążenia muszą znajdować się w tym samym zestawie dostępności. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndIPConfig| Nie można mieć więcej niż jednej reguły dla danego typu usługi równoważenia obciążenia (wewnętrzny, publiczny) z tym samym portem zaplecza i protokołem, do którego odwołuje się ten sam zestaw skalowania maszyn wirtualnych. Zaktualizuj regułę, aby zmienić to duplikowanie tworzenia reguł. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndVmssIPConfig| Nie można mieć więcej niż jednej reguły dla danego typu usługi równoważenia obciążenia (wewnętrzny, publiczny) z tym samym portem zaplecza i protokołem, do którego odwołuje się ten sam zestaw skalowania maszyn wirtualnych. Zaktualizuj parametry reguły, aby zmienić to duplikowanie tworzenia reguł. |
|AnotherInternalLoadBalancerExists| Można mieć tylko jeden Load Balancer typu odwołanie wewnętrzne tego samego zestawu maszyn wirtualnych/interfejsów sieciowych w zapleczu Load Balancer. Zaktualizuj wdrożenie, aby upewnić się, że tworzysz tylko jeden Load Balancer tego samego typu. |
|CannotUseInactiveHealthProbe| Nie można mieć sondy, która nie jest używana przez żadną regułę skonfigurowaną dla kondycji zestawu skalowania maszyn wirtualnych. Upewnij się, że wybrana sonda jest aktywnie używana. |
|VMScaleSetCannotUseMultipleLoadBalancersOfSameType| Nie można mieć wielu modułów równoważenia obciążenia tego samego typu (wewnętrzny, publiczny). Można korzystać z maksymalnie jednej wewnętrznej i jednej Load Balancer publicznej. |
|VMScaleSetCannotReferenceLoadbalancerWhenLargeScaleOrCrossAZ | Podstawowa Load Balancer nie jest obsługiwana w przypadku grup z wieloma położeniami dla zestawów skalowania maszyn wirtualnych lub wielodostępnego zestawu skalowania maszyn wirtualnych. Zamiast tego użyj usługa Load Balancer w warstwie Standardowa. |
|MarketplacePurchaseEligibilityFailed | Przełącz się na poprawne konto administracyjne, aby włączyć zakupy ze względu na subskrypcję umowy EA. Więcej informacji znajduje się [tutaj](../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase). |
|ResourceDeploymentFailure| Jeśli moduł równoważenia obciążenia jest w stanie niepowodzenia, wykonaj następujące kroki, aby przywrócić go z stanu niepowodzenia:<ol><li>Przejdź do https://resources.azure.com i zaloguj się przy użyciu poświadczeń Azure Portal.</li><li>Wybierz pozycję **Odczyt/zapis**.</li><li>Po lewej stronie rozwiń pozycję **subskrypcje**, a następnie rozwiń subskrypcję z Load Balancer, aby je zaktualizować.</li><li>Rozwiń węzeł **ResourceGroups**, a następnie rozwiń grupę zasobów z Load Balancer, aby ją zaktualizować.</li><li>Wybierz pozycję **Microsoft. Network**  >  **LoadBalancers**, a następnie wybierz Load Balancer do zaktualizowania, **LoadBalancer_1**.</li><li>Na stronie wyświetlanie **LoadBalancer_1** wybierz pozycję **Pobierz**  >  **edycję**.</li><li>Aktualizacja wartości **ProvisioningState** z **nie** **powiodła się.**</li><li>Wybierz pozycję **PUT**.</li></ol>|
|  |  |

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [tabelą porównania Azure Load Balancer jednostki SKU](./skus.md)
* Dowiedz się więcej na temat [limitów Azure Load Balancer](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer)