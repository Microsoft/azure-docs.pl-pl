---
title: 'Samouczek: integracja Azure Active Directory z usługą Andromeda | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Andromeda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 678bb46efb19f3451566306ddbbe372c631c717e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736041"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Samouczek: integracja Azure Active Directory z usługą Andromeda

W tym samouczku dowiesz się, jak zintegrować usługę Andromeda z usługą Azure Active Directory (Azure AD).
Integracja Andromeda z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi Andromeda.
* Możesz pozwolić użytkownikom na automatyczne logowanie do Andromeda (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Andromeda, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączonym logowaniem jednokrotnym w Andromeda

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Andromeda obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne
* Andromeda obsługuje Inicjowanie obsługi użytkowników **just in Time**

## <a name="adding-andromeda-from-the-gallery"></a>Dodawanie Andromeda z galerii

Aby skonfigurować integrację programu Andromeda z usługą Azure AD, musisz dodać Andromeda z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Andromeda** w polu wyszukiwania.
1. Wybierz pozycję **Andromeda** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-andromeda"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Andromeda

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Andromeda przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Andromeda.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Andromeda, wykonaj następujące czynności:


1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj Logowanie jednokrotne](#configure-andromeda-sso)** w usłudze Andromeda, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Utwórz użytkownika testowego Andromeda](#create-andromeda-test-user)** , aby uzyskać odpowiednik Britta Simon w Andromeda, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Andromeda** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<tenantURL>.ngcxpress.com/`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    ![Zrzut ekranu przedstawia ustawienie dodatkowego U R LS, gdzie można wprowadzić znak U R L.](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Wartość zostanie zaktualizowana o rzeczywisty identyfikator, adres URL odpowiedzi i adres URL Sign-On, który zostanie przedstawiony w dalszej części tego samouczka.

6. Aplikacja Andromeda oczekuje potwierdzeń SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![Zrzut ekranu przedstawia atrybuty użytkownika, takie jak użytkownik. imięname i EmailAddress User. mail.](common/edit-attribute.png)

    > [!Important]
    > Wyczyść definicje przestrzeni nazw podczas konfigurowania tych ustawień.

7. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** edytuj oświadczenia, korzystając z **ikony edycji**, lub dodaj je za pomocą opcji **Dodaj nowe oświadczenie**, aby skonfigurować atrybut tokenu języka SAML, jak pokazano na ilustracji powyżej, a następnie wykonaj następujące czynności: 

    | Nazwa | Atrybut źródłowy|
    | ------ | -----------|
    | role (rola)        | Rola specyficzna dla aplikacji |
    | typ        | Typ aplikacji |
    | company       | CompanyName |

    > [!NOTE]
    > Andromeda oczekuje ról dla użytkowników przypisanych do aplikacji. Skonfiguruj te role w usłudze Azure AD, aby użytkownicy mogli przypisywać odpowiednie role. Aby dowiedzieć się, jak skonfigurować role w usłudze Azure AD, zobacz [tutaj](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![Zrzut ekranu przedstawia oświadczenia użytkowników z opcjami dodawania nowego oświadczenia i zapisywania.](common/new-save-attribute.png)

    ![Zrzut ekranu przedstawia Zarządzanie oświadczeniami użytkowników, w których można wprowadzać wartości opisane w tym kroku.](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK** .

    przykład Kliknij pozycję **Zapisz**.

8. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

9. W sekcji **Konfigurowanie Andromeda** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Andromeda.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Andromeda**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli skonfigurowano role zgodnie z opisem w powyższej tabeli, można wybrać ją z listy rozwijanej **Wybierz rolę** .
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="configure-andromeda-sso"></a>Konfigurowanie logowania jednokrotnego Andromeda

1. Zaloguj się do witryny firmowej Andromeda jako administrator.

2. W górnej części paska menu kliknij pozycję **administrator** i przejdź do obszaru **Administracja**.

    ![Administrator Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

3. Po lewej stronie paska narzędzi w obszarze **interfejsy** kliknij pozycję Konfiguracja protokołu **SAML**.

    ![Andromeda SAML](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

4. Na stronie sekcja **konfiguracji SAML** wykonaj następujące czynności:

    ![Konfiguracja Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Zaznacz pole wyboru **Włącz logowanie jednokrotne przy użyciu protokołu SAML**.

    b. W sekcji **informacje Andromeda** skopiuj wartość **tożsamość Sp** i wklej ją do pola tekstowego **Identyfikator** w sekcji **Podstawowa konfiguracja SAML** .

    c. Skopiuj wartość **adres URL klienta** i wklej ją do pola tekstowego **adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** .

    d. Skopiuj wartość **adres URL logowania** i wklej ją do pola tekstowego **adres URL** logowania w sekcji **Podstawowa konfiguracja SAML** .

    e. W sekcji **dostawca tożsamości SAML** wpisz swoją nazwę dostawcy tożsamości.

    f. W polu tekstowym **punkt końcowy** logowania jednokrotnego wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    przykład Otwórz pobrany **certyfikat kodowany algorytmem Base64** z Azure Portal w Notatniku, wklej go do pola tekstowego **certyfikat X 509** .
    
    h. Mapuj następujące atrybuty z odpowiednią wartością, aby ułatwić Logowanie jednokrotne z usługi Azure AD. Atrybut **identyfikatora użytkownika** jest wymagany do logowania. Do obsługi administracyjnej, **poczty e-mail**, **firmy**, **użytkownika** i **roli** są wymagane. W tej sekcji definiujemy Mapowanie atrybutów (nazwa i wartości), które są skorelowane z tymi zdefiniowanymi w Azure Portal

    ![Andromeda attbmap](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Kliknij pozycję **Zapisz**.


### <a name="create-andromeda-test-user"></a>Utwórz użytkownika testowego Andromeda

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w Andromeda. Andromeda obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze Andromeda, zostanie utworzony nowy po uwierzytelnieniu. Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem obsługi klienta Andromeda](https://www.ngcsoftware.com/support/).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania Andromeda, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania Andromeda i zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do Andromeda, dla którego skonfigurowano Logowanie jednokrotne 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka Andromeda w obszarze Moje aplikacje, jeśli jest skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do Andromeda, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Andromeda można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
