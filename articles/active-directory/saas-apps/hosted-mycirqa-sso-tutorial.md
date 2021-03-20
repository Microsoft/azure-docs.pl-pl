---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z hostowanym logowaniem jednokrotnym w usłudze MyCirqa | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i hostowanym logowaniem jednokrotnym MyCirqa.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2019
ms.author: jeedes
ms.openlocfilehash: 06918f6d9c62624915be327b61aa6a3ee4e2b12a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92442761"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hosted-mycirqa-sso"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z hostowanym logowaniem jednokrotnym w usłudze MyCirqa

W tym samouczku dowiesz się, jak zintegrować hostowaną usługę SSO MyCirqa z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi hostowanej z logowaniem jednokrotnym MyCirqa w usłudze Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do hostowanej rejestracji jednokrotnej MyCirqa.
* Zezwól użytkownikom na automatyczne logowanie do hostowanego logowania jednokrotnego MyCirqa przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne w usłudze MyCirqa (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Hostowane Logowanie jednokrotne w usłudze MyCirqa 

## <a name="adding-hosted-mycirqa-sso-from-the-gallery"></a>Dodawanie hostowanego logowania jednokrotnego MyCirqa z galerii

Aby skonfigurować integrację hostowanego logowania jednokrotnego MyCirqa z usługą Azure AD, musisz dodać hostowane Logowanie jednokrotne MyCirqa z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Hosted MyCirqa SSO** w polu wyszukiwania.
1. Wybierz pozycję **hostowane logowanie JEDNOkrotne MyCirqa** z poziomu panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hosted-mycirqa-sso"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD na potrzeby hostowanego logowania jednokrotnego MyCirqa

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą hostowanego logowania jednokrotnego MyCirqa przy użyciu użytkownika testowego o nazwie **B. Simon** Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w hostowanej rejestracji jednokrotnej MyCirqa.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą hostowanej rejestracji jednokrotnej MyCirqa, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj hostowane logowanie JEDNOkrotne](#configure-hosted-mycirqa-sso)** w usłudze MyCirqa, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego logowania jednokrotnego](#create-hosted-mycirqa-sso-test-user)** w usłudze MyCirqa, aby dysponować odpowiednikiem B. Simon w hostowanej usłudze SSO MyCirqa, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **hostowana integracja aplikacji MyCirqa SSO** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.cirqahosting.com/CirqaIdentity/external?`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://isoxford.com/<CUSTOMID>/cirqaidentity/saml2`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta rejestracji jednokrotnej MyCirqa](not sure) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do hostowanego logowania jednokrotnego MyCirqa.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **hostowane logowanie JEDNOkrotne** w usłudze MyCirqa.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-hosted-mycirqa-sso"></a>Konfigurowanie hostowanego logowania jednokrotnego MyCirqa

Aby skonfigurować Logowanie jednokrotne na **hostowanej stronie logowania jednokrotnego MyCirqa** , musisz wysłać **adres URL metadanych federacji aplikacji** do [hostowanego zespołu obsługi rejestracji jednokrotnej usługi MyCirqa](mailto:support@isoxford.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

## <a name="create-hosted-mycirqa-sso-test-user"></a>Utwórz użytkownika testowego hostowanej MyCirqa rejestracji jednokrotnej

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w hostowanym logowaniu jednokrotnym MyCirqa. Aby dodać użytkowników z hostowanej platformy rejestracji jednokrotnej w usłudze MyCirqa, Pracuj z [hostowanym zespołem obsługi rejestracji jednokrotnej MyCirqa](mailto:support@isoxford.com) . Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Logowanie jednokrotne MyCirqa w panelu dostępu należy automatycznie zalogować się do hostowanego logowania jednokrotnego MyCirqa, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj hostowane Logowanie jednokrotne MyCirqa z usługą Azure AD](https://aad.portal.azure.com/)