---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Exium | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Exium.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: jeedes
ms.openlocfilehash: 01b3bbc0a4f0c7d53ed0a9ba2449cf5985b2ab86
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610234"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-exium"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Exium

W tym samouczku dowiesz się, jak zintegrować usługę Exium z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Exium z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Exium.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Exium przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Exium.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Exium obsługuje usługę **SP** zainicjowaną przez usługę SSO.

## <a name="adding-exium-from-the-gallery"></a>Dodawanie Exium z galerii

Aby skonfigurować integrację programu Exium z usługą Azure AD, musisz dodać Exium z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Exium** w polu wyszukiwania.
1. Wybierz pozycję **Exium** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-exium"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Exium

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Exium przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Exium.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Exium, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-exium-sso)** w usłudze Exium, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Exium](#create-exium-test-user)** , aby dysponować odpowiednikiem B. Simon w Exium, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Exium** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://subapi.exium.net/saml/<WORKSPACE_ID>/metadata`
    
    b.  W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://subapi.exium.net/saml/<WORKSPACE_ID>/acs`

    c. W polu tekstowym **Adres URL logowania** wpisz następujący adres URL: `https://service.exium.net/sign-in`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta Exium](mailto:support@exium.net) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Exium.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Exium**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-exium-sso"></a>Konfigurowanie logowania jednokrotnego Exium

1. Zaloguj się, aby Exium witrynę firmy jako administrator.

1. W **konsoli administracyjnej** wybierz pozycję Panel **profil firmy** .

    ![zrzut ekranu przedstawiający konsolę administracyjną](./media/exium-tutorial/company-profile.png)

1. W **profilu** kliknij pozycję **Ustawienia logowania jednokrotnego** i **Edytuj** je.

1. Wykonaj poniższe czynności w sekcji **Ustawienia logowania jednokrotnego** .

    ![zrzut ekranu dla ustawień logowania jednokrotnego](./media/exium-tutorial/update.png)

    a. Wybierz pozycję **Typ logowania jednokrotnego** jako **AzureAD** z listy rozwijanej.

    b. Wklej wartość **adresu URL metadanych federacji aplikacji** w polu **adres URL metadanych dostawcy tożsamości SAML 2,0** .

    c. Skopiuj wartość **adresu URL SSO protokołu SAML 2,0** , wklej tę wartość w polu tekstowym **adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    d. Skopiuj wartość **Identyfikator jednostki programu SAML 2,0 Sp** , wklej tę wartość do pola tekstowego **Identyfikator** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.  

    e. Kliknij pozycję **Aktualizuj**.

### <a name="create-exium-test-user"></a>Utwórz użytkownika testowego Exium

1. Zaloguj się, aby Exium witrynę firmy jako administrator.

1. Przejdź do pozycji **Zarządzanie użytkownikami — > użytkownicy** i kliknij pozycję **Dodaj użytkownika**.

    ![zrzut ekranu przedstawiający tworzenie użytkownika testowego](./media/exium-tutorial/add-user.png)

1. Wprowadź wymagane pola na poniższej stronie i kliknij przycisk **Zapisz**.

    ![zrzut ekranu dotyczący tworzenia pól użytkownika testowego za pomocą przycisku Zapisz](./media/exium-tutorial/add-user-2.png)

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania Exium, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania Exium i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka Exium w obszarze Moje aplikacje zostanie on przekierowany do adresu URL logowania Exium. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Exium można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


