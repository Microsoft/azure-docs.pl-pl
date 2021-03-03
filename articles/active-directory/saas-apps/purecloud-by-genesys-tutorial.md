---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą PureCloud przez Genesys | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją PureCloud by Genesys.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 7c6f84ee3bb4920dbe57221b8b0bbf9f5880742b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653027"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą PureCloud przez Genesys

W tym samouczku dowiesz się, jak zintegrować usługę PureCloud przez Genesys z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi PureCloud przez Genesys z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do PureCloud przez Genesys.
* Zezwól użytkownikom na automatyczne logowanie do PureCloud przez Genesys przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli go nie masz, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączoną usługą PureCloud przez funkcję logowania jednokrotnego (SSO) Genesys.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* PureCloud przez Genesys obsługuje usługę **SP i dostawcy tożsamości**— zainicjowano Logowanie jednokrotne.

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="add-purecloud-by-genesys-from-the-gallery"></a>Dodawanie PureCloud przez Genesys z galerii

Aby skonfigurować integrację PureCloud przez Genesys z usługą Azure AD, musisz dodać PureCloud przez Genesys z galerii do listy zarządzanych aplikacji SaaS. W tym celu wykonaj następujące kroki:

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub za pomocą konto Microsoft osobistych.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do pozycji **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **PureCloud by Genesys** w polu wyszukiwania.
1. Wybierz pozycję **PureCloud by Genesys** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-purecloud-by-genesys"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla PureCloud przez Genesys

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą PureCloud przez Genesys przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w PureCloud przez Genesys.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą PureCloud przez Genesys, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj usługę PureCloud przez Genesys Logowanie jednokrotne](#configure-purecloud-by-genesys-sso)** w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz PureCloud przez Genesys użytkownika testowego](#create-purecloud-by-genesys-test-user)** , aby dysponować odpowiednikiem B. Simon w PureCloud przez Genesys, która jest połączona z reprezentacją usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal, wykonaj następujące kroki:

1. W Azure Portal na stronie **PureCloud przez Genesys** integrację aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz pojedynczą Sign-On metodę** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przy użyciu usługi **dostawcy tożsamości**, w sekcji **Podstawowa konfiguracja SAML** wpisz wartości następujących pól:

    a. W polu **Identyfikator** wprowadź adresy URL odpowiadające regionowi:

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.au/saml
    ```

    b. W polu **adres URL odpowiedzi** wprowadź adresy URL odpowiadające regionowi:

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.com.au/saml
    ```

1. Wybierz opcję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu **adres URL logowania** wprowadź adresy URL odpowiadające regionowi:
    
    ```http
    https://login.mypurecloud.com
    https://login.mypurecloud.de
    https://login.mypurecloud.jp
    https://login.mypurecloud.ie
    https://login.mypurecloud.com.au
    ```

1. Aplikacja PureCloud by Genesys oczekuje asercji SAML w określonym formacie, co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenów języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych:

    ![image (obraz)](common/default-attributes.png)

1. Ponadto PureCloud przez aplikację Genesys oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML, jak pokazano w poniższej tabeli. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć w razie konieczności.

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | E-mail | user.userprincipalname |
    | OrganizationName | `Your organization name` |

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie PureCloud według Genesys** skopiuj odpowiedni adres URL (lub adresy URL) zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie B. Simon w Azure Portal:

1. W lewym okienku Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź nazwę użytkownika w następującym formacie: username@companydomain.extension . Na przykład: `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** , a następnie zanotuj wartość wyświetlaną w polu **hasło** .
   1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do PureCloud przez Genesys.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **PureCloud by Genesys**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-purecloud-by-genesys-sso"></a>Konfigurowanie PureCloud przez Genesys Logowanie jednokrotne

1. W innym oknie przeglądarki sieci Web Zaloguj się do PureCloud przez Genesys jako administrator.

1. Wybierz pozycję **administrator** u góry, a następnie przejdź do opcji **Logowanie** jednokrotne w obszarze **integracji**.

    ![Zrzut ekranu przedstawia okno administratora PureCloud, w którym można wybrać opcję Logowanie jednokrotne.](./media/purecloud-by-genesys-tutorial/configure-1.png)

1. Przejdź do karty **ADFS/Azure AD (Premium)** , a następnie wykonaj następujące kroki:

    ![Zrzut ekranu przedstawia stronę integracji, na której można wprowadzić podane wartości.](./media/purecloud-by-genesys-tutorial/configure-2.png)

    a. Wybierz pozycję **Przeglądaj** , aby przesłać certyfikat zakodowany base-64 pobrany z Azure Portal do **certyfikatu usług AD FS**.

    b. W polu **Identyfikator URI wystawcy usług AD FS** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z Azure Portal.

    c. W polu **docelowy identyfikator URI** wklej wartość **adresu URL logowania** skopiowanego z Azure Portal.

    d. W polu wartość **identyfikatora jednostki uzależnionej** przejdź do Azure Portal, a następnie na stronie **PureCloud przez Genesys** integrację aplikacji wybierz kartę **Właściwości** i skopiuj wartość **Identyfikator aplikacji** . Wklej ją do pola **Identyfikator jednostki uzależnionej** .

    ![Zrzut ekranu przedstawia okienko właściwości, w którym można znaleźć wartość aplikacji I D.](./media/purecloud-by-genesys-tutorial/configure-6.png)

    e. Wybierz pozycję **Zapisz**.

### <a name="create-purecloud-by-genesys-test-user"></a>Tworzenie użytkownika testowego aplikacji PureCloud by Genesys

Aby umożliwić użytkownikom usługi Azure AD logowanie się do PureCloud przez Genesys, muszą one być obsługiwane w PureCloud przez Genesys. W aplikacji PureCloud by Genesys aprowizowanie wykonywane jest ręcznie.

**Aby zainicjować obsługę administracyjną konta użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do PureCloud przez Genesys jako administrator.

1. Wybierz pozycję **administrator** u góry i przejdź do obszaru **osoby** w obszarze **osoby & uprawnienia**.

    ![Zrzut ekranu przedstawia okno administratora PureCloud, w którym można wybrać osoby.](./media/purecloud-by-genesys-tutorial/configure-3.png)

1. Na stronie **osoby** wybierz pozycję **Dodaj osobę**.

    ![Zrzut ekranu przedstawia stronę osoby, na której można dodać osobę.](./media/purecloud-by-genesys-tutorial/configure-4.png)

1. W oknie dialogowym **Dodawanie osób do organizacji** wykonaj następujące kroki:

    ![Zrzut ekranu przedstawia stronę, na której można wprowadzić podane wartości.](./media/purecloud-by-genesys-tutorial/configure-5.png)

    a. W polu **pełna nazwa** wprowadź nazwę użytkownika. Na przykład: **B. Simon**.

    b. W polu **adres e-mail** wprowadź adres e-mail użytkownika. Na przykład: **b. simon \@ contoso.com**.

    c. Wybierz przycisk **Utwórz**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do PureCloud przy użyciu adresu URL logowania Genesys, na którym można zainicjować przepływ logowania.  

* Bezpośrednio przejdź do PureCloud przez adres URL logowania Genesys i zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do PureCloud przez Genesys, dla którego skonfigurowano Logowanie jednokrotne. 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka PureCloud by Genesys w obszarze Moje aplikacje, jeśli zostanie on skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do PureCloud przez Genesys, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu PureCloud przez Genesys można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).