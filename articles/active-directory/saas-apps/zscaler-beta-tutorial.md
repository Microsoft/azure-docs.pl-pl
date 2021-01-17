---
title: 'Samouczek: integracja Azure Active Directory z programem rozwiązania Zscaler beta | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą Zscaler Beta.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 6914fb50cdb157cf8ef7b5433ebbde47eff8fc32
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539809"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Samouczek: integracja Azure Active Directory z programem rozwiązania Zscaler beta

Z tego samouczka dowiesz się, jak zintegrować usługę Zscaler Beta z usługą Azure Active Directory (Azure AD).
W przypadku integracji rozwiązania Zscaler beta z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do rozwiązania Zscaler beta.
* Zezwól użytkownikom na automatyczne logowanie do rozwiązania Zscaler beta przy użyciu kont usługi Azure AD. Ta kontrola dostępu jest nazywana logowaniem jednokrotnym (SSO).
* Zarządzaj kontami w jednej centralnej lokalizacji przy użyciu Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z usługą Zscaler Beta są potrzebne następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja rozwiązania Zscaler beta, która korzysta z rejestracji jednokrotnej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program rozwiązania Zscaler beta obsługuje usługę **SP** zainicjowaną przez usługę SSO.
* Rozwiązania Zscaler beta obsługuje Inicjowanie obsługi użytkowników **just in Time** .

## <a name="adding-zscaler-beta-from-the-gallery"></a>Dodawanie usługi Zscaler Beta z galerii

Aby skonfigurować integrację usługi Zscaler Beta z usługą Azure AD, musisz dodać usługę Zscaler Beta z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **rozwiązania Zscaler beta** w polu wyszukiwania.
1. Wybierz pozycję **rozwiązania Zscaler beta** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-beta"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla programu rozwiązania Zscaler beta

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą rozwiązania Zscaler beta przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie rozwiązania Zscaler beta.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu rozwiązania Zscaler beta, wykonaj następujące czynności:


