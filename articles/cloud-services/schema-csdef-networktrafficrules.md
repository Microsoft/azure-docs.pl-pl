---
title: Azure Cloud Services def. NetworkTrafficRules schemat | Microsoft Docs
description: Dowiedz się więcej na temat NetworkTrafficRules, który ogranicza role, które mogą uzyskiwać dostęp do wewnętrznych punktów końcowych roli. Łączy się z rolami w pliku definicji usługi.
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 351b369f-365e-46c1-82ce-03fc3655cc88
caps.latest.revision: 17
author: tgore03
ms.author: tagore
ms.openlocfilehash: e53c10395ec3168e656633cc43fb2d01902209fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "79534732"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Schemat NetworkTrafficRules definicji Cloud Services platformy Azure
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




