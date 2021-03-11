---
title: Informacje o łącznikach interfejsu API w przepływach rejestracji samoobsługowej — Azure AD
description: Za pomocą łączników interfejsu API usługi Azure Active Directory (Azure AD) można dostosowywać i zwiększać przepływy użytkowników samoobsługowego rejestrowania przy użyciu interfejsów API sieci Web.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a5563ff1f57f6b3684834a2488fc0665ac5eddd
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102610046"
---
# <a name="use-api-connectors-to-customize-and-extend-self-service-sign-up"></a>Używanie łączników interfejsu API do dostosowywania i zwiększania rejestracji samoobsługowej 

## <a name="overview"></a>Omówienie 
Deweloperem lub administratorem IT można używać łączników interfejsu API do integrowania [przepływów użytkowników samoobsługowego tworzenia konta](self-service-sign-up-overview.md) za pomocą interfejsów API sieci Web w celu dostosowania środowiska tworzenia konta i integracji z systemami zewnętrznymi. Na przykład za pomocą łączników interfejsu API można:

- [**Integruj z niestandardowym przepływem pracy zatwierdzania**](self-service-sign-up-add-approvals.md). Łączenie się z niestandardowym systemem zatwierdzania w celu zarządzania i ograniczania tworzenia kont.
- [**Przeprowadź weryfikację tożsamości**](code-samples-self-service-sign-up.md#identity-verification). Aby dodać dodatkowy poziom zabezpieczeń do decyzji o tworzeniu konta, należy użyć usługi weryfikacji tożsamości.
- **Sprawdź poprawność danych wejściowych użytkownika**. Sprawdź poprawność nieprawidłowo sformułowanych lub nieprawidłowych danych użytkownika. Na przykład można sprawdzić poprawność danych dostarczonych przez użytkownika do istniejących danych w zewnętrznym magazynie danych lub na liście dozwolonych wartości. Jeśli jest nieprawidłowy, można polecić użytkownikowi podanie prawidłowych danych lub uniemożliwić użytkownikowi kontynuowanie przepływu rejestracji.
- **Zastąp atrybuty użytkownika**. Ponownie sformatuj lub Przypisz wartość do atrybutu zebranego od użytkownika. Na przykład, jeśli użytkownik wprowadzi imię i nazwisko we wszystkich małych i wielkich literach, można sformatować nazwę tylko przy użyciu pierwszej litery. 
- **Uruchom niestandardową logikę biznesową**. Zdarzenia podrzędne można wyzwolić w systemach w chmurze w celu wysyłania powiadomień wypychanych, aktualizacji firmowych baz danych, zarządzania uprawnieniami, inspekcji baz danych i wykonywania innych akcji niestandardowych.

Łącznik interfejsu API zapewnia Azure Active Directory z informacjami wymaganymi do wywołania punktu końcowego interfejsu API przez zdefiniowanie adresu URL punktu końcowego HTTP i uwierzytelniania dla wywołania interfejsu API. Po skonfigurowaniu łącznika interfejsu API można włączyć go dla określonego kroku w przepływie użytkownika. Gdy użytkownik osiągnie ten krok w przepływie rejestracji, łącznik interfejsu API jest wywoływany i materializuje jako żądanie HTTP POST do interfejsu API, wysyłając informacje o użytkowniku ("oświadczenia") jako pary klucz-wartość w treści JSON. Odpowiedź interfejsu API może mieć wpływ na wykonywanie przepływu użytkownika. Na przykład odpowiedź interfejsu API może blokować rejestrowanie użytkownika, poproszenie użytkownika o ponowne wprowadzenie informacji lub zastępowanie i dołączenie atrybutów użytkownika.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>Gdzie można włączyć łącznik interfejsu API w przepływie użytkownika

W przepływie użytkownika znajdują się dwa miejsca, w których można włączyć łącznik interfejsu API:

- Po zalogowaniu się za pomocą dostawcy tożsamości
- Przed utworzeniem użytkownika

> [!IMPORTANT]
> W obu tych przypadkach łączniki interfejsu API są wywoływane podczas **rejestrowania** użytkownika, a nie logowania.

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
- Dowiedz się, jak [dodać łącznik interfejsu API do przepływu użytkownika](self-service-sign-up-add-api-connector.md)
- Dowiedz się, jak [dodać niestandardowy system zatwierdzania do](self-service-sign-up-add-approvals.md) samoobsługowego tworzenia konta