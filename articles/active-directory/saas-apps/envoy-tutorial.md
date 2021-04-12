---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą wysłannika | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Envoy.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/01/2021
ms.author: jeedes
ms.openlocfilehash: bbf961e7b99efe29fd8b13c2104c33e42ae7d4be
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286535"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-envoy"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą wysłannika

W tym samouczku dowiesz się, jak zintegrować usługę wysłannika z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi wysłannika z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do wysłannika.
* Zezwól użytkownikom na automatyczne logowanie się do usługi wysłannika przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) wysłannika.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa wysłannika obsługuje usługę **SP** zainicjowaną przez usługę SSO.

* Wysłannika obsługuje Inicjowanie obsługi użytkowników **just in Time** .

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="add-envoy-from-the-gallery"></a>Dodaj wysłannika z galerii

Aby skonfigurować integrację aplikacji Envoy z usługą Azure AD, należy dodać aplikację Envoy z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **wysłannika** w polu wyszukiwania.
1. Wybierz pozycję **wysłannika** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-envoy"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla wysłannika

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą wysłannika przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w wysłannika.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą wysłannika, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-envoy-sso)** w usłudze wysłannika, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego wysłannika](#create-envoy-test-user)** , aby dysponować odpowiednikiem B. Simon w wysłannika, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **wysłannika** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://app.envoy.com/a/saml/auth/<company-ID-from-Envoy>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Envoy](https://envoy.com/contact/), aby uzyskać tę wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

1. W sekcji **certyfikat podpisywania SAML** Skopiuj **wartość odcisku palca** i Zapisz ją na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

1. W sekcji **Konfigurowanie wysłannika** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi wysłannika.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Envoy**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-envoy-sso"></a>Konfigurowanie logowania jednokrotnego wysłannika

1. Aby zautomatyzować konfigurację w programie wysłannika, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Setup wysłannika** , aby skierować do aplikacji wysłannika. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi wysłannika. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować wysłannika, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy wysłannika jako administrator i wykonaj następujące czynności:

4. Na pasku narzędzi u góry kliknij pozycję **Settings** (Ustawienia).

    ![Envoy](./media/envoy-tutorial/envoy-1.png "Envoy")

5. Kliknij pozycję **Company** (Firma).

    ![Przedsiębiorstwo](./media/envoy-tutorial/envoy-2.png "Firma")

6. Kliknij pozycję **SAML**.

    ![SAML](./media/envoy-tutorial/envoy-3.png "SAML")

7. W sekcji konfiguracyjnej **SAML Authentication** (Uwierzytelnianie SAML) wykonaj następujące kroki:

    ![Uwierzytelnianie SAML](./media/envoy-tutorial/envoy-4.png "Uwierzytelnianie SAML")
    
    >[!NOTE]
    >Wartość dla identyfikatora lokalizacji centrali jest automatycznie generowana przez aplikację.
    
    a. W polu tekstowym **Fingerprint** (Odcisk palca) wklej wartość pola **Odcisk palca** certyfikatu skopiowaną z witryny Azure Portal.
    
    b. W polu tekstowym **IDENTITY PROVIDER HTTP SAML URL** (ADRES URL SAML HTTP DOSTAWCY TOŻSAMOŚCI) wklej wartość pola **Adres URL logowania** skopiowaną z witryny Azure Portal.
    
    c. Kliknij przycisk **Zapisz zmiany**.

### <a name="create-envoy-test-user"></a>Tworzenie użytkownika testowego aplikacji Envoy

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w wysłannika. Wysłannika obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze wysłannika, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania wysłannika, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania wysłannika i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka wysłannika w obszarze Moje aplikacje zostanie on przekierowany do adresu URL logowania wysłannika. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu wysłannika można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
