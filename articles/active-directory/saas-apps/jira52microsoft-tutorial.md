---
title: 'Samouczek: integracja Azure Active Directory z usługą JIRA SAML SSO przez firmę Microsoft (V 5.2) | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a programem JIRA SAML SSO by Microsoft (V5.2).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 7b85cc064babf44b14e80abc02669573b4730da2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736900"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Samouczek: integracja Azure Active Directory z usługą JIRA SAML SSO przez firmę Microsoft (V 5.2)

W tym samouczku dowiesz się, jak zintegrować usługę JIRA SAML SSO przez firmę Microsoft (V 5.2) z Azure Active Directory (Azure AD). Po zintegrowaniu usługi JIRA SAML SSO przez firmę Microsoft (V 5.2) z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do JIRA SAML SSO przez firmę Microsoft (V 5.2).
* Zezwól użytkownikom na automatyczne logowanie do JIRA SAML SSO przez firmę Microsoft (V 5.2) z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="description"></a>Opis

Użyj konta usługi Microsoft Azure Active Directory w połączeniu z serwerem Atlassian JIRA, aby umożliwić logowanie jednokrotne. Dzięki temu wszyscy użytkownicy w organizacji mogą korzystać z poświadczeń usługi Azure AD w celu zalogowania się do aplikacji JIRA. Ta wtyczka używa protokołu SAML 2.0 na potrzeby federacji.

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z programem JIRA SAML SSO by Microsoft (V5.2) są potrzebne następujące elementy:

- Subskrypcji usługi Azure AD
- Usługi JIRA Core i Software w wersji 5.2 zainstalowane w 64-bitowym systemie Windows
- Dla serwera JIRA włączono obsługę protokołu HTTPS
- Zwróć uwagę na obsługiwane wersje wtyczki usługi JIRA podane w poniżej sekcji.
- Serwer JIRA jest dostępny w Internecie, w szczególności dla strony logowania usługi AD Azure na potrzeby uwierzytelniania, i ma możliwość odebrania tokenu z usługi Azure AD
- Poświadczenia administratora są skonfigurowane w usłudze JIRA
- Funkcja WebSudo jest wyłączona w usłudze JIRA
- Użytkownik testowy został utworzony w aplikacji serwera JIRA

