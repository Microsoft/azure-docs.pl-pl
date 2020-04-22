---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z usługą InVision | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą InVision.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 02487206-30b0-4b1d-ae99-573c3d2ef9b0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/09/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c2427fff37158745e416f4b2f0641697ad62ea9
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682693"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-invision"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z usługą InVision

W tym samouczku dowiesz się, jak zintegrować program InVision z usługą Azure Active Directory (Azure AD). Podczas integracji usługi InVision z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do usługi InVision.
* Włącz użytkownikom automatyczne logowanie do usługi InVision za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego inVision.InVision single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* InVision obsługuje jednostki SSO inicjowane przez **sp i idp**
* Po skonfigurowaniu programu InVision można wymusić kontrolę sesji, która chroni eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-invision-from-the-gallery"></a>Dodawanie programu InVision z galerii

Aby skonfigurować integrację programu InVision z usługą Azure AD, należy dodać program InVision z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **invision** w polu wyszukiwania.
1. Wybierz **pozycję InVision** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-invision"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi InVision

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą usługi InVision przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze InVision.

Aby skonfigurować i przetestować sytą usługę Azure AD za pomocą usługi InVision, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne invision](#configure-invision-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego InVision](#create-invision-test-user)** — aby mieć odpowiednik B.Simon w invision, który jest połączony z reprezentacji usługi Azure AD użytkownika.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **InVision** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<SUBDOMAIN>.invisionapp.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.invisionapp.com//sso/auth`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.invisionapp.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta InVision,](mailto:support@invisionapp.com) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie invision** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do InVision.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **InVision**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-invision-sso"></a>Konfigurowanie sytcha w uidziale w udziale w udziale w udziale w udziale

1. W innym oknie przeglądarki internetowej zaloguj się do witryny InVision jako administrator.

1. Kliknij **na Zespół** i wybierz **pozycję Ustawienia**.

    ![Konfiguracja invision](./media/invision-tutorial/config1.png)

1. Przewiń w dół do **logowania jednokrotnego,** a następnie kliknij przycisk **Zmień**.

    ![Konfiguracja invision](./media/invision-tutorial/config3.png)

1. Na stronie **logowanie jednokrotne** wykonaj następujące czynności:

    ![Konfiguracja invision](./media/invision-tutorial/config4.png)

    a. Zmień **wymagaj podania coso dla każdego członka < nazwy konta >** na **Włączone**.

    b. W polu tekstowym **nazwa** wprowadź nazwę `azureadsso`na przykład na przykład .

    d. Wprowadź wartość adresu URL logowania w polu tekstowym **Adresu URL logowania.**

    d. W polu tekstowym **adresu URL wylogowywania** wklej wartość adresu URL **wylogowywania,** która została skopiowana z portalu Azure.

    e. W **textboxie certyfikatu SAML** otwórz pobrany **certyfikat (Base64)** w Notatniku, skopiuj zawartość i wklej ją do pola tekstowego certyfikatu SAML.

    f. W obszarze tekstowym **Format** nazwy `Unspecified` użyj **formatu identyfikatora nazwy**.

    g. Z listy rozwijanej algorytmu **HASH**wybierz opcję **SHA-256** .

    h. Wprowadź odpowiednią nazwę etykiety **przycisku SSO**.

    i. Włącz **aprowizować po prostu w czasie.**

    j. Kliknij przycisk **Update** (Aktualizuj).

### <a name="create-invision-test-user"></a>Utwórz użytkownika testowego InVision

1. W innym oknie przeglądarki internetowej zaloguj się do witryny InVision jako administrator.

1. Kliknij **na Zespół** i wybierz pozycję **Kontakty**.

    ![Konfiguracja invision](./media/invision-tutorial/config2.png)

1. Kliknij **ikonę + ICON,** aby dodać nowego użytkownika.

    ![Konfiguracja invision](./media/invision-tutorial/user1.png)

1. Wprowadź adres e-mail użytkownika i kliknij przycisk **Dalej**.

    ![Konfiguracja invision](./media/invision-tutorial/user2.png)

1. Po zweryfikowaniu adresu e-mail, a następnie kliknij przycisk **Zaproś**.

    ![Konfiguracja invision](./media/invision-tutorial/user3.png)

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka InVision w Panelu dostępu należy automatycznie zalogować się do invision, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę InVision w usłudze Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić InVision dzięki zaawansowanej widoczności i sterowaniu](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)