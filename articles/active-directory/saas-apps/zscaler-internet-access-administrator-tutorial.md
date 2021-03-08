---
title: 'Samouczek: integracja Azure Active Directory z administratorem dostępu do Internetu rozwiązania Zscaler | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Zscaler Internet Access Administrator.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: jeedes
ms.openlocfilehash: 70afa0a02f4e303105aec1884b966796854c6f49
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449327"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>Samouczek: integracja Azure Active Directory z administratorem dostępu do Internetu rozwiązania Zscaler

W tym samouczku dowiesz się, jak zintegrować usługę rozwiązania Zscaler Internet Access administrator z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi rozwiązania Zscaler z dostępem do Internetu w usłudze Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do rozwiązania Zscaler administratora dostępu do Internetu.
* Zezwól użytkownikom na automatyczne logowanie się, aby rozwiązania Zscaler administrator dostępu do Internetu przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) usługi rozwiązania Zscaler dostępu do Internetu.

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Administrator dostępu do Internetu rozwiązania Zscaler obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne.

## <a name="add-zscaler-internet-access-administrator-from-the-gallery"></a>Dodawanie administratora rozwiązania Zscaler dostępu do Internetu z galerii

Aby skonfigurować integrację aplikacji Zscaler Internet Access Administrator w usłudze Azure AD, należy dodać aplikację Zscaler Internet Access Administrator z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **rozwiązania Zscaler administrator dostępu do Internetu** w polu wyszukiwania.
1. Wybierz pozycję **rozwiązania Zscaler administrator dostępu do Internetu** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-internet-access-administrator"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla administratora rozwiązania Zscaler dostępu do Internetu

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą administratora rozwiązania Zscaler dostępu do Internetu przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w rozwiązania Zscaler administrator dostępu do Internetu.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą administratora rozwiązania Zscaler dostępu do Internetu, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj Logowanie jednokrotne w usłudze rozwiązania Zscaler dostępu do Internetu](#configure-zscaler-internet-access-administrator-sso)** , aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji Zscaler Internet Access Administrator](#create-zscaler-internet-access-administrator-test-user)** — aby w aplikacji Zscaler Internet Access Administrator utworzyć odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **administratora dostępu do Internetu rozwiązania Zscaler** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz jeden z następujących adresów URL zgodnie z wymaganiami:

    | Identyfikator |
    |------------|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. W polu tekstowym **adres URL odpowiedzi** wpisz jeden z następujących adresów URL zgodnie z wymaganiami:

    | Adres URL odpowiedzi |
    |-----------|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. Aplikacja Zscaler Internet Access Administrator oczekuje potwierdzeń SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty i oświadczenia użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty i oświadczenia użytkownika**.

    ![Link do atrybutu](./media/zscaler-internet-access-administrator-tutorial/attributes.png)

6. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej, i wykonaj następujące czynności:

    | Nazwa  | Atrybut źródłowy  |
    | ---------| ------------ |
    | Rola | user.assignedroles |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    b. Z listy **atrybutów źródłowych** wybierz wartość atrybutu.

    c. Kliknij przycisk **OK**.

    d. Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Kliknij [tutaj](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) , aby dowiedzieć się, jak skonfigurować rolę w usłudze Azure AD.

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

8. W sekcji **konfigurowania aplikacji Zscaler Internet Access Administrator** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi rozwiązania Zscaler administrator dostępu do Internetu.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **rozwiązania Zscaler administrator dostępu do Internetu**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli skonfigurowano role zgodnie z opisem w powyższej tabeli, można wybrać ją z listy rozwijanej **Wybierz rolę** .
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-zscaler-internet-access-administrator-sso"></a>Konfigurowanie logowania jednokrotnego administratora dostępu do Internetu rozwiązania Zscaler

1. W innym oknie przeglądarki internetowej zaloguj się do interfejsu użytkownika aplikacji Zscaler Internet Access Administrator.

2. Przejdź do pozycji **Administracja > Zarządzanie administratorami** i wykonaj następujące kroki, po czym kliknij przycisk Zapisz:

    ![Zrzut ekranu przedstawia zarządzanie administratorami z opcjami umożliwiającymi włączenie uwierzytelniania przy użyciu protokołu SAML, przekazywanie certyfikatu S S i określanie wystawcy.](./media/zscaler-internet-access-administrator-tutorial/management.png "Administracja")

    a. Zaznacz pozycję **Włącz uwierzytelnianie SAML**.

    b. Kliknij pozycję **Przekaż**, aby przekazać certyfikat podpisywania języka SAML na platformie Azure, który został pobrany z witryny Azure Portal w obrębie **publicznego certyfikatu SSL**.

    c. Opcjonalnie, w celu poprawy zabezpieczeń, dodaj szczegóły **wystawcy**, aby sprawdzić odpowiedź wystawcy SAML.

3. W interfejsie użytkownika administratora wykonaj następujące kroki:

    ![Zrzut ekranu przedstawia administrator I miejsce, w którym można wykonać te czynności.](./media/zscaler-internet-access-administrator-tutorial/activation.png)

    a. Umieść kursor nad menu **Aktywacja** w lewym dolnym rogu.

    b. Kliknij pozycję **Aktywuj**.

### <a name="create-zscaler-internet-access-administrator-test-user"></a>Tworzenie użytkownika testowego aplikacji Zscaler Internet Access Administrator

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w aplikacji Zscaler Internet Access Administrator. Aplikacja Zscaler Internet Access nie obsługuje inicjowania aprowizacji Just-In-Time dla logowania jednokrotnego administratora. Musisz ręcznie utworzyć konto administratora.
Instrukcje dotyczące sposobu tworzenia konta administratora można znaleźć w dokumentacji aplikacji Zscaler:

https://help.zscaler.com/zia/adding-admins

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Testuj tę aplikację w Azure Portal i zaloguj się automatycznie do administratora dostępu do Internetu rozwiązania Zscaler, dla którego skonfigurowano Logowanie jednokrotne

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka administrator dostępu do Internetu rozwiązania Zscaler w obszarze Moje aplikacje należy automatycznie zalogować się do administratora dostępu do Internetu usługi rozwiązania Zscaler, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu administratora rozwiązania Zscaler dostępu do Internetu można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
