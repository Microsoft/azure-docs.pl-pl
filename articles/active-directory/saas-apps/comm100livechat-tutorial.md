---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Comm100 Live Chat | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a aplikacją Comm100 Live Chat.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: 0ce6e4dc44a19f4aaee35cc4477761010d76a96b
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91775923"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-comm100-live-chat"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Comm100 Live Chat

W tym samouczku dowiesz się, jak zintegrować usługę Comm100 Live Chat z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Comm100 Live Chat z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Comm100 rozmowy na żywo.
* Zezwól użytkownikom na automatyczne logowanie się, aby Comm100 rozmowę na żywo z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) usługi Comm100 na żywo.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Comm100 Live Chat obsługuje jednokrotne logowanie inicjowane przez **dostawcę usługi**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Dodawanie aplikacji Comm100 Live Chat z galerii

Aby skonfigurować integrację aplikacji Comm100 Live Chat z usługą Azure AD, musisz dodać aplikację Comm100 Live Chat z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Comm100 Live Chat** w polu wyszukiwania.
1. Wybierz pozycję **Comm100 Live Chat** from the Results panel, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-comm100-live-chat"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD na potrzeby Comm100 Live Chat

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Comm100 Live Chat przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Comm100 Live Chat.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Comm100 Live Chat, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj rejestrację jednokrotną usługi Comm100 na żywo](#configure-comm100-live-chat-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego usługi Comm100 Live Chat](#create-comm100-live-chat-test-user)** , aby uzyskać odpowiednik B. Simon w usłudze Comm100 Live Chat, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Comm100 Live Chat** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > Wartość adresu URL logowania nie jest prawdziwa. Ta wartość adresu URL logowania zostanie zaktualizowana przy użyciu rzeczywistego adresu URL logowania, co objaśniono w dalszej części tego samouczka.

1. Aplikacja Comm100 Live Chat oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/edit-attribute.png)

1. Oprócz powyższych Comm100 aplikacja czatu na żywo oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa |  Atrybut źródłowy|
    | ---------------| --------------- |
    |   poczta e-mail    | user.mail |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie usługi Comm100 Live Chat** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do usługi Comm100 Live Chat.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Comm100 Live Chat**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-comm100-live-chat-sso"></a>Konfigurowanie rejestracji jednokrotnej na żywo usługi Comm100

1. W innym oknie przeglądarki sieci Web Zaloguj się, aby Comm100 rozmowę na żywo jako administrator zabezpieczeń.

1. W prawej górnej części strony kliknij pozycję **Moje konto**.

   ![Comm100 Live Chat myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

1. W lewej części menu kliknij pozycję **Zabezpieczenia**, a następnie kliknij pozycję **Agent logowania jednokrotnego**.

   ![Zrzut ekranu pokazujący menu konta po lewej stronie z wyróżnioną opcją "zabezpieczenia" i "Logowanie jednokrotne agenta".](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

1. Na stronie **Agent logowania jednokrotnego** wykonaj następujące czynności:

   ![Zabezpieczenia aplikacji Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

   a. Skopiuj pierwsze wyróżnione łącze i wklej je w polu tekstowym **adres URL logowania** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

   b. W polu tekstowym **Adres URL logowania jednokrotnego protokołu SAML** wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

   c. W polu tekstowym **adres URL wylogowywania zdalnego** wklej wartość **adresu URL wylogowania**, która została skopiowana z Azure Portal.

   d. Kliknij pozycję **Wybierz plik**, aby przekazać certyfikat zakodowany w formacie base-64 pobrany wcześniej z witryny Azure Portal do pozycji **Certyfikat**.

   e. Kliknij przycisk **Zapisz zmiany**.

### <a name="create-comm100-live-chat-test-user"></a>Tworzenie użytkownika testowego aplikacji Comm100 Live Chat

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Comm100 Live Chat, muszą one być obsługiwane w usłudze Comm100 Live Chat. W aplikacji Comm100 Live Chat aprowizacja to zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się, aby Comm100 rozmowę na żywo jako administrator zabezpieczeń.

2. W prawej górnej części strony kliknij pozycję **Moje konto**.

    ![Comm100 Live Chat myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. W lewej części menu, kliknij pozycję **Agenci**, a następnie kliknij pozycję **Nowy agent**.

    ![Agent aplikacji Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Na stronie **Nowy agent** wykonaj następujące czynności:

    ![Nowy agent aplikacji Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. W polu tekstowym **adres e-mail** wprowadź adres e-mail użytkownika, na przykład **B. Simon \@ contoso.com**.

    b. W polu tekstowym **imię i nazwisko** , wprowadź imię użytkownika, np. **B**.

    c. W polu tekstowym **Last Name** (Nazwisko) wprowadź nazwisko użytkownika, na przykład **Simon**.

    d. W polu tekstowym **Nazwa wyświetlana** wprowadź nazwę wyświetlaną użytkownika, np **. B. Simon**

    e. W polu tekstowym **Hasło** wpisz swoje hasło.

    f. Kliknij pozycję **Zapisz**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka aplikacji Comm100 Live Chat w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Comm100 Live Chat, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę Comm100 Live Chat z usługą Azure AD](https://aad.portal.azure.com/)

