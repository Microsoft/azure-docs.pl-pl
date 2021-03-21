---
title: 'Samouczek: integracja Azure Active Directory z usługą HubSpot | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i HubSpot.
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
ms.openlocfilehash: 91dfdcef01a121c8282b8fad2e75f67989b75dc3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98733253"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Samouczek: integracja Azure Active Directory z usługą HubSpot

W tym samouczku dowiesz się, jak zintegrować usługę HubSpot z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi HubSpot z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do HubSpot.
* Zezwól użytkownikom na automatyczne logowanie się do usługi HubSpot przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą HubSpot, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji usługi Azure AD, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
* Subskrypcja HubSpot z włączonym logowaniem jednokrotnym.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku należy skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD w środowisku testowym i zintegrować usługę HubSpot z usługą Azure AD.

Program HubSpot obsługuje następujące funkcje:

* **Logowanie jednokrotne zainicjowane przez program SP**
* **Logowanie jednokrotne inicjowane przez dostawcę tożsamości**

## <a name="adding-hubspot-from-the-gallery"></a>Dodawanie HubSpot z galerii

Aby skonfigurować integrację programu HubSpot z usługą Azure AD, musisz dodać HubSpot z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **HubSpot** w polu wyszukiwania.
1. Wybierz pozycję **HubSpot** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-hubspot"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla HubSpot

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą HubSpot na podstawie użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić połączoną relację między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w HubSpot.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi HubSpot, należy wykonać następujące bloki konstrukcyjne:

