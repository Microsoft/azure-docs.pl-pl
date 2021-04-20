---
title: 'Samouczek: konfigurowanie usługi Zscaler do automatycznego aprowizowania użytkowników za pomocą Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie aprowizować i deprowizować konta użytkowników w aplikacji Zscaler.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 92553e9d598823ee1b812a4f07480dec380b2490
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739426"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Samouczek: konfigurowanie usługi Zscaler do automatycznego aprowizowania użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w usługach Zscaler i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizowania użytkowników i/lub grup w usłudze Zscaler.

> [!NOTE]
> W tym samouczku opisano łącznik zbudowany na podstawie usługi Azure AD User Provisioning Service. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>

## <a name="prerequisites"></a>Wymagania wstępne

W scenariuszu opisanym w tym samouczku założono, że masz już następujące elementy:

* Dzierżawa usługi Azure AD.
* Dzierżawa usługi Zscaler.
* Konto użytkownika w aplikacji Zscaler z uprawnieniami administratora.

> [!NOTE]
> Integracja aprowizowania usługi Azure AD opiera się na interfejsie API Zscaler SCIM, który jest dostępny dla deweloperów rozwiązania Zscaler dla kont z pakietem Enterprise.

## <a name="adding-zscaler-from-the-gallery"></a>Dodawanie aplikacji Zscaler z galerii

Przed skonfigurowaniem rozwiązania Zscaler do automatycznego aprowizowania użytkowników w usłudze Azure AD należy dodać aplikację Zscaler z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Zscaler z galerii aplikacji usługi Azure AD, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Zscaler,** wybierz pozycję **Zscaler** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Zscaler na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Przypisywanie użytkowników do aplikacji Zscaler

Azure Active Directory używa koncepcji o nazwie "przypisania", aby określić, którzy użytkownicy powinni uzyskać dostęp do wybranych aplikacji. W kontekście automatycznego aprowizowania użytkowników synchronizowane są tylko użytkownicy i/lub grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznego aprowizowania użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD muszą mieć dostęp do rozwiązania Zscaler. Po zakończeniu decyzji możesz przypisać tych użytkowników i/lub grupy do aplikacji Zscaler, korzystając z poniższych instrukcji:

* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Ważne wskazówki dotyczące przypisywania użytkowników do aplikacji Zscaler

* Zaleca się, aby do rozwiązania Zscaler został przypisany jeden użytkownik usługi Azure AD w celu przetestowania konfiguracji automatycznej aprowności użytkowników. Dodatkowi użytkownicy i/lub grupy mogą zostać przypisani później.

* Podczas przypisywania użytkownika do aplikacji Zscaler należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z rolą **Dostęp domyślny** są wykluczeni z aprowowania.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Konfigurowanie automatycznego aprowizowania użytkowników w aplikacji Zscaler

Ta sekcja zawiera instrukcje konfigurowania usługi aprowizowania usługi Azure AD w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w usłudze Zscaler na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.


