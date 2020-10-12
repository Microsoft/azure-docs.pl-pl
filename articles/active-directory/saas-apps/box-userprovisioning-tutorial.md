---
title: 'Samouczek: Konfigurowanie pola do automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Box.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/20/2020
ms.author: jeedes
ms.openlocfilehash: 44f2195fb68b5a17eab3980f72cbc5374e1c033a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91312910"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie pola do automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w usłudze Box i usłudze Azure AD w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników z usługi Azure AD w usłudze Box.

> [!NOTE]
> Ten samouczek zawiera opis łącznika utworzonego na podstawie usługi Azure AD User Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Box, potrzebne są następujące elementy:

- Dzierżawa usługi Azure AD
- Plan biznesowy dla pudełka lub lepszy

> [!NOTE]
> Podczas testowania kroków opisanych w tym samouczku zalecamy, aby *nie* używać środowiska produkcyjnego.

> [!NOTE]
> Aplikacje muszą być najpierw włączone w aplikacji Box.

Aby przetestować kroki w tym samouczku, musisz mieć dostęp do następujących elementów:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska usługi Azure AD w wersji próbnej, możesz skorzystać z [miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-box"></a>Przypisywanie użytkowników do pola 

Azure Active Directory używa koncepcji o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymywać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi kont użytkowników są synchronizowane tylko użytkownicy i grupy, które zostały przypisane do aplikacji w usłudze Azure AD.

Przed skonfigurowaniem i włączeniem usługi aprowizacji należy zdecydować, którzy użytkownicy i/lub grupy w usłudze Azure AD reprezentują użytkowników, którzy potrzebują dostępu do aplikacji usługi Box. Po ustaleniu tych użytkowników możesz przypisać je do aplikacji Box, postępując zgodnie z poniższymi instrukcjami:

[Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>Przypisywanie użytkowników i grup
Karta **> Użytkownicy i grupy** w Azure Portal umożliwia określenie użytkowników i grup, do których należy udzielić dostępu do pola. Przypisanie użytkownika lub grupy powoduje wystąpienie następujących rzeczy:

* Usługa Azure AD umożliwia przypisanemu użytkownikowi (przez przypisanie bezpośrednie lub członkostwo w grupie) uwierzytelnianie do usługi Box. Jeśli użytkownik nie jest przypisany, usługa Azure AD nie będzie zezwalać na logowanie się w usłudze Box i zwraca błąd na stronie logowania usługi Azure AD.
* Kafelek aplikacji dla usługi Box zostanie dodany do [uruchamiania aplikacji](../manage-apps/end-user-experiences.md)użytkownika.
* Jeśli włączono automatyczną obsługę administracyjną, przypisane Użytkownicy i/lub grupy zostaną dodane do kolejki aprowizacji w celu automatycznego aprowizacji.
  
  * Jeśli skonfigurowano tylko obiekty użytkownika do obsługi administracyjnej, wszystkie bezpośrednio przypisani użytkownicy są umieszczane w kolejce aprowizacji, a wszyscy użytkownicy, którzy są członkami wszystkich przypisanych grup, są umieszczani w kolejce aprowizacji. 
  * Jeśli obiekty grupy zostały skonfigurowane do obsługi administracyjnej, wszystkie przypisane obiekty grupy są obsługiwane do pola i wszyscy użytkownicy będący członkami tych grup. Członkostwa grup i użytkowników są zachowywane po zapisaniu do pola.

Możesz użyć **atrybutów > karcie Logowanie** jednokrotne, aby skonfigurować atrybuty użytkownika (lub oświadczenia), które zostaną wyświetlone w polu podczas uwierzytelniania opartego na protokole SAML, oraz kartę **atrybuty > aprowizacji** , aby skonfigurować sposób przepływu atrybutów użytkowników i grup z usługi Azure AD do pola podczas operacji aprowizacji.

### <a name="important-tips-for-assigning-users-to-box"></a>Ważne porady dotyczące przypisywania użytkowników do usługi Box 

*   Zaleca się, aby jeden użytkownik usługi Azure AD został przypisany do pola w celu przetestowania konfiguracji aprowizacji. Dodatkowych użytkowników i/lub grupy można przypisywać później.

*   Podczas przypisywania użytkownika do pola należy wybrać prawidłową rolę użytkownika. Rola "dostęp domyślny" nie działa w przypadku aprowizacji.

## <a name="enable-automated-user-provisioning"></a>Włącz automatyczne Inicjowanie obsługi użytkowników

W tej sekcji Przewodnik po łączeniu się z interfejsem API aprowizacji konta użytkownika usługi Azure AD, a następnie skonfigurowania usługi aprowizacji do tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w usłudze Box na podstawie przypisywania użytkowników i grup w usłudze Azure AD.

Jeśli włączono automatyczną obsługę administracyjną, przypisane Użytkownicy i/lub grupy zostaną dodane do kolejki aprowizacji w celu automatycznego aprowizacji.
    
 * Jeśli skonfigurowano tylko obiekty użytkownika do obsługi administracyjnej, przypisane do nich użytkownicy są umieszczani w kolejce aprowizacji, a wszyscy użytkownicy będący członkami wszelkich przypisanych grup są umieszczani w kolejce aprowizacji. 
    
 * Jeśli obiekty grupy zostały skonfigurowane do obsługi administracyjnej, wszystkie przypisane obiekty grupy są obsługiwane do pola i wszyscy użytkownicy będący członkami tych grup. Członkostwa grup i użytkowników są zachowywane po zapisaniu do pola.

> [!TIP] 
> Możesz również włączyć opcję pojedyncze Sign-On oparte na protokole SAML, postępując zgodnie z instrukcjami podanymi w [Azure Portal](https://portal.azure.com). Logowanie jednokrotne można skonfigurować niezależnie od automatycznej aprowizacji, chociaż te dwie funkcje napadają nawzajem.

### <a name="to-configure-automatic-user-account-provisioning"></a>Aby skonfigurować automatyczne Inicjowanie obsługi konta użytkownika:

Celem tej sekcji jest utworzenie konspektu, jak włączyć obsługę administracyjną kont użytkowników Active Directory.

1. W [Azure Portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje** .

2. Jeśli masz już skonfigurowane pole do logowania jednokrotnego, Wyszukaj swoje wystąpienie pola przy użyciu pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i Wyszukaj **pole** w galerii aplikacji. Wybierz pole z wyników wyszukiwania i Dodaj je do listy aplikacji.

3. Wybierz swoje wystąpienie pola, a następnie wybierz kartę **aprowizacji** .

4. Ustaw **Tryb aprowizacji** na **Automatyczny**. 

    ![Zrzut ekranu przedstawiający kartę Inicjowanie obsługi dla pola w Azure Portal. Tryb aprowizacji jest ustawiony na automatyczny, a Autoryzacja jest wyróżniona w poświadczeniach administratora.](./media/box-userprovisioning-tutorial/provisioning.png)

5. W sekcji **poświadczenia administratora** kliknij przycisk **Autoryzuj** , aby otworzyć okno dialogowe logowania do pola w nowym oknie przeglądarki.

6. Na stronie **Zaloguj się, aby udzielić dostępu do pola** podaj wymagane poświadczenia, a następnie kliknij przycisk **Autoryzuj**. 
   
    ![Zrzut ekranu przedstawiający ekran Zaloguj się, aby udzielić dostępu do pola, pokazujący wpis dla wiadomości E-mail i hasła oraz przycisk Autoryzuj.](./media/box-userprovisioning-tutorial/IC769546.png "Włącz automatyczne Inicjowanie obsługi użytkowników")

7. Kliknij przycisk **Udziel dostępu do pola** , aby autoryzować tę operację i powrócić do Azure Portal. 
   
    ![Zrzut ekranu przedstawiający ekran Autoryzuj dostęp w usłudze Box, który pokazuje komunikat objaśniający i przycisk Udziel dostępu do pola.](./media/box-userprovisioning-tutorial/IC769549.png "Włącz automatyczne Inicjowanie obsługi użytkowników")

8. W Azure Portal kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z aplikacją usługi Box. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Box ma uprawnienia administratora zespołu i spróbuj ponownie wykonać krok **"Autoryzuj"** .

9. Wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji w polu **E-mail powiadomienia** , i zaznacz pole wyboru.

10. Kliknij przycisk **Zapisz.**

11. W sekcji mapowania wybierz pozycję **synchronizuj Azure Active Directory użytkowników do pola.**

12. W sekcji **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które są synchronizowane z usługą Azure AD do usługi Box. Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do kont użytkowników w usłudze Box dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

13. Aby włączyć usługę Azure AD Provisioning Service dla usługi Box, Zmień **stan aprowizacji** na **włączone** w sekcji Ustawienia.

14. Kliknij przycisk **Zapisz.**

Spowoduje to rozpoczęcie synchronizacji początkowej dla wszystkich użytkowników i/lub grup przypisanych do pola w sekcji Użytkownicy i grupy. Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa jest uruchomiona. Za pomocą sekcji **szczegóły synchronizacji** można monitorować postęp i wykonywać linki do dzienników aktywności aprowizacji, które opisują wszystkie akcje wykonywane przez usługę aprowizacji w aplikacji Box.

Aby uzyskać więcej informacji na temat sposobu odczytywania dzienników aprowizacji usługi Azure AD, zobacz [Raportowanie dotyczące automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md).

W dzierżawie w usłudze Box zsynchronizowani użytkownicy są wyświetlani w obszarze **Użytkownicy zarządzani** w **konsoli administracyjnej**.

![Stan integracji](./media/box-userprovisioning-tutorial/IC769556.png "Stan integracji")


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](box-tutorial.md)
