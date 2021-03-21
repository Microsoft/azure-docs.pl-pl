---
title: 'Samouczek: integracja Azure Active Directory z pomocą techniczną Jitbit | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i pomocą techniczną Jitbit.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: jeedes
ms.openlocfilehash: b3fbf73ab51092f614a416355477fc552a404fd3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581812"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Samouczek: integracja Azure Active Directory z pomocą techniczną Jitbit

W tym samouczku dowiesz się, jak zintegrować pomoc techniczną Jitbit z usługą Azure Active Directory (Azure AD). Po zintegrowaniu pomocy technicznej Jitbit z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do pomocy technicznej Jitbit.
* Zezwól użytkownikom na automatyczne logowanie do Jitbit pomocy technicznej przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne (SSO) usługi Jitbit.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Pomoc techniczna Jitbit obsługuje usługę **SP** zainicjowaną przez usługę SSO.

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="add-jitbit-helpdesk-from-the-gallery"></a>Dodaj pomoc techniczną Jitbit z galerii

Aby skonfigurować integrację pomocy technicznej Jitbit z usługą Azure AD, musisz dodać do listy zarządzanych aplikacji SaaS pomoc techniczną Jitbit z galerii.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Jitbit pomocy technicznej** w polu wyszukiwania.
1. Wybierz pozycję Jitbit Help ( **Pomoc techniczna** ) w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-jitbit-helpdesk"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Jitbit pomocy technicznej

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Jitbit pomocy technicznej przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w pomocy technicznej Jitbit.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą pomocy technicznej Jitbit, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-jitbit-helpdesk-sso)** w usłudze Jitbit, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego pomocy technicznej Jitbit](#create-jitbit-helpdesk-test-user)** , aby dysponować odpowiednikiem B. Simon w pomocy technicznej Jitbit, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **pomocy technicznej Jitbit** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **adres URL logowania** wpisz jeden z adresów URL, używając następującego wzorca:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej programu Jitbit](https://www.jitbit.com/support/) w celu uzyskania tej wartości.

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `https://www.jitbit.com/web-helpdesk/`

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie pomocy technicznej Jitbit** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do pomocy technicznej Jitbit.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Pomoc techniczna Jitbit**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-jitbit-helpdesk-sso"></a>Konfigurowanie rejestracji jednokrotnej w Jitbit pomocy technicznej

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny pomocy technicznej Jitbit jako administrator.

1. Na pasku narzędzi u góry kliknij pozycję **Administracja**.

    ![Administracja](./media/jitbit-helpdesk-tutorial/settings.png "Administracja")

1. Kliknij pozycję **Ustawienia ogólne**.

    ![Zrzut ekranu przedstawia link Ustawienia ogólne.](./media/jitbit-helpdesk-tutorial/general.png "Użytkownicy, firmy i uprawnienia")

1. W sekcji Konfiguracja **ustawień uwierzytelniania** wykonaj następujące czynności:

    ![Ustawienia uwierzytelniania](./media/jitbit-helpdesk-tutorial/authentication.png "Ustawienia uwierzytelniania")

    a. Wybierz pozycję Włącz logowanie jednokrotne w protokole **SAML 2,0**, aby zalogować się przy użyciu jednego Sign-On (SSO) z **OneLogin**.

    b. W polu tekstowym **adres URL punktu końcowego** wklej wartość **adresu URL logowania** skopiowanego z Azure Portal.

    c. Otwórz certyfikat zakodowany w formacie **Base-64** w Notatniku, skopiuj zawartość tego pliku do schowka, a następnie wklej go do pola tekstowego **certyfikatu X. 509** .

    d. Kliknij przycisk **Zapisz zmiany**.

### <a name="create-jitbit-helpdesk-test-user"></a>Utwórz użytkownika testowego pomocy technicznej Jitbit

Aby umożliwić użytkownikom usługi Azure AD logowanie się do pomocy technicznej Jitbit, muszą one być obsługiwane w pomocy technicznej Jitbit. W przypadku pomocy technicznej Jitbit, Inicjowanie obsługi jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do dzierżawy **pomocy technicznej Jitbit** .

1. W menu u góry kliknij pozycję **Administracja**.

    ![Administracja](./media/jitbit-helpdesk-tutorial/settings.png "Administracja")

1. Kliknij pozycję **Użytkownicy, firmy i uprawnienia**.

    ![Użytkownicy, firmy i uprawnienia](./media/jitbit-helpdesk-tutorial/users.png "Użytkownicy, firmy i uprawnienia")

1. Kliknij pozycję **Dodaj użytkownika**.

    ![Dodaj użytkownika](./media/jitbit-helpdesk-tutorial/add.png "Dodaj użytkownika")

1. W sekcji Tworzenie wpisz dane konta usługi Azure AD, które chcesz udostępnić w następujący sposób:

    ![Utwórz](./media/jitbit-helpdesk-tutorial/create-section.png "Utwórz")

   a. W polu tekstowym **Nazwa** użytkownika wpisz nazwę użytkownika, na przykład **BrittaSimon**.

   b. W polu tekstowym **adres e-mail** wpisz adres e-mail użytkownika **BrittaSimon@contoso.com** .

   c. W polu tekstowym **imię i nazwisko** wpisz imię użytkownika, np. **Britta**.

   d. W polu tekstowym **nazwisko** wpisz nazwisko użytkownika, np. **Simon**.

   e. Kliknij pozycję **Utwórz**.

> [!NOTE]
> Do aprowizacji kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników i interfejsów API usługi Jitbit.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do pomocy technicznej Jitbit, w którym można zainicjować przepływ logowania. 

* Przejdź do adresu URL logowania pomocy technicznej Jitbit bezpośrednio i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka pomocy technicznej Jitbit w obszarze Moje aplikacje zostanie on przekierowany do adresu URL logowania działu pomocy technicznej Jitbit. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu pomocy technicznej Jitbit można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).