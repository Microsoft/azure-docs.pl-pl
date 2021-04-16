---
title: Samouczek — opracowywanie punktu końcowego SCIM na celu aprowizowanie użytkowników w aplikacjach z usługi Azure AD
description: System for Cross-domain Identity Management (SCIM) standaryzuje automatyczną aprowizowanie użytkowników. Z tego samouczka dowiesz się, jak opracować punkt końcowy SCIM, zintegrować interfejs API standardu SCIM z usługą Azure Active Directory i rozpocząć automatyzowanie aprowzowania użytkowników i grup w aplikacjach w chmurze.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: tutorial
ms.date: 04/12/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: contperf-fy21q2
ms.openlocfilehash: 3d53c96c4b0306911b0c8a0b8576f35a73419db0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498156"
---
# <a name="tutorial-develop-and-plan-provisioning-for-a-scim-endpoint"></a>Samouczek: opracowywanie i planowanie aprowowania dla punktu końcowego SCIM

Jako deweloper aplikacji możesz użyć interfejsu API zarządzania użytkownikami systemu do zarządzania tożsamościami między domenami (SCIM, Cross-Domain Identity Management), aby włączyć automatyczną aprowizowanie użytkowników i grup między aplikacją i usługą Azure AD (AAD). W tym artykule opisano sposób tworzenia punktu końcowego SCIM i integracji z usługą aprowizowania usługi AAD. Specyfikacja SCIM zawiera wspólny schemat użytkownika do aprowiowania. W połączeniu ze standardami federacji, takich jak SAML lub OpenID Connect, standard SCIM zapewnia administratorom end-to-end, oparte na standardach rozwiązanie do zarządzania dostępem.

