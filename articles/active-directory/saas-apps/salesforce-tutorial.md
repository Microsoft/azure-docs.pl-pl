---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Salesforce | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory a usługą Salesforce.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 6d653594cce20439a6e1214f104d875ec76bf1a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90979853"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO) w usłudze Salesforce

W tym samouczku dowiesz się, jak zintegrować usługę Salesforce z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Salesforce z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do usługi Salesforce.
* Zezwól użytkownikom na automatyczne logowanie do usługi Salesforce przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne w usłudze Salesforce.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Salesforce obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**

* Usługi Salesforce obsługują [ **Automatyczne** Inicjowanie obsługi i cofanie aprowizacji użytkowników](salesforce-provisioning-tutorial.md) (zalecane)

* Aplikacja Salesforce obsługuje aprowizowanie użytkowników typu **just in time**

* Aplikację mobilną Salesforce można teraz skonfigurować za pomocą usługi Azure AD w celu włączenia logowania jednokrotnego. W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

## <a name="adding-salesforce-from-the-gallery"></a>Dodawanie usługi Salesforce z galerii

Aby skonfigurować integrację usługi Salesforce z usługą Azure AD, musisz dodać usługę Salesforce z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Salesforce** w polu wyszukiwania.
1. Wybierz pozycję **Salesforce** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-salesforce"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla usług Salesforce

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą usług Salesforce przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Salesforce.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD w usłudze Salesforce, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-salesforce-sso)** w usłudze Salesforce, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego usługi Salesforce](#create-salesforce-test-user)** , aby dysponować odpowiednikiem B. Simon w usłudze Salesforce, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Salesforce** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz wartość, korzystając z następującego wzorca:

    Konto przedsiębiorstwa: `https://<subdomain>.my.salesforce.com`

    Konto dewelopera: `https://<subdomain>-dev-ed.my.salesforce.com`
    
    b. W polu tekstowym **adres URL odpowiedzi** wpisz wartość przy użyciu następującego wzorca:

    Konto przedsiębiorstwa: `https://<subdomain>.my.salesforce.com`

    Konto dewelopera: `https://<subdomain>-dev-ed.my.salesforce.com`

    c. W polu tekstowym **Identyfikator** wpisz wartość, korzystając z następującego wzorca:

    Konto przedsiębiorstwa: `https://<subdomain>.my.salesforce.com`

    Konto dewelopera: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta usługi Salesforce](https://help.salesforce.com/support).

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie usługi Salesforce** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz funkcję B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Salesforce.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Salesforce**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-salesforce-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Salesforce

1. Aby zautomatyzować konfigurację w usłudze Salesforce, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Konfiguracja usługi Salesforce** spowoduje przekierowanie do aplikacji Sign-On w usłudze Salesforce. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do logowania jednokrotnego w usłudze Salesforce. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-13.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować usługi Salesforce, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy Salesforce jako administrator i wykonaj następujące czynności:

1. Kliknij pozycję **Konfiguracja** pod **ikoną ustawień** w prawym górnym rogu strony.

    ![Ikona konfigurowania ustawień pojedynczego Sign-On](./media/salesforce-tutorial/configure1.png)

1. Przewiń w dół do pozycji **USTAWIENIA** w okienku nawigacji i kliknij pozycję **Tożsamość**, aby rozwinąć odpowiednią sekcję. Następnie kliknij pozycję **Ustawienia logowania jednokrotnego**.

    ![Skonfiguruj ustawienia pojedynczego Sign-On](./media/salesforce-tutorial/sf-admin-sso.png)

1. Na stronie **Ustawienia logowania jednokrotnego** kliknij przycisk **Edytuj**.

    ![Skonfiguruj edytowanie pojedynczej Sign-On](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Jeśli nie możesz włączyć ustawień logowania jednokrotnego dla swojego konta usługi Salesforce, może być konieczne skontaktowanie się z [zespołem pomocy technicznej klienta usługi Salesforce](https://help.salesforce.com/support).

1. Wybierz pozycję **Włączona obsługa protokołu SAML**, a następnie kliknij pozycję **Zapisz**.

    ![Konfigurowanie protokołu SAML z włączonym pojedynczym Sign-On](./media/salesforce-tutorial/sf-enable-saml.png)

1. Aby skonfigurować ustawienia logowania jednokrotnego SAML, kliknij pozycję **Nowe z pliku metadanych**.

    ![Konfiguruj pojedyncze Sign-On nowe z pliku metadanych](./media/salesforce-tutorial/sf-admin-sso-new.png)

1. Kliknij pozycję **Wybierz plik**, aby przekazać plik metadanych XML pobrany z witryny Azure Portal, i kliknij pozycję **Utwórz**.

    ![Konfigurowanie pojedynczego Sign-On Wybieranie pliku](./media/salesforce-tutorial/xmlchoose.png)

1. Na stronie **Ustawienia pojedynczej Sign-On SAML** pola wypełniaj automatycznie, wybierz opcję **Inicjowanie obsługi użytkowników** , a następnie kliknij przycisk **Zapisz**.

    ![Konfigurowanie włączenia obsługi pojedynczej Sign-On użytkownika](./media/salesforce-tutorial/salesforcexml.png)

1. W okienku nawigacji po lewej stronie w usłudze Salesforce kliknij pozycję **Ustawienia firmowe**, aby rozwinąć odpowiednią sekcję, a następnie kliknij pozycję **Moja domena**.

    ![Konfigurowanie jednego Sign-On moją domenę](./media/salesforce-tutorial/sf-my-domain.png)

1. Przewiń w dół do sekcji **Konfiguracja uwierzytelniania** i kliknij przycisk **Edytuj**.

    ![Skonfiguruj konfigurację uwierzytelniania pojedynczego Sign-On](./media/salesforce-tutorial/sf-edit-auth-config.png)

1. W sekcji **Konfiguracja uwierzytelniania** zaznacz pozycję **AzureSSO** jako **usługę uwierzytelniania** konfiguracji logowania jednokrotnego SAML, a następnie kliknij pozycję **Zapisz**.

    ![Konfigurowanie usługi uwierzytelniania pojedynczego Sign-On](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Jeśli zostanie wybrana więcej niż jedna usługa uwierzytelniania, podczas inicjowania logowania w środowisku usługi Salesforce użytkownikom będzie wyświetlany monit o wybranie usługi uwierzytelniania, przy użyciu której chcą się zalogować. Jeśli nie chcesz, aby ten monit był wyświetlany, **pozostaw wszystkie inne usługi uwierzytelniania niezaznaczone**.

### <a name="create-salesforce-test-user"></a>Tworzenie użytkownika testowego usługi Salesforce

W tej sekcji użytkownik o nazwie B. Simon został utworzony w usłudze Salesforce. Usługa Salesforce obsługuje aprowizację typu just in time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w usłudze Salesforce, zostanie utworzony podczas próby uzyskania dostępu do usługi Salesforce. Usługa Salesforce obsługuje także automatyczne aprowizowanie użytkowników. Więcej szczegółowych informacji na temat konfigurowania automatycznego aprowizowania użytkowników możesz znaleźć [tutaj](salesforce-provisioning-tutorial.md).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

1. Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do usługi Salesforce, w którym można zainicjować przepływ logowania. 

2. Przejdź bezpośrednio do adresu URL logowania usługi Salesforce i zainicjuj w nim przepływ logowania.

3. Możesz użyć panelu programu Microsoft Access. Po kliknięciu kafelka usługi Salesforce w panelu dostępu należy automatycznie zalogować się do usługi Salesforce, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-salesforce-mobile"></a>Testowanie logowania jednokrotnego dla usługi Salesforce (Mobile)

1. Otwórz aplikację mobilną Salesforce. Na stronie logowania kliknij pozycję **Użyj domeny niestandardowej**.

    ![Aplikacja mobilna Salesforce Użyj domeny niestandardowej](media/salesforce-tutorial/mobile-app1.png)

1. W polu tekstowym **domena niestandardowa** Wprowadź zarejestrowaną niestandardową nazwę domeny, a następnie kliknij przycisk **Kontynuuj**.

    ![Domena niestandardowa aplikacji mobilnej Salesforce](media/salesforce-tutorial/mobile-app2.png)

1. Wprowadź swoje poświadczenia usługi Azure AD, aby zalogować się do aplikacji Salesforce, a następnie kliknij przycisk **dalej**.

    ![Poświadczenia usługi Azure AD aplikacji mobilnej Salesforce](media/salesforce-tutorial/mobile-app3.png)

1. Na stronie **Zezwalanie na dostęp** , jak pokazano poniżej, kliknij pozycję **Zezwól** , aby udzielić dostępu do aplikacji usługi Salesforce.

    ![Aplikacja mobilna Salesforce — Zezwalanie na dostęp](media/salesforce-tutorial/mobile-app4.png)

1. Na koniec po pomyślnym zalogowaniu zostanie wyświetlona strona główna aplikacji.

    ![](media/salesforce-tutorial/mobile-app5.png)Strona główna usługi Salesforce aplikacji ![ mobilnej Salesforce](media/salesforce-tutorial/mobile-app6.png)

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu usługi Salesforce można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)
