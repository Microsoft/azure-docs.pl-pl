---
title: Wypełnianie wartości UserPrincipalName w usłudze Azure AD
description: W poniższym dokumencie opisano sposób wypełniania atrybutu UserPrincipalName.
author: billmath
ms.subservice: hybrid
ms.author: billmath
ms.date: 06/26/2018
ms.topic: conceptual
ms.workload: identity
ms.service: active-directory
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c748df10e432e3bebbce0dc8cb39dd2101d52e2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680044"
---
# <a name="azure-ad-userprincipalname-population"></a>Wypełnianie wartości UserPrincipalName w usłudze Azure AD

W tym artykule opisano sposób wypełniania atrybutu UserPrincipalName w usłudze Azure Active Directory (Azure AD).
Wartość atrybutu UserPrincipalName jest nazwą użytkownika usługi Azure AD dla kont użytkowników.

## <a name="upn-terminology"></a>Terminologia UPN
W tym artykule używana jest następująca terminologia:

|Termin|Opis|
|-----|-----|
|Domena początkowa|Domena domyślna (onmicrosoft.com) w dzierżawie usługi Azure AD. Na przykład contoso.onmicrosoft.com.|
|Adres routingu poczty e-mail firmy Microsoft Online (MOERA)|Usługa Azure AD oblicza atrybut MOERA z usługi Azure AD &lt;MailNickName&gt; i domenę początkową usługi Azure AD jako adres MailNickName&#64;domenie &lt;&gt;początkowej.|
|Atrybut MailNatamie lokalnej|Atrybut w usłudze Active Directory, którego wartość reprezentuje alias użytkownika w organizacji programu Exchange.|
|Atrybut poczty lokalnej|Atrybut w usłudze Active Directory, którego wartość reprezentuje adres e-mail użytkownika|
|Podstawowy adres SMTP|Podstawowy adres e-mail obiektu adresata programu Exchange. Na przykład SMTP:user\@contoso.com.|
|Alternatywny identyfikator logowania|Atrybut lokalny inny niż UserPrincipalName, taki jak atrybut mail, używany do logowania.|

