---
title: Zarządzanie zagrożeniami dla zasobów i danych
titleSuffix: Azure AD B2C
description: Dowiedz się więcej o technikach wykrywania i łagodzenia ataków typu "odmowa usługi" i ataki z hasłami w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e5184698cab1874f327173fb30cf527feee48cad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85384978"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Zarządzanie zagrożeniami dla zasobów i danych w Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) ma wbudowane funkcje, które mogą pomóc w ochronie przed zagrożeniami dla zasobów i danych. Zagrożenia te obejmują ataki typu "odmowa usługi" i ataki z hasłem. Ataki typu "odmowa usługi" mogą sprawiać, że zasoby są niedostępne dla zamierzonych użytkowników. Ataki z wykorzystaniem hasła prowadzą do nieautoryzowanego dostępu do zasobów.

## <a name="denial-of-service-attacks"></a>Ataki typu "odmowa usługi"

Azure AD B2C obrony przed atakami na zalanie SYN przy użyciu pliku cookie SYN. Azure AD B2C również chroni przed atakami typu "odmowa usługi" przy użyciu limitów stawek i połączeń.

## <a name="password-attacks"></a>Ataki z hasłem

Hasła, które są ustawiane przez użytkowników, muszą być odpowiednio skomplikowane. Azure AD B2C ma stosowane techniki zaradcze na potrzeby ataków na hasła. Środki zaradcze obejmują wykrywanie ataków z hasłami w postaci wymuszenia i hasła słownika. Korzystając z różnych sygnałów, Azure AD B2C analizuje integralność żądań. Azure AD B2C zaprojektowano w celu inteligentnego odróżnienia zamierzonych użytkowników od hakerów i botnetami.

Azure AD B2C używa zaawansowanej strategii do blokowania kont. Konta są blokowane na podstawie adresu IP żądania i wprowadzonych haseł. Czas trwania blokady również zwiększa się w zależności od prawdopodobieństwa, że jest to atak. Gdy hasło zostanie ponowione 10 razy (domyślny próg próby), nastąpi Jednominutowa blokada. Następnym razem, gdy logowanie nie powiedzie się po tym, gdy konto zostanie odblokowane (to oznacza, że po upływie okresu blokady następuje automatyczne odblokowanie konta), inna blokada Jednominutowa zostanie uruchomiona i będzie kontynuowana dla każdej nieudanej operacji logowania. Wprowadzanie tego samego hasła wielokrotnie nie jest traktowane jako wiele nieudanych logowań.

Pierwsze 10 okresów blokowania to jedna minuta. Kolejne 10 okresów blokowania są nieco dłuższe i zwiększają czas trwania po każdym 10 okresach blokady. Licznik blokady resetuje do zera po pomyślnym zalogowaniu, gdy konto nie jest zablokowane. Okresy blokowania mogą trwać do pięciu godzin.

## <a name="manage-password-protection-settings"></a>Zarządzanie ustawieniami ochrony hasłem

Aby zarządzać ustawieniami ochrony hasłem, w tym progiem blokady:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. Użyj filtru **katalogów i subskrypcji** w górnym menu, aby wybrać katalog, który zawiera dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. W obszarze **zabezpieczenia**wybierz pozycję **metody uwierzytelniania (wersja zapoznawcza)**, a następnie wybierz pozycję **Ochrona hasłem**.
1. Wprowadź odpowiednie ustawienia ochrony hasłem, a następnie wybierz pozycję **Zapisz**.

    ![Strona ochrony hasłem Azure Portal w ustawieniach usługi Azure AD](./media/threat-management/portal-02-password-protection.png)
    <br />*Ustawienie progu blokady na 5 w ustawieniach **ochrony haseł** *.

## <a name="view-locked-out-accounts"></a>Wyświetl zablokowane konta

Aby uzyskać informacje o kontach zablokowanych, można sprawdzić [raport dotyczący działań związanych z logowaniem](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)Active Directory. W obszarze **stan**wybierz pozycję **Niepowodzenie**. Nieudane próby logowania przy użyciu **kodu błędu logowania** `50053` wskazują zablokowane konto:

![Sekcja raportu logowania usługi Azure AD przedstawiająca zablokowane konto](./media/threat-management/portal-01-locked-account.png)

Aby dowiedzieć się więcej na temat wyświetlania raportu działania związanego z logowaniem w Azure Active Directory, zobacz [kody błędów raportów działań związanych z logowaniem](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md).
