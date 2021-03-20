---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO, Single Sign-on) z usługą Parkalot — zarządzanie parkami | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory a Parkalotem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 0098d28d2b211ad9e4bbe60a44c5de4058f2b96b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651428"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-parkalot---car-park-management"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Parkalot — zarządzanie parkami

W ramach tego samouczka dowiesz się, jak zintegrować zarządzanie parkami Parkalotymi z usługą Azure Active Directory (Azure AD). W przypadku integrowania zarządzania parkami Parkalotymi z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do zarządzania parkami Parkalot.
* Zezwól użytkownikom na automatyczne logowanie do usługi Parkalot — zarządzanie zaparkowaniem samochodów przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Parkalot — subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) usługi zarządzania zaparkowaniem samochodu.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Parkalot — zarządzanie parkami samochodowymi obsługuje logowanie jednokrotne w usłudze **SP**

* Parkalot — zarządzanie parkami samochodowymi obsługuje funkcję aprowizacji użytkowników **just in Time**

## <a name="adding-parkalot---car-park-management-from-the-gallery"></a>Dodawanie Parkalot — zarządzanie zaparkowaniem samochodów z galerii

Aby skonfigurować integrację usługi Parkalot — zarządzanie zaparkowaniem samochodów w usłudze Azure AD, musisz dodać zarządzanie parkami Parkalot-samochodowymi z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Parkalot — zarządzanie zaparkowaniem samochodów** w polu wyszukiwania.
1. Wybierz pozycję **Parkalot — zarządzanie zaparkowaniem samochodów** z poziomu panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-parkalot---car-park-management"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD na potrzeby Parkalot — zarządzanie parkami

Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Parkalot — zarządzanie parkami przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Parkalot — zarządzanie parkami.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą zarządzania parkami Parkalot, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Parkalot-Car parkowanie — logowanie JEDNOkrotne](#configure-parkalot-car-park-management-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego usługi Parkalot-Car parkowanie](#create-parkalot-car-park-management-test-user)** , aby uzyskać odpowiednika B. Simon w usłudze Parkalot — zarządzanie zaparkowaniem samochodów, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji do **zarządzania parkami Parkalot-samochodowych** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL przy użyciu jednego z następujących wzorców:

    | Identyfikator (identyfikator jednostki) |
    | -------------- |
    | `https://parkalot.io` |
    | `https://<CUSTOMERNAME>.parkalot.io` |
    |

    b. W polu tekstowym **adres URL odpowiedzi** wpisz adres URL przy użyciu jednego z następujących wzorców:

    | Adres URL odpowiedzi |
    | -------------- |
    | `https://<CUSTOMERNAME>.parkalot.io` |
    | `https://parkalot-saml.firebaseapp.com/__/auth/handler` |
    | `https://parkalot-saml.web.app/__/auth/handler` |
    | `https://<CustomerName>.parkalot.io/__/auth/handler` |
    |

    c. W polu tekstowym **adres URL logowania** wpisz adres URL przy użyciu jednego z następujących wzorców:

    | Adres URL logowania |
    | -------------- |
    | `https://<CUSTOMERNAME>.parkalot.io/#/login` |
    | `https://parkalot-saml.firebaseapp.com/#/login` |
    | `https://parkalot-saml.web.app/#/login` |
    |

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z firmą [Parkalot — zespół ds. pomocy technicznej dotyczącej zaparkowania samochodów](mailto:contact-us@parkalot.io) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie zarządzania zaparkowaniem Parkalot-samochodów** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do zarządzania parkami Parkalot-samochodowymi.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Parkalot — zarządzanie zaparkowaniem samochodów**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-parkalot-car-park-management-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Parkalot-Car parkowania

1. W innym oknie przeglądarki sieci Web Zaloguj się do witryny firmy Parkalot Management do zarządzania parkami jako administrator.

1. Wybierz pozycję **Instalator SAML** i kliknij ikonę **Edytuj** na stronie **Dodaj nową** kartę.

    ![Dodaj nową ikonę edycji.](./media/parkalot-car-park-management-tutorial/setup-saml.png)

1. Wykonaj poniższe czynności opisane poniżej.

    ![Konfigurowanie logowania jednokrotnego do zarządzania parkami Parkalot.](./media/parkalot-car-park-management-tutorial/configuration.png)

    a. W polu tekstowym **Nazwa wyświetlana** podaj prawidłową nazwę.

    b. W polu tekstowym **Identyfikator jednostki dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD** , która została skopiowana z Azure Portal.

    c. W polu tekstowym **adres URL rejestracji jednokrotnej** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    d. Otwórz pobrany **certyfikat (base64)** z Azure Portal do Notatnika i wklej zawartość do pola tekstowego **certyfikatu** .

    e. Kliknij przycisk **Zapisz**.

### <a name="create-parkalot-car-park-management-test-user"></a>Utwórz użytkownika testowego do zarządzania parkami Parkalot-Car

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w ramach zarządzania zaparkowaniem Parkalot. Parkalot — zarządzanie parkami samochodowymi obsługuje funkcję aprowizacji użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze Parkalot — zarządzanie parkami, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do Parkalotego w celu zainicjowania przepływu logowania. 

* Przejdź do adresu URL logowania do usługi Parkalot (zaparkowanie w celu zarządzania samochodem) bezpośrednio i zainicjuj tam przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka zarządzanie parkami Parkalot-samochód w obszarze Moje aplikacje zostanie ono przekierowany do adresu URL logowania do sieci Parkalot — zaparkowanie. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu zarządzania Parkem Parkalot-samochodów można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).