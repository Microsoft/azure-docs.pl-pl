---
title: 'Samouczek: integracja Azure Active Directory z TalentSpace Saba | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory a Saba TalentSpace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.openlocfilehash: 746f60374a4e8f01af119d6e9c604b941e7c4950
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91944244"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-talentspace"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z TalentSpace Saba

W tym samouczku dowiesz się, jak zintegrować TalentSpace Saba z usługą Azure Active Directory (Azure AD). Po zintegrowaniu TalentSpace Saba z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do TalentSpace Saba.
* Zezwól użytkownikom na automatyczne logowanie do TalentSpace Saba z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z obsługą logowania jednokrotnego TalentSpace (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Saba TalentSpace obsługuje logowanie jednokrotne w usłudze **SP**
* Po skonfigurowaniu Saba TalentSpace można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-saba-talentspace-from-the-gallery"></a>Dodawanie TalentSpace Saba z galerii

Aby skonfigurować integrację Saba TalentSpace z usługą Azure AD, musisz dodać Saba TalentSpace z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Saba TalentSpace** w polu wyszukiwania.
1. Wybierz pozycję **Saba TalentSpace** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-saba-talentspace"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla TalentSpace Saba

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Saba TalentSpace przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w TalentSpace Saba.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą TalentSpace Saba, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Saba TalentSpace Logowanie jednokrotne](#configure-saba-talentspace-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika w TalentSpace Saba](#create-saba-talentspace-test-user)** , aby dysponować odpowiednikiem B. Simon w Saba TalentSpace, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Saba TalentSpace** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://global.hgncloud.com/[companyname]/saml/login`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://global.hgncloud.com/[companyname]/saml/metadata`

    c. W polu tekstowym **adres URL odpowiedzi (adres URL usługi konsumenckej odbiorcy)** wpisz adres URL, używając następującego wzorca: `https://global.hgncloud.com/[companyname]/saml/SSO`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta Saba TalentSpace](https://support.saba.com/) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie TalentSpace Saba** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do TalentSpace Saba.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Saba TalentSpace**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-saba-talentspace-sso"></a>Konfigurowanie Saba TalentSpace SSO

1. W innym oknie przeglądarki Zaloguj się do aplikacji **Saba TalentSpace** jako administrator.

2. Kliknij kartę**Options** (Opcje).
  
    ![Zrzut ekranu przedstawiający stronę główną "Saba TalentSpace" z wybraną kartą "Opcje".](./media/halogen-software-tutorial/tutorial-halogen-12.png)

3. W okienku nawigacji po lewej stronie kliknij pozycję **SAML Configuration** (Konfiguracja SAML).
  
    ![Zrzut ekranu przedstawiający okienko nawigacji po lewej stronie "interfejs użytkownika" z wybraną konfiguracją "S A M L".](./media/halogen-software-tutorial/tutorial-halogen-13.png)

4. Na stronie **SAML Configuration** (Konfiguracja SAML) wykonaj następujące kroki:

    ![Zrzut ekranu przedstawiający stronę "S A M" konfiguracji z wyróżnionymi opcjami "Ustawienia".](./media/halogen-software-tutorial/tutorial-halogen-14.png)

    a. W sekcji **Unique Identifier** (Unikatowy identyfikator) wybierz opcję **NameID** (Id. nazwy).

    b. W sekcji **Unique Identifier Maps To** (Unikatowy identyfikator jest mapowany na) wybierz opcję **Username** (Nazwa użytkownika).
  
    c. Aby przekazać pobrany plik metadanych, kliknij przycisk **Browse** (Przeglądaj), aby wybrać plik, a następnie wybierz przycisk **Upload File** (Przekaż plik).

    d. Aby przetestować konfigurację, kliknij przycisk **Run test** (Uruchom test).

    > [!NOTE]
    > Musisz poczekać na komunikat "*test SAML został ukończony. Zamknij to okno*. Następnie zamknij otwarte okno przeglądarki. Pole wyboru **Enable SAML** (Włącz SAML) jest włączone tylko wtedy, gdy ukończono test.

    e. Wybierz pozycję **Enable SAML** (Włącz SAML).

    f. Kliknij przycisk **Zapisz zmiany**.

### <a name="create-saba-talentspace-test-user"></a>Utwórz użytkownika testu Saba TalentSpace

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w obszarze Saba TalentSpace.

**Aby utworzyć użytkownika o nazwie Britta Simon w Saba TalentSpace, wykonaj następujące czynności:**

1. Zaloguj się do aplikacji **Saba TalentSpace** jako administrator.

2. Kliknij kartę **User Center** (Centrum użytkowników), a następnie kliknij pozycję **Create User** (Utwórz użytkownika).

    ![Zrzut ekranu pokazujący kartę "centrum użytkowników" i wybraną pozycję "Utwórz użytkownika".](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. Na stronie dialogowej **New User** (Nowy użytkownik) wykonaj następujące kroki:

    ![Co to jest program Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    a. W polu tekstowym **imię i nazwisko** wpisz imię użytkownika, np. **B**.

    b. W polu tekstowym **nazwisko** wpisz nazwisko użytkownika, np. **Simon**.

    c. W polu tekstowym **username** wpisz **B. Simon**, nazwę użytkownika, jak w Azure Portal.

    d. W polu tekstowym **hasło** wpisz hasło dla B. Simon.

    e. Kliknij przycisk **Zapisz**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Saba TalentSpace w panelu dostępu należy automatycznie zalogować się do TalentSpace Saba, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj TalentSpace Saba z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)