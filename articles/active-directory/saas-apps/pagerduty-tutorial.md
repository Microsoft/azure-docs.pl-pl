---
title: 'Samouczek: Integracja usługi Azure Active Directory z PagerDuty | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a PagerDuty.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 99d9988a02b046562c2517df8a81b8ef5c778ef2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683516"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pagerduty"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z PagerDuty

W tym samouczku dowiesz się, jak zintegrować PagerDuty z usługą Azure Active Directory (Azure AD). Po zintegrowaniu PagerDuty z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do PagerDuty.
* Włącz użytkownikom automatyczne logowanie do PagerDuty za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego PagerDuty.PagerDuty single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* PagerDuty obsługuje sso inicjowane przez **SP**
* Po skonfigurowaniu PagerDuty można wymusić kontrolę sesji, które chronią eksfiltracji i infiltracji poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-pagerduty-from-the-gallery"></a>Dodawanie PagerDuty z galerii

Aby skonfigurować integrację PagerDuty z usługą Azure AD, należy dodać PagerDuty z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **PagerDuty** w polu wyszukiwania.
1. Wybierz **pagerDuty** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-pagerduty"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi PagerDuty

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą PagerDuty przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuował działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w PagerDuty.

Aby skonfigurować i przetestować sytuasz usługi Azure AD za pomocą PagerDuty, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj pagerDuty SSO](#configure-pagerduty-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz pagerDuty użytkownika testowego](#create-pagerduty-test-user)** — mieć odpowiednik B.Simon w PagerDuty, który jest połączony z reprezentacji usługi Azure AD użytkownika.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **PagerDuty** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.pagerduty.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.pagerduty.com`

    d. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta PagerDuty,](https://www.pagerduty.com/support/) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie PagerDuty** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do PagerDuty.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **PagerDuty**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-pagerduty-sso"></a>Konfigurowanie identyfikatora SYT PagerDuty

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy Pagerduty jako administrator.

2. W menu u góry kliknij pozycję **Ustawienia konta**.

    ![Ustawienia konta](./media/pagerduty-tutorial/ic778535.png "Ustawienia konta")

3. Kliknij **pozycję Logowanie jednokrotne**.

    ![Rejestracja jednokrotna](./media/pagerduty-tutorial/ic778536.png "Logowanie jednokrotne")

4. Na stronie **Włącz logowanie jednokrotne wykonaj** następujące czynności:

    ![Włączanie logowania jednokrotnego](./media/pagerduty-tutorial/ic778537.png "Włączanie logowania jednokrotnego")

    a. Otwórz certyfikat zakodowany w bazie podstawowej 64 pobrany z witryny Azure portal w notatniku, skopiuj jego zawartość do schowka, a następnie wklej go do pola tekstowego **certyfikatu X.509**
  
    b. W polu tekstowym **Login URL** (Adres URL logowania) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.
  
    d. W polu tekstowym **Logout URL** (Adres URL wylogowywania) wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    d. Wybierz **pozycję Zezwalaj na logowanie do nazwy użytkownika/hasła**.

    e. Zaznacz pole wyboru **Wymagaj porównania kontekstu uwierzytelniania EXACT.**

    f. Kliknij **pozycję Zapisz zmiany**.

### <a name="create-pagerduty-test-user"></a>Utwórz użytkownika testowego PagerDuty

Aby umożliwić użytkownikom usługi Azure AD zalogować się do PagerDuty, muszą być aprowizowane do PagerDuty. W przypadku PagerDuty inicjowania obsługi administracyjnej jest zadanie ręczne.

> [!NOTE]
> Do aprowizowania kont użytkowników usługi Azure Active Directory można użyć dowolnych innych narzędzi do tworzenia kont użytkowników pagerduty lub interfejsów API udostępnianych przez Pagerduty.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do **dzierżawy Pagerduty.**

2. W menu u góry kliknij pozycję **Użytkownicy**.

3. Kliknij **pozycję Dodaj użytkowników**.
   
    ![Dodaj użytkowników](./media/pagerduty-tutorial/ic778539.png "Dodawanie użytkowników")

4.  W oknie dialogowym **Zaproś zespół** wykonaj następujące czynności:
   
    ![Zaproś swój zespół](./media/pagerduty-tutorial/ic778540.png "Zaproś swój zespół")

    a. Wpisz **imię i nazwisko** użytkownika, takiego jak **B.Simon**. 
   
    b. Wprowadź adres **e-mail** użytkownika, taki jak **\@b.simon contoso.com**.
   
    d. Kliknij **pozycję Dodaj**, a następnie kliknij pozycję Wyślij **zaproszenia**.
   
    > [!NOTE]
    > Wszyscy dodali użytkownicy otrzymają zaproszenie do utworzenia konta PagerDuty.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka PagerDuty w Panelu dostępu należy automatycznie zalogować się do pagera, dla którego skonfigurowano logującą się jednoślik. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj PagerDuty z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić PagerDuty dzięki zaawansowanej widoczności i kontrolom](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

