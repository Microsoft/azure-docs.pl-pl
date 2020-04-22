---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z aplikacją Profit.co SAML | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a aplikacją Profit.co SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 27f42934-c636-43dd-9c20-a3c6316f2763
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 564ca97210d85c5118901f30261abe3de9df1053
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770923"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-profitco-saml-app"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z aplikacją Profit.co SAML

W tym samouczku dowiesz się, jak zintegrować Profit.co aplikacji SAML z usługą Azure Active Directory (Azure AD). Podczas integracji aplikacji PROFIT.CO SAML z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do Profit.co aplikacji SAML.
* Włącz użytkownikom automatyczne logowanie się do Profit.co aplikacji SAML za pomocą swoich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji, witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji z usługą Azure AD z aplikacją —SaaS( SaaS), zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Profit.co subskrypcję z włączoną funkcją logowania jednokrotnego aplikacji SAML.Profit.co SAML App single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Profit.co APLIKACJA SAML obsługuje identyfikator sytowy inicjowany przez protokół IDP.

* Po skonfigurowaniu Profit.co aplikacji SAML można wymusić kontrolę sesji. Chroni to eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-profitco-saml-app-from-the-gallery"></a>Dodaj Profit.co aplikacji SAML z galerii

Aby skonfigurować integrację Profit.co aplikacji SAML z usługą Azure AD, należy dodać Profit.co aplikacji SAML z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Profit.co aplikacji SAML** w polu wyszukiwania.
1. Wybierz **Profit.co aplikacji SAML** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-profitco-saml-app"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla aplikacji Profit.co SAML

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą Profit.co aplikacji SAML przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, nawiązuj połączenie między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w aplikacji Profit.co SAML.

Oto ogólne kroki konfigurowania i testowania usługi Azure AD SSO za pomocą aplikacji Profit.co SAML:

1. **[Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso)** aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć B.Simon do korzystania z usługi Azure AD logowania jednokrotnego.
1. **[Skonfiguruj Profit.co logowania jednokrotnego aplikacji SAML,](#configure-profitco-saml-app-sso)** aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz Profit.co użytkownika testu aplikacji SAML,](#create-a-profitco-saml-app-test-user)** aby mieć odpowiednik B.Simon w aplikacji Profit.co SAML. Ten odpowiednik jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Przetestuj sytą próbę sycącą,](#test-sso)** aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/), na stronie integracji aplikacji **Profit.co SAML** znajdź sekcję **Zarządzaj.** Wybierz **opcję logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Zrzut ekranu przedstawiający konfigurowanie logowania jednokrotnego na stronie SAML z wyróżnioną ikoną ołówka](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** aplikacja jest wstępnie skonfigurowana, a niezbędne adresy URL są już wstępnie wypełniane na platformie Azure. Użytkownicy muszą zapisać konfigurację, wybierając przycisk **Zapisz.**

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** wybierz przycisk **Kopiuj.** Spowoduje to skopiowanie **adresu URL metadanych federacji aplikacji** i zapisanie go na komputerze.

    ![Zrzut ekranu przedstawiający certyfikat podpisywania SAML z wyróżnionym przyciskiem kopiowania](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję**Użytkownicy** >  **usługi Azure Active Directory** > **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło,** a następnie zapisz wartość wyświetlaną w polu **Hasło.**
   1. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć B.Simon do korzystania z usługi Azure logowania jednokrotnego, udzielając dostępu do Profit.co aplikacji SAML.

1. W portalu Azure wybierz pozycję **Aplikacje** > dla**przedsiębiorstw Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **pozycję Profit.co aplikacji SAML**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Zrzut ekranu przedstawiający sekcję Zarządzanie z wyróżnionymi wyróżnionymi użytkownikami i grupami](common/users-groups-blade.png)

1. Wybierz przycisk **Dodaj użytkownika**. W oknie dialogowym **Dodawanie przydziału** wybierz pozycję **Użytkownicy i grupy**.

    ![Zrzut ekranu przedstawiający stronę Użytkownicy i grupy z wyróżnioną pozycją Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy użytkowników. Następnie wybierz przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Następnie wybierz przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-profitco-saml-app-sso"></a>Konfigurowanie Profit.co aplikacji SAML — sycowe

Aby skonfigurować logowanie jednokrotne po stronie aplikacji SAML Profit.co, należy wysłać adres URL metadanych federacji aplikacji do [zespołu pomocy technicznej aplikacji PROFIT.CO SAML](mailto:support@profit.co). Ustawi on to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-a-profitco-saml-app-test-user"></a>Tworzenie Profit.co użytkownika testowego aplikacji SAML

W tej sekcji utworzysz użytkownika o nazwie B.Simon w aplikacji Profit.co SAML. Praca z [zespołem pomocy technicznej aplikacji PROFIT.CO SAML,](mailto:support@profit.co) aby dodać użytkowników na platformie aplikacji PROFIT.CO SAML. Nie można używać logowania jednokrotnego, dopóki nie utworzysz i nie aktywujesz użytkowników.

## <a name="test-sso"></a>Test SSO

W tej sekcji można przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka aplikacji SAML Profit.co w Panelu dostępu należy automatycznie zalogować się do aplikacji PROFIT.CO SAML, dla której skonfigurowano logującą się logującą. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Profit.co aplikacji SAML za pomocą usługi Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić Profit.co aplikacji SAML z zaawansowaną widocznością i kontrolą](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
