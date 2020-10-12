---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Akamai | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Akamai.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: c8978504244fbf68875ca8cff7a92f042e8e988b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329520"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Akamai

W tym samouczku dowiesz się, jak zintegrować usługę Akamai z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Akamai z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Akamai.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Akamai przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Integracja dostępu do aplikacji dla przedsiębiorstw Azure Active Directory i Akamai umożliwia bezproblemowe dostęp do starszych aplikacji hostowanych w chmurze lub lokalnie. Zintegrowane rozwiązanie korzysta ze wszystkich nowoczesnych możliwości Azure Active Directory takich jak [dostęp warunkowy usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal), [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) i [Azure AD Identity Governance](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview) w przypadku starszych aplikacji dostęp bez modyfikacji aplikacji ani agentów.

Na poniższym obrazie opisano, gdzie Akamai EAA mieści się w szerszym scenariuszu bezpiecznego dostępu hybrydowego

![Akamai EAA mieści się w szerszym scenariuszu bezpiecznego dostępu hybrydowego](./media/header-akamai-tutorial/introduction01.png)

### <a name="key-authentication-scenarios"></a>Scenariusze uwierzytelniania kluczy

Poza Azure Active Directory natywną integracją z obsługą nowoczesnych protokołów uwierzytelniania, takich jak Open ID Connect, SAML i WS-karmione, Akamai EAA rozszerza bezpieczny dostęp do aplikacji uwierzytelniania opartych na starszych wersjach dla dostępu wewnętrznego i zewnętrznego z usługą Azure AD, umożliwiając nowoczesne scenariusze (na przykład dostęp bez hasła) do tych aplikacji. Możliwości obejmują:

* Aplikacje uwierzytelniania oparte na nagłówkach
* Pulpit zdalny
* SSH (Secure Shell)
* Aplikacje uwierzytelniania Kerberos
* VNC (Virtual Network Computing)
* Uwierzytelnianie anonimowe lub brak nieskompilowanych aplikacji uwierzytelniania
* Aplikacje uwierzytelniania NTLM (ochrona przy użyciu podwójnych wskazówek dla użytkownika)
* Aplikacja Forms-Based (ochrona przy użyciu podwójnych wskazówek dla użytkownika)

### <a name="integration-scenarios"></a>Scenariusze integracji

Partnerstwo firmy Microsoft i Akamai EAA pozwala elastycznie spełnić wymagania biznesowe dzięki obsłudze wielu scenariuszy integracji na podstawie wymagań firmy. Mogą one służyć do zapewnienia pokrycia zero dni dla wszystkich aplikacji i stopniowego klasyfikowania i konfigurowania odpowiednich klasyfikacji zasad.

#### <a name="integration-scenario-1"></a>Scenariusz integracji 1

Akamai EAA jest skonfigurowany jako pojedyncza aplikacja w usłudze Azure AD. Administrator może skonfigurować zasady urzędu certyfikacji w aplikacji, a gdy warunki są spełnione, użytkownicy będą mogli uzyskać dostęp do portalu Akamai EAA.

**Specjaliści**:

* Należy tylko skonfigurować dostawcy tożsamości raz

**Wady**:

* Użytkownicy kończący dwa portale aplikacji

* Pojedyncze typowe pokrycie zasad urzędu certyfikacji dla wszystkich aplikacji.

![Scenariusz integracji 1](./media/header-akamai-tutorial/scenario1.png)

#### <a name="integration-scenario-2"></a>Scenariusz integracji 2

Aplikacja Akamai EAA jest skonfigurowana indywidualnie w portalu usługi Azure AD. Administrator może skonfigurować zasady dotyczące poszczególnych urzędów certyfikacji w aplikacjach, a gdy warunki są spełnione, użytkownicy będą mogli bezpośrednio przekierowywać do określonej aplikacji.

**Specjaliści**:

* Można definiować poszczególne zasady urzędu certyfikacji

* Wszystkie aplikacje są reprezentowane w panelu 0365 Gofr i myApps.microsoft.com.


**Wady**:

* Należy skonfigurować wiele dostawcy tożsamości.

![Scenariusz integracji 2](./media/header-akamai-tutorial/scenario2.png)

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Akamai.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

- Usługa Akamai obsługuje dostawcy tożsamości zainicjowane przez logowanie jednokrotne

