---
title: 'Samouczek: integracja Azure Active Directory z platformą SAP w chmurze | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i platformą SAP Cloud Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.openlocfilehash: c2738e1a6168440adee79ebaa599a313600153a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546767"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Samouczek: integracja Azure Active Directory z platformą SAP w chmurze

Z tego samouczka dowiesz się, jak zintegrować platformę SAP Cloud Platform z usługą Azure Active Directory (Azure AD).
Zintegrowanie platformy SAP Cloud Platform z usługą Azure AD daje następujące korzyści:

* Możliwość kontrolowania dostępu do platformy SAP Cloud Platform za pomocą usługi Azure AD.
* Umożliwianie użytkownikom automatycznego logowania się do platformy SAP Cloud Platform (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z platformą SAP Cloud Platform, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja platformy SAP Cloud Platform z obsługą logowania jednokrotnego

Po ukończeniu tego samouczka użytkownicy usługi Azure AD przypisani do platformy SAP Cloud Platform będą mogli logować się do aplikacji za pomocą funkcji logowania jednokrotnego, korzystając z informacji zawartych w sekcji [Wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>Aby przetestować logowanie jednokrotne, należy wdrożyć własną aplikację lub zasubskrybować aplikację w ramach własnego konta platformy SAP Cloud Platform. W tym samouczku aplikacja jest wdrożona w ramach konta.
> 

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Platforma SAP Cloud Platform obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Dodawanie platformy SAP Cloud Platform z galerii

Aby skonfigurować integrację platformy SAP Cloud Platform z usługą Azure AD, należy dodać tę platformę z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać platformę SAP Cloud Platform z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **SAP Cloud Platform**, wybierz pozycję **SAP Cloud Platform** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Platforma SAP Cloud Platform na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z platformą SAP Cloud Platform, korzystając z danych użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD a powiązanym użytkownikiem platformy SAP Cloud Platform.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z platformą SAP Cloud Platform, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego platformy SAP Cloud Platform](#configure-sap-cloud-platform-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego platformy SAP Cloud Platform](#create-sap-cloud-platform-test-user)** — aby udostępnić na platformie SAP Cloud Platform odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD na platformie SAP Cloud Platform, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **SAP Cloud Platform** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL logowania jednokrotnego platformy SAP Cloud Platform](common/sp-identifier-reply.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL używany przez użytkowników do logowania się do aplikacji **SAP Cloud Platform**. To jest adres URL chronionego zasobu w aplikacji SAP Cloud Platform specyficzny dla konta. Adres URL jest zgodny z następującym wzorcem: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
    >[!NOTE]
    >To jest adres URL w aplikacji SAP Cloud Platform, która wymaga uwierzytelnienia użytkownika.
    > 

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`

    b. W polu tekstowym **Identyfikator** podaj adres URL platformy SAP Cloud Platform, korzystając z jednego z następujących wzorców: 

    - `https://hanatrial.ondemand.com/<instancename>`
    - `https://hana.ondemand.com/<instancename>`
    - `https://us1.hana.ondemand.com/<instancename>`
    - `https://ap1.hana.ondemand.com/<instancename>`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta platformy SAP Cloud Platform](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html), aby pobrać adres URL logowania i identyfikator. Adres URL odpowiedzi można uzyskać w sekcji zarządzania relacjami zaufania, co zostało wyjaśnione w dalszej części tego samouczka.
    > 
4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

### <a name="configure-sap-cloud-platform-single-sign-on"></a>Konfigurowanie logowania jednokrotnego na platformie SAP Cloud Platform

1. W innym oknie przeglądarki internetowej zaloguj się do panelu sterowania platformy SAP Cloud Platform pod adresem `https://account.<landscape host>.ondemand.com/cockpit`(na przykład: https://account.hanatrial.ondemand.com/cockpit).

2. Kliknij kartę **Trust** (Relacja zaufania).
   
    ![Zaufanie](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Zaufanie")

3. W sekcji Trust Management (Zarządzanie relacjami zaufania) w obszarze **Local Service Provider** (Lokalny dostawca usług), wykonaj następujące kroki:

    ![Zarządzanie zaufaniem](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Zarządzanie zaufaniem")
   
    a. Kliknij pozycję **Edytuj**.

    b. W polu **Configuration Type** (Typ konfiguracji) wybierz pozycję **Custom** (Niestandardowa).

    c. W polu **Local Provider Name** (Nazwa dostawcy lokalnego) pozostaw wartość domyślną. Skopiuj tę wartość i wklej ją w polu **Identifier** (Identyfikator) w konfiguracji usługi Azure AD dla platformy SAP Cloud Platform.

    d. Aby wygenerować parę kluczy w polach **Signing Key** (Klucz podpisywania) i **Signing Certificate** (Certyfikat podpisywania), kliknij przycisk **Generate Key Pair** (Generuj parę kluczy).

    e. Ustaw pole **Principal Propagation** (Propagacja jednostki) na wartość **Disabled** (Wyłączona).

    f. Ustaw pole **Force Authentication** (Wymuś uwierzytelnianie) na wartość **Disabled** (Wyłączone).

    przykład Kliknij przycisk **Zapisz**.

4. Po zapisaniu ustawień w sekcji **Local Service Provider** (Lokalny dostawca usług) wykonaj następujące kroki, aby uzyskać adres URL odpowiedzi:
   
    ![Pobierz metadane](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Pobierz metadane")

    a. Pobierz plik metadanych platformy SAP Cloud Platform, klikając pozycję **Get Metadata** (Pobierz metadane).

    b. Otwórz pobrany plik XML z metadanymi platformy SAP Cloud Platform, a następnie znajdź tag **ns3:AssertionConsumerService**.
 
    c. Skopiuj wartość atrybutu **Location**, a następnie wklej ją w polu **Adres URL odpowiedzi** w ramach konfiguracji usługi Azure AD dla platformy SAP Cloud Platform.

5. Kliknij kartę **Trusted Identity Provider** (Zaufany dostawca tożsamości), a następnie kliknij pozycję **Add Trusted Identity Provider** (Dodaj zaufanego dostawcę tożsamości).
   
    ![Zarządzanie zaufaniem](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Zarządzanie zaufaniem")
   
    >[!NOTE]
    >Aby zarządzać listą zaufanych dostawców tożsamości, należy wybrać niestandardowy typ konfiguracji w sekcji Local Service Provider (Lokalny dostawca usług). W przypadku domyślnego typu konfiguracji relacja zaufania z usługą identyfikatora SAP jest nieedytowalna i niejawna. W przypadku braku konfiguracji nie istnieją żadne ustawienia relacji zaufania.
    > 
    > 

6. Kliknij kartę **General** (Ogólne), a następnie kliknij pozycję **Browse** (Przeglądaj), aby przekazać pobrany plik metadanych.
    
    ![Zarządzanie zaufaniem](./media/sap-hana-cloud-platform-tutorial/ic793932.png "Zarządzanie zaufaniem")
    
    >[!NOTE]
    >Po przekazaniu pliku metadanych wartości pól **Single Sign-on URL** (Adres URL logowania jednokrotnego), **Single Logout URL** (Adres URL wylogowywania jednokrotnego) i **Signing Certificate** (Certyfikat podpisywania) zostaną wypełnione automatycznie.
    > 
     
7. Kliknij kartę **Atrybuty**.

8. Na karcie **Attributes** (Atrybuty) wykonaj następujące kroki:
    
    ![Atrybuty](./media/sap-hana-cloud-platform-tutorial/ic790804.png "Atrybuty") 

    a. Kliknij pozycję **Add Assertion-Based Attribute** (Dodaj atrybut oparty na asercji), a następnie dodaj następujące atrybuty oparte na asercjach:
       
    | Atrybut asercji | Atrybut jednostki |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |firstname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |lastname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |poczta e-mail |
   
     >[!NOTE]
     >Konfiguracja atrybutów zależy od sposobu tworzenia aplikacji w ramach punktu połączenia usługi, to znaczy od tego, które atrybuty są oczekiwane w odpowiedzi SAML i pod jaką nazwą (atrybut jednostki) uzyskiwany jest dostęp do tego atrybutu w kodzie.
     > 
    
    b. Wartość pola **Default Attribute** (Domyślny atrybut) na zrzucie ekranu służy tylko do celów ilustracyjnych. Nie jest ona wymagana do działania tego scenariusza.  
 
    c. Nazwy i wartości w kolumnie **Principal Attribute** (Atrybut jednostki) na zrzucie ekranu są zależne od sposobu tworzenia aplikacji. Istnieje możliwość, że w Twojej aplikacji wymagane będzie użycie innych mapowań.

### <a name="assertion-based-groups"></a>Grupy oparte na asercjach

Jako opcjonalny krok możesz skonfigurować grupy oparte na asercjach na potrzeby dostawcy tożsamości usługi Azure Active Directory.

Korzystanie z grup na platformie SAP Cloud Platform pozwala na dynamiczne przypisywanie co najmniej jednego użytkownika do co najmniej jednej roli w aplikacjach platformy SAP Cloud Platform na podstawie wartości atrybutów w ramach asercji protokołu SAML 2.0. 

Na przykład jeśli asercja zawiera atrybut „*contract=temporary*”, możesz dodać wszystkich użytkowników, których on dotyczy, do grupy „*TEMPORARY*”. Grupa „*TEMPORARY*” może zawierać co najmniej jedną rolę z co najmniej jednej aplikacji wdrożonej w ramach Twojego konta platformy SAP Cloud Platform.
 
Użyj grup opartych na asercjach, jeśli chcesz jednocześnie przypisać wielu użytkowników do co najmniej jednej roli aplikacji w ramach Twojego konta platformy SAP Cloud Platform. Jeśli chcesz przypisać tylko jednego lub niewielką liczbę użytkowników do określonych ról, zalecamy przypisanie ich bezpośrednio na karcie **Authorizations** (Autoryzacje) z poziomu panelu sterowania platformy SAP Cloud Platform.

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

W tej sekcji udostępnisz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do platformy SAP Cloud Platform.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw** i **Wszystkie aplikacje**, a następnie **SAP Cloud Platform**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz ciąg **SAP Cloud Platform** i wybierz odpowiednią pozycję.

    ![Link platformy SAP Cloud Platform na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-sap-cloud-platform-test-user"></a>Tworzenie użytkownika testowego platformy SAP Cloud Platform

Aby umożliwić użytkownikom usługi Azure AD logowanie się do platformy SAP Cloud Platform, należy przypisać im role na tej platformie.

**Aby przypisać rolę użytkownikowi, wykonaj następujące kroki:**

1. Zaloguj się do panelu sterowania platformy **SAP Cloud Platform**.

2. Wykonaj poniższe kroki:
   
    ![Autoryzacje](./media/sap-hana-cloud-platform-tutorial/ic790805.png "Autoryzacje")
   
    a. Kliknij pozycję **Authorization** (Autoryzacja).

    b. Kliknij kartę **Użytkownicy** .

    c. W polu tekstowym **User** (Użytkownik) wpisz adres e-mail użytkownika.

    d. Kliknij pozycję **Assign** (Przypisz), aby przypisać użytkownika do roli.

    e. Kliknij przycisk **Zapisz**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SAP Cloud Platform w panelu dostępu powinno nastąpić automatyczne zalogowanie do platformy SAP Cloud Platform, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

