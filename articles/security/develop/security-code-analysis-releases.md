---
title: Wersje analizy kodu zabezpieczeń firmy Microsoft
description: W tym artykule opisano przyszłe wersje rozszerzenia Microsoft Security code Analysis
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/24/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: d4281d3b6132e551283a71cd1801ef462fbfc68c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146123"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Wersje i plany analizy kodu zabezpieczeń firmy Microsoft

Zespół analizy kodu zabezpieczeń firmy Microsoft w partnerstwie z pomoc techniczna Developerem jest dumny, aby ogłosić najnowsze i nadchodzące ulepszenia rozszerzenia MSCA. Zapoznaj się z planem poniżej.

![Wydania](./media/security-code-analysis-releases/releases.png)

## <a name="credential-scanner-v20-released-in-april-2020"></a>Skaner poświadczeń v 2.0: wydano w kwietniu 2020

### <a name="innovations--improvements"></a>Innowacje & ulepszenia

- **Silnik podstawowy**

   - Średnie uaktualnienie wydajności o 25% z niemal liniowym czasem wykonywania
   - Wyszukiwanie na podstawie kontekstu/dowodu i klasyfikacja dla zwiększonej dokładności
   - Ulepszenia ogólnego wykrywania haseł i logiki dopasowania dla oczywistych symboli zastępczych (na przykład fakePassword)

- **Pokrycie** — wsparcie dla 25 + tajnych typów, w tym następujące wymagane u góry:

   - Hasło certyfikatu konta sieci szkieletowej
   - Wpis tajny klienta/klucz interfejsu API
   - Nagłówek autoryzacji HTTP
   - Klucz dostępu klucza tajnego klienta Amazon S3
   - Azure Active Directory token dostępu klienta
   - Główny klucz interfejsu API usługi Azure Functions
   - Klucz dostępu Power BI
   - Wzorzec hasła szablonu Azure Resource Manager

- **Dane wyjściowe**

   - Obsługa formatów plików wyjściowych SARIF 2,1 i CSV

## <a name="binskim-v160-released-in-april-2020"></a>BinSkim v 1.6.0: Opublikowano w kwietniu 2020

### <a name="improvements"></a>Ulepszenia

- Funkcja: Aktualizacja do wersji ostatecznej SARIF v2 (wersja 2.1.16). Dzięki temu wyniki są buforowane w przypadku przekazywania wartości skrótu w wierszu polecenia, a znaczne zwiększenie wydajności podczas rekursywnego analizowania katalogów z wieloma kopiami elementów docelowych skanowania.
- Poprawka usterki: Poprawka literówki w BA2021. DoNotMarkWritableSectionsAsExecutable dane wyjściowe.
- WYDAJNOŚĆ: eliminowanie ładowania plików PDB dla wszystkich trybów niemieszanych dla zestawów zarządzanych, w tym biblioteki IL (przed czasem skompilowaną).
- FAŁSZYWa poprawka ujemna: Sprawdź, czy plik PDB umieszczony obok danych binarnych rzeczywiście pasuje do danych binarnych w obszarze Analiza
- Funkcja: Podaj--Local-Symbols argumentu, aby określić dodatkowe lokalizacje wyszukiwania plików PDB (lokalne, niebędące symbolami)
- FAŁSZ POZYTYWNEj poprawki: Pomiń analizę opartą na pliku PDB dla wygenerowanego natywnego pliku wykonywalnego Bootstrap platformy .NET Core (który nie jest kodem kontrolowanym przez użytkownika).

## <a name="whats-next-in-fy20"></a>Co dalej w FY20?

- Narzędzie do analizy zabezpieczeń języka Java
- Narzędzie do analizy zabezpieczeń języka Python
- ES lint zastępujący usługi TS lint dla języka TypeScript i JavaScript

## <a name="next-steps"></a>Następne kroki

Aby uzyskać instrukcje na temat sposobu dołączania i instalowania analizy kodu zabezpieczeń firmy Microsoft, zapoznaj się z naszym przewodnikiem dotyczącym dołączania [i instalacji](security-code-analysis-onboard.md).

Jeśli masz więcej pytań na temat rozszerzenia i oferowanych narzędzi, zapoznaj się ze [stroną często zadawanych pytań](security-code-analysis-faq.md).
