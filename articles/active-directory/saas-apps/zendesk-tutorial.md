---
title: 'Samouczek: integracja Azure Active Directory z usługą systemu Zendesk | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Zendesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 58e9cc2fda7779cf0d62db8e0b6f78e5bb4d95f8
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98731822"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zendesk"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą systemu Zendesk

W tym samouczku dowiesz się, jak zintegrować usługę systemu Zendesk z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi systemu Zendesk z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do systemu Zendesk.
* Zezwól użytkownikom na automatyczne logowanie się do usługi systemu Zendesk przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.


## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) systemu Zendesk.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Zendesk obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**
* Aplikacja Zendesk obsługuje [**zautomatyzowaną** aprowizację użytkowników](zendesk-provisioning-tutorial.md)


## <a name="adding-zendesk-from-the-gallery"></a>Dodawanie aplikacji Zendesk z galerii

Aby skonfigurować integrację aplikacji Zendesk z usługą Azure AD, musisz dodać aplikację Zendesk z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **systemu Zendesk** w polu wyszukiwania.
1. Wybierz pozycję **systemu Zendesk** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-zendesk"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla systemu Zendesk

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą systemu Zendesk przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w systemu Zendesk.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą systemu Zendesk, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-zendesk-sso)** w usłudze systemu Zendesk, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego systemu Zendesk](#create-zendesk-test-user)** , aby dysponować odpowiednikiem B. Simon w systemu Zendesk, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **systemu Zendesk** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.zendesk.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.zendesk.com`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.zendesk.com/access/saml`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Zendesk](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) w celu uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja Zendesk oczekuje asercji SAML w konkretnym formacie. Nie istnieją obowiązkowe atrybuty protokołu SAML, ale opcjonalnie możesz zarządzać za pomocą sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![Zrzut ekranu przedstawia atrybuty użytkownika z wybraną ikoną Edytuj.](common/edit-attribute.png)

    > [!NOTE]
    > Atrybuty rozszerzenia umożliwiają dodawanie atrybutów, które nie są domyślnie dostępne w usłudze Azure AD. Kliknij pozycję [Atrybuty użytkownika, które można ustawić w protokole SAML](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-), aby uzyskać pełną listę atrybutów protokołu SAML, które akceptuje aplikacja **Zendesk**.

1. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

1. W sekcji **certyfikat podpisywania SAML** Skopiuj **wartość odcisku palca** i Zapisz ją na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

1. W sekcji **Konfigurowanie systemu Zendesk** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi systemu Zendesk.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **systemu Zendesk**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-zendesk-sso"></a>Konfigurowanie logowania jednokrotnego systemu Zendesk

1. Aby zautomatyzować konfigurację w programie **systemu Zendesk**, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Zrzut ekranu przedstawia przycisk Zainstaluj rozszerzenie.](./media/target-process-tutorial/install_extension.png)

1. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Setup systemu Zendesk** , aby skierować do aplikacji systemu Zendesk. W tym miejscu podaj poświadczenia administratora w celu zalogowania się do aplikacji Zendesk. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować systemu Zendesk, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy systemu Zendesk jako administrator i wykonaj następujące czynności:

1. W **centrum administracyjnym systemu Zendesk** kliknij pozycję **Ustawienia zabezpieczeń** na karcie **zabezpieczenia** .

    ![Zrzut ekranu przedstawia centrum administracyjne systemu Zendesk z wybranymi ustawieniami zabezpieczeń.](./media/zendesk-tutorial/settings.png "Zabezpieczenia")

1. Przejdź do strony **logowania jednokrotnego** i kliknij pozycję **Edytuj** w elemencie **SAML**.

    ![Zrzut ekranu przedstawia stronę logowania jednokrotnego z wybraną pozycją Edytuj.](./media/zendesk-tutorial/saml-sso.png "Zabezpieczenia")

1. Wykonaj następujące kroki na stronie **logowania jednokrotnego** .

    ![Rejestracja jednokrotna](./media/zendesk-tutorial/saml-configuration.png "Logowanie jednokrotne")

    a. W polu tekstowym **Adres URL logowania jednokrotnego protokołu SAML** wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    b. W polu tekstowym **Odcisk palca certyfikatu** wklej wartość **odcisku palca** certyfikatu skopiowaną z witryny Azure Portal.

    c. W polu tekstowym **Adres URL zdalnego wylogowywania** wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    d. Kliknij pozycję **Zapisz**.

### <a name="create-zendesk-test-user"></a>Tworzenie użytkownika testowego aplikacji Zendesk

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w aplikacji Zendesk. Aplikacja Zendesk obsługuje automatyczną aprowizację użytkowników, która jest domyślnie włączona. Więcej szczegółów dotyczących konfigurowania automatycznego inicjowania obsługi użytkowników można znaleźć [tutaj](Zendesk-provisioning-tutorial.md).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania systemu Zendesk, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania systemu Zendesk i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka systemu Zendesk w obszarze Moje aplikacje zostanie on przekierowany do adresu URL logowania systemu Zendesk. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu systemu Zendesk można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).