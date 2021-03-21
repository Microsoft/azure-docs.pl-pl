---
title: Atrybuty synchronizowane przez Azure AD Connect | Microsoft Docs
description: Wyświetla listę atrybutów, które są synchronizowane z Azure Active Directory.
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
ms.openlocfilehash: 6ec05c4160c6502904644bf7035bda0bed66cc33
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94413194"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Synchronizacja programu Azure AD Connect: atrybuty synchronizowane z usługą Azure Active Directory
W tym temacie wymieniono atrybuty, które są synchronizowane przez Azure AD Connect Sync.  
Atrybuty są pogrupowane według powiązanej aplikacji usługi Azure AD.

## <a name="attributes-to-synchronize"></a>Atrybuty do synchronizowania
Typowym pytaniem jest *Lista minimalnych atrybutów do synchronizowania*. Domyślnym i Zalecanym podejściem jest zachowanie atrybutów domyślnych, dzięki czemu w chmurze można utworzyć pełną listę metod (globalna lista adresów) i uzyskać wszystkie funkcje Microsoft 365 obciążeń. W niektórych przypadkach istnieją pewne atrybuty, których Twoja organizacja nie chce synchronizować z chmurą, ponieważ te atrybuty zawierają poufne dane osobowe, takie jak w poniższym przykładzie:  
![złe atrybuty](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

W takim przypadku należy zacząć od listy atrybutów w tym temacie i zidentyfikować te atrybuty, które będą zawierać dane osobowe i nie można ich synchronizować. Następnie należy usunąć zaznaczenie tych atrybutów podczas instalacji przy użyciu funkcji [filtrowania atrybutów i aplikacji usługi Azure AD](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> Podczas dezaznaczania atrybutów należy zachować ostrożność i tylko usunąć zaznaczenie tych atrybutów absolutnie niemożliwe do synchronizacji. Odwybór innych atrybutów może mieć negatywny wpływ na funkcje.
>
>

## <a name="microsoft-365-apps-for-enterprise"></a>Aplikacje platformy Microsoft 365 dla przedsiębiorstw
| Nazwa atrybutu | Użytkownik | Komentarz |
| --- |:---:| --- |
| accountEnabled |X |Określa, czy konto jest włączone. |
| CN |X | |
| displayName |X | |
| objectSid |X |Właściwość mechaniczna. Identyfikator użytkownika usługi AD używany do obsługi synchronizacji między usługą Azure AD i usługą AD. |
| pwdLastSet |X |Właściwość mechaniczna. Używane do sprawdzania, kiedy unieważnia już wystawione tokeny. Używany w przypadku synchronizacji skrótów haseł, uwierzytelniania przekazywanego i Federacji. |
|samAccountName|X| |
| sourceAnchor |X |Właściwość mechaniczna. Niezmienny identyfikator do obsługi relacji między DODAWANIEm i usługą Azure AD. |
| usageLocation |X |Właściwość mechaniczna. Kraj/region użytkownika. Używany do przypisywania licencji. |
| userPrincipalName |X |Nazwa UPN to identyfikator logowania użytkownika. Najczęściej taka sama jak wartość [mail]. |

## <a name="exchange-online"></a>Exchange Online
| Nazwa atrybutu | Użytkownik | Kontakt | Group (Grupa) | Komentarz |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Określa, czy konto jest włączone. |
| pomocnik |X |X | | |
| altRecipient |X | | |Wymaga Azure AD Connect build 1.1.552.0 lub After. |
| authOrig |X |X |X | |
| c |X |X | | |
| CN |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| działu, |X |X | | |
| description (opis) | | |X | |
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
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| homePhone |X |X | | |
| informacje o |X |X |X |Ten atrybut nie jest obecnie używany dla grup. |
| Inicjały |X |X | | |
| l |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| członek | | |X | |
| telefon komórkowy |X |X | | |
| msDS-HABSeniorityIndex |X |X |X | |
| msDS-PhoneticDisplayName |X |X |X | |
| msExchArchiveGUID |X | | | |
| msExchArchiveName |X | | | |
| msExchAssistantName |X |X | | |
| msExchAuditAdmin |X | | | |
| msExchAuditDelegate |X | | | |
| msExchAuditDelegateAdmin |X | | | |
| msExchAuditOwner |X | | | |
| msExchBlockedSendersHash |X |X | | |
| msExchBypassAudit |X | | | |
| msExchBypassModerationLink | | |X |Dostępne w wersji Azure AD Connect 1.1.524.0 |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxFlags |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Ten atrybut nie jest obecnie używany przez usługi Exchange Online. |
| msExchExtensionCustomAttribute2 |X |X |X |Ten atrybut nie jest obecnie używany przez usługi Exchange Online. |
| msExchExtensionCustomAttribute3 |X |X |X |Ten atrybut nie jest obecnie używany przez usługi Exchange Online. |
| msExchExtensionCustomAttribute4 |X |X |X |Ten atrybut nie jest obecnie używany przez usługi Exchange Online. |
| msExchExtensionCustomAttribute5 |X |X |X |Ten atrybut nie jest obecnie używany przez usługi Exchange Online. |
| msExchHideFromAddressLists |X |X |X | |
| msExchImmutableID |X | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldOwner |X |X |X | |
| msExchMailboxAuditEnable |X | | | |
| msExchMailboxAuditLogAgeLimit |X | | | |
| msExchMailboxGuid |X | | | |
| msExchModeratedByLink |X |X |X | |
| msExchModerationFlags |X |X |X | |
| msExchRecipientDisplayType |X |X |X | |
| msExchRecipientTypeDetails |X |X |X | |
| msExchRemoteRecipientType |X | | | |
| msExchRequireAuthToSendTo |X |X |X | |
| msExchResourceCapacity |X | | | |
| msExchResourceDisplay |X | | | |
| msExchResourceMetaData |X | | | |
| msExchResourceSearchProperties |X | | | |
| msExchRetentionComment |X |X |X | |
| msExchRetentionURL |X |X |X | |
| msExchSafeRecipientsHash |X |X | | |
| msExchSafeSendersHash |X |X | | |
| msExchSenderHintTranslations |X |X |X | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| msExchUserHoldPolicies |X | | | |
| msOrg — isorganizationer | | |X | |
| objectSid |X | |X |Właściwość mechaniczna. Identyfikator użytkownika usługi AD używany do obsługi synchronizacji między usługą Azure AD i usługą AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherTelephone |X |X | | |
| modułu stronicowania |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| proxyAddresses |X |X |X | |
| publicDelegates |X |X |X | |
| pwdLastSet |X | | |Właściwość mechaniczna. Używane do sprawdzania, kiedy unieważnia już wystawione tokeny. Używany zarówno w przypadku synchronizacji haseł, jak i Federacji. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| sourceAnchor |X |X |X |Właściwość mechaniczna. Niezmienny identyfikator do obsługi relacji między DODAWANIEm i usługą Azure AD. |
| st |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |synchronizowane tylko raz z usługi Azure AD do usługi Exchange Online, po której usługa Exchange Online będzie źródłem urzędu dla tego atrybutu, a wszelkie późniejsze zmiany nie będą synchronizowane z lokalnego. Aby uzyskać więcej informacji, zobacz ([KB](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange)).|
| tytuł |X |X | | |
| unauthOrig |X |X |X | |
| usageLocation |X | | |Właściwość mechaniczna. Kraj/region użytkownika. Używany do przypisywania licencji. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |Nazwa UPN to identyfikator logowania użytkownika. Najczęściej taka sama jak wartość [mail]. |
| userSMIMECertificates |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| Nazwa atrybutu | Użytkownik | Kontakt | Group (Grupa) | Komentarz |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Określa, czy konto jest włączone. |
| authOrig |X |X |X | |
| c |X |X | | |
| CN |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| działu, |X |X | | |
| description (opis) |X |X |X | |
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
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| hideDLMembership | | |X | |
| HomePhone |X |X | | |
| informacje o |X |X |X | |
| inicjały |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| mail (poczta) |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| członek | | |X | |
| middleName |X |X | | |
| telefon komórkowy |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| objectSid |X | |X |Właściwość mechaniczna. Identyfikator użytkownika usługi AD używany do obsługi synchronizacji między usługą Azure AD i usługą AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherIpPhone |X |X | | |
| otherMobile |X |X | | |
| otherPager |X |X | | |
| otherTelephone |X |X | | |
| modułu stronicowania |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| postOfficeBox |X |X | |Ten atrybut nie jest obecnie używany przez program SharePoint Online. |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |Właściwość mechaniczna. Używane do sprawdzania, kiedy unieważnia już wystawione tokeny. Używany w przypadku synchronizacji skrótów haseł, uwierzytelniania przekazywanego i Federacji. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| sourceAnchor |X |X |X |Właściwość mechaniczna. Niezmienny identyfikator do obsługi relacji między DODAWANIEm i usługą Azure AD. |
| st |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |synchronizowane tylko raz z usługi Azure AD do usługi Exchange Online, po której usługa Exchange Online będzie źródłem urzędu dla tego atrybutu, a wszelkie późniejsze zmiany nie będą synchronizowane z lokalnego. Aby uzyskać więcej informacji, zobacz ([KB](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange)).|
| tytuł |X |X | | |
| unauthOrig |X |X |X | |
| url |X |X | | |
| usageLocation |X | | |Właściwość mechaniczna. Kraj/region użytkownika
. Używany do przypisywania licencji. |
| userPrincipalName |X | | |Nazwa UPN to identyfikator logowania użytkownika. Najczęściej taka sama jak wartość [mail]. |
| wWWHomePage |X |X | | |

## <a name="teams-and-skype-for-business-online"></a>Zespoły i Skype dla firm Online
| Nazwa atrybutu | Użytkownik | Kontakt | Group (Grupa) | Komentarz |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Określa, czy konto jest włączone. |
| c |X |X | | |
| CN |X | |X | |
| co |X |X | | |
| company |X |X | | |
| działu, |X |X | | |
| description (opis) |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X |X | |
| givenName |X |X | | |
| HomePhone |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| mail (poczta) |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| członek | | |X | |
| telefon komórkowy |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP — ApplicationOptions |X | | | |
| msRTCSIP — DeploymentLocator |X |X | | |
| msRTCSIP-line |X |X | | |
| msRTCSIP — OptionFlags |X |X | | |
| msRTCSIP — OwnerUrn |X | | | |
| msRTCSIP — PrimaryUserAddress |X |X | | |
| msRTCSIP — UserEnabled |X |X | | |
| objectSid |X | |X |Właściwość mechaniczna. Identyfikator użytkownika usługi AD używany do obsługi synchronizacji między usługą Azure AD i usługą AD. |
| otherTelephone |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |Właściwość mechaniczna. Używane do sprawdzania, kiedy unieważnia już wystawione tokeny. Używany w przypadku synchronizacji skrótów haseł, uwierzytelniania przekazywanego i Federacji. |
| sn |X |X | | |
| sourceAnchor |X |X |X |Właściwość mechaniczna. Niezmienny identyfikator do obsługi relacji między DODAWANIEm i usługą Azure AD. |
| st |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |synchronizowane tylko raz z usługi Azure AD do usługi Exchange Online, po której usługa Exchange Online będzie źródłem urzędu dla tego atrybutu, a wszelkie późniejsze zmiany nie będą synchronizowane z lokalnego. Aby uzyskać więcej informacji, zobacz ([KB](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange)).|
| tytuł |X |X | | |
| usageLocation |X | | |Właściwość mechaniczna. Kraj/region użytkownika. Używany do przypisywania licencji. |
| userPrincipalName |X | | |Nazwa UPN to identyfikator logowania użytkownika. Najczęściej taka sama jak wartość [mail]. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Nazwa atrybutu | Użytkownik | Kontakt | Group (Grupa) | Komentarz |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Określa, czy konto jest włączone. |
| CN |X | |X |Nazwa pospolita lub alias. Najczęściej jest to prefiks wartości [mail]. |
| displayName |X |X |X |Ciąg reprezentujący nazwę często wyświetlany jako przyjazna nazwa (imię nazwisko). |
| mail (poczta) |X |X |X |pełny adres e-mail. |
| członek | | |X | |
| objectSid |X | |X |Właściwość mechaniczna. Identyfikator użytkownika usługi AD używany do obsługi synchronizacji między usługą Azure AD i usługą AD. |
| proxyAddresses |X |X |X |Właściwość mechaniczna. Używany przez usługę Azure AD. Zawiera wszystkie pomocnicze adresy e-mail użytkownika. |
| pwdLastSet |X | | |Właściwość mechaniczna. Używane do sprawdzania, kiedy unieważnia już wystawione tokeny. |
| sourceAnchor |X |X |X |Właściwość mechaniczna. Niezmienny identyfikator do obsługi relacji między DODAWANIEm i usługą Azure AD. |
| usageLocation |X | | |Właściwość mechaniczna. Kraj/region użytkownika. Używany do przypisywania licencji. |
| userPrincipalName |X | | |Ta nazwa UPN to identyfikator logowania użytkownika. Najczęściej taka sama jak wartość [mail]. |

## <a name="intune"></a>Intune
| Nazwa atrybutu | Użytkownik | Kontakt | Group (Grupa) | Komentarz |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Określa, czy konto jest włączone. |
| c |X |X | | |
| CN |X | |X | |
| description (opis) |X |X |X | |
| displayName |X |X |X | |
| mail (poczta) |X |X |X | |
| mailNickname |X |X |X | |
| członek | | |X | |
| objectSid |X | |X |Właściwość mechaniczna. Identyfikator użytkownika usługi AD używany do obsługi synchronizacji między usługą Azure AD i usługą AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |Właściwość mechaniczna. Używane do sprawdzania, kiedy unieważnia już wystawione tokeny. Używany w przypadku synchronizacji skrótów haseł, uwierzytelniania przekazywanego i Federacji. |
| sourceAnchor |X |X |X |Właściwość mechaniczna. Niezmienny identyfikator do obsługi relacji między DODAWANIEm i usługą Azure AD. |
| usageLocation |X | | |Właściwość mechaniczna. Kraj/region użytkownika. Używany do przypisywania licencji. |
| userPrincipalName |X | | |Nazwa UPN to identyfikator logowania użytkownika. Najczęściej taka sama jak wartość [mail]. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Nazwa atrybutu | Użytkownik | Kontakt | Group (Grupa) | Komentarz |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Określa, czy konto jest włączone. |
| c |X |X | | |
| CN |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| description (opis) |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| l |X |X | | |
| managedBy | | |X | |
| manager |X |X | | |
| członek | | |X | |
| telefon komórkowy |X |X | | |
| objectSid |X | |X |Właściwość mechaniczna. Identyfikator użytkownika usługi AD używany do obsługi synchronizacji między usługą Azure AD i usługą AD. |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| pwdLastSet |X | | |Właściwość mechaniczna. Używane do sprawdzania, kiedy unieważnia już wystawione tokeny. Używany w przypadku synchronizacji skrótów haseł, uwierzytelniania przekazywanego i Federacji. |
| sn |X |X | | |
| sourceAnchor |X |X |X |Właściwość mechaniczna. Niezmienny identyfikator do obsługi relacji między DODAWANIEm i usługą Azure AD. |
| st |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| tytuł |X |X | | |
| usageLocation |X | | |Właściwość mechaniczna. Kraj/region użytkownika. Używany do przypisywania licencji. |
| userPrincipalName |X | | |Nazwa UPN to identyfikator logowania użytkownika. Najczęściej taka sama jak wartość [mail]. |

## <a name="3rd-party-applications"></a>aplikacje innych firm
Ta grupa jest zestawem atrybutów używanych jako minimalne atrybuty, które są zbędne dla ogólnego obciążenia lub aplikacji. Może służyć do obciążenia niewymienionego w innej sekcji lub dla aplikacji innej niż Microsoft. Jest on jawnie używany dla następujących:

* Yammer (tylko użytkownik jest używany)
* [Hybrydowe scenariusze współpracy między firmami (B2B) między organizacjami oferowane przez zasoby, takie jak SharePoint](/sharepoint/create-b2b-extranet)

Ta grupa jest zestawem atrybutów, których można użyć, jeśli katalog usługi Azure AD nie jest używany do obsługi Microsoft 365, Dynamics lub Intune. Ma niewielki zestaw atrybutów podstawowych. Należy pamiętać, że logowanie jednokrotne lub inicjowanie obsługi administracyjnej w niektórych aplikacjach innych firm wymaga skonfigurowania synchronizacji atrybutów oprócz atrybutów opisanych tutaj. Wymagania dotyczące aplikacji są opisane w [samouczku aplikacji SaaS](../saas-apps/tutorial-list.md) dla każdej aplikacji.

| Nazwa atrybutu | Użytkownik | Kontakt | Group (Grupa) | Komentarz |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Określa, czy konto jest włączone. |
| CN |X | |X | |
| displayName |X |X |X | |
| employeeID |X |  |  | |
| givenName |X |X | | |
| mail (poczta) |X | |X | |
| managedBy | | |X | |
| mailNickName |X |X |X | |
| członek | | |X | |
| objectSid |X | | |Właściwość mechaniczna. Identyfikator użytkownika usługi AD używany do obsługi synchronizacji między usługą Azure AD i usługą AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |Właściwość mechaniczna. Używane do sprawdzania, kiedy unieważnia już wystawione tokeny. Używany w przypadku synchronizacji skrótów haseł, uwierzytelniania przekazywanego i Federacji. |
| sn |X |X | | |
| sourceAnchor |X |X |X |Właściwość mechaniczna. Niezmienny identyfikator do obsługi relacji między DODAWANIEm i usługą Azure AD. |
| usageLocation |X | | |Właściwość mechaniczna. Kraj/region użytkownika. Używany do przypisywania licencji. |
| userPrincipalName |X | | |Nazwa UPN to identyfikator logowania użytkownika. Najczęściej taka sama jak wartość [mail]. |

## <a name="windows-10"></a>Windows 10
Komputer przyłączony do domeny systemu Windows 10 synchronizuje niektóre atrybuty z usługą Azure AD. Aby uzyskać więcej informacji na temat scenariuszy, zobacz [Łączenie urządzeń przyłączonych do domeny z usługą Azure AD dla systemu Windows 10](../devices/hybrid-azuread-join-plan.md). Te atrybuty zawsze są synchronizowane, a system Windows 10 nie jest wyświetlany jako aplikacja, którą można usunąć. Komputer przyłączony do domeny systemu Windows 10 jest identyfikowany przez wypełnienie atrybutu userCertificate.

| Nazwa atrybutu | Urządzenie | Komentarz |
| --- |:---:| --- |
| accountEnabled |X | |
| deviceTrustType |X |Wartość stałe dla komputerów przyłączonych do domeny. |
| displayName |X | |
| MS-DS-CreatorSID |X |Nazywana również registeredOwnerReference. |
| objectGUID |X |Nazywana również identyfikatorem deviceID. |
| objectSid |X |Nazywana również onPremisesSecurityIdentifier. |
| operatingSystem |X |Nazywana również deviceOSType. |
| operatingSystemVersion |X |Nazywana również deviceOSVersion. |
| userCertificate |X | |

Te atrybuty dla **użytkownika** są uzupełnieniem innych wybranych aplikacji.  

| Nazwa atrybutu | Użytkownik | Komentarz |
| --- |:---:| --- |
| domainFQDN |X |Nazywana również NazwaDomenyDNS. Na przykład contoso.com. |
| domainNetBios |X |Nazywana również systemem NetBiosName. Na przykład CONTOSO. |
| msDS-KeyCredentialLink |X |Po zarejestrowaniu użytkownika w usłudze Windows Hello dla firm. | 

## <a name="exchange-hybrid-writeback"></a>Stornowanie hybrydowe programu Exchange
Te atrybuty są zapisywane z powrotem z usługi Azure AD do Active Directory lokalnych, gdy wybierzesz opcję włączenia **hybrydowego programu Exchange**. W zależności od wersji programu Exchange można synchronizować mniejszą liczbę atrybutów.

| Nazwa atrybutu (lokalna usługi AD) | Nazwa atrybutu (interfejs użytkownika połączenia) | Użytkownik | Kontakt | Group (Grupa) | Komentarz |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| MS-DS-External-Directory-Object-ID |X | | |Pochodny od cloudAnchor w usłudze Azure AD. Ten atrybut jest nowy w programie Exchange 2016 i Windows Server 2016 AD. |
| msExchArchiveStatus| MS-ArchiveStatus — wymiany |X | | |Archiwum online: umożliwia klientom archiwizowanie poczty e-mail. |
| msExchBlockedSendersHash| MS-BlockedSendersHash — wymiany |X | | |Filtrowanie: zapisuje z powrotem lokalne filtrowanie oraz bezpieczne i zablokowane dane nadawcy w trybie online. |
| msExchSafeRecipientsHash| MS-SafeRecipientsHash — wymiany  |X | | |Filtrowanie: zapisuje z powrotem lokalne filtrowanie oraz bezpieczne i zablokowane dane nadawcy w trybie online. |
| msExchSafeSendersHash| MS-SafeSendersHash — wymiany  |X | | |Filtrowanie: zapisuje z powrotem lokalne filtrowanie oraz bezpieczne i zablokowane dane nadawcy w trybie online. |
| msExchUCVoiceMailSettings| MS-UCVoiceMailSettings — wymiany |X | | |Włączanie ujednoliconych komunikatów (UM) — poczta głosowa w trybie online: używana przez integrację programu Microsoft Lync Server do wskazania, że użytkownik ma pocztę lokalną w Usługi online. |
| msExchUserHoldPolicies| MS-UserHoldPolicies — wymiany |X | | |Wstrzymanie sądowe: umożliwia usługom w chmurze ustalenie, którzy użytkownicy podlegają postępowaniu sądowemu. |
| proxyAddresses| proxyAddresses |X |X |X |Wstawiany jest tylko adres x500 z usługi Exchange Online. |
| publicDelegates| MS-wymiany-Public-Delegats  |X | | |Zezwala na dostęp do skrzynki pocztowej usługi Exchange Online użytkownikom z lokalną skrzynek pocztowych programu Exchange. Wymaga Azure AD Connect build 1.1.552.0 lub After. |

## <a name="exchange-mail-public-folder"></a>Folder publiczny poczty programu Exchange
Te atrybuty są synchronizowane z Active Directory lokalnego do usługi Azure AD w przypadku wybrania opcji włączenia **folderu publicznego poczty programu Exchange**.

| Nazwa atrybutu | PublicFolder | Komentarz |
| --- | :---:| --- |
| displayName | X |  |
| mail (poczta) | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID | X |  |
| proxyAddresses | X |  |
| targetAddress | X |  |

## <a name="device-writeback"></a>Zapisywanie zwrotne urządzeń
Obiekty urządzeń są tworzone w Active Directory. Te obiekty mogą być urządzeniami przyłączonymi do usługi Azure AD lub komputerów z systemem Windows 10 przyłączonych do domeny.

| Nazwa atrybutu | Urządzenie | Komentarz |
| --- |:---:| --- |
| altSecurityIdentities |X | |
| displayName |X | |
| dn |X | |
| msDS-CloudAnchor |X | |
| msDS-DeviceID |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DevicePhysicalIDs |X | |
| msDS-KeyCredentialLink |X |Tylko z schematem usługi AD systemu Windows Server 2016 |
| msDS-iszgodne |X | |
| msDS-IsEnabled |X | |
| msDS-IsManaged |X | |
| msDS-RegisteredOwner |X | |

## <a name="notes"></a>Uwagi
* W przypadku używania alternatywnego identyfikatora Atrybut userPrincipalName jest synchronizowany z atrybutem usługi Azure AD onPremisesUserPrincipalName. Atrybut identyfikatora alternatywnego, na przykład poczta, jest synchronizowany z atrybutem userPrincipalName usługi Azure AD.
* Na powyższych listach obiekt typu **użytkownik** ma również zastosowanie do typu obiektu **InetOrgPerson**.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o konfiguracji [synchronizacji Azure AD Connect](how-to-connect-sync-whatis.md) .

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).