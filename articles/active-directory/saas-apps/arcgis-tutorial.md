---
title: 'Samouczek: integracja Azure Active Directory z usługą ArcGIS Online | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługami Azure Active Directory i ArcGIS Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/08/2021
ms.author: jeedes
ms.openlocfilehash: 7a445eefa31e741562105e89fa105d404ccc0c7e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646354"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Samouczek: integracja Azure Active Directory z usługą ArcGIS Online

W tym samouczku dowiesz się, jak zintegrować usługę ArcGIS Online z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi ArcGIS w trybie online z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do usługi ArcGIS Online.
* Zezwól użytkownikom na automatyczne logowanie do ArcGIS Online przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym w usłudze ArcGIS Online.

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa ArcGIS Online obsługuje usługę **SP** zainicjowaną przez usługę SSO.

## <a name="add-arcgis-online-from-the-gallery"></a>Dodaj ArcGIS Online z galerii

Aby skonfigurować integrację usługi ArcGIS Online z usługą Azure AD, należy dodać usługę ArcGIS Online z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **ArcGIS Online** w polu wyszukiwania.
1. Wybierz pozycję **ArcGIS Online** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-arcgis-online"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla usługi ArcGIS Online

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą ArcGIS Online przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze ArcGIS Online.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD w usłudze ArcGIS Online, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne w usłudze ArcGIS Online](#configure-arcgis-online-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego usługi ArcGIS Online](#create-arcgis-online-test-user)** , aby uzyskać odpowiednik B. Simon w usłudze ArcGIS Online, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **ArcGIS Online** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.maps.arcgis.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej klienta usługi ArcGIS Online](https://support.esri.com/en/). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. Aby zautomatyzować konfigurację w usłudze **ArcGIS Online**, musisz zainstalować **rozszerzenie przeglądarki do bezpiecznego logowania Moje aplikacje**, klikając pozycję **Zainstaluj rozszerzenie**.

    ![image (obraz)](./media/arcgis-tutorial/install-extension.png)

7. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj aplikację ArcGIS Online**. Spowoduje to bezpośrednie przejście do aplikacji ArcGIS Online. W tym miejscu podaj poświadczenia administratora w celu zalogowania się do aplikacji ArcGIS Online. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki w sekcji **Konfigurowanie logowania jednokrotnego usługi ArcGIS Online**.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi ArcGIS w trybie online.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **ArcGIS Online**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-arcgis-online-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze ArcGIS Online

1. Jeśli chcesz ręcznie skonfigurować usługę ArcGIS Online, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmowej usługi ArcGIS Online jako administrator, a następnie wykonaj następujące czynności:

2. Kliknij pozycję **EDYTUJ USTAWIENIA**.

    ![Edytuj ustawienia](./media/arcgis-tutorial/settings.png "Edytuj ustawienia")

3. Kliknij pozycję **Security** (Zabezpieczenia).

    ![Bezpieczeństwo](./media/arcgis-tutorial/secure.png "Zabezpieczenia")

4. W obszarze **Identyfikatory logowania warstwy Enterprise** kliknij przycisk **SET IDENTITY PROVIDER** (USTAW DOSTAWCĘ TOŻSAMOŚCI).

    ![Identyfikatory logowania przedsiębiorstwa](./media/arcgis-tutorial/enterprise.png "Identyfikatory logowania przedsiębiorstwa")

5. Na stronie konfiguracji **Ustawienie dostawcy tożsamości** wykonaj następujące działania:

    ![Ustaw dostawcę tożsamości](./media/arcgis-tutorial/identity-provider.png "Ustaw dostawcę tożsamości")

    a. W polu tekstowym **Nazwa** wpisz nazwę swojej organizacji.

    b. W pozycji **Metadata for the Enterprise Identity Provider will be supplied using** (Metadane dla dostawcy tożsamości przedsiębiorstwa zostaną dostarczone przy użyciu) wybierz opcję **A File** (Plik).

    c. Aby przekazać pobrany plik metadanych, kliknij pozycję **Wybierz plik**.

    d. Kliknij opcję **SET IDENTITY PROVIDER** (USTAW DOSTAWCĘ TOŻSAMOŚCI).

### <a name="create-arcgis-online-test-user"></a>Tworzenie użytkownika testowego usługi ArcGIS Online

Aby umożliwić użytkownikom usługi Azure AD logowanie w usłudze ArcGIS Online, należy ich aprowizować w usłudze ArcGIS Online.  
W przypadku usługi ArcGIS Online aprowizacja jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do dzierżawcy **ArcGIS**.

2. Kliknij przycisk **INVITE MEMBERS** (ZAPROŚ CZŁONKÓW).

    ![Zapraszanie członków](./media/arcgis-tutorial/invite.png "Zapraszanie członków")

3. Wybierz opcję **Add members automatically without sending an email** (Dodaj członków automatycznie bez wysyłania wiadomości e-mail), a następnie kliknij przycisk **NEXT** (DALEJ).

    ![Dodaj członków automatycznie](./media/arcgis-tutorial/members.png "Dodaj członków automatycznie")

4. Na stronie okna dialogowego **Members** (Członkowie) wykonaj następujące czynności:

    ![Dodaj i przejrzyj](./media/arcgis-tutorial/review.png "Dodaj i przejrzyj")

     a. Wprowadź **adres e-mail**, **imię** i nazwisko prawidłowego konta usługi Azure AD **, dla którego** chcesz zainicjować obsługę administracyjną.

     b. Kliknij przycisk **ADD AND REVIEW** (DODAJ I PRZEJRZYJ).
5. Przejrzyj wprowadzone dane, a następnie kliknij przycisk **ADD MEMBERS** (DODAJ CZŁONKÓW).

    ![Dodaj członka](./media/arcgis-tutorial/add.png "Dodaj członka")

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem, którego użyje w celu potwierdzenia konta, zanim stanie się ono aktywne.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do usługi ArcGIS Online, na którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania do usługi ArcGIS Online i zainicjuj tam przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka ArcGIS Online w obszarze Moje aplikacje zostanie on przekierowany do adresu URL logowania do usługi ArcGIS Online. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu usługi ArcGIS Online można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).