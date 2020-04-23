---
title: Atrybuty zsynchronizowane przez usługę Azure AD Connect | Dokumenty firmy Microsoft
description: Wyświetla listę atrybutów, które są synchronizowane z usługą Azure Active Directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c2bb36e0-5205-454c-b9b6-f4990bcedf51
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab51030ad39e1360cabc7d63390af7c1654d2891
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082122"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Synchronizacja programu Azure AD Connect: atrybuty synchronizowane z usługą Azure Active Directory
W tym temacie wymieniono atrybuty, które są synchronizowane przez synchronizację usługi Azure AD Connect.  
Atrybuty są pogrupowane według powiązanej aplikacji usługi Azure AD.

## <a name="attributes-to-synchronize"></a>Atrybuty do synchronizacji
Częstym pytaniem *jest to, co jest lista minimalnych atrybutów do synchronizacji*. Domyślnym i zalecanym podejściem jest zachowanie atrybutów domyślnych, dzięki czemu można utworzyć pełną gal (globalną listę adresów) w chmurze i uzyskać wszystkie funkcje w obciążeniach usługi Office 365. W niektórych przypadkach istnieją pewne atrybuty, których organizacja nie chce synchronizować z chmurą, ponieważ te atrybuty zawierają poufne lub umożliwiające identyfikację użytkownika (dane umożliwiające identyfikację użytkownika), takie jak w tym przykładzie:  
![złe atrybuty](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

W takim przypadku należy rozpocząć od listy atrybutów w tym temacie i zidentyfikować te atrybuty, które zawierają poufne lub dane umożliwiające identyfikację użytkownika i nie mogą być synchronizowane. Następnie usuń zaznaczenie tych atrybutów podczas instalacji przy użyciu [aplikacji usługi Azure AD i filtrowania atrybutów](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> Podczas usuwania zaznaczenia atrybutów, należy zachować ostrożność i tylko odznaczuj te atrybuty absolutnie nie można synchronizować. Odznaczanie innych atrybutów może mieć negatywny wpływ na funkcje.
>
>

## <a name="office-365-proplus"></a>Office 365 ProPlus
| Nazwa atrybutu | Użytkownik | Komentarz |
| --- |:---:| --- |
| kontoWłasny |X |Określa, czy konto jest włączone. |
| Cn |X | |
| displayName |X | |
| objectSid |X |właściwości mechanicznej. Identyfikator użytkownika usługi AD używany do obsługi synchronizacji między usługą Azure AD i AD. |
| pwdLastSet (zestaw pwdLastSet) |X |właściwości mechanicznej. Używane wiedzieć, kiedy unieważnić już wydane tokeny. Używane zarówno przez synchronizację skrótów haseł, uwierzytelnianie przekazywane, jak i federację. |
|samAccountName|X| |
| źródłoAnchor |X |właściwości mechanicznej. Niezmienny identyfikator do utrzymania relacji między ADDS i usługi Azure AD. |
| usageLocation (korzystanieLokalizacja) |X |właściwości mechanicznej. Kraj/region użytkownika. Używane do przypisywania licencji. |
| userPrincipalName |X |Nazwa UPN to identyfikator logowania użytkownika. Najczęściej taka sama jak wartość [mail]. |

## <a name="exchange-online"></a>Exchange Online
| Nazwa atrybutu | Użytkownik | Kontakt | Grupa | Komentarz |
| --- |:---:|:---:|:---:| --- |
| kontoWłasny |X | | |Określa, czy konto jest włączone. |
| Assistent |X |X | | |
| altRecipient |X | | |Wymaga kompilacji usługi Azure AD Connect 1.1.552.0 lub później. |
| authOrig (authOrig) |X |X |X | |
| c |X |X | | |
| Cn |X | |X | |
| Co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| department |X |X | | |
| description | | |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| numer facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| homePhone |X |X | | |
| informacje o |X |X |X |Ten atrybut nie jest obecnie zużywany dla grup. |
| Inicjały |X |X | | |
| l |X |X | | |
| Legacyexchangedn |X |X |X | |
| mailNickname (nazwa pocztowa) |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| członek | | |X | |
| telefon komórkowy |X |X | | |
| msDS-HABSeniorityIndex |X |X |X | |
| nazwa msDS-FonetykaWyświetla |X |X |X | |
| msExchArchiveGUID |X | | | |
| nazwa msExchArchiveName |X | | | |
| msExchAssistantName |X |X | | |
| msExchAuditAdmin |X | | | |
| msExchAuditDelegate |X | | | |
| msExchAuditDelegateAdmin |X | | | |
| MsExchAuditOwner |X | | | |
| msExchBlockedSendersHash |X |X | | |
| msExchBypassAudit |X | | | |
| msExchBypassModerationLink | | |X |Dostępne w usłudze Azure AD Connect w wersji 1.1.524.0 |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxPlags |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Ten atrybut nie jest obecnie zużywany przez program Exchange Online. |
| msExchExtensionCustomAttribute2 |X |X |X |Ten atrybut nie jest obecnie zużywany przez program Exchange Online. |
| msExchExtensionCustomAttribute3 |X |X |X |Ten atrybut nie jest obecnie zużywany przez program Exchange Online. |
| msExchExtensionCustomAttribute4 |X |X |X |Ten atrybut nie jest obecnie zużywany przez program Exchange Online. |
| msExchExtensionCustomAttribute5 |X |X |X |Ten atrybut nie jest obecnie zużywany przez program Exchange Online. |
| msExchHideFromAddressLists |X |X |X | |
| msExchImmutableID |X | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldOwner |X |X |X | |
| msExchMailboxAuditWzwowalalny |X | | | |
| msExchMailboxAuditLogAgeLimit |X | | | |
| MsExchMailboxGuid (2005) |X | | | |
| MsExchModeratedByLink |X |X |X | |
| msExchModerationSlags |X |X |X | |
| MsExchRecipientDisplayType |X |X |X | |
| msExchRecipientTypeDetails |X |X |X | |
| msExchRemoteRecipientType |X | | | |
| msExchRequireAuthToSendTo |X |X |X | |
| msExchResourceCapacity |X | | | |
| msExchResourceDisplay |X | | | |
| msExchResourceMetaData |X | | | |
| właściwości msExchResourceSearchProperties |X | | | |
| msExchRetentionKoment |X |X |X | |
| msExchRetentionURL |X |X |X | |
| msExchSafeRecipientsHash |X |X | | |
| msExchSafeSendersHash |X |X | | |
| msExchSenderHintTranslations |X |X |X | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamSabory pocztowe |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| msExchUserHoldPolicies |X | | | |
| msOrg-IsOrganizational | | |X | |
| objectSid |X | |X |właściwości mechanicznej. Identyfikator użytkownika usługi AD używany do obsługi synchronizacji między usługą Azure AD i AD. |
| oOFReplyToOriginator | | |X | |
| inneFacsimileTelephone |X |X | | |
| inneHomePhone |X |X | | |
| inneTelefon |X |X | | |
| Pagera |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| Postalcode |X |X | | |
| proxyAddresses |X |X |X | |
| publicDelegates |X |X |X | |
| pwdLastSet (zestaw pwdLastSet) |X | | |właściwości mechanicznej. Używane wiedzieć, kiedy unieważnić już wydane tokeny. Używane zarówno przez synchronizację haseł, jak i federację. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| źródłoAnchor |X |X |X |właściwości mechanicznej. Niezmienny identyfikator do utrzymania relacji między ADDS i usługi Azure AD. |
| st |X |X | | |
| Streetaddress |X |X | | |
| Targetaddress |X |X | | |
| telefonAsysant |X |X | | |
| Telephonenumber |X |X | | |
| Thumbnailphoto |X |X | | |
| title |X |X | | |
| unauthOrig |X |X |X | |
| usageLocation (korzystanieLokalizacja) |X | | |właściwości mechanicznej. Kraj/region użytkownika. Używane do przypisywania licencji. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |Nazwa UPN to identyfikator logowania użytkownika. Najczęściej taka sama jak wartość [mail]. |
| userSMIMECertificates |X |X | | |
| strona wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| Nazwa atrybutu | Użytkownik | Kontakt | Grupa | Komentarz |
| --- |:---:|:---:|:---:| --- |
| kontoWłasny |X | | |Określa, czy konto jest włączone. |
| authOrig (authOrig) |X |X |X | |
| c |X |X | | |
| Cn |X | |X | |
| Co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| department |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| numer facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| ukryj czmięstowanie | | |X | |
| telefon domowy |X |X | | |
| informacje o |X |X |X | |
| Inicjały |X |X | | |
| telefon ipPhone |X |X | | |
| l |X |X | | |
| mail (poczta) |X |X |X | |
| nazwa pocztowa |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| członek | | |X | |
| Middlename |X |X | | |
| telefon komórkowy |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamSabory pocztowe |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| objectSid |X | |X |właściwości mechanicznej. Identyfikator użytkownika usługi AD używany do obsługi synchronizacji między usługą Azure AD i AD. |
| oOFReplyToOriginator | | |X | |
| inneFacsimileTelephone |X |X | | |
| inneHomePhone |X |X | | |
| inneIpPhone |X |X | | |
| inneMobile |X |X | | |
| innePager |X |X | | |
| inneTelefon |X |X | | |
| Pagera |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| Postalcode |X |X | | |
| pocztaOfficeBox |X |X | |Ten atrybut nie jest obecnie zużywany przez sharepoint online. |
| preferowany Język |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet (zestaw pwdLastSet) |X | | |właściwości mechanicznej. Używane wiedzieć, kiedy unieważnić już wydane tokeny. Używane zarówno przez synchronizację skrótów haseł, uwierzytelnianie przekazywane, jak i federację. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| źródłoAnchor |X |X |X |właściwości mechanicznej. Niezmienny identyfikator do utrzymania relacji między ADDS i usługi Azure AD. |
| st |X |X | | |
| Streetaddress |X |X | | |
| Targetaddress |X |X | | |
| telefonAsysant |X |X | | |
| Telephonenumber |X |X | | |
| Thumbnailphoto |X |X | | |
| title |X |X | | |
| unauthOrig |X |X |X | |
| url |X |X | | |
| usageLocation (korzystanieLokalizacja) |X | | |właściwości mechanicznej. Kraj/region użytkownika
. Używane do przypisywania licencji. |
| userPrincipalName |X | | |Nazwa UPN to identyfikator logowania użytkownika. Najczęściej taka sama jak wartość [mail]. |
| strona wWWHomePage |X |X | | |

## <a name="teams-and-skype-for-business-online"></a>Zespoły i skype dla firm Online
| Nazwa atrybutu | Użytkownik | Kontakt | Grupa | Komentarz |
| --- |:---:|:---:|:---:| --- |
| kontoWłasny |X | | |Określa, czy konto jest włączone. |
| c |X |X | | |
| Cn |X | |X | |
| Co |X |X | | |
| company |X |X | | |
| department |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| numer facsimiletelephonenumber |X |X |X | |
| givenName |X |X | | |
| telefon domowy |X |X | | |
| telefon ipPhone |X |X | | |
| l |X |X | | |
| mail (poczta) |X |X |X | |
| mailNickname (nazwa pocztowa) |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| członek | | |X | |
| telefon komórkowy |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-ApplicationOptions |X | | | |
| lokalizator wdrażania msRTCSIP |X |X | | |
| msRTCSIP-Line |X |X | | |
| msRTCSIP-OptionPłagi |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress |X |X | | |
| msRTCSIP-UserEnabled |X |X | | |
| objectSid |X | |X |właściwości mechanicznej. Identyfikator użytkownika usługi AD używany do obsługi synchronizacji między usługą Azure AD i AD. |
| inneTelefon |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| Postalcode |X |X | | |
| preferowany Język |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet (zestaw pwdLastSet) |X | | |właściwości mechanicznej. Używane wiedzieć, kiedy unieważnić już wydane tokeny. Używane zarówno przez synchronizację skrótów haseł, uwierzytelnianie przekazywane, jak i federację. |
| sn |X |X | | |
| źródłoAnchor |X |X |X |właściwości mechanicznej. Niezmienny identyfikator do utrzymania relacji między ADDS i usługi Azure AD. |
| st |X |X | | |
| Streetaddress |X |X | | |
| Telephonenumber |X |X | | |
| Thumbnailphoto |X |X | | |
| title |X |X | | |
| usageLocation (korzystanieLokalizacja) |X | | |właściwości mechanicznej. Kraj/region użytkownika. Używane do przypisywania licencji. |
| userPrincipalName |X | | |Nazwa UPN to identyfikator logowania użytkownika. Najczęściej taka sama jak wartość [mail]. |
| strona wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Nazwa atrybutu | Użytkownik | Kontakt | Grupa | Komentarz |
| --- |:---:|:---:|:---:| --- |
| kontoWłasny |X | | |Określa, czy konto jest włączone. |
| Cn |X | |X |Nazwa pospolita lub alias. Najczęściej prefiks wartości [mail]. |
| displayName |X |X |X |Ciąg reprezentujący nazwę często wyświetlaną jako przyjazna nazwa (imię nazwisko). |
| mail (poczta) |X |X |X |pełny adres e-mail. |
| członek | | |X | |
| objectSid |X | |X |właściwości mechanicznej. Identyfikator użytkownika usługi AD używany do obsługi synchronizacji między usługą Azure AD i AD. |
| proxyAddresses |X |X |X |właściwości mechanicznej. Używane przez usługę Azure AD. Zawiera wszystkie pomocnicze adresy e-mail użytkownika. |
| pwdLastSet (zestaw pwdLastSet) |X | | |właściwości mechanicznej. Używane wiedzieć, kiedy unieważnić już wydane tokeny. |
| źródłoAnchor |X |X |X |właściwości mechanicznej. Niezmienny identyfikator do utrzymania relacji między ADDS i usługi Azure AD. |
| usageLocation (korzystanieLokalizacja) |X | | |właściwości mechanicznej. Kraj/region użytkownika. Używane do przypisywania licencji. |
| userPrincipalName |X | | |Ta nazwa UPN jest identyfikatorem logowania użytkownika. Najczęściej taka sama jak wartość [mail]. |

## <a name="intune"></a>Usługa Intune
| Nazwa atrybutu | Użytkownik | Kontakt | Grupa | Komentarz |
| --- |:---:|:---:|:---:| --- |
| kontoWłasny |X | | |Określa, czy konto jest włączone. |
| c |X |X | | |
| Cn |X | |X | |
| description |X |X |X | |
| displayName |X |X |X | |
| mail (poczta) |X |X |X | |
| nazwa pocztowa |X |X |X | |
| członek | | |X | |
| objectSid |X | |X |właściwości mechanicznej. Identyfikator użytkownika usługi AD używany do obsługi synchronizacji między usługą Azure AD i AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet (zestaw pwdLastSet) |X | | |właściwości mechanicznej. Używane wiedzieć, kiedy unieważnić już wydane tokeny. Używane zarówno przez synchronizację skrótów haseł, uwierzytelnianie przekazywane, jak i federację. |
| źródłoAnchor |X |X |X |właściwości mechanicznej. Niezmienny identyfikator do utrzymania relacji między ADDS i usługi Azure AD. |
| usageLocation (korzystanieLokalizacja) |X | | |właściwości mechanicznej. Kraj/region użytkownika. Używane do przypisywania licencji. |
| userPrincipalName |X | | |Nazwa UPN to identyfikator logowania użytkownika. Najczęściej taka sama jak wartość [mail]. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Nazwa atrybutu | Użytkownik | Kontakt | Grupa | Komentarz |
| --- |:---:|:---:|:---:| --- |
| kontoWłasny |X | | |Określa, czy konto jest włączone. |
| c |X |X | | |
| Cn |X | |X | |
| Co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| numer facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| l |X |X | | |
| managedBy | | |X | |
| manager |X |X | | |
| członek | | |X | |
| telefon komórkowy |X |X | | |
| objectSid |X | |X |właściwości mechanicznej. Identyfikator użytkownika usługi AD używany do obsługi synchronizacji między usługą Azure AD i AD. |
| physicalDeliveryOfficeName |X |X | | |
| Postalcode |X |X | | |
| preferowany Język |X | | | |
| pwdLastSet (zestaw pwdLastSet) |X | | |właściwości mechanicznej. Używane wiedzieć, kiedy unieważnić już wydane tokeny. Używane zarówno przez synchronizację skrótów haseł, uwierzytelnianie przekazywane, jak i federację. |
| sn |X |X | | |
| źródłoAnchor |X |X |X |właściwości mechanicznej. Niezmienny identyfikator do utrzymania relacji między ADDS i usługi Azure AD. |
| st |X |X | | |
| Streetaddress |X |X | | |
| Telephonenumber |X |X | | |
| title |X |X | | |
| usageLocation (korzystanieLokalizacja) |X | | |właściwości mechanicznej. Kraj/region użytkownika. Używane do przypisywania licencji. |
| userPrincipalName |X | | |Nazwa UPN to identyfikator logowania użytkownika. Najczęściej taka sama jak wartość [mail]. |

## <a name="3rd-party-applications"></a>Aplikacje innych firm
Ta grupa jest zestawem atrybutów używanych jako minimalne atrybuty potrzebne dla ogólnego obciążenia lub aplikacji. Może być używany dla obciążenia niewymienione w innej sekcji lub dla aplikacji firmy innej firmy. Jest on wyraźnie używany dla następujących czynności:

* Usługa Yammer (tylko użytkownik jest zużywany)
* [Hybrydowe scenariusze współpracy między organizacji typu Business-to-Business (B2B) oferowane przez zasoby takie jak SharePoint](https://go.microsoft.com/fwlink/?LinkId=747036)

Ta grupa jest zestawem atrybutów, których można użyć, jeśli katalog usługi Azure AD nie jest używany do obsługi usługi Office 365, Dynamics lub Intune. Ma mały zestaw atrybutów rdzenia. Należy zauważyć, że logowanie jednokrotne lub inicjowanie obsługi administracyjnej niektórych aplikacji innych firm wymaga skonfigurowania synchronizacji atrybutów oprócz atrybutów opisanych w tym miejscu. Wymagania dotyczące aplikacji są opisane w [samouczku aplikacji SaaS](../saas-apps/tutorial-list.md) dla każdej aplikacji.

| Nazwa atrybutu | Użytkownik | Kontakt | Grupa | Komentarz |
| --- |:---:|:---:|:---:| --- |
| kontoWłasny |X | | |Określa, czy konto jest włączone. |
| Cn |X | |X | |
| displayName |X |X |X | |
| employeeID |X |  |  | |
| givenName |X |X | | |
| mail (poczta) |X | |X | |
| managedBy | | |X | |
| mailNickName |X |X |X | |
| członek | | |X | |
| objectSid |X | | |właściwości mechanicznej. Identyfikator użytkownika usługi AD używany do obsługi synchronizacji między usługą Azure AD i AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet (zestaw pwdLastSet) |X | | |właściwości mechanicznej. Używane wiedzieć, kiedy unieważnić już wydane tokeny. Używane zarówno przez synchronizację skrótów haseł, uwierzytelnianie przekazywane, jak i federację. |
| sn |X |X | | |
| źródłoAnchor |X |X |X |właściwości mechanicznej. Niezmienny identyfikator do utrzymania relacji między ADDS i usługi Azure AD. |
| usageLocation (korzystanieLokalizacja) |X | | |właściwości mechanicznej. Kraj/region użytkownika. Używane do przypisywania licencji. |
| userPrincipalName |X | | |Nazwa UPN to identyfikator logowania użytkownika. Najczęściej taka sama jak wartość [mail]. |

## <a name="windows-10"></a>Windows 10
Komputer (urządzenie) przyłączony do domeny systemu Windows 10 synchronizuje niektóre atrybuty z usługą Azure AD. Aby uzyskać więcej informacji na temat scenariuszy, zobacz [Łączenie urządzeń przyłączonych do domeny z usługą Azure AD dla środowiska systemu Windows 10](../active-directory-azureadjoin-devices-group-policy.md). Te atrybuty zawsze są synchronizowane, a system Windows 10 nie jest wyświetlany jako aplikacja, którą można usunąć. Komputer przyłączony do domeny systemu Windows 10 jest identyfikowany przez zapełnienie atrybutu userCertificate.

| Nazwa atrybutu | Urządzenie | Komentarz |
| --- |:---:| --- |
| kontoWłasny |X | |
| Typ zaufania urządzenia |X |Wartość zakodowana na stałe dla komputerów przyłączonych do domeny. |
| displayName |X | |
| ms-DS-CreatorSID |X |Nazywany również registeredOwnerReference. |
| Objectguid |X |Nazywany również deviceID. |
| objectSid |X |Wywołano również onPremisesSecurityIdentifier. |
| operatingSystem |X |Nazywany również deviceOSType. |
| operatingSystemVersion |X |Nazywany również deviceOSVersion. |
| userCertificate |X | |

Te atrybuty dla **użytkownika** są dodatkiem do innych wybranych aplikacji.  

| Nazwa atrybutu | Użytkownik | Komentarz |
| --- |:---:| --- |
| domainFQDN |X |Nazywany również dnsDomainName. Na przykład contoso.com. |
| domainNetBios (DomenaNetBios) |X |Nazywane również netBiosName. Na przykład CONTOSO. |
| msDS-KeyCredentialLink |X |Po zarejestrowaniu użytkownika w usłudze Windows Hello dla firm. | 

## <a name="exchange-hybrid-writeback"></a>Hybrydowy odpis programu Exchange
Te atrybuty są zapisywane z usługi Azure AD do lokalnej usługi Active Directory po wybraniu włączenia **hybrydowego programu Exchange**. W zależności od wersji programu Exchange może być synchronizowanych mniej atrybutów.

| Nazwa atrybutu (lokalna ad) | Nazwa atrybutu (interfejs connect ui) | Użytkownik | Kontakt | Grupa | Komentarz |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| ms-DS-External-Directory-Object-Id |X | | |Pochodzi z cloudAnchor w usłudze Azure AD. Ten atrybut jest nowy w programach Exchange 2016 i Windows Server 2016 AD. |
| msExchArchiveStatus| ms-Exch-ArchiveStatus |X | | |Archiwum online: Umożliwia klientom archiwizowanie poczty. |
| msExchBlockedSendersHash| ms-Exch-BlockedSendersHash |X | | |Filtrowanie: odpisuje lokalne filtrowanie oraz bezpieczne i zablokowane dane nadawców w trybie online od klientów. |
| msExchSafeRecipientsHash| ms-Exch-SafeRecipientsHash  |X | | |Filtrowanie: odpisuje lokalne filtrowanie oraz bezpieczne i zablokowane dane nadawców w trybie online od klientów. |
| msExchSafeSendersHash| ms-Exch-SafeSendersHash  |X | | |Filtrowanie: odpisuje lokalne filtrowanie oraz bezpieczne i zablokowane dane nadawców w trybie online od klientów. |
| msExchUCVoiceMailSettings| ms-Exch-UCVoiceMailSettings |X | | |Włącz ujednoliconych wiadomości (UM) — poczta głosowa online: używana przez integrację programu Microsoft Lync Server do wskazywania lokalnie programu Lync Server, że użytkownik ma pocztę głosową w usługach online. |
| msExchUserHoldPolicies| ms-Exch-UserHoldPolicies |X | | |Wstrzymanie sporów: umożliwia usługom w chmurze określenie, którzy użytkownicy są w ramach wstrzymania postępowania sądowego. |
| proxyAddresses| proxyAddresses |X |X |X |Wstawiany jest tylko adres x500 z usługi Exchange Online. |
| publicDelegates| ms-Exch-Public-Delegatów  |X | | |Umożliwia skrzynki pocztowej usługi Exchange Online, aby przyznać SendOnBehalfTo praw do użytkowników z lokalnej skrzynki pocztowej programu Exchange. Wymaga kompilacji usługi Azure AD Connect 1.1.552.0 lub później. |

## <a name="exchange-mail-public-folder"></a>Folder publiczny programu Exchange Mail
Te atrybuty są synchronizowane z lokalnej usługi Active Directory do usługi Azure AD po wybraniu włączenia **folderu publicznego poczty programu Exchange**.

| Nazwa atrybutu | PublicFolder (waśniarka publiczna) | Komentarz |
| --- | :---:| --- |
| displayName | X |  |
| mail (poczta) | X |  |
| msExchRecipientTypeDetails | X |  |
| Objectguid | X |  |
| proxyAddresses | X |  |
| Targetaddress | X |  |

## <a name="device-writeback"></a>Zapisywanie zwrotne urządzeń
Obiekty urządzenia są tworzone w usłudze Active Directory. Obiekty te mogą być urządzeniami przyłączone do usługi Azure AD lub przyłączonych do domeny komputerów z systemem Windows 10.

| Nazwa atrybutu | Urządzenie | Komentarz |
| --- |:---:| --- |
| altSecurityIdentities (Niepewność) |X | |
| displayName |X | |
| dn |X | |
| msDS-CloudAnchor |X | |
| msDS-DeviceID |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DeviceFizyczne identyfikatory |X | |
| msDS-KeyCredentialLink |X |Tylko ze schematem usługi AD systemu Windows Server 2016 |
| msDS-IsCompliant |X | |
| msDS-IsEnabled |X | |
| msDS-IsManaged |X | |
| MsDS-RegisteredOwner |X | |

## <a name="notes"></a>Uwagi
* Podczas korzystania z alternatywnego identyfikatora, lokalnego atrybutu userPrincipalName jest synchronizowany z atrybutem usługi Azure AD onPremisesUserPrincipalName. Atrybut identyfikatora alternatywnego, na przykład poczta, jest synchronizowany z atrybutem Azure AD userPrincipalName.
* Na powyższych listach typ obiektu **Użytkownik** stosuje się również do typu obiektu **iNetOrgPerson**.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o konfiguracji [synchronizacji usługi Azure AD Connect.](how-to-connect-sync-whatis.md)

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
