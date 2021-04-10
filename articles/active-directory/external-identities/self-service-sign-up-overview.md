---
title: Samoobsługowe rejestrowanie tożsamości zewnętrznych — Azure AD
description: Dowiedz się, jak zezwolić zewnętrznym użytkownikom na rejestrowanie się w swoich aplikacjach, włączając samoobsługowe Tworzenie konta. Utwórz spersonalizowane środowisko rejestrowania przez dostosowanie przepływu użytkownika samoobsługowego tworzenia konta.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13023ef93cabcf46924cc2cc76dc2d868c4a1ddd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653572"
---
# <a name="self-service-sign-up"></a>Rejestracja samoobsługowa

W przypadku udostępniania aplikacji użytkownikom zewnętrznym użytkownik może nie zawsze z góry znać, kto będzie potrzebować dostępu do aplikacji. Zamiast wysyłać zaproszenia bezpośrednio do osób, możesz zezwolić zewnętrznym użytkownikom na rejestrowanie się w określonych aplikacjach, włączając samoobsługowe rejestrowanie się. Możesz utworzyć spersonalizowane środowisko rejestracji, dostosowując przepływ użytkownika samoobsługowego tworzenia konta. Możesz na przykład udostępnić opcje rejestracji w usłudze Azure AD lub dostawcach tożsamości społecznościowych oraz zbierać informacje o użytkowniku podczas procesu rejestracji.

> [!NOTE]
> Przepływy użytkowników można kojarzyć z aplikacjami skompilowanymi przez organizację. Przepływów użytkowników nie można używać dla aplikacji firmy Microsoft, takich jak SharePoint i zespoły.

## <a name="user-flow-for-self-service-sign-up"></a>Przepływ użytkownika do samoobsługowego rejestrowania się

Samoobsługowe przepływy pracy tworzenia konta dla użytkowników zewnętrznych są tworzone przez aplikację, którą chcesz udostępnić. Przepływ użytkownika można skojarzyć z co najmniej jedną z aplikacji. Najpierw włącz samoobsługowe rejestrowanie dla dzierżawy i sfederować się z dostawcami tożsamości, z których chcesz zezwolić użytkownikom zewnętrznym do logowania. Następnie utworzysz i dostosowasz przepływ użytkownika rejestracji i przypiszesz do niego swoje aplikacje.
Ustawienia przepływu użytkownika można skonfigurować w celu kontrolowania sposobu, w jaki użytkownik rejestruje się w aplikacji:

- Typy kont używane do logowania, takie jak konta społecznościowe, takie jak Facebook lub konta usługi Azure AD
- Atrybuty, które mają być zbierane z konta użytkownika, takie jak imię i nazwisko, kod pocztowy lub kraj/region w miejscu zamieszkania

Gdy użytkownik chce zalogować się do aplikacji, niezależnie od tego, czy jest to aplikacja sieci Web, urządzenia przenośne, klasyczne czy jednostronicowe, aplikacja inicjuje żądanie autoryzacji do punktu końcowego dostarczonego przez przepływ użytkownika. Przepływ użytkownika definiuje i kontroluje środowisko użytkownika. Gdy użytkownik ukończy przepływ użytkownika tworzenia konta, usługa Azure AD generuje token i przekierowuje użytkownika z powrotem do aplikacji. Po zakończeniu tworzenia konta konto gościa jest inicjowane dla użytkownika w katalogu. Wiele aplikacji może korzystać z tego samego przepływu użytkownika.

## <a name="example-of-self-service-sign-up"></a>Przykład rejestracji samoobsługowej

Poniższy przykład ilustruje sposób przełączania dostawców tożsamości społecznościowych do usługi Azure AD za pomocą samoobsługowego rejestrowania funkcji dla użytkowników-Gości.  
Partner Woodgrove otwiera aplikację Woodgrove. Zdecydują się one na zarejestrowaniu się w celu uzyskania konta dostawcy, dlatego wybierają żądanie konta dostawcy, które inicjuje przepływ rejestracji samoobsługowej.

![Przykład strony początkowej rejestracji samoobsługowej](media/self-service-sign-up-overview/example-start-sign-up-flow.png)

Używają oni wiadomości e-mail wybranych do rejestracji.

![Przykład pokazujący wybór usługi Facebook do logowania](media/self-service-sign-up-overview/example-sign-in-with-facebook.png)

Usługa Azure AD tworzy relację z Woodgrove przy użyciu konta w serwisie Facebook partnera i tworzy nowe konto gościa dla użytkownika po zarejestrowaniu się.

Woodgrove chce dowiedzieć się więcej o użytkowniku, takie jak nazwisko, nazwa firmy, kod rejestracyjny firmy, numer telefonu.

![Przykład pokazujący atrybuty rejestracji użytkownika](media/self-service-sign-up-overview/example-enter-user-attributes.png)

Użytkownik wprowadza informacje, kontynuuje przepływ rejestracji i uzyskuje dostęp do zasobów, których potrzebują.

![Przykład przedstawiający zalogowanego użytkownika](media/self-service-sign-up-overview/example-signed-in.png)

## <a name="next-steps"></a>Następne kroki

 Aby uzyskać szczegółowe informacje, zobacz Jak dodać samoobsługowe [Tworzenie konta w aplikacji](self-service-sign-up-user-flow.md).