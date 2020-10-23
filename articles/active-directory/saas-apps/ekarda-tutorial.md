---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą ekarda | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i ekarda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2020
ms.author: jeedes
ms.openlocfilehash: 7bb74732074482c12d3bc760e259bb014ccf006f
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92454195"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ekarda"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą ekarda

W tym samouczku dowiesz się, jak zintegrować usługę ekarda z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi ekarda z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do ekarda.
* Umożliwienie użytkownikom automatycznego logowania się do usługi ekarda przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji oprogramowania jako usługi (SaaS) z usługą Azure AD, zobacz [co to jest logowanie jednokrotne (SSO)?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja usługi ekarda z włączoną obsługą logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa ekarda obsługuje logowanie jednokrotne z użyciem zainicjowanych przez usługę SP i dostawcy tożsamości.
* ekarda obsługuje Inicjowanie obsługi użytkowników just in Time.
* Po skonfigurowaniu ekarda można wymusić kontrolę sesji. To zabezpieczenie chroni przed eksfiltracji i niefiltrowaniem poufnych danych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od Kontrola dostępu warunkowego aplikacji. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-ekarda-from-the-gallery"></a>Dodaj ekarda z galerii

Aby skonfigurować integrację programu ekarda z usługą Azure AD, Dodaj ekarda z galerii do listy zarządzanych aplikacji SaaS:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.

1. W okienku po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **ekarda** w polu wyszukiwania.
1. Wybierz pozycję **ekarda** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ekarda"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla ekarda

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą ekarda przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy nawiązać relację między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w ekarda.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą ekarda, wykonaj następujące czynności:

1. [Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso) , aby umożliwić użytkownikom korzystanie z tej funkcji.

    1. [Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. [Skonfiguruj Logowanie jednokrotne](#configure-ekarda-sso) w usłudze ekarda, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * [Utwórz użytkownika testowego ekarda](#create-an-ekarda-test-user) , aby miał odpowiednik B. Simon w ekarda, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. [Przetestuj Logowanie jednokrotne](#test-sso) , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki w Azure Portal, aby włączyć logowanie jednokrotne w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Na stronie integracja aplikacji **ekarda** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę ołówka, aby edytować **podstawowe ustawienia konfiguracji protokołu SAML** .

   ![Zrzut ekranu przedstawiający Konfigurowanie pojedynczej Sign-On ze stroną SAML z wyróżnioną ikoną ołówka.](common/edit-urls.png)

1. Jeśli w sekcji **Podstawowa konfiguracja SAML** zostanie wyświetlony **plik metadanych dostawcy usług**, wykonaj następujące czynności:
    1. Wybierz pozycję **Przekaż plik metadanych**.
    1. Wybierz ikonę folderu, aby wybrać plik metadanych, a następnie wybierz pozycję **Przekaż**.
    1. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatorów** i **adresów URL odpowiedzi** pojawiają się automatycznie w polu tekstowym sekcja ekarda.

    > [!Note]
    > Jeśli wartości **identyfikatorów** i **adresów URL odpowiedzi** nie są wyświetlane automatycznie, wprowadź wartości ręcznie zgodnie z wymaganiami.

1. Jeśli **plik metadanych dostawcy usług** nie jest wyświetlany w sekcji **Podstawowa konfiguracja języka SAML** i chcesz skonfigurować aplikację w trybie inicjowanym przez dostawcy tożsamości, wprowadź wartości dla następujących pól:

    1. W polu tekstowym **Identyfikator** wpisz adres URL, który następuje po tym wzorcu: `https://my.ekarda.com/users/saml_metadata/<COMPANY_ID>`
    1. W polu tekstowym **adres URL odpowiedzi** wpisz adres URL następujący po tym wzorcu: `https://my.ekarda.com/users/saml_acs/<COMPANY_ID>`

1. Aby skonfigurować aplikację w trybie inicjowania programu SP, wybierz opcję **Ustaw dodatkowe adresy URL** i wykonaj następujące czynności:

    * W polu tekstowym **adres URL logowania** wpisz adres URL, który następuje po tym wzorcu: `https://my.ekarda.com/users/saml_sso/<COMPANY_ID>`

    > [!NOTE]
    > Wartości w dwóch poprzednich krokach nie są rzeczywiste. Zaktualizuj je za pomocą rzeczywistego identyfikatora, adresu URL odpowiedzi i wartości adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta ekarda](mailto:contact@ekarda.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie jednego Sign-On ze** standardem SAML w sekcji **certyfikat podpisywania SAML** wybierz pozycję **Pobierz** , aby zapisać **certyfikat (base64)** na komputerze.

    ![Zrzut ekranu przedstawiający sekcję certyfikatu podpisywania SAML na stronie Konfigurowanie pojedynczego Sign-On ze stroną SAML z wyróżnionym linkiem pobierania dla certyfikatu Base64.](common/certificatebase64.png)

1. W sekcji **Konfiguracja ekarda** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Zrzut ekranu przedstawiający sekcję Set up ekarda na stronie Konfigurowanie pojedynczego Sign-On ze stroną SAML z wyróżnionymi łączami kopiowania adresów URL.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji użyjesz Azure Portal, aby utworzyć użytkownika testowego o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.

1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład wprowadź `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i Zapisz wartość, która pojawia się w polu **hasło** .
   1. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi ekarda.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **ekarda**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Zrzut ekranu przedstawiający sekcję Zarządzanie z wyróżnionymi użytkownikami i grupami.](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Zrzut ekranu przedstawiający sekcję Użytkownicy i grupy z wyróżnioną pozycją Dodaj użytkownika.](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy użytkowników. Następnie wybierz **pozycję zaznacz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, wybierz odpowiednią rolę dla użytkownika z listy w oknie dialogowym **Wybierz rolę** . Następnie wybierz **pozycję zaznacz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-ekarda-sso"></a>Konfigurowanie logowania jednokrotnego ekarda

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny ekarda jako administrator.
1. Wybierz pozycję **administrator**  >  **Moje konto**.

    ![Zrzut ekranu przedstawiający interfejs użytkownika witryny ekarda z wyróżnionym kontem w menu administratora.](./media/ekarda-tutorial/ekarda.png)

1. W dolnej części strony Znajdź sekcję **Ustawienia protokołu SAML** . W tej sekcji znajduje się konfiguracja integracji protokołu SAML.
1. W sekcji **Ustawienia SAML** wykonaj następujące kroki:

    ![Zrzut ekranu strony ustawień SAML ekarda z wyróżnionymi polami konfiguracji SAML.](./media/ekarda-tutorial/ekarda1.png)

    1. Wybierz łącze **metadanych dostawcy usług** i Zapisz je jako plik na komputerze.
    1. Zaznacz pole wyboru **Włącz SAML** .
    1. W polu tekstowym **Identyfikator jednostki dostawcy tożsamości** wklej wartość identyfikatora usługi **Azure AD** , która została skopiowana wcześniej z Azure Portal.
    1. W polu tekstowym **adres URL logowania dostawcy tożsamości** wklej wartość **adresu URL logowania** , która została skopiowana wcześniej z Azure Portal.
    1. W polu tekstowym **adres URL wylogowywania dostawcy tożsamości** wklej wartość **adresu URL wylogowania** , która została skopiowana wcześniej z Azure Portal.
    1. Użyj Notatnika, aby otworzyć plik **certyfikatu (base64)** pobrany z Azure Portal. Wklej tę zawartość do pola tekstowego **certyfikat x509 dostawcy tożsamości** .
    1. Zaznacz pole wyboru **Włącz cel SLO** w sekcji **Opcje** .
    1. Wybierz pozycję **Aktualizuj**.

### <a name="create-an-ekarda-test-user"></a>Tworzenie użytkownika testowego ekarda

W tej sekcji użytkownik o nazwie B. Simon został utworzony w ekarda. ekarda obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. Nie masz żadnych akcji do wykonania w tej sekcji. Jeśli użytkownik o nazwie B. Simon nie istnieje już w ekarda, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD za pomocą portalu My Apps.

Po wybraniu kafelka ekarda w portalu My Apps należy automatycznie zalogować się do witryny ekarda, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat portalu Moje aplikacje, zobacz [wprowadzenie do portalu My Apps](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)
* [Wypróbuj ekarda z usługą Azure AD](https://aad.portal.azure.com/)
* Użyj [rozwiązania ekarda Enterprise eCard](https://ekarda.com/ecards-ecards-with-logo-for-business-corporate-enterprise) , aby udostępnić klientom i współpracownikom dowolną liczbę pracowników, którzy mają do nich wysyłać eCards z logo firmy. Dowiedz się więcej o [aprowizacji ekarda jako rozwiązanie logowania jednokrotnego](https://support.ekarda.com/#SSO-Implementation).
* [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
* [Jak chronić ekarda z zaawansowaną widocznością i kontrolkami](/cloud-app-security/proxy-intro-aad)