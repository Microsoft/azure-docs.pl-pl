---
title: Wypełnianie wartości UserPrincipalName w usłudze Azure AD
description: W poniższym dokumencie opisano sposób wypełnienia atrybutu UserPrincipalName.
author: billmath
ms.subservice: hybrid
ms.author: billmath
ms.date: 06/26/2018
ms.topic: conceptual
ms.workload: identity
ms.service: active-directory
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b4bbcac110398ee4ff132b76ce8c4868ee17f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91317595"
---
# <a name="azure-ad-userprincipalname-population"></a>Wypełnianie wartości UserPrincipalName w usłudze Azure AD

W tym artykule opisano, jak atrybut UserPrincipalName jest wypełniany w Azure Active Directory (Azure AD).
Wartość atrybutu UserPrincipalName to nazwa użytkownika usługi Azure AD dla kont użytkowników.

## <a name="upn-terminology"></a>Terminologia UPN
W tym artykule jest używana następująca terminologia:

|Okres|Opis|
|-----|-----|
|Domena początkowa|Domena domyślna (onmicrosoft.com) w dzierżawie usługi Azure AD. Na przykład contoso.onmicrosoft.com.|
|Adres E-mail usługi Microsoft Online (MOERA)|Usługa Azure AD oblicza MOERA z atrybutu MailNickName usługi Azure AD i domeny początkowej usługi Azure AD jako &lt; &gt; &lt; domeny początkowej MailNickName&#64;&gt; .|
|Lokalny atrybut mailNickName|Atrybut w Active Directory, wartość reprezentująca alias użytkownika w organizacji programu Exchange.|
|Lokalny atrybut poczty|Atrybut w Active Directory, wartość reprezentująca adres e-mail użytkownika.|
|Podstawowy adres SMTP|Podstawowy adres e-mail obiektu odbiorcy programu Exchange. Na przykład SMTP: User \@ contoso.com.|
|Alternatywny identyfikator logowania|Atrybut lokalny inny niż UserPrincipalName, taki jak atrybut mail używany do logowania.|

