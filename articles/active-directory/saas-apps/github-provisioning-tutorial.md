---
title: 'Samouczek: Inicjowanie obsługi użytkowników w usłudze GitHub — Azure AD'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i dezaktywowania kont użytkowników w usłudze GitHub.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82f7252f2d9cdd2c54fae593d8463bfe84bd6ce2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77057655"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi GitHub na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w witrynie GitHub i usłudze Azure AD w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników z usługi Azure AD w serwisie GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące elementy:

* Dzierżawa usługi Azure Active Directory
* Organizacja usługi GitHub utworzona w [chmurze usługi GitHub dla przedsiębiorstw](https://help.github.com/articles/github-s-products/#github-enterprise), która wymaga [planu rozliczeniowego usługi GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)
* Konto użytkownika w usłudze GitHub z uprawnieniami administratora do organizacji
* Upewnij się, że dostęp OAuth został podany dla Twojej organizacji, zgodnie z opisem w [tym miejscu](https://help.github.com/en/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)

> [!NOTE]
> Integracja z obsługą administracyjną usługi Azure AD opiera się na [interfejsie API Standard scim](https://developer.github.com/v3/scim/)w usłudze GitHub, który jest dostępny dla klientów w [chmurze dla przedsiębiorstw](https://help.github.com/articles/github-s-products/#github-enterprise) w serwisie GitHub. [GitHub Enterprise billing plan](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)

## <a name="assigning-users-to-github"></a>Przypisywanie użytkowników do usługi GitHub

Azure Active Directory używa koncepcji o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi kont użytkowników są synchronizowane tylko użytkownicy i grupy, które zostały przypisane do aplikacji w usłudze Azure AD. 

Przed skonfigurowaniem i włączeniem usługi aprowizacji należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji w serwisie GitHub. Po ustaleniu tych użytkowników można przypisać je do aplikacji usługi GitHub, postępując zgodnie z poniższymi instrukcjami:

[Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Ważne porady dotyczące przypisywania użytkowników do usługi GitHub

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do serwisu GitHub w celu przetestowania konfiguracji aprowizacji. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do usługi GitHub należy wybrać rolę **użytkownika** lub inną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. **Domyślna rola dostępu** nie działa w przypadku inicjowania obsługi administracyjnej, a użytkownicy są pomijani.

## <a name="configuring-user-provisioning-to-github"></a>Konfigurowanie aprowizacji użytkowników w usłudze GitHub

Ta sekcja przeprowadzi Cię przez proces łączenia się z interfejsem API aprowizacji usługi Azure AD do konta użytkownika usługi GitHub, a następnie Konfigurując usługę aprowizacji do tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w usłudze GitHub na podstawie przypisywania użytkowników i grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć funkcję logowania jednokrotnego opartego na protokole SAML dla usługi GitHub, postępując zgodnie z instrukcjami podanymi w [Azure Portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji, chociaż te dwie funkcje napadają nawzajem.

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Konfigurowanie automatycznego inicjowania obsługi konta użytkownika w usłudze GitHub w usłudze Azure AD

1. W [Azure Portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** .

2. Jeśli masz już skonfigurowaną funkcję rejestracji jednokrotnej w usłudze GitHub, Wyszukaj swoje wystąpienie usługi GitHub przy użyciu pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i Wyszukaj witrynę **GitHub** w galerii aplikacji. Wybierz pozycję GitHub z wyników wyszukiwania, a następnie dodaj ją do listy aplikacji.

3. Wybierz wystąpienie usługi GitHub, a następnie wybierz kartę **aprowizacji** .

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Inicjowanie obsługi usługi GitHub](./media/github-provisioning-tutorial/GitHub1.png)

5. W sekcji **poświadczenia administratora** kliknij przycisk **Autoryzuj**. Ta operacja otwiera okno dialogowe autoryzacji usługi GitHub w nowym oknie przeglądarki. Pamiętaj, że musisz upewnić się, że masz zatwierdzenie do autoryzowania dostępu. Postępuj zgodnie z instrukcjami opisanymi [tutaj](https://help.github.com/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization).

6. W nowym oknie Zaloguj się do usługi GitHub przy użyciu konta administratora. W oknie dialogowym uzyskana autoryzacja wybierz zespół usługi GitHub, dla którego chcesz włączyć obsługę administracyjną, a następnie wybierz pozycję **Autoryzuj**. Po zakończeniu Wróć do Azure Portal, aby zakończyć konfigurację aprowizacji.

    ![Okno dialogowe autoryzacji](./media/github-provisioning-tutorial/GitHub2.png)

7. W Azure Portal wprowadź **adres URL dzierżawy** i kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z Twoją aplikacją w usłudze GitHub. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto w usłudze GitHub ma uprawnienia administratora i **adres URL dzierżawy** został niepoprawnie podano, a następnie spróbuj ponownie wykonać krok "Autoryzuj" ( **adres URL dzierżawy** można określić za pomocą reguły: możesz `https://api.github.com/scim/v2/organizations/<Organization_name>` znaleźć swoje organizacje w ramach konta usługi GitHub: **Ustawienia**  >  **organizacji**).

    ![Okno dialogowe autoryzacji](./media/github-provisioning-tutorial/GitHub3.png)

8. Wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach dotyczących aprowizacji w polu **E-mail powiadomienia** , i zaznacz pole wyboru "Wyślij powiadomienie e-mail, gdy wystąpi błąd".

9. Kliknij pozycję **Zapisz**.

10. W sekcji mapowania wybierz pozycję **synchronizuj Azure Active Directory użytkowników do serwisu GitHub**.

11. W sekcji **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do serwisu GitHub. Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do kont użytkowników w usłudze GitHub dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

12. Aby włączyć usługę Azure AD Provisioning dla usługi GitHub, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

13. Kliknij pozycję **Zapisz**.

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup przypisanych do usługi GitHub w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa jest uruchomiona. Za pomocą sekcji **szczegóły synchronizacji** można monitorować postęp i wykonywać linki do dzienników aktywności aprowizacji, które opisują wszystkie akcje wykonywane przez usługę aprowizacji.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
