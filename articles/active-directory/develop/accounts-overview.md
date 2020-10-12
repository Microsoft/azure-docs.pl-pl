---
title: Konta platformy tożsamości firmy Microsoft & profilów dzierżawy w systemie Android | Azure
description: Omówienie kont platformy tożsamości firmy Microsoft dla systemu Android
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.devlang: java
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev, devx-track-java
ms.reviewer: shoatman
ms.openlocfilehash: 404ffbc09a69b623a421bd0c01550d72e5c03158
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88115989"
---
# <a name="accounts--tenant-profiles-android"></a>Konta i profile dzierżaw (Android)

Ten artykuł zawiera omówienie `account` platformy tożsamości firmy Microsoft.

Interfejs API Microsoft Authentication Library (MSAL) *zastępuje termin* *użytkownika* terminem. Jedną z przyczyn jest to, że użytkownik (Agent ludzki lub programowy) może mieć lub użyć wielu kont. Te konta mogą znajdować się w organizacji użytkownika i/lub w innych organizacjach, do których należy użytkownik.

Konto na platformie tożsamości firmy Microsoft składa się z:

- Unikatowy identyfikator.  
- Co najmniej jedno poświadczenie używane do zademonstrowania własności/kontroli konta.
- Co najmniej jeden profil składający się z atrybutów, takich jak:
  - Zdjęcie, imię i nazwisko, tytuł, Lokalizacja biura
- Konto ma źródło urzędu lub systemu rekordów. Jest to system, w którym konto zostanie utworzone, i miejsce, w którym są przechowywane poświadczenia skojarzone z tym kontem. W systemach z wieloma dzierżawcami, takimi jak platforma tożsamości firmy Microsoft, system rekordów to miejsce, w `tenant` którym konto zostało utworzone. Ta dzierżawa jest również nazywana `home tenant` .
- Konta na platformie tożsamości firmy Microsoft mają następujące systemy rekordów:
  - Azure Active Directory, w tym Azure Active Directory B2C.
  - Konto Microsoft (na żywo).
- Konta z systemów rekordu poza platformą tożsamości firmy Microsoft są reprezentowane w ramach platformy tożsamości firmy Microsoft, w tym:
  - tożsamości z połączonych katalogów lokalnych (Windows Server Active Directory)
  - tożsamości zewnętrzne z serwisu LinkedIn, GitHub i tak dalej.
  W takich przypadkach konto ma zarówno system pochodzenia rekordu, jak i system rekordów w ramach platformy tożsamości firmy Microsoft.
- Platforma tożsamości firmy Microsoft umożliwia korzystanie z jednego konta do uzyskiwania dostępu do zasobów należących do wielu organizacji (Azure Active Directory dzierżawców).
  - Aby zarejestrować konto z jednego systemu rekordu (dzierżawa usługi AAD A) miało dostęp do zasobu w innym systemie rekordów (dzierżawa usługi AAD B), konto musi być reprezentowane w dzierżawie, w której jest zdefiniowany zasób. Jest to realizowane przez utworzenie lokalnego rekordu konta z systemu A w systemie B.
  - Ten rekord lokalny, który jest reprezentacją konta, jest powiązany z oryginalnym kontem.
  - MSAL uwidacznia ten rekord lokalny jako `Tenant Profile` .
  - Profil dzierżawy może mieć różne atrybuty, które są odpowiednie dla kontekstu lokalnego, takie jak stanowisko, Lokalizacja biura, informacje kontaktowe itp.
- Ponieważ konto może być obecne w co najmniej jednej dzierżawie, konto może mieć więcej niż jeden profil.

