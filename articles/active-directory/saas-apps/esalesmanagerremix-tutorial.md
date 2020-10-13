---
title: 'Samouczek: integracja Azure Active Directory z programem E Sales Manager — Remix | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i E Sales Manager Remix.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: efcf634c9f34fd2b479140af800bb650f66bd821
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826741"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Integracja Azure Active Directory z programem E Sales Manager Remix

W tym samouczku dowiesz się, jak zintegrować usługę Azure Active Directory (Azure AD) z programem E Sales Manager Remix.

Integracja usługi Azure AD z usługą E Sales Manager Remix zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do programu E Sales Manager Remix.
- Możesz umożliwić użytkownikom automatyczne logowanie do usługi E Sales Manager remix (Logowanie jednokrotne, lub Logowanie jednokrotne) przy użyciu swoich kont w usłudze Azure AD.
- Możesz zarządzać kontami z jednej centralnej lokalizacji — witryny Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z programem E Sales Manager remix, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Subskrypcja usługi E Sales Manager Remix z obsługą logowania jednokrotnego

> [!NOTE]
> Podczas testowania kroków opisanych w tym samouczku zalecamy, aby *nie* używać środowiska produkcyjnego.

Aby przetestować kroki w tym samouczku, musisz mieć dostęp do następujących elementów:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska usługi Azure AD w wersji próbnej, możesz skorzystać z [miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku przetestujesz Logowanie jednokrotne w usłudze Azure AD w środowisku testowym. 

Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

* Dodawanie Remix z programu E Sales Manager z galerii
* Konfigurowanie i testowanie logowania jednokrotnego w usłudze Azure AD

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>Dodaj Remix z galerii sprzedaży
Aby skonfigurować integrację usługi Azure AD z programem E Sales Manager remix, Dodaj polecenie E Sales Manager Remix z galerii do listy zarządzanych aplikacji SaaS, wykonując następujące czynności:

1. W [witrynie Azure Portal](https://portal.azure.com) w okienku po lewej stronie wybierz pozycję **Azure Active Directory**. 

    ![Przycisk Azure Active Directory][1]

1. Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.

    ![Okno "aplikacje przedsiębiorstwa"][2]
    
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **e Sales Manager Remix**, wybierz pozycję **e Sales Manager Remix** na liście wyników, a następnie wybierz pozycję **Dodaj**.

    ![E Sales Manager Remix na liście wyników](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego w usłudze Azure AD za pomocą usługi E Sales Manager Remix na podstawie użytkownika testowego o nazwie "Britta Simon".

Aby logowanie jednokrotne działało w usłudze, usługa Azure AD musi identyfikować użytkownika Remixego Menedżera sprzedaży i jego odpowiednika w usłudze Azure AD. Innymi słowy, należy ustanowić relację linku między użytkownikiem usługi Azure AD i tym samym użytkownikiem w programie E Sales Manager Remix.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi E Sales Manager remix, Wypełnij bloki konstrukcyjne w kolejnych pięciu sekcjach:

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Włącz logowanie jednokrotne usługi Azure AD w Azure Portal i skonfiguruj Logowanie jednokrotne w aplikacji Remix w programie online Sales Manager, wykonując następujące czynności:

1. W Azure Portal na stronie integracji aplikacji **Remix w programie online Sales Manager** wybierz pozycję **Logowanie jednokrotne**.

    ![Link "Logowanie jednokrotne"][4]

1. W oknie **Logowanie** jednokrotne w **trybie logowania** jednokrotnego wybierz pozycję **Logowanie oparte na protokole SAML**.
 
    ![Okno "Logowanie jednokrotne"](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. W obszarze **E Sales Manager Remix domenę i adresy URL**wykonaj następujące czynności:

    ![E Sales Manager Remix informacje logowania jednokrotnego w domenie i adresach URL](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. W polu **adres URL logowania** wpisz adres URL w następującym formacie: *https:// \<Server-Based-URL> / \<sub-domain> /eSales-PC*.

    b. W polu **Identyfikator** wpisz adres URL w następującym formacie: *https:// \<Server-Based-URL> / \<sub-domain> / *.

    c. Zanotuj wartość **identyfikatora** do późniejszego użycia w tym samouczku.
    
    > [!NOTE] 
    > Podane wyżej wartości nie są rzeczywiste. Zaktualizuj je za pomocą rzeczywistego adresu URL logowania i identyfikatora. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej Remix Sales Manager](mailto:esupport@softbrain.co.jp).

1. W obszarze **certyfikat podpisywania SAML**wybierz pozycję **certyfikat (base64)**, a następnie Zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu (base64)](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. Zaznacz pole wyboru **Wyświetl i edytuj wszystkie inne atrybuty użytkownika** , a następnie wybierz atrybut **EmailAddress** .
    
    ![Okno atrybutów użytkownika](./media/esalesmanagerremix-tutorial/configure1.png)

    Zostanie otwarte okno **Edytowanie atrybutu** .

1. Skopiuj wartości **przestrzeni nazw** i **nazwy** . Wygeneruj wartość we wzorcu *\<Namespace>/\<Name>* i Zapisz ją do późniejszego użycia w tym samouczku.

    ![Edytowanie okna atrybutów](./media/esalesmanagerremix-tutorial/configure2.png)

1. W obszarze **E Sales Manager Remix Configuration (Konfiguracja**) wybierz pozycję **Konfiguruj E Sales Manager Remix**.

    ![Zrzut ekranu przedstawiający sekcję "Konfiguracja Remixa Menedżera sprzedaży" z wybraną pozycją "Konfiguruj E Sales Manager Remix".](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    Zostanie otwarte okno **Konfigurowanie logowania** .

1. W sekcji **krótkie odwołanie** Skopiuj adres URL wylogowania i adres URL usługi logowania jednokrotnego protokołu SAML.

1. Wybierz pozycję **Zapisz**.

    ![Przycisk Zapisz](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. Zaloguj się do aplikacji Remix Manager w programie online, jako administrator.

1. W prawym górnym rogu wybierz pozycję **menu administratora**.

    ![Polecenie "do menu administratora"](./media/esalesmanagerremix-tutorial/configure4.png)

1. W okienku po lewej stronie wybierz pozycję **Ustawienia systemowe**  >  **współpraca z systemem zewnętrznym**.

    ![Linki "Ustawienia systemowe" i "Współpraca z systemem zewnętrznym"](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. W oknie **współpraca z systemem zewnętrznym** wybierz pozycję **SAML**.

    ![Okno "Współpraca z systemem zewnętrznym"](./media/esalesmanagerremix-tutorial/configure6.png)

1. W obszarze **Ustawienia uwierzytelniania SAML**wykonaj następujące czynności:

    ![Sekcja "ustawienie uwierzytelniania SAML"](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Zaznacz pole wyboru **wersja komputera** .
    
    b. Z listy rozwijanej w sekcji **element współpracy** wybierz pozycję **poczta e-mail**.

    c. W polu **element współpracy** wklej wartość pola, która została skopiowana wcześniej z Azure Portal (czyli **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** ).

    d. W polu **wystawca (identyfikator jednostki)** wklej wartość identyfikatora, która została skopiowana wcześniej z sekcji **Remix i adresy URL programu E Sales Manager** w Azure Portal.

    e. Aby przekazać pobrany certyfikat z Azure Portal, wybierz opcję **Wybór pliku**.

    f. W polu **adres URL logowania dostawcy** , wklej adres URL usługi logowania jednokrotnego protokołu SAML, który został skopiowany wcześniej w Azure Portal.

    przykład W polu **adres URL wylogowania dostawcy tożsamości** wklej wartość adresu URL wylogowania, która została skopiowana wcześniej w Azure Portal.

    h. Wybierz pozycję **ustawienie ukończone**.

> [!TIP]
> Podczas konfigurowania aplikacji można odczytać zwięzłą wersję powyższych instrukcji przedstawionych w [Azure Portal](https://portal.azure.com). Po dodaniu aplikacji w sekcji **Active Directory**  >  **aplikacje dla przedsiębiorstw** wybierz kartę **Logowanie** jednokrotne, a następnie dostęp do osadzonej dokumentacji w sekcji **Konfiguracja** u dołu. Aby uzyskać więcej informacji na temat funkcji osadzonej dokumentacji, zobacz [dokumentację usługi Azure AD Embedded]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego Britta Simon w Azure Portal, wykonując następujące czynności:

![Tworzenie użytkownika testowego usługi Azure AD][100]

1. W Azure Portal w lewym okienku wybierz pozycję **Azure Active Directory**.

    ![Link Azure Active Directory](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę bieżących użytkowników, wybierz pozycję **Użytkownicy i grupy**  >  **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. W górnej części okna **Wszyscy użytkownicy** wybierz pozycję **Dodaj**.

    ![Przycisk Dodaj](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    Zostanie otwarte okno **użytkownika** .

1. W oknie **użytkownika** wykonaj następujące czynności:

    ![Okno użytkownika](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. W polu **Nazwa** wpisz **BrittaSimon**.

    b. W polu **Nazwa użytkownika** wpisz adres E-mail użytkownika Britta Simon.

    c. Zaznacz pole wyboru **Pokaż hasło** , a następnie zanotuj wartość wyświetlaną w polu **hasło** .

    d. Wybierz przycisk **Utwórz**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Utwórz użytkownika testowego programu E Sales Manager Remix

1. Zaloguj się do swojej aplikacji w programie E Sales Manager Remix jako administrator.

1. Wybierz polecenie **do menu administratora** z menu w prawym górnym rogu.

    ![Remix konfiguracja programu E Sales Manager](./media/esalesmanagerremix-tutorial/configure4.png)

1. Wybierz **Ustawienia firmy**  >  **konserwacja działów i pracowników**, a następnie wybierz pozycję **pracownicy zarejestrowani**.

    ![Karta "pracownicy zarejestrowani"](./media/esalesmanagerremix-tutorial/user1.png)

1. W sekcji **Rejestracja nowego pracownika** wykonaj następujące czynności:
    
    ![Sekcja "Nowa rejestracja pracownika"](./media/esalesmanagerremix-tutorial/user2.png)

    a. W polu **Nazwa pracownika** wpisz nazwę użytkownika (na przykład **Britta**).

    b. Wypełnij pozostałe wymagane pola.
    
    c. Po włączeniu protokołu SAML administrator nie może zalogować się na stronie logowania. Przyznaj użytkownikowi uprawnienia do logowania administratora, zaznaczając pole wyboru **Logowanie administratora** .

    d. Wybierz pozycję **rejestracja**.

1. W przyszłości, aby zalogować się jako administrator, zaloguj się jako użytkownik z uprawnieniami administratora, a następnie w prawym górnym rogu wybierz pozycję **menu Administrator**.

    ![Polecenie "do menu administratora"](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączasz Britta użytkownika do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi E Sales Manager Remix. Aby to zrobić, wykonaj następujące czynności: 

![Przypisywanie roli użytkownika][200] 

1. W Azure Portal Otwórz widok **aplikacje** , przejdź do widoku **katalogu** , a następnie wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.

    ![Linki "aplikacje dla przedsiębiorstw" i "wszystkie aplikacje"][201] 

1. Na liście **aplikacje** wybierz pozycję **E Sales Manager Remix**.

    ![Link Remix programu E Sales Manager](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. W lewym okienku wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”][202]

1. Wybierz pozycję **Dodaj** , a następnie w okienku **Dodaj przypisanie** wybierz pozycję **Użytkownicy i grupy**.

    ![Okienko Dodawanie przypisania][203]

1. W oknie **Użytkownicy i grupy** na liście **Użytkownicy** wybierz pozycję **Britta Simon**.

1. Wybierz przycisk **Wybierz**.

1. W oknie **Dodawanie przypisania** wybierz pozycję **Przypisz**.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka Remix E Sales Manager w panelu dostępu użytkownik powinien być zalogowany automatycznie do aplikacji Remix Manager.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/esalesmanagerremix-tutorial/tutorial_general_203.png

