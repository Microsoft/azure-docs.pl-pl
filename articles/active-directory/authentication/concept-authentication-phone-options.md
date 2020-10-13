---
title: Metody uwierzytelniania telefonicznego — Azure Active Directory
description: Dowiedz się więcej na temat korzystania z metod uwierzytelniania telefonicznego w Azure Active Directory, aby pomóc ulepszyć i zabezpieczyć zdarzenia logowania
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09093a5dbd2142d3542e86a99e24f228320453d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90532744"
---
# <a name="authentication-methods-in-azure-active-directory---phone-options"></a>Metody uwierzytelniania w opcjach Azure Active Directory-Phone

Do bezpośredniego uwierzytelniania przy użyciu wiadomości tekstowej można [skonfigurować i umożliwić użytkownikom uwierzytelnianie SMS (wersja zapoznawcza)](howto-authentication-sms-signin.md). Logowanie oparte na programie SMS jest doskonałe dla pracowników frontonu. Korzystając z logowania opartego na programie SMS, użytkownicy nie muszą znać nazwy użytkownika i hasła w celu uzyskania dostępu do aplikacji i usług. Użytkownik wprowadza swój zarejestrowany numer telefonu komórkowego, odbiera wiadomość SMS z kodem weryfikacyjnym i wprowadza ją w interfejsie logowania.

Użytkownicy mogą również weryfikować się za pomocą telefonu komórkowego lub telefonu biurowego jako dodatkowej formy uwierzytelniania używanej podczas korzystania z usługi Azure Multi-Factor Authentication lub samoobsługowego resetowania hasła (SSPR).

Aby działały prawidłowo, numery telefonów muszą mieć format *+ CountryCode*, na przykład *+ 1 4251234567*.

> [!NOTE]
> Musi być odstęp między kodem kraju/regionu i numerem telefonu.
>
> Resetowanie hasła nie obsługuje rozszerzeń telefonu. Nawet w formacie *4251234567X12345 + 1* rozszerzenia są usuwane przed umieszczeniem wywołania.

## <a name="mobile-phone-verification"></a>Weryfikacja telefonu komórkowego

W przypadku usługi Azure Multi-Factor Authentication lub SSPR użytkownicy mogą zdecydować się na otrzymanie wiadomości SMS z kodem weryfikacyjnym, aby wejść do interfejsu logowania, lub odebrać połączenie telefoniczne z monitem o wprowadzenie zdefiniowanego kodu PIN.

Jeśli użytkownicy nie chcą, aby numer telefonu komórkowego był widoczny w katalogu, ale chcą używać go do resetowania haseł, Administratorzy nie zapełnią numeru telefonu w katalogu. Zamiast tego użytkownicy powinni zapełniać atrybut **telefonu uwierzytelniania** za pośrednictwem rejestracji informacji o zabezpieczeniach w usłudze [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) . Administratorzy mogą zobaczyć te informacje w profilu użytkownika, ale nie zostały opublikowane w innym miejscu.

![Zrzut ekranu przedstawiający Azure Portal pokazujący metody uwierzytelniania z wypełnionym numerem telefonu](media/concept-authentication-methods/user-authentication-methods.png)

Firma Microsoft nie gwarantuje spójnego dostarczania wiadomości SMS lub głosowego opartego na usłudze Azure Multi-Factor Authentication na podstawie tej samej liczby. W interesie naszych użytkowników możemy dodawać lub usuwać krótkie kody w dowolnym momencie, gdy wprowadzimy zmiany trasy w celu poprawy możliwości dostarczania wiadomości SMS. Firma Microsoft nie obsługuje krótkich kodów dla krajów/regionów poza Stany Zjednoczone i Kanadę.

### <a name="text-message-verification"></a>Weryfikacja wiadomości tekstowych

Za pomocą weryfikacji wiadomości tekstowych podczas SSPR lub platformy Azure Multi-Factor Authentication usługa SMS jest wysyłana na numer telefonu komórkowego zawierający kod weryfikacyjny. Aby ukończyć proces logowania, podany kod weryfikacyjny jest wprowadzany do interfejsu logowania.

### <a name="phone-call-verification"></a>Weryfikacja połączenia telefonicznego

Podczas weryfikacji połączeń telefonicznych podczas SSPR lub platformy Azure Multi-Factor Authentication automatyczne połączenie głosowe jest nawiązywane z numerem telefonu zarejestrowanym przez użytkownika. Aby ukończyć proces logowania, użytkownik otrzymuje monit o wprowadzenie numeru PIN, po którym następuje znak # na klawiaturze.

## <a name="office-phone-verification"></a>Weryfikacja telefonu służbowego

Atrybut telefon biurowy jest zarządzany przez administratora usługi Azure AD i nie może być zarejestrowany przez samego użytkownika.

Podczas weryfikacji połączeń telefonicznych podczas SSPR lub platformy Azure Multi-Factor Authentication automatyczne połączenie głosowe jest nawiązywane z numerem telefonu zarejestrowanym przez użytkownika. Aby ukończyć proces logowania, użytkownik otrzymuje monit o wprowadzenie numeru PIN, po którym następuje znak # na klawiaturze.

## <a name="troubleshooting-phone-options"></a>Rozwiązywanie problemów z opcjami telefonu

Jeśli masz problemy z uwierzytelnianiem za pomocą telefonu w usłudze Azure AD, zapoznaj się z następującymi krokami rozwiązywania problemów:

* Zablokowany identyfikator obiektu wywołującego na pojedynczym urządzeniu.
   * Przejrzyj wszystkie zablokowane numery skonfigurowane na urządzeniu.
* Błędny numer telefonu lub nieprawidłowy kod kraju/regionu lub pomyłek między osobistym numerem telefonu a numerem telefonu służbowego.
   * Rozwiązywanie problemów z obiektem użytkownika i skonfigurowanymi metodami uwierzytelniania. Upewnij się, że zarejestrowano poprawne numery telefonów.
* Wprowadzono nieprawidłowy kod PIN.
   * Upewnij się, że użytkownik użył poprawnego numeru PIN zarejestrowanego dla swojego konta.
* Połączenie przekazane do poczty głosowej.
   * Upewnij się, że użytkownik ma włączony telefon i że usługa jest dostępna w swoim obszarze, lub użyj alternatywnej metody.
* Użytkownik jest zablokowany
   * Skontaktuj się z administratorem usługi Azure AD w Azure Portal.
* Wiadomość SMS nie jest subskrybowana na urządzeniu.
   * Użytkownik zmienia metody lub aktywuje wiadomość SMS na urządzeniu.
* Uszkodzonych dostawców telekomunikacyjnych, takich jak nie wykryto wejścia telefonu, brakujące sygnały DTMF, zablokowany identyfikator rozmówcy na wielu urządzeniach lub zablokowany program SMS na wielu urządzeniach.
   * Firma Microsoft używa wielu dostawców Telecom do przesyłania połączeń telefonicznych i wiadomości SMS w celu uwierzytelnienia. Jeśli zobaczysz którykolwiek z powyższych problemów, poproś użytkownika o użycie metody co najmniej pięć razy w ciągu 5 minut i udostępnienie informacji o tym użytkowniku podczas kontaktowania się z pomocą techniczną firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, zobacz Samouczek dotyczący samoobsługowego [resetowania hasła (SSPR)][tutorial-sspr] i [platformy Azure Multi-Factor Authentication][tutorial-azure-mfa].

Aby dowiedzieć się więcej o pojęciach SSPR, zobacz Jak działa funkcja samoobsługowego [resetowania hasła w usłudze Azure AD][concept-sspr].

Aby dowiedzieć się więcej na temat pojęć MFA, zobacz [jak działa usługa Azure Multi-Factor Authentication][concept-mfa].

Dowiedz się więcej na temat konfigurowania metod uwierzytelniania przy użyciu [interfejsu API REST Microsoft Graph w wersji beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
