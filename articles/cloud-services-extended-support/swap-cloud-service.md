---
title: Zamień/przełączaj między dwoma Cloud Servicesami platformy Azure (obsługa rozszerzona)
description: Zamień/przełączaj między dwoma Cloud Servicesami platformy Azure (obsługa rozszerzona)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: 6f96656af9afd9874cc6273a9cea9ed43e8c69cc
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294309"
---
# <a name="swapswitch-between-two-azure-cloud-services-extended-support"></a>Zamień/przełączaj między dwoma Cloud Servicesami platformy Azure (obsługa rozszerzona)
Usługi Cloud Services (obsługa rozszerzona) umożliwiają wymianę między dwoma niezależnymi wdrożeniami usługi w chmurze. W przeciwieństwie do usług w chmurze (klasycznych), pojęcie miejsc nie istnieje w modelu Azure Resource Manager. W przypadku podjęcia decyzji o wdrożeniu nowej wersji usługi w chmurze (obsługa rozszerzona) można ją zmienić z inną istniejącą usługą w chmurze (rozszerzoną pomoc techniczną), umożliwiając przemieszczenie i przetestowanie nowej wersji przy użyciu tego wdrożenia. Usługę w chmurze można "wymienić" z inną usługą w chmurze tylko w czasie wdrażania drugiej usługi w chmurze (pary). Korzystając z metody wdrażania opartego na szablonach ARM, można to zrobić, ustawiając właściwość SwappableCloudService w profilu sieci obiektu usługi w chmurze na identyfikator sparowanej usługi w chmurze. 

```
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
```
> [!Note] 
> Nie można zamienić między usługą w chmurze (klasyczną) a usługą w chmurze (obsługa rozszerzona)

Użyj opcji **Zamień** , aby przełączyć adresy URL, na podstawie których są rozwiązywane dwie usługi w chmurze, w efekcie podwyższanie poziomu nowej usługi w chmurze (przemieszczanej) do wersji produkcyjnej.
Możesz zamienić wdrożenia ze strony Cloud Services lub pulpitu nawigacyjnego.

1. W [Azure Portal](https://portal.azure.com)wybierz usługę w chmurze, którą chcesz zaktualizować. Ten krok powoduje otwarcie bloku wystąpienia usługi w chmurze.
2. W bloku wybierz pozycję **Zamień** 
    :::image type="content" source="media/swap-cloud-service-1.png" alt-text="obraz — Opcja Zamień usługa w chmurze":::
   
3. Zostanie otwarty następujący monit dotyczący potwierdzenia
   
   :::image type="content" source="media/swap-cloud-service-2.png" alt-text="Obraz przedstawia zamianę usługi w chmurze":::
   
4. Po sprawdzeniu informacji o wdrożeniu wybierz pozycję OK, aby wymienić wdrożenia.
Wymiana odbywa się szybko, ponieważ tylko te zmiany są wirtualnymi adresami IP (VIP) dla dwóch usług w chmurze.

Aby zaoszczędzić koszty obliczeń, można usunąć jedną z usług w chmurze (wyznaczonej jako środowisko tymczasowe do wdrożenia aplikacji) po sprawdzeniu, czy zamieniony usługa w chmurze działa zgodnie z oczekiwaniami.

Interfejs API REST do przeprowadzenia "wymiany" między dwoma wdrożeniami rozszerzonej obsługi usług w chmurze jest poniżej:
```http
POST https://management.azure.com/subscriptions/subId/providers/Microsoft.Network/locations/region/setLoadBalancerFrontendPublicIpAddresses?api-version=2020-11-01
```
```
{
  "frontendIPConfigurations": [
    {
    "id": "#LBFE1#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP2#"
    }
      }
    },
   {
    "id": "#LBFE2#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP1#"
     }
       }
    }
  ]
 }
```
## <a name="common-questions-about-swapping-deployments"></a>Często zadawane pytania dotyczące wymiany wdrożeń

### <a name="what-are-the-prerequisites-for-swapping-between-two-cloud-services"></a>Jakie są wymagania wstępne dotyczące wymiany między dwiema usługami w chmurze?
Istnieją dwa kluczowe wymagania wstępne dotyczące usługi w chmurze (obsługa rozszerzona):
* Jeśli chcesz użyć statycznego/zastrzeżonego adresu IP dla jednej z wymienialnych usług w chmurze, inna usługa w chmurze musi również używać zastrzeżonego adresu IP. W przeciwnym razie swap nie powiedzie się.
* Aby można było przeprowadzić zamianę, wszystkie wystąpienia ról muszą być uruchomione. Stan wystąpień można sprawdzić w bloku przegląd Azure Portal. Alternatywnie możesz użyć polecenia Get-AzRole w programie Windows PowerShell.

Aktualizacje systemu operacyjnego gościa i operacje naprawy usług mogą również spowodować niepowodzenie zamiany wdrożenia. Aby uzyskać więcej informacji, zobacz Rozwiązywanie problemów z wdrażaniem usługi w chmurze.

### <a name="can-i-perform-a-vip-swap-in-parallel-with-another-mutating-operation"></a>Czy mogę przeprowadzić wymianę VIP równolegle z inną operacją mutacji?
Nie. Wymiana adresów VIP to zmiana sieciowa, która musi zostać zakończona przed wykonaniem innej operacji obliczeniowej w usługach w chmurze. Wykonywanie operacji aktualizowania, usuwania lub automatycznego skalowania w usługach w chmurze podczas wymiany adresu VIP lub wyzwolenie zamiany adresu VIP, gdy inna operacja obliczeniowa jest w toku, może opuścić usługę w chmurze w stanie niepożądanym, z którego odzyskiwanie może być niemożliwe. 

### <a name="does-a-swap-incur-downtime-for-my-application-how-should-i-handle-it"></a>Czy Zamiana wiąże się z czasem przestoju aplikacji? Jak należy ją obsłużyć?
Zgodnie z opisem w poprzedniej sekcji wymiana usługi w chmurze jest zwykle szybka, ponieważ jest to tylko zmiana konfiguracji w module równoważenia obciążenia platformy Azure. W niektórych przypadkach może upłynąć 10 lub więcej sekund i spowodować błędy połączeń przejściowych. Aby ograniczyć wpływ klientów, należy rozważyć implementację logiki ponawiania klienta.

## <a name="next-steps"></a>Następne kroki 
- Zapoznaj się z [wymaganiami wstępnymi](deploy-prerequisite.md) dotyczącymi wdrażania Cloud Services (obsługa rozszerzona).
- Zapoznaj się z [często zadawanymi pytaniami](faq.md) dotyczącymi Cloud Services (obsługa rozszerzona).
- Wdróż usługę w chmurze (obsługę rozszerzoną) przy użyciu [Azure Portal](deploy-portal.md), [programu PowerShell](deploy-powershell.md), [szablonu](deploy-template.md) lub [programu Visual Studio](deploy-visual-studio.md).
