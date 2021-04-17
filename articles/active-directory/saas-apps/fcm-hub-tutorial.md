---
title: 'Samouczek: Azure Active Directory integracji logowania jednokrotnego z usługą FCM HUB | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedynczej między usługą Azure Active Directory i usługą FCM HUB.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2021
ms.author: jeedes
ms.openlocfilehash: 23177363cada5d8fde5a3fc93a14da2144b250c3
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516760"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fcm-hub"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego z usługą FCM HUB

Z tego samouczka dowiesz się, jak zintegrować usługę FCM HUB z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi FCM HUB z usługą Azure AD można:

* Kontroluj w usłudze Azure AD, kto ma dostęp do centrum FCM HUB.
* Umożliwianie użytkownikom automatycznego zalogowania się do usługi FCM HUB przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja aplikacji FCM HUB z obsługą logowania jednokrotnego.

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc w jednej dzierżawie można skonfigurować tylko jedno wystąpienie.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujemy logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Centrum FCM HUB obsługuje logowanie **jednokrotne inicjowane przez spjędy i** dostawcy tożsamości.

## <a name="add-fcm-hub-from-the-gallery"></a>Dodawanie centrum FCM HUB z galerii

Aby skonfigurować integrację usługi FCM HUB z usługą Azure AD, musisz dodać usługę FCM HUB z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W **sekcji Dodaj z galerii** wpisz **FCM HUB** w polu wyszukiwania.
1. Wybierz **pozycję FCM HUB** z panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-fcm-hub"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi FCM HUB

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z usługą FCM HUB przy użyciu użytkownika testowego **o nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem usługi FCM HUB.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z usługą FCM HUB, wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedyncze usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie logowania jednokrotnego w aplikacji FCM HUB](#configure-fcm-hub-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego usługi FCM HUB](#create-fcm-hub-test-user)** — aby mieć w usłudze FCM HUB odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Na stronie Azure Portal aplikacji **FCM HUB** znajdź sekcję  Zarządzanie i wybierz **pozycję Logowanie pojedyncze.**
1. Na stronie **Select a single sign-on method (Wybieranie metody logowania pojedynczego)** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania pojedynczego za pomocą saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie **inicjowanym przez** sp. z o.o.:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://hub.fcm.travel/SsoSp/SpInit?clientid=<CUSTOMID>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z przypisanym menedżerem konta lub skontaktuj się z zespołem pomocy technicznej klienta [CENTRUM FCM,](mailto:fcmssoadmin@us.fcm.travel) aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Wybierz pozycję **Zapisz**.

1. Na **stronie Zarządzanie oświadczeniem** w sekcji Atrybuty użytkownika **& oświadczenia** dodaj następujące atrybuty niestandardowe:
   - **Nazwa:** PortalID
   - **Źródło:** Atrybut
   - **Atrybut źródłowy:** PortalID, wartość dostarczana przez usługę FCM

1. W sekcji **Certyfikat podpisywania SAML** użyj opcji edycji, aby wybrać lub wprowadzić następujące ustawienia, a następnie wybierz pozycję **Zapisz:**
   - **Opcja podpisywania:** podpisz odpowiedź SAML
   - **Algorytm podpisywania:** SHA-256

1. Na stronie Konfigurowanie logowania pojedynczego za pomocą saml w sekcji Certyfikat podpisywania  **SAML** znajdź pozycję Certyfikat  **(Base64)** i wybierz pozycję Pobierz, aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W **sekcji Konfigurowanie usługi FCM HUB** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w aplikacji o nazwie Azure Portal B.Simon.

1. W okienku po lewej stronie Azure Portal pozycję Azure Active Directory **pozycję** **Użytkownicy,** a następnie pozycję **Wszyscy użytkownicy.**
1. Wybierz **pozycję Nowy** użytkownik w górnej części ekranu.
1. We **właściwościach** Użytkownik wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **polu Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do usługi FCM HUB.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **FCM HUB.**
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy w** **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie ustawiono żadnej roli, zostanie wybrana rola "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-fcm-hub-sso"></a>Konfigurowanie logowania jednokrotnego usługi FCM HUB

Aby skonfigurować logowanie pojedyncze po stronie usługi **FCM HUB,** musisz wysłać pobrany certyfikat **(Base64)** i odpowiednie adresy URL skopiowane z usługi Azure Portal do menedżera konta, który jest przypisany do Ciebie w celu wsparcia technicznego, lub skontaktować się z zespołem pomocy technicznej klienta centrum [FCM HUB.](mailto:fcmssoadmin@us.fcm.travel) Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-fcm-hub-test-user"></a>Tworzenie użytkownika testowego usługi FCM HUB

W tej sekcji utworzysz użytkownika o nazwie B.Simon w aplikacji FCM HUB. Skontaktuj się z menedżerem konta lub skontaktuj się z zespołem pomocy technicznej klienta usługi [FCM HUB,](mailto:fcmssoadmin@us.fcm.travel) aby dodać użytkowników na platformie FCM HUB. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Inicjowane przez sp:

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowanie do adresu URL logowania centrum FCM, pod którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania usługi FCM HUB i zainicjuj przepływ logowania.

#### <a name="idp-initiated"></a>Inicjowane przez IDP:

* Kliknij pozycję **Test this application** in Azure Portal (Przetestuj tę aplikację) i powinno nas na przykład automatycznie zalogować się do centrum USŁUGI FCM, dla którego została ustawiona logowanie jednokrotne. 

Możesz również użyć usługi Microsoft Moje aplikacje, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka FCM HUB w trybie Moje aplikacje, jeśli zostanie skonfigurowany w trybie SP nastąpi przekierowanie do strony logowania aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie skonfigurowane w trybie dostawcy tożsamości, powinno na celu automatyczne zalogowanie się do centrum USŁUGI FCM, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Moje aplikacje, [zobacz Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu usługi FCM HUB można wymusić kontrolę sesji, która chroni eksfiltrację i przefiltrowanie poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji wykracza poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
