---
title: Środki zaradcze — Microsoft Threat Modeling Tool na platformie Azure | Microsoft Docs
description: Strona środki zaradcze dla Microsoft Threat Modeling Tool wyróżniania możliwych rozwiązań dla najbardziej narażonych wygenerowanych zagrożeń.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 748d10b994080b667885e5d0d5f4d688269e86ab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "68728042"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Microsoft Threat Modeling Tool środki zaradcze

Threat Modeling Tool jest elementem podstawowym Microsoft Security Development Lifecycle (SDL). Dzięki temu architektom oprogramowania można szybko identyfikować i ograniczać potencjalne problemy z zabezpieczeniami, gdy są one stosunkowo proste i ekonomiczne do rozwiązania. W efekcie znacznie zmniejsza łączny koszt rozwoju. Ponadto zaprojektowano narzędzie z ekspertami nienależącymi do zabezpieczeń, co sprawia, że modelowanie zagrożeń jest łatwiejsze dla wszystkich deweloperów, oferując jasne wskazówki dotyczące tworzenia i analizowania modeli zagrożeń.

Odwiedź **[Threat Modeling Tool](threat-modeling-tool.md)** , aby zacząć już dzisiaj!

## <a name="mitigation-categories"></a>Kategorie łagodzenia

Środki zaradcze Threat Modeling Tool są podzielone zgodnie z ramką zabezpieczeń aplikacji sieci Web, która składa się z następujących elementów:

| Kategoria | Opis |
| -------- | ----------- |
| **[Inspekcja i rejestrowanie](threat-modeling-tool-auditing-and-logging.md)** | Jak i kiedy? Inspekcja i rejestrowanie — Zobacz, jak aplikacja rejestruje zdarzenia związane z zabezpieczeniami |
| **[Ponowne](threat-modeling-tool-authentication.md)** | Jaka jest Twoja rola? Uwierzytelnianie to proces, w którym jednostka udowadnia tożsamość innej jednostki, zwykle za pomocą poświadczeń, takich jak nazwa użytkownika i hasło. |
| **[Autoryzacja](threat-modeling-tool-authorization.md)** | Co możesz zrobić? Autoryzacja to sposób, w jaki aplikacja zapewnia kontrolę dostępu dla zasobów i operacji |
| **[Zabezpieczenia komunikacji](threat-modeling-tool-communication-security.md)** | Na kogo rozmawiasz? Zabezpieczenia komunikacji zapewniają, że cała komunikacja jest tak bezpieczna, jak to możliwe |
| **[Zarządzanie konfiguracją](threat-modeling-tool-configuration-management.md)** | Kto działa jako aplikacja? Z których baz danych nawiązuje połączenie? Jak ma być administrowane Twoja aplikacja? Jak są zabezpieczane te ustawienia? Zarządzanie konfiguracją to sposób, w jaki aplikacja obsługuje te problemy operacyjne |
| **[Kryptografia](threat-modeling-tool-cryptography.md)** | Jak zachowuje się tajemnice (poufność)? Jak można modyfikować dane lub biblioteki (integralność)? Jak są udostępniane nasiona losowo wybranych wartości, które muszą być silnie mocne? Kryptografia odnosi się do sposobu, w jaki aplikacja wymusza poufność i integralność |
| **[Zarządzanie wyjątkami](threat-modeling-tool-exception-management.md)** | Gdy wywołanie metody w aplikacji zakończy się niepowodzeniem, co robi aplikacja? Jak ma być ujawniane? Czy w przypadku użytkowników końcowych są zwracane przyjazne informacje o błędach? Czy przekazujesz cenne informacje o wyjątku z powrotem do obiektu wywołującego? Czy aplikacja działa prawidłowo? |
| **[Walidacja danych wejściowych](threat-modeling-tool-input-validation.md)** | Jak wiadomo, że dane wejściowe odbierane przez aplikację są prawidłowe i bezpieczne? Walidacja danych wejściowych odnosi się do sposobu, w jaki aplikacja filtruje, umożliwia szybkie lub odrzucanie danych wejściowych przed dodatkowym przetwarzaniem. Rozważ ograniczenie danych wejściowych za poorednictwem punktów wejścia i kodowanie danych wyjściowych za poorednictwem punktów wyjścia. Czy dane są zaufane ze źródeł, takich jak bazy danych i udziały plików? |
| **[Dane poufne](threat-modeling-tool-sensitive-data.md)** | Jak aplikacja obsługuje poufne dane? Poufne dane to sposób, w jaki aplikacja obsługuje wszelkie dane, które muszą być chronione w pamięci, za pośrednictwem sieci lub magazynów trwałych |
| **[Zarządzanie sesjami](threat-modeling-tool-session-management.md)** | Jak aplikacja obsługuje i chroni sesje użytkowników? Sesja odnosi się do szeregu powiązanych interakcji między użytkownikiem a aplikacją sieci Web. |

Ułatwia to zidentyfikowanie:

* Gdzie znajdują się najczęstsze błędy
* Gdzie są najbardziej funkcjonalne ulepszenia

W związku z tym te kategorie są używane do skoncentrowania się i określania priorytetów pracy bezpieczeństwa, dlatego w przypadku znajomości najważniejszych problemów z zabezpieczeniami w kategoriach walidacji danych wejściowych, uwierzytelniania i autoryzacji można rozpocząć pracę. Aby uzyskać więcej informacji, odwiedź stronę **[tego linku patentowego](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Następne kroki

Odwiedź **[Threat Modeling Tool zagrożeń](threat-modeling-tool-threats.md)** , aby dowiedzieć się więcej o kategoriach zagrożeń używanych przez narzędzie do generowania możliwych zagrożeń związanych z projektowaniem.