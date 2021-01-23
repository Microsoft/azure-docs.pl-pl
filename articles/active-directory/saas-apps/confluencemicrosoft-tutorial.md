---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Confluence SAML SSO przez firmę Microsoft | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a aplikacją Confluence SAML SSO by Microsoft.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/25/2020
ms.author: jeedes
ms.openlocfilehash: 34365a8bd7a15f502aa89a966adb14807e802cc4
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737002"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-confluence-saml-sso-by-microsoft"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Confluence SAML SSO przez firmę Microsoft

W tym samouczku dowiesz się, jak zintegrować usługę Confluence SAML SSO przez firmę Microsoft z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Confluence SAML SSO przez firmę Microsoft z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Confluence SAML SSO przez firmę Microsoft.
* Zezwól użytkownikom na automatyczne logowanie się, aby Confluence protokół SAML SSO przez firmę Microsoft przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.


## <a name="description"></a>Opis:

Użyj konta usługi Microsoft Azure Active Directory w połączeniu z serwerem Atlassian Confluence, aby umożliwić logowanie jednokrotne. Dzięki temu wszyscy użytkownicy w organizacji mogą korzystać z poświadczeń usługi Azure AD w celu zalogowania się do aplikacji Confluence. Ta wtyczka używa protokołu SAML 2.0 na potrzeby federacji.

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Confluence SAML SSO by Microsoft są potrzebne następujące elementy:

- Subskrypcji usługi Azure AD
- Aplikacja serwera Confluence zainstalowana na serwerze z 64-bitowym systemem Windows (lokalnie lub w infrastrukturze IaaS chmury)
- Serwer Confluence obsługuje protokół HTTPS
- Zwróć uwagę na obsługiwane wersje wtyczki Confluence podane w poniższej sekcji.
- Serwer Confluence jest dostępny w Internecie, w szczególności dla strony logowania usługi Azure AD na potrzeby uwierzytelniania, i powinien mieć możliwość odbierania tokenu z usługi Azure AD
- Poświadczenia administratora są skonfigurowane w aplikacji Confluence
- WebSudo jest wyłączone w aplikacji Confluence
- Użytkownik testowy utworzony w aplikacji serwera Confluence

> [!NOTE]
> Nie zalecamy używania środowiska produkcyjnego aplikacji Confluence do testowania czynności opisanych w tym samouczku. Integrację należy najpierw przetestować w środowisku programistycznym lub przejściowym aplikacji, a dopiero później użyć środowiska produkcyjnego.

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

Aby rozpocząć, potrzebne są następujące elementy:

* Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Confluence z logowaniem jednokrotnym SAML za pomocą subskrypcji usługi logowania jednokrotnego (SSO) firmy Microsoft.

## <a name="supported-versions-of-confluence"></a>Obsługiwane wersje aplikacji Confluence

Aktualnie obsługiwane są następujące wersje aplikacji Confluence:

- Confluence: 5,0 do 5,10
- Confluence: 6.0.1 do 6.15.9
- Confluence: 7.0.1 do 7.9.3

> [!NOTE]
> Należy pamiętać, że nasza wtyczka Confluence działa również na Ubuntu w wersji 16,04

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program Confluence SAML SSO by Microsoft obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Dodawanie aplikacji Confluence SAML SSO by Microsoft z galerii

