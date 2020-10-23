---
title: 'Samouczek: Konfigurowanie dodatkowych funkcji automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 33329fc30d1c20b4e20901d06a061accb2aeceb0
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92456905"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie dodatkowych funkcji automatycznej aprowizacji użytkowników

Celem tego samouczka jest zademonstrowanie kroków, które należy wykonać w celu uzyskania dodatkowych Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące elementy:

* Dzierżawa usługi Azure AD
* [Dzierżawa](https://bonus.ly/pricing)
* Konto użytkownika w niekorzyść z uprawnieniami administratora

> [!NOTE]
> Integracja z obsługą administracyjną usługi Azure AD opiera się na [dodatkowym interfejsie API REST](https://konghq.com/solutions/gateway/), który jest dostępny w celu uzyskania dodatkowych deweloperów.

## <a name="adding-bonusly-from-the-gallery"></a>Dodatkowe Dodawanie z galerii

Przed przystąpieniem do samoobsługowego konfigurowania funkcji automatycznego aprowizacji użytkowników w usłudze Azure AD należy dodać ją z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać dodatkowe elementy z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **premię** **, wybierz pozycję** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Dodatkowa lista wyników](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Przypisywanie użytkowników do dodatkowych

Azure Active Directory używa koncepcji o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD. 

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD muszą mieć dostęp do dodatkowych potrzeb. Po ustaleniu tych użytkowników i/lub grup można przypisywać je do dodatkowych, postępując zgodnie z poniższymi instrukcjami:

* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Ważne porady dotyczące przypisywania użytkowników do dodatkowych

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* W przypadku przypisywania użytkownika do dodatkowych opcji, należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Konfigurowanie automatycznej aprowizacji użytkowników w celu uzyskania premii

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu utworzenia, zaktualizowania i wyłączenia użytkowników i/lub grup w sposób niezależny od przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć funkcję jednokrotnego logowania opartego na protokole SAML, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](bonus-tutorial.md)jednokrotne. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej dla użytkowników w usłudze Azure AD:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **dodatkowe**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz opcję **dodatkowe**.

    ![Link dodatkowy na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningTab.png" alt-text="Zrzut ekranu przedstawiający kartę z dodatkową obsługą. W obszarze Zarządzanie aprowizacji jest wyróżnione." border="false":::

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png" alt-text="Zrzut ekranu przedstawiający kartę z dodatkową obsługą. W obszarze Zarządzanie aprowizacji jest wyróżnione." border="false":::

5. W sekcji **poświadczenia administratora** wprowadź **klucz tajny** konta z premią, zgodnie z opisem w kroku 6.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/secrettoken.png" alt-text="Zrzut ekranu przedstawiający kartę z dodatkową obsługą. W obszarze Zarządzanie aprowizacji jest wyróżnione." border="false":::

6. **Token tajny** dla konta z premią znajduje się w obszarze **administrator > integracji > firmy**. W sekcji **Jeśli chcesz wykonać kod** , kliknij pozycję **interfejs API > Utwórz nowy token dostępu interfejsu API** , aby utworzyć nowy token klucza tajnego.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png" alt-text="Zrzut ekranu przedstawiający kartę z dodatkową obsługą. W obszarze Zarządzanie aprowizacji jest wyróżnione." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/BonsulyRestApi.png" alt-text="Zrzut ekranu przedstawiający kartę z dodatkową obsługą. W obszarze Zarządzanie aprowizacji jest wyróżnione." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/CreateToken.png" alt-text="Zrzut ekranu przedstawiający kartę z dodatkową obsługą. W obszarze Zarządzanie aprowizacji jest wyróżnione." border="false":::

7. Na poniższym ekranie wpisz nazwę tokenu dostępu w podanym polu tekstowym, a następnie naciśnij pozycję **Utwórz klucz interfejsu API**. Nowy token dostępu zostanie wyświetlony przez kilka sekund w wyskakującym okienku.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/Token01.png" alt-text="Zrzut ekranu przedstawiający kartę z dodatkową obsługą. W obszarze Zarządzanie aprowizacji jest wyróżnione." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/Token02.png" alt-text="Zrzut ekranu przedstawiający kartę z dodatkową obsługą. W obszarze Zarządzanie aprowizacji jest wyróżnione." border="false":::

8. Po wypełnieniu pól przedstawionych w kroku 5 kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać dodatkowe połączenie. Jeśli połączenie nie powiedzie się, upewnij się, że konto z dodatkowymi uprawnieniami administratora i spróbuj ponownie.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/TestConnection.png" alt-text="Zrzut ekranu przedstawiający kartę z dodatkową obsługą. W obszarze Zarządzanie aprowizacji jest wyróżnione." border="false":::

9. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/EmailNotification.png" alt-text="Zrzut ekranu przedstawiający kartę z dodatkową obsługą. W obszarze Zarządzanie aprowizacji jest wyróżnione." border="false":::

10. Kliknij pozycję **Zapisz**.

11. W sekcji **mapowania** wybierz kolejno pozycje **Synchronizuj Azure Active Directory użytkownicy, aby uzyskać premię**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/UserMappings.png" alt-text="Zrzut ekranu przedstawiający kartę z dodatkową obsługą. W obszarze Zarządzanie aprowizacji jest wyróżnione." border="false":::

12. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługą Azure AD, aby uzyskać dodatkową wartość w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu uzyskania dodatkowych odpowiedników kont użytkowników w przypadku operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/UserAttributeMapping.png" alt-text="Zrzut ekranu przedstawiający kartę z dodatkową obsługą. W obszarze Zarządzanie aprowizacji jest wyróżnione." border="false":::

13. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Aby włączyć usługę Azure AD Provisioning w celu zapewnienia premii, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningStatus.png" alt-text="Zrzut ekranu przedstawiający kartę z dodatkową obsługą. W obszarze Zarządzanie aprowizacji jest wyróżnione." border="false":::

15. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ScopeSync.png" alt-text="Zrzut ekranu przedstawiający kartę z dodatkową obsługą. W obszarze Zarządzanie aprowizacji jest wyróżnione." border="false":::

16. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/SaveProvisioning.png" alt-text="Zrzut ekranu przedstawiający kartę z dodatkową obsługą. W obszarze Zarządzanie aprowizacji jest wyróżnione." border="false":::

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Za pomocą sekcji **szczegóły synchronizacji** można monitorować postęp i postępować zgodnie z raportem o aktywności aprowizacji, który opisuje wszystkie akcje wykonywane w ramach usługi Azure AD Provisioning.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png