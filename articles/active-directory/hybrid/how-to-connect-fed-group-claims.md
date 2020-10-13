---
title: Skonfiguruj oświadczenia grupy dla aplikacji z Azure Active Directory | Microsoft Docs
description: Informacje na temat konfigurowania oświadczeń grupy do użycia z usługą Azure AD.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: ce438ad0725aff677f897a635a0cd32d92bbbdbe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91265473"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory"></a>Konfigurowanie oświadczeń grupy dla aplikacji z Azure Active Directory

Azure Active Directory może zapewnić użytkownikom informacje o członkostwie w grupach, które mają być używane w aplikacjach.  Obsługiwane są dwa główne wzorce:

- Grupy identyfikowane przez ich Azure Active Directory atrybut identyfikatora obiektu (OID)
- Grupy identyfikowane przez atrybuty sAMAccountName lub GroupSID dla grup i użytkowników zsynchronizowanych z Active Directory (AD)

> [!IMPORTANT]
> Istnieją pewne zastrzeżenia dotyczące tej funkcji:
>
>- Obsługa używania atrybutów sAMAccountName i identyfikatora zabezpieczeń (SID) synchronizowanych ze środowiska lokalnego została zaprojektowana, aby umożliwić przeniesienie istniejących aplikacji z AD FS i innych dostawców tożsamości. Grupy zarządzane w usłudze Azure AD nie zawierają atrybutów niezbędnych do emisji tych oświadczeń.
>- W dużych organizacjach liczba grup, do których należy użytkownik, może przekroczyć limit, który Azure Active Directory doda do tokenu. 150 grup dla tokenu SAML i 200 dla JWT. Może to prowadzić do nieprzewidzianych wyników. Jeśli użytkownicy mają dużą liczbę członkostw w grupach, zalecamy użycie opcji w celu ograniczenia grup emitowanych w oświadczeniach do odpowiednich grup dla aplikacji.  
>- W przypadku tworzenia nowych aplikacji lub w przypadkach, w których można dla nich skonfigurować aplikację, a w przypadku, gdy obsługa grup zagnieżdżonych nie jest wymagana, zaleca się, aby autoryzacja w aplikacji była oparta na rolach aplikacji, a nie w grupach.  Ogranicza to ilość informacji potrzebnych do przechodzenia do tokenu, jest bezpieczniejsze i oddziela przypisanie użytkowników od konfiguracji aplikacji.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>Oświadczenia grupy dla aplikacji migrowanych z AD FS i innych dostawców tożsamości

Wiele aplikacji skonfigurowanych do uwierzytelniania za pomocą AD FS polega na informacjach dotyczących członkostwa w grupach w postaci atrybutów grupy usługi Windows AD.   Te atrybuty to nazwa sAMAccountName grupy, która może być kwalifikowana według nazwy domeny lub identyfikatora zabezpieczeń grupy systemu Windows (GroupSID).  Gdy aplikacja jest federacyjny z AD FS, AD FS używa funkcji TokenGroups, aby pobrać członkostwo w grupach dla użytkownika.

Aplikacja, która została przeniesiona z AD FS wymaga oświadczeń w tym samym formacie. Oświadczenia grup i ról mogą być emitowane z Azure Active Directory zawierającego kwalifikowana nazwa użytkownika domeny lub GroupSID zsynchronizowany z Active Directory, a nie obiektu Azure Active Directory objectID grupy.

Obsługiwane formaty oświadczeń grup są następujące:

- Identyfikator **objectid grupy Azure Active Directory** (dostępny dla wszystkich grup)
- **sAMAccountName** (dostępne dla grup synchronizowanych z Active Directory)
- **NetbiosDomain\sAMAccountName** (dostępne dla grup synchronizowanych z Active Directory)
- **DNSDomainName\sAMAccountName** (dostępne dla grup synchronizowanych z Active Directory)
- **Identyfikator zabezpieczeń grupy lokalnej** (dostępny dla grup synchronizowanych z Active Directory)

> [!NOTE]
> atrybuty sAMAccountName i właściwości identyfikatora SID grupy lokalnej są dostępne tylko dla obiektów grup synchronizowanych z Active Directory.   Nie są one dostępne w grupach utworzonych w Azure Active Directory ani usługi Office 365.   Aplikacje skonfigurowane w Azure Active Directory, aby uzyskać zsynchronizowane atrybuty grupy lokalnej, pobrać je tylko dla zsynchronizowanych grup.

## <a name="options-for-applications-to-consume-group-information"></a>Opcje dla aplikacji korzystających z informacji o grupach

