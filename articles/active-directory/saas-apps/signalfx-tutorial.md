---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą SignalFx | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i SignalFx.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/24/2020
ms.author: jeedes
ms.openlocfilehash: 1fbc42864761360d252ed62cea1aef6f2937b599
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516073"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą SignalFx

W tym samouczku dowiesz się, jak zintegrować usługę SignalFx z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi SignalFx z usługą Azure AD można:

* Kontrola od usługi Azure AD, która ma dostęp do SignalFx;
* Zezwól użytkownikom na automatyczne logowanie do SignalFx przy użyciu kont usługi Azure AD; lub
* Zarządzaj kontami w jednej lokalizacji (Azure Portal).

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy:

* Subskrypcji usługi Azure AD
    * Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto w tym miejscu](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) SignalFx

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa SignalFx obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne
* SignalFx obsługuje Inicjowanie obsługi użytkowników **just in Time**
* Po skonfigurowaniu SignalFx można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="step-1-add-the-signalfx-application-in-azure"></a>Krok 1. Dodawanie aplikacji SignalFx na platformie Azure

Użyj tych instrukcji, aby dodać aplikację SignalFx do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
1. W okienku nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**.
1. Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** w polu wyszukiwania wprowadź i wybierz pozycję **SignalFx**.
     * Może być konieczne odczekanie kilku minut, aż aplikacja zostanie dodana do dzierżawy.
1. Pozostaw Azure Portal Otwórz, a następnie otwórz nową kartę sieci Web.    

## <a name="step-2-begin-signalfx-sso-configuration"></a>Krok 2. rozpoczęcie konfiguracji SignalFx SSO

Użyj tych instrukcji, aby rozpocząć proces konfiguracji dla logowania jednokrotnego SignalFx.

1. Na nowo otwartej karcie dostęp do interfejsu użytkownika SignalFx i logowanie się do niego. 
1. W górnym menu kliknij pozycję **Integrations (integracje**). 
1. W polu wyszukiwania wprowadź i wybierz **Azure Active Directory**.
1. Kliknij pozycję **Utwórz nową integrację**.
1. W polu **Nazwa**wprowadź łatwą do rozpoznania nazwę, którą użytkownicy będą zrozumieć.
1. Zaznacz pole **Pokaż na stronie logowania**.
    * Ta funkcja wyświetli dostosowany przycisk na stronie logowania, którą użytkownicy mogą kliknąć. 
    * Informacje wprowadzone w polu **Nazwa** będą wyświetlane na przycisku. W związku z tym wprowadź **nazwę** , którą użytkownicy będą rozpoznawać. 
    * Ta opcja będzie działać tylko wtedy, gdy używana jest niestandardowa poddomena dla aplikacji SignalFx, na przykład **YourCompanyName.signalfx.com**. Aby uzyskać niestandardową poddomenę, skontaktuj się z pomocą techniczną SignalFx. 
1. Skopiuj **Identyfikator integracji**. Te informacje będą potrzebne w późniejszym kroku. 
1. Pozostaw otwarty interfejs użytkownika SignalFx. 

## <a name="step-3-configure-azure-ad-sso"></a>Krok 3. Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Użyj tych instrukcji, aby włączyć logowanie jednokrotne w usłudze Azure AD w Azure Portal.

1. Wróć do [Azure Portal](https://portal.azure.com/)i na stronie integracja aplikacji **SignalFx** Znajdź sekcję **Zarządzanie** , a następnie wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę pióra (Edytuj), aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą języka SAML** wykonaj następujące pola: 

    a. W polu **Identyfikator**wprowadź następujący adres URL `https://api.<realm>.signalfx.com/v1/saml/metadata` i zamień go `<realm>` na obszar SignalFx. 

    b. W polu **adres URL odpowiedzi**wprowadź następujący adres URL `https://api.<realm>.signalfx.com/v1/saml/acs/<integration ID>` i zamień go na `<realm>` obszar SIGNALFX, a także `<integration ID>` **Identyfikator integracji** skopiowany wcześniej z interfejsu użytkownika SignalFx.

1. Aplikacja SignalFx oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. 
    
1. Przejrzyj i sprawdź, czy następujące oświadczenia są mapowane do atrybutów źródłowych, które są wypełnione w Active Directory. 

    | Nazwa |  Atrybut źródłowy|
    | ------------------- | -------------------- |
    | User.FirstName  | user.givenname |
    | User. email  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    > [!NOTE]
    > Ten proces wymaga, aby Active Directory został skonfigurowany z co najmniej jedną zweryfikowaną domeną niestandardową, a także mieć dostęp do kont e-mail w tej domenie. Jeśli nie masz pewności lub potrzebujesz pomocy dotyczącej tej konfiguracji, skontaktuj się z pomocą techniczną SignalFx.  

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)**, a następnie wybierz pozycję **Pobierz**. Pobierz certyfikat i Zapisz go na komputerze. Następnie skopiuj wartość **adres URL metadanych federacji aplikacji** ; te informacje będą potrzebne w późniejszym kroku w interfejsie użytkownika SignalFx. 

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie SignalFx** skopiuj wartość **Identyfikator usługi Azure AD** . Te informacje będą potrzebne w późniejszym kroku w interfejsie użytkownika SignalFx. 

