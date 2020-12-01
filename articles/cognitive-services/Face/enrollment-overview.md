---
title: Przegląd interfejs API rozpoznawania twarzy rejestracji
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej na temat procesu rejestracji w usłudze, aby zarejestrować użytkowników w usłudze rozpoznawania.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: ac5106aa661cb2baea31ee15d57e9c6fac8c7192
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350334"
---
# <a name="face-api-enrollment"></a>Rejestracja interfejs API rozpoznawania twarzy

Aby można było użyć Cognitive Services interfejs API rozpoznawania twarzy do weryfikacji twarzy lub identyfikacji, należy zarejestrować twarze w **LargePersonGroup**. W tym głębokim szczegółowe przedstawiono najlepsze rozwiązania dotyczące zbierania znaczących wyrazów zgody od użytkowników, a także logiki tworzenia rejestracji wysokiej jakości, które optymalizują dokładność rozpoznawania.  

## <a name="meaningful-consent"></a>Znacząca zgoda 

Jednym z najważniejszych celów aplikacji rejestracyjnych w celu rozpoznawania twarzy jest udostępnienie użytkownikom możliwości wyrażania zgody na korzystanie z obrazów ich twarzy do określonych celów, takich jak dostęp do Worksite. Ponieważ technologie rozpoznawania twarzy mogą być postrzegane jako zbierające poufne dane osobowe, szczególnie ważne jest, aby zażądać zgody w taki sposób, że jest to przejrzysty i respectful. Wyrażanie zgody na użytkowników, gdy ma ona na celu podjęcie decyzji, że jest dla nich Najlepsza.   

W oparciu o badania użytkownika firmy Microsoft, osoby odpowiedzialne za technologie AI firmy Microsoft i [badania zewnętrzne](ftp://ftp.cs.washington.edu/tr/2000/12/UW-CSE-00-12-02.pdf)stwierdziły, że zgoda jest istotna, gdy oferuje użytkownikom rejestrację w technologii:

* Świadomość: użytkownicy nie powinni mieć żadnych wątpliwości, gdy są proszeni o podanie ich szablonu lub zdjęcia do rejestracji. 
* Zrozumienie: użytkownicy powinni mieć możliwość dokładnego opisywania własnych wyrazów, do których zażądali, od kogo, do czego się odnoszą, i z jakimi gwarancjami. 
* Swoboda wyboru: użytkownicy nie powinni wymuszać działania ani manipulować nimi podczas wybierania, czy należy wyrazić zgodę i zarejestrować się w usłudze rozpoznawania twarzy. 
* Kontrolka: użytkownicy powinni być w stanie odwołać swoją zgodę i usunąć swoje dane w dowolnym momencie. 

Ta sekcja zawiera wskazówki dotyczące tworzenia aplikacji do rejestracji w celu rozpoznawania twarzy. Te wskazówki zostały opracowane w oparciu o badania użytkownika firmy Microsoft w kontekście rejestracji pojedynczych osób w systemie rozpoznawania twarzy. W związku z tym te zalecenia mogą nie dotyczyć wszystkich rozwiązań rozpoznawania twarzy. Odpowiednie użycie interfejs API rozpoznawania twarzy zależy od konkretnego kontekstu, w którym jest zintegrowany, więc priorytetyzacja i stosowanie tych zaleceń należy dostosować do danego scenariusza. 

> [!NOTE]
> Ponosisz odpowiedzialność za dostosowanie aplikacji do rejestracji do odpowiednich wymagań prawnych w danej jurysdykcji i dokładne odzwierciedlenie wszystkich rozwiązań dotyczących zbierania i przetwarzania danych.

## <a name="application-development"></a>Opracowywanie aplikacji 

Przed zaprojektowaniem przepływu rejestracji należy zastanowić się nad sposobem, w jaki aplikacja, którą tworzysz, może obsłużyć niesie obietnice zwiększenia dla użytkowników o tym, jak ich dane są chronione. Poniższe zalecenia mogą pomóc w utworzeniu środowiska rejestracji, które obejmuje odpowiednie podejścia do zabezpieczania danych osobowych, zarządzania prywatnością użytkowników i zapewnienia dostępności aplikacji dla wszystkich użytkowników.  

|Kategoria | Zalecenia |
|---|---|
|Sprzęt | Weź pod uwagę jakość aparatu rejestracyjnego urządzenia. |
|Zalecane funkcje rejestracji | Dołącz krok logowania z uwierzytelnianiem wieloskładnikowym.</br></br>Połącz informacje o użytkowniku, takie jak alias lub numer identyfikacyjny z IDENTYFIKATORem szablonu do interfejs API rozpoznawania twarzy (znany jako identyfikator osoby). To mapowanie jest niezbędne do pobrania i zarządzania rejestracją użytkownika. Uwaga: Identyfikator osoby powinien być traktowany jako wpis tajny w aplikacji.</br></br>Skonfiguruj zautomatyzowany proces usuwania wszystkich danych rejestracji, w tym szablonów twarzy i zdjęć osób, które nie są już użytkownikami technologii rozpoznawania twarzy, takich jak byli pracownicy.</br></br>Unikaj automatycznej rejestracji, ponieważ nie daje ona użytkownikowi świadomości, rozumienia, swobody wyboru lub kontroli, która jest zalecana do uzyskania zgody. </br></br>Poproszenie użytkowników o zezwolenie na zapisanie obrazów używanych do rejestracji. Jest to przydatne w przypadku aktualizacji modelu, ponieważ nowe zdjęcia rejestracyjne będą wymagane do ponownego zarejestrowania w nowym modelu co 10 miesięcy. Jeśli oryginalne obrazy nie są zapisywane, użytkownicy będą musieli przejść przez proces rejestracji od początku.</br></br>Zezwól użytkownikom na rezygnację z przechowywania zdjęć w systemie. Aby dokonać wyboru przez wybór, można dodać drugi ekran żądania zgody na potrzeby zapisywania zdjęć rejestracyjnych. </br></br>Jeśli Zapisano zdjęcia, Utwórz zautomatyzowany proces, aby ponownie zarejestrować wszystkich użytkowników w przypadku aktualizacji modelu. Osoby, które zapisały Zdjęcia rejestracyjne, nie będą musiały zarejestrować się ponownie. </br></br>Utwórz funkcję aplikacji, która umożliwia Wyznaczeni administratorom przesłonięcie określonych filtrów jakości, jeśli użytkownik ma problemy z rejestrowaniem. |
|Zabezpieczenia | Cognitive Services postępuj zgodnie z [najlepszymi rozwiązaniami](../cognitive-services-virtual-networks.md?tabs=portal) dotyczącymi szyfrowania danych użytkowników przechowywanych w czasie spoczynku i podczas przesyłania. Poniżej znajdują się dodatkowe wskazówki, które mogą pomóc w zawieszeniu niesie obietnice zwiększenia zabezpieczeń dla użytkowników podczas rejestracji. </br></br>Podejmuj środki bezpieczeństwa, aby upewnić się, że nikt nie ma dostępu do identyfikatora osoby w dowolnym momencie podczas rejestracji. Uwaga: PersonID powinna być traktowana jako wpis tajny systemu rejestracji. </br></br>Używanie [kontroli dostępu opartej na rolach](../../role-based-access-control/overview.md) z Cognitive Services. </br></br>Używaj uwierzytelniania opartego na tokenach i/lub sygnatur dostępu współdzielonego za pośrednictwem kluczy i wpisów tajnych, aby uzyskiwać dostęp do zasobów, takich jak bazy danych. Za pomocą tokenów żądania lub SAS można udzielić ograniczonego dostępu do danych bez naruszania kluczy konta i można określić czas wygaśnięcia tokenu. </br></br>Nigdy nie przechowuj żadnych wpisów tajnych, kluczy ani haseł w aplikacji. |
|Prywatność użytkownika |Podaj zakres opcji rejestracji, aby rozwiązać różne poziomy zagadnień związanych z ochroną prywatności. Nie należy zezwalać osobie na korzystanie z urządzeń osobistych do rejestracji w systemie rozpoznawania twarzy. </br></br>Umożliwia użytkownikom ponowne rejestrowanie, odwoływanie zgody oraz usuwanie danych z aplikacji do rejestracji w dowolnym momencie i z dowolnego powodu. |
|Ułatwienia dostępu |Postępuj zgodnie ze standardami dostępności (na przykład [Ada](https://www.ada.gov/regs2010/2010ADAStandards/2010ADAstandards.htm) lub [W3C](https://www.w3.org/TR/WCAG21/)), aby upewnić się, że aplikacja może być używana przez osoby z przenośnością lub upośledzeniem wizualnym. |

## <a name="next-steps"></a>Następne kroki  

Aby rozpocząć pracę z przykładową aplikacją rejestracji, postępuj zgodnie z przewodnikiem [Tworzenie aplikacji rejestracji](build-enrollment-app.md) . Następnie dostosuj je lub napisz własną aplikację, aby odpowiadała potrzebom produktu.