---
title: 'Samouczek: Konfigurowanie piaskownicy usługi Salesforce na potrzeby automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Informacje o krokach, które należy wykonać w piaskownicy usługi Salesforce i usłudze Azure AD w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników z usługi Azure AD do piaskownicy usług Salesforce.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: ae8bc3ce5822b8d49db0fdc6e611badd75b0d27b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91273317"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie piaskownicy usługi Salesforce na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w piaskownicy usługi Salesforce i usłudze Azure AD w celu automatycznego aprowizacji i dezaktywowania kont użytkowników z usługi Azure AD do piaskownicy usług Salesforce.

## <a name="prerequisites"></a>Wymagania wstępne

W scenariuszu opisanym w tym samouczku założono, że masz już następujące elementy:

*   Dzierżawa usługi Azure Active Directory.
*   Prawidłowy dzierżawca dla piaskownicy usługi Salesforce na potrzeby edukacji służbowej lub piaskownicy usługi Salesforce. Możesz użyć bezpłatnego konta próbnego dla każdej usługi.
*   Konto użytkownika w piaskownicy usługi Salesforce z uprawnieniami administratora zespołu.

## <a name="assigning-users-to-salesforce-sandbox"></a>Przypisywanie użytkowników do piaskownicy usługi Salesforce

Azure Active Directory używa koncepcji o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi kont użytkowników są synchronizowane tylko użytkownicy i grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem usługi aprowizacji należy zdecydować, którzy użytkownicy lub które grupy w usłudze Azure AD potrzebują dostępu do aplikacji piaskownicy usługi Salesforce. Po wykonaniu tej decyzji można przypisać tych użytkowników do aplikacji piaskownicy usługi Salesforce, postępując zgodnie z instrukcjami w temacie [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) .

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Ważne porady dotyczące przypisywania użytkowników do piaskownicy usługi Salesforce

* Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do piaskownicy Salesforce w celu przetestowania konfiguracji aprowizacji. Dodatkowych użytkowników i/lub grupy można przypisywać później.

* Podczas przypisywania użytkownika do piaskownicy usługi Salesforce należy wybrać prawidłową rolę użytkownika. Rola "dostęp domyślny" nie działa w przypadku aprowizacji.

> [!NOTE]
> Ta aplikacja importuje role niestandardowe z piaskownicy usługi Salesforce jako część procesu aprowizacji, który klient może chcieć wybrać podczas przypisywania użytkowników.

## <a name="enable-automated-user-provisioning"></a>Włącz automatyczne Inicjowanie obsługi użytkowników

Ta sekcja przeprowadzi Cię przez proces łączenia się z interfejsem API aprowizacji usługi Azure AD do konta użytkownika w usłudze Salesforce i konfigurowania usługi aprowizacji do tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w piaskownicy usługi Salesforce na podstawie przypisywania użytkowników i grup w usłudze Azure AD.

>[!Tip]
>Możesz również wybrać opcję włączenia jednoSign-On opartej na protokole SAML dla piaskownicy usługi Salesforce, postępując zgodnie z instrukcjami podanymi w [Azure Portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji, chociaż te dwie funkcje napadają nawzajem.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurowanie automatycznego inicjowania obsługi konta użytkownika

Celem tej sekcji jest zaprojektowanie sposobu włączania obsługi administracyjnej użytkowników w Active Directory kontach użytkowników w piaskownicy usługi Salesforce.

1. W [Azure Portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** .

1. Jeśli w obszarze piaskownicy usługi Salesforce już skonfigurowano Logowanie jednokrotne, Wyszukaj wystąpienie piaskownicy usługi Salesforce przy użyciu pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i Wyszukaj **piaskownicę usługi Salesforce** w galerii aplikacji. Wybierz opcję piaskownicy Salesforce z wyników wyszukiwania, a następnie dodaj ją do listy aplikacji.

1. Wybierz wystąpienie piaskownicy usługi Salesforce, a następnie wybierz kartę **aprowizacji** .

1. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Zrzut ekranu przedstawia stronę aprowizacji piaskownicy usługi Salesforce, z trybem aprowizacji ustawionym na automatyczne i inne wartości, które można ustawić.](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. W sekcji **poświadczenia administratora** podaj następujące ustawienia konfiguracji:
   
    a. W polu tekstowym **Nazwa użytkownika administratora** wpisz nazwę konta piaskownicy usługi Salesforce z profilem **administratora systemu** w Salesforce.com przypisane.
   
    b. W polu tekstowym **hasło administratora** wpisz hasło dla tego konta.

1. Aby uzyskać token zabezpieczający piaskownicy usług Salesforce, Otwórz nową kartę i zaloguj się do tego samego konta administratora piaskownicy usługi Salesforce. W prawym górnym rogu strony kliknij swoją nazwę, a następnie kliknij pozycję **Ustawienia**.

     ![Włącz automatyczne Inicjowanie obsługi użytkowników](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "Włącz automatyczne Inicjowanie obsługi użytkowników")

1. W okienku nawigacji po lewej stronie kliknij pozycję **moje dane osobowe** , aby rozwinąć sekcję powiązaną, a następnie kliknij pozycję **Zresetuj mój token zabezpieczający**.
  
    ![Włącz automatyczne Inicjowanie obsługi użytkowników](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "Włącz automatyczne Inicjowanie obsługi użytkowników")

1. Na stronie **Resetowanie tokenu zabezpieczeń** kliknij przycisk **Resetuj token zabezpieczający** .

    ![Włącz automatyczne Inicjowanie obsługi użytkowników](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "Włącz automatyczne Inicjowanie obsługi użytkowników")

1. Sprawdź skrzynkę odbiorczą wiadomości e-mail skojarzoną z tym kontem administratora. Poszukaj wiadomości e-mail z usługi Salesforce Sandbox.com, która zawiera nowy token zabezpieczający.

1. Skopiuj token, przejdź do okna usługi Azure AD i wklej go w polu **token tajny** .

1. W Azure Portal kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z aplikacją piaskownicy usługi Salesforce.

1. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru.

1. Kliknij przycisk **Zapisz.**  
    
1.  W sekcji mapowania wybierz pozycję **synchronizuj Azure Active Directory użytkownicy z piaskownicą usługi Salesforce.**

1. W sekcji **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do piaskownicy usług Salesforce. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania do kont użytkowników w piaskownicy usługi Salesforce na potrzeby operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

1. Aby włączyć usługę Azure AD Provisioning dla piaskownicy usługi Salesforce, Zmień **stan aprowizacji** na **włączone** w sekcji Ustawienia.

1. Kliknij przycisk **Zapisz.**

Rozpocznie się początkową synchronizację wszystkich użytkowników i/lub grup przypisanych do piaskownicy usługi Salesforce w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa jest uruchomiona. Za pomocą sekcji **szczegóły synchronizacji** można monitorować postęp i wykonywać linki do dzienników aktywności aprowizacji, które opisują wszystkie akcje wykonywane przez usługę aprowizacji w aplikacji piaskownicy usługi Salesforce.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)