![Aprowizowanie z usługi Azure AD do aplikacji przy użyciu standardu SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

SCIM to standardowa definicja dwóch punktów końcowych: punktu `/Users` końcowego i `/Groups` punktu końcowego. Używa ona typowych czasowników REST do tworzenia, aktualizowania i usuwania obiektów oraz wstępnie zdefiniowanego schematu dla typowych atrybutów, takich jak nazwa grupy, nazwa użytkownika, imię, nazwisko i adres e-mail. Aplikacje, które oferują interfejs API REST SCIM 2.0, mogą zmniejszyć lub wyeliminować problem pracy z zastrzeżonym interfejsem API zarządzania użytkownikami. Na przykład każdy zgodny klient SCIM wie, jak utworzyć wpis użytkownika post protokołu HTTP obiektu JSON w punkcie `/Users` końcowym. Zamiast wymagać nieco innego interfejsu API dla tych samych podstawowych akcji, aplikacje zgodne ze standardem SCIM mogą natychmiast korzystać z istniejących już klientów, narzędzi i kodu. 

Standardowy schemat obiektów użytkownika i interfejsy API REST do zarządzania zdefiniowane w standardzie SCIM 2.0 (RFC [7642,](https://tools.ietf.org/html/rfc7642) [7643,](https://tools.ietf.org/html/rfc7643) [7644](https://tools.ietf.org/html/rfc7644)) umożliwiają dostawcom tożsamości i aplikacjom łatwiejszą integrację ze sobą. Deweloperzy aplikacji, którzy skompilowali punkt końcowy SCIM, mogą integrować się z dowolnym klientem zgodnym ze standardem SCIM bez konieczności pracy niestandardowej.

Aby zautomatyzować aprowizowanie aplikacji, konieczne będzie skomponowanie i zintegrowanie punktu końcowego SCIM z klientem scim usługi Azure AD. Aby rozpocząć aprowizowanie użytkowników i grup w aplikacji, należy wykonać poniższe kroki. 
    
1. Projektowanie schematu użytkowników i grup

   Zidentyfikuj obiekty i atrybuty aplikacji, aby określić, jak są mapowane na schemat użytkownika i grupy obsługiwany przez implementację standardu SCIM usługi AAD.

1. Opis implementacji standardu SCIM w UAD

   Dowiedz się, w jaki sposób klient standardu SCIM usługi AAD jest implementowany w celu modelowania obsługi żądań i odpowiedzi protokołu SCIM.

1. Tworzenie punktu końcowego standardu SCIM

   Punkt końcowy musi być zgodny ze standardem SCIM 2.0, aby można go było zintegrować z usługą AAD Provisioning Service. Opcjonalnie możesz użyć bibliotek Common Language Infrastructure (CLI) firmy Microsoft i przykładów kodu do skompilowania punktu końcowego. Te przykłady są tylko do celów referencyjnych i testowych; Zalecamy, aby nie używać ich jako zależności w aplikacji produkcyjnej.

1. Integrowanie punktu końcowego SCIM z klientem scim usługi AAD 

   Jeśli Twoja organizacja używa aplikacji innej firmy do zaimplementowania profilu standardu SCIM 2.0, który obsługuje usługi AAD, możesz szybko zautomatyzować aprowizowanie i coprowizowanie użytkowników i grup.

1. Publikowanie aplikacji w galerii aplikacji WAD 

   Ułatwianie klientom odnajdywania aplikacji i łatwego konfigurowania aprowizowania. 

![Procedura integracji punktu końcowego SCIM z usługą Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="design-your-user-and-group-schema"></a>Projektowanie schematu użytkowników i grup

Każda aplikacja wymaga innych atrybutów, aby utworzyć użytkownika lub grupę. Rozpocznij integrację, identyfikując wymagane obiekty (użytkowników, grupy) i atrybuty (nazwa, menedżer, stanowisko itp.), których potrzebuje aplikacja. 

Standard SCIM definiuje schemat zarządzania użytkownikami i grupami. 

Podstawowy **schemat** użytkownika wymaga tylko trzech atrybutów (wszystkie inne atrybuty są opcjonalne):

- `id`, zdefiniowany identyfikator dostawcy usług
- `userName`, unikatowy identyfikator użytkownika (zazwyczaj mapuje na główną nazwę użytkownika usługi Azure AD)
- `meta`, *metadane tylko* do odczytu utrzymywane przez dostawcę usług

Oprócz podstawowego **schematu** użytkownika standard SCIM definiuje  rozszerzenie użytkownika przedsiębiorstwa z modelem rozszerzania schematu użytkownika zgodnie z potrzebami aplikacji. 

Jeśli na przykład aplikacja wymaga zarówno adresu e-mail użytkownika,  jak i menedżera użytkownika, użyj  podstawowego schematu do zbierania wiadomości e-mail użytkownika i schematu użytkownika przedsiębiorstwa w celu zebrania menedżera użytkownika.

Aby zaprojektować schemat, wykonaj następujące kroki:

1. Wyliczysz atrybuty wymagane przez aplikację, a następnie skategoryzuj je jako atrybuty wymagane do uwierzytelniania (np. loginName i email), atrybuty wymagane do zarządzania cyklem życia użytkownika (np. stan/aktywny) i wszystkie inne atrybuty wymagane do działania aplikacji (np. menedżer, tag).

1. Sprawdź, czy atrybuty są już zdefiniowane w podstawowym **schemacie** użytkownika lub **w schemacie użytkownika** przedsiębiorstwa. Jeśli nie, należy zdefiniować rozszerzenie schematu użytkownika, które obejmuje brakujące atrybuty. Zobacz przykład poniżej, aby uzyskać rozszerzenie dla użytkownika umożliwiające aprowizowanie `tag` użytkownika.

1. Mapowanie atrybutów SCIM na atrybuty użytkownika w usłudze Azure AD. Jeśli jeden z atrybutów zdefiniowanych w punkcie końcowym SCIM nie ma wyraźnego odpowiednika w schemacie użytkownika usługi Azure AD, przewodnik administratora dzierżawy w celu rozszerzenia schematu lub użycia atrybutu rozszerzenia, jak pokazano poniżej dla `tags` właściwości.

|Wymagany atrybut aplikacji|Zamapowany atrybut SCIM|Zamapowany atrybut usługi Azure AD|
|--|--|--|
|Loginname|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|name.familyName|Nazwisko|
|workMail|emails[type eq "work"].value|Mail|
|manager|manager|manager|
|tag|urn:ietf:params:scim:schemas:extension:2.0:CustomExtension:tag|extensionAttribute1|
|status|aktywne|isSoftDeleted (obliczona wartość nie jest przechowywana w użytkowniku)|

**Przykładowa lista wymaganych atrybutów**

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen@testuser.com",
     "id": "48af03ac28ad4fb88478",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
}   
```
**Przykładowy schemat zdefiniowany przez ładunek JSON**

> [!NOTE]
> Oprócz atrybutów wymaganych przez aplikację reprezentacja JSON obejmuje również wymagane atrybuty `id` `externalId` , i `meta` .

Ułatwia kategoryzowanie i mapowanie wszelkich domyślnych atrybutów użytkownika w usłudze Azure AD na standard `/User` SCIM RFC. Zobacz, jak dostosowywać atrybuty są mapowane między usługą Azure AD i punktem `/Group` [końcowym SCIM.](customize-application-attributes.md)


| Azure Active Directory użytkownika | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |aktywne |
|działu,|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|
| displayName |displayName |
|Idpracownika|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "faks"].value |
| givenName |name.givenName |
| jobTitle |tytuł |
| mail (poczta) |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager |
| telefon komórkowy |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"]. Wartość |
| physical-Delivery-OfficeName |addresses[type eq "other"]. Sformatowany |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| numer telefonu |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

**Przykładowa lista atrybutów użytkowników i grup**

| Azure Active Directory grupy | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| mail (poczta) |emails[type eq "work"].value |
| mailNickname (nazwa mailNickname) |displayName |
| elementy członkowskie |elementy członkowskie |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"]. Wartość |

**Przykładowa lista atrybutów grupy**

> [!NOTE]
> Nie jest wymagane, aby obsługiwać zarówno użytkowników, jak i grupy, ani wszystkie pokazane tutaj atrybuty. Jest to jedynie odwołanie do sposobu, w jaki atrybuty w usłudze Azure AD są często mapowane na właściwości w protokole SCIM. 

Istnieje kilka punktów końcowych zdefiniowanych w dokumencie SCIM RFC. Możesz rozpocząć od punktu `/User` końcowego, a następnie rozwinąć w tym miejscu. 

|Punkt końcowy|Opis|
|--|--|
|/User|Wykonywanie operacji CRUD na obiekcie użytkownika.|
|/Group|Wykonywanie operacji CRUD na obiekcie grupy.|
|/Schemas|Zestaw atrybutów obsługiwanych przez każdego klienta i dostawcę usług może się różnić. Jeden dostawca usług może obejmować usługi , i , natomiast inny `name` dostawca usług używa usług , i `title` `emails` `name` `title` `phoneNumbers` . Punkt końcowy schematów umożliwia odnajdywanie obsługiwanych atrybutów.|
|/Bulk|Operacje zbiorcze umożliwiają wykonywanie operacji na dużej kolekcji obiektów zasobów w ramach jednej operacji (np. aktualizowanie członkostwa w dużej grupie).|
|/ServiceProviderConfig|Zawiera szczegółowe informacje na temat obsługiwanych funkcji standardu SCIM, na przykład obsługiwanych zasobów i metody uwierzytelniania.|
|/ResourceTypes|Określa metadane dotyczące każdego zasobu.|

**Przykładowa lista punktów końcowych**

> [!NOTE]
> Użyj punktu końcowego do obsługi atrybutów niestandardowych lub jeśli schemat zmienia się często, ponieważ umożliwia klientowi automatyczne pobieranie `/Schemas` najbardziej aktualnego schematu. Użyj punktu `/Bulk` końcowego do obsługi grup.

## <a name="understand-the-aad-scim-implementation"></a>Opis implementacji standardu SCIM w UAD

W celu obsługi interfejsu API zarządzania użytkownikami standardu SCIM 2.0 w tej sekcji opisano sposób implementowanie klienta standardu SCIM usługi AAD oraz przedstawiono sposób modelowania obsługi żądań i odpowiedzi protokołu SCIM.

> [!IMPORTANT]
> Zachowanie implementacji standardu SCIM usługi Azure AD zostało ostatnio zaktualizowane 18 grudnia 2018 r. Aby uzyskać informacje o tym, co się zmieniło, zobacz Zgodność protokołu [SCIM 2.0 usługi Azure AD User Provisioning.](application-provisioning-config-problem-scim-compatibility.md)

W ramach [specyfikacji protokołu SCIM 2.0](http://www.simplecloud.info/#Specification)aplikacja musi spełniać następujące wymagania:

|Wymaganie|Uwagi dotyczące odwołania (protokół SCIM)|
|-|-|
|Tworzenie użytkowników i opcjonalnie grup|[sekcja 3.3](https://tools.ietf.org/html/rfc7644#section-3.3)|
|Modyfikowanie użytkowników lub grup przy użyciu żądań PATCH|[sekcja 3.5.2.](https://tools.ietf.org/html/rfc7644#section-3.5.2) Obsługa zapewnia, że grupy i użytkownicy są aprowieni w sposób efektywny.|
|Pobieranie znanego zasobu dla utworzonego wcześniej użytkownika lub grupy|[sekcja 3.4.1](https://tools.ietf.org/html/rfc7644#section-3.4.1)|
|Wykonywanie zapytań o użytkowników lub grupy|[sekcja 3.4.2.](https://tools.ietf.org/html/rfc7644#section-3.4.2)  Domyślnie użytkownicy są pobierani przez swoich i pytani przez ich i , a grupy są pytane `id` `username` przez `externalId` . `displayName`|
|Wykonywanie zapytania o użytkownika według identyfikatora i menedżera|sekcja 3.4.2|
|Wykonywanie zapytań o grupy według identyfikatora i członka|sekcja 3.4.2|
|Filtr [excludedAttributes =members](#get-group) podczas wykonywania zapytania o zasób grupy|sekcja 3.4.2.5|
|Zaakceptuj pojedynczy token bearer do uwierzytelniania i autoryzacji usługi AAD w aplikacji.||
|Nie soft-deleting a user `active=false` and restoring the user `active=true`|Obiekt użytkownika powinien być zwracany w żądaniu niezależnie od tego, czy użytkownik jest aktywny. Użytkownik nie powinien być zwracany tylko wtedy, gdy jest trwale usunięty z aplikacji.|
|Obsługa punktu końcowego /Schemas|[sekcja 7](https://tools.ietf.org/html/rfc7643#page-30) Punkt końcowy odnajdywania schematu będzie używany do odnajdywania dodatkowych atrybutów.|

Podczas implementowania punktu końcowego SCIM należy stosować się do ogólnych wytycznych w celu zapewnienia zgodności z usługi AAD:

* `id` jest wymaganą właściwością dla wszystkich zasobów. Każda odpowiedź, która zwraca zasób, powinna zapewnić, że każdy zasób ma tę właściwość, z wyjątkiem `ListResponse` zerem elementów członkowskich.
* Odpowiedzią na żądanie zapytania/filtru zawsze powinna być odpowiedź `ListResponse` .
* Grupy są opcjonalne, ale obsługiwane tylko wtedy, gdy implementacja standardu SCIM obsługuje **żądania PATCH.**
* Nie trzeba uwzględniać całego zasobu w odpowiedzi **PATCH.**
* Usługa Microsoft AAD używa tylko następujących operatorów: `eq` , `and`
* Nie należy wymagać dopasowania z wielkością liter dla elementów strukturalnych w standardach SCIM, w szczególności wartości operacji **PATCH,** zgodnie z definicją w sekcji `op` [3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2). AAD emituje wartości jako `op` **Dodaj,** **Zastąp** i **Usuń**.
* Usługa Microsoft AAD żąda pobrania losowego użytkownika i grupy, aby upewnić się, że punkt końcowy i poświadczenia są prawidłowe. Jest to również wykonywane w ramach przepływu **Testuj** połączenie w Azure Portal [.](https://portal.azure.com) 
* Atrybut, dla których można tworzyć zapytania dotyczące [zasobów,](https://portal.azure.com)należy ustawić jako pasujący atrybut w aplikacji w aplikacji w Azure Portal , zobacz Dostosowywanie mapowań atrybutów aprowizacji [użytkowników.](customize-application-attributes.md)
* Atrybut entitlements nie jest obsługiwany.
* Obsługa protokołu HTTPS w punkcie końcowym SCIM.
* [Odnajdywanie schematów](#schema-discovery)
  * Odnajdywanie schematów nie jest obecnie obsługiwane w aplikacji niestandardowej, ale jest używane w niektórych aplikacjach galerii. W przyszłości odnajdywanie schematu będzie używane jako metoda podstawowa do dodawania dodatkowych atrybutów do łącznika. 
  * Jeśli wartość nie istnieje, nie wysyłaj wartości null.
  * Wartości właściwości powinny mieć camel cased (np. readWrite).
  * Musi zwrócić odpowiedź listy.
  
### <a name="user-provisioning-and-deprovisioning"></a>Aprowizowanie i coprowizowanie użytkowników

Na poniższej ilustracji przedstawiono komunikaty wysyłane przez usługę AAD do usługi SCIM w celu zarządzania cyklem życia użytkownika w magazynie tożsamości aplikacji.  

![Pokazuje sekwencję aprowacji i coprowizowania użytkowników](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Sekwencja aprowizowania i coprowizowania użytkowników*

### <a name="group-provisioning-and-deprovisioning"></a>Aprowizowanie i coprowizowanie grup

Aprowizowanie i coprowizowanie grup jest opcjonalne. Po zaimplementowaniu i włączeniu na poniższej ilustracji przedstawiono komunikaty wysyłane przez usługę AAD do usługi SCIM w celu zarządzania cyklem życia grupy w magazynie tożsamości aplikacji. Te komunikaty różnią się od komunikatów o użytkownikach na dwa sposoby:

* Żądania pobrania grup określają, że atrybut members ma zostać wykluczony z dowolnego zasobu dostarczonego w odpowiedzi na żądanie.  
* Żądania ustalenia, czy atrybut odwołania ma określoną wartość, to żądania dotyczące atrybutu elementów członkowskich.  

![Pokazuje sekwencję aprowacji i coprowizowania grup](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Sekwencja aprowacji i coprowizowania grup*

### <a name="scim-protocol-requests-and-responses"></a>Żądania i odpowiedzi dotyczące protokołu SCIM
Ta sekcja zawiera przykładowe żądania SCIM emitowane przez klienta AAD SCIM oraz przykładowe oczekiwane odpowiedzi. Aby uzyskać najlepsze wyniki, należy zakodować aplikację do obsługi tych żądań w tym formacie i emitować oczekiwane odpowiedzi.

> [!IMPORTANT]
> Aby zrozumieć, jak i kiedy usługa APROWIZOWANIE użytkowników usługi AAD emituje operacje opisane poniżej, zobacz sekcję [Cykle](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) aprowowania: początkowe i przyrostowe w artykule [Jak działa aprowizowanie.](how-provisioning-works.md)

[Operacje użytkownika](#user-operations)
  - [Create User](#create-user) [(Żądanie](#request)  /  [odpowiedzi](#response))
  - [Get User](#get-user) [(Żądanie](#request-1)  /  [odpowiedzi](#response-1))
  - [Get User by query](#get-user-by-query) [(Żądanie](#request-2)  /  [odpowiedzi)](#response-2)
  - [Uzyskiwanie użytkownika według zapytania — zero wyników](#get-user-by-query---zero-results) [(odpowiedź na](#request-3)  /  [żądanie](#response-3))
  - [Aktualizowanie użytkownika [właściwości o wielu wartościach]](#update-user-multi-valued-properties) ([Odpowiedź](#request-4)  /  [na żądanie](#response-4))
  - [Aktualizowanie użytkownika [właściwości o pojedynczej wartości]](#update-user-single-valued-properties) ([Odpowiedź](#request-5)  /  [na żądanie](#response-5)) 
  - [Wyłącz użytkownika](#disable-user) [(odpowiedź na](#request-14)  /  [żądanie](#response-14))
  - [Usuń użytkownika](#delete-user) [(odpowiedź na](#request-6)  /  [żądanie](#response-6))

[Operacje na grupach](#group-operations)
  - [Utwórz grupę](#create-group) [(odpowiedź](#request-7)  /  [na żądanie)](#response-7)
  - [Get Group](#get-group) [(Żądanie](#request-8)  /  [odpowiedzi](#response-8))
  - [Get Group by displayName](#get-group-by-displayname) ([Request](#request-9)  /  [Response](#response-9))
  - [Update Group [Non-member attributes]](#update-group-non-member-attributes) [(Odpowiedź na](#request-10)  /  [żądanie](#response-10))
  - [Aktualizuj grupę [Dodaj członków]](#update-group-add-members) ([Odpowiedź](#request-11)  /  [na żądanie](#response-11))
  - [Update Group [Remove Members]](#update-group-remove-members) [(Odpowiedź na](#request-12)  /  [żądanie)](#response-12)
  - [Usuń grupę](#delete-group) ([Odpowiedź](#request-13)  /  [na żądanie](#response-13))

[Odnajdywanie schematów](#schema-discovery)
  - [Odnajdywanie schematu](#discover-schema) [(odpowiedź](#request-15)  /  [na żądanie)](#response-15)

### <a name="user-operations"></a>Operacje użytkownika

* Użytkownicy mogą być pytani według `userName` atrybutów `email[type eq "work"]` lub .  

#### <a name="create-user"></a>Utwórz użytkownika

##### <a name="request"></a>Żądanie

*POST /Users*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Reakcja

*Utworzono protokół HTTP/1.1 201*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>Pobieranie użytkownika

###### <a name="request"></a><a name="request-1"></a>Żądanie
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>Odpowiedź (znaleziono użytkownika)
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

###### <a name="request"></a>Żądanie
*GET /Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Odpowiedź (Nie znaleziono użytkownika. Należy pamiętać, że szczegóły nie są wymagane, a tylko stan).

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Pobierz użytkownika według zapytania

##### <a name="request"></a><a name="request-2"></a>Żądanie

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>Odpowiedzi

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="get-user-by-query---zero-results"></a>Uzyskiwanie użytkownika według zapytania — zero wyników

##### <a name="request"></a><a name="request-3"></a>Żądanie

*GET /Users?filter=userName eq "nieistniejący użytkownik"*

##### <a name="response"></a><a name="response-3"></a>Odpowiedzi

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-user-multi-valued-properties"></a>Aktualizowanie użytkownika [właściwości o wielu wartościach]

##### <a name="request"></a><a name="request-4"></a>Żądanie

*PATCH /Users/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response"></a><a name="response-4"></a>Odpowiedzi

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Aktualizowanie użytkownika [właściwości jedno valued]

##### <a name="request"></a><a name="request-5"></a>Żądanie

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response"></a><a name="response-5"></a>Odpowiedzi

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

### <a name="disable-user"></a>Wyłączanie użytkownika

##### <a name="request"></a><a name="request-14"></a>Żądanie

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response"></a><a name="response-14"></a>Odpowiedzi

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>Usuń użytkownika

##### <a name="request"></a><a name="request-6"></a>Żądanie

*DELETE /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>Odpowiedzi

*HTTP/1.1 204 Brak zawartości*

### <a name="group-operations"></a>Operacje na grupach

* Grupy należy zawsze tworzyć z pustą listą elementów członkowskich.
* Grupy mogą być wyszukiwane za pomocą `displayName` atrybutu .
* Aktualizacja żądania PATCH grupy powinna zwrócić *http 204 brak zawartości* w odpowiedzi. Zwracanie treści z listą wszystkich elementów członkowskich nie jest zalecane.
* Nie trzeba obsługiwać zwracania wszystkich członków grupy.

#### <a name="create-group"></a>Tworzenie grupy

##### <a name="request"></a><a name="request-7"></a>Żądanie

*POST /Groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a><a name="response-7"></a>Odpowiedzi

*Utworzono protokół HTTP/1.1 201*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Pobieranie grupy

##### <a name="request"></a><a name="request-8"></a>Żądanie

*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>Odpowiedzi
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Pobierz grupę według displayName

##### <a name="request"></a><a name="request-9"></a>Żądanie
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>Odpowiedzi

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>Aktualizacja grupy [atrybuty inne niż członkowskie]

##### <a name="request"></a><a name="request-10"></a>Żądanie

*PATCH /Groups/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response"></a><a name="response-10"></a>Odpowiedzi

*HTTP/1.1 204 Brak zawartości*

### <a name="update-group-add-members"></a>Aktualizacja grupy [Dodaj członków]

##### <a name="request"></a><a name="request-11"></a>Żądanie

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-11"></a>Odpowiedzi

*HTTP/1.1 204 Brak zawartości*

#### <a name="update-group-remove-members"></a>Zaktualizuj grupę [Usuń członków]

##### <a name="request"></a><a name="request-12"></a>Żądanie

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-12"></a>Odpowiedzi

*HTTP/1.1 204 Brak zawartości*

#### <a name="delete-group"></a>Usuń grupę

##### <a name="request"></a><a name="request-13"></a>Żądanie

*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>Odpowiedzi

*HTTP/1.1 204 Brak zawartości*

### <a name="schema-discovery"></a>Odnajdywanie schematów
#### <a name="discover-schema"></a>Odnajdywanie schematu

##### <a name="request"></a><a name="request-15"></a>Żądanie
*GET /Schemas* 
##### <a name="response"></a><a name="response-15"></a>Odpowiedzi
*HTTP/1.1 200 OK*
```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:ListResponse"
    ],
    "itemsPerPage": 50,
    "startIndex": 1,
    "totalResults": 3,
    "Resources": [
  {
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Schema"],
    "id" : "urn:ietf:params:scim:schemas:core:2.0:User",
    "name" : "User",
    "description" : "User Account",
    "attributes" : [
      {
        "name" : "userName",
        "type" : "string",
        "multiValued" : false,
        "description" : "Unique identifier for the User, typically
used by the user to directly authenticate to the service provider.
Each User MUST include a non-empty userName value.  This identifier
MUST be unique across the service provider's entire set of Users.
REQUIRED.",
        "required" : true,
        "caseExact" : false,
        "mutability" : "readWrite",
        "returned" : "default",
        "uniqueness" : "server"
      },                
    ],
    "meta" : {
      "resourceType" : "Schema",
      "location" :
        "/v2/Schemas/urn:ietf:params:scim:schemas:core:2.0:User"
    }
  },
  {
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Schema"],
    "id" : "urn:ietf:params:scim:schemas:core:2.0:Group",
    "name" : "Group",
    "description" : "Group",
    "attributes" : [
      {
        "name" : "displayName",
        "type" : "string",
        "multiValued" : false,
        "description" : "A human-readable name for the Group.
REQUIRED.",
        "required" : false,
        "caseExact" : false,
        "mutability" : "readWrite",
        "returned" : "default",
        "uniqueness" : "none"
      },
    ],
    "meta" : {
      "resourceType" : "Schema",
      "location" :
        "/v2/Schemas/urn:ietf:params:scim:schemas:core:2.0:Group"
    }
  },
  {
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Schema"],
    "id" : "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
    "name" : "EnterpriseUser",
    "description" : "Enterprise User",
    "attributes" : [
      {
        "name" : "employeeNumber",
        "type" : "string",
        "multiValued" : false,
        "description" : "Numeric or alphanumeric identifier assigned
to a person, typically based on order of hire or association with an
organization.",
        "required" : false,
        "caseExact" : false,
        "mutability" : "readWrite",
        "returned" : "default",
        "uniqueness" : "none"
      },
    ],
    "meta" : {
      "resourceType" : "Schema",
      "location" :
"/v2/Schemas/urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
    }
  }
]
}
```

### <a name="security-requirements"></a>Wymagania dotyczące zabezpieczeń
**Wersje protokołu TLS**

Jedyne dopuszczalne wersje protokołu TLS to TLS 1.2 i TLS 1.3. Inne wersje TLS nie są dozwolone. Nie jest dozwolona żadna wersja protokołu SSL. 
- Klucze RSA muszą mieć co najmniej 2048 bitów.
- Klucze ECC muszą mieć co najmniej 256 bitów i być generowane przy użyciu zatwierdzonej krzywej wielokropka

**Długości kluczy**

Wszystkie usługi muszą używać certyfikatów X.509 generowanych przy użyciu kluczy kryptograficznych o wystarczającej długości, co oznacza:

**Pakiety szyfrowania**

Wszystkie usługi muszą być skonfigurowane do używania następujących mechanizmów szyfrowania w dokładnej kolejności określonej poniżej. Należy pamiętać, że jeśli masz tylko certyfikat RSA, zainstalowane pakiety szyfrowania ECDSA nie mają żadnego wpływu. </br>

Minimalny pasek mechanizmów szyfrowania TLS 1.2:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

### <a name="ip-ranges"></a>Zakresy adresów IP
Usługa aprowizowania Usługi Azure AD działa obecnie w ramach zakresów adresów IP dla usługi AzureActiveDirectory, jak podano [tutaj.](https://www.microsoft.com/download/details.aspx?id=56519&WT.mc_id=rss_alldownloads_all) Możesz dodać zakresy adresów IP wymienione w tagu AzureActiveDirectory, aby zezwolić na ruch z usługi aprowacji Azure AD do aplikacji. Należy pamiętać, że należy uważnie przejrzeć listę zakresów adresów IP dla adresów obliczanych. Adres, taki jak "40.126.25.32", może być reprezentowany na liście zakresów adresów IP jako "40.126.0.0/18". Możesz również programowo pobrać listę zakresów adresów IP przy użyciu następującego interfejsu [API](/rest/api/virtualnetwork/servicetags/list).

## <a name="build-a-scim-endpoint"></a>Tworzenie punktu końcowego standardu SCIM

Teraz, po zaprojektowaniu schematu i poznaniu implementacji standardu SCIM usługi Azure AD, możesz rozpocząć tworzenie punktu końcowego SCIM. Zamiast rozpoczynać od podstaw i samodzielnie budować implementację, możesz polegać na wielu open source SCIM opublikowanych przez społeczność SCIM.

Aby uzyskać wskazówki dotyczące tworzenia punktu końcowego SCIM, w tym przykłady, zobacz [Develop a sample SCIM endpoint (Opracowywanie przykładowego punktu końcowego SCIM).](use-scim-to-build-users-and-groups-endpoints.md)

Przykład open source kodu referencyjnego platformy .NET [Core](https://aka.ms/SCIMReferenceCode) opublikowany przez zespół aprowizowania usługi Azure AD to jeden z takich zasobów, który może szybko rozpocząć tworzenie aplikacji. Po s zbudowaniu punktu końcowego SCIM należy go przetestować. Możesz użyć kolekcji testów [postman dostarczonych](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) jako część kodu referencyjnego lub uruchomić przykładowe żądania/odpowiedzi podane [powyżej.](#user-operations)  

   > [!Note]
   > Kod referencyjny ma pomóc ci rozpocząć tworzenie punktu końcowego SCIM i jest dostarczany "W STANIE, W ILE JEST". Wkład społeczności jest mile widziany, aby pomóc kompilować i konserwować kod.

Rozwiązanie składa się z dwóch projektów: _Microsoft.SCIM_ i _Microsoft.SCIM.WebHostSample._

Projekt _Microsoft.SCIM_ to biblioteka, która definiuje składniki usługi internetowej zgodne ze specyfikacją SCIM. Deklaruje interfejs _Microsoft.SCIM.IProvider_, żądania są tłumaczone na wywołania metod dostawcy, które zostałyby zaprogramowane do działania w magazynie tożsamości.

![Podział: żądanie przetłumaczone na wywołania metod dostawcy](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

Projekt _Microsoft.SCIM.WebHostSample_ jest Visual Studio ASP.NET Core Web Application na podstawie _pustego_ szablonu. Dzięki temu przykładowy kod można wdrożyć jako autonomiczny, hostowany w kontenerach lub w Internet Information Services. Implementuje również interfejs _Microsoft.SCIM.IProvider_ przechowujący klasy w pamięci jako przykładowy magazyn tożsamości.

```csharp
    public class Startup
    {
        ...
        public IMonitor MonitoringBehavior { get; set; }
        public IProvider ProviderBehavior { get; set; }

        public Startup(IWebHostEnvironment env, IConfiguration configuration)
        {
            ...
            this.MonitoringBehavior = new ConsoleMonitor();
            this.ProviderBehavior = new InMemoryProvider();
        }
        ...
```

### <a name="building-a-custom-scim-endpoint"></a>Tworzenie niestandardowego punktu końcowego standardu SCIM

Usługa SCIM musi mieć adres HTTP i certyfikat uwierzytelniania serwera, których główny urząd certyfikacji jest jedną z następujących nazw:

* CNNIC
* Comodo
* Cybertrust
* DigiCert
* Geotrust
* GlobalSign
* Go Daddy
* Verisign
* WoSign
* Główny urząd certyfikacji DST X3

Ten zestaw .NET Core SDK zawiera certyfikat dewelopera HTTPS, który może być używany podczas opracowywania. Certyfikat jest instalowany w ramach pierwszego uruchomienia. W zależności od sposobu uruchamiania aplikacji ASP.NET Core aplikacja internetowa nasłuchuje na innym porcie:

* Microsoft.SCIM.WebHostSample: https://localhost:5001
* IIS Express: https://localhost:44359/

Aby uzyskać więcej informacji na temat protokołu HTTPS w programie ASP.NET Core, użyj następującego linku: Enforce HTTPS in ASP.NET Core (Wymuszanie [protokołu HTTPS w programie ASP.NET Core)](/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>Obsługa uwierzytelniania punktu końcowego

Żądania od Azure Active Directory zawierają token element bearer OAuth 2.0. Każda usługa odbieraca żądanie powinna uwierzytelnić wystawcę jako Azure Active Directory dla oczekiwanej Azure Active Directory dzierżawy.

W tokenie wystawca jest identyfikowany przez oświadczenie iss, takie jak `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"` . W tym przykładzie adres podstawowy wartości oświadczenia , identyfikuje Azure Active Directory jako wystawcę, natomiast segment adresu względnego `https://sts.windows.net` _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_ jest unikatowym identyfikatorem dzierżawy usługi Azure Active Directory, dla której wystawiono token.

Odbiorcy tokenu będą identyfikatorem szablonu aplikacji w galerii. Każda aplikacja zarejestrowana w jednej dzierżawie może otrzymać to samo oświadczenie z żądaniami `iss` SCIM. Identyfikator szablonu aplikacji dla wszystkich aplikacji niestandardowych to _8adf8e6e-67b2-4cf2-a259-e3dc5476c621._ Token wygenerowany przez usługę aprowizatora Azure AD powinien być używany tylko do testowania. Nie należy jej używać w środowiskach produkcyjnych.

W przykładowym kodzie żądania są uwierzytelniane przy użyciu pakietu Microsoft.AspNetCore.Authentication.JwtBearer. Poniższy kod wymusza uwierzytelnianie żądań do dowolnego punktu końcowego usługi przy użyciu tokenu okaziciela wystawionego przez Azure Active Directory dla określonej dzierżawy:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                ...
            }
            else
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.Authority = " https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/";
                        options.Audience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621";
                        ...
                    });
            }
            ...
        }

        public void Configure(IApplicationBuilder app)
        {
            ...
            app.UseAuthentication();
            app.UseAuthorization();
            ...
       }
