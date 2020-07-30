---
title: Logowanie jednokrotne w języku SAML — aplikacje bez galerii — platforma tożsamości firmy Microsoft | Microsoft Docs
description: Skonfiguruj Logowanie jednokrotne do aplikacji innych niż Galeria na platformie tożsamości firmy Microsoft (Azure AD)
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 06/08/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3cee2b9a0ea32a3b331849263c8a97f55930542d
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87379650"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>Konfigurowanie logowania jednokrotnego opartego na protokole SAML w aplikacjach spoza galerii

Po [dodaniu aplikacji galerii](add-gallery-app.md) lub [aplikacji sieci Web bez galerii](add-non-gallery-app.md) do aplikacji usługi Azure AD Enterprise jedna z opcji logowania jednokrotnego jest dostępna przy użyciu [protokołu SAML](what-is-single-sign-on.md#saml-sso). Wybierz pozycję SAML wszędzie tam, gdzie to możliwe, dla aplikacji, które uwierzytelniają się przy użyciu jednego z protokołów SAML. Za pomocą logowania jednokrotnego SAML usługa Azure AD uwierzytelnia się w aplikacji przy użyciu konta usługi Azure AD użytkownika. Usługa Azure AD komunikuje informacje logowania do aplikacji za pomocą protokołu połączenia. Użytkowników można mapować na określone role aplikacji na podstawie reguł zdefiniowanych w oświadczeniach SAML. W tym artykule opisano sposób konfigurowania logowania jednokrotnego opartego na protokole SAML dla aplikacji spoza galerii. 

> [!NOTE]
> Dodajesz aplikację z galerii? Instrukcje dotyczące instalacji krok po kroku znajdują się na [liście samouczków aplikacji SaaS](../saas-apps/tutorial-list.md)

Aby skonfigurować Logowanie jednokrotne SAML dla aplikacji spoza galerii bez pisania kodu, musisz mieć subskrypcję usługi Azure AD, a aplikacja musi obsługiwać protokół SAML 2,0. Aby uzyskać więcej informacji na temat wersji usługi Azure AD, zobacz [Cennik usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Jeśli aplikacja nie została dodana do dzierżawy usługi Azure AD, zobacz [Dodawanie aplikacji spoza galerii](add-non-gallery-app.md).

## <a name="step-1-edit-the-basic-saml-configuration"></a>Krok 1. Edytuj podstawową konfigurację języka SAML

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator aplikacji w chmurze lub administrator aplikacji dla dzierżawy usługi Azure AD.

2. Przejdź do **Azure Active Directory**  >  **aplikacji przedsiębiorstwa** i wybierz aplikację z listy. 
   
   - Aby wyszukać aplikację, w menu **Typ aplikacji** wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Zastosuj**. Wprowadź nazwę aplikacji w polu wyszukiwania, a następnie wybierz aplikację z wyników.

3. W sekcji **Zarządzanie** wybierz pozycję **Logowanie jednokrotne**. 

   - Należy zauważyć, że istnieje kilka scenariuszy, w których opcja **logowania** jednokrotnego nie będzie obecna. Na przykład jeśli aplikacja została zarejestrowana przy użyciu **rejestracje aplikacji** , funkcja logowania jednokrotnego jest domyślnie skonfigurowana do używania OIDC OAuth. W takim przypadku opcja **logowania** jednokrotnego nie będzie widoczna w obszarze nawigacji w obszarze **aplikacje dla przedsiębiorstw**. W przypadku dodawania niestandardowej aplikacji przy użyciu **rejestracje aplikacji** można skonfigurować opcje w pliku manifestu. Aby dowiedzieć się więcej na temat pliku manifestu, zobacz https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) . Aby dowiedzieć się więcej na temat standardów rejestracji jednokrotnej, zobacz https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform) . Inne scenariusze, w których nie będzie można korzystać z **logowania** jednokrotnego w nawigacji, obejmują, gdy aplikacja jest hostowana w innej dzierżawie lub że Twoje konto nie ma wymaganych uprawnień (Administrator globalny, administrator aplikacji w chmurze, administrator aplikacji lub właściciel jednostki usługi). Uprawnienia mogą również prowadzić do scenariusza, w którym można otworzyć **Logowanie jednokrotne** , ale nie będzie można go zapisać. Aby dowiedzieć się więcej na temat ról administracyjnych usługi Azure AD, zobacz https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .

