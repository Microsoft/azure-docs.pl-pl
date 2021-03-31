---
title: 'Samouczek: integracja Azure Active Directory z usługą RunMyProcess | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i RunMyProcess.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.openlocfilehash: 011bbbda07806f1493ae27fbeef8509f0d12c44b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518454"
---
# <a name="tutorial-integrate-runmyprocess-with-azure-active-directory"></a>Samouczek: integracja RunMyProcess z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę RunMyProcess z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi RunMyProcess z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do RunMyProcess.
* Zezwól użytkownikom na automatyczne logowanie się do usługi RunMyProcess przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) RunMyProcess.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* RunMyProcess obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="adding-runmyprocess-from-the-gallery"></a>Dodawanie RunMyProcess z galerii

Aby skonfigurować integrację programu RunMyProcess z usługą Azure AD, musisz dodać RunMyProcess z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **RunMyProcess** w polu wyszukiwania.
1. Wybierz pozycję **RunMyProcess** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą RunMyProcess przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w RunMyProcess.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą RunMyProcess, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Logowanie jednokrotne](#configure-runmyprocess-sso)** w usłudze RunMyProcess, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
5. **[Utwórz użytkownika testowego RunMyProcess](#create-runmyprocess-test-user)** , aby dysponować odpowiednikiem B. Simon w RunMyProcess, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **RunMyProcess** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem obsługi klienta RunMyProcess](mailto:support@runmyprocess.com) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie RunMyProcess** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-runmyprocess-sso"></a>Konfigurowanie logowania jednokrotnego RunMyProcess

1. W innym oknie przeglądarki sieci Web Zaloguj się do swojej dzierżawy RunMyProcess jako administrator.

1. W okienku nawigacji po lewej stronie kliknij pozycję **konto** i wybierz pozycję **Konfiguracja**.

    ![Zrzut ekranu przedstawia konfigurację wybraną z konta.](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. Przejdź do sekcji **Metoda uwierzytelniania** i wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia kartę Metoda uwierzytelniania, w której można wprowadzić podane wartości.](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Jako **metodę** wybierz pozycję **SSO z Samlv2**.

    b. W polu tekstowym **przekierowanie rejestracji jednokrotnej** wklej wartość **adresu URL logowania**, która została skopiowana z Azure Portal.

    c. W polu tekstowym **przekierowanie wylogowania** wklej wartość **adresu URL wylogowania**, która została skopiowana z Azure Portal.

    d. W polu tekstowym **Format identyfikatora nazwy** wpisz wartość **Format identyfikatora nazwy** jako **urn: języka Oasis: names: TC: SAML: 1.1: NameID-format: EmailAddress**.

    e. Otwórz pobrany plik certyfikatu z Azure Portal w Notatniku, skopiuj zawartość pliku certyfikatu, a następnie wklej ją do pola tekstowego **certyfikatu** .

    f. Kliknij przycisk **Zapisz** ikonę.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi RunMyProcess.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **RunMyProcess**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-runmyprocess-test-user"></a>Utwórz użytkownika testowego RunMyProcess

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze RunMyProcess, muszą one być obsługiwane w RunMyProcess. W przypadku RunMyProcess, Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do firmowej witryny RunMyProcess jako administrator.

1. Kliknij pozycję **konto** i wybierz pozycję **Użytkownicy** w lewym panelu nawigacyjnym, a następnie kliknij pozycję **nowy użytkownik**.

    ![Nowy użytkownik](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "Nowy użytkownik")

1. W sekcji **Ustawienia użytkownika** wykonaj następujące czynności:

    ![Profil](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profil")
  
    a. Wpisz **nazwę** i **adres E-mail** prawidłowego konta usługi Azure AD, które chcesz udostępnić do powiązanych pól tekstowych.

    b. Wybierz **język IDE**, **Język** i **profil**.

    c. Wybierz opcję **Wyślij wiadomość e-mail do utworzenia konta**.

    d. Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Do aprowizacji Azure Active Directory kontami użytkowników można używać innych narzędzi do tworzenia kont użytkowników lub interfejsów API udostępnionych przez program RunMyProcess.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka RunMyProcess w panelu dostępu należy automatycznie zalogować się do RunMyProcess, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)