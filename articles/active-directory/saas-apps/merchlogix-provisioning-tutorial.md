---
title: 'Samouczek: Konfigurowanie MerchLogix dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze MerchLogix.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.openlocfilehash: 9be2205ad0664d58c7a2ef0c07481b1c7aa02402
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91273351"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie MerchLogix na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w MerchLogix i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do MerchLogix.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* Dzierżawa MerchLogix
* Skontaktuj się z pomocą techniczną w witrynie MerchLogix, który może dostarczyć adres URL punktu końcowego Standard scim i token tajny wymagany do aprowizacji użytkowników

## <a name="adding-merchlogix-from-the-gallery"></a>Dodawanie MerchLogix z galerii

Przed skonfigurowaniem usługi MerchLogix do automatycznego aprowizacji użytkowników w usłudze Azure AD należy dodać MerchLogix z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

**Aby dodać MerchLogix z galerii aplikacji usługi Azure AD, wykonaj następujące czynności:**

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym kliknij ikonę **Azure Active Directory** . 

    ![Przycisk Azure Active Directory][1]

2. Przejdź do **aplikacji dla przedsiębiorstw**  >  **wszystkie aplikacje**.

    ![Sekcja aplikacje dla przedsiębiorstw][2]

3. Aby dodać MerchLogix, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **MerchLogix**.

5. W panelu wyników wybierz pozycję **MerchLogix**, a następnie kliknij przycisk **Dodaj** , aby dodać MerchLogix do listy aplikacji SaaS.

    ![Zrzut ekranu przedstawiający dodanie z sekcji Korekta szpaltowa z polem tekstowym wprowadź nazwę o nazwie.][4]

## <a name="assigning-users-to-merchlogix"></a>Przypisywanie użytkowników do MerchLogix

Azure Active Directory używa koncepcji o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD. 

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do MerchLogix. Po ustaleniu tych użytkowników i/lub grup można przypisywać do MerchLogix, postępując zgodnie z poniższymi instrukcjami:

* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Ważne wskazówki dotyczące przypisywania użytkowników do MerchLogix

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do MerchLogix w celu przetestowania początkowej automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowe użytkownicy i/lub grupy mogą zostać przypisane później, gdy testy zakończą się pomyślnie.

* Podczas przypisywania użytkownika do MerchLogix należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Konfigurowanie automatycznej aprowizacji użytkowników do MerchLogix 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie MerchLogix na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla MerchLogix, postępując zgodnie z instrukcjami podanymi w [samouczku logowanie](merchlogix-tutorial.md)jednokrotne w MerchLogix. Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla MerchLogix w usłudze Azure AD:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do **Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje**.

2. Wybierz pozycję MerchLogix z listy aplikacji SaaS.

3. Wybierz kartę **Aprowizacja**.

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawiający sekcję MerchLogix-Prisioning z opcją aprowizacji o nazwie out, tryb aprowizacji ustawiony na automatyczny i opcję połączenia testowego wywołana.](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. W sekcji **poświadczenia administratora** :

    * W polu **adres URL dzierżawy** wprowadź adres URL punktu końcowego Standard scim podany przez kontakt techniczny programu MerchLogix.

    * W polu **token tajny** wprowadź tajny token dostarczony przez kontakt techniczny MerchLogix.

6. Po zapełnieniu pól przedstawionych w kroku 5 kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą MerchLogix. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi MerchLogix ma uprawnienia administratora, a następnie spróbuj ponownie.

7. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

8. Kliknij przycisk **Zapisz**.

9. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do MerchLogix**.

10. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do MerchLogix w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie MerchLogix for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

11. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do MerchLogix**.

12. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD, do MerchLogix w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w MerchLogix dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

13. Aby włączyć usługę Azure AD Provisioning dla MerchLogix, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

14. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w witrynie MerchLogix.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
