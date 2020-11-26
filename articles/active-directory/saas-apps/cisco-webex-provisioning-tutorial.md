---
title: 'Samouczek: Konfigurowanie programu Cisco WebEx do automatycznego inicjowania obsługi użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i dezaktywowania kont użytkowników w programie Cisco WebEx.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: bc05e83ac6c7f0f7c5e9a571c1fa7397af858f44
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180102"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie programu Cisco WebEx do automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w programie Cisco WebEx i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników w programie Cisco WebEx.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Dzierżawa Cisco WebEx](https://www.webex.com/pricing/index.html).
* Konto użytkownika w programie Cisco WebEx z uprawnieniami administratora.

## <a name="adding-cisco-webex-from-the-gallery"></a>Dodawanie aplikacji Cisco Webex z galerii

Przed skonfigurowaniem programu Cisco WebEx do automatycznego aprowizacji użytkowników w usłudze Azure AD należy dodać Cisco WebEx z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać Cisco WebEx z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz ciąg **Cisco Webex**, wybierz pozycję **Cisco Webex** w panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

    ![Aplikacja Cisco Webex na liście wyników](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>Przypisywanie użytkowników do Cisco WebEx

Azure Active Directory używa koncepcji o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy usługi Azure AD potrzebują dostępu do rozwiązania Cisco WebEx. Po ustaleniu tych użytkowników można przypisać je do programu Cisco WebEx, postępując zgodnie z poniższymi instrukcjami:

* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Ważne porady dotyczące przypisywania użytkowników do programu Cisco WebEx

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do programu Cisco WebEx w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowi użytkownicy mogą zostać przypisani później.

* Podczas przypisywania użytkownika do programu Cisco WebEx należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Konfigurowanie automatycznej aprowizacji użytkowników w programie Cisco WebEx

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników w programie Cisco WebEx na podstawie przypisań użytkowników w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla programu Cisco WebEx w usłudze Azure AD:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Cisco WebEx**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Cisco Webex**.

    ![Link aplikacji Cisco Webex na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    :::image type="content" source="common/provisioning.png" alt-text="Zrzut ekranu przedstawiający menu w Azure Portal. W obszarze Zarządzanie aprowizacji jest wyróżnione." border="false":::

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    :::image type="content" source="common/provisioning-automatic.png" alt-text="Zrzut ekranu przedstawiający pole listy Tryb aprowizacji z automatycznym wyróżnionym." border="false":::

5. W sekcji **poświadczenia administratora** wprowadź **adres URL dzierżawy** i **token tajny** konta Cisco WebEx.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/secrettoken1.png" alt-text="Zrzut ekranu przedstawiający sekcję poświadczenia administratora. Pola tokenów dzierżawy U R L i Secret są wyróżnione, ale są puste." border="false":::

6.  W polu **adres URL dzierżawy** wprowadź wartość w postaci `https://api.ciscospark.com/v1/scim/[OrgId]` . Aby uzyskać dostęp `[OrgId]` , zaloguj się do [centrum sterowania Cisco WebEx](https://admin.webex.com/login). Kliknij nazwę organizacji w lewym dolnym rogu i skopiuj wartość z **identyfikatora organizacji**. 

    * Aby uzyskać wartość dla **tokenu tajnego**, przejdź do tego [adresu URL](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%2f%2flocalhost%253A3000%2fauth%2fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose). Na wyświetlonej stronie logowania WebEx Zaloguj się przy użyciu pełnego konta administratora Cisco WebEx dla swojej organizacji. Zostanie wyświetlona strona błędu z informacją, że nie można skontaktować się z lokacją, ale jest to normalne.

        :::image type="content" source="./media/cisco-webex-provisioning-tutorial/test.png" alt-text="Zrzut ekranu przedstawiający stronę sieci Web wyświetlającą komunikat o błędzie. Komunikat informuje o tym, że nie można skontaktować się z witryną i zawiera kilka porad dotyczących rozwiązywania problemów." border="false":::
 
    * Skopiuj wartość wygenerowanego tokenu okaziciela z adresu URL, który został wyróżniony poniżej. Ten token jest ważny przez 365 dni.
        
        :::image type="content" source="./media/cisco-webex-provisioning-tutorial/test1.png" alt-text="Zrzut ekranu przedstawiający Long U R L. część adresu jest nierozpoznawalny, ale jest wyróżniony i oznaczony tokenem okaziciela." border="false":::

7. Po wypełnieniu pól przedstawionych w kroku 5 kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą Cisco WebEx. Jeśli połączenie nie powiedzie się, upewnij się, że konto Cisco WebEx ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)
   
8. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

9. Kliknij przycisk **Zapisz**.

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do programu Cisco WebEx**.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/usermapping.png" alt-text="Zrzut ekranu przedstawiający sekcję mapowania w Azure Portal. W obszarze Nazwa zsynchronizuj Azure Active Directory użytkownicy z CiscoSpark jest wyróżniony." border="false":::

11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Cisco WebEx w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do kont użytkowników w programie Cisco WebEx for Update. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/usermappingattributes.png" alt-text="Zrzut ekranu sekcji mapowania atrybutów przedstawiający atrybuty Azure Active Directory, odpowiednie atrybuty CiscoSpark i pasujący stan." border="false":::

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla programu Cisco WebEx, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić do firmy Cisco WebEx, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w programie Cisco WebEx.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Cisco WebEx jest obecnie w fazie testowania wczesnego (EFT) firmy Cisco. Aby uzyskać więcej informacji, skontaktuj się z [zespołem pomocy technicznej firmy Cisco](https://www.webex.co.in/support/support-overview.html). 
* Więcej informacji na temat konfiguracji Cisco WebEx można znaleźć [w dokumentacji firmy](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub)Cisco.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