```

Token bearer jest również wymagany do korzystania z dostarczonych testów [postman i](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) wykonywania debugowania lokalnego przy użyciu hosta lokalnego. Przykładowy kod używa ASP.NET Core, aby zmienić opcje uwierzytelniania na etapie projektowania i umożliwić korzystanie z tokenu z podpisem własnym.

Aby uzyskać więcej informacji na temat wielu środowisk w programie ASP.NET Core, zobacz Use multiple environments in ASP.NET Core (Używanie wielu środowisk w [programie ASP.NET Core).](/aspnet/core/fundamentals/environments)

Poniższy kod wymusza uwierzytelnianie żądań do dowolnego punktu końcowego usługi przy użyciu tokenu element bearer podpisanego przy użyciu klucza niestandardowego:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    if (_env.IsDevelopment())
    {
        services.AddAuthentication(options =>
        {
            options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
            options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
            options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
        })
        .AddJwtBearer(options =>
        {
            options.TokenValidationParameters =
                new TokenValidationParameters
                {
                    ValidateIssuer = false,
                    ValidateAudience = false,
                    ValidateLifetime = false,
                    ValidateIssuerSigningKey = false,
                    ValidIssuer = "Microsoft.Security.Bearer",
                    ValidAudience = "Microsoft.Security.Bearer",
                    IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"))
                };
        });
    }
...
```

