---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Cloud Academy — Logowanie jednokrotne | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Cloud Academy — Logowanie jednokrotne.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 560c86fb-e25c-4428-a1dd-a5711cfece5c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb118e4d2fa811bf88ff13db84848ebb230ed209
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294799"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloud-academy---sso"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Cloud Academy — Logowanie jednokrotne

W tym samouczku dowiesz się, jak zintegrować usługę Cloud Academy — Logowanie jednokrotne z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Cloud Academy — Logowanie jednokrotne w usłudze Azure AD pozwala:

* Kontrolka w usłudze Azure AD, która ma dostęp do usługi Cloud Academy — Logowanie jednokrotne.
* Zezwól użytkownikom na automatyczne logowanie do usługi Cloud Academy — Logowanie jednokrotne przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Cloud Academy — subskrypcja z włączonym logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Cloud Academy — Logowanie jednokrotne **obsługuje** zainicjowanie rejestracji jednokrotnej

* Po skonfigurowaniu usługi Cloud Academy — Logowanie jednokrotne umożliwia wymuszenie kontroli sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-cloud-academy---sso-from-the-gallery"></a>Dodawanie usługi Cloud Academy — Logowanie jednokrotne z galerii

Aby skonfigurować integrację programu Cloud Academy — Logowanie jednokrotne do usługi Azure AD, musisz dodać chmurę Cloud Academy — Logowanie jednokrotne z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Cloud Academy-SSO** w polu wyszukiwania.
1. Wybierz pozycję **Cloud Academy — logowanie JEDNOkrotne** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-cloud-academy---sso"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD dla chmury Cloud Academy — Logowanie jednokrotne

Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Cloud Academy — SSO przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Cloud Academy — Logowanie jednokrotne.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Cloud Academy — Logowanie jednokrotne, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Konfigurowanie usługi Cloud Academy — logowanie JEDNOkrotne](#configure-cloud-academy-sso-sso)** SSO — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Tworzenie usługi Cloud Academy — logowanie JEDNOkrotne do użytkownika testowego](#create-cloud-academy-sso-test-user)** — aby dysponować odpowiednikiem B. Simon w chmurze Academy — Logowanie jednokrotne jest połączone z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z usługą **Cloud Academy — Logowanie jednokrotne** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://cloudacademy.com/login/enterprise/`

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do chmury w chmurze — Logowanie jednokrotne.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Cloud Academy — logowanie JEDNOkrotne**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-cloud-academy-sso-sso"></a>Konfigurowanie usługi Cloud Academy — Logowanie jednokrotne SSO

1. W innym oknie przeglądarki Zaloguj się do witryny firmowej usługi Cloud Academy — Logowanie jednokrotne jako administrator.

1. Kliknij nazwę firmy i wybierz pozycję **ustawienia & integracji** z menu.

    ![Konfigurowanie ](./media/cloud-academy-sso-tutorial/config-1.PNG)

1. Na stronie **ustawienia & integracji** przejdź do karty **integracje** i kliknij kartę **Logowanie jednokrotne** .

    ![Konfigurowanie ](./media/cloud-academy-sso-tutorial/config-2.PNG)

1. Wykonaj następujące czynności na poniższej stronie:

    ![Konfigurowanie ](./media/cloud-academy-sso-tutorial/config-3.PNG)

    a. W polu tekstowym **adres URL identyfikatora jednostki** wklej wartość **identyfikatora jednostki** , która została skopiowana z Azure Portal.

    b. W polu tekstowym **adres URL logowania jednokrotnego** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    c. Otwórz pobrany **certyfikat (base64)** z Azure Portal do Notatnika i wklej zawartość do pola tekstowego **certyfikatu** .

    d. W polu tekstowym **Format identyfikatora nazwy** wartość domyślna,`urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`

1. Kliknij przycisk **Zapisz** .

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat konfigurowania usługi Cloud Academy — Logowanie jednokrotne, należy zapoznać się z [artykułem dotyczącym pomocy technicznej](https://support.cloudacademy.com/hc/articles/360043908452-Setting-Up-Single-Sign-On).

### <a name="create-cloud-academy-sso-test-user"></a>Tworzenie usługi Cloud Academy — Użytkownik testowy rejestracji jednokrotnej

1. Zaloguj się do usługi **Cloud Academy — logowanie JEDNOkrotne** .

1. Kliknij nazwę firmy i wybierz pozycję **elementy członkowskie** z menu.

    ![ Utwórz użytkownika testowego ](./media/cloud-academy-sso-tutorial/create-user.PNG)

1. Kliknij pozycję **Zaproś członków** i wybierz pozycję **Zaproś jednego członka**.

    ![ Utwórz użytkownika testowego ](./media/cloud-academy-sso-tutorial/create-user-1.PNG)

1. Wprowadź wymagane pola i kliknij pozycję **Zaproś**.

    ![ Utwórz użytkownika testowego ](./media/cloud-academy-sso-tutorial/create-user-2.PNG)

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Cloud Academy — Logowanie jednokrotne w panelu dostępu należy automatycznie zalogować się do usługi Cloud Academy — Logowanie jednokrotne, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę Cloud Academy — Logowanie jednokrotne w usłudze Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić usługi Cloud Academy — Logowanie jednokrotne z zaawansowaną widocznością i kontrolkami](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)