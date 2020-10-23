---
title: 'Samouczek: integracja Azure Active Directory z usługą EY GlobalOne | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i EY GlobalOne.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2020
ms.author: jeedes
ms.openlocfilehash: a248718d12abf90abd80c9210b994e7d74ab111b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92448745"
---
# <a name="tutorial-integrate-ey-globalone-with-azure-active-directory"></a>Samouczek: Integrowanie EY GlobalOne z Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę EY GlobalOne z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi EY GlobalOne z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do EY GlobalOne.
* Zezwól użytkownikom na automatyczne logowanie do EY GlobalOne przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO, EY GlobalOne).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.
* EY GlobalOne obsługuje logowanie jednokrotne **z użyciem SP i dostawcy tożsamości** 
* EY GlobalOne obsługuje Inicjowanie obsługi użytkowników **just in Time** .
* Po skonfigurowaniu EY GlobalOne można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-ey-globalone-from-the-gallery"></a>Dodawanie EY GlobalOne z galerii

Aby skonfigurować integrację usługi EY GlobalOne w usłudze Azure AD, musisz dodać GlobalOne EY z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **EY GlobalOne** w polu wyszukiwania.
1. Wybierz pozycję **EY GlobalOne** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-ey-globalone"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla EY GlobalOne

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą EY GlobalOne przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w EY GlobalOne.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą EY GlobalOne, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj EY GlobalOne](#configure-ey-globalone)** , aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego EY GlobalOne](#create-ey-globalone-test-user)** , aby miał odpowiednik B. Simon w EY GlobalOne, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **EY GlobalOne** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** aplikacja została wstępnie skonfigurowana i wymagane adresy URL są już wstępnie wypełnione na platformie Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz** .

1. Aplikacja EY GlobalOne oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![Zrzut ekranu pokazujący sekcję "atrybuty użytkownika" z wybraną ikoną "Edytuj".](common/edit-attribute.png)

1. Oprócz powyższych, aplikacja EY GlobalOne oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | FirstName (Imię) | user.givenname |
    | LastName (Nazwisko) | user.surname |
    | E-mail | user.mail |
    | Firma | `<YOUR COMPANY NAME>` |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![Zrzut ekranu przedstawiający sekcję "oświadczenia użytkownika" z wyróżnionymi akcjami "Dodaj nowe oświadczenie" i "Zapisz".](common/new-save-attribute.png)

    ![image (obraz)](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK** .

    przykład Kliknij pozycję **Zapisz**.

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź pozycję **certyfikat (RAW)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/certificateraw.png)

1. W sekcji **Konfigurowanie EY GlobalOne** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B. Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B. Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do EY GlobalOne.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **EY GlobalOne**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-ey-globalone"></a>Konfigurowanie EY GlobalOne

Aby skonfigurować Logowanie jednokrotne na stronie **EY GlobalOne** , musisz wysłać pobrany **certyfikat (RAW)** i odpowiednie skopiowane adresy URL z Azure Portal do [zespołu pomocy technicznej EY GlobalOne](mailto:globalone.support@ey.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-ey-globalone-test-user"></a>Utwórz użytkownika testowego EY GlobalOne

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w EY GlobalOne. EY GlobalOne obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w programie EY GlobalOne, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka EY GlobalOne w panelu dostępu należy automatycznie zalogować się do GlobalOne EY, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)