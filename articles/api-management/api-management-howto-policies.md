---
title: Zasady na platformie Azure API Management | Microsoft Docs
description: Dowiedz się, jak tworzyć, edytować i konfigurować zasady w programie API Management. Zobacz przykłady kodu i Wyświetl dodatkowe dostępne zasoby.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: a0786c1ce5d3a693dabe422b65af7177e16c20b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87905520"
---
# <a name="policies-in-azure-api-management"></a>Zasady w usłudze Azure API Management

W przypadku usługi Azure API Management (APIM) zasady stanowią zaawansowaną funkcję systemu, która umożliwia wydawcy zmianę zachowania interfejsu API za pomocą konfiguracji. Zasady są zbiorem instrukcji, które są wykonywane sekwencyjnie na żądanie lub odpowiedź interfejsu API. Popularne instrukcje obejmują konwersję formatu z XML do JSON i ograniczanie szybkości wywołań, aby ograniczyć liczbę wywołań przychodzących od dewelopera. Dostępnych jest wiele innych zasad.

Zasady są stosowane w ramach bramy, która znajduje się między klientem interfejsu API i zarządzanym interfejsem API. Brama odbiera wszystkie żądania i zwykle przekazuje je w niezmienionej postaci do bazowego interfejsu API. Jednak zasady mogą stosować zmiany zarówno do żądania przychodzącego, jak i do odpowiedzi wychodzącej.

Wyrażenia zasad mogą służyć jako wartości atrybutów lub wartości tekstowe w dowolnej z zasad usługi API Management, o ile w zasadach nie określono inaczej. Niektóre zasady, np. [Przepływ sterowania][Control flow] i [Ustawianie zmiennej][Set variable], są oparte na wyrażeniach zasad. Aby uzyskać więcej informacji, zobacz tematy [Advanced policies][Advanced policies] (Zaawansowane zasady) i [Policy expressions][Policy expressions] (Wyrażenia zasad).

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>Omówienie konfiguracji zasad

Definicja zasad to prosty dokument XML, który opisuje sekwencję przychodzących i wychodzących instrukcji. KOD XML można edytować bezpośrednio w oknie definicji. Lista instrukcji jest podawana po prawej stronie, a instrukcje mające zastosowanie do bieżącego zakresu są włączone i wyróżnione.

Kliknięcie instrukcji Enabled spowoduje dodanie odpowiedniego kodu XML w lokalizacji kursora w widoku definicji. 

> [!NOTE]
> Jeśli zasady, które chcesz dodać, nie są włączone, upewnij się, że jesteś w prawidłowym zakresie dla tych zasad. Każda instrukcja zasad jest przeznaczona do użycia w określonych zakresach i sekcjach zasad. Aby zapoznać się z sekcjami zasad i zakresami zasad, należy zapoznać się z sekcją **użycie** tych zasad w temacie [Informacje o zasadach][Policy Reference].
> 
> 

Konfiguracja jest podzielona na `inbound` , `backend` , `outbound` , i `on-error` . Seria określonych instrukcji zasad jest wykonywana w celu żądania i odpowiedzi.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

Jeśli wystąpi błąd podczas przetwarzania żądania, wszystkie pozostałe kroki w `inbound` `backend` `outbound` sekcjach, lub są pomijane, a wykonywanie przechodzi do instrukcji w `on-error` sekcji. Umieszczając instrukcje zasad w `on-error` sekcji, można sprawdzić błąd przy użyciu `context.LastError` właściwości, sprawdzić i dostosować odpowiedź na błąd przy użyciu `set-body` zasad i skonfigurować działanie w przypadku wystąpienia błędu. Istnieją kody błędów dla wbudowanych kroków i dla błędów, które mogą wystąpić podczas przetwarzania instrukcji zasad. Aby uzyskać więcej informacji, zobacz [Obsługa błędów w zasadach API Management](./api-management-error-handling-policies.md).

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>Jak skonfigurować zasady

Aby uzyskać informacje na temat konfigurowania zasad, zobacz [Ustawianie lub edytowanie zasad](set-edit-policies.md).

## <a name="policy-reference"></a>Dokumentacja zasad

Aby zapoznać się z pełną listą instrukcji zasad i ich ustawień, zobacz [Informacje o zasadach](./api-management-policies.md) .

## <a name="policy-samples"></a>Przykłady zasad

Zobacz [przykłady zasad](policy-samples.md) , aby uzyskać więcej przykładów kodu.

## <a name="examples"></a>Przykłady

### <a name="apply-policies-specified-at-different-scopes"></a>Stosowanie zasad określonych w różnych zakresach

Jeśli masz zasady na poziomie globalnym i skonfigurowano zasady dla interfejsu API, za każdym razem, gdy zostanie użyty określony interfejs API, zostaną zastosowane obie zasady. API Management umożliwia deterministyczne porządkowanie instrukcji połączonych zasad za pośrednictwem elementu podstawowego. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

W powyższej przykładowej definicji zasad, `cross-domain` instrukcja zostanie wykonana przed wszystkimi wyższymi zasadami, które byłyby po kolei przestrzegane przez `find-and-replace` zasady. 

### <a name="restrict-incoming-requests"></a>Ogranicz żądania przychodzące

Aby dodać nową instrukcję, aby ograniczyć żądania przychodzące do określonych adresów IP, umieść kursor bezpośrednio wewnątrz zawartości `inbound` elementu XML i kliknij instrukcję **Ogranicz wywołania IP wywołującego** .

![Zasady ograniczeń][policies-restrict]

Spowoduje to dodanie fragmentu kodu XML do `inbound` elementu, który zawiera wskazówki dotyczące sposobu konfigurowania instrukcji.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Aby ograniczyć żądania przychodzące i akceptować tylko te z adresu IP 1.2.3.4, zmodyfikuj kod XML w następujący sposób:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

+ [Przekształć interfejsy API](transform-api.md)
+ [Dokumentacja zasad](./api-management-policies.md) pełna lista instrukcji zasad i ich ustawień
+ [Przykłady zasad](policy-samples.md)   

[Policy Reference]: ./api-management-policies.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: ./mock-api-responses.md

[Advanced policies]: ./api-management-advanced-policies.md
[Control flow]: ./api-management-advanced-policies.md#choose
[Set variable]: ./api-management-advanced-policies.md#set-variable
[Policy expressions]: ./api-management-policy-expressions.md

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
