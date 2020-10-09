---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu oprogramowania Maxients Manager | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między programami Azure Active Directory i Maxients Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2019
ms.author: jeedes
ms.openlocfilehash: dd6872fd8dca3f29b61c6f1dffb5f219abac5cb8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88518896"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maxient-conduct-manager-software"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu oprogramowania Maxients Manager

W tym samouczku dowiesz się, jak zintegrować oprogramowanie Maxients Manager z usługą Azure Active Directory (Azure AD). Po zintegrowaniu oprogramowania Maxients Manager z usługą Azure AD można:

* Korzystanie z usługi Azure AD do uwierzytelniania użytkowników na potrzeby oprogramowania Maxients Manager
* Zezwól użytkownikom na automatyczne logowanie do oprogramowania Maxients Manager przy użyciu swoich kont usługi Azure AD.


Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) w programie Maxient.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz usługę Azure AD do użycia z oprogramowaniem Maxients Manager.


* Oprogramowanie Maxients Manager obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-maxient-conduct-manager-software-from-the-gallery"></a>Dodawanie oprogramowania Maxients Manager z galerii

Aby skonfigurować integrację oprogramowania Maxients Manager z usługą Azure AD, musisz dodać oprogramowanie Maxients Manager z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Maxient** w polu wyszukiwania.
1. Wybierz pozycję **Maxient Przeprowadź kontrolę oprogramowania** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-maxient-conduct-manager-software"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla oprogramowania Maxients Manager

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD przy użyciu oprogramowania Maxients Manager. Aby logowanie jednokrotne działało, należy nawiązać połączenie między usługą Azure AD a oprogramowaniem Maxient.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu oprogramowania Maxients Manager, wykonaj następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom uwierzytelnianie do użytku w oprogramowaniu Maxients Manager
    1. **[Przypisz wszystkim użytkownikom możliwość korzystania z Maxient](#assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software)** — aby umożliwić wszystkim członkom Twojej instytucji uwierzytelnianie.
1. **[Testowanie konfiguracji usługi Azure AD za pomocą programu Maxient](#test-with-maxient)** — aby sprawdzić, czy konfiguracja działa, i wydawaniu poprawnych atrybutów

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Maxients Manager** , Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML**   aplikacja została wstępnie skonfigurowana w **IDP**   trybie inicjalizacji dostawcy tożsamości, a wymagane adresy URL są już wstępnie wypełnione na platformie Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz**   .

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://cm.maxient.com/<SCHOOLCODE>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Aby uzyskać wartość, skontaktuj się z przedstawicielem ds. implementacji/pomocy technicznej Maxient.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.  W tym adresie URL należy podać przedstawiciela implementacji/pomocy technicznej Maxient.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software"></a>Przypisz wszystkim użytkownikom możliwość uwierzytelniania w oprogramowaniu Maxients Manager

W tej sekcji zostanie udzielony dostęp do wszystkich kont w celu uwierzytelnienia przy użyciu systemu Azure dla oprogramowania Maxients Manager.  Należy pamiętać, że ten krok jest **wymagany** do poprawnego działania programu Maxient.  Maxient wykorzystuje system usługi Azure AD do *uwierzytelniania* użytkowników. *Autoryzacja* użytkowników jest przeprowadzana w systemie Maxient dla konkretnej funkcji, którą próbują wykonać. Maxient nie używa atrybutów z katalogu do podejmowania tych decyzji.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz opcję **oprogramowanie Maxients Manager**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję Wszyscy użytkownicy (lub odpowiednie grupy) i **Przypisz** je do uwierzytelniania za pomocą Maxient.

## <a name="test-with-maxient"></a>Testowanie za pomocą Maxient 

Jeśli bilet pomocy technicznej nie został jeszcze otwarty z Maxientem/przedstawicielem pomocy technicznej, Wyślij wiadomość e-mail na adres [support@maxient.com](mailto:support@maxient.com) z podmiotem "uwierzytelnianie Kampusowe/konfiguracja platformy Azure \<\<School Name\> \> ". W treści wiadomości e-mail Podaj **adres URL metadanych federacji aplikacji**. Personel Maxient odpowie przy użyciu linku testowego, aby sprawdzić, czy są wydane odpowiednie atrybuty.  
    
## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj oprogramowanie Maxients Manager z usługą Azure AD](https://aad.portal.azure.com/)

