---
title: 'Samouczek: integracja Azure Active Directory z usługą zegarki | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją AirWatch.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: e4f9d05a4b76e440f986a9003c20a48b22c516a8
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259892"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Samouczek: Integrowanie zegarków z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować czujkę z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do czujki.
* Zezwól użytkownikom na automatyczne logowanie do usługi Azure AD przy użyciu swoich kont w usłudze Active Directory.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:
 
* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. 

* Obsługa logowania jednokrotnego przez usługę **SP**

## <a name="add-airwatch-from-the-gallery"></a>Dodaj czujkę z galerii

Aby skonfigurować integrację aplikacji AirWatch z usługą Azure AD, musisz dodać aplikację AirWatch z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Watch** w polu wyszukiwania.
1. Wybierz pozycję **Obejrzyj** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-airwatch"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla usługi

Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w obserwie.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Watch, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Konfigurowanie logowania jednokrotnego](#configure-airwatch-sso)** w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego](#create-airwatch-test-user)** , aby uzyskać odpowiednika B. Simon w obserwie, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji w **zegarku** , Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    1. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz wartość: `AirWatch`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Aby uzyskać tę wartość, skontaktuj się z [zespołem obsługi klienta firmy AirWatch](https://www.air-watch.com/company/contact-us/). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja AirWatch oczekuje asercji SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image (obraz)](common/edit-attribute.png)

1. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** edytuj oświadczenia, korzystając z **ikony edycji**, lub dodaj je za pomocą opcji **Dodaj nowe oświadczenie**, aby skonfigurować atrybut tokenu języka SAML, jak pokazano na ilustracji powyżej, a następnie wykonaj następujące czynności:

    | Nazwa |  Atrybut źródłowy|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK** .

    przykład Kliknij pozycję **Zapisz**.

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać plik XML metadanych i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie czujki** , skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji zostanie włączone Logowanie jednokrotne w usłudze B. Simon, przyznając dostęp do usługi zegarki.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **AirWatch**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="configure-airwatch-sso"></a>Konfigurowanie logowania jednokrotnego

1. W innym oknie przeglądarki sieci Web Zaloguj się do swojej witryny firmowej jako administrator.

1. Na stronie Ustawienia. Wybierz pozycję **ustawienia > Integracja dla przedsiębiorstw > usług katalogowych**.

   ![Ustawienia](./media/airwatch-tutorial/services.png "Ustawienia")

1. Kliknij kartę **User** (Użytkownik), w polu tekstowym **Base DN** (Bazowa nazwa domeny) wpisz nazwę domeny, a następnie kliknij pozycję **Save** (Zapisz).

   ![Zrzut ekranu, który podświetla pole tekstowe podstawowej nazwy wyróżniającej.](./media/airwatch-tutorial/user.png "Użytkownik")

1. Kliknij kartę **Server** (Serwer).

   ![Server](./media/airwatch-tutorial/directory.png "Serwer") (Serwer)

1. Wykonaj następujące kroki w sekcji **LDAP** :

    ![Zrzut ekranu pokazujący zmiany, które należy wprowadzić w sekcji LDAP.](./media/airwatch-tutorial/ldap.png "LDAP")   

    a. W polu **Directory Type** (Typ katalogu) wybierz pozycję **None** (Brak).

    b. Wybierz pozycję **Use SAML For Authentication** (Użyj protokołu SAML na potrzeby uwierzytelniania).

1. W sekcji **SAML 2,0** , aby przekazać pobrany certyfikat, kliknij przycisk **Przekaż**.

    ![Przekaż](./media/airwatch-tutorial/uploads.png "Przekazywanie")

1. W sekcji **Request** (Żądanie) wykonaj następujące czynności:

    ![Sekcja żądania](./media/airwatch-tutorial/request.png "Żądanie")  

    a. W polu **Request Binding Type** (Typ powiązania żądania) wybierz opcję **POST**.

    b. W Azure Portal na stronie Konfigurowanie logowania **jednokrotnego w usłudze w zegarku** , skopiuj wartość **adres URL logowania** , a następnie wklej ją do pola tekstowego **adres URL** logowania jednokrotnego dostawcy tożsamości.

    c. W polu **NameID Format** (Format identyfikatora nazwy) wybierz opcję **Email Address** (Adres e-mail).

    d. Jako **zabezpieczenia żądania uwierzytelniania**, wybierz opcję **Brak**.

    e. Kliknij pozycję **Zapisz**.

1. Ponownie kliknij kartę **User** (Użytkownik).

    ![Użytkownik](./media/airwatch-tutorial/users.png "Użytkownik")

1. W sekcji **Attribute** (Atrybut) wykonaj następujące czynności:

    ![Atrybut](./media/airwatch-tutorial/attributes.png "Atrybut")

    a. W polu tekstowym **Object Identifier** (Identyfikator obiektu) wpisz `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. W polu tekstowym **Username** (Nazwa użytkownika) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. W polu tekstowym **Display Name** (Nazwa wyświetlana) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. W polu tekstowym **First Name** (Imię) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. W polu tekstowym **Last Name** (Nazwisko) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. W polu tekstowym **Email** (Adres e-mail) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    przykład Kliknij pozycję **Zapisz**.

### <a name="create-airwatch-test-user"></a>Tworzenie użytkownika testowego aplikacji AirWatch

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze, należy zalogować się w usłudze w celu przewidzenia. W przypadku aplikacji AirWatch aprowizowanie jest zadaniem ręcznym.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:**

1. Zaloguj się **do swojej witryny** sieci firmowej jako administrator.

2. W okienku nawigacji po lewej stronie kliknij pozycję **Accounts** (Konta), a następnie kliknij pozycję **Users** (Użytkownicy).
  
   ![Użytkownicy](./media/airwatch-tutorial/accounts.png "Użytkownicy")

3. W menu **Użytkownicy** kliknij przycisk **Widok listy**, a następnie kliknij przycisk **Dodaj > dodać użytkownika**.
  
   ![Zrzut ekranu, który podświetla przyciski Dodaj i Dodaj użytkownika.](./media/airwatch-tutorial/add.png "Dodaj użytkownika")

4. W oknie dialogowym **Add / Edit User** (Dodawanie/edytowanie użytkownika) wykonaj następujące czynności:

   ![Dodaj użytkownika](./media/airwatch-tutorial/save.png "Dodaj użytkownika")

   a. W polach tekstowych **Username** (Nazwa użytkownika), **Password** (Hasło), **Confirm Password** (Potwierdź hasło), **First Name** (Imię), **Last Name** (Nazwisko), **Email Address** (Adres e-mail) wpisz wartości prawidłowego konta usługi Azure Active Directory, które chcesz aprowizować.

   b. Kliknij pozycję **Zapisz**.

> [!NOTE]
> Możesz użyć jakichkolwiek innych narzędzi do tworzenia kont użytkowników i interfejsów API udostępnianych przez usługę Obejrzyj usługę Azure AD.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do obserwacji, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania do usługi Live Watch i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka do oglądania w obszarze Moje aplikacje zostanie przekierowany na adres URL logowania do obserwacji. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu aplikacji do monitorowania można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
