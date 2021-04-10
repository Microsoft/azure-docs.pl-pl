---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) za pomocą zapory aplikacji sieci Web FortiWeb | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i zaporą aplikacji sieci Web FortiWeb.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/24/2020
ms.author: jeedes
ms.openlocfilehash: e34664bd81023da7a50b8ff4645c670146ef2554
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98731939"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortiweb-web-application-firewall"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) przy użyciu zapory aplikacji sieci Web FortiWeb

W tym samouczku dowiesz się, jak zintegrować zaporę aplikacji sieci Web FortiWeb z usługą Azure Active Directory (Azure AD). Gdy integrujesz zaporę aplikacji sieci Web FortiWeb z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do zapory aplikacji sieci Web FortiWeb.
* Zezwól użytkownikom na automatyczne logowanie do zapory aplikacji sieci Web FortiWeb za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) zapory aplikacji sieci Web FortiWeb.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Zapora aplikacji sieci Web FortiWeb obsługuje usługę **SP** zainicjowaną przez usługę SSO.

## <a name="adding-fortiweb-web-application-firewall-from-the-gallery"></a>Dodawanie zapory aplikacji sieci Web FortiWeb z galerii

Aby skonfigurować integrację zapory aplikacji sieci Web FortiWeb w usłudze Azure AD, musisz dodać zaporę aplikacji sieci Web FortiWeb z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodawanie z galerii** wpisz **FortiWeb zapory aplikacji sieci Web** w polu wyszukiwania.
1. Wybierz opcję **Zapora aplikacji sieci Web FortiWeb** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-fortiweb-web-application-firewall"></a>Konfigurowanie i testowanie usługi Azure AD SSO dla zapory aplikacji sieci Web FortiWeb

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą zapory aplikacji sieci Web FortiWeb przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w zaporze aplikacji sieci Web FortiWeb.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą zapory aplikacji sieci Web FortiWeb, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj zaporę aplikacji sieci Web FortiWeb Logowanie jednokrotne](#configure-fortiweb-web-application-firewall-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego zapory aplikacji sieci Web FortiWeb](#create-fortiweb-web-application-firewall-test-user)** , aby dysponować odpowiednikiem B. Simon w zaporze aplikacji sieci Web FortiWeb, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **zapory aplikacji sieci Web FortiWeb** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

   a. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://www.<CUSTOMER_DOMAIN>.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://www.<CUSTOMER_DOMAIN>.com/<FORTIWEB_NAME>/saml.sso/SAML2/POST`

    c. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://www.<CUSTOMER_DOMAIN>.com`

    d. W polu tekstowym **adres URL wylogowywania** wpisz adres URL, używając następującego wzorca: `https://www.<CUSTOMER_DOMAIN>.info/<FORTIWEB_NAME>/saml.sso/SLO/POST`
 
    > [!NOTE]
    > `<FORTIWEB_NAME>` jest identyfikatorem nazwy, który będzie później używany podczas dostarczania konfiguracji do FortiWeb.
    > Skontaktuj się z [zespołem pomocy technicznej aplikacji sieci Web FortiWeb](mailto:support@fortinet.com) , aby uzyskać prawdziwe wartości adresu URL. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)


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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do zapory aplikacji sieci Web FortiWeb.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz opcję **Zapora aplikacji sieci Web FortiWeb**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-fortiweb-web-application-firewall-sso"></a>Konfigurowanie zapory aplikacji sieci Web FortiWeb Logowanie jednokrotne

1.  Przejdź do `https://<address>:8443` lokalizacji `<address>` , gdzie jest nazwa FQDN lub publiczny adres IP przypisany do maszyny wirtualnej FortiWeb.

2.  Zaloguj się przy użyciu poświadczeń administratora podanych podczas wdrażania maszyny wirtualnej FortiWeb.

1. Wykonaj następujące czynności na poniższej stronie.

    ![Strona serwera SAML](./media/fortiweb-web-application-firewall-tutorial/configure-sso.png)

    a.  W menu po lewej stronie kliknij pozycję **użytkownik**.

    b.  W obszarze Użytkownik kliknij pozycję **serwer zdalny**.

    c.  Kliknij pozycję **serwer SAML**.

    d.  Kliknij przycisk **Create New** (Utwórz nowego).

    e.  W polu **Nazwa** podaj wartość `<fwName>` użytą w sekcji Konfigurowanie usługi Azure AD.

    f.  W polu tekstowym **Identyfikator jednostki** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z Azure Portal.

    przykład Obok pozycji **metadane** kliknij pozycję **Wybierz plik** , a następnie wybierz plik **XML metadanych Federacji** pobrany z Azure Portal.

    h.  Kliknij przycisk **OK**.

### <a name="create-a-site-publishing-rule"></a>Tworzenie reguły publikowania witryny

1.  Przejdź do `https://<address>:8443` lokalizacji `<address>` , gdzie jest nazwa FQDN lub publiczny adres IP przypisany do maszyny wirtualnej FortiWeb.

