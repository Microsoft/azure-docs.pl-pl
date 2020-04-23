---
title: 'Samouczek: Integracja usługi Azure Active Directory z jobscience | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a jobscience.
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
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870542"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Samouczek: Integracja usługi Azure Active Directory z jobscience

W tym samouczku dowiesz się, jak zintegrować jobscience z usługą Azure Active Directory (Azure AD).

Integracja jobscience z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Jobscience
- Możesz włączyć użytkownikom automatyczne logowanie do jobscience (logowanie jednokrotne) za pomocą swoich kont usługi Azure AD
- Możesz zarządzać kontami w jednej centralnej lokalizacji - witrynie Azure portal

Jeśli chcesz dowiedzieć się więcej szczegółów na temat integracji aplikacji SaaS z usługą Azure AD, zobacz, [co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z jobscience, potrzebujesz następujących elementów:

- Subskrypcji usługi Azure AD
- Subskrypcja z obsługą logowania jednokrotnego w ucho.

> [!NOTE]
> Nie zalecamy używania środowiska produkcyjnego do testowania czynności opisanych w tym samouczku.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska testowego usługi Azure AD, możesz uzyskać miesięczną wersję próbną tutaj: [Oferta próbna.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku testujesz logowanie jednokrotne usługi Azure AD w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Jobscience z galerii
1. Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

## <a name="adding-jobscience-from-the-gallery"></a>Dodawanie Jobscience z galerii
Aby skonfigurować integrację jobscience z usługą Azure AD, należy dodać Jobscience z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Jobscience z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacji Enterprise**. Następnie przejdź do **wszystkich aplikacji**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Jobscience**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. W panelu wyników wybierz pozycję **Jobscience**, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD
W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego za pomocą Jobscience na podstawie użytkownika testowego o nazwie "Britta Simon".

Aby logowanie jednokrotne działało, usługa Azure AD musi wiedzieć, czym jest odpowiedni użytkownik w jobscience dla użytkownika w usłudze Azure AD. Innymi słowy należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Jobscience.

W Jobscience przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość nazwy **użytkownika,** aby ustanowić relację łącza.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą jobscience, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configuring-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)** — do testowania logowania jednokrotnego usługi Azure AD za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Jobscience](#creating-a-jobscience-test-user)** — mieć odpowiednik Britta Simon w Jobscience, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)** — aby umożliwić Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure portal i konfigurujesz logowanie jednokrotne w aplikacji Jobscience.

**Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą narzędzia Jobscience, wykonaj następujące kroki:**

1. W witrynie Azure portal na stronie integracji aplikacji **Jobscience** kliknij pozycję **Logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. W oknie dialogowym **logowania jednokrotnego** wybierz **opcję Tryb** jako **logowanie oparte na SAML,** aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. W sekcji **Domena zadań i adresy URL** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    W polu **tekstowym Logowania adres URL** wpisz adres URL przy użyciu następującego wzorca:`http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Pobierz tę wartość przez [zespół pomocy technicznej klienta Jobscience](http://www.jobscience.com/support) lub z profilu jednookiego użytkownika, który zostanie utworzony, co zostało wyjaśnione w dalszej części samouczka. 
 
1. W sekcji **Saml Podpisywanie certyfikatu** kliknij pozycję **Certyfikat (Base64),** a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/jobscience-tutorial/tutorial_general_400.png)

1. W sekcji **Konfiguracja określania zadań** kliknij pozycję **Konfiguruj narzędzie Jobscience,** aby otworzyć okno **Konfigurowanie logowania.** Skopiuj **adres URL wylogowania, identyfikator jednostki SAML i adres URL usługi logowania jednokrotnego SAML** z **sekcji Podręczna dokumentacja.**

    ![Konfigurowanie logowania jednokrotnego](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Zaloguj się do witryny firmy Jobscience jako administrator.

1. Przejdź do **strony Instalator**.
   
   ![Konfiguracja](./media/jobscience-tutorial/IC784358.png "Setup")

1. W lewym okienku nawigacji w sekcji **Administrowanie** kliknij pozycję **Zarządzanie domenami,** aby rozwinąć powiązaną sekcję, a następnie kliknij pozycję **Moja domena,** aby otworzyć stronę **Moja domena.** 
   
   ![Moja domena](./media/jobscience-tutorial/ic767825.png "Moja domena")

1. Aby sprawdzić, czy twoja domena została poprawnie skonfigurowana, upewnij się, że znajduje się w "**Kroku 4 Wdrożone dla użytkowników**" i przejrzyj swoje " Moje ustawienia**domeny**".

    ![Domena wdrożona dla użytkownika](./media/jobscience-tutorial/ic784377.png "Domena wdrożona dla użytkownika")

1. W witrynie firmy Jobscience kliknij pozycję **Kontrola zabezpieczeń**, a następnie kliknij pozycję **Ustawienia logowania jednokrotnego**.
    
    ![Mechanizmy zabezpieczeń](./media/jobscience-tutorial/ic784364.png "Mechanizmy zabezpieczeń")

1. W sekcji **Ustawienia logowania jednokrotnego** wykonaj następujące czynności:
    
    ![Ustawienia logowania jednokrotnego](./media/jobscience-tutorial/ic781026.png "Ustawienia logowania jednokrotnego")
    
    a. Wybierz pozycję **SAML Enabled** (SAML włączone).

    b. Kliknij **pozycję Nowy**.

1. W oknie dialogowym **Edycji logowania jednokrotnego SAML** wykonaj następujące czynności:
    
    ![Ustawienie logowania jednokrotnego SAML](./media/jobscience-tutorial/ic784365.png "Ustawienie logowania jednokrotnego SAML")
    
    a. W polu tekstowym **Name** (Nazwa) wpisz nazwę konfiguracji.

    b. W polu tekstowym **wystawcy** wklej wartość **identyfikatora jednostki SAML,** który został skopiowany z witryny Azure portal.

    d. W polach tekstowych **Identyfikator jednostki** wpisz`https://salesforce-jobscience.com`

    d. Kliknij **przycisk Przeglądaj,** aby przekazać certyfikat usługi Azure AD.

    e. Jako **typ tożsamości SAML**wybierz **asercja zawiera identyfikator federacji z obiektu User**.

    f. Jako **lokalizacja tożsamości SAML,** wybierz **tożsamość jest w NameIdentfier element instrukcji Podmiot**.

    g. W polu tekstowym **adresu URL logowania dostawcy tożsamości** wklej wartość adresu URL usługi logowania **jednokrotnego SAML,** który został skopiowany z witryny Azure portal.

    h. W polu tekstowym **adresu URL wylogowania dostawcy tożsamości** wklej wartość adresu URL **wylogowania,** który został skopiowany z witryny Azure portal.

    i. Kliknij pozycję **Zapisz**.

1. W lewym okienku nawigacji w sekcji **Administrowanie** kliknij pozycję **Zarządzanie domenami,** aby rozwinąć powiązaną sekcję, a następnie kliknij pozycję **Moja domena,** aby otworzyć stronę **Moja domena.** 
    
    ![Moja domena](./media/jobscience-tutorial/ic767825.png "Moja domena")

1. Na stronie **Moja domena** w sekcji **Znakowanie strony logowania** kliknij pozycję **Edytuj**.
    
    ![Znakowanie strony logowania](./media/jobscience-tutorial/ic767826.png "Znakowanie strony logowania")

1. Na stronie **Znakowanie strony logowania** w sekcji **Usługa uwierzytelniania** wyświetlana jest nazwa ustawień logowania **SAML.** Zaznacz go, a następnie kliknij przycisk **Zapisz**.
    
    ![Znakowanie strony logowania](./media/jobscience-tutorial/ic784366.png "Znakowanie strony logowania")

1. Aby uzyskać zainicjowany przez sp adres URL logowania logowania, kliknij **ustawienia logowania jednokrotnego** w sekcji menu **Kontrole zabezpieczeń.**

    ![Mechanizmy zabezpieczeń](./media/jobscience-tutorial/ic784368.png "Mechanizmy zabezpieczeń")
    
    Kliknij profil jedno i przesuńszą się utworzony w powyższym kroku. Na tej stronie jest wyświetlany adres URL logowania `https://companyname.my.salesforce.com?so=companyid`jednokrotnego w firmie (na przykład .    

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji ** Konfiguracja** w dolnej części strony. Więcej informacji na temat osadzonej dokumentacji można znaleźć tutaj: [Dokumentacja osadzona usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Tworzenie użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące kroki:**

1. W **witrynie Azure portal**w lewym okienku nawigacji kliknij ikonę **usługi Azure Active Directory.**

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **pozycję Użytkownicy i grupy** oraz kliknij pozycję Wszyscy **użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/create_aaduser_02.png) 

1. Aby otworzyć okno dialogowe **Użytkownik,** kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/create_aaduser_03.png) 

1. Na stronie Okno dialogowe **Użytkownik** wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. W polach tekstowych **Nazwa** wpisz **BrittaSimon**.

    b. W polach tekstowych **Nazwa użytkownika** wpisz **adres e-mail** brittaSimon.

    d. Wybierz **pozycję Pokaż hasło** i zapisz wartość **hasła**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-jobscience-test-user"></a>Tworzenie użytkownika testowego Jobscience

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Jobscience, muszą być aprowizacji do Jobscience. W przypadku Jobscience inicjowania obsługi administracyjnej jest zadanie ręczne.

>[!NOTE]
>Do aprowizowania kont użytkowników usługi Azure Active Directory można użyć innych narzędzi do tworzenia kont użytkowników witryny Jobscience lub interfejsów API udostępnianych przez jobscience.
>  
        
**Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy **Jobscience** jako administrator.

1. Przejdź do instalatora.
   
   ![Konfiguracja](./media/jobscience-tutorial/ic784358.png "Setup")
1. Przejdź do **zarządzania użytkownikami \> **.
   
   ![Użytkownicy](./media/jobscience-tutorial/ic784369.png "Użytkownicy")
1. Kliknij pozycję **New User** (Nowy użytkownik).
   
   ![Wszyscy użytkownicy](./media/jobscience-tutorial/ic784370.png "Wszyscy użytkownicy")
1. W oknie dialogowym **Edytowanie użytkownika** wykonaj następujące czynności:
   
   ![Edycja użytkownika](./media/jobscience-tutorial/ic784371.png "Edycja użytkownika")
   
   a. W polu tekstowym **Imię** wpisz imię użytkownika, takiego jak Britta.
   
   b. W polu **tekstowym Nazwisko** wpisz nazwisko użytkownika, takiego jak Simon.
   
   d. W polu tekstowym **Alias** wpisz nazwę aliasu użytkownika, taką jak brittas.

   d. W polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail użytkownika, np. Brittasimon@contoso.com.

   e. W polu tekstowym **Nazwa użytkownika** wpisz nazwę Brittasimon@contoso.comużytkownika, taką jak .

   f. W polu **tekstowym Nazwa nicku** wpisz nazwę nicku użytkownika takiego jak Simon.

   g. Kliknij pozycję **Zapisz**.

    
> [!NOTE]
> Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim stanie się ono aktywne.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Jobscience.

![Przypisywanie użytkownika][200] 

**Aby przypisać Britta Simon do Jobscience, wykonaj następujące czynności:**

1. W witrynie Azure portal otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacji przedsiębiorstwa,** a następnie kliknij pozycję **Wszystkie aplikacje**.

    ![Przypisywanie użytkownika][201] 

1. Na liście aplikacji wybierz pozycję **Jobscience**.

    ![Konfigurowanie logowania jednokrotnego](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. W menu po lewej stronie kliknij pozycję **Użytkownicy i grupy**.

    ![Przypisywanie użytkownika][202] 

1. Kliknij przycisk **Add** (Dodaj). Następnie wybierz **pozycję Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Przypisywanie użytkownika][203]

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **Wybierz** w oknie dialogowym **Użytkownicy i grupy.**

1. Kliknij przycisk **Przypisz** w oknie dialogowym **Dodawanie przydziału.**
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Jobscience w Panelu dostępu należy automatycznie zalogować się do aplikacji Jobscience.
Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

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

