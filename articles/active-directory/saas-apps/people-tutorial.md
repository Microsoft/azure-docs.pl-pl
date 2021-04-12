---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z osobami | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i osobami.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 43edf62a625d80de0a5de8e0924e771d68a595e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101647119"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-people"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z osobami

W tym samouczku dowiesz się, jak zintegrować osoby z usługą Azure Active Directory (Azure AD). Podczas integrowania osób z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do osób.
* Zezwól użytkownikom na automatyczne logowanie do osób mających konta usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Użytkownicy obsługują Logowanie jednokrotne w usłudze **SP**
* Aplikacje mobilne dla osób można teraz skonfigurować za pomocą usługi Azure AD w celu włączenia logowania jednokrotnego. W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

>[!NOTE]
>Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="add-people-from-the-gallery"></a>Dodawanie osób z galerii

Aby skonfigurować integrację osób z usługą Azure AD, musisz dodać osoby z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **osoby** w polu wyszukiwania.
1. Wybierz pozycję **osoby** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-people"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla osób

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą osób przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w osobach.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą osób, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
2. **[Konfigurowanie logowania jednokrotnego osoby](#configure-people-sso)** — aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Utwórz użytkowników testowego użytkownika](#create-people-test-user)** — aby dysponować odpowiednikiem B. Simon w osobach, które są połączone z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **osoby** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<company name>.peoplehr.net`

    b. W polu **Identyfikator** wpisz adres URL: `https://www.peoplehr.com`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<company name>.peoplehr.net/Pages/Saml/ConsumeAzureAD.aspx`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta](mailto:customerservices@peoplehr.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfiguruj pojedyncze Sign-On za pomocą elementu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie osób** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, udzielając dostępu osobom.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **osoby**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-people-sso"></a>Konfigurowanie logowania jednokrotnego dla osób

1. Aby zautomatyzować konfigurację w ramach osób, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Instalator osoby** przekieruje Cię do aplikacji osoby. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do osób. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować osoby, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy osoby jako administrator i wykonaj następujące czynności:
   
4. W menu po lewej stronie kliknij pozycję **Ustawienia**.

    ![Zrzut ekranu pokazujący menu po lewej stronie z wybranymi ustawieniami "Ustawienia".](./media/people-tutorial/settings.png)

5. Kliknij pozycję **Company** (Firma).

    ![Zrzut ekranu pokazujący, że wybrano "Firma" z menu "Ustawienia".](./media/people-tutorial/company.png)

6. W **pliku metadanych SAML metadanych przekazywania "Logowanie** jednokrotne" kliknij przycisk **Przeglądaj** w celu przekazania pobranego pliku.

    ![Konfigurowanie logowania jednokrotnego](./media/people-tutorial/xml.png)

### <a name="create-people-test-user"></a>Utwórz użytkownika testowego

W tej sekcji utworzysz użytkownika o nazwie B. Simon w osobach. Współpracuj z [zespołem pomocy technicznej dla osób](mailto:customerservices@peoplehr.com) , aby dodać użytkowników na platformie osoby. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do osoby, na której można zainicjować przepływ logowania. 

* Bezpośrednio przejdź do adresu URL logowania do osoby i zainicjuj przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Kliknięcie kafelka osoby w obszarze Moje aplikacje spowoduje przekierowanie do adresu URL logowania do osoby. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="test-sso-for-people-mobile"></a>Testowanie logowania jednokrotnego dla osób (mobilnych)

1. Otwórz aplikację mobilną dla osób. Na stronie logowania wprowadź **Identyfikator poczty e-mail** , a następnie kliknij pozycję Logowanie **jednokrotne**.

    ![Logowanie](./media/people-tutorial/test01.png)

2. Wprowadź identyfikator **użytkownika organizacji** i kliknij przycisk **dalej**.

    ![Wiadomość e-mail](./media/people-tutorial/test02.png)

3. Na koniec po pomyślnym zalogowaniu zostanie wyświetlona strona główna aplikacji:

    ![Raz](./media/people-tutorial/test03.png)

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu osób można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).