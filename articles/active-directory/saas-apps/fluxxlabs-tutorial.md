---
title: 'Samouczek: integracja Azure Active Directory z usługą FluxX Labs | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i FluxX Labs.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.openlocfilehash: 00cf3e33c87255485f7ea73526e474b0d941124b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826371"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fluxx-labs"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą FluxX Labs

W tym samouczku dowiesz się, jak zintegrować FluxX Labs z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi FluxX Labs z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do FluxX Labs.
* Zezwól użytkownikom na automatyczne logowanie do FluxX Labs przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym w usłudze FluxX Labs.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa FluxX Labs obsługuje zainicjowane przez **dostawcy tożsamości** Logowanie jednokrotne
* Po skonfigurowaniu FluxX Labs można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-fluxx-labs-from-the-gallery"></a>Dodawanie FluxX Labs z galerii

Aby skonfigurować integrację FluxX Labs z usługą Azure AD, musisz dodać laboratoria FluxX z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **FluxX Labs** w polu wyszukiwania.
1. Wybierz pozycję **FluxX Labs** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-fluxx-labs"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla laboratoriów FluxX Labs

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą FluxX Labs przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze FluxX Labs.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą FluxX Labs, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne w usłudze FluxX Labs](#configure-fluxx-labs-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego FluxX Labs](#create-fluxx-labs-test-user)** , aby dysponować odpowiednikiem B. Simon w FluxX Labs, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **FluxX Labs** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: 

    | Środowisko | Wzorzec adresu URL|
    |-------------|------------|
    | Produkcja | `https://<subdomain>.fluxx.io` |
    | Przed produkcją | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 

    | Środowisko | Wzorzec adresu URL|
    |-------------|------------|
    | Produkcja | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Przed produkcją | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej FluxX Labs](https://fluxx.zendesk.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie FluxX Labs** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do laboratoriów FluxX Labs.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **FluxX Labs**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-fluxx-labs-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze FluxX Labs

1. W innym oknie przeglądarki sieci Web Zaloguj się do witryny firmy FluxX Labs jako administrator.

2. Wybierz pozycję **administrator** poniżej sekcji **Ustawienia** .

    ![Zrzut ekranu pokazujący sekcję "Ustawienia" z wybraną pozycją "Administrator".](./media/fluxxlabs-tutorial/config1.png)

3. W panelu administratora wybierz opcję integracje dodatków **plug-in**,  >  **Integrations** a następnie wybierz pozycję **SAML SSO — (wyłączone).**

    ![Zrzut ekranu pokazujący kartę "integracje" z wybraną opcją "S A M L s s O-(disabled).](./media/fluxxlabs-tutorial/config2.png)

4. W sekcji atrybut wykonaj następujące czynności:

    ![Zrzut ekranu pokazujący sekcję "atrybuty" z zaznaczonym przyciskiem "S A M L s O", wartości wprowadzonych w polach i wybranym przycisku "Zapisz".](./media/fluxxlabs-tutorial/config3.png)

    a. Zaznacz pole wyboru **SAML SSO** .

    b. W polu tekstowym **Ścieżka żądania** wpisz **/AUTH/SAML**.

    c. W polu tekstowym **ścieżka wywołania zwrotnego** wpisz **/AUTH/SAML/callback**.

    d. W polu tekstowym **adres URL usługi konsumenckej potwierdzenia (pojedynczy adres url Sign-On)** wprowadź wartość **adresu URL odpowiedzi** wprowadzoną w Azure Portal.

    e. W polu tekstowym **odbiorcy (identyfikator jednostki SP)** wprowadź wartość **identyfikatora** , która została wprowadzona w Azure Portal.

    f. W polu tekstowym **adres URL logowania JEDNOkrotnego dla dostawcy tożsamości** wklej wartość **adresu URL** , która została skopiowana z Azure Portal.

    przykład Otwórz certyfikat zakodowany w formacie Base-64 w Notatniku, skopiuj zawartość tego pliku do schowka, a następnie wklej go do pola tekstowego **certyfikat dostawcy tożsamości** .

    h. W polu tekstowym **Format identyfikatora nazwy** wprowadź wartość `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` .

    i. Kliknij przycisk **Zapisz**.

    > [!NOTE]
    > Po zapisaniu zawartości pole będzie wyświetlane jako puste dla zabezpieczeń, ale wartość została zapisana w konfiguracji.

### <a name="create-fluxx-labs-test-user"></a>Tworzenie użytkownika testowego FluxX Labs

Aby umożliwić użytkownikom usługi Azure AD logowanie się do FluxX Labs, muszą one być obsługiwane w usłudze FluxX Labs. W przypadku Fluxxych laboratoriów Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy FluxX Labs jako administrator.

2. Kliknij poniżej wyświetlaną **ikonę**.

    ![Zrzut ekranu pokazujący Opcje administratora z ikoną "plus" wybraną w obszarze "pulpit nawigacyjny jest pusty".](./media/fluxxlabs-tutorial/config6.png)

3. Na pulpicie nawigacyjnym kliknij poniżej ikonę wyświetlaną, aby otworzyć kartę **nowe osoby** .

    ![Zrzut ekranu pokazujący menu "Zarządzanie kontaktem" z ikoną "plus" obok wybranej pozycji "osoby".](./media/fluxxlabs-tutorial/config4.png)

4. W sekcji **nowe osoby** wykonaj następujące czynności:

    ![Konfiguracja FluxX Labs](./media/fluxxlabs-tutorial/config5.png)

    a. FluxX Labs używają poczty e-mail jako unikatowego identyfikatora logowania jednokrotnego (SSO). Wypełnij pole **UID logowania jednokrotnego** , podając adres e-mail użytkownika, który jest zgodny z adresem e-mail, który jest używany jako logowanie za pomocą logowania jednokrotnego.

    b. Kliknij przycisk **Zapisz**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka FluxX Labs w panelu dostępu należy automatycznie zalogować się do laboratoriów FluxX, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj FluxX Labs z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić FluxX Labs z zaawansowaną widocznością i kontrolkami](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)