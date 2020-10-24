---
title: 'Samouczek: integracja z logowaniem jednokrotnym (SSO) Azure Active Directory z użyciem pulsu Secure Virtual Traffic Manager | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory a bezpiecznymi Traffic Managerami wirtualnymi.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: jeedes
ms.openlocfilehash: 1e295075a5c1ae8daa6673757770bbef01208c1d
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92505747"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-virtual-traffic-manager"></a>Samouczek: integracja z logowaniem jednokrotnym (SSO) Azure Active Directory z użyciem pulsu Secure Virtual Traffic Manager

W tym samouczku dowiesz się, jak zintegrować bezpieczne wirtualne Traffic Manager z usługą Azure Active Directory (Azure AD). W przypadku integrowania pulsu Secure Virtual Traffic Manager z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do pulsu bezpiecznej Traffic Manager wirtualnej.
* Zezwól użytkownikom na automatyczne logowanie się w celu pulsowania bezpiecznych Traffic Manager wirtualnych przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Zażądaj subskrypcji z włączoną funkcją logowania jednokrotnego (SSO) usługi Virtual Traffic Manager.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Pulse Secure Virtual Traffic Manager obsługują usługę **SP** zainicjowaną przez usługę SSO

## <a name="adding-pulse-secure-virtual-traffic-manager-from-the-gallery"></a>Dodawanie pulsu bezpiecznej Traffic Manager wirtualnej z galerii

