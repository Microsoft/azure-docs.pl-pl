---
title: 'Samouczek: integracja z logowaniem jednokrotnym (SSO) Azure Active Directory z dryfem | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a aplikacją Drift.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: 056b20325b58db74feced67e88157c798214ae70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88555777"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-drift"></a>Samouczek: integracja logowania jednokrotnego (SSO) Azure Active Directory z dryfem

W tym samouczku dowiesz się, jak zintegrować dryf z Azure Active Directory (Azure AD). Gdy integrujesz dryf z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do dryfu.
* Zezwól użytkownikom na automatyczne logowanie się, aby przepisywać swoje konta usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Drift obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług oraz dostawcę tożsamości**
* Aplikacja Drift obsługuje aprowizowanie użytkowników typu **Just in Time**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-drift-from-the-gallery"></a>Dodawanie aplikacji Drift z galerii

Aby skonfigurować integrację aplikacji Drift z usługą Azure AD, musisz dodać aplikację Drift z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **dryf** w polu wyszukiwania.
1. Wybierz pozycję **dryf** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-drift"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD na potrzeby dryfowania

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą dryfu przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w dryfie.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD z dryfem, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-drift-sso)** w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego dryfu](#create-drift-test-user)** — Aby uzyskać odpowiednika B. Simon w dryfie, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **integracja aplikacji do** rozłożenia Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML**   aplikacja została wstępnie skonfigurowana w **IDP**   trybie inicjalizacji dostawcy tożsamości, a wymagane adresy URL są już wstępnie wypełnione na platformie Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz**   .

    a. Kliknij pozycję **Ustaw dodatkowe adresy URL**.
 
    b. W polu tekstowym **Stan przekaźnika** wpisz adres URL: `https://app.drift.com` 

    c. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę usług**, wykonaj następujący krok:

    d. W polu tekstowym **adres URL logowania** wpisz adres URL: `https://start.drift.com`

6. Aplikacja dryfu oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/edit-attribute.png)

7. Oprócz powyższych, aplikacja dryfu oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami. 

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |    
    | Nazwa | user.displayname |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie dryfu** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz funkcję B. Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do dryfu.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Drift**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-drift-sso"></a>Konfigurowanie rejestracji jednokrotnej

1. Aby zautomatyzować konfigurację w ramach dryfowania, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **dryf konfiguracji** nakazujesz aplikacji z dryfem. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do dryfu. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-4.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować dryf, Otwórz nowe okno przeglądarki sieci Web i zaloguj się do firmowej witryny z przedziału jako administrator i wykonaj następujące czynności:

4. W lewej części paska menu kliknij pozycję **Ustawienia**ustawienia  >  **aplikacji**  >  **uwierzytelnianie** i wykonaj następujące czynności:

    ![Link administratora](./media/drift-tutorial/tutorial_drift_admin.png)

    a. Przekaż **plik XML metadanych federacji** pobrany z witryny Azure Portal do pola tekstowego **Upload Identity Provider metadata file (Przekaż plik metadanych dostawcy tożsamości)**.

    b. Po przekazaniu pliku metadanych pozostałe wartości na stronie zostaną automatycznie wypełnione.

    c. Kliknij pozycję **Enable SAML** (Włącz protokół SAML).

### <a name="create-drift-test-user"></a>Tworzenie użytkownika testowego aplikacji Drift

W tej sekcji w aplikacji Drift jest tworzony użytkownik o nazwie Britta Simon. Aplikacja Drift obsługuje aprowizację użytkowników typu just-in-time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Drift, zostanie utworzony po uwierzytelnieniu.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej aplikacji Drift](mailto:integrations@drift.com).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Drift w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Drift, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj dryfowanie za pomocą usługi Azure AD](https://aad.portal.azure.com/)

