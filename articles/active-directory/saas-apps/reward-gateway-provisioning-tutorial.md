---
title: 'Samouczek: Konfigurowanie usługi Nagroda Gateway na potrzeby automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników w celu nagradzania bramy.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2d51903aff6f3fd1cd53d85a980f1b5dc2a893e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94354329"
---
# <a name="tutorial-configure-reward-gateway-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie nagrody bramy dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w ramach bramy z wynagrodzeniem i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do nagradzania bramy.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Dzierżawa bramy](https://www.rewardgateway.com/).
* Konto użytkownika w nagradzaniu bramy z uprawnieniami administratora.

## <a name="assigning-users-to-reward-gateway"></a>Przypisywanie użytkowników do nagradzania bramy 

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do nagradzania bramy. Po ustaleniu tych użytkowników i/lub grup można przypisywać je do bramy, postępując zgodnie z instrukcjami w temacie [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md).


## <a name="important-tips-for-assigning-users-to-reward-gateway"></a>Ważne porady dotyczące przypisywania użytkowników do nagradzania bramy 

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do nagradzania bramy w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do nagradzania bramy należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="setup-reward-gateway--for-provisioning"></a>Skonfiguruj wynagrodzenie bramy na potrzeby aprowizacji
Przed skonfigurowaniem bramy nagradzania w celu automatycznego aprowizacji użytkowników przy użyciu usługi Azure AD należy włączyć obsługę administracyjną Standard scim na bramie z wynagrodzeniem.

1. Zaloguj się do [konsoli administracyjnej bramy z wynagrodzeniem](https://rewardgateway.photoshelter.com/login/). Kliknij przycisk **integracji**.

    ![Zrzut ekranu przedstawiający konsolę administracyjną bramy z wynagrodzeniem z opcją Integrations o nazwie out.](media/reward-gateway-provisioning-tutorial/image00.png)

2.  Wybierz **moją integrację**.

    ![Zrzut ekranu przedstawiający dwie opcje integracji z opcją my Integrations o nazwie out.](media/reward-gateway-provisioning-tutorial/image001.png)

3.  Skopiuj wartości **adresu URL Standard scim (v2)** i **tokenu okaziciela OAuth**. Te wartości zostaną wprowadzone w polu adres URL dzierżawy i klucz tajny tokenu na karcie aprowizacji aplikacji bramy nagradzania w Azure Portal.

    ![Zrzut ekranu przedstawiający panel moje integracje z polem tekstowym token okaziciela OAuth o nazwie out.](media/reward-gateway-provisioning-tutorial/image03.png)

## <a name="add-reward-gateway-from-the-gallery"></a>Dodaj bramę nagradzania z galerii

Aby skonfigurować nagradzaną bramę do automatycznego aprowizacji użytkowników w usłudze Azure AD, należy dodać do listy aplikacji zarządzanych SaaS bramę z galerii aplikacji usługi Azure AD.

**Aby dodać bramę nagradzania z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź polecenie **nagradzanie bramy**, wybierz pozycję **nagradzanie bramy** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Nagradzanie bramy na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-reward-gateway"></a>Konfigurowanie automatycznej aprowizacji użytkowników w celu nagradzania bramy  

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w bramie z wynagrodzeniem na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć funkcję logowania jednokrotnego opartego na protokole SAML dla bramy nagradzania, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](reward-gateway-tutorial.md)jednokrotne w bramie. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-reward-gateway-in-azure-ad"></a>Aby skonfigurować automatyczną obsługę administracyjną dla bramy nagradzania w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **nagradzanie bramy**.

    ![Link bramy nagradzania na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź wartości w polach adres URL dzierżawy **(v2)** i **tokeny okaziciela OAuth** pobrane wcześniej odpowiednio w **adresie URL dzierżawców** i w **tokenie tajnym** . Kliknij przycisk **Testuj połączenie** , aby upewnić się, że usługa Azure AD może połączyć się z bramą. Jeśli połączenie nie powiedzie się, sprawdź, czy konto bramy ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Kliknij pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz kolejno pozycje **Synchronizuj Azure Active Directory użytkownicy, aby uzyskać dostęp do bramy**.

    ![Zrzut ekranu sekcji mapowania z opcją Synchronizuj Azure Active Directory użytkownicy, aby uzyskać bramę o nazwie out.](media/reward-gateway-provisioning-tutorial/user-mappings.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługą Azure AD, aby uzyskać dostęp do bramy w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w bramie z wynagrodzeniem dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Zrzut ekranu przedstawiający sekcję mapowania atrybutów z wyświetlonymi sześcioma mapowaniami.](media/reward-gateway-provisioning-tutorial/user-attributes.png)

10. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning dla bramy nagradzania, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić bramie, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning na bramie z wynagrodzeniem.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

Brama nagradzania nie obsługuje obecnie udostępniania grup.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)