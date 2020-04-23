---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Mitel Connect | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą Mitel Connect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: 26a761708f56ff7aba8daf86d2991579e60291cb
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870191"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>Samouczek: Integracja usługi Azure Active Directory z usługą Mitel MiCloud Connect

W tym samouczku dowiesz się, jak zintegrować mitel MiCloud Connect z usługą Azure Active Directory (Azure AD). Integracja usługi MiCloud Connect z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do aplikacji MiCloud Connect przy użyciu poświadczeń przedsiębiorstwa.
* Możesz umożliwić użytkownikom na swoim koncie automatyczne logowanie się do usługi MiCloud Connect (logowanie jednokrotne) za pomocą swoich kont usługi Azure AD.


Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą MiCloud Connect, potrzebne są następujące elementy:

* Subskrypcji usługi Azure AD

  Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Konto Mitel MiCloud Connect

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednookrotne usługi Azure AD.In this tutorial, you'll configure and test Azure AD single sign-on (SSO).

* Mitel Connect obsługuje sso inicjowane przez **SP**

## <a name="adding-mitel-connect-from-the-gallery"></a>Dodawanie Mitel Connect z galerii

Aby skonfigurować integrację aplikacji Mitel Connect z usługą Azure AD, należy dodać usługę Mitel Connect z galerii do listy zarządzanych aplikacji SaaS w witrynie Azure portal.

**Aby dodać mitel Connect z galerii, wykonaj następujące czynności:**

