---
title: 'Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO) w usłudze omnichannel Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i jasno omnichannel z centrum kontaktów.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/18/2019
ms.author: jeedes
ms.openlocfilehash: 9929dc3dbe5870fbe1a5047fbc3fc552e32aba59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456702"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bright-pattern-omnichannel-contact-center"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą omnichannel

W tym samouczku dowiesz się, jak zintegrować jasny wzorzec omnichannel z centrum kontaktów z Azure Active Directory (Azure AD). Gdy integrujesz jasny wzorzec omnichannel z centrum kontaktów z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do jasno Omnichannelego centrum kontaktów.
* Zezwól użytkownikom na automatyczne logowanie do jasno Omnichannelego centrum kontaktów z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Jasny wzorzec omnichannel — subskrypcja z włączoną obsługą logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.



* Jasny wzorzec omnichannel centrum kontaktów obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne
* Jasny wzorzec omnichannel centrum kontaktów obsługuje funkcję aprowizacji użytkowników **just in Time**


## <a name="adding-bright-pattern-omnichannel-contact-center-from-the-gallery"></a>Dodawanie jasnego Omnichannelego centrum kontaktów z galerii

Aby skonfigurować integrację z programem omnichannel Contact Center w usłudze Azure AD, musisz dodać jasny wzorzec omnichannel centrum kontaktów z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **jasny wzorzec omnichannel Center Contact** w polu wyszukiwania.
1. Wybierz pozycję **jasnozielony wzorzec omnichannel Contact Center** from panel wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bright-pattern-omnichannel-contact-center"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD w celu uzyskania jasnego wzorca omnichannel Center

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD przy użyciu jasnego wzorca omnichannel Center contacting za pomocą użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w jasnym Omnichannele centrum kontaktów.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD przy użyciu jasnego wzorca omnichannel Contact Center, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj jasny wzorzec omnichannel Contact Center Logowanie jednokrotne](#configure-bright-pattern-omnichannel-contact-center-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Tworzenie jasnego wzorca omnichannel użytkownika testowego](#create-bright-pattern-omnichannel-contact-center-test-user)** — Aby uzyskać odpowiedni odpowiednik B. Simon w jasnym modelu omnichannel Contact Center, który jest połączony z reprezentacją usługi Azure AD użytkownika.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie " **jasna struktura omnichannel Contact Center** Application Integration" Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `<SUBDOMAIN>_sso`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.brightpattern.com/agentdesktop/sso/redirect`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.brightpattern.com/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [techniczną omnichannel Team Center](mailto:support@brightpattern.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Jasny wzorzec omnichannel aplikacja centrum kontaktów oczekuje potwierdzenia SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/edit-attribute.png)

1. Oprócz powyższych, jasna omnichannel aplikacji centrum kontaktów oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Przestrzeń nazw  |
    | ---------------| --------------- |
    | firstName | user.givenname |
    | lastName | user.surname |
    | poczta e-mail | user.mail |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie jasnego wzorca omnichannel Contact Center** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do jasnego wzorca omnichannel centrum kontaktów.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **jasnozielony wzorzec omnichannel Contact Center**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-bright-pattern-omnichannel-contact-center-sso"></a>Konfigurowanie jasnego wzorca omnichannel centrum kontaktów Logowanie jednokrotne

Aby skonfigurować Logowanie jednokrotne w witrynie **omnichannel Center** , musisz wysłać pobrany **certyfikat (base64)** i odpowiednie skopiowane adresy URL z Azure Portal do [jasnego wzorca omnichannel zespołu pomocy technicznej centrum kontaktów](mailto:support@brightpattern.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-bright-pattern-omnichannel-contact-center-test-user"></a>Utwórz jasny wzorzec omnichannel użytkownika testowego centrum kontaktów

W tej sekcji użytkownik o nazwie B. Simon został utworzony w jasnym wzorcu omnichannel centrum kontaktów. Jasny wzorzec omnichannel centrum kontaktów obsługuje funkcję aprowizacji użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w jasnym wzorcu omnichannel, zostanie utworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka omnichannel z centrum kontaktów w panelu dostępu należy automatycznie zalogować się do jasnego wzorca omnichannel centrum kontaktów, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj Omnichannele z centrum kontaktów w usłudze Azure AD](https://aad.portal.azure.com/)