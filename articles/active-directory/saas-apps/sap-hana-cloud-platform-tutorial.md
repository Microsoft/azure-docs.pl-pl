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
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: b15c5a9f9f1e4e144caa2ddaa36d42a2a225b31b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97964052"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Samouczek: integracja Azure Active Directory z platformą SAP w chmurze

W ramach tego samouczka dowiesz się, jak zintegrować platformę SAP w chmurze z usługą Azure Active Directory (Azure AD). Po zintegrowaniu platformy SAP Cloud Platform z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do platformy SAP Cloud Platform.
* Zezwól użytkownikom na automatyczne logowanie do platformy SAP Cloud Platform przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z platformą SAP Cloud Platform, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja platformy SAP Cloud Platform z obsługą logowania jednokrotnego

Po ukończeniu tego samouczka użytkownicy usługi Azure AD przypisani do platformy SAP Cloud Platform będą mogli logować się do aplikacji za pomocą funkcji logowania jednokrotnego, korzystając z informacji zawartych w sekcji [Wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

>[!IMPORTANT]
>Aby przetestować logowanie jednokrotne, należy wdrożyć własną aplikację lub zasubskrybować aplikację w ramach własnego konta platformy SAP Cloud Platform. W tym samouczku aplikacja jest wdrożona w ramach konta.
> 

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Platforma SAP Cloud Platform obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Dodawanie platformy SAP Cloud Platform z galerii

Aby skonfigurować integrację platformy SAP Cloud Platform z usługą Azure AD, należy dodać tę platformę z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SAP Cloud Platform** w polu wyszukiwania.
1. Wybierz pozycję **SAP Cloud Platform** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla platformy SAP Cloud Platform

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą platformy SAP Cloud Platform przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem na platformie SAP Cloud Platform.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą platformy SAP Cloud Platform, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj usługę SAP Cloud Platform SSO](#configure-sap-cloud-platform-sso)** — aby skonfigurować ustawienia pojedynczej Sign-On po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego platformy SAP Cloud Platform](#create-sap-cloud-platform-test-user)** — aby udostępnić na platformie SAP Cloud Platform odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **SAP Cloud Platform** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

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

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do platformy SAP Cloud Platform.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **SAP Cloud Platform**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-sap-cloud-platform-sso"></a>Konfigurowanie logowania jednokrotnego dla platformy SAP Cloud

1. W innym oknie przeglądarki internetowej zaloguj się do panelu sterowania platformy SAP Cloud Platform pod adresem `https://account.<landscape host>.ondemand.com/cockpit`(na przykład: https://account.hanatrial.ondemand.com/cockpit).

2. Kliknij kartę **Trust** (Relacja zaufania).
   
    ![Zaufanie](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Zaufanie")

3. W sekcji Trust Management (Zarządzanie relacjami zaufania) w obszarze **Local Service Provider** (Lokalny dostawca usług), wykonaj następujące kroki:

    ![Zrzut ekranu przedstawiający sekcję "Zarządzanie zaufaniem" z wybraną kartą "dostawca usług lokalnych" i wszystkie pola tekstowe wyróżnione.](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Zarządzanie zaufaniem")
   
    a. Kliknij pozycję **Edytuj**.

    b. W polu **Configuration Type** (Typ konfiguracji) wybierz pozycję **Custom** (Niestandardowa).

    c. W polu **Local Provider Name** (Nazwa dostawcy lokalnego) pozostaw wartość domyślną. Skopiuj tę wartość i wklej ją w polu **Identifier** (Identyfikator) w konfiguracji usługi Azure AD dla platformy SAP Cloud Platform.

    d. Aby wygenerować parę kluczy w polach **Signing Key** (Klucz podpisywania) i **Signing Certificate** (Certyfikat podpisywania), kliknij przycisk **Generate Key Pair** (Generuj parę kluczy).

    e. Ustaw pole **Principal Propagation** (Propagacja jednostki) na wartość **Disabled** (Wyłączona).

    f. Ustaw pole **Force Authentication** (Wymuś uwierzytelnianie) na wartość **Disabled** (Wyłączone).

    przykład Kliknij pozycję **Zapisz**.

4. Po zapisaniu ustawień w sekcji **Local Service Provider** (Lokalny dostawca usług) wykonaj następujące kroki, aby uzyskać adres URL odpowiedzi:
   
    ![Pobierz metadane](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Pobierz metadane")

    a. Pobierz plik metadanych platformy SAP Cloud Platform, klikając pozycję **Get Metadata** (Pobierz metadane).

    b. Otwórz pobrany plik XML z metadanymi platformy SAP Cloud Platform, a następnie znajdź tag **ns3:AssertionConsumerService**.
 
    c. Skopiuj wartość atrybutu **Location**, a następnie wklej ją w polu **Adres URL odpowiedzi** w ramach konfiguracji usługi Azure AD dla platformy SAP Cloud Platform.

5. Kliknij kartę **Trusted Identity Provider** (Zaufany dostawca tożsamości), a następnie kliknij pozycję **Add Trusted Identity Provider** (Dodaj zaufanego dostawcę tożsamości).
   
    ![Zrzut ekranu przedstawiający stronę "Zarządzanie zaufaniem" z wybraną kartą "zaufany dostawca tożsamości".](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Zarządzanie zaufaniem")
   
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

    e. Kliknij pozycję **Zapisz**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do platformy SAP Cloud Platform, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania platformy SAP Cloud Platform, a następnie zainicjuj w tym miejscu przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka platforma SAP Cloud Platform w obszarze Moje aplikacje należy automatycznie zalogować się do platformy SAP Cloud Platform, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu platformy SAP Cloud Platform można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).