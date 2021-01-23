---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Netskope konsola administratora | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i konsola administratora Netskope.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2020
ms.author: jeedes
ms.openlocfilehash: 8435cab1855e9df871d17ff7fa393b6ab2cf0cb1
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736348"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-administrator-console"></a>Samouczek: integracja logowania jednokrotnego (SSO) Azure Active Directory z usługą Netskope konsola administratora

W tym samouczku dowiesz się, jak zintegrować konsola administratora Netskope z usługą Azure Active Directory (Azure AD). W przypadku integrowania konsola administratora Netskope z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Netskope konsola administratora.
* Zezwól użytkownikom na automatyczne logowanie do Netskope konsola administratora z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Netskope konsola administratora subskrypcję z włączonym logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Netskope konsola administratora obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.


## <a name="adding-netskope-administrator-console-from-the-gallery"></a>Dodawanie konsola administratora Netskope z galerii

Aby skonfigurować integrację Netskope konsola administratora z usługą Azure AD, musisz dodać Netskope konsola administratora z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Netskope Konsola administratora** w polu wyszukiwania.
1. Wybierz pozycję **Netskope Konsola administratora** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-netskope-administrator-console"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Netskope konsola administratora

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Netskope konsola administratora przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Netskope konsola administratora.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Netskope konsola administratora, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Netskope Konsola administratora Logowanie jednokrotne](#configure-netskope-administrator-console-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz Netskope Konsola administratora użytkownika testowego](#create-netskope-administrator-console-test-user)** , aby uzyskać odpowiednik B. Simon w Netskope Konsola administratora połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Netskope Konsola administratora** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, używając następującego wzorca:  `https://<tenant_host_name>/saml/acs`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Wartość zostanie omówiona w dalszej części tego samouczka.

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantname>.goskope.com`

    > [!NOTE]
    > Wartości adresu URL logowania nie są prawdziwe. Zaktualizuj wartość adresu URL logowania przy użyciu rzeczywistego adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej Netskope Konsola administratora klienta](mailto:support@netskope.com) , aby uzyskać wartość adresu URL logowania. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja Netskope konsola administratora oczekuje potwierdzenia SAML w określonym formacie, co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja Netskope konsola administratora oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa |  Atrybut źródłowy|
    | ---------| --------- |
    | rola administratora | user.assignedroles |

    > [!NOTE]
    > Kliknij [tutaj](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) , aby dowiedzieć się, jak utworzyć role w usłudze Azure AD.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **konfigurowanie Konsola administratora Netskope** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do Netskope konsola administratora.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Netskope Konsola administratora**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli skonfigurowano role zgodnie z opisem w powyższej tabeli, można wybrać ją z listy rozwijanej **Wybierz rolę** .
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-netskope-administrator-console-sso"></a>Konfigurowanie Netskope konsola administratora Logowanie jednokrotne

1. Otwórz nową kartę w przeglądarce i zaloguj się do witryny Netskope konsola administratora firmy jako administrator.

1. Kliknij kartę **Ustawienia** w okienku nawigacji po lewej stronie.

    ![Zrzut ekranu przedstawia ustawienie wybrane w okienku nawigacji.](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Kliknij pozycję Karta **Administracja** .

    ![Zrzut ekranu przedstawia administrację wybraną z ustawień.](./media/netskope-cloud-security-tutorial/config-administration.png)

1. Kliknij kartę **Logowanie jednokrotne** .

    ![Zrzut ekranu przedstawia S S O wybranym w obszarze Administracja.](./media/netskope-cloud-security-tutorial/config-sso.png)

1. W sekcji **Ustawienia sieciowe** wykonaj następujące czynności:
    
    ![Zrzut ekranu przedstawia ustawienia sieci, w których można wprowadzić podane wartości.](./media/netskope-cloud-security-tutorial/config-pasteurls.png)

    a. Skopiuj wartość **adresu URL usługi konsumenckej potwierdzenia** i wklej ją do pola tekstowego **adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    b. Skopiuj wartość **Identyfikator jednostki dostawcy usługi** i wklej ją do pola tekstowego **Identyfikator** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

1. Kliknij pozycję **Edytuj ustawienia** w sekcji **Ustawienia rejestracji jednokrotnej/SLO** .

    ![Zrzut ekranu przedstawia ustawienia S s O/S, w których można wybrać pozycję Edytuj ustawienia.](./media/netskope-cloud-security-tutorial/config-editsettings.png)

1. W oknie podręcznym **Ustawienia** wykonaj następujące kroki:

    ![Zrzut ekranu przedstawia okno dialogowe Ustawienia, w którym można wprowadzić podane wartości.](./media/netskope-cloud-security-tutorial/configuration.png)

    a. Wybierz pozycję **Włącz logowanie jednokrotne**.

    b. W polu tekstowym **adres URL dostawcy tożsamości** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    c. W polu tekstowym **Identyfikator jednostki dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD** , która została skopiowana z Azure Portal.

    d. Otwórz pobrany certyfikat zakodowany w formacie base64 w Notatniku, skopiuj zawartość tego pliku do schowka, a następnie wklej go do pola tekstowego **certyfikatu dostawcy tożsamości** .

    e. Wybierz pozycję **Włącz logowanie jednokrotne**.

    f. W polu tekstowym **adres URL dostawcy tożsamości SLO** wklej wartość **adresu URL wylogowania** , która została skopiowana z Azure Portal.

    przykład Kliknij przycisk **Prześlij**.

### <a name="create-netskope-administrator-console-test-user"></a>Utwórz użytkownika testowego konsola administratora Netskope

1. Otwórz nową kartę w przeglądarce i zaloguj się do witryny Netskope konsola administratora firmy jako administrator.

1. Kliknij kartę **Ustawienia** w okienku nawigacji po lewej stronie.

    ![Zrzut ekranu przedstawia wybrane ustawienia.](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Kliknij pozycję **aktywna karta platformy** .

    ![Zrzut ekranu przedstawia aktywną platformę wybraną z ustawień.](./media/netskope-cloud-security-tutorial/user1.png)

1. Kliknij kartę **Użytkownicy** .

    ![Zrzut ekranu przedstawia użytkowników wybranych z aktywnej platformy.](./media/netskope-cloud-security-tutorial/add-user.png)

1. Kliknij pozycję **Dodaj użytkowników**.

    ![Zrzut ekranu przedstawia okno dialogowe użytkownicy, w którym można wybrać opcję Dodaj użytkowników.](./media/netskope-cloud-security-tutorial/user-add.png)

1. Wprowadź adres e-mail użytkownika, który chcesz dodać, a następnie kliknij przycisk **Dodaj**.

    ![Zrzut ekranu przedstawia Dodawanie użytkowników, w których można wprowadzić listę użytkowników.](./media/netskope-cloud-security-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Zostanie ono przekierowanie do Netskope konsola administratora adres URL logowania, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do Netskope konsola administratora adres URL logowania i zainicjuj tam przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do Konsola administratora Netskope, dla którego skonfigurowano Logowanie jednokrotne 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka Netskope konsola administratora w obszarze Moje aplikacje, jeśli zostanie on skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do konsola administratora Netskope, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Netskope konsola administratora można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
