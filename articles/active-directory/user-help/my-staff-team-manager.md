---
title: Zarządzanie hasłami i numerami telefonów za pomocą programu Mój personel (wersja zapoznawcza) — Azure AD | Dokumenty firmy Microsoft
description: Zarządzanie hasłami i numerami telefonów dla użytkowników za pomocą usługi Mój personel
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: article
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: c5804d9e386ff1b17c08fc4ce4f3a232ebe1a39b
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394438"
---
# <a name="delegate-user-management-with-my-staff-preview"></a>Delegowanie zarządzania użytkownikami za pomocą mojego personelu (wersja zapoznawcza)

Organizacja może używać **moich pracowników** do delegowania zadań zarządzania użytkownikami do osób odpowiedzialnych, takich jak kierownik sklepu lub kierownik zespołu, aby pomóc swoim pracownikom uzyskać dostęp do potrzebnych im aplikacji. Jeśli członek zespołu nie może uzyskać dostępu do aplikacji, ponieważ nie zapomni hasła, wydajność zostanie utracona. Zwiększa to również koszty pomocy technicznej i powoduje wąskie gardło w procesach administracyjnych.  Dzięki mojemu personelowi członek zespołu, który nie może uzyskać dostępu do swojego konta, może odzyskać dostęp za pomocą zaledwie kilku kliknięć, bez potrzeby pomocy administratora.

## <a name="manage-your-staff-in-my-staff"></a>Zarządzaj swoimi pracownikami w my staff

Zarządzanie członkami zespołu w moim sztabie jest proste. Aby rozpocząć, [przejdź do pozycji Mój personel](https://aka.ms/mystaff), wybierz zespół lub lokalizację, a następnie wybierz użytkownika. Lokalizacje i członkowie zespołu w lokalizacji są określane przez administratora IT i nie można ich zmienić.

Jeśli zarządzasz więcej niż jedną lokalizacją, gdy przejdziesz do strony Mój personel, musisz wybrać lokalizację, aby wyświetlić członka zespołu, który jest przypisany do lokalizacji.

Jeśli nie masz jeszcze wystarczających uprawnień dostępu do mojego personelu, zobaczysz następujący komunikat "Ups, wydaje się, że nie masz uprawnień do wyświetlania moich pracowników w tej chwili. Aby uzyskać więcej informacji, skontaktuj się z administratorem."

### <a name="find-a-staff-member-in-my-staff"></a>Znajdź pracownika w : Mój personel

Aby rozpocząć zarządzanie nim, należy otworzyć profil pracownika.

1. [Otwórz pozycję Mój personel](https://aka.ms/mystaff) i w razie potrzeby wybierz lokalizację.

    ![Wybieranie lokalizacji dla członka zespołu w obszarze Mój personel](media/my-staff-team-manager/allaus.png)

1. Otwórz profil członka zespołu.

    ![Wybierz jednego z użytkowników w lokalizacji w obszarze Mój personel](media/my-staff-team-manager/aupage.png)

## <a name="reset-a-user-password"></a>Resetowanie hasła użytkownika

Jeśli twoja organizacja udzieliła Ci uprawnień, możesz zresetować hasła dla członków personelu.

1. [Otwórz mój personel](https://aka.ms/mystaff).
1. Otwórz profil pracownika.
1. Wybierz **pozycję Resetuj hasło**.

    ![Resetowanie hasła użytkownika w obszarze Mój personel](media/my-staff-team-manager/resetpassword1.png)

1. Wygeneruj lub wprowadź nowe hasło. Może zostać wyświetlone automatycznie wygenerowane hasło tymczasowe lub może zostać wyświetlony monit o wprowadzenie tymczasowego hasła dla użytkownika.

    ![Kopiowanie tymczasowego hasła użytkownika po zresetowaniu w folderze Mój personel](media/my-staff-team-manager/resetpassword2.png)

Po zresetowaniu hasła użytkownika podaj użytkownikowi hasło tymczasowe. Gdy użytkownik zaloguje się przy za pomocą tymczasowego hasła, musi je zmienić.

## <a name="manage-a-users-phone-number"></a>Zarządzanie numerem telefonu użytkownika

Jeśli twoja organizacja udzieliła Ci uprawnień, możesz zarządzać numerami telefonów dla członków personelu.

### <a name="add-a-phone-number"></a>Dodawanie numeru telefonu

1. [Otwórz mój personel](https://aka.ms/mystaff).
1. Otwórz profil pracownika.
1. Wybierz **pozycję Dodaj numer telefonu**.

    ![Dodawanie numeru telefonu użytkownika w aplikacji Mój personel](media/my-staff-team-manager/addphone1.png)

1. Dodaj numer telefonu i wybierz pozycję **Zapisz**.

    ![Zapisywanie dodanego numeru telefonu użytkownika w aplikacji Mój personel](media/my-staff-team-manager/addphone2.png)

Po zarejestrowaniu numeru telefonu dla użytkownika może go używać do logowania się za pomocą wiadomości SMS, przeprowadzania weryfikacji dwuetapowej lub samodzielnego resetowania hasła, w zależności od ustawień organizacji.

![Nowy numer telefonu zarejestrowany w my staff](media/my-staff-team-manager/addphone3.png)

### <a name="edit-a-phone-number"></a>Edytowanie numeru telefonu

1. [Otwórz mój personel](https://aka.ms/mystaff).
1. Otwórz profil pracownika.
1. Wybierz **pozycję Edytuj numer telefonu**.

    ![Wybierz pozycję Edytuj z profilu użytkownika w obszarze Mój personel](media/my-staff-team-manager/editphone2.png)

1. Wprowadź nowy numer telefonu i wybierz pozycję **Zapisz**.

    ![Edytowanie numeru telefonu pracownika w aplikacji Mój personel](media/my-staff-team-manager/editphone1.png)

### <a name="enable-phone-number-sign-in-for-a-user"></a>Włączanie logowania numeru telefonu dla użytkownika

Jeśli logowanie przy użyciu numeru telefonu jako nazwy użytkownika (logowania SMS) jest włączone w organizacji, można dodać to uwierzytelnianie do istniejącego numeru telefonu użytkownika.

1. [Otwórz mój personel](https://aka.ms/mystaff).
1. Otwórz profil pracownika.
1. Jeśli u dołu ekranu znajduje się komunikat informujący, że logowanie się przy użyciu numeru telefonu jako nazwy użytkownika jest dla Ciebie dostępne, wybierz **włącz,** aby rozpocząć proces. Ten komunikat pojawia się, jeśli użytkownik ma włączoną funkcję logowania się przy za pomocą swojego numeru telefonu.

    ![Wyświetlanie wiadomości, gdy logowanie się przez telefon jest obsługiwane w lokalizacji w umiejeń i personelu](media/my-staff-team-manager/enableforms1.png)

1. Po zakończeniu wybierz przycisk **OK.**

    ![Usuwanie numeru telefonu pracownika w aplikacji Mój personel](media/my-staff-team-manager/enableforms2.png)

### <a name="remove-a-phone-number"></a>Usuwanie numeru telefonu

1. [Otwórz mój personel](https://aka.ms/mystaff).
1. Otwórz profil pracownika.
1. Wybierz **pozycję Usuń numer telefonu**.
1. Po zakończeniu wybierz **pozycję Usuń.**

    ![Usuwanie numeru telefonu pracownika w aplikacji Mój personel](media/my-staff-team-manager/deletephone1.png)