Aplikacje mogą wywoływać punkt końcowy grup programu MS Graph, aby uzyskać informacje o grupie dla uwierzytelnionego użytkownika. To wywołanie zapewnia, że wszystkie grupy, do których należy użytkownik, są dostępne nawet wtedy, gdy istnieje duża liczba grup.  Wyliczanie grupowe jest następnie niezależne od ograniczeń rozmiaru tokenu.

Jeśli jednak istniejąca aplikacja oczekuje na korzystanie z informacji o grupie za pośrednictwem oświadczeń, Azure Active Directory można skonfigurować przy użyciu wielu różnych formatów oświadczeń.  Należy wziąć pod uwagę następujące opcje:

- W przypadku korzystania z członkostwa w grupie w celach autoryzacji w aplikacji zaleca się użycie identyfikatora ObjectID grupy. Identyfikator ObjectID grupy jest niezmienny i unikatowy w Azure Active Directory i jest dostępny dla wszystkich grup.
- Jeśli używasz konta sAMAccountName grupy lokalnej do autoryzacji, użyj nazw kwalifikowanych dla domeny;  istnieje mniej możliwości kolizji nazw. Nazwa sAMAccountName może być unikatowa w ramach domeny Active Directory, ale jeśli więcej niż jedna domena Active Directory jest synchronizowana z dzierżawą Azure Active Directory, istnieje możliwość, że więcej niż jedna grupa ma taką samą nazwę.
- Rozważ użycie [ról aplikacji](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) , aby zapewnić warstwę pośrednią między członkostwem grupy a aplikacją.   Następnie aplikacja wykonuje wewnętrzne decyzje dotyczące autoryzacji na podstawie Clams ról w tokenie.
- Jeśli aplikacja jest skonfigurowana do pobierania atrybutów grup, które są synchronizowane z Active Directory, a grupa nie zawiera tych atrybutów, nie zostanie uwzględniona w oświadczeniach.
- Oświadczenia grupowe w tokenach obejmują grupy zagnieżdżone, z wyjątkiem przypadków, gdy przy użyciu opcji ograniczają oświadczenia grupy do grup przypisanych do aplikacji.  Jeśli użytkownik należy do GroupB, a GroupB jest członkiem grupy, wówczas oświadczenia grupy dla użytkownika będą zawierać zarówno grupę, jak i GroupB. Gdy użytkownicy organizacji mają dużą liczbę członkostw w grupie, liczba grup wymieniona w tokenie może zwiększyć rozmiar tokenu.  Azure Active Directory ogranicza liczbę grup, które będą emitowane w tokenie do 150 dla zatwierdzeń SAML i 200 dla tokenu JWT.  Jeśli użytkownik jest członkiem większej liczby grup, grupy zostaną pominięte i zostanie uwzględniony link do punktu końcowego grafu w celu uzyskania informacji o grupie.

## <a name="prerequisites-for-using-group-attributes-synchronized-from-active-directory"></a>Wymagania wstępne dotyczące używania atrybutów grup synchronizowanych z Active Directory

W przypadku użycia formatu ObjectId oświadczenia członkostwa w grupie mogą być emitowane w tokenach dla każdej grupy. Aby można było używać oświadczeń grup w formacie innym niż identyfikator ObjectId grupy, grupy muszą być synchronizowane z Active Directory przy użyciu Azure AD Connect.

Istnieją dwie kroki konfigurowania Azure Active Directory, aby emitować nazwy grup dla Active Directory grup.

1. **Synchronizuj nazwy grup z Active Directory** Zanim Azure Active Directory mogą emitować nazwy grup lub lokalny identyfikator SID grupy w oświadczeniach grup lub ról, wymagane atrybuty muszą być synchronizowane z Active Directory.  Musisz mieć uruchomiony program Azure AD Connect w wersji 1.2.70 lub nowszej.   Wcześniejsze wersje Azure AD Connect niż 1.2.70 będą synchronizować obiekty grup z Active Directory, ale nie będą zawierać wymaganych atrybutów nazwy grupy.  Uaktualnij do bieżącej wersji.

2. **Skonfiguruj rejestrację aplikacji w Azure Active Directory, aby uwzględnić oświadczenia grupy w tokenach** Oświadczenia grup można skonfigurować w sekcji aplikacje dla przedsiębiorstw w portalu lub za pomocą manifestu aplikacji w sekcji rejestracje aplikacji.  Aby skonfigurować oświadczenia grupy w manifeście aplikacji, zobacz sekcję "Konfigurowanie rejestracji aplikacji Azure Active Directory dla atrybutów grup" poniżej.

## <a name="add-group-claims-to-tokens-for-saml-applications-using-sso-configuration"></a>Dodawanie oświadczeń grupy do tokenów dla aplikacji SAML przy użyciu konfiguracji rejestracji jednokrotnej

