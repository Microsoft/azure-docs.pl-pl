---
title: Planowanie i rozwiązywanie problemów ze zmianami głównej nazwy użytkownika (UPN) platformy Azure
description: Poznaj znane problemy i środki zaradcze dla zmian nazw UPN
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19e40d135316c1c7cd270d2804fff1f487937685
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96858539"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Planowanie i rozwiązywanie problemów ze zmianami nazwy głównej użytkownika w Azure Active Directory

Główna nazwa użytkownika (UPN) to atrybut, który jest standardem komunikacji internetowej dla kont użytkowników. Nazwa UPN składa się z prefiksu UPN (nazwy konta użytkownika) i sufiksu UPN (nazwy domeny DNS). Prefiks sprzęga sufiks przy użyciu symbolu " \@ ". Na przykład someone@example.com. Nazwa UPN musi być unikatowa wśród wszystkich obiektów podmiotu zabezpieczeń w lesie katalogu. 

**W tym artykule przyjęto założenie, że nazwa UPN jest używana jako identyfikator użytkownika. Dotyczy to planowania zmian nazw UPN i odzyskiwania z problemów, które mogą wynikać ze zmian nazw UPN.**

> [!NOTE]
> Dla deweloperów zalecamy użycie identyfikatora objectID użytkownika jako niezmiennego identyfikatora, a nie nazwy UPN ani adresów e-mail, ponieważ ich wartości mogą ulec zmianie.


## <a name="learn-about-upns-and-upn-changes"></a>Dowiedz się więcej na temat nazw UPN i zmian nazw UPN
Strony logowania często monitują użytkowników o wprowadzenie adresu e-mail, gdy wymagana wartość jest rzeczywiście nazwą UPN. W związku z tym należy koniecznie zmienić nazwę UPN użytkowników w dowolnym momencie, gdy zmienią się podstawowe adresy e-mail.

Podstawowe adresy e-mail użytkowników mogą ulec zmianie z wielu powodów:

* oznaczenie firmy

* pracownicy przeniesieni do różnych działów firmy 

* Scalanie i pozyskiwanie

* zmiany nazwy pracownika

### <a name="types-of-upn-changes"></a>Typy zmian nazw UPN

Nazwę UPN można zmienić, zmieniając prefiks, sufiks lub oba te elementy.

* **Zmiana prefiksu**.

   *  Na przykład jeśli zmieniono nazwę osoby, możesz zmienić nazwę konta:  
BSimon@contoso.comdoBJohnson@contoso.com

   * Możesz również zmienić standardową firmę dla prefiksów:  
Bsimon@contoso.comdoBritta.Simon@contoso.com

* **Zmiana sufiksu**. <br>

    Na przykład, jeśli osoba zmieniła podział, można zmienić domenę: 

   * Britta.Simon@contoso.com do Britta.Simon@contosolabs.com <br>
     Lub<br>
    * Britta.Simon@corp.contoso.com do Britta.Simon@labs.contoso.com 

Zalecamy zmianę nazwy UPN użytkowników za każdym razem, gdy ich podstawowy adres e-mail zostanie zaktualizowany.

Podczas początkowej synchronizacji Active Directory z usługą Azure AD upewnij się, że wiadomości e-mail użytkowników są identyczne z ich nazwami UPN.

### <a name="upns-in-active-directory"></a>Nazwy UPN w Active Directory

W Active Directory domyślnym sufiksem nazwy UPN jest nazwa DNS domeny, w której utworzono konto użytkownika. W większości przypadków jest to nazwa domeny, która jest rejestrowana jako domena przedsiębiorstwa w Internecie. Jeśli utworzysz konto użytkownika w domenie contoso.com, domyślna nazwa UPN to

username@contoso.com

 Można jednak [dodać więcej sufiksów nazw UPN](../fundamentals/add-custom-domain.md) przy użyciu domen Active Directory i relacji zaufania. 

Na przykład możesz chcieć dodać labs.contoso.com i uzyskać do nich adres e-mail użytkowników. Stają się one następnie

username@labs.contoso.com.

>[!IMPORTANT]
> Jeśli [zmieniasz sufiks w Active Directory](../fundamentals/add-custom-domain.md), musisz się upewnić, że dodaliśmy pasującą niestandardową nazwę domeny do [usługi Azure AD](../fundamentals/add-custom-domain.md). 

