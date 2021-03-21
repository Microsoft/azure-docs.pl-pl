---
title: 'Samouczek: integracja z logowaniem jednokrotnym (SSO) Azure Active Directory z pojęciem | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i pojęciem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: jeedes
ms.openlocfilehash: 520eb25bcb138c96b24166816d3374255fb7c3b2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99493992"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-notion"></a>Samouczek: integracja z logowaniem jednokrotnym (SSO) Azure Active Directory z pojęciem

W tym samouczku dowiesz się, jak zintegrować koncepcję z Azure Active Directory (Azure AD). W przypadku integracji koncepcji z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do pojęcia.
* Zezwól użytkownikom na automatyczne logowanie się w celu postanowień z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Oparta na subskrypcji Funkcja rejestracji jednokrotnej (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Pojęcie obsługuje funkcję SSO zainicjowaną przez usługę **SP i dostawcy tożsamości**
* Pojęcie obsługuje Inicjowanie obsługi użytkowników **just in Time**
> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dzięki czemu można skonfigurować jeden hipotetyczny obszar roboczy w jednej dzierżawie.


## <a name="adding-notion-from-the-gallery"></a>Dodawanie koncepcji z galerii

Aby skonfigurować integrację koncepcji z usługą Azure AD, należy dodać pojęcie z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz " **koncepcje** " w polu wyszukiwania.
1. Wybierz pozycję **pojęcie** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-notion"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla koncepcji

Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD z użyciem koncepcji przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w koncepcji.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą koncepcji, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj pojęcie Logowanie jednokrotne](#configure-notion-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz pojęcie użytkownika testowego](#create-notion-test-user)** , aby uzyskać odpowiednik elementu B. Simon w koncepcji, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie **pojęcie** integracji aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **adres URL odpowiedzi** wprowadź adres URL z następującym wzorcem, który można uzyskać z ustawień obszaru roboczego z pojęciem **& członkowie** > **& tożsamość** > **adres URL logowania** jednokrotnego:  `https://www.notion.so/sso/saml/<CUSTOM_ID>`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **adres URL logowania** wprowadź następujący adres URL:  `https://www.notion.so/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL odpowiedzi i adresu URL logowania. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Pojęcie "aplikacja" oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, hipotetyczna aplikacja oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.
    
    | Nazwa |  Atrybut źródłowy|
    | ----------- | --------- |
    | poczta e-mail | user.mail |
    | firstName | user.givenname |
    | lastName | user.surname |


1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji**. Przejdź do pozycji Ustawienia obszaru roboczego z **pojęciem** **& członkowie**  >  **& tożsamość** i wklej wartość skopiowaną do pola **adres URL metadanych dostawcy tożsamości** .

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do pojęcia.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **pojęcie**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-notion-sso"></a>Konfigurowanie koncepcji logowania jednokrotnego

Przejdź do pozycji Ustawienia obszaru roboczego **pojęcia** **& członkowie**  >  **& tożsamość** i wklej wartość **adresu URL metadanych federacji aplikacji** skopiowaną do pola **adres URL metadanych dostawcy tożsamości** .

Na tej samej stronie ustawień w obszarze **domeny poczty e-mail** kliknij pozycję **skontaktuj się z pomocą techniczną** , aby dodać domeny poczty e-mail w organizacji.

Po zatwierdzeniu i dodaniu domen poczty e-mail Włącz logowanie jednokrotne SAML przy użyciu przełącznika **enable SAML** .

Po pomyślnym przetestowaniu można wymusić Logowanie jednokrotne SAML przy użyciu przełącznika **Wymuś protokołu SAML** . Należy pamiętać, że w obszarze roboczym administrastrators nie można zalogować się za pomocą poczty e-mail, ale wszystkie inne elementy członkowskie będą musieli używać logowania jednokrotnego w celu zalogowania się do pojęcia.

### <a name="create-notion-test-user"></a>Utwórz użytkownika testowego koncepcji

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w koncepcji. Pojęcie obsługuje Inicjowanie obsługi użytkowników just in Time, które jest domyślnie włączone. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do zakładanego adresu URL logowania, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do pozycji adres URL logowania, a następnie zainicjuj w tym miejscu przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do koncepcji, dla której skonfigurowano Logowanie jednokrotne 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka koncepcji w obszarze Moje aplikacje, jeśli jest skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do koncepcji, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu koncepcji można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).