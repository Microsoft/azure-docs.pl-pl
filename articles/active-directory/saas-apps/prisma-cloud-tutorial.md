---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu logowania jednokrotnego w chmurze Prisma Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i logowaniem jednokrotnym w chmurze Prisma.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/07/2020
ms.author: jeedes
ms.openlocfilehash: 0a5caf3a52f388bceeec76c411b0bda21669baf2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515274"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-prisma-cloud-sso"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu logowania jednokrotnego w chmurze Prisma

W tym samouczku dowiesz się, jak zintegrować Logowanie jednokrotne w chmurze Prisma z usługą Azure Active Directory (Azure AD). W przypadku integrowania logowania jednokrotnego w chmurze Prisma z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do logowania jednokrotnego w chmurze Prisma.
* Zezwól użytkownikom na automatyczne logowanie do Prisma logowania jednokrotnego w chmurze przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne w chmurze Prisma (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Logowanie jednokrotne w chmurze Prisma obsługuje **dostawcy tożsamości** zainicjowane Logowanie jednokrotne

* Logowanie jednokrotne w chmurze Prisma obsługuje Inicjowanie obsługi użytkowników **just in Time**

* Po skonfigurowaniu logowania jednokrotnego w chmurze Prisma można wymusić kontrolki sesji, które chronią eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji wykraczają poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-prisma-cloud-sso-from-the-gallery"></a>Dodawanie logowania jednokrotnego w chmurze Prisma z galerii

Aby skonfigurować integrację logowania jednokrotnego w chmurze Prisma w usłudze Azure AD, musisz dodać Logowanie jednokrotne w chmurze Prisma z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Prisma w chmurze Logowanie jednokrotne** w polu wyszukiwania.
1. Wybierz pozycję **Prisma Logowanie jednokrotne w chmurze** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-prisma-cloud-sso"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD na potrzeby logowania jednokrotnego w chmurze Prisma

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego w chmurze Prisma przy użyciu użytkownika testowego o nazwie **B. Simon** Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Prisma Cloud SSO.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu logowania jednokrotnego w chmurze Prisma, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne w chmurze Prisma](#configure-prisma-cloud-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego logowania jednokrotnego w chmurze Prisma](#create-prisma-cloud-sso-test-user)** , aby dysponować odpowiednikiem B. Simon w usłudze Prisma Cloud SSO, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **logowania jednokrotnego w chmurze Prisma** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://app2.prismacloud.io/customer/<CUSTOMERID>`

    b. Wartości **adresu URL odpowiedzi** są stałe i są już wstępnie wypełnione w Azure Portal. Musisz wybrać odpowiedni adres URL zgodnie z wymaganiami.

    > [!NOTE]
    > Wartość identyfikatora nie jest prawdziwa. Zaktualizuj wartość za pomocą rzeczywistego identyfikatora. Skontaktuj się z [zespołem obsługi klienta rejestracji jednokrotnej w chmurze Prisma](mailto:support@paloaltonetworks.com) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie logowania jednokrotnego w chmurze Prisma** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Prisma Cloud SSO.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Prisma Cloud SSO**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-prisma-cloud-sso"></a>Konfigurowanie logowania jednokrotnego w chmurze Prisma

Aby skonfigurować Logowanie jednokrotne na stronie logowania **JEDNOkrotnego w chmurze Prisma** , należy wysłać pobrany **certyfikat (base64)** i odpowiednie skopiowane adresy URL z usługi Azure Portal do [Prisma chmury obsługi logowania jednokrotnego w chmurze](mailto:support@paloaltonetworks.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-prisma-cloud-sso-test-user"></a>Utwórz użytkownika testowego logowania jednokrotnego w chmurze Prisma

W tej sekcji użytkownik o nazwie B. Simon jest tworzony w usłudze Prisma w chmurze logowania jednokrotnego. Logowanie jednokrotne w chmurze Prisma obsługuje Inicjowanie obsługi just-in-Time, które jest domyślnie włączone. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze Prisma w chmurze Logowanie jednokrotne, zostanie utworzona nowa usługa podczas próby dostępu do usługi Prisma w chmurze logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Logowanie jednokrotne w chmurze Prisma w panelu dostępu należy automatycznie zalogować się do usługi Prisma w chmurze logowania jednokrotnego, dla którego skonfigurowano logowanie jednozarejestrowane. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Wypróbuj Logowanie jednokrotne w chmurze Prisma w usłudze Azure AD](https://aad.portal.azure.com/)