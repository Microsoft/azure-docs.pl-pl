---
title: 'Samouczek: integracja Azure Active Directory z dostępem prywatnym rozwiązania Zscaler (ZPA) | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i dostępem prywatnym rozwiązania Zscaler (ZPA).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: jeedes
ms.openlocfilehash: 347fc931dcc7f794fe7376daaac21ce7ed9198df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184747"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Samouczek: Integrowanie prywatnego dostępu rozwiązania Zscaler (ZPA) z Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować rozwiązania Zscaler Private Access (ZPA) z usługą Azure Active Directory (Azure AD). W przypadku integracji prywatnego dostępu rozwiązania Zscaler (ZPA) z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do prywatnego dostępu rozwiązania Zscaler (ZPA).
* Zezwól użytkownikom na automatyczne logowanie do rozwiązania Zscaler prywatnego dostępu (ZPA) przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (rozwiązania Zscaler Private Access) (ZPA).

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. 

* Rozwiązania Zscaler Private Access (ZPA) obsługuje usługę **SP** zainicjowaną przez usługę SSO.

## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Dodawanie prywatnego dostępu rozwiązania Zscaler (ZPA) z galerii

Aby skonfigurować integrację prywatnego dostępu rozwiązania Zscaler (ZPA) z usługą Azure AD, musisz dodać rozwiązania Zscaler Private Access (ZPA) z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **rozwiązania Zscaler Private Access (ZPA)** w polu wyszukiwania.
1. Wybierz pozycję **rozwiązania Zscaler Private Access (ZPA)** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą rozwiązania Zscaler prywatnego dostępu (ZPA) przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze rozwiązania Zscaler Private Access (ZPA).

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu prywatnego dostępu rozwiązania Zscaler (ZPA), wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne w usłudze rozwiązania Zscaler Private Access (ZPA)](#configure-zscaler-private-access-zpa-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego rozwiązania Zscaler dostępu prywatnego (ZPA)](#create-zscaler-private-access-zpa-test-user)** , aby dysponować odpowiednikiem B. Simon w rozwiązania Zscaler prywatnym dostęp (ZPA), który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **rozwiązania Zscaler Private Access (ZPA)** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    1. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > Wartość **adresu URL logowania** nie jest prawdziwa. Zaktualizuj wartość za pomocą rzeczywistego adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej rozwiązania Zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfiguruj pojedyncze Sign-On za pomocą elementu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie rozwiązania Zscaler prywatnego dostępu (ZPA)** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie Britta Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `Britta Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do prywatnego dostępu rozwiązania Zscaler (ZPA).

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **rozwiązania Zscaler Private Access (ZPA)**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-zscaler-private-access-zpa-sso"></a>Konfigurowanie logowania jednokrotnego (ZPA) rozwiązania Zscaler prywatnego dostępu

1. Aby zautomatyzować konfigurację w ramach prywatnego dostępu rozwiązania Zscaler (ZPA), musisz zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Konfiguracja rozwiązania Zscaler prywatny dostęp (ZPA)** spowoduje przekierowanie do aplikacji rozwiązania Zscaler Private Access (ZPA). Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi rozwiązania Zscaler Private Access (ZPA). Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować rozwiązania Zscaler prywatnego dostępu (ZPA), Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy rozwiązania Zscaler Private Access (ZPA) jako administrator i wykonaj następujące czynności:

4. Z lewej strony menu kliknij pozycję **Administracja** i przejdź do sekcji **uwierzytelnianie** , a następnie kliknij pozycję **Konfiguracja dostawcy tożsamości**.

    ![Administracja administratora dostępu prywatnego rozwiązania Zscaler](./media/zscalerprivateaccess-tutorial/administration.png)

5. W prawym górnym rogu kliknij pozycję **Dodaj konfigurację dostawcy tożsamości**. 

    ![Administrator dostępu prywatnego rozwiązania Zscaler dostawcy tożsamości](./media/zscalerprivateaccess-tutorial/metadata.png)

6. Na stronie **Dodawanie konfiguracji dostawcy tożsamości** wykonaj następujące czynności:
 
    ![Rozwiązania Zscaler administrator dostępu prywatnego](./media/zscalerprivateaccess-tutorial/select.png)

    a. Kliknij pozycję **Wybierz plik** , aby przekazać pobrany plik metadanych z usługi Azure AD w polu **przekazywania pliku metadanych dostawcy tożsamości** .

    b. Odczytuje **metadane dostawcy tożsamości** z usługi Azure AD i wypełnia wszystkie informacje o polach, jak pokazano poniżej.

    ![Rozwiązania Zscaler konfiguracja administratora dostępu prywatnego](./media/zscalerprivateaccess-tutorial/configure.png)

    c. Wybierz **domenę z pola domeny.**
    
    d. Kliknij pozycję **Zapisz**.

### <a name="create-zscaler-private-access-zpa-test-user"></a>Tworzenie użytkownika testowego rozwiązania Zscaler Private Access (ZPA)

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w usłudze rozwiązania Zscaler Private Access (ZPA). Skontaktuj się z [zespołem pomocy technicznej rozwiązania Zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) , aby dodać użytkowników z platformy rozwiązania Zscaler Private Access (ZPA).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do prywatnego dostępu rozwiązania Zscaler (ZPA), w którym można zainicjować przepływ logowania. 

* Przejdź do adresu URL logowania do usługi rozwiązania Zscaler Private Access (ZPA) bezpośrednio i zainicjuj tam przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka rozwiązania Zscaler Private Access (ZPA) w obszarze Moje aplikacje zostanie on przekierowany do adresu URL logowania do rozwiązania Zscaler prywatnego (ZPA). Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu prywatnego dostępu rozwiązania Zscaler (ZPA) można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).