Aby skonfigurować integrację aplikacji Confluence SAML SSO by Microsoft z usługą Azure AD, musisz dodać aplikację Confluence SAML SSO by Microsoft z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Confluence SAML SSO przez firmę Microsoft** w polu wyszukiwania.
1. Wybierz pozycję **Confluence SAML SSO przez firmę Microsoft** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-confluence-saml-sso-by-microsoft"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla usługi Confluence SAML SSO przez firmę Microsoft

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Confluence SAML SSO przez firmę Microsoft przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Confluence SAML SSO przez firmę Microsoft.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Confluence SAML SSO przez firmę Microsoft, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Konfigurowanie logowania JEDNOkrotnego SAML Confluence za pomocą logowania jednokrotnego przez firmę Microsoft](#configure-confluence-saml-sso-by-microsoft-sso)** w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz Confluence SAML SSO przez użytkownika testowego firmy Microsoft](#create-confluence-saml-sso-by-microsoft-test-user)** , aby dysponować odpowiednikiem B. Simon w Confluence SAML SSO przez firmę Microsoft, która jest połączona z reprezentacją usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie **Confluence SAML SSO przez** integrację aplikacji firmy Microsoft Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<domain:port>/plugins/servlet/saml/auth`

    b. W polu **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<domain:port>/`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Port jest opcjonalny, jeśli adres URL zawiera nazwę. Te wartości można uzyskać podczas konfigurowania wtyczki Confluence, co jest objaśnione w dalszej części tego samouczka.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do Confluence SAML SSO przez firmę Microsoft.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Confluence SAML SSO by Microsoft**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-confluence-saml-sso-by-microsoft-sso"></a>Konfigurowanie logowania jednokrotnego SAML Confluence przez usługę Microsoft SSO

1. W innym oknie przeglądarki sieci Web Zaloguj się do swojego wystąpienia Confluence jako administrator.

1. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **Dodatki**.

    ![Zrzut ekranu pokazujący wybraną ikonę "koło zębate" i "Dodatki" wyróżnioną w menu rozwijanym.](./media/confluencemicrosoft-tutorial/addon1.png)

1. Pobierz wtyczkę z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=56503). Ręcznie przekaż wtyczkę udostępnioną przez firmę Microsoft za pomocą menu **Przekazywanie dodatku**. Pobieranie wtyczki jest objęte [umową serwisową firmy Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Zrzut ekranu przedstawiający stronę "Zarządzanie dodatkami" z wybraną akcją "Przekaż dodatek".](./media/confluencemicrosoft-tutorial/addon12.png)

1. W celu uruchomienia scenariusza zwrotnego serwera proxy Confluence lub modułu równoważenia obciążenia wykonaj następujące czynności:

    > [!NOTE]
    > Najpierw skonfiguruj serwer za pomocą poniższych instrukcji, a następnie zainstaluj wtyczkę.

    a. Dodaj poniższy atrybut w elemencie **Connector** port w pliku **server.xml** aplikacji serwera JIRA.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Zrzut ekranu pokazujący plik "server.xml" z atrybutem dodanym do portu "łącznik".](./media/confluencemicrosoft-tutorial/reverseproxy1.png)

    b. Zmień wartość w polu **Podstawowy adres URL** w obszarze **Ustawienia systemu** odpowiednio dla serwera proxy lub modułu równoważenia obciążenia.

    ![Zrzut ekranu przedstawiający stronę "Administracja-ustawienia" z wyróżnioną pozycją "podstawowy adres URL".](./media/confluencemicrosoft-tutorial/reverseproxy2.png)

1. Po zainstalowaniu wtyczki jest ona wyświetlana w sekcji dodatków **Zainstalowane przez użytkownika** w ramach sekcji **Zarządzanie dodatkami**. Kliknij pozycję **Configure** (Konfiguruj), aby skonfigurować nową wtyczkę.

    ![Zrzut ekranu pokazujący sekcję "zainstalowane przez użytkownika" z wyróżnionym przyciskiem "Konfiguruj".](./media/confluencemicrosoft-tutorial/addon15.png)

1. Wykonaj następujące kroki na stronie konfiguracji:

    ![Zrzut ekranu przedstawiający stronę konfiguracji logowania jednokrotnego.](./media/confluencemicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Upewnij się, że tylko jeden certyfikat jest zamapowany do aplikacji, aby zapobiec błędom podczas rozpoznawania metadanych. Jeśli dostępnych jest wiele certyfikatów, administrator otrzyma błąd podczas rozpoznawania metadanych.

    1. W polu tekstowym **Adres URL metadanych** wklej wartość pola **Adres URL metadanych federacyjnych aplikacji** skopiowaną z witryny Azure Portal i kliknij przycisk **Rozpoznaj**. Spowoduje to odczytanie adresu URL metadanych dostawcy tożsamości i wypełnienie wszystkich pól danymi.

    1. Skopiuj wartości **identyfikatora, adresu URL odpowiedzi i adresu URL logowania**, a następnie wklej je odpowiednio w polach tekstowych **Identyfikator, Adres URL odpowiedzi i Adres URL logowania** w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    1. W polu **Nazwa przycisku logowania** wpisz nazwę przycisku, która według Twojej organizacji ma być wyświetlana użytkownikom na ekranie logowania.

    1. W polu **Opis przycisku Zaloguj** wpisz opis przycisku, który organizacja chce, aby użytkownicy widzieli na ekranie logowania.

    1. W polu **Lokalizacje identyfikatora użytkownika SAML** wybierz opcję **Identyfikator użytkownika znajduje się w elemencie NameIdentifier instrukcji Subject** lub **Identyfikator użytkownika znajduje się w elemencie Attribute**.  Ten identyfikator musi być IDENTYFIKATORem użytkownika Confluence. Jeśli identyfikator użytkownika nie jest zgodny, system nie zezwoli użytkownikom na logowanie się. 

       > [!Note]
       > Domyślna lokalizacja identyfikatora użytkownika SAML to element NameIdentifier. Można ją zmienić za pomocą opcji elementu Attribute, podając nazwę odpowiedniego atrybutu.

    1. Jeśli wybierzesz pozycję **Identyfikator użytkownika w opcji elementu atrybutu** , w polu tekstowym **nazwa atrybutu** wpisz nazwę atrybutu, w którym jest oczekiwany identyfikator użytkownika. 

    1. Jeśli używasz domeny federacyjnej (na przykład usług ADFS itp.) w połączeniu z usługą Azure AD, kliknij opcję **Włącz odnajdywanie obszaru głównego** i skonfiguruj pole **Nazwa domeny**.

    1. W polu **Nazwa domeny** wpisz nazwę domeny w przypadku logowania za pomocą usług ADFS.

    1. Zaznacz pole wyboru **Włącz logowanie** jednokrotne, jeśli chcesz się wylogować z usługi Azure AD, gdy użytkownik wyloguje się z Confluence. 

    1. Zaznacz pole wyboru **Wymuszaj zalogowanie się na platformie Azure** , jeśli chcesz zalogować się tylko za pomocą poświadczeń usługi Azure AD.

       > [!Note]
       > Aby włączyć domyślną formę logowania administratora na stronie logowania, gdy jest włączona funkcja Wymuś logowanie do platformy Azure, Dodaj parametr zapytania w adresie URL przeglądarki.
       > `https://<domain:port>/login.action?force_azure_login=false`

    1. Kliknij przycisk **Zapisz**, aby zapisać ustawienia.

       > [!NOTE]
       > Aby uzyskać więcej informacji na temat instalacji i rozwiązywania problemów, odwiedź [Przewodnik administratora łącznika usługi MS Confluence SSO](./ms-confluence-jira-plugin-adminguide.md). Istnieje również [często zadawane pytania](./ms-confluence-jira-plugin-adminguide.md) dotyczące pomocy.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Tworzenie użytkownika testowego aplikacji Confluence SAML SSO by Microsoft

Aby umożliwić użytkownikom usługi Azure AD logowanie się na serwerze lokalnym Confluence, muszą one być obsługiwane w usłudze Confluence SAML SSO przez firmę Microsoft. W przypadku aplikacji Confluence SAML SSO by Microsoft aprowizowanie to zadanie wykonywane ręcznie.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do serwera lokalnego Confluence jako administrator.

1. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **User management** (Zarządzanie użytkownikami).

    ![Dodawanie pracownika](./media/confluencemicrosoft-tutorial/user1.png)

1. W obszarze Użytkownicy kliknij przycisk **Dodaj użytkowników** kartę. Na stronie **Dodawanie użytkownika** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający "Confluence Administration" z wybraną kartą "Dodaj użytkowników" i "Dodaj użytkownika".](./media/confluencemicrosoft-tutorial/user2.png)

    a. W polu tekstowym **username** wpisz adres e-mail użytkownika, na przykład B. Simon.

    b. W polu tekstowym **pełna nazwa** wpisz pełną nazwę użytkownika, na przykład B. Simon.

    c. W polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail użytkownika, np. B.Simon@contoso.com.

    d. W polu tekstowym **hasło** wpisz hasło dla B. Simon.

    e. Kliknij pozycję **Confirm Password** (Potwierdź hasło), a następnie ponownie wprowadź hasło.

    f. Kliknij przycisk **Add** (Dodaj).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do Confluence SAML SSO przy użyciu adresu URL logowania firmy Microsoft, w którym można zainicjować przepływ logowania. 

* Przejdź do Confluence SAML SSO przez adres URL logowania Microsoft bezpośrednio i zainicjuj w tym miejscu przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka SSO Confluence SAML przez firmę Microsoft w obszarze Moje aplikacje zostanie ono przekierowany do Confluence SAML SSO przy użyciu adresu URL logowania Microsoft. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Confluence SAML SSO przez firmę Microsoft można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)