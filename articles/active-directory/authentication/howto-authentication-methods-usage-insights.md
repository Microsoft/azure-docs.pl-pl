---
title: Działanie metod uwierzytelniania — Azure Active Directory
description: Omówienie metod uwierzytelniania zarejestrowanych i używanych przez organizację do logowania się i resetowania haseł.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6566ee7e744411fc3b7005938f95181e5c5ec94d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645183"
---
# <a name="authentication-methods-activity"></a>Działanie metod uwierzytelniania 

Pulpit nawigacyjny nowe działania metod uwierzytelniania umożliwia administratorom monitorowanie rejestracji i użycia metod uwierzytelniania w całej organizacji. Ta funkcja raportowania umożliwia organizacji korzystanie z metod, aby zrozumieć, jakie metody są rejestrowane i jak są używane.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>Uprawnienia i licencje

Następujące role mogą uzyskać dostęp do użycia i szczegółowych informacji:

- Czytelnik raportów
- Czytelnik zabezpieczeń
- Administrator zabezpieczeń
- Administrator globalny

 Aby uzyskać dostęp do użycia i szczegółowych informacji, wymagana jest licencja na Azure AD — wersja Premium P1 lub P2. Informacje o licencjach usługi Azure AD Multi-Factor Authentication i samoobsługowego resetowania hasła (SSPR) znajdują się w [witrynie Azure Active Directory cenowej](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Jak to działa

Aby uzyskać dostęp do informacji o użyciu metody uwierzytelniania i szczegółowych informacji:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Kliknij **Azure Active Directory**  >    >  **działanie metody uwierzytelniania** zabezpieczeń  >  .
1. Raport zawiera dwie karty: **rejestracja** i **użycie**.

   ![Omówienie działania metod uwierzytelniania](media/how-to-authentication-methods-usage-insights/registration-usage-tabs.png)

## <a name="registration-details"></a>Szczegóły rejestracji

Możesz uzyskać dostęp do [**karty Rejestracja**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) , aby wyświetlić liczbę użytkowników, którzy mogą korzystać z uwierzytelniania wieloskładnikowego, uwierzytelniania passowordless i samoobsługowego resetowania hasła. 

Kliknij pozycję **Użytkownicy z możliwością Multi-Factor Authentication platformy Azure**, **możliwość uwierzytelniania bez hasła** lub **możliwość samodzielnego resetowania hasła**, a także szczegółowe informacje o rejestracji użytkownika.

- **Użytkownicy korzystający z usługi azure Multi-Factor Authentication** pokazują podział użytkowników z możliwością uwierzytelniania wieloskładnikowego w usłudze Azure AD. Użytkownicy są uważani za mogą, jeśli są zarejestrowani do metody silnego uwierzytelniania i włączane przez zasady do korzystania z tej metody do wykonywania uwierzytelniania wieloskładnikowego. Ta liczba nie odzwierciedla użytkowników zarejestrowanych w usłudze MFA poza usługą Azure AD. 
- **Użytkownicy z uwierzytelnianiem bez hasła** pokazuje podział użytkowników, którzy mogą się zalogować, nie mając hasła. Obejmuje to użytkowników zarejestrowanych dla usługi FIDO2, usługi Windows Hello dla firm oraz logowania za pomocą telefonu bez hasła przy użyciu aplikacji Microsoft Authenticator. 
- **Użytkownicy korzystający z funkcji samoobsługowego resetowania hasła** przedstawiają podział użytkowników z możliwością samoobsługowego resetowania hasła. Użytkownicy są uważani za SSPR, jeśli są zarejestrowani do wystarczającej liczby metod w celu spełnienia zasad SSPR organizacji i włączenia SSPR. 

  ![Zrzut ekranu przedstawiający użytkowników, którzy mogą przeprowadzić rejestrację](media/how-to-authentication-methods-usage-insights/users-capable.png)

**Użytkownicy zarejestrowani przez metodę uwierzytelniania pokazują,** ile użytkowników jest zarejestrowanych dla każdej metody uwierzytelniania. Kliknij metodę uwierzytelniania, aby zobaczyć, którzy użytkownicy są zarejestrowani dla tej metody. 

![Zrzut ekranu zarejestrowanego użytkownika](media/how-to-authentication-methods-usage-insights/users-registered.png)

**Metoda Ostatnia Rejestracja przez metodę uwierzytelniania** przedstawia liczbę rejestracji metod uwierzytelniania zakończonych powodzeniem i niepowodzeniem przez metodę uwierzytelniania. Kliknij metodę uwierzytelniania, aby zobaczyć ostatnie zdarzenia rejestracji dla tej metody.

![Zrzut ekranu przedstawiający ostatnio zarejestrowane](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>Szczegóły użycia

Raport **użycia** zawiera listę metod uwierzytelniania używanych przez użytkowników do logowania i resetowania haseł.

![Zrzut ekranu przedstawiający stronę użycia](media/how-to-authentication-methods-usage-insights/usage-page.png)

**Logowania przez wymóg uwierzytelniania** przedstawiają liczbę pomyślnych logowań interakcyjnych użytkownika, które były wymagane do przeprowadzenia uwierzytelniania wieloskładnikowego i wieloskładnikowego w usłudze Azure AD. Nie odzwierciedla to logowania, w przypadku których usługa MFA została wymuszona przez innego dostawcę usługi MFA.

![Zrzut ekranu przedstawiający logowania przez wymaganie uwierzytelniania](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

**Logowanie za pomocą metody uwierzytelniania** wskazuje liczbę logowań interakcyjnych użytkownika (sukces i niepowodzenie) przez użytą metodę uwierzytelniania. Nie obejmuje logowania, w których wymagania dotyczące uwierzytelniania zostały spełnione przez żądanie w tokenie.

![Zrzut ekranu dotyczący logowania według metody](media/how-to-authentication-methods-usage-insights/sign-ins-by-method.png)

**Liczba resetowania haseł i odblokowywania kont** pokazuje liczbę pomyślnych zmian haseł i resetowania haseł (samoobsługa i administrator) w czasie.

![Zrzut ekranu przedstawiający Resetowanie i odblokowywanie](media/how-to-authentication-methods-usage-insights/password-changes.png)

**Metoda resetowania haseł przez metodę uwierzytelniania** wskazuje liczbę uwierzytelnień zakończonych powodzeniem i niepowodzeniem podczas przepływu resetowania hasła przez metodę uwierzytelniania.

![Zrzut ekranu przedstawiający metody resetowania przez metodę](media/how-to-authentication-methods-usage-insights/resets-by-method.png)

## <a name="user-registration-details"></a>Szczegóły rejestracji użytkownika 

Za pomocą kontrolek w górnej części listy można wyszukać użytkownika i odfiltrować listę użytkowników na podstawie wyświetlanych kolumn.

Raport szczegóły rejestracji zawiera następujące informacje dla każdego użytkownika:

- Główna nazwa użytkownika
- Nazwa
- Obsługa MFA (z obsługą, bez możliwości)
- Możliwość bez hasła (możliwa, niemożliwa)
- SSPR zarejestrowane (zarejestrowane, niezarejestrowane)
- SSPR włączony (włączony, Niewłączony)
- SSPR (z możliwością, bez możliwości) 
- Zarejestrowano metody (Poczta E-mail, telefon komórkowy, alternatywny numer telefonu komórkowego, telefonu biurowego, Microsoft Authenticator wypychania, oprogramowanie jednorazowe, FIDO2, klucz zabezpieczeń, pytania zabezpieczające)

  ![Zrzut ekranu przedstawiający szczegóły rejestracji użytkownika](media/how-to-authentication-methods-usage-insights/registration-details.png)

## <a name="registration-and-reset-events"></a>Rejestrowanie i resetowanie zdarzeń 

**Zdarzenia rejestrowania i resetowania** pokazują zdarzenia rejestracji i resetowania z ostatnich 24 godzin, ostatnie siedem dni lub ostatnie 30 dni, w tym:

- Date (Data)
- Nazwa użytkownika
- Użytkownik 
- Funkcja (rejestracja, Reset)
- Używana metoda (powiadomienie aplikacji, kod aplikacji, połączenie telefoniczne, połączenie biurowe, alternatywne wywołanie mobilne, wiadomości SMS, Poczta E-mail, pytania zabezpieczające)
- Stan (powodzenie, niepowodzenie)
- Przyczyna niepowodzenia (wyjaśnienie)

  ![Zrzut ekranu przedstawiający stronę użycia](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>Ograniczenia

Rejestracje dostępu tymczasowego (TAP) nie są odzwierciedlone na karcie Rejestracja raportu, ponieważ są tylko prawidłowe przez krótki czas.

## <a name="next-steps"></a>Następne kroki

- [Praca z interfejsem API raportów użycia metod uwierzytelniania](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Wybieranie metod uwierzytelniania dla organizacji](concept-authentication-methods.md)
- [Połączone środowisko rejestracji](concept-registration-mfa-sspr-combined.md)