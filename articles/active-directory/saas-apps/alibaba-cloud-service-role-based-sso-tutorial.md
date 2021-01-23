---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z usługą Alibaba w chmurze (Logowanie jednokrotne oparte na rolach) | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między usługami Azure Active Directory i Alibaba w chmurze (Logowanie jednokrotne oparte na rolach).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/08/2020
ms.author: jeedes
ms.openlocfilehash: 6e4cb3c372b696b203d2441c74a6cafff175af47
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736156"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alibaba-cloud-service-role-based-sso"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z usługą Alibaba w chmurze (Logowanie jednokrotne oparte na rolach)

W tym samouczku dowiesz się, jak zintegrować usługę Alibaba w chmurze (Logowanie jednokrotne oparte na rolach) z Azure Active Directory (Azure AD). W przypadku integrowania usługi Alibaba w chmurze (Logowanie jednokrotne oparte na rolach) z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do usługi w chmurze Alibaba (Logowanie jednokrotne oparte na rolach).
* Zezwól użytkownikom na automatyczne logowanie do usługi Alibaba w chmurze (Logowanie jednokrotne oparte na rolach) przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja usługi Alibaba w chmurze (Logowanie jednokrotne oparte na rolach).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa w chmurze Alibaba (Logowanie jednokrotne oparte na rolach) obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Dodawanie usługi w chmurze Alibaba (Logowanie jednokrotne oparte na rolach) z galerii

Aby skonfigurować integrację usługi Alibaba w chmurze (Logowanie jednokrotne oparte na rolach) w usłudze Azure AD, musisz dodać usługę chmurową Alibaba (Logowanie jednokrotne oparte na rolach) z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Alibaba Cloud Service (Logowanie jednokrotne oparte na rolach)** w polu wyszukiwania.
1. Wybierz pozycję **Alibaba Cloud Service (logowanie JEDNOkrotne oparte na rolach)** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.
5. Na stronie **Alibaba Cloud Service (logowanie JEDNOkrotne oparte na rolach)** kliknij pozycję **Właściwości** w okienku nawigacji po lewej stronie i skopiuj **Identyfikator obiektu** i Zapisz go na komputerze do późniejszego użycia.

    ![Konfiguracja właściwości](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)


