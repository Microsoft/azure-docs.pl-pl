---
title: Wersje analizy kodu zabezpieczeń firmy Microsoft
description: W tym artykule opisano przyszłe wersje rozszerzenia Microsoft Security code Analysis
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 03/22/2021
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 7596df66dbcbe1b7cdefab4811da7174bc83ac65
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801183"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Wersje i plany analizy kodu zabezpieczeń firmy Microsoft

> [!Note]
> Od 1 marca 2022 rozszerzenie Microsoft Security code Analysis (MSCA) zostanie wycofane. Istniejący klienci MSCA będą zachować dostęp do MSCA do 1 marca 2022. Zapoznaj się z [narzędziami do analizy kodu źródłowego OWASP](https://owasp.org/www-community/Source_Code_Analysis_Tools) , aby zapoznać się z alternatywnymi opcjami w usłudze Azure DevOps. W przypadku klientów mających na celu zaplanowanie migracji do usługi GitHub można sprawdzić [Zaawansowane zabezpieczenia usługi GitHub](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security).

Zespół analizy kodu zabezpieczeń firmy Microsoft w partnerstwie z pomoc techniczna Developerem jest dumny, aby ogłosić najnowsze i nadchodzące ulepszenia rozszerzenia MSCA.


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

- Funkcja: Aktualizacja do wersji ostatecznej SARIF v2 (wersja 2.1.16). Ta aktualizacja umożliwia Buforowanie wyników w przypadku przekazywania wartości skrótu w wierszu polecenia, znaczący wzrost wydajności podczas rekursywnego analizowania katalogów z wieloma kopiami elementów docelowych skanowania.
- Poprawka usterki: Poprawka literówki w BA2021. DoNotMarkWritableSectionsAsExecutable dane wyjściowe.
- WYDAJNOŚĆ: eliminowanie ładowania plików PDB dla wszystkich trybów niemieszanych dla zestawów zarządzanych, w tym biblioteki IL (przed czasem skompilowaną).
- FAŁSZYWa poprawka ujemna: Sprawdź, czy plik PDB umieszczony obok danych binarnych rzeczywiście pasuje do danych binarnych w obszarze Analiza
- Funkcja: Podaj--Local-Symbols argumentu, aby określić dodatkowe lokalizacje wyszukiwania plików PDB (lokalne, niebędące symbolami)
- FAŁSZ POZYTYWNEj poprawki: Pomiń analizę opartą na pliku PDB dla wygenerowanego natywnego pliku wykonywalnego Bootstrap platformy .NET Core (który nie jest kodem kontrolowanym przez użytkownika).

## <a name="whats-next-in-q3-cy20"></a>Co dalej w kwartale CY20?

- Narzędzie do analizy zabezpieczeń języka Java
- Narzędzie do analizy zabezpieczeń języka Python
- ES lint zastępujący usługi TS lint dla języka TypeScript i JavaScript
- Narzędzie do analizy szablonów Menedżer zasobów

## <a name="tool-deprecation-notification"></a>Powiadomienie o zaniechaniu narzędzia

### <a name="microsoft-security-risk-detection-msrd-is-deprecated-on-june-26-2020"></a>Wykrywanie zagrożeń zabezpieczeń firmy Microsoft (MSRD) jest przestarzałe w czerwcu 26 2020.

Przestarzała usługa MSRD rozmytego zostanie zastąpiona platformą Open Source, która umożliwia samodzielne rozciąganie dla deweloperów platformy Azure. Ta platforma jest obecnie opracowywana i testowana w ramach partnerstwa z wieloma podstawowymi zespołami produktów firmy Microsoft. Ta platforma rozmytego integruje programy oczyszczające i zezwala na adaptacyjne testy rozmyte wbudowane w potoki ciągłej integracji/ciągłego wdrażania, które zwiększają się wraz z projektami oprogramowania. Wydanie typu open source tej platformy zaplanowano na drugą połowę 2020.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać instrukcje na temat sposobu dołączania i instalowania analizy kodu zabezpieczeń firmy Microsoft, zapoznaj się z naszym przewodnikiem dotyczącym dołączania [i instalacji](security-code-analysis-onboard.md).

Jeśli masz więcej pytań na temat rozszerzenia i oferowanych narzędzi, zapoznaj się ze [stroną często zadawanych pytań](security-code-analysis-faq.md).
