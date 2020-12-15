---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą JIRA SAML SSO przez firmę Microsoft | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a programem JIRA SAML SSO by Microsoft.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.openlocfilehash: 621bbac6bc9ff3cb147110942060c8268fd86989
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511182"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jira-saml-sso-by-microsoft"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą JIRA SAML SSO przez firmę Microsoft

W tym samouczku dowiesz się, jak zintegrować usługę JIRA SAML SSO przez firmę Microsoft z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi JIRA SAML SSO przez firmę Microsoft z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do JIRA SAML SSO przez firmę Microsoft.
* Zezwól użytkownikom na automatyczne logowanie się, aby JIRA protokół SAML SSO przez firmę Microsoft przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="description"></a>Opis

Użyj konta usługi Microsoft Azure Active Directory w połączeniu z serwerem Atlassian JIRA, aby umożliwić logowanie jednokrotne. Dzięki temu wszyscy użytkownicy w organizacji mogą korzystać z poświadczeń usługi Azure AD w celu zalogowania się do aplikacji JIRA. Ta wtyczka używa protokołu SAML 2.0 na potrzeby federacji.

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z programem JIRA SAML SSO by Microsoft są potrzebne następujące elementy:

- Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
- JIRA rdzeń i oprogramowanie 6,4 do 8.14.0 lub JIRA Service Desk 3,0 do 4.11.1 powinno być zainstalowane i skonfigurowane w wersji systemu Windows 64-bit
- Dla serwera JIRA włączono obsługę protokołu HTTPS
- Zwróć uwagę na obsługiwane wersje wtyczki usługi JIRA podane w poniżej sekcji.
- Serwer JIRA jest dostępny w Internecie, szczególnie na stronie logowania usługi Azure AD na potrzeby uwierzytelniania i powinien być w stanie odebrać token z usługi Azure AD
- Poświadczenia administratora są skonfigurowane w usłudze JIRA
- Funkcja WebSudo jest wyłączona w usłudze JIRA
- Użytkownik testowy został utworzony w aplikacji serwera JIRA

> [!NOTE]
> Podczas testowania kroków w tym samouczku nie zalecamy używania środowiska produkcyjnego usługi JIRA. Integrację należy najpierw przetestować w środowisku programistycznym lub przejściowym aplikacji, a dopiero później użyć środowiska produkcyjnego.

Aby rozpocząć, potrzebne są następujące elementy:

* Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
* JIRA z logowaniem jednokrotnym SAML za pomocą subskrypcji usługi logowania jednokrotnego (SSO) firmy Microsoft.

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="supported-versions-of-jira"></a>Obsługiwane wersje usługi JIRA

* JIRA rdzeń i oprogramowanie: 6,4 do 8.14.0
* JIRA Service Desk 3.0.0 do 4.11.1
* Usługa JIRA obsługuje także wersję 5.2. Aby uzyskać więcej szczegółowych informacji, kliknij następujący link: [logowanie jednokrotne usługi Microsoft Azure Active Directory dla usługi JIRA 5.2](jira52microsoft-tutorial.md)

> [!NOTE]
> Należy pamiętać, że nasza wtyczka JIRA działa również na Ubuntu w wersji 16,04 i Linux.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program JIRA SAML SSO by Microsoft obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**.

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>Dodawanie programu JIRA SAML SSO by Microsoft z galerii

