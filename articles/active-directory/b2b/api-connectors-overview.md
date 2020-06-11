---
title: Zarządzanie łącznikami interfejsu API w przepływach rejestracji samoobsługowej
description: Używanie łączników interfejsu API do dostosowywania i zwiększania przepływów użytkowników samoobsługowego rejestrowania
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45bb22f5f6c15e326bed2524fbc541cbdec26a70
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84680161"
---
# <a name="use-api-connectors-to-customize-and-extend-self-service-sign-up"></a>Używanie łączników interfejsu API do dostosowywania i zwiększania rejestracji samoobsługowej 

## <a name="overview"></a>Omówienie 
Deweloperem lub administratorem IT można używać łączników interfejsu API do integrowania [przepływów użytkowników samoobsługi rejestracji](self-service-sign-up-overview.md) z systemami zewnętrznymi przy użyciu interfejsów API sieci Web. Łączniki interfejsu API można na przykład używać do następujących celów:

- [**Integruj z niestandardowymi przepływami pracy zatwierdzania**](self-service-sign-up-add-approvals.md). Połącz się z niestandardowym systemem zatwierdzania w celu zarządzania tworzeniem kont.
<!-- - [**Perform identity proofing**](code-samples-self-service-sign-up.md#identity-proofing). Use an identity proofing and verification service to add an extra level of security to account creation decisions. -->
- **Wykonaj**weryfikację tożsamości. Aby dodać dodatkowy poziom zabezpieczeń do podejmowania decyzji dotyczących tworzenia kont, należy użyć usługi weryfikacji tożsamości i sprawdzenia.
- **Sprawdź poprawność danych wejściowych użytkownika**. Sprawdź poprawność nieprawidłowo sformułowanych lub nieprawidłowych danych użytkownika. Na przykład można sprawdzić poprawność danych dostarczonych przez użytkownika do istniejących danych w zewnętrznym magazynie danych lub na liście dozwolonych wartości. Jeśli jest nieprawidłowy, można polecić użytkownikowi podanie prawidłowych danych lub uniemożliwić użytkownikowi kontynuowanie przepływu rejestracji.
- **Zastąp atrybuty użytkownika**. Ponownie sformatuj lub Przypisz wartość do atrybutu zebranego od użytkownika. Na przykład, jeśli użytkownik wprowadzi imię i nazwisko we wszystkich małych i wielkich literach, można sformatować nazwę tylko przy użyciu pierwszej litery. 
<!-- - **Enrich user data**. Integrate with your external cloud systems that store user information to integrate them with the sign-up flow. For example, your API can receive the user's email address, query a CRM system, and return the user's loyalty number. Returned claims can be used to pre-fill form fields or return additional data in the application token.  -->
- **Uruchom niestandardową logikę biznesową**. Zdarzenia podrzędne można wyzwolić w systemach w chmurze w celu wysyłania powiadomień wypychanych, aktualizacji firmowych baz danych, zarządzania uprawnieniami, inspekcji baz danych i wykonywania innych akcji niestandardowych.

Łącznik interfejsu API reprezentuje kontrakt między usługą Azure AD a punktem końcowym interfejsu API, definiując punkt końcowy HTTP, uwierzytelnianie, żądanie i oczekiwaną odpowiedź. Po skonfigurowaniu łącznika interfejsu API można włączyć go dla określonego kroku w przepływie użytkownika. Gdy użytkownik osiągnie ten krok w przepływie rejestracji, łącznik interfejsu API jest wywoływany i materializuje jako żądanie HTTP POST, wysyłając wybrane oświadczenia jako pary klucz-wartość w treści JSON. Odpowiedź interfejsu API może mieć wpływ na wykonywanie przepływu użytkownika. Na przykład odpowiedź interfejsu API może blokować rejestrowanie użytkownika, poproszenie użytkownika o ponowne wprowadzenie informacji lub zastępowanie i dołączenie atrybutów użytkownika.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>Gdzie można włączyć łącznik interfejsu API w przepływie użytkownika

W przepływie użytkownika znajdują się dwa miejsca, w których można włączyć łącznik interfejsu API:

- Po zalogowaniu się za pomocą dostawcy tożsamości
- Przed utworzeniem użytkownika

W obu tych przypadkach łączniki interfejsu API są wywoływane podczas rejestracji, a nie logowania.

### <a name="after-signing-in-with-an-identity-provider"></a>Po zalogowaniu się za pomocą dostawcy tożsamości

Łącznik interfejsu API w tym kroku w procesie tworzenia konta jest wywoływany natychmiast po uwierzytelnieniu użytkownika przy użyciu dostawcy tożsamości (Google, Facebook, Azure AD). Ten krok poprzedza ***stronę kolekcji atrybutów***, która jest formularzem prezentowanym użytkownikowi w celu zbierania atrybutów użytkownika. Poniżej przedstawiono przykłady scenariuszy łączników interfejsu API, które można włączyć w tym kroku:

- Użyj adresu e-mail lub tożsamości federacyjnej dostarczonej przez użytkownika w celu wyszukania oświadczeń w istniejącym systemie. Zwróć te oświadczenia z istniejącego systemu, wstępnie Wypełnij stronę kolekcji atrybutów i udostępnij je do powrotu w tokenie.
- Sprawdź, czy użytkownik znajduje się na liście dozwolonych lub zablokowanych, oraz określ, czy mogą oni kontynuować przepływ rejestracji.

### <a name="before-creating-the-user"></a>Przed utworzeniem użytkownika

Łącznik interfejsu API w tym kroku w procesie tworzenia konta jest wywoływany po stronie kolekcji atrybutów, jeśli jest dołączony. Ten krok jest zawsze wywoływany przed utworzeniem konta użytkownika w usłudze Azure AD. Poniżej przedstawiono przykładowe scenariusze, które można włączyć w tym momencie podczas rejestracji:

- Sprawdź poprawność danych wejściowych użytkownika i poproszenie użytkownika o ponowne przesłanie danych.
- Zablokuj rejestrację użytkownika na podstawie danych wprowadzonych przez użytkownika.
- Wykonaj weryfikację tożsamości.
- Wykonaj zapytanie dotyczące zewnętrznych systemów dla istniejących danych o użytkowniku, aby je zwracały w tokenie aplikacji lub przechowywać w usłudze Azure AD.

<!-- > [!IMPORTANT]
> If an invalid response is returned or another error occurs (for example, a network error), the user will be redirected to the app with the error re -->

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

### <a name="how-do-i-integrate-with-an-existing-api-endpoint"></a>Jak mogę zintegrować z istniejącym punktem końcowym interfejsu API?
[Wyzwalacza http w Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp) można używać jako prostego sposobu wywoływania i wywoływania innych interfejsów API sieci Web.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [dodać łącznik interfejsu API do przepływu użytkownika](self-service-sign-up-add-api-connector.md)
- Dowiedz się, jak [dodać niestandardowy system zatwierdzania do](self-service-sign-up-add-approvals.md) samoobsługowego tworzenia konta
<!--#TODO: Make doc, link.-->