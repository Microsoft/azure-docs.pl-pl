---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Roadmunk | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Roadmunk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 2ee794237c644cd6ff9652f4bc2e47a8ff590161
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93243909"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-roadmunk"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Roadmunk

W tym samouczku dowiesz się, jak zintegrować usługę Roadmunk z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Roadmunk z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Roadmunk.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Roadmunk przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Roadmunk.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Roadmunk obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne

## <a name="adding-roadmunk-from-the-gallery"></a>Dodawanie Roadmunk z galerii

Aby skonfigurować integrację programu Roadmunk z usługą Azure AD, musisz dodać Roadmunk z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Roadmunk** w polu wyszukiwania.
1. Wybierz pozycję **Roadmunk** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-roadmunk"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Roadmunk

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Roadmunk przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Roadmunk.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Roadmunk, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-roadmunk-sso)** w usłudze Roadmunk, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Roadmunk](#create-roadmunk-test-user)** , aby dysponować odpowiednikiem B. Simon w Roadmunk, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Roadmunk** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** , jeśli masz **plik metadanych dostawcy usług** i chcesz skonfigurować w trybie zainicjowania programu **dostawcy tożsamości** , wykonaj następujące czynności:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij pozycję **logo folderu** , aby wybrać plik metadanych, który został pobrany w kroku 4 w sekcji **Konfigurowanie Roadmunk rejestracji jednokrotnej** i kliknij przycisk **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatorów** i **adresów URL odpowiedzi** są automatycznie wypełniane w sekcji Podstawowa konfiguracja SAML.

    ![image1](common/idp-intiated.png)

    > [!Note]
    > Jeśli wartości **Identyfikator** i **Adres URL odpowiedzi** nie zostaną automatycznie wypełnione, wpisz te wartości ręcznie zgodnie z wymaganiami.

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    ![image2](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://login.roadmunk.com`

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie Roadmunk** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory** , wybierz pozycję **Użytkownicy** , a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Roadmunk.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Roadmunk**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika** , a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-roadmunk-sso"></a>Konfigurowanie logowania jednokrotnego Roadmunk

1. Zaloguj się do witryny sieci Web Roadmunk jako administrator.

1. Kliknij **ikonę użytkownika** w dolnej części strony i wybierz pozycję **Ustawienia konta**.

    ![Ustawienia konta](./media/roadmunk-tutorial/account.png)

1. Przejdź do pozycji **Ustawienia uwierzytelniania > firmy**.

1. W obszarze Ustawienia uwierzytelniania wykonaj następujące czynności.

    ![Ustawienia uwierzytelniania](./media/roadmunk-tutorial/saml-sso.png)

    a. Włącz logowanie jednokrotne **SAML (SSO)**.

    b. W kroku 1 można przekazać plik **XML metadanych** lub podać jego adres URL.

    c. W kroku 2 Pobierz plik **metadanych Roadmunk** i Zapisz go na komputerze.

    d. Włącz opcję **wymuś Sign-In SAML tylko** w kroku 3, jeśli chcesz zalogować się za pomocą logowania jednokrotnego.

    e. Kliknij pozycję **Zapisz**.


### <a name="create-roadmunk-test-user"></a>Utwórz użytkownika testowego Roadmunk

1. Zaloguj się do witryny sieci Web Roadmunk jako administrator.

1. Kliknij **ikonę użytkownika** w dolnej części strony i wybierz pozycję **Ustawienia konta**.

    ![Użytkownik testowy ustawień konta](./media/roadmunk-tutorial/account.png)

1. Przejdź do karty **Użytkownicy** i kliknij pozycję **Zaproś użytkownika**.

    ![Zaproś użytkownika](./media/roadmunk-tutorial/create-user.png)

1. Wprowadź wymagane pola w formularzu i kliknij pozycję **Zaproś**.


## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Roadmunk w panelu dostępu należy automatycznie zalogować się do Roadmunk, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Roadmunk można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