Wyślij żądanie GET do kontrolera tokenu w celu uzyskania prawidłowego tokenu bearer. Metoda _GenerateJSONWebToken_ jest odpowiedzialna za utworzenie tokenu zgodnego z parametrami skonfigurowanymi do tworzenia aplikacji:

```csharp
private string GenerateJSONWebToken()
{
    // Create token key
    SymmetricSecurityKey securityKey =
        new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"));
    SigningCredentials credentials =
        new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

    // Set token expiration
    DateTime startTime = DateTime.UtcNow;
    DateTime expiryTime = startTime.AddMinutes(120);

    // Generate the token
    JwtSecurityToken token =
        new JwtSecurityToken(
            "Microsoft.Security.Bearer",
            "Microsoft.Security.Bearer",
            null,
            notBefore: startTime,
            expires: expiryTime,
            signingCredentials: credentials);

    string result = new JwtSecurityTokenHandler().WriteToken(token);
    return result;
}
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Obsługa aprowizowania i coprowizowania użytkowników

***Przykład 1. Wykonywanie zapytania o pasującego użytkownika w usłudze***

Azure Active Directory (AAD) wysyła do usługi zapytanie o użytkownika o wartość atrybutu pasującą do wartości atrybutu `externalId` mailNickname użytkownika w usłudze AAD. Zapytanie jest wyrażane jako żądanie http (Hypertext Transfer Protocol), takie jak w tym przykładzie, gdzie jyoung jest przykładem mailNickname użytkownika w Azure Active Directory.

>[!NOTE]
> Jest to tylko przykład. Nie wszyscy użytkownicy będą mieć atrybut mailNickname, a wartość użytkownika może nie być unikatowa w katalogu. Ponadto atrybut używany do dopasowywania (w tym przypadku jest ) można skonfigurować w mapowaniach atrybutów `externalId` [usługi AAD.](customize-application-attributes.md)

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

W przykładowym kodzie żądanie jest tłumaczone na wywołanie metody QueryAsync dostawcy usługi. Oto podpis tej metody: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.Resource is defined in 
// Microsoft.SCIM.Schemas.  
// Microsoft.SCIM.IQueryParameters is defined in 
// Microsoft.SCIM.Protocol.  

Task<Resource[]> QueryAsync(IRequest<IQueryParameters> request);
```

