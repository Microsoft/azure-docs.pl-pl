---
title: Użycie metod uwierzytelniania & Insights — Azure Active Directory
description: Raportowanie w usłudze Azure AD Samoobsługowe resetowanie hasła i użycie metody uwierzytelniania Multi-Factor Authentication
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03989e37ac05228dade2fdcda43856e8a5240865
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964914"
---
# <a name="authentication-methods-usage--insights-preview"></a>Użycie metod uwierzytelniania & Insights (wersja zapoznawcza)

Usługa & Insights umożliwia zrozumienie, jak metody uwierzytelniania dla funkcji, takich jak platforma Azure Multi-Factor Authentication i Samoobsługowe resetowanie haseł, działają w organizacji. Ta funkcja raportowania umożliwia organizacji korzystanie z metod, aby zrozumieć, jakie metody są rejestrowane i jak są używane.

## <a name="permissions-and-licenses"></a>Uprawnienia i licencje

Następujące role mogą uzyskać dostęp do użycia i szczegółowych informacji:

- Administrator globalny
- Czytelnik zabezpieczeń
- Administrator zabezpieczeń
- Czytelnik raportów

Do uzyskania dostępu do informacji o użyciu i szczegółowych informacji nie są wymagane żadne dodatkowe licencje. Informacje dotyczące licencjonowania Multi-Factor Authentication platformy Azure i samoobsługowego resetowania hasła (SSPR) znajdują się w [witrynie Azure Active Directory cenowej](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Jak to działa

Aby uzyskać dostęp do informacji o użyciu metody uwierzytelniania i szczegółowych informacji:

1. Przejdź do [Azure Portal](https://portal.azure.com).
1. Przejdź do **Azure Active Directory**  >  użyciu**resetowania hasła**  >  **& szczegółowe informacje**.
1. Z omówienia **rejestracji** lub **użycia** można wybrać opcję otwarcia wstępnie filtrowanych raportów do filtrowania na podstawie Twoich potrzeb.

![Omówienie użycia & Insights](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Aby uzyskiwać dostęp do & szczegółowych informacji, przejdź do [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) . Ten link spowoduje przeprowadzenie przeglądu rejestracji.

Kafelki Użytkownicy zarejestrowani, użytkownicy i użytkownicy z możliwością obsługi użytkowników pokazują następujące dane rejestracyjne dla użytkowników:

- Zarejestrowane: użytkownik jest uważany za zarejestrowany, jeśli (lub administrator) zarejestrował wystarczającą metodę uwierzytelniania w celu spełnienia zasad SSPR lub Multi-Factor Authentication organizacji.
- Włączone: użytkownik jest uznawany za włączony, jeśli znajdują się w zakresie zasad SSPRymi. Jeśli SSPR jest włączona dla grupy, użytkownik jest uznawany za włączony, jeśli znajdują się w tej grupie. Jeśli SSPR jest włączona dla wszystkich użytkowników, zostaną uznane za włączone wszyscy użytkownicy w dzierżawie (z wyjątkiem Gości).
- Możliwe: użytkownik jest uznawany za możliwy do zarejestrowania i włączenia. Ten stan oznacza, że w razie konieczności można wykonać SSPR w dowolnym momencie.

Kliknięcie dowolnego z tych kafelków lub szczegółowych informacji w nich spowoduje przełączenie do wstępnie przefiltrowanej listy szczegółów rejestracji.

Na wykresie **rejestracje** na karcie **rejestracja** wyświetlana jest liczba pomyślnych i nieudanych rejestracji metod uwierzytelniania przez metodę uwierzytelniania. Na wykresie **resetowania** na karcie **użycie** wyświetlana jest liczba pomyślnych i nieudanych uwierzytelnień podczas przepływu resetowania hasła przez metodę uwierzytelniania.

Kliknięcie dowolnego z wykresów spowoduje przełączenie do wstępnie filtrowanej listy zdarzeń rejestracji lub resetowania.

Za pomocą kontrolki w górnym prawym rogu można zmienić zakres dat dla danych inspekcji pokazywanych w rejestracjach i resetować wykresy do 24 godzin, 7 dni lub 30 dni.

### <a name="registration-details"></a>Szczegóły rejestracji

Kliknięcie kafelków **Użytkownicy**, **którzy włączyli**rejestrację lub Użytkownicy **mogą** uzyskać szczegółowe informacje o rejestracji.

Raport szczegóły rejestracji zawiera następujące informacje dla każdego użytkownika:

- Nazwa
- Nazwa użytkownika
- Stan rejestracji (wszystkie, zarejestrowane, niezarejestrowane)
- Włączony stan (wszystkie, włączone, nie włączono)
- Stan możliwości (wszystkie, obsługujące, nieobsługujący)
- Metody (powiadomienie aplikacji, kod aplikacji, połączenie telefoniczne, wiadomości SMS, wiadomości E-mail, pytania zabezpieczające)

Za pomocą kontrolek w górnej części listy można wyszukać użytkownika i odfiltrować listę użytkowników na podstawie wyświetlanych kolumn.

### <a name="reset-details"></a>Resetuj szczegóły

Kliknięcie na wykresach rejestracji lub resetowania spowoduje przełączenie do szczegółów resetowania.

Raport szczegóły resetu przedstawia zdarzenia rejestracji i resetowania z ostatnich 30 dni, w tym:

- Nazwa
- Nazwa użytkownika
- Funkcja (wszystkie, rejestracja, Reset)
- Metoda uwierzytelniania (powiadomienie aplikacji, kod aplikacji, połączenie telefoniczne, połączenie biurowe, wiadomość SMS, adres E-mail, pytania zabezpieczające)
- Stan (wszystkie, sukces, niepowodzenie)

Za pomocą kontrolek w górnej części listy można wyszukać użytkownika i odfiltrować listę użytkowników na podstawie wyświetlanych kolumn.

## <a name="limitations"></a>Ograniczenia

Dane wyświetlane w tych raportach zostaną opóźnione o maksymalnie 60 minut. Pole "ostatnio odświeżone" istnieje w Azure Portal, aby określić, jak ostatnie dane są.

Dane dotyczące użycia i szczegółowych informacji nie są zamiennikiem raportów dotyczących aktywności platformy Azure Multi-Factor Authentication ani informacje zawarte w raporcie dotyczącym logowania w usłudze Azure AD.

Nie można obecnie filtrować raportu w celu wykluczenia użytkowników zewnętrznych.

## <a name="next-steps"></a>Następne kroki

- [Praca z interfejsem API raportów użycia metod uwierzytelniania](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Wybieranie metod uwierzytelniania dla organizacji](concept-authentication-methods.md)
- [Połączone środowisko rejestracji](concept-registration-mfa-sspr-combined.md)