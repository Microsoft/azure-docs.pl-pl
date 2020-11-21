---
title: Wytyczne dotyczące odpowiedzialnego wdrożenia syntetycznej technologii głosowej
titleSuffix: Azure Cognitive Services
description: Ogólne wytyczne dotyczące projektowania firmy Microsoft dotyczące korzystania z syntetycznej technologii głosowej. Zostały one opracowane w badaniach, które firma Microsoft prowadziła przy użyciu głosu talent, konsumentów, jak również osób z zaburzeniami mowy, aby zapewnić odpowiedzialny rozwój głosu syntetycznego.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: 7d80ffb575c6aa15695279584b58288cbc16be43
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024980"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Wytyczne dotyczące odpowiedzialnego wdrożenia syntetycznej technologii głosowej
Poniżej przedstawiono ogólne wytyczne dotyczące projektowania firmy Microsoft dotyczące korzystania z syntetycznej technologii głosowej. Zostały one opracowane w badaniach, które firma Microsoft prowadziła przy użyciu głosu talent, konsumentów, a także osób z zaburzeniami mowy, aby zapewnić odpowiedzialny rozwój głosu syntetycznego.

## <a name="general-considerations"></a>Zagadnienia ogólne
W przypadku wdrażania syntetycznej technologii mowy w większości scenariuszy stosowane są następujące wytyczne.

### <a name="disclose-when-the-voice-is-synthetic"></a>Ujawnianie, gdy głos jest syntetyczny
Odkończenie wygenerowanego przez komputer głosu nie tylko minimalizuje ryzyko szkodliwych rezultatów, ale również zwiększa zaufanie w organizacji dostarczającej głos. Dowiedz się więcej [na temat sposobu ujawniania](concepts-disclosure-guidelines.md).

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Wybieranie odpowiednich typów głosu dla danego scenariusza
Należy uważnie rozważyć kontekst użycia i potencjalną szkodę skojarzoną z użyciem głosu syntetycznego. Na przykład, głosy syntetyczne o wysokiej dokładności mogą nie być odpowiednie w scenariuszach o wysokim ryzyku, na przykład w przypadku komunikacji osobistej, transakcji finansowych lub złożonych sytuacji, które wymagają adaptacji ludzkiego lub empatię. Użytkownicy mogą również mieć różne oczekiwania na typy głosu. Na przykład podczas nasłuchiwania poufnych wiadomości odczytywanych przez syntetyczne dane głosowe niektórzy użytkownicy preferują bardziej empatycznegoe i podobne do odczytu wiadomości, podczas gdy inne preferują więcej niż jeden głos. Rozważ testowanie aplikacji, aby lepiej zrozumieć preferencje użytkownika.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Przezroczyste informacje o możliwościach i ograniczeniach
Użytkownicy mogą mieć większe oczekiwania w przypadku korzystania z syntetycznych agentów głosu o wysokiej wierności. W związku z tym, gdy możliwości systemu nie spełnią tych oczekiwań, zaufanie może pogorszyć się i może skutkować nieprzyjemnymi lub nawet szkodliwymi środowiskami.

### <a name="provide-optional-human-support"></a>Zapewnianie opcjonalnej pomocy technicznej
W niejednoznacznych scenariuszach transakcyjnych (na przykład w centrum pomocy technicznej) użytkownicy nie zawsze ufają agentowi komputera, aby odpowiednio reagować na ich żądania. W takich sytuacjach może być wymagana pomoc techniczna, niezależnie od realistycznej jakości głosu lub możliwości systemu.

## <a name="considerations-for-voice-talent"></a>Uwagi dotyczące Talenti głosu
Podczas pracy z usługą Voice talent, na przykład aktorów głosowych, do tworzenia głosów syntetycznych stosuje się poniższe wytyczne.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Uzyskaj znaczącą zgodę z talent głosu
Talent głosu oczekuje na kontrolę nad ich czcionką głosową (jak i miejsce jej użycia) i są kompensowane w dowolnym momencie użycia. Właściciele systemu powinni w związku z tym uzyskiwać jawne uprawnienia od talentów głosu i mieć jasne specyfikacje umowne dotyczące przypadków użycia, czas użytkowania, wynagrodzenie i tak dalej. Niektóre Talente głosu nie znają potencjalnych złośliwych użycia technologii i powinny być przestawione przez właścicieli systemu na informacje o możliwościach technologii. Aby uzyskać więcej informacji na temat Talenti i wyrażania głosu, Przeczytaj nasze [ujawnienie dla głosu talent](/legal/cognitive-services/speech-service/disclosure-voice-talent).


## <a name="considerations-for-those-with-speech-disorders"></a>Zagadnienia związane z zaburzeniami mowy
Podczas pracy z osobami z zaburzeniami mowy, aby utworzyć lub wdrożyć syntetyczną technologię głosową, obowiązują następujące wytyczne.

### <a name="provide-guidelines-to-establish-contracts"></a>Zapewnienie wytycznych w celu ustalenia umów
Podaj wytyczne dotyczące ustanawiania kontraktów z osobami korzystającymi z syntetycznego głosu w celu uzyskania pomocy. Umowę należy rozważyć określenie podmiotów, które są właścicielami głosu, okresu użytkowania, kryteriów transferu własności, procedur usuwania czcionki głosowej i zapobiegania nieautoryzowanemu dostępowi. Ponadto należy włączyć umowny transfer własności dźwiękowej po śmierci do członków rodziny, jeśli dana osoba ma odpowiednie uprawnienia.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Konto dla niespójności w wzorcach mowy
Dla użytkowników indywidualnych z zaburzeniami mowy, które nagrywają własne czcionki głosowe, niespójności we wzorcu mowy (gnojowicowanie lub niemożność wymawiania niektórych wyrazów) mogą spowodować skomplikowanie procesu rejestrowania. W takim przypadku syntetyczna technologia głosu i sesje rejestrowania powinny je obsłużyć (to znaczy zapewnić przerwy i dodatkową liczbę sesji rejestrowania).

### <a name="allow-modification-over-time"></a>Zezwalaj na modyfikowanie w czasie
Osoby z zaburzeniami mowy chcą wprowadzać aktualizacje do głosu syntetycznego w celu odzwierciedlenia przedawnienia (na przykład elementu podrzędnego, który dociera do puberty). Użytkownicy mogą również mieć preferencje stylistyczne, które zmieniają się wraz z upływem czasu i mogą chcieć wprowadzić zmiany w wysokości, akcentie lub innych cechach głosu.


## <a name="reference-docs"></a>Dokumentacja dokumentacji

* [Ujawnienie dla talent głosu](/legal/cognitive-services/speech-service/disclosure-voice-talent)
* [Kontroli — Omówienie](concepts-gating-overview.md)
* [Jak ujawnić](concepts-disclosure-guidelines.md)
* [Wzorce projektowania ujawniania](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>Następne kroki

* [Ujawnienie dla talent głosu](/legal/cognitive-services/speech-service/disclosure-voice-talent)
* [Jak ujawnić](concepts-disclosure-guidelines.md)
* [Wzorce projektowania ujawniania](concepts-disclosure-patterns.md)