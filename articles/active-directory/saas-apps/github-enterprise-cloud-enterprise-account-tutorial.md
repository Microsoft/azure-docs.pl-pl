---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą GitHub Enterprise Cloud — konto przedsiębiorstwa | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między usługami Azure Active Directory i GitHub Enterprise Cloud — Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/29/2020
ms.author: jeedes
ms.openlocfilehash: 7f23551fee5331d14cdcf9e31e248cf42022d4c3
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92449306"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-cloud---enterprise-account"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą GitHub Enterprise Cloud — konto przedsiębiorstwa

W ramach tego samouczka dowiesz się, jak zintegrować konto w chmurze w usłudze GitHub Enterprise z Azure Active Directory (Azure AD). Po zintegrowaniu konta usługi GitHub Enterprise Cloud-Enterprise z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do konta przedsiębiorstwa w serwisie GitHub Enterprise w chmurze.
* Zezwól użytkownikom na automatyczne logowanie do konta usługi GitHub Enterprise Cloud-Enterprise z kontami w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Usługa GitHub Enterprise Cloud — subskrypcja z włączonym logowaniem jednokrotnym (SSO) konta przedsiębiorstwa.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa GitHub Enterprise Cloud — konto przedsiębiorstwa obsługuje logowanie jednokrotne w ramach usługi **SP** i **dostawcy tożsamości**
* Usługa GitHub Enterprise Cloud — konto w przedsiębiorstwie obsługuje Inicjowanie obsługi klienta **just in Time**
* Po skonfigurowaniu konta w usłudze GitHub Enterprise Cloud-Enterprise można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie poufnych danych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-github-enterprise-cloud---enterprise-account-from-the-gallery"></a>Dodawanie konta w chmurze usługi GitHub dla przedsiębiorstw z galerii

Aby skonfigurować integrację konta przedsiębiorstwa z usługą GitHub Enterprise Cloud-Enterprise w usłudze Azure AD, musisz dodać do listy zarządzanych aplikacji SaaS konto w chmurze dla przedsiębiorstw z galerii.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania opcję konto w chmurze w usłudze **GitHub Enterprise — przedsiębiorstwo** .
1. Wybierz pozycję **konto usługi GitHub Enterprise Cloud-Enterprise** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-cloud---enterprise-account"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD dla przedsiębiorstwa GitHub Enterprise Cloud-Enterprise

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą konta w chmurze w usłudze GitHub dla przedsiębiorstw przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze GitHub Enterprise Cloud — konto przedsiębiorstwa.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu konta w chmurze w usłudze GitHub Enterprise, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj konto usługi GitHub Enterprise Cloud-Enterprise Logowanie jednokrotne](#configure-github-enterprise-cloud-enterprise-account-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego konta usługi GitHub enterprise Cloud-Enterprise](#create-github-enterprise-cloud-enterprise-account-test-user)** , aby uzyskać odpowiednik elementu B. Simon w usłudze GitHub Enterprise Cloud-Enterprise, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracji aplikacji w **chmurze w przedsiębiorstwie** w serwisie GitHub Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://github.com/enterprises/<ENTERPRISE-SLUG>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://github.com/enterprises/<ENTERPRISE-SLUG>/saml/consume`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

     W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca:  `https://github.com/enterprises/<ENTERPRISE-SLUG>/sso`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania, adresu URL odpowiedzi i identyfikatora. Skontaktuj się z [zespołem pomocy technicznej w witrynie GitHub Enterprise Cloud-Enterprise klienta](mailto:support@github.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificateBase64.png)

1. W sekcji **Konfigurowanie konta usługi GitHub Enterprise Cloud-Enterprise** należy skopiować odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do konta przedsiębiorstwa w serwisie GitHub Enterprise Cloud.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **GitHub Enterprise Cloud — konto przedsiębiorstwa**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-github-enterprise-cloud-enterprise-account-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze GitHub Enterprise Cloud-Enterprise

Aby skonfigurować Logowanie jednokrotne w usłudze **GitHub Enterprise Cloud-Enterprise** po stronie konta, musisz wysłać pobrany **certyfikat (base64)** i odpowiednie skopiowane adresy URL z Azure Portal do usługi [GitHub Enterprise Cloud — zespół pomocy technicznej dla przedsiębiorstw](mailto:support@github.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-github-enterprise-cloud-enterprise-account-test-user"></a>Utwórz użytkownika testowego konta usługi GitHub Enterprise Cloud-Enterprise

W tej sekcji użytkownik o nazwie B. Simon został utworzony w ramach konta usługi GitHub Enterprise Cloud-Enterprise. Usługa GitHub Enterprise Cloud — konto przedsiębiorstwa obsługuje Inicjowanie obsługi klienta just-in-Time, które jest domyślnie włączone. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje na koncie usługi GitHub Enterprise Cloud-Enterprise, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka konta w chmurze usługi GitHub dla przedsiębiorstw w panelu dostępu należy automatycznie zalogować się do konta usługi GitHub Enterprise Cloud — Enterprise, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj konto Cloud-Enterprise w usłudze GitHub Enterprise w usłudze Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chronić konto korporacyjne w chmurze w usłudze GitHub Enterprise z zaawansowanymi możliwościami widoczności i kontroli](/cloud-app-security/proxy-intro-aad)