---
title: 'Samouczek: integracja Azure Active Directory z roztocznym połączeniem | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i roztoczą.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/31/2020
ms.author: jeedes
ms.openlocfilehash: b89ae74a1f4ead568a5454c9494d4a82b0eeea3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015183"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect-or-cloudlink-platform"></a>Samouczek: integracja Azure Active Directory z roztocznym połączeniem MiCloud lub platformą CloudLink

W ramach tego samouczka nauczysz się używać aplikacji do nawiązywania połączenia w celu zintegrowania Azure Active Directory (Azure AD) z platformą MiCloud Connect lub CloudLink. Aplikacja ze zgięciem jest dostępna w galerii platformy Azure. Integracja usługi Azure AD z platformą MiCloud Connect lub CloudLink zapewnia następujące korzyści:

* Można kontrolować dostęp użytkowników do usługi MiCloud Connect Apps i CloudLink aplikacje w usłudze Azure AD przy użyciu poświadczeń przedsiębiorstwa.
* Możesz umożliwić użytkownikom na Twoim koncie automatyczne logowanie do usługi MiCloud Connect lub CloudLink (Logowanie jednokrotne) przy użyciu swoich kont usługi Azure AD.

Aby uzyskać szczegółowe informacje na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

Jeśli nie masz subskrypcji platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem integracji usługi Azure AD z roztoczną platformą MiCloud Connect lub CloudLink.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z programem MiCloud Connect, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Roztoczne konto MiCloud Connect lub CloudLinke konto, w zależności od aplikacji, którą chcesz skonfigurować.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne w usłudze Azure AD.

* Roztoczne połączenie obsługuje usługę **SP** zainicjowaną przez usługę SSO
* Po skonfigurowaniu roztoczne Połącz można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mitel-connect-from-the-gallery"></a>Dodaj roztoczne połączenie z galerii

Aby skonfigurować integrację nawiązywania połączenia z usługą Azure AD, należy dodać roztoczne połączenie z galerii do listy zarządzanych aplikacji SaaS w Azure Portal.

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Wybierz pozycję **Nowa aplikacja**.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz tekst **ścięcia** , wybierz pozycję **Rozłącz się** z panelem wyników, a następnie wybierz pozycję **Dodaj**.

     ![Roztoczne połączenie na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD przy użyciu platformy MiCloud Connect lub CloudLink na podstawie użytkownika testowego o nazwie **_Britta Simon_**. Aby logowanie jednokrotne działało, należy nawiązać połączenie między użytkownikiem w portalu usługi Azure AD i odpowiednim użytkownikiem na platformie rozłożenia. W poniższych sekcjach znajdują się informacje o konfigurowaniu i testowaniu rejestracji jednokrotnej usługi Azure AD przy użyciu platformy MiCloud Connect lub CloudLink.
* Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD przy użyciu połączenia MiCloud
* Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą platformy CloudLink

## <a name="configure-and-test-azure-ad-sso-with-micloud-connect"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD przy użyciu połączenia MiCloud

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą MiCloud Connect:

1. **[Skonfiguruj MiCloud Connect dla logowania jednokrotnego za pomocą usługi Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)** — aby umożliwić użytkownikom korzystanie z tej funkcji oraz Konfigurowanie ustawień logowania jednokrotnego po stronie aplikacji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
3. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
4. **[Utwórz test "Roztoczne MiCloud](#create-a-mitel-micloud-connect-test-user)** ", aby uzyskać odpowiednik Britta Simon na koncie MiCloud, które jest połączone z reprezentacją usługi Azure AD.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Konfigurowanie MiCloud Connect dla logowania jednokrotnego za pomocą usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD dla MiCloud Connect w Azure Portal i skonfiguruj konto połączenia MiCloud, aby zezwolić na logowanie jednokrotne przy użyciu usługi Azure AD.

Aby skonfigurować MiCloud łączenie z logowaniem jednokrotnym w usłudze Azure AD, najłatwiej otworzyć Azure Portal i roztoczny Portal konta. Należy skopiować niektóre informacje z Azure Portal do portalu konta roztocznego, a część z portalu konta roztocznego do Azure Portal.


1. Aby otworzyć stronę konfiguracji w [Azure Portal](https://portal.azure.com/):

    1. Na stronie **Rozwiąż połączenie z** integracją aplikacji wybierz pozycję **Logowanie jednokrotne**.

       ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

    1. W oknie dialogowym **Wybierz metodę logowania** jednokrotnego wybierz pozycję **SAML**.
    
       ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)
    
       Zostanie wyświetlona strona logowania oparta na protokole SAML.

2. Aby otworzyć okno dialogowe konfiguracji w portalu konta roztocznego:

    1. W menu **system telefoniczny** wybierz pozycję **funkcje dodatków**.

    1. Z prawej strony **logowania jednokrotnego** wybierz pozycję **Aktywuj** lub **Ustawienia**.
    
    Zostanie wyświetlone okno dialogowe Połącz pojedyncze Sign-On ustawienia.
    
3. Zaznacz pole wyboru **Włącz logowanie jednokrotne** .
    
    ![Zrzut ekranu, na którym jest wyświetlana strona Ustawienia w obszarze Rozłącz pojedyncze Sign-On, z zaznaczonym polem wyboru Włącz pojedynczy Sign-On.](./media/mitel-connect-tutorial/mitel-connect-enable.png)

4. W Azure Portal wybierz ikonę **Edytuj** w sekcji **podstawowe konfiguracje języka SAML** .
   
    ![Zrzut ekranu przedstawia stronę Konfigurowanie pojedynczego Sign-On ze stroną SAML z wybraną ikoną Edytuj.](common/edit-urls.png)

    Zostanie wyświetlone okno dialogowe Podstawowa konfiguracja protokołu SAML.

5.  Skopiuj adres URL z pola **Identyfikator Roztoczny (identyfikator jednostki)** w portalu konta roztocznego i wklej go do pola **Identyfikator (identyfikator jednostki)** w Azure Portal.

6. Skopiuj adres URL z pola **adres URL odpowiedzi (adres URL usługi konsumenckej odbiorcy)** w portalu roztocza konta i wklej go do pola **adres URL odpowiedzi (adres URL usługi konsumenckej odbiorcy)** w Azure Portal.

   ![Zrzut ekranu przedstawia podstawową konfigurację języka SAML w sekcji Azure Portal i Konfigurowanie dostawcy tożsamości w portalu roztocznego konta z wierszami wskazującymi relację między nimi.](./media/mitel-connect-tutorial/mitel-azure-basic-configuration.png)

7. W polu tekstowym **adres URL logowania** wpisz jeden z następujących adresów URL:

    1. **https://portal.shoretelsky.com** — Aby użyć portalu konta roztocznego jako domyślnej aplikacji pod kątem rozłożenia
    1. **https://teamwork.shoretel.com** -Aby użyć zespołowej jako domyślnej aplikacji pod kątem rozłożenia

    > [!NOTE]
    > Domyślna aplikacja pod kątem aplikacji jest dostępna, gdy użytkownik wybierze kafelek roztoczne połączenie w panelu dostępu. Jest to również aplikacja, do której można uzyskać dostęp podczas przeprowadzania konfiguracji testowej z usługi Azure AD.

8. Wybierz pozycję **Zapisz** w oknie dialogowym **Podstawowa konfiguracja SAML** w Azure Portal.

9. W sekcji **certyfikat podpisywania SAML** na stronie **logowania opartej na protokole SAML** w Azure Portal wybierz pozycję **Pobierz** obok pozycji **certyfikat (base64)** , aby pobrać **certyfikat podpisywania** i zapisać go na komputerze.

    ![Zrzut ekranu przedstawia okienko certyfikatu podpisywania SAML, w którym można pobrać certyfikat.](./media/mitel-connect-tutorial/azure-signing-certificate.png)

10. Otwórz plik certyfikatu podpisywania w edytorze tekstów, skopiuj wszystkie dane z pliku, a następnie wklej dane w polu **certyfikat podpisywania** w portalu konta roztocznego. 

      ![Zrzut ekranu przedstawia pole certyfikatu podpisywania.](./media/mitel-connect-tutorial/mitel-connect-signing-certificate.png)

11. W sekcji **Ustawienia Roztoczne połączenie** na stronie **logowania opartej** na protokole SAML Azure Portal:

     1. Skopiuj adres URL z pola **adres URL logowania** i wklej go w polu **adres URL** logowania w portalu rozłożenia konta.

     1. Skopiuj adres URL z pola **Identyfikator usługi Azure AD** i wklej go do pola **Identyfikator jednostki** w portalu roztocznego konta.
         
         ![Zrzut ekranu przedstawia relację między stroną logowania opartą na protokole SAML Azure Portal i portalem konta rozłożenia.](./media/mitel-connect-tutorial/mitel-azure-set-up-connect.png)

12. Wybierz pozycję **Zapisz** w oknie dialogowym **Połącz pojedyncze Sign-On ustawienia** w portalu konta roztocznego.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w Azure Portal.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. W oknie dialogowym właściwości użytkownika wykonaj następujące czynności:

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wpisz brittasimon@ \<yourcompanydomain\> . \<extension\> .  Na przykład BrittaSimon@contoso.com.

    1. Zaznacz pole wyboru **Pokaż hasło** , a następnie Zapisz wartość, która jest wyświetlana w polu **hasło** .

    1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do rozłożenia.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Rozłącz się**.

    ![Link roztoczne połączenie na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście **Użytkownicy** , a następnie wybierz **pozycję zaznacz** w dolnej części ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, wybierz odpowiednią rolę dla użytkownika z listy w oknie dialogowym **Wybierz rolę** , a następnie wybierz **opcję Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Tworzenie MiCloudego użytkownika testowego połączenia

W tej sekcji utworzysz użytkownika o nazwie Britta Simon na koncie MiCloud Connect. Przed skorzystaniem z logowania jednokrotnego należy utworzyć i aktywować użytkowników.

Aby uzyskać szczegółowe informacje o dodawaniu użytkowników w portalu konta rozbudowanego, zapoznaj się z artykułem [Dodawanie użytkownika](https://shoretelcommunity.force.com/s/article/Adding-Users-092815) w bazie wiedzy o zawieszeniu.

Utwórz użytkownika na koncie MiCloud Connect z następującymi szczegółami:

* **Nazwa:** Britta Simon
* **Służbowy adres e-mail:**`brittasimon@<yourcompanydomain>.<extension>`   
  (Przykład: [brittasimon@contoso.com](mailto:brittasimon@contoso.com) )
* **Nazwa użytkownika:**`brittasimon@<yourcompanydomain>.<extension>`  
  (Przykład: [brittasimon@contoso.com](mailto:brittasimon@contoso.com) ; Nazwa użytkownika jest zwykle taka sama jak w przypadku firmowego adresu e-mail użytkownika.

> [!NOTE]
> Nazwa użytkownika MiCloud kontaktu musi być taka sama jak adres e-mail użytkownika na platformie Azure.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka ze zgięciem Połącz w panelu dostępu należy automatycznie przekierować do logowania się do aplikacji MiCloud Connect skonfigurowanej jako wartość domyślna w polu **adres URL logowania** . Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="configure-and-test-azure-ad-sso-with-cloudlink-platform"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą platformy CloudLink

W tej sekcji opisano, jak włączyć usługę Azure AD SSO dla platformy CloudLink w Azure Portal i jak skonfigurować konto platformy CloudLink, aby umożliwić Logowanie jednokrotne przy użyciu usługi Azure AD.

Aby skonfigurować platformę CloudLink z logowaniem jednokrotnym w usłudze Azure AD, zaleca się otwarcie Azure Portal i portalu kont CloudLink obok siebie, ponieważ trzeba będzie skopiować niektóre informacje z Azure Portal do portalu konta CloudLink i na odwrót.

1. Aby otworzyć stronę konfiguracji w [Azure Portal](https://portal.azure.com/):

    1. Na stronie **Rozwiąż połączenie z** integracją aplikacji wybierz pozycję **Logowanie jednokrotne**.

       ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

    1. W oknie dialogowym **Wybierz metodę logowania** jednokrotnego wybierz pozycję **SAML**.

       ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)
    
       Zostanie otwarta strona **logowania oparta na protokole SAML** , w której jest wyświetlana sekcja **Podstawowa konfiguracja języka SAML** .

       ![Zrzut ekranu przedstawia stronę logowania opartą na protokole SAML z podstawową konfiguracją SAML.](./media/mitel-connect-tutorial/mitel-azure-saml-settings.png)

2. Aby uzyskać dostęp do panelu konfiguracji logowania jednokrotnego usługi **Azure AD** w portalu konta CloudLink:

    1. Przejdź do strony **Informacje o koncie** konta klienta, na którym chcesz włączyć integrację.

    1. W sekcji **integracje** wybierz pozycję **+ Dodaj nowe**. Na ekranie podręcznym zostanie wyświetlony panel **integracji** .

    1. Wybierz kartę **inne firmy** . Zostanie wyświetlona lista obsługiwanych aplikacji innych firm. Wybierz przycisk **Dodaj** skojarzony z **logowaniem jednokrotnym w usłudze Azure AD**, a następnie wybierz pozycję **gotowe**.

       ![Zrzut ekranu przedstawia stronę integracji, na której można dodać Logowanie jednokrotne w usłudze Azure A D.](./media/mitel-connect-tutorial/mitel-cloudlink-integrations.png)

       Rejestracja jednokrotna **usługi Azure AD** jest włączona dla konta klienta i zostanie dodana do sekcji **Integrations** na stronie **Informacje o koncie** .   

   1. Wybierz pozycję **Ukończ instalację**.
    
      ![Zrzut ekranu przedstawia pełną opcję instalacji usługi Azure A D Logowanie jednokrotne.](./media/mitel-connect-tutorial/mitel-cloudlink-complete-setup.png)
      
      Zostanie otwarty panel konfiguracja logowania jednokrotnego **usługi Azure AD** .
      
       ![Zrzut ekranu przedstawia jednoSign-Onową konfigurację usługi Azure A D.](./media/mitel-connect-tutorial/mitel-cloudlink-sso-setup.png)
       
       Należy zastanowić się, że nie jest zaznaczone pole wyboru **Włącz poświadczenia ścięcia (opcjonalnie)** w sekcji **opcjonalne poświadczenia z zatoczeniem** . Zaznacz to pole wyboru tylko wtedy, gdy chcesz, aby użytkownik zalogować się do aplikacji CloudLink przy użyciu poświadczeń pod kątem jednolitego logowania.

3. W Azure Portal na stronie **logowania opartej na protokole SAML** wybierz ikonę **Edytuj** w sekcji **Podstawowa konfiguracja protokołu SAML** . Zostanie otwarty panel **podstawowe Konfigurowanie protokołu SAML** .

    ![Zrzut ekranu przedstawia okienko podstawowe konfiguracje języka SAML z wybraną ikoną Edytuj.](./media/mitel-connect-tutorial/mitel-azure-saml-basic.png)
 
 4. Skopiuj adres URL z pola **Identyfikator Roztoczny (identyfikator jednostki)** w portalu konta CloudLink i wklej go do pola **Identyfikator (identyfikator jednostki)** w Azure Portal.

 5. Skopiuj adres URL z pola **adres URL odpowiedzi (adres URL usługi konsumenckej odbiorcy)** w portalu konta CloudLink i wklej go do pola **adres URL odpowiedzi (adres URL usługi konsumenckej odbiorcy)** w Azure Portal.  
    
    ![Zrzut ekranu przedstawia relację między stronami w portalu kont CloudLink i Azure Portal.](./media/mitel-connect-tutorial/mitel-cloudlink-saml-mapping.png) 

 6. W polu tekstowym **adres URL logowania** wpisz adres URL, `https://accounts.mitel.io` Aby użyć portalu kont CloudLink jako domyślnej aplikacji pod kątem rozłożenia.
     
     ![Zrzut ekranu przedstawia pole tekstowe logowanie U R L.](./media/mitel-connect-tutorial/mitel-cloudlink-sign-on-url.png)
  
     > [!NOTE]
     > Domyślna aplikacja pod kątem aplikacji jest otwierana, gdy użytkownik wybierze kafelek roztoczne połączenie w panelu dostępu. Jest to również aplikacja, do której można uzyskać dostęp, gdy użytkownik skonfiguruje konfigurację testową z usługi Azure AD.

7. W oknie dialogowym **Podstawowa konfiguracja SAML** wybierz pozycję **Zapisz** .

8. W sekcji **certyfikat podpisywania SAML** na stronie **logowania opartej na protokole SAML** w Azure Portal wybierz pozycję **Pobierz** obok **certyfikatu (base64)** , aby pobrać **certyfikat podpisywania**. Zapisz certyfikat na komputerze.
  
    ![Zrzut ekranu przedstawia sekcję certyfikat podpisywania SAML, w której można pobrać certyfikat Base64.](./media/mitel-connect-tutorial/mitel-cloudlink-save-certificate.png)

9. Otwórz plik certyfikatu podpisywania w edytorze tekstów, skopiuj wszystkie dane z pliku, a następnie wklej dane do pola **certyfikat podpisywania** w portalu konta CloudLink.  

    > [!NOTE]
    > Jeśli masz więcej niż jeden certyfikat, zalecamy wklejenie ich jeden po drugim. 
       
    ![Zrzut ekranu przedstawia krok dwie procedury, w której można wypełniać wartości z integracji z platformą Azure A D.](./media/mitel-connect-tutorial/mitel-cloudlink-enter-certificate.png)

10. W sekcji **Skonfiguruj nawiązanie połączenia** na stronie **logowania opartej na protokole SAML** Azure Portal:

     1. Skopiuj adres URL z pola **adres URL logowania** i wklej go w polu **adres URL logowania** w portalu konta CloudLink.

     1. Skopiuj adres URL z pola **Identyfikator usługi Azure AD** i wklej go do pola **Identyfikator dostawcy tożsamości (identyfikator jednostki)** w portalu konta CloudLink.
     
        ![Zrzut ekranu przedstawia źródło wartości opisanych w tym miejscu w Mintel Connect.](./media/mitel-connect-tutorial/mitel-cloudlink-copy-settings.png)

11. Wybierz pozycję **Zapisz** w panelu Logowanie jednokrotne **usługi Azure AD** w portalu konta CloudLink.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w Azure Portal.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. W oknie dialogowym właściwości użytkownika wykonaj następujące czynności:

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wpisz brittasimon@ \<yourcompanydomain\> . \<extension\> .  Na przykład BrittaSimon@contoso.com.

    1. Zaznacz pole wyboru **Pokaż hasło** , a następnie Zapisz wartość, która jest wyświetlana w polu **hasło** .

    1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do rozłożenia.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Rozłącz się**.

    ![Link roztoczne połączenie na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście **Użytkownicy** , a następnie wybierz **pozycję zaznacz** w dolnej części ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, wybierz odpowiednią rolę dla użytkownika z listy w oknie dialogowym **Wybierz rolę** , a następnie wybierz **opcję Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-cloudlink-test-user"></a>Tworzenie użytkownika testowego CloudLink

W tej sekcji opisano sposób tworzenia użytkownika testowego o nazwie **_Britta Simon_** na platformie CloudLink. Aby można było korzystać z logowania jednokrotnego, użytkownicy muszą być utworzeni i aktywowani.

Aby uzyskać szczegółowe informacje na temat dodawania użytkowników w portalu konta usługi CloudLink, zobacz **_Zarządzanie użytkownikami_** w [dokumentacji kont CloudLink](https://www.mitel.com/document-center/technology/cloudlink/all-releases/en/cloudlink-accounts-html).

Utwórz użytkownika w portalu kont CloudLink z następującymi szczegółami:

* Nazwa: Britta Simon
* Imię: Britta
* Nazwisko: Simon
* Poczta e-mail: BrittaSimon@contoso.com

> [!NOTE]
> Adres e-mail CloudLink użytkownika musi być taki sam jak **główna nazwa użytkownika** w Azure Portal.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przedstawiono Testowanie konfiguracji logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka ze zgięciem Połącz w panelu dostępu nastąpi automatyczne przekierowanie w celu zalogowania się do aplikacji CloudLink, która została skonfigurowana jako domyślna w polu **adres URL logowania** . Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)