---
title: 'Samouczek: integracja Azure Active Directory z systemami Pega | Microsoft Docs'
description: W tym samouczku dowiesz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i systemami Pega.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 802bd61d499f64a128a4d1c0585363cb1962f8a1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650043"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Samouczek: integracja Azure Active Directory z systemami Pega

W tym samouczku dowiesz się, jak zintegrować systemy Pega z usługą Azure Active Directory (Azure AD). W przypadku integrowania systemów Pega z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do systemów Pega.
* Zezwól użytkownikom na automatyczne logowanie do systemów Pega przy użyciu ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) w systemie Pega.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne w usłudze Azure AD w środowisku testowym.

* Systemy Pega obsługują Logowanie jednokrotne zainicjowane przez usługę SP i dostawcy tożsamości.

## <a name="add-pega-systems-from-the-gallery"></a>Dodawanie systemów Pega z galerii

Aby skonfigurować integrację systemów Pega z usługą Azure AD, należy dodać systemy Pega z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Pega Systems** w polu wyszukiwania.
1. Wybierz pozycję **systemy Pega** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-pega-systems"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla systemów Pega

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD z systemami Pega przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w systemach Pega.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD z systemami Pega, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj rejestrację jednokrotną systemów Pega](#configure-pega-systems-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego systemów Pega](#create-pega-systems-test-user)** , aby dysponować odpowiednikiem B. Simon w systemach Pega, które są połączone z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracji aplikacji **Pega systemu** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie zainicjowanym przy użyciu dostawcy tożsamości, w oknie dialogowym **Podstawowa konfiguracja SAML** wykonaj następujące czynności.

    ![Podstawowa konfiguracja SAML — okno dialogowe](common/idp-intiated.png)

    1. W polu **Identyfikator** wprowadź adres URL w tym wzorcu:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. W polu **adres URL odpowiedzi** wprowadź adres URL w tym wzorcu:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Jeśli chcesz skonfigurować aplikację w trybie inicjowania programu SP, wybierz opcję **Ustaw dodatkowe adresy URL** i wykonaj następujące czynności.

    ![Informacje o rejestracji jednokrotnej w systemach Pega i adresach URL](common/both-advanced-urls.png)

    1. W polu **adres URL logowania** wprowadź wartość adres URL logowania.

    1. W polu **Stan przekazywania** wprowadź adres URL w tym wzorcu: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Podane tu wartości są symbolami zastępczymi. Musisz użyć rzeczywistego identyfikatora, adresu URL odpowiedzi, adresu URL logowania i adresu URL stanu przekazywania. Możesz uzyskać identyfikatory i adresy URL odpowiedzi z aplikacji Pega, jak wyjaśniono w dalszej części tego samouczka. Aby uzyskać wartość stanu przekazywania, skontaktuj się z [zespołem pomocy technicznej systemów Pega](https://www.pega.com/contact-us). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Aplikacja Pega Systems wymaga, aby potwierdzenia SAML były w określonym formacie. Aby uzyskać poprawny format, należy dodać niestandardowe mapowania atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia domyślne atrybuty. Wybierz ikonę **Edytuj** , aby otworzyć okno dialogowe **atrybuty użytkownika** :

    ![Atrybuty użytkownika](common/edit-attribute.png)

7. Oprócz atrybutów przedstawionych na poprzednim zrzucie ekranu aplikacja Pega Systems wymaga, aby w odpowiedzi SAML z powrotem przekazano kilka atrybutów. W sekcji **oświadczenia użytkownika** okna dialogowego **atrybuty użytkownika** wykonaj następujące kroki, aby dodać te atrybuty tokenu SAML:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Te wartości są specyficzne dla Twojej organizacji. Podaj odpowiednie wartości.

    1. Wybierz pozycję **Dodaj nowe oświadczenie** , aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkowników** :

    ![Wybierz pozycję Dodaj nowe zgłoszenie](common/new-save-attribute.png)

    ![Okno dialogowe Zarządzanie oświadczeniami użytkownika](common/new-attribute-details.png)

    1. W polu tekstowym **Nazwa** podaj nazwę atrybutu pokazanego dla tego wiersza.

    1. Pozostaw puste pole **przestrzeni nazw** .

    1. W polu **Źródło** wybierz pozycję **atrybut**.

    1. Na liście **atrybutów źródłowych** wybierz wartość atrybutu wyświetlaną dla tego wiersza.

    1. Wybierz przycisk **OK**.

    1. Wybierz pozycję **Zapisz**.

8. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz link **pobierania** obok pozycji **XML metadanych Federacji**, zgodnie z wymaganiami, a następnie Zapisz certyfikat na komputerze:

    ![Link do pobierania certyfikatu](common/metadataxml.png)

9. W sekcji **Konfigurowanie systemów Pega** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiuj adresy URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do systemów Pega.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **systemy Pega**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="configure-pega-systems-sso"></a>Konfigurowanie logowania jednokrotnego dla systemów Pega

1. Aby skonfigurować Logowanie jednokrotne na stronie **systemy Pega** , zaloguj się do portalu Pega przy użyciu konta administratora w innym oknie przeglądarki.

2. Wybierz pozycję **Utwórz**  >    >  **usługę uwierzytelniania** sysadmin:

    ![Wybieranie usługi uwierzytelniania](./media/pegasystems-tutorial/admin.png)
    
3. Wykonaj następujące kroki na ekranie **Tworzenie usługi uwierzytelniania** .

    ![Ekran tworzenia usługi uwierzytelniania](./media/pegasystems-tutorial/admin1.png)

    1. Na liście **Typ** wybierz pozycję **SAML 2,0**.

    1. W polu **Nazwa** wprowadź dowolną nazwę (na przykład **Logowanie jednokrotne w usłudze Azure AD**).

    1. W polu **Krótki opis** wprowadź opis.  

    1. Wybierz pozycję **Utwórz i Otwórz**.
    
4. W sekcji **Informacje o dostawcy tożsamości (dostawcy tożsamości)** wybierz pozycję **Importuj metadane dostawcy tożsamości** i przejdź do pliku metadanych pobranego z Azure Portal. Kliknij pozycję **Prześlij** , aby załadować metadane:

    ![Sekcja informacji o dostawcy tożsamości (dostawcy tożsamości)](./media/pegasystems-tutorial/admin2.png)
    
    Import wypełni dane dostawcy tożsamości, jak pokazano poniżej:

    ![Zaimportowane dane dostawcy tożsamości](./media/pegasystems-tutorial/idp.png)
    
6. Wykonaj następujące kroki w sekcji **Ustawienia dostawcy usług (SP)** .

    ![Ustawienia dostawcy usług](./media/pegasystems-tutorial/sp.png)

    1. Skopiuj wartość **identyfikacji jednostki** i wklej ją w polu **Identyfikator** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    1. Skopiuj wartość **lokalizacji usługi konsumenckej odbiorcy (ACS)** i wklej ją do pola **adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    1. Wybierz pozycję **Wyłącz podpisywanie żądania**.

7. Wybierz pozycję **Zapisz**.

### <a name="create-pega-systems-test-user"></a>Tworzenie użytkownika testowego systemów Pega

Następnie należy utworzyć użytkownika o nazwie Britta Simon w systemach Pega. Współpraca z [zespołem pomocy technicznej systemów Pega](https://www.pega.com/contact-us) umożliwia tworzenie użytkowników.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do systemu Pega adres URL logowania, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania do systemu Pega, a następnie zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do systemów Pega, dla których skonfigurowano Logowanie jednokrotne. 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka systemy Pega w obszarze Moje aplikacje, jeśli skonfigurowano w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do systemów Pega, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu systemów Pega można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).