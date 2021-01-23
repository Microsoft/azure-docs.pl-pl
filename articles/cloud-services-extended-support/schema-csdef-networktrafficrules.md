---
title: Azure Cloud Services (obsługa rozszerzona) def. NetworkTrafficRules schemat | Microsoft Docs
description: Informacje związane z regułami ruchu sieciowego skojarzonymi z Cloud Services (obsługa rozszerzona)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 0064794701e87419da086c458673f7ccee4f37dd
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744798"
---
# <a name="azure-cloud-services-extended-support-definition-networktrafficrules-schema"></a>Schemat NetworkTrafficRules definicji platformy Cloud Services Azure (obsługa rozszerzona)

`NetworkTrafficRules`Węzeł jest opcjonalnym elementem w pliku definicji usługi, który określa, jak role komunikują się ze sobą. Ogranicza role, które mogą uzyskiwać dostęp do wewnętrznych punktów końcowych określonej roli. `NetworkTrafficRules`Nie jest elementem autonomicznym; jest on połączony z co najmniej dwiema rolami w pliku definicji usługi.

Domyślnym rozszerzeniem dla pliku definicji usługi jest csdef.

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

[AllowAllTraffic, element](#AllowAllTraffic)

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

## <a name="allowalltraffic-element"></a><a name="AllowAllTraffic"></a> AllowAllTraffic, element
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

## <a name="see-also"></a>Zobacz także
[Schemat definicji usługi w chmurze (obsługa rozszerzona)](schema-csdef-file.md).