> [!NOTE]
> MSAL traktuje system konto Microsoft (na żywo, MSA) jako inną dzierżawę w ramach platformy tożsamości firmy Microsoft. Identyfikator dzierżawy dzierżawy konto Microsoft: `9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>Diagram omówienia konta

![Diagram omówienia konta](./media/accounts-overview/accounts-overview.svg)

Na powyższym diagramie:

- Konto `bob@contoso.com` jest tworzone w lokalnym systemie Windows Server Active Directory (pierwotny system lokalny rekordu).
- Konto `tom@live.com` zostanie utworzone w dzierżawie konto Microsoft.
- `bob@contoso.com` ma dostęp do co najmniej jednego zasobu w następujących Azure Active Directory dzierżawców:
  - contoso.com (system w chmurze rekordu — połączony z systemem lokalnym rekordu)
  - fabrikam.com
  - woodgrovebank.com
  - Profil dzierżawy `bob@contoso.com` istnieje w każdej z tych dzierżawców.
- `tom@live.com` ma dostęp do zasobów w następujących dzierżawcach firmy Microsoft:
  - contoso.com
  - fabrikam.com
  - Profil dzierżawy `tom@live.com` istnieje w każdej z tych dzierżawców.
- Informacje o postaci Tomasz i Roberta w innych dzierżawcach mogą się różnić od tego w systemie rejestrowania. Mogą się one różnić w zależności od atrybutów, takich jak stanowisko, Lokalizacja biura i tak dalej. Mogą być członkami grup i/lub ról w ramach każdej organizacji (Azure Active Directory dzierżawy). Te informacje są nazywane bob@contoso.com profilem dzierżawy.

Na diagramie bob@contoso.com i tom@live.com dostęp do zasobów w różnych dzierżawach Azure Active Directory. Aby uzyskać więcej informacji, zobacz [dodawanie Azure Active Directory użytkowników współpracy B2B w Azure Portal](../external-identities/add-users-administrator.md).

## <a name="accounts-and-single-sign-on-sso"></a>Konta i logowanie jednokrotne (SSO)

Pamięć podręczna tokenów MSAL przechowuje *pojedynczy token odświeżania* dla każdego konta. Ten token odświeżania może służyć do dyskretnego żądania tokenów dostępu z wielu dzierżawców platformy tożsamości firmy Microsoft. Gdy Broker jest zainstalowany na urządzeniu, konto jest zarządzane przez brokera, a możliwość logowania jednokrotnego na poziomie urządzenia.

> [!IMPORTANT]
> Zachowanie konta Business-Consumer (B2C) i tokenu odświeżania różni się od pozostałej części platformy tożsamości firmy Microsoft. Aby uzyskać więcej informacji, zobacz [B2C Policies & accounts](#b2c-policies--accounts).

## <a name="account-identifiers"></a>Identyfikatory kont

Identyfikator konta MSAL nie jest IDENTYFIKATORem obiektu konta. Nie ma potrzeby przeanalizować i/lub opierać się na przekazaniem informacji innych niż unikatowość w ramach platformy tożsamości firmy Microsoft.

Aby zapewnić zgodność z biblioteką uwierzytelniania usługi Azure AD (ADAL), a także ułatwić migrację z biblioteki ADAL do MSAL, usługa MSAL może wyszukiwać konta przy użyciu dowolnego prawidłowego identyfikatora konta dostępnego w pamięci podręcznej MSAL.  Na przykład następujące elementy będą zawsze pobierać ten sam obiekt konta dla, tom@live.com ponieważ każdy z identyfikatorów jest prawidłowy:

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>Uzyskiwanie dostępu do oświadczeń dotyczących konta

Oprócz żądania tokenu dostępu MSAL również zawsze żąda tokenu identyfikatora od poszczególnych dzierżawców. Robi to, zawsze żądając następujących zakresów:

- OpenID Connect
- profil

Token identyfikatora zawiera listę oświadczeń. `Claims` są parami nazw/wartości na koncie i są używane do żądania.

Jak wspomniano wcześniej, każda dzierżawa, w której istnieje konto, może przechowywać różne informacje o koncie, w tym m.in.: stanowisko, Lokalizacja biura i tak dalej.

Chociaż konto może być członkiem lub gościem w wielu organizacjach, MSAL nie wysyła zapytania do usługi w celu uzyskania listy dzierżawców, do których należy konto. Zamiast tego MSAL tworzy listę dzierżawców, w których konto jest obecne w wyniku żądań tokenów, które zostały wykonane.

Oświadczenia uwidocznione na obiekcie konta są zawsze oświadczeniami z/{Authority} "dzierżawy domowej" dla konta. Jeśli to konto nie zostało użyte do żądania tokenu dla dzierżawy głównej, MSAL nie może zapewnić oświadczeń za pośrednictwem obiektu account.  Na przykład:

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> Aby wyświetlić listę oświadczeń dostępnych w obiekcie konta, zapoznaj się z [oświadczeniami w id_token](./id-tokens.md#claims-in-an-id_token)

> [!TIP]
> Aby uwzględnić dodatkowe oświadczenia w id_token, zapoznaj się z opcjonalną dokumentacją dotyczącą oświadczeń w temacie [How to: Podaj opcjonalne oświadczenia do aplikacji usługi Azure AD](./active-directory-optional-claims.md)

### <a name="access-tenant-profile-claims"></a>Dostęp do oświadczeń profilu dzierżawy

Aby uzyskać dostęp do oświadczeń dotyczących konta, które są wyświetlane w innych dzierżawcach, należy najpierw rzutować obiekt konta na `IMultiTenantAccount` . Wszystkie konta mogą być wielodostępne, ale liczba profilów dzierżawy dostępnych za pośrednictwem usługi MSAL jest oparta na tym, z których dzierżawców żądali tokenów przy użyciu bieżącego konta.  Na przykład:

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>Zasady B2Cymi & kont

Tokeny odświeżania dla konta nie są współużytkowane przez zasady B2Cymi. W związku z tym nie jest możliwe logowanie jednokrotne przy użyciu tokenów. Nie oznacza to, że logowanie jednokrotne nie jest możliwe. Oznacza to, że logowanie jednokrotne musi korzystać z interaktywnego środowiska, w którym jest dostępny plik cookie umożliwiający logowanie jednokrotne.

Oznacza to również, że w przypadku MSAL, Jeśli uzyskujesz tokeny przy użyciu różnych zasad B2C, są one traktowane jako osobne konta — z ich własnymi identyfikatorami. Jeśli chcesz użyć konta do żądania tokenu przy użyciu `acquireTokenSilent` , musisz wybrać konto z listy kont, które są zgodne z zasadami używanymi z żądaniem tokenu. Na przykład:

```java
// Get Account For Policy

String policyId = "SignIn";
IAccount signInPolicyAccount = getAccountForPolicyId(app, policyId);

private IAccount getAccountForPolicy(IPublicClientApplication app, String policyId)
{
    List<IAccount> accounts = app.getAccounts();

    foreach(IAccount account : accounts)
   {
        if (account.getClaims().get("tfp").equals(policyId))
        {
            return account;
        }
    }

    return null;
}
```