---
title: Ustaw limit czasu braku aktywności na poziomie katalogu dla użytkowników Azure Portal | Microsoft Docs
description: Administratorzy mogą wymusić maksymalny czas bezczynności przed wylogowaniem sesji. Zasady limitu czasu bezczynności są ustawiane na poziomie katalogu.
services: azure-portal
keywords: Ustawienia, limit czasu
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: how-to
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 2efee29ba709607e1e7935215ca7689a39966de1
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84764166"
---
# <a name="set-directory-level-inactivity-timeout"></a>Ustaw limit czasu nieaktywności poziomu katalogu

Ustawienie limitu czasu bezczynności pomaga chronić swoje zasoby przed nieautoryzowanym dostępem, jeśli użytkownicy zapomnią zabezpieczyć swoją stację roboczą. Gdy użytkownik jest bezczynny przez pewien czas, sesja Azure Portal zostanie wylogowana automatycznie. Administratorzy w [roli administratora globalnego](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) mogą wymusić maksymalny czas bezczynności przed wylogowaniem sesji. Ustawienie limitu czasu nieaktywności dotyczy poziomu katalogu. Aby uzyskać więcej informacji o katalogach, zobacz [Active Directory Domain Services Omówienie](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

## <a name="configure-the-inactive-timeout-setting"></a>Skonfiguruj ustawienie nieaktywnego limitu czasu

Jeśli jesteś administratorem globalnym i chcesz wymusić ustawienie limitu czasu bezczynności dla wszystkich użytkowników Azure Portal, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Ustawienia** z globalnego nagłówka strony.
3. Wybierz pozycję tekst linku **Skonfiguruj limit czasu na poziomie katalogu**.

    ![Zrzut ekranu przedstawiający ustawienia portalu z wyróżnionym tekstem linku](./media/admin-timeout/settings.png)

4. Zostanie otwarta nowa strona. Na stronie **Konfiguruj limit czasu braku aktywności na poziomie katalogu** wybierz opcję **Włącz limit czasu bezczynności dla Azure Portal** , aby włączyć ustawienie.
5. Następnie wprowadź **godziny** i **minuty** maksymalnego czasu, przez jaki użytkownik może być bezczynny, zanim sesja zostanie wylogowana automatycznie.
6. Wybierz przycisk **Zastosuj**.

    ![Zrzut ekranu przedstawiający stronę służącą do ustawiania limitu czasu nieaktywności poziomu katalogu](./media/admin-timeout/configure.png)

Aby upewnić się, że zasady limitu czasu bezczynności są ustawione prawidłowo, wybierz pozycję **powiadomienia** z globalnego nagłówka strony. Sprawdź, czy na liście znajduje się powiadomienie o powodzeniu.

  ![Zrzut ekranu przedstawiający pomyślne powiadomienie o pomyślnym przekroczeniu limitu czasu nieaktywności na poziomie katalogu](./media/admin-timeout/confirmation.png)

Ustawienie zacznie obowiązywać w przypadku nowych sesji. Nie będzie od razu stosowana dla wszystkich użytkowników, którzy są już zalogowani.

> [!NOTE]
> Jeśli administrator globalny skonfigurował ustawienie limitu czasu poziomu katalogu, użytkownicy mogą przesłonić zasady i ustawić własne nieaktywne okresy wylogowania. Użytkownik musi jednak wybrać przedział czasu, który jest mniejszy niż ustawiony na poziomie katalogu przez administratora globalnego.
>

## <a name="next-steps"></a>Następne kroki

* [Ustawianie preferencji witryny Azure Portal](set-preferences.md)
* [Eksportowanie i usuwanie ustawień użytkownika](azure-portal-export-delete-settings.md)
* [Włączanie dużego kontrastu lub zmienianie motywu](azure-portal-change-theme-high-contrast.md)
