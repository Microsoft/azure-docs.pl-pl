---
title: 'Samouczek: integracja Azure Active Directory z usługą łączenie | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Coupa.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: d6a686b38c9b67ed8b1a7801c2a6ba95ef29558c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101652994"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Samouczek: integracja Azure Active Directory z łączeniem

W tym samouczku dowiesz się, jak zintegrować połączenie z usługą Azure Active Directory (Azure AD). Po zintegrowaniu z usługą Azure AD możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do.
* Zezwól użytkownikom na automatyczne logowanie do konta usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Coupa obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="add-coupa-from-the-gallery"></a>Dodaj łączenie z galerii

Aby skonfigurować integrację aplikacji Coupa z usługą Azure AD, musisz dodać aplikację Coupa z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** w polu wyszukiwania wpisz ciąg **łączenie** .
1. Wybierz pozycję **łączenie** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-coupa"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD dla programu

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w temacie.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfigurowanie logowania jednokrotnego](#configure-coupa-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego](#create-coupa-test-user)** , aby uzyskać odpowiednika B. Simon, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie **integracji aplikacji w** programie Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.coupahost.com`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Aby uzyskać tę wartość, skontaktuj się z [zespołem pomocy technicznej i obsługi klienta Coupa](https://success.coupa.com/Support/Contact_Us?).

    b. W polu **Identyfikator** wpisz adres URL: 

    | Środowisko  | Adres URL |
    |:-------------|----|
    | Piaskownica | `sso-stg1.coupahost.com`|
    | Produkcja | `sso-prd1.coupahost.com`|
    | | |

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL: 

    | Środowisko | Adres URL |
    |------------- |----|
    | Piaskownica | `https://sso-stg1.coupahost.com/sp/ACS.saml2`|
    | Produkcja | `https://sso-prd1.coupahost.com/sp/ACS.saml2`|
    | | |

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie aplikacji Coupa** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do programu.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz aplikację **Coupa**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-coupa-sso"></a>Konfigurowanie logowania jednokrotnego

1. Zaloguj się do witryny firmy w aplikacji Coupa jako administrator.

2. Przejdź do pozycji **Setup (Konfiguracja) \> Security Control (Kontrola zabezpieczeń)**.

    ![Kontrolki zabezpieczeń](./media/coupa-tutorial/setup.png "Mechanizmy zabezpieczeń")

3. W sekcji **Log in using Coupa credentials** (Zaloguj się przy użyciu poświadczeń Coupa) wykonaj następujące kroki:

    ![Łączenie metadanych SP](./media/coupa-tutorial/login.png "Łączenie metadanych SP")

    a. Wybierz pozycję **Log in using SAML** (Zaloguj się za pośrednictwem protokołu SAML).

    b. Kliknij przycisk **Browse** (Przeglądaj), aby przekazać metadane pobrane z witryny Azure Portal.

    c. Kliknij pozycję **Zapisz**.

### <a name="create-coupa-test-user"></a>Tworzenie użytkownika testowego aplikacji Coupa

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji Coupa, należy aprowizować ich w aplikacji Coupa.  

* W przypadku aplikacji Coupa aprowizacja jest zadaniem ręcznym.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy w aplikacji **Coupa** jako administrator.

2. W menu u góry kliknij opcję **Setup** (Konfiguracja), a następnie kliknij pozycję **Users** (Użytkownicy).

    ![Użytkownicy](./media/coupa-tutorial/user.png "Użytkownicy")

3. Kliknij pozycję **Utwórz**.

    ![Tworzenie użytkowników](./media/coupa-tutorial/create.png "Tworzenie użytkowników")

4. W sekcji **User Create** (Tworzenie użytkownika) wykonaj następujące kroki:

    ![Szczegóły użytkownika](./media/coupa-tutorial/details.png "Szczegóły użytkownika")

    a. W polach tekstowych **Login**, **First name** (Imię), **Last Name** (Nazwisko), **Single Sign-On ID** (Identyfikator logowania jednokrotnego) i **Email** (Adres e-mail) wpisz wartości prawidłowego konta usługi Azure Active Directory, które chcesz aprowizować.

    b. Kliknij pozycję **Utwórz**.

    >[!NOTE]
    >Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem w celu potwierdzenia konta, zanim stanie się ono aktywne.
    >

>[!NOTE]
>Do udostępniania kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników i interfejsów API dostarczonych przez program.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do konta, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania przy użyciu konta użytkownika i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka linku w moich aplikacjach należy automatycznie zalogować się do linku, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu przeglądarki można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app)