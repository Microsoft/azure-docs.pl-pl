---
title: Możliwości struktury interfejsu użytkownika usług Azure Communication Services
titleSuffix: An Azure Communication Services conceptual document
description: Informacje o możliwościach struktury interfejsu użytkownika
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5b1aab8b38614249d6b502044b5c4c8170f46b3c
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492221"
---
# <a name="ui-framework-capabilities"></a>Możliwości struktury interfejsu użytkownika

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Struktura interfejsu użytkownika usług Azure Communication Services umożliwia tworzenie środowisk komunikacji przy użyciu zestawu składników wielokrotnego użytku. Te składniki są dostępne w dwóch wersjach: składniki **podstawowe** są najbardziej podstawowymi blokami konstrukcyjnymi środowiska interfejsu użytkownika, podczas gdy kombinacje tych podstawowych składników są nazywane składnikami **złożonymi** .

## <a name="ui-framework-composite-components"></a>Składniki złożone środowiska interfejsu użytkownika

| Złożenie               | Opis                                               | Sieć Web   | Android | iOS   |
|-------------------------|-----------------------------------------------------------|-------|---------|-------|
| Grupa wywołująca złożone | Lekkie i wychodzące środowisko wywoływania głosu i wideo dla usług Azure Communications Services, które używają zasobów projektowania interfejsu użytkownika Fluent. Obsługuje wywoływanie grupy przy użyciu identyfikatora grupy usług Azure Communications Services. Element złożony umożliwia wywołanie metody "jeden do jednego" przez odwołanie się do tożsamości usługi Azure Communications Services lub numeru telefonu dla sieci PSTN przy użyciu numeru telefonu pozyskanego za pośrednictwem platformy Azure.                                    | React |  |  |
| Złożona Grupa rozmów    | Lekki sposób rozmowy dla usług Azure Communication Services przy użyciu zasobów projektowania interfejsu użytkownika Fluent. To środowisko koncentruje się na dostarczaniu prostego klienta czatu, który może nawiązać połączenie z wątkami usług Azure Communications Services. Pozwala ona użytkownikom na wysyłanie komunikatów i wyświetlanie otrzymanych komunikatów przy użyciu wskaźników pisania i odczytów. Skaluje się od 1:1 do grup scenariuszy rozmowy. Obsługuje pojedynczy wątek rozmowy.                         | React |  |  |

## <a name="ui-framework-base-components"></a>Składniki podstawowe struktury interfejsu użytkownika

| Składnik             | Opis                                                                                                                                                                                                                                                                        | Sieć Web   | Android | iOS |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------|---------|-----|
| Dostawca wywołujący    | Podstawowy składnik inicjujący wywoływany. Składnik wymagany, który następnie inicjuje inne składniki na tym komputerze. Obsługuje logikę podstawową w celu zainicjowania klienta wywołującego przy użyciu tokenów dostępu usługi Azure Communication Services. Obsługuje przyłączanie do grup. | React | NIE DOTYCZY     | NIE DOTYCZY |
| Kontrolki multimediów   | Umożliwia użytkownikom zarządzanie bieżącym wywołaniem przez przełączenie wyciszenia, włączenie/wyłączenie wideo i zakończenie rozmowy.                                                                                                                                                              | React | NIE DOTYCZY     | NIE DOTYCZY |
| Galeria multimediów   | Zaprezentuj wszystkich uczestników połączeń w jednej galerii. Galeria obsługuje zarówno uczestników z obsługą wideo, jak i statycznie. W przypadku uczestników z obsługą filmów wideo jest renderowane wideo.                                                                                                                | React | NIE DOTYCZY     | NIE DOTYCZY |
| Ustawienia mikrofonu | Wybierz mikrofon, który ma być używany do wywoływania. Ta kontrolka może być używana przed i podczas wywołania wybierania urządzenia mikrofonu.                                                                                                                                               | React | NIE DOTYCZY     | NIE DOTYCZY |
| Ustawienia aparatu     | Wybierz kamerę, która ma być używana na potrzeby wywoływania wideo. Ta kontrolka może być używana przed i podczas wywołania wybierania urządzenia wideo.                                                                                                                                             | React | NIE DOTYCZY     | NIE DOTYCZY |
| Ustawienia urządzenia     | Łączy ustawienia mikrofonu i aparatu w jeden składnik                                                                                                 | React | NIE DOTYCZY     | NIE DOTYCZY |
| Dostawca rozmowy       | Podstawowy składnik inicjujący dla rozmowy. Składnik wymagany, który następnie inicjuje inne składniki na tym komputerze. Obsługuje logikę podstawową w celu zainicjowania klienta czatu przy użyciu tokenu dostępu usług Azure Communication Services oraz wątku, do którego zostanie dołączony.                                     | React | NIE DOTYCZY     | NIE DOTYCZY |
| Wyślij pole          | Składnik wejściowy, który umożliwia użytkownikom wysyłanie komunikatów do wątku rozmowy. Dane wejściowe obsługują tekst, hiperłącza, emoji i inne znaki Unicode, w tym inne alfabety.                                                                                                                         | React | NIE DOTYCZY     | NIE DOTYCZY |
| Wątek rozmowy           | Składnik wątku, który pokazuje, że użytkownik otrzymał i wysłał komunikaty z informacjami o nadawcy. Wątek obsługuje wskaźniki pisania i odczytaje potwierdzenia. Możesz przewinąć te wątki, aby przejrzeć historię rozmowy.
| Lista uczestników      | Pokaż wszystkich uczestników wywołania lub wątku czatu jako listę.  | React | NIE DOTYCZY     | NIE DOTYCZY |

