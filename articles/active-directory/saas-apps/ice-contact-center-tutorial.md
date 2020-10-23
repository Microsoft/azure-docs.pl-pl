---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z centrum kontaktów w postaci lodu | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory a centrum kontaktów lodu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/15/2020
ms.author: jeedes
ms.openlocfilehash: 2d67e960f89954cebfd67118fd435fea8e30caf7
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460482"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ice-contact-center"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z centrum kontaktów w postaci lodu

W ramach tego samouczka dowiesz się, jak zintegrować z usługą Azure Active Directory (Azure AD). Po zintegrowaniu z usługą Azure AD centrum kontaktów w postaci lodu można:

* Kontrolka w usłudze Azure AD, która ma dostęp do centrum kontaktów w postaci lodu.
* Zezwól użytkownikom na automatyczne logowanie do centrum kontaktów w postaci lodu z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* subskrypcja z włączoną obsługą logowania jednokrotnego (SSO) w centrum kontaktów.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* w centrum kontaktów lodu obsługiwane jest logowanie jednokrotne zainicjowane przez usługę **SP**
* Po skonfigurowaniu centrum kontaktów lodu można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-ice-contact-center-from-the-gallery"></a>Dodawanie centrum kontaktów lodu z galerii

Aby skonfigurować integrację z centrum kontaktów lodu z usługą Azure AD, musisz dodać do swojej listy zarządzane aplikacje SaaS centrum kontaktowe z galerii.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania pozycję **centrum kontaktów** w postaci lodu.
1. Wybierz pozycję **centrum kontaktów lodu** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ice-contact-center"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla centrum kontaktów w postaci lodu

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą centrum kontaktów lodu przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w centrum kontaktów w postaci lodu.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą centrum kontaktów lodu, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj logowanie](#configure-ice-contact-center-sso)** jednokrotne w centrum kontaktów w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego centrum kontaktów](#create-ice-contact-center-test-user)** , aby dysponować odpowiednikiem B. Simon w centrum kontaktów w postaci lodu, które jest połączone z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **centrum kontaktów** w ramach programu zapoznaj się z sekcją **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<TENANT>.iceuc.com/iceManager`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL przy użyciu jednego z następujących wzorców:

    ```http
    https://<TENANT>-imrpool.icescape365.com:PORT/identity
    https://<TENANT>-imrpool.icescape.com:PORT/identity
    https://<TENANT>-imrpool.iceuc.com:PORT/identity
    ```

    c. W polu tekstowym **adres URL odpowiedzi** wpisz adres URL przy użyciu jednego z następujących wzorców:

    ```http
    https://<TENANT>-imrpool.icescape365.com:PORT/identity
    https://<TENANT>-imrpool.icescape.com:PORT/identity
    https://<TENANT>-imrpool.iceuc.com:PORT/identity
    ```

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej w centrum kontaktów](mailto:support@computer-talk.com) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do centrum kontaktów w postaci lodu.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **centrum kontaktów**w postaci lodu.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-ice-contact-center-sso"></a>Konfigurowanie logowania jednokrotnego w centrum kontaktów

Aby skonfigurować Logowanie jednokrotne na stronie **centrum kontaktów** w postaci lodu, musisz wysłać **adres URL metadanych federacji aplikacji** do [zespołu pomocy technicznej centrum kontaktów](mailto:support@computer-talk.com)w ramach usług. Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-ice-contact-center-test-user"></a>Utwórz użytkownika testowego centrum kontaktów

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w centrum kontaktów w postaci lodu. Współpraca z [zespołem pomocy technicznej z centrum kontaktów lodu](mailto:support@computer-talk.com) pozwala dodawać użytkowników na platformie centrum kontaktów w postaci lodu. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka z centrum kontaktów lodu w panelu dostępu należy automatycznie zalogować się do centrum kontaktów lodu, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj centrum kontaktów w postaci lodu z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chronić międzyfirmowe centrum kontaktowe z zaawansowaną widocznością i kontrolkami](/cloud-app-security/proxy-intro-aad)