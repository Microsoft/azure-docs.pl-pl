---
title: 'Samouczek: integracja Azure Active Directory z chmurą SAP Analytics Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i chmurą SAP Analytics.
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
ms.openlocfilehash: 19bdb6d2a586dcb279687673c7fa4e302dc4928b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652644"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Samouczek: integracja chmury SAP Analytics z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować chmurę z usługą SAP Analytics z usługą Azure Active Directory (Azure AD). Gdy integrujesz chmurę SAP Analytics z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do chmury SAP Analytics.
* Zezwól użytkownikom na automatyczne logowanie do chmury SAP Analytics przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne w chmurze usługi SAP Analytics.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Chmura SAP Analytics obsługuje funkcję SSO zainicjowaną przez usługę **SP** .

## <a name="add-sap-analytics-cloud-from-the-gallery"></a>Dodaj chmurę SAP Analytics z galerii

Aby skonfigurować integrację chmury SAP Analytics z usługą Azure AD, musisz dodać chmurę analizy SAP z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania pozycję **chmura analizy SAP** .
1. Wybierz pozycję **chmura analizy SAP** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-sap-analytics-cloud"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD dla chmury SAP Analytics

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą chmury SAP Analytics przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w chmurze analizy SAP.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą chmury SAP Analytics, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj usługę SAP Analytics — logowanie JEDNOkrotne w chmurze](#configure-sap-analytics-cloud-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Tworzenie użytkownika testowego chmury SAP Analytics](#create-sap-analytics-cloud-test-user)** — aby dysponować odpowiednikiem B. Simon w chmurze analizy SAP, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji w **chmurze SAP Analytics** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL przy użyciu jednego z następujących wzorców:

    - `https://<sub-domain>.sapanalytics.cloud/`
    - `https://<sub-domain>.sapbusinessobjects.cloud/`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL przy użyciu jednego z następujących wzorców:

    - `<sub-domain>.sapbusinessobjects.cloud`
    - `<sub-domain>.sapanalytics.cloud`

    > [!NOTE] 
    > Wartości tych adresów URL są tylko na potrzeby demonstracyjne. Zaktualizuj wartości rzeczywistym adresem URL logowania i adresem URL identyfikatora. Aby uzyskać adres URL logowania, skontaktuj się z [zespołem pomocy technicznej usługi SAP Analytics Cloud Client](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Możesz uzyskać adres URL identyfikatora, pobierając metadane chmury SAP Analytics z konsoli administracyjnej. Jest to wyjaśnione w dalszej części tego samouczka.

4. Na stronie **Konfiguruj pojedyncze Sign-On za pomocą elementu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie chmury SAP Analytics** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do chmury analizy SAP.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **chmura Analytics Cloud**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-sap-analytics-cloud-sso"></a>Konfigurowanie logowania jednokrotnego w chmurze SAP Analytics

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny usługi SAP Analytics w chmurze jako administrator.

2. Wybierz pozycję **menu**  >    >  **Administracja** systemu.
    
    ![Wybierz pozycję Menu (Menu), wybierz pozycję System (System), a następnie wybierz pozycję Administration (Administracja)](./media/sapboc-tutorial/configure-1.png)

3. Na karcie **Security** (Zabezpieczenia) wybierz ikonę **edycji** (ikona ołówka).
    
    ![Na karcie Security (Zabezpieczenia) wybierz ikonę edycji (ikona ołówka)](./media/sapboc-tutorial/configure-2.png)  

4. W obszarze **Authentication Method** (Metoda uwierzytelniania) wybierz pozycję **SAML Single Sign-On (SSO)** (Logowanie jednokrotne protokołu SAML).

    ![Wybierz pozycję SAML Single Sign-On (SSO) (Logowanie jednokrotne protokołu SAML) jako metodę uwierzytelniania](./media/sapboc-tutorial/configure-3.png)  

5. Aby pobrać metadane dostawcy usług (Krok 1.), wybierz pozycję **Download** (Pobierz). W pliku metadanych znajdź i skopiuj wartość elementu **entityID**. W witrynie Azure Portal w oknie dialogowym **Podstawowa konfiguracja protokołu SAML** wklej wartość w polu **Identyfikator**.

    ![Skopiuj i wklej wartość elementu entityID](./media/sapboc-tutorial/configure-4.png)  

6. Aby przekazać metadane dostawcy usług (Krok 2.) do pliku, który został pobrany z witryny Azure Portal, w obszarze **Upload Identity Provider metadata** (Przekazywanie metadanych dostawcy tożsamości) wybierz pozycję **Upload** (Przekaż).  

    ![W obszarze Upload Identity Provider metadata (Przekazywanie metadanych dostawcy tożsamości) wybierz pozycję Upload (Przekaż)](./media/sapboc-tutorial/configure-5.png)

7. Z listy **User Attribute** (Atrybut użytkownika) wybierz atrybut użytkownika (Krok 3.), którego chcesz użyć na potrzeby implementacji. Ten atrybut użytkownika jest mapowany na dostawcę tożsamości. Aby wprowadzić atrybut niestandardowy na stronie użytkownika, użyj opcji **Custom SAML Mapping** (Niestandardowe mapowanie protokołu SAML). Możesz również wybrać wartość **Email** (Adres e-mail) lub **USER ID** (Identyfikator użytkownika) jako atrybut użytkownika. W tym przykładzie wybraliśmy wartość **Email** (Adres e-mail), ponieważ oświadczenie identyfikatora użytkownika zamapowano z atrybutem **userprincipalname** w sekcji **Atrybuty i oświadczenia użytkownika** w witrynie Azure Portal. Zapewnia to unikatowy adres e-mail użytkownika, który jest wysyłany do aplikacji w chmurze SAP Analytics przy każdej pomyślnej odpowiedzi SAML.

    ![Wybierz atrybut użytkownika](./media/sapboc-tutorial/configure-6.png)

8. Aby zweryfikować konto za pomocą dostawcy tożsamości (Krok 4.) w polu **Login Credential (Email)** (Poświadczenia danych logowania — adres e-mail) wprowadź adres e-mail użytkownika. Następnie wybierz pozycję **Verify Account** (Sprawdź konto). System dodaje poświadczenia logowania do konta użytkownika.

    ![Wprowadź adres e-mail, a następnie wybierz pozycję Verify Account (Sprawdź konto)](./media/sapboc-tutorial/configure-7.png)

9. Wybierz ikonę **zapisywania**.

    ![Ikona zapisywania](./media/sapboc-tutorial/save.png)

### <a name="create-sap-analytics-cloud-test-user"></a>Tworzenie użytkownika testowego chmury SAP Analytics

Aby można było zalogować się do chmury SAP Analytics, użytkownicy usługi Azure AD muszą być obsługiwani w chmurze analizy SAP. W chmurze analizy SAP Inicjowanie obsługi jest zadaniem ręcznym.

Aby aprowizować konto użytkownika:

1. Zaloguj się do firmowej witryny usługi SAP Analytics w chmurze jako administrator.

2. Wybierz **menu**  >    >  **Użytkownicy** zabezpieczeń.

    ![Dodawanie pracownika](./media/sapboc-tutorial/user-1.png)

3. Na stronie **Users** (Użytkownicy) wybierz pozycję **+**, aby dodać szczegółowe informacje dotyczące nowego użytkownika. 

    ![Strona dodawania użytkowników](./media/sapboc-tutorial/user-4.png)

    Następnie wykonaj następujące kroki:

    1. W polu **Identyfikator użytkownika** wprowadź identyfikator użytkownika, na przykład **B**.

    1. W polu **imię** Wprowadź imię i nazwisko użytkownika, na przykład **B**.

    1. W polu **LAST NAME** (Nazwisko) wprowadź nazwisko użytkownika, np. **Simon**.

    1. W polu **Nazwa wyświetlana** wprowadź pełną nazwę użytkownika, na przykład **B. Simon**.

    1. W polu **E-MAIL** (Adres e-mail) wprowadź adres e-mail użytkownika, np. `b.simon@contoso.com`.

    1. Na stronie **Select Roles** (Wybieranie ról) wybierz odpowiednią rolę użytkownika, a następnie wybierz pozycję **OK**.

        ![Wybierz rolę](./media/sapboc-tutorial/user-3.png)

    1. Wybierz ikonę **zapisywania**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania w chmurze usługi SAP Analytics, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania do usługi SAP Analytics w chmurze i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka usługi SAP Analytics w obszarze Moje aplikacje zostanie on przekierowany do adresu URL logowania w chmurze SAP Analytics. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu chmury SAP Analytics można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).