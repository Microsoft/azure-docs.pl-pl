---
title: 'Samouczek: integracja Azure Active Directory z oprogramowaniem SAP Business ByDesign | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i SAP Business ByDesign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 6959b7e819ad906f04a18661d6bd4aa2d82f8984
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88543381"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Samouczek: integracja usługi Azure Active Directory z oprogramowaniem SAP Business ByDesign

W tym samouczku dowiesz się, jak zintegrować oprogramowanie SAP Business ByDesign z usługą Azure Active Directory (Azure AD).
Integracja oprogramowania SAP Business ByDesign z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do SAP Business ByDesign.
* Możesz umożliwić użytkownikom automatyczne logowanie do oprogramowania SAP Business ByDesign (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z oprogramowaniem SAP Business ByDesign, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączonym logowaniem jednokrotnym w oprogramowaniu SAP Business ByDesign

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Oprogramowanie SAP Business ByDesign obsługuje zainicjowanie rejestracji jednokrotnej **SP**

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Dodawanie SAP Business ByDesign z galerii

Aby skonfigurować integrację SAP Business ByDesign w usłudze Azure AD, musisz dodać SAP Business ByDesign z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać SAP Business ByDesign z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **SAP Business ByDesign**, wybierz pozycję **SAP Business ByDesign** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![SAP Business ByDesign na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD przy użyciu oprogramowania SAP Business ByDesign na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w oprogramowaniu SAP Business ByDesign.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu oprogramowania SAP Business ByDesign, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego SAP Business ByDesign](#configure-sap-business-bydesign-single-sign-on)** — aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego SAP Business ByDesign](#create-sap-business-bydesign-test-user)** , aby dysponować odpowiednikiem Britta Simon w oprogramowaniu SAP Business ByDesign, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD przy użyciu oprogramowania SAP Business ByDesign, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **SAP Business ByDesign** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o rejestracji jednokrotnej w programie SAP Business ByDesign i adresach URL](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<servername>.sapbydesign.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej SAP Business ByDesign Client](https://www.sap.com/products/cloud-analytics.support.html) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Aplikacja SAP Business ByDesign oczekuje potwierdzeń SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image (obraz)](common/edit-attribute.png)

6. Kliknij ikonę **Edytuj** , aby edytować **wartość identyfikatora nazwy**.

    ![image (obraz)](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. W sekcji **Zarządzanie oświadczeniami użytkowników** wykonaj następujące czynności:  ![ obraz](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Wybierz **transformację** jako **Źródło**.

    b. Na liście rozwijanej **transformacja** wybierz pozycję **ExtractMailPrefix ()**.

    c. Z listy rozwijanej **parametr 1** Wybierz atrybut użytkownika, którego chcesz użyć dla implementacji. Na przykład jeśli chcesz użyć identyfikatora EmployeeID jako unikatowego identyfikatora użytkownika, a wartość atrybutu jest przechowywana w elemencie ExtensionAttribute2, wybierz pozycję user.extensionattribute2.

    d. Kliknij przycisk **Zapisz**.

8. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

9. W sekcji **Konfigurowanie programu SAP Business ByDesign** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-sap-business-bydesign-single-sign-on"></a>Konfigurowanie jednego Sign-On SAP Business ByDesign

1. Zaloguj się do portalu SAP Business ByDesign z uprawnieniami administratora.

2. Przejdź do **typowego zadania zarządzania aplikacjami i użytkownikami** , a następnie kliknij kartę **dostawca tożsamości** .

3. Kliknij pozycję **Nowy dostawca tożsamości** i wybierz plik XML metadanych pobrany z Azure Portal. Przez zaimportowanie tych metadanych system automatycznie przekaże wymagany certyfikat podpisywania i certyfikat szyfrowania.

    ![Konfigurowanie logowania jednokrotnego](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Aby dołączyć **adres URL usługi konsumenckej potwierdzenia** do żądania SAML, wybierz pozycję **Dołącz adres URL usługi potwierdzenia odbiorcy**.

5. Kliknij przycisk **Activate Single Sign-On** (Aktywuj logowanie jednokrotne).

6. Zapisz zmiany.

7. Kliknij kartę **My System** (Mój system).

    ![Konfigurowanie logowania jednokrotnego](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. W polu tekstowym **adres URL logowania do usługi Azure AD** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    ![Konfigurowanie logowania jednokrotnego](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Określ, czy pracownik może ręcznie wybrać opcję logowania przy użyciu identyfikatora użytkownika i hasła lub logowania jednokrotnego, wybierając pozycję **Ręczne wybieranie dostawcy tożsamości**.

10. W sekcji **adres URL logowania jednokrotnego** Określ adres URL, który powinien być używany przez pracownika do jednokrotnego do systemu.
    Z listy rozwijanej adres URL wysyłany do pracownika możesz wybrać jedną z następujących opcji:

    **Non-SSO URL** (Adres URL w przypadku braku obsługi logowania jednokrotnego)

    System wysyła do pracownika tylko normalny adres URL systemu. Pracownik nie może jednokrotnego za pomocą logowania jednokrotnego i należy zamiast tego użyć hasła lub certyfikatu.

    **SSO URL** (Adres URL logowania jednokrotnego)

    System wysyła do pracownika tylko adres URL logowania jednokrotnego. Pracownik może jednokrotnego za pomocą logowania jednokrotnego. Żądanie uwierzytelnienia zostanie przekierowane za pośrednictwem dostawcy tożsamości.

    **Automatic Selection** (Wybór automatyczny)

    Jeśli logowanie jednokrotne nie jest aktywne, system wysyła do pracownika normalny adres URL systemu. Jeśli logowanie jednokrotne jest aktywne, system sprawdza, czy pracownik ma hasło. Jeśli hasło jest dostępne, do pracownika wysyłany jest zarówno adres URL logowania jednokrotnego, jak i adres URL w przypadku braku obsługi logowania jednokrotnego. Jeśli jednak pracownik nie ma hasła, do pracownika wysyłany jest tylko adres URL logowania jednokrotnego.

11. Zapisz zmiany.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz `brittasimon@yourcompanydomain.extension` . Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Ta sekcja umożliwia włączenie usługi Britta Simon w celu korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do oprogramowania SAP Business ByDesign.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **SAP Business ByDesign**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **SAP Business ByDesign**.

    ![Link SAP Business ByDesign na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-sap-business-bydesign-test-user"></a>Utwórz użytkownika testowego SAP Business ByDesign

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w oprogramowaniu SAP Business ByDesign. Aby dodać użytkowników z platformy SAP Business ByDesign, skontaktuj się z [zespołem obsługi klienta SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) . 

> [!NOTE]
> Upewnij się, że wartość NameID powinna być zgodna z polem username na platformie SAP Business ByDesign platform.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SAP Business ByDesign w panelu dostępu należy automatycznie zalogować się do rozwiązania SAP Business ByDesign, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
