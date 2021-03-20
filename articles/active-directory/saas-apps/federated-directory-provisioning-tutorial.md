---
title: 'Samouczek: Konfigurowanie katalogu federacyjnego dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników w katalogu federacyjnym.
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
ms.openlocfilehash: 8ca7654d930247f70d85cbc20fbbeb961223f05f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95998366"
---
# <a name="tutorial-configure-federated-directory-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie katalogu federacyjnego dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w katalogu federacyjnym i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup w katalogu federacyjnym.

> [!NOTE]
>  Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD.
* [Katalog federacyjny](https://www.federated.directory/pricing).
* Konto użytkownika w katalogu federacyjnym z uprawnieniami administratora.

## <a name="assign-users-to-federated-directory"></a>Przypisywanie użytkowników do katalogu federacyjnego
Azure Active Directory używa koncepcji zwanej zadaniami w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do katalogu federacyjnego. Po ustaleniu tych użytkowników i/lub grup można przypisać do katalogu federacyjnego, postępując zgodnie z poniższymi instrukcjami:

 * [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md) 
 
 ## <a name="important-tips-for-assigning-users-to-federated-directory"></a>Ważne porady dotyczące przypisywania użytkowników do katalogu federacyjnego
 * Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do katalogu federacyjnego w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do katalogu federacyjnego należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji.
    
 ## <a name="set-up-federated-directory-for-provisioning"></a>Konfigurowanie katalogu federacyjnego na potrzeby aprowizacji

Przed skonfigurowaniem katalogu federacyjnego do automatycznego aprowizacji użytkowników w usłudze Azure AD należy włączyć obsługę Standard scim w katalogu federacyjnym.

1. Zaloguj się do [konsoli administracyjnej katalogu federacyjnego](https://federated.directory/of)

    :::image type="content" source="media/federated-directory-provisioning-tutorial/companyname.png" alt-text="Zrzut ekranu konsoli administracyjnej katalogu federacyjnego przedstawiający pole umożliwiające wprowadzenie nazwy firmy. Przyciski logowania są również widoczne." border="false":::

2. Przejdź do **katalogu > katalogi użytkowników** i wybierz swoją dzierżawę. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/ad-user-directories.png" alt-text="Zrzut ekranu konsoli administracyjnej katalogu federacyjnego z wyróżnioną pozycją katalogi i katalog federacyjny Azure A D." border="false":::

3.  Aby wygenerować trwały token okaziciela, przejdź do **katalogu klucze > Utwórz nowy klucz.** 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated01.png" alt-text="Zrzut ekranu przedstawiający stronę klucze katalogu w konsoli administracyjnej katalogu federacyjnego. Przycisk Utwórz nowy klucz jest wyróżniony." border="false":::

4. Utwórz klucz katalogu. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated02.png" alt-text="Zrzut ekranu przedstawiający stronę Tworzenie klucza katalogu w konsoli administracyjnej katalogu federacyjnego przy użyciu pól Nazwa i opis oraz przycisk Utwórz klucz." border="false":::
    

5. Skopiuj wartość **tokenu dostępu** . Ta wartość zostanie wprowadzona w polu **token tajny** na karcie aprowizacji aplikacji katalogu federacyjnego w Azure Portal. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated03.png" alt-text="Zrzut ekranu strony w konsoli administracyjnej katalogu federacyjnego. Widoczny jest symbol zastępczy tokenu dostępu oraz nazwa klucza, opis i wystawca." border="false":::
    
## <a name="add-federated-directory-from-the-gallery"></a>Dodawanie katalogu federacyjnego z galerii

Aby skonfigurować katalog federacyjny do automatycznej aprowizacji użytkowników w usłudze Azure AD, musisz dodać katalog federacyjny z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać katalog federacyjny z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **katalog federacyjny**, a następnie wybierz pozycję **katalog federacyjny** w panelu wyniki.

    ![Katalog federacyjny na liście wyników](common/search-new-app.png)

5. Przejdź do **adresu URL** wyróżnionego poniżej w oddzielnej przeglądarce. 

    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage1.png" alt-text="Zrzut ekranu przedstawiający stronę w Azure Portal, która wyświetla informacje w katalogu federacyjnym. Wartość U R L jest wyróżniona." border="false":::

6. Kliknij przycisk **Zaloguj**.

    :::image type="content" source="media/federated-directory-provisioning-tutorial/federated04.png" alt-text="Zrzut ekranu przedstawiający menu główne w lokacji katalogu federacyjnego. Przycisk Zaloguj jest wyróżniony." border="false":::

7.  Ponieważ katalog federacyjny jest aplikacją OpenIDConnect, wybierz logowanie do katalogu federacyjnego przy użyciu konta służbowego firmy Microsoft.
    
    :::image type="content" source="media/federated-directory-provisioning-tutorial/loginpage3.png" alt-text="Zrzut ekranu przedstawiający stronę testową S C I D w lokacji katalogu federacyjnego. Zaloguj się przy użyciu konto Microsoft zostanie wyróżniony." border="false":::
 
8. Po pomyślnym uwierzytelnieniu Zaakceptuj monit o zgodę na stronie zgody. Aplikacja zostanie następnie automatycznie dodana do dzierżawy i nastąpi przekierowanie do konta katalogu federacyjnego.

    ![Dodawanie Standard scim do katalogu federacyjnego](media/federated-directory-provisioning-tutorial/premission.png)



## <a name="configuring-automatic-user-provisioning-to-federated-directory"></a>Konfigurowanie automatycznego aprowizacji użytkowników w katalogu federacyjnym 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w katalogu federacyjnym na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-federated-directory-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej dla katalogu federacyjnego w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **katalog federacyjny**.

    ![Link katalogu federacyjnego na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z opcją aprowizacji o nazwie out.](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający listę rozwijaną trybu aprowizacji z opcją automatyczną o nazwie out.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź `https://api.federated.directory/v2/` adres URL dzierżawy. Wprowadź wartość, która została pobrana i zapisana wcześniej z katalogu federacyjnego w **tokenie tajnym**. Kliknij pozycję **Testuj połączenie** , aby zapewnić, że usługa Azure AD może połączyć się z katalogiem federacyjnym. Jeśli połączenie nie powiedzie się, upewnij się, że konto katalogu federacyjnego ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)

8. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

9. Kliknij pozycję **Zapisz**.

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników z katalogiem federacyjnym**.

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-mappings.png" alt-text="Zrzut ekranu przedstawiający sekcję mapowania. W obszarze Nazwa zsynchronizuj Azure Active Directory użytkowników z katalogiem federacyjnym." border="false":::
    
    
11. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do katalogu federacyjnego w sekcji **Mapowanie atrybutu** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w katalogu federacyjnym dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    :::image type="content" source="media/federated-directory-provisioning-tutorial/user-attributes.png" alt-text="Zrzut ekranu przedstawiający stronę mapowania atrybutów. W tabeli wymieniono Azure Active Directory i atrybuty katalogu federacyjnego oraz stan pasujący." border="false":::
    

12. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla katalogu federacyjnego, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

14. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić katalog federacyjny, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w katalogu federacyjnym.

Aby uzyskać więcej informacji na temat odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md)
## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
