---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z chmurą Saba | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Saba Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: 5ce9eb41755d7faa2ce00b38dfd971313443bfb7
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572693"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-cloud"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z chmurą Saba

W tym samouczku dowiesz się, jak zintegrować chmurę Saba z usługą Azure Active Directory (Azure AD). Gdy integrujesz chmurę Saba z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do chmury Saba.
* Zezwól użytkownikom na automatyczne logowanie do chmury Saba przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) w chmurze.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Chmura Saba obsługuje usługi **SP i dostawcy tożsamości** zainicjowane przez usługę SSO.
* Chmura Saba obsługuje Inicjowanie obsługi użytkowników **just in Time** .
* Aplikację mobilną w chmurze Saba można teraz skonfigurować za pomocą usługi Azure AD w celu włączenia logowania jednokrotnego. W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

## <a name="adding-saba-cloud-from-the-gallery"></a>Dodawanie chmury Saba z galerii

Aby skonfigurować integrację chmury Saba z usługą Azure AD, musisz dodać chmurę Saba z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **chmurę Saba** w polu wyszukiwania.
1. Wybierz pozycję **chmura Saba** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-saba-cloud"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla chmury Saba

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą chmury Saba przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w chmurze Saba.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą chmury Saba, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne w chmurze](#configure-saba-cloud-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego w chmurze Saba](#create-saba-cloud-test-user)** , aby dysponować odpowiednikiem B. Simon w chmurze Saba, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.
1. **[Przetestuj Logowanie jednokrotne w chmurze Saba (Mobile)](#test-sso-for-saba-cloud-mobile)** , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji w **chmurze Saba** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `<CUSTOMER_NAME>_SPLN_PRINCIPLE`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<SIGN-ON URL>/Saba/saml/SSO/alias/<ENTITY_ID>`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<CUSTOMER_NAME>.sabacloud.com`

    b. W polu tekstowym **stan przekaźnika** wpisz adres URL przy użyciu następującego wzorca: `IDP_INIT---SAML_SSO_SITE=<SITE_ID> ` lub w przypadku skonfigurowania protokołu SAML dla microsite wpisz adres URL, używając następującego wzorca: `IDP_INIT---SAML_SSO_SITE=<SITE_ID>---SAML_SSO_MICRO_SITE=<MicroSiteId>`

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat konfigurowania RelayState, Skorzystaj z [tego](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-idp-and-sp-initiated-sso-for-a-microsite.html) linku.

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora, adresu URL odpowiedzi, adresu URL logowania i stanu przekazywania. Aby uzyskać te wartości, skontaktuj się z [zespołem obsługi klienta w chmurze Saba](mailto:support@saba.com) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie chmury Saba** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do chmury Saba.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **chmura Saba**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-saba-cloud-sso"></a>Konfigurowanie logowania jednokrotnego w chmurze

1. Zaloguj się do firmowej witryny firmy w chmurze jako administrator.
1. Kliknij ikonę **menu** , a następnie kliknij pozycję **administrator**, a następnie wybierz kartę **administrator systemu** .

    ![zrzut ekranu dla administratora systemu](./media/saba-cloud-tutorial/system.png)

1. W obszarze **Konfigurowanie systemu** wybierz pozycję **Konfiguracja logowania jednokrotnego protokołu SAML** , a następnie kliknij przycisk Konfigurowanie protokołu **SAML SSO** . 

    ![zrzut ekranu przedstawiający konfigurację](./media/saba-cloud-tutorial/configure.png)

1. W oknie podręcznym wybierz pozycję **microsite** z listy rozwijanej, a następnie kliknij pozycję **Dodaj i skonfiguruj**.

    ![zrzut ekranu przedstawiający Dodawanie witryny/microsite](./media/saba-cloud-tutorial/microsite.png)

1. W sekcji **Konfigurowanie dostawcy tożsamości** kliknij przycisk **Przeglądaj** , aby przesłać plik **XML metadanych Federacji** pobrany z Azure Portal. Włącz pole wyboru **dostawcy tożsamości specyficzne dla lokacji** , a następnie kliknij przycisk **Importuj**.

    ![zrzut ekranu dotyczący importowania certyfikatów](./media/saba-cloud-tutorial/certificate.png) 

1. W sekcji **Konfigurowanie Sp** skopiuj wartość **aliasu jednostki** i wklej tę wartość do pola tekstowego **Identyfikator (identyfikator jednostki)** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal. Kliknij przycisk **Generuj**.

    ![zrzut ekranu przedstawiający Konfigurowanie programu SP](./media/saba-cloud-tutorial/generate-metadata.png) 

1. W sekcji **Konfigurowanie właściwości** Sprawdź wypełnione pola i kliknij przycisk **Zapisz**. 

    ![zrzut ekranu przedstawiający Konfigurowanie właściwości](./media/saba-cloud-tutorial/configure-properties.png) 

### <a name="create-saba-cloud-test-user"></a>Tworzenie użytkownika testowego w chmurze Saba

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w chmurze Saba. Chmura Saba obsługuje funkcję aprowizacji użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w chmurze Saba, zostanie utworzony nowy po uwierzytelnieniu.

> [!NOTE]
> Aby włączyć Inicjowanie obsługi użytkowników just in Time w usłudze w chmurze Saba, zapoznaj się z [tą](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-user-provisioning-with-saml.html) dokumentacją.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania w chmurze Saba, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania w chmurze Saba i zainicjuj tam przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do chmury Saba, dla której skonfigurowano Logowanie jednokrotne 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka Saba w chmurze w obszarze Moje aplikacje, jeśli skonfigurowano w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do chmury Saba, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

> [!NOTE]
> Jeśli adres URL logowania nie zostanie wypełniony w usłudze Azure AD, aplikacja jest traktowana jako dostawcy tożsamości tryb zainicjowany i jeśli zostanie wypełniony adres URL logowania, usługa Azure AD zawsze przekieruje użytkownika do aplikacji w chmurze Saba dla przepływu zainicjowanego przez dostawcę usług.

## <a name="test-sso-for-saba-cloud-mobile"></a>Testuj Logowanie jednokrotne dla chmury Saba (Mobile)

1. Otwórz aplikację mobilną w chmurze Saba, nadaj jej **nazwę** w polu tekstowym, a następnie kliknij przycisk **Enter**.

    ![Zrzut ekranu przedstawiający nazwę lokacji.](./media/saba-cloud-tutorial/site-name.png)

1. Wprowadź **adres e-mail** i kliknij przycisk **dalej**.

    ![Zrzut ekranu przedstawiający adres e-mail.](./media/saba-cloud-tutorial/email-address.png)

1. Na koniec po pomyślnym zalogowaniu zostanie wyświetlona strona aplikacji.

    ![Zrzut ekranu przedstawiający Pomyślne logowanie.](./media/saba-cloud-tutorial/dashboard.png)

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu chmury Saba można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


