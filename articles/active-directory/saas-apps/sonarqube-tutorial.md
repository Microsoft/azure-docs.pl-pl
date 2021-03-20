---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą SonarQube | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i SonarQube.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: jeedes
ms.openlocfilehash: f756e738989775bf9c06b44a03f002c14f42e3ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182354"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sonarqube"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą SonarQube

W tym samouczku dowiesz się, jak zintegrować usługę SonarQube z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi SonarQube z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do SonarQube.
* Zezwól użytkownikom na automatyczne logowanie się do usługi SonarQube przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) SonarQube.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* SonarQube obsługuje logowanie jednokrotne w usłudze **SP**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-sonarqube-from-the-gallery"></a>Dodawanie SonarQube z galerii

Aby skonfigurować integrację programu SonarQube z usługą Azure AD, musisz dodać SonarQube z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SonarQube** w polu wyszukiwania.
1. Wybierz pozycję **SonarQube** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-sonarqube"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla SonarQube

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą SonarQube przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w SonarQube.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą SonarQube, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-sonarqube-sso)** w usłudze SonarQube, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego SonarQube](#create-sonarqube-test-user)** , aby dysponować odpowiednikiem B. Simon w SonarQube, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **SonarQube** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Adres URL logowania** wpisz adres URL: 

    * **Dla środowiska produkcyjnego**

        `https://servicessonar.corp.microsoft.com/`

    * **Dla środowiska deweloperskiego**

        `https://servicescode-dev.westus.cloudapp.azure.com`

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie SonarQube** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi SonarQube.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **SonarQube**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".

1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-sonarqube-sso"></a>Konfigurowanie logowania jednokrotnego SonarQube

1. Otwórz nowe okno przeglądarki sieci Web i zaloguj się do firmowej witryny SonarQube jako administrator.

1. Kliknij pozycję **administracja > konfiguracja > zabezpieczenia** i przejdź do **wtyczki SAML** , wykonując następujące kroki.

1. Skopiuj następujące szczegóły z metadanych dostawcy tożsamości i wklej je do odpowiednich pól tekstowych w dodatku SonarQube.
    1. Identyfikator jednostki dostawcy tożsamości
    2. Adres URL logowania
    3. Certyfikat X. 509 
1. Zapisz wszystkie szczegóły.
    ![DOSTAWCY tożsamości wtyczki SAML](./media/sonarqube-tutorial/sso-idp-metadata.png)

1. Na stronie **SAML** wykonaj następujące czynności:

    ![Konfiguracja SonarQube](./media/sonarqube-tutorial/config01.png)

    a. Przełącz opcję **włączone** na **wartość tak**.

    b. W polu tekstowym **Identyfikator aplikacji** wprowadź nazwę, taką jak **SonarQube**.

    c. W polu tekstowym **Nazwa dostawcy** wprowadź nazwę, na przykład **SAML**.

    d. W polu tekstowym **Identyfikator dostawcy** wklej wartość **identyfikatora usługi Azure AD**, która została skopiowana z Azure Portal.

    e. W polu tekstowym **adres URL logowania SAML** wklej wartość **adresu URL logowania**, który został skopiowany z Azure Portal.

    f. Otwórz w Notatniku certyfikat szyfrowany algorytmem Base64, skopiuj jego zawartość i wklej go do pola tekstowego **certyfikat dostawcy** .

    przykład W polu tekstowym **atrybut logowania użytkownika SAML** wprowadź wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    h. W polu tekstowym **Nazwa użytkownika SAML atrybutu** wprowadź wartość `http://schemas.microsoft.com/identity/claims/displayname` .

    i. W polu tekstowym **atrybut e-mail użytkownika SAML** wprowadź wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    j. Kliknij pozycję **Zapisz**.

### <a name="create-sonarqube-test-user"></a>Utwórz użytkownika testowego SonarQube

W tej sekcji utworzysz użytkownika o nazwie B. Simon w SonarQube. Współpracuj z [zespołem obsługi klienta SonarQube](https://www.sonarsource.com/support/) , aby dodać użytkowników z platformy SonarQube. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego. 

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

1. Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania SonarQube, w którym można zainicjować przepływ logowania. 

2. Przejdź bezpośrednio do adresu URL logowania SonarQube i zainicjuj w nim przepływ logowania.

3. Możesz użyć panelu programu Microsoft Access. Po kliknięciu kafelka SonarQube w panelu dostępu zostanie on przekierowany do adresu URL logowania SonarQube. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

* Po skonfigurowaniu SonarQube można wymusić kontrolki sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji rozciągają się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).