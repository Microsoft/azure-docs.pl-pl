---
title: Użycie metod uwierzytelniania & szczegółowych informacji — usługa Azure Active Directory
description: Raportowanie samoobsługowego resetowania hasła usługi Azure AD i użycia metody uwierzytelniania wieloskładnikowego
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 053b052abf6f23c385dc7447639aa40b6c2c58a1
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680187"
---
# <a name="authentication-methods-usage--insights-preview"></a>Użycie metod uwierzytelniania & szczegółowych informacji (wersja zapoznawcza)

Zastosowanie & szczegółowych informacji pozwala zrozumieć, jak metody uwierzytelniania dla funkcji, takich jak uwierzytelnianie wieloskładnikowe platformy Azure i samoobsługowe resetowanie haseł, działają w Twojej organizacji. Ta funkcja raportowania zapewnia organizacji środki, aby zrozumieć, jakie metody są rejestrowane i jak są one używane.

## <a name="permissions-and-licenses"></a>Uprawnienia i licencje

Następujące role mogą uzyskiwać dostęp do użycia i szczegółowych informacji:

- Administrator globalny
- Czytelnik zabezpieczeń
- Administrator zabezpieczeń
- Czytnik raportów

Aby uzyskać dostęp do użycia i szczegółowych informacji, nie jest wymagane żadne dodatkowe licencjonowanie. Informacje o licencjonowaniu azure multi-factor authentication i samoobsługowe resetowanie haseł (SSPR) można znaleźć w [witrynie cenowej usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Jak to działa

Aby uzyskać dostęp do użycia metody uwierzytelniania i szczegółowych informacji:

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
1. Przejdź do funkcji**Resetowanie** > hasła **usługi Azure Active Directory** > Użycie & szczegółowych**informacji**.
1. W przeglądach **Rejestracji** lub **Użycia** możesz otworzyć wstępnie filtrowane raporty, aby filtrować na podstawie twoich potrzeb.

![Omówienie szczegółowych informacji o & użycia](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Aby uzyskać bezpośredni dostęp do & [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade)szczegółowych informacji o użyciu, przejdź do pliku . Ten link spowoduje, że przejdziesz do przeglądu rejestracji.

Zarejestrowane, Włączone użytkownicy i kafelki z obsługą Użytkowników wyświetlą następujące dane rejestracyjne dla użytkowników:

- Zarejestrowany: Użytkownik jest uważany za zarejestrowany, jeśli on (lub administrator) zarejestrował wystarczająco dużo metod uwierzytelniania, aby spełnić zasady SSPR lub uwierzytelniania wieloskładnikowego w organizacji.
- Włączone: Użytkownik jest uważany za włączone, jeśli są one w zakresie zasad sspr. Jeśli funkcja SSPR jest włączona dla grupy, użytkownik jest uważany za włączonego, jeśli znajduje się w tej grupie. Jeśli funkcja SSPR jest włączona dla wszystkich użytkowników, wszyscy użytkownicy w dzierżawie (z wyłączeniem gości) są uznawani za włączonych.
- Zdolny: Użytkownik jest uważany za zdolnego, jeśli są one zarówno zarejestrowane i włączone. Ten stan oznacza, że mogą wykonywać sspr w dowolnym momencie, jeśli to konieczne.

Kliknięcie dowolnego z tych kafelków lub przedstawionych w nich statystyk spowoduje wyświetlenie wstępnie przefiltrowanego wykazu szczegółów rejestracji.

Wykres **Rejestracje** na karcie **Rejestracja** pokazuje liczbę rejestracji metod uwierzytelniania, które zakończyły się pomyślnie i nie powiodło się według metody uwierzytelniania. Wykres **Resetuje** na karcie **Użycie** pokazuje liczbę pomyślnych i nieudanych uwierzytelniania podczas resetowania hasła przez metodę uwierzytelniania.

Kliknięcie jednego z wykresów spowoduje wyświetlenie wstępnie przefiltrowanej listy zdarzeń rejestracji lub resetowania.

Za pomocą formantu w górnym, prawym rogu można zmienić zakres dat dla danych inspekcji wyświetlanych na wykresach Rejestracje i Resetuje do 24 godzin, 7 dni lub 30 dni.

### <a name="registration-details"></a>Szczegóły rejestracji

Kliknięcie na **zarejestrowanych Użytkowników**, **Użytkownicy włączone**, lub **Użytkownicy obsługujące** kafelki lub statystyki spowoduje, że do ciebie dane rejestracyjne.

Raport szczegółów rejestracji zawiera następujące informacje dla każdego użytkownika:

- Nazwa
- Nazwa użytkownika
- Status rejestracji (wszystkie, zarejestrowane, niezarejestrowane)
- Stan włączone (Wszystkie, Włączone, Nie włączone)
- Stan stanie (wszystkie, zdolne, nie zdolne)
- Metody (powiadomienie o aplikacji, kod aplikacji, połączenie telefoniczne, SMS, e-mail, pytania zabezpieczające)

Korzystając z formantów u góry listy, można wyszukać użytkownika i filtrować listę użytkowników na podstawie pokazanych kolumn.

### <a name="reset-details"></a>Szczegóły resetowania

Kliknięcie na wykresy Rejestracje lub Resetuje spowoduje, że przejdziesz do szczegółów resetowania.

Raport szczegółów resetowania pokazuje zdarzenia rejestracji i resetowania z ostatnich 30 dni, w tym:

- Nazwa
- Nazwa użytkownika
- Funkcja (wszystko, rejestracja, resetowanie)
- Metoda uwierzytelniania (powiadomienie aplikacji, kod aplikacji, połączenie telefoniczne, połączenie z biurem, SMS, e-mail, pytania zabezpieczające)
- Status (Wszystko, Sukces, Porażka)

Korzystając z formantów u góry listy, można wyszukać użytkownika i filtrować listę użytkowników na podstawie pokazanych kolumn.

## <a name="limitations"></a>Ograniczenia

Dane przedstawione w tych raportach będą opóźnione o maksymalnie 60 minut. W witrynie Azure Portal istnieje pole "Ostatnio odświeżone", aby określić, jak aktualne są twoje dane.

Dane użycia i szczegółowe informacje nie zastępują raportów aktywności uwierzytelniania wieloskładnikowego platformy Azure ani informacji zawartych w raporcie logowania usługi Azure AD.

Raport nie może być obecnie filtrowany, aby wykluczyć użytkowników zewnętrznych.

## <a name="next-steps"></a>Następne kroki

- [Praca z interfejsem API raportu użycia metod uwierzytelniania](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Wybieranie metod uwierzytelniania dla organizacji](concept-authentication-methods.md)
- [Połączone doświadczenie rejestracji](concept-registration-mfa-sspr-combined.md)
