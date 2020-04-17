---
title: Wersje analizy kodu zabezpieczeń firmy Microsoft
description: Ten artykuł zawiera opis nadchodzących wydań rozszerzenia Analizy kodu zabezpieczeń firmy Microsoft
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/14/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: a07ce7437d664baca0cfdc310dbc2631f41fdbcc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81462037"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Publikacje i plan analizy kodu zabezpieczeń firmy Microsoft

Zespół Microsoft Security Code Analysis we współpracy z pomocą techniczną dla deweloperów z dumą ogłasza najnowsze i nadchodzące ulepszenia naszego rozszerzenia MSCA. Zapoznaj się z poniższym planem działania.

![Wydania](./media/security-code-analysis-releases/releases.png)

## <a name="credential-scanner-v20-released-on-april-1-2020"></a>Skaner poświadczeń w wersji 2.0: Wydany 1 kwietnia 2020

### <a name="innovations--improvements"></a>Innowacje & ulepszenia

- **Silnik rdzenia**

   - Średnia aktualizacja wydajności o 25% przy niemal liniowym czasie pracy
   - Wyszukiwanie kontekstowe/oparte na dowodach w celu zwiększenia dokładności
   - Ulepszenia ogólnego wykrywania haseł i logiki dopasowywania oczywistych symboli zastępczych (na przykład fakePassword)

- **Zasięg** — obsługa 25+ tajnych typów, w tym następujące góry wymagane:

   - Hasło certyfikatu konta sieci szkieletowej
   - Klucz tajny/API klienta
   - Nagłówek autoryzacji HTTP
   - Klucz tajnego dostępu klienta Amazon S3
   - Token dostępu klienta usługi Azure Active Directory
   - Klucz wzorzec/interfejs API funkcji platformy Azure
   - Klucz dostępu usługi Power BI
   - Wzorzec hasła szablonu usługi Azure Resource Manager

- **Dane wyjściowe**

   - Obsługa formatów plików wyjściowych SARIF 2.1 i CSV

## <a name="binskim-v160-to-be-released-on-april-2020"></a>BinSkim v1.6.0: Ukaże się w kwietniu 2020

### <a name="improvements"></a>Ulepszenia

- FUNKCJA: Aktualizacja do końcowego SARIF v2 (wersja 2.1.16). Umożliwia to buforowanie wyników podczas przekazywania skrótów w wierszu polecenia, co stanowi znaczną poprawę wydajności podczas cyklicznej analizy katalogów z wieloma kopiami obiektów docelowych skanowania.
- POPRAWKA BŁĘDU: Napraw literówkę w BA2021. DoNotMarkWritableSectionsAsWykonalne wyjście.
- WYDAJNOŚĆ: Wyeliminuj ładowanie bazy danych dla wszystkich niemieszkalnych trybów dla zarządzanych zestawów, w tym plików binarnych biblioteki IL (z wyprzedzeniem skompilowanych).
- FALSE NEGATIVE FIX: Sprawdź, czy PDB umieszczony obok pliku binarnego faktycznie pasuje do pliku binarnego w analizie
- CECHA: Podaj argument --local-symbol-directories, aby określić dodatkowe lokalizacje wyszukiwania PDB (lokalne, nienazytalne)
- FALSE POSITIVE FIX: Pomiń analizę opartą na PDB dla wygenerowanego .NET core native bootstrap exe (który nie jest kodem kontrolowanym przez użytkownika).

## <a name="whats-next-in-fy20"></a>Co dalej w FY20?

- Narzędzie Analizy zabezpieczeń Java
- Narzędzie Analizy zabezpieczeń języka Python
- ES Lint zastąpić TS Lint dla TypeScript i JavaScript

## <a name="next-steps"></a>Następne kroki

Instrukcje dotyczące dołączania i instalowania kodu zabezpieczeń firmy Microsoft można znaleźć w naszym [podręczniku dołączania i instalacji.](security-code-analysis-onboard.md)

Jeśli masz więcej pytań na temat rozszerzenia i oferowanych narzędzi, sprawdź naszą [stronę FAQ](security-code-analysis-faq.md).