1.  Zaloguj się przy użyciu poświadczeń administratora podanych podczas wdrażania maszyny wirtualnej FortiWeb.
1. Wykonaj następujące czynności na poniższej stronie.

    ![Reguła publikowania witryny](./media/fortiweb-web-application-firewall-tutorial/site-publish-rule.png)

    a.  W menu po lewej stronie kliknij pozycję **dostarczanie aplikacji**.
    
    b.  W obszarze **dostarczanie aplikacji** kliknij pozycję **Publikuj w witrynie**.
    
    c.  W obszarze **Publikowanie w witrynie** kliknij pozycję Publikuj w **witrynie**.
    
    d.  Kliknij pozycję **Witryna Publikuj regułę**.
    
    e.  Kliknij przycisk **Create New** (Utwórz nowego).
    
    f.  Podaj nazwę reguły publikacji witryny.
    
    przykład  Obok pozycji **Typ opublikowanej witryny** kliknij pozycję **wyrażenie regularne**.
    
    i.  W obszarze **opublikowana witryna** Podaj ciąg, który będzie pasować do nagłówka hosta witryny sieci Web, która jest publikowana.
    
    j.  Obok pozycji **ścieżka** podaj wartość/.
    
    k.  W obszarze **Metoda uwierzytelniania klienta** wybierz pozycję **uwierzytelnianie SAML**.
    
    l.  Z listy rozwijanej **serwer SAML** wybierz utworzony wcześniej serwer SAML.
    
    m.  Kliknij przycisk **OK**.


### <a name="create-a-site-publishing-policy"></a>Tworzenie zasad publikowania witryny

1.  Przejdź do `https://<address>:8443` lokalizacji `<address>` , gdzie jest nazwa FQDN lub publiczny adres IP przypisany do maszyny wirtualnej FortiWeb.

2.  Zaloguj się przy użyciu poświadczeń administratora podanych podczas wdrażania maszyny wirtualnej FortiWeb.

1. Wykonaj następujące czynności na poniższej stronie.

    ![Zasady publikowania lokacji](./media/fortiweb-web-application-firewall-tutorial/site-publish-policy.png)

    a.  W menu po lewej stronie kliknij pozycję **dostarczanie aplikacji**.

    b.  W obszarze **dostarczanie aplikacji** kliknij pozycję **Publikuj w witrynie**.

    c.  W obszarze **Publikowanie w witrynie** kliknij pozycję Publikuj w **witrynie**.

    d.  Kliknij pozycję **Witryna publikowanie zasad**.

    e.  Kliknij przycisk **Create New** (Utwórz nowego).

    f.  Podaj nazwę zasad publikowania witryn.

    przykład  Kliknij przycisk **OK**.

    h.  Kliknij przycisk **Create New** (Utwórz nowego).

    i.  Z listy rozwijanej **reguła** Wybierz utworzoną wcześniej regułę publikowania witryn.

    j.  Kliknij przycisk **OK**.

### <a name="create-and-assign-a-web-protection-profile"></a>Tworzenie i Przypisywanie profilu ochrony sieci Web

1.  Przejdź do `https://<address>:8443` lokalizacji `<address>` , gdzie jest nazwa FQDN lub publiczny adres IP przypisany do maszyny wirtualnej FortiWeb.

2.  Zaloguj się przy użyciu poświadczeń administratora podanych podczas wdrażania maszyny wirtualnej FortiWeb.
3.  W menu po lewej stronie kliknij pozycję **zasady**.
4.  W obszarze **zasady** kliknij pozycję **profil ochrony sieci Web**.
5.  Kliknij pozycję **Wbudowana ochrona standardowa** i kliknij pozycję **Klonuj**.
6.  Podaj nazwę nowego profilu ochrony sieci Web i kliknij przycisk **OK**.
7.  Wybierz nowy profil ochrony sieci Web, a następnie kliknij przycisk **Edytuj**.
8.  Obok pozycji **Publikowanie w witrynie** wybierz utworzone wcześniej zasady publikowania lokacji.
9.  Kliknij przycisk **OK**.
 
    ![Publikowanie witryny](./media/fortiweb-web-application-firewall-tutorial/web-protection.png)

10. W menu po lewej stronie kliknij pozycję **zasady**.
11. W obszarze **zasady** kliknij pozycję **zasady serwera**.
12. Wybierz zasady serwera używane do publikowania witryny sieci Web, dla której chcesz używać Azure Active Directory do uwierzytelniania.
13. Kliknij pozycję **Edytuj**.
14. Z listy rozwijanej **profil ochrony sieci Web** wybierz właśnie utworzony profil ochrony sieci Web.
15. Kliknij przycisk **OK**.
16. Podjęto próbę uzyskania dostępu do zewnętrznego adresu URL, do którego FortiWeb publikuje witrynę sieci Web. Należy przekierować do Azure Active Directory na potrzeby uwierzytelniania.


### <a name="create-fortiweb-web-application-firewall-test-user"></a>Tworzenie użytkownika testowego zapory aplikacji sieci Web FortiWeb

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w zaporze aplikacji sieci Web FortiWeb. Współpraca z [zespołem pomocy technicznej aplikacji sieci Web FortiWeb](mailto:support@fortinet.com) w celu dodania użytkowników z platformy zapory aplikacji sieci Web FortiWeb. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania aplikacji sieci Web FortiWeb, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania aplikacji sieci Web FortiWeb i zainicjuj tam przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka aplikacja sieci Web FortiWeb w obszarze Moje aplikacje spowoduje to przekierowanie do adresu URL logowania aplikacji sieci Web FortiWeb. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu zapory aplikacji sieci Web FortiWeb można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).