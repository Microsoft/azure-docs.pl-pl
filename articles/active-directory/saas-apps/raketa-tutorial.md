---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Raketa | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Raketa.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/28/2020
ms.author: jeedes
ms.openlocfilehash: 649a26e474c7c4d6b4f51a035b3f8f9da35b9dd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92511158"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-raketa"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Raketa

W tym samouczku dowiesz się, jak zintegrować usługę Raketa z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Raketa z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Raketa.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Raketa przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Raketa.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Raketa obsługuje usługę **SP** zainicjowaną przez usługę SSO.
* Po skonfigurowaniu Raketa można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-raketa-from-the-gallery"></a>Dodawanie Raketa z galerii

Aby skonfigurować integrację programu Raketa z usługą Azure AD, musisz dodać Raketa z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** [1].

    ![rkt_1](./media/raketa-tutorial/azure-active-directory.png)

1. Przejdź do **aplikacji korporacyjnych** [2], a następnie wybierz pozycję **wszystkie aplikacje** [3].

1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** [4]. 

    ![rkt_2](./media/raketa-tutorial/new-app.png)

1. W sekcji **Dodaj z galerii** [5] wpisz **Raketa** w polu wyszukiwania [6].

1. Wybierz pozycję **Raketa** z panelu wyników [7], a następnie kliknij przycisk **Dodaj** [8]. 

    ![rkt_3](./media/raketa-tutorial/add-btn.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-raketa"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Raketa

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Raketa przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Raketa.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Raketa, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-raketa-sso)** w usłudze Raketa, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Raketa](#create-raketa-test-user)** , aby dysponować odpowiednikiem B. Simon w Raketa, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Raketa** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne** [9].

    ![rkt_4](./media/raketa-tutorial/manage-sso.png)

1. Na stronie **Wybierz metodę logowania jednokrotnego** [9] wybierz pozycję **SAML** [10].

    ![rkt_5](./media/raketa-tutorial/saml.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** kliknij ikonę Edytuj/pióro dla **podstawowej konfiguracji protokołu SAML** [11], aby edytować ustawienia.

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    1. W polach tekstowych **Identyfikator (identyfikator jednostki)** [12] i **adres URL logowania** [14] wpisz adres URL: `https://raketa.travel/` .

    1. W polu tekstowym **adres URL odpowiedzi** [13] wpisz adres URL, używając następującego wzorca: `https://raketa.travel/sso/acs?clientId=<CLIENT_ID>` .  

    ![rkt_6](./media/raketa-tutorial/enter-urls.png)

    > [!NOTE]
    > Wartość adresu URL odpowiedzi nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta Raketa](mailto:help@raketa.travel) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** [15], aby pobrać certyfikat i zapisać go na komputerze.

1. W sekcji **Konfigurowanie Raketa** skopiuj odpowiednie adresy URL na podstawie wymagania.

    1. Adres URL logowania [16] — adres URL strony sieci Web autoryzacji używany do przekierowywania użytkowników do systemu uwierzytelniania.

    1. Identyfikator usługi Azure AD [17] — identyfikator usługi Azure AD.

    1. Adres URL wylogowywania [18] — adres URL strony sieci Web używany do przekierowywania użytkowników po wylogowaniu.

    ![rkt_7](./media/raketa-tutorial/copy-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory** [1], wybierz pozycję **Użytkownicy** [19], a następnie wybierz pozycję **Wszyscy użytkownicy** [20].

1. Wybierz pozycję **nowy użytkownik** [21] w górnej części ekranu.

    ![rkt_8](./media/raketa-tutorial/new-user.png)

1. We właściwościach **użytkownika** wykonaj następujące kroki:

   1. W polu **Nazwa użytkownika** [22] wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.

   1. W polu **Nazwa** [23] wprowadź wartość `B.Simon` .

   1. Zaznacz pole wyboru **Pokaż hasło** [25], a następnie Zapisz wartość wyświetlaną w polu **hasło** [24].

   1. Kliknij przycisk **Utwórz** [26]. 

    ![rkt_9](./media/raketa-tutorial/create-user.png)


### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Raketa.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw** [2], a następnie wybierz pozycję **wszystkie aplikacje** [3].

1. Na liście Aplikacje wybierz pozycję **Raketa** [27].  

    ![rkt_10](./media/raketa-tutorial/add-raketa.png)

1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy** [28]. 

    ![rkt_11](./media/raketa-tutorial/users-groups.png)

1. Wybierz pozycję **Dodaj użytkownika** [29], a następnie wybierz pozycję **Użytkownicy i grupy** [30] w oknie dialogowym **Dodaj przypisanie** .

    ![rkt_12](./media/raketa-tutorial/add-user-raketa.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** [31] z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** [32] w dolnej części ekranu.

1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** [33]. 

    ![rkt_13](./media/raketa-tutorial/assign-user.png)

## <a name="configure-raketa-sso"></a>Konfigurowanie logowania jednokrotnego Raketa

Aby skonfigurować Logowanie jednokrotne na stronie **Raketa** , musisz wysłać pobrany **certyfikat (base64)** i odpowiednie skopiowane adresy URL z Azure Portal do [zespołu pomocy technicznej Raketa](mailto:help@raketa.travel). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-raketa-test-user"></a>Utwórz użytkownika testowego Raketa

W tej sekcji utworzysz użytkownika o nazwie B. Simon w Raketa. Aby dodać użytkowników na platformie Raketa, Pracuj z [zespołem pomocy technicznej Raketa](mailto:help@raketa.travel) . Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Raketa w panelu dostępu należy automatycznie zalogować się do Raketa, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj Raketa z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chronić Raketa z zaawansowaną widocznością i kontrolkami](/cloud-app-security/proxy-intro-aad)