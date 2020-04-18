---
title: Jak działa synchronizacja w usługach domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak działa proces synchronizacji obiektów i poświadczeń z dzierżawy usługi Azure AD lub lokalnego środowiska Usług domenowych Active Directory do domeny zarządzanej usług domenowych Usługi active directory platformy Azure.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 38ed48df4d681543cc30daccf46b98635d973b89
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639906"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-ad-domain-services-managed-domain"></a>Jak obiekty i poświadczenia są synchronizowane w domenie zarządzanej usług domenowych usługi Azure AD

Obiekty i poświadczenia w domenie zarządzanej usług domenowych usługi active directory platformy Azure (AD DS) można utworzyć lokalnie w domenie lub zsynchronizować z dzierżawy usługi Azure Active Directory (Azure AD). Podczas pierwszego wdrażania usługi Azure AD DS jest konfigurowana automatyczna synchronizacja jednokierunkowa i uruchamiana w celu replikowania obiektów z usługi Azure AD. Ta jednokierunkowa synchronizacja jest nadal uruchamiana w tle, aby zapewnić aktualną domenę zarządzaną usługą Azure AD DS z wszelkimi zmianami z usługi Azure AD. Synchronizacja nie występuje z usług Azure AD DS z powrotem do usługi Azure AD.

W środowisku hybrydowym obiekty i poświadczenia z lokalnej domeny usług AD DS można synchronizować z usługą Azure AD przy użyciu usługi Azure AD Connect. Gdy te obiekty zostaną pomyślnie zsynchronizowane z usługą Azure AD, automatyczna synchronizacja w tle powoduje udostępnienie tych obiektów i poświadczeń aplikacjom korzystającym z domeny zarządzanej usług Azure AD DS.

Na poniższym diagramie przedstawiono, jak działa synchronizacja między usługami Azure AD DS, usługą Azure AD i opcjonalnym lokalnym środowiskiem usług AD DS:

