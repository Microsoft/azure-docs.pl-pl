---
title: 'Samouczek: Konfigurowanie usługi G Suite do automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD w usłudze G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 969a2fb5444ae8ece2aa302c04a5bbb85dcca917
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77057723"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie usługi G Suite do automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w usłudze G Suite i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup w usłudze G Suite.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../app-provisioning/user-provisioning.md).

> [!NOTE]
> Łącznik pakietu G Suite został niedawno zaktualizowany o 2019 października. Zmiany wprowadzone do łącznika G Suite obejmują:
> - Dodano obsługę dodatkowych atrybutów użytkowników i grup usługi G Suite. 
> - Zaktualizowano nazwy atrybutów docelowych G Suite, aby były zgodne z informacjami zdefiniowanymi w [tym miejscu](https://developers.google.com/admin-sdk/directory).
> - Zaktualizowano domyślne mapowania atrybutów.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą G Suite, potrzebne są następujące elementy:

- Dzierżawa usługi Azure AD
- [Dzierżawa G Suite](https://gsuite.google.com/pricing.html)
- Konto użytkownika w pakiecie G Suite z uprawnieniami administratora.

## <a name="assign-users-to-g-suite"></a>Przypisywanie użytkowników do zestawu G Suite

Azure Active Directory używa koncepcji zwanej zadaniami w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy i/lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD potrzebują dostępu do usługi G Suite. Po ustaleniu tych użytkowników i/lub grup można przypisać do pakietu G Suite, postępując zgodnie z poniższymi instrukcjami:

* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>Ważne porady dotyczące przypisywania użytkowników do usługi G Suite

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do pakietu G Suite w celu przetestowania automatycznej konfiguracji inicjowania obsługi użytkowników. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do pakietu G Suite należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym przypisania. Użytkownicy z **domyślną rolą dostępu** są wykluczeni z aprowizacji.

## <a name="setup-g-suite-for-provisioning"></a>Konfiguracja usługi G Suite na potrzeby aprowizacji

Przed skonfigurowaniem pakietu G dla automatycznej aprowizacji użytkowników w usłudze Azure AD należy włączyć Inicjowanie obsługi Standard scim w usłudze G Suite.

1. Zaloguj się do [konsoli administracyjnej G Suite](https://admin.google.com/) przy użyciu konta administratora, a następnie wybierz pozycję **zabezpieczenia**. Jeśli łącze nie jest widoczne, może być ukryte w menu **Więcej kontrolek** w dolnej części ekranu.

    ![Wybierz pozycję Zabezpieczenia.][10]

1. Na stronie **zabezpieczenia** wybierz pozycję **Dokumentacja interfejsu API**.

    ![Wybierz pozycję dokumentacja interfejsu API.][15]

1. Wybierz pozycję **Włącz dostęp do interfejsu API**.

    ![Wybierz pozycję dokumentacja interfejsu API.][16]

   > [!IMPORTANT]
   > Dla każdego użytkownika, który zamierzasz udostępnić do usługi G Suite, jego nazwa użytkownika w usłudze Azure AD **musi** być powiązana z domeną niestandardową. Na przykład nazwy użytkowników, które wyglądają jak bob@contoso.onmicrosoft.com nie są akceptowane przez pakiet G Suite. Z drugiej strony bob@contoso.com zostanie zaakceptowany. Istniejącą domenę użytkownika można zmienić, postępując zgodnie z instrukcjami znajdującymi się [tutaj](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain).

1. Po dodaniu i zweryfikowaniu żądanych domen niestandardowych za pomocą usługi Azure AD należy zweryfikować je ponownie z pakietem G Suite. Aby sprawdzić domeny w usłudze G Suite, zapoznaj się z następującymi krokami:

    a. W [konsoli administracyjnej programu G Suite](https://admin.google.com/)wybierz pozycję **domeny**.

    ![Wybierz domeny][20]

    b. Wybierz pozycję **Dodaj domenę lub alias domeny**.

    ![Dodaj nową domenę][21]

    c. Wybierz pozycję **Dodaj inną domenę**, a następnie wpisz nazwę domeny, którą chcesz dodać.

    ![Wpisz nazwę domeny][22]

    d. Wybierz pozycję **Kontynuuj i sprawdź własność domeny**. Następnie postępuj zgodnie z instrukcjami, aby sprawdzić, czy jesteś właocicielem nazwy domeny. Aby uzyskać wyczerpujące instrukcje dotyczące weryfikowania domeny za pomocą usługi Google, zobacz [Sprawdzanie własności lokacji](https://support.google.com/webmasters/answer/35179).

    e. Powtórz powyższe kroki dla wszystkich dodatkowych domen, które mają zostać dodane do usługi G Suite.

1. Następnie określ konto administratora, które ma być używane do zarządzania aprowizacji użytkowników w usłudze G Suite. Przejdź do **ról administratora**.

    ![Wybierz aplikacje Google Apps][26]

1. W przypadku **roli administratora** tego konta Edytuj **uprawnienia** dla tej roli. Upewnij się, że wszystkie **uprawnienia administratora interfejsu API** są włączone, aby można było używać tego konta do obsługi administracyjnej.

    ![Wybierz aplikacje Google Apps][27]

## <a name="add-g-suite-from-the-gallery"></a>Dodaj pakiet G Suite z galerii

Aby skonfigurować usługę G dla automatycznej aprowizacji użytkowników w usłudze Azure AD, musisz dodać pakiet G Suite z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS. 

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

1. Przejdź do pozycji **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

1. W polu wyszukiwania wprowadź wartość **g Suite**, wybierz pozycję **g Suite** w panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Usługa G Suite na liście wyników](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>Konfigurowanie automatycznej aprowizacji użytkowników w usłudze G Suite 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w usłudze G Suite na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć funkcję logowania jednokrotnego opartego na protokole SAML dla pakietu G Suite, postępując zgodnie z instrukcjami podanymi w samouczku Logowanie jednokrotne w [usłudze g Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje napadają nawzajem.

> [!NOTE]
> Aby dowiedzieć się więcej o punkcie końcowym interfejsu API katalogu usługi G, zapoznaj się z [interfejsem API katalogu](https://developers.google.com/admin-sdk/directory).

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej użytkowników dla pakietu G Suite w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście Aplikacje wybierz pozycję **G Suite**.

    ![Link do usługi G Suite na liście aplikacji](common/all-applications.png)

1. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

1. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

1. W sekcji **poświadczenia administratora** wybierz pozycję **Autoryzuj**. Spowoduje to otwarcie okna dialogowego autoryzacji Google w nowym oknie przeglądarki.

    ![G Suite — Autoryzuj](media/google-apps-provisioning-tutorial/authorize.png)

1. Potwierdź, że chcesz nadać uprawnienia usługi Azure AD, aby wprowadzić zmiany w dzierżawie w usłudze G Suite. Wybierz pozycję **Zaakceptuj**.

    ![Potwierdź uprawnienia.][28]

1. W Azure Portal wybierz pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z Twoją aplikacją. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi G Suite ma uprawnienia administratora zespołu. Następnie spróbuj ponownie wykonać krok **Autoryzuj** .

1. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

1. Kliknij przycisk **Zapisz**.

1. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy z usługą G Suite**.

    ![Mapowania użytkowników w ramach pakietu G Suite](media/google-apps-provisioning-tutorial/usermappings.png)

1. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD z usługą G Suite w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do kont użytkowników w usłudze G Suite dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    ![Atrybuty użytkownika w usłudze G Suite](media/google-apps-provisioning-tutorial/userattributes.png)

1. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory z usługą G Suite**.

    ![Mapowania grupy G Suite](media/google-apps-provisioning-tutorial/groupmappings.png)

1. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD z usługą G Suite w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w usłudze G Suite dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany. Interfejs użytkownika wyświetla domyślny zestaw mapowań atrybutów między usługami Azure AD i G Suite. Możesz dodać dodatkowe atrybuty, takie jak jednostka organizacyjna, klikając pozycję Dodaj nowe mapowanie.

    ![Atrybuty grupy G Suite](media/google-apps-provisioning-tutorial/groupattributes.png)

1. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Aby włączyć usługę Azure AD Provisioning dla usługi G Suite, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

1. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić do zbioru G Suite, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

1. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Możesz użyć sekcji **szczegóły synchronizacji** do monitorowania postępu i postępuj zgodnie z raportem aktywności aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w usłudze G Suite.

> [!NOTE]
> Jeśli użytkownicy mają już istniejące konto osobiste/konsumencki przy użyciu adresu e-mail użytkownika usługi Azure AD, może to spowodować pewne problemy, które można rozwiązać za pomocą narzędzia Google transfer przed przeprowadzeniem synchronizacji katalogów.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="common-issues"></a>Typowe problemy
* Błędy autoryzacji mogą wystąpić, jeśli konto używane do nawiązania połączenia nie jest przeznaczone dla administratora w GSuite. Upewnij się, że konto używane do autoryzacji dostępu ma uprawnienia administratora **we wszystkich domenach** , które użytkownicy muszą mieć do aprowizacji. 
* Usługa Azure AD obsługuje wyłączanie użytkowników w programie GSuite, dzięki czemu nie mogą uzyskać dostępu do aplikacji, ale nie usuwa użytkowników w GSuite.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
