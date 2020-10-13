---
title: Informacje o łącznikach interfejsu API w Azure AD B2C
description: Użyj łączników interfejsu API usługi Azure Active Directory (Azure AD), aby dostosowywać i rozłożyć przepływy użytkowników do rejestracji przy użyciu interfejsów API sieci Web.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 09/30/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.openlocfilehash: 195101a432d16c2236ea2d164416e75df33b12e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91828452"
---
# <a name="use-api-connectors-to-customize-and-extend-sign-up-user-flows"></a>Używanie łączników interfejsu API do dostosowywania i zwiększania przepływów użytkowników podczas rejestracji

## <a name="overview"></a>Omówienie 
Jako deweloper lub administrator IT możesz użyć łączników interfejsu API, aby zintegrować przepływy użytkownika tworzenia konta z interfejsami API sieci Web, aby dostosować środowisko rejestracji. Za pomocą łączników interfejsu API można:

- **Przeprowadź weryfikację tożsamości**. Aby dodać dodatkowy poziom zabezpieczeń do decyzji o tworzeniu konta, należy użyć usługi weryfikacji tożsamości.
- **Sprawdź poprawność danych wejściowych użytkownika**. Sprawdź poprawność nieprawidłowo sformułowanych lub nieprawidłowych danych użytkownika. Na przykład można sprawdzić poprawność danych dostarczonych przez użytkownika do istniejących danych w zewnętrznym magazynie danych lub na liście dozwolonych wartości. Jeśli jest nieprawidłowy, można polecić użytkownikowi podanie prawidłowych danych lub uniemożliwić użytkownikowi kontynuowanie przepływu rejestracji.
- **Integruj z niestandardowym przepływem pracy zatwierdzania**. Łączenie się z niestandardowym systemem zatwierdzania w celu zarządzania i ograniczania tworzenia kont.
- **Zastąp atrybuty użytkownika**. Ponownie sformatuj lub Przypisz wartość do atrybutu zebranego od użytkownika. Na przykład, jeśli użytkownik wprowadzi imię i nazwisko we wszystkich małych i wielkich literach, można sformatować nazwę tylko przy użyciu pierwszej litery. 
- **Uruchom niestandardową logikę biznesową**. Zdarzenia podrzędne można wyzwolić w systemach w chmurze w celu wysyłania powiadomień wypychanych, aktualizacji firmowych baz danych, zarządzania uprawnieniami, inspekcji baz danych i wykonywania innych akcji niestandardowych.

Łącznik interfejsu API zapewnia Azure Active Directory z informacjami wymaganymi do wywołania interfejsu API, w tym adres URL punktu końcowego i uwierzytelnianie. Po skonfigurowaniu łącznika interfejsu API można włączyć go dla określonego kroku w przepływie użytkownika. Gdy użytkownik osiągnie ten krok w przepływie rejestracji, łącznik interfejsu API jest wywoływany i materializuje jako żądanie HTTP POST do interfejsu API, wysyłając informacje o użytkowniku ("oświadczenia") jako pary klucz-wartość w treści JSON. Odpowiedź interfejsu API może mieć wpływ na wykonywanie przepływu użytkownika. Na przykład odpowiedź interfejsu API może blokować rejestrowanie użytkownika, poproszenie użytkownika o ponowne wprowadzenie informacji lub zastępowanie i dołączenie atrybutów użytkownika.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>Gdzie można włączyć łącznik interfejsu API w przepływie użytkownika

W przepływie użytkownika znajdują się dwa miejsca, w których można włączyć łącznik interfejsu API:

- Po zalogowaniu się za pomocą dostawcy tożsamości
- Przed utworzeniem użytkownika

> [!IMPORTANT]
> W obu tych przypadkach łączniki interfejsu API są wywoływane podczas **rejestrowania**użytkownika, a nie logowania.

### <a name="after-signing-in-with-an-identity-provider"></a>Po zalogowaniu się za pomocą dostawcy tożsamości

Łącznik interfejsu API w tym kroku w procesie tworzenia konta jest wywoływany natychmiast po uwierzytelnieniu użytkownika przy użyciu dostawcy tożsamości (np. Google, Facebook, & usługi Azure AD). Ten krok poprzedza ***stronę kolekcji atrybutów***, która jest formularzem prezentowanym użytkownikowi w celu zbierania atrybutów użytkownika. Ten krok nie jest wywoływany, jeśli użytkownik jest rejestrowany przy użyciu konta lokalnego. Poniżej przedstawiono przykłady scenariuszy łączników interfejsu API, które można włączyć w tym kroku:

- Użyj adresu e-mail lub tożsamości federacyjnej dostarczonej przez użytkownika w celu wyszukania oświadczeń w istniejącym systemie. Zwróć te oświadczenia z istniejącego systemu, wstępnie Wypełnij stronę kolekcji atrybutów i udostępnij je do powrotu w tokenie.
- Zaimplementuj listę dozwolonych lub zablokowanych w oparciu o tożsamość społecznościową.

### <a name="before-creating-the-user"></a>Przed utworzeniem użytkownika

Łącznik interfejsu API w tym kroku w procesie tworzenia konta jest wywoływany po stronie kolekcji atrybutów, jeśli jest dołączony. Ten krok jest zawsze wywoływany przed utworzeniem konta użytkownika. Poniżej przedstawiono przykładowe scenariusze, które można włączyć w tym momencie podczas rejestracji:

- Sprawdź poprawność danych wejściowych użytkownika i poproszenie użytkownika o ponowne przesłanie danych.
- Zablokuj rejestrację użytkownika na podstawie danych wprowadzonych przez użytkownika.
- Przeprowadź weryfikację tożsamości.
- Wykonaj zapytanie dotyczące zewnętrznych systemów dla istniejących danych o użytkowniku, aby je zwracały w tokenie aplikacji lub przechowywać w usłudze Azure AD.


## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [dodać łącznik interfejsu API do przepływu użytkownika](add-api-connector.md)
<!-- - Learn how to [add a custom approval system to self-service sign-up](add-approvals.md) -->