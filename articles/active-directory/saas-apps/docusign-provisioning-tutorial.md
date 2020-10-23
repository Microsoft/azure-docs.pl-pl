---
title: 'Samouczek: Konfigurowanie DocuSign dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i DocuSign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: 3a0a69784be3b03b030ef599037b57c2c20ea2c6
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92454681"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie DocuSign na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w DocuSign i usłudze Azure AD w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników z usługi Azure AD do DocuSign.

## <a name="prerequisites"></a>Wymagania wstępne

W scenariuszu opisanym w tym samouczku założono, że masz już następujące elementy:

*   Dzierżawa usługi Azure Active Directory.
*   Subskrypcja z włączonym logowaniem jednokrotnym w usłudze DocuSign.
*   Konto użytkownika w DocuSign z uprawnieniami administratora zespołu.

## <a name="assigning-users-to-docusign"></a>Przypisywanie użytkowników do DocuSign

Azure Active Directory używa koncepcji o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi kont użytkowników są synchronizowane tylko użytkownicy i grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem usługi aprowizacji należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji DocuSign. Po ustaleniu tych użytkowników możesz przypisać je do aplikacji DocuSign, postępując zgodnie z poniższymi instrukcjami:

[Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Ważne wskazówki dotyczące przypisywania użytkowników do DocuSign

*   Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do DocuSign w celu przetestowania konfiguracji aprowizacji. Dodatkowi użytkownicy mogą zostać przypisani później.

*   Podczas przypisywania użytkownika do DocuSign należy wybrać prawidłową rolę użytkownika. Rola "dostęp domyślny" nie działa w przypadku aprowizacji.

> [!NOTE]
> Usługa Azure AD nie obsługuje aprowizacji grup w aplikacji Docusign, ale tylko użytkownicy mogą być obsługiwani.

## <a name="enable-user-provisioning"></a>Włącz Inicjowanie obsługi użytkowników

Ta sekcja przeprowadzi Cię przez proces łączenia się z interfejsem API aprowizacji usługi Azure AD do konta użytkownika DocuSign, a następnie Konfigurując usługę aprowizacji do tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w usłudze DocuSign na podstawie przypisywania użytkowników i grup w usłudze Azure AD.

> [!Tip]
> Możesz również włączyć opcję Sign-On Single opartych na protokole SAML dla DocuSign, postępując zgodnie z instrukcjami podanymi w [Azure Portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-user-account-provisioning"></a>Aby skonfigurować Inicjowanie obsługi konta użytkownika:

Celem tej sekcji jest zaprojektowanie sposobu włączania obsługi administracyjnej użytkowników Active Directory kont użytkowników w usłudze DocuSign.

1. W [Azure Portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** .

1. Jeśli już skonfigurowano DocuSign do logowania jednokrotnego, Wyszukaj wystąpienie elementu DocuSign przy użyciu pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i Wyszukaj **Docusign** w galerii aplikacji. Wybierz pozycję DocuSign z wyników wyszukiwania, a następnie dodaj ją do listy aplikacji.

1. Wybierz wystąpienie elementu DocuSign, a następnie wybierz kartę **Inicjowanie obsługi** .

1. Ustaw **Tryb aprowizacji** na **Automatyczny**. 

    ![Zrzut ekranu przedstawiający kartę aprowizacji dla DocuSign w Azure Portal. Tryb aprowizacji jest ustawiony na automatyczny, a nazwa użytkownika administratora, hasło i połączenie testowe są wyróżnione.](./media/docusign-provisioning-tutorial/provisioning.png)

1. W sekcji **poświadczenia administratora** podaj następujące ustawienia konfiguracji:
   
    a. W polu tekstowym **Nazwa użytkownika administratora** wpisz nazwę konta Docusign z profilem **administratora systemu** w Docusign.com przypisane.
   
    b. W polu tekstowym **hasło administratora** wpisz hasło dla tego konta.

> [!NOTE]
> Jeśli skonfigurowano Logowanie jednokrotne i Inicjowanie obsługi użytkownika, poświadczenia autoryzacji używane do aprowizacji muszą zostać skonfigurowane do pracy z logowaniem jednokrotnym i nazwą użytkownika/hasłem.

1. W Azure Portal kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z aplikacją Docusign.

1. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru.

1. Kliknij przycisk **Zapisz.**

1. W sekcji mapowania wybierz pozycję **synchronizuj Azure Active Directory użytkowników do Docusign.**

1. W sekcji **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Docusign. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Docusign for Updates. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

1. Aby włączyć usługę Azure AD Provisioning dla usługi DocuSign, Zmień **stan aprowizacji** na **włączone** w sekcji Ustawienia.

1. Kliknij przycisk **Zapisz.**

Rozpocznie się początkową synchronizację wszystkich użytkowników przypisanych do DocuSign w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa jest uruchomiona. Za pomocą sekcji **szczegóły synchronizacji** można monitorować postęp i wykonywać linki do dzienników aktywności aprowizacji, które opisują wszystkie akcje wykonywane przez usługę aprowizacji w aplikacji Docusign.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów
* Inicjowanie obsługi administracyjnej roli lub profilu uprawnień dla użytkownika w Docusign można wykonać przy użyciu wyrażenia w mapowaniu atrybutów przy użyciu funkcji [Switch](https://docs.microsoft.com/azure/active-directory/app-provisioning/functions-for-customizing-application-data#switch) i [singleAppRoleAssignment](https://docs.microsoft.com/azure/active-directory/app-provisioning/functions-for-customizing-application-data#singleapproleassignment) . Na przykład poniższe wyrażenie spowoduje udostępnienie identyfikatora "8032066", gdy użytkownik ma przypisaną rolę "Administrator DS" w usłudze Azure AD. Jeśli użytkownik nie ma przypisanej roli po stronie usługi Azure AD, nie będzie mógł zainicjować żadnego profilu uprawnień. Identyfikator można pobrać z [portalu](https://support.docusign.com/articles/Default-settings-for-out-of-the-box-DocuSign-Permission-Profiles)Docusign.

Switch (SingleAppRoleAssignment ([appRoleAssignments]), "", "8032066", "Administrator DS.")


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](docusign-tutorial.md)