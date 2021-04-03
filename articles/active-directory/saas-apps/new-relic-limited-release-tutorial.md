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
ms.openlocfilehash: 29e19eea51b5ee55831bf1d694a9a6473a62d471
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97504053"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Samouczek: integracja logowania jednokrotnego (SSO) Azure Active Directory z nowymi Relic

W tym samouczku dowiesz się, jak zintegrować nowe Relic z usługą Azure Active Directory (Azure AD). Po zintegrowaniu nowego Relic z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do nowego Relic.
* Zezwól użytkownikom na automatyczne logowanie do nowego Relic przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji oprogramowania jako usługi (SaaS) z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne będą następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Nowa subskrypcja Relic, dla której włączono Logowanie jednokrotne (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Nowy Relic obsługuje logowanie jednokrotne zainicjowane przez dostawcę usług lub dostawcę tożsamości.
* Po skonfigurowaniu nowych Relic można wymusić kontrolę sesji, która chroni przed eksfiltracji i niefiltrowaniem poufnych danych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-new-relic-from-the-gallery"></a>Dodawanie nowego Relic z galerii

Aby skonfigurować integrację nowych Relic z usługą Azure AD, musisz dodać **nową Relic (przez organizację)** z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. Wybierz usługę **Azure Active Directory** .
1. Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **Nowa aplikacja**.
1. Na stronie **przeglądanie galerii usługi Azure AD** wpisz w polu wyszukiwania pozycję **New Relic (według organizacji)** .
1. Wybierz pozycję **Nowy Relic (według organizacji)** z wyników, a następnie wybierz pozycję **Utwórz**. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla nowego Relic

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą nowego Relic przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy nawiązać relację między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w nowym Relic.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu nowego Relic:

1. [Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso) , aby umożliwić użytkownikom korzystanie z tej funkcji.
   1. [Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
   1. [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. [Skonfiguruj nowe Logowanie jednokrotne](#configure-new-relic-sso) w usłudze Relic, aby skonfigurować ustawienia logowania jednokrotnego na nowej stronie Relic.
   1. [Utwórz nowego użytkownika testowego Relic](#create-a-new-relic-test-user) w celu uzyskania odpowiednika dla B. Simon w nowym Relic połączonym z użytkownikiem usługi Azure AD.
1. [Przetestuj Logowanie jednokrotne](#test-sso) , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **nowe Relic przez** integrację aplikacji organizacji Znajdź sekcję **Zarządzanie** . Następnie wybierz pozycję **Logowanie jednokrotne**.

1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.

1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Zrzut ekranu przedstawiający Konfigurowanie jednego Sign-On za pomocą języka SAML z wyróżnioną ikoną ołówka.](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości w polu **Identyfikator** i **adres URL odpowiedzi**.

   * Pobierz te wartości przy użyciu nowej aplikacji Relic **My Organization** . Aby użyć tej aplikacji:
      1. [Zaloguj](https://login.newrelic.com/) się do nowego Relic.
      1. W górnym menu wybierz pozycję **aplikacje**.
      1. W sekcji **aplikacje** wybierz pozycję **Moja organizacja**  >  **domeny uwierzytelniania**.
      1. Wybierz domenę uwierzytelniania, do której ma nawiązać połączenie Logowanie jednokrotne w usłudze Azure AD (Jeśli masz więcej niż jedną domenę uwierzytelniania). Większość firm ma tylko jedną domenę uwierzytelniania o nazwie **default**. Jeśli istnieje tylko jedna domena uwierzytelniania, nie musisz wybierać żadnych elementów.
      1. W sekcji **uwierzytelnianie** **adres URL odbiorcy potwierdzenia** zawiera wartość używaną dla **adresu URL odpowiedzi**.
      1. W sekcji **uwierzytelnianie** **nasz identyfikator jednostki** zawiera wartość do użycia dla **identyfikatora**.

1. W sekcji **atrybuty użytkownika & oświadczenia** upewnij się, że **unikatowy identyfikator użytkownika** jest mapowany do pola, które zawiera adres E-mail używany w nowym Relic.

   * Pole domyślne **User. userPrincipalName** będzie działało, jeśli jego wartości są takie same jak nowe adresy e-mail Relic.
   * Pole  **User. mail** może być bardziej odpowiednie dla Ciebie, jeśli **użytkownik. userPrincipalName** nie jest nowym adresem e-mail Relic.

1. W sekcji **certyfikat podpisywania SAML** Skopiuj **adres URL metadanych federacji aplikacji** i Zapisz jego wartość do późniejszego użycia.

1. W sekcji **Konfigurowanie nowej Relic według organizacji** Skopiuj **adres URL logowania** i Zapisz jego wartość do późniejszego użycia.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Poniżej przedstawiono sposób tworzenia użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W Azure Portal wybierz pozycję **Azure Active Directory**.
1. Wybierz pozycję **Użytkownicy**  >  **nowy użytkownik**.
1. Na stronie **nowy użytkownik** :
   1. W polu **Nazwa użytkownika** wprowadź wartość `username@companydomain.extension` . Na przykład `b.simon@contoso.com`. Ta wartość powinna być zgodna z adresem e-mail, który będzie używany na nowej stronie Relic.
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. Wybierz pozycję **Pokaż hasło**, a następnie Zapisz wartość, która jest wyświetlana.
   1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Poniżej przedstawiono sposób włączania logowania jednokrotnego w usłudze Azure AD przez przyznanie dostępu do nowej Relic przez aplikację organizacji.

1. W Azure Portal wybierz pozycję **Azure Active Directory**.
1. Wybierz pozycję **aplikacje**  >  **dla przedsiębiorstw nowe Relic według organizacji**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Zrzut ekranu przedstawiający sekcję Zarządzanie z wyróżnionymi użytkownikami i grupami.](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**. W obszarze **Dodaj przypisanie** wybierz pozycję **Użytkownicy i grupy** (lub **użytkowników**, w zależności od poziomu planu).

   ![Zrzut ekranu przedstawiający opcję Dodaj użytkownika.](common/add-assign-user.png)

1. W obszarze **Użytkownicy i grupy** (lub **Użytkownicy**) wybierz pozycję **B. Simon** z listy **Użytkownicy** , a następnie wybierz **pozycję Wybierz** w dolnej części ekranu.
1. W obszarze **Dodaj przypisanie** wybierz pozycję **Przypisz**.

## <a name="configure-new-relic-sso"></a>Konfigurowanie nowego logowania jednokrotnego Relic

Wykonaj następujące kroki, aby skonfigurować Logowanie jednokrotne przy nowym Relic.

1. [Zaloguj](https://login.newrelic.com/) się do nowego Relic.

1. W górnym menu wybierz pozycję **aplikacje**.

1. W sekcji **aplikacje** wybierz pozycję **Moja organizacja**  >  **domeny uwierzytelniania**.

1. Wybierz domenę uwierzytelniania, do której ma nawiązać połączenie Logowanie jednokrotne w usłudze Azure AD (Jeśli masz więcej niż jedną domenę uwierzytelniania). Większość firm ma tylko jedną domenę uwierzytelniania o nazwie **default**. Jeśli istnieje tylko jedna domena uwierzytelniania, nie musisz wybierać żadnych elementów.

1. W sekcji **uwierzytelnianie** wybierz pozycję **Konfiguruj**.

   1. Dla **źródła metadanych SAML** wprowadź wartość, która została wcześniej zapisana z pola **adres URL metadanych federacji aplikacji** usługi Azure AD.

   1. W polu **adres URL celu logowania jednokrotnego** wprowadź wartość, która została wcześniej zapisana z pola **adresu URL logowania** do usługi Azure AD.

   1. Po sprawdzeniu, czy ustawienia wyglądają dobrze zarówno w usłudze Azure AD, jak i na nowych stronach Relic, wybierz pozycję **Zapisz**. Jeśli obie strony nie są poprawnie skonfigurowane, użytkownicy nie będą mogli zalogować się do nowego Relic.

### <a name="create-a-new-relic-test-user"></a>Utwórz nowego użytkownika testowego Relic

W tej sekcji utworzysz użytkownika o nazwie B. Simon w nowym Relic.

1. [Zaloguj](https://login.newrelic.com/) się do nowego Relic.

1. W górnym menu wybierz pozycję **aplikacje**.

1. W sekcji **aplikacje** wybierz pozycję **Zarządzanie użytkownikami**.

1. Wybierz pozycję **Dodaj użytkownika**.

   1. W obszarze **Nazwa** wpisz **B. Simon**.
   
   1. W polu **adres e-mail** wprowadź wartość, która zostanie wysłana przez logowanie jednokrotne usługi Azure AD.
   
   1. Wybierz **Typ** użytkownika i **grupę** użytkowników dla użytkownika. Dla użytkownika testowego **użytkownik podstawowy** dla typu i **użytkownika** dla grupy jest rozsądnie wybierany.
   
   1. Aby zapisać użytkownika, wybierz pozycję **Dodaj użytkownika**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

Oto jak przetestować konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po wybraniu opcji **Nowy Relic według organizacji** w panelu dostępu należy automatycznie zalogować się do nowej Relic. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz artykuł [Logowanie i uruchamianie aplikacji z poziomu portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj nowy Relic z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
