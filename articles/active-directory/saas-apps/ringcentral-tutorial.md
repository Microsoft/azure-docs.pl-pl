---
title: 'Samouczek: integracja Azure Active Directory z usługą RingCentral | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i RingCentral.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 20e59f134c51662f9530862790d5a47b8bb21d6d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650774"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Samouczek: integracja RingCentral z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę RingCentral z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi RingCentral z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do RingCentral.
* Zezwól użytkownikom na automatyczne logowanie się do usługi RingCentral przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) RingCentral.

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa RingCentral obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne.

## <a name="add-ringcentral-from-the-gallery"></a>Dodaj RingCentral z galerii

Aby skonfigurować integrację programu RingCentral z usługą Azure AD, musisz dodać RingCentral z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **RingCentral** w polu wyszukiwania.
1. Wybierz pozycję **RingCentral** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-ringcentral"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla RingCentral

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą RingCentral przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w RingCentral.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą RingCentral, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-ringcentral-sso)** w usłudze RingCentral, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego RingCentral](#create-ringcentral-test-user)** , aby dysponować odpowiednikiem B. Simon w RingCentral, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **RingCentral** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    1. Kliknij pozycję **Przekaż plik metadanych**.
    1. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.
    1. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatorów** i **adresów URL odpowiedzi** są automatycznie wypełniane w sekcji **Podstawowa konfiguracja SAML** .

    > [!Note]
    > **Plik metadanych dostawcy usług** jest pobierany na stronie konfiguracji rejestracji jednokrotnej RingCentral, która została omówiona w dalszej części tego samouczka.

1. Jeśli nie masz **pliku metadanych dostawcy usług**, wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz jeden z adresów URL:
  
    | Identyfikator |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. W polu tekstowym **adres URL odpowiedzi** wpisz jeden z adresów URL:

    | Adres URL odpowiedzi |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie Britta Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `Britta Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi RingCentral.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **RingCentral**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-ringcentral-sso"></a>Konfigurowanie logowania jednokrotnego RingCentral

1. Aby zautomatyzować konfigurację w programie RingCentral, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj** , aby przekierować użytkownika do aplikacji RingCentral. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi RingCentral. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować RingCentral, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy RingCentral jako administrator i wykonaj następujące czynności:

1. W górnej części kliknij pozycję **Narzędzia**.

    ![Zrzut ekranu przedstawia narzędzia wybrane z witryny firmy RingCentral.](./media/ringcentral-tutorial/ringcentral-1.png)

1. Przejdź do **logowania jednokrotnego**.

    ![Zrzut ekranu przedstawia pojedyncze Sign-On wybrane z menu Narzędzia.](./media/ringcentral-tutorial/ringcentral-2.png)

1. Na stronie **Logowanie** jednokrotne w obszarze **Konfiguracja logowania jednokrotnego** z **kroku 1** kliknij przycisk **Edytuj** i wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia stronę konfiguracji S-O, na której można wybrać opcję Edytuj.](./media/ringcentral-tutorial/ringcentral-3.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia stronę Konfigurowanie pojedynczego Sign-On, na której można przekazać I D P metadanych.](./media/ringcentral-tutorial/ringcentral-4.png)

    a. Kliknij przycisk **Przeglądaj** , aby przekazać plik metadanych pobrany z Azure Portal.

    b. Po przekazaniu metadanych wartości są automatycznie wypełniane w sekcji **Informacje ogólne logowania jednokrotnego** .

    c. W sekcji **Mapowanie atrybutów** wybierz pozycję **Mapuj atrybut poczty e-mail na** jako `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Kliknij pozycję **Zapisz**.

    e. W **kroku 2** kliknij **pozycję Pobierz** , aby pobrać **plik metadanych dostawcy usług** i przekazać go w sekcji **Podstawowa konfiguracja języka SAML** , aby automatycznie wypełnić wartości **identyfikatorów** i **adresów URL odpowiedzi** w Azure Portal.

    ![Zrzut ekranu przedstawia stronę konfiguracji S-O, na której można wybrać pozycję Pobierz.](./media/ringcentral-tutorial/ringcentral-6.png) 

    f. Na tej samej stronie przejdź do sekcji **Włączanie logowania jednokrotnego** i wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia sekcję Enable S O, w której można zakończyć konfigurację.](./media/ringcentral-tutorial/ringcentral-5.png)

    * Wybierz pozycję **Włącz usługę SSO**.

    * Wybierz opcję **zezwól użytkownikom na logowanie się przy użyciu poświadczeń logowania jednokrotnego lub RingCentral**.

    * Kliknij pozycję **Zapisz**.

### <a name="create-ringcentral-test-user"></a>Utwórz użytkownika testowego RingCentral

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w RingCentral. Współpracuj z [zespołem obsługi klienta RingCentral](https://success.ringcentral.com/RCContactSupp) , aby dodać użytkowników z platformy RingCentral. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Testuj tę aplikację w Azure Portal i należy automatycznie zalogować się do RingCentral, dla którego skonfigurowano Logowanie jednokrotne.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka RingCentral w obszarze Moje aplikacje należy automatycznie zalogować się do RingCentral, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu RingCentral można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).