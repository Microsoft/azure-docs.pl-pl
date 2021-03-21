---
title: 'Samouczek: Konfigurowanie Concur dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Concur.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: edb21287b30f8ba77d6312ec6b456e20aa260598
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358216"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Concur na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w Concur i usłudze Azure AD w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników z usługi Azure AD do Concur.

## <a name="prerequisites"></a>Wymagania wstępne

W scenariuszu opisanym w tym samouczku założono, że masz już następujące elementy:

*   Dzierżawa usługi Azure Active Directory.
*   Subskrypcja z włączonym logowaniem jednokrotnym w usłudze Concur.
*   Konto użytkownika w Concur z uprawnieniami administratora zespołu.

## <a name="assigning-users-to-concur"></a>Przypisywanie użytkowników do Concur

Azure Active Directory używa koncepcji o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi kont użytkowników są synchronizowane tylko użytkownicy i grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem usługi aprowizacji należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji Concur. Po ustaleniu tych użytkowników możesz przypisać je do aplikacji Concur, postępując zgodnie z poniższymi instrukcjami:

[Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-concur"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Concur

*   Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do Concur w celu przetestowania konfiguracji aprowizacji. Dodatkowych użytkowników i/lub grupy można przypisywać później.

*   Podczas przypisywania użytkownika do Concur należy wybrać prawidłową rolę użytkownika. Rola "dostęp domyślny" nie działa w przypadku aprowizacji.

## <a name="enable-user-provisioning"></a>Włącz Inicjowanie obsługi użytkowników

Ta sekcja przeprowadzi Cię przez proces łączenia się z interfejsem API aprowizacji usługi Azure AD do konta użytkownika Concur, a następnie Konfigurując usługę aprowizacji do tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w usłudze Concur na podstawie przypisywania użytkowników i grup w usłudze Azure AD.

> [!Tip] 
> Możesz również włączyć opcję Sign-On Single opartych na protokole SAML dla Concur, postępując zgodnie z instrukcjami podanymi w [Azure Portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-user-account-provisioning"></a>Aby skonfigurować Inicjowanie obsługi konta użytkownika:

Celem tej sekcji jest zaprojektowanie sposobu włączania obsługi kont użytkowników Active Directory w programie Concur.

Aby włączyć aplikacje w usłudze wydatków, trzeba mieć odpowiednią konfigurację i użycie profilu administratora usługi sieci Web. Nie dodawaj roli administratora WS do istniejącego profilu administratora, który jest używany dla funkcji administracyjnych T&E.

Concur konsultanci lub administrator klienta musi utworzyć unikatowy profil administratora usługi sieci Web, a administrator klienta musi używać tego profilu dla funkcji administratora usług sieci Web (na przykład Włączanie aplikacji). Te profile muszą być oddzielone od dziennego profilu administratora usługi T&E administratora klienta (profil administratora T&E nie powinien mieć przypisanej roli WSAdmin).

Podczas tworzenia profilu, który ma być używany do włączania aplikacji, wprowadź nazwę administratora klienta w polach profil użytkownika. Spowoduje to przypisanie własności do profilu. Po utworzeniu jednego lub większej liczby profilów klient musi zalogować się przy użyciu tego profilu, aby kliknąć przycisk "*Włącz*" dla aplikacji partnerskiej w menu usługi sieci Web.

Z następujących powodów ta akcja nie powinna być wykonywana przy użyciu profilu do normalnej administracji T&E.

* Klient musi być jednym kliknięciem przycisku "*tak*" w oknie dialogowym, które jest wyświetlane po włączeniu aplikacji. Kliknięcie tego przycisku potwierdzi klientowi chęć do uzyskiwania dostępu do danych przez aplikację Partnerskią, przez co ty lub partner nie może kliknąć przycisk Tak.

* Jeśli administrator klienta, który włączył aplikację korzystającą z profilu administratora T&E, opuści firmę (w wyniku dezaktywacji profilu), wszystkie aplikacje z włączonym tym profilem nie działają, dopóki aplikacja nie zostanie włączona z innym aktywnym profilem administratora usługi WS. Dlatego należy utworzyć odrębne profile administratora usługi WS.

* Jeśli administrator opuści firmę, nazwa skojarzona z profilem administratora WS może zostać zmieniona na administratora zastępczego w razie potrzeby bez wpływu na włączoną aplikację, ponieważ ten profil nie musi być zdezaktywowany.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do dzierżawy **Concur** .

2. Z menu **Administracja** wybierz pozycję **usługi sieci Web**.
   
    ![Dzierżawa Concur](./media/concur-provisioning-tutorial/IC721729.png "Dzierżawa Concur")

3. Po lewej stronie w okienku **usługi sieci Web** wybierz pozycję **Włącz aplikację partnera**.
   
    ![Włącz aplikację partnera](./media/concur-provisioning-tutorial/ic721730.png "Włącz aplikację partnera")

4. Z listy **Włącz aplikację** wybierz pozycję **Azure Active Directory**, a następnie kliknij pozycję **Włącz**.
   
    ![Usługa Microsoft Azure Active Directory](./media/concur-provisioning-tutorial/ic721731.png "Usługi Active Directory systemu Microsoft Azure")

5. Kliknij przycisk **tak** , aby zamknąć okno dialogowe **Potwierdź akcję** .
   
    ![Potwierdź akcję](./media/concur-provisioning-tutorial/ic721732.png "Potwierdź akcję")

6. W [Azure Portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** .

7. Jeśli już skonfigurowano Concur do logowania jednokrotnego, Wyszukaj wystąpienie elementu Concur przy użyciu pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i Wyszukaj **Concur** w galerii aplikacji. Wybierz pozycję Concur z wyników wyszukiwania, a następnie dodaj ją do listy aplikacji.

8. Wybierz wystąpienie elementu Concur, a następnie wybierz kartę **Inicjowanie obsługi** .

9. Ustaw **Tryb aprowizacji** na **Automatyczny**. 
 
    ![Zrzut ekranu przedstawiający kartę aprowizacji dla Concur w Azure Portal. Tryb aprowizacji jest ustawiony na automatyczny, a przycisk Testuj połączenie jest wyróżniony.](./media/concur-provisioning-tutorial/provisioning.png)

10. W sekcji **poświadczenia administratora** wprowadź **nazwę użytkownika** i **hasło** administratora Concur.

11. W Azure Portal kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z aplikacją Concur. Jeśli połączenie nie powiedzie się, upewnij się, że Konto Concur ma uprawnienia administratora zespołu.

12. Wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji w polu **E-mail powiadomienia** , i zaznacz pole wyboru.

13. Kliknij przycisk **Zapisz.**

14. W sekcji mapowania wybierz pozycję **synchronizuj Azure Active Directory użytkowników do Concur.**

15. W sekcji **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Concur. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Concur for Updates. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

16. Aby włączyć usługę Azure AD Provisioning dla usługi Concur, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

17. Kliknij przycisk **Zapisz.**

Możesz teraz utworzyć konto testowe. Poczekaj aż do 20 minut, aby sprawdzić, czy konto zostało zsynchronizowane z usługą Concur.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](concur-tutorial.md)