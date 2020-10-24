---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Veracode | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Veracode.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.openlocfilehash: a62863607798e7f64a74926ebea4f1a1d05cbd6c
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517722"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Veracode

W tym samouczku dowiesz się, jak zintegrować usługę Veracode z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Veracode z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Veracode.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Veracode przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji oprogramowania jako usługi (SaaS) z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne (SSO) Veracode.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. Usługa Veracode obsługuje zainicjowane przez dostawcę tożsamości Logowanie jednokrotne i Inicjowanie obsługi użytkowników just in Time.

## <a name="add-veracode-from-the-gallery"></a>Dodaj Veracode z galerii

Aby skonfigurować integrację programu Veracode z usługą Azure AD, Dodaj Veracode z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** w polu wyszukiwania wpisz ciąg "Veracode".
1. Na panelu Wyniki wybierz pozycję **Veracode** , a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-veracode"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Veracode

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Veracode przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy nawiązać połączenie między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Veracode.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Veracode, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-veracode-sso)** w usłudze Veracode, aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego Veracode](#create-veracode-test-user)** , aby miał odpowiednik B. Simon w Veracode połączony z reprezentacją usługi Azure AD użytkownika.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Veracode** Znajdź sekcję **Zarządzanie** . Wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Zrzut ekranu przedstawiający Konfigurowanie pojedynczego Sign-On przy użyciu protokołu SAML z wyróżnioną ikoną ołówka](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** aplikacja została wstępnie skonfigurowana i wymagane adresy URL są już wstępnie wypełnione na platformie Azure. Wybierz pozycję **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź pozycję **certyfikat (base64)**. Wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Zrzut ekranu przedstawiający sekcję certyfikat podpisywania SAML z wyróżnionym linkiem pobierania](common/certificatebase64.png)

1. Veracode oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![Zrzut ekranu przedstawiający sekcje User Attributes & oświadczenia](common/default-attributes.png)

1. Veracode oczekuje także kilku atrybutów do przekazania z powrotem do odpowiedzi SAML. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | firstname |Użytkownik. podanąname |
    | lastname |User. nazwisko |
    | poczta e-mail |User.mail |

1. W sekcji **Konfigurowanie Veracode** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Zrzut ekranu przedstawiający sekcję Set up Veracode z wyróżnionymi adresami URL konfiguracji](common/copy-configuration-urls.png)

## <a name="configure-veracode-sso"></a>Konfigurowanie logowania jednokrotnego Veracode

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny Veracode jako administrator.

1. W menu u góry wybierz pozycję **Ustawienia**  >  **administrator**.
   
    ![Zrzut ekranu przedstawiający administrowanie Veracode z ikonami ustawień i administratorami wyróżnionymi](./media/veracode-tutorial/ic802911.png "Administracja")

1. Wybierz kartę **SAML** .

1. W sekcji **Ustawienia protokołu SAML organizacji** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający sekcję ustawień SAML organizacji](./media/veracode-tutorial/ic802912.png "Administracja")

    a.  W przypadku **wystawcy**należy wkleić wartość **identyfikatora usługi Azure AD** skopiowaną z Azure Portal.

    b. W polu **certyfikat podpisywania potwierdzenia**wybierz pozycję **Wybierz plik** , aby przekazać pobrany certyfikat z Azure Portal.

    c. W celu **samorejestracji**wybierz pozycję **Włącz rejestrację samoczynną**.

1. W sekcji **Ustawienia rejestracji automatycznej** wykonaj następujące kroki, a następnie wybierz pozycję **Zapisz**:

    ![Zrzut ekranu przedstawiający sekcję Ustawienia rejestracji automatycznej z różnymi opcjami wyróżnionymi](./media/veracode-tutorial/ic802913.png "Administracja")

    a. W przypadku **aktywacji nowego użytkownika**wybierz opcję **Brak wymagania aktywacji**.

    b. W przypadku **aktualizacji danych użytkownika**wybierz pozycję **preferencja Veracode dane użytkownika**.

    c. W polu **szczegóły atrybutu SAML**wybierz następujące opcje:
      * **Role użytkowników**
      * **Administrator zasad**
      * **Recenzent**
      * **Klient zabezpieczeń**
      * **Executive**
      * **Osoba przesyłająca**
      * **Kreator**
      * **Wszystkie typy skanów**
      * **Członkostwa w zespole**
      * **Zespół domyślny**

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**  > **Użytkownicy**  >  **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:

   1. W obszarze **Nazwa**wprowadź `B.Simon` .  
   1. W polu **Nazwa użytkownika**wprowadź username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Wybierz pozycję **Pokaż hasło**, a następnie Zapisz wartość wyświetlaną.
   1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji należy włączyć usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Veracode.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Veracode**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Zrzut ekranu przedstawiający sekcję Zarządzanie z wyróżnionymi użytkownikami i grupami](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Zrzut ekranu strony użytkownicy i grupy z wyróżnioną pozycją Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** **Wybierz pozycję** **B. Simon**. Następnie wybierz **pozycję zaznacz** w dolnej części ekranu.
1. Jeśli oczekujesz, że jakakolwiek wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz z listy odpowiednią rolę dla użytkownika. Następnie wybierz **pozycję zaznacz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-veracode-test-user"></a>Utwórz użytkownika testowego Veracode

Aby zalogować się do usługi Veracode, użytkownicy usługi Azure AD muszą być obsługiwani do usługi Veracode. To zadanie jest zautomatyzowane i nie trzeba wykonywać żadnych czynności ręcznie. Użytkownicy są automatycznie tworzeniu w razie potrzeby podczas pierwszej próby logowania jednokrotnego.

> [!NOTE]
> Do aprowizacji kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników Veracode i interfejsów API udostępnionych przez usługę Veracode.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu opcji **Veracode** w panelu dostępu należy automatycznie zalogować się do Veracode, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj Veracode z usługą Azure AD](https://aad.portal.azure.com/)