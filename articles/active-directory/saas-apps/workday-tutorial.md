---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu produktu Workday | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Workday.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: af001ddd8dc468d06706e63eaf092d1179fe3fdc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181377"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu produktu Workday

W tym samouczku dowiesz się, jak zintegrować usługę Workday z usługą Azure Active Directory (Azure AD). W przypadku integrowania produktu Workday z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do produktu Workday.
* Zezwól użytkownikom na automatyczne logowanie do produktu Workday przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) w ramach usługi Workday.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Workday **obsługuje usługę** zainicjowaną przez usługę SSO.

* Aplikację mobilną Workday można teraz skonfigurować za pomocą usługi Azure AD w celu włączenia logowania jednokrotnego. Aby uzyskać więcej informacji na temat sposobu konfigurowania, użyj [tego](workday-mobile-tutorial.md) linku.

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-workday-from-the-gallery"></a>Dodawanie produktu Workday z galerii

Aby skonfigurować integrację programu Workday z usługą Azure AD, musisz dodać dzień roboczy z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Workday** w polu wyszukiwania.
1. Wybierz pozycję **Workday** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-workday"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla produktu Workday

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą produktu Workday przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Workday.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą produktu Workday, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj aplikację Workday](#configure-workday)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego produktu Workday](#create-workday-test-user)** , aby dysponować odpowiednikiem B. Simon w usłudze Workday, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Workday** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://impl.workday.com/<tenant>/login-saml.htmld`

    c. W polu tekstowym **adres URL wylogowywania** wpisz adres URL, używając następującego wzorca: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Te wartości nie są rzeczywiste. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania, adresu URL odpowiedzi i adresu URL wylogowywania. Adres URL odpowiedzi musi mieć poddomenę na przykład: www, WD2, WD3, WD3-Impl, WD5, WD5-Impl).
    > Użycie podobnej do `http://www.myworkday.com` programu Works, ale nie `http://myworkday.com` . Skontaktuj się z [zespołem obsługi klienta Workday](https://www.workday.com/en-us/partners-services/services/support.html) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja Workday oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Aplikacja Workday oczekuje, że **NameIdentifier** ma być mapowany z **użytkownikiem. mail**, **UPN** itp., dlatego należy edytować Mapowanie atrybutów, klikając ikonę **Edytuj** i zmieniając mapowanie atrybutu.

    ![Zrzut ekranu przedstawia atrybuty użytkownika z wybraną ikoną Edytuj.](common/edit-attribute.png)

    > [!NOTE]
    > W tym miejscu zamapowano identyfikator nazwy z nazwą UPN (User. userPrincipalName) jako domyślną. Aby pomyślnie pracować z logowaniem jednokrotnym, należy zmapować identyfikator nazwy z rzeczywistym IDENTYFIKATORem użytkownika na koncie w usłudze Workday (adres e-mail, nazwę UPN itp.).

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. Aby zmodyfikować opcje **podpisywania** zgodnie z wymaganiami, kliknij przycisk **Edytuj** , aby otworzyć okno dialogowe **certyfikatu podpisywania SAML** .

    ![Certyfikat](common/edit-certificate.png) 

    ![Certyfikat podpisywania SAML](./media/workday-tutorial/signing-option.png)

    a. Wybierz opcję **Podpisz odpowiedź SAML i potwierdzenie** dla **podpisywania**.

    b. Kliknij pozycję **Zapisz**.

1. W sekcji **Konfigurowanie produktu Workday** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do produktu Workday.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Workday**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli żadna rola nie została skonfigurowana dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-workday"></a>Konfigurowanie produktu Workday

1. W innym oknie przeglądarki sieci Web Zaloguj się do swojej witryny firmowej jako administrator.

1. W **polu wyszukiwania** Wyszukaj nazwę **Edycja dzierżawy — zabezpieczenia** w lewym górnym rogu strony głównej.

    ![Edytuj zabezpieczenia dzierżawy](./media/workday-tutorial/IC782925.png "Edytuj zabezpieczenia dzierżawy")


1. W sekcji **Instalator języka SAML** wykonaj następujące czynności:

    ![Konfiguracja protokołu SAML](./media/workday-tutorial/IC782926.png "Konfiguracja protokołu SAML")

    a.  Wybierz pozycję **Włącz uwierzytelnianie SAML**.

    b.  Kliknij przycisk **Dodaj wiersz**.

1. W sekcji **dostawcy tożsamości SAML** wykonaj następujące akcje dla nowo utworzonego wiersza.

    a. Wykonaj następujące akcje dla pól, które są wyświetlane poniżej.

    ![Dostawcy tożsamości SAML 1](./media/workday-tutorial/IC7829271.png "Dostawcy tożsamości języka SAML")

    * W polu tekstowym **Nazwa dostawcy tożsamości** wpisz nazwę dostawcy (na przykład: *SPInitiatedSSO*).

    * W Azure Portal w sekcji **Konfigurowanie produktu Workday** skopiuj wartość **Identyfikator usługi Azure AD** , a następnie wklej ją do pola tekstowego **wystawcy** .

    * Otwórz pobrany **certyfikat** z Azure Portal do Notatnika i wklej zawartość do pola tekstowego **certyfikatu x. 509** .

    b. Wykonaj następujące akcje dla pól, które są wyświetlane poniżej.

    ![Dostawcy tożsamości SAML 2](./media/workday-tutorial/saml-identity-provider-2.png "Dostawcy tożsamości języka SAML")

    * Kliknij pole wyboru **Włącz wylogowanie zainicjowane przez dostawcy tożsamości** .

    * W polu tekstowym **adres URL odpowiedzi na wylogowanie** wpisz polecenie **http://www.workday.com** .

    * W polu tekstowym **adres URL żądania wylogowania** wklej wartość **adresu URL wylogowania** , która została skopiowana z Azure Portal.

    * Kliknij pole wyboru **SP zainicjowany** .

    * W polu tekstowym **Identyfikator dostawcy usług** wpisz **http://www.workday.com** .


    * Wybierz pozycję **nie rób korekt w zainicjowanym programie Sp żądanie uwierzytelniania**.

    c. Wykonaj następujące akcje dla pól, które są wyświetlane poniżej.

    ![Dostawcy tożsamości SAML 3](./media/workday-tutorial/saml-identity-provider-3.png "Dostawcy tożsamości języka SAML")

    * W Azure Portal w sekcji **Konfigurowanie produktu Workday** skopiuj wartość **adres URL logowania** , a następnie wklej ją do pola tekstowego **adres URL usługi SSO dostawcy tożsamości** .

    * W polu tekstowym **używane dla środowisk** wybierz odpowiednie nazwy środowisk z listy rozwijanej.

1. Wykonaj następujące kroki na poniższym obrazie.

    ![Workday](./media/workday-tutorial/service-provider.png "Dostawcy tożsamości języka SAML")

    a. W polu tekstowym **Identyfikator dostawcy usług (zostanie zaniechane)** wpisz polecenie **http://www.workday.com** .

    b. W polu tekstowym **adres URL usługi SSO dostawcy tożsamości (będzie przestarzały)** wpisz wartość **adresu URL logowania** .

    c. Wybierz pozycję **nie Wklęśnięcie żądań uwierzytelniania inicjowanych przez program SP (zostanie zaniechane)**.

    d. W przypadku **metody podpisu żądania uwierzytelniania** wybierz pozycję **SHA256**.

    e. Kliknij przycisk **OK**.

    > [!NOTE]
    > Upewnij się, że poprawnie skonfigurowano Logowanie jednokrotne. Jeśli włączysz logowanie jednokrotne przy użyciu nieprawidłowej konfiguracji, możesz nie być w stanie wprowadzić aplikacji z poświadczeniami i zablokować. W tej sytuacji dzień Workday zawiera adres URL logowania do kopii zapasowej, w którym użytkownicy mogą logować się przy użyciu swojej zwykłej nazwy użytkownika i hasła w następującym formacie: [adres URL w dniach roboczych]/login.Flex? redirect = n

### <a name="create-workday-test-user"></a>Utwórz użytkownika testowego produktu Workday

1. Zaloguj się do witryny firmowej w firmie Workday jako administrator.

1. Kliknij pozycję **profil** w prawym górnym rogu, wybierz opcję **Strona główna** , a następnie kliknij pozycję **katalog** na karcie **aplikacje** . 

1. Na stronie **katalog** wybierz pozycję **Znajdź pracowników** na karcie Widok.

    ![Znajdź pracowników](./media/workday-tutorial/user-directory.png)

1.  Na stronie **Znajdź pracowników** wybierz użytkownika z wyników.

1. Na poniższej stronie wybierz kolejno pozycje **zadanie > zabezpieczenia procesu roboczego** , a **konto Workday** musi pasować do usługi Azure Active Directory jako wartość **identyfikatora nazwy** .

    ![Zabezpieczenia procesu roboczego](./media/workday-tutorial/worker-security.png)

> [!NOTE]
> Aby uzyskać więcej informacji na temat tworzenia użytkownika testowego produktu Workday, skontaktuj się z [zespołem pomocy technicznej klienta](https://www.workday.com/en-us/partners-services/services/support.html)w dniu Workday.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

1. Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do produktu Workday, w którym można zainicjować przepływ logowania. 

2. Przejdź bezpośrednio do adresu URL logowania do produktu Workday i zainicjuj tam przepływ logowania.

3. Możesz użyć panelu programu Microsoft Access. Po kliknięciu kafelka Workday w panelu dostępu należy automatycznie zalogować się do dnia roboczego, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu produktu Workday można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)