Aby skonfigurować oświadczenia grupy dla galerii lub aplikacji SAML innej niż Galeria, Otwórz **aplikacje dla przedsiębiorstw**, kliknij aplikację na liście, wybierz opcję **Konfiguracja logowania**jednokrotnego, a następnie wybierz pozycję **atrybuty użytkownika & oświadczenia**.

Kliknij pozycję **Dodaj zgłoszenie do grupy**  

![Zrzut ekranu przedstawiający stronę "atrybuty użytkownika & oświadczenia" z wybraną pozycją "Dodaj oświadczenie grupy".](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

Użyj przycisków radiowych, aby wybrać grupy do uwzględnienia w tokenie

![Zrzut ekranu pokazujący okno "oświadczenia grupy" z wybranymi grupami zabezpieczeń.](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| Zaznaczenie | Opis |
|----------|-------------|
| **Wszystkie grupy** | Emituje grupy zabezpieczeń i listy dystrybucyjne i role.  |
| **Grupy zabezpieczeń** | Emituje grupy zabezpieczeń, których użytkownik jest członkiem w ramach roszczeń grupowych |
| **Role katalogu** | Jeśli użytkownik ma przypisane role katalogu, są emitowane jako wids ' (nie będą wysyłane żądania grup) |
| **Grupy przypisane do aplikacji** | Emituje tylko te grupy, które są jawnie przypisane do aplikacji, a użytkownik jest członkiem |

Na przykład, aby wyemitować wszystkie grupy zabezpieczeń, których użytkownik jest członkiem, wybierz pozycję grupy zabezpieczeń

![Zrzut ekranu pokazujący okno "oświadczenia grupy" z wybraną opcją "grupy zabezpieczeń" i otwarte menu rozwijane "atrybut źródłowy".](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Aby emitować grupy przy użyciu atrybutów Active Directory synchronizowanych z Active Directory zamiast obiektów objectID usługi Azure AD wybierz wymagany format z listy rozwijanej. Oświadczenia będą uwzględniane tylko w przypadku grup synchronizowanych z Active Directory.

![Zrzut ekranu pokazujący menu rozwijane "atrybut źródłowy" jest otwarty.](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

Aby emitować tylko grupy przypisane do aplikacji, wybierz **grupy przypisane do aplikacji**

![Zrzut ekranu przedstawiający okno "oświadczenia grupy" z wybraną opcją "grupy przypisane do aplikacji".](media/how-to-connect-fed-group-claims/group-claims-ui-4-1.png)

Grupy przypisane do aplikacji zostaną uwzględnione w tokenie.  Inne grupy, do których należy użytkownik, zostaną pominięte.  W przypadku tej opcji grupy zagnieżdżone nie są uwzględniane, a użytkownik musi być bezpośrednim członkiem grupy przypisanej do aplikacji.

Aby zmienić grupy przypisane do aplikacji, wybierz aplikację z listy **aplikacje dla przedsiębiorstw** , a następnie kliknij pozycję **Użytkownicy i grupy** w menu nawigacji po lewej stronie aplikacji.

Zapoznaj się z dokumentem [Przypisz użytkownika lub grupę do aplikacji przedsiębiorstwa](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) , aby uzyskać szczegółowe informacje o zarządzaniu przypisaniem grupy do aplikacji.

### <a name="advanced-options"></a>Opcje zaawansowane

Sposób emitowania oświadczeń grup można modyfikować przez ustawienia w obszarze Opcje zaawansowane

Dostosuj nazwę oświadczenia grupy: w przypadku wybrania dla oświadczeń grupy można określić inny typ oświadczenia.   Wprowadź typ zgłoszenia w polu Nazwa i opcjonalną przestrzeń nazw dla żądania w polu przestrzeń nazw.

![Zrzut ekranu przedstawiający sekcję "Opcje zaawansowane" z wprowadzonymi wartościami "Dostosuj nazwę żądania grupy" wybrane i "nazwa" oraz "Przestrzeń nazw".](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

Niektóre aplikacje wymagają, aby informacje o członkostwie w grupie były wyświetlane w ramach roszczeń "role". Opcjonalnie można emitować grupy użytkownika jako role, zaznaczając pole "Emituj grupy jako oświadczenia roli".

![Zrzut ekranu przedstawiający sekcję "Opcje zaawansowane" z wybraną opcją "Dostosowywanie nazwy oświadczenia grupy" i "Emituj grupy jako oświadczenia roli".](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> Jeśli opcja wyemituj dane grupy jako role są używane, w ramach żądania roli będą wyświetlane tylko grupy.  Wszystkie role aplikacji, do których użytkownik jest przypisany, nie będą wyświetlane w ramach roszczeń ról.

### <a name="edit-the-group-claims-configuration"></a>Edytowanie konfiguracji oświadczeń grupy

Po dodaniu konfiguracji oświadczenia grupy do atrybutów użytkownika & konfiguracji oświadczeń opcja dodania oświadczenia grupy będzie wyszarzona.  Aby zmienić konfigurację oświadczenia grupy, kliknij oświadczenie grupy na liście **dodatkowe oświadczenia** .

![Interfejs użytkownika oświadczeń](media/how-to-connect-fed-group-claims/group-claims-ui-7.png)

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>Skonfiguruj rejestrację aplikacji usługi Azure AD dla atrybutów grupy

Oświadczenia grup można również skonfigurować w sekcji [opcjonalne oświadczenia](../../active-directory/develop/active-directory-optional-claims.md) [manifestu aplikacji](../../active-directory/develop/reference-app-manifest.md).

1. W portalu >Azure Active Directory > — Rejestracja aplikacji — >wybierz manifest >aplikacji

2. Włącz oświadczenia członkostwa w grupie, zmieniając groupMembershipClaim

Prawidłowe wartości:

| Zaznaczenie | Opis |
|----------|-------------|
| **Całą** | Emituje grupy zabezpieczeń, listy dystrybucyjne i role |
| **Grupy securitygroup** | Emituje grupy zabezpieczeń, których użytkownik jest członkiem w ramach roszczeń grupowych |
| **"DirectoryRole** | Jeśli użytkownik ma przypisane role katalogu, są emitowane jako wids ' (nie będą wysyłane żądania grup) |
| **"Aplikacja** | Emituje tylko te grupy, które są jawnie przypisane do aplikacji, a użytkownik jest członkiem |

   Na przykład:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Domyślnie identyfikatory obiektów grupy będą emitowane w wartości Claim Group.  Aby zmodyfikować wartość żądania w taki sposób, aby zawierała atrybuty grupy lokalnej, lub aby zmienić typ typu "rola", użyj konfiguracji OptionalClaims w następujący sposób:

3. Określ ustawienia nazw grup opcjonalne oświadczenia.

   Jeśli chcesz, aby grupy w tokenie zawierały atrybuty lokalnych grup usługi AD, określ, które opcjonalne oświadczenie o typie tokenu należy zastosować do w sekcji opcjonalne oświadczenia.  Można wymienić wiele typów tokenów:

   - idToken dla tokenu identyfikatora OIDC
   - accessToken dla tokenu dostępu OAuth/OIDC
   - Saml2Token dla tokenów SAML.

   > [!NOTE]
   > Typ Saml2Token ma zastosowanie do tokenów formatu SAML 1.1 i SAML 2.0

   Dla każdego odpowiedniego typu tokenu zmodyfikuj je, aby użyć sekcji OptionalClaims w manifeście. Schemat OptionalClaims jest następujący:

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Opcjonalny schemat oświadczeń | Wartość |
   |----------|-------------|
   | **Nazwij** | Musi być "grupami" |
   | **zewnętrz** | Nie używany. Pomiń lub określ wartość null |
   | **olejk** | Nie używany. Pomiń lub określ wartość false |
   | **AdditionalProperties** | Lista dodatkowych właściwości.  Prawidłowe opcje to "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   W additionalProperties są wymagane tylko jeden z "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Jeśli istnieje więcej niż jeden, zostanie użyta pierwsza wartość i wszystkie pozostałe zostały zignorowane.

   Niektóre aplikacje wymagają informacji o grupie dla użytkownika w ramach roszczeń ról.  Aby zmienić typ roszczeń na z na podstawie żądania grupy do roli, Dodaj "emit_as_roles" do dodatkowych właściwości.  Wartości grupy będą emitowane w ramach roszczeń ról.

   > [!NOTE]
   > Jeśli zostanie użyta wartość "emit_as_roles", wszystkie role aplikacji skonfigurowane do przypisania użytkownika nie będą wyświetlane w ramach tego żądania

### <a name="examples"></a>Przykłady

Emituj grupy jako nazwy grup w tokenach dostępu OAuth w formacie dnsDomainName\SAMAccountName

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Aby emitować nazwy grup, które mają być zwracane w formacie netbiosDomain\samAccountName jako rolę role w tokenach identyfikatorów SAML i OIDC:

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }
 ```

## <a name="next-steps"></a>Następne kroki

- [Dodawanie autoryzacji przy użyciu grup & grup oświadczeń do ASP.NET Core aplikacji sieci Web (przykład kodu)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-2-Groups/README.md)
- [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)
- [Konfigurowanie oświadczeń roli](../../active-directory/develop/active-directory-enterprise-app-role-management.md)