## <a name="ui-framework-capabilities"></a>Możliwości struktury interfejsu użytkownika

| Cecha                                                             | Grupa wywołująca złożone | Złożona Grupa rozmów | Składniki podstawowe |
|---------------------------------------------------------------------|-------------------------|----------------------|-----------------|
| Dołącz do spotkania zespołów                                                  |                         |                      |           
| Połączenie z wydarzeniem zespołu na żywo                                               |                         |                      | 
| Rozpocznij rozmowę VoIP z użytkownikami zespołów                                       |                         |                      | 
| Dołącz do rozmowy na spotkaniu zespołów                                           |                         |                      |            
| Dołącz do wywołania usługi Azure Communications Services z identyfikatorem grupy                | ✔                      |                      | ✔
| Rozpocznij rozmowę VoIP z co najmniej jednym użytkownikami usług Azure Communications Services |                         |                      |           
| Dołącz do wątku czatu usługi Azure Communication Services                    |                         | ✔                   | ✔
| Wyciszenie/odciszenie wywołania                                                    | ✔                       |                      | ✔
| Wideo włączone/wyłączone przy wywołaniu                                                | ✔                       |                      | ✔
| Udostępnianie ekranu                                                      | ✔                       |                      | ✔
| Galeria uczestników                                                 | ✔                       |                      | ✔
| Zarządzanie mikrofonem                                               | ✔                       |                      | ✔
| Zarządzanie kamerą                                                   | ✔                       |                      | ✔
| Zadzwoń do poczekalni                                                          |                         |                      | ✔
| Wyślij wiadomość czatu                                                   |                         | ✔                   |            
| Odbieranie komunikatu czatu                                                |                         | ✔                   | ✔
| Wpisywanie wskaźników                                                   |                         | ✔                   | ✔
| Odczytaj potwierdzenie                                                        |                         | ✔                   | ✔
| Lista uczestników                                                    |                         |                      | ✔


## <a name="customization-support"></a>Obsługa dostosowywania

| Typ składnika            | Motywy     | Layout                                                              | Modele danych |
|---------------------------|------------|---------------------------------------------------------------------|-------------|
| Składnik złożony       |     NIE DOTYCZY    | NIE DOTYCZY                                                                 |     NIE DOTYCZY     |
| Składnik podstawowy            |     Nie dotyczy    | Układ składników można modyfikować przy użyciu zewnętrznego stylu         |     Nie dotyczy     |


## <a name="platform-support"></a>Obsługa platform

| SDK    | Windows            | macOS                | Ubuntu   | Linux    | Android  | iOS        |
|--------|--------------------|----------------------|----------|----------|----------|------------|
| ZESTAW SDK INTERFEJSU UŻYTKOWNIKA | Chrome \* , Nowa krawędź | Chrome \* , Safari\*\* | Chrome\* | Chrome\* | Chrome\* | Safari\*\* |

\*Należy pamiętać, że Najnowsza wersja programu Chrome jest obsługiwana poza poprzednimi dwiema wersjami.

\*\*Należy pamiętać, że obsługiwane są wersje 13.1 + programu Safari. Wychodzące wideo dla programu Safari macOS nie jest jeszcze obsługiwane, ale jest obsługiwane w systemie iOS. Udostępnianie ekranu wychodzącego jest obsługiwane tylko na komputerze stacjonarnym z systemem iOS.
