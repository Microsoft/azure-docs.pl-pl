---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu platformy patentowej IamIP | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i IamIP na platformie patentowej.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: jeedes
ms.openlocfilehash: dc9be41cd7975610f2779da09b94066c70a91652
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92460533"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iamip-patent-platform"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu platformy patentowej IamIP

W tym samouczku dowiesz się, jak zintegrować platformę patentową IamIP z usługą Azure Active Directory (Azure AD). Gdy integrujesz platformę patentową IamIP z usługą Azure AD, możesz:

* Użyj usługi Azure AD, aby kontrolować, kto może uzyskać dostęp do platformy patentowej IamIP.
* Zezwól użytkownikom na automatyczne logowanie do IamIP platformy patentowej przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* IamIP subskrypcja platformy patentowej z włączonym logowaniem jednokrotnym (SSO).

## <a name="tutorial-description"></a>Opis samouczka

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

Platforma patentowa IamIP obsługuje logowanie jednokrotne zainicjowane przez usługę SP i dostawcy tożsamości.

Po skonfigurowaniu platformy patentowej IamIP można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji wykraczają poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).


## <a name="add-iamip-patent-platform-from-the-gallery"></a>Dodaj platformę patentową IamIP z galerii

Aby skonfigurować integrację platformy patentowej IamIP z usługą Azure AD, należy dodać IamIP platformę patentową z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W lewym okienku wybierz pozycję **Azure Active Directory**.
1. Przejdź do pozycji **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **IamIP platformę patentową** w polu wyszukiwania.
1. Wybierz pozycję **IamIP platformę patentową** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-iamip-patent-platform"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla IamIP na platformie patentowej

Skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD za pomocą platformy patentowej IamIP przy użyciu użytkownika testowego o nazwie B. Simon. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i odpowiednim użytkownikiem na platformie patentowej IamIP.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą platformy patentowej IamIP, należy wykonać następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD.
    * **[Przyznaj użytkownikowi testowemu](#grant-access-to-the-test-user)** uprawnienia umożliwiające użytkownikowi korzystanie z logowania jednokrotnego w usłudze Azure AD.

1. **[Skonfiguruj rejestrację jednokrotną IamIP platformy](#configure-iamip-patent-platform-sso)** na stronie aplikacji.
    * **[Utwórz użytkownika testowego platformy patentowej IamIP](#create-iamip-patent-platform-test-user)** jako odpowiednik do reprezentacji usługi Azure AD użytkownika.

1. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne w usłudze Azure AD w Azure Portal:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracji aplikacji **platformy patentowej IamIP** w sekcji **Zarządzanie** wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz przycisk ołówek dla **podstawowej konfiguracji SAML** , aby edytować ustawienia:

   ![Przycisk ołówek dla podstawowej konfiguracji języka SAML](common/edit-urls.png)

1. Jeśli masz plik metadanych dostawcy usług i chcesz skonfigurować Logowanie jednokrotne w trybie zainicjowanym przy użyciu usługi dostawcy tożsamości, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. Wybierz **plik metadanych przekazywania**:

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Wybierz przycisk folder, wybierz plik metadanych, a następnie wybierz pozycję **Przekaż**:

    ![Przyciski folderów i przekazywania](common/browse-upload-metadata.png)

    c. Po przeładowaniu pliku metadanych wartości **identyfikatorów** i **adresów URL odpowiedzi** są wypełniane automatycznie w sekcji **Podstawowa konfiguracja SAML** :

    ![Identyfikator i wartości adresu URL odpowiedzi](common/idp-intiated.png)

    > [!Note]
    > Jeśli wartości **identyfikatorów** i **adresów URL odpowiedzi** nie zostaną wypełnione automatycznie, podaj wartości ręcznie zgodnie z wymaganiami.

1. Wybierz opcję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie zainicjowanym przez program SP:

    W polu **adres URL logowania** wprowadź wartość  **https: \/ /Patents.iamip.com/login-User**.

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz link **pobierania** dla **certyfikatu (RAW)** , aby pobrać certyfikat i zapisać go na komputerze:

    ![Link do pobierania certyfikatu](common/certificateraw.png)

1. W sekcji **Konfigurowanie platformy patentowej IamIP** należy skopiować odpowiedni adres URL lub adresy URL zgodnie z wymaganiami:

    ![Kopiowanie adresów URL konfiguracji](common/idp-intiated.png))

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie temu użytkownikowi dostępu do IamIPej platformy patentowej.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **IamIP platform patentowej**.
1. Na stronie Przegląd aplikacji w sekcji **Zarządzanie** wybierz pozycję **Użytkownicy i grupy**:

   ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** :

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** na liście **Użytkownicy** , a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz przycisk **Przypisz** .

## <a name="configure-iamip-patent-platform-sso"></a>Konfigurowanie IamIP rejestracji jednokrotnej platformy patentowej

Aby skonfigurować Logowanie jednokrotne na stronie platformy patentowej IamIP, należy wysłać pobrany certyfikat pierwotny oraz odpowiednie adresy URL skopiowane z Azure Portal do [zespołu ds. pomocy technicznej platformy IamIP](mailto:info@iamip.com). Konfigurują połączenie SSO protokołu SAML, aby były poprawne po obu stronach.

### <a name="create-iamip-patent-platform-test-user"></a>Utwórz użytkownika testowego platformy patentowej IamIP

Współpracuj z [zespołem pomocy technicznej platformy patentowej IamIP](mailto:info@iamip.com) , aby dodać użytkownika o nazwie B. Simon na platformie patentowej IamIP. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przedstawiono Testowanie konfiguracji logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka IamIP na platformie patentowej w panelu dostępu należy automatycznie zalogować się do wystąpienia usługi patentowej IamIP, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczki dotyczące integracji aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj platformę patentową IamIP za pomocą usługi Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)