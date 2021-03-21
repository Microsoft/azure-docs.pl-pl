---
author: mmacy
ms.author: marsma
ms.date: 12/16/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 53198c663d318a2eb47bcb3207939bbcb1fdd59c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98763478"
---
Aplikacje Microsoft Authentication Library (MSAL) generują komunikaty dziennika, które mogą pomóc zdiagnozować problemy. Aplikacja może skonfigurować rejestrowanie z kilkoma wierszami kodu i mieć kontrolę niestandardową na poziomie szczegółowości oraz o tym, czy dane osobiste i organizacyjne są rejestrowane. Zalecamy utworzenie wywołania zwrotnego rejestrowania MSAL i udostępnienie użytkownikom przesyłania dzienników w przypadku problemów z uwierzytelnianiem.

## <a name="logging-levels"></a>Poziomy rejestrowania

MSAL zapewnia kilka poziomów rejestrowania szczegółów:

- Błąd: wskazuje, że wystąpił problem i został wygenerowany błąd. Używany do debugowania i identyfikowania problemów.
- Ostrzeżenie: niekoniecznie Wystąpił błąd lub błąd, ale są one przeznaczone do diagnostyki i lokalizowania problemów.
- Informacja: MSAL będzie rejestrować zdarzenia przeznaczone do celów informacyjnych niekoniecznie do debugowania.
- Verbose: domyślne. MSAL rejestruje szczegółowe informacje o zachowaniu biblioteki.

## <a name="personal-and-organizational-data"></a>Dane osobowe i organizacyjne

Domyślnie Rejestrator MSAL nie przechwytuje żadnych wysoce poufnych danych osobistych lub organizacji. Biblioteka zawiera opcję włączenia rejestrowania danych osobowych i organizacji w przypadku podjęcia decyzji.

Poniższe sekcje zawierają więcej informacji na temat rejestrowania błędów MSAL dla aplikacji.