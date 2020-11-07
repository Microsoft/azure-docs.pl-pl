---
title: 'Samouczek: Inicjowanie obsługi użytkowników dla Pingboard — Azure AD'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze Pingboard.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: arvinh
ms.openlocfilehash: ac36f5d6d1f57fd8453c54bcc8cf19dd964f47f6
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357899"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Pingboard na potrzeby automatycznego aprowizacji użytkowników

W tym samouczku przedstawiono kroki, które należy wykonać, aby włączyć automatyczne Inicjowanie obsługi i cofanie aprowizacji kont użytkowników z Azure Active Directory (Azure AD) do Pingboard.

## <a name="prerequisites"></a>Wymagania wstępne

W scenariuszu opisanym w tym samouczku założono, że masz już następujące elementy:

* Dzierżawa usługi Azure AD
* [Konto Pro](https://pingboard.com/pricing) dzierżawy Pingboard
* Konto użytkownika w Pingboard z uprawnieniami administratora

> [!NOTE]
> Integracja z obsługą administracyjną usługi Azure AD opiera się na [interfejsie API Pingboard](https://pingboard.docs.apiary.io/#), który jest dostępny dla Twojego konta.

## <a name="assign-users-to-pingboard"></a>Przypisywanie użytkowników do Pingboard

Usługa Azure AD używa koncepcji zwanej "przypisaniami" w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi konta użytkownika są synchronizowane tylko użytkownicy przypisani do aplikacji w usłudze Azure AD. 

Przed skonfigurowaniem i włączeniem usługi aprowizacji należy zdecydować, którzy użytkownicy usługi Azure AD potrzebują dostępu do aplikacji Pingboard. Następnie możesz przypisać tych użytkowników do aplikacji Pingboard, postępując zgodnie z poniższymi instrukcjami:

[Przypisywanie użytkownika do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Pingboard

Zalecamy przypisanie jednego użytkownika usługi Azure AD do Pingboard w celu przetestowania konfiguracji aprowizacji. Dodatkowych użytkowników można przypisywać później.

## <a name="configure-user-provisioning-to-pingboard"></a>Konfigurowanie aprowizacji użytkowników do Pingboard 

Ta sekcja przeprowadzi Cię przez proces łączenia usługi Azure AD z interfejsem API aprowizacji konta użytkownika Pingboard. Należy również skonfigurować usługę aprowizacji, aby tworzyć, aktualizować i wyłączać przypisane konta użytkowników w usłudze Pingboard, które są oparte na przypisaniach użytkowników w usłudze Azure AD.

> [!TIP]
> Aby włączyć rejestrację jednokrotną opartą na protokole SAML dla Pingboard, postępuj zgodnie z instrukcjami podanymi w [Azure Portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji, chociaż te dwie funkcje uzupełniają się wzajemnie.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi konta użytkownika w usłudze Azure AD Pingboard

1. W [Azure Portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory** aplikacje dla  >  **Enterprise Apps**  >  **wszystkich aplikacji** w przedsiębiorstwie.

1. Jeśli już skonfigurowano Pingboard do logowania jednokrotnego, Wyszukaj wystąpienie elementu Pingboard przy użyciu pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i Wyszukaj **Pingboard** w galerii aplikacji. Wybierz pozycję **Pingboard** z wyników wyszukiwania, a następnie dodaj ją do listy aplikacji.

1. Wybierz wystąpienie elementu Pingboard, a następnie wybierz kartę **Inicjowanie obsługi** .

1. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Inicjowanie obsługi Pingboard](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)

1. W sekcji **poświadczenia administratora** wykonaj następujące czynności:

    a. W polu **adres URL dzierżawy** wpisz `https://your_domain.pingboard.com/scim/v2` i Zastąp ciąg "your_domain" rzeczywistą domeną.

    b. Zaloguj się do usługi [Pingboard](https://pingboard.com/) przy użyciu konta administratora.

    c. Wybierz **Add-Ons** opcję  >  **integracje** dodatków  >  **Azure Active Directory**.

    d. Przejdź na kartę **Konfiguracja** i wybierz pozycję **Włącz Inicjowanie obsługi użytkowników na platformie Azure**.

    e. Skopiuj token w **tokenie okaziciela OAuth** i wprowadź go w **tokenie tajnym**.

1. W Azure Portal wybierz pozycję **Testuj połączenie** , aby sprawdzić, czy usługa Azure AD może nawiązać połączenie z aplikacją Pingboard. Jeśli połączenie nie powiedzie się, sprawdź, czy konto usługi Pingboard ma uprawnienia administratora, a następnie spróbuj ponownie wykonać krok **połączenia testowego** .

1. Wprowadź adres e-mail osoby lub grupy, dla której chcesz otrzymywać powiadomienia o błędach aprowizacji w **wiadomości e-mail z powiadomieniem**. Zaznacz pole wyboru poniżej.

1. Wybierz pozycję **Zapisz**.

1. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do Pingboard**.

1. W sekcji **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które mają być synchronizowane z usługi Azure AD do Pingboard. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Pingboard for Updates. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany. Aby uzyskać więcej informacji, zobacz [Dostosowywanie mapowań atrybutów aprowizacji użytkowników](../app-provisioning/customize-application-attributes.md).

1. Aby włączyć usługę Azure AD Provisioning for Pingboard, w sekcji **Ustawienia** Zmień **stan aprowizacji** na **włączone**.

1. Wybierz pozycję **Zapisz** , aby rozpocząć początkową synchronizację użytkowników przypisanych do Pingboard.

Synchronizacja początkowa trwa dłużej niż następujące synchronizacje, które wystąpiły co około 40 minut, o ile usługa jest uruchomiona. Sekcja **szczegóły synchronizacji** służy do monitorowania postępu i wykonywania linków do dzienników aktywności aprowizacji. W dziennikach opisano wszystkie akcje podejmowane przez usługę aprowizacji w aplikacji Pingboard.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [raport dotyczący automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](pingboard-tutorial.md)
