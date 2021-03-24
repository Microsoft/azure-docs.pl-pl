---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą GlobalLink Dashboard Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory a nieidealnym pulpitem nawigacyjnym GlobalLink.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: jeedes
ms.openlocfilehash: ad720f5e625d2054a5c79b47c9659d4d10d251e4
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104955112"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-transperfect-globallink-dashboard"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) za pomocą GlobalLinkego pulpitu nawigacyjnego

W tym samouczku dowiesz się, jak zintegrować GlobalLinky pulpit nawigacyjny z usługą Azure Active Directory (Azure AD). Po zintegrowaniu GlobalLink pulpitu nawigacyjnego z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do nieidealnego pulpitu nawigacyjnego GlobalLink.
* Zezwól użytkownikom na automatyczne logowanie się do nieidealnego pulpitu nawigacyjnego GlobalLink przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) pulpitu nawigacyjnego GlobalLink.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* GlobalLink pulpit nawigacyjny **z usługą SP i dostawcy tożsamości** zainicjował Logowanie jednokrotne.
* Nieidealny pulpit nawigacyjny GlobalLink obsługuje Inicjowanie obsługi użytkowników **just in Time** .

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-transperfect-globallink-dashboard-from-the-gallery"></a>Dodawanie niedoskonałego pulpitu nawigacyjnego GlobalLink z galerii

Aby skonfigurować integrację nieidealnego pulpitu nawigacyjnego GlobalLink z usługą Azure AD, musisz dodać do listy zarządzanych aplikacji SaaS pulpit nawigacyjny GlobalLink z galerii.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** w polu wyszukiwania wpisz ciąg **GlobalLink** .
1. Wybierz pozycję **nieidealny pulpit nawigacyjny GlobalLink** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-transperfect-globallink-dashboard"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla nieidealnego pulpitu nawigacyjnego GlobalLink

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą GlobalLinkego pulpitu nawigacyjnego przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem na pulpicie nawigacyjnym GlobalLink.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą GlobalLink pulpitu nawigacyjnego, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj GlobalLinke logowanie JEDNOkrotne pulpitu nawigacyjnego](#configure-transperfect-globallink-dashboard-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz Nieidealnym użytkownikom testowym pulpitu nawigacyjnego GlobalLink](#create-transperfect-globallink-dashboard-test-user)** , aby dysponować odpowiednikiem B. Simon w nieidealnym pulpicie nawigacyjnym GlobalLink, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **GlobalLink pulpitu nawigacyjnego** , Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://sso.transperfect.com`

1. Kliknij pozycję **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do niedoskonałego pulpitu nawigacyjnego GlobalLink.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **GlobalLink pulpit nawigacyjny**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-transperfect-globallink-dashboard-sso"></a>Konfigurowanie nieidealnego logowania jednokrotnego GlobalLink pulpitu nawigacyjnego

Aby skonfigurować Logowanie jednokrotne po stronie **pulpitu nawigacyjnego GlobalLink** , musisz wysłać **adres URL metadanych federacji aplikacji** do [GlobalLink zespołu pomocy technicznej pulpitu nawigacyjnego](mailto:TechOps_Consulting@transperfect.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-transperfect-globallink-dashboard-test-user"></a>Utwórz GlobalLink użytkownika testowego pulpitu nawigacyjnego

W tej sekcji użytkownik o nazwie B. Simon został utworzony na pulpicie nawigacyjnym GlobalLink. Nieidealny pulpit nawigacyjny GlobalLink obsługuje Inicjowanie obsługi just-in-Time, które jest domyślnie włączone. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje na nieidealnym pulpicie nawigacyjnym GlobalLink, zostanie utworzony nowy, gdy spróbujesz uzyskać dostęp do pulpitu nawigacyjnego GlobalLink.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie na adres URL logowania do pulpitu nawigacyjnego GlobalLink, w którym można zainicjować przepływ logowania.  

* Przejdź do adresu URL logowania za pomocą pulpitu nawigacyjnego GlobalLink bezpośrednio i zainicjuj tam przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do pulpitu nawigacyjnego GlobalLink, dla którego skonfigurowano Logowanie jednokrotne 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka pulpitu nawigacyjnego GlobalLink w obszarze Moje aplikacje, jeśli zostanie on skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do pulpitu nawigacyjnego GlobalLink, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu GlobalLinkego pulpitu nawigacyjnego można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).