![Zrzut ekranu zweryfikowanych domen](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>Nazwy UPN w Azure Active Directory

Użytkownicy logują się do usługi Azure AD przy użyciu wartości atrybutu userPrincipalName. 

W przypadku korzystania z usługi Azure AD w połączeniu z lokalną Active Directory konta użytkowników są synchronizowane przy użyciu usługi Azure AD Connect. Domyślnie Kreator Azure AD Connect używa atrybutu userPrincipalName z lokalnej Active Directory jako nazwy UPN w usłudze Azure AD. Można go zmienić na inny atrybut w instalacji niestandardowej.

Ważne jest, aby podczas aktualizowania głównej nazwy użytkownika (UPN) jednego użytkownika lub całej organizacji był zdefiniowany proces. 

Zobacz znane problemy i obejścia tego dokumentu.

Podczas synchronizowania kont użytkowników z Active Directory z usługą Azure AD upewnij się, że nazwy UPN w Active Directory mapują na zweryfikowane domeny w usłudze Azure AD.

![Zrzut ekranu przedstawiający przykłady nazw UPN zamapowanych do zweryfikowanych domen platformy Azure A D.](./media/howto-troubleshoot-upn-changes/verified-domains.png)

Jeśli wartość atrybutu userPrincipalName nie jest zgodna z zweryfikowaną domeną w usłudze Azure AD, proces synchronizacji zastępuje sufiks wartością default. onmicrosoft.com.


### <a name="roll-out-bulk-upn-changes"></a>Zbiorcza zmianami nazw UPN

Postępuj zgodnie z [najlepszymi rozwiązaniami dla programu pilotażowego](../fundamentals/active-directory-deployment-plans.md) dla zbiorczych zmian nazw UPN. Istnieje również przetestowany plan wycofania do przywracania nazw UPN, jeśli znajdziesz problemy, których nie można szybko rozwiązać. Po uruchomieniu pilotażu można rozpocząć pracę z małymi zestawami użytkowników przy użyciu różnych ról organizacyjnych i ich określonych zestawów aplikacji lub urządzeń.

Przechodzenie przez ten pierwszy podzestaw użytkowników daje dobry pomysł na to, czego użytkownicy powinni oczekiwać jako części zmiany. Dołącz te informacje do komunikacji użytkownika.

Utwórz zdefiniowaną procedurę zmieniania nazw UPN poszczególnych użytkowników w ramach normalnych operacji. Zalecamy przeprowadzenie przetestowanej procedury, która zawiera dokumentację o znanych problemach i obejść.

W poniższych sekcjach szczegółowo opisano potencjalne znane problemy i obejścia, gdy nazwy UPN zostały zmienione.

## <a name="apps-known-issues-and-workarounds"></a>Znane problemy i rozwiązania dotyczące aplikacji

[Oprogramowanie jako usługa (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) i aplikacje biznesowe (LOB) często korzystają z nazw UPN do znajdowania użytkowników i przechowywania informacji o profilach użytkowników, w tym ról. Aplikacje korzystające z usługi [just in Time](../app-provisioning/user-provisioning.md) do tworzenia profilu użytkownika podczas logowania się do aplikacji po raz pierwszy mogą mieć wpływ zmiany nazwy UPN.

**Znany problem**<br>
Zmiana nazwy UPN użytkownika może spowodować przerwanie relacji między użytkownikiem usługi Azure AD a profilem użytkownika utworzonym w aplikacji. Jeśli aplikacja używa  [Justing Time](../app-provisioning/user-provisioning.md), może utworzyć nowy profil użytkownika. Spowoduje to, że administrator aplikacji wprowadzi ręcznie zmiany w celu naprawienia tej relacji.

**Obejście**<br>
[Automatyczne Inicjowanie obsługi użytkowników w usłudze Azure AD](../app-provisioning/user-provisioning.md) umożliwia automatyczne tworzenie, konserwowanie i usuwanie tożsamości użytkowników w obsługiwanych aplikacjach w chmurze. Skonfigurowanie automatycznego aprowizacji użytkowników w aplikacjach automatycznie aktualizuje nazwy UPN w aplikacjach. Przetestuj aplikacje w ramach wdrożenia progresywnego, aby sprawdzić, czy nie wpływają na zmiany nazwy UPN.
Jeśli jesteś deweloperem, rozważ [dodanie obsługi Standard scim do aplikacji](../app-provisioning/use-scim-to-provision-users-and-groups.md) , aby umożliwić automatyczne Inicjowanie obsługi użytkowników z Azure Active Directory. 

## <a name="managed-devices-known-issues-and-workarounds"></a>Znane problemy i Obejścia dotyczące zarządzanych urządzeń

Przełączenie [urządzeń do usługi Azure AD](../devices/overview.md)pozwala zmaksymalizować produktywność użytkowników dzięki użyciu logowania jednokrotnego (SSO) w chmurze i zasobach lokalnych.

### <a name="azure-ad-joined-devices"></a>Urządzenia dołączone do usługi Azure AD

Urządzenia [przyłączone do usługi Azure AD](../devices/concept-azure-ad-join.md) są połączone bezpośrednio z usługą Azure AD i umożliwiają użytkownikom logowanie się na urządzeniu przy użyciu tożsamości swojej organizacji.

**Znane problemy** <br>
Użytkownicy mogą napotkać problemy z logowaniem jednokrotnym w aplikacjach, które są zależne od usługi Azure AD na potrzeby uwierzytelniania.

**Rozwiązanie** <br>
Problemy wymienione w tej sekcji zostały rozwiązane w aktualizacji Windows 10 maja 2020 (2004).

**Obejście** <br>
Poczekaj na zmianę nazwy UPN na synchronizację z usługą Azure AD. Po zweryfikowaniu, że nowa nazwa UPN jest odzwierciedlona w portalu usługi Azure AD, poproszenie użytkownika o wybranie kafelka "inny użytkownik", aby zalogować się przy użyciu nowej nazwy UPN. Możesz również sprawdzić za poorednictwem [programu PowerShell](/powershell/module/azuread/get-azureaduser). Po zalogowaniu się przy użyciu nowej nazwy UPN odwołania do starej nazwy UPN mogą nadal pojawić się w ustawieniu systemu Windows "dostęp do pracy lub nauki".

![Zrzut ekranu zweryfikowanych domen](./media/howto-troubleshoot-upn-changes/other-user.png)


### <a name="hybrid-azure-ad-joined-devices"></a>Urządzenia dołączone hybrydowo do usługi Azure AD

[Hybrydowe urządzenia dołączone do usługi Azure AD](../devices/concept-azure-ad-join-hybrid.md) są przyłączone do Active Directory i usługi Azure AD. Możesz zaimplementować hybrydowe dołączanie do usługi Azure AD, jeśli środowisko ma Active Directory, a także chcesz skorzystać z możliwości zapewnianych przez usługę Azure AD.

**Znane problemy** 

Hybrydowe urządzenia dołączone do usługi Azure AD systemu Windows 10 mogą wystąpić nieoczekiwane ponowne uruchomienia i problemy z dostępem.

Jeśli użytkownicy logują się do systemu Windows przed synchronizacją nowej nazwy UPN do usługi Azure AD lub kontynuują korzystanie z istniejącej sesji systemu Windows, mogą wystąpić problemy z logowaniem jednokrotnym w aplikacjach korzystających z usługi Azure AD do uwierzytelniania, jeśli dostęp warunkowy został skonfigurowany w celu wymuszenia użycia hybrydowych urządzeń przyłączonych do dostępu do zasobów. 

Ponadto zostanie wyświetlony następujący komunikat z wymuszeniem ponownego uruchomienia po upływie minuty. 

"Komputer zostanie automatycznie uruchomiony ponownie w ciągu minuty. System Windows napotkał problem i wymaga ponownego uruchomienia. Należy zamknąć ten komunikat teraz i zapisać swoją służbę ".

**Rozwiązanie** <br>
Problemy wymienione w tej sekcji zostały rozwiązane w aktualizacji Windows 10 maja 2020 (2004).

**Obejście** 

Urządzenie musi zostać odłączone od usługi Azure AD i uruchomione ponownie. Po ponownym uruchomieniu urządzenie automatycznie przywróci do usługi Azure AD, a użytkownik musi zalogować się przy użyciu nowej nazwy UPN, wybierając kafelek "inny użytkownik". Aby odłączyć urządzenie od usługi Azure AD, uruchom następujące polecenie w wierszu polecenia:

**dsregcmd /leave**

Jeśli jest używany, użytkownik będzie musiał [ponownie zarejestrować](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision) się w usłudze Windows Hello dla firm. Ten problem nie występuje w przypadku urządzeń z systemami Windows 7 i 8,1 po zmianie nazwy UPN.


## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Microsoft Authenticator znanych problemów i obejść

Organizacja może wymagać korzystania z [aplikacji Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) , aby zalogować się i uzyskać dostęp do aplikacji i danych organizacji. Mimo że nazwa użytkownika może pojawić się w aplikacji, konto nie jest skonfigurowane do działania jako metoda weryfikacji, dopóki użytkownik nie ukończy procesu rejestracji.

[Aplikacja Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) ma cztery główne funkcje:

* Uwierzytelnianie wieloskładnikowe za pośrednictwem powiadomienia wypychanego lub kodu weryfikacyjnego

* Działaj jako Broker uwierzytelniania na urządzeniach z systemem iOS i Android w celu zapewnienia logowania jednokrotnego dla aplikacji korzystających z uwierzytelniania obsługiwanego przez [brokera](../develop/msal-android-single-sign-on.md)

* Rejestracja urządzeń (znana również jako Workplace Join) w usłudze Azure AD, która jest wymagana do innych funkcji, takich jak Intune App Protection i rejestracji urządzeń/zarządzania,

* Logowanie przy użyciu telefonu, które wymaga uwierzytelniania wieloskładnikowego i urządzenia.

### <a name="multi-factor-authentication-with-android-devices"></a>Multi-Factor Authentication z urządzeniami z systemem Android

Aplikacja Microsoft Authenticator oferuje opcję weryfikacji poza pasmem. Zamiast przekazywać automatyczne połączenie telefoniczne lub wiadomość SMS do użytkownika podczas logowania, [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) wypycha powiadomienie do aplikacji Microsoft Authenticator na smartfonie lub tablecie użytkownika. Użytkownik po prostu naciśnie przycisk Zatwierdź (lub wprowadzi kod PIN lub biometryczne) w aplikacji, aby zakończyć logowanie.

**Znane problemy** 

Po zmianie nazwy UPN użytkownika stara nazwa UPN nadal jest wyświetlana na koncie użytkownika, a powiadomienie może nie zostać odebrane. [Kody weryfikacyjne](../user-help/user-help-auth-app-faq.md) nadal działają.

**Obejście**

Jeśli zostanie odebrane powiadomienie, Poinstruuj użytkownika, aby odrzucić powiadomienie, Otwórz aplikację Authenticator, wybierz opcję "Wyszukaj powiadomienia" i zatwierdź wiersz polecenia usługi MFA. Po wykonaniu tej tej nazwy UPN wyświetlana na koncie zostaną zaktualizowane. Zwróć uwagę, że zaktualizowana nazwa UPN może być wyświetlana jako nowe konto, jest to spowodowane innymi używanymi funkcjami uwierzytelniania. Aby uzyskać więcej informacji, zobacz dodatkowe znane problemy w tym artykule.

### <a name="brokered-authentication"></a>Uwierzytelnianie obsługiwane przez brokera

W przypadku brokerów systemów Android i iOS, takich jak Microsoft Authenticator enable:

* Logowanie jednokrotne (SSO) — użytkownicy nie będą musieli zalogować się do poszczególnych aplikacji.

* Identyfikacja urządzenia — Broker uzyskuje dostęp do certyfikatu urządzenia utworzonego na urządzeniu, gdy było ono dołączone do miejsca pracy.

* Weryfikacja identyfikacji aplikacji — gdy aplikacja wywołuje brokera, przekazuje jego adres URL przekierowania, a Broker weryfikuje je.

Ponadto umożliwia aplikacjom uczestnictwo w bardziej zaawansowanych funkcjach, takich jak [dostęp warunkowy](../conditional-access/index.yml), i obsługuje [scenariusze Microsoft Intune](../develop/msal-net-use-brokers-with-xamarin-apps.md).

**Znane problemy**<br>
Użytkownik jest prezentowany z bardziej interaktywnymi instrukcjami uwierzytelniania dla nowych aplikacji, które korzystają z logowania za pomocą brokera z powodu niezgodności między login_hint przekazaną przez aplikację a nazwą UPN przechowywaną w brokerze.

**Obejście** <br> Użytkownik musi ręcznie usunąć konto z Microsoft Authenticator i rozpocząć nowe logowanie z aplikacji z obsługą brokera. Konto zostanie automatycznie dodane po uwierzytelnieniu początkowym.

### <a name="device-registration"></a>Rejestracja urządzenia

Aplikacja Microsoft Authenticator jest odpowiedzialna za zarejestrowanie urządzenia w usłudze Azure AD. Rejestracja urządzeń pozwala na uwierzytelnianie urządzenia w usłudze Azure AD i jest wymagane w następujących scenariuszach:

* Ochrona aplikacji usługi Intune

* Rejestracja urządzeń w usłudze Intune

* Logowanie przy użyciu telefonu

**Znane problemy**<br>
Po zmianie nazwy UPN nowe konto z nową nazwą UPN zostanie wyświetlone na liście w aplikacji Microsoft Authenticator, a konto z starą nazwą UPN jest nadal wymienione. Ponadto stara nazwa UPN jest wyświetlana w sekcji Rejestracja urządzenia w ustawieniach aplikacji. Nie wprowadzono zmian w normalnych funkcjach rejestracji urządzeń ani scenariuszy zależnych.

**Obejście** <br> Aby usunąć wszystkie odwołania do starej nazwy UPN w aplikacji Microsoft Authenticator, Poinstruuj użytkownika, aby ręcznie usunął zarówno stare, jak i nowe konta z Microsoft Authenticator, ponownie zarejestrować się na potrzeby uwierzytelniania wieloskładnikowego i przyłączyć urządzenie.

### <a name="phone-sign-in"></a>Logowanie przy użyciu telefonu

Logowanie za pomocą telefonu umożliwia użytkownikom logowanie się do usługi Azure AD bez hasła. Aby włączyć logowanie za pomocą telefonu, użytkownik musi zarejestrować się na potrzeby uwierzytelniania wieloskładnikowego przy użyciu aplikacji uwierzytelniającej, a następnie włączyć logowanie telefoniczne bezpośrednio na wystawcy uwierzytelnienia. W ramach konfiguracji urządzenie jest rejestrowane w usłudze Azure AD.

**Znane problemy** <br>
Użytkownicy nie mogą korzystać z logowania za pomocą telefonu, ponieważ nie odbierają żadnych powiadomień. Jeśli użytkownik naciśnie kontrolę nad powiadomieniami, wystąpi błąd.

**Obejście**<br>
Użytkownik musi wybrać menu rozwijane na koncie włączonym do logowania za pomocą telefonu i wybrać opcję Wyłącz logowanie za pomocą telefonu. W razie potrzeby można ponownie włączyć logowanie przy użyciu telefonu.

## <a name="security-key-fido2-known-issues-and-workarounds"></a>Klucz zabezpieczeń (FIDO2) — znane problemy i obejścia

**Znane problemy** <br>
Gdy wielu użytkowników jest zarejestrowanych w tym samym kluczu, na ekranie logowania zostanie wyświetlona strona wyboru konta, na której jest wyświetlana stara nazwa UPN. Zmiany nazwy UPN nie wpływają na logowania przy użyciu kluczy zabezpieczeń.  

**Obejście**<br>
Aby usunąć odwołania do starych nazw UPN, użytkownicy muszą [zresetować klucz zabezpieczeń i ponownie zarejestrować](../authentication/howto-authentication-passwordless-security-key.md#known-issues).

## <a name="onedrive-known-issues-and-workarounds"></a>Znane problemy i Obejścia dotyczące usługi OneDrive

Użytkownicy usługi OneDrive mogą napotkać problemy po zmianie nazwy UPN. Aby uzyskać więcej informacji, zobacz [jak zmiany nazw UPN wpływają na adres URL usługi OneDrive i funkcje usługi OneDrive](/onedrive/upn-changes).

## <a name="next-steps"></a>Następne kroki

Zobacz następujące zasoby:
* [Azure AD Connect: koncepcje projektowania](./plan-connect-design-concepts.md)

* [Wypełnianie wartości UserPrincipalName w usłudze Azure AD](./plan-connect-userprincipalname.md)

* [Tokeny identyfikatora platformy tożsamości firmy Microsoft](../develop/id-tokens.md)
