---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu nowego Relic | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i nowymi Relic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: 4cf3f9d0ae23bab4d2412b47e5841d6b8a56b65a
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327070"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Samouczek: integracja logowania jednokrotnego (SSO) Azure Active Directory z nowymi Relic

W tym samouczku dowiesz się, jak zintegrować nowe Relic z usługą Azure Active Directory (Azure AD). Po zintegrowaniu nowego Relic z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do nowego Relic.
* Zezwól użytkownikom na automatyczne logowanie do nowego Relic przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) usługi Relic.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa New Relic obsługuje funkcję SSO zainicjowaną przez usługę **SP i dostawcy tożsamości** .
* Po skonfigurowaniu nowego Relic można wymusić kontrolę sesji, która chroni przed eksfiltracji i niefiltrowaniem poufnych danych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-new-relic-application-from-the-gallery"></a>Dodaj nową aplikację Relic z galerii

Aby skonfigurować integrację nowych Relic z usługą Azure AD, musisz dodać **nową Relic (przez organizację)** z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. Wybierz usługę **Azure Active Directory** .
1. Wybierz pozycję **aplikacje dla przedsiębiorstw**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. Na stronie **przeglądanie galerii usługi Azure AD** wpisz w polu wyszukiwania pozycję **New Relic (według organizacji)** .
1. Wybierz pozycję **Nowy Relic (według organizacji)** w panelu wyników, a następnie wybierz pozycję **Utwórz**. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla nowego Relic

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą nowego Relic przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w nowym Relic.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą nowego Relic, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
   1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
   1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj nowe logowanie JEDNOkrotne](#configure-new-relic-sso)** w usłudze Relic, aby skonfigurować ustawienia logowania jednokrotnego na nowej stronie Relic.
   1. **[Utwórz nowego użytkownika testowego Relic](#create-a-new-relic-test-user)** , aby dysponować odpowiednikiem B. Simon w nowej Relic, która jest połączona z użytkownikiem usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **nowe Relic przez** integrację aplikacji organizacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.

1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.

1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości w polu **Identyfikator** i **adres URL odpowiedzi**.

   * Te wartości są pobierane przy użyciu nowej aplikacji Relic **My Organization** . Aby użyć tej aplikacji, wykonaj następujące kroki:
      1. [Zaloguj](https://login.newrelic.com/) się do nowego Relic.
      1. W górnym menu wybierz pozycję **aplikacje**.
      1. W sekcji **aplikacje** wybierz pozycję **Moja organizacja**.
      1. Kliknij pozycję **domeny uwierzytelniania**.
      1. Wybierz domenę uwierzytelniania, do której ma nawiązać połączenie Logowanie jednokrotne w usłudze Azure AD (Jeśli masz więcej niż jedną domenę uwierzytelniania). Większość firm ma tylko jedną domenę uwierzytelniania o nazwie **default**. Tylko w przypadku jednej domeny uwierzytelniania nie trzeba wybierać.
      1. W sekcji **uwierzytelnianie** **adres URL odbiorcy potwierdzenia** zawiera wartość używaną dla **adresu URL odpowiedzi**.
      1. W sekcji **uwierzytelnianie** **nasz identyfikator jednostki** zawiera wartość do użycia dla **identyfikatora**.

1. W sekcji **atrybuty użytkownika & oświadczenia** upewnij się, że **unikatowy identyfikator użytkownika** jest mapowany na pole zawierające adres E-mail używany w nowym Relic.

   * Pole domyślne **User. userPrincipalName** będzie działało, jeśli wartości są takie same jak nowe adresy e-mail Relic.
   * Pole  **User. mail** może być bardziej odpowiednie dla Ciebie, jeśli **użytkownik. userPrincipalName** nie jest nowym adresem e-mail Relic.

1. W sekcji **certyfikat podpisywania SAML** Skopiuj **adres URL metadanych federacji aplikacji** i Zapisz go jako wartość do późniejszego użycia.

1. W sekcji **Konfigurowanie nowej Relic według organizacji** Skopiuj **adres URL logowania** i Zapisz go jako wartość do późniejszego użycia.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W Azure Portal wybierz pozycję Usługa **Azure Active Directory**
1. Wybierz pozycję **Użytkownicy**.
1. Aby dodać nowego użytkownika, wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. Na stronie **nowy użytkownik** wykonaj następujące kroki:
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `b.simon@contoso.com`. Ta wartość powinna być zgodna z adresem e-mail, który będzie używany na nowej stronie Relic.
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. Zaznacz pole wyboru **Pokaż hasło** , a następnie Zapisz wartość, która jest wyświetlana w polu **początkowe hasło** .
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego w usłudze Azure AD, przyznając dostęp do **nowej Relic przez aplikację organizacji** .

1. W Azure Portal wybierz pozycję Usługa **Azure Active Directory**
1. Wybierz pozycję **aplikacje dla przedsiębiorstw**.
1. Na liście Aplikacje wybierz pozycję **Nowy Relic według organizacji**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** (lub **użytkowników** w zależności od poziomu planu) w oknie dialogowym **Dodaj przypisanie** .

   ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** (lub **Użytkownicy**) wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-new-relic-sso"></a>Konfigurowanie nowego logowania jednokrotnego Relic

Wykonaj następujące kroki, aby skonfigurować Logowanie jednokrotne przy nowym Relic.

1. [Zaloguj](https://login.newrelic.com/) się do nowego Relic.

1. W górnym menu wybierz pozycję **aplikacje**.

1. W sekcji **aplikacje** wybierz pozycję **Moja organizacja**.

1. Kliknij pozycję **domeny uwierzytelniania**.

1. Wybierz domenę uwierzytelniania, do której ma nawiązać połączenie Logowanie jednokrotne w usłudze Azure AD (Jeśli masz więcej niż jedną domenę uwierzytelniania). Większość firm ma tylko jedną domenę uwierzytelniania o nazwie **default**. Tylko w przypadku jednej domeny uwierzytelniania nie trzeba wybierać.

1. W sekcji **uwierzytelnianie** kliknij przycisk **Konfiguruj**.

   1. W polu **Źródło metadanych SAML** wprowadź wartość zapisaną wcześniej w polu **adres URL metadanych federacji aplikacji** usługi Azure AD.

   1. W polu **adres URL logowania jednokrotnego** wprowadź wartość, która została wcześniej zapisana, w polu **adres URL** usługi Azure Active Directory.

   1. Kliknij przycisk **Zapisz** po zweryfikowaniu ustawień sprawdź, czy w usłudze Azure AD i nowych stronach Relic. Jeśli obie strony nie są poprawnie skonfigurowane, użytkownicy nie będą mogli zalogować się do nowego Relic.

### <a name="create-a-new-relic-test-user"></a>Utwórz nowego użytkownika testowego Relic

W tej sekcji utworzysz użytkownika o nazwie B. Simon w nowym Relic. Wykonaj następujące kroki, aby utworzyć użytkownika.

1. [Zaloguj](https://login.newrelic.com/) się do nowego Relic.

1. W górnym menu wybierz pozycję **aplikacje**.

1. W sekcji **aplikacje** wybierz pozycję **Zarządzanie użytkownikami**.

1. Kliknij przycisk **Dodaj użytkownika** .

   1. W polu **Nazwa** wpisz **B. Simon**.
   
   1. W polu **adres e-mail** wprowadź wartość, która zostanie wysłana przez logowanie jednokrotne usługi Azure AD.
   
   1. Wybierz **Typ** użytkownika i **grupę** użytkowników dla użytkownika. Dla użytkownika testowego **użytkownik podstawowy** dla typu i **użytkownika** dla grupy jest rozsądnie wybierany.
   
   1. Kliknij pozycję **Dodaj użytkownika** , aby zapisać użytkownika.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka **Nowy Relic według organizacji** w panelu dostępu należy automatycznie zalogować się do nowej Relic. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj nowy Relic z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
