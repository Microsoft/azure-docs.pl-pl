---
title: Zapobiegaj atakom przy użyciu blokady inteligentnej — Azure Active Directory
description: Dowiedz się, jak Azure Active Directory inteligentna blokada pomaga chronić organizację przed atakami z wykorzystaniem wymuszenia, które próbują odgadnąć hasła użytkowników.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8a57e77ea572f899bf540714e8ac9968988f028
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741732"
---
# <a name="protect-user-accounts-from-attacks-with-azure-active-directory-smart-lockout"></a>Ochrona kont użytkowników przed atakami za pomocą usługi inteligentnego blokowania Azure Active Directory

Inteligentna blokada ułatwia zablokowanie nieprawidłowych aktorów próbujących odgadnąć hasła użytkowników lub użyć metod w celu uzyskania w programie. Inteligentna blokada może rozpoznawać logowania pochodzące od prawidłowych użytkowników i traktować je inaczej niż osoby atakujące i inne nieznane źródła. Osoby atakujące są Zablokowani, podczas gdy użytkownicy nadal uzyskują dostęp do swoich kont i mogą pracować wydajnie.

## <a name="how-smart-lockout-works"></a>Jak działa inteligentna blokada

Domyślnie inteligentna blokada blokuje konto przed próbami logowania przez jedną minutę po 10 nieudanych próbach. Konto jest blokowane ponownie po każdej kolejnej nieudanej próbie logowania w ciągu jednej minuty na pierwszy czas i dłużej w kolejnych próbach. Aby zminimalizować sposoby obejścia tego problemu przez atakującego, nie ujawniamy informacji o szybkości, z jaką czas blokady rośnie w przypadku dodatkowych nieudanych prób logowania.

Inteligentna blokada śledzi ostatnie trzy niewłaściwe skróty haseł, aby uniknąć zwiększenia licznika blokady dla tego samego hasła. Jeśli ktoś wprowadzi to samo złe hasło wielokrotnie, to zachowanie nie spowoduje zablokowania konta.

> [!NOTE]
> Funkcja śledzenia wyznaczania wartości skrótu nie jest dostępna dla klientów z włączonym uwierzytelnianiem przekazującym, ponieważ uwierzytelnianie odbywa się lokalnie, a nie w chmurze.

Wdrożenia federacyjne korzystające z AD FS 2016 i AF FS 2019 mogą zapewnić podobne korzyści przy użyciu [blokady ekstranetu i blokady inteligentnej AD FS w ekstranecie](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection).

Inteligentna blokada jest zawsze włączona w przypadku wszystkich klientów usługi Azure AD z tymi domyślnymi ustawieniami, które oferują odpowiednie połączenie zabezpieczeń i użyteczności. Dostosowanie ustawień blokady inteligentnej przy użyciu wartości specyficznych dla Twojej organizacji wymaga, aby użytkownicy korzystali z licencji na Azure AD — wersja Premium P1 lub wyższą.

Użycie inteligentnej blokady nie gwarantuje, że oryginalny użytkownik nigdy nie jest zablokowany. Po zablokowaniu konta użytkownika przez inteligentną blokadę firma Microsoft zaleca, aby nie zablokować oryginalnego użytkownika. Usługa blokowania próbuje upewnić się, że niewłaściwe podmioty nie mogą uzyskać dostępu do oryginalnego konta użytkownika. Obowiązują następujące zastrzeżenia:

* Każde centrum danych usługi Azure AD śledzi blokowanie niezależnie. Użytkownik ma (*threshold_limit * datacenter_count*) liczbę prób, jeśli użytkownik trafi każde centrum danych.
* Inteligentna blokada używa znanej lokalizacji a nieznanej lokalizacji, aby rozróżnić niewłaściwy aktora i oryginalny użytkownik. Nieznane i znane lokalizacje mają oddzielne liczniki blokady.

Inteligentna blokada może być zintegrowana z wdrożeniami hybrydowymi, które korzystają z funkcji synchronizacji skrótów haseł lub uwierzytelniania przekazywanego w celu ochrony lokalnych kont Active Directory Domain Services (AD DS) przed zablokowaniem przez osoby atakujące. Odpowiednio ustawiając zasady inteligentnego blokowania w usłudze Azure AD, ataki można odfiltrować przed osiągnięciem AD DS lokalnego.

W przypadku korzystania z [uwierzytelniania przekazywanego](../hybrid/how-to-connect-pta.md)są stosowane następujące zagadnienia:

