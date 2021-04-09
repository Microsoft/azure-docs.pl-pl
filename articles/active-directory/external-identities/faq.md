---
title: Często zadawane pytania dotyczące współpracy B2B — Azure Active Directory | Microsoft Docs
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące Azure Active Directory współpracy B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3308c2263f80a0772a389900e08c81cfe8da32a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952630"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Azure Active Directory często zadawane pytania dotyczące współpracy B2B

Te często zadawane pytania dotyczące współpracy między firmami (AD-i Business-to-biznesowe) w ramach programu Azure Active Directory (Azure Active Directory) są okresowo aktualizowane w celu uwzględnienia nowych tematów.

> [!IMPORTANT]
> - **Od 4 stycznia 2021** firma Google jest [przestarzałą obsługą logowania do usługi WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). W przypadku korzystania z usługi Google Federation lub samoobsługowego rejestrowania się w usłudze Gmail należy [przetestować natywne aplikacje biznesowe pod kątem zgodności](google-federation.md#deprecation-of-webview-sign-in-support).
> - **Od października 2021** firma Microsoft nie będzie już obsługiwać wykupu zaproszeń przez tworzenie niezarządzanych kont usługi Azure AD i dzierżawców na potrzeby scenariuszy współpracy B2B. W przygotowaniu Zachęcamy klientów do samodzielnego [uwierzytelniania kodu dostępu za pośrednictwem poczty e-mail](one-time-passcode.md). Prosimy o opinię na temat tej publicznej funkcji w wersji zapoznawczej i przyjemnością, aby utworzyć jeszcze więcej sposobów współpracy.

### <a name="can-we-customize-our-sign-in-page-so-its-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Czy mogę dostosować naszą stronę logowania, aby była bardziej intuicyjna dla naszych użytkowników-Gości współpracy B2B?
Jak najbardziej! Zapoznaj się z naszym [wpisem w blogu dotyczącym tej funkcji](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Aby uzyskać więcej informacji na temat sposobu dostosowywania strony logowania w organizacji, zobacz [Dodawanie znakowania firmowego do stron logowania i panelu dostępu](../fundamentals/customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Czy użytkownicy współpracy B2B mogą uzyskiwać dostęp do usług SharePoint Online i OneDrive?
Tak. Jednak możliwość wyszukiwania istniejących użytkowników-Gości w usłudze SharePoint Online przy użyciu selektora osób jest domyślnie **wyłączona** . Aby włączyć opcję wyszukiwania istniejących użytkowników-Gości, ustaw wartość **ShowPeoplePickerSuggestionsForGuestUsers** na **włączone**. To ustawienie można włączyć na poziomie dzierżawy lub na poziomie zbioru witryn. To ustawienie można zmienić za pomocą poleceń cmdlet Set-SPOTenant i Set-SPOSite. Za pomocą tych poleceń cmdlet członkowie mogą przeszukiwać wszystkich istniejących użytkowników-Gości w katalogu. Zmiany w zakresie dzierżawy nie wpływają na witryny usługi SharePoint Online, które zostały już zainicjowane.

### <a name="is-the-csv-upload-feature-still-supported"></a>Czy funkcja przekazywania woluminów CSV jest nadal obsługiwana?
Tak. Aby uzyskać więcej informacji na temat korzystania z funkcji przekazywania plików CSV, zobacz [ten przykład programu PowerShell](code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Jak mogę dostosować moje wiadomości e-mail z zaproszeniem?
Niemal wszystko o procesie zapraszania można dostosować za pomocą [interfejsów API zaproszenia B2B](customize-invitation-api.md).

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Czy użytkownicy-Goście mogą zresetować swoją metodę uwierzytelniania wieloskładnikowego?
Tak. Użytkownicy-Goście mogą resetować swoje metody uwierzytelniania wieloskładnikowego w taki sam sposób, jak w przypadku zwykłych użytkowników.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Która organizacja jest odpowiedzialna za licencje usługi uwierzytelniania wieloskładnikowego?
Zapraszana organizacja wykonuje uwierzytelnianie wieloskładnikowe. Organizacja zapraszania musi upewnić się, że organizacja ma wystarczającą liczbę licencji dla użytkowników B2B korzystających z uwierzytelniania wieloskładnikowego.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Co zrobić, jeśli w organizacji partnerskiej jest już skonfigurowane uwierzytelnianie wieloskładnikowe? Czy można zaufać usłudze uwierzytelniania wieloskładnikowego i nie używać własnego uwierzytelniania wieloskładnikowego?
Ta funkcja nie jest obecnie obsługiwana. Jeśli dostęp do zasobów organizacji wymaga uwierzytelniania wieloskładnikowego, organizacja partnerska będzie potrzebować rejestracji w usłudze uwierzytelniania wieloskładnikowego w organizacji (zapraszanie).

### <a name="how-can-i-use-delayed-invitations"></a>Jak mogę używać opóźnionych zaproszeń?
Organizacja może chcieć dodać użytkowników współpracy B2B, udostępnić aplikacje w razie potrzeby, a następnie wysłać zaproszenia. Możesz użyć interfejsu API zaproszenia do współpracy B2B, aby dostosować przepływ pracy do dołączania.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Czy można sprawić, aby użytkownicy-Goście widoczni na globalnej liście adresów programu Exchange?
Tak. Obiekty gościa nie są domyślnie widoczne na globalnej liście adresowej organizacji, ale można użyć programu Azure Active Directory PowerShell, aby je wyświetlić. Zobacz [, czy obiekty gościa są widoczne na globalnej liście adresów?](/office365/admin/create-groups/manage-guest-access-in-groups#add-guests-to-the-global-address-list)

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Czy mogę utworzyć użytkownika-gościa z ograniczonym administratorem?
Oczywiście. Aby uzyskać więcej informacji, zobacz [Dodawanie użytkowników-Gości do roli](add-guest-to-role.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Czy współpraca B2B w usłudze Azure AD umożliwia użytkownikom B2B dostęp do Azure Portal?
Jeśli użytkownik nie ma przypisanej roli administratora z ograniczonym dostępem, użytkownicy współpracy B2B nie będą potrzebowali dostępu do Azure Portal. Jednak użytkownicy współpracy B2B, którzy mają przypisaną rolę ograniczonego administratora, mogą uzyskać dostęp do portalu. Ponadto, jeśli użytkownik-Gość, który nie ma przypisanej jednej z tych ról administratora, uzyskuje dostęp do portalu, może uzyskać dostęp do niektórych części tego środowiska. Rola użytkownika-Gość ma pewne uprawnienia w katalogu.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Czy mogę zablokować dostęp do Azure Portal dla użytkowników-Gości?

Tak! Można utworzyć zasady dostępu warunkowego, które blokują dostęp do Azure Portal wszystkim Gościom i użytkownikom zewnętrznym. Podczas konfigurowania tych zasad należy zachować ostrożność, aby zapobiec przypadkowemu zablokowaniu dostępu do członków i administratorów.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) jako administrator zabezpieczeń lub administrator dostępu warunkowego.
2. W witrynie Azure Portal wybierz pozycję **Azure Active Directory**. 
3. W obszarze **Zarządzaj** wybierz pozycję **zabezpieczenia**.
4. W obszarze **Ochrona** wybierz pozycję **dostęp warunkowy**. Wybierz pozycję **nowe zasady**.
5. Na **nowej** stronie, w polu tekstowym **Nazwa** wprowadź nazwę zasad (na przykład "Blokuj Gościom dostęp do portalu").
6. W obszarze **Przypisania** wybierz pozycję **Użytkownicy i grupy**.
7. Na karcie **Dołącz** wybierz **pozycję Użytkownicy i grupy**, a następnie wybierz pozycję **Wszyscy użytkownicy i Goście (wersja zapoznawcza)**.
9. Kliknij **Gotowe**.
10. Na stronie **Nowy** w sekcji **przypisania** wybierz pozycję **aplikacje w chmurze lub akcje**.
11. Na stronie **aplikacje lub akcje w chmurze** wybierz **pozycję Wybierz aplikacje**, a następnie wybierz **pozycję Wybierz**.
12. Na stronie **Wybieranie** wybierz pozycję **Microsoft Azure Management**, a następnie wybierz pozycję **Wybierz**.
13. Na stronie **aplikacje lub akcje w chmurze** wybierz pozycję **gotowe**.

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Czy współpraca B2B w usłudze Azure AD obsługuje uwierzytelnianie wieloskładnikowe i konta konsumentów poczty e-mail?
Tak. Usługa Azure AD B2B obsługuje funkcje uwierzytelniania wieloskładnikowego i konsumenckiej poczty e-mail.

### <a name="do-you-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Czy jest obsługiwane resetowanie haseł dla użytkowników współpracy B2B usługi Azure AD?
Jeśli Twoja dzierżawa usługi Azure AD jest katalogiem macierzystym dla użytkownika, możesz [zresetować hasło użytkownika](../fundamentals/active-directory-users-reset-password-azure-portal.md) z Azure Portal. Ale nie można bezpośrednio zresetować hasła dla użytkownika-gościa, który loguje się przy użyciu konta, które jest zarządzane przez innego katalogu usługi Azure AD lub zewnętrzny dostawca tożsamości. Hasło może zresetować tylko użytkownik-Gość lub administrator w katalogu macierzystym użytkownika. Poniżej przedstawiono kilka przykładów działania resetowania haseł dla użytkowników-Gości:
 
* Użytkownicy-Goście w dzierżawie usługi Azure AD, którzy są oznaczeni jako "Gość" (UserType = gość), nie mogą zarejestrować się w celu SSPR za pomocą [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . Ten typ gościa może wykonywać tylko SSPR przez [https://aka.ms/sspr](https://aka.ms/sspr) . 
* Użytkownicy-Goście, którzy logują się za pomocą konto Microsoft (na przykład guestuser@live.com ) mogą zresetować swoje hasła przy użyciu funkcji konto Microsoft samoobsługowego resetowania hasła (SSPR). Zobacz [jak zresetować hasło konto Microsoft](https://support.microsoft.com/help/4026971/microsoft-account-how-to-reset-your-password).
* Użytkownicy-Goście, którzy logują się za pomocą konta Google lub innego dostawcy tożsamości zewnętrznych, mogą resetować swoje hasła przy użyciu metody SSPR dostawcy tożsamości. Na przykład użytkownik-Gość z kontem Google guestuser@gmail.com może zresetować swoje hasło, postępując zgodnie z instrukcjami w temacie [zmiana lub Resetowanie hasła](https://support.google.com/accounts/answer/41078).
* Jeśli dzierżawa tożsamości jest dzierżawą just-in-Time (JIT) lub "wirusowym" (co oznacza, że jest to oddzielna, niezarządzana dzierżawa platformy Azure), tylko użytkownik-Gość może zresetować swoje hasło. Czasami organizacja zajmie się [zarządzaniem wirusami](../enterprise-users/domains-admin-takeover.md) , które są tworzone, gdy pracownicy używają służbowych adresów e-mail do rejestracji w usłudze. Gdy organizacja zajmie się wirusową dzierżawą, tylko administrator w tej organizacji może zresetować hasło użytkownika lub włączyć SSPR. Jeśli to konieczne, jako zapraszana organizacja możesz usunąć konto użytkownika-gościa z katalogu i ponownie wysłać zaproszenie.

* Jeśli katalog macierzysty użytkownika gościa jest dzierżawą usługi Azure AD, możesz zresetować hasło użytkownika. Można na przykład utworzyć użytkownika lub zsynchronizować użytkownika z lokalnej Active Directory i ustawić dla nich wartość UserType na gość. Ponieważ ten użytkownik znajduje się w katalogu, możesz zresetować swoje hasło z Azure Portal.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>Czy firma Microsoft Dynamics 365 zapewnia pomoc techniczną online dla współpracy B2B usługi Azure AD?
Tak, Dynamics 365 (online) obsługuje funkcję współpracy B2B usługi Azure AD. Aby uzyskać więcej informacji, zobacz artykuł Dynamics 365 [Zapraszanie użytkowników za pomocą funkcji współpracy B2B usługi Azure AD](/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration).

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Jaki jest okres istnienia początkowego hasła dla nowo utworzonego użytkownika współpracy B2B?
Usługa Azure AD ma stały zestaw znaków, siłę hasła i wymagania dotyczące blokady konta, które są stosowane jednocześnie do wszystkich kont użytkowników w chmurze usługi Azure AD. Konta użytkowników w chmurze to konta, które nie są federacyjne przy użyciu innego dostawcy tożsamości, takie jak 
* Konto Microsoft
* Facebook
* Usługi Active Directory Federation Services
* Inna dzierżawa w chmurze (na potrzeby współpracy B2B)

W przypadku kont federacyjnych zasady haseł są zależne od zasad, które są stosowane w lokalnej dzierżawie i ustawieniach konto Microsoft użytkownika.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Organizacja może chcieć mieć różne środowiska w swoich aplikacjach dla użytkowników dzierżawy i użytkowników-Gości. Czy istnieją standardowe wskazówki dotyczące tego? Czy obecność dostawcy tożsamości może zatwierdzić właściwy model do użycia?
Użytkownik-gość może użyć dowolnego dostawcy tożsamości w celu uwierzytelnienia. Aby uzyskać więcej informacji, zobacz [właściwości użytkownika współpracy B2B](user-properties.md). Użyj właściwości **UserType** , aby określić środowisko użytkownika. W tokenie nie znajduje się obecnie oświadczenia **UserType** . Aplikacje powinny używać interfejsu API Microsoft Graph, aby wysłać zapytanie do katalogu dla użytkownika i uzyskać wartość UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Gdzie mogę znaleźć społeczność współpracy B2B do udostępniania rozwiązań i przesyłania pomysłów?
Nieustannie nawiązujemy nam nasłuchiwanie opinii, aby poprawić współpracę B2B. Podziel się scenariuszami użytkownika, najlepszymi rozwiązaniami i zapoznaj się z informacjami o współpracy B2B w usłudze Azure AD. Dołącz do dyskusji w [społeczności technicznej firmy Microsoft](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Zapraszamy również Cię do przesyłania swoich pomysłów i głosu dla przyszłych funkcji w [pomysłach współpracy B2B](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Czy można wysłać zaproszenie, które jest automatycznie zrealizowane, aby użytkownik miał po prostu "gotowe do użycia"? Lub czy użytkownik zawsze musi klikać w adresie URL wykupu?
Możesz zapraszać innych użytkowników w organizacji partnerskiej za pomocą interfejsu użytkownika, skryptów programu PowerShell lub interfejsów API. Następnie można wysłać użytkownikowi gość bezpośredni link do udostępnionej aplikacji. W większości przypadków nie trzeba już otwierać zaproszenia e-mail i klikać adres URL realizacji. Zobacz [Azure Active Directory realizacji zaproszeń do współpracy B2B](redemption-experience.md).

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Jak działa współpraca B2B, gdy zaproszony partner korzysta z Federacji, aby dodać własne uwierzytelnianie lokalne?
Jeśli partner ma dzierżawcę usługi Azure AD, który jest federacyjny dla lokalnej infrastruktury uwierzytelniania, zostanie automatycznie zrealizowane lokalne Logowanie jednokrotne. Jeśli partner nie ma dzierżawy usługi Azure AD, tworzone jest konto usługi Azure AD dla nowych użytkowników. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Uważam, że usługa Azure AD B2B nie akceptuje adresów e-mail gmail.com i outlook.com, a B2C została użyta dla tych rodzajów kont?
Usuwamy różnice między współpracy B2B i Business-to-Consumer (B2C) pod względem tego, które tożsamości są obsługiwane. Użyta tożsamość nie jest dobrym powodem do wyboru między korzystaniem z usługi B2B lub B2C. Aby uzyskać informacje na temat wybierania opcji współpracy, zobacz [porównanie współpracy B2B i B2C w Azure Active Directory](compare-with-b2c.md).

### <a name="can-an-azure-ad-b2c-local-account-be-invited-to-an-azure-ad-tenant-for-b2b-collaboration"></a>Czy Azure AD B2C konta lokalnego można zaprosić do dzierżawy usługi Azure AD na potrzeby współpracy B2B?
Nie. Konta lokalnego Azure AD B2C można używać tylko do logowania się do dzierżawy Azure AD B2C. Nie można użyć konta do logowania się do dzierżawy usługi Azure AD. Zapraszanie konta lokalnego Azure AD B2C do dzierżawy usługi Azure AD w celu współpracy B2B nie jest obsługiwane.

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Jakie aplikacje i usługi obsługują użytkowników systemu Azure B2B?
Wszystkie aplikacje zintegrowane z usługą Azure AD mogą obsługiwać użytkowników Gości systemu Azure B2B, ale muszą używać punktu końcowego skonfigurowanego jako dzierżawca do uwierzytelniania użytkowników-Gości. Może być również konieczne [dostosowanie oświadczeń](claims-mapping.md) w tokenie SAML, które są wydawane, gdy użytkownik-Gość uwierzytelnia się w aplikacji. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Czy firma Microsoft może wymusić uwierzytelnianie wieloskładnikowe dla użytkowników Gości B2B, jeśli partnerzy nie mają uwierzytelniania wieloskładnikowego?
Tak. Aby uzyskać więcej informacji, zobacz [dostęp warunkowy dla użytkowników współpracy B2B](conditional-access.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>W programie SharePoint można zdefiniować listę "Zezwalaj" lub "Odmów" dla użytkowników zewnętrznych. Czy możemy to zrobić na platformie Azure?
Tak. Współpraca B2B w usłudze Azure AD obsługuje listy dozwolonych list i Odmów. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Jakich licencji potrzebujemy do korzystania z usługi Azure AD B2B?
Aby uzyskać informacje o licencjach wymaganych przez organizację do korzystania z usługi Azure AD B2B, zobacz [Cennik zewnętrznych tożsamości](external-identities-pricing.md).

### <a name="next-steps"></a>Następne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)