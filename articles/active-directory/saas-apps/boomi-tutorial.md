---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Boomi | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i platformą Boomi.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: jeedes
ms.openlocfilehash: c58566c628eedd1dbc3d86ae6a142156cbf31211
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585200"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Boomi

W tym samouczku dowiesz się, jak zintegrować usługę Boomi z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Boomi z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Boomi.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Boomi przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Boomi.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Boomi obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne.

## <a name="add-boomi-from-the-gallery"></a>Dodaj Boomi z galerii

Aby skonfigurować integrację platformy Boomi z usługą Azure AD, musisz dodać platformę Boomi z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Boomi** w polu wyszukiwania.
1. Wybierz pozycję **Boomi** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-boomi"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Boomi

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Boomi przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Boomi.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Boomi, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-boomi-sso)** w usłudze Boomi, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego Boomi](#create-boomi-test-user)** , aby dysponować odpowiednikiem B. Simon w Boomi, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Boomi** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** , jeśli masz **plik metadanych dostawcy usług** i chcesz skonfigurować w trybie zainicjowania programu **dostawcy tożsamości** , wykonaj następujące czynności:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatorów** i **adresów URL odpowiedzi** są automatycznie wypełniane w sekcji Podstawowa konfiguracja SAML.

    d. Wprowadź **adres URL logowania**, taki jak `https://platform.boomi.com/AtomSphere.html#build;accountId={your-accountId}` .

    > [!Note]
    > **Plik metadanych dostawcy usług** zostanie pobrany z sekcji **Konfigurowanie logowania jednokrotnego Boomi** , która została omówiona w dalszej części tego samouczka. Jeśli wartości **identyfikatorów** i **adresów URL odpowiedzi** nie zostaną wypełnione automatycznie, wprowadź wartości ręcznie zgodnie z wymaganiami.

1. Aplikacja Boomi oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![Zrzut ekranu przedstawia atrybuty użytkownika & oświadczenia z wartościami domyślnymi, takimi jak podano użytkownikaname i EmailAddress User. mail.](common/default-attributes.png)

1. Oprócz powyższych, aplikacja Boomi oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa |  Atrybut źródłowy|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie Boomi** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Boomi.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Boomi**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-boomi-sso"></a>Konfigurowanie logowania jednokrotnego Boomi

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny Boomi jako administrator.

1. Przejdź do pozycji **Nazwa firmy** i przejdź do pozycji **Set up** (Konfigurowanie).

1. Kliknij kartę **SSO Options** (Opcje logowania jednokrotnego), a następnie wykonaj poniższe kroki.

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/boomi-tutorial/import.png)

    a. Zaznacz pole wyboru **Enable SAML Single Sign-On** (Włącz logowanie jednokrotne SAML).

    b. Kliknij przycisk **Import** (Importuj), aby przekazać pobrany certyfikat z usługi Azure AD do pola **Identity Provider Certificate** (Certyfikat dostawcy tożsamości).

    c. W polu tekstowym **Identity Provider Login URL** (Adres URL logowania dostawcy tożsamości) wstaw wartość **adresu URL logowania** z okna konfiguracji aplikacji usługi Azure AD.

    d. W polu **Lokalizacja identyfikatora federacyjnego** wybierz pozycję **identyfikator Federacji w FEDERATION_ID** przycisk radiowy elementu atrybutu.

    e. Skopiuj **adres URL metadanych AtomSphere**, przejdź do **adresu URL metadanych** za pomocą wybranej przeglądarki i Zapisz dane wyjściowe do pliku. Przekaż **adres URL metadanych** w sekcji **podstawowe konfiguracje języka SAML** w Azure Portal.

    f. Kliknij przycisk **Save** (Zapisz).

### <a name="create-boomi-test-user"></a>Tworzenie użytkownika testowego platformy Boomi

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Boomi, muszą one być obsługiwane w Boomi. W przypadku platformy Boomi aprowizowanie jest zadaniem ręcznym.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Aby aprowizować konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do firmowej witryny Boomi jako administrator.

1. Po zalogowaniu się przejdź do pozycji **User Management** (Zarządzanie użytkownikami) i wybierz pozycję **Users** (Użytkownicy).

    ![Zrzut ekranu pokazuje stronę zarządzania użytkownikami z wybranymi użytkownikami.](./media/boomi-tutorial/user.png "Użytkownicy")

1. Kliknij **+**  ikonę i zostanie otwarte okno dialogowe **Dodawanie/obsługa ról użytkownika** .

    ![Zrzut ekranu przedstawia wybraną ikonę +.](./media/boomi-tutorial/add.png "Użytkownicy")

    ![Zrzut ekranu przedstawia role użytkownika Dodawanie/obsługa, w których konfigurowany jest użytkownik.](./media/boomi-tutorial/roles.png "Użytkownicy")

    a. W polu tekstowym **User e-mail address** (Adres e-mail użytkownika) wpisz adres e-mail użytkownika, taki jak B.Simon@contoso.com.

    b. W polu tekstowym **imię i nazwisko** wpisz imię użytkownika jak B.

    c. W polu **tekstowym nazwisko wpisz nazwisko użytkownika** , np. Simon.

    d. Wprowadź **identyfikator federacyjny** użytkownika. Każdy użytkownik musi mieć identyfikator federacyjny, który w sposób unikatowy identyfikuje użytkownika w ramach konta.

    e. Przypisz do użytkownika rolę **Standard User** (Użytkownik standardowy). Nie przypisuj roli administratora, ponieważ będzie ona mieć dostęp do normalnego dostępu do atmosfery, a także dostępu do logowania jednokrotnego.

    f. Kliknij przycisk **OK**.

    > [!NOTE]
    > Użytkownik nie otrzyma wiadomości e-mail z powiadomieniem powitalnym zawierającym hasło, które może służyć do logowania się do konta AtomSphere, ponieważ ich hasło jest zarządzane przez dostawcę tożsamości. Do aprowizowania kont użytkowników usługi Azure Active Directory możesz użyć dowolnych innych interfejsów API lub narzędzi do tworzenia kont użytkowników platformy Boomi oferowanych przez tę platformę.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Testuj tę aplikację w Azure Portal i należy automatycznie zalogować się do Boomi, dla którego skonfigurowano Logowanie jednokrotne.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka Boomi w obszarze Moje aplikacje należy automatycznie zalogować się do Boomi, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Boomi można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).