Aby skonfigurować integrację programu JIRA SAML SSO by Microsoft z usługą Azure AD, należy dodać program JIRA SAML SSO by Microsoft z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **JIRA SAML SSO przez firmę Microsoft** w polu wyszukiwania.
1. Wybierz pozycję **JIRA SAML SSO przez firmę Microsoft** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-jira-saml-sso-by-microsoft"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla JIRA SAML SSO przez firmę Microsoft

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą JIRA SAML SSO przez firmę Microsoft przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze JIRA SAML SSO przez firmę Microsoft.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą JIRA SAML SSO przez firmę Microsoft, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Konfigurowanie logowania JEDNOkrotnego SAML JIRA za pomocą logowania jednokrotnego przez firmę Microsoft](#configure-jira-saml-sso-by-microsoft-sso)** w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz JIRA SAML SSO przez użytkownika testowego firmy Microsoft](#create-jira-saml-sso-by-microsoft-test-user)** , aby dysponować odpowiednikiem B. Simon w JIRA SAML SSO przez firmę Microsoft, która jest połączona z reprezentacją usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **JIRA SAML SSO przez** integrację aplikacji firmy Microsoft Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<domain:port>/plugins/servlet/saml/auth`

    b. W polu **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<domain:port>/`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Port jest opcjonalny, jeśli adres URL zawiera nazwę. Te wartości można uzyskać podczas konfigurowania wtyczki usługi Jira, co jest objaśnione w dalszej części tego samouczka.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do JIRA SAML SSO przez firmę Microsoft.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **JIRA SAML SSO by Microsoft**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-jira-saml-sso-by-microsoft-sso"></a>Konfigurowanie logowania jednokrotnego SAML JIRA przez usługę Microsoft SSO

1. W innym oknie przeglądarki sieci Web Zaloguj się do swojego wystąpienia JIRA jako administrator.

2. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **Dodatki**.

    ![Zrzut ekranu przedstawia Dodatki wybrane z menu Ustawienia.](./media/jiramicrosoft-tutorial/addon1.png)

3. Pobierz wtyczkę z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=56506). Ręcznie przekaż wtyczkę udostępnioną przez firmę Microsoft za pomocą menu **Przekazywanie dodatku**. Pobieranie wtyczki jest objęte [umową serwisową firmy Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Zrzut ekranu przedstawia Zarządzanie dodatkami za pomocą linku Przekaż dodatek o nazwie.](./media/jiramicrosoft-tutorial/addon12.png)

4. Aby uruchomić scenariusz ze zwrotnym serwerem proxy lub modułem równoważenia obciążenia usługi JIRA, wykonaj następujące kroki:

    > [!NOTE]
    > Najpierw skonfiguruj serwer za pomocą poniższych instrukcji, a następnie zainstaluj wtyczkę.

    a. Dodaj poniższy atrybut w elemencie **Connector** port w pliku **server.xml** aplikacji serwera JIRA.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Zrzut ekranu przedstawia plik dot. x m l w edytorze z dodanym nowym wierszem.](./media/jiramicrosoft-tutorial/reverseproxy1.png)

    b. Zmień wartość w polu **Podstawowy adres URL** w obszarze **Ustawienia systemu** odpowiednio dla serwera proxy lub modułu równoważenia obciążenia.

    ![Zrzut ekranu przedstawia ustawienia administracyjne, w których można zmienić podstawową U R L.](./media/jiramicrosoft-tutorial/reverseproxy2.png)

5. Po zainstalowaniu wtyczki jest ona wyświetlana w sekcji dodatków **Zainstalowane przez użytkownika** w ramach sekcji **Zarządzanie dodatkami**. Kliknij pozycję **Configure** (Konfiguruj), aby skonfigurować nową wtyczkę.

    ![Zrzut ekranu przedstawia sekcję "Logowanie jednokrotne SAML usługi Azure A D" dla JIRA z wybraną pozycją Konfiguruj.](./media/jiramicrosoft-tutorial/addon14.png)

6. Wykonaj następujące kroki na stronie konfiguracji:

    ![Zrzut ekranu przedstawia stronę konfiguracji logowania jednokrotnego Microsoft Azure Active Directory dla JIRA.](./media/jiramicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Upewnij się, że tylko jeden certyfikat jest zamapowany do aplikacji, aby zapobiec błędom podczas rozpoznawania metadanych. Jeśli jest dostępnych wiele certyfikatów podczas rozpoznawania metadanych, administrator otrzyma błąd.

    1. W polu tekstowym **Adres URL metadanych** wklej wartość pola **Adres URL metadanych federacyjnych aplikacji** skopiowaną z witryny Azure Portal i kliknij przycisk **Rozpoznaj**. Spowoduje to odczytanie adresu URL metadanych dostawcy tożsamości i wypełnienie wszystkich pól danymi.

    1. Skopiuj wartości **identyfikatora, adresu URL odpowiedzi i adresu URL logowania**, a następnie wklej je odpowiednio w polach **Identyfikator, Adres URL odpowiedzi i Adres URL logowania** w sekcji **Domena i adresy URL programu JIRA SAML SSO by Microsoft** w witrynie Azure Portal.

    1. W polu **Nazwa przycisku logowania** wpisz nazwę przycisku, która według Twojej organizacji ma być wyświetlana użytkownikom na ekranie logowania.
    
    1. W polu **Opis przycisku Zaloguj** wpisz opis przycisku, który organizacja chce, aby użytkownicy widzieli na ekranie logowania.

    1. W obszarze **Identyfikator użytkownika SAML lokalizacje** wybierz opcję **Identyfikator użytkownika znajduje się w elemencie NameIdentifier instrukcji subject** lub **Identyfikator użytkownika znajduje się w elemencie atrybutu**.  Ten identyfikator musi być IDENTYFIKATORem użytkownika JIRA. Jeśli identyfikator użytkownika nie jest zgodny, system nie zezwoli użytkownikom na logowanie się.

       > [!Note]
       > Domyślna lokalizacja identyfikatora użytkownika SAML to element NameIdentifier. Można ją zmienić za pomocą opcji elementu Attribute, podając nazwę odpowiedniego atrybutu.

    1. Jeśli wybierzesz pozycję **Identyfikator użytkownika w opcji elementu atrybutu** , w polu tekstowym **nazwa atrybutu** wpisz nazwę atrybutu, w którym jest oczekiwany identyfikator użytkownika.

    1. Jeśli używasz domeny federacyjnej (na przykład usług ADFS itp.) w połączeniu z usługą Azure AD, kliknij opcję **Włącz odnajdywanie obszaru głównego** i skonfiguruj pole **Nazwa domeny**.

    1. W polu **Nazwa domeny** wpisz nazwę domeny w przypadku logowania za pomocą usług ADFS.

    1. Zaznacz pole wyboru **Włącz logowanie** jednokrotne, jeśli chcesz się wylogować z usługi Azure AD, gdy użytkownik wyloguje się z JIRA.
    
    1. Zaznacz pole wyboru **Wymuszaj zalogowanie się na platformie Azure** , jeśli chcesz zalogować się tylko za pomocą poświadczeń usługi Azure AD.
    
       > [!Note]
       > Aby włączyć domyślny formularz logowania dla logowania administratora na stronie logowania, gdy jest włączona funkcja Wymuś logowanie platformy Azure, Dodaj parametr zapytania w adresie URL przeglądarki.
       > `https://<domain:port>/login.jsp?force_azure_login=false`

    1. Kliknij przycisk **Zapisz**, aby zapisać ustawienia.

       > [!NOTE]
       > Aby uzyskać więcej informacji na temat instalacji i rozwiązywania problemów, odwiedź [Przewodnik administratora łącznika usługi MS JIRA SSO](./ms-confluence-jira-plugin-adminguide.md). Istnieje również [często zadawane pytania](./ms-confluence-jira-plugin-adminguide.md) dotyczące pomocy.

### <a name="create-jira-saml-sso-by-microsoft-test-user"></a>Tworzenie użytkownika testowego programu JIRA SAML SSO by Microsoft

Aby umożliwić użytkownikom usługi Azure AD logowanie się na serwerze lokalnym JIRA, muszą one być obsługiwane w usłudze JIRA SAML SSO przez firmę Microsoft. W przypadku programu JIRA SAML SSO by Microsoft aprowizowanie to zadanie wykonywane ręcznie.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do serwera lokalnego JIRA jako administrator.

2. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **User management** (Zarządzanie użytkownikami).

    ![Zrzut ekranu przedstawia zarządzanie użytkownikami wybrane z menu Ustawienia.](./media/jiramicrosoft-tutorial/user1.png)

3. Nastąpi przekierowanie do strony dostępu administratora. Podaj wartość w polu **Password** (Hasło) i kliknij przycisk **Confirm** (Potwierdź).

    ![Zrzut ekranu przedstawia stronę dostępu administratora, na której wprowadzane są poświadczenia.](./media/jiramicrosoft-tutorial/user2.png)

4. W sekcji karty **User management** (Zarządzanie użytkownikami) kliknij pozycję **create user** (utwórz użytkownika).

    ![Zrzut ekranu przedstawia kartę Zarządzanie użytkownikami, w której można utworzyć użytkownika.](./media/jiramicrosoft-tutorial/user3.png) 

5. Na stronie okna dialogowego **„Create new user”** (Tworzenie nowego użytkownika) wykonaj następujące kroki:

    ![Zrzut ekranu przedstawia okno dialogowe Tworzenie nowego użytkownika, w którym można wprowadzić informacje w tym kroku.](./media/jiramicrosoft-tutorial/user4.png) 

    a. W polu tekstowym **Email address** (Adres e-mail) wpisz adres e-mail użytkownika, na przykład B.simon@contoso.com.

    b. W polu tekstowym **pełna nazwa** wpisz pełną nazwę użytkownika, na przykład B. Simon.

    c. W polu tekstowym **Username** (Nazwa użytkownika) wpisz adres e-mail użytkownika, na przykład B.simon@contoso.com.

    d. W polu tekstowym **Password** (Hasło) wpisz hasło użytkownika.

    e. Kliknij pozycję **Utwórz użytkownika**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka JIRA SAML SSO by Microsoft na panelu dostępu powinno nastąpić automatyczne zalogowanie do programu JIRA SAML SSO by Microsoft, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj usługę JIRA SAML SSO przez firmę Microsoft w usłudze Azure AD](https://aad.portal.azure.com/)
