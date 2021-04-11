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
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: f56f2dc974df58575c72c93a0609026cd7bbf88d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101652627"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Veracode

W tym samouczku dowiesz się, jak zintegrować usługę Veracode z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Veracode z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Veracode.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Veracode przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne (SSO) Veracode.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. Usługa Veracode obsługuje zainicjowane przez dostawcę tożsamości Logowanie jednokrotne i Inicjowanie obsługi użytkowników just in Time.

## <a name="add-veracode-from-the-gallery"></a>Dodaj Veracode z galerii

Aby skonfigurować integrację programu Veracode z usługą Azure AD, Dodaj Veracode z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** w polu wyszukiwania wpisz ciąg "Veracode".
1. Na panelu Wyniki wybierz pozycję **Veracode** , a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-veracode"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Veracode

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Veracode przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy nawiązać połączenie między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Veracode.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Veracode, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-veracode-sso)** w usłudze Veracode, aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego Veracode](#create-veracode-test-user)** , aby miał odpowiednik B. Simon w Veracode połączony z reprezentacją usługi Azure AD użytkownika.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Veracode** Znajdź sekcję **Zarządzanie** . Wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

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

### <a name="create-an-azure-ad-test-user&quot;></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Veracode.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Veracode**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola &quot;domyślny dostęp&quot;.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name=&quot;configure-veracode-sso&quot;></a>Konfigurowanie logowania jednokrotnego Veracode

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny Veracode jako administrator.

1. W menu u góry wybierz pozycję **Ustawienia**  >  **administrator**.
   
    ![Zrzut ekranu przedstawiający administrowanie Veracode z ikonami ustawień i administratorami wyróżnionymi](./media/veracode-tutorial/admin.png &quot;Administracja")

1. Wybierz kartę **SAML** .

1. W sekcji **Ustawienia protokołu SAML organizacji** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający sekcję ustawień SAML organizacji](./media/veracode-tutorial/saml.png "Administracja")

    a.  W przypadku **wystawcy** należy wkleić wartość **identyfikatora usługi Azure AD** skopiowaną z Azure Portal.

    b. W polu **certyfikat podpisywania potwierdzenia** wybierz pozycję **Wybierz plik** , aby przekazać pobrany certyfikat z Azure Portal.

    c. W celu **samorejestracji** wybierz pozycję **Włącz rejestrację samoczynną**.

1. W sekcji **Ustawienia rejestracji automatycznej** wykonaj następujące kroki, a następnie wybierz pozycję **Zapisz**:

    ![Zrzut ekranu przedstawiający sekcję Ustawienia rejestracji automatycznej z różnymi opcjami wyróżnionymi](./media/veracode-tutorial/save.png "Administracja")

    a. W przypadku **aktywacji nowego użytkownika** wybierz opcję **Brak wymagania aktywacji**.

    b. W przypadku **aktualizacji danych użytkownika** wybierz pozycję **preferencja Veracode dane użytkownika**.

    c. W polu **szczegóły atrybutu SAML** wybierz następujące opcje:
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

### <a name="create-veracode-test-user"></a>Utwórz użytkownika testowego Veracode

W tej sekcji użytkownik o nazwie B. Simon został utworzony w Veracode. Veracode obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie ma elementu Action. Jeśli użytkownik nie istnieje jeszcze w usłudze Veracode, zostanie utworzony nowy po uwierzytelnieniu.

> [!NOTE]
> Do aprowizacji kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników Veracode i interfejsów API udostępnionych przez usługę Veracode.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Testuj tę aplikację w Azure Portal i należy automatycznie zalogować się do Veracode, dla którego skonfigurowano Logowanie jednokrotne.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka Veracode w obszarze Moje aplikacje należy automatycznie zalogować się do Veracode, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Veracode można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).