> [!NOTE]
> Otwórz bilet [pomocy technicznej,](https://help.zscaler.com/) aby utworzyć domenę w aplikacji Zscaler.

> [!TIP]
> Możesz również włączyć logowanie pojedynczej oparte na saml dla usługi Zscaler, zgodnie z instrukcjami podanymi w samouczku dotyczącym logowania pojedynczej usługi [Zscaler.](zscaler-tutorial.md) Logowanie pojedynczego użytkownika można skonfigurować niezależnie od automatycznej aprowiwizowania użytkowników, chociaż te dwie funkcje uzupełniają się nawzajem.

> [!NOTE]
> Gdy użytkownicy i grupy są aprowizowana lub co najmniej raz aprowizowana, zaleca się okresowe ponowne uruchamianie aprowowania, aby upewnić się, że członkostwo w grupach jest prawidłowo aktualizowane. Wykonanie ponownego uruchomienia wymusi, że nasza usługa ponownie oceni wszystkie grupy i zaktualizuje członkostwo. 

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Aby skonfigurować automatyczną aprowizowanie użytkowników dla rozwiązania Zscaler w usłudze Azure AD:

1. Zaloguj się do aplikacji [Azure Portal](https://portal.azure.com) aplikacje dla **przedsiębiorstw,** wybierz pozycję **Wszystkie aplikacje,** a następnie wybierz **pozycję Zscaler.**

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Zscaler.**

    ![Link do aplikacji Zscaler na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający pasek boczny Zscaler — Provisioning Enterprise Application z wyróżnioną opcją Aprowowanie.](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający stronę aprowizowania z trybem aprowizowania ustawionym na Wartość automatyczna.](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. W sekcji **Poświadczenia administratora wprowadź** adres **URL** dzierżawy i **token** tajny konta usługi Zscaler zgodnie z opisem w kroku 6.

6. Aby uzyskać adres **URL dzierżawy** i **token** wpisów tajnych, przejdź do > **administration > Authentication Settings** w interfejsie użytkownika portalu Zscaler i kliknij pozycję **SAML** w obszarze **Typ uwierzytelniania.**

    ![Zrzut ekranu przedstawiający stronę Ustawienia uwierzytelniania.](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Kliknij pozycję **Konfiguruj saml,** aby otworzyć **opcje konfiguracji SAML.**

    ![Zrzut ekranu przedstawiający okno dialogowe Configure S A M L (Konfigurowanie aplikacji S A M L) z polami tekstowymi Base U R L i Bearer Token (Token okaziciela).](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Wybierz **pozycję Włącz SCIM-Based aprowizowanie,** aby pobrać podstawowy adres **URL** i **token bearer,** a następnie zapisz ustawienia. Skopiuj podstawowy **adres URL do** adresu URL **dzierżawy** i **token bearer do** **tokenu** tajnego w Azure Portal.

7. Po wypełniniu pól widocznych w kroku 5 kliknij pozycję **Testuj** połączenie, aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą Zscaler. Jeśli połączenie nie powiedzie się, upewnij się, że twoje konto Zscaler ma uprawnienia administratora, i spróbuj ponownie.

    ![Zrzut ekranu przedstawiający sekcję Poświadczenia administratora z wywołaną opcją Testuj połączenie.](./media/zscaler-provisioning-tutorial/test-connection.png)

8. W polu **Wiadomość e-mail** z powiadomieniem wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach aprowizowania, i zaznacz pole wyboru Wyślij powiadomienie e-mail w przypadku wystąpienia **błędu.**

    ![Zrzut ekranu przedstawiający pole tekstowe Wiadomość e-mail z powiadomieniem.](./media/zscaler-provisioning-tutorial/notification.png)

9. Kliknij pozycję **Zapisz**.

10. W sekcji **Mapowania** wybierz pozycję **Synchronizuj użytkowników Azure Active Directory z programem Zscaler.**

    ![Zrzut ekranu przedstawiający sekcję Mapowania z wyróżnioną opcją Synchronizuj Azure Active Directory users to Zscaler.](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Przejrzyj atrybuty użytkownika synchronizowane z usługi Azure AD do rozwiązania Zscaler w **sekcji Mapowanie** atrybutów. Atrybuty wybrane jako pasujące **właściwości** są używane do dopasowywania kont użytkowników w programie Zscaler w przypadku operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Zrzut ekranu przedstawiający sekcję Mapowania atrybutów z wyświetlonymi siedmiu mapowań.](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. W sekcji **Mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory z programem Zscaler.**

    ![Zrzut ekranu przedstawiający sekcję Mapowania z wyróżnioną opcją Synchronizuj grupy Azure Active Directory z rozwiązaniem Zscaler.](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do rozwiązania Zscaler w **sekcji Mapowanie** atrybutów. Atrybuty wybrane jako właściwości **dopasowania** są używane do dopasowywania grup w programie Zscaler dla operacji aktualizacji. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

    ![Zrzut ekranu przedstawiający sekcję Mapowania atrybutów z wyświetlonymi trzema mapowaniami.](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługę aprowizowania usługi  Azure AD dla rozwiązania Zscaler, zmień stan inicjowania obsługi na **Wł.** w **sekcji** Ustawienia.

    ![Zrzut ekranu przedstawiający opcję Stan aprowowania ustawioną na Wł.](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Zdefiniuj użytkowników i/lub grupy, które chcesz aprowizować  w aplikacji Zscaler, wybierając odpowiednie wartości w sekcji **Zakres w sekcji** Ustawienia.

    ![Zrzut ekranu przedstawiający ustawienie Zakres z wyróżnioną opcją Synchronizuj tylko przypisanych użytkowników i grupy.](./media/zscaler-provisioning-tutorial/scoping.png)

17. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zrzut ekranu przedstawiający pasek boczny Zscaler — Provisioning Enterprise Application z wywołaną opcją Zapisz.](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Ta operacja rozpoczyna początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **sekcji** Zakres **w ustawieniach.** Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które odbywają się co około 40 minut, o ile usługa aprowowania Azure AD jest uruchomiona. Za pomocą sekcji Szczegóły **synchronizacji** możesz monitorować postęp i korzystać z linków do raportu działań aprowizowania, w którym opisano wszystkie akcje wykonywane przez usługę aprowowania Azure AD w usłudze Zscaler.

Aby uzyskać więcej informacji na temat odczytywania dzienników aprowizowania usługi Azure AD, zobacz [Reporting on automatic user account provisioning](../app-provisioning/check-status-user-account-provisioning.md)(Raportowanie dotyczące automatycznego aprowizowania kont użytkowników).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png