#### <a name="important"></a>Ważne

Wszystkie wymienione poniżej ustawienia są takie same dla **scenariusza integracji 1** i **scenariusza 2**. W **scenariuszu integracji 2** trzeba skonfigurować indywidualne dostawcy tożsamości w Akamai EAA i Właściwość adresu URL musi zostać zmodyfikowana, aby WSKAZYWAŁA adres URL aplikacji.

![Zrzut ekranu przedstawiający kartę Ogólne dla AZURESSO-SP w Akamai dostęp do aplikacji w przedsiębiorstwie. Pole adresu URL konfiguracji uwierzytelniania jest wyróżnione.](./media/header-akamai-tutorial/important.png)

## <a name="adding-akamai-from-the-gallery"></a>Dodawanie Akamai z galerii

Aby skonfigurować integrację programu Akamai z usługą Azure AD, musisz dodać Akamai z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Akamai** w polu wyszukiwania.
1. Wybierz pozycję **Akamai** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-akamai"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Akamai

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Akamai przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Akamai.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Akamai, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-akamai-sso)** w usłudze Akamai, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Konfigurowanie dostawcy tożsamości](#setting-up-idp)**
    * **[Uwierzytelnianie na podstawie nagłówka](#header-based-authentication)**
    * **[Pulpit zdalny](#remote-desktop)**
    * **[Protokół SSH](#ssh)**
    * **[Uwierzytelnianie Kerberos](#kerberos-authentication)**
    * **[Utwórz użytkownika testowego Akamai](#create-akamai-test-user)** , aby dysponować odpowiednikiem B. Simon w Akamai, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Akamai** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<Yourapp>.login.go.akamai-access.com/saml/sp/response`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https:// <Yourapp>.login.go.akamai-access.com/saml/sp/response`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta Akamai](https://www.akamai.com/us/en/contact-us/) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie Akamai** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Akamai.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Akamai**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-akamai-sso"></a>Konfigurowanie logowania jednokrotnego Akamai

### <a name="setting-up-idp"></a>Konfigurowanie dostawcy tożsamości

**Konfiguracja AKAMAI EAA dostawcy tożsamości**

1. Zaloguj się do **Akamai konsoli dostępu aplikacji przedsiębiorstwa** .
1. W **konsoli Akamai EAA**wybierz pozycję **Identity**  >  **dostawcy tożsamości** tożsamości i kliknij pozycję **Dodaj dostawcę tożsamości**.

    ![Zrzut ekranu przedstawiający okno dostawców tożsamości konsoli Akamai EAA. Wybierz pozycję dostawcy tożsamości w menu tożsamość, a następnie wybierz pozycję Dodaj dostawcę tożsamości.](./media/header-akamai-tutorial/configure01.png)

1. Na stronie **Tworzenie nowego dostawcy tożsamości** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający okno dialogowe Tworzenie nowych dostawców tożsamości w konsoli Akamai EAA.](./media/header-akamai-tutorial/configure02.png)

    a. Określ **unikatową nazwę**.

    b. Wybierz element **SAML innej firmy** , a następnie kliknij pozycję **Utwórz dostawcę tożsamości i skonfiguruj**.

### <a name="general-settings"></a>Ustawienia ogólne

1. **Przechwycenie tożsamości** — Podaj nazwę (podstawowy adres URL, który będzie używany na potrzeby konfiguracji usługi Azure AD)

    > [!NOTE]
    > Możesz wybrać własną domenę niestandardową (wymaga wpisu DNS i certyfikatu). W tym przykładzie będziemy używać domeny Akamai.

1. **Strefa w chmurze Akamai** — wybierz odpowiednią strefę w chmurze.
1. **Sprawdzanie poprawności certyfikatu** — Sprawdź dokumentację Akamai (opcjonalnie)

    ![Zrzut ekranu przedstawiający kartę Ogólne w konsoli usługi Akamai EAA z ustawieniami przechwycenia tożsamości, strefy chmury Akamai i weryfikacji certyfikatu.](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>Konfiguracja uwierzytelniania

1. URL — określ adres URL, który jest taki sam, jak w przypadku przechwycenia tożsamości (jest to miejsce, w którym użytkownicy są przekierowywani po uwierzytelnieniu)
2. Adres URL wylogowywania: zaktualizuj adres URL wylogowywania.
3. Podpisz żądanie SAML: domyślnie niezaznaczone.
4. W przypadku pliku metadanych dostawcy tożsamości Dodaj aplikację w konsoli usługi Azure AD.

    ![Zrzut ekranu przedstawiający konfigurację uwierzytelniania konsoli Akamai EAA z ustawieniami adresu URL, adres URL wylogowywania, podpisać żądanie SAML i plik metadanych dostawcy tożsamości.](./media/header-akamai-tutorial/configure04.png)

### <a name="session-settings"></a>Ustawienia sesji

Pozostaw ustawienia domyślne.

![Zrzut ekranu przedstawiający okno dialogowe ustawień sesji konsoli EAA Akamai.](./media/header-akamai-tutorial/sessionsettings.png)

### <a name="directories"></a>Katalogi

Pomiń konfigurację katalogu.

![Zrzut ekranu przedstawiający kartę katalogi w konsoli Akamai EAA.](./media/header-akamai-tutorial/directories.png)

### <a name="customization-ui"></a>Interfejs użytkownika dostosowywania

Można dodać dostosowanie do dostawcy tożsamości.

![Zrzut ekranu przedstawiający kartę dostosowywanie konsoli Akamai EAA z ustawieniami dostosowywania interfejsu użytkownika, ustawień języka i motywów.](./media/header-akamai-tutorial/customizationui.png)

### <a name="advanced-settings"></a>Ustawienia zaawansowane

Pomiń ustawienia zaawansowane/odwołaj się do dokumentacji Akamai, aby uzyskać więcej szczegółów.

![Zrzut ekranu karty Zaawansowane ustawienia konsoli Akamai EAA przedstawiający ustawienia dla klienta EAA, zaawansowane i OIDC do mostkowania SAML.](./media/header-akamai-tutorial/advancesettings.png)

### <a name="deployment"></a>Wdrożenie

1. Kliknij pozycję Wdróż dostawcę tożsamości.

    ![Zrzut ekranu przedstawiający kartę wdrożenie konsoli Akamai EAA z przyciskiem Wdróż dostawcę z wcięciem.](./media/header-akamai-tutorial/deployment.png)

2. Sprawdzenie, czy wdrożenie zakończyło się pomyślnie

### <a name="header-based-authentication"></a>Uwierzytelnianie na podstawie nagłówka

Uwierzytelnianie na podstawie nagłówka Akamai

1. Wybierz pozycję **niestandardowy protokół http** w Kreatorze dodawania aplikacji.

    ![Zrzut ekranu przedstawiający Kreatora dodawania aplikacji w konsoli Akamai EAA z CustomHTTP wymienionym w sekcji Aplikacje dostępu.](./media/header-akamai-tutorial/configure05.png)

2. Wprowadź **nazwę** i **Opis**aplikacji.

    ![Zrzut ekranu przedstawiający okno dialogowe niestandardowa aplikacja HTTP z ustawieniami nazwy i opisu aplikacji.](./media/header-akamai-tutorial/configure06.png)

    ![Zrzut ekranu przedstawiający kartę Ogólne konsoli usługi Akamai EAA z ustawieniami ogólnymi dla MYHEADERAPP.](./media/header-akamai-tutorial/configure07.png)

    ![Zrzut ekranu przedstawiający konsolę Akamai EAA z ustawieniami certyfikatu i lokalizacji.](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>Authentication

1. Wybierz kartę **uwierzytelnianie** .

    ![Zrzut ekranu przedstawiający konsolę Akamai EAA z wybraną kartą uwierzytelnianie.](./media/header-akamai-tutorial/configure09.png)

2. Przypisywanie **dostawcy tożsamości**

    ![Zrzut ekranu przedstawiający kartę Uwierzytelnianie konsoli usługi Akamai EAA dla MYHEADERAPP, w której dostawca tożsamości jest ustawiony na logowanie jednokrotne w usłudze Azure AD.](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>Usługi

Kliknij przycisk Zapisz i przejdź do uwierzytelniania.

![Zrzut ekranu przedstawiający kartę usługi konsoli Akamai EAA dla MYHEADERAPP z przyciskiem Zapisz i przejdź do AdvancedSettings w prawym dolnym rogu.](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>Ustawienia zaawansowane

1. W **nagłówkach HTTP klienta**Określ atrybut **CustomerHeader** i **SAML**.

    ![Zrzut ekranu przedstawiający kartę Ustawienia zaawansowane konsoli Akamai EAA z polem zarejestrowany adres URL logowania jednokrotnego w obszarze Uwierzytelnianie.](./media/header-akamai-tutorial/configure12.png)

1. Kliknij przycisk **Zapisz i przejdź do wdrożenia** .

    ![Zrzut ekranu przedstawiający kartę Ustawienia zaawansowane konsoli Akamai EAA z przyciskiem Zapisz i przejdź do wdrożenia w prawym dolnym rogu.](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>Wdrażanie aplikacji

1. Kliknij przycisk **Wdróż aplikację** .

    ![Zrzut ekranu przedstawiający kartę wdrożenie konsoli Akamai EAA z przyciskiem Wdróż aplikację.](./media/header-akamai-tutorial/configure14.png)

1. Sprawdź, czy aplikacja została pomyślnie wdrożona.

    ![Zrzut ekranu przedstawiający kartę wdrożenie konsoli Akamai EAA z komunikatem o stanie aplikacji: "aplikacja została pomyślnie wdrożona".](./media/header-akamai-tutorial/configure15.png)

1. End-User środowiska.

    ![Zrzut ekranu przedstawiający ekran otwierający dla myapps.microsoft.com z obrazem tła i oknem dialogowym logowania.](./media/header-akamai-tutorial/enduser01.png)

    ![Zrzut ekranu przedstawiający część okna aplikacji z ikonami dla dodatków, HRWEB, Akamai-CorpApps, wydatków, grup i przeglądów dostępu. ](./media/header-akamai-tutorial/enduser02.png)

1. Dostęp warunkowy.

    ![Zrzut ekranu przedstawiający komunikat: Zatwierdź żądanie logowania. Wysłaliśmy powiadomienie na urządzenie przenośne. Proszę odpowiedzieć, aby kontynuować.](./media/header-akamai-tutorial/conditionalaccess01.png)

    ![Zrzut ekranu przedstawiający ekran aplikacji z ikoną dla MyHeaderApp.](./media/header-akamai-tutorial/conditionalaccess02.png)

#### <a name="remote-desktop"></a>Pulpit zdalny

1. Wybierz pozycję **RDP** w Kreatorze dodawania aplikacji.

    ![Zrzut ekranu przedstawiający Kreatora dodawania aplikacji w konsoli Akamai EAA z informacjami o protokole RDP wymienionym wśród aplikacji w sekcji Aplikacje dostępu.](./media/header-akamai-tutorial/configure16.png)

1. Wprowadź **nazwę** i **Opis**aplikacji.

    ![Zrzut ekranu przedstawiający okno dialogowe aplikacji RDP pokazujące ustawienia nazwy i opisu aplikacji.](./media/header-akamai-tutorial/configure17.png)

    ![Zrzut ekranu przedstawiający kartę Ogólne konsoli usługi Akamai EAA z ustawieniami tożsamości aplikacji dla SECRETRDPAPP.](./media/header-akamai-tutorial/configure18.png)

1. Określ łącznik, który będzie obsługiwać ten program.

    ![Zrzut ekranu przedstawiający konsolę Akamai EAA z ustawieniami certyfikatu i lokalizacji. Skojarzone łączniki są ustawione na USWST-CON1.](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>Authentication

Kliknij przycisk **Zapisz i przejdź do usługi**.

![Zrzut ekranu przedstawiający kartę Uwierzytelnianie w konsoli usługi Akamai EAA dla SECRETRDPAPP, na którym znajduje się przycisk Zapisz i przejdź do usług, znajduje się w prawym dolnym rogu.](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>Usługi

Kliknij przycisk **Zapisz i przejdź do pozycji Ustawienia zaawansowane**.

![Zrzut ekranu przedstawiający kartę usługi konsoli Akamai EAA dla SECRETRDPAPP z przyciskiem Zapisz i przejdź do AdvancedSettings w prawym dolnym rogu.](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>Ustawienia zaawansowane

1. Kliknij przycisk **Zapisz i przejdź do wdrożenia**.

    ![Zrzut ekranu przedstawiający kartę Ustawienia zaawansowane konsoli Akamai EAA dla SECRETRDPAPP z ustawieniami konfiguracji pulpitu zdalnego.](./media/header-akamai-tutorial/configure22.png)

    ![Zrzut ekranu przedstawiający kartę Ustawienia zaawansowane konsoli Akamai EAA dla SECRETRDPAPP z ustawieniami konfiguracji uwierzytelniania i kontroli kondycji.](./media/header-akamai-tutorial/configure23.png)

    ![Zrzut ekranu konsoli usługi Akamai EAA niestandardowe ustawienia nagłówków HTTP dla SECRETRDPAPP z przyciskiem Zapisz i przejdź do wdrożenia w prawym dolnym rogu.](./media/header-akamai-tutorial/configure24.png)

1. Środowisko End-User

    ![Zrzut ekranu okna myapps.microsoft.com z obrazem tła i oknem dialogowym logowania.](./media/header-akamai-tutorial/enduser03.png)

    ![Zrzut ekranu przedstawiający okno aplikacje myapps.microsoft.com z ikonami dodatków, HRWEB, Akamai-CorpApps, wydatków, grup i przeglądów dostępu.](./media/header-akamai-tutorial/enduser02.png)

1. Dostęp warunkowy

    ![Zrzut ekranu komunikatu o dostępie warunkowym: Zatwierdź żądanie logowania. Wysłaliśmy powiadomienie na urządzenie przenośne. Proszę odpowiedzieć, aby kontynuować.](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Zrzut ekranu przedstawiający ekran aplikacji z ikonami dla MyHeaderApp i SecretRDPApp.](./media/header-akamai-tutorial/conditionalaccess05.png)

    ![Zrzut ekranu przedstawiający ekran z systemem Windows Server 2012 RS z niezwykłymi ikonami użytkownika. Ikony dla administratorów, user0 i Użytkownik1 pokazują, że są zalogowani.](./media/header-akamai-tutorial/conditionalaccess06.png)

1. Alternatywnie można również bezpośrednio wpisać adres URL aplikacji RDP.

#### <a name="ssh"></a>Protokół SSH

1. Przejdź do pozycji Dodaj aplikacje, wybierz pozycję **SSH**.

    ![Zrzut ekranu przedstawiający Kreatora dodawania aplikacji w konsoli Akamai EAA z informacją o użyciu protokołu SSH na liście aplikacji w sekcji Aplikacje dostępu.](./media/header-akamai-tutorial/configure25.png)

1. Wprowadź **nazwę** i **Opis**aplikacji.

    ![Zrzut ekranu przedstawiający okno dialogowe aplikacji SSH z ustawieniami nazwy i opisu aplikacji.](./media/header-akamai-tutorial/configure26.png)

1. Skonfiguruj tożsamość aplikacji.

    ![Zrzut ekranu przedstawiający kartę Ogólne konsoli usługi Akamai EAA z ustawieniami tożsamości aplikacji dla protokołu SSH-SECURE.](./media/header-akamai-tutorial/configure27.png)

    a. Określ nazwę/opis.

    b. Określ adres IP/nazwę FQDN serwera aplikacji i port dla protokołu SSH.

    c. Określ nazwę użytkownika/hasło SSH * Sprawdź Akamai EAA.

    d. Określ nazwę hosta zewnętrznego.

    e. Określ lokalizację łącznika i wybierz łącznik.

#### <a name="authentication"></a>Authentication

Kliknij przycisk **Zapisz i przejdź do usług**.

![Zrzut ekranu przedstawiający kartę Uwierzytelnianie w konsoli usługi Akamai EAA dla protokołu SSH — zabezpieczenia za pomocą przycisku Zapisz i przejdź do usług znajduje się w prawym dolnym rogu.](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>Usługi

Kliknij przycisk **Zapisz i przejdź do pozycji Ustawienia zaawansowane**.

![Zrzut ekranu przedstawiający kartę usługi konsoli Akamai EAA dla protokołu SSH — zabezpieczenia za pomocą przycisku Zapisz i przejdź do AdvancedSettings w prawym dolnym rogu.](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>Ustawienia zaawansowane

Kliknij pozycję Zapisz i przejdź do wdrożenia

![Zrzut ekranu przedstawiający kartę Ustawienia zaawansowane konsoli usługi Akamai EAA dla opcji SECURE SSH (ustawienia uwierzytelniania i kontroli kondycji).](./media/header-akamai-tutorial/configure30.png)

![Zrzut ekranu konsoli usługi Akamai EAA niestandardowe ustawienia nagłówków HTTP dla SSH-SECURE przy użyciu przycisku Zapisz i przejdź do wdrożenia w prawym dolnym rogu.](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>Wdrożenie

1. Kliknij pozycję **Wdróż aplikację**.

    ![Zrzut ekranu przedstawiający kartę wdrożenie konsoli usługi Akamai EAA dla protokołu SSH — zabezpieczenia z pokazywaniem przycisku Wdróż aplikację.](./media/header-akamai-tutorial/configure32.png)

1. Środowisko End-User

    ![Zrzut ekranu okna dialogowego logowania do okna myapps.microsoft.com.](./media/header-akamai-tutorial/enduser03.png)

    ![Zrzut ekranu przedstawiający okno aplikacje dla myapps.microsoft.com, w którym są wyświetlane ikony dodatków, HRWEB, Akamai-CorpApps, wydatków, grup i przeglądów dostępu.](./media/header-akamai-tutorial/enduser04.png)

1. Dostęp warunkowy

    ![Zrzut ekranu przedstawiający komunikat: Zatwierdź żądanie logowania. Wysłaliśmy powiadomienie na urządzenie przenośne. Proszę odpowiedzieć, aby kontynuować.](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Zrzut ekranu przedstawiający ekran aplikacji z ikonami dla MyHeaderApp, SSH Secure i SecretRDPApp.](./media/header-akamai-tutorial/conditionalaccess07.png)

    ![Zrzut ekranu okna polecenia dla ssh-secure-go.akamai-access.com, w którym jest wyświetlany monit o hasło.](./media/header-akamai-tutorial/conditionalaccess08.png)

    ![Zrzut ekranu przedstawiający okno poleceń dla programu ssh-secure-go.akamai-access.com z informacjami o aplikacji i wyświetlaniu monitu o polecenia.](./media/header-akamai-tutorial/conditionalaccess09.png)

### <a name="kerberos-authentication"></a>Uwierzytelnianie Kerberos

W poniższym przykładzie opublikujemy wewnętrzny serwer sieci Web <code>http://frp-app1.superdemo.live</code> i włączysz logowanie jednokrotne za pomocą KCD

#### <a name="general-tab"></a>Karta Ogólne

![Zrzut ekranu przedstawiający kartę Ogólne konsoli usługi Akamai EAA dla MYKERBOROSAPP.](./media/header-akamai-tutorial/generaltab.png)

#### <a name="authentication-tab"></a>Karta Uwierzytelnianie

Przypisywanie dostawcy tożsamości

![Zrzut ekranu przedstawiający kartę Uwierzytelnianie konsoli Akamai EAA dla MYKERBOROSAPP, w której jest wyświetlany dostawca tożsamości ustawiony na logowanie jednokrotne usługi Azure AD.](./media/header-akamai-tutorial/authenticationtab.png)

#### <a name="services-tab"></a>Karta usługi

![Zrzut ekranu przedstawiający kartę usługi konsoli Akamai EAA dla MYKERBOROSAPP.](./media/header-akamai-tutorial/servicestab.png)

#### <a name="advanced-settings"></a>Ustawienia zaawansowane

![Zrzut ekranu przedstawiający kartę Ustawienia zaawansowane konsoli Akamai EAA dla MYKERBOROSAPP, w którym są wyświetlane ustawienia powiązanych aplikacji i uwierzytelniania.](./media/header-akamai-tutorial/advancesettings02.png)

> [!NOTE]
> Nazwa SPN serwera sieci Web ma SPN@Domain Format ex: `HTTP/frp-app1.superdemo.live@SUPERDEMO.LIVE` dla tego pokazu. Pozostaw pozostałe ustawienia domyślne.

#### <a name="deployment-tab"></a>Karta wdrożenie

![Zrzut ekranu przedstawiający kartę wdrożenie konsoli Akamai EAA dla MYKERBOROSAPP z przyciskiem Wdróż aplikację.](./media/header-akamai-tutorial/deploymenttab.png)

#### <a name="adding-directory"></a>Dodawanie katalogu

1. Wybierz pozycję **AD** z listy rozwijanej.

    ![Zrzut ekranu przedstawiający okno katalogów konsoli Akamai EAA z okna dialogowego Utwórz nowy katalog z wybraną pozycją usługi AD w polu listy rozwijanej dla typu katalogu.](./media/header-akamai-tutorial/configure33.png)

1. Podaj niezbędne dane.

    ![Zrzut ekranu przedstawiający okno SUPERDEMOLIVE konsoli Akamai EAA z ustawieniami katalogu DirectoryName, usługi katalogowej, łącznika i mapowania atrybutów.](./media/header-akamai-tutorial/configure34.png)

1. Sprawdź Tworzenie katalogów.

    ![Zrzut ekranu przedstawiający okno katalogów konsoli usługi Akamai EAA z informacją o tym, że został dodany niewidoczny na żywo.](./media/header-akamai-tutorial/directorydomain.png)

1. Dodaj grupy/jednostki organizacyjne, które będą wymagały dostępu.

    ![Zrzut ekranu przedstawiający ustawienia dla predemonstracyjnego katalogu. Live. Zostanie wyróżniona ikona wybrana do dodania grup lub jednostek organizacyjnych.](./media/header-akamai-tutorial/addgroup.png)

1. W poniższej tabeli Grupa nosi nazwę EAAGroup i ma 1 element członkowski.

    ![Zrzut ekranu przedstawiający grupy konsoli Akamai EAA w oknie katalogu SUPERDEMOLIVE. EAAGroup z 1 użytkownikiem znajduje się na liście grup.](./media/header-akamai-tutorial/eaagroup.png)

1. Dodaj katalog do dostawcy tożsamości **, klikając pozycje tożsamość**  >  **dostawcy** i kliknij kartę **katalogi** , a następnie kliknij pozycję **Przypisz katalog**.

    ![Zrzut ekranu przedstawiający kartę katalogi usługi Akamai EAA na potrzeby rejestracji jednokrotnej w usłudze Azure AD, która zawiera listę aktualnie przypisanych katalogów.](./media/header-akamai-tutorial/assigndirectory.png)

### <a name="configure-kcd-delegation-for-eaa-walkthrough"></a>Skonfiguruj delegowanie KCD dla przewodnika EAA

#### <a name="step-1-create-an-account"></a>Krok 1. Tworzenie konta 

1. W przykładzie zostanie użyte konto o nazwie **EAADelegation**. Można to wykonać przy użyciu przyciągania **użytkowników i komputerów Active Directory** .

    ![Zrzut ekranu przedstawiający kartę katalogi usługi Akamai EAA dla logowania jednokrotnego w usłudze Azure AD. Predemonstracyjny katalog. na żywo znajduje się na liście aktualnie przypisanych katalogów.](./media/header-akamai-tutorial/assigndirectory.png)

    > [!NOTE]
    > Nazwa użytkownika musi znajdować się w określonym formacie na podstawie **nazwy przechwycenia tożsamości**. Na rysunku 1 zobaczymy, że jest **corpapps.Login.go.Akamai-Access.com**

1. Nazwa logowania użytkownika będzie:`HTTP/corpapps.login.go.akamai-access.com`

    ![Zrzut ekranu przedstawiający właściwości EAADelegation z nazwą "EAADelegation" i nazwą logowania użytkownika ustawioną na HTTP/corpapps. Login. Przejdź. Akamai-Access. com.](./media/header-akamai-tutorial/eaadelegation.png)

#### <a name="step-2-configure-the-spn-for-this-account"></a>Krok 2. Konfigurowanie nazwy SPN dla tego konta

1. Na podstawie tego przykładu Nazwa SPN będzie niższa.

2. SetSPN-s **http/corpapps. Login. Przejdź. Akamai-Access. com eaadelegation**

    ![Zrzut ekranu przedstawiający wiersz polecenia administratora z wynikami polecenia setspn-s http/corpapps. Login. go. Akamai-Access. com eaadelegation.](./media/header-akamai-tutorial/spn.png)

#### <a name="step-3-configure-delegation"></a>Krok 3. Konfigurowanie delegowania

1. W przypadku konta EAADelegation kliknij kartę Delegowanie.

    ![Zrzut ekranu przedstawiający wiersz polecenia administratora z poleceniem służącym do konfigurowania nazwy SPN.](./media/header-akamai-tutorial/spn.png)

    * Określ użycie dowolnego protokołu uwierzytelniania
    * Kliknij przycisk Dodaj i Dodaj konto puli aplikacji dla witryny sieci Web Kerberos. Jeśli poprawnie skonfigurowano, należy automatycznie rozwiązać ten problem.

#### <a name="step-4-create-a-keytab-file-for-akamai-eaa"></a>Krok 4. Tworzenie pliku plik KEYTAB dla AKAMAI EAA

1. Oto Składnia ogólna.

1. ktpass/out ActiveDirectorydomain. plik KEYTAB/Princ `HTTP/yourloginportalurl@ADDomain.com`  /mapuser serviceaccount@ADdomain.com /Pass + RdnPass/Crypto All/pType KRB5_NT_PRINCIPAL

1. Przykład został wyjaśniony

    | Fragment kodu | Objaśnienie |
    | - | - |
    | Ktpass/out EAADemo. plik KEYTAB | Nazwa pliku wyjściowego plik KEYTAB |
    | /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live | // HTTP/yourIDPName@YourdomainName |
    | /mapuser eaadelegation@superdemo.live | Konto delegowania EAA |
    | /pass RANDOMPASS | Hasło konta delegowania EAA |
    | /Crypto wszystkie pType KRB5_NT_PRINCIPAL | Zapoznaj się z dokumentacją Akamai EAA |
    | | |

1. Ktpass/out EAADemo. plik KEYTAB/Princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live /mapuser/Pass RANDOMPASS eaadelegation@superdemo.live /Crypto All pType KRB5_NT_PRINCIPAL

    ![Zrzut ekranu przedstawiający wiersz polecenia administratora z wynikami polecenia tworzenia pliku plik KEYTAB dla AKAMAI EAA.](./media/header-akamai-tutorial/administrator.png)

#### <a name="step-5-import-keytab-in-the-akamai-eaa-console"></a>Krok 5. Importowanie plik KEYTAB w konsoli AKAMAI EAA

1. Kliknij **System**pozycję  >  **karty**systemowe.

    ![Zrzut ekranu przedstawiający konsolę Akamai EAA z kartami, które są wybierane z menu systemowego.](./media/header-akamai-tutorial/keytabs.png)

1. W polu Typ plik KEYTAB wybierz opcję **delegowanie protokołu Kerberos**.

    ![Zrzut ekranu przedstawiający ekran EAAKEYTAB konsoli Akamai EAA z ustawieniami plik KEYTAB. Typ plik KEYTAB jest ustawiony na delegowanie protokołu Kerberos.](./media/header-akamai-tutorial/keytabdelegation.png)

1. Upewnij się, że plik KEYTAB jest wyświetlany jako wdrożony i zweryfikowany.

    ![Zrzut ekranu przedstawiający ekran karty okna konsoli Akamai EAA z listą EAA plik KEYTAB jako "plik KEYTAB wdrożone i zweryfikowane".](./media/header-akamai-tutorial/keytabs02.png)

1. Doświadczenie użytkownika

    ![Zrzut ekranu okna dialogowego logowania w witrynie myapps.microsoft.com. ](./media/header-akamai-tutorial/enduser03.png)

    ![Zrzut ekranu przedstawiający okno aplikacje dla myapps.microsoft.com, w którym są wyświetlane ikony aplikacji.](./media/header-akamai-tutorial/enduser04.png)

1. Dostęp warunkowy

    ![Zrzut ekranu przedstawiający komunikat żądania Zatwierdź logowanie. komunikat.](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Zrzut ekranu przedstawiający ekran aplikacji z ikonami dla MyHeaderApp, SSH Secure, SecretRDPApp i myKerberosApp.](./media/header-akamai-tutorial/conditionalaccess10.png)

    ![Zrzut ekranu przedstawiający ekran powitalny dla myKerberosApp. Komunikat "Welcome superdemo\user1" jest wyświetlany na obrazie tła.](./media/header-akamai-tutorial/conditionalaccess11.png)

### <a name="create-akamai-test-user"></a>Utwórz użytkownika testowego Akamai

W tej sekcji utworzysz użytkownika o nazwie B. Simon w Akamai. Współpracuj z [zespołem obsługi klienta Akamai](https://www.akamai.com/us/en/contact-us/) , aby dodać użytkowników z platformy Akamai. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego. 

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Akamai w panelu dostępu należy automatycznie zalogować się do Akamai, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Akamai z usługą Azure AD](https://aad.portal.azure.com/)
