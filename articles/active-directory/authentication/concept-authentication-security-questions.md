---
title: Metoda uwierzytelniania pytań zabezpieczających — Azure Active Directory
description: Dowiedz się więcej o używaniu pytań zabezpieczających Azure Active Directory, które ułatwiają ulepszanie i zabezpieczanie zdarzeń logowania
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 841391778e0fb8c00f503aa0cc79b5562661e309
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530352"
---
# <a name="authentication-methods-in-azure-active-directory---security-questions"></a>Metody uwierzytelniania w Azure Active Directory — pytania zabezpieczające

Pytania zabezpieczające nie są używane jako metoda uwierzytelniania podczas zdarzenia logowania. Zamiast tego podczas procesu samoobsługowego resetowania hasła (SSPR) można użyć pytań zabezpieczających w celu potwierdzenia, kim jesteś. Konta administratorów nie mogą używać pytań zabezpieczających jako metody weryfikacji za pomocą funkcji SSPR.

Gdy użytkownicy zarejestrują się w funkcji SSPR, zostanie wyświetlony monit o wybranie metod uwierzytelniania do użycia. Jeśli zdecyduje się użyć pytań zabezpieczających, wybierają z zestawu pytań, o które należy monitować, a następnie dostarczają własne odpowiedzi.

![Zrzut ekranu przedstawiający Azure Portal metody uwierzytelniania i opcje dotyczące pytań zabezpieczających](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> Pytania zabezpieczające są przechowywane prywatnie i bezpiecznie w obiekcie użytkownika w katalogu i mogą na nie odpowiadać tylko użytkownicy podczas rejestracji. Administrator nie może odczytywać ani modyfikować pytań ani odpowiedzi użytkownika.

Pytania zabezpieczające mogą być mniej bezpieczne niż inne metody, ponieważ niektórzy mogą znać odpowiedzi na pytania innego użytkownika. Jeśli używasz pytań zabezpieczających z funkcją SSPR, zaleca się ich użycie w połączeniu z inną metodą. Użytkownik może zostać poproszony o użycie uwierzytelniania za pomocą aplikacji Microsoft Authenticator lub telefonu w celu zweryfikowania swojej tożsamości podczas procesu SSPR i wybranie pytań zabezpieczających tylko wtedy, gdy nie ma przy sobie telefonu lub zarejestrowanego urządzenia.

## <a name="predefined-questions"></a>Wstępnie zdefiniowane pytania

Następujące wstępnie zdefiniowane pytania zabezpieczające są dostępne do użycia jako metoda weryfikacji z użyciem funkcji SSPR. Wszystkie te pytania zabezpieczające są tłumaczone i zlokalizowane w pełnym zestawie języków Microsoft 365 na podstawie ustawień regionalnych przeglądarki użytkownika:

* W jakim mieście spotkasz się z pierwszym partnerem/partnerem?
* W jakim mieście spotkali się Twoi rodziców?
* W jakim mieście mieszka najbliższy rodzeństwo?
* W jakim mieście narodził się Twój syn?
* W jakim mieście była Twoja pierwsza praca?
* W jakim mieście narodziła się Twoja matki?
* W jakim mieście jesteś w nowym roku 2000?
* Jakie jest nazwisko ulubionego nauczyciela w szkole średniej?
* Jak nazywa się uczelnia, do której zastosowano wniosek, ale do której nie należysz?
* Jaka jest nazwa miejsca, w którym był utrzymywany pierwszy odbiór urządzenia?
* Jakie jest drugie imię twojego dziecka?
* Co to jest ulubione jedzenie?
* Jakie jest imię i nazwisko matki?
* Jakie jest drugie imię matki?
* Jaki jest miesiąc i rok urodzenia najstarszego rodzeństwa? (na przykład listopad 1985 r.)
* Jakie jest drugie imię najstarszego rodzeństwa?
* Jakie jest imię i nazwisko twojego dziecka?
* Jakie jest drugie imię Twojego najmłodszego rodzeństwa?
* W której szkole uczestniczysz w szóstej klasie?
* Jakie było pierwsze i nazwisko twojego najlepszego znajomego?
* Jakie było pierwsze i nazwisko twojego pierwszego partner?
* Jakie było nazwisko ulubionego nauczyciela na poziomie szkoły średniej?
* Jaka była marki i modelu Twojego pierwszego samochodu lub motocykla?
* Jak nazywała się pierwsza szkoła, w której uczestniczysz?
* Jak nazywa się szpital, w którym się urodzisz?
* Jak nazywała się ulica Twojego pierwszego domu?
* Jak nazywa się Twój hero?
* Jaka była nazwa ulubionego wypchanych zwierząt?
* Jak nazywa się Twoje pierwsze zwierzę?
* Jaki był Twój pseudonim?
* Jaki był Twój ulubiony sport w szkole średniej?
* Jakie było Twoje pierwsze zadanie?
* Jakie były cztery ostatnie cyfry twojego numeru telefonu komórkowego?
* Kiedy jesteś w wieku lat, co chcesz mieć, gdy wyrósła na ciebie?
* Who is the most famous person you have ever met? (Kto jest najbardziej znaną osobą, z którą kiedykolwiek się spotykasz?)

## <a name="custom-security-questions"></a>Niestandardowe pytania zabezpieczające

Aby uzyskać dodatkową elastyczność, możesz zdefiniować własne niestandardowe pytania zabezpieczające. Maksymalna długość niestandardowego pytania zabezpieczającego wynosi 200 znaków.

Niestandardowe pytania zabezpieczające nie są automatycznie zlokalizowane na przykład przy użyciu domyślnych pytań zabezpieczających. Wszystkie pytania niestandardowe są wyświetlane w tym samym języku, w jakim zostały wprowadzone w administracyjnym interfejsie użytkownika, nawet jeśli ustawienia lokalne przeglądarki użytkownika są inne. Jeśli potrzebujesz zlokalizowanych pytań, użyj wstępnie zdefiniowanych pytań.

## <a name="security-question-requirements"></a>Wymagania dotyczące pytań zabezpieczających

W przypadku domyślnych i niestandardowych pytań zabezpieczających obowiązują następujące wymagania i ograniczenia:

* Minimalny limit znaków odpowiedzi to trzy znaki.
* Maksymalny limit znaków odpowiedzi to 40 znaków.
* Użytkownicy nie mogą odpowiedzieć na to samo pytanie więcej niż jeden raz.
* Użytkownicy nie mogą podać tej samej odpowiedzi na więcej niż jedno pytanie.
* Dowolny zestaw znaków może służyć do definiowania pytań i odpowiedzi, w tym znaków Unicode.
* Liczba zdefiniowanych pytań musi być większa lub równa liczbie pytań, które były wymagane do zarejestrowania.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę, zobacz [samouczek dotyczący samoobsługowego resetowania hasła ( SSPR).][tutorial-sspr]

Aby dowiedzieć się więcej na temat pojęć związanych z samoobsługowym resetowaniem hasła, zobacz Jak działa [samoobsługowe resetowanie hasła usługi Azure AD.][concept-sspr]

Dowiedz się więcej na temat konfigurowania metod uwierzytelniania przy użyciu [Microsoft Graph API REST.](/graph/api/resources/authenticationmethods-overview)

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[concept-sspr]: concept-sspr-howitworks.md
