---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Coralogix | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i platformą Coralogix.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: 17e2d04a39a2ef5a935ea6e56ef16958419eddb1
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91775645"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coralogix"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Coralogix

W tym samouczku dowiesz się, jak zintegrować usługę Coralogix z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Coralogix z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Coralogix.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Coralogix przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Coralogix.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Platforma Coralogix obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-coralogix-from-the-gallery"></a>Dodawanie platformy Coralogix z galerii

Aby skonfigurować integrację platformy Coralogix z usługą Azure AD, musisz dodać platformę Coralogix z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Coralogix** w polu wyszukiwania.
1. Wybierz pozycję **Coralogix** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-coralogix"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Coralogix

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Coralogix przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Coralogix.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Coralogix, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-coralogix-sso)** w usłudze Coralogix, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Coralogix](#create-coralogix-test-user)** , aby dysponować odpowiednikiem B. Simon w Coralogix, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Coralogix** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu **adres URL logowania** wprowadź adres URL z następującym wzorcem: `https://<SUBDOMAIN>.coralogix.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wprowadź adres URL, na przykład:
    
    `https://api.coralogix.com/saml/metadata.xml`

    lub

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zaktualizuj wartość przy użyciu rzeczywistego adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta Coralogix](mailto:info@coralogix.com) , aby uzyskać wartość. Można również odwołać się do wzorców w sekcji **podstawowe informacje o konfiguracji SAML** w Azure Portal.

 1. Aplikacja Coralogix oczekuje potwierdzeń SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wybierz przycisk **Edytuj** , aby otworzyć okno dialogowe **atrybuty użytkownika** .

    ![Zrzut ekranu pokazujący okno dialogowe "atrybuty użytkownika" z wyróżnionym przyciskiem "Edytuj".](common/edit-attribute.png)

1. W sekcji **oświadczenia użytkownika** w oknie dialogowym **atrybuty użytkownika** Edytuj oświadczenia przy użyciu ikony **Edytuj** . Możesz również dodać oświadczenia za pomocą polecenia **Dodaj nowe oświadczenie** , aby skonfigurować ATRYBUT tokenu SAML, jak pokazano na poprzedniej ilustracji. Następnie wykonaj następujące kroki:
    
    a. Wybierz **ikonę Edytuj** , aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkowników** .

    ![Zrzut ekranu przedstawiający okno dialogowe "atrybuty użytkownika & oświadczenia" z wyróżnionym przyciskiem "Edytuj".](./media/coralogix-tutorial/tutorial_usermail.png)
    ![obraz](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. Z listy **Format identyfikatora** wybierz pozycję **Adres e-mail**.

    c. Z listy **Atrybut źródłowy** wybierz pozycję **user.mail**.

    d. Wybierz pozycję **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie Coralogix** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Coralogix.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Coralogix**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-coralogix-sso"></a>Konfigurowanie logowania jednokrotnego Coralogix

Aby skonfigurować logowanie jednokrotne po stronie platformy **Coralogix**, musisz wysłać pobrany **plik XML metadanych federacji** i odpowiednie adresy URL skopiowane z witryny Azure Portal [zespołowi pomocy technicznej platformy Coralogix](mailto:info@coralogix.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-coralogix-test-user"></a>Tworzenie użytkownika testowego platformy Coralogix

W tej sekcji utworzysz użytkownika o nazwie Britta Simon na platformie Coralogix. Aby dodać użytkowników na platformie Coralogix, współpracuj z  [zespołem pomocy technicznej platformy Coralogix](mailto:info@coralogix.com). Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Coralogix w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Coralogix, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Coralogix z usługą Azure AD](https://aad.portal.azure.com/)

