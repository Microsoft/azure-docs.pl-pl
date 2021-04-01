---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu usługi uwierzytelniania Perforce Helix Core-Helix | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między usługami Azure Active Directory i Perforce Helix Core-Helix.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: jeedes
ms.openlocfilehash: c5958b46c36623cf9f409927a9eaa6aa50966c45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92512025"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-perforce-helix-core---helix-authentication-service"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) za pomocą usługi uwierzytelniania Perforce Helix Core-Helix

W tym samouczku dowiesz się, jak zintegrować usługę uwierzytelniania Perforce Helix Core-Helix z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi uwierzytelniania Perforce Helix Core-Helix z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do usługi uwierzytelniania Perforce Helix Core-Helix.
* Zezwól użytkownikom na automatyczne logowanie do usługi uwierzytelniania Perforce Helix Core-Helix przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Perforce Helix Core — subskrypcja z włączonym logowaniem jednokrotnym (SSO) usługi uwierzytelniania Helix.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Perforce Helix Core-Helix uwierzytelniania obsługuje logowanie jednokrotne w usłudze **SP**
* Po skonfigurowaniu usługi uwierzytelniania Perforce Helix Core-Helix można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-perforce-helix-core---helix-authentication-service-from-the-gallery"></a>Dodawanie usługi uwierzytelniania Perforce Helix Core-Helix z galerii

Aby skonfigurować integrację usługi uwierzytelniania Perforce Helix Core-Helix z usługą Azure AD, musisz dodać usługę uwierzytelniania Perforce Helix Core-Helix z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Perforce Helix Core-Helix Authentication** w polu wyszukiwania.
1. Wybierz pozycję **Perforce Helix Core-Helix Authentication Service** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-perforce-helix-core---helix-authentication-service"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla usługi uwierzytelniania Perforce Helix Core-Helix

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą usługi Perforce Helix Core-Helix Authentication przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Perforce Helix Core-Helix Authentication.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą usługi uwierzytelniania Perforce Helix Core-Helix, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. Konfigurowanie logowania jednokrotnego w **[usłudze Perforce Helix Core](#configure-perforce-helix-core---helix-authentication-service-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego usługi Perforce Helix Core-Helix](#create-perforce-helix-core---helix-authentication-service-test-user)** , aby uzyskać odpowiednik B. Simon w Perforce Helix Core-Helix usługi uwierzytelniania, która jest połączona z reprezentacją usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **usługi uwierzytelniania Perforce Helix Core-Helix** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<helix-auth-service>.<customer-hostname>.com/`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<helix-auth-service>.<customer-hostname>.com/saml`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<HELIX-AUTH-SERVICE>.<CUSTOMER-HOSTNAME>.com/saml/sso`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta usługi uwierzytelniania Perforce Helix Core-Helix](mailto:support@perforce.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

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

W tej sekcji zostanie włączone Logowanie jednokrotne w usłudze B. Simon, przyznając dostęp do usługi uwierzytelniania Perforce Helix Core-Helix.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Perforce Helix Core-Helix Authentication Service**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-perforce-helix-core---helix-authentication-service-sso"></a>Konfigurowanie usługi uwierzytelniania Perforce Helix Core-Helix

Aby skonfigurować Logowanie jednokrotne na stronie **usługi uwierzytelniania Perforce Helix Core-Helix** , musisz wysłać **adres URL metadanych federacji aplikacji** do [Perforce Helix Core-Helix](mailto:support@perforce.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-perforce-helix-core---helix-authentication-service-test-user"></a>Utwórz użytkownika testowego usługi Perforce Helix Core-Helix

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w usłudze uwierzytelniania Perforce Helix Core-Helix. Współpracuj z [zespołem pomocy technicznej usługi Perforce Helix Core-Helix](mailto:support@perforce.com) , aby dodać użytkowników do platformy usługi uwierzytelniania Perforce Helix Core-Helix. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi uwierzytelniania Perforce Helix Core-Helix w panelu dostępu należy automatycznie zalogować się do usługi uwierzytelniania Perforce Helix Core-Helix, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj usługę uwierzytelniania Perforce Helix Core-Helix z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chronić usługę uwierzytelniania Perforce Helix Core-Helix za pomocą zaawansowanej widoczności i kontroli](/cloud-app-security/proxy-intro-aad)