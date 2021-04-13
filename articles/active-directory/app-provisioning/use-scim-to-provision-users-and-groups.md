---
title: Samouczek — Tworzenie punktu końcowego Standard scim na potrzeby aprowizacji użytkowników w aplikacjach z usługi Azure AD
description: System do zarządzania tożsamościami między domenami (standard scim) zapewnia standaryzację automatycznej aprowizacji użytkowników. W tym samouczku dowiesz się, jak opracowywać punkt końcowy Standard scim, zintegrować interfejs API Standard scim z usługą Azure Active Directory i rozpocząć automatyzację użytkowników i grup aprowizacji w aplikacjach w chmurze.
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
ms.openlocfilehash: 4130ed4bb690edb3c0c5d72d7d158262ed6ff39d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305603"
---
# <a name="tutorial-develop-and-plan-provisioning-for-a-scim-endpoint"></a>Samouczek: opracowywanie i planowanie aprowizacji dla punktu końcowego Standard scim

Jako deweloper aplikacji możesz użyć interfejsu API zarządzania użytkownikami (standard scim) dla wielu domen, aby włączyć automatyczną obsługę administracyjną użytkowników i grup między aplikacją a usługą Azure AD (AAD). W tym artykule opisano sposób tworzenia punktu końcowego Standard scim i integrowania z usługą AAD. Specyfikacja Standard scim zawiera wspólny schemat użytkownika na potrzeby aprowizacji. W połączeniu z standardami Federacji, takimi jak SAML lub OpenID Connect Connect, standard scim zapewnia administratorom kompleksowe, oparte na standardach rozwiązanie do zarządzania dostępem.

![Inicjowanie obsługi administracyjnej z usługi Azure AD do aplikacji za pomocą Standard scim](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Standard scim jest standardową definicją dwóch punktów końcowych: `/Users` punkt końcowy i `/Groups` punkt końcowy. Używa typowych czasowników REST do tworzenia, aktualizowania i usuwania obiektów oraz wstępnie zdefiniowanego schematu dla wspólnych atrybutów, takich jak nazwa grupy, username, imię, nazwisko i adres e-mail. Aplikacje oferujące interfejs API REST w systemie Standard scim 2,0 mogą zmniejszyć lub wyeliminować możliwości pracy z własnym interfejsem API zarządzania użytkownikami. Na przykład każdy zgodny klient Standard scim wie, jak wykonać wpis HTTP obiektu JSON do `/Users` punktu końcowego, aby utworzyć nowy wpis użytkownika. Zamiast niepotrzebnego nieco innego interfejsu API dla tych samych podstawowych akcji, aplikacje zgodne z standardem Standard scim mogą natychmiast korzystać z istniejących klientów, narzędzi i kodu. 

Schemat obiektów użytkownika standardowego i interfejsy API REST do zarządzania zdefiniowane w Standard scim 2,0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) umożliwiają dostawcom tożsamości i aplikacjom łatwą integrację ze sobą. Deweloperzy aplikacji tworzący punkt końcowy Standard scim można zintegrować z dowolnym klientem zgodnym z standard scim bez konieczności wykonywania niestandardowych zadań.

Aby zautomatyzować Inicjowanie obsługi aplikacji, musisz skompilować i zintegrować punkt końcowy Standard scim z klientem usługi Azure AD Standard scim. Wykonaj następujące kroki, aby rozpocząć Inicjowanie obsługi administracyjnej użytkowników i grup w aplikacji. 
    
1. Projektowanie schematu użytkownika i grupy

   Zidentyfikuj obiekty i atrybuty aplikacji, aby określić sposób mapowania do schematu użytkownika i grupy obsługiwanego przez implementację Standard scim usługi AAD.

1. Opis implementacji usługi AAD Standard scim

   Zapoznaj się z implementacją klienta usługi AAD Standard scim, aby modelować obsługę żądań i odpowiedzi protokołu Standard scim.

1. Tworzenie punktu końcowego Standard scim

   Aby można było przeprowadzić integrację z usługą aprowizacji usługi AAD, punkt końcowy musi być zgodny z standard scim 2,0. Jako opcję Użyj bibliotek Microsoft Common Language Infrastructure (CLI) i przykładów kodu do kompilowania punktu końcowego. Te przykłady są przeznaczone wyłącznie do celów referencyjnych i testowych. Zalecamy używanie ich jako zależności w aplikacji produkcyjnej.

1. Integrowanie punktu końcowego Standard scim z klientem usługi AAD Standard scim 

   Jeśli organizacja używa aplikacji innej firmy do zaimplementowania profilu Standard scim 2,0 obsługiwanego przez usługi AAD, można szybko zautomatyzować obsługę administracyjną i cofanie aprowizacji użytkowników i grup.

1. Publikowanie aplikacji w galerii aplikacji usługi AAD 

   Ułatwiaj klientom odnajdywanie aplikacji i łatwe konfigurowanie aprowizacji. 

