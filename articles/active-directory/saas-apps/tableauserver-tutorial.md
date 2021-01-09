---
title: 'Samouczek: integracja logowania jednokrotnego (SSO) Azure Active Directory z serwerem Tableau | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i serwerem Tableau.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 531b22122ff19eb3653400cd7e5d06e2162e0365
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98045142"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Samouczek: integracja logowania jednokrotnego (SSO) Azure Active Directory z serwerem Tableau

W tym samouczku dowiesz się, jak zintegrować serwer Tableau z usługą Azure Active Directory (Azure AD). W przypadku integrowania serwera Tableau z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do serwera Tableau.
* Zezwól użytkownikom na automatyczne logowanie do serwera Tableau przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.


## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) na serwerze Tableau.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Serwer Tableau obsługuje logowanie jednokrotne zainicjowane przez usługę **SP**

## <a name="adding-tableau-server-from-the-gallery"></a>Dodawanie serwera Tableau z galerii

Aby skonfigurować integrację serwera Tableau z usługą Azure AD, musisz dodać serwer Tableau z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Tableau Server** w polu wyszukiwania.
1. Wybierz pozycję **serwer Tableau** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-tableau-server"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla serwera Tableau

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą serwera Tableau przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem na serwerze Tableau.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą serwera Tableau, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne serwera Tableau](#configure-tableau-server-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego serwera Tableau](#create-tableau-server-test-user)** , aby dysponować odpowiednikiem B. Simon na serwerze Tableau, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **serwera Tableau** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://azure.<domain name>.link`

    b. W polu **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://azure.<domain name>.link`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Poprzednie wartości nie są wartościami rzeczywistymi. Zaktualizuj wartości z rzeczywistym adresem URL i identyfikatorem na stronie konfiguracji serwera Tableau, która została omówiona w dalszej części tego samouczka.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie serwera Tableau** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do serwera Tableau.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **serwer Tableau**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-tableau-server-sso"></a>Konfigurowanie logowania jednokrotnego serwera Tableau

1. Aby skonfigurować Logowanie jednokrotne dla aplikacji, musisz zalogować się do dzierżawy serwera Tableau jako administrator.

2. Na karcie **Konfiguracja** wybierz pozycję **tożsamość użytkownika & dostęp**, a następnie wybierz kartę Metoda **uwierzytelniania** .

    ![Zrzut ekranu przedstawia uwierzytelnianie wybrane z tożsamości użytkownika & dostępu.](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Na stronie **Konfiguracja** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia stronę konfiguracji, na której można wprowadzić podane wartości.](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. W obszarze **Metoda uwierzytelniania** wybierz pozycję SAML.

    b. Zaznacz pole wyboru **Włącz uwierzytelnianie SAML dla serwera**.

    c. Zwrotny adres URL serwera Tableau — adres URL, do którego będą uzyskiwać dostęp użytkownicy serwera Tableau, na przykład `http://tableau_server` . Użycie `http://localhost` nie jest zalecane. Użycie adresu URL z końcowym ukośnikiem (na przykład `http://tableau_server/` ) nie jest obsługiwane. Skopiuj **adres URL zwrotnego serwera Tableau** i wklej go w celu **zalogowania się do adresu URL** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal

    d. Identyfikator jednostki SAML — identyfikator jednostki jednoznacznie identyfikuje instalację serwera Tableau w dostawcy tożsamości. Możesz tutaj wprowadzić adres URL serwera Tableau, jeśli chcesz, ale nie musi to być adres URL serwera Tableau. Skopiuj **Identyfikator jednostki SAML** i wklej go do pola tekstowego **identyfikatora** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal

    e. Kliknij **plik metadanych XML pobierania** i otwórz go w aplikacji Edytor tekstu. Znajdź adres URL usługi Konsumenckej potwierdzenia przy użyciu protokołu HTTP POST i indeksu 0 i skopiuj adres URL. Teraz wklej je do pola tekstowego **adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal

    f. Znajdź plik metadanych Federacji pobrany z Azure Portal, a następnie Przekaż go w **pliku metadanych SAML dostawcy tożsamości**.

    przykład Wprowadź nazwy atrybutów, których dostawcy tożsamości używa do przechowywania nazw użytkowników, nazw wyświetlanych i adresów e-mail.

    h. Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Klient musi przekazać plik certyfikatu x509 zakodowany przez PEM z rozszerzeniem CRT i plikiem klucza prywatnego RSA lub DSA, który ma rozszerzenie. Key jako plik klucza certyfikatu. Aby uzyskać więcej informacji na temat pliku certyfikatu i pliku klucza certyfikatu, zapoznaj się z [tym](https://help.tableau.com/current/server/en-us/saml_requ.htm) dokumentem. Jeśli potrzebujesz pomocy przy konfigurowaniu protokołu SAML na serwerze Tableau, zapoznaj się z tym artykułem [Konfigurowanie całego serwera SAML](https://help.tableau.com/current/server/en-us/config_saml.htm).

### <a name="create-tableau-server-test-user"></a>Utwórz użytkownika testowego serwera Tableau

Celem tej sekcji jest utworzenie użytkownika o nazwie B. Simon na serwerze Tableau. Musisz udostępnić wszystkim użytkownikom na serwerze Tableau.

Ta nazwa użytkownika powinna być zgodna z wartością skonfigurowaną w niestandardowym atrybucie usługi Azure AD **nazwy użytkownika**. Przy prawidłowym mapowaniu należy wykonać integrację, aby skonfigurować Logowanie jednokrotne w usłudze Azure AD.

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, musisz skontaktować się z administratorem serwera Tableau w organizacji.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do serwera Tableau, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania do serwera Tableau, a następnie zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka serwera Tableau w obszarze Moje aplikacje zostanie on przekierowany do adresu URL logowania do serwera Tableau. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu serwera Tableau można wymusić kontrolki sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji rozciągają się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)