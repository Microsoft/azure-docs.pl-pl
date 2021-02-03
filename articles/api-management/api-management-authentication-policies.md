---
title: Zasady uwierzytelniania API Management platformy Azure | Microsoft Docs
description: Dowiedz się więcej na temat zasad uwierzytelniania dostępnych do użycia w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/27/2021
ms.author: apimpm
ms.openlocfilehash: 22d2960801cac2222f868c384a55b4bf436bc75b
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492607"
---
# <a name="api-management-authentication-policies"></a>Zasady uwierzytelniania w usłudze API Management
Ten temat zawiera informacje dotyczące następujących zasad API Management. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [zasady w API Management](./api-management-policies.md).

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a> Zasady uwierzytelniania

-   [Uwierzytelnianie z](api-management-authentication-policies.md#Basic) uwierzytelnianiem Basic z użyciem usługi wewnętrznej bazy danych przy użyciu uwierzytelniania podstawowego.

-   [Uwierzytelnianie przy użyciu certyfikatu klienta](api-management-authentication-policies.md#ClientCertificate) — uwierzytelnianie za pomocą usługi zaplecza przy użyciu certyfikatów klienta.

-   [Uwierzytelnianie przy użyciu tożsamości zarządzanej](api-management-authentication-policies.md#ManagedIdentity) — uwierzytelnianie za pomocą [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) dla usługi API Management.

##  <a name="authenticate-with-basic"></a><a name="Basic"></a> Uwierzytelnianie za pomocą języka Basic
 Zasady służą `authentication-basic` do uwierzytelniania w usłudze wewnętrznej bazy danych przy użyciu uwierzytelniania podstawowego. Te zasady skutecznie ustawiają nagłówek autoryzacji HTTP na wartość odpowiadającą podanym w zasadzie poświadczeniami.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Przykład

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagane|
|----------|-----------------|--------------|
|Uwierzytelnianie — podstawowe|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

|Nazwa|Opis|Wymagane|Domyślne|
|----------|-----------------|--------------|-------------|
|nazwa użytkownika|Określa nazwę użytkownika poświadczeń podstawowych.|Tak|Nie dotyczy|
|hasło|Określa hasło poświadczeń podstawowych.|Tak|Nie dotyczy|

### <a name="usage"></a>Użycie
 Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** przychodzące

-   **Zakresy zasad:** wszystkie zakresy

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a> Uwierzytelnianie przy użyciu certyfikatu klienta
 Zasady służą `authentication-certificate` do uwierzytelniania w usłudze wewnętrznej bazy danych przy użyciu certyfikatu klienta. Certyfikat musi zostać [zainstalowany do API Management](./api-management-howto-mutual-certificates.md) jako pierwszy i jest identyfikowany za pomocą odcisku palca lub identyfikatora certyfikatu (nazwa zasobu). 

> [!CAUTION]
> Jeśli certyfikat odwołuje się do certyfikatu przechowywanego w Azure Key Vault, zidentyfikuj go przy użyciu identyfikatora certyfikatu. Gdy certyfikat magazynu kluczy zostanie obrócony, jego odcisk palca w API Management ulegnie zmianie, a zasady nie rozpoznają nowego certyfikatu, jeśli zostanie on zidentyfikowany przez odcisk palca.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Przykłady

W tym przykładzie certyfikat klienta jest identyfikowany przez identyfikator certyfikatu:

```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
``` 

W tym przykładzie certyfikat klienta jest identyfikowany za pomocą odcisku palca:

```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
W tym przykładzie certyfikat klienta jest ustawiany w zasadach, a nie pobierany z wbudowanego magazynu certyfikatów:

```xml
<authentication-certificate body="@(context.Variables.GetValueOrDefault<byte[]>("byteCertificate"))" password="optional-certificate-password" />
```

### <a name="elements"></a>Elementy  
  
|Nazwa|Opis|Wymagane|  
|----------|-----------------|--------------|  
|Uwierzytelnianie — certyfikat|Element główny.|Tak|  
  
### <a name="attributes"></a>Atrybuty  
  
|Nazwa|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|odcisk palca|Odcisk palca certyfikatu klienta.|Albo `thumbprint` `certificate-id` musi być obecny.|Nie dotyczy|
|Identyfikator certyfikatu|Nazwa zasobu certyfikatu.|Albo `thumbprint` `certificate-id` musi być obecny.|Nie dotyczy|
|body|Certyfikat klienta jako tablicę bajtów.|Nie|Nie dotyczy|
|hasło|Hasło dla certyfikatu klienta.|Używany, jeśli certyfikat określony w programie `body` jest chroniony hasłem.|Nie dotyczy|
  
### <a name="usage"></a>Użycie  
 Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.  
  
-   **Sekcje zasad:** przychodzące  
  
-   **Zakresy zasad:** wszystkie zakresy  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a> Uwierzytelnianie przy użyciu tożsamości zarządzanej  
 Zasady służą `authentication-managed-identity` do uwierzytelniania w usłudze zaplecza przy użyciu tożsamości zarządzanej. Te zasady zasadniczo wykorzystują zarządzaną tożsamość do uzyskiwania tokenu dostępu z Azure Active Directory na potrzeby uzyskiwania dostępu do określonego zasobu. Po pomyślnym uzyskaniu tokenu zasady ustawili wartość tokenu w `Authorization` nagłówku przy użyciu `Bearer` schematu.

Do żądania tokenu można użyć zarówno tożsamości przypisanej do systemu, jak i dowolnej tożsamości przypisanej do użytkownika. Jeśli `client-id` nie podano tożsamości przypisanej do systemu, przyjmuje się. Jeśli `client-id` dla tej tożsamości przypisanej do użytkownika jest żądany token, na podstawie Azure Active Directory
  
### <a name="policy-statement"></a>Instrukcja zasad  
  
```xml  
<authentication-managed-identity resource="resource" client-id="clientid of user-assigned identity" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Przykład  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Uwierzytelnianie za pomocą usługi wewnętrznej bazy danych przy użyciu tożsamości zarządzanej
```xml  
<authentication-managed-identity resource="https://graph.microsoft.com"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Bus-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```

```xml
<authentication-managed-identity resource="api://Client_id_of_Backend"/> <!--Your own Azure AD Application-->
```

#### <a name="use-managed-identity-and-set-header-manually"></a>Ręczne używanie tożsamości zarządzanej i zestawu

```xml
<authentication-managed-identity resource="api://Client_id_of_Backend"
   output-token-variable-name="msi-access-token" ignore-error="false" /> <!--Your own Azure AD Application-->
<set-header name="Authorization" exists-action="override">
   <value>@("Bearer " + (string)context.Variables["msi-access-token"])</value>
</set-header>
```

#### <a name="use-managed-identity-in-send-request-policy"></a>Używanie tożsamości zarządzanej w zasadach wysyłania żądania
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Elementy  
  
|Nazwa|Opis|Wymagane|  
|----------|-----------------|--------------|  
|Uwierzytelnianie — tożsamość zarządzana |Element główny.|Tak|  
  
### <a name="attributes"></a>Atrybuty  
  
|Nazwa|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|zasób|Ciąg. Identyfikator aplikacji docelowego internetowego interfejsu API (zabezpieczony zasób) w Azure Active Directory.|Tak|Nie dotyczy|
|Identyfikator klienta|Ciąg. Identyfikator aplikacji tożsamości przypisanej przez użytkownika w Azure Active Directory.|Nie|tożsamość przypisana przez system|
|Output-token-Variable-Name|Ciąg. Nazwa zmiennej kontekstowej, która będzie otrzymywać wartość tokenu jako typ obiektu `string` . |Nie|Nie dotyczy|  
|Ignoruj-błąd|Typu. W przypadku wybrania opcji `true` potok zasad będzie nadal wykonywany nawet wtedy, gdy nie zostanie uzyskany token dostępu.|Nie|fałsz|  
  
### <a name="usage"></a>Użycie  
 Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.  
  
-   **Sekcje zasad:** przychodzące  
  
-   **Zakresy zasad:** wszystkie zakresy

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

+ [Zasady w API Management](api-management-howto-policies.md)
+ [Przekształć interfejsy API](transform-api.md)
+ [Dokumentacja zasad](./api-management-policies.md) pełna lista instrukcji zasad i ich ustawień
+ [Przykłady zasad](./policy-reference.md)