## <a name="what-is-userprincipalname"></a>Co to jest UserPrincipalName?
UserPrincipalName to atrybut, który jest nazwą logowania w formacie internetowym dla użytkownika w oparciu o internetowy standard [RFC 822](https://www.ietf.org/rfc/rfc0822.txt). 

### <a name="upn-format"></a>Format nazwy UPN
Nazwa UPN składa się z prefiksu UPN (nazwy konta użytkownika) i sufiksu UPN (nazwy domeny DNS). Prefiks jest przyłączony do sufiksu przy użyciu \@ symbolu "". Na przykład "ktoś \@ example.com". Nazwa UPN musi być unikatowa wśród wszystkich obiektów podmiotu zabezpieczeń w lesie katalogu. 

## <a name="upn-in-azure-ad"></a>Nazwa UPN w usłudze Azure AD 
Nazwa UPN jest używana przez usługę Azure AD, aby umożliwić użytkownikom logowanie się.  Nazwa UPN, która może być używana przez użytkownika, zależy od tego, czy domena została zweryfikowana.  Jeśli domena została zweryfikowana, użytkownik mający ten sufiks będzie mógł zalogować się do usługi Azure AD.  

Ten atrybut jest synchronizowany przez Azure AD Connect.  Podczas instalacji można wyświetlić zweryfikowane domeny oraz te, które nie zostały przez nie skonfigurowane.

   ![Niezweryfikowane domeny](./media/plan-connect-userprincipalname/unverifieddomain.png) 

## <a name="alternate-login-id"></a>Alternatywny identyfikator logowania
W niektórych środowiskach użytkownicy końcowi mogą mieć świadomość swojego adresu e-mail, a nie nazwy UPN.  Korzystanie z adresu e-mail może być spowodowane przez zasady firmowe lub lokalną zależność aplikacji biznesowych.

Alternatywny identyfikator logowania umożliwia skonfigurowanie środowiska logowania, w którym użytkownicy mogą logować się przy użyciu atrybutu innego niż nazwy UPN, na przykład poczta.

Aby włączyć alternatywny identyfikator logowania w usłudze Azure AD, podczas korzystania z Azure AD Connect nie są wymagane żadne dodatkowe czynności konfiguracyjne. Można skonfigurować alternatywne identyfikator bezpośrednio z kreatora. Zapoznaj się z tematem Konfiguracja logowania usługi Azure AD dla użytkowników w sekcji Synchronizacja. Z listy rozwijanej **główna nazwa użytkownika** Wybierz atrybut alternatywnego identyfikatora logowania.

![Zrzut ekranu, który wyróżnia listę głównych nazw użytkowników, w których wybierasz alternatywny atrybut identyfikatora logowania.](./media/plan-connect-userprincipalname/altloginid.png)  

Aby uzyskać więcej informacji, zobacz [Konfigurowanie alternatywnego identyfikatora logowania](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) i [konfiguracji logowania usługi Azure AD](how-to-connect-install-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>Niezweryfikowany sufiks nazwy UPN
Jeśli lokalny atrybut UserPrincipalName/alternatywny sufiks identyfikatora logowania nie jest weryfikowany przez dzierżawę usługi Azure AD, wartość atrybutu UserPrincipalName usługi Azure AD jest ustawiona na MOERA. Usługa Azure AD oblicza MOERA z atrybutu MailNickName usługi Azure AD i domeny początkowej usługi Azure AD &lt; jako &gt; &lt; domeny początkowej MailNickName&#64;&gt; .

## <a name="verified-upn-suffix"></a>Zweryfikowano sufiks nazwy UPN
Jeśli lokalny atrybut UserPrincipalName/alternatywny sufiks identyfikatora logowania zostanie zweryfikowany za pomocą dzierżawy usługi Azure AD, wartość atrybutu UserPrincipalName usługi Azure AD będzie taka sama jak wartość lokalnego atrybutu UserPrincipalName/alternatywny identyfikator logowania.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Obliczanie wartości atrybutu MailNickName usługi Azure AD
Ponieważ wartość atrybutu UserPrincipalName usługi Azure AD może być ustawiona na MOERA, ważne jest, aby zrozumieć, jak jest obliczana wartość atrybutu MailNickName usługi Azure AD, która jest prefiksem MOERA.

Gdy obiekt użytkownika jest synchronizowany z dzierżawą usługi Azure AD po raz pierwszy, usługa Azure AD sprawdza następujące elementy w danej kolejności i ustawia wartość atrybutu MailNickName na pierwszą istniejącą:

- Lokalny atrybut mailNickName
- Prefiks podstawowego adresu SMTP
- Prefiks lokalnego atrybutu poczty
- Prefiks lokalnego atrybutu userPrincipalName/alternatywnego identyfikatora logowania
- Prefiks pomocniczego adresu SMTP

Gdy aktualizacje obiektu użytkownika są synchronizowane z dzierżawą usługi Azure AD, usługa Azure AD aktualizuje wartość atrybutu MailNickName tylko w przypadku, gdy istnieje aktualizacja lokalnej wartości atrybutu mailNickName.

>[!IMPORTANT]
>Usługa Azure AD ponownie oblicza wartość atrybutu UserPrincipalName tylko w przypadku, gdy aktualizacja lokalnego atrybutu UserPrincipalName/alternatywna wartość identyfikatora logowania jest synchronizowana z dzierżawą usługi Azure AD. 
>
>Za każdym razem, gdy usługa Azure AD ponownie oblicza atrybut UserPrincipalName, ponownie oblicza MOERA. 

## <a name="upn-scenarios"></a>Scenariusze nazw UPN
Poniżej przedstawiono przykładowe scenariusze obliczania nazwy UPN w oparciu o dany scenariusz.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>Scenariusz 1: nie zweryfikowano sufiksu UPN — synchronizacja początkowa

![Scenario1](./media/plan-connect-userprincipalname/example1.png)

Lokalny obiekt użytkownika:
- mailNickName: &lt; nie ustawiono&gt;
- proxyAddresses: { SMTP:us1@contoso.com }
- poczt us2@contoso.com
- userPrincipalName us3@contoso.com

Synchronizowanie obiektu użytkownika z dzierżawą usługi Azure AD po raz pierwszy
- Ustaw atrybut MailNickName usługi Azure AD na podstawowy prefiks adresu SMTP.
- Ustaw MOERA na  &lt; MailNickName &gt;&#64;&lt; początkowej domeny &gt; .
- Ustaw atrybut UserPrincipalName usługi Azure AD na MOERA.

Obiekt użytkownika dzierżawy usługi Azure AD:
- MailNickName: us1           
- UserPrincipalName us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>Scenariusz 2: nie zweryfikowano sufiksu UPN — Ustaw lokalny atrybut mailNickName

![Scenario2](./media/plan-connect-userprincipalname/example2.png)

Lokalny obiekt użytkownika:
- mailNickName: US4
- proxyAddresses: { SMTP:us1@contoso.com }
- poczt us2@contoso.com
- userPrincipalName us3@contoso.com

Synchronizowanie aktualizacji lokalnego atrybutu mailNickName z dzierżawą usługi Azure AD
- Zaktualizuj atrybut MailNickName usługi Azure AD za pomocą lokalnego atrybutu mailNickName.
- Ponieważ nie ma aktualizacji lokalnego atrybutu userPrincipalName, nie ma zmian w atrybucie UserPrincipalName usługi Azure AD.

Obiekt użytkownika dzierżawy usługi Azure AD:
- MailNickName: US4
- UserPrincipalName us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>Scenariusz 3: nie zweryfikowano sufiksu UPN — aktualizacja lokalnego atrybutu userPrincipalName

![Scenario3](./media/plan-connect-userprincipalname/example3.png)

Lokalny obiekt użytkownika:
- mailNickName: US4
- proxyAddresses: { SMTP:us1@contoso.com }
- poczt us2@contoso.com
- userPrincipalName us5@contoso.com

Synchronizowanie aktualizacji lokalnego atrybutu userPrincipalName z dzierżawą usługi Azure AD
- Aktualizacja lokalnego atrybutu userPrincipalName wyzwala ponowne obliczenie MOERA i atrybutu UserPrincipalName usługi Azure AD.
- Ustaw MOERA na &lt; MailNickName &gt;&#64;&lt; początkowej domeny &gt; .
- Ustaw atrybut UserPrincipalName usługi Azure AD na MOERA.

Obiekt użytkownika dzierżawy usługi Azure AD:
- MailNickName: US4
- UserPrincipalName us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>Scenariusz 4: niezweryfikowany sufiks nazwy UPN — aktualizowanie podstawowego adresu SMTP i lokalnego atrybutu poczty

![Scenario4](./media/plan-connect-userprincipalname/example4.png)

Lokalny obiekt użytkownika:
- mailNickName: US4
- proxyAddresses: { SMTP:us6@contoso.com }
- poczt us7@contoso.com
- userPrincipalName us5@contoso.com

Synchronizowanie aktualizacji lokalnego atrybutu poczty i podstawowego adresu SMTP z dzierżawą usługi Azure AD
- Po początkowej synchronizacji obiektu użytkownika aktualizacje lokalnego atrybutu poczty i podstawowego adresu SMTP nie będą miały wpływu na MailNickName usługi Azure AD lub atrybut UserPrincipalName.

Obiekt użytkownika dzierżawy usługi Azure AD:
- MailNickName: US4
- UserPrincipalName us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>Scenariusz 5: zweryfikowany sufiks nazwy UPN — aktualizacja lokalnego sufiksu atrybutu userPrincipalName

![Scenario5](./media/plan-connect-userprincipalname/example5.png)

Lokalny obiekt użytkownika:
- mailNickName: US4
- proxyAddresses: { SMTP:us6@contoso.com }
- poczt us7@contoso.com
- userPrincipalName us5@verified.contoso.com

Synchronizowanie aktualizacji lokalnego atrybutu userPrincipalName z dzierżawą usługi Azure AD
- Aktualizacja lokalnego atrybutu userPrincipalName wyzwala ponowne obliczenie atrybutu UserPrincipalName usługi Azure AD.
- Ustaw atrybut UserPrincipalName usługi Azure AD na lokalny Atrybut userPrincipalName jako sufiks nazwy UPN jest weryfikowany w dzierżawie usługi Azure AD.

Obiekt użytkownika dzierżawy usługi Azure AD:
- MailNickName: US4     
- UserPrincipalName us5@verified.contoso.com

## <a name="next-steps"></a>Następne kroki
- [Integrowanie katalogów lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
- [Niestandardowa instalacja programu Azure AD Connect](how-to-connect-install-custom.md)