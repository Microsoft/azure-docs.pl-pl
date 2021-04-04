---
title: Azure Cloud Services (klasyczny) schemat NetworkConfiguration | Microsoft Docs
description: Dowiedz się więcej na temat elementów podrzędnych elementu NetworkConfiguration w pliku konfiguracji usługi, który określa Virtual Network i wartości DNS.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
'---thor': tagore
ms.openlocfilehash: acf4c050ade21a6e5fc51ee6ace512eff00360ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743461"
---
# <a name="azure-cloud-services-classic-config-networkconfiguration-schema"></a>Schemat NetworkConfiguration konfiguracji platformy Cloud Services Azure (klasyczny)

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

`NetworkConfiguration`Element pliku konfiguracji usługi określa wartości Virtual Network i DNS. Te ustawienia są opcjonalne dla usług Cloud Services.

Aby dowiedzieć się więcej o sieciach wirtualnych i skojarzonych schematach, można użyć poniższego zasobu:

- [Schemat konfiguracji usługi w chmurze (klasycznej)](schema-cscfg-file.md)
- [Schemat definicji usługi w chmurze (klasycznej)](schema-csdef-file.md)
- [Tworzenie Virtual Network (klasyczny)](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal)

## <a name="networkconfiguration-element"></a>NetworkConfiguration, element
Poniższy przykład pokazuje `NetworkConfiguration` element i jego elementy podrzędne.

```xml
<ServiceConfiguration>
  <NetworkConfiguration>
    <AccessControls>
      <AccessControl name="aclName1">
        <Rule order="<rule-order>" action="<rule-action>" remoteSubnet="<subnet-address>" description="rule-description"/>
      </AccessControl>
    </AccessControls>
    <EndpointAcls>
      <EndpointAcl role="<role-name>" endpoint="<endpoint-name>" accessControl="<acl-name>"/>
    </EndpointAcls>
    <Dns>
      <DnsServers>
        <DnsServer name="<server-name>" IPAddress="<server-address>" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSite name="Group <RG-VNet> <VNet-name>"/>
    <AddressAssignments>
      <InstanceAddress roleName="<role-name>">
        <Subnets>
          <Subnet name="<subnet-name>"/>
        </Subnets>
      </InstanceAddress>
      <ReservedIPs>
        <ReservedIP name="<reserved-ip-name>"/>
      </ReservedIPs>
    </AddressAssignments>
  </NetworkConfiguration>
</ServiceConfiguration>
```

W poniższej tabeli opisano elementy podrzędne `NetworkConfiguration` elementu.

| Element       | Opis |
| ------------- | ----------- |
| AccessControl | Opcjonalny. Określa reguły dostępu do punktów końcowych w usłudze w chmurze. Nazwa kontroli dostępu jest definiowana przez ciąg dla `name` atrybutu. `AccessControl`Element zawiera jeden lub więcej `Rule` elementów. Można zdefiniować więcej niż jeden `AccessControl` element.|
| Reguła | Opcjonalny. Określa akcję, która ma zostać wykonana dla określonego zakresu podsieci adresów IP. Kolejność reguły jest definiowana przez wartość ciągu dla `order` atrybutu. Dolna reguła ma wyższy priorytet. Na przykład można określić reguły o numerach porządkowych 100, 200 i 300. Reguła o numerze porządkowym 100 ma pierwszeństwo przed regułą z kolejnością 200.<br /><br /> Akcja dla reguły jest definiowana przez ciąg dla `action` atrybutu. Możliwe wartości:<br /><br /> -   `permit` — Określa, że tylko pakiety z określonego zakresu podsieci mogą komunikować się z punktem końcowym.<br />-   `deny` — Określa, czy dostęp do punktów końcowych w określonym zakresie podsieci jest zabroniony.<br /><br /> Zakres podsieci adresów IP, na które ma wpływ reguła, jest definiowany przez ciąg dla `remoteSubnet` atrybutu. Opis reguły jest definiowany przez ciąg dla `description` atrybutu.|
| EndpointAcl | Opcjonalny. Określa przypisanie reguł kontroli dostępu do punktu końcowego. Nazwa roli zawierającej punkt końcowy jest definiowana przez ciąg dla `role` atrybutu. Nazwa punktu końcowego jest definiowana przez ciąg dla `endpoint` atrybutu. Nazwa zestawu `AccessControl` reguł, które mają zostać zastosowane do punktu końcowego, jest definiowana w ciągu dla `accessControl` atrybutu. Można zdefiniować więcej niż jeden `EndpointAcl` element.|
| Serwer DNS | Opcjonalny. Określa ustawienia dla serwera DNS. Możesz określić ustawienia dla serwerów DNS bez Virtual Network. Nazwa serwera DNS jest definiowana przez ciąg dla `name` atrybutu. Adres IP serwera DNS jest definiowany przez ciąg dla `IPAddress` atrybutu. Adres IP musi być prawidłowym adresem IPv4.|
| VirtualNetworkSite | Opcjonalny. Określa nazwę witryny Virtual Network, w której ma zostać wdrożona usługa w chmurze. To ustawienie nie powoduje utworzenia witryny Virtual Network. Odwołuje się do lokacji, która została wcześniej zdefiniowana w pliku sieciowym dla Virtual Network. Usługa w chmurze może być tylko składową jednego Virtual Network. Jeśli to ustawienie nie zostanie określone, usługa w chmurze nie zostanie wdrożona do Virtual Network. Nazwa witryny Virtual Network jest definiowana przez ciąg dla `name` atrybutu.|
| InstanceAddress | Opcjonalny. Określa skojarzenie roli z podsiecią lub zestawem podsieci w Virtual Network. W przypadku kojarzenia nazwy roli z adresem wystąpienia można określić podsieci, do których ta rola ma być skojarzona. `InstanceAddress`Zawiera element z podsieciami. Nazwa roli skojarzonej z podsiecią lub podsieciami jest definiowana przez ciąg dla `roleName` atrybutu.|
| Podsieć | Opcjonalny. Określa podsieć zgodną z nazwą podsieci w pliku konfiguracyjnym sieci. Nazwa podsieci jest definiowana przez ciąg dla `name` atrybutu.|
| ReservedIP | Opcjonalny. Określa zastrzeżony adres IP, który ma zostać skojarzony z wdrożeniem. Aby utworzyć zastrzeżony adres IP, należy użyć Utwórz adres Zastrzeżony adres IP. Każde wdrożenie w usłudze w chmurze może być skojarzone z jednym zastrzeżonym adresem IP. Nazwa zastrzeżonego adresu IP jest definiowana przez ciąg dla `name` atrybutu.|

## <a name="see-also"></a>Zobacz też
[Schemat konfiguracji usługi w chmurze (klasycznej)](schema-cscfg-file.md)