W przykładowym zapytaniu dla użytkownika z podaną wartością atrybutu wartości argumentów przekazanych do metody `externalId` QueryAsync są:

* Parametry. AlternateFilters.Count: 1
* Parametry. AlternateFilters.ElementAt(0). AttributePath: "externalId"
* Parametry. AlternateFilters.ElementAt(0). ComparisonOperator: ComparisonOperator.Equals
* Parametry. AlternateFilter.ElementAt(0). ComparisonValue: "jyoung"

***Przykład 2. Aprowizuj użytkownika***

Jeśli odpowiedź na zapytanie do usługi internetowej dla użytkownika z wartością atrybutu odpowiadającą wartości atrybutu mailNickname użytkownika nie zwraca żadnych użytkowników, usługa AAD żąda, aby usługa aprowizowała użytkownika odpowiadającego temu w usłudze `externalId` AAD.  Oto przykład takiego żądania: 

```
POST https://.../scim/Users HTTP/1.1
Authorization: Bearer ...
Content-type: application/scim+json
{
   "schemas":
   [
     "urn:ietf:params:scim:schemas:core:2.0:User",
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
   "externalId":"jyoung",
   "userName":"jyoung@testuser.com",
   "active":true,
   "addresses":null,
   "displayName":"Joy Young",
   "emails": [
     {
       "type":"work",
       "value":"jyoung@Contoso.com",
       "primary":true}],
   "meta": {
     "resourceType":"User"},
    "name":{
     "familyName":"Young",
     "givenName":"Joy"},
   "phoneNumbers":null,
   "preferredLanguage":null,
   "title":null,
   "department":null,
   "manager":null}
```

W przykładowym kodzie żądanie jest tłumaczone na wywołanie metody CreateAsync dostawcy usługi. Oto podpis tej metody: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.Resource is defined in 
// Microsoft.SCIM.Schemas.  

