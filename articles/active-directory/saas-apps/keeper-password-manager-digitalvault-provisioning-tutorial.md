---
title: 'Samouczek: Konfigurowanie Menedżera haseł programu opiekuna & magazynem cyfrowym dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników dla programu opiekuna haseł & magazynu cyfrowego.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 2670dc0cb56805a2afa966bee1d2aa52b6c8e46a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358987"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Menedżera haseł programu opiekuna & magazynem cyfrowym dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w Menedżerze haseł opiekuna & magazynu cyfrowego i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do Menedżera haseł programu opiekuna & magazynu cyfrowego.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Menedżer haseł opiekuna & dzierżawy magazynu cyfrowego](https://keepersecurity.com/pricing.html?t=e)
* Konto użytkownika w Menedżerze haseł opiekuna & magazyn cyfrowy z uprawnieniami administratora.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Dodawanie Menedżera haseł programu opiekuna & magazynu cyfrowego z galerii

Przed skonfigurowaniem usługi Menedżer haseł programu opiekuna & magazynem cyfrowym w celu automatycznego aprowizacji użytkowników w usłudze Azure AD należy dodać Menedżera haseł programu opiekuna & magazynu cyfrowego z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać Menedżera haseł programu opiekuna & magazynu cyfrowego z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź wartość **Menedżer haseł programu opiekuna & magazynie cyfrowym**, wybierz pozycję **Menedżer haseł opiekuna & magazyn cyfrowy** w panelu wyniki, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Usługa Keeper Password Manager & Digital Vault na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Przypisywanie użytkowników do Menedżera haseł programu opiekuna & magazynu cyfrowego

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do Menedżera haseł opiekuna & magazynie cyfrowym. Po ustaleniu tych użytkowników i/lub grup do Menedżera haseł programu opiekuna & magazynu cyfrowego, postępując zgodnie z poniższymi instrukcjami:

* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Ważne porady dotyczące przypisywania użytkowników do Menedżera haseł programu opiekuna & magazynu cyfrowego

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do Menedżera haseł opiekuna & cyfrowy magazyn do testowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do Menedżera haseł programu opiekuna & magazynu cyfrowego należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Konfigurowanie automatycznego aprowizacji użytkowników do Menedżera haseł programu opiekuna & magazynu cyfrowego 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w Menedżerze haseł opiekunów & magazynu cyfrowego na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć funkcję logowania jednokrotnego opartego na protokole SAML dla Menedżera haseł programu opiekuna & magazynie cyfrowym, postępując zgodnie z instrukcjami podanymi w [Menedżerze haseł opiekuna & użyciu logowania](keeperpasswordmanager-tutorial.md)jednokrotnego w magazynie cyfrowym. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla Menedżera haseł programu opiekuna & magazynie cyfrowym w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Keeper Password Manager & Digital Vault**.

    ![Link do usługi Keeper Password Manager & Digital Vault na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź **adres URL dzierżawy** i **klucz tajny** Menedżera haseł opiekuna & konto magazynu cyfrowego zgodnie z opisem w kroku 6.

6. Zaloguj się do [konsoli administracyjnej programu opiekuna](https://keepersecurity.com/console/#login). Kliknij pozycję **administrator** i wybierz istniejący węzeł lub Utwórz nowy. Przejdź do karty **Inicjowanie obsługi administracyjnej** i wybierz pozycję **Dodaj metodę**.

    ![Konsola administracyjna opiekuna](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Wybierz pozycję **Standard scim (system do zarządzania tożsamościami między domenami**.

    ![Dodaj Standard scim](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Kliknij pozycję **Utwórz token aprowizacji**.

    ![Punkt końcowy tworzenia opiekuna](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Skopiuj wartości dla **adresu URL** i **tokenu** , a następnie wklej je do **adresu URL dzierżawy** i **tokenu tajnego** w usłudze Azure AD. Kliknij przycisk **Zapisz** , aby zakończyć instalację aprowizacji na opiekuna.

    ![Tworzenie tokenu przez opiekuna](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. Po wypełnieniu pól przedstawionych w kroku 5 kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z menedżerem haseł opiekuna & magazynu cyfrowego Jeśli połączenie nie powiedzie się, upewnij się, że Menedżer haseł opiekuna & konto magazynu cyfrowego ma uprawnienia administratora i spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

8. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

9. Kliknij pozycję **Zapisz**.

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do programu opiekuna Password Manager & magazynu cyfrowego**.

    ![Mapowania użytkowników opiekunów](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD z menedżerem haseł opiekuna & magazynie cyfrowym w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania kont użytkowników w Menedżerze haseł opiekuna & magazynu cyfrowego dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Atrybuty użytkownika opiekuna](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do Menedżera haseł programu opiekuna & magazynu cyfrowego**.

    ![Mapowania grup opiekunów](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD z menedżerem haseł opiekuna & magazynie cyfrowym w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w Menedżerze haseł opiekuna & magazynu cyfrowego dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Atrybuty grupy opiekunów](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługę Azure AD aprowizacji dla Menedżera haseł programu opiekuna & magazynie cyfrowym, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

16. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić menedżerowi haseł programu opiekuna & magazynu cyfrowego, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

17. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w Menedżerze haseł opiekuna & magazynem cyfrowym.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Menedżer haseł programu opiekuna & magazyn cyfrowy wymaga, aby **wiadomości e-mail** i **Nazwa użytkownika** miały taką samą wartość Source, ponieważ wszystkie aktualizacje atrybutów spowodują modyfikację drugiej wartości.
* Menedżer haseł programu opiekuna & magazyn cyfrowy nie obsługuje usuwania użytkowników, należy wyłączyć. Użytkownicy niepełnosprawni będą wyświetlani jako Zablokowani w interfejsie użytkownika konsoli administracyjnej programu opiekuna.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

