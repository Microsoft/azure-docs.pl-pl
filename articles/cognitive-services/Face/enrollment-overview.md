---
title: Najlepsze rozwiązania dotyczące dodawania użytkowników do usługi rozpoznawania twarzy
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej na temat procesu rejestracji twarzy w celu rejestrowania użytkowników w usłudze rozpoznawania twarzy.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: e710771dd14b389e856e752b6587e2a76ad9d85b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505075"
---
# <a name="best-practices-for-adding-users-to-a-face-service"></a>Najlepsze rozwiązania dotyczące dodawania użytkowników do usługi rozpoznawania twarzy

Aby używać interfejsu API Cognitive Services rozpoznawania twarzy do weryfikacji lub identyfikacji twarzy, należy zarejestrować twarze w **grupie LargePersonGroup.** W tym dogłębnym opisie pokazano najlepsze rozwiązania dotyczące zbierania znaczących zgody od użytkowników, a także przykładową logikę tworzenia rejestracji wysokiej jakości, które optymalizują dokładność rozpoznawania.  

## <a name="meaningful-consent"></a>Znacząca zgoda 

Jednym z kluczowych celów aplikacji do rejestracji w celu rozpoznawania twarzy jest dać użytkownikom możliwość wyrażenia zgody na używanie obrazów twarzy do określonych celów, takich jak dostęp do prac. Ponieważ technologie rozpoznawania twarzy mogą być odbierane jako zbieranie poufnych danych osobowych, szczególnie ważne jest, aby poprosić o zgodę w sposób, który jest zarówno przezroczysty, jak i respektowny. Zgoda jest zrozumiała dla użytkowników, gdy umożliwia im podejmowanie decyzji, że ich opinie są dla nich najlepsze.   

