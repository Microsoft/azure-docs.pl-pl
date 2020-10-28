---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą usługi Pipedrive | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i usługi PipeDrive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: jeedes
ms.openlocfilehash: f85cb97406e8b6cbb4811268696fc36f47ec3adb
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896552"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pipedrive"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą usługi Pipedrive

W tym samouczku dowiesz się, jak zintegrować usługę usługi Pipedrive z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi usługi Pipedrive z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do usługi PipeDrive.
* Zezwól użytkownikom na automatyczne logowanie się do usługi usługi Pipedrive przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) usługi PipeDrive.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługi Pipedrive obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne
* Po skonfigurowaniu logowania jednokrotnego usługi Pipedrive można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).


## <a name="adding-pipedrive-from-the-gallery"></a>Dodawanie usługi Pipedrive z galerii

Aby skonfigurować integrację programu usługi Pipedrive z usługą Azure AD, musisz dodać usługi Pipedrive z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje** .
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** .
1. W sekcji **Dodaj z galerii** wpisz **usługi Pipedrive** w polu wyszukiwania.
1. Wybierz pozycję **usługi Pipedrive** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-pipedrive"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla usługi Pipedrive

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą usługi Pipedrive przy użyciu użytkownika testowego o nazwie **B. Simon** . Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usługi PipeDrive.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą usługi Pipedrive, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-pipedrive-sso)** w usłudze usługi Pipedrive, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego usługi Pipedrive](#create-pipedrive-test-user)** , aby dysponować odpowiednikiem B. Simon w usługi Pipedrive, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **usługi Pipedrive** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne** .
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML** .
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp/metadata.xml`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<COMPANY-NAME>.pipedrive.com/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta usługi Pipedrive](mailto:support@pipedrive.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja usługi Pipedrive oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja usługi Pipedrive oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ------------ | --------- |
    | poczta e-mail | user.mail |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze, a także skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](./media/pipedrive-tutorial/certificate-data.png)

1. W sekcji **Konfigurowanie usługi Pipedrive** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory** , wybierz pozycję **Użytkownicy** , a następnie wybierz pozycję **Wszyscy użytkownicy** .
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło** .
   1. Kliknij pozycję **Utwórz** .

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi usługi PipeDrive.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje** .
1. Na liście Aplikacje wybierz pozycję **usługi Pipedrive** .
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy** .

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika** , a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-pipedrive-sso"></a>Konfigurowanie logowania jednokrotnego usługi Pipedrive

1. W innym oknie przeglądarki Zaloguj się do witryny sieci Web usługi Pipedrive jako administrator.

1. Kliknij pozycję **profil użytkownika** , a następnie wybierz pozycję **Ustawienia** .

    ![Zrzut ekranu pokazujący "Ustawienia" wybrane z menu "profil użytkownika".](./media/pipedrive-tutorial/configure1.png)

1. Przewiń w dół do centrum zabezpieczeń i wybierz pozycję **Logowanie jednokrotne** .

    ![Zrzut ekranu przedstawiający "Logowanie jednokrotne" wybrane w "Security Center".](./media/pipedrive-tutorial/configure2.png)

1. W sekcji **Konfiguracja SAML dla usługi Pipedrive** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający sekcję "S A M L dla usługi Pipedrive" z wyróżnionymi wszystkimi polami tekstowymi.](./media/pipedrive-tutorial/configure3.png)

    a. W polu tekstowym **wystawca** wklej wartość **adresu URL metadanych federacji aplikacji** , która została skopiowana z Azure Portal.

    b. W polu tekstowym adres URL rejestracji jednokrotnej **(SSO)** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    c. W polu tekstowym **adres URL wylogowania jednokrotnego (SLO)** wklej wartość **adresu URL wylogowania** , która została skopiowana z Azure Portal.

    d. W polu tekstowym **certyfikat x. 509** Otwórz pobrany plik **certyfikatu (Base64)** z Azure Portal do Notatnika i skopiuj jego zawartość i wklej do pola tekstowego **certyfikatu x. 509** i Zapisz zmiany.

### <a name="create-pipedrive-test-user"></a>Utwórz użytkownika testowego usługi Pipedrive

1. W innym oknie przeglądarki Zaloguj się do witryny sieci Web usługi Pipedrive jako administrator.

1. Przewiń w dół do firmy i wybierz pozycję **Zarządzaj użytkownikami** .

    ![Zrzut ekranu pokazujący "Zarządzanie użytkownikami" wybrany w menu "Firma".](./media/pipedrive-tutorial/user1.png)

1. Kliknij pozycję **Dodaj użytkowników** .
    
    ![Zrzut ekranu pokazujący stronę "Zarządzanie użytkownikami" z przyciskiem "Dodaj użytkowników" wybranym po prawej stronie.](./media/pipedrive-tutorial/user2.png)

1. W sekcji **Zarządzanie użytkownikami** wykonaj następujące czynności:

    ![Konfiguracja usługi Pipedrive](./media/pipedrive-tutorial/user3.png)

    a. W polu tekstowym **adres e-mail** wprowadź adres e-mail użytkownika, np `B.Simon@contoso.com` .

    b. W polu tekstowym **imię i nazwisko** Wprowadź imię użytkownika.

    c. W polu **tekstowym nazwisko Wprowadź nazwisko użytkownika** .

    d. Kliknij przycisk **Potwierdź i zaproś użytkowników** .

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi Pipedrive w panelu dostępu należy automatycznie zalogować się do usługi Pipedrive, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj usługi Pipedrive z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)