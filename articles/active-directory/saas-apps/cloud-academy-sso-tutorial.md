---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym za pomocą usługi Cloud Academy — SSO'
description: W tym samouczku dowiesz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Cloud Academy — Logowanie jednokrotne.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/15/2020
ms.author: jeedes
ms.openlocfilehash: fa46d6e5c7f1007e3a90e22eb9d4f46e18251a28
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98729835"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-cloud-academy---sso"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego z usługą Cloud Academy — Logowanie jednokrotne

W tym samouczku dowiesz się, jak zintegrować usługę Cloud Academy — Logowanie jednokrotne z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Cloud Academy — Logowanie jednokrotne w usłudze Azure AD pozwala:

* Użyj usługi Azure AD, aby kontrolować, kto może uzyskiwać dostęp do usługi Cloud Academy — Logowanie jednokrotne.
* Zezwól użytkownikom na automatyczne logowanie do usługi Cloud Academy — Logowanie jednokrotne przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja usługi Cloud Academy — Rejestracja jednokrotna z włączonym logowaniem jednokrotnym (SSO).

## <a name="tutorial-description"></a>Opis samouczka

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Cloud Academy — Logowanie jednokrotne **obsługuje** zainicjowanie rejestracji jednokrotnej
* Cloud Academy — Logowanie jednokrotne obsługuje Inicjowanie obsługi użytkowników **just in Time**

## <a name="add-cloud-academy---sso-from-the-gallery"></a>Dodawanie usługi Cloud Academy — Logowanie jednokrotne z galerii

Aby skonfigurować integrację programu Cloud Academy — Logowanie jednokrotne do usługi Azure AD, musisz dodać chmurę Cloud Academy — Logowanie jednokrotne z galerii do listy zarządzanych aplikacji SaaS:

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W lewym okienku wybierz pozycję **Azure Active Directory**.
1. Przejdź do pozycji **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wprowadź w polu wyszukiwania pozycję **Cloud Academy — Logowanie jednokrotne** .
1. Wybierz pozycję **Cloud Academy — logowanie JEDNOkrotne** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-cloud-academy---sso"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD dla chmury Cloud Academy — Logowanie jednokrotne

