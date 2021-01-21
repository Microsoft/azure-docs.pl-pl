---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Freshworks | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Freshworks.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 0070a91706fc7efe81a7679801e8c10ea9a05242
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624745"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshworks"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Freshworks

W tym samouczku dowiesz się, jak zintegrować usługę Freshworks z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Freshworks z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Freshworks.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Freshworks przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Freshworks.

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Freshworks obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="add-freshworks-from-the-gallery"></a>Dodaj Freshworks z galerii

Aby skonfigurować integrację programu Freshworks z usługą Azure AD, musisz dodać Freshworks z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Freshworks** w polu wyszukiwania.
1. Wybierz pozycję **Freshworks** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-freshworks"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Freshworks

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Freshworks przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Freshworks.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Freshworks, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-freshworks-sso)** w usłudze Freshworks, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Freshworks](#create-freshworks-test-user)** , aby dysponować odpowiednikiem B. Simon w Freshworks, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Freshworks** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.freshworks.com/login`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.freshworks.com/sp/SAML/<MODULE_ID>/metadata`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta Freshworks](mailto:support@freshworks.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. Aby zmodyfikować opcje **podpisywania** zgodnie z wymaganiami, kliknij przycisk **Edytuj** , aby otworzyć okno dialogowe **certyfikatu podpisywania SAML** .

     ![image (obraz)](common/edit-certificate.png)

     ![Zrzut ekranu przedstawiający okno dialogowe "S A M L Signing Certificate" z wybranym przyciskiem "Edytuj".](./media/freshworks-tutorial/response.png)

    a. Wybierz opcję **podpisania odpowiedzi SAML** jako **podpisywania**.

    b. Kliknij pozycję **Zapisz**.

1. W sekcji **Konfigurowanie Freshworks** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Freshworks.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Freshworks**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-freshworks-sso"></a>Konfigurowanie logowania jednokrotnego Freshworks

1. Otwórz nowe okno przeglądarki sieci Web i zaloguj się do firmowej witryny Freshworks jako administrator i wykonaj następujące czynności:

2. Z lewej strony menu kliknij pozycję ikona **zabezpieczeń** , zaznacz opcję **Logowanie** jednokrotne i wybierz pozycję **Rejestracja jednokrotna protokołu SAML** w obszarze **metody uwierzytelniania**.

    ![Zrzut ekranu przedstawiający sekcję "metody uwierzytelniania zabezpieczeń" z włączoną opcją "Logowanie jednokrotne" i "S A M L s s".](./media/freshworks-tutorial/configure01.png)

3. W sekcji **Logowanie** jednokrotne wykonaj następujące czynności:

    ![Konfiguracja Freshworks](./media/freshworks-tutorial/configure02.png)

    a. Kliknij przycisk **Kopiuj** , aby skopiować **Identyfikator jednostki dostawcy usług (SP)** dla swojego wystąpienia i wkleić go w polu tekstowym **Identyfikator obiektu (identyfikator jednostki)** w sekcji **Podstawowa konfiguracja SAML** na Azure Portal.

    b. W polu tekstowym **dostawcy tożsamości (identyfikator jednostki** ) Wklej wartość **identyfikatora usługi Azure AD** , która została skopiowana z Azure Portal.

    c. W polu tekstowym **adres URL rejestracji jednokrotnej protokołu SAML** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    d. Otwórz w Notatniku certyfikat szyfrowany algorytmem Base64, skopiuj jego zawartość i wklej go do pola tekstowego **certyfikat zabezpieczeń** .

    e. Kliknij pozycję **Zapisz**.

### <a name="create-freshworks-test-user"></a>Utwórz użytkownika testowego Freshworks

W tej sekcji utworzysz użytkownika o nazwie B. Simon w Freshworks. Współpracuj z [zespołem obsługi klienta Freshworks](mailto:support@freshworks.com) , aby dodać użytkowników z platformy Freshworks. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego. 

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania Freshworks, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania Freshworks i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka Freshworks w obszarze Moje aplikacje należy automatycznie zalogować się do Freshworks, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Freshworks można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).