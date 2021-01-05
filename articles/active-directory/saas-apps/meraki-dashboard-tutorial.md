---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z pulpitem nawigacyjnym Meraki | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i pulpitem nawigacyjnym Meraki.
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
ms.openlocfilehash: 0c43e085725ceb5db718c9b4c9c9d83787964d63
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813534"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-meraki-dashboard"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z pulpitem nawigacyjnym Meraki

W tym samouczku dowiesz się, jak zintegrować pulpit nawigacyjny usługi Meraki z Azure Active Directory (Azure AD). Podczas integrowania pulpitu nawigacyjnego Meraki z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do pulpitu nawigacyjnego Meraki.
* Zezwól użytkownikom na automatyczne logowanie do pulpitu nawigacyjnego Meraki przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) na pulpicie nawigacyjnym Meraki.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Pulpit nawigacyjny Meraki obsługuje **dostawcy tożsamości** zainicjowane Logowanie jednokrotne

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-meraki-dashboard-from-the-gallery"></a>Dodawanie pulpitu nawigacyjnego Meraki z galerii

Aby skonfigurować integrację pulpitu nawigacyjnego Meraki z usługą Azure AD, musisz dodać pulpit nawigacyjny Meraki z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Meraki Dashboard** w polu wyszukiwania.
1. Wybierz pozycję **pulpit nawigacyjny Meraki** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-meraki-dashboard"></a>Konfigurowanie i testowanie usługi Azure AD SSO dla pulpitu nawigacyjnego Meraki

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą pulpitu nawigacyjnego Meraki przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem na pulpicie nawigacyjnym Meraki.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą pulpitu nawigacyjnego Meraki, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne pulpitu nawigacyjnego Meraki](#configure-meraki-dashboard-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego pulpitu nawigacyjnego Meraki](#create-meraki-dashboard-test-user)** , aby dysponować odpowiednikiem B. Simon na pulpicie nawigacyjnym Meraki, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **pulpitu nawigacyjnego Meraki** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:
     
    W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, używając następującego wzorca:  `https://n27.meraki.com/saml/login/m9ZEgb/< UNIQUE ID >`

    > [!NOTE]
    > Wartość adresu URL odpowiedzi nie jest prawdziwa. Zaktualizuj tę wartość za pomocą rzeczywistej wartości adresu URL odpowiedzi, która została omówiona w dalszej części tego samouczka.

1. Kliknij przycisk **Zapisz**.

1. Aplikacja pulpitu nawigacyjnego Meraki oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja pulpitu nawigacyjnego Meraki oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.
    
    | Nazwa | Atrybut źródłowy|
    | ---------------| --------- |
    | `https://dashboard.meraki.com/saml/attributes/username` | user.userprincipalname |
    | `https://dashboard.meraki.com/saml/attributes/role` | user.assignedroles |

    > [!NOTE]
    > Aby dowiedzieć się, jak skonfigurować role w usłudze Azure AD, zobacz [tutaj](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui).

1. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

1. W sekcji **certyfikat podpisywania SAML** Skopiuj **wartość odcisku palca** i Zapisz ją na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

1. Na stronie **Konfigurowanie pulpitu nawigacyjnego Meraki** skopiuj wartość adres URL wylogowania i Zapisz ją na komputerze.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do pulpitu nawigacyjnego Meraki.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **pulpit nawigacyjny Meraki**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".

    ![rola użytkownika](./media/meraki-dashboard-tutorial/user-role.png)

    > [!NOTE]
    > **Wybierz opcję roli** zostanie wyłączona, a rola domyślna to użytkownik wybranego użytkownika.

1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-meraki-dashboard-sso"></a>Konfigurowanie logowania jednokrotnego dla pulpitu nawigacyjnego Meraki

1. Aby zautomatyzować konfigurację na pulpicie nawigacyjnym Meraki, musisz zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj pulpit nawigacyjny Meraki** , aby kierować do aplikacji pulpitu nawigacyjnego Meraki. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do pulpitu nawigacyjnego usługi Meraki. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować pulpit nawigacyjny Meraki w innym oknie przeglądarki sieci Web, zaloguj się do witryny firmy pulpitu nawigacyjnego Meraki jako administrator.

1. Przejdź do   ->  **ustawień** organizacji.

    ![Karta Ustawienia pulpitu nawigacyjnego Meraki](./media/meraki-dashboard-tutorial/configure-1.png)

1. W obszarze Uwierzytelnianie Zmień opcję **Logowanie jednokrotne SAML** na polecenie **SAML SSO**.

    ![Uwierzytelnianie pulpitu nawigacyjnego Meraki](./media/meraki-dashboard-tutorial/configure-2.png)

1. Kliknij pozycję **Dodaj element SAML dostawcy tożsamości**.

    ![Pulpit nawigacyjny Meraki Dodaj dostawcy tożsamości SAML](./media/meraki-dashboard-tutorial/configure-3.png)

1. Wklej wartość **odcisku palca** , która została skopiowana z Azure Portal do pola tekstowego **odcisku palca SHA1 certyfikatu X. 590** . Następnie kliknij przycisk **Zapisz**. Po zapisaniu adres URL klienta zostanie wyświetlony. Skopiuj wartość adresu URL klienta i wklej ją do pola tekstowego **adresu URL odpowiedzi** w **sekcji Podstawowa konfiguracja SAML** w Azure Portal.

    ![Konfiguracja pulpitu nawigacyjnego Meraki](./media/meraki-dashboard-tutorial/configure-4.png)

### <a name="create-meraki-dashboard-test-user"></a>Utwórz użytkownika testowego pulpitu nawigacyjnego Meraki

1. W innym oknie przeglądarki sieci Web Zaloguj się do pulpitu nawigacyjnego Meraki jako administrator.

1. Przejdź do   ->  **administratorów** organizacji.

    ![Administratorzy pulpitu nawigacyjnego Meraki](./media/meraki-dashboard-tutorial/user-1.png)

1. W sekcji role administratora SAML kliknij przycisk **Dodaj rolę SAML** .

    ![Przycisk Dodaj rolę SAML pulpitu nawigacyjnego Meraki](./media/meraki-dashboard-tutorial/user-2.png)

1. Wprowadź **Meraki_full_admin** roli, Oznacz **dostęp organizacji** jako **pełny** , a następnie kliknij pozycję **Utwórz rolę**. Powtórz ten proces dla **meraki_readonly_admin**. ten czas oznacza **dostęp organizacji** jako pole **tylko do odczytu** .
 
    ![Pulpit nawigacyjny Meraki — Tworzenie użytkownika](./media/meraki-dashboard-tutorial/user-3.png)

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Testuj tę aplikację w Azure Portal i zaloguj się automatycznie do pulpitu nawigacyjnego Meraki, dla którego skonfigurowano Logowanie jednokrotne

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka pulpitu nawigacyjnego Meraki w obszarze Moje aplikacje należy automatycznie zalogować się do pulpitu nawigacyjnego usługi Meraki, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu pulpitu nawigacyjnego Meraki można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).