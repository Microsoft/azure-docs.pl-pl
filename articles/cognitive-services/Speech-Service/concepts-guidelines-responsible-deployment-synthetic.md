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
ms.openlocfilehash: 371ad2220b2b2b1b73e5611c5abef74454a548ad
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508273"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Wytyczne dotyczące odpowiedzialnego wdrożenia syntetycznej technologii głosowej

W tym artykule przedstawiono ogólne wytyczne dotyczące projektowania firmy Microsoft dotyczące korzystania z syntetycznej technologii głosowej. Te wytyczne zostały opracowane w ramach badań, które firma Microsoft prowadziła przy użyciu talent głosu, konsumentów i osób z zaburzeniami mowy, aby zapewnić odpowiedzialny rozwój głosów syntetycznych.

W przypadku wdrażania syntetycznej technologii mowy w większości scenariuszy stosowane są następujące wytyczne.

### <a name="disclose-when-the-voice-is-synthetic"></a>Ujawnianie, gdy głos jest syntetyczny
Odkończenie wygenerowanego przez komputer głosu nie tylko minimalizuje ryzyko szkodliwych rezultatów, ale również zwiększa zaufanie w organizacji dostarczającej głos. Dowiedz się więcej [na temat sposobu ujawniania](concepts-disclosure-guidelines.md).

Firma Microsoft wymaga od klientów ujawnienia syntetycznego charakteru niestandardowych neuronowych głosu dla użytkowników. 
* Upewnij się, że podajesz odpowiednie ujawnienie do odbiorców, szczególnie w przypadku korzystania z głosu dobrze znanej osoby — osoby podejmują swoją opinię na temat informacji na podstawie osoby, która ją udostępnia, niezależnie od tego, czy jest to świadome czy nieświadome.  Na przykład ujawnienie może być udostępniane na początku emisji. Aby uzyskać więcej informacji, odwiedź [wzorce ujawniania](concepts-disclosure-patterns.md).   
* Należy wziąć pod uwagę odpowiednie ujawnienie do rodziców lub innych stron z przypadkami użycia, które są przeznaczone dla małoletnich i podrzędnych — jeśli przypadek użycia jest przeznaczony dla małoletnich lub dzieci, należy się upewnić, że rodzice lub opiekuni prawne mogą zrozumieć ujawnienie informacji o korzystaniu z nośników syntetycznych i podjąć odpowiednie decyzje dotyczące małoletnich lub dzieci w zakresie korzystania z tego środowiska. 

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Wybieranie odpowiednich typów głosu dla danego scenariusza
Należy uważnie rozważyć kontekst użycia i potencjalną szkodę skojarzoną z użyciem głosu syntetycznego. Na przykład, głosy syntetyczne o wysokiej dokładności mogą nie być odpowiednie w scenariuszach o wysokim ryzyku, na przykład w przypadku komunikacji osobistej, transakcji finansowych lub złożonych sytuacji, które wymagają adaptacji ludzkiego lub empatię. 

Użytkownicy mogą również mieć różne oczekiwania na typy głosu. Na przykład podczas nasłuchiwania poufnych wiadomości odczytanych przez głos syntetyczny niektórzy użytkownicy preferują bardziej empatycznegoe i podobne do siebie tony, a inne wolą nieobciążonego głosu. Rozważ testowanie aplikacji, aby lepiej zrozumieć preferencje użytkownika.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Przezroczyste informacje o możliwościach i ograniczeniach
Użytkownicy mogą mieć większe oczekiwania w przypadku korzystania z syntetycznych agentów głosu o wysokiej wierności. Gdy możliwości systemu nie spełnią tych oczekiwań, zaufanie może pogorszyć się i może skutkować nieprzyjemnymi lub nawet szkodliwymi środowiskami.

### <a name="provide-optional-human-support"></a>Zapewnianie opcjonalnej pomocy technicznej
W niejednoznacznych scenariuszach transakcyjnych (na przykład w centrum pomocy technicznej) użytkownicy nie zawsze ufają agentowi komputera, aby odpowiednio reagować na ich żądania. W takich sytuacjach może być wymagana pomoc techniczna, niezależnie od realistycznej jakości głosu lub możliwości systemu.

