---
title: 'Samouczek: Azure Active Directory integracji z | PolicyStat Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedyncze między usługami Azure Active Directory i PolicyStat.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 5c2520c8e209ab8319cbc5a369b70d247a52232c
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601002"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Samouczek: Azure Active Directory integracji z policystat

Z tego samouczka dowiesz się, jak zintegrować usługę PolicyStat z Azure Active Directory (Azure AD).
Zintegrowanie aplikacji PolicyStat z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do zasad PolicyStat.
* Możesz umożliwić swoim użytkownikom automatyczne logowanie do aplikacji PolicyStat (logowanie pojedynczej) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z usługą PolicyStat potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji PolicyStat z obsługą logowania pojedynczego

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku usługi Azure AD US Government w chmurze. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA w usłudze Azure AD i skonfigurować w taki sam sposób, jak w chmurze publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* PolicyStat obsługuje logowanie **jednokrotne inicjowane** przez sp

* PolicyStat obsługuje **aprowizowanie** użytkowników just in time

## <a name="adding-policystat-from-the-gallery"></a>Dodawanie funkcji PolicyStat z galerii

Aby skonfigurować integrację aplikacji PolicyStat z usługą Azure AD, musisz dodać aplikację PolicyStat z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać policystat z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **PolicyStat,** wybierz **pozycję PolicyStat** z panelu wyników i kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![PolicyStat na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie pojedynczej usługi Azure AD z usługą PolicyStat, bazując na danych użytkownika testowego **Britta Simon.**
Aby logowanie pojedyncze działało, należy utworzyć relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji PolicyStat.

Aby skonfigurować i przetestować logowanie pojedyncze usługi Azure AD z usługą PolicyStat, należy wykonać czynności z poniższych bloków konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania pojedynczego w aplikacji PolicyStat](#configure-policystat-single-sign-on)** — aby skonfigurować ustawienia logowania Sign-On po stronie aplikacji.
3. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
4. **[Tworzenie użytkownika testowego aplikacji PolicyStat](#create-policystat-test-user)** — aby mieć w aplikacji PolicyStat odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednorazowe usługi Azure AD w aplikacji PolicyStat, wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com/)aplikacji na stronie **integracji aplikacji PolicyStat** wybierz **pozycję Logowanie pojedyncze.**

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL logowania pojedynczego aplikacji PolicyStat](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.policystat.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z zespołem pomocy technicznej klienta aplikacji [PolicyStat.](https://rldatix.com/services-support/support) Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

5. Aplikacja PolicyStat oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij **ikonę Edytuj,** aby **otworzyć okno dialogowe Atrybuty** użytkownika.

    ![Zrzut ekranu przedstawiający okno dialogowe "Atrybuty użytkownika" z wybraną ikoną "Edytuj".](common/edit-attribute.png)

6. Oprócz powyższych aplikacja PolicyStat oczekuje jeszcze kilku atrybutów, które zostaną przekazane w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Nazwa | Atrybut źródłowy |
    |------------------- | -------------------- |
    | Identyfikator UID | ExtractMailPrefix([mail]) |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.
    
    ![Zrzut ekranu przedstawiający sekcję "Oświadczenia użytkownika" z wyróżnieniami "Dodaj nowe oświadczenie" i "Zapisz".](common/new-save-attribute.png)

    ![Zrzut ekranu przedstawiający okno dialogowe "Zarządzanie oświadczeniami użytkowników" z wyróżnione polami tekstowymi "Nazwa", "Przekształcenie" i "Parametr" oraz wybranym przyciskiem "Zapisz".](./media/policystat-tutorial/attribute01.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Pozostaw pole **Przestrzeń nazw** puste.

    d. Wybierz źródło jako **przekształcenie**.

    e. Na liście **Przekształcenia** wpisz wartość atrybutu pokazaną dla tego wiersza.
    
    f. Z listy **Parametr 1** wpisz wartość atrybutu pokazaną dla tego wiersza.

    przykład Kliknij pozycję **Zapisz**.

7. W **sekcji Konfigurowanie zasadStat** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-policystat-single-sign-on&quot;></a>Konfigurowanie pojedynczego Sign-On PolicyStat

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny PolicyStat jako administrator.

2. Kliknij **kartę Administrator,** a następnie kliknij pozycję **Konfiguracja Sign-On w** lewym okienku nawigacji.
   
    ![Menu administratora](./media/policystat-tutorial/ic808633.png &quot;Menu administratora")

3. Kliknij **pozycję Your IDP Metadata (Metadane** dostawcy tożsamości), a następnie w sekcji **Your IDP Metadata (Metadane dostawcy** tożsamości) wykonaj następujące kroki:
   
    ![Zrzut ekranu przedstawiający wybraną akcję "Twoje metadane ID P".](./media/policystat-tutorial/ic808636.png "Konfiguracja Sign-On pojedynczej aplikacji")
   
    a. Otwórz pobrany plik metadanych, skopiuj zawartość, a następnie wklej ją w polu tekstowym **Your Identity Provider Metadata (Metadane** dostawcy tożsamości).

    b. Kliknij **pozycję Zapisz zmiany.**

4. Kliknij **pozycję Konfiguruj atrybuty,** a następnie w sekcji **Konfigurowanie atrybutów** wykonaj następujące kroki:
   
    a. W polu **tekstowym Username Attribute** (Atrybut nazwy użytkownika) wpisz **uid**.

    b. W polu **tekstowym First Name Attribute (Atrybut** imienia) wpisz nazwę oświadczenia First Name Attribute (Atrybut imienia) z platformy **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`** Azure.

    c. W polu **tekstowym Last Name Attribute (Atrybut** nazwiska) wpisz nazwę oświadczenia Last Name Attribute z platformy **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`** Azure.

    d. W polu **tekstowym Email Attribute (Atrybut** adresu e-mail) wpisz nazwę oświadczenia atrybutu adresu e-mail z platformy **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** Azure.

    e. Kliknij **pozycję Zapisz zmiany.**

5. W sekcji **Setup (Konfiguracja)** wybierz **pozycję Enable Single Sign-on Integration**(Włącz integrację logowania pojedynczego).
   
    ![Konfiguracja Sign-On pojedynczej aplikacji](./media/policystat-tutorial/ic808634.png "Konfiguracja Sign-On pojedynczej aplikacji")


### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz własne konto, aby korzystać z logowania pojedynczego platformy Azure, udzielając dostępu do aplikacji PolicyStat.

1. Na stronie Azure Portal aplikacje dla **przedsiębiorstw,** wybierz pozycję **Wszystkie aplikacje,** a następnie wybierz pozycję **PolicyStat.**

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **PolicyStat.**

    ![Link PolicyStat na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W **oknie dialogowym** Użytkownicy i grupy wybierz swoje konto na liście Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz jakiejkolwiek wartości roli w asercji  SAML, w oknie dialogowym Wybieranie roli wybierz  z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk Wybierz w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-policystat-test-user"></a>Tworzenie użytkownika testowego policystat

W tej sekcji w aplikacji PolicyStat jest tworzony użytkownik o nazwie Britta Simon. Funkcja PolicyStat obsługuje aprowizowanie użytkowników just in time, które jest domyślnie włączone. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w policystat, zostanie utworzony po uwierzytelnieniu.

>[!NOTE]
>Aby aprowizować konta użytkowników usługi Azure AD, możesz użyć dowolnych innych interfejsów API lub narzędzi do tworzenia konta użytkownika PolicyStat dostarczonych przez usługę PolicyStat.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka PolicyStat w Panel dostępu powinno nastać automatyczne zalogowanie do aplikacji PolicyStat, dla której została ustawiona logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panel dostępu, [zobacz Wprowadzenie do Panel dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)
