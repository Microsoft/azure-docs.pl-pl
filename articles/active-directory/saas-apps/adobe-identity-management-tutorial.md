---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Adobe Identity Management | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i usługą Adobe Identity Management.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: fdca04c645e1bb956c8e9f294c702b639c8e2f74
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726404"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-identity-management"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Adobe Identity Management

W tym samouczku dowiesz się, jak zintegrować usługę Adobe Identity Management z usługą Azure Active Directory (Azure AD). Integracja programu Adobe Identity Management z usługą Azure AD pozwala:

* Kontrolka w usłudze Azure AD, która ma dostęp do usługi Adobe Identity Management.
* Zezwól użytkownikom na automatyczne logowanie do usługi Adobe Identity Management przy użyciu swoich kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) usługi Adobe Identity Management.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Adobe Identity Management obsługuje zainicjowane przez usługę **SP** SSO

## <a name="adding-adobe-identity-management-from-the-gallery"></a>Dodawanie programu Adobe Identity Management z galerii

Aby skonfigurować integrację usługi Adobe Identity Management w usłudze Azure AD, musisz dodać do niej aplikację Adobe Identity Management z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Adobe Identity Management** w polu wyszukiwania.
1. Wybierz pozycję **Adobe Identity Management** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-adobe-identity-management"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla programu Adobe Identity Management

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą rozwiązania Adobe Identity Management przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie Adobe Identity Management.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu programu Adobe Identity Management, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne w usłudze Adobe Identity Management](#configure-adobe-identity-management-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego programu Adobe Identity Management](#create-adobe-identity-management-test-user)** , aby dysponować odpowiednikiem B. Simon w usłudze Adobe Identity Management, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Adobe Identity Management** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz następujący adres URL: `https://adobe.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://federatedid-na1.services.adobe.com/federated/saml/metadata/alias/<CUSTOM_ID>`

    > [!NOTE]
    > Wartość identyfikatora nie jest prawdziwa. Zaktualizuj wartość za pomocą rzeczywistego identyfikatora. Skontaktuj się z [zespołem pomocy technicznej firmy Adobe Identity Management](mailto:identity@adobe.com) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie rozwiązania Adobe Identity Management** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Adobe Identity Management.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Adobe Identity Management**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-adobe-identity-management-sso"></a>Konfigurowanie logowania jednokrotnego dla programu Adobe Identity Management

1. Aby zautomatyzować konfigurację w programie Adobe Identity Management, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Konfigurowanie Adobe Identity Management** przekieruje Cię do aplikacji Zarządzanie tożsamościami firmy Adobe. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi Adobe Identity Management. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-8.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować program Adobe Identity Management, w innym oknie przeglądarki sieci Web Zaloguj się do lokacji firmy Adobe Identity Management jako administrator.

4. Przejdź do karty **Ustawienia** , a następnie kliknij pozycję **Utwórz katalog**.

    ![Ustawienia programu Adobe Identity Management](./media/adobe-identity-management-tutorial/settings.png)

5. Nadaj nazwę katalogowi w polu tekstowym i wybierz pozycję **Identyfikator federacyjny**, a następnie kliknij przycisk **dalej**.

    ![Tworzenie katalogu przez program Adobe Identity Management](./media/adobe-identity-management-tutorial/create-directory.png)

6. Wybierz **innych dostawców SAML** i kliknij przycisk **dalej**.
 
    ![Dostawcy SAML firmy Adobe Identity Management](./media/adobe-identity-management-tutorial/saml-providers.png)

7. Kliknij pozycję **Wybierz** , aby przekazać plik **XML metadanych** pobrany z Azure Portal.

    ![Konfiguracja SAML programu Adobe Identity Management](./media/adobe-identity-management-tutorial/saml-configuration.png)

8. Kliknij przycisk **gotowe**.

### <a name="create-adobe-identity-management-test-user"></a>Utwórz użytkownika testowego zarządzania tożsamościami firmy Adobe

1. Przejdź do karty **Użytkownicy** i kliknij pozycję **Dodaj użytkownika**.

    ![Dodawanie użytkownika do programu Adobe Identity Management](./media/adobe-identity-management-tutorial/add-user.png)

2. W polu tekstowym **wprowadź adres e-mail użytkownika** Podaj **adres e-mail**.

    ![Zapisywanie użytkownika przez program Adobe Identity Management](./media/adobe-identity-management-tutorial/save-user.png)

3. Kliknij pozycję **Zapisz**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do programu Adobe Identity Management, w którym można zainicjować przepływ logowania.

* Przejdź bezpośrednio do adresu URL logowania do usługi Adobe Identity Management, a następnie zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka rozwiązania Adobe Identity Management w obszarze Moje aplikacje zostanie przekierowany do adresu URL logowania do programu Adobe Identity Management. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu programu Adobe Identity Management można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).