![Omówienie synchronizacji domeny zarządzanej usług domenowych usługi AD azure](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>Synchronizacja z usługi Azure AD na usługi Azure AD DS

Konta użytkowników, członkostwa w grupach i skróty poświadczeń są synchronizowane w jedną stronę z usługi Azure AD na usługi Azure AD DS. Ten proces synchronizacji jest automatyczny. Nie trzeba konfigurować, monitorować ani zarządzać tym procesem synchronizacji. Początkowa synchronizacja może potrwać od kilku godzin do kilku dni, w zależności od liczby obiektów w katalogu usługi Azure AD. Po zakończeniu synchronizacji początkowej zmiany wprowadzone w usłudze Azure AD, takie jak zmiany hasła lub atrybutu, są następnie automatycznie synchronizowane z usługą Azure AD DS.

Gdy użytkownik jest tworzony w usłudze Azure AD, nie są one synchronizowane z usługą Azure AD DS, dopóki nie zmienią hasła w usłudze Azure AD. Ten proces zmiany hasła powoduje, że skróty haseł dla uwierzytelniania Kerberos i NTLM mają być generowane i przechowywane w usłudze Azure AD. Skróty haseł są potrzebne do pomyślnego uwierzytelnienia użytkownika w usługach Azure AD DS.

Proces synchronizacji jest jednokierunkowy / jednokierunkowy według projektu. Nie ma odwrotnej synchronizacji zmian z usług Azure AD DS z powrotem do usługi Azure AD. Domena zarządzana usługą Azure AD DS jest w dużej mierze tylko do odczytu, z wyjątkiem niestandardowych procesorów operacyjnych, które można utworzyć. Nie można wprowadzać zmian w atrybutach użytkownika, hasłach użytkowników ani członkostw w grupach w domenie zarządzanej usług Azure AD DS.

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>Synchronizacja i mapowanie atrybutów do usług Azure AD DS

W poniższej tabeli wymieniono niektóre typowe atrybuty i sposób ich synchronizacji z usługą Azure AD DS.

| Atrybut w usługach Azure AD DS | Element źródłowy | Uwagi |
|:--- |:--- |:--- |
| UPN | Atrybut *UPN* użytkownika w dzierżawie usługi Azure AD | Atrybut UPN z dzierżawy usługi Azure AD jest synchronizowany w stanie— z usługą Azure AD DS. Najbardziej niezawodnym sposobem logowania się do domeny zarządzanej usług Azure AD DS jest użycie nazwy UPN. |
| Samaccountname | Atrybut *mailNickname* użytkownika w dzierżawie usługi Azure AD lub autogenerowane | Atrybut *SAMAccountName* pochodzi z atrybutu *mailNickname* w dzierżawie usługi Azure AD. Jeśli wiele kont użytkowników ma ten sam atrybut *mailNickname,* *SAMAccountName* jest generowany automatycznie. Jeśli prefiks *nazwy pocztowej* lub *UPN* użytkownika jest dłuższy niż 20 znaków, *SAMAccountName* jest automatycznie generowany w celu spełnienia limitu 20 znaków atrybutów *SAMAccountName.* |
| Hasła | Hasło użytkownika z dzierżawy usługi Azure AD | Starsze skróty haseł wymagane do uwierzytelniania NTLM lub Kerberos są synchronizowane z dzierżawy usługi Azure AD. Jeśli dzierżawa usługi Azure AD jest skonfigurowana do synchronizacji hybrydowej przy użyciu usługi Azure AD Connect, te skróty haseł pochodzą z lokalnego środowiska usług AD DS. |
| Podstawowy identyfikator SID użytkownika/grupy | Wygenerowany automatycznie | Podstawowy identyfikator SID dla kont użytkowników/grup jest generowany automatycznie w usługach Azure AD DS. Ten atrybut nie jest zgodny z podstawowym identyfikatorem SID użytkownika/grupy obiektu w lokalnym środowisku usług AD DS. Ta niezgodność jest, ponieważ domena zarządzana usługą Azure AD DS ma inny obszar nazw sid niż lokalna domena usług AD DS. |
| Historia identyfikatorów SID dla użytkowników i grup | Lokalny identyfikator SID użytkownika podstawowego i grupy | Atrybut *SidHistory* dla użytkowników i grup w usługach Azure AD DS jest ustawiony tak, aby odpowiadał odpowiedniemu głównemu identyfikatorowi SID użytkownika lub grupy w lokalnym środowisku usług AD DS. Ta funkcja ułatwia przenoszenie i przenoszenie aplikacji lokalnych do usług Azure AD DS, ponieważ nie trzeba ponownie przenosić zasobów listy ACL. |

> [!TIP]
> **Logowanie się do domeny zarządzanej przy użyciu formatu nazwy UPN** Atrybut *SAMAccountName,* taki `AADDSCONTOSO\driley`jak , może być generowany automatycznie dla niektórych kont użytkowników w domenie zarządzanej usług Azure AD DS. Automatycznie wygenerowany PRZEZ UŻYTKOWNIKÓW *SAMAccountName* może różnić się od prefiksu nazwy UPN, więc nie zawsze jest niezawodnym sposobem logowania.
>
> Na przykład jeśli wielu użytkowników ma ten sam atrybut *mailNickname* lub użytkownicy mają zbyt długie prefiksy NAZWY UPN, *SAMAccountName* dla tych użytkowników może być generowany automatycznie. Użyj formatu nazwy UPN, takiego jak `driley@aaddscontoso.com`, aby niezawodnie zalogować się do domeny zarządzanej usług Azure AD DS.

### <a name="attribute-mapping-for-user-accounts"></a>Mapowanie atrybutów dla kont użytkowników

W poniższej tabeli przedstawiono, jak określone atrybuty obiektów użytkowników w usłudze Azure AD są synchronizowane z odpowiednimi atrybutami w usługach Azure AD DS.

| Atrybut użytkownika w usłudze Azure AD | Atrybut użytkownika w usługach Azure AD DS |
|:--- |:--- |
| kontoWłasny |userAccountControl (ustawia lub czyści ACCOUNT_DISABLED bit) |
| city |l |
| country |Co |
| department |department |
| displayName |displayName |
| numer telefonu faksymiiltelephoneNumber |numer telefonu faksymiiltelephoneNumber |
| givenName |givenName |
| jobTitle (Tytuł pracy) |title |
| mail (poczta) |mail (poczta) |
| mailNickname (nazwa pocztowa) |msDS-AzureADMailNickname |
| mailNickname (nazwa pocztowa) |SAMAccountName (czasami może być automatyczniegenerowane) |
| telefon komórkowy |telefon komórkowy |
| Objectid |identyfikatorobjectid msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |Sidhistory |
| hasłoPolicja |userAccountControl (ustawia lub czyści DONT_EXPIRE_PASSWORD bit) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| Postalcode |Postalcode |
| preferowany Język |preferowany Język |
| state |st |
| Streetaddress |Streetaddress |
| surname |sn |
| Telephonenumber |Telephonenumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Mapowanie atrybutów dla grup

W poniższej tabeli przedstawiono, jak określone atrybuty dla obiektów grupy w usłudze Azure AD są synchronizowane z odpowiednimi atrybutami w usługach Azure AD DS.

| Atrybut grupy w usłudze Azure AD | Atrybut grupy w usługach Azure AD DS |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (czasami może być automatyczniegenerowane) |
| mail (poczta) |mail (poczta) |
| mailNickname (nazwa pocztowa) |msDS-AzureADMailNickname |
| Objectid |identyfikatorobjectid msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |Sidhistory |
| securityEnabled (Nie można pojąć) |groupType |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>Synchronizacja z lokalnych usług AD DS z usługami Azure AD i usługą Azure AD DS

Usługa Azure AD Connect służy do synchronizowania kont użytkowników, członkostw w grupach i skrótów poświadczeń z lokalnego środowiska usług AD DS do usługi Azure AD. Atrybuty kont użytkowników, takie jak nazwa UPN i lokalny identyfikator zabezpieczeń (SID) są synchronizowane. Aby zalogować się przy użyciu usług Azure AD DS, starsze skróty haseł wymagane dla uwierzytelniania NTLM i Kerberos są również synchronizowane z usługą Azure AD.

> [!IMPORTANT]
> Usługa Azure AD Connect powinna być instalowana i konfigurowana tylko do synchronizacji z lokalnymi środowiskami usług AD DS. Nie jest obsługiwana instalacja usługi Azure AD Connect w domenie zarządzanej usług Azure AD DS w celu synchronizowania obiektów z powrotem do usługi Azure AD.

Jeśli skonfigurujesz odpis, zmiany z usługi Azure AD są synchronizowane z powrotem do lokalnego środowiska usług AD DS. Na przykład jeśli użytkownik zmieni swoje hasło przy użyciu samoobsługowego zarządzania hasłami usługi Azure AD, hasło jest aktualizowane z powrotem w lokalnym środowisku usług AD DS.

> [!NOTE]
> Zawsze używaj najnowszej wersji usługi Azure AD Connect, aby upewnić się, że masz poprawki dla wszystkich znanych błędów.

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Synchronizacja ze środowiska lokalnego wielolesowego

Wiele organizacji ma dość złożone lokalne środowisko usług AD DS, które obejmuje wiele lasów. Usługa Azure AD Connect obsługuje synchronizowanie użytkowników, grup i skrótów poświadczeń ze środowisk wielolednych do usługi Azure AD.

Usługa Azure AD ma znacznie prostszą i płaską przestrzeń nazw. Aby umożliwić użytkownikom niezawodny dostęp do aplikacji zabezpieczonych przez usługę Azure AD, należy rozwiązać konflikty nazwy UPN między kontami użytkowników w różnych lasach. Domeny zarządzane usługi Azure AD DS używają płaskiej struktury jednostki organizacyjnej, podobnej do usługi Azure AD. Wszystkie konta użytkowników i grupy są przechowywane w kontenerze *Użytkownicy usługi AADDC,* mimo że są synchronizowane z różnych domen lokalnych lub lasów, nawet jeśli skonfigurowano hierarchiczną strukturę operacji organizacyjnych lokalnie. Domena zarządzana usługą Azure AD DS spłaszcza wszelkie hierarchiczne struktury jednostki organizacyjnej.

Jak wcześniej szczegółowo, nie ma synchronizacji z usług Azure AD DS z powrotem do usługi Azure AD. Można [utworzyć niestandardową jednostkę organizacyjną (OU)](create-ou.md) w usługach Azure AD DS, a następnie użytkowników, grup lub kont usług w ramach tych niestandardowych jednostek organizacyjnych. Żaden z obiektów utworzonych w niestandardowych procesorach organizacyjnych nie są synchronizowane z powrotem do usługi Azure AD. Obiekty te są dostępne tylko w domenie zarządzanej usług Azure AD DS i nie są widoczne przy użyciu poleceń cmdlet programu Azure AD PowerShell, interfejsu API programu Microsoft Graph lub przy użyciu interfejsu użytkownika zarządzania usługą Azure AD.

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>Co nie jest synchronizowane z usługami Ad DS usługi Azure

Następujące obiekty lub atrybuty nie są synchronizowane z lokalnego środowiska usług AD DS do usługi Azure AD lub usługi Azure AD DS:

* **Wykluczone atrybuty:** Można wykluczyć niektóre atrybuty z synchronizacji z usługą Azure AD z lokalnego środowiska usług AD DS przy użyciu usługi Azure AD Connect. Te wykluczone atrybuty nie są wtedy dostępne w usługach Azure AD DS.
* **Zasady grupy:** Zasady grupy skonfigurowane w lokalnym środowisku usług AD DS nie są synchronizowane z usługą Azure AD DS.
* **Folder Sysvol:** Zawartość folderu *Sysvol* w lokalnym środowisku usług AD DS nie jest synchronizowana z usługą Azure AD DS.
* **Obiekty komputerowe:** Obiekty komputera dla komputerów przyłączonych do lokalnego środowiska usług AD DS nie są synchronizowane z usługą Azure AD DS. Te komputery nie mają relacji zaufania z domeną zarządzaną usługą Azure AD DS i należą tylko do lokalnego środowiska usług AD DS. W usłudze Azure AD DS wyświetlane są tylko obiekty komputerów dla komputerów, które jawnie przyłączone do domeny zarządzanej są wyświetlane.
* **Atrybuty SidHistory dla użytkowników i grup:** Identyfikatory identyfikatorów SID użytkowników podstawowych i podstawowych grupy z lokalnego środowiska usług AD DS są synchronizowane z usługą Azure AD DS. Jednak istniejące atrybuty *SidHistory* dla użytkowników i grup nie są synchronizowane z lokalnego środowiska usług AD DS do usług Azure AD DS.
* **Struktury jednostek organizacyjnych (OU):** Jednostki organizacyjne zdefiniowane w lokalnym środowisku usług AD DS nie są synchronizowane z usługą Azure AD DS. Istnieją dwa wbudowane procesory organizacyjne w usługach Azure AD DS — jeden dla użytkowników i jeden dla komputerów. Domena zarządzana usługą Azure AD DS ma płaską strukturę jednostki organizacyjnej. W [domenie zarządzanej można utworzyć niestandardową jednostkę organizacyjną](create-ou.md).

## <a name="password-hash-synchronization-and-security-considerations"></a>Synchronizacja skrótów haseł i zagadnienia dotyczące zabezpieczeń

Po włączeniu usługi Azure AD DS wymagane są starsze skróty haseł dla uwierzytelniania NTLM + Kerberos. Usługa Azure AD nie przechowuje haseł w postaci zwykłego tekstu, więc te skróty nie mogą być generowane automatycznie dla istniejących kont użytkowników. Po wygenerowaniu i zapisaniu skróty haseł zgodne z usługą NTLM i Kerberos są zawsze przechowywane w sposób zaszyfrowany w usłudze Azure AD.

Klucze szyfrowania są unikatowe dla każdej dzierżawy usługi Azure AD. Te skróty są szyfrowane w taki sposób, że tylko usługi Azure AD DS ma dostęp do kluczy deszyfrowania. Żadna inna usługa lub składnik w usłudze Azure AD nie ma dostępu do kluczy deszyfrujących.

Starsze skróty haseł są następnie synchronizowane z usługi Azure AD do kontrolerów domeny dla domeny zarządzanej usług Azure AD DS. Dyski dla tych kontrolerów domeny zarządzanej w usługach Azure AD DS są szyfrowane w spoczynku. Te skróty haseł są przechowywane i zabezpieczane na tych kontrolerach domeny, podobnie jak hasła są przechowywane i zabezpieczane w lokalnym środowisku usług AD DS.

W przypadku środowisk usługi Azure AD tylko w chmurze [użytkownicy muszą zresetować/zmienić swoje hasło,](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) aby wymagane skróty haseł były generowane i przechowywane w usłudze Azure AD. W przypadku dowolnego konta użytkownika w chmurze utworzonego w usłudze Azure AD po włączeniu usług domenowych usługi Azure AD skróty haseł są generowane i przechowywane w formatach zgodnych z usługami NTLM i Kerberos. Wszystkie konta użytkowników w chmurze muszą zmienić swoje hasło, zanim będą synchronizowane z usługą Azure AD DS.

W przypadku hybrydowych kont użytkowników zsynchronizowanych z lokalnego środowiska usług AD DS przy użyciu usługi Azure AD Connect należy [skonfigurować usługę Azure AD Connect tak, aby synchronizowała skróty haseł w formatach zgodnych z usługami NTLM i Kerberos](tutorial-configure-password-hash-sync.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat specyfiki synchronizacji haseł, zobacz [Jak synchronizacja skrótów haseł działa z usługą Azure AD Connect](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context).

Aby rozpocząć korzystanie z usług Azure AD DS, [utwórz domenę zarządzaną](tutorial-create-instance.md).
