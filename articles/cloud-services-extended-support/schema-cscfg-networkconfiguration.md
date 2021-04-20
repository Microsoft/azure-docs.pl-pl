---
title: Azure Cloud Services (rozszerzona obsługa) NetworkConfiguration Schema | Microsoft Docs
description: Informacje związane ze schematem konfiguracji sieci dla Cloud Services (rozszerzona obsługa)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ed2d48288bf97fe3ebaa1e8ffc1336d8a82d940e
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719029"
---
# <a name="azure-cloud-services-extended-support-config-networkconfiguration-schema"></a>Azure Cloud Services konfiguracji sieci (rozszerzona obsługa) Schemacie konfiguracji

Element pliku konfiguracji usługi określa wartości Virtual Network `NetworkConfiguration` DNS. Te ustawienia są opcjonalne dla Cloud Services.

Aby dowiedzieć się więcej o sieciach wirtualnych i skojarzonych schematach, możesz użyć następującego zasobu:

- [Schemat konfiguracji usługi w chmurze (rozszerzona obsługa).](schema-cscfg-file.md)
- [Schemat definicji usługi w chmurze (rozszerzona obsługa).](schema-csdef-file.md)
- [Utwórz Virtual Network](../virtual-network/manage-virtual-network.md).

## <a name="networkconfiguration-element"></a>NetworkConfiguration, element
W poniższym przykładzie pokazano `NetworkConfiguration` element i jego elementy podrzędne.

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

W poniższej tabeli opisano elementy podrzędne `NetworkConfiguration` elementu .

| Element       | Opis |
| ------------- | ----------- |
| AccessControl | Opcjonalny. Określa reguły dostępu do punktów końcowych w usłudze w chmurze. Nazwa kontroli dostępu jest definiowana przez ciąg dla `name` atrybutu. Element `AccessControl` zawiera co najmniej jeden `Rule` element. Można zdefiniować `AccessControl` więcej niż jeden element.|
| Reguła | Opcjonalny. Określa akcję, która ma zostać podjęta dla określonego zakresu podsieci adresów IP. Kolejność reguły jest definiowana przez wartość ciągu dla `order` atrybutu . Im niższa reguła, tym wyższy priorytet. Można na przykład określić reguły z numerami zamówień 100, 200 i 300. Reguła o numerze zamówienia 100 ma pierwszeństwo przed regułą, która ma kolejność 200.<br /><br /> Akcja reguły jest definiowana przez ciąg dla `action` atrybutu. Możliwe wartości:<br /><br /> -   `permit` — określa, że tylko pakiety z określonego zakresu podsieci mogą komunikować się z punktem końcowym.<br />-   `deny` — określa, że odmowa dostępu do punktów końcowych w określonym zakresie podsieci.<br /><br /> Zakres podsieci adresów IP, których dotyczy reguła, jest definiowany przez ciąg dla `remoteSubnet` atrybutu. Opis reguły jest definiowany przez ciąg dla `description` atrybutu .|
| EndpointAcl | Opcjonalny. Określa przypisanie reguł kontroli dostępu do punktu końcowego. Nazwa roli, która zawiera punkt końcowy, jest definiowana przez ciąg dla `role` atrybutu. Nazwa punktu końcowego jest definiowana przez ciąg dla `endpoint` atrybutu . Nazwa zestawu reguł, które mają być stosowane do punktu końcowego, jest definiowana `AccessControl` w ciągu dla `accessControl` atrybutu . Można zdefiniować `EndpointAcl` więcej niż jeden element.|
| Serwer DNS | Opcjonalny. Określa ustawienia serwera DNS. Można określić ustawienia dla serwerów DNS bez Virtual Network. Nazwa serwera DNS jest definiowana przez ciąg dla `name` atrybutu. Adres IP serwera DNS jest definiowany przez ciąg dla `IPAddress` atrybutu . Adres IP musi być prawidłowym adresem IPv4.|
| VirtualNetworkSite | Opcjonalny. Określa nazwę lokacji Virtual Network, w której chcesz wdrożyć usługę w chmurze. To ustawienie nie powoduje utworzenia Virtual Network lokacji. Odwołuje się do lokacji, która została wcześniej zdefiniowana w pliku sieciowym dla Virtual Network. Usługa w chmurze może być członkiem tylko jednego Virtual Network. Jeśli to ustawienie nie zostanie określone, usługa w chmurze nie zostanie wdrożona w Virtual Network. Nazwa witryny Virtual Network jest definiowana przez ciąg dla `name` atrybutu .|
| InstanceAddress (InstanceAddress) | Opcjonalny. Określa skojarzenie roli z podsiecią lub zestawem podsieci w Virtual Network. Podczas kojarzenia nazwy roli z adresem wystąpienia można określić podsieci, z którymi ta rola ma być skojarzona. Element `InstanceAddress` zawiera element Podsieci. Nazwa roli, która jest skojarzona z podsiecią lub podsieciami, jest definiowana przez ciąg dla `roleName` atrybutu .|
| Podsieć | Opcjonalny. Określa podsieć, która odpowiada nazwie podsieci w pliku konfiguracji sieci. Nazwa podsieci jest definiowana przez ciąg dla `name` atrybutu .|
| ReservedIP | Opcjonalny. Określa zastrzeżony adres IP, który powinien być skojarzony z wdrożeniem. Metodę alokacji dla zastrzeżonego adresu IP należy określić tak jak w przypadku wdrożeń szablonu `Static` i programu PowerShell. Każde wdrożenie w usłudze w chmurze może być skojarzone tylko z jednym zastrzeżonym adresem IP. Nazwa zastrzeżonego adresu IP jest definiowana przez ciąg dla `name` atrybutu .|

## <a name="see-also"></a>Zobacz też
[Schemat konfiguracji usługi w chmurze (rozszerzona obsługa).](schema-cscfg-file.md)
