---
title: 'Samouczek: integracja Azure Active Directory z usługą Jobscience | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Jobscience.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23d2694aa4936090367cf881379f81911ae70f9d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81870542"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Samouczek: integracja Azure Active Directory z usługą Jobscience

W tym samouczku dowiesz się, jak zintegrować usługę Jobscience z usługą Azure Active Directory (Azure AD).

Integracja Jobscience z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Jobscience
- Możesz umożliwić użytkownikom automatyczne uzyskiwanie zalogowanych do Jobscience (Logowanie jednokrotne) przy użyciu kont usługi Azure AD
- Kontami można zarządzać w jednej centralnej lokalizacji — Azure Portal

Jeśli chcesz dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Jobscience, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Subskrypcja z włączonym logowaniem jednokrotnym w usłudze Jobscience

> [!NOTE]
> Nie zalecamy używania środowiska produkcyjnego do testowania czynności opisanych w tym samouczku.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska w wersji próbnej usługi Azure AD, możesz uzyskać miesięczną wersję próbną tutaj: [ofertę wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku przetestujesz Logowanie jednokrotne w usłudze Azure AD w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Jobscience z galerii
1. Konfigurowanie i testowanie logowania jednokrotnego w usłudze Azure AD

## <a name="adding-jobscience-from-the-gallery"></a>Dodawanie Jobscience z galerii
Aby skonfigurować integrację programu Jobscience z usługą Azure AD, musisz dodać Jobscience z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Jobscience z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacji dla przedsiębiorstw**. Następnie przejdź do **wszystkich aplikacji**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Jobscience**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. W panelu wyników wybierz pozycję **Jobscience**, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego w usłudze Azure AD
W tej sekcji należy skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Jobscience na podstawie użytkownika testowego o nazwie "Britta Simon".

Aby logowanie jednokrotne działało, usługa Azure AD musi znać, co odpowiedni użytkownik w Jobscience, jest użytkownikiem w usłudze Azure AD. Innymi słowy, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Jobscience.

W programie Jobscience Przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość parametru **username** , aby określić relację łącza.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Jobscience, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configuring-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Jobscience](#creating-a-jobscience-test-user)** — Aby uzyskać odpowiednik Britta Simon w Jobscience, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)** — aby umożliwić usłudze Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie rejestracji](#testing-single-sign-on)** jednokrotnej — aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

W tej sekcji włączasz Logowanie jednokrotne w usłudze Azure AD w Azure Portal i skonfiguruj Logowanie jednokrotne w aplikacji Jobscience.

**Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą Jobscience, wykonaj następujące czynności:**

1. W Azure Portal na stronie integracja aplikacji **Jobscience** kliknij pozycję **Logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. W oknie dialogowym **Logowanie** jednokrotne wybierz pozycję **tryb** jako **Logowanie oparte na protokole SAML** , aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. W sekcji **domena i adresy URL Jobscience** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca:`http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Pobierz tę wartość przez [zespół obsługi klienta Jobscience](http://www.jobscience.com/support) lub z profilu rejestracji jednokrotnej, który zostanie utworzony w dalszej części tego samouczka. 
 
1. W sekcji **certyfikat podpisywania SAML** kliknij pozycję **certyfikat (base64)** , a następnie Zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/jobscience-tutorial/tutorial_general_400.png)

1. W sekcji **Konfiguracja Jobscience** kliknij pozycję **Konfiguruj Jobscience** , aby otworzyć okno **Konfigurowanie logowania** . Skopiuj adres **URL wylogowania, identyfikator jednostki SAML i adres URL usługi logowania** jednokrotnego SAML z **sekcji Szybkie informacje.**

    ![Konfigurowanie logowania jednokrotnego](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Zaloguj się do swojej witryny firmowej Jobscience jako administrator.

1. Przejdź do pozycji **Konfiguracja**.
   
   ![Instalacja](./media/jobscience-tutorial/IC784358.png "Konfigurowanie")

1. W okienku nawigacji po lewej stronie w sekcji **Administruj** kliknij pozycję **Zarządzanie domeną** , aby rozwinąć sekcję pokrewne, a następnie kliknij pozycję **moja domena** , aby otworzyć stronę **Moje domeny** . 
   
   ![Moja domena](./media/jobscience-tutorial/ic767825.png "Moja domena")

1. Aby sprawdzić, czy domena została prawidłowo skonfigurowana, upewnij się, że znajduje się w sekcji "**krok 4 wdrożony dla użytkowników**" i przejrzyj "**Moje ustawienia domeny**".

    ![Domena wdrożona dla użytkownika](./media/jobscience-tutorial/ic784377.png "Domena wdrożona dla użytkownika")

1. W witrynie firmy Jobscience kliknij pozycję **Security Controls**, a następnie kliknij pozycję **Ustawienia rejestracji**jednokrotnej.
    
    ![Mechanizmy zabezpieczeń](./media/jobscience-tutorial/ic784364.png "Mechanizmy zabezpieczeń")

1. W sekcji **Ustawienia logowania** jednokrotnego wykonaj następujące czynności:
    
    ![Ustawienia logowania jednokrotnego](./media/jobscience-tutorial/ic781026.png "Ustawienia logowania jednokrotnego")
    
    a. Wybierz pozycję **SAML Enabled** (SAML włączone).

    b. Kliknij pozycję **Nowy**.

1. W oknie dialogowym **Edytowanie ustawienia logowania** jednokrotnego SAML wykonaj następujące czynności:
    
    ![Ustawienie protokołu SAML logowania jednokrotnego](./media/jobscience-tutorial/ic784365.png "Ustawienie protokołu SAML logowania jednokrotnego")
    
    a. W polu tekstowym **Name** (Nazwa) wpisz nazwę konfiguracji.

    b. W polu tekstowym **wystawcy** wklej wartość **identyfikatora jednostki SAML**, która została skopiowana z Azure Portal.

    c. W polu tekstowym **Identyfikator jednostki** wpisz`https://salesforce-jobscience.com`

    d. Kliknij przycisk **Przeglądaj** , aby przekazać certyfikat usługi Azure AD.

    e. Jako **Typ tożsamości SAML**, wybierz pozycję **potwierdzenie zawiera identyfikator Federacji z obiektu użytkownika**.

    f. Jako **lokalizację tożsamości SAML**, **w elemencie NameIdentfier instrukcji subject**wybierz pozycję Identity (tożsamość).

    przykład W polu tekstowym **adres URL logowania dostawcy tożsamości** wklej wartość **adres URL usługi logowania**jednokrotnego SAML, który został skopiowany z Azure Portal.

    h. W polu tekstowym **adres URL wylogowania dostawcy tożsamości** wklej wartość **adresu URL wylogowania**, który został skopiowany z Azure Portal.

    i. Kliknij pozycję **Zapisz**.

1. W okienku nawigacji po lewej stronie w sekcji **Administruj** kliknij pozycję **Zarządzanie domeną** , aby rozwinąć sekcję pokrewne, a następnie kliknij pozycję **moja domena** , aby otworzyć stronę **Moje domeny** . 
    
    ![Moja domena](./media/jobscience-tutorial/ic767825.png "Moja domena")

1. Na stronie **moja domena** w sekcji **znakowanie strony logowania** kliknij przycisk **Edytuj**.
    
    ![Znakowanie strony logowania](./media/jobscience-tutorial/ic767826.png "Znakowanie strony logowania")

1. Na stronie **znakowanie strony logowania** w sekcji **Usługa uwierzytelniania** zostanie wyświetlona nazwa **ustawień logowania jednokrotnego protokołu SAML** . Wybierz go, a następnie kliknij przycisk **Zapisz**.
    
    ![Znakowanie strony logowania](./media/jobscience-tutorial/ic784366.png "Znakowanie strony logowania")

1. Aby uzyskać adres URL logowania zainicjowany przy użyciu rejestracji jednokrotnej SP, kliknij **Ustawienia rejestracji** jednokrotnej w sekcji Opcje **zabezpieczeń** w menu.

    ![Mechanizmy zabezpieczeń](./media/jobscience-tutorial/ic784368.png "Mechanizmy zabezpieczeń")
    
    Kliknij profil rejestracji jednokrotnej utworzony w powyższym kroku. Na tej stronie jest wyświetlany adres URL logowania jednokrotnego dla swojej firmy (na przykład `https://companyname.my.salesforce.com?so=companyid` .    

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji ** Konfiguracja** w dolnej części strony. Więcej informacji na temat funkcji osadzonej dokumentacji można znaleźć tutaj: [Dokumentacja usługi Azure AD Embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **Azure Portal**w okienku nawigacji po lewej stronie kliknij ikonę **Azure Active Directory** .

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do pozycji **Użytkownicy i grupy** , a następnie kliknij pozycję **Wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/create_aaduser_02.png) 

1. Aby otworzyć okno dialogowe **użytkownika** , kliknij przycisk **Dodaj** w górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/create_aaduser_03.png) 

1. Na stronie **użytkownika** wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. W polu tekstowym **Nazwa** wpisz **BrittaSimon**.

    b. W polu tekstowym **Nazwa użytkownika** wpisz **adres e-mail** BrittaSimon.

    c. Wybierz pozycję **Pokaż hasło** i Zapisz wartość **hasła**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-jobscience-test-user"></a>Tworzenie użytkownika testowego Jobscience

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze Jobscience, muszą one być obsługiwane w usłudze Jobscience. W przypadku Jobscience, Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

>[!NOTE]
>Do aprowizacji Azure Active Directory kontami użytkowników można używać innych narzędzi do tworzenia kont użytkowników lub interfejsów API udostępnionych przez program Jobscience.
>  
        
**Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmowej **Jobscience** jako administrator.

1. Przejdź do pozycji Konfiguracja.
   
   ![Instalacja](./media/jobscience-tutorial/ic784358.png "Konfigurowanie")
1. Przejdź do pozycji **Zarządzaj \> użytkownikami**użytkownicy.
   
   ![Użytkownicy](./media/jobscience-tutorial/ic784369.png "Użytkownicy")
1. Kliknij pozycję **New User** (Nowy użytkownik).
   
   ![Wszyscy użytkownicy](./media/jobscience-tutorial/ic784370.png "Wszyscy użytkownicy")
1. W oknie dialogowym **Edytowanie użytkownika** wykonaj następujące czynności:
   
   ![Edycja użytkownika](./media/jobscience-tutorial/ic784371.png "Edycja użytkownika")
   
   a. W polu tekstowym **imię i nazwisko** wpisz imię użytkownika, np. Britta.
   
   b. W polu **tekstowym nazwisko wpisz nazwisko użytkownika** , np. Simon.
   
   c. W polu tekstowym **alias** wpisz nazwę aliasu użytkownika, na przykład Brittas.

   d. W polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail użytkownika, np. Brittasimon@contoso.com.

   e. W polu tekstowym **Nazwa użytkownika** wpisz nazwę użytkownika, np Brittasimon@contoso.com .

   f. W polu tekstowym **Nazwa nick** wpisz nazwę nick użytkownika, na przykład Simon.

   przykład Kliknij pozycję **Zapisz**.

    
> [!NOTE]
> Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim stanie się ono aktywne.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Jobscience.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon do Jobscience, wykonaj następujące czynności:**

1. W Azure Portal Otwórz widok aplikacje, a następnie przejdź do widoku katalogu i przejdź do pozycji **aplikacje przedsiębiorstwa** , a następnie kliknij pozycję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście Aplikacje wybierz pozycję **Jobscience**.

    ![Konfigurowanie logowania jednokrotnego](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. W menu po lewej stronie kliknij pozycję **Użytkownicy i grupy**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Add** (Dodaj). Następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodaj przypisanie** .

    ![Przypisz użytkownika][203]

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **Wybierz** w oknie dialogowym **Użytkownicy i grupy** .

1. Kliknij przycisk **Assign (Przypisz** ) w oknie dialogowym **Dodaj przypisanie** .
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Jobscience w panelu dostępu należy automatycznie zalogować się do aplikacji Jobscience.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jobscience-tutorial/tutorial_general_01.png
[2]: ./media/jobscience-tutorial/tutorial_general_02.png
[3]: ./media/jobscience-tutorial/tutorial_general_03.png
[4]: ./media/jobscience-tutorial/tutorial_general_04.png

[100]: ./media/jobscience-tutorial/tutorial_general_100.png

[200]: ./media/jobscience-tutorial/tutorial_general_200.png
[201]: ./media/jobscience-tutorial/tutorial_general_201.png
[202]: ./media/jobscience-tutorial/tutorial_general_202.png
[203]: ./media/jobscience-tutorial/tutorial_general_203.png