## <a name="what-is-userprincipalname"></a>Co to jest UserPrincipalName?
UserPrincipalName jest atrybutem, który jest nazwą logowania w stylu internetowym dla użytkownika na podstawie standardowego internetowego [RFC 822](https://www.ietf.org/rfc/rfc0822.txt). 

### <a name="upn-format"></a>Format upn
Nazwa UPN składa się z prefiksu nazwy UŻYTKOWNIKA (nazwa konta użytkownika) i sufiksu nazwy UPN (nazwa domeny DNS). Prefiks jest połączony z sufiksem przy użyciu symbolu "\@". Na przykład "ktoś\@example.com". Numer UPN musi być unikatowy dla wszystkich obiektów głównych zabezpieczeń w lesie katalogu. 

## <a name="upn-in-azure-ad"></a>Usługa UPN w usłudze Azure AD 
Usługa UPN jest używana przez usługę Azure AD w celu umożliwienia użytkownikom logowania.  Nazwa UPN, z której użytkownik może korzystać, zależy od tego, czy domena została zweryfikowana.  Jeśli domena została zweryfikowana, użytkownik z tym sufiksem będzie mógł zalogować się do usługi Azure AD.  

Atrybut jest synchronizowany przez usługę Azure AD Connect.  Podczas instalacji można wyświetlić domeny, które zostały zweryfikowane i te, które nie zostały zweryfikowane.

   ![Niezweryfikowane domeny](./media/plan-connect-userprincipalname/unverifieddomain.png) 

## <a name="alternate-login-id"></a>Alternatywny identyfikator logowania
W niektórych środowiskach użytkownicy końcowi mogą być świadomi tylko swojego adresu e-mail, a nie ich upn.  Korzystanie z adresu e-mail może być spowodowane zasadami firmowymi lub zależnością aplikacji lokalnej.

Alternatywny identyfikator logowania umożliwia skonfigurowanie środowiska logowania, w którym użytkownicy mogą logować się za pomocą atrybutu innego niż ich nazwa UPN, takiego jak poczta.

Aby włączyć alternatywny identyfikator logowania za pomocą usługi Azure AD, podczas korzystania z usługi Azure AD Connect nie są wymagane żadne dodatkowe kroki konfiguracji. Można skonfigurować alternatywne identyfikator bezpośrednio z kreatora. Zobacz konfigurację logowania usługi Azure AD dla użytkowników w sekcji Synchronizacja. W obszarze listy rozwijanej **Nazwa główna użytkownika** wybierz atrybut dla alternatywnego identyfikatora logowania.

![Niezweryfikowane domeny](./media/plan-connect-userprincipalname/altloginid.png)  

Aby uzyskać więcej informacji, zobacz [Konfigurowanie alternatywnego identyfikatora logowania](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) i [konfiguracji logowania usługi Azure AD](how-to-connect-install-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>Niezweryfikowany sufiks upn
Jeśli lokalny atrybut UserPrincipalName/alternatywny sufiks identyfikatora logowania nie jest weryfikowany za pomocą dzierżawy usługi Azure AD, wartość atrybutu Azure AD UserPrincipalName jest ustawiona na MOERA. Usługa Azure AD oblicza moera z atrybutu Azure AD MailNickName i domeny początkowej usługi Azure AD &lt;jako MailNickName&gt;&#64;domenie &lt;&gt;początkowej .

## <a name="verified-upn-suffix"></a>Zweryfikowany sufiks sieci UPN
Jeśli lokalny atrybut UserPrincipalName/alternatywny sufiks identyfikatora logowania jest weryfikowany za pomocą dzierżawy usługi Azure AD, wartość atrybutu Azure AD UserPrincipalName będzie taka sama jak lokalna wartość atrybutu UserPrincipalName/alternatywna wartość identyfikatora logowania.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Obliczanie wartości atrybutu usługi Azure MailNickName
Ponieważ wartość atrybutu Azure AD UserPrincipalName może być ustawiona na MOERA, ważne jest, aby zrozumieć, jak obliczana jest wartość atrybutu Azure AD MailNickName, która jest prefiksem MOERA.

Gdy obiekt użytkownika jest synchronizowany z dzierżawą usługi Azure AD po raz pierwszy, usługa Azure AD sprawdza następujące elementy w danej kolejności i ustawia wartość atrybutu MailNickName na pierwszą istniejącą wartość:

- Atrybut MailNatamie lokalnej
- Prefiks podstawowego adresu SMTP
- Prefiks lokalnego atrybutu poczty
- Prefiks lokalnego atrybutu PrincipalName/alternatywny identyfikator logowania
- Prefiks pomocniczego adresu smtp

Gdy aktualizacje obiektu użytkownika są synchronizowane z dzierżawą usługi Azure AD, usługa Azure AD aktualizuje wartość atrybutu MailNickName tylko w przypadku aktualizacji lokalnej wartości atrybutu mailNickName.

>[!IMPORTANT]
>Usługa Azure AD ponownie oblicza wartość atrybutu UserPrincipalName tylko w przypadku, gdy aktualizacja lokalnego atrybutu UserPrincipalName/alternatywna wartość identyfikatora logowania jest synchronizowana z dzierżawą usługi Azure AD. 
>
>Za każdym razem, gdy usługa Azure AD ponownie oblicza UserPrincipalName atrybut, również ponownie oblicza MOERA. 

## <a name="upn-scenarios"></a>Scenariusze upn
Poniżej przedstawiono przykładowe scenariusze obliczania nazwy UPN na podstawie danego scenariusza.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>Scenariusz 1: Niezweryfikowany sufiks sieci UPN – synchronizacja początkowa

![Scenariusz1](./media/plan-connect-userprincipalname/example1.png)

Lokalny obiekt użytkownika:
- mailNickName &lt;: nie ustawiono&gt;
- proxyAddresses :SMTP:us1@contoso.com{ }
- Poczty:us2@contoso.com
- Userprincipalname:us3@contoso.com

Zsynchronizowanie obiektu użytkownika z dzierżawą usługi Azure AD po raz pierwszy
- Ustaw atrybut Azure MAILNickName na podstawowy prefiks adresu SMTP.
- Ustaw moera &lt;mailnickname&gt;&#64;&lt;domenie&gt;początkowej .
- Ustaw atrybut UserPrincipalName usługi Azure AD na MOERA.

Obiekt użytkownika dzierżawy usługi Azure AD:
- MailNickName : us1           
- Userprincipalname:us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>Scenariusz 2: Niezweryfikowany sufiks nazwy UPN — atrybut lokalnej poczty

![Scenariusz2](./media/plan-connect-userprincipalname/example2.png)

Lokalny obiekt użytkownika:
- mailNickName : us4
- proxyAddresses :SMTP:us1@contoso.com{ }
- Poczty:us2@contoso.com
- Userprincipalname:us3@contoso.com

Synchronizowanie aktualizacji w lokalnym atrybucie mailNickName z dzierżawą usługi Azure AD
- Zaktualizuj atrybut Usługi Azure AD MailNickName za pomocą lokalnego atrybutu mailNickName.
- Ponieważ nie ma aktualizacji do lokalnego atrybutu userPrincipalName, nie ma żadnych zmian w atrybucie Azure AD UserPrincipalName.

Obiekt użytkownika dzierżawy usługi Azure AD:
- MailNickName : us4
- Userprincipalname:us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>Scenariusz 3: Niezweryfikowany sufiks nazwy UPN — aktualizacja lokalnego atrybutu userPrincipalName

![Scenariusz3](./media/plan-connect-userprincipalname/example3.png)

Lokalny obiekt użytkownika:
- mailNickName : us4
- proxyAddresses :SMTP:us1@contoso.com{ }
- Poczty:us2@contoso.com
- Userprincipalname:us5@contoso.com

Synchronizowanie aktualizacji atrybutu userPrincipalName w środowisku lokalnym do dzierżawy usługi Azure AD
- Aktualizacja lokalnego atrybutu userPrincipalName wyzwala ponowne obliczanie atrybutu MOERA i Azure AD UserPrincipalName.
- Ustaw moera &lt;mailnickname&gt;&#64;&lt;domenie&gt;początkowej .
- Ustaw atrybut UserPrincipalName usługi Azure AD na MOERA.

Obiekt użytkownika dzierżawy usługi Azure AD:
- MailNickName : us4
- Userprincipalname:us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>Scenariusz 4: Niezweryfikowany sufiks nazwy UPN — aktualizowanie podstawowego adresu SMTP i lokalnego atrybutu poczty

![Scenariusz4](./media/plan-connect-userprincipalname/example4.png)

Lokalny obiekt użytkownika:
- mailNickName : us4
- proxyAddresses :SMTP:us6@contoso.com{ }
- Poczty:us7@contoso.com
- Userprincipalname:us5@contoso.com

Synchronizowanie aktualizacji atrybutu lokalnej poczty i podstawowego adresu SMTP z dzierżawą usługi Azure AD
- Po początkowej synchronizacji obiektu użytkownika aktualizacje atrybutu poczty lokalnej i podstawowego adresu SMTP nie będą miały wpływu na atrybut Azure AD MailNickName ani UserPrincipalName.

Obiekt użytkownika dzierżawy usługi Azure AD:
- MailNickName : us4
- Userprincipalname:us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>Scenariusz 5: Zweryfikowany sufiks nazwy UPN — aktualizacja sufiksu atrybutu userPrincipalName w środowisku lokalnym

![Scenariusz5](./media/plan-connect-userprincipalname/example5.png)

Lokalny obiekt użytkownika:
- mailNickName : us4
- proxyAddresses :SMTP:us6@contoso.com{ }
- Poczty:us7@contoso.com
- Userprincipalname:us5@verified.contoso.com

Synchronizowanie aktualizacji atrybutu userPrincipalName w środowisku lokalnym do dzierżawy usługi Azure AD
- Aktualizacja lokalnego atrybutu userPrincipalName wyzwala ponowne obliczanie atrybutu Azure AD UserPrincipalName.
- Ustaw atrybut UserPrincipalName usługi Azure AD na lokalny atrybut userPrincipalName, ponieważ sufiks nazwy UPN jest weryfikowany za pomocą dzierżawy usługi Azure AD.

Obiekt użytkownika dzierżawy usługi Azure AD:
- MailNickName : us4     
- Userprincipalname:us5@verified.contoso.com

## <a name="next-steps"></a>Następne kroki
- [Integrowanie katalogów lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
- [Niestandardowa instalacja programu Azure AD Connect](how-to-connect-install-custom.md)
