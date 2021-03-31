---
title: 'Samouczek: Konfigurowanie programu dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie inicjować udostępnianie kont użytkowników i cofać ich obsługę.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 13ae960f5d259314f00f8f09b2999a36c0919bc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94353734"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie programu dla automatycznej aprowizacji użytkowników

W tym artykule dowiesz się, jak skonfigurować usługę Azure Active Directory (Azure AD) w celu automatycznego aprowizacji i cofania aprowizacji użytkowników lub grup do udostępnienia.

> [!NOTE]
> W tym artykule opisano łącznik, który jest oparty na usłudze aprowizacji użytkowników usługi Azure AD. Aby uzyskać ważne informacje dotyczące tej usługi i odpowiedzi na często zadawane pytania, zobacz [Automatyzowanie aprowizacji użytkowników i Cofanie udostępniania aplikacji SaaS przy użyciu Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Ten łącznik jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

W scenariuszu opisanym w tym artykule przyjęto założenie, że istnieją już następujące wymagania wstępne:

* Dzierżawa usługi Azure AD
* [Dzierżawa programu](https://harness.io/pricing/)
* Konto użytkownika w programie do współdzielenia z uprawnieniami *administratora*

## <a name="assign-users-to-harness"></a>Przypisywanie użytkowników do programu

Azure Active Directory używa koncepcji zwanej *zadaniami* w celu określenia, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznej aprowizacji użytkowników są synchronizowane tylko użytkownicy lub grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem automatycznej aprowizacji użytkowników należy zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD potrzebują dostępu do programu. Następnie można przypisać tych użytkowników lub grupy do programu, postępując zgodnie z instrukcjami w temacie [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-harness"></a>Ważne porady dotyczące przypisywania użytkowników do programu

* Zalecamy przypisanie pojedynczego użytkownika usługi Azure AD do programu, aby przetestować konfigurację automatycznej aprowizacji użytkowników. Dodatkowych użytkowników lub grup można przypisywać później.

* Po przypisaniu użytkownika do programu, należy wybrać dowolną prawidłową rolę specyficzną dla aplikacji (jeśli jest dostępna) w oknie dialogowym **przypisanie** . Użytkownicy z *domyślną rolą dostępu* są wykluczeni z aprowizacji.

## <a name="set-up-harness-for-provisioning"></a>Konfigurowanie programu dla aprowizacji

1. Zaloguj się do [konsoli administracyjnej](https://app.harness.io/#/login)programu, a następnie przejdź do pozycji **ciągłe**  >  **Zarządzanie dostępem** do zabezpieczeń.

    ![Konsola administracyjna programu](media/harness-provisioning-tutorial/admin.png)

1. Wybierz pozycję **klucze interfejsu API**.

    ![Link kluczy interfejsu API usługi](media/harness-provisioning-tutorial/apikeys.png)

1. Wybierz pozycję **Dodaj klucz interfejsu API**. 

    ![Link dodawania klucza interfejsu API](media/harness-provisioning-tutorial/addkey.png)

1. W okienku **Dodaj klucz interfejsu API** wykonaj następujące czynności:

    ![Panel dodawania klucza interfejsu API](media/harness-provisioning-tutorial/title.png)
   
   a. W polu **Nazwa** Podaj nazwę klucza.  
   b. Z listy rozwijanej **uprawnienia Odziedziczone z** wybierz opcję. 
   
1. Wybierz pozycję **Prześlij**.

1. Skopiuj **klucz** do późniejszego użycia w tym samouczku.

    ![Utwórz token dla zespołu](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Dodaj zespół z galerii

Przed skonfigurowaniem programu do automatycznej aprowizacji użytkowników przy użyciu usługi Azure AD należy dodać zespół z galerii aplikacji usługi Azure AD do listy zarządzanych aplikacji SaaS.

1. W [witrynie Azure Portal](https://portal.azure.com) w okienku po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Przycisk "Azure Active Directory"](common/select-azuread.png)

1. Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.

    ![Łącze "wszystkie aplikacje"](common/enterprise-applications.png)

1. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okienka.

    ![Przycisk "Nowa aplikacja"](common/add-new-app.png)

1. W polu wyszukiwania wprowadź wartość **zespół**, wybierz pozycję **zespół** na liście wyników, a następnie wybierz przycisk **Dodaj** , aby dodać aplikację.

    ![Korzystanie z listy wyników](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>Konfigurowanie automatycznego aprowizacji użytkowników do programu 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników lub grup w ramach programu obsługi na podstawie przypisań użytkowników lub grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć logowanie jednokrotne oparte na protokole SAML dla programu obsługi, postępując zgodnie z instrukcjami w [samouczku logowanie](./harness-tutorial.md)jednokrotne w programie. Można skonfigurować Logowanie jednokrotne niezależnie od automatycznej aprowizacji użytkowników, chociaż te dwie funkcje uzupełniają się wzajemnie.

> [!NOTE]
> Aby dowiedzieć się więcej o punkcie końcowym programu Standard scim, zobacz artykuł [klucze interfejsu API](https://docs.harness.io/article/smloyragsm-api-keys) usługi.

Aby skonfigurować automatyczną obsługę administracyjną dla programu obsługi w usłudze Azure AD, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście Aplikacje wybierz pozycję **zespół**.

    ![Link programu dla programu z listy aplikacji](common/all-applications.png)

1. Wybierz opcję **aprowizacji**.

    ![Przycisk aprowizacji](common/provisioning.png)

1. Z listy rozwijanej **tryb aprowizacji** wybierz pozycję **automatycznie**.

    ![Lista rozwijana "tryb aprowizacji"](common/provisioning-automatic.png)

1. W obszarze **poświadczenia administratora** wykonaj następujące czynności:

    ![Adres URL dzierżawy + token](common/provisioning-testconnection-tenanturltoken.png)
 
   a. W polu **adres URL dzierżawy** wprowadź wartość **`https://app.harness.io/gateway/api/scim/account/<your_harness_account_ID>`** . Identyfikator konta usługi można uzyskać z adresu URL w przeglądarce, gdy użytkownik jest zalogowany do programu.
   b. W polu **token Secret** wprowadź wartość tokenu uwierzytelniania Standard scim, która została zapisana w kroku 6 sekcji "Konfigurowanie usługi dla aprowizacji".  
   c. Wybierz pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z serwerem programu. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi klienta ma uprawnienia *administratora* , a następnie spróbuj ponownie.

1. W polu **E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach aprowizacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail, gdy wystąpi błąd** .

    ![Pole "powiadomienie E-mail"](common/provisioning-notification-email.png)

1. Wybierz pozycję **Zapisz**.

1. W obszarze **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy, aby uzyskać** dostęp.

    ![Łącze "Synchronizuj Azure Active Directory użytkowników do zespołu"](media/harness-provisioning-tutorial/usermappings.png)

1. W obszarze **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do programu. Atrybuty wybrane jako *zgodne* są używane do dopasowywania do kont użytkowników w programie dla operacji aktualizacji. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.

    ![Okienko "Mapowanie atrybutów" użytkownika](media/harness-provisioning-tutorial/userattributes.png)

1. W obszarze **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory, aby uzyskać** dostęp.

    ![Łącze "Synchronizuj grupy Azure Active Directory do zespołu"](media/harness-provisioning-tutorial/groupmappings.png)

1. W obszarze **mapowania atrybutów** Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do programu. Atrybuty wybrane jako *pasujące* właściwości są używane do dopasowania do grup w programie dla operacji aktualizacji. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.

    ![Okienko "Mapowanie atrybutów" grupy](media/harness-provisioning-tutorial/groupattributes.png)

1. Aby skonfigurować filtry określania zakresu, zobacz Tworzenie [aplikacji opartych na atrybutach przy użyciu filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. W obszarze **Ustawienia**, aby włączyć usługę Azure AD Provisioning dla programu, Przełącz przełącznik **stanu aprowizacji** na **włączony**.

    ![Przełącznik stanu aprowizacji jest przełączany do "on"](common/provisioning-toggle-on.png)

1. W obszarze **Ustawienia** wybierz z listy rozwijanej **zakres** , w jaki sposób chcesz synchronizować użytkowników lub grupy, które są używane do obsługi.

    ![Zakres aprowizacji](common/provisioning-scope.png)

1. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**.

    ![Przycisk zapisywania aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację użytkowników lub grup, które są inicjowane. Synchronizacja początkowa trwa dłużej niż w późniejszym czasie. Synchronizacje odbywają się około co 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. Aby monitorować postęp, przejdź do sekcji **szczegóły synchronizacji** . Możesz również śledzić łącza do raportu działań aprowizacji, który opisuje wszystkie akcje wykonywane przez usługę Azure AD Provisioning w ramach programu.

Aby uzyskać więcej informacji o sposobie odczytywania dzienników aprowizacji usługi Azure AD, zobacz [raport dotyczący automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)