## <a name="configure-and-test-azure-ad-sso-for-alibaba-cloud-service-role-based-sso"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla usługi Alibaba w chmurze (Logowanie jednokrotne oparte na rolach)

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą usługi Alibaba w chmurze (opartej na rolach SSO) przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Alibaba Cloud Service (Logowanie jednokrotne oparte na rolach).

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą usługi Alibaba w chmurze (opartej na rolach Logowanie jednokrotne), wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj Role-Based pojedynczy Sign-On w usłudze Alibaba w chmurze](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Skonfiguruj usługę w chmurze Alibaba (logowanie JEDNOkrotne oparte na rolach)](#configure-alibaba-cloud-service-role-based-sso-sso)** , aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Utwórz usługę Alibaba w chmurze (logowanie JEDNOkrotne oparte na rolach)](#create-alibaba-cloud-service-role-based-sso-test-user)** , aby uzyskać odpowiednik Britta Simon w usłudze Alibaba Cloud Service (Logowanie jednokrotne oparte na rolach), które jest połączone z reprezentacją usługi Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracji aplikacji w **chmurze Alibaba (opartej na rolach)** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.


    >[!NOTE]
    >1. W przypadku witryny Alibaba Cloud International należy pobrać metadane dostawcy usług z [tego](https://signin.alibabacloud.com/saml-role/sp-metadata.xml) linku.
    > 1. W przypadku lokacji usługi Alibaba w chmurze (CN) Pobierz metadane dostawcy usług z [tego](https://signin.aliyun.com/saml-role/sp-metadata.xml) linku.

    c. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatorów** i **adresów URL odpowiedzi** są automatycznie wypełniane w polu tekstowym sekcja Alibaba (Logowanie jednokrotne oparte na rolach):

    > [!Note]
    > Jeśli wartości **identyfikatorów** i **adresów URL odpowiedzi** nie zostaną wypełnione automatycznie, wprowadź wartości ręcznie zgodnie z wymaganiami.

1. Usługi w chmurze Alibaba (Logowanie jednokrotne oparte na rolach) wymagają skonfigurowania ról w usłudze Azure AD. To zdarzenie jest wstępnie skonfigurowane, więc nie trzeba go konfigurować, ale nadal musisz je utworzyć w usłudze Azure AD przy użyciu tego [artykułu](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie usługi Alibaba w chmurze (logowanie JEDNOkrotne oparte na rolach)** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon do korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do usługi Alibaba w chmurze (opartej na rolach SSO).

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Usługa w chmurze Alibaba (logowanie JEDNOkrotne oparte na rolach)**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. Na karcie **Użytkownicy i grupy** wybierz pozycję U2 z listy użytkownik, a następnie kliknij pozycję **Wybierz**. Następnie kliknij przycisk **Przypisz**.

    ![Przypisz test usługi Azure AD Użytkownik1](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

1. Wyświetl przypisane role i przetestuj usługę Alibaba w chmurze (Logowanie jednokrotne oparte na rolach).

    ![Przypisywanie testu usługi Azure AD](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >Po przypisaniu użytkownika (U2) utworzona rola zostanie automatycznie dołączona do użytkownika. Jeśli utworzono wiele ról, musisz dołączyć odpowiednie role do użytkownika zgodnie z potrzebami. Jeśli chcesz zaimplementować Logowanie jednokrotne oparte na rolach z usługi Azure AD do wielu kont w chmurze Alibaba, powtórz powyższe kroki.

## <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Konfigurowanie Role-Based pojedynczego Sign-On w usłudze Alibaba w chmurze

1. Zaloguj się do konsoli usługi Alibaba Cloud [RAM](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) przy użyciu Account1.

2. W okienku nawigacji po lewej stronie wybierz pozycję **Logowanie jednokrotne**.

3. Na karcie **Logowanie jednokrotne oparte na rolach** kliknij pozycję **Utwórz dostawcy tożsamości**.

4. Na wyświetlonej stronie Wprowadź tekst `AAD` w polu Nazwa dostawcy tożsamości, wprowadź opis w polu **notatka** , kliknij przycisk **Przekaż** , aby załadować pobrany wcześniej plik metadanych Federacji, a następnie kliknij przycisk **OK**.

5. Po pomyślnym utworzeniu dostawcy tożsamości kliknij pozycję **Utwórz rolę pamięci RAM**.

6. W polu **Nazwa roli pamięci RAM** wprowadź `AADrole` Wybierz `AAD` z listy rozwijanej **Wybierz dostawcy tożsamości** , a następnie kliknij przycisk OK.

    >[!NOTE]
    >W razie potrzeby można udzielić uprawnienia do roli. Po utworzeniu dostawcy tożsamości i odpowiedniej roli zalecamy zapisanie ARNs dostawcy tożsamości i roli do późniejszego użycia. ARNs można uzyskać na stronie informacje o dostawcy tożsamości i stronie informacje o rolach.

7. Skojarz rolę Alibaba w chmurze (AADrole) z użytkownikiem usługi Azure AD (U2): Aby skojarzyć rolę RAM z użytkownikiem usługi Azure AD, musisz utworzyć rolę w usłudze Azure AD, wykonując następujące czynności:

    a. Zaloguj się do [eksploratora Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer).

    b. Kliknij przycisk **Modyfikuj uprawnienia** , aby uzyskać wymagane uprawnienia do tworzenia roli.

    ![Config1 wykresu](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Wybierz z listy następujące uprawnienia i kliknij przycisk **Modyfikuj uprawnienia**, jak pokazano na poniższej ilustracji.

    ![Config2 wykresu](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >Po udzieleniu uprawnień Zaloguj się ponownie do Eksploratora grafów.

    d. Na stronie Eksplorator grafów wybierz pozycję **Pobierz** z pierwszej listy rozwijanej i **wersji beta** z drugiej listy rozwijanej. Następnie wprowadź `https://graph.microsoft.com/beta/servicePrincipals` wartość w polu obok list rozwijanych, a następnie kliknij pozycję **Uruchom zapytanie**.

    ![Config3 wykresu](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Jeśli używasz wielu katalogów, możesz wprowadzić wartość `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` w polu zapytania.

    e. W sekcji " **Podgląd odpowiedzi** " Wyodrębnij Właściwość appRoles z "jednostki usługi" do późniejszego użycia.

    ![Config4 wykresu](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >Właściwość appRoles można zlokalizować, wprowadzając `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` w polu zapytania. Należy zauważyć, że `objectID` jest to identyfikator obiektu skopiowanego ze strony **Właściwości** usługi Azure AD.

    f. Wróć do Eksploratora grafów, Zmień metodę z **Get** na **patch**, wklej poniższą zawartość do sekcji **treść żądania** , a następnie kliknij pozycję **Uruchom zapytanie**:
    ```
    { 
      "appRoles": [
        { 
          "allowedMemberTypes":[
            "User"
          ],
          "description": "msiam_access",
          "displayName": "msiam_access",
          "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
          "isEnabled": true,
          "origin": "Application",
          "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
      ]
    }
    ```
    > [!NOTE]
    > `value`Jest to ARNs dostawcy tożsamości i roli utworzonej w konsoli pamięci RAM. W tym miejscu możesz dodać wiele ról, jeśli jest to konieczne. Usługa Azure AD wyśle wartość tych ról jako wartość żądania w odpowiedzi SAML. Można jednak dodawać tylko nowe role po `msiam_access` części dla operacji patch. Aby wygładzić proces tworzenia, zalecamy używanie generatora identyfikatorów, takiego jak Generator identyfikatorów GUID, do generowania identyfikatorów w czasie rzeczywistym.

    przykład Po zastosowaniu poprawki do jednostki usługi przy użyciu wymaganej roli należy dołączyć rolę do użytkownika usługi Azure AD (U2), wykonując kroki **przypisywania użytkownika testowego usługi Azure AD** w samouczku.

### <a name="configure-alibaba-cloud-service-role-based-sso-sso"></a>Konfigurowanie usługi w chmurze Alibaba (Logowanie jednokrotne oparte na rolach)

Aby skonfigurować Logowanie jednokrotne w **usłudze Alibaba Cloud Service (logowanie JEDNOkrotne oparte na rolach)** , należy wysłać pobrany **kod XML metadanych Federacji** i odpowiednie skopiowane adresy URL z Azure Portal do [Alibaba usługi w chmurze (Logowanie jednokrotne oparte na rolach)](https://www.aliyun.com/service/). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Tworzenie usługi w chmurze Alibaba (Logowanie jednokrotne oparte na rolach)

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w usłudze Alibaba Cloud Service (Logowanie jednokrotne oparte na rolach). Pracuj z [zespołem pomocy technicznej Alibaba Cloud Service (logowanie JEDNOkrotne oparte na rolach)](https://www.aliyun.com/service/) , aby dodać użytkowników w usłudze Alibaba Cloud Service (Logowanie jednokrotne oparte na rolach). Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

Po ukończeniu powyższych konfiguracji Przetestuj usługę Alibaba w chmurze (Logowanie jednokrotne oparte na rolach), wykonując następujące czynności:

1. W Azure Portal przejdź do strony usługi w **chmurze Alibaba (logowanie JEDNOkrotne oparte na rolach)** , wybierz pozycję **Logowanie** jednokrotne, a następnie kliknij pozycję **Testuj**.

    ![Test config1](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Kliknij pozycję **Zaloguj się jako bieżący użytkownik**.

    ![Test Config2](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. Na stronie wybór konta wybierz przycisk U2.

    ![Test Config3](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. Zostanie wyświetlona następująca strona z informacją, że logowanie jednokrotne oparte na rolach zakończyło się pomyślnie.

    ![Test config4](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu usługi w chmurze Alibaba (Logowanie jednokrotne oparte na rolach) można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