| Zadanie | Opis |
| --- | --- |
| **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** | Umożliwia użytkownikom korzystanie z tej funkcji. |
| **[Konfigurowanie logowania jednokrotnego HubSpot](#configure-hubspot-single-sign-on)** | Konfiguruje ustawienia logowania jednokrotnego w aplikacji. |
| **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** | Testuje Logowanie jednokrotne w usłudze Azure AD dla użytkownika o nazwie Britta Simon. |
| **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** | Umożliwia usłudze Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD. |
| **[Tworzenie użytkownika testowego HubSpot](#create-a-hubspot-test-user)** | Tworzy odpowiednik Britta Simon w HubSpot, który jest połączony z reprezentacją usługi Azure AD użytkownika. |
| **[Testowanie logowania jednokrotnego](#test-single-sign-on)** | Sprawdza, czy konfiguracja działa. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

1. W Azure Portal na stronie integracja aplikacji **HubSpot** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W okienku **podstawowe konfiguracje języka SAML** , aby skonfigurować *tryb zainicjowany dostawcy tożsamości*, wykonaj następujące czynności:

    1. W polu **Identyfikator** wprowadź adres URL, który ma następujący wzorzec: https: \/ /API.HubSpot.com/login-API/V1/SAML/login?portalId = \<CUSTOMER ID\> .

    1. W polu **adres URL odpowiedzi** wprowadź adres URL, który ma następujący wzorzec: https: \/ /API.HubSpot.com/login-API/V1/SAML/ACS?portalId = \<CUSTOMER ID\> .

    ![HubSpot domenę i adresy URL Logowanie jednokrotne](common/idp-intiated.png)

    > [!NOTE]
    > Aby sformatować adresy URL, można również odwołać się do wzorców przedstawionych w okienku podstawowe informacje o **konfiguracji SAML** w Azure Portal.

1. Aby skonfigurować aplikację w trybie *inicjowania programu Sp* :

    1. Wybierz pozycję **Ustaw dodatkowe adresy URL**.

    1. W polu **adres URL logowania** wprowadź wartość **https: \/ /App.HubSpot.com/login**.

    ![Opcja Ustaw dodatkowe adresy URL](common/metadata-upload-additional-signon.png)

1. W okienku **Skonfiguruj pojedyncze Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz pozycję **Pobierz** obok pozycji **certyfikat (base64)**. Wybierz opcję pobierania w zależności od wymagań. Zapisz certyfikat na komputerze.

    ![Opcja pobierania certyfikatu (base64)](common/certificatebase64.png)

1. W sekcji **Konfiguracja HubSpot** Skopiuj następujące adresy URL zgodnie z wymaganiami:

    * Adres URL logowania
    * Identyfikator usługi Azure AD
    * Adres URL wylogowywania

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Konfigurowanie logowania jednokrotnego HubSpot

1. Otwórz nową kartę w przeglądarce i zaloguj się na swoim koncie administratora HubSpot.

1. Wybierz ikonę **Ustawienia** w prawym górnym rogu strony.

    ![Ikona Ustawienia w HubSpot](./media/hubspot-tutorial/config1.png)

1. Wybierz pozycję **Ustawienia domyślne konta**.

    ![Opcja ustawienia domyślne konta w HubSpot](./media/hubspot-tutorial/config2.png)

1. Przewiń w dół do sekcji **zabezpieczenia** , a następnie wybierz pozycję **Konfiguruj**.

    ![Opcja konfiguracji w HubSpot](./media/hubspot-tutorial/config3.png)

1. W sekcji **Konfigurowanie logowania jednokrotnego** wykonaj następujące czynności:

    1. W polu **adres URL odbiorców (identyfikator jednostki dostawcy usługi)** wybierz pozycję **Kopiuj** , aby skopiować wartość. W Azure Portal w okienku **podstawowe konfiguracje języka SAML** wklej wartość w polu **Identyfikator** .

    1. W polu **adres URL logowania, ACS, adresat lub przekierowanie** wybierz opcję **Kopiuj** , aby skopiować wartość. W Azure Portal w okienku **podstawowe konfiguracje języka SAML** wklej wartość w polu **adres URL odpowiedzi** .

    1. W HubSpot, w polu **Identyfikator dostawcy tożsamości lub adres URL wystawcy** , wklej wartość **identyfikatora usługi Azure AD** skopiowaną w Azure Portal.

    1. W HubSpot, w polu **adres URL Single Sign-On dostawcy tożsamości** , wklej wartość **adresu URL logowania** skopiowanego w Azure Portal.

    1. W Notatniku systemu Windows Otwórz pobrany plik certyfikatu (base64). Wybierz i skopiuj zawartość pliku. Następnie w HubSpot wklej go w polu **certyfikat X. 509** .

    1. Wybierz pozycję **Weryfikuj**.

        ![Sekcja Konfigurowanie logowania jednokrotnego w HubSpot](./media/hubspot-tutorial/config4.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi HubSpot.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **HubSpot**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-a-hubspot-test-user"></a>Tworzenie użytkownika testowego HubSpot

Aby umożliwić użytkownikom logowanie się do usługi Azure AD, użytkownik musi być zainicjowany w HubSpot. W HubSpot, Inicjowanie obsługi jest zadaniem ręcznym.

Aby zainicjować obsługę administracyjną konta użytkownika w programie HubSpot:

1. Zaloguj się do witryny firmy HubSpot jako administrator.

1. Wybierz ikonę **Ustawienia** w prawym górnym rogu strony.

    ![Ikona Ustawienia w HubSpot](./media/hubspot-tutorial/config1.png)

1. Wybierz pozycję **użytkownicy & zespoły**.

    ![Opcja Użytkownicy & zespoły w HubSpot](./media/hubspot-tutorial/user1.png)

1. Wybierz pozycję **Utwórz użytkownika**.

    ![Opcja tworzenia użytkownika w HubSpot](./media/hubspot-tutorial/user2.png)

1. W polu **Dodaj e-mail addess (ES)** wprowadź adres e-mail użytkownika w formacie brittasimon \@ contoso.com, a następnie wybierz przycisk **dalej**.

    ![Pole Dodaj adresy e-mail w sekcji Tworzenie użytkowników w HubSpot](./media/hubspot-tutorial/user3.png)

1. W sekcji **Tworzenie użytkowników** wybierz każdą kartę. Na każdej karcie Ustaw odpowiednie opcje i uprawnienia dla użytkownika. Następnie wybierz pozycję **Dalej**.

    ![Karty w sekcji Tworzenie użytkowników w HubSpot](./media/hubspot-tutorial/user4.png)

1. Aby wysłać zaproszenie do użytkownika, wybierz pozycję **Wyślij**.

    ![Opcja wysyłania w HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > Użytkownik jest aktywowany po zaakceptowaniu zaproszenia przez użytkownika.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania HubSpot, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania HubSpot i zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do HubSpot, dla którego skonfigurowano Logowanie jednokrotne 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka HubSpot w obszarze Moje aplikacje, jeśli jest skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do HubSpot, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu HubSpot można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).