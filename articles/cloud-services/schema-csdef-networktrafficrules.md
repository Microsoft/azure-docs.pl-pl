---
title: Azure Cloud Services (klasyczny) def. NetworkTrafficRules schemat | Microsoft Docs
description: Dowiedz się więcej na temat NetworkTrafficRules, który ogranicza role, które mogą uzyskiwać dostęp do wewnętrznych punktów końcowych roli. Łączy się z rolami w pliku definicji usługi.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2c8ab53068b71652d03d03bf79a224fe5e34dff3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98739772"
---
# <a name="azure-cloud-services-classic-definition-networktrafficrules-schema"></a>Schemat NetworkTrafficRules definicji platformy Cloud Services Azure (klasyczny)

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

`NetworkTrafficRules`Węzeł jest opcjonalnym elementem w pliku definicji usługi, który określa, jak role komunikują się ze sobą. Ogranicza role, które mogą uzyskiwać dostęp do wewnętrznych punktów końcowych określonej roli. `NetworkTrafficRules`Nie jest elementem autonomicznym; jest on połączony z co najmniej dwiema rolami w pliku definicji usługi.

Domyślnym rozszerzeniem dla pliku definicji usługi jest. csdef.

> [!NOTE]
>  `NetworkTrafficRules`Węzeł jest dostępny tylko przy użyciu zestawu Azure SDK w wersji 1,3 lub nowszej.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Podstawowy schemat definicji usługi dla reguł ruchu sieciowego
Poniżej przedstawiono podstawowy format pliku definicji usługi zawierającego definicje ruchu sieciowego.

```xml
<ServiceDefinition …>
   <NetworkTrafficRules>
      <OnlyAllowTrafficTo>
         <Destinations>
            <RoleEndpoint endpointName="<name-of-the-endpoint>" roleName="<name-of-the-role-containing-the-endpoint>"/>
         </Destinations>
         <AllowAllTraffic/>
         <WhenSource matches="[AnyRule]">
            <FromRole roleName="<name-of-the-role-to-allow-traffic-from>"/>
         </WhenSource>
      </OnlyAllowTrafficTo>
   </NetworkTrafficRules>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementy schematu
`NetworkTrafficRules`Węzeł pliku definicji usługi zawiera te elementy, które opisano szczegółowo w kolejnych sekcjach w tym temacie:

[NetworkTrafficRules, element](#NetworkTrafficRules)

[OnlyAllowTrafficTo, element](#OnlyAllowTrafficTo)

[Element Destinations](#Destinations)

[RoleEndpoint, element](#RoleEndpoint)

AllowAllTraffic, element

[WhenSource, element](#WhenSource)

[FromRole, element](#FromRole)

##  <a name="networktrafficrules-element"></a><a name="NetworkTrafficRules"></a> NetworkTrafficRules, element
`NetworkTrafficRules`Element określa, które role mogą komunikować się z punktem końcowym w innej roli. Usługa może zawierać jedną `NetworkTrafficRules` definicję.

##  <a name="onlyallowtrafficto-element"></a><a name="OnlyAllowTrafficTo"></a> OnlyAllowTrafficTo, element
`OnlyAllowTrafficTo`Element opisuje zbiór docelowych punktów końcowych i role, które mogą się z nimi komunikować. Można określić wiele `OnlyAllowTrafficTo` węzłów.

##  <a name="destinations-element"></a><a name="Destinations"></a> Element Destinations
`Destinations`Element zawiera opis kolekcji RoleEndpoints niż może być przekazywany za pomocą.

##  <a name="roleendpoint-element"></a><a name="RoleEndpoint"></a> RoleEndpoint, element
`RoleEndpoint`Element opisuje punkt końcowy roli, aby zezwalać na komunikację z usługą. Można określić wiele `RoleEndpoint` elementów, jeśli w danej roli istnieje więcej niż jeden punkt końcowy.

| Atrybut      | Typ     | Opis |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Wymagane. Nazwa punktu końcowego, do którego ma być dozwolony ruch.|
| `roleName`     | `string` | Wymagane. Nazwa roli sieci Web, w której ma być dozwolona komunikacja.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic, element
`AllowAllTraffic`Element jest regułą, która umożliwia wszystkim rolom komunikowanie się z punktami końcowymi zdefiniowanymi w `Destinations` węźle.

##  <a name="whensource-element"></a><a name="WhenSource"></a> WhenSource, element
`WhenSource`Element opisuje zbiór ról niż może komunikować się z punktami końcowymi zdefiniowanymi w `Destinations` węźle.

| Atrybut | Typ     | Opis |
| --------- | -------- | ----------- |
| `matches` | `string` | Wymagane. Określa regułę, która ma zostać zastosowana podczas zezwalania na komunikację. Jedyną prawidłową wartością jest obecnie `AnyRule` .|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a> FromRole, element
`FromRole`Element określa role, które mogą komunikować się z punktami końcowymi zdefiniowanymi w `Destinations` węźle. Można określić wiele `FromRole` elementów, jeśli istnieje więcej niż jedna rola, która może komunikować się z punktami końcowymi.

| Atrybut  | Typ     | Opis |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Wymagane. Nazwa roli, z której ma być dozwolona komunikacja.|

## <a name="see-also"></a>Zobacz też
[Schemat definicji usługi w chmurze (klasycznej)](schema-csdef-file.md)




