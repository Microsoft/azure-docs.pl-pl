---
title: Zamienianie lub przełączanie wdrożeń w Azure Cloud Services (rozszerzona pomoc techniczna)
description: Dowiedz się, jak przełączać się między wdrożeniami w Azure Cloud Services (rozszerzona pomoc techniczna).
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: f5e01075ffb460c7ddd70b40a6b19f7ea70dd776
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748836"
---
# <a name="swap-or-switch-deployments-in-azure-cloud-services-extended-support"></a>Zamienianie lub przełączanie wdrożeń w Azure Cloud Services (rozszerzona pomoc techniczna)

Możesz wymieniać się dwoma niezależnymi wdrożeniami usług w chmurze w Azure Cloud Services (rozszerzona pomoc techniczna). W przeciwieństwie Azure Cloud Services (klasycznej) model Azure Resource Manager w Azure Cloud Services (rozszerzona obsługa) nie używa miejsc wdrożenia. W Azure Cloud Services (rozszerzona pomoc techniczna) podczas wdrażania nowej wersji usługi w chmurze można "zamienić" usługę w chmurze z istniejącą usługą w chmurze w usłudze Azure Cloud Services (rozszerzona pomoc techniczna).

Po zamianie wdrożeń możesz schować i przetestować nowe wydanie przy użyciu nowego wdrożenia usługi w chmurze. W efekcie zamiana podniesie nową usługę w chmurze, która jest przesłoowana do wersji produkcyjnej.

> [!NOTE]
> Nie można zamienić wdrożenia Azure Cloud Services (klasycznego) i wdrożenia Azure Cloud Services (rozszerzonej pomocy technicznej).

Podczas wdrażania drugiej z par usług w chmurze należy dokonać wymiany usługi w chmurze z inną usługą w chmurze.

Wdrożenia można zamienić przy użyciu szablonu Azure Resource Manager ARM, Azure Portal lub interfejsu API REST.

## <a name="arm-template"></a>Szablon ARM

Jeśli używasz metody wdrażania szablonu usługi ARM, aby można było zamienić usługi w chmurze, ustaw właściwość w obiekcie na identyfikator sparowanych `SwappableCloudService` `networkProfile` usług w `cloudServices` chmurze:

```json
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
        }
```

## <a name="azure-portal"></a>Azure Portal

Aby zamienić wdrożenie w Azure Portal:

1. W menu portalu wybierz pozycję Cloud Services **(rozszerzona pomoc techniczna) lub** Pulpit **nawigacyjny.**
1. Wybierz usługę w chmurze, którą chcesz zaktualizować.
1. W **przeglądzie** usługi w chmurze wybierz pozycję **Zamień:**

   :::image type="content" source="media/swap-cloud-service-portal-swap.png" alt-text="Zrzut ekranu przedstawiający kartę zamiany dla usługi w chmurze.":::

1. W okienku potwierdzenia zamiany sprawdź informacje o wdrożeniu, a następnie wybierz przycisk **OK,** aby zamienić wdrożenia:

   :::image type="content" source="media/swap-cloud-service-portal-confirm.png" alt-text="Zrzut ekranu przedstawiający potwierdzanie informacji o zamianie wdrożenia.":::

Wdrożenia są szybko wymieniane, ponieważ jedyną zmianą jest wirtualny adres IP wdrożonej usługi w chmurze.

Aby zaoszczędzić koszty obliczeń, możesz usunąć jedną z usług w chmurze (wyznaczoną jako środowisko przejściowe dla wdrożenia aplikacji) po sprawdzeniu, czy zamieniona usługa w chmurze działa zgodnie z oczekiwaniami.

## <a name="rest-api"></a>Interfejs API REST

Aby użyć interfejsu API REST do zamiany na nowe wdrożenie usług w chmurze w usłudze Azure Cloud Services (rozszerzona obsługa), użyj następującego polecenia i konfiguracji JSON:

```http
POST https://management.azure.com/subscriptions/subId/providers/Microsoft.Network/locations/region/setLoadBalancerFrontendPublicIpAddresses?api-version=2020-11-01
```

```json
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
}
```

## <a name="common-questions-about-swapping-deployments"></a>Typowe pytania dotyczące zamiany wdrożeń

Zapoznaj się z tymi odpowiedziami na często zadawane pytania dotyczące zamiany wdrożeń w Azure Cloud Services (rozszerzona pomoc techniczna).

### <a name="what-are-the-prerequisites-for-swapping-to-a-new-cloud-services-deployment"></a>Jakie są wymagania wstępne dotyczące zamiany na nowe wdrożenie usług w chmurze?

Należy spełnić dwa kluczowe wymagania wstępne dotyczące pomyślnej wymiany wdrożenia w programie Azure Cloud Services (rozszerzona pomoc techniczna):

* Jeśli chcesz użyć statycznego lub zastrzeżonego adresu IP dla jednej z zamienialnych usług w chmurze, druga usługa w chmurze musi również używać zastrzeżonego adresu IP. W przeciwnym razie zamiana zakończy się niepowodzeniem.
* Aby zamiana zakończyła się powodzeniem, wszystkie wystąpienia ról muszą być uruchomione. Aby sprawdzić stan wystąpień, na stronie Azure Portal przejdź  do tematu Omówienie nowo wdrożonej usługi w chmurze lub użyj polecenia w `Get-AzRole` Windows PowerShell.

Aktualizacje systemu operacyjnego gościa i operacje korygowania usługi mogą spowodować niepowodzenie zamiany wdrożenia. Aby uzyskać więcej informacji, zobacz [Troubleshoot cloud service deployments (Rozwiązywanie problemów z wdrożeniami usług w chmurze).](../cloud-services/cloud-services-troubleshoot-deployment-problems.md)

### <a name="can-i-make-a-vip-swap-in-parallel-with-another-mutating-operation"></a>Czy mogę dokonać zamiany adresów VIP równolegle z inną operacją mutowania?

Nie. Zamiana adresów VIP to zmiana tylko w sieci, która musi zakończyć się przed rozpoczęciem jakiejkolwiek innej operacji obliczeniowej w usłudze w chmurze. Rozpoczęcie operacji aktualizacji, usuwania lub skalowania automatycznego dla usługi w chmurze w czasie, gdy trwa wymiana adresów VIP lub wyzwolenie zamiany adresów VIP, gdy inna operacja obliczeniowa jest w toku, może spowodować, że usługa w chmurze będzie w stanie nieodwracalnego błędu.

### <a name="does-a-swap-incur-downtime-for-my-application-and-how-should-i-handle-it"></a>Czy wymiana powoduje przestój mojej aplikacji i jak należy ją obsłużyć?

Zamiana usług w chmurze jest zwykle szybka, ponieważ jest to tylko zmiana konfiguracji w usłudze Azure Load Balancer. W niektórych przypadkach zamiana może potrwać 10 sekund lub więcej i spowodować przejściowe błędy połączenia. Aby ograniczyć wpływ zamiany na użytkowników, rozważ zaimplementowanie logiki ponawiania prób klienta.

## <a name="next-steps"></a>Następne kroki 

* Przejrzyj [wymagania wstępne dotyczące wdrażania](deploy-prerequisite.md) Azure Cloud Services (rozszerzona pomoc techniczna).
* Przejrzyj [często zadawane pytania dotyczące Azure Cloud Services](faq.md) (rozszerzona pomoc techniczna).
* Wdrażanie usługi Azure Cloud Services (rozszerzonej pomocy technicznej) w chmurze przy użyciu jednej z tych opcji:
  * [Witryna Azure Portal](deploy-portal.md)
  * [Program PowerShell](deploy-powershell.md)
  * [Szablon usługi ARM](deploy-template.md)
  * [Visual Studio](deploy-visual-studio.md)