Na podstawie badań użytkowników firmy Microsoft, zasad [](ftp://ftp.cs.washington.edu/tr/2000/12/UW-CSE-00-12-02.pdf)odpowiedzialnego korzystania ze AI i badań zewnętrznych ustaliliśmy, że zgoda jest znacząca, gdy oferuje ona następujące informacje użytkownikom rejestrujących się w tej technologii:

* Świadomość: Użytkownicy nie powinni mieć wątpliwości, gdy są proszeni o podanie szablonu twarzy lub zdjęć rejestracyjnych. 
* Zrozumienie: Użytkownicy powinni być w stanie dokładnie opisać własnymi słowami, o co prosili, przez kogo, do czego celu i z jakimi gwarancjami. 
* Swoboda wyboru: Użytkownicy nie powinni być wywłaszczani ani manipulowani podczas wybierania, czy wyrazić zgodę i zarejestrować się w funkcji rozpoznawania twarzy. 
* Kontrola: Użytkownicy powinni mieć możliwość odwołania zgody i usunięcia swoich danych w dowolnym momencie. 

Ta sekcja zawiera wskazówki dotyczące tworzenia aplikacji do rejestracji w celu rozpoznawania twarzy. Te wskazówki zostały opracowane na podstawie badań użytkowników firmy Microsoft w kontekście rejestrowania osób w rozpoznaniu twarzy w celu wejścia do budynku. W związku z tym te zalecenia mogą nie dotyczyć wszystkich rozwiązań do rozpoznawania twarzy. Odpowiedzialne używanie interfejsu API rozpoznawania twarzy w dużym stopniu zależy od konkretnego kontekstu, w którym jest zintegrowany, dlatego priorytetyzację i zastosowanie tych zaleceń należy dostosować do danego scenariusza. 

> [!NOTE]
> Twoim obowiązkiem jest dopasowanie aplikacji rejestracji do odpowiednich wymagań prawnych w Twojej jurysdykcji i dokładne odzwierciedlenie wszystkich praktyk zbierania i przetwarzania danych.

## <a name="application-development"></a>Opracowywanie aplikacji 

Przed rozpoczęciem projektowania przepływu rejestracji zastanów się, jak budować aplikację, która jest w stanie podtrzymać obietnice dla użytkowników dotyczące sposobu ochrony ich danych. Poniższe zalecenia mogą ułatwić tworzenie środowisko rejestracji, które obejmuje odpowiedzialne podejście do zabezpieczania danych osobowych, zarządzania prywatnością użytkowników i zapewniania, że aplikacja jest dostępna dla wszystkich użytkowników.  

|Kategoria | Zalecenia |
|---|---|
|Sprzęt | Należy wziąć pod uwagę jakość aparatu urządzenia rejestracji. |
|Zalecane funkcje rejestracji | Dołącz krok logowania z uwierzytelnianiem wieloskładnikowym.</br></br>Połącz informacje o użytkowniku, takie jak alias lub numer identyfikacyjny, z identyfikatorem szablonu twarzy z interfejsu API rozpoznawania twarzy (tzw. identyfikator osoby). To mapowanie jest niezbędne do pobrania rejestracji użytkownika i zarządzania tą rejestracją. Uwaga: identyfikator osoby powinien być traktowany w aplikacji jako klucz tajny.</br></br>Skonfiguruj zautomatyzowany proces usuwania wszystkich danych rejestracji, w tym szablonów twarzy i zdjęć rejestracji osób, które nie są już użytkownikami technologii rozpoznawania twarzy, takich jak byli pracownicy.</br></br>Unikaj automatycznej rejestracji, ponieważ nie daje ona użytkownikowi świadomości, zrozumienia, swobodnego wyboru ani kontroli zalecanej do uzyskania zgody. </br></br>Poproś użytkowników o uprawnienia do zapisywania obrazów używanych do rejestracji. Jest to przydatne w przypadku aktualizacji modelu, ponieważ nowe zdjęcia rejestracji będą wymagane do ponownego zarejestrowania w nowym modelu co około 10 miesięcy. Jeśli oryginalne obrazy nie zostaną zapisane, użytkownicy będą musieli przejść proces rejestracji od początku.</br></br>Zezwalaj użytkownikom na rezygnację z przechowywania zdjęć w systemie. Aby ułatwić wybór, możesz dodać drugi ekran żądania zgody na zapisywanie zdjęć rejestracji. </br></br>Jeśli zdjęcia są zapisywane, utwórz zautomatyzowany proces ponownego rejestrowania wszystkich użytkowników w przypadku aktualizacji modelu. Osoby, które zapisały zdjęcia z rejestracji, nie będą musiały ponownie rejestrować się. </br></br>Utwórz funkcję aplikacji, która umożliwia wyznaczonym administratorom zastępowanie pewnych filtrów jakości, jeśli użytkownik ma problemy z rejestracją. |
|Zabezpieczenia | Cognitive Services stosować [najlepsze rozwiązania dotyczące](../cognitive-services-virtual-networks.md?tabs=portal) szyfrowania danych użytkownika podczas przechowywania i przesyłania. Poniżej przedstawiono dodatkowe rozwiązania, które mogą pomóc w dotrzymaniu obietnic zabezpieczeń, które są zapewniane użytkownikom podczas procesu rejestracji. </br></br>Należy podjąć środki bezpieczeństwa, aby upewnić się, że nikt nie ma dostępu do identyfikatora osoby w żadnym momencie podczas rejestracji. Uwaga: w systemie rejestracji personID powinien być traktowany jako wpis tajny. </br></br>Używanie [kontroli dostępu opartej na rolach z](../../role-based-access-control/overview.md) Cognitive Services. </br></br>Użyj uwierzytelniania opartego na tokenach i/lub sygnatur dostępu współdzielonego (SAS) za pośrednictwem kluczy i wpisów tajnych, aby uzyskać dostęp do zasobów, takich jak bazy danych. Przy użyciu tokenów żądania lub sygnatury dostępu współdzielonego można przyznać ograniczony dostęp do danych bez naruszania kluczy konta i określić czas wygaśnięcia tokenu. </br></br>Nigdy nie przechowuj żadnych wpisów tajnych, kluczy ani haseł w aplikacji. |
|Prywatność użytkownika |Podaj szereg opcji rejestracji, aby rozwiązać różne problemy związane z prywatnością. Nie nakań, aby ludzie używali swoich urządzeń osobistych do rejestracji w systemie rozpoznawania twarzy. </br></br>Zezwalaj użytkownikom na ponowne rejestrowanie, odwoływanie zgody i usuwanie danych z aplikacji rejestracji w dowolnym momencie i z dowolnej przyczyny. |
|Ułatwienia dostępu |Postępuj zgodnie ze standardami ułatwień dostępu [(np. ADA](https://www.ada.gov/regs2010/2010ADAStandards/2010ADAstandards.htm) lub [W3C),](https://www.w3.org/TR/WCAG21/)aby zapewnić, że z aplikacji będą korzystać osoby z mobilnością lub wadami wzroku. |

## <a name="next-steps"></a>Następne kroki  

Postępuj zgodnie [z przewodnikiem Tworzenie aplikacji rejestracji,](build-enrollment-app.md) aby rozpocząć pracę z przykładową aplikacją rejestracji. Następnie dostosuj go lub napisz własną aplikację do potrzeb swojego produktu.