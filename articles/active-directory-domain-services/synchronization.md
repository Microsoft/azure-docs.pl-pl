---
title: Jak działa synchronizacja w Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak proces synchronizacji działa w przypadku obiektów i poświadczeń z dzierżawy usługi Azure AD lub lokalnego środowiska Active Directory Domain Services do domeny zarządzanej Azure Active Directory Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 41ba337765b4a0a93be52f08ae6656707cf7aa73
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618811"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-active-directory-domain-services-managed-domain"></a>Jak obiekty i poświadczenia są synchronizowane w Azure Active Directory Domain Servicesej domenie zarządzanej

Obiekty i poświadczenia w domenie zarządzanej Azure Active Directory Domain Services (Azure AD DS) można utworzyć lokalnie w domenie lub zsynchronizować z dzierżawy usługi Azure Active Directory (Azure AD). Podczas pierwszego wdrażania usługi Azure AD DS zostanie skonfigurowana automatyczna synchronizacja jednokierunkowa i uruchomiona w celu replikowania obiektów z usługi Azure AD. Ta Jednokierunkowa synchronizacja nadal działa w tle, aby zapewnić aktualność domeny zarządzanej przez platformę Azure AD DS przy użyciu wszelkich zmian z usługi Azure AD. Na platformie Azure nie są wykonywane żadne synchronizacje AD DS z powrotem do usługi Azure AD.

W środowisku hybrydowym obiekty i poświadczenia z lokalnej domeny AD DS mogą być synchronizowane z usługą Azure AD przy użyciu Azure AD Connect. Po pomyślnym zsynchronizowaniu tych obiektów z usługą Azure AD automatyczna synchronizacja w tle sprawia, że te obiekty i poświadczenia są dostępne dla aplikacji korzystających z domeny zarządzanej.

Jeśli na Premium AD DS i usługi Azure AD są skonfigurowane do uwierzytelniania federacyjnego przy użyciu usług AD FS, w usłudze Azure DS nie jest dostępny żaden (bieżący/prawidłowy) skrót hasła. Konta użytkowników usługi Azure AD utworzone przed zaimplementowanym uwierzytelnianiem pokarmowym mogą mieć stary skrót hasła, ale prawdopodobnie nie pasuje do skrótu hasła Premium. W związku z tym usługa Azure AD DS nie będzie w stanie zweryfikować poświadczeń użytkowników.

Na poniższym diagramie pokazano, jak działa synchronizacja między usługą Azure AD DS, usługą Azure AD i opcjonalnym środowiskiem lokalnym AD DS:

![Przegląd synchronizacji Azure AD Domain Services domeny zarządzanej](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>Synchronizacja z usługi Azure AD do usługi Azure AD DS

Konta użytkowników, członkostwa w grupach i skróty poświadczeń są synchronizowane jeden sposób z usługi Azure AD do AD DS platformy Azure. Ten proces synchronizacji jest automatyczny. Nie musisz konfigurować ani monitorować tego procesu synchronizacji ani nim zarządzać. Synchronizacja początkowa może potrwać kilka godzin, w zależności od liczby obiektów w katalogu usługi Azure AD. Po zakończeniu synchronizacji początkowej zmiany wprowadzone w usłudze Azure AD, takie jak zmiany hasła lub atrybutów, zostaną automatycznie zsynchronizowane z usługą Azure AD DS.

Po utworzeniu użytkownika w usłudze Azure AD nie są one zsynchronizowane z usługą Azure AD DS, dopóki nie zmienią hasła w usłudze Azure AD. Ten proces zmiany haseł powoduje generowanie w usłudze Azure AD skrótów haseł dla uwierzytelniania Kerberos i NTLM. W celu pomyślnego uwierzytelnienia użytkownika w usłudze Azure AD DS są konieczne skróty haseł.

Proces synchronizacji jest jednym ze sposobów/jednokierunkowy przez projektowanie. Nie istnieje wsteczna Synchronizacja zmian z platformy Azure AD DS z powrotem do usługi Azure AD. Domena zarządzana jest w dużym stopniu tylko do odczytu, z wyjątkiem niestandardowych jednostek organizacyjnych, które można utworzyć. Nie można wprowadzać zmian w atrybutach użytkownika, hasłach użytkowników ani członkostwie w grupach w domenie zarządzanej.

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>Synchronizacja atrybutów i mapowanie do AD DS platformy Azure

W poniższej tabeli wymieniono niektóre typowe atrybuty i sposób ich synchronizowania z usługą Azure AD DS.

| Atrybut w usłudze Azure AD DS | Element źródłowy | Uwagi |
|:--- |:--- |:--- |
| UPN | Atrybut *nazwy UPN* użytkownika w dzierżawie usługi Azure AD | Atrybut nazwy UPN dzierżawy usługi Azure AD jest zsynchronizowany z usługą Azure AD DS. Najbardziej niezawodnym sposobem zalogowania się do domeny zarządzanej jest użycie nazwy UPN. |
| Nazwy | Atrybut *mailNickname* użytkownika w dzierżawie usługi Azure AD lub wygenerowany automatycznie | Atrybut *sAMAccountName* jest źródłem z atrybutu *mailNickname* w dzierżawie usługi Azure AD. Jeśli wiele kont użytkowników ma ten sam atrybut *mailNickname* , nazwa *sAMAccountName* jest generowana automatycznie. Jeśli długość prefiksu *mailNickname* lub *nazwy UPN* użytkownika jest dłuższa niż 20 znaków, nazwa *sAMAccountName* jest generowana automatycznie, aby spełnić limit 20 znaków dla atrybutów *sAMAccountName* . |
| Hasła | Hasło użytkownika z dzierżawy usługi Azure AD | Starsze skróty haseł wymagane do uwierzytelniania NTLM lub Kerberos są synchronizowane z dzierżawy usługi Azure AD. Jeśli dzierżawa usługi Azure AD jest skonfigurowana pod kątem synchronizacji hybrydowej przy użyciu Azure AD Connect, te skróty haseł pochodzą z lokalnego środowiska AD DS. |
| Podstawowy identyfikator SID użytkownika/grupy | Automatycznie generowanych | Podstawowy identyfikator SID kont użytkowników/grup jest generowany automatycznie na platformie Azure AD DS. Ten atrybut nie jest zgodny z identyfikatorem SID podstawowego użytkownika/grupy obiektu w środowisku lokalnym AD DS. Ta niezgodność wynika z faktu, że domena zarządzana ma inną przestrzeń nazw identyfikatora SID niż domena lokalna AD DS. |
| Historia identyfikatora SID dla użytkowników i grup | Lokalny podstawowy użytkownik i identyfikator SID grupy | Atrybut *SIDHistory* dla użytkowników i grup w usłudze Azure AD DS jest ustawiony jako zgodny z identyfikatorem SID odpowiedniego użytkownika podstawowego lub grupy w środowisku lokalnym AD DS. Ta funkcja ułatwia tworzenie i przenoszenie aplikacji lokalnych na platformę Azure AD DS łatwiejsze, ponieważ nie trzeba ponownie tworzyć listy ACL zasobów. |

> [!TIP]
> **Zaloguj się do domeny zarządzanej przy użyciu formatu nazwy UPN** Atrybut *sAMAccountName* , taki jak `AADDSCONTOSO\driley` , może być generowany automatycznie dla niektórych kont użytkowników w domenie zarządzanej. Automatycznie generowane *sAMAccountName* użytkownika może różnić się od prefiksu UPN, więc nie zawsze jest to niezawodne rozwiązanie do logowania.
>
> Jeśli na przykład wielu użytkowników ma ten sam atrybut *mailNickname* lub użytkownicy mają zbyt długie prefiksy UPN, można automatycznie wygenerować *sAMAccountName* dla tych użytkowników. Użyj formatu UPN, takiego jak `driley@aaddscontoso.com` , aby w sposób niezawodny zalogować się do domeny zarządzanej.

### <a name="attribute-mapping-for-user-accounts"></a>Mapowanie atrybutów dla kont użytkowników

W poniższej tabeli przedstawiono, w jaki sposób konkretne atrybuty obiektów użytkownika w usłudze Azure AD są synchronizowane z odpowiednimi atrybutami na platformie Azure AD DS.

| Atrybut użytkownika w usłudze Azure AD | Atrybut użytkownika w usłudze Azure AD DS |
|:--- |:--- |
| accountEnabled |kontroli konta użytkownika (ustawia lub czyści bit ACCOUNT_DISABLED) |
| city |l |
| country |co |
| działu, |działu, |
| displayName |displayName |
| employeedId |IDPracownika |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| Stanowiska |tytuł |
| mail (poczta) |mail (poczta) |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (czasami może być generowane automatycznie) |
| manager |manager |
| telefon komórkowy |telefon komórkowy |
| obiektu |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |Skopiowano element SIDHistory |
| passwordPolicies |kontroli konta użytkownika (ustawia lub czyści bit DONT_EXPIRE_PASSWORD) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| Pocztowy |Pocztowy |
| preferredLanguage |preferredLanguage |
| proxyAddresses | proxyAddresses |
| stan |st |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Mapowanie atrybutów dla grup

W poniższej tabeli przedstawiono, w jaki sposób konkretne atrybuty obiektów grupy w usłudze Azure AD są synchronizowane z odpowiednimi atrybutami na platformie Azure AD DS.

| Group — atrybut w usłudze Azure AD | Grupuj atrybut na platformie Azure AD DS |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (czasami może być generowane automatycznie) |
| mail (poczta) |mail (poczta) |
| mailNickname |msDS-AzureADMailNickname |
| obiektu |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |Skopiowano element SIDHistory |
| proxyAddresses | proxyAddresses |
| securityEnabled |groupType |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>Synchronizacja z AD DS lokalnych do usługi Azure AD i platformy Azure AD DS

Azure AD Connect służy do synchronizowania kont użytkowników, członkostw w grupach i skrótów poświadczeń z lokalnego środowiska AD DS do usługi Azure AD. Są synchronizowane atrybuty kont użytkowników, takich jak nazwy UPN i Lokalny identyfikator zabezpieczeń (SID). Aby zalogować się przy użyciu usługi Azure AD DS, starsze skróty haseł wymagane do uwierzytelniania NTLM i Kerberos są również synchronizowane z usługą Azure AD.

> [!IMPORTANT]
> Azure AD Connect należy instalować i konfigurować tylko na potrzeby synchronizacji z lokalnymi środowiskami AD DS. Nie jest obsługiwane Instalowanie Azure AD Connect w domenie zarządzanej w celu synchronizacji obiektów z powrotem do usługi Azure AD.

W przypadku skonfigurowania zapisu zmiany z usługi Azure AD są synchronizowane z powrotem do środowiska lokalnego AD DS. Jeśli na przykład użytkownik zmieni hasło przy użyciu samoobsługowego zarządzania hasłami w usłudze Azure AD, hasło zostanie zaktualizowane ponownie w środowisku lokalnym AD DS.

> [!NOTE]
> Zawsze używaj najnowszej wersji Azure AD Connect, aby mieć pewność, że masz poprawki dla wszystkich znanych usterek.

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Synchronizacja z środowiska lokalnego z obsługą kilku lasów

Wiele organizacji ma dość skomplikowane środowisko lokalne AD DS, które obejmuje wiele lasów. Azure AD Connect obsługuje synchronizowanie użytkowników, grup i skrótów poświadczeń z środowisk wielu lasów z usługą Azure AD.

Usługa Azure AD ma znacznie prostszy i płaski obszar nazw. Aby umożliwić użytkownikom niezawodne uzyskiwanie dostępu do aplikacji zabezpieczonych za pomocą usługi Azure AD, Rozwiąż konflikty nazw UPN między kontami użytkowników w różnych lasach. Domeny zarządzane używają płaskiej struktury jednostki organizacyjnej, podobnej do usługi Azure AD. Wszystkie konta i grupy użytkowników są przechowywane w kontenerze *AADDC users* , mimo że są synchronizowane z różnych domen lokalnych lub lasów, nawet jeśli skonfigurowano hierarchiczną strukturę organizacyjną w środowisku lokalnym. Domena zarządzana Spłaszcza wszystkie hierarchiczne struktury jednostek organizacyjnych.

Jak wspomniano wcześniej, nie istnieje żadna synchronizacja z platformy Azure AD DS z powrotem do usługi Azure AD. Można [utworzyć niestandardową jednostkę organizacyjną (OU)](create-ou.md) w usłudze Azure AD DS, a następnie użytkowników, grupy lub konta usług w ramach tych niestandardowych jednostek organizacyjnych. Żaden z obiektów utworzonych w niestandardowych jednostkach organizacyjnych nie jest synchronizowany z powrotem do usługi Azure AD. Te obiekty są dostępne tylko w ramach domeny zarządzanej i nie są widoczne przy użyciu poleceń cmdlet programu PowerShell usługi Azure AD, interfejsu API Microsoft Graph lub przy użyciu interfejsu użytkownika zarządzania usługą Azure AD.

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>Co nie jest zsynchronizowane z usługą Azure AD DS

Następujące obiekty lub atrybuty nie są zsynchronizowane z lokalnego środowiska AD DS do usługi Azure AD lub platformy Azure AD DS:

* **Wykluczone atrybuty:** Można zdecydować się na wykluczenie niektórych atrybutów z synchronizacji do usługi Azure AD z lokalnego środowiska AD DS przy użyciu Azure AD Connect. Te wykluczone atrybuty nie są następnie dostępne w usłudze Azure AD DS.
* **Zasady grupy:** Zasady grupy skonfigurowane w lokalnym środowisku AD DS nie są synchronizowane z usługą Azure AD DS.
* **Folder SYSVOL:** Zawartość folderu *SYSVOL* w lokalnym środowisku AD DS nie jest synchronizowana z usługą Azure AD DS.
* **Obiekty komputera:** Obiekty komputerów dla komputerów przyłączonych do środowiska lokalnego AD DS nie są synchronizowane z usługą Azure AD DS. Te komputery nie mają relacji zaufania z domeną zarządzaną i należą tylko do środowiska lokalnego AD DS. W usłudze Azure AD DS są wyświetlane tylko obiekty komputerów w przypadku komputerów, które mają jawnie przyłączoną do domeny zarządzanej domeny.
* **Atrybuty SIDHistory dla użytkowników i grup:** Identyfikatory SID użytkownika podstawowego i grupy podstawowej z lokalnego środowiska AD DS są synchronizowane z usługą Azure AD DS. Jednak istniejące atrybuty *historii SID* dla użytkowników i grup nie są zsynchronizowane ze środowiska lokalnego AD DS do usługi Azure AD DS.
* **Struktury jednostek organizacyjnych (OU):** Jednostki organizacyjne zdefiniowane w środowisku lokalnym AD DS nie są synchronizowane z usługą Azure AD DS. Istnieją dwie wbudowane jednostki organizacyjne na platformie Azure AD DS — jeden dla użytkowników i jeden dla komputerów. Domena zarządzana ma płaską strukturę jednostki organizacyjnej. Możesz [utworzyć niestandardową jednostkę organizacyjną w domenie zarządzanej](create-ou.md).

## <a name="password-hash-synchronization-and-security-considerations"></a>Synchronizacja skrótów haseł i zagadnienia dotyczące zabezpieczeń

Po włączeniu usługi Azure AD DS wymagane są starsze skróty haseł dla uwierzytelniania NTLM + Kerberos. Usługa Azure AD nie przechowuje haseł w postaci zwykłego tekstu, przez co nie można automatycznie generować tych skrótów dla istniejących kont użytkowników. Po wygenerowaniu i zapisaniu skróty haseł zgodne z NTLM i Kerberos są zawsze przechowywane w sposób zaszyfrowany w usłudze Azure AD.

Klucze szyfrowania są unikatowe dla każdej dzierżawy usługi Azure AD. Te skróty są szyfrowane w taki sposób, że tylko usługa Azure AD DS ma dostęp do kluczy odszyfrowywania. Żadna inna usługa lub składnik w usłudze Azure AD nie ma dostępu do kluczy odszyfrowywania.

Starsze skróty haseł są następnie synchronizowane z usługi Azure AD z kontrolerami domeny dla domeny zarządzanej. Dyski dla tych zarządzanych kontrolerów domeny w usłudze Azure AD DS są szyfrowane w stanie spoczynku. Te skróty haseł są przechowywane i zabezpieczane na tych kontrolerach domeny, podobnie jak w przypadku przechowywania i zabezpieczania haseł w środowisku lokalnym AD DS.

W przypadku środowisk usługi Azure AD tylko w chmurze [Użytkownicy muszą zresetować/zmienić hasło](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) , aby można było generować i przechowywać w usłudze Azure AD odpowiednie skróty haseł. W przypadku wszystkich kont użytkowników w chmurze utworzonych w usłudze Azure AD po włączeniu Azure AD Domain Services skróty haseł są generowane i przechowywane w formacie zgodnym z NTLM i Kerberos. Wszystkie konta użytkowników w chmurze muszą zmienić swoje hasło przed ich zsynchronizowaniem z usługą Azure AD DS.

W przypadku kont użytkowników hybrydowych synchronizowanych z lokalnego środowiska AD DS przy użyciu Azure AD Connect, należy [skonfigurować Azure AD Connect do synchronizowania skrótów haseł w formatach zgodnych z protokołem NTLM i Kerberos](tutorial-configure-password-hash-sync.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat synchronizacji haseł, zobacz [jak działa synchronizacja skrótów haseł z Azure AD Connect](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context).

Aby rozpocząć pracę z usługą Azure AD DS, [Utwórz domenę zarządzaną](tutorial-create-instance.md).
