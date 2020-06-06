---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Helix Core-Helix Authentication | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między usługami Azure Active Directory i Helix Core-Helix Authentication.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 70625d03-2156-4dea-828b-0af251e12db9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: afe752dbaffc96186d37bfae319367e2c6e13385
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2020
ms.locfileid: "84466157"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-helix-core---helix-authentication-service"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Helix Core-Helix Authentication

W tym samouczku dowiesz się, jak zintegrować usługę uwierzytelniania Helix Core-Helix z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi uwierzytelniania Helix Core-Helix z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do usługi uwierzytelniania Helix Core-Helix.
* Zezwól użytkownikom na automatyczne logowanie do usługi uwierzytelniania Helix Core-Helix przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Helix Core-Helix uwierzytelnianie logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Helix Core — usługa uwierzytelniania Helix obsługuje logowanie jednokrotne w usłudze **SP**
* Po skonfigurowaniu usługi uwierzytelniania Helix Core-Helix można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych w organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-helix-core---helix-authentication-service-from-the-gallery"></a>Dodawanie usługi uwierzytelniania Helix Core-Helix z galerii

Aby skonfigurować integrację usługi uwierzytelniania Helix Core-Helix w usłudze Azure AD, musisz dodać usługę uwierzytelniania Helix Core-Helix z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Helix Core-Helix Authentication** w polu wyszukiwania.
1. Wybierz pozycję **Usługa uwierzytelniania Helix Core-Helix** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-helix-core---helix-authentication-service"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla usługi uwierzytelniania Helix Core-Helix

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą usługi uwierzytelniania Helix Core-Helix przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Helix Core-Helix Authentication.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą usługi uwierzytelniania Helix Core-Helix, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj usługę uwierzytelniania Helix Core-Helix](#configure-helix-core-helix-authentication-service-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego usługi Helix Core-Helix](#create-helix-core-helix-authentication-service-test-user)** , aby uzyskać odpowiedni odpowiednik B. Simon w usłudze uwierzytelniania Helix Core-Helix, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **usługi uwierzytelniania Helix Core-Helix** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<helix-auth-service>.<customer-hostname>.com/`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<helix-auth-service>.<customer-hostname>.com/saml`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta usługi uwierzytelniania Helix Core-Helix](mailto:support@perforce.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

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
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji zostanie włączone Logowanie jednokrotne w usłudze B. Simon, przyznając dostęp do usługi uwierzytelniania Helix Core-Helix.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Helix Core-Helix Authentication**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-helix-core-helix-authentication-service-sso"></a>Konfigurowanie usługi uwierzytelniania Helix Core-Helix — Logowanie jednokrotne

Aby skonfigurować Logowanie jednokrotne na stronie **usługi uwierzytelniania Helix Core-Helix** , należy wysłać **adres URL metadanych federacji aplikacji** do [zespołu usługi uwierzytelniania Helix Core-Helix](mailto:support@perforce.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-helix-core-helix-authentication-service-test-user"></a>Tworzenie użytkownika testowego usługi uwierzytelniania Helix Core-Helix

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w usłudze uwierzytelniania Helix Core-Helix. Współpracuj z [zespołem pomocy technicznej usługi Helix Core-Helix](mailto:support@perforce.com) , aby dodać użytkowników z platformy usługi uwierzytelniania Helix Core-Helix. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi uwierzytelniania Helix Core-Helix w panelu dostępu należy automatycznie zalogować się do usługi uwierzytelniania Helix Core-Helix, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę uwierzytelniania Helix Core-Helix z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić usługę uwierzytelniania Helix Core-Helix z zaawansowaną widocznością i kontrolkami](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

