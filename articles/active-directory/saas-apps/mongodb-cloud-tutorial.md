---
title: 'Samouczek: Azure Active Directory logowania jednokrotnego z usługą MongoDB Cloud | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedyncze między usługą Azure Active Directory a usługą MongoDB Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2021
ms.author: jeedes
ms.openlocfilehash: 5904d3eeec3f5880213f8a8c6a41cefbe76801b3
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520092"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego z usługą MongoDB Cloud

Z tego samouczka dowiesz się, jak zintegrować usługę MongoDB Cloud z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi MongoDB Cloud z usługą Azure AD można:

* Kontroluj w usłudze Azure AD, kto ma dostęp do chmury MongoDB, usługi MongoDB Atlas, społeczności bazy danych MongoDB, uniwersytetu MongoDB i pomocy technicznej bazy danych MongoDB.
* Umożliwianie użytkownikom automatycznego zalogowania się do usługi MongoDB Cloud przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji: Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja aplikacji MongoDB Cloud z obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujemy logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa MongoDB Cloud obsługuje logowanie **jednokrotne** **inicjowane przez** dostawców usług oraz dostawców tożsamości.
* Usługa MongoDB Cloud obsługuje **aprowizowanie** użytkowników just in time.

## <a name="add-mongodb-cloud-from-the-gallery"></a>Dodawanie usługi MongoDB Cloud z galerii

Aby skonfigurować integrację usługi MongoDB Cloud z usługą Azure AD, musisz dodać usługę MongoDB Cloud z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do konta Azure Portal przy użyciu konta służbowego lub osobistego konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W sekcji **Dodaj z galerii** wpisz **MongoDB Cloud** w polu wyszukiwania.
1. Wybierz **pozycję MongoDB Cloud** z panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-mongodb-cloud"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi MongoDB Cloud

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z usługą MongoDB Cloud przy użyciu użytkownika testowego **O nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem usługi MongoDB Cloud.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z usługą MongoDB Cloud, wykonaj następujące kroki:

1. [Skonfiguruj logowanie jednokrotne usługi Azure AD,](#configure-azure-ad-sso) aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. [Tworzenie użytkownika testowego usługi Azure AD w](#create-an-azure-ad-test-user) celu przetestowania logowania pojedynczego usługi Azure AD z użytkownikiem B.Simon.
    1. [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. [Konfigurowanie logowania jednokrotnego w usłudze MongoDB Cloud SSO](#configure-mongodb-cloud-sso) w celu skonfigurowania ustawień logowania jednokrotnego po stronie aplikacji.
    1. [Tworzenie użytkownika testowego usługi MongoDB w](#create-a-mongodb-cloud-test-user) chmurze, aby mieć w usłudze MongoDB Cloud odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. [Przetestuj logowanie jednokrotne,](#test-sso) aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Na stronie [Azure Portal](https://portal.azure.com/)aplikacji **MongoDB Cloud** znajdź **sekcję** Zarządzanie. Wybierz **pozycję Logowanie pojedyncze.**
1. Na stronie **Select a single sign-on method (Wybieranie metody logowania pojedynczego)** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie aplikacji Sign-On saml** wybierz ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Zrzut ekranu przedstawiający konfigurowanie aplikacji Sign-On za pomocą strony SAML z wyróżniona ikoną ołówka](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym  przez dostawcy tożsamości, w sekcji Podstawowa konfiguracja protokołu **SAML** wprowadź wartości następujących pól:

    a. W polu **tekstowym** Identyfikator wpisz adres URL, który używa następującego wzorca: `https://www.okta.com/saml2/service-provider/<Customer_Unique>`

    b. W polu **tekstowym Adres URL** odpowiedzi wpisz adres URL, który używa następującego wzorca: `https://auth.mongodb.com/sso/saml2/<Customer_Unique>`

1. Wybierz **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie **inicjowanym przez** spjęcie:

    W **polu tekstowym Adres URL** logowania wpisz adres URL, który używa następującego wzorca:  `https://cloud.mongodb.com/sso/<Customer_Unique>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z zespołem [pomocy technicznej klienta usługi MongoDB Cloud](https://support.mongodb.com/). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja MongoDB Cloud oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![Zrzut ekranu przedstawiający atrybuty domyślne](common/default-attributes.png)

1. Oprócz powyższych atrybutów aplikacja MongoDB Cloud oczekuje jeszcze kilku atrybutów, które zostaną przekazane w odpowiedzi SAML. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć według wymagań.
    
    | Nazwa | Atrybut źródłowy|
    | ---------------| --------- |
    | poczta e-mail | user.userprincipalname |
    | firstName | user.givenname |
    | lastName | user.surname |

1. Na stronie Konfigurowanie logowania pojedynczego za pomocą języka **SAML** w sekcji Certyfikat podpisywania **SAML** znajdź plik **XML metadanych federacji**. Wybierz **pozycję** Pobierz, aby pobrać certyfikat i zapisać go na komputerze.

    ![Zrzut ekranu przedstawiający sekcję Certyfikat podpisywania SAML z wyróżnionym linkiem Pobierz](common/metadataxml.png)

1. W **sekcji Konfigurowanie usługi MongoDB Cloud** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Zrzut ekranu przedstawiający sekcję Konfigurowanie chmury usługi Mongo DB z wyróżnieniami adresów URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w aplikacji Azure Portal o nazwie B.Simon.

1. W okienku po lewej stronie w Azure Portal wybierz pozycję **Azure Active Directory**  >  **Wszyscy**  >  **użytkownicy.**
1. Wybierz **pozycję Nowy** użytkownik w górnej części ekranu.
1. We **właściwościach** Użytkownik wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **polu Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole **wyboru Pokaż hasło,** a następnie zapisz hasło.
   1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do usługi MongoDB Cloud.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **MongoDB Cloud.**
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy** w **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie ustawiono żadnej roli, zostanie wybrana rola "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-mongodb-cloud-sso"></a>Konfigurowanie logowania jednokrotnego w chmurze usługi MongoDB

Aby skonfigurować logowanie pojedyncze po stronie usługi MongoDB Cloud, potrzebne są odpowiednie adresy URL skopiowane z Azure Portal. Należy również skonfigurować aplikację federacyjną dla organizacji mongoDB w chmurze. Postępuj zgodnie z instrukcjami w [dokumentacji usługi MongoDB Cloud.](https://docs.atlas.mongodb.com/security/federated-auth-azure-ad/) Jeśli masz problem, skontaktuj się z zespołem pomocy [technicznej usługi MongoDB Cloud.](https://support.mongodb.com/)

### <a name="create-a-mongodb-cloud-test-user"></a>Tworzenie użytkownika testowego usługi MongoDB Cloud

Usługa MongoDB Cloud obsługuje aprowizowanie użytkowników just in time, które jest domyślnie włączone. Nie ma żadnych dodatkowych czynności do podjęcia. Jeśli użytkownik jeszcze nie istnieje w chmurze MongoDB, zostanie utworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Inicjowane przez sp:

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do chmury bazy danych MongoDB, pod którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania do chmury usługi MongoDB i zainicjuj przepływ logowania.

#### <a name="idp-initiated"></a>Inicjowane przez IDP:

* Kliknij pozycję **Test this application** in Azure Portal and you should be automatically signed in to the MongoDB Cloud which you set up the SSO (Przetestuj tę aplikację w usłudze Azure Portal i powinno nas na przykład automatycznie zalogować się do chmury bazy danych MongoDB, dla której została skonfigurowania logowania jednokrotnego. 

Możesz również użyć usługi Microsoft Moje aplikacje, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka MongoDB Cloud w usłudze Moje aplikacje, jeśli zostanie skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania. Jeśli jest on skonfigurowany w trybie dostawcy tożsamości, powinno na celu automatyczne zalogowanie się do chmury mongoDB, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Moje aplikacje, [zobacz Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu chmury Bazy danych MongoDB można wymusić kontrolę sesji, która chroni eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji wykracza poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
