---
title: 'Samouczek: integracja Azure Active Directory z usługą ArcGIS Enterprise | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją ArcGIS Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: ef64d857cb2215281b50617e030c634618e14dc4
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556578"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Samouczek: integracja Azure Active Directory z usługą ArcGIS Enterprise

W tym samouczku dowiesz się, jak zintegrować usługę ArcGIS Enterprise z usługą Azure Active Directory (Azure AD). W przypadku integracji usługi ArcGIS Enterprise z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do ArcGIS Enterprise.
* Zezwól użytkownikom na automatyczne logowanie do ArcGIS przedsiębiorstwa przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym w przedsiębiorstwie ArcGIS.

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa ArcGIS Enterprise obsługuje funkcję SSO zainicjowaną przez usługę **SP i dostawcy tożsamości** .
* ArcGIS Enterprise obsługuje funkcję udostępniania **just in Time** użytkownika.

## <a name="add-arcgis-enterprise-from-the-gallery"></a>Dodaj ArcGIS Enterprise z galerii

Aby skonfigurować integrację aplikacji ArcGIS Enterprise z usługą Azure AD, należy dodać aplikację ArcGIS Enterprise z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **ArcGIS Enterprise** w polu wyszukiwania.
1. Wybierz pozycję **ArcGIS Enterprise** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-arcgis-enterprise"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla usługi ArcGIS Enterprise

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą ArcGIS przedsiębiorstwa przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w ArcGIS Enterprise.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą ArcGIS Enterprise, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne w przedsiębiorstwie ArcGIS](#configure-arcgis-enterprise-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego ArcGIS Enterprise](#create-arcgis-enterprise-test-user)** , aby dysponować odpowiednikiem B. Simon w ArcGIS Enterprise, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja z aplikacją **ArcGIS Enterprise** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie **dostawcy tożsamości** zainicjowany:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `<EXTERNAL_DNS_NAME>.portal`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

    c. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej klienta aplikacji ArcGIS Enterprise](mailto:support@esri.com). Wartość identyfikatora można uzyskać z **sekcji Set Identity Provider (Ustawianie dostawcy tożsamości)**, co wyjaśniono w dalszej części tego samouczka.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do przedsiębiorstwa ArcGIS.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **ArcGIS Enterprise**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-arcgis-enterprise-sso"></a>Konfigurowanie logowania jednokrotnego w przedsiębiorstwie ArcGIS

1. Aby zautomatyzować konfigurację w ramach ArcGIS Enterprise, musisz zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Konfiguracja ArcGIS Enterprise** , aby kierować do aplikacji ArcGIS Enterprise. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi ArcGIS Enterprise. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować ArcGIS Enterprise, zaloguj się do witryny firmowej firmy ArcGIS jako administrator.


1. Wybierz pozycję **Organization >EDIT SETTINGS** (Organizacja > EDYTUJ USTAWIENIA).

    ![Zrzut ekranu przedstawia kartę ArcGIS Enterprise Organization z ustawieniami edycji o nazwie out.](./media/arcgisenterprise-tutorial/configure-1.png)

1. Wybierz kartę **Security** (Zabezpieczenia).

    ![Zrzut ekranu przedstawia wybraną kartę Zabezpieczenia.](./media/arcgisenterprise-tutorial/configure-2.png)

1. Przewiń w dół do sekcji **Enterprise Logins via SAML** (Logowanie dla przedsiębiorstwa za pomocą protokołu SAML) i wybierz pozycję **SET ENTERPRISE LOGIN** (USTAW LOGOWANIE DLA PRZEDSIĘBIORSTWA).

    ![Zrzut ekranu przedstawia identyfikatory logowania przedsiębiorstwa za pośrednictwem protokołu SAML, w którym można wybrać opcję Ustaw identyfikator logowania przedsiębiorstwa.](./media/arcgisenterprise-tutorial/configure-3.png)

1. W sekcji **Set Identity Provider** (Ustawianie dostawcy tożsamości) wykonaj następujące działania:

    ![Zrzut ekranu przedstawia Ustawianie dostawcy tożsamości, w którym wykonywane są kroki opisane tutaj.](./media/arcgisenterprise-tutorial/configure-4.png)

    a. W polu tekstowym **Name** (Nazwa) podaj nazwę, na przykład **Test usługi Azure Active Directory**.

    b. W polu tekstowym **URL** (Adres URL) wklej wartość **adresu URL metadanych federacyjnych aplikacji**, którą skopiowano z witryny Azure Portal.

    c. Kliknij pozycję **Show advanced settings** (Pokaż ustawienia zaawansowane) i skopiuj wartość z pola **Entity ID** (Identyfikator jednostki), a następnie wklej ją w polu tekstowym **Identyfikator** w sekcji **Domena i adresy URL aplikacji ArcGIS Enterprise** w witrynie Azure Portal.

    ![Zrzut ekranu pokazuje, gdzie uzyskać jednostkę I D i zaktualizować dostawcę.](./media/arcgisenterprise-tutorial/configure-5.png)

    d. Kliknij przycisk **UPDATE IDENTITY PROVIDER** (ZAKTUALIZUJ DOSTAWCĘ TOŻSAMOŚCI).

### <a name="create-arcgis-enterprise-test-user"></a>Tworzenie użytkownika testowego aplikacji ArcGIS Enterprise

W tej sekcji w aplikacji ArcGIS Enterprise jest tworzony użytkownik o nazwie Britta Simon. Aplikacja ArcGIS Enterprise obsługuje aprowizację użytkowników just in time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji ArcGIS Enterprise, zostanie utworzony po uwierzytelnieniu.

> [!Note]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej ArcGIS Enterprise](mailto:support@esri.com).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do ArcGIS przedsiębiorstwa, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania do usługi ArcGIS Enterprise i zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do przedsiębiorstwa ArcGIS, dla którego skonfigurowano Logowanie jednokrotne. 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka ArcGIS Enterprise w obszarze Moje aplikacje, jeśli zostanie on skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do przedsiębiorstwa ArcGIS Enterprise, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu ArcGIS Enterprise można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