* Wartość progowa blokady usługi Azure AD jest **mniejsza** niż wartość progowa blokady konta AD DS. Ustaw wartości tak, aby wartość progowa blokady konta AD DS wynosić co najmniej dwa lub trzy razy dłużej niż wartość progowa blokady usługi Azure AD.
* Czas trwania blokady usługi Azure AD musi być ustawiony na wartość większą niż AD DS Resetuj licznik blokady konta po czasie trwania. Czas trwania usługi Azure AD jest ustawiany w sekundach, podczas gdy czas trwania jest ustawiany w minutach.

Na przykład jeśli chcesz, aby licznik usługi Azure AD miał wartość wyższą niż AD DS, usługa Azure AD będzie 120 sekund (2 minuty), a w lokalnej usłudze AD jest ustawiona na 1 minutę (60 s).

> [!IMPORTANT]
> Obecnie administrator nie może odblokować kont w chmurze użytkowników, jeśli zostały one zablokowane przez funkcję inteligentnej blokady. Administrator musi poczekać na wygaśnięcie czasu trwania blokady. Użytkownik może jednak odblokować przy użyciu funkcji samoobsługowego resetowania hasła (SSPR) z zaufanego urządzenia lub lokalizacji.

## <a name="verify-on-premises-account-lockout-policy"></a>Weryfikuj Zasady blokady konta lokalnego

Aby sprawdzić lokalne zasady blokady konta AD DS, wykonaj następujące kroki w systemie przyłączonym do domeny z uprawnieniami administratora:

1. Otwórz narzędzie do zarządzania zasady grupy.
2. Edytuj zasady grupy zawierające zasady blokady konta w organizacji, takie jak **domyślne zasady domeny**.
3. Przejdź do ustawień **Konfiguracja komputera**  >  **zasady**  >  **Ustawienia systemu Windows** ustawienia  >  **zabezpieczeń** konta  >  **zasady kont**  >  **zasad blokady**.
4. Sprawdź **próg blokady konta** i **Zresetuj licznik blokady konta po** wartościach.

![Modyfikowanie zasad blokady lokalnych kont Active Directory](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Zarządzanie wartościami inteligentnych blokad usługi Azure AD

Na podstawie wymagań organizacji można dostosować wartości inteligentnego blokowania usługi Azure AD. Dostosowanie ustawień blokady inteligentnej przy użyciu wartości specyficznych dla Twojej organizacji wymaga, aby użytkownicy korzystali z licencji na Azure AD — wersja Premium P1 lub wyższą.

Aby sprawdzić lub zmodyfikować wartości blokady inteligentnej dla organizacji, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj i wybierz pozycję *Azure Active Directory*, a następnie **Security** wybierz pozycję  >  **metody uwierzytelniania** zabezpieczeń  >  **Ochrona hasłem**.
1. Ustaw **próg blokady** w zależności od liczby nieudanych logowań dozwolonych na koncie przed pierwszym zablokowaniem.

    Wartość domyślna to 10.

1. Ustaw **czas trwania blokady (w sekundach**) na długość w sekundach każdej blokady.

    Wartość domyślna to 60 sekund (jedna minuta).

> [!NOTE]
> Jeśli pierwsze logowanie po zablokowaniu również zakończy się niepowodzeniem, konto zostanie ponownie zablokowane. Jeśli konto zostanie zablokowane wielokrotnie, czas trwania blokady wzrasta.

![Dostosuj zasady inteligentnego blokowania usługi Azure AD w Azure Portal](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Jak ustalić, czy funkcja inteligentnego blokowania działa, czy nie

Po wyzwoleniu progu inteligentnego blokowania zostanie wyświetlony następujący komunikat, gdy konto jest zablokowane:

*Twoje konto jest tymczasowo zablokowane, aby zapobiec nieautoryzowanemu użyciu. Spróbuj ponownie później, a jeśli nadal masz problemy, skontaktuj się z administratorem.*

## <a name="next-steps"></a>Następne kroki

Aby dodatkowo dostosować środowisko, można [skonfigurować niestandardowe zabronione hasła dla ochrony hasłem usługi Azure AD](tutorial-configure-custom-password-protection.md).

Aby ułatwić użytkownikom Resetowanie lub zmienianie hasła z przeglądarki sieci Web, można skonfigurować samoobsługowe [resetowanie haseł w usłudze Azure AD](tutorial-enable-sspr.md).