> [!NOTE]
> Podczas testowania kroków w tym samouczku nie zalecamy używania środowiska produkcyjnego usługi JIRA. Integrację należy najpierw przetestować w środowisku programistycznym lub przejściowym aplikacji, a dopiero później użyć środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska w wersji próbnej usługi Azure AD, możesz uzyskać miesięczną wersję próbną tutaj: [ofertę wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-jira"></a>Obsługiwane wersje usługi JIRA

* JIRA rdzeń i oprogramowanie: 5,2
* Usługa JIRA obsługuje także wersje od 6.0 do 7.12. Aby uzyskać więcej informacji, kliknij pozycję [JIRA SAML SSO by Microsoft](jiramicrosoft-tutorial.md)

> [!NOTE]
> Należy pamiętać, że nasza wtyczka JIRA działa również na Ubuntu w wersji 16,04.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program JIRA SAML SSO by Microsoft (V5.2) obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**.

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>Dodawanie programu JIRA SAML SSO by Microsoft (V5.2) z galerii

Aby skonfigurować integrację programu JIRA SAML SSO by Microsoft (V5.2) z usługą Azure AD, należy dodać program JIRA SAML SSO by Microsoft (V5.2) z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **JIRA SAML SSO przez firmę Microsoft (v 5.2)** w polu wyszukiwania.
1. Wybierz pozycję **JIRA SAML SSO przez firmę Microsoft (v 5.2)** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-jira-saml-sso-by-microsoft-v52"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla usługi JIRA SAML SSO przez firmę Microsoft (V 5.2)

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą JIRA SAML SSO przez firmę Microsoft (V 5.2) na podstawie użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić połączoną relację między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w JIRA SAML SSO przez firmę Microsoft (V 5.2).

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi JIRA SAML SSO przez firmę Microsoft (V 5.2), wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
2. Skonfiguruj rejestrację jednokrotną **[JIRA SAML przez firmę Microsoft (v 5.2) logowanie JEDNOkrotne](#configure-jira-saml-sso-by-microsoft-v52-sso)** — aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego programu JIRA SAML SSO by Microsoft (V5.2)](#create-jira-saml-sso-by-microsoft-v52-test-user)** — aby mieć w programie JIRA SAML SSO by Microsoft (V5.2) odpowiednik użytkownika Britta Simon, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

1. W Azure Portal na stronie integracja aplikacji **JIRA SAML SSO przez Microsoft (v 5.2)** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<domain:port>/plugins/servlet/saml/auth`

    b. W polu **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<domain:port>/`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Port jest opcjonalny, jeśli adres URL zawiera nazwę. Te wartości można uzyskać podczas konfigurowania wtyczki usługi Jira, co jest objaśnione w dalszej części tego samouczka.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do JIRA SAML SSO przez firmę Microsoft (V 5.2).

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **JIRA SAML SSO by Microsoft (V5.2)**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-jira-saml-sso-by-microsoft-v52-sso"></a>Konfigurowanie logowania jednokrotnego SAML JIRA przez Microsoft (V 5.2) Logowanie jednokrotne

1. W innym oknie przeglądarki sieci Web Zaloguj się do swojego wystąpienia JIRA jako administrator.

2. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **Dodatki**.

    ![Zrzut ekranu przedstawia Dodatki wybrane z menu Ustawienia.](./media/jira52microsoft-tutorial/addon1.png)

3. W sekcji karty Dodatki kliknij pozycję **Zarządzaj dodatkami**.

    ![Zrzut ekranu przedstawia Dodatki zarządzania wybrane na karcie dodatki.](./media/jira52microsoft-tutorial/addon7.png)

4. Pobierz wtyczkę z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=56521). Ręcznie przekaż wtyczkę udostępnioną przez firmę Microsoft za pomocą menu **Przekazywanie dodatku**. Pobieranie wtyczki jest objęte [umową serwisową firmy Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Zrzut ekranu przedstawia Zarządzanie dodatkami za pomocą linku Przekaż dodatek o nazwie.](./media/jira52microsoft-tutorial/addon12.png)

5. Po zainstalowaniu wtyczki jest ona wyświetlana w sekcji dodatków **Zainstalowane przez użytkownika**. Kliknij pozycję **Configure** (Konfiguruj), aby skonfigurować nową wtyczkę.

    ![Zrzut ekranu przedstawia sekcję "Logowanie jednokrotne SAML usługi Azure A D" dla JIRA z wybraną pozycją Konfiguruj.](./media/jira52microsoft-tutorial/addon13.png)

6. Wykonaj następujące kroki na stronie konfiguracji:

    ![Zrzut ekranu przedstawia stronę konfiguracji łącznika Microsoft JIRA S s.](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Upewnij się, że tylko jeden certyfikat jest zamapowany do aplikacji, aby zapobiec błędom podczas rozpoznawania metadanych. Jeśli jest dostępnych wiele certyfikatów podczas rozpoznawania metadanych, administrator otrzyma błąd.

    a. W polu tekstowym **Adres URL metadanych** wklej wartość pola **Adres URL metadanych federacyjnych aplikacji** skopiowaną z witryny Azure Portal i kliknij przycisk **Rozpoznaj**. Spowoduje to odczytanie adresu URL metadanych dostawcy tożsamości i wypełnienie wszystkich pól danymi.

    b. Skopiuj wartości **identyfikatora, adresu URL odpowiedzi i adresu URL logowania**, a następnie wklej je odpowiednio w polach tekstowych **Identyfikator, Adres URL odpowiedzi i Adres URL logowania** w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    c. W polu **Nazwa przycisku logowania** wpisz nazwę przycisku, która według Twojej organizacji ma być wyświetlana użytkownikom na ekranie logowania.

    d. W obszarze **Identyfikator użytkownika SAML lokalizacje** wybierz opcję **Identyfikator użytkownika znajduje się w elemencie NameIdentifier instrukcji subject** lub **Identyfikator użytkownika znajduje się w elemencie atrybutu**.  Ten identyfikator musi być IDENTYFIKATORem użytkownika JIRA. Jeśli identyfikator użytkownika nie jest zgodny, system nie zezwoli użytkownikom na logowanie się.

    > [!Note]
    > Domyślna lokalizacja identyfikatora użytkownika SAML to element NameIdentifier. Można ją zmienić za pomocą opcji elementu Attribute, podając nazwę odpowiedniego atrybutu.

    e. Jeśli wybierzesz pozycję **Identyfikator użytkownika w opcji elementu atrybutu** , w polu tekstowym **nazwa atrybutu** wpisz nazwę atrybutu, w którym jest oczekiwany identyfikator użytkownika. 

    f. Jeśli używasz domeny federacyjnej (na przykład usług ADFS itp.) w połączeniu z usługą Azure AD, kliknij opcję **Włącz odnajdywanie obszaru głównego** i skonfiguruj pole **Nazwa domeny**.

    przykład W polu **Nazwa domeny** wpisz nazwę domeny w przypadku logowania za pomocą usług ADFS.

    h. Zaznacz pole wyboru **Włącz logowanie** jednokrotne, jeśli chcesz się wylogować z usługi Azure AD, gdy użytkownik wyloguje się z JIRA. 

    i. Kliknij przycisk **Zapisz**, aby zapisać ustawienia.

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat instalacji i rozwiązywania problemów, odwiedź [Podręcznik administratora łącznika usługi MS JIRA SSO](./ms-confluence-jira-plugin-adminguide.md) , a także [często zadawane pytania](./ms-confluence-jira-plugin-adminguide.md) dotyczące pomocy.

### <a name="create-jira-saml-sso-by-microsoft-v52-test-user"></a>Tworzenie użytkownika testowego programu JIRA SAML SSO by Microsoft (V5.2)

Aby umożliwić użytkownikom usługi Azure AD logowanie się na serwerze lokalnym JIRA, muszą one być obsługiwane na serwerze lokalnym JIRA.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do serwera lokalnego JIRA jako administrator.

2. Zatrzymaj wskaźnik myszy nad ikoną koła zębatego i kliknij pozycję **User management** (Zarządzanie użytkownikami).

    ![Zrzut ekranu przedstawia zarządzanie użytkownikami wybrane z menu Ustawienia.](./media/jira52microsoft-tutorial/user1.png)

3. Nastąpi przekierowanie do strony dostępu administratora. Podaj wartość w polu **Password** (Hasło) i kliknij przycisk **Confirm** (Potwierdź).

    ![Zrzut ekranu przedstawia stronę dostępu administratora, na której wprowadzane są poświadczenia.](./media/jira52microsoft-tutorial/user2.png)

4. W sekcji karty **User management** (Zarządzanie użytkownikami) kliknij pozycję **create user** (utwórz użytkownika).

    ![Zrzut ekranu przedstawia kartę Zarządzanie użytkownikami, w której można utworzyć użytkownika.](./media/jira52microsoft-tutorial/user3.png) 

5. Na stronie okna dialogowego **„Create new user”** (Tworzenie nowego użytkownika) wykonaj następujące kroki:

    ![Zrzut ekranu przedstawia okno dialogowe Tworzenie nowego użytkownika, w którym można wprowadzić informacje w tym kroku.](./media/jira52microsoft-tutorial/user4.png)

    a. W polu tekstowym **Email address** (Adres e-mail) wpisz adres e-mail użytkownika, na przykład Brittasimon@contoso.com.

    b. W polu tekstowym **Full Name** (Imię i nazwisko) wpisz imię i nazwisko użytkownika, np. Britta Simon.

    c. W polu tekstowym **Username** (Nazwa użytkownika) wpisz adres e-mail użytkownika, na przykład Brittasimon@contoso.com.

    d. W polu tekstowym **Password** (Hasło) wpisz hasło użytkownika.

    e. Kliknij pozycję **Utwórz użytkownika**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do JIRAgo logowania jednokrotnego SAML przez firmę Microsoft (V 5.2) adresu URL, gdzie można zainicjować przepływ logowania. 

* Przejdź do adresu URL logowania jednokrotnego w usłudze JIRA SAML przez firmę Microsoft (V 5.2) bezpośrednio i zainicjuj przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka JIRA SAML SSO przez Microsoft (V 5.2) w obszarze Moje aplikacje zostanie ono przekierowany do JIRA protokołu SAML SSO przez firmę Microsoft (V 5.2) adres URL logowania. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu JIRA SAML SSO przez firmę Microsoft (V 5.2) można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).