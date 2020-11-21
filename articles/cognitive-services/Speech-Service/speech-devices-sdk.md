---
title: Zestaw SDK urządzeń mowy — usługa mowy
titleSuffix: Azure Cognitive Services
description: Rozpocznij pracę z zestawem SDK urządzeń mowy. Usługa mowy współpracuje z wieloma urządzeniami i źródłami audio. Zestaw SDK urządzeń rozpoznawania mowy jest wstępnie dostrojoną biblioteką, która jest sparowana z utworzonymi przez siebie pakietami deweloperskimi.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: 0309329b37436022dc88f8951853c4bb09a2c080
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015277"
---
# <a name="what-is-the-speech-devices-sdk"></a>Co to jest zestaw SDK urządzeń mowy?

[Usługa mowy](overview.md) współpracuje z wieloma urządzeniami i źródłami audio. Teraz możesz robić aplikacje mowy na następnym poziomie przy użyciu dopasowanego sprzętu i oprogramowania. Zestaw SDK urządzeń rozpoznawania mowy jest wstępnie dostrojoną biblioteką, która jest sparowana z utworzonymi przez siebie pakietami deweloperskimi.

Zestaw SDK urządzeń mowy może pomóc:

- Szybkie testowanie nowych scenariuszy głosowych.
- Łatwiejsza integracja usługi mowy opartej na chmurze na urządzeniu.
- Utwórz wyjątkowe środowisko użytkownika dla klientów.

Zestaw SDK urządzeń mowy korzysta z [zestawu Speech SDK](speech-sdk.md). Korzystając z naszych zaawansowanych algorytmów przetwarzania dźwięku z tablicą mikrofonu urządzenia, można wysłać dźwięk do [usługi mowy](overview.md). Zapewnia dokładne [rozpoznawanie mowy](speech-to-text.md) z odłożeniami przy użyciu funkcji pomijania szumów, anulowania echa, Beamforming i dereverberation.

Za pomocą zestawu Speech Devices SDK można także tworzyć urządzenia otoczenia z własnymi własnymi [słowami kluczowymi](./custom-keyword-basics.md). Słowo kluczowe Custom zawiera wskaźnik uruchamiający interakcję użytkownika, która jest unikatowa dla Twojej marki.

Zestaw SDK urządzeń mowy umożliwia korzystanie z różnych scenariuszy z obsługą głosu, takich jak [asystentów głosowych](./voice-assistants.md), systemów porządkujących się na stacjach, [transkrypcji konwersacji](./conversation-transcription.md)i inteligentnych głośników. Możesz odpowiedzieć użytkownikom z tekstem, mówić z powrotem do nich przy użyciu domyślnego lub [niestandardowego głosu](./how-to-custom-voice-create-voice.md), podać wyniki wyszukiwania, [przetłumaczać](speech-translation.md) je na inne języki i nie tylko. Czekamy na zapoznaj się z kompilacją.

## <a name="get-the-speech-devices-sdk"></a>Pobieranie zestawu Speech Devices SDK

### <a name="android"></a>Android

Zestaw SDK urządzeń usługi Speech dla systemu Android obsługuje [roobo V1](speech-devices-sdk-roobo-v1.md) i urządzenia równoważne, aby pobrać najnowszą wersję [zestawu SDK urządzeń z systemem Android](https://aka.ms/sdsdk-download-android).


Jeśli masz inne urządzenie z systemem Android, takie jak telefon lub telefon komórkowy, Zacznij od [zestawu Android Speech SDK](speech-sdk.md)


### <a name="windows"></a>Windows

W przypadku systemu Windows Przykładowa aplikacja jest udostępniana jako aplikacja Java dla wielu platform. Pobierz najnowszą wersję [zestawu SDK urządzeń mowy środowiska JRE](https://aka.ms/sdsdk-download-JRE).
Aplikacja została skompilowana przy użyciu pakietu zestawu Speech SDK, a środowisko IDE środowiska Java (v4) w systemie 64-bitowym. Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

### <a name="linux"></a>Linux

W przypadku systemu Linux Przykładowa aplikacja jest udostępniana jako aplikacja w języku Java dla wielu platform. Pobierz najnowszą wersję [zestawu SDK urządzeń mowy środowiska JRE](https://aka.ms/sdsdk-download-JRE).
Aplikacja została skompilowana przy użyciu pakietu SDK mowy, a środowisko IDE języka Java (v4) w systemie 64-bitowym Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8). Działa ona w 64-bitowym środowisku uruchomieniowym Java 8 języka Java (JRE).

Dodatkowe pliki binarne są udostępniane do obsługi nadchodzących urządzeń, [roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2), [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter), [GGEC głośnik](https://aka.ms/sdsdk-download-speaker), [Linux ARM32](https://aka.ms/sdsdk-download-linux-arm32)i [Linux arm64](https://aka.ms/sdsdk-download-linux-arm64).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wybieranie urządzenia mowy](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](overview.md#try-the-speech-service-for-free)