Aby skonfigurować integrację Traffic Manager z usługą Azure AD, należy dodać Pulse Secure Virtual Traffic Manager z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Pulse Secure Virtual Traffic Manager** w polu wyszukiwania.
1. Wybierz pozycję **Pulse bezpieczne wirtualne Traffic Manager** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-virtual-traffic-manager"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla pulsu Secure Virtual Traffic Manager

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą pulsu Secure Virtual Traffic Manager przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w bezpiecznym Traffic Manager wirtualnym pulsu.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu pulsu Secure Virtual Traffic Manager, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj bezpieczny Traffic Manager Logowanie jednokrotne](#configure-pulse-secure-virtual-traffic-manager-sso)** w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Tworzenie pulsu bezpiecznego wirtualnego Traffic Manager testowego użytkownika](#create-pulse-secure-virtual-traffic-manager-test-user)** w celu uzyskania odpowiedników B. Simon w usłudze Pulse secure Virtual Traffic Manager, która jest połączona z reprezentacją użytkownika usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie **Pulse bezpiecznego wirtualnego Traffic Manager** aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<published virtual server FQDN>/saml/consume`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<published virtual server FQDN>/saml/metadata`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<published virtual server FQDN>/saml/consume`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej dotyczącej programu Virtual Traffic Manager Client](mailto:support@pulsesecure.net) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie opcji Pulse bezpieczne wirtualne Traffic Manager** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi pulsu Secure Virtual Traffic Manager.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Pulse Secure Virtual Traffic Manager**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-pulse-secure-virtual-traffic-manager-sso"></a>Konfigurowanie pulsu Secure Virtual Traffic Manager Logowanie jednokrotne

W tej sekcji omówiono konfigurację wymaganą do włączenia uwierzytelniania SAML usługi Azure AD na Traffic Manager wirtualnym pulsu. Wszystkie zmiany konfiguracji są wprowadzane w Traffic Managerach wirtualnych pulsu przy użyciu interfejsu użytkownika administratora sieci Web. 

#### <a name="create-a-saml-trusted-identity-provider"></a>Tworzenie dostawcy zaufanej tożsamości SAML

a. Przejdź do katalogu **> > > interfejsu użytkownika programu Virtual Traffic Manager urządzenia** , a następnie kliknij pozycję **Edytuj**.

![Strona wykazów SAML](./media/pulse-secure-virtual-traffic-manager-tutorial/saml-catalogs.png)

b. Dodaj szczegóły nowego dostawcy zaufanych tożsamości SAML, kopiując informacje z aplikacji Azure AD Enterprise na stronie ustawień logowania jednokrotnego, a następnie klikając pozycję **Utwórz nowego dostawcę zaufanej tożsamości**.

![Utwórz nowego zaufanego dostawcę tożsamości](./media/pulse-secure-virtual-traffic-manager-tutorial/create-trusted-identity-provider.png)

* W polu tekstowym **Nazwa** wprowadź nazwę zaufanego dostawcy tożsamości. 

* W polu tekstowym **Entity_id** wprowadź wartość **identyfikatora usługi Azure AD** skopiowaną z Azure Portal.  

* W polu tekstowym **adres URL** wprowadź wartość **adresu URL logowania** , która została skopiowana z Azure Portal. 

* Otwórz pobrany **certyfikat** z Azure Portal do Notatnika i wklej zawartość do pola tekstowego **certyfikatu** .

c. Sprawdź, czy nowy dostawca tożsamości SAML został pomyślnie utworzony. 

![Weryfikowanie zaufanego dostawcy tożsamości](./media/pulse-secure-virtual-traffic-manager-tutorial/verify-saml-identity-provider.png)

#### <a name="configure-the-virtual-server-to-use-azure-ad-authentication"></a>Konfigurowanie serwera wirtualnego do korzystania z uwierzytelniania usługi Azure AD

a. Przejdź do strony programu **Pulse virtual Traffic Manager urządzenie > interfejsu użytkownika > serwery wirtualne** , a następnie kliknij pozycję **Edytuj** obok wcześniej utworzonego serwera wirtualnego.

![Edycja serwerów wirtualnych](./media/pulse-secure-virtual-traffic-manager-tutorial/virtual-servers.png)

b. W sekcji **uwierzytelnianie** kliknij przycisk **Edytuj**. 

![Sekcja uwierzytelniania](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication.png)

c. Skonfiguruj następujące ustawienia uwierzytelniania dla serwera wirtualnego: 

1. Ponowne

    ![ustawienia uwierzytelniania dla serwera wirtualnego](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-1.png)

    a. W obszarze **Typ uwierzytelniania**wybierz pozycję **Dostawca usługi SAML** . 

    b. W polu **AUTH! verbose**ustaw wartość "tak", aby rozwiązać problemy z uwierzytelnianiem. w przeciwnym razie pozostaw wartość domyślną "nie" 

2. Zarządzanie sesjami uwierzytelniania —

    ![Zarządzanie sesjami uwierzytelniania](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-session.png)

    a. Dla elementu **AUTH! Session! cookie_name**pozostaw wartość domyślną "VS_SamlSP_Auth" 

    b. Dla elementu **AUTH! Session! Timeout**pozostaw wartość domyślną "7200" 

    c. W obszarze **AUTH! Session! log_external_state**ustaw wartość "tak", aby rozwiązać problemy z uwierzytelnianiem. w przeciwnym razie pozostaw wartość domyślną "nie" 

    d. W obszarze **AUTH! Session! cookie_attributes**Zmień wartość na "HTTPOnly". 

3. Dostawca usługi SAML —

    ![Dostawca usług SAML](./media/pulse-secure-virtual-traffic-manager-tutorial/saml-service-provider.png)

    a. W polu tekstowym Authentication **! SAML! sp_entity_id** ustaw na ten sam adres URL, który jest używany jako identyfikator konfiguracji logowania jednokrotnego usługi Azure AD (identyfikator jednostki). Lubię to `https://pulseweb.labb.info/saml/metadata` . 

    b. W polu Authentication **! SAML! sp_acs_url**ustaw na ten sam adres URL, który jest używany jako adres URL powtarzania konfiguracji logowania jednokrotnego w usłudze Azure AD (adres URL usługi konsumenckej potwierdzenia). Lubię to `https://pulseweb.labb.info/saml/consume` . 

    c. W obszarze **uwierzytelnianie! SAML! dostawcy tożsamości**wybierz **zaufanego dostawcę tożsamości** utworzonego w poprzednim kroku. 

    d. W time_tolerance auth! SAML! pozostaw wartość domyślną "5" s. 

    e. W nameid_format auth! SAML! wybierz pozycję **nieokreślone**.

    f. Zastosuj zmiany, klikając przycisk **Aktualizuj** w dolnej części strony.
    
### <a name="create-pulse-secure-virtual-traffic-manager-test-user"></a>Utwórz użytkownika testu pulsu Secure Virtual Traffic Manager

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w obszarze Pulse Secure Virtual Traffic Manager. Pracuj z [zespołem pomocy technicznej dotyczącej pulsu z bezpiecznymi Traffic Manager wirtualnymi](mailto:support@pulsesecure.net) , aby dodać użytkowników na platformie pulsu secure Virtual Traffic Manager. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

1. Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL bezpiecznego logowania Traffic Manager wirtualnego, w którym można zainicjować przepływ logowania. 

2. Przejdź bezpośrednio do pozycji Pulse bezpieczny wirtualny adres URL logowania Traffic Manager, a następnie zainicjuj w tym miejscu przepływ logowania.

3. Możesz użyć panelu programu Microsoft Access. Po kliknięciu kafelka bezpieczne wirtualne Traffic Manager w panelu dostępu zostanie przekierowany do adresu URL "Puls Secure Virtual Traffic Manager". Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu pulsu Secure Virtual Traffic Manager można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).