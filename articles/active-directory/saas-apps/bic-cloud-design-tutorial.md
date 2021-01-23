---
title: 'Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO, Single Sign-on) przy użyciu projektu BIC w chmurze | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i BIC projektowanie w chmurze.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: 7a706eb120f45fcd08febd0bda9aaaa54a82135a
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98728210"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bic-cloud-design"></a>Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO, Single Sign-on) przy użyciu projektu BIC Cloud

W tym samouczku dowiesz się, jak zintegrować projekt w chmurze BIC z Azure Active Directory (Azure AD). Po zintegrowaniu projektu z chmurą w chmurze z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do projektu BIC w chmurze.
* Zezwól użytkownikom na automatyczne logowanie się w celu BIC projektowania chmury przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* BIC subskrypcja z włączonym logowaniem jednokrotnym (SSO) w chmurze.

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* BIC — projekt w chmurze obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="adding-bic-cloud-design-from-the-gallery"></a>Dodawanie projektu w chmurze BIC z galerii

Aby skonfigurować integrację projektu w chmurze BIC z usługą Azure AD, musisz dodać do swojej listy zarządzanych aplikacji SaaS projekt w chmurze BIC z galerii.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **BIC Cloud Design** w polu wyszukiwania.
1. Wybierz pozycję **BIC projektowanie chmury** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-bic-cloud-design"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD w celu uzyskania projektu w chmurze

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą kodu BIC chmury przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w ramach projektu BIC Cloud.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu kodu BIC w chmurze, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj funkcję rejestracji Jednokrotnej dla projektu w chmurze](#configure-bic-cloud-design-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego do projektowania](#create-bic-cloud-design-test-user)** kodu w chmurze, aby uzyskać odpowiednik elementu B. Simon w ramach projektu w chmurze, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie **Zarządzanie** integracją aplikacji w **chmurze** , Znajdź sekcję Zarządzaj i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych wartość **identyfikatora** zostanie wypełniona automatycznie w sekcji Podstawowa konfiguracja SAML.

    ![BIC domena projektu chmury i adresy URL Logowanie jednokrotne](common/sp-identifier.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: 

    ```https
    https://<customer-specific-name/tenant>.biccloud.com
    https://<customer-specific-name/tenant>.biccloud.de
    ```

    > [!Note]
    > Jeśli wartość **Identyfikator** nie zostanie automatycznie wypełniona, wpisz tę wartość ręcznie zgodnie z wymaganiami. Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej w zakresie projektowania chmury](mailto:bicsupport@gbtec.de) , aby uzyskać tę wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja typu BIC w chmurze oczekuje potwierdzenia SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, BIC aplikacja projektowania w chmurze oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ------------ | --------- |
    | Nazwa | user.name |
    | Adres E-Mail | user.mail |
    | Identyfikator nazwy | user.userprincipalname |
    | poczta e-mail | user.mail |
    | nametest | user.displayname |

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do projektu BIC w chmurze.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **BIC projektowanie chmury**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-bic-cloud-design-sso"></a>Konfigurowanie usługi SSO projektowanie rejestracji jednokrotnej w chmurze

Aby skonfigurować Logowanie jednokrotne na stronie **projektowania BIC w chmurze** , musisz wysłać **adres URL metadanych federacji aplikacji** do [BIC zespołu pomocy technicznej projektowania w chmurze](mailto:bicsupport@gbtec.de). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-bic-cloud-design-test-user"></a>Tworzenie użytkownika testowego projektu w chmurze BIC

W tej sekcji utworzysz użytkownika o nazwie B. Simon w artykule BIC Cloud Design. Pracuj z [zespołem pomocy technicznej](mailto:bicsupport@gbtec.de) w zakresie projektowania w chmurze, aby dodać użytkowników na platformie projektowania w chmurze BIC. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do kodu URL logowania w chmurze, w którym można zainicjować przepływ logowania. 

* Przejdź na adres URL logowania do usługi BIC w chmurze bezpośrednio i zainicjuj tam przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka projekt w chmurze w obszarze Moje aplikacje zostanie on przekierowany do kodu URL logowania do projektu w chmurze. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu projektu w chmurze BIC można wymusić kontrolki sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji rozciągają się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).