Skonfigurujesz i testujesz Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Cloud Academy — Logowanie jednokrotne przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i odpowiednim użytkownikiem w usłudze Cloud Academy — Logowanie jednokrotne.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Cloud Academy — Logowanie jednokrotne, należy wykonać następujące czynności wysokiego poziomu:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD.
    1. **[Przyznaj użytkownikowi testowemu](#grant-access-to-the-test-user)** uprawnienia umożliwiające użytkownikowi korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. Skonfiguruj Logowanie jednokrotne w usłudze **[Cloud Academy — logowanie JEDNOkrotne](#configure-single-sign-on-for-cloud-academy)** po stronie aplikacji.
    1. **[Utwórz użytkownika testowego w usłudze Cloud Academy — logowanie JEDNOkrotne](#create-a-cloud-academy-test-user)** jako odpowiednik reprezentacji użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne w usłudze Azure AD w Azure Portal:

1. Na Azure Portal na stronie integracja z usługą **Cloud Academy — logowanie JEDNOkrotne** w sekcji **Zarządzanie** wybierz pozycję **Logowanie** jednokrotne.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz przycisk ołówek dla **podstawowej konfiguracji SAML** , aby edytować ustawienia:

   ![Zrzut ekranu pokazujący przycisk ołówka służący do edytowania podstawowej konfiguracji języka SAML.](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wykonaj następujące czynności:

    a. W polu tekstowym **adres URL logowania** wpisz jeden z następujących adresów URL:
    
    | Adres URL logowania |
    |--------------|
    | `https://cloudacademy.com/login/enterprise/` |
    | `https://app.qa.com/login/enterprise/` |
    |
    
    b. W polu tekstowym **adres URL odpowiedzi** wpisz jeden z następujących adresów URL:
    
    | Adres URL odpowiedzi |
    |--------------|
    | `https://cloudacademy.com/labs/social/complete/saml/` |
    | `https://app.qa.com/labs/social/complete/saml/` |
    |
1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji**. Zapisz adres URL.

    ![Zrzut ekranu pokazujący przycisk kopiowania dla adresu URL metadanych federacji aplikacji.](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie B. Simon w Azure Portal.

1. W lewym okienku Azure Portal wybierz pozycję **Azure Active Directory**. Wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące czynności:
   1. W polu **Nazwa** wprowadź wartość **B. Simon**.  
   1. W polu **Nazwa użytkownika** wprowadź \<username> @ \<companydomain> . \<extension> . Na przykład `B.Simon@contoso.com`.
   1. Wybierz pozycję **Pokaż hasło**, a następnie Zapisz wartość wyświetlaną w polu **hasło** .
   1. Wybierz przycisk **Utwórz**.

### <a name="grant-access-to-the-test-user"></a>Udzielanie dostępu użytkownikowi testowemu

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie temu użytkownikowi dostępu do usługi Cloud Academy — Logowanie jednokrotne.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Cloud Academy — logowanie JEDNOkrotne**.
1. Na stronie Przegląd aplikacji w sekcji **Zarządzanie** wybierz pozycję **Użytkownicy i grupy**:
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** :
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** na liście **Użytkownicy** , a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-single-sign-on-for-cloud-academy"></a>Konfigurowanie logowania jednokrotnego dla usługi Cloud Academy

1. W innym oknie przeglądarki Zaloguj się do witryny firmowej usługi Cloud Academy — Logowanie jednokrotne jako administrator.

1. Wybierz nazwę swojej firmy, a następnie wybierz pozycję **ustawienia & integracje** w wyświetlonym menu:

    ![Zrzut ekranu przedstawiający opcję Ustawienia & integracji.](./media/cloud-academy-sso-tutorial/config-1.PNG)

1. Na stronie **ustawienia & integracji** na karcie **integracje** wybierz kartę **Logowanie jednokrotne** :

    ![Zrzut ekranu przedstawiający kartę Logowanie jednokrotne na karcie integracji.](./media/cloud-academy-sso-tutorial/config-2.PNG)

1. Wykonaj następujące czynności na tej stronie:

    ![Zrzut ekranu przedstawiający stronę Inegrations > rejestracji jednokrotnej.](./media/cloud-academy-sso-tutorial/config-3.PNG)

    a. W polu **adres URL identyfikatora jednostki** wprowadź wartość identyfikatora jednostki skopiowaną z Azure Portal.

    b. W polu **adres URL logowania jednokrotnego** wklej wartość adresu URL logowania skopiowaną z Azure Portal.

    c. Otwórz pobrany certyfikat Base64 z Azure Portal w Notatniku. Wklej jego zawartość do pola **certyfikat** .

    d. W polu **Format identyfikatora nazwy** Zachowaj wartość domyślną: `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` .

1. Wybierz pozycję **Zapisz**.

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat konfigurowania usługi Cloud Academy — Logowanie jednokrotne, zobacz [Konfigurowanie logowania](https://support.cloudacademy.com/hc/articles/360043908452-Setting-Up-Single-Sign-On)jednokrotnego.

### <a name="create-a-cloud-academy-test-user"></a>Tworzenie użytkownika testowego w chmurze Academy

W tej sekcji użytkownik o nazwie Britta Simon został utworzony w programie Cloud Academy — Logowanie jednokrotne. Cloud Academy — Logowanie jednokrotne obsługuje Inicjowanie obsługi użytkowników just in Time, które jest domyślnie włączone. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze Cloud Academy — Logowanie jednokrotne, po uwierzytelnieniu zostanie utworzone nowe konto.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do usługi Cloud Academy — adres URL logowania jednokrotnego, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do chmury Cloud Academy — adres URL logowania jednokrotnego i zainicjuj tam przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka Cloud Academy-SSO w obszarze Moje aplikacje zostanie ono przekierowany na adres URL logowania jednokrotnego w usłudze Cloud Academy. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu usługi Cloud Academy — Logowanie jednokrotne umożliwia wymuszenie kontroli sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).