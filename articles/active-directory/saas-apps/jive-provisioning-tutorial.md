---
title: 'Samouczek: Konfigurowanie Jive dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Zapoznaj się z krokami, które należy wykonać w Jive i usłudze Azure AD w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników z usługi Azure AD do Jive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: ebee5d986007e07d497056620f0cfc437b2da4d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94356403"
---
# <a name="tutorial-configure-jive-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Jive na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w Jive i usłudze Azure AD w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników z usługi Azure AD do Jive.

## <a name="prerequisites"></a>Wymagania wstępne

W scenariuszu opisanym w tym samouczku założono, że masz już następujące elementy:

*   Dzierżawa usługi Azure Active Directory.
*   Subskrypcja z włączonym logowaniem jednokrotnym (Jive).
*   Konto użytkownika w Jive z uprawnieniami administratora zespołu.

## <a name="assigning-users-to-jive"></a>Przypisywanie użytkowników do Jive

Azure Active Directory używa koncepcji o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi kont użytkowników są synchronizowane tylko użytkownicy i grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem usługi aprowizacji należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji Jive. Po ustaleniu tych użytkowników możesz przypisać je do aplikacji Jive, postępując zgodnie z poniższymi instrukcjami:

[Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-jive"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Jive

*   Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do Jive w celu przetestowania konfiguracji aprowizacji. Dodatkowych użytkowników i/lub grupy można przypisywać później.

*   Podczas przypisywania użytkownika do Jive należy wybrać prawidłową rolę użytkownika. Rola "dostęp domyślny" nie działa w przypadku aprowizacji.

## <a name="enable-user-provisioning"></a>Włącz Inicjowanie obsługi użytkowników

Ta sekcja przeprowadzi Cię przez proces łączenia się z interfejsem API aprowizacji usługi Azure AD do konta użytkownika Jive, a następnie Konfigurując usługę aprowizacji do tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w usłudze Jive na podstawie przypisywania użytkowników i grup w usłudze Azure AD.

> [!TIP]
> Możesz również włączyć opcję Sign-On Single opartych na protokole SAML dla Jive, postępując zgodnie z instrukcjami podanymi w [Azure Portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-user-account-provisioning"></a>Aby skonfigurować Inicjowanie obsługi konta użytkownika:

Celem tej sekcji jest zaprojektowanie sposobu włączania obsługi administracyjnej użytkowników Active Directory kont użytkowników w usłudze Jive.
W ramach tej procedury konieczne jest podanie tokenu zabezpieczającego użytkownika potrzebnego do żądania od Jive.com.

1. W [Azure Portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** .

1. Jeśli już skonfigurowano Jive do logowania jednokrotnego, Wyszukaj wystąpienie elementu Jive przy użyciu pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i Wyszukaj **Jive** w galerii aplikacji. Wybierz pozycję Jive z wyników wyszukiwania, a następnie dodaj ją do listy aplikacji.

1. Wybierz wystąpienie elementu Jive, a następnie wybierz kartę **Inicjowanie obsługi** .

1. Ustaw **Tryb aprowizacji** na **Automatyczny**. 

    ![Zrzut ekranu przedstawia stronę aprowizacji Jive, z trybem aprowizacji ustawionym na automatyczne i inne wartości, które można ustawić.](./media/jive-provisioning-tutorial/provisioning.png)

1. W sekcji **poświadczenia administratora** podaj następujące ustawienia konfiguracji:
   
    a. W polu tekstowym **Nazwa użytkownika administratora Jive** wpisz nazwę konta Jive z profilem **administratora systemu** w Jive.com przypisanym.
   
    b. W polu tekstowym **hasło administratora Jive** wpisz hasło dla tego konta.
   
    c. W polu tekstowym **adres URL dzierżawy Jive** wpisz adres URL dzierżawy Jive.
      
      > [!NOTE]
      > Adres URL dzierżawy Jive jest adresem URL używanym przez organizację do logowania się do Jive.  
      > Zazwyczaj adres URL ma następujący format: **www. \<organization\> . jive.com**.          

1. W Azure Portal kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z aplikacją Jive.

1. Wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji w polu **E-mail powiadomienia** , a następnie zaznacz pole wyboru poniżej.

1. Kliknij przycisk **Zapisz.**

1. W sekcji mapowania wybierz pozycję **synchronizuj Azure Active Directory użytkowników do Jive.**

1. W sekcji **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Jive. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Jive for Updates. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

1. Aby włączyć usługę Azure AD Provisioning dla usługi Jive, Zmień **stan aprowizacji** na **włączone** w sekcji Ustawienia.

1. Kliknij przycisk **Zapisz.**

Rozpocznie się początkową synchronizację wszystkich użytkowników i/lub grup przypisanych do Jive w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa jest uruchomiona. Za pomocą sekcji **szczegóły synchronizacji** można monitorować postęp i wykonywać linki do dzienników aktywności aprowizacji, które opisują wszystkie akcje wykonywane przez usługę aprowizacji w aplikacji Jive.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](jive-tutorial.md)