## <a name="step-4-create-an-azure-ad-test-user"></a>Krok 4. Tworzenie użytkownika testowego usługi Azure AD

Użyj tych instrukcji, aby utworzyć użytkownika testowego w Azure Portal o nazwie **B. Simon**.

1. W Azure Portal w okienku nawigacji po lewej stronie wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. W górnej części strony wybierz pozycję **nowy użytkownik**.
1. We właściwościach **użytkownika** :
   1. W polu **Nazwa użytkownika**wprowadź `username@companydomain.extension` , na przykład `b.simon@contoso.com` .
   1. W polu **Nazwa**wprowadź `B.Simon` .
   1. Oznacz opcję **Pokaż hasło**, a następnie skopiuj wartość wyświetlaną w polu **hasło**. Te informacje będą potrzebne później do przetestowania tej integracji. 
   1. Kliknij przycisk **Utwórz**.

## <a name="step-5-assign-the-azure-ad-test-user"></a>Krok 5. Przypisywanie użytkownika testowego usługi Azure AD

Te instrukcje umożliwiają użytkownikom testowym korzystanie z rejestracji jednokrotnej na platformie Azure dla SignalFx.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **SignalFx**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** , a następnie wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** z listy **Użytkownicy** wybierz pozycję **B. Simon**, a następnie w dolnej części strony kliknij pozycję **Wybierz**.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części strony.
1. W oknie dialogowym **Dodaj przypisanie** kliknij przycisk **Przypisz**.

## <a name="step-6-complete-the-signalfx-sso-configuration"></a>Krok 6. dokończ konfigurację SignalFx SSO 

1. Otwórz poprzednią kartę i wróć do interfejsu użytkownika SignalFx, aby wyświetlić bieżącą stronę integracji Azure Active Directory. 
1. Obok pozycji **certyfikat (base64)** kliknij pozycję **Przekaż plik**, a następnie zlokalizuj plik **certyfikatu zakodowany algorytmem Base64** , który został wcześniej pobrany z Azure Portal.
1. W obszarze **Identyfikator usługi Azure AD**wklej wartość **identyfikatora usługi Azure AD** skopiowaną wcześniej z Azure Portal. 
1. W polu **adres URL metadanych Federacji**wklej wartość **adresu URL metadanych federacji aplikacji** skopiowaną wcześniej z Azure Portal. 
1. Kliknij pozycję **Zapisz**.

## <a name="step-7-test-sso"></a>Krok 7. Testowanie rejestracji jednokrotnej

Zapoznaj się z poniższymi informacjami dotyczącymi testowania logowania jednokrotnego, a także oczekiwania na logowanie do usługi SignalFx po raz pierwszy. 

### <a name="test-logins"></a>Identyfikatory logowania testów

* Aby przetestować logowanie, należy użyć okna prywatnego/incognito lub wylogować się z Azure Portal. W przeciwnym razie pliki cookie dla użytkownika, który skonfigurował aplikację, będą zakłócać i uniemożliwiają pomyślne zalogowanie się do użytkownika testowego.

* Po pierwszym zalogowaniu się nowego użytkownika testowego platforma Azure wymusi zmianę hasła. W takim przypadku proces logowania jednokrotnego nie zostanie ukończony; Użytkownik testowy zostanie skierowany do Azure Portal. Aby rozwiązać problem, Użytkownik testowy powinien zmienić hasło, a następnie przejść do strony logowania SignalFx lub do panelu dostępu i spróbować ponownie.
    * Po kliknięciu kafelka SignalFx w panelu dostępu należy automatycznie zalogować się do SignalFx. 
        * Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

* Dostęp do aplikacji SignalFx można uzyskać z panelu dostępu lub za pośrednictwem niestandardowej strony logowania przypisanej do organizacji. Użytkownik testowy powinien przetestować integrację, rozpoczynając od jednej z tych lokalizacji.
    * Użytkownik testowy może użyć poświadczeń utworzonych wcześniej w tym procesie dla **b. simon \@ contoso.com**.

### <a name="first-time-logins"></a>Logowanie przy pierwszym czasie

* Gdy użytkownik loguje się do SignalFx z logowania jednokrotnego SAML po raz pierwszy, użytkownik otrzyma wiadomość e-mail SignalFx z linkiem. Użytkownik musi kliknąć link w celu uwierzytelnienia. Ta weryfikacja poczty e-mail będzie odbywać się tylko dla użytkowników po raz pierwszy. 

* Usługa SignalFx obsługuje funkcję tworzenia użytkowników **just in Time** , co oznacza, że jeśli użytkownik nie istnieje w SignalFx, konto użytkownika zostanie utworzone przy pierwszej próbie logowania.

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Wypróbuj SignalFx z usługą Azure AD](https://aad.portal.azure.com/)