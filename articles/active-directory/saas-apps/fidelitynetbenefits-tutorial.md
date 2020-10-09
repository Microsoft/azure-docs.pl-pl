---
title: 'Samouczek: integracja Azure Active Directory z zaletami z dokładnością | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Fidelity NetBenefits.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.openlocfilehash: 199a443bf2f0c674f85fec0ecfd0083c92f17fe4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88555222"
---
# <a name="tutorial-azure-active-directory-integration-with-fidelity-netbenefits"></a>Samouczek: integracja Azure Active Directory z zaletami z dokładnością

Z tego samouczka dowiesz się, jak zintegrować aplikację Fidelity NetBenefits z usługą Azure Active Directory (Azure AD).
Integrowanie aplikacji Fidelity NetBenefits z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do aplikacji Fidelity NetBenefits.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Fidelity NetBenefits (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Fidelity NetBenefits potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Fidelity NetBenefits z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Fidelity NetBenefits obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**

* Aplikacja Fidelity NetBenefits obsługuje aprowizowanie użytkowników typu **Just In Time**

## <a name="adding-fidelity-netbenefits-from-the-gallery"></a>Dodawanie aplikacji Fidelity NetBenefits z galerii

Aby skonfigurować integrację aplikacji Fidelity NetBenefits z usługą Azure AD, musisz dodać aplikację Fidelity NetBenefits z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Fidelity NetBenefits z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Fidelity NetBenefits**, wybierz pozycję **Fidelity NetBenefits** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Fidelity NetBenefits na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Fidelity NetBenefits, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Fidelity NetBenefits.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją Fidelity NetBenefits, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Fidelity NetBenefits](#configure-fidelity-netbenefits-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji Fidelity NetBenefits](#create-fidelity-netbenefits-test-user)** — aby mieć w aplikacji Fidelity NetBenefits odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji Fidelity NetBenefits, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Fidelity NetBenefits** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![Domena i adresy URL aplikacji Fidelity NetBenefits — informacje dotyczące logowania jednokrotnego](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: 

    W przypadku środowiska testowego: `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    W przypadku środowiska produkcyjnego: `urn:sp:fidelity:geninbndnbparts20`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL dostarczony przez firmę Fidelity w czasie wdrażania lub skontaktuj się z przypisanym menedżerem ds. obsługi klienta Fidelity.

5. Aplikacja Fidelity NetBenefits oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu języka SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Aplikacja Fidelity NetBenefits oczekuje mapowania atrybutu **nameidentifier** z oświadczeniem **employeeid** lub dowolnym innym oświadczeniem odpowiednim dla danej organizacji jako atrybut **nameidentifier**, więc należy edytować mapowanie atrybutów, klikając ikonę **Edytuj**, i zmienić mapowanie atrybutów.

    ![image (obraz)](common/edit-attribute.png)

    >[!Note]
    >Aplikacja Fidelity NetBenefits obsługuje federację statyczną i dynamiczną. Statyczna oznacza, że nie będzie używać aprowizacji użytkowników typu just in time opartej na SAML, natomiast dynamiczna oznacza, że obsługuje aprowizację użytkowników typu just in time. W przypadku korzystania z obsługi opartej na kompilatorze JIT klienci muszą dodać więcej oświadczeń w usłudze Azure AD, takich jak dataurodzenie użytkownika itd. Te szczegóły są udostępniane przez przydzieloną **Service Manager klienta** i muszą włączać tę dynamiczną Federację dla danego wystąpienia.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. W sekcji **Konfigurowanie aplikacji Fidelity NetBenefits** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-fidelity-netbenefits-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji Fidelity NetBenefits

Aby skonfigurować logowanie jednokrotne po stronie aplikacji **Fidelity NetBenefits**, musisz wysłać pobrany **kod XML metadanych federacji** i odpowiednie adresy URL skopiowane z witryny Azure Portal [zespołowi pomocy technicznej aplikacji Fidelity NetBenefits](mailto:SSOMaintenance@fmr.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon \@ yourcompanydomain. Extension**  
    Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Fidelity NetBenefits.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, pozycję **Wszystkie aplikacje**, a następnie pozycję **Fidelity NetBenefits**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Fidelity NetBenefits**.

    ![Link aplikacji Fidelity NetBenefits na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-fidelity-netbenefits-test-user"></a>Tworzenie użytkownika testowego aplikacji Fidelity NetBenefits

W tej sekcji utworzysz w aplikacji Fidelity NetBenefits użytkownika o nazwie Britta Simon. Jeśli tworzysz federację statyczną, skontaktuj się z przypisanym **menedżerem usługi klienta Fidelity** w celu utworzenia użytkowników na platformie Fidelity NetBenefits. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

W przypadku federacji dynamicznej użytkownicy są tworzeni przy użyciu aprowizacji użytkowników typu Just In Time. W przypadku korzystania z obsługi opartej na kompilatorze JIT klienci muszą dodać więcej oświadczeń w usłudze Azure AD, takich jak dataurodzenie użytkownika itd. Te szczegóły są udostępniane przez przydzieloną **Service Manager klienta** i muszą włączać tę dynamiczną Federację dla danego wystąpienia.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Fidelity NetBenefits w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Fidelity NetBenefits, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

