---
title: Działanie metod uwierzytelniania — Azure Active Directory
description: Omówienie metod uwierzytelniania, które użytkownicy rejestrują w celu logowania się i resetowania haseł.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/16/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 699ff88e4181dada5eacaa3f13469722cdf7ceaa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530452"
---
# <a name="authentication-methods-activity"></a>Działanie metod uwierzytelniania 

Nowy pulpit nawigacyjny działania metod uwierzytelniania umożliwia administratorom monitorowanie rejestracji i użycia metody uwierzytelniania w całej organizacji. Ta funkcja raportowania zapewnia organizacji środki do zrozumienia, jakie metody są rejestrowane i jak są używane.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>Uprawnienia i licencje

Role wbudowane i niestandardowe z następującymi uprawnieniami mogą uzyskać dostęp do bloku Działania metod uwierzytelniania i interfejsów API:

- Microsoft.directory/auditLogs/allProperties/read
- Microsoft.directory/signInReports/allProperties/read

Następujące role mają wymagane uprawnienia:

- Czytelnik raportów
- Czytelnik zabezpieczeń
- Czytelnik globalny
- Operator zabezpieczeń
- Administrator zabezpieczeń
- Administrator globalny

 Aby uzyskać Azure AD — wersja Premium użycia i szczegółowych informacji, wymagana jest licencja P1 lub P2. Informacje o licencjonowaniu usługi Azure AD Multi-Factor Authentication i samoobsługowego resetowania hasła (SSPR) można znaleźć w [Azure Active Directory cennika](https://azure.microsoft.com/pricing/details/active-directory/)usługi .

## <a name="how-it-works"></a>Jak to działa

Aby uzyskać dostęp do informacji i użycia metody uwierzytelniania:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Kliknij **Azure Active Directory**  >  **działanie Metody**  >  **uwierzytelniania**  >  **zabezpieczeń.**
1. Raport zawiera dwie karty: **Rejestracja i** **Użycie.**

   ![Omówienie działania metod uwierzytelniania](media/how-to-authentication-methods-usage-insights/registration-usage-tabs.png)

## <a name="registration-details"></a>Szczegóły rejestracji

Możesz uzyskać dostęp do karty [**Rejestracja,**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) aby wyświetlić liczbę użytkowników, którzy mogą korzystać z uwierzytelniania wieloskładnikowego, uwierzytelniania bez hasła i samoobsługowego resetowania hasła. 

Kliknij dowolną z następujących opcji, aby wstępnie przefiltrować listę szczegółów rejestracji użytkownika:

- **Użytkownicy z możliwością usługi Azure Multi-Factor Authentication** pokazują podział użytkowników, którzy są:
  - Zarejestrowano metodę silnego uwierzytelniania 
  - Włączone przez zasady do używania tej metody dla uwierzytelniania wieloskładnikowego 
  
  Ta liczba nie odzwierciedla użytkowników zarejestrowanych w usłudze MFA poza usługą Azure AD. 
-  Użytkownicy z uwierzytelnianiem bez hasła pokazują podział użytkowników zarejestrowanych do logowania się bez hasła przy użyciu logowania fido2, Windows Hello dla firm lub bez hasła w aplikacji Microsoft Authenticator phone. 
- **Użytkownicy z możliwością samoobsługowego resetowania haseł** pokazują podział użytkowników, którzy mogą resetować swoje hasła. Użytkownicy mogą zresetować swoje hasło, jeśli obaj są:
  - Zarejestrowano dla wystarczającej ilości metod, aby spełnić zasady organizacji dotyczące samoobsługowego resetowania hasła 
  - Włączono resetowanie hasła 

  ![Zrzut ekranu przedstawiający użytkowników, którzy mogą się zarejestrować](media/how-to-authentication-methods-usage-insights/users-capable.png)

**Użytkownicy zarejestrowani przy użyciu metody uwierzytelniania** pokazują, ilu użytkowników jest zarejestrowanych dla każdej metody uwierzytelniania. Kliknij metodę uwierzytelniania, aby zobaczyć, kto jest zarejestrowany dla tej metody.

![Zrzut ekranu przedstawiający zarejestrowanych użytkowników](media/how-to-authentication-methods-usage-insights/users-registered.png)

**Ostatnia rejestracja według metody uwierzytelniania** pokazuje, ile rejestracji zakończyło się powodzeniem i niepowodzeniem, posortowanych według metody uwierzytelniania. Kliknij metodę uwierzytelniania, aby wyświetlić ostatnie zdarzenia rejestracji dla tej metody.

![Zrzut ekranu przedstawiający ostatnio zarejestrowane](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>Szczegóły użycia

Raport **Użycie** pokazuje, które metody uwierzytelniania są używane do logowania i resetowania haseł.

![Zrzut ekranu przedstawiający stronę Użycie](media/how-to-authentication-methods-usage-insights/usage-page.png)

**Wymagania dotyczące logowania według uwierzytelniania** pokazują liczbę pomyślnych interakcyjnych logie użytkowników, które były wymagane do uwierzytelniania jednoskładnikowego w porównaniu z uwierzytelnianiem wieloskładnikowym w usłudze Azure AD. Logowania, w których uwierzytelniania wieloskładnikowego został wymuszony przez dostawcę uwierzytelniania wieloskładnikowego innej firmy nie są uwzględniane.

![Zrzut ekranu przedstawiający logowanie według wymagania uwierzytelniania](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

**Logowania według metody uwierzytelniania** pokazują liczbę interakcyjnych logowania użytkowników (powodzenie i niepowodzenie) według użytej metody uwierzytelniania. Nie obejmuje logowania, w których wymaganie uwierzytelniania zostało spełnione przez oświadczenie w tokenie.

![Zrzut ekranu przedstawiający logowania według metody](media/how-to-authentication-methods-usage-insights/sign-ins-by-method.png)

**Liczba resetów haseł** i odblokowania konta pokazuje liczbę pomyślnych zmian haseł i resetowania haseł (samoobsługa i przez administratora) w czasie.

![Zrzut ekranu przedstawiający resetowanie i odblokowywanie](media/how-to-authentication-methods-usage-insights/password-changes.png)

**Resetowanie hasła według metody uwierzytelniania** pokazuje liczbę pomyślnych i nieudanych uwierzytelnień podczas przepływu resetowania hasła według metody uwierzytelniania.

![Zrzut ekranu przedstawiający resetowanie według metody](media/how-to-authentication-methods-usage-insights/resets-by-method.png)

## <a name="user-registration-details"></a>Szczegóły rejestracji użytkownika 

Za pomocą kontrolek w górnej części listy można wyszukać użytkownika i filtrować listę użytkowników na podstawie wyświetlanych kolumn.

Raport szczegółów rejestracji zawiera następujące informacje dla każdego użytkownika:

- Główna nazwa użytkownika
- Nazwa
- Możliwość uwierzytelniania wieloskładnikowego (z możliwością, bez możliwości)
- Możliwość bez hasła (z możliwością, bez możliwości)
- Zarejestrowane SSPR (zarejestrowane, nierejestrowane)
- Funkcja SSPR włączona (włączona, niewłączona)
- SSPR Capable (Capable, Not Capable) 
- Zarejestrowane metody (poczta e-mail, telefon komórkowy, alternatywny telefon komórkowy, telefon biurowy, wypychanie Microsoft Authenticator, kod dostępu czasowego oprogramowania, FIDO2, klucz zabezpieczeń, pytania zabezpieczające)

  ![Zrzut ekranu przedstawiający szczegóły rejestracji użytkownika](media/how-to-authentication-methods-usage-insights/registration-details.png)

## <a name="registration-and-reset-events"></a>Zdarzenia rejestracji i resetowania 

**Zdarzenia rejestracji i resetowania** pokazują zdarzenia rejestracji i resetowania z ostatnich 24 godzin, ostatnich siedmiu dni lub ostatnich 30 dni, w tym:

- Date (Data)
- Nazwa użytkownika
- Użytkownik 
- Funkcja (rejestracja, resetowanie)
- Używana metoda (powiadomienie aplikacji, kod aplikacji, połączenie telefoniczne, połączenie z biurem, alternatywne połączenie mobilne, wiadomość SMS, wiadomość e-mail, pytania zabezpieczające)
- Stan (powodzenie, niepowodzenie)
- Przyczyna błędu (wyjaśnienie)

  ![Zrzut ekranu przedstawiający zdarzenia rejestracji i resetowania](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>Ograniczenia

- Dane w raporcie nie są aktualizowane w czasie rzeczywistym i mogą odzwierciedlać opóźnienie do kilku godzin.
- Tymczasowy kod dostępu rejestracji nie są odzwierciedlane na karcie rejestracji raportu, ponieważ są one ważne tylko przez krótki czas.
- Metody **PhoneAppNotification** lub **PhoneAppOTP,** które użytkownik mógł skonfigurować, nie są wyświetlane na pulpicie nawigacyjnym. 

## <a name="next-steps"></a>Następne kroki

- [Praca z interfejsem API raportu użycia metod uwierzytelniania](/graph/api/resources/authenticationmethods-usage-insights-overview)
- [Wybieranie metod uwierzytelniania dla organizacji](concept-authentication-methods.md)
- [Połączone środowisko rejestracji](concept-registration-mfa-sspr-combined.md)