1. W **[witrynie Azure portal](https://portal.azure.com)** po lewej stronie panelu nawigacji kliknij pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Kliknij **pozycję Aplikacje przedsiębiorstwa,** a następnie kliknij pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Kliknij **pozycję Nowa aplikacja**.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. Wpisz **Mitel Connect** w polu wyszukiwania, kliknij pozycję **Mitel Connect** z panelu wyników, a następnie kliknij przycisk **Dodaj**.

     ![Mitel Connect na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD za pomocą usługi MiCloud Connect na podstawie użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze MiCloud Connect.

Aby skonfigurować i przetestować logowanie jednookrotne usługi Azure AD za pomocą usługi MiCloud Connect, należy wykonać następujące kroki:

1. **[Skonfiguruj aplikację Logowania micloud connect dla usługi Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)** — aby umożliwić użytkownikom korzystanie z tej funkcji i skonfigurowanie ustawień logowania jednorazowego po stronie aplikacji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
3. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
4. **[Utwórz użytkownika testowego Mitel MiCloud Connect](#create-a-mitel-micloud-connect-test-user)** — aby mieć odpowiednik Britta Simon na swoim koncie MiCloud Connect, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Konfigurowanie usługi MiCloud Connect dla usługi SSO za pomocą usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD dla usługi MiCloud Connect w witrynie Azure portal i skonfigurujesz konto MiCloud Connect, aby zezwolić na logowanie jednokrotne przy użyciu usługi Azure AD.

Aby skonfigurować micloud connect z przyłączem SSO dla usługi Azure AD, najłatwiej jest otworzyć witrynę Azure portal i portal konta Mitel obok siebie. Musisz skopiować niektóre informacje z witryny Azure portal do portalu konta Mitel, a niektóre z portalu konta Mitel do witryny Azure portal.


1. Aby otworzyć stronę konfiguracji w witrynie [Azure portal,](https://portal.azure.com/)wykonaj następujące czynności:

    a. Na stronie integracji aplikacji **Mitel Connect** kliknij pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

    b. W oknie **dialogowym Wybieranie metody logowania jednokrotnego** kliknij przycisk **SAML**.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)
    
    Zostanie wyświetlona strona logowania oparta na saml.

2. Aby otworzyć okno dialogowe konfiguracji w portalu Konta Mitel, wykonaj następujące czynności:

    a. W menu **System telefoniczny** kliknij polecenie **Funkcje dodatkowe**.

    b. Po prawej stronie **logowania jednokrotnego**kliknij pozycję **Aktywuj** lub **Ustawienia**.
    
    Zostanie wyświetlone okno dialogowe Połącz ustawienia logowania jednokrotnego.
    
3. Zaznacz pole wyboru **Włącz logowanie jednokrotne.**
    ![obraz](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. W witrynie Azure portal kliknij ikonę **Edytuj** w sekcji **Podstawowa konfiguracja SAML.**
    ![obraz](common/edit-urls.png)

    Zostanie wyświetlone okno dialogowe Podstawowa konfiguracja SAML.

5.  Skopiuj adres URL z pola **Identyfikator Mitel (identyfikator jednostki)** w portalu Konta Mitel i wklej go do pola **Identyfikator (identyfikator jednostki)** w witrynie Azure portal.

6. Skopiuj adres URL z pola **Adres URL odpowiedzi (adres URL usługi konsumenta potwierdzenia)** w portalu Konta Mitel i wklej go do pola **Adres URL odpowiedzi (adres URL usługi konsumenta potwierdzenia)** w witrynie Azure portal.  
   ![obraz](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. W polu tekstowym **Podpisz adres URL** wpisz jeden z następujących adresów URL:

    * **https://portal.shoretelsky.com**- aby korzystać z portalu Konta Mitel jako domyślnej aplikacji Mitel
    * **https://teamwork.shoretel.com**- aby używać pracy zespołowej jako domyślnej aplikacji Mitel

    **UWAGA:** Domyślną aplikacją Mitel jest aplikacja, do która uzyskuje dostęp, gdy użytkownik kliknie kafelek Mitel Connect w Panelu dostępu. Jest to również aplikacja dostępna podczas wykonywania konfiguracji testu z usługi Azure AD.

8. Kliknij **pozycję Zapisz** w oknie dialogowym **Podstawowa konfiguracja SAML** w witrynie Azure portal.

9. W sekcji **Certyfikat podpisywania SAML** na stronie **logowania opartego na SAML** w witrynie Azure portal kliknij pozycję **Pobierz** obok pozycji **Certyfikat (Base64),** aby pobrać **certyfikat podpisywania** i zapisać go na komputerze.
    ![obraz](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. Otwórz plik certyfikatu podpisywania w edytorze tekstu, skopiuj wszystkie dane w pliku, a następnie wklej je w polu **Podpisywanie certyfikatu** w portalu Konta Mitel. 
    ![obraz](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. W sekcji **Setup Mitel Connect** na stronie **logowania opartego na SAML w witrynie** Azure portal wykonaj następujące czynności:

    a. Skopiuj adres URL z pola **Adres URL logowania** i **wklej** go do pola Adresu URL logowania w portalu konta Mitel.

    b. Skopiuj adres URL z pola **Identyfikator usługi Azure AD** i wklej go do pola Identyfikator **jednostki** w portalu Konta Mitel.
    ![obraz](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. Kliknij pozycję **Zapisz** w oknie dialogowym **Połącz ustawienia logowania jednokrotnego** w portalu konta Mitel.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure portal.

1. W witrynie Azure portal w lewym okienku kliknij pozycję **Usługa Azure Active Directory**, kliknij pozycję **Użytkownicy**, a następnie kliknij pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Kliknij **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. W oknie dialogowym Właściwości użytkownika wykonaj następujące czynności:

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wpisz **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz\<brittasimon@ swoją domenę.\> \<rozszerzenie\>.  
Na przykład BrittaSimon@contoso.com.

    d. Zaznacz pole wyboru **Pokaż hasło,** a następnie zapisz wartość wyświetlaną w polu **Hasło.**

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Mitel Connect.

1. W portalu Azure kliknij pozycję **Aplikacje przedsiębiorstwa**, a następnie kliknij pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji kliknij przycisk **Mitel Connect**.

    ![Łącze Mitel Connect na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie kliknij pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij **pozycję Dodaj użytkownika**, a następnie kliknij pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście **Użytkownicy,** a następnie kliknij pozycję **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, wybierz odpowiednią rolę dla użytkownika z listy w oknie dialogowym **Wybierz rolę,** a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przydziału** kliknij pozycję **Przypisz**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Tworzenie użytkownika testowego Mitel MiCloud Connect

W tej sekcji utworzysz użytkownika o nazwie Britta Simon na swoim koncie MiCloud Connect. Użytkownicy muszą być tworzone i aktywowane przed użyciem logowania jednokrotnego.

Szczegółowe informacje na temat dodawania użytkowników do portalu konta Mitel można znaleźć w artykule [Dodawanie użytkownika](https://oneview.mitel.com/s/article/Adding-a-User-092815) w bazie wiedzy Mitel.

Utwórz użytkownika na swoim koncie MiCloud Connect z następującymi szczegółami:

  * **Nazwa:** Britta Simon

* **Firmowy adres e-mail:**`brittasimon@<yourcompanydomain>.<extension>`   
(Przykład: [brittasimon@contoso.com](mailto:brittasimon@contoso.com))

* **Nazwa użytkownika:**`brittasimon@<yourcompanydomain>.<extension>`  
(Przykład: [brittasimon@contoso.com](mailto:brittasimon@contoso.com); nazwa użytkownika jest zazwyczaj taka sama jak firmowy adres e-mail użytkownika)

**UWAGA:** Nazwa użytkownika MiCloud Connect musi być identyczna z adresem e-mail użytkownika na platformie Azure.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu Panelu dostępu.

Po kliknięciu kafelka Mitel Connect w Panelu dostępu należy automatycznie przekierować, aby zalogować się do aplikacji MiCloud Connect skonfigurowany jako domyślny w polu **Zaloguj się na adres URL.** Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
