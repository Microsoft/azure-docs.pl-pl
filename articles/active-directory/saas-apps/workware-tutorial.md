---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) za pomocą programu Working | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Work.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: f3f48695f0ef26b6e54073dbfc4bb0c61802fe58
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905724"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workware"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) za pomocą programu Work

W tym samouczku dowiesz się, jak zintegrować oprogramowanie robocze z usługą Azure Active Directory (Azure AD). Podczas integrowania programu Work z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do programu Work.
* Umożliwia użytkownikom automatyczne logowanie się do pracy z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) usługi Work.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program Working obsługuje **dostawcy tożsamości** zainicjowane Logowanie jednokrotne

## <a name="adding-workware-from-the-gallery"></a>Dodawanie programu Work z galerii

Aby skonfigurować integrację programu workowego z usługą Azure AD, należy dodać do listy zarządzanych aplikacji SaaS aplikacje z galerii.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje** .
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** .
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania pozycję **Work (Pracuj** ).
1. Wybierz pozycję **workion** from z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-workware"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla programu Work

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą programu Work przy użyciu użytkownika testowego o nazwie **B. Simon** . Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie Work.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu Work, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-workware-sso)** dla programu Work-in, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego](#create-workware-test-user)** , aby uzyskać odpowiednika B. Simon w worku służbowym, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **pracującej nad** aplikacjami Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne** .
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML** .
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `<WORKWARE_URL>/WW/AuthServices`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `<WORKWARE_URL>/WW/AuthServices/Acs`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem obsługi klienta służbowego](mailto:support@activeops.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie pakietu służbowego** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do programu Work.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje** .
1. Na liście Aplikacje wybierz pozycję program **Work** .
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy** .
1. Wybierz pozycję **Dodaj użytkownika** , a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-workware-sso"></a>Konfigurowanie logowania jednokrotnego dla służbowych zadań

Aby korzystać z funkcji logowania jednokrotnego w programie Workion, należy wykonać następujące czynności konfiguracyjne:

#### <a name="enable-sso-permissions-for-workware-system-administrators"></a>Włącz uprawnienia logowania jednokrotnego dla administratorów systemów roboczych

* Aby umożliwić administratorom systemów roboczych skonfigurowanie uwierzytelniania jednokrotnego, należy włączyć uprawnienie uwierzytelnianie logowania jednokrotnego (w **kategorii uprawnienia konfiguracja systemu > administrator w ustawieniach systemu > uprawnienia do ekranu roli** ) dla administratorów systemów roboczych.

    ![Uprawnienie uwierzytelniania SSO](./media/workware-tutorial/permission.png)

#### <a name="set-up-sso-authentication-in-workware"></a>Konfigurowanie uwierzytelniania SSO w programie Work

1. Przejdź do strony **Ustawienia systemu** , a następnie kliknij pozycję **uwierzytelnianie jednokrotne**

1. W sekcji **uwierzytelnianie logowania jednokrotnego** kliknij przycisk **Dodaj uwierzytelnianie Logowanie jednokrotne** , a następnie wykonaj następujące czynności: 

    ![Uwierzytelnianie SSO](./media/workware-tutorial/authentication.png)

    1. W **zewnętrznym dostawcy tożsamości** Podaj nazwę dostawcy tożsamości.
    1. Wybierz pozycję **SAML 2.0** jako **Typ uwierzytelniania**
    1. W polu tekstowym **adres URL logowania dostawcy tożsamości** wprowadź wartość **adresu URL logowania** , która została skopiowana z Azure Portal.
    1. W polu tekstowym **adres URL wystawcy dostawcy tożsamości** wprowadź wartość **identyfikatora usługi Azure AD** , która została skopiowana z Azure Portal.
    1. W polu tekstowym **adres URL wylogowania dostawcy tożsamości** wprowadź wartość **adres URL wylogowania** , która została skopiowana z Azure Portal.
    1. Kliknij pozycję **Włącz** .
    1. Przekaż pobrany **certyfikat** do **certyfikatu dostawcy tożsamości** z Azure Portal.
    1. Kliknij pozycję **Zapisz**


### <a name="create-workware-test-user"></a>Utwórz użytkownika testowego

1. Zaloguj się do witryny sieci Web programu Work jako administrator.

1. Wybierz pozycję **administrator > Utwórz/wyświetl > konta użytkowników > Dodaj nowe**

1. Wykonaj następujące czynności na poniższej stronie.

    ![Użytkownik testowy](./media/workware-tutorial/create-user.png)

    a. Podaj prawidłową nazwę w polu **Nazwa** .

    b. Wybierz **Typ uwierzytelniania** jako **Logowanie jednokrotne** .

    c. Wprowadź wymagane pola i kliknij przycisk **Zapisz** .

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Testuj tę aplikację w Azure Portal i należy automatycznie zalogować się do programu Work, dla którego skonfigurowano Logowanie jednokrotne

* Możesz użyć panelu programu Microsoft Access. Po kliknięciu kafelka pracy w panelu dostępu należy automatycznie zalogować się do programu workowego, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu pakietu służbowego można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