## <a name="considerations-for-voice-talent"></a>Uwagi dotyczące Talenti głosu
Podczas pracy z usługą Voice talent, na przykład aktorów głosowych, do tworzenia głosów syntetycznych stosuje się poniższe wytyczne.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Uzyskaj znaczącą zgodę z talent głosu
Funkcja Voice oraz talentem powinna mieć kontrolę nad modelem głosowym (jak i miejscem, w którym zostanie użyty) i będzie kompensowana do użycia. Firma Microsoft wymaga od klientów niestandardowych, aby uzyskali jawne, jednotalente uprawnienia do tworzenia głosu syntetycznego i jego umowy z oraz talentemami głosu.  Jeśli tworzysz syntetyczny głos dobrze znanej osoby, należy udostępnić osobie za głos, aby edytować lub zatwierdzić zawartość.

Niektóre oraz talenteme głosu nie znają potencjalnych złośliwych użycia technologii i powinny być przestawione przez właścicieli systemu na informacje o możliwościach technologii. Firma Microsoft wymaga od klientów udostępnienia [informacji o talent głosu](/legal/cognitive-services/speech-service/disclosure-voice-talent) firmy Microsoft z użyciem głosu talent bezpośrednio lub przez upoważnionego przedstawiciela działu głosu talent, który opisuje sposób, w jaki rozmieszczono syntetyczne głosy i działają w połączeniu z funkcją zamiany tekstu na mowę.

## <a name="considerations-for-those-with-speech-disorders"></a>Zagadnienia związane z zaburzeniami mowy
Podczas pracy z osobami z zaburzeniami mowy, aby utworzyć lub wdrożyć syntetyczną technologię głosową, obowiązują następujące wytyczne.

### <a name="provide-guidelines-to-establish-contracts"></a>Zapewnienie wytycznych w celu ustalenia umów
Podaj wytyczne dotyczące ustanawiania kontraktów z osobami korzystającymi z syntetycznego głosu w celu uzyskania pomocy. Umowę należy rozważyć określenie podmiotów, które są właścicielami głosu, okresu użytkowania, kryteriów transferu własności, procedur usuwania czcionki głosowej i zapobiegania nieautoryzowanemu dostępowi. Ponadto należy włączyć przesyłanie własności czcionki głosowej po śmierci do członków rodziny, jeśli podano uprawnienia.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Konto dla niespójności w wzorcach mowy
Dla użytkowników indywidualnych z zaburzeniami mowy, które nagrywają własne czcionki głosowe, niespójności we wzorcu mowy (gnojowicowanie lub niemożność wymawiania niektórych wyrazów) mogą spowodować skomplikowanie procesu rejestrowania. W takim przypadku syntetyczna technologia głosu i sesje rejestrowania powinny je obsłużyć (to znaczy zapewnić przerwy i dodatkową liczbę sesji rejestrowania).

### <a name="allow-modification-over-time"></a>Zezwalaj na modyfikowanie w czasie
Osoby z zaburzeniami mowy chcą wprowadzać aktualizacje do głosu syntetycznego w celu odzwierciedlenia przedawnienia (na przykład elementu podrzędnego, który dociera do puberty). Użytkownicy mogą również mieć preferencje stylistyczne, które zmieniają się wraz z upływem czasu i mogą chcieć wprowadzić zmiany w wysokości, akcentie lub innych cechach głosu.


## <a name="reference-docs"></a>Dokumentacja dokumentacji

* [Ujawnienie dla talent głosu](/legal/cognitive-services/speech-service/disclosure-voice-talent)
* [Jak ujawnić](concepts-disclosure-guidelines.md)
* [Wzorce projektowania ujawniania](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>Następne kroki

* [Ujawnienie dla talent głosu](/legal/cognitive-services/speech-service/disclosure-voice-talent)
* [Jak ujawnić](concepts-disclosure-guidelines.md)
* [Wzorce projektowania ujawniania](concepts-disclosure-patterns.md)
