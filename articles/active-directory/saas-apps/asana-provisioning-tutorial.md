---
title: 'Samouczek: Inicjowanie obsługi użytkowników dla Asana — Azure AD'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze Asana.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.reviewer: celested
ms.openlocfilehash: 11e0606568fdaad20f996a7ae20a622576acea15
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88549470"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Asana na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w Asana i Azure Active Directory (Azure AD) w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników z usługi Azure AD do Asana.

## <a name="prerequisites"></a>Wymagania wstępne

W scenariuszu opisanym w tym samouczku założono, że masz już następujące elementy:

* Dzierżawa usługi Azure AD
* Dzierżawa Asana z planem [Enterprise](https://www.asana.com/pricing) lub lepsza
* Konto użytkownika w Asana z uprawnieniami administratora

> [!NOTE]
> Integracja z obsługą administracyjną usługi Azure AD opiera się na [interfejsie API Asana](https://asana.com/developers/api-reference/users), który jest dostępny dla Asana.

## <a name="assign-users-to-asana"></a>Przypisywanie użytkowników do Asana

Usługa Azure AD korzysta ze koncepcji o nazwie *przypisania* , aby określić, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi konta użytkownika są synchronizowane tylko użytkownicy przypisani do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem usługi aprowizacji należy zdecydować, którzy użytkownicy usługi Azure AD potrzebują dostępu do aplikacji Asana. Następnie możesz przypisać tych użytkowników do aplikacji Asana, postępując zgodnie z poniższymi instrukcjami:

[Przypisywanie użytkownika do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Asana

Zalecamy przypisanie jednego użytkownika usługi Azure AD do Asana w celu przetestowania konfiguracji aprowizacji. Dodatkowych użytkowników można przypisywać później.

## <a name="configure-user-provisioning-to-asana"></a>Konfigurowanie aprowizacji użytkowników do Asana

Ta sekcja przeprowadzi Cię przez proces łączenia usługi Azure AD z interfejsem API aprowizacji konta użytkownika Asana. Należy również skonfigurować usługę aprowizacji, aby tworzyć, aktualizować i wyłączać przypisane konta użytkowników w programie Asana na podstawie przypisań użytkowników w usłudze Azure AD.

> [!TIP]
> Aby włączyć rejestrację jednokrotną opartą na protokole SAML dla Asana, postępuj zgodnie z instrukcjami podanymi w [Azure Portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji, chociaż te dwie funkcje uzupełniają się wzajemnie.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi konta użytkownika w usłudze Azure AD Asana

1. W [Azure Portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory**aplikacje dla  >  **Enterprise Apps**  >  **wszystkich aplikacji** w przedsiębiorstwie.

1. Jeśli już skonfigurowano Asana do logowania jednokrotnego, Wyszukaj wystąpienie elementu Asana przy użyciu pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i Wyszukaj **Asana** w galerii aplikacji. Wybierz pozycję **Asana** z wyników wyszukiwania, a następnie dodaj ją do listy aplikacji.

1. Wybierz wystąpienie elementu Asana, a następnie wybierz kartę **Inicjowanie obsługi** .

1. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Inicjowanie obsługi Asana](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

1. W sekcji **poświadczenia administratora** postępuj zgodnie z poniższymi instrukcjami, aby wygenerować token i wprowadzić go w  **tokenie tajnym**:

    a. Zaloguj się do usługi [Asana](https://app.asana.com) przy użyciu konta administratora.

    b. Wybierz zdjęcie profilu z górnego paska, a następnie wybierz bieżące ustawienia nazwy organizacji.

    c. Przejdź do karty **konta usług** .

    d. Wybierz pozycję **Dodaj konto usługi**.

    e. W razie konieczności zaktualizuj **nazwę** **oraz zdjęcie i profil** . Skopiuj token w **tokenie**i wybierz go w obszarze **Zapisz zmiany**.

1. W Azure Portal wybierz pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z aplikacją Asana. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Asana ma uprawnienia administratora, a następnie spróbuj ponownie wykonać krok **połączenia testowego** .

1. Wprowadź adres e-mail osoby lub grupy, dla której chcesz otrzymywać powiadomienia o błędach aprowizacji w  **wiadomości e-mail z powiadomieniem**. Zaznacz pole wyboru poniżej.

1. Wybierz pozycję **Zapisz**.

1. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do Asana**.

1. W sekcji **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które mają być synchronizowane z usługi Azure AD do Asana. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Asana for Updates. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany. Aby uzyskać więcej informacji, zobacz [Dostosowywanie mapowań atrybutów obsługi użytkowników](../app-provisioning/customize-application-attributes.md).

1. Aby włączyć usługę Azure AD Provisioning for Asana, w sekcji **Ustawienia** Zmień **stan aprowizacji** na **włączone**.

1. Wybierz pozycję **Zapisz**.

Teraz początkowa synchronizacja jest uruchamiana dla wszystkich użytkowników przypisanych do Asana w sekcji **Użytkownicy** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa jest uruchomiona. Sekcja **szczegóły synchronizacji** służy do monitorowania postępu i wykonywania linków do dzienników aktywności aprowizacji. Dzienniki inspekcji opisują wszystkie akcje wykonywane przez usługę aprowizacji w aplikacji Asana.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [raport dotyczący automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](asana-tutorial.md)