Task<Resource> CreateAsync(IRequest<Resource> request);
```

W żądaniu aprowizacji użytkownika wartością argumentu zasobu jest wystąpienie klasy Microsoft.SCIM.Core2EnterpriseUser zdefiniowanej w bibliotece Microsoft.SCIM.Schemas.  Jeśli żądanie aprowizacji użytkownika powiedzie się, implementacja metody zwróci wystąpienie klasy Microsoft.SCIM.Core2EnterpriseUser z wartością właściwości Identyfikator ustawioną na unikatowy identyfikator nowo aprowizacji użytkownika.  

***Przykład 3. Wykonywanie zapytania o bieżący stan użytkownika*** 

Aby zaktualizować użytkownika, który istnieje w magazynie tożsamości frontowym przez standard SCIM, Azure Active Directory żądanie bieżącego stanu tego użytkownika z usługi za pomocą żądania, takiego jak: 

```
GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
Authorization: Bearer ...
```

W przykładowym kodzie żądanie jest tłumaczone na wywołanie metody RetrieveAsync dostawcy usługi. Oto podpis tej metody: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.Resource and 
// Microsoft.SCIM.IResourceRetrievalParameters 
// are defined in Microsoft.SCIM.Schemas 

Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

W przykładzie żądania pobrania bieżącego stanu użytkownika wartości właściwości obiektu podane jako wartość argumentu parameters są następujące: 
  
* Identyfikator: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***Przykład 4. Wykonywanie zapytania o wartość atrybutu odwołania do zaktualizowania*** 

Jeśli atrybut odwołania ma zostać zaktualizowany, usługa Azure Active Directory wysyła do usługi zapytanie w celu ustalenia, czy bieżąca wartość atrybutu odwołania w magazynie tożsamości frontionem usługi jest już taka, jak wartość tego atrybutu w Azure Active Directory. W przypadku użytkowników jedynym atrybutem, którego bieżąca wartość jest pytana w ten sposób, jest atrybut menedżera. Oto przykład żądania w celu określenia, czy atrybut menedżera obiektu użytkownika ma obecnie określoną wartość: W przykładowym kodzie żądanie jest tłumaczone na wywołanie metody QueryAsync dostawcy usługi. Wartość właściwości obiektu podanego jako wartość argumentu parameters jest następująca: 
  
* Parametry. AlternateFilters.Count: 2
* Parametry. AlternateFilters.ElementAt(x). AttributePath: "ID"
* Parametry. AlternateFilters.ElementAt(x). ComparisonOperator: ComparisonOperator.Equals
* Parametry. AlternateFilter.ElementAt(x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* Parametry. AlternateFilters.ElementAt(y). AttributePath: "manager"
* Parametry. AlternateFilters.ElementAt(y). ComparisonOperator: ComparisonOperator.Equals
* Parametry. AlternateFilter.ElementAt(y). ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* Parametry. RequestedAttributePaths.ElementAt(0): "ID"
* Parametry. SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

W tym przypadku wartość indeksu x może być 0, a wartość indeksu y może być 1 lub wartość x może być 1, a wartość y może być 0, w zależności od kolejności wyrażeń parametru zapytania filtru.   

***Przykład 5. Żądanie z usługi Azure AD do usługi SCIM o zaktualizowanie użytkownika*** 

Oto przykład żądania z usługi Azure Active Directory do usługi SCIM w celu zaktualizowania użytkownika: 

```
PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
Authorization: Bearer ...
Content-type: application/scim+json
{
    "schemas": 
    [
      "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations":
    [
      {
        "op":"Add",
        "path":"manager",
        "value":
          [
            {
              "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
              "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
```

W przykładowym kodzie żądanie jest tłumaczone na wywołanie metody UpdateAsync dostawcy usługi. Oto podpis tej metody: 

```csharp
// System.Threading.Tasks.Tasks and 
// System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in
// Microsoft.SCIM.Service.
// Microsoft.SCIM.IPatch, 
// is defined in Microsoft.SCIM.Protocol. 

Task UpdateAsync(IRequest<IPatch> request);
```

W przykładzie żądania aktualizacji użytkownika obiekt podany jako wartość argumentu patch ma następujące wartości właściwości: 

|Argument|Wartość|
|-|-|
|ResourceIdentifier.Identifier|"54D382A4-2050-4C03-94D1-E769F1D15682"|
|ResourceIdentifier.SchemaIdentifier|"urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"|
|(PatchRequest as PatchRequest2). Operations.Count|1|
|(PatchRequest as PatchRequest2). Operations.ElementAt(0). Operationname|OperationName.Add|
|(PatchRequest as PatchRequest2). Operations.ElementAt(0). Path.AttributePath|"menedżer"|
|(PatchRequest as PatchRequest2). Operations.ElementAt(0). Value.Count|1|
|(PatchRequest as PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Odwołanie|http://.../scim/Users/2819c223-7f76-453a-919d-413861904646|
|(PatchRequest as PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Wartość| 2819c223-7f76-453a-919d-413861904646|

***Przykład 6. Coprowizuj użytkownika***

Aby coprowizować użytkownika z magazynu tożsamości frontowego przez usługę SCIM, usługa AAD wysyła żądanie, takie jak:

```
DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
Authorization: Bearer ...
```

W przykładowym kodzie żądanie jest tłumaczone na wywołanie metody DeleteAsync dostawcy usługi. Oto podpis tej metody: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.IResourceIdentifier, 
// is defined in Microsoft.SCIM.Protocol. 

Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

Obiekt podany jako wartość argumentu resourceIdentifier ma następujące wartości właściwości w przykładzie żądania coprowizować użytkownika: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="integrate-your-scim-endpoint-with-the-aad-scim-client"></a>Integrowanie punktu końcowego SCIM z klientem AAD SCIM

Usługę Azure AD można skonfigurować tak, aby automatycznie aprowizować przypisanych użytkowników i grupy do aplikacji, które implementują określony profil protokołu [SCIM 2.0.](https://tools.ietf.org/html/rfc7644) Szczegółowe informacje o profilu zostały udokumentowane w tesłudze Understand the Azure AD SCIM implementation (Informacje [o implementacji standardu SCIM usługi Azure AD).](#understand-the-aad-scim-implementation)

Skontaktuj się z dostawcą aplikacji lub dokumentacją dostawcy aplikacji, aby uzyskać instrukcje zgodności z tymi wymaganiami.

> [!IMPORTANT]
> Implementacja standardu SCIM w usłudze Azure AD jest zbudowana w oparciu o usługę aprowowania użytkowników usługi Azure AD, która ma na celu stałe synchronizowanie użytkowników między usługą Azure AD i aplikacją docelową, a także implementuje bardzo konkretny zestaw standardowych operacji. Ważne jest, aby zrozumieć te zachowania, aby zrozumieć zachowanie klienta standardu SCIM usługi Azure AD. Aby uzyskać więcej informacji, zobacz sekcję [Provisioning cycles: Initial and incremental (Cykle](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) inicjowania obsługi administracyjnej: początkowe i przyrostowe) w [tece How provisioning works (Jak działa aprowizowanie).](how-provisioning-works.md)

### <a name="getting-started"></a>Wprowadzenie

Aplikacje, które obsługują profil SCIM opisany w tym artykule, można połączyć z usługą Azure Active Directory przy użyciu funkcji "aplikacja spoza galerii" w galerii aplikacji usługi Azure AD. Po nadaniu połączenia usługa Azure AD uruchamia proces synchronizacji co 40 minut, w którym wysyła zapytanie do punktu końcowego SCIM aplikacji o przypisanych użytkowników i grupy oraz tworzy lub modyfikuje je zgodnie ze szczegółami przypisania.

**Aby połączyć aplikację, która obsługuje standard SCIM:**

1. Zaloguj się do [portalu AAD.](https://aad.portal.azure.com) Pamiętaj, że możesz uzyskać dostęp do bezpłatnej wersji próbnej Azure Active Directory licencji P2, logując się do [programu dewelopera](https://developer.microsoft.com/office/dev-program)
1. W **okienku po** lewej stronie wybierz pozycję Aplikacje dla przedsiębiorstw. Zostanie wyświetlona lista wszystkich skonfigurowanych aplikacji, w tym aplikacji dodanych z galerii.
1. Wybierz **pozycję + Nowa aplikacja**+ Utwórz własną  >  **aplikację.**
1. Wprowadź nazwę aplikacji, wybierz opcję *"integruj* inną aplikację, która nie znajduje się w galerii", a następnie wybierz pozycję **Dodaj,** aby utworzyć obiekt aplikacji. Nowa aplikacja zostanie dodana do listy aplikacji dla przedsiębiorstw i zostanie otwarta na ekranie zarządzania aplikacjami.

   ![Zrzut ekranu przedstawiający galerię aplikacji usługi Azure AD w ](media/use-scim-to-provision-users-and-groups/scim-figure-2b-1.png)
    *usłudze Azure AD*

   > [!NOTE]
   > Jeśli używasz starego środowisko galerii aplikacji, postępuj zgodnie z poniższym przewodnikiem po ekranie.
   
   ![Zrzut ekranu przedstawiający stare środowisko galerii aplikacji usługi Azure AD w usłudze ](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)
    *Azure AD*

1. Na ekranie zarządzania aplikacją wybierz pozycję **Aprowizowanie** w panelu po lewej stronie.
1. W menu **Tryb aprowowania** wybierz pozycję **Automatyczne.**

   ![Przykład: strona aprowizowania aplikacji w Azure Portal](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Konfigurowanie aprowizowania w Azure Portal*

1. W polu **Adres URL** dzierżawy wprowadź adres URL punktu końcowego SCIM aplikacji. Przykład: `https://api.contoso.com/scim/`
1. Jeśli punkt końcowy SCIM wymaga tokenu okazcy OAuth od wystawcy innego niż usługa Azure AD, skopiuj wymagany token okaziciela OAuth do opcjonalnego pola **Tajny token.** Jeśli to pole zostanie pozostawione puste, usługa Azure AD będzie zawierać token element bearer OAuth wystawiony z usługi Azure AD przy każdym żądaniu. Aplikacje, które używają usługi Azure AD jako dostawcy tożsamości, mogą walidować token wystawiony przez usługę Azure AD. 
   > [!NOTE]
   > Nie zaleca się ***pozostawiania*** tego pola pustego i polegania na tokenie wygenerowanym przez usługę Azure AD. Ta opcja jest dostępna głównie do celów testowych.
1. Wybierz **pozycję Testuj** połączenie, aby Azure Active Directory próbę nawiązania połączenia z punktem końcowym SCIM. Jeśli próba nie powiedzie się, zostaną wyświetlone informacje o błędzie.  

    > [!NOTE]
    > **Połączenie testowe** wysyła zapytanie do punktu końcowego SCIM o użytkownika, który nie istnieje, używając losowego identyfikatora GUID jako pasującej właściwości wybranej w konfiguracji usługi Azure AD. Oczekiwana poprawna odpowiedź to HTTP 200 OK z pustym komunikatem ListResponse standardu SCIM.

1. Jeśli próby nawiązania połączenia z aplikacją zakończyły się pomyślnie, wybierz pozycję **Zapisz,** aby zapisać poświadczenia administratora.
1. W **sekcji Mapowania** znajdują się dwa dostępne do wyboru zestawy mapowań [atrybutów:](customize-application-attributes.md)jeden dla obiektów użytkownika i jeden dla obiektów grupy. Wybierz każdy z nich, aby przejrzeć atrybuty, które są synchronizowane Azure Active Directory z aplikacją. Atrybuty wybrane jako Właściwości **dopasowania** są używane do dopasowywania użytkowników i grup w aplikacji na czas operacji aktualizacji. Wybierz **pozycję Zapisz,** aby zatwierdzić wszelkie zmiany.

    > [!NOTE]
    > Opcjonalnie można wyłączyć synchronizację obiektów grup, wyłączając mapowanie "grup".

1. W **obszarze** Ustawienia **pole** Zakres określa, którzy użytkownicy i grupy są synchronizowane. Wybierz **pozycję Synchronizuj tylko przypisanych użytkowników i grupy** (zalecane), aby synchronizować tylko użytkowników i grupy przypisane na karcie Użytkownicy i **grupy.**
1. Po zakończeniu konfiguracji ustaw stan **aprowowania** na **Wł.**.
1. Wybierz **pozycję Zapisz,** aby uruchomić usługę aprowizowania usługi Azure AD.
1. W przypadku synchronizowania tylko przypisanych użytkowników i grup  (zalecane) wybierz kartę Użytkownicy i grupy i przypisz użytkowników lub grupy, które chcesz zsynchronizować.

Po zakończeniu początkowego cyklu  możesz wybrać pozycję Dzienniki aprowowania w panelu po lewej stronie, aby monitorować postęp, który pokazuje wszystkie akcje wykonywane przez usługę aprowizowania w aplikacji. Aby uzyskać więcej informacji na temat odczytywania dzienników aprowizowania usługi Azure AD, zobacz [Reporting on automatic user account provisioning](check-status-user-account-provisioning.md)(Raportowanie automatycznego aprowizowania kont użytkowników).

> [!NOTE]
> Początkowy cykl trwa dłużej niż późniejsze synchronizacje, które odbywają się co około 40 minut, o ile usługa jest uruchomiona.

## <a name="publish-your-application-to-the-aad-application-gallery"></a>Publikowanie aplikacji w galerii aplikacji WAD

W przypadku tworzenia aplikacji, która będzie używana przez więcej niż jedną dzierżawę, możesz udostępnić ją w galerii aplikacji usługi Azure AD. Dzięki temu organizacje mogą łatwo odnajdywać aplikację i konfigurować aprowizowanie. Publikowanie aplikacji w galerii usługi Azure AD i udostępnianie jej innym osobom jest łatwe. Zapoznaj się z krokami [tutaj.](../develop/v2-howto-app-gallery-listing.md) Firma Microsoft pomoże Ci zintegrować Twoją aplikację z naszą galerią, [](../saas-apps/tutorial-list.md) przetestować punkt końcowy i wydać dokumentację dołączania dla klientów do użycia.

### <a name="gallery-onboarding-checklist"></a>Lista kontrolna dołączania do galerii
Lista kontrolna umożliwia szybkie dołączanie aplikacji, a klienci mają bezproblemowe środowisko wdrażania. Informacje zostaną zebrane podczas dołączania do galerii. 
> [!div class="checklist"]
> * Obsługa punktu końcowego użytkownika i grupy standardu [SCIM 2.0](#understand-the-aad-scim-implementation) (wymagany jest tylko jeden z nich, ale oba są zalecane)
> * Obsługa co najmniej 25 żądań na sekundę na dzierżawę w celu zapewnienia, że użytkownicy i grupy będą aprowizowane i coprowizowane bez opóźnień (wymagane)
> * Ustanawianie kontaktów inżynierów i pomocy technicznej w celu pokierowania klientami po dojechanie do galerii (wymagane)
> * 3. Nie wygasające poświadczenia testowe dla aplikacji (wymagane)
> * Obsługa udzielania kodu autoryzacji OAuth lub tokenu długotrwałego zgodnie z poniższym opisem (wymagane)
> * Ustanawianie punktu kontaktu inżynieryjnego i pomocy technicznej w celu obsługi klientów po do dołączaniu do galerii (wymagane)
> * [Obsługa odnajdywania schematów (wymagane)](https://tools.ietf.org/html/rfc7643#section-6)
> * Obsługa aktualizowania wielu członkoseń w grupach za pomocą jednej poprawki PATCH
> * Publiczne dokumentować punkt końcowy SCIM

### <a name="authorization-to-provisioning-connectors-in-the-application-gallery"></a>Autoryzacja do aprowizowania łączników w galerii aplikacji
Specyfikacja SCIM nie definiuje specyficznego dla standardu SCIM schematu uwierzytelniania i autoryzacji i opiera się na użyciu istniejących standardów branżowych.

|Metoda autoryzacji|Zalety|Wady|Pomoc techniczna|
|--|--|--|--|
|Nazwa użytkownika i hasło (nie jest zalecane ani obsługiwane przez usługę Azure AD)|Łatwe do zaimplementowania|Niezabezpieczone — [Twój $word Pa$ nie ma znaczenia](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Obsługiwane w przypadku aplikacji z galerii. Nie jest obsługiwana w przypadku aplikacji spoza galerii.|
|Długotrwały token bearer|Tokeny długoterminowe nie wymagają, aby użytkownik był obecny. Są one łatwe do użycia przez administratorów podczas konfigurowania aprowizowania.|Długoterminowe tokeny mogą być trudne do udostępnienia administratorowi bez użycia niezabezpieczonych metod, takich jak poczta e-mail. |Obsługiwane w przypadku aplikacji z galerii i spoza galerii. |
|Przyznawanie kodu autoryzacji OAuth|Tokeny dostępu są znacznie krótsze niż hasła i mają zautomatyzowany mechanizm odświeżania, który nie ma tokenów elementami niosącymi długi czas życia.  Podczas początkowej autoryzacji musi być obecny rzeczywisty użytkownik, co dodaje poziom odpowiedzialności. |Wymaga, aby użytkownik był obecny. Jeśli użytkownik opuści organizację, token jest nieprawidłowy i należy ponownie ukończyć autoryzację.|Obsługiwane w przypadku aplikacji z galerii, ale nie aplikacji spoza galerii. Można jednak podać token dostępu w interfejsie użytkownika jako token tajny na potrzeby testowania krótkoterminowego. Obsługa przyznawania kodu OAuth w przypadku aplikacji spoza galerii znajduje się na naszej zaległości, oprócz obsługi konfigurowalnych adresów URL uwierzytelniania/tokenu w aplikacji galerii.|
|Przyznawanie poświadczeń klienta OAuth|Tokeny dostępu są znacznie krótsze niż hasła i mają zautomatyzowany mechanizm odświeżania, który nie ma długotrwałych tokenów okaziciela. Zarówno udzielenie kodu autoryzacji, jak i poświadczenia klienta tworzą ten sam typ tokenu dostępu, więc przenoszenie między tymi metodami jest niewidoczne dla interfejsu API.  Aprowizowanie można całkowicie zautomatyzować, a nowe tokeny mogą być żądane w trybie dyskretnym bez interakcji z użytkownikiem. ||Nie jest obsługiwane w przypadku aplikacji spoza galerii. Pomoc techniczna znajduje się na naszej zaległości.|

> [!NOTE]
> Nie zaleca się pozostawiania pola tokenu pustego w niestandardowym interfejsie użytkownika aplikacji konfiguracji aprowzatora usługi AAD. Wygenerowany token jest dostępny głównie do celów testowych.

### <a name="oauth-code-grant-flow"></a>Przepływ przyznawania kodu OAuth

Usługa aprowizowania [](https://tools.ietf.org/html/rfc6749#page-24) obsługuje przyznawanie kodu autoryzacji, a po przesłaniu żądania opublikowania aplikacji w galerii nasz zespół pomoże Ci zebrać następujące informacje:

- **Adres URL autoryzacji**, adres URL klienta w celu uzyskania autoryzacji od właściciela zasobu za pośrednictwem przekierowania użytkownik-agent. Użytkownik jest przekierowywany do tego adresu URL w celu autoryzowania dostępu. 

- **Adres URL wymiany tokenu**, adres URL używany przez klienta do wymiany przyznawania autoryzacji dla tokenu dostępu, zazwyczaj z uwierzytelnianiem klienta.

- **Identyfikator klienta**, serwer autoryzacji wydaje zarejestrowanemu klientowi identyfikator klienta, który jest unikatowym ciągiem reprezentującym informacje o rejestracji dostarczone przez klienta.  Identyfikator klienta nie jest kluczem tajnym; Jest on ujawniony właścicielowi zasobu i **nie może być** używany samodzielnie do uwierzytelniania klienta.  

- **Klucz tajny** klienta , klucz tajny wygenerowany przez serwer autoryzacji, który powinien być unikatową wartością znaną tylko serwerowi autoryzacji. 

> [!NOTE]
> Adres **URL autoryzacji** i **adres URL wymiany tokenu** nie są obecnie konfigurowalne dla dzierżawy.

> [!NOTE]
> Uwierzytelnianie OAuth w wersji 1 nie jest obsługiwane z powodu narażenia na klucz tajny klienta. Obsługiwane jest uwierzytelnianie OAuth w wersji 2.  

Najlepsze rozwiązania (zalecane, ale nie wymagane):
* Obsługa wielu adresów URL przekierowania. Administratorzy mogą konfigurować aprowizowanie zarówno z portal.azure.com, jak i "aad.portal.azure.com". Obsługa wielu adresów URL przekierowania zapewni, że użytkownicy będą mieć możliwość autoryzowania dostępu z jednego z portali.
* Obsługa wielu wpisów tajnych w celu łatwego odnawiania bez przestojów. 

#### <a name="how-to-setup-oauth-code-grant-flow"></a>Jak skonfigurować przepływ udzielania kodu OAuth

1. Zaloguj się do usługi Azure Portal, przejdź do aprowizowania **aplikacji** dla przedsiębiorstw i  >    >   wybierz pozycję **Autoryzuj**.

   1. Azure Portal przekierowuje użytkownika do adresu URL autoryzacji (strony logowania dla aplikacji innej firmy).

   1. Administrator udostępnia poświadczenia aplikacji innej firmy. 

   1. Aplikacja innej firmy przekierowuje użytkownika z powrotem Azure Portal i udostępnia kod udzielania 

   1. Usługi aprowizowania w usłudze Azure AD wywołuje adres URL tokenu i dostarcza kod udzielenia. Aplikacja innej firmy odpowiada przy użyciu tokenu dostępu, tokenu odświeżania i daty wygaśnięcia

1. Po rozpoczęciem cyklu aprowizowania usługa sprawdza, czy bieżący token dostępu jest prawidłowy, i wymienia go na nowy token w razie potrzeby. Token dostępu jest dostarczany w każdym żądaniu do aplikacji, a ważność żądania jest sprawdzana przed każdym żądaniem.

> [!NOTE]
> Nie można skonfigurować uwierzytelniania OAuth w aplikacjach spoza galerii, ale można ręcznie wygenerować token dostępu z serwera autoryzacji i wprowadzić go jako token tajny do aplikacji spoza galerii. Umożliwia to sprawdzenie zgodności serwera SCIM z klientem AAD SCIM przed dodaniem do galerii aplikacji, która obsługuje przyznawanie kodu OAuth.  

**Długotrwałe tokeny okaziciela OAuth:** Jeśli aplikacja nie obsługuje przepływu przyznawania kodu autoryzacji OAuth, wygeneruj długotrwały token element bearer OAuth, za pomocą których administrator może skonfigurować integrację aprowizatora. Token powinien być bezterminowy. W innym przypadku zadanie aprowowania zostanie poddane kwarantannie [po](application-provisioning-quarantine-status.md) wygaśnięciu tokenu.

Aby uzyskać dodatkowe metody uwierzytelniania i autoryzacji, daj nam znać na [platformie UserVoice.](https://aka.ms/appprovisioningfeaturerequest)

### <a name="gallery-go-to-market-launch-check-list"></a>Lista kontrolna wprowadzenia galerii na rynek
Aby zwiększyć świadomość i zwiększyć zapotrzebowanie na naszą wspólną integrację, zalecamy zaktualizowanie istniejącej dokumentacji i zwiększenie integracji w kanałach marketingowych.  Poniżej przedstawiono zestaw działań z listą kontrolną, które zalecamy zakończyć w celu obsługi startu

> [!div class="checklist"]
> * Upewnij się, że zespoły sprzedaży i obsługi klienta są świadome, gotowe i mogą mówić o możliwościach integracji. Krótki opis zespołów, często zadawane pytania i integracja z materiałami sprzedażowych. 
> * Przygotuj wpis w blogu lub komunikat press, który opisuje wspólną integrację, korzyści i sposób rozpoczęcia pracy. [Przykład: Imprivata i Azure Active Directory press release](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
> * Skorzystaj z mediów społecznościowych, takich jak Twitter, Facebook lub LinkedIn, aby promować integrację z klientami. Pamiętaj, aby dołączyć , abyśmy w stanie ponownie @AzureAD opublikować wpis. [Przykład: Imprivata Twitter Post](https://twitter.com/azuread/status/1123964502909779968)
> * Utwórz lub zaktualizuj strony marketingowe/witrynę internetową (np. stronę integracji, stronę partnera, stronę cennika itp.), aby uwzględnić dostępność wspólnej integracji. [Przykład: strona integracji programu Pingboard,](https://pingboard.com/org-chart-for)strona integracji [usługi Smartsheet,](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad) [Monday.com cennik](https://monday.com/pricing/) 
> * Utwórz artykuł lub dokumentację techniczną centrum pomocy, aby dowiedzieć się, jak klienci mogą rozpocząć pracę. [Przykład: Integracja aplikacji Envoy Microsoft Azure Active Directory.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
> * Powiadamiaj klientów o nowej integracji za pośrednictwem komunikacji z klientami (comiesięczne biuletyny, kampanie e-mail, informacje o wersji produktu). 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Opracowywanie przykładowego punktu końcowego SCIM](use-scim-to-build-users-and-groups-endpoints.md) 
>  [Automatyzowanie aprowzowania i coprowizowania użytkowników w aplikacjach SaaS](user-provisioning.md) 
>  [Dostosowywanie mapowań atrybutów na celu aprowizowanie użytkowników](customize-application-attributes.md) 
>  [Pisanie wyrażeń dla mapowań atrybutów](functions-for-customizing-application-data.md) 
>  [Filtry zakresu dla aprowizowania użytkowników](define-conditional-rules-for-provisioning-user-accounts.md) 
>  [Powiadomienia dotyczące aprowizowania konta](user-provisioning.md) 
>  [Lista samouczków na temat sposobu integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)