1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj rejestrację jednokrotną rozwiązania Zscaler beta](#configure-zscaler-beta-sso)** — aby skonfigurować ustawienia pojedynczej Sign-On po stronie aplikacji.
    1. **[Utwórz użytkownika testowego rozwiązania Zscaler beta](#create-zscaler-beta-test-user)** , aby dysponować odpowiednikiem B. Simon w rozwiązania Zscaler beta, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **rozwiązania Zscaler beta** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu **adres URL logowania** wprowadź adres URL używany przez użytkowników do logowania się do aplikacji rozwiązania Zscaler beta beta.

    > [!NOTE]
    > Wartość nie jest rzeczywista. Zaktualizuj wartość za pomocą rzeczywistej wartości adresu URL logowania. Aby uzyskać wartość, skontaktuj się z [zespołem pomocy technicznej rozwiązania Zscaler beta Client](https://www.zscaler.com/company/contact).

5. Aplikacja rozwiązania Zscaler beta oczekuje potwierdzeń SAML w określonym formacie. Należy dodać niestandardowe mapowania atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Wybierz pozycję **Edytuj** , aby otworzyć okno dialogowe **atrybuty użytkownika** .

    ![Okno dialogowe Atrybuty użytkownika](common/edit-attribute.png)

6. Aplikacja rozwiązania Zscaler beta oczekuje, że kilka atrybutów jest przekazanie z powrotem w odpowiedzi SAML. W sekcji **oświadczenia użytkownika** w oknie dialogowym **atrybuty użytkownika** wykonaj następujące kroki, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli.
    
    | Nazwa | Atrybut źródłowy | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. Wybierz pozycję **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    b. W polu tekstowym **Nazwa** podaj nazwę atrybutu pokazanego dla tego wiersza.

    c. Pozostaw puste pole **obszar nazw** .

    d. W obszarze **Źródło** wybierz pozycję **atrybut**.

    e. Na liście **Atrybut źródłowy** podaj wartość atrybutu pokazanego dla tego wiersza.

    f. Wybierz przycisk **OK**.

    przykład Wybierz pozycję **Zapisz**.

    > [!NOTE]
    > Kliknij [tutaj](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui) , aby dowiedzieć się, jak skonfigurować rolę w usłudze Azure AD.

7. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz pozycję **Pobierz** , aby pobrać **certyfikat (base64)**. Zapisz go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

8. W sekcji **Konfigurowanie programu rozwiązania Zscaler beta** Skopiuj adresy URL, które są potrzebne do spełnienia wymagań:

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do rozwiązania Zscaler beta.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **rozwiązania Zscaler beta**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli skonfigurowano role zgodnie z opisem w powyższej tabeli, można wybrać ją z listy rozwijanej **Wybierz rolę** .
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-zscaler-beta-sso"></a>Konfigurowanie rejestracji jednokrotnej w programie rozwiązania Zscaler beta

1. Aby zautomatyzować konfigurację w programie rozwiązania Zscaler beta, zainstaluj **Moje aplikacje bezpieczne logowanie do przeglądarki** , wybierając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki wybierz pozycję Skonfiguruj program **rozwiązania Zscaler beta** kieruje do aplikacji rozwiązania Zscaler w wersji beta. W tym miejscu podaj poświadczenia administratora, aby zalogować się do programu rozwiązania Zscaler beta. Rozszerzenie przeglądarki automatycznie konfiguruje aplikację dla Ciebie i automatyzuje kroki od 3 do 6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Aby ręcznie skonfigurować rozwiązania Zscaler beta, Otwórz nowe okno przeglądarki sieci Web. Zaloguj się do witryny firmowej rozwiązania Zscaler beta jako administrator i wykonaj następujące kroki.

4. Przejdź do pozycji **Administracja**  >    >  **Ustawienia uwierzytelniania** uwierzytelniania i wykonaj następujące kroki.
   
    ![Administracja](./media/zscaler-beta-tutorial/ic800206.png "Administracja")

    a. W obszarze **Typ uwierzytelniania** wybierz pozycję **SAML**.

    b. Wybierz pozycję **Konfiguruj SAML**.

5. W oknie **Edycja języka SAML** wykonaj następujące kroki: 
            
    ![Zarządzanie użytkownikami & uwierzytelnianie](./media/zscaler-beta-tutorial/ic800208.png "Zarządzanie użytkownikami & uwierzytelnianie")
    
    a. W polu **adres URL portalu SAML** wklej **adres URL logowania** skopiowany z Azure Portal.

    b. W polu **atrybut nazwy logowania** wprowadź **NameID**.

    c. W polu **publiczny certyfikat SSL** wybierz pozycję **Przekaż** , aby przekazać certyfikat podpisywania SAML platformy Azure pobrany z Azure Portal.

    d. Przełącz **opcję Włącz funkcję autoaprowizacji SAML**.

    e. W polu **atrybut Nazwa wyświetlana użytkownika** wpisz **DisplayName** , jeśli chcesz włączyć autoudostępnianie SAML dla atrybutów DisplayName.

    f. W polu **atrybut nazwy grupy** wprowadź wartość **memberOf** , jeśli chcesz włączyć funkcję autozastrzegania SAML dla atrybutów memberOf.

    przykład W polu **atrybut nazwy działu** wprowadź **dział** , jeśli chcesz włączyć funkcję autoaprowizacji SAML dla atrybutów działu.

    h. Wybierz pozycję **Zapisz**.

6. Na stronie **Konfigurowanie uwierzytelniania użytkownika** wykonaj następujące czynności:

    ![Menu aktywacja i przycisk Aktywuj](./media/zscaler-beta-tutorial/ic800207.png)

    a. Umieść kursor nad menu **Aktywacja** w lewym dolnym rogu.

    b. Wybierz pozycję **Aktywuj**.

## <a name="configure-proxy-settings"></a>Konfigurowanie ustawień serwera proxy
Aby skonfigurować ustawienia serwera proxy w programie Internet Explorer, wykonaj następujące kroki.

1. Uruchom program **Internet Explorer**.

2. Wybierz **Opcje internetowe** z menu **Narzędzia** , aby otworzyć okno dialogowe **Opcje internetowe** . 
    
     ![Opcje internetowe — okno dialogowe](./media/zscaler-beta-tutorial/ic769492.png "Opcje internetowe")

3. Wybierz kartę **połączenia** . 
  
     ![Karta Połączenia](./media/zscaler-beta-tutorial/ic769493.png "Połączenia")

4. Wybierz pozycję **Ustawienia sieci LAN** , aby otworzyć okno dialogowe **Ustawienia sieci lokalnej (LAN)** .

5. W sekcji **serwer proxy** wykonaj następujące kroki: 
   
    ![Sekcja serwera proxy](./media/zscaler-beta-tutorial/ic769494.png "Serwer proxy")

    a. Zaznacz pole wyboru **Użyj serwera proxy dla sieci LAN** .

    b. W polu **adres** wprowadź **bramę. Rozwiązania Zscaler Beta.net**.

    c. W polu **port** wprowadź **80**.

    d. Zaznacz pole wyboru **Obejdź serwer proxy dla adresów lokalnych** .

    e. Wybierz **przycisk OK** , aby zamknąć okno dialogowe **Ustawienia sieci lokalnej (LAN)** .

6. Wybierz **przycisk OK** , aby zamknąć okno dialogowe **Opcje internetowe** .

### <a name="create-zscaler-beta-test-user"></a>Tworzenie użytkownika testowego usługi Zscaler Beta

W tej sekcji użytkownik Britta Simon jest tworzony w rozwiązania Zscaler beta. Usługa Zscaler Beta obsługuje **aprowizację użytkowników typu just in time**, która jest domyślnie włączona. Nie ma niczego do wykonania w tej sekcji. Jeśli użytkownik jeszcze nie istnieje w usłudze Zscaler Beta, to zostanie utworzony po uwierzytelnieniu.

>[!Note]
>Aby ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej rozwiązania Zscaler beta](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do rozwiązania Zscaler beta, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania do rozwiązania Zscaler beta i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka rozwiązania Zscaler beta w obszarze Moje aplikacje zostanie on przekierowany do adresu URL logowania rozwiązania Zscaler w wersji beta. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu programu rozwiązania Zscaler beta można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).