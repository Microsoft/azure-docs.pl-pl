---
title: Metoda uwierzytelniania pytań zabezpieczających — Azure Active Directory
description: Dowiedz się więcej na temat używania pytań zabezpieczających w Azure Active Directory, aby pomóc w ulepszaniu i zabezpieczaniu zdarzeń związanych z logowaniem
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42ba860c3174ad8725dd15ac3045d270524b110e
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965475"
---
# <a name="authentication-methods-in-azure-active-directory---security-questions"></a>Metody uwierzytelniania w Azure Active Directory — pytania zabezpieczające

Pytania zabezpieczające nie są używane jako metoda uwierzytelniania podczas zdarzenia logowania. Zamiast tego pytania zabezpieczające mogą być używane podczas procesu samoobsługowego resetowania hasła (SSPR) w celu potwierdzenia, kim jesteś. Konta administratorów nie mogą używać pytań zabezpieczających jako metody weryfikacji w programie SSPR.

Gdy użytkownicy rejestrują się pod kątem usługi SSPR, są monitowani o wybranie metod uwierzytelniania do użycia. Jeśli zdecydują się na korzystanie z pytań zabezpieczających, wybierają się z zestawu pytań na potrzeby monitowania, a następnie zapewniają własne odpowiedzi.

![Zrzut ekranu przedstawiający Azure Portal pokazujący metody uwierzytelniania i opcje dotyczące pytań zabezpieczających](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> Pytania zabezpieczające są przechowywane prywatnie i bezpiecznie w obiekcie użytkownika w katalogu i mogą być udzielane tylko przez użytkowników podczas rejestracji. Nie ma możliwości, aby administrator mógł odczytywać lub modyfikować pytania lub odpowiedzi użytkownika.

Pytania zabezpieczające mogą być mniej bezpieczne niż inne metody, ponieważ niektóre osoby mogą znać odpowiedzi na pytania innego użytkownika. W przypadku używania pytań zabezpieczających z SSPR zaleca się ich użycie w połączeniu z inną metodą. Użytkownik może zostać poproszony o użycie aplikacji Microsoft Authenticator lub uwierzytelniania na telefonie, aby zweryfikować swoją tożsamość podczas procesu SSPR i wybierać pytania zabezpieczające tylko wtedy, gdy nie mają ich telefonu lub zarejestrowanego urządzenia.

## <a name="predefined-questions"></a>Wstępnie zdefiniowane pytania

Poniższe wstępnie zdefiniowane pytania zabezpieczające są dostępne do użycia jako metoda weryfikacji z SSPR. Wszystkie te pytania zabezpieczające są tłumaczone i lokalizowane do pełnego zestawu języków Microsoft 365 w oparciu o ustawienia regionalne przeglądarki użytkownika:

* W jakim mieście Twój pierwszy współmałżonek/partner?
* W jakim mieście spotkali się rodzice?
* W jakim mieście znajduje się najbliższy element równorzędny?
* W jakim mieście urodziły się ojciec?
* W jakim mieście było Twoje pierwsze zadanie?
* W jakim mieście urodziła się twoja matka?
* W którym mieście nastąpiło w nowym roku 2000?
* Jaka jest nazwisko Twojego ulubionego nauczyciela w wysokiej szkoły?
* Jaka jest nazwa uczelni, która została zastosowana, ale nie uczestniczyła?
* Jaka jest nazwa miejsca, w którym ma zostać przeprowadzone pierwsze odbiór z ślubu?
* Co to jest drugie imię Twojego ojca?
* Jaka jest Twoja ulubiona żywność?
* Co to jest imię i nazwisko matki babcia?
* Co to jest imię i nazwisko matki?
* Co to jest miesiąc i rok urodzinowy Twojego elementu równorzędnego? (na przykład listopad 1985)
* Jaka jest nazwa najstarszego elementu równorzędnego?
* Co to jest imię i nazwisko z Paternal dziadka?
* Co to jest drugie imię z najmłodszego elementu równorzędnego?
* Jaką szkołę znasz na szóstej ocenie?
* Jakie było imię i nazwisko najlepszego przyjaciela?
* Jakie było imię i nazwisko Twojego pierwszego znaczenia?
* Jaka była nazwisko Twojego ulubionego nauczyciela szkoły szkolnej?
* Co to jest marka i model Twojego pierwszego samochodu lub motocykla?
* Jaka była nazwa pierwszej szkolnej szkoły?
* Jaka była nazwa szpitala, z którego korzystasz?
* Jaka była nazwa ulicy Twojego pierwszego domu z domem?
* Jaka była nazwa Twojego heroi z domu?
* Jaka była nazwa ulubionego zwierzęcia?
* Jaka była nazwa Twojego pierwszego zwierzęcia domowego?
* Jaki był pseudonim w Twoim domu,?
* Jaki był Twój ulubiony sport w dużej szkole?
* Jakie było Twoje pierwsze zadanie?
* Jakie były cztery ostatnie cyfry Twojego numeru telefonu w Twoim serwisie
* Kiedy jesteś w młodych, co chcesz zrobić po wzrósłe?
* Kto jest najbardziej sławęą osobą?

## <a name="custom-security-questions"></a>Niestandardowe pytania zabezpieczające

Aby uzyskać dodatkową elastyczność, możesz zdefiniować własne niestandardowe pytania zabezpieczające. Maksymalna długość niestandardowego pytania zabezpieczającego to 200 znaków.

Niestandardowe pytania zabezpieczające nie są automatycznie lokalizowane, podobnie jak w przypadku domyślnych pytań zabezpieczających. Wszystkie pytania niestandardowe są wyświetlane w tym samym języku, w jakim są wprowadzane w administracyjnym interfejsie użytkownika, nawet jeśli ustawienia regionalne przeglądarki użytkownika są inne. Jeśli potrzebujesz zlokalizowanych pytań, musisz użyć wstępnie zdefiniowanych pytań.

## <a name="security-question-requirements"></a>Wymagania dotyczące zabezpieczeń

W przypadku domyślnych i niestandardowych pytań zabezpieczających obowiązują następujące wymagania i ograniczenia:

* Minimalny limit znaków odpowiedzi to trzy znaki.
* Maksymalny limit znaków odpowiedzi to 40 znaków.
* Użytkownicy nie mogą odpowiedzieć na te same pytania więcej niż jeden raz.
* Użytkownicy nie mogą udzielić odpowiedzi na więcej niż jedno pytanie.
* Dowolny zestaw znaków może służyć do definiowania pytań i odpowiedzi, w tym znaków Unicode.
* Liczba zdefiniowanych pytań musi być większa lub równa liczbie pytań, które były wymagane do zarejestrowania.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, zobacz Samouczek dotyczący samoobsługowego [resetowania hasła (SSPR)][tutorial-sspr].

Aby dowiedzieć się więcej o pojęciach SSPR, zobacz Jak działa funkcja samoobsługowego [resetowania hasła w usłudze Azure AD][concept-sspr].

Dowiedz się więcej na temat konfigurowania metod uwierzytelniania przy użyciu [interfejsu API REST Microsoft Graph w wersji beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[concept-sspr]: concept-sspr-howitworks.md
