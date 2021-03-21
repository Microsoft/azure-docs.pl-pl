---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z systemem operacyjnym Synerise Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między systemami operacyjnymi Azure Active Directory i Synerise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/06/2021
ms.author: jeedes
ms.openlocfilehash: 0ffc6543a2597e39470c0d4aff86e7d5a1631d69
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736639"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-synerise-ai-growth-operating-system"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z systemem operacyjnym Synerise

W tym samouczku dowiesz się, jak zintegrować usługę Synerise z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Synerise z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Synerise.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Synerise przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Synerise.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Synerise obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne
* Synerise obsługuje Inicjowanie obsługi użytkowników **just in Time**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.


## <a name="adding-synerise-ai-growth-operating-system-from-the-gallery"></a>Dodawanie systemu operacyjnego Synerise

Aby skonfigurować integrację programu Synerise z usługą Azure AD, musisz dodać Synerise z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Synerise system operacyjny REGEXPW** w polu wyszukiwania.
1. Wybierz pozycję **Synerise REGEXPW system operacyjny** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-synerise-ai-growth-operating-system"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla systemu operacyjnego Synerise

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Synerise przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Synerise.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Synerise, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Konfigurowanie logowania JEDNOkrotnego systemu operacyjnego SYNERISE AI](#configure-synerise-ai-growth-operating-system-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego systemu operacyjnego Synerise](#create-synerise-ai-growth-operating-system-test-user)** , aby dysponować odpowiednikiem B. Simon w Synerise, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracji aplikacji **systemu operacyjnego SYNERISE AI** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, używając następującego wzorca:  `https://app.synerise.com/api-portal/uauth/saml/auth/<PROFILE_HASH>`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://app.synerise.com/api-portal/uauth/saml/auth/<PROFILE_HASH>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej Synerise](mailto:support@synerise.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie Synerise** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Synerise.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Synerise Operating System operacyjny**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-synerise-ai-growth-operating-system-sso"></a>Konfigurowanie logowania jednokrotnego systemu operacyjnego Synerise AI

1. Zaloguj się do Synerise jako administrator.

1. Przejdź do **Access Control ustawień >**.

    ![Ustawienia Synerise](./media/synerise-ai-growth-ecosystem-tutorial/settings.png)

1. Na stronie **Access Control** kliknij przycisk **Pokaż** na karcie **Logowanie** jednokrotne.

    ![Synerise Access Control](./media/synerise-ai-growth-ecosystem-tutorial/single-sign-on.png)

1. Wykonaj następujące czynności na poniższej stronie.

    ![Konfiguracja Synerise](./media/synerise-ai-growth-ecosystem-tutorial/configuration.png)

    a. W polu tekstowym **Identyfikator jednostki dostawcy identyfikatora** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z Azure Portal.

    b. W polu tekstowym "Logowanie **jednokrotne" (https)** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    c. W polu tekstowym **Identyfikator aplikacji dostawcy tożsamości** wklej wartość **identyfikatora aplikacji** .

    d. Kopiuj wartość **identyfikatora URI przekierowania dostawcy usług** , wklej tę wartość w polu tekstowym **adres URL odpowiedzi** w sekcji podstawowa konfiguracja SAML w Azure Portal.

    e. Wybierz pozycję **Przekierowanie HTTP** w **powiązaniu żądania**.

    f. Przełącz się na **podpis żądania**.

    przykład Przekaż pobrany plik **certyfikatu (base64)** do **certyfikatu sygnatury dostawcy tożsamości**.

    i. Kliknij pozycję **Zastosuj**.

### <a name="create-synerise-ai-growth-operating-system-test-user"></a>Utwórz użytkownika testowego systemu operacyjnego Synerise

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w Synerise. Synerise obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze Synerise, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania Synerise, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania Synerise i zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do Synerise, dla którego skonfigurowano Logowanie jednokrotne 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka Synerise w obszarze Moje aplikacje, jeśli jest skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do Synerise, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Synerise można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).