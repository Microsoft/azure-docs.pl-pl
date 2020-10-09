---
title: Tryb udostępnionego urządzenia — Omówienie
titleSuffix: Microsoft identity platform | Azure
description: Dowiedz się więcej o trybie udostępnionego urządzenia, aby włączyć udostępnianie urządzeń dla procesów roboczych Firstline.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 514782d62f117af5bfff4a5d2b3354c4e263eece
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80550242"
---
# <a name="overview-of-shared-device-mode"></a>Przegląd trybu udostępnionego urządzenia

Tryb udostępnionego urządzenia jest funkcją Azure Active Directory, która umożliwia tworzenie aplikacji, które obsługują procesy robocze Firstline i włączają tryb udostępnionego urządzenia na urządzeniach, które zostały wdrożone.

> [!NOTE]
> Ta funkcja jest dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-are-firstline-workers"></a>Co to są procesy robocze Firstline?

Pracownicy Firstline to pracownicy detaliczni, konserwacje i agenci pól, personel medyczny i inni użytkownicy, którzy nie znajdują się przed komputerem lub używają firmowej poczty e-mail do współpracy. Poniższe sekcje zawierają zagadnienia i wyzwania wspierające pracowników Firstline, a następnie wprowadzenie do funkcji oferowanych przez firmę Microsoft, które umożliwiają aplikacji korzystanie przez pracowników Firstlineych w organizacji.

### <a name="challenges-of-supporting-firstline-workers"></a>Wyzwania dla wspierających pracowników Firstline

Włączenie przepływów pracy procesu roboczego Firstline obejmuje wyzwania, które nie są zwykle prezentowane przez typowych pracowników przetwarzających informacje. Takie wyzwania mogą obejmować wysoką ilość obrotów i mniej znajomości z podstawowymi narzędziami do pracy w organizacji. Aby umożliwić swoim pracownikom Firstline, organizacje stosują różne strategie. Niektóre z nich przyjmują strategię przydzielenia urządzenia (BYOD), w której ich pracownicy korzystają z aplikacji firmowych na swoim telefonie osobistym, podczas gdy inne zapewniają swoim pracownikom udostępnione urządzenia, takie jak tablety iPad lub Android.

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>Obsługa wielu użytkowników na urządzeniach przeznaczonych dla jednego użytkownika

Ponieważ urządzenia przenośne z systemem iOS lub Android zostały zaprojektowane dla pojedynczych użytkowników, większość aplikacji optymalizuje ich środowisko do użytku przez jednego użytkownika. W ramach tego zoptymalizowanego środowiska należy umożliwić Logowanie jednokrotne w aplikacjach i przechowywanie użytkowników zalogowanych na ich urządzeniach. Gdy użytkownik usunie swoje konto z aplikacji, zazwyczaj aplikacja nie będzie uwzględniać zdarzenia związanego z zabezpieczeniami. Wiele aplikacji jeszcze nie zachowuje poświadczeń użytkownika na potrzeby szybkiego logowania. Możesz nawet zaznajomić się z Tobą po usunięciu aplikacji z urządzenia przenośnego, a następnie jej ponownym zainstalowaniu, tylko w celu odnalezienia nadal zalogowanego użytkownika.

### <a name="global-sign-in-and-sign-out-sso"></a>Logowanie globalne i wylogowywanie (SSO)

Aby umożliwić pracownikom organizacji korzystanie z jej aplikacji w puli urządzeń udostępnianych przez tych pracowników, deweloperzy muszą zapewnić odwrotne działanie. Pracownicy powinni mieć możliwość wybrania urządzenia z puli i przeprowadzenia jednego gestu w celu "przydzielenia go" przez czas ich przesunięcia. Po zakończeniu przesunięcia powinno być możliwe wykonanie innego gestu w celu wylogowania się globalnie na urządzeniu wraz ze wszystkimi informacjami osobistymi i firmowymi, dzięki czemu mogą one zwrócić do puli urządzeń. Ponadto, jeśli pracownik zapomni się wylogować, urządzenie powinno zostać automatycznie wylogowane na końcu przesunięcia i/lub po okresie braku aktywności.

Azure Active Directory włącza te scenariusze przy użyciu funkcji o nazwie **tryb udostępnionego urządzenia**.

## <a name="introducing-shared-device-mode"></a>Wprowadzenie do trybu udostępnionego urządzenia

Jak wspomniano, tryb udostępnionego urządzenia jest funkcją Azure Active Directory, która umożliwia:

* Twórz aplikacje obsługujące Firstline procesy robocze
* Wdrażanie urządzeń w celu Firstline procesów roboczych i Włączanie trybu udostępnionego urządzenia

### <a name="build-applications-that-support-firstline-workers"></a>Twórz aplikacje obsługujące Firstline procesy robocze

Można obsługiwać Firstline procesy robocze w aplikacjach za pomocą biblioteki uwierzytelniania firmy Microsoft (MSAL) i [aplikacji Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) w celu włączenia stanu urządzenia o nazwie *tryb udostępniania urządzenia*. Gdy urządzenie jest w trybie udostępnionego urządzenia, firma Microsoft udostępnia swoją aplikację, aby umożliwić jej modyfikowanie jej działania w oparciu o stan użytkownika na urządzeniu, chroniąc dane użytkowników.

Obsługiwane są następujące funkcje:

* **Zaloguj się na urządzeniu użytkownika** za pomocą dowolnej obsługiwanej aplikacji.
* **Wyloguj się z urządzenia użytkownika** za pomocą dowolnej obsługiwanej aplikacji.
* **Zbadaj stan urządzenia** , aby określić, czy aplikacja znajduje się na urządzeniu, które jest w trybie udostępniania.
* **Zbadaj stan urządzenia użytkownika** na urządzeniu, aby określić, czy wszystkie zmiany zostały zmienione od czasu ostatniego użycia aplikacji.

Obsługa trybu udostępnionego urządzenia powinna być uznawana za rozszerzenie zabezpieczeń i uaktualnienie funkcji dla aplikacji, a także może pomóc zwiększyć jej wdrożenie w środowiskach wysoce regulowanych, takich jak opieka zdrowotna i finanse.

Użytkownicy zależą od użytkownika, aby upewnić się, że ich dane nie są wyciekiem innemu użytkownikowi. Tryb udostępniania urządzenia zapewnia przydatne sygnały wskazujące aplikację, że nastąpiła zmiana, która powinna być zarządzana. Aplikacja jest odpowiedzialna za sprawdzanie stanu użytkownika na urządzeniu za każdym razem, gdy aplikacja jest używana, przez wyczyszczenie danych poprzedniego użytkownika. Obejmuje to również ponowne załadowanie z tła w ramach wielozadań. Po zmianie użytkownika należy upewnić się, że zarówno dane poprzedniego użytkownika są wyczyszczone, jak i wszystkie dane w pamięci podręcznej wyświetlane w aplikacji zostaną usunięte. Zalecamy, aby po dodaniu możliwości trybu udostępnionego urządzenia do aplikacji zawsze wykonywałeś dokładny proces przeglądu zabezpieczeń.

Aby uzyskać szczegółowe informacje na temat modyfikowania aplikacji w celu obsługi trybu udostępnionego urządzenia, zobacz sekcję [następne kroki](#next-steps) na końcu tego artykułu.

### <a name="deploy-devices-to-firstline-workers-and-turn-on-shared-device-mode"></a>Wdrażanie urządzeń w celu Firstline procesów roboczych i Włączanie trybu udostępnionego urządzenia

Gdy aplikacje obsługują tryb udostępnionego urządzenia i zawierają wymagane zmiany danych i zabezpieczeń, można je anonsować jako możliwe do użycia przez Firstline pracowników.

Administratorzy urządzeń organizacji mogą wdrażać swoje urządzenia i aplikacje w swoich sklepach i miejscach w miejscu pracy za pomocą rozwiązania do zarządzania urządzeniami przenośnymi (MDM), takiego jak Microsoft Intune. Częścią procesu aprowizacji jest oznaczenie urządzenia jako *urządzenia udostępnionego*. Administratorzy konfigurują tryb udostępnionego urządzenia, wdrażając [aplikację Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) i ustawiając tryb udostępnionego urządzenia za pomocą parametrów konfiguracji. Po wykonaniu tych kroków wszystkie aplikacje obsługujące tryb udostępnionego urządzenia będą używać aplikacji Microsoft Authenticator do zarządzania stanem użytkownika i zapewnienia funkcji zabezpieczeń dla urządzenia i organizacji.

## <a name="next-steps"></a>Następne kroki

Obsługujemy platformy iOS i Android dla trybu udostępnionego urządzenia. Zapoznaj się z poniższą dokumentacją platformy, aby rozpocząć obsługę procesów roboczych Firstline w aplikacjach.

* [Obsługa trybu udostępnionego urządzenia dla systemu iOS](msal-ios-shared-devices.md)
* [Obsługa trybu udostępnionego urządzenia dla systemu Android](msal-android-shared-devices.md)