![Kroki związane z integracją punktu końcowego Standard scim z usługą Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="design-your-user-and-group-schema"></a>Projektowanie schematu użytkownika i grupy

Każda aplikacja wymaga innych atrybutów do utworzenia użytkownika lub grupy. Rozpocznij integrację, identyfikując wymagane obiekty (użytkowników, grupy) i atrybuty (nazwę, Menedżera, stanowisko, itp.), których potrzebuje aplikacja. 

Standard Standard scim definiuje schemat do zarządzania użytkownikami i grupami. 

**Podstawowy** schemat użytkownika wymaga tylko trzech atrybutów (wszystkie inne atrybuty są opcjonalne):

- `id`, identyfikator zdefiniowany przez dostawcę usług
- `userName`unikatowy identyfikator użytkownika (zazwyczaj jest mapowany na główną nazwę użytkownika usługi Azure AD)
- `meta`, metadane *tylko do odczytu* utrzymywane przez dostawcę usług

Oprócz **podstawowego** schematu użytkownika Standard Standard scim definiuje rozszerzenie użytkownika **przedsiębiorstwa** z modelem umożliwiającym rozszerzenie schematu użytkownika w celu spełnienia wymagań aplikacji. 

Na przykład jeśli aplikacja wymaga zarówno poczty e-mail użytkownika, jak i Menedżera użytkownika, użyj **podstawowego** schematu, aby zebrać wiadomość e-mail użytkownika i schemat użytkownika **przedsiębiorstwa** w celu zebrania Menedżera użytkownika.

Aby zaprojektować schemat, wykonaj następujące kroki:

1. Lista atrybutów wymaganych przez aplikację, a następnie kategoryzowanie jako atrybuty wymagane do uwierzytelniania (np. loginName i poczta e-mail), atrybuty potrzebne do zarządzania cyklem życia użytkownika (np. status/aktywny) i wszystkie inne atrybuty potrzebne do działania aplikacji (np. Menedżer, tag).

1. Sprawdź, czy atrybuty są już zdefiniowane w **podstawowym** schemacie użytkownika lub w schemacie użytkownika **przedsiębiorstwa** . W przeciwnym razie należy zdefiniować rozszerzenie schematu użytkownika, które obejmuje brakujące atrybuty. Zobacz przykład poniżej rozszerzenia użytkownika, aby umożliwić obsługę administracyjną użytkownika `tag` .

1. Mapowanie atrybutów Standard scim do atrybutów użytkownika w usłudze Azure AD. Jeśli jeden z atrybutów zdefiniowanych w punkcie końcowym Standard scim nie ma wyraźnego odpowiednika w schemacie użytkownika usługi Azure AD, należy poprowadzić administratora dzierżawy, aby rozszerzał swój schemat, lub użyć atrybutu rozszerzenia, jak pokazano poniżej dla `tags` właściwości.

|Wymagany atrybut aplikacji|Zamapowany atrybut Standard scim|Mapowany atrybut usługi Azure AD|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|name.familyName|Imię|
|workMail|wiadomości e-mail [Type EQ "Work"]. Value|Mail|
|manager|manager|manager|
|tag|urn: IETF: params: Standard scim: schematy: rozszerzenie: 2.0: CustomExtension: tag|extensionAttribute1|
|status|aktywne|isSoftDeleted (obliczona wartość nie jest przechowywana na użytkowniku)|

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
> Oprócz atrybutów wymaganych dla aplikacji, reprezentacja JSON obejmuje również wymagane `id` `externalId` atrybuty, i `meta` .

Ułatwia ona kategoryzowanie `/User` i `/Group` Mapowanie wszelkich domyślnych atrybutów użytkownika w usłudze Azure AD do Standard scim RFC, zobacz [jak dostosować atrybuty są mapowane między usługą Azure AD a punktem końcowym Standard scim](customize-application-attributes.md).


| Azure Active Directory użytkownika | "urn: IETF: params: Standard scim: schematy: Extension: Enterprise: 2.0: User" |
| --- | --- |
| IsSoftDeleted |aktywne |
|działu,|urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Department|
| displayName |displayName |
|IDPracownika|urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: employeeNumber|
| Facsimile-TelephoneNumber |numer telefonu [typ EQ "Fax"]. wartość |
| givenName |name.givenName |
| jobTitle |tytuł |
| mail (poczta) |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User: Manager |
| telefon komórkowy |phoneNumbers[type eq "mobile"].value |
| postalCode |addresss [Type EQ "Work"]. KodPocztowy |
| Adresy serwera proxy |wiadomości e-mail [Type EQ "Other"]. Wartościami |
| Physical-Delivery-OfficeName |adresy [Type EQ "Other"]. Poprawić |
| streetAddress |adresy [typ EQ "Work"]. streetAddress |
| surname |name.familyName |
| Numer telefonu |phoneNumbers[type eq "work"].value |
| User-PrincipalName |userName |

**Przykładowa lista atrybutów użytkowników i grup**

| Grupa Azure Active Directory | urn: IETF: params: Standard scim: schematy: rdzeń: 2.0: Grupa |
| --- | --- |
| displayName |displayName |
| mail (poczta) |emails[type eq "work"].value |
| mailNickname |displayName |
| elementy członkowskie |elementy członkowskie |
| objectId |externalId |
| proxyAddresses |wiadomości e-mail [Type EQ "Other"]. Wartościami |

**Przykładowa lista atrybutów grupy**

> [!NOTE]
> Nie jest wymagane obsługiwanie użytkowników i grup ani wszystkich atrybutów pokazanych w tym miejscu, ale tylko informacje o sposobie, w jaki atrybuty w usłudze Azure AD są często mapowane na właściwości w protokole Standard scim. 

Istnieje kilka punktów końcowych zdefiniowanych w Standard scim RFC. Możesz rozpocząć od `/User` punktu końcowego, a następnie rozwinąć z tego miejsca. 

|Punkt końcowy|Opis|
|--|--|
|/User|Wykonywanie operacji CRUD na obiekcie użytkownika.|
|/Group|Wykonywanie operacji CRUD na obiekcie grupy.|
|/Schemas|Zestaw atrybutów obsługiwanych przez każdego klienta i dostawcę usług może się różnić. Jeden dostawca usług może obejmować `name` , `title` , i `emails` , podczas gdy inny dostawca usług używa `name` , `title` , i `phoneNumbers` . Punkt końcowy schematów umożliwia odnajdywanie obsługiwanych atrybutów.|
|/Bulk|Operacje zbiorcze umożliwiają wykonywanie operacji na dużej kolekcji obiektów zasobów w ramach jednej operacji (np. aktualizacji członkostwa w dużej grupie).|
|/ServiceProviderConfig|Zawiera szczegółowe informacje na temat funkcji obsługiwanego standardu Standard scim, na przykład obsługiwanych zasobów i metody uwierzytelniania.|
|/ResourceTypes|Określa metadane dotyczące poszczególnych zasobów.|

**Przykładowa lista punktów końcowych**

> [!NOTE]
> Użyj `/Schemas` punktu końcowego do obsługi atrybutów niestandardowych lub jeśli schemat zmienia się często, ponieważ umożliwia klientowi automatyczne pobranie najbardziej aktualnego schematu. Użyj `/Bulk` punktu końcowego do obsługi grup.

## <a name="understand-the-aad-scim-implementation"></a>Opis implementacji usługi AAD Standard scim

Aby zapewnić obsługę interfejsu API zarządzania użytkownikami w programie Standard scim 2,0, w tej sekcji opisano sposób implementacji klienta usługi AAD Standard scim i przedstawiono sposób modelowania obsługi żądań i odpowiedzi protokołu Standard scim.

> [!IMPORTANT]
> Ostatnio Zaktualizowano zachowanie implementacji usługi Azure AD Standard scim w dniu 18 grudnia 2018. Aby uzyskać informacje na temat zmian, zobacz [zgodność protokołów standard scim 2,0 usługi Azure AD User Provisioning](application-provisioning-config-problem-scim-compatibility.md).

W [specyfikacji protokołu standard scim 2,0](http://www.simplecloud.info/#Specification), aplikacja musi obsługiwać te wymagania:

|Wymaganie|Uwagi dotyczące odwołań (protokół Standard scim)|
|-|-|
|Tworzenie użytkowników i opcjonalnie grup|[sekcja 3,3](https://tools.ietf.org/html/rfc7644#section-3.3)|
|Modyfikowanie użytkowników lub grup przy użyciu żądań poprawek|[sekcja 3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2). Obsługa gwarantuje, że grupy i użytkownicy są obsługiwani w sposób efektywny.|
|Pobieranie znanego zasobu dla utworzonego wcześniej użytkownika lub grupy|[Sekcja pkt.](https://tools.ietf.org/html/rfc7644#section-3.4.1)|
|Tworzenie zapytań dotyczących użytkowników lub grup|[sekcja 3.4.2](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Domyślnie użytkownicy są pobierani przez ich i sprawdzani według `id` ich `username` , a `externalId` grupy są pytani przez `displayName` .|
|Kwerenda użytkownika według identyfikatora i Menedżera|sekcja 3.4.2|
|Zapytania grup według identyfikatorów i elementów członkowskich|sekcja 3.4.2|
|Filtr [excludedAttributes = Members](#get-group) podczas wykonywania zapytania o zasób grupy|sekcja 3.4.2.5|
|Zaakceptuj pojedynczy token okaziciela na potrzeby uwierzytelniania i autoryzacji usługi AAD w aplikacji.||
|Nietrwałe Usuwanie użytkownika `active=false` i przywracanie użytkownika `active=true`|Obiekt użytkownika powinien być zwracany w żądaniu niezależnie od tego, czy użytkownik jest aktywny. Jedynym czasem, gdy użytkownik nie powinien być zwracany, jest to, że jest on trwale usuwany z aplikacji.|
|Obsługa punktu końcowego/schemas|[sekcja 7](https://tools.ietf.org/html/rfc7643#page-30) Punkt końcowy odnajdywania schematu zostanie użyty do odnalezienia dodatkowych atrybutów.|

Podczas wdrażania punktu końcowego Standard scim w celu zapewnienia zgodności z usługą AAD należy użyć ogólnych wytycznych:

* `id` jest właściwością wymaganą dla wszystkich zasobów. Każda odpowiedź zwracająca zasób powinna mieć pewność, że każdy zasób ma tę właściwość, z wyjątkiem `ListResponse` z zerowymi elementami członkowskimi.
* Odpowiedź na żądanie zapytania/filtru zawsze powinna być `ListResponse` .
* Grupy są opcjonalne, ale są obsługiwane tylko wtedy, gdy implementacja Standard scim obsługuje żądania **patch** .
* Nie trzeba uwzględniać całego zasobu w odpowiedzi na **poprawkę** .
* W usłudze Microsoft AAD są stosowane tylko następujące operatory: `eq` , `and`
* Nie wymagaj dopasowania uwzględniającego wielkość liter w elementach konstrukcyjnych w Standard scim,  w określonych `op` wartościach operacji patch, zgodnie z definicją w [sekcji 3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2). AAD emituje wartości `op` jako **Dodawanie**, **zastępowanie** i **usuwanie**.
* W usłudze Microsoft AAD są wysyłane żądania pobrania losowego użytkownika i grupy, aby upewnić się, że punkt końcowy i poświadczenia są prawidłowe. Jest również wykonywana jako część przepływu **połączenia testowego** w [Azure Portal](https://portal.azure.com). 
* Atrybut, w którym można wykonywać zapytania dotyczące zasobów, powinien być ustawiony jako pasujący atrybut w aplikacji w [Azure Portal](https://portal.azure.com), zobacz [Dostosowywanie mapowań atrybutów aprowizacji użytkowników](customize-application-attributes.md).
* Obsługa protokołu HTTPS w punkcie końcowym Standard scim
* [Odnajdywanie schematu](#schema-discovery)
  * Funkcja odnajdywania schematu nie jest obecnie obsługiwana w aplikacji niestandardowej, ale jest używana w niektórych aplikacjach galerii. W przód funkcja odnajdywania schematu będzie używana jako podstawowa metoda dodawania dodatkowych atrybutów do łącznika. 
  * Jeśli wartość nie jest obecna, nie wysyłaj wartości null.
  * Wartości właściwości powinny być notacji CamelCase (np. readWrite).
  * Musi zwracać odpowiedź na listę.
  
### <a name="user-provisioning-and-deprovisioning"></a>Inicjowanie obsługi i cofanie aprowizacji użytkowników

Na poniższej ilustracji przedstawiono komunikaty wysyłane przez usługę AAD do usługi Standard scim w celu zarządzania cyklem życia użytkownika w magazynie tożsamości aplikacji.  

![Pokazuje sekwencję inicjowania obsługi i anulowania aprowizacji użytkowników](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Sekwencja inicjowania obsługi i anulowania aprowizacji użytkowników*

### <a name="group-provisioning-and-deprovisioning"></a>Inicjowanie obsługi i cofanie aprowizacji grupy

Inicjowanie obsługi i cofanie aprowizacji grupy jest opcjonalne. Po zaimplementowaniu i włączeniu na poniższej ilustracji przedstawiono komunikaty wysyłane przez usługę AAD do usługi Standard scim w celu zarządzania cyklem życia grupy w magazynie tożsamości aplikacji. Komunikaty te różnią się od komunikatów o użytkownikach na dwa sposoby:

* Żądania pobrania grup określają, że atrybut Members ma być wykluczony z dowolnego zasobu dostarczonego w odpowiedzi na żądanie.  
* Żądania, aby określić, czy atrybut odwołania ma pewne wartości są żądaniami dotyczącymi atrybutu Members.  

![Pokazuje sekwencję inicjowania obsługi i anulowania aprowizacji grupy](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Sekwencjonowanie grup i cofanie aprowizacji*

### <a name="scim-protocol-requests-and-responses"></a>Żądania i odpowiedzi protokołu Standard scim
Ta sekcja zawiera przykładowe żądania Standard scim emitowane przez klienta usługi AAD Standard scim i przykładowe oczekiwane odpowiedzi. Aby uzyskać najlepsze wyniki, należy zakodować aplikację do obsługi tych żądań w tym formacie i emitować oczekiwane odpowiedzi.

> [!IMPORTANT]
> Aby zrozumieć, jak i kiedy usługa aprowizacji użytkowników usługi AAD emituje opisane poniżej operacje, zapoznaj się z sekcją [cykle aprowizacji: początkowe i przyrostowe](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) w temacie [jak działa Inicjowanie obsługi](how-provisioning-works.md).

[Operacje użytkownika](#user-operations)
  - [Utwórz użytkownika](#create-user) ([](#request)  /  [odpowiedź](#response)na żądanie)
  - [Pobierz użytkownika](#get-user) ([](#request-1)  /  [odpowiedź](#response-1)na żądanie)
  - [Pobierz użytkownika według zapytania](#get-user-by-query) ([](#request-2)  /  [odpowiedź](#response-2)żądania)
  - [Pobierz użytkownika według zapytania — wyniki zerowe](#get-user-by-query---zero-results) ([](#request-3)  /  [odpowiedź](#response-3)na żądanie)
  - [Aktualizowanie użytkownika [właściwości wielowartościowe]](#update-user-multi-valued-properties) (odpowiedź na[żądanie](#request-4)  /  [](#response-4))
  - [Aktualizowanie użytkownika [właściwości pojedynczej wartości]](#update-user-single-valued-properties) ([](#request-5)  /  [odpowiedź](#response-5)żądania) 
  - [Wyłącz użytkownika](#disable-user) ([](#request-14)  /  [odpowiedź](#response-14)na żądanie)
  - [Usuń użytkownika](#delete-user) ([](#request-6)  /  [odpowiedź](#response-6)na żądanie)

[Operacje grupy](#group-operations)
  - [Utwórz grupę](#create-group) ([](#request-7)  /  [odpowiedź](#response-7)na żądanie)
  - [Pobierz grupę](#get-group) ([](#request-8)  /  [odpowiedź](#response-8)na żądanie)
  - [Pobierz grupę według DisplayName](#get-group-by-displayname) ([](#request-9)  /  [odpowiedź](#response-9)na żądanie)
  - [Grupa aktualizacji [atrybuty inne niż Członkowskie]](#update-group-non-member-attributes) ([](#request-10)  /  [odpowiedź](#response-10)żądania)
  - [Grupa aktualizacji [Dodaj członków]](#update-group-add-members) ([żądanie żądania](#request-11)  /  [](#response-11))
  - [Grupa aktualizacji [usuwanie członków]](#update-group-remove-members) ([żądanie żądania](#request-12)  /  [](#response-12))
  - [Usuń grupę](#delete-group) ([](#request-13)  /  [odpowiedź](#response-13)na żądanie)

[Odnajdywanie schematu](#schema-discovery)
  - [Odnajdź schemat](#discover-schema) (odpowiedź na[żądanie](#request-15)  /  [](#response-15))

### <a name="user-operations"></a>Operacje użytkownika

* Użytkownicy mogą wykonywać zapytania `userName` lub `email[type eq "work"]` atrybuty.  

#### <a name="create-user"></a>Utwórz użytkownika

##### <a name="request"></a>Żądanie

*Opublikuj/users*
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
*Pobierz/users/5d48a0a8e9f04aa38008* 

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
*Pobierz/users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Odpowiedź (nie znaleziono użytkownika. Należy zauważyć, że szczegóły nie są wymagane, tylko stan.)

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

*POBRAĆ wartość/users? Filter = userName EQ "Test_User_dfeef4c5-5681 -4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>Reakcji

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

#### <a name="get-user-by-query---zero-results"></a>Pobierz użytkownika według zapytania — wyniki zerowe

##### <a name="request"></a><a name="request-3"></a>Żądanie

*POBRAĆ wartość/users? Filter = userName EQ "nieistniejącego użytkownika"*

##### <a name="response"></a><a name="response-3"></a>Reakcji

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

#### <a name="update-user-multi-valued-properties"></a>Aktualizowanie użytkownika [właściwości wielowartościowe]

##### <a name="request"></a><a name="request-4"></a>Żądanie

*Poprawka/users/6764549bef60420686bc HTTP/1.1*
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

##### <a name="response"></a><a name="response-4"></a>Reakcji

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

#### <a name="update-user-single-valued-properties"></a>Aktualizowanie użytkownika [właściwości pojedynczej wartości]

##### <a name="request"></a><a name="request-5"></a>Żądanie

*Poprawka/users/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="response"></a><a name="response-5"></a>Reakcji

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

*Poprawka/users/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="response"></a><a name="response-14"></a>Reakcji

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

*Usuń/users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>Reakcji

*HTTP/1.1 204 Brak zawartości*

### <a name="group-operations"></a>Operacje grupy

* Grupy zawsze są tworzone z pustą listą członków.
* Do grup mogą być wysyłane zapytania według `displayName` atrybutu.
* Aktualizacja żądania poprawek grupy powinna spowodować, że w odpowiedzi *nie ma żadnej zawartości HTTP 204* . Zwracanie treści z listą wszystkich elementów członkowskich nie jest zalecane.
* Nie trzeba obsługiwać zwracania wszystkich elementów członkowskich grupy.

#### <a name="create-group"></a>Tworzenie grupy

##### <a name="request"></a><a name="request-7"></a>Żądanie

*/Groups HTTP/1.1*
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

##### <a name="response"></a><a name="response-7"></a>Reakcji

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

*GET/Groups/40734ae655284ad3abcc? excludedAttributes = Members HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>Reakcji
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

#### <a name="get-group-by-displayname"></a>Pobierz grupowanie według displayName

##### <a name="request"></a><a name="request-9"></a>Żądanie
*GET/Groups? excludedAttributes = memberss&Filter = displayName EQ "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>Reakcji

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

#### <a name="update-group-non-member-attributes"></a>Aktualizacja grupy [atrybuty niebędące elementami członkowskimi]

##### <a name="request"></a><a name="request-10"></a>Żądanie

*Poprawka/Groups/fa2ce26709934589afc5 HTTP/1.1*
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

##### <a name="response"></a><a name="response-10"></a>Reakcji

*HTTP/1.1 204 Brak zawartości*

### <a name="update-group-add-members"></a>Grupa aktualizacji [Dodaj członków]

##### <a name="request"></a><a name="request-11"></a>Żądanie

*Poprawka/Groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response"></a><a name="response-11"></a>Reakcji

*HTTP/1.1 204 Brak zawartości*

#### <a name="update-group-remove-members"></a>Grupa aktualizacji [usuwanie członków]

##### <a name="request"></a><a name="request-12"></a>Żądanie

*Poprawka/Groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response"></a><a name="response-12"></a>Reakcji

*HTTP/1.1 204 Brak zawartości*

#### <a name="delete-group"></a>Usuń grupę

##### <a name="request"></a><a name="request-13"></a>Żądanie

*Usuń/Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>Reakcji

*HTTP/1.1 204 Brak zawartości*

### <a name="schema-discovery"></a>Odnajdywanie schematu
#### <a name="discover-schema"></a>Odnajdź schemat

##### <a name="request"></a><a name="request-15"></a>Żądanie
*Pobierz/schemas* 
##### <a name="response"></a><a name="response-15"></a>Reakcji
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

Jedyne akceptowalne wersje protokołu TLS to TLS 1,2 i TLS 1,3. Żadna inna wersja protokołu TLS nie jest dozwolona. Nie jest dozwolona żadna wersja protokołu SSL. 
- Klucze RSA muszą mieć co najmniej 2 048 bitów.
- Klucze ECC muszą mieć co najmniej 256 bitów, generowane przy użyciu zatwierdzonej krzywej eliptycznej

**Długości kluczy**

Wszystkie usługi muszą używać certyfikatów X. 509 wygenerowanych przy użyciu kluczy kryptograficznych o wystarczającej długości, co oznacza:

**Mechanizmy szyfrowania**

Wszystkie usługi muszą być skonfigurowane tak, aby korzystały z następujących mechanizmów szyfrowania w kolejności określonej poniżej. Należy pamiętać, że jeśli masz tylko certyfikat RSA, instalacja mechanizmów szyfrowania ECDSA nie ma żadnego skutku. </br>

Minimalny pasek mechanizmów szyfrowania TLS 1,2:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

### <a name="ip-ranges"></a>Zakresy adresów IP
Usługa Azure AD Provisioning obecnie działa pod zakresem adresów IP dla usługi azureactivedirectory jak wymieniono [tutaj](https://www.microsoft.com/download/details.aspx?id=56519&WT.mc_id=rss_alldownloads_all). Możesz dodać zakresy adresów IP wymienione w tagu usługi azureactivedirectory, aby zezwolić na ruch z usługi Azure AD Provisioning do aplikacji. Należy pamiętać, że należy uważnie przejrzeć listę zakresów adresów IP dla obliczanych adresów. Adres taki jak "40.126.25.32" może być przedstawiony na liście zakresów adresów IP jako "40.126.0.0/18". Możesz również programowo pobrać listę zakresów adresów IP przy użyciu poniższego [interfejsu API](/rest/api/virtualnetwork/servicetags/list).

## <a name="build-a-scim-endpoint"></a>Tworzenie punktu końcowego Standard scim

Teraz, Po zaprojektowaniu schematu i zrozumieniu implementacji usługi Azure AD Standard scim, możesz rozpocząć tworzenie punktu końcowego Standard scim. Zamiast zaczynać od początku i całkowicie samodzielnie kompilować implementację, możesz polegać na wielu bibliotekach Standard scim typu open source opublikowanych przez społeczność Standard scim.

Aby uzyskać wskazówki dotyczące sposobu tworzenia punktu końcowego Standard scim, w tym przykładów, zobacz [Tworzenie przykładowego punktu końcowego Standard scim](use-scim-to-build-users-and-groups-endpoints.md).

[Przykładowy kod referencyjny](https://aka.ms/SCIMReferenceCode) programu .NET Core typu open source Opublikowany przez zespół aprowizacji usługi Azure AD jest jednym z zasobów, które mogą szybko rozpocząć programowanie. Po skompilowaniu punktu końcowego Standard scim należy go przetestować. Można użyć kolekcji [testów post](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) dostarczonych jako część kodu odwołania lub wykonać przez przykładowe żądania/odpowiedzi podane [powyżej](#user-operations).  

   > [!Note]
   > Kod referencyjny ma pomóc w rozpoczęciu tworzenia punktu końcowego Standard scim i jest dostarczany "w takiej postaci, w jakiej jest". Wkłady ze społeczności pomogą Ci pomóc w tworzeniu i utrzymaniu kodu.

Rozwiązanie składa się z dwóch projektów, _Microsoft. Standard scim_ i _Microsoft. Standard scim. WebHostSample_.

Projekt _Microsoft. Standard scim_ to biblioteka, która definiuje składniki usługi sieci Web, która jest zgodna ze specyfikacją Standard scim. Deklaruje Interfejs _Microsoft. Standard scim. IProvider_, żądania są tłumaczone na wywołania metod dostawcy, które będą działać w ramach magazynu tożsamości.

![Podział: żądanie przetłumaczone na wywołania metod dostawcy](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

Projekt _Microsoft. Standard scim. WebHostSample_ jest aplikacją internetową programu Visual Studio ASP.NET Core na podstawie _pustego_ szablonu. Pozwala to na wdrożenie przykładowego kodu jako autonomicznego, hostowanego w kontenerach lub w Internet Information Services. Implementuje także interfejs _Microsoft. Standard scim. IProvider_ , który umożliwia przechowywanie klas w pamięci jako przykładowego magazynu tożsamości.

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

### <a name="building-a-custom-scim-endpoint"></a>Kompilowanie niestandardowego punktu końcowego Standard scim

Usługa Standard scim musi mieć adres HTTP i certyfikat uwierzytelniania serwera, którego główny urząd certyfikacji jest jedną z następujących nazw:

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Przejdź do Daddy
* Firmy
* WoSign
* Główny urząd certyfikacji DST

Zestaw .NET Core SDK zawiera certyfikat programistyczny HTTPS, który może być używany podczas opracowywania, certyfikat jest instalowany w ramach pierwszego uruchomienia. W zależności od sposobu uruchomienia ASP.NET Core aplikacji sieci Web nasłuchuje na innym porcie:

* Microsoft. Standard scim. WebHostSample: https://localhost:5001
* IIS Express: https://localhost:44359/

Aby uzyskać więcej informacji na temat protokołu HTTPS w ASP.NET Core Użyj następującego linku: [Wymuszaj protokół https w programie ASP.NET Core](/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>Obsługa uwierzytelniania punktu końcowego

Żądania od Azure Active Directory zawierają token elementu nośnego OAuth 2,0. Każda usługa otrzymująca żądanie powinna uwierzytelniać wystawcę jako Azure Active Directory dla oczekiwanego Azure Active Directory dzierżawy.

W tokenie wystawca jest identyfikowany przez zgłoszenie ISS, np `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"` .. W tym przykładzie adres podstawowy wartości żądania, `https://sts.windows.net` określa Azure Active Directory jako wystawca, natomiast segment adresu względnego, _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_, jest unikatowym identyfikatorem dzierżawy Azure Active Directory, dla którego został wystawiony token.

Odbiorcy tokenu będą IDENTYFIKATORem szablonu aplikacji dla aplikacji w galerii, a każda z aplikacji zarejestrowanych w jednej dzierżawie może otrzymać to samo `iss` żądanie z żądaniami Standard scim. Identyfikator szablonu aplikacji dla wszystkich aplikacji niestandardowych to _8adf8e6e-67b2-4cf2-A259-e3dc5476c621_. Tokenu wygenerowanego przez usługę Azure AD Provisioning należy używać tylko do celów testowych. Nie powinna być używana w środowiskach produkcyjnych.

W przykładowym kodzie żądania są uwierzytelniane za pomocą pakietu Microsoft. AspNetCore. Authentication. JwtBearer. Poniższy kod wymusza, aby żądania kierowane do dowolnego z punktów końcowych usługi były uwierzytelniane przy użyciu tokenu okaziciela wydanego przez Azure Active Directory dla określonej dzierżawy:

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

Token okaziciela jest również wymagany do korzystania z dostarczonych [testów post](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) i przeprowadzania debugowania lokalnego przy użyciu localhost. Przykładowy kod używa środowisk ASP.NET Core, aby zmienić opcje uwierzytelniania podczas etapu tworzenia i włączyć token z podpisem własnym.

Aby uzyskać więcej informacji na temat wielu środowisk w ASP.NET Core, zobacz [Używanie wielu środowisk w programie ASP.NET Core](/aspnet/core/fundamentals/environments).

Poniższy kod wymusza, aby żądania kierowane do dowolnego z punktów końcowych usługi były uwierzytelniane przy użyciu tokenu okaziciela podpisanego przy użyciu klucza niestandardowego:

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

Wyślij żądanie GET do kontrolera tokenu w celu uzyskania prawidłowego tokenu okaziciela, Metoda _GenerateJSONWebToken_ jest odpowiedzialna za utworzenie tokenu zgodnego z parametrami skonfigurowanymi do celów deweloperskich:

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

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Obsługa obsługi administracyjnej i cofanie aprowizacji użytkowników

***Przykład 1. Wysyłanie zapytań do usługi pod kątem pasującego użytkownika***

Azure Active Directory (AAD) wysyła zapytanie do usługi dla użytkownika o `externalId` wartość atrybutu zgodną z wartością atrybutu mailNickName użytkownika w usłudze AAD. Zapytanie jest wyrażone jako żądanie protokołu HTTP (Hypertext Transfer Protocol), takie jak ten przykład, w którym jyoung jest próbka mailNickname użytkownika w Azure Active Directory.

>[!NOTE]
> Jest to tylko przykład. Nie wszyscy użytkownicy będą mieć atrybut mailNickname, a wartość użytkownika nie może być unikatowa w katalogu. Ponadto atrybut używany do dopasowywania (w tym przypadku jest `externalId` ) można skonfigurować w [mapowaniu atrybutów usługi AAD](customize-application-attributes.md).

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

W zapytaniu przykładowym dla użytkownika mającego daną wartość `externalId` atrybutu wartości argumentów przekazane do metody QueryAsync są następujące:

* wejściowe. AlternateFilters. Count: 1
* wejściowe. AlternateFilters. ElementAt (0). AttributePath: "externalId"
* wejściowe. AlternateFilters. ElementAt (0). Operatorporównania: operatorporównania. Equals
* wejściowe. AlternateFilter. ElementAt (0). ComparisonValue: "jyoung"

***Przykład 2. Inicjowanie obsługi administracyjnej użytkownika***

Jeśli odpowiedź na zapytanie do usługi sieci Web dla użytkownika o `externalId` wartości atrybutu, która pasuje do wartości atrybutu mailNickName użytkownika, nie zwróci żadnych użytkowników, a następnie usługi AAD żądają, aby usługa zainicjuje użytkownika odpowiadającego mu w usłudze AAD.  Oto przykład tego żądania: 

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

W przykładowym kodzie żądanie jest tłumaczone na wywołanie metody onasync dostawcy usługi. Oto podpis tej metody: 

```csharp
// System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
// Microsoft.SCIM.IRequest is defined in 
// Microsoft.SCIM.Service.  
// Microsoft.SCIM.Resource is defined in 
// Microsoft.SCIM.Schemas.  

Task<Resource> CreateAsync(IRequest<Resource> request);
```

W żądaniu do aprowizacji użytkownika wartość argumentu zasobu jest wystąpieniem klasy Microsoft. Standard scim. Core2EnterpriseUser, zdefiniowanej w bibliotece Microsoft. Standard scim. Schematys.  Jeśli żądanie zainicjowania obsługi użytkownika zakończyło się pomyślnie, implementacja metody zwróci wystąpienie klasy Microsoft. Standard scim. Core2EnterpriseUser, a wartość właściwości identyfikator jest ustawiona na unikatowy identyfikator nowo zainicjowanego użytkownika.  

***Przykład 3. Zbadaj bieżący stan użytkownika*** 

Aby zaktualizować użytkownika znanego w magazynie tożsamości, z którego korzysta Standard scim, Azure Active Directory kontynuować, żądając bieżącego stanu tego użytkownika od usługi przy użyciu żądania takiego jak: 

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

W przykładzie żądania pozyskania bieżącego stanu użytkownika wartości właściwości obiektu dostarczonego jako wartość argumentu Parameters są następujące w następujących przypadkach: 
  
* Identyfikator: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User"

***Przykład 4. Zbadaj wartość atrybutu odwołania do zaktualizowania*** 

Jeśli atrybut odwołania ma zostać zaktualizowany, Azure Active Directory wysyła zapytanie do usługi w celu ustalenia, czy bieżąca wartość atrybutu odwołania w magazynie tożsamości przedniego przez usługę już pasuje do wartości tego atrybutu w Azure Active Directory. W przypadku użytkowników jedynym atrybutem, którego bieżącą wartością jest zapytanie w ten sposób, jest atrybut Manager. Oto przykład żądania, aby określić, czy atrybut Menedżera obiektu użytkownika ma obecnie określoną wartość: w przykładowym kodzie żądanie jest tłumaczone na wywołanie metody QueryAsync dostawcy usługi. Wartość właściwości obiektu dostarczonego jako wartość argumentu Parameters są następujące: 
  
* wejściowe. AlternateFilters. Count: 2
* wejściowe. AlternateFilters. ElementAt (x). AttributePath: "ID"
* wejściowe. AlternateFilters. ElementAt (x). Operatorporównania: operatorporównania. Equals
* wejściowe. AlternateFilter. ElementAt (x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* wejściowe. AlternateFilters. ElementAt (y). AttributePath: "Manager"
* wejściowe. AlternateFilters. ElementAt (y). Operatorporównania: operatorporównania. Equals
* wejściowe. AlternateFilter. ElementAt (y). ComparisonValue: "2819c223-7f76-453A-919d-413861904646"
* wejściowe. RequestedAttributePaths. ElementAt (0): "ID"
* wejściowe. SchemaIdentifier: "urn: IETF: params: Standard scim: schematy: rozszerzenie: Enterprise: 2.0: User"

W tym miejscu wartość indeksu x może być równa 0, a wartość indeksu y może wynosić 1 lub wartość x może wynosić 1, a wartość y może być równa 0, w zależności od kolejności wyrażeń parametru kwerendy filtru.   

***Przykład 5. Zażądaj od usługi Azure AD, aby zaktualizować użytkownika*** 

Oto przykład żądania od Azure Active Directory do usługi Standard scim w celu zaktualizowania użytkownika: 

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

W przykładzie żądania zaktualizowania użytkownika obiekt dostarczony jako wartość argumentu patch ma następujące wartości właściwości: 

|Argument|Wartość|
|-|-|
|ResourceIdentifier. Identyfikator|"54D382A4-2050-4C03-94D1-E769F1D15682"|
|ResourceIdentifier.SchemaIdentifier|"urn: IETF: params: Standard scim: schematy: Extension: Enterprise: 2.0: User"|
|(PatchRequest jako PatchRequest2). Operations. Count|1|
|(PatchRequest jako PatchRequest2). Operations. ElementAt (0). OperationName|OperationName. Add|
|(PatchRequest jako PatchRequest2). Operations. ElementAt (0). Path. AttributePath|Menedżera|
|(PatchRequest jako PatchRequest2). Operations. ElementAt (0). Wartość. Count|1|
|(PatchRequest jako PatchRequest2). Operations. ElementAt (0). Value. ElementAt (0). Odwoła|http://.../scim/Users/2819c223-7f76-453a-919d-413861904646|
|(PatchRequest jako PatchRequest2). Operations. ElementAt (0). Value. ElementAt (0). Wartościami| 2819c223-7f76-453A-919d-413861904646|

***Przykład 6. Anulowanie aprowizacji użytkownika***

Aby anulować obsługę administracyjną użytkownika z magazynu tożsamości przedniego przez usługę Standard scim, usługa AAD wysyła żądanie, takie jak:

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

Obiekt dostarczony jako wartość argumentu resourceIdentifier ma te wartości właściwości w przykładzie żądania anulowania aprowizacji użytkownika: 

* ResourceIdentifier. Identyfikator: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier. SchemaIdentifier: "urn: IETF: params: Standard scim: schematy: Extension: Enterprise: 2.0: User"

## <a name="integrate-your-scim-endpoint-with-the-aad-scim-client"></a>Integrowanie punktu końcowego Standard scim z klientem usługi AAD Standard scim

Usługę Azure AD można skonfigurować tak, aby automatycznie udostępniać przypisanym użytkownikom i grupom aplikacje implementujące konkretny profil [protokołu standard scim 2,0](https://tools.ietf.org/html/rfc7644). Szczegóły profilu są udokumentowane w [opisie implementacji usługi Azure AD Standard scim](#understand-the-aad-scim-implementation).

Skontaktuj się z dostawcą aplikacji lub dokumentacją dostawcy aplikacji, aby uzyskać instrukcje dotyczące zgodności z tymi wymaganiami.

> [!IMPORTANT]
> Implementacja usługi Azure AD Standard scim jest oparta na usłudze Azure AD User Provisioning, która została zaprojektowana w celu ciągłego utrzymywania synchronizacji użytkowników między usługą Azure AD a aplikacją docelową i implementuje bardzo konkretny zestaw standardowych operacji. Ważne jest, aby zrozumieć te zachowania, aby zrozumieć zachowanie klienta usługi Azure AD Standard scim. Aby uzyskać więcej informacji, zapoznaj się z sekcją [cykle aprowizacji: początkowe i przyrostowe](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) w [sposobie działania aprowizacji](how-provisioning-works.md).

### <a name="getting-started"></a>Wprowadzenie

Aplikacje obsługujące profil Standard scim opisany w tym artykule mogą być połączone z Azure Active Directory przy użyciu funkcji "aplikacja bez galerii" w galerii aplikacji usługi Azure AD. Po nawiązaniu połączenia usługa Azure AD uruchamia proces synchronizacji co 40 minut, gdzie wysyła zapytanie do punktu końcowego Standard scim aplikacji dla przypisanych użytkowników i grup, a następnie tworzy lub modyfikuje je zgodnie z informacjami o przypisaniu.

**Aby połączyć aplikację, która obsługuje standard scim:**

1. Zaloguj się do [portalu usługi AAD](https://aad.portal.azure.com). Należy pamiętać, że możesz uzyskać dostęp do bezpłatnej wersji próbnej Azure Active Directory z licencjami P2, rejestrując się w [programie dla deweloperów](https://developer.microsoft.com/office/dev-program)
1. W okienku po lewej stronie wybierz pozycję **aplikacje dla przedsiębiorstw** . Zostanie wyświetlona lista wszystkich skonfigurowanych aplikacji, w tym aplikacji, które zostały dodane z galerii.
1. Wybierz pozycję **+ Nowa aplikacja**  >  **i Utwórz własną aplikację**.
1. Wprowadź nazwę aplikacji, wybierz opcję "*Zintegruj każdą inną aplikację, której nie ma w galerii*", a następnie wybierz pozycję **Dodaj** , aby utworzyć obiekt aplikacji. Nowa aplikacja zostanie dodana do listy aplikacji dla przedsiębiorstw i zostanie otwarta na swoim ekranie zarządzania aplikacjami.

   ![Zrzut ekranu przedstawia galerię aplikacji usługi Azure AD Galeria aplikacji usługi ](media/use-scim-to-provision-users-and-groups/scim-figure-2b-1.png)
    *Azure AD*

   > [!NOTE]
   > Jeśli używasz starego środowiska galerii aplikacji, postępuj zgodnie z poniższym przewodnikiem ekranu.
   
   ![Zrzut ekranu przedstawia galerię starych aplikacji usługi Azure AD środowisko ](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)
    *galerii starych aplikacji w usłudze Azure AD*

1. Na ekranie Zarządzanie aplikacjami wybierz opcję **Inicjowanie obsługi** w lewym panelu.
1. W menu **tryb aprowizacji** wybierz pozycję **automatycznie**.

   ![Przykład: Strona aprowizacji aplikacji w Azure Portal](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Konfigurowanie aprowizacji w Azure Portal*

1. W polu **adres URL dzierżawy** wprowadź adres URL punktu końcowego Standard scim aplikacji. Przykład: `https://api.contoso.com/scim/`
1. Jeśli punkt końcowy Standard scim wymaga tokenu okaziciela OAuth od wystawcy innego niż usługa Azure AD, a następnie skopiuj wymagany token okaziciela OAuth do pola opcjonalnego **tokenu tajnego** . Jeśli to pole pozostanie puste, usługa Azure AD zawiera token okaziciela OAuth wystawiony przez usługę Azure AD za pomocą każdego żądania. Aplikacje korzystające z usługi Azure AD jako dostawca tożsamości mogą sprawdzić poprawność tego tokenu wystawionego przez usługę Azure AD. 
   > [!NOTE]
   > ***Nie*** zaleca się pozostawienia tego pola pustego i polegania na tokenie wygenerowanym przez usługę Azure AD. Ta opcja jest dostępna głównie do celów testowych.
1. Wybierz pozycję **Testuj połączenie** , aby uzyskać Azure Active Directory próbę nawiązania połączenia z punktem końcowym Standard scim. Jeśli próba nie powiedzie się, zostanie wyświetlony komunikat o błędzie.  

    > [!NOTE]
    > **Test connection** wysyła zapytanie do punktu końcowego Standard scim dla użytkownika, który nie istnieje, przy użyciu losowego identyfikatora GUID jako pasującej właściwości wybranej w konfiguracji usługi Azure AD. Oczekiwana prawidłowa odpowiedź to HTTP 200 OK z pustym komunikatem Standard scim ListResponse.

1. Jeśli próba nawiązania połączenia z aplikacją zakończyła się pomyślnie, wybierz pozycję **Zapisz** , aby zapisać poświadczenia administratora.
1. W sekcji **mapowania** istnieją dwa możliwe do wyboru zestawy [mapowań atrybutów](customize-application-attributes.md): jeden dla obiektów użytkownika i jeden dla obiektów grupy. Wybierz każdą z nich, aby przejrzeć atrybuty, które są synchronizowane z Azure Active Directory do aplikacji. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania do użytkowników i grup w aplikacji w celu wykonywania operacji aktualizacji. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.

    > [!NOTE]
    > Można opcjonalnie wyłączyć synchronizowanie obiektów grupy przez wyłączenie mapowania "grupy".

1. W obszarze **Ustawienia** pole **zakres** określa, którzy użytkownicy i grupy są synchronizowane. Wybierz opcję **Synchronizuj tylko przypisani Użytkownicy i grupy** (zalecane) tylko do synchronizacji użytkowników i grup przypisanych na karcie **Użytkownicy i grupy** .
1. Po zakończeniu konfiguracji Ustaw **stan aprowizacji** na **włączone**.
1. Wybierz pozycję **Zapisz** , aby uruchomić usługę Azure AD Provisioning.
1. W przypadku synchronizacji tylko przypisanych użytkowników i grup (zalecane) należy wybrać kartę **Użytkownicy i grupy** i przypisać użytkowników lub grupy, które mają zostać zsynchronizowane.

Po rozpoczęciu pierwszego cyklu możesz wybrać opcję **dzienniki aprowizacji** w lewym panelu, aby monitorować postęp, który pokazuje wszystkie akcje wykonywane przez usługę aprowizacji w aplikacji. Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](check-status-user-account-provisioning.md).

> [!NOTE]
> Cykl początkowy trwa dłużej niż w przypadku późniejszej synchronizacji, który występuje około co 40 minut, o ile usługa jest uruchomiona.

## <a name="publish-your-application-to-the-aad-application-gallery"></a>Publikowanie aplikacji w galerii aplikacji usługi AAD

Jeśli tworzysz aplikację, która będzie używana przez więcej niż jedną dzierżawę, możesz ją udostępnić w galerii aplikacji usługi Azure AD. Dzięki temu organizacje mogą łatwo odnaleźć aplikację i skonfigurować obsługę administracyjną. Publikowanie aplikacji w galerii usługi Azure AD i udostępnianie jej innym osobom jest proste. Zapoznaj się z opisanymi [tutaj](../develop/v2-howto-app-gallery-listing.md)instrukcjami. Firma Microsoft będzie współpracować z klientem w celu zintegrowania aplikacji z naszą galerią, przetestowania punktu końcowego i udostępnienia [dokumentacji](../saas-apps/tutorial-list.md) do dołączania do klientów.

### <a name="gallery-onboarding-checklist"></a>Lista kontrolna dołączania galerii
Skorzystaj z listy kontrolnej, aby szybko dodać swoją aplikację, a klienci mają bezproblemowe środowisko wdrażania. Informacje zostaną zebrane z użytkownika podczas dołączania do galerii. 
> [!div class="checklist"]
> * Obsługa punktu końcowego użytkownika i grupy [standard scim 2,0](#understand-the-aad-scim-implementation) (tylko jeden jest wymagany, ale oba są zalecane)
> * Obsługa co najmniej 25 żądań na sekundę na dzierżawcę, aby upewnić się, że użytkownicy i grupy są zainicjowani i są cofane bez opóźnień (wymagane)
> * Ustanów kontakty inżynieryjne i pomoc techniczną, aby Przewodnik po dołączaniu do galerii (wymagany)
> * 3 niewygasające poświadczenia testów dla aplikacji (wymagane)
> * Obsługa przydzielenia kodu autoryzacji OAuth lub tokenu długotrwałego, zgodnie z poniższym opisem (wymagane)
> * Ustanów inżynierów inżynieryjnych i pomocy technicznej w celu obsługi klientów po dołączeniu do galerii (wymagane)
> * [Obsługa odnajdywania schematu (wymagane)](https://tools.ietf.org/html/rfc7643#section-6)
> * Obsługa aktualizowania wielu członkostw w grupie za pomocą pojedynczej poprawki
> * Publicznie udokumentowanie punktu końcowego Standard scim

### <a name="authorization-to-provisioning-connectors-in-the-application-gallery"></a>Autoryzacja do łączników aprowizacji w galerii aplikacji
Specyfikacja Standard scim nie definiuje schematu specyficznego dla Standard scim na potrzeby uwierzytelniania i autoryzacji i opiera się na korzystaniu z istniejących standardów branżowych.

|Metoda autoryzacji|Zalety|Wady|Pomoc techniczna|
|--|--|--|--|
|Nazwa użytkownika i hasło (niezalecane lub obsługiwane przez usługę Azure AD)|Łatwa implementacja|Niezabezpieczone — [Twoje PA $ $Word nie ma znaczenia](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Obsługiwane w przypadku aplikacji galerii w przypadku wielkości liter. Nieobsługiwane w przypadku aplikacji innych niż Galeria.|
|Token okaziciela o długim czasie życia|Tokeny długotrwałe nie wymagają obecności użytkownika. Administratorzy mogą łatwo używać podczas konfigurowania aprowizacji.|Tokeny długotrwałe mogą być trudne do udostępnienia administratorowi bez użycia niezabezpieczonych metod, takich jak poczta e-mail. |Obsługiwane w przypadku aplikacji Galeria i innych niż Galeria. |
|Przyznanie kodu autoryzacji OAuth|Tokeny dostępu są znacznie krótsze niż hasła i mają mechanizm zautomatyzowanego odświeżania, który nie ma tokenów okaziciela o długim czasie trwania.  Rzeczywisty użytkownik musi być obecny podczas wstępnej autoryzacji, co umożliwia dodanie poziomu odpowiedzialności. |Wymaga, aby użytkownik był obecny. Jeśli użytkownik opuści organizację, token jest nieprawidłowy, a autoryzacja będzie musiała zostać ukończona ponownie.|Obsługiwane w przypadku aplikacji galerii, ale nie aplikacji innych niż Galeria. Można jednak podać token dostępu w interfejsie użytkownika jako token tajny do celów testowania krótkoterminowego. Obsługa przyznawania kodu OAuth w przypadku braku galerii znajduje się w naszym zaległości, oprócz obsługi konfigurowalnych adresów URL uwierzytelniania/tokenu w aplikacji galerii.|
|Przyznanie poświadczeń klienta OAuth|Tokeny dostępu są znacznie krótsze niż hasła i mają mechanizm zautomatyzowanego odświeżania, który nie ma tokenów okaziciela o długim czasie trwania. Zarówno kod autoryzacji przydzielenia, jak i poświadczenia klienta umożliwiają tworzenie tego samego typu tokenu dostępu, więc przechodzenie między tymi metodami jest niewidoczne dla interfejsu API.  Inicjowanie obsługi może być całkowicie zautomatyzowane i nowe tokeny mogą być wymagane w trybie dyskretnym bez interakcji z użytkownikiem. ||Nieobsługiwane w przypadku aplikacji Galeria i innych niż Galeria. Pomoc techniczna znajduje się w naszej zaległości.|

> [!NOTE]
> Nie zaleca się pozostawiania pola token pustego w interfejsie użytkownika niestandardowej aplikacji konfiguracji aprowizacji usługi AAD. Wygenerowany token jest przeznaczony głównie do celów testowych.

### <a name="oauth-code-grant-flow"></a>Przepływ przyznania kodu OAuth

Usługa aprowizacji obsługuje [przyznawanie kodu autoryzacji](https://tools.ietf.org/html/rfc6749#page-24) i po przesłaniu żądania opublikowania aplikacji w galerii, nasz zespół pomoże Ci zebrać następujące informacje:

- **Adres URL autoryzacji**, adres URL klienta, aby uzyskać autoryzację od właściciela zasobu za pośrednictwem przekierowania agenta użytkownika. Użytkownik zostanie przekierowany do tego adresu URL, aby autoryzować dostęp. 

- **Adres URL wymiany tokenów**, adres URL klienta służący do wymiany autoryzacji dostępu do tokenu, zazwyczaj z uwierzytelnianiem klienta.

- **Identyfikator klienta** serwer autoryzacji wystawia zarejestrowany klientowi identyfikator klienta, który jest unikatowym ciągiem reprezentującym Informacje rejestracyjne dostarczone przez klienta.  Identyfikator klienta nie jest wpisem tajnym; jest on narażony na właściciela zasobu i **nie może** być używany do uwierzytelniania klientów.  

- **Klucz tajny klienta**, klucz tajny wygenerowany przez serwer autoryzacji, który powinien być unikatową wartością znaną tylko serwerowi autoryzacji. 

> [!NOTE]
> Adres **URL autoryzacji** i **adres URL wymiany tokenu** nie są obecnie konfigurowalne dla dzierżawy.

> [!NOTE]
> Uwierzytelnianie OAuth V1 nie jest obsługiwane ze względu na narażenie klucza tajnego klienta. Uwierzytelnianie OAuth v2 jest obsługiwane.  

Najlepsze rozwiązania (zalecane, ale nie wymagane):
* Obsługa wielu adresów URL przekierowań. Administratorzy mogą skonfigurować obsługę administracyjną zarówno z "portal.azure.com", jak i "aad.portal.azure.com". Obsługa wielu adresów URL przekierowania gwarantuje, że użytkownicy będą mogli autoryzować dostęp z dowolnego portalu.
* Obsługa wielu wpisów tajnych w celu łatwego odnawiania bez przestojów. 

#### <a name="how-to-setup-oauth-code-grant-flow"></a>Jak skonfigurować przepływ przyznawania kodu OAuth

1. Zaloguj się do Azure Portal, przejdź do aplikacji **korporacyjnych aplikacje dla przedsiębiorstw**  >    >   i wybierz pozycję **Autoryzuj**.

   1. Azure Portal przekierowuje użytkownika do adresu URL autoryzacji (strona logowania dla aplikacji innej firmy).

   1. Administrator udostępnia poświadczenia aplikacji innej firmy. 

   1. Aplikacja innej firmy przekierowuje użytkownika z powrotem do Azure Portal i udostępnia kod dotacji 

   1. Usługi Azure AD Provisioning są wywołaniem adresu URL tokenu i udostępniają kod przydzielenia. Aplikacja innej firmy odpowiada za pomocą tokenu dostępu, tokenu odświeżania i daty wygaśnięcia

1. Po rozpoczęciu cyklu aprowizacji usługa sprawdza, czy bieżący token dostępu jest prawidłowy i w razie potrzeby wymienia go z nowym tokenem. Token dostępu jest udostępniany w każdym żądaniu wykonywanym w aplikacji, a ważność żądania jest sprawdzana przed każdym żądaniem.

> [!NOTE]
> Chociaż nie jest możliwe skonfigurowanie uwierzytelniania OAuth w aplikacjach spoza galerii, można ręcznie wygenerować token dostępu z serwera autoryzacji i wprowadzić go jako token tajny w aplikacji innej niż Galeria. Dzięki temu można sprawdzić zgodność serwera Standard scim z klientem usługi AAD Standard scim przed dołączeniem do galerii aplikacji, która obsługuje przyznanie kodu OAuth.  

**Tokeny okaziciela OAuth o długim czasie trwania:** Jeśli aplikacja nie obsługuje przepływu przydzielenia kodu autoryzacji OAuth, zamiast tego Generuj token okaziciela OAuth o długim czasie trwania, za pomocą którego administrator może skonfigurować integrację aprowizacji. Token powinien być tymczasowy lub w przeciwnym razie zadanie aprowizacji zostanie poddane [kwarantannie](application-provisioning-quarantine-status.md) po wygaśnięciu tokenu.

Aby uzyskać dodatkowe metody uwierzytelniania i autoryzacji, daj nam znać w witrynie [UserVoice](https://aka.ms/appprovisioningfeaturerequest).

### <a name="gallery-go-to-market-launch-check-list"></a>Lista kontrolna uruchamiania z galerii przejdź do rynku
Aby zwiększyć świadomość i zapotrzebowanie naszej integracji, Zalecamy zaktualizowanie istniejącej dokumentacji i wzmocnienie integracji w kanałach marketingowych.  Poniżej znajduje się zestaw działań list kontrolnych, zalecamy zakończenie obsługi uruchamiania

> [!div class="checklist"]
> * Upewnij się, że zespoły ds. sprzedaży i obsługi klienta są świadome, gotowe i mogą mówić do możliwości integracji. Krótkie zespoły, udostępniają im często zadawane pytania i obejmują integrację z materiałami sprzedaży. 
> * Umieść wpis w blogu lub wersję próbną opisującą integrację, zalety i sposób rozpoczynania pracy. [Przykład: Niewprowadzenie i Azure Active Directory Press](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
> * Skorzystaj z mediów społecznościowych, takich jak Twitter, Facebook lub LinkedIn, aby promować integrację z klientami. Pamiętaj, aby dołączać się do @AzureAD Twojego wpisu. [Przykład: Niemniej wpis w serwisie Twitter](https://twitter.com/azuread/status/1123964502909779968)
> * Utwórz lub zaktualizuj strony marketingowe/witrynę sieci Web (np. stronę integracji, stronę partnera, stronę cennika itp.) w celu uwzględnienia dostępności wspólnej integracji. [Przykład: Strona integracji Pingboard](https://pingboard.com/org-chart-for), [Strona integracji narzędzia Smartsheet](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), [Strona z cennikiem Monday.com](https://monday.com/pricing/) 
> * Utwórz artykuł centrum pomocy lub dokumentację techniczną dotyczącą sposobu, w jaki klienci mogą rozpocząć pracę. [Przykład: wysłannika + Microsoft Azure Active Directory integrację.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
> * Powiadamiaj klientów o nowej integracji za pośrednictwem komunikacji z klientem (Biuletyny Miesięczne, kampanie e-mail i informacje o wersji produktu). 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Opracowywanie przykładowego punktu końcowego Standard scim](use-scim-to-build-users-and-groups-endpoints.md) 
>  [Automatyzowanie aprowizacji użytkowników i anulowanie obsługi aplikacji SaaS](user-provisioning.md) 
>  [Dostosuj mapowania atrybutów na potrzeby aprowizacji użytkowników](customize-application-attributes.md) 
>  [Pisanie wyrażeń do mapowania atrybutów](functions-for-customizing-application-data.md) 
>  [Filtry zakresu dla aprowizacji użytkowników](define-conditional-rules-for-provisioning-user-accounts.md) 
>  [Powiadomienia o](user-provisioning.md) 
>  aprowizacji konta [Lista samouczków dotyczących integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)
