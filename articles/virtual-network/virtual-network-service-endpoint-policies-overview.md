---
title: Zasady punku końcowego usługi dla sieci wirtualnej platformy Azure | Microsoft Docs
description: Dowiedz się, jak filtrować ruch w sieci wirtualnej do zasobów usługi platformy Azure przy użyciu zasad punktu końcowego usługi
services: virtual-network
documentationcenter: na
author: RDhillon
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: 14ecb30af11bf750c90e45c3fb6b443d861a1445
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400759"
---
# <a name="virtual-network-service-endpoint-policies-for-azure-storage"></a>Zasady punktu końcowego usługi dla sieci wirtualnej dla usługi Azure Storage

Zasady punktu końcowego usługi Virtual Network (VNet) umożliwiają filtrowanie ruchu wychodzącego z sieci wirtualnej do kont usługi Azure Storage za pośrednictwem punktu końcowego usługi i Zezwalanie na eksfiltracji danych tylko na określone konta usługi Azure Storage. Zasady punktu końcowego zapewniają szczegółową kontrolę dostępu dla ruchu w sieci wirtualnej do usługi Azure Storage podczas nawiązywania połączenia przez punkt końcowy usługi.

![Zabezpieczanie ruchu wychodzącego z sieci wirtualnej do kont usługi Azure Storage](./media/virtual-network-service-endpoint-policies-overview/vnet-service-endpoint-policies-overview.png)

Ta funkcja jest ogólnie dostępna dla __usługi Azure Storage__ we __wszystkich globalnych regionach platformy Azure__.

## <a name="key-benefits"></a>Najważniejsze korzyści

Zasady punktu końcowego usługi dla sieci wirtualnej zapewniają następujące korzyści:

- __Ulepszone zabezpieczenia ruchu Virtual Network w usłudze Azure Storage__

  [Tagi usług platformy Azure dla sieciowych grup zabezpieczeń](https://aka.ms/servicetags) umożliwiają ograniczenie ruchu wychodzącego z sieci wirtualnej do określonych regionów usługi Azure Storage. Umożliwia to jednak ruch do dowolnego konta w wybranym regionie usługi Azure Storage.
  
  Zasady punktu końcowego umożliwiają określenie kont usługi Azure Storage, które są dozwolone dostępu wychodzącego do sieci wirtualnej, i ogranicza dostęp do wszystkich innych kont magazynu. Zapewnia to znacznie bardziej szczegółową kontrolę zabezpieczeń w zakresie ochrony danych eksfiltracji z sieci wirtualnej.

- __Skalowalne zasady o wysokiej dostępności umożliwiające filtrowanie ruchu w usługach platformy Azure__

   Zasady punktu końcowego zapewniają skalowalne w poziomie, wysoce dostępne rozwiązanie, które umożliwia filtrowanie ruchu w usługach platformy Azure z sieci wirtualnych za pośrednictwem punktów końcowych usługi. Żadne dodatkowe nakłady nie są wymagane, aby utrzymywać centralne urządzenia sieciowe do obsługi ruchu w sieciach wirtualnych.

## <a name="json-object-for-service-endpoint-policies"></a>Obiekt JSON dla zasad punktu końcowego usługi
Przyjrzyjmy się szybkiemu wyszukiwaniu obiektu zasad punktu końcowego usługi.

```json
"serviceEndpointPolicyDefinitions": [
    {
            "description": null,
            "name": "MySEP-Definition",
            "resourceGroup": "MySEPDeployment",
            "service": "Microsoft.Storage",
            "serviceResources": [ 
                    "/subscriptions/subscriptionID/resourceGroups/MySEPDeployment/providers/Microsoft.Storage/storageAccounts/mystgacc"
            ],
            "type": "Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions"
    }
]
```

## <a name="configuration"></a>Konfiguracja

-   Zasady punktu końcowego można skonfigurować w taki sposób, aby ograniczyć ruch sieciowy sieci wirtualnej do określonych kont usługi Azure Storage.
-   Zasady punktu końcowego są konfigurowane w podsieci sieci wirtualnej. Punkty końcowe usługi Azure Storage powinny być włączone w podsieci, aby zastosować zasady.
-   Zasady punktu końcowego umożliwiają dodawanie określonych kont usługi Azure Storage do listy dozwolonych przy użyciu formatu resourceID. Można ograniczyć dostęp do programu
    - wszystkie konta magazynu w ramach subskrypcji<br>
      `E.g. /subscriptions/subscriptionId`

    - wszystkie konta magazynu w grupie zasobów<br>
      `E.g. subscriptions/subscriptionId/resourceGroups/resourceGroupName`
     
    - pojedyncze konto magazynu przez wystawienie odpowiadającego mu Azure Resource Manager resourceId. Obejmuje to ruch do obiektów blob, tabel, kolejek, plików oraz usługi Azure Data Lake Storage Gen2. <br>
    `E.g. /subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/storageAccountName`
-   Domyślnie jeśli żadne zasady nie są dołączone do podsieci z punktami końcowymi, można uzyskać dostęp do wszystkich kont magazynu w usłudze. Po skonfigurowaniu zasad w tej podsieci tylko zasoby określone w zasadach będą dostępne z wystąpień obliczeniowych w tej podsieci. Nastąpi odmowa dostępu do wszystkich innych kont magazynu.
-   W przypadku stosowania zasad punktu końcowego usługi Azure Storage w ramach podsieci *zakres punktów końcowych usługi* magazynu platformy Microsoft jest uaktualniany z regionu regionalnego do **globalnego**. Oznacza to, że cały ruch do usługi Azure Storage jest następnie zabezpieczony przez punkt końcowy usługi. Zasady punktu końcowego usługi są również stosowane globalnie, dlatego nie będzie można odmówić dostępu do żadnych kont magazynu, które nie są jawnie dozwolone. 
-   Do podsieci możesz zastosować wiele zasad. Gdy wiele zasad jest skojarzonych z podsiecią, ruch sieciowy sieci wirtualnej do zasobów określonych w ramach dowolnej z tych zasad będzie dozwolony. Dostęp do wszystkich innych zasobów usługi, które nie zostały określone w żadnej z zasad, zostanie zablokowany.

    > [!NOTE]  
    > Zasady punktu końcowego usługi są **dozwolone dla zasad**, poza określonymi zasobami, a wszystkie inne zasoby są ograniczone. Upewnij się, że wszystkie zależności zasobów usługi dla aplikacji są zidentyfikowane i wymienione w zasadach.

- W zasadach punktu końcowego można określić tylko konta magazynu korzystające z modelu zasobów platformy Azure. Klasyczne konta usługi Azure Storage nie będą obsługiwały zasad punktu końcowego usługi platformy Azure.
- Pomocniczy dostęp do odczytu z magazynu geograficznie nadmiarowego zostanie automatycznie zapewniony, jeśli wskazane będzie konto podstawowe.
- Konta magazynu mogą znajdować się w tej samej lub innej subskrypcji bądź dzierżawie usługi Azure Active Directory co sieć wirtualna.

## <a name="scenarios"></a>Scenariusze

- **Sieci wirtualne połączone równorzędnie, połączone sieci wirtualne lub wiele sieci wirtualnych**: aby odfiltrować ruch w połączonych równorzędnie sieciach wirtualnych, należy zastosować zasady punktu końcowego indywidualnie do tych sieci wirtualnych.
- **Filtrowanie ruchu internetowego za pomocą urządzeń sieciowych lub zapory platformy Azure**: Filtruj ruch usługi platformy Azure przy użyciu zasad, punktów końcowych usługi i Filtruj resztę Internetu lub ruchu platformy Azure za pośrednictwem urządzeń lub zapory platformy Azure.
- **Filtrowanie ruchu w ramach usług platformy Azure wdrożonych w sieciach wirtualnych**: w tej chwili zasady punktu końcowego usługi platformy Azure nie są obsługiwane dla żadnej zarządzanej usługi platformy Azure, która jest wdrażana w sieci wirtualnej. 
- **Filtrowanie ruchu do usług platformy Azure ze środowiska lokalnego**: zasady punktu końcowego usługi mają zastosowanie tylko do ruchu z podsieci związanych z zasadami. Aby umożliwić dostęp do konkretnych zasobów usługi platformy Azure ze środowisk lokalnych, ruch należy filtrować przy użyciu wirtualnych urządzeń sieciowych lub zapór.

## <a name="logging-and-troubleshooting"></a>Rejestrowanie i rozwiązywanie problemów
Nie ma dostępnego centralnego rejestrowania dla zasad punktu końcowego usługi. W przypadku dzienników zasobów usługi zobacz [Rejestrowanie punktów końcowych usługi](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting).

### <a name="troubleshooting-scenarios"></a>Scenariusze rozwiązywania problemów
- Odmowa dostępu do kont magazynu, które działały w wersji zapoznawczej (a nie w regionie geograficznym)
  - Dzięki uaktualnieniu usługi Azure Storage do korzystania z globalnych tagów usług, zakres punktu końcowego usługi i w ten sposób zasady punktu końcowego usługi są teraz globalne. Cały ruch do usługi Azure Storage jest szyfrowany za pośrednictwem punktów końcowych usługi i tylko konta magazynu, które są jawnie wymienione w zasadach, są dozwolone.
  - Jawnie Zezwól na listę wszystkich wymaganych kont magazynu w celu przywrócenia dostępu.  
  - Skontaktuj się z pomocą techniczną platformy Azure.
- Odmowa dostępu dla kont wymienionych w zasadach punktu końcowego
  - Filtrowanie przez sieciowe grupy zabezpieczeń lub zaporę może blokować dostęp
  - Jeśli usunięcie/ponowne zastosowanie zasad powoduje utratę połączenia:
    - Sprawdź, czy usługa platformy Azure jest skonfigurowana tak, aby zezwalać na dostęp z sieci wirtualnej za pośrednictwem punktów końcowych, lub czy zasady domyślne dla zasobu są ustawione na *Zezwalaj na wszystkie*.
    - Sprawdź, czy diagnostyka usługi pokazuje ruch przez punkty końcowe.
    - Sprawdź, czy dzienniki przepływu sieciowej grupy zabezpieczeń oraz dzienniki magazynu pokazują oczekiwany dostęp za pośrednictwem punktów końcowych usługi.
    - Skontaktuj się z pomocą techniczną platformy Azure.
- Odmowa dostępu dla kont niewymienionych w zasadach punktu końcowego usługi
  - Sprawdź, czy usługa Azure Storage jest skonfigurowana tak, aby zezwalać na dostęp z sieci wirtualnej za pośrednictwem punktów końcowych, lub czy zasady domyślne dla zasobu są ustawione na *Zezwalaj wszystkie*.
  - Upewnij się, że konta nie są **klasycznymi kontami magazynu** z zasadami punktu końcowego usługi w podsieci.
- Zarządzana usługa platformy Azure przestała działać po zastosowaniu zasad punktu końcowego usługi w podsieci
  - Usługi zarządzane nie są w tej chwili obsługiwane w ramach zasad punktu końcowego usługi. *Obejrzyj to miejsce na potrzeby aktualizacji*.

## <a name="provisioning"></a>Inicjowanie obsługi

Zasady punktu końcowego usługi można konfigurować w podsieciach — czynność tę może wykonać użytkownik z uprawnieniami do zapisu w sieci wirtualnej. Dowiedz się więcej na temat [wbudowanych ról](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) na platformie Azure i przypisywania określonych uprawnień do [ról niestandardowych](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Sieci wirtualne i konta usługi Azure Storage mogą znajdować się w tych samych lub różnych subskrypcjach lub Azure Active Directory dzierżawców.

## <a name="limitations"></a>Ograniczenia

- Możesz wdrożyć zasady punktu końcowego usługi tylko dla sieci wirtualnych wdrożonych za pomocą modelu wdrażania przy użyciu usługi Azure Resource Manager.
- Sieci wirtualne muszą znajdować się w tym samym regionie co zasady punktu końcowego usługi.
- Zasady punktu końcowego usługi możesz zastosować wobec podsieci tylko wtedy, gdy dla usług platformy Azure wymienionych w zasadach zostały skonfigurowane punkty końcowe usługi.
- Nie możesz użyć zasad punktu końcowego usługi w przypadku ruchu z sieci lokalnej do usług platformy Azure.
- Usługi zarządzane przez platformę Azure nie obsługują obecnie zasad punktu końcowego. Obejmuje to usługi zarządzane wdrożone w podsieciach współużytkowanych (np. *Azure HDInsight, Azure Batch, Azure:, azure Application Gateway, azure VPN Gateway, Azure firewall*) lub w dedykowanych podsieciach (np. *Azure App Service Environment, Azure Redis Cache, Azure API Management, Azure SQL, i klasycznych usług zarządzanych*).

 > [!WARNING]
 > Usługi platformy Azure wdrożone w sieci wirtualnej, np. usługa Azure HDInsight, uzyskują dostęp do innych usług platformy Azure, np. do usługi Azure Storage, w związku z wymaganiami infrastruktury. Ograniczenie zasad punktu końcowego do wybranych zasobów może uniemożliwić dostęp do tych zasobów infrastruktury dla usług platformy Azure wdrożonych w sieci wirtualnej.

- klasyczne konta magazynu nie są obsługiwane w zasadach punktu końcowego. Zasady będą domyślnie odrzucać dostęp do wszystkich klasycznych kont magazynu. Jeśli aplikacja potrzebuje dostępu do usługi Azure Resource Manager i klasycznych kont magazynu, zasady punktu końcowego nie powinny być stosowane dla tego ruchu.

## <a name="pricing-and-limits"></a>Ceny i limity

Za korzystanie z zasad punktu końcowego usługi nie są naliczane dodatkowe opłaty. Do punktów końcowych usługi stosowany jest bieżący model cen usług platformy Azure (takich jak Azure Storage).

Następujące limity są wymuszane w zasadach punktu końcowego usługi: 

 |Zasób | Limit domyślny |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpointPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [skonfigurować zasady punktu końcowego usługi dla sieci wirtualnej](virtual-network-service-endpoint-policies-portal.md)
- Dowiedz się więcej na temat [punktów końcowych usługi dla sieci wirtualnej](virtual-network-service-endpoints-overview.md)
