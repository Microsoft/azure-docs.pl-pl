---
title: 'Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z dokumentami w dokumentach Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i dokumentów.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: 48ba2810c0aaf304042580cdf6579df54fd9ccd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645680"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>Samouczek: integracja logowania jednokrotnego (SSO) Azure Active Directory z dokumentami

W tym samouczku dowiesz się, jak zintegrować dokumenty z usługą Azure Active Directory (Azure AD). Podczas integrowania dokumentów z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do dokumentów w usłudze.
* Umożliwia użytkownikom automatyczne logowanie się do dokumentów w dokumentach sieci przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Dokumenty w programie obsługuje zainicjowanie rejestracji jednokrotnej w programie **SP**

## <a name="adding-netdocuments-from-the-gallery"></a>Dodawanie dokumentów z galerii

Aby skonfigurować integrację dokumentów w usłudze Azure AD, musisz dodać dokumenty z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **dokumenty** w polu wyszukiwania.
1. Wybierz pozycję **dokumenty** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-netdocuments"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla dokumentów

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą dokumentów sieci przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w dokumentach sieciowych.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą dokumentów, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj rejestrację jednokrotną dla dokumentów](#configure-netdocuments-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz dokumenty w postaci użytkownika testowego](#create-netdocuments-test-user)** , aby dysponować odpowiednikiem B. Simon w dokumentach sieci połączonych z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie **integracja aplikacji sieci** Web Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **adres URL logowania** wpisz jeden z następujących wzorców URL:

    |Adres URL logowania|
    |-----------|
    |`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://eu.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://de.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://au.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz jeden z adresów URL:

    |Identyfikator|
    |-----------|
    |`http://netdocuments.com/VAULT`|
    |`http://netdocuments.com/EU`|
    |`http://netdocuments.com/AU`|
    |`http://netdocuments.com/DE`|
    |

    c. W polu tekstowym **adres URL odpowiedzi** wpisz jeden z następujących wzorców URL:

    |Adres URL odpowiedzi|
    |-----------|
    |`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://eu.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://de.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://au.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu adresu URL logowania i adresu URL odpowiedzi. Identyfikator repozytorium to wartość rozpoczynająca się od **urzędu certyfikacji** , po którym następuje 8 kodów znaków skojarzonych z repozytorium dokumentów. Aby uzyskać więcej informacji, możesz sprawdzić dokument dotyczący [obsługi tożsamości federacyjnych w dokumentach](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login) . Możesz też skontaktować się z [zespołem pomocy technicznej klienta](https://support.netdocuments.com/hc/) , aby uzyskać te wartości, jeśli masz problemy z konfiguracją przy użyciu powyższych informacji. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja sieci Documents oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Aplikacja dla dokumentów sieci oczekuje mapowania **NameIdentifier** z identyfikatorem **objectid** lub innym zastrzeżeniem, które ma zastosowanie do Twojej organizacji jako **NameIdentifier**, dlatego należy edytować Mapowanie atrybutów, klikając ikonę **Edytuj** i zmieniając mapowanie atrybutu.

    ![image (obraz)](common/edit-attribute.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **adres URL metadanych federacji aplikacji** i skopiuj adres URL.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

1. W sekcji **Konfigurowanie dokumentów** sieci skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do dokumentów w usłudze.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **dokumenty**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola &quot;domyślny dostęp&quot;.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name=&quot;configure-netdocuments-sso&quot;></a>Konfigurowanie rejestracji jednokrotnej dokumentów w dokumentach

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny dokumentów w organizacji jako administrator.

2. W prawym górnym rogu wybierz swoją nazwę>**administrator**.

3. Wybierz pozycję **Security Center**.
   
    ![Repozytorium](./media/netdocuments-tutorial/security-center.png &quot;Security Center")

4. Wybierz pozycję **Zaawansowane uwierzytelnianie**.
    
    ![Skonfiguruj zaawansowane opcje uwierzytelniania](./media/netdocuments-tutorial/advance-authentication.png "Skonfiguruj zaawansowane opcje uwierzytelniania")

5.  Na karcie **Identyfikator federacyjny** wykonaj następujące czynności:   
   
    [![Tożsamość federacyjna](./media/netdocuments-tutorial/federated-id.png "Tożsamość federacyjna")](./media/netdocuments-tutorial/federated-id.png#lightbox)
   
    a. W przypadku **typu federacyjnego serwera tożsamości** wybierz opcję jako **Azure Active Directory systemu Windows**.
    
    b.  Wybierz pozycję **Wybierz plik**, aby przekazać pobrany plik metadanych pobrany z Azure Portal.
    
    c.  Wybierz pozycję **Zapisz**.

### <a name="create-netdocuments-test-user"></a>Tworzenie użytkownika testowego

Aby umożliwić użytkownikom usługi Azure AD logowanie się w dokumentach, muszą one być obsługiwane w dokumentach. W przypadku dokumentów, Inicjowanie obsługi jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmowej **dokumentów** w organizacji jako administrator.

2. W prawym górnym rogu wybierz swoją nazwę>**administrator**.
   
    ![Administracja](./media/netdocuments-tutorial/user-admin.png "Administracja")

3. Wybierz pozycję **Użytkownicy i grupy**.
   
    ![Użytkownicy i grupy](./media/netdocuments-tutorial/users-groups.png "Repozytorium")

4. W polu tekstowym **adres e-mail** wpisz adres e-mail prawidłowego konta Azure Active Directory, które chcesz udostępnić, a następnie kliknij przycisk **Dodaj użytkownika**.
   
    ![Adres e-mail](./media/netdocuments-tutorial/user-mail.png "Adres e-mail")
   
    > [!NOTE]
    > Właściciel konta Azure Active Directory otrzyma wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim staną się aktywne. Do aprowizacji Azure Active Directory kont użytkowników można używać innych narzędzi do tworzenia kont użytkowników i interfejsów API dostarczonych przez dokumenty.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania do dokumentu, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do dokumentacji usługi dokumenty dotyczącej logowania i zainicjuj tam przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka dokumenty w moich aplikacjach należy automatycznie zalogować się do dokumentów, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu dokumentów sieci Web można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).