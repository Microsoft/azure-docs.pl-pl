---
title: 'Samouczek: konfigurowanie usługi Dropbox dla Firm na potrzeby automatycznego aprowizowania użytkowników za pomocą Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie aprowizować i deprowizować konta użytkowników w usłudze Dropbox dla Firm.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 4e718ef30d029995c49efe36f19a6efcf7de0616
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589605"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Samouczek: konfigurowanie usługi Dropbox dla Firm na potrzeby automatycznej aprowrowi dla użytkowników

Celem tego samouczka jest zademonstrować kroki do wykonania w usługach Dropbox dla Firm i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizować i wyłączać aprowizować użytkowników i/lub grupy w usłudze Dropbox dla Firm.

> [!IMPORTANT]
> W przyszłości firmy Microsoft i Dropbox wycofają starą integrację z usługą Dropbox. Pierwotnie zaplanowano to na 1.04.2021 r., ale zostało przesunięte na czas nieokreślony. Jednak aby uniknąć przerw w działaniu usługi, zalecamy migrowanie do nowej integracji scim 2.0 Dropbox, która obsługuje grupy. Aby przeprowadzić migrację do nowej integracji z usługą Dropbox, dodaj i skonfiguruj nowe wystąpienie usługi Dropbox na celu aprowizowanie w dzierżawie usługi Azure AD, korzystając z poniższych kroków. Po skonfigurowaniu nowej integracji z usługą Dropbox wyłącz opcję Aprowizowanie w starej integracji z usługą Dropbox, aby uniknąć konfliktów aprowowania. Aby uzyskać bardziej szczegółowe instrukcje dotyczące migracji do nowej integracji z usługą Dropbox, zobacz [Update to the newest Dropbox for Business application using Azure AD](https://help.dropbox.com/installs-integrations/third-party/update-dropbox-azure-ad-connector)(Aktualizowanie do najnowszej aplikacji Dropbox dla Firm przy użyciu usługi Azure AD).

> [!NOTE]
> W tym samouczku opisano łącznik zbudowany na podstawie usługi Azure AD User Provisioning Service. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

W scenariuszu opisanym w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa usługi Dropbox dla Firm](https://www.dropbox.com/business/pricing)
* Konto użytkownika w usłudze Dropbox dla Firm z uprawnieniami administratora.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Dodawanie aplikacji Dropbox dla Firm z galerii

Przed skonfigurowaniem usługi Dropbox dla Firm na potrzeby automatycznej aprowrowi użytkowników w usłudze Azure AD należy dodać aplikację Dropbox dla Firm z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Dropbox dla Firm z galerii aplikacji usługi Azure AD, wykonaj następujące kroki:**

1. Na **[Azure Portal](https://portal.azure.com)** panelu nawigacyjnym po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Dropbox** dla Firm, wybierz **pozycję Dropbox dla** Firm w panelu wyników, a następnie kliknij przycisk Dodaj, aby dodać aplikację. 

    ![Aplikacja Dropbox dla Firm na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Przypisywanie użytkowników do usługi Dropbox dla Firm

Azure Active Directory używa koncepcji o nazwie *przypisania,* aby określić, którzy użytkownicy powinni uzyskać dostęp do wybranych aplikacji. W kontekście automatycznego aprowizowania użytkowników synchronizowane są tylko użytkownicy i/lub grupy przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznego aprowizowania użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD muszą mieć dostęp do usługi Dropbox dla Firm. Po zakończeniu decyzji możesz przypisać tych użytkowników i/lub grupy do usługi Dropbox dla Firm, zgodnie z instrukcjami podanymi tutaj:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Ważne wskazówki dotyczące przypisywania użytkowników do usługi Dropbox dla Firm

* Zaleca się, aby do usługi Dropbox dla Firm został przypisany jeden użytkownik usługi Azure AD w celu przetestowania konfiguracji automatycznej aprowności użytkowników. Dodatkowi użytkownicy i/lub grupy mogą zostać przypisani później.

* Podczas przypisywania użytkownika do usługi Dropbox dla Firm musisz wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **Dostęp domyślny** są wykluczeni z aprowizowania.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Konfigurowanie automatycznego aprowizowania użytkowników w usłudze Dropbox dla Firm 

Ta sekcja zawiera instrukcje konfigurowania usługi aprowizowania usługi Azure AD w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w usłudze Dropbox dla Firm na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie pojedyncze oparte na saml dla aplikacji Dropbox dla Firm, zgodnie z instrukcjami podanymi w samouczku dotyczącym logowania pojedynczego usługi [Dropbox dla Firm.](dropboxforbusiness-tutorial.md) Logowanie pojedynczego użytkownika można skonfigurować niezależnie od automatycznego aprowizowania użytkowników, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Aby skonfigurować automatyczną aprowizowanie użytkowników dla usługi Dropbox dla Firm w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Dropbox dla Firm.**

    ![Link aplikacji Dropbox dla Firm na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcję Zarządzaj opcjami z wywołaną opcją Aprownia.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną Tryb aprowizowania z wywołaną opcją Automatyczne.](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora** kliknij pozycję **Autoryzuj**. Spowoduje to otwarcie okna dialogowego logowania do usługi Dropbox dla Firm w nowym oknie przeglądarki.

    ![Inicjowanie obsługi ](common/provisioning-oauth.png)

6. W **oknie dialogowym Logowanie do usługi Dropbox dla Firm** w celu połączenia z usługą Azure AD zaloguj się do dzierżawy usługi Dropbox dla Firm i zweryfikuj swoją tożsamość.

    ![Logowanie do aplikacji Dropbox dla Firm](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. Po wykonaniu kroków 5 i 6 kliknij pozycję **Testuj** połączenie, aby upewnić się, że usługa Azure AD może połączyć się z usługą Dropbox dla Firm. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Dropbox dla Firm ma uprawnienia administratora, i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-oauth.png)

8. W polu **Wiadomość e-mail** z powiadomieniem wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach aprowizowania, i zaznacz pole wyboru — Wyślij powiadomienie e-mail w przypadku wystąpienia **błędu.**

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

9. Kliknij pozycję **Zapisz**.

10. W sekcji **Mapowania wybierz** pozycję Synchronizuj użytkowników Azure Active Directory **z usługą Dropbox.**

    ![Mapowania użytkowników usługi Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Przejrzyj atrybuty użytkownika synchronizowane z usługi Azure AD do usługi Dropbox w **sekcji Mapowanie** atrybutów. Atrybuty wybrane jako Pasujące **właściwości** są używane do dopasowywania kont użytkowników w usłudze Dropbox w przypadku operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty użytkownika usługi Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. W sekcji **Mapowania wybierz** pozycję Synchronizuj grupy Azure Active Directory **z usługą Dropbox.**

    ![Mapowania grup usługi Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do usługi Dropbox, w **sekcji Mapowanie** atrybutów. Atrybuty wybrane jako Właściwości **dopasowania** są używane do dopasowywania grup w usłudze Dropbox na czas operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Atrybuty grupy Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługę aprowizowania Azure AD dla usługi Dropbox, zmień stan **aprowowania** na **Wł.** w **sekcji** Ustawienia.

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

16. Zdefiniuj użytkowników i/lub grupy, które chcesz aprowizować w usłudze Dropbox, wybierając odpowiednie wartości w sekcji **Zakres w sekcji** Ustawienia. 

    ![Zakres aprowizacji](common/provisioning-scope.png)

17. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w **sekcji** Ustawienia. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które odbywają się co około 40 minut, o ile usługa aprowności Azure AD jest uruchomiona. Możesz użyć sekcji Szczegóły **synchronizacji,** aby monitorować postęp i korzystać z linków do raportu działań aprowiwizowania, który opisuje wszystkie akcje wykonywane przez usługę aprowizowania Azure AD w usłudze Dropbox.

Aby uzyskać więcej informacji na temat odczytywania dzienników aprowizowania usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego aprowizowania kont użytkowników.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Ograniczenia łącznika
 
* Usługa Dropbox nie obsługuje zawieszania zapraszanych użytkowników. Jeśli zaproszony użytkownik zostanie zawieszony, ten użytkownik zostanie usunięty.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

