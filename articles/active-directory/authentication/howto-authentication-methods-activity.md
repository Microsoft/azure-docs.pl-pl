---
title: Działanie metod uwierzytelniania — Azure Active Directory
description: Omówienie metod uwierzytelniania rejestrowanych przez użytkowników w celu logowania i resetowania haseł.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/04/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0865fb2bda04f5a7e9ba2ef73a717946fa656a5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102175315"
---
# <a name="authentication-methods-activity"></a>Działanie metod uwierzytelniania 

Pulpit nawigacyjny nowe działania metod uwierzytelniania umożliwia administratorom monitorowanie rejestracji i użycia metod uwierzytelniania w całej organizacji. Ta funkcja raportowania umożliwia organizacji korzystanie z metod, aby zrozumieć, jakie metody są rejestrowane i jak są używane.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>Uprawnienia i licencje

Wbudowane i niestandardowe role z następującymi uprawnieniami mogą uzyskać dostęp do bloku działania metody uwierzytelniania i interfejsów API:

- Microsoft. Directory/auditLogs/allProperties/odczyt
- Microsoft. Directory/signInReports/allProperties/odczyt

Następujące role mają wymagane uprawnienia:

- Czytelnik raportów
- Czytelnik zabezpieczeń
- Czytelnik globalny
- Operator zabezpieczeń
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

Kliknij dowolną z następujących opcji, aby wstępnie filtrować listę szczegółów rejestracji użytkownika:

- **Użytkownicy z możliwością Multi-Factor Authentication platformy Azure** pokazują podział użytkowników:
  - Zarejestrowano dla metody silnego uwierzytelniania 
  - Włączone przez zasady do korzystania z tej metody usługi MFA 
  
  Ta liczba nie odzwierciedla użytkowników zarejestrowanych na potrzeby uwierzytelniania wieloskładnikowego poza usługą Azure AD. 
- **Użytkownicy z uwierzytelnianiem** bez hasła pokazuje podział użytkowników, którzy zarejestrowali się w celu zalogowania się za pomocą funkcji FIDO2, Windows Hello dla firm lub logowania jednokrotnego za pomocą aplikacji Microsoft Authenticator. 
- **Użytkownicy obsługujący funkcję samoobsługowego resetowania hasła** pokazują podział użytkowników, którzy mogą resetować swoje hasła. Użytkownicy mogą resetować swoje hasła, jeśli są one:
  - Zarejestrowano wystarczającą liczbę metod w celu spełnienia zasad organizacji w celu samoobsługowego resetowania hasła 
  - Włączono, aby zresetować swoje hasło 

  ![Zrzut ekranu użytkowników, którzy mogą rejestrować](media/how-to-authentication-methods-usage-insights/users-capable.png)

**Użytkownicy zarejestrowani przez metodę uwierzytelniania pokazują,** ile użytkowników jest zarejestrowanych dla każdej metody uwierzytelniania. Kliknij metodę uwierzytelniania, aby zobaczyć, kto jest zarejestrowany dla tej metody.

![Zrzut ekranu zarejestrowanego użytkownika](media/how-to-authentication-methods-usage-insights/users-registered.png)

**W przypadku ostatniej rejestracji przez metodę uwierzytelniania** pokazano, ile rejestracji zakończyło się powodzeniem i niepowodzeniem, posortowanych według metody uwierzytelniania. Kliknij metodę uwierzytelniania, aby zobaczyć ostatnie zdarzenia rejestracji dla tej metody.

![Zrzut ekranu przedstawiający ostatnio zarejestrowane](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>Szczegóły użycia

Raport **użycia** pokazuje, które metody uwierzytelniania są używane do logowania i resetowania haseł.

![Zrzut ekranu przedstawiający stronę użycia](media/how-to-authentication-methods-usage-insights/usage-page.png)

**Logowania przez wymóg uwierzytelniania** przedstawiają liczbę pomyślnych logowań interakcyjnych użytkownika, które były wymagane do uwierzytelniania wieloskładnikowego i usługi wieloskładnikowe w usłudze Azure AD. Logowania, w których funkcja MFA została wymuszona przez dostawcę usługi MFA innej firmy, nie jest uwzględniona.

![Zrzut ekranu przedstawiający logowania przez wymaganie uwierzytelniania](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

**Logowanie za pomocą metody uwierzytelniania** wskazuje liczbę logowań interakcyjnych użytkownika (sukces i niepowodzenie) przez użytą metodę uwierzytelniania. Nie obejmuje to logowań, w których wymagania dotyczące uwierzytelniania zostały spełnione przez żądanie w tokenie.

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

  ![Zrzut ekranu zdarzeń rejestracji i resetowania](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>Ograniczenia

- Dane w raporcie nie są aktualizowane w czasie rzeczywistym i mogą odzwierciedlać opóźnienia do kilku godzin.
- Rejestracje dostępu tymczasowego nie są odzwierciedlone na karcie Rejestracja raportu, ponieważ są one prawidłowe tylko przez krótki czas.

## <a name="next-steps"></a>Następne kroki

- [Praca z interfejsem API raportów użycia metod uwierzytelniania](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Wybieranie metod uwierzytelniania dla organizacji](concept-authentication-methods.md)
- [Połączone środowisko rejestracji](concept-registration-mfa-sspr-combined.md)