4. Wybierz pozycję **SAML**. Zostanie wyświetlona strona **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML-Preview** .

   ![Krok 1 Edytuj podstawową konfigurację języka SAML](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. Aby edytować podstawowe opcje konfiguracji SAML, wybierz ikonę **edycji** (ołówek) w prawym górnym rogu sekcji **Podstawowa konfiguracja SAML** .

1. Wprowadź następujące ustawienia. Należy uzyskać wartości od dostawcy aplikacji. Można ręcznie wprowadzić wartości lub przekazać plik metadanych w celu wyodrębnienia wartości pól.

    | Podstawowe ustawienia konfiguracji SAML | Zainicjowane przez dostawcę usługi | Zainicjowane przez dostawcę tożsamości | Opis |
    |:--|:--|:--|:--|
    | **Identyfikator (identyfikator jednostki)** | Wymagane w przypadku niektórych aplikacji | Wymagane w przypadku niektórych aplikacji | Jednoznacznie identyfikuje aplikację. Usługa Azure AD wysyła identyfikator do aplikacji jako parametr odbiorców tokenu SAML. Aplikacja powinna go zweryfikować. Ta wartość jest widoczna również jako identyfikator jednostki w dowolnych metadanych SAML udostępnianych przez aplikację. Wprowadź adres URL, który używa następującego wzorca: "https:// <subdomain> . contoso.com" *można znaleźć tę wartość jako element **Issuer** w **AuthnRequest** (żądanie SAML) wysyłanej przez aplikację.* |
    | **Adres URL odpowiedzi** | Wymagane | Wymagane | Określa miejsce, w którym aplikacja oczekuje otrzymać token języka SAML. Adres URL odpowiedzi jest również nazywany adresem URL usługi Assertion Consumer Service (ACS). Aby określić wiele adresów URL odpowiedzi, można użyć pól dodatkowych adresów URL odpowiedzi. Na przykład mogą być potrzebne dodatkowe adresy URL odpowiedzi dla wielu poddomen. Lub w celach testowych można jednocześnie określić wiele adresów URL odpowiedzi (lokalnego hosta i publicznych adresów URL). |
    | **Adres URL logowania** | Wymagane | Nie podawaj | Gdy użytkownik otwiera ten adres URL, dostawca usługi przekierowuje go do usługi Azure AD w celu uwierzytelnienia i zalogowania. Usługa Azure AD używa adresu URL do uruchomienia aplikacji z usługi Office 365 lub panelu dostępu usługi Azure AD. Gdy to pole jest puste, usługa Azure AD wykonuje logowanie zainicjowane przez dostawcy tożsamości, gdy użytkownik uruchomi aplikację z pakietu Office 365, panelu dostępu usługi Azure AD lub adresu URL rejestracji jednokrotnej usługi Azure AD.|
    | **Stan przekaźnika** | Opcjonalne | Opcjonalne | Określa aplikacji, dokąd przekierować użytkownika po zakończeniu uwierzytelniania. Zazwyczaj wartość jest prawidłowym adresem URL dla aplikacji. Jednak niektóre aplikacje używają tego pola inaczej. Aby uzyskać więcej informacji, skontaktuj się z dostawcą aplikacji.
    | **Adres URL wylogowywania** | Opcjonalne | Opcjonalne | Służy do wysyłania odpowiedzi na wylogowanie SAML z powrotem do aplikacji.

Aby uzyskać więcej informacji, zobacz Logowanie jednokrotne [protokołu SAML](../develop/single-sign-on-saml-protocol.md).

## <a name="step-2-configure-user-attributes-and-claims"></a>Krok 2. Konfigurowanie atrybutów użytkownika i oświadczeń 

Gdy użytkownik uwierzytelnia się w aplikacji, usługa Azure AD wystawia aplikację tokenem SAML z informacjami (lub oświadczeniami) dotyczącymi użytkownika, który jednoznacznie identyfikuje je. Domyślnie te informacje obejmują nazwę użytkownika, adres e-mail, imię i nazwisko. Może być konieczne dostosowanie tych oświadczeń, jeśli na przykład aplikacja wymaga określonych wartości oświadczenia lub formatu **nazwy** innej niż nazwa użytkownika. Wymagania dotyczące aplikacji galerii są opisane w [samouczkach specyficznych dla aplikacji](../saas-apps/tutorial-list.md). można też polecić dostawcę aplikacji. Poniżej przedstawiono ogólne kroki konfigurowania atrybutów użytkownika i oświadczeń.

1. W sekcji **atrybuty użytkownika i oświadczenia** wybierz ikonę **Edytuj** (ołówek) w prawym górnym rogu.

   ![Krok 2 Konfigurowanie atrybutów użytkownika i oświadczeń](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. Sprawdź **wartość identyfikatora nazwy**. Wartość domyślna to *User. PrincipalName*. Identyfikator użytkownika jednoznacznie identyfikuje każdego użytkownika w aplikacji. Jeśli na przykład adresem e-mail jest zarówno nazwa użytkownika, jak i unikatowy identyfikator, ustaw wartość *user.mail*.

3. Aby zmodyfikować **wartość identyfikatora nazwy**, wybierz ikonę **edycji** (ołówek) dla pola **wartość identyfikatora nazwy** . Wprowadź odpowiednie zmiany w formacie i źródle identyfikatora, zgodnie z potrzebami. Aby uzyskać szczegółowe informacje, zobacz [Edytowanie NameID](../develop/active-directory-saml-claims-customization.md#editing-nameid). Zapisz zmiany po zakończeniu. 
 
4. Aby skonfigurować oświadczenia grupy, wybierz ikonę **edycji** dla **grup zwracanych w polu oświadczenia** . Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie oświadczeń grupy](../hybrid/how-to-connect-fed-group-claims.md).

5. Aby dodać zastrzeżenie, wybierz pozycję **Dodaj nowe zastrzeżenie** w górnej części strony. Wprowadź **nazwę** i wybierz odpowiednie źródło. W przypadku wybrania źródła **atrybutów** należy wybrać **atrybut źródłowy** , który ma być używany. W przypadku wybrania źródła **tłumaczenia** należy wybrać **transformację** i **parametr 1** , które mają być używane. Aby uzyskać szczegółowe informacje, zobacz [Dodawanie oświadczeń specyficznych dla aplikacji](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims). Zapisz zmiany po zakończeniu. 

6. Wybierz pozycję **Zapisz**. Nowe zgłoszenie pojawi się w tabeli.

   > [!NOTE]
   > Dodatkowe sposoby dostosowywania tokenu SAML z usługi Azure AD do aplikacji można znaleźć w następujących zasobach.
   >- Aby utworzyć role niestandardowe za pośrednictwem Azure Portal, zobacz [Konfigurowanie oświadczeń ról](../develop/active-directory-enterprise-app-role-management.md).
   >- Aby dostosować oświadczenia za pomocą programu PowerShell, zobacz [Dostosowywanie oświadczeń — PowerShell](../develop/active-directory-claims-mapping.md).
   >- Aby zmodyfikować manifest aplikacji w celu skonfigurowania opcjonalnych oświadczeń dla aplikacji, zobacz [Konfigurowanie opcjonalnych oświadczeń](../develop/active-directory-optional-claims.md).
   >- Aby ustawić zasady okresu istnienia tokenu dla tokenów odświeżania, tokenów dostępu, tokenów sesji i tokenów identyfikatorów, zobacz [Konfigurowanie okresów istnienia tokenu](../develop/active-directory-configurable-token-lifetimes.md). Aby ograniczyć sesje uwierzytelniania za pośrednictwem dostępu warunkowego usługi Azure AD, zobacz [możliwości zarządzania sesjami uwierzytelniania](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>Krok 3. Zarządzanie certyfikatem podpisywania SAML

Usługa Azure AD używa certyfikatu do podpisywania tokenów SAML wysyłanych do aplikacji. Ten certyfikat jest wymagany do skonfigurowania relacji zaufania między usługą Azure AD a aplikacją. Aby uzyskać szczegółowe informacje na temat formatu certyfikatu, zobacz dokumentację SAML aplikacji. Aby uzyskać więcej informacji, zobacz [Zarządzanie certyfikatami dla federacyjnego logowania](manage-certificates-for-federated-single-sign-on.md) jednokrotnego i [Zaawansowane opcje podpisywania certyfikatu w tokenie SAML](certificate-signing-options.md).

W usłudze Azure AD można pobrać aktywny certyfikat w formacie base64 lub RAW bezpośrednio z głównego konfigurowania logowania jednokrotnego **za pomocą strony SAML** . Alternatywnie można uzyskać aktywny certyfikat przez pobranie pliku XML metadanych aplikacji lub użycie adresu URL metadanych federacji aplikacji. Aby wyświetlić, utworzyć lub pobrać certyfikaty (aktywne lub nieaktywne), wykonaj następujące kroki.

1. Przejdź do sekcji **certyfikat podpisywania SAML** . 

   ![Krok 3 Zarządzanie certyfikatem podpisywania SAML](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. Sprawdź, czy certyfikat ma:

   - *Żądana Data wygaśnięcia.* Można skonfigurować datę wygaśnięcia na maksymalnie trzy lata w przyszłości.
   - *Stan aktywny dla żądanego certyfikatu.* Jeśli stan jest **nieaktywny**, Zmień stan na **aktywny**. Aby zmienić stan, kliknij prawym przyciskiem myszy wiersz żądanego certyfikatu i wybierz pozycję **Ustaw certyfikat jako aktywny**.
   - *Poprawna opcja podpisywania i algorytm.*
   - *Poprawne adresy e-mail powiadomień.* Gdy aktywny certyfikat zbliża się do daty wygaśnięcia, usługa Azure AD wyśle powiadomienie na adres e-mail skonfigurowany w tym polu.

2. Aby pobrać certyfikat, wybierz jedną z opcji format Base64, format nieprzetworzony lub XML metadanych Federacji. Usługa Azure AD udostępnia również **adres URL metadanych federacji aplikacji** , w którym można uzyskać dostęp do metadanych specyficznych dla aplikacji w formacie `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` .

3. Aby zarządzać, utworzyć lub zaimportować certyfikat, wybierz ikonę **edycji** (ołówek) w prawym górnym rogu sekcji **certyfikat podpisywania SAML** .

   ![Certyfikat podpisywania SAML](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   Wykonaj jedną z następujących czynności:

   - Aby utworzyć nowy certyfikat, wybierz pozycję **nowy certyfikat**, wybierz **datę wygaśnięcia**, a następnie wybierz pozycję **Zapisz**. Aby uaktywnić certyfikat, wybierz menu kontekstowe (**...**) i wybierz pozycję **Ustaw certyfikat jako aktywny**.
   - Aby przekazać certyfikat z kluczem prywatnym i poświadczeniami PFX, wybierz pozycję **Importuj certyfikat** i przejdź do certyfikatu. Wprowadź **hasło PFX**, a następnie wybierz pozycję **Dodaj**.  
   - Aby skonfigurować zaawansowane opcje podpisywania certyfikatu, należy użyć następujących opcji. Opisy tych opcji można znaleźć w artykule [Zaawansowane opcje podpisywania certyfikatu](certificate-signing-options.md) .
      - Z listy rozwijanej **Opcja podpisywania** wybierz pozycję **Podpisz odpowiedź SAML**, **Podpisz potwierdzenie SAML**lub **Podpisz odpowiedź i potwierdzenie protokołu SAML**.
      - Z listy rozwijanej **algorytm podpisywania** wybierz pozycję **SHA-1** lub **SHA-256**.
   - Aby powiadomić dodatkowe osoby, gdy aktywny certyfikat zbliża się do daty wygaśnięcia, wprowadź adresy e-mail w polach **powiadomienia e-mail adresy** .

4. Jeśli wprowadzono zmiany, wybierz pozycję **Zapisz** w górnej części sekcji **certyfikat podpisywania SAML** . 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Krok 4. Konfigurowanie aplikacji do korzystania z usługi Azure AD

Sekcja **Konfigurowanie \<applicationName> ** zawiera listę wartości, które należy skonfigurować w aplikacji, aby używały usługi Azure AD jako dostawcy tożsamości SAML. Wymagane wartości różnią się w zależności od aplikacji. Aby uzyskać szczegółowe informacje, zobacz dokumentację języka SAML aplikacji. Aby znaleźć dokumentację, przejdź do nagłówka **konfiguracji \<application name> ** i wybierz pozycję **Wyświetl instrukcje krok po kroku**. Dokumentacja zostanie wyświetlona na stronie **Konfigurowanie logowania** . Ta strona przeprowadzi Cię przez użytkownika w celu ** \<application name> ** wypełniania wartości **adresu URL logowania**, **identyfikatora usługi Azure AD**i **adresów URL wylogowywania** w nagłówku konfigurowania.

1. Przewiń w dół do sekcji **Konfiguracja \<applicationName> ** . 
   
   ![Krok 4 — Konfigurowanie aplikacji](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. Skopiuj wartość z każdego wiersza w tej sekcji zgodnie z wymaganiami i postępuj zgodnie z instrukcjami dla aplikacji dotyczącymi dodawania wartości do aplikacji. W przypadku aplikacji galerii można wyświetlić dokumentację, wybierając opcję **Wyświetl instrukcje krok po kroku**. 
   - Wartości **adresu URL logowania** i **wylogowania** są rozpoznawane w tym samym punkcie końcowym, który jest punktem końcowym obsługi żądania SAML dla wystąpienia usługi Azure AD. 
   - **Identyfikator usługi Azure AD** jest wartością **wystawcy** w tokenie SAML wystawionym dla aplikacji.
2. Po wklejeniu wszystkich wartości do odpowiednich pól wybierz pozycję **Zapisz**.

## <a name="step-5-validate-single-sign-on"></a>Krok 5. Weryfikuj Logowanie jednokrotne

Po skonfigurowaniu aplikacji do korzystania z usługi Azure AD jako dostawcy tożsamości opartej na protokole SAML można przetestować ustawienia, aby sprawdzić, czy logowanie jednokrotne działa dla Twojego konta. 

2. Przewiń do sekcji **Weryfikowanie logowania <applicationName> jednokrotnego** .

   ![Krok 5. Sprawdzanie poprawności logowania jednokrotnego](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. Wybierz przycisk **Weryfikuj**. Zostaną wyświetlone opcje testowania.

4. Wybierz opcję **Zaloguj się jako bieżący użytkownik**. 

Jeśli logowanie powiedzie się, możesz przydzielić użytkowników i grupy do aplikacji SAML.
Jeśli zostanie wyświetlony komunikat o błędzie, wykonaj następujące czynności:

1. Skopiuj szczegóły i wklej je w polu **Jak wygląda błąd?**.

    ![Uzyskiwanie wskazówek dotyczących rozwiązywania](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Wybierz pozycję **Uzyskaj wskazówki dotyczące rozpoznawania**. Zostaną wyświetlone główna przyczyna i wskazówki dotyczące rozwiązywania.  W tym przykładzie użytkownik nie został przypisany do aplikacji.

3. Zapoznaj się ze wskazówkami dotyczącymi rozdzielczości, a następnie, jeśli to możliwe, usuń problem.

4. Uruchamiaj test ponownie, dopóki nie zostanie pomyślnie zakończony.

Aby uzyskać więcej informacji, zobacz Debugowanie logowania jednokrotnego [opartego na protokole SAML do aplikacji w Azure Active Directory](../azuread-dev/howto-v1-debug-saml-sso-issues.md).

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie użytkowników lub grup do aplikacji](methods-for-assigning-users-and-groups.md)
- [Konfigurowanie automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/configure-automatic-user-provisioning-portal.md)
