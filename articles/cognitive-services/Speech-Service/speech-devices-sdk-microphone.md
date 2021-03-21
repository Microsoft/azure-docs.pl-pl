---
title: Zestaw SDK urządzenia mowy zalecenia dotyczące tablicy mikrofonów
titleSuffix: Azure Cognitive Services
description: Zalecenia dotyczące tablicy mikrofonów zestawu SDK urządzeń mowy. Te geometrie tablicy są zalecane do użycia z stosem audio firmy Microsoft.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: 698a1d52af6c2472d6c025851ead1a0b6a6dff82
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95015345"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Zestaw SDK urządzenia mowy zalecenia dotyczące tablicy mikrofonów

W tym artykule dowiesz się, jak zaprojektować macierz mikrofonową dla zestawu Speech Devices SDK.

Zestaw SDK urządzeń mowy działa najlepiej z tablicą mikrofonu, która została zaprojektowana zgodnie z poniższymi wskazówkami, w tym geometrią mikrofonu i wyborem składnika. Wskazówki są również podane w kwestiach dotyczących integracji i instalacji elektrycznej.

## <a name="microphone-geometry"></a>Geometria mikrofonu

Poniższe geometrie tablicowe są zalecane do użycia z stosem audio firmy Microsoft. Lokalizacja źródeł dźwięku i odrzucanie szumów otoczenia jest ulepszona z większą liczbą mikrotelefonów z zależnościami od konkretnych aplikacji, scenariuszy użytkowników i współczynnika formularza urządzenia.

| MICS & geometrii | Tablica cykliczna | Tablica cykliczna | Tablica liniowa | Tablica liniowa |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \# Mics | 7 | 4 | 4 | 2 |
| Geometria | 6 zewnętrznych, 1 centrum, promień = 42,5 mm, równomiernie rozmieszczone | 3 zewnętrzne, 1 centrum, promień = 42,5 mm, równomiernie rozmieszczone | Długość = 120 mm, odstępy = 40 mm | Odstępy = 40 mm |

Kanały mikrofonu powinny być uporządkowane zgodnie z numerami przedstawionymi dla każdej z powyższych tablic i zwiększają się od 0. Stos audio firmy Microsoft wymaga dodatkowego strumienia referencyjnego odtwarzania audio do wykonywania anulowania echa.

## <a name="component-selection"></a>Wybór składników

Należy wybrać składniki mikrofonu, aby dokładnie odtworzyć sygnał bez szumu i zniekształcenia.

Zalecane właściwości w przypadku wybrania mikrotelefonu są następujące:

| Parametr | Zalecane |
| --------- | ----------- |
| SNR | \>= 65 dB (1 kHz sygnał 94 dBSPL, szum ważony) |
| Dopasowanie amplitudy | 1 dB @ 1 kHz |
| Dopasowanie fazy | 2 ° @ 1 kHz |
| Punkt przeciążenia akustycznego (AOP) | \>= 120 dBSPL (THD = 10%) |
| Szybkość transmisji bitów | Minimum 24-bitowe |
| Częstotliwość próbkowania | Minimum 16 kHz\* |
| Odpowiedź dotycząca częstotliwości | ± 3 dB, 200-8000 Hz maska zmiennoprzecinkowa\* |
| Niezawodność | Zakres temperatury magazynu — 40 °C do 70 °C<br />Zakres temperatury działania — od 20 °C do 55 °C |

\*_W przypadku aplikacji do obsługi wysokiej jakości (VoIP) mogą być potrzebne wyższe szybkości próbkowania lub zakresy częstotliwości "szersze"_

Dobry wybór składnika musi być sparowany z dobrą integracją Electroacoustic, aby uniknąć utraty wydajności użytych składników. Unikatowe przypadki użycia mogą również wymagać dodatkowych wymagań (na przykład zakresów temperatury działania).

## <a name="microphone-array-integration"></a>Integracja z tablicą mikrofonu

Wydajność macierzy mikrofonu, gdy jest ona zintegrowana z urządzeniem, różni się od specyfikacji składnika. Należy upewnić się, że mikrofony są dobrze dopasowane po integracji. W związku z tym wydajność urządzenia mierzona po jakichkolwiek stałych zyskach lub EQ powinna spełniać następujące zalecenia:

| Parametr          | Zalecane                                        |
| ------------------ | -------------------------------------------------- |
| SNR                | \> 63 dB (1 kHz sygnał 94 dBSPL, szum ważony) |
| Czułość wyjściowa | -26 dBFS/PA @ 1 kHz (zalecane)                  |
| Dopasowanie amplitudy | 2 dB, 200-8000 Hz                                |
| THD%\*             | ≤ 1%, 200-8000 Hz, 94 dBSPL, piąty porządek             |
| Odpowiedź dotycząca częstotliwości | ± 6 dB, 200-8000 Hz maska zmiennoprzecinkowa\*\*              |

\*\*_Do mierzenia THD (np. Neumann KH120) wymagany jest głośnik o niskim zniekształceniu._

\*\*_Zakres częstotliwości "szerszy" może być wymagany w przypadku aplikacji do komunikacji z wysoką jakością (VoIP)_

## <a name="speaker-integration-recommendations"></a>Zalecenia dotyczące integracji głośników

Ponieważ w przypadku urządzeń rozpoznawania mowy, które zawierają głośniki, jest wymagane anulowanie ECHA, dostępne są dodatkowe zalecenia dotyczące wyboru głośników i integracji.

| Parametr | Zalecane |
| --------- | ----------- |
| Uwagi dotyczące liniowości | Bez przetwarzania nieliniowego po odwołaniu się do osoby mówiącej, w przeciwnym razie wymagany jest strumień referencyjny pętli sprzężenia zwrotnego |
| Sprzężenie zwrotne głośników | Udostępniane za pośrednictwem WASAPI, prywatnych interfejsów API, wtyczki niestandardowej ALSA (Linux) lub dostarczonych za pośrednictwem kanału oprogramowania układowego |
| THD% | 5.3.630-5000 5. rzędów o minimalnym przepięciu, 315-500 6,3 0,8 70 |
| Echo sprzęgania z mikrotelefonami | \> -10 dB TCLw z użyciem ITU-T G. 122 załącznik B. 4 Metoda, znormalizowana do poziomu mikrofonu<br />TCLw = TCLwmeasured \+ (zmierzony poziom — czułość wyjściowa)<br />TCLw = TCLwmeasured \+ (poziom zmierzony-(-26)) |

## <a name="integration-design-architecture"></a>Architektura projektowania integracji

Następujące wskazówki dotyczące architektury są niezbędne podczas integrowania urządzeń z urządzeniami:

| Parametr | Zalecenie |
| --------- | -------------- |
| Podobieństwo portów mikrofonu | Wszystkie porty mikrofonu mają tę samą długość w tablicy |
| Wymiary portów MIC | Rozmiar portu o 0,8 – 1,0 mm. Długość portu/średnica portu \< 2 |
| Pieczętowanie mikrofonu         | Pieczętowanie gaskets jednolicie zaimplementowane w stosie. Zalecany \> współczynnik kompresji na 70% dla gaskets piany |
| Niezawodność mikrofonu     | Siatka powinna być używana w celu zapobiegania pył i ruchu przychodzącego (między PCB w przypadku końcowych portów z portami i pieczętowania Gasket/górnej okładki) |
| Izolacja MIC       | Gaskets gumowe i wibracje dzięki strukturze, szczególnie w przypadku izolowania wszelkich ścieżek wibracji ze względu na zintegrowane głośniki |
| Zegar próbkowania      | Dźwięk urządzenia musi być bezpłatny i rozwijany z niską dryfem |
| Możliwość rejestrowania   | Urządzenie musi mieć możliwość równoczesnego rejestrowania pojedynczych strumieni nieprzetworzonych kanałów |
| USB                 | Wszystkie urządzenia wejściowe audio USB muszą ustawić deskryptory zgodnie z [specyfikacją rev3 urządzeń audio USB](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| Geometria mikrofonu | Sterowniki muszą implementować poprawnie [deskryptory geometrii macierzy mikrofonu](/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) |
| Możliwości odnajdywania     | Urządzenia nie mogą mieć żadnych niewykrywalnych lub niekontrolowanych sprzętu, oprogramowania układowego ani nieliniowych algorytmów przetwarzania audio na/z urządzenia |
| Format przechwytywania      | Formaty przechwytywania muszą mieć minimalną częstotliwość próbkowania wynoszącą 16 kHz i zalecaną 24-bitową głębię |

## <a name="electrical-architecture-considerations"></a>Zagadnienia dotyczące architektury elektrycznej

Jeśli ma to zastosowanie, tablice mogą być połączone z hostem USB (na przykład SoC z uruchomionym stosem Microsoft audio) i interfejsami do usług mowy lub innych aplikacji.

Składniki sprzętowe, takie jak konwersja PDM na TDM, powinny mieć pewność, że zakres dynamiczny i SNR mikrotelefonu są zachowywane w ramach ponownych prób.

Aby zapewnić wymaganą przepustowość dla maksymalnie siedmiu kanałów przy wyższych stawkach i głębiach bitów, należy obsłużyć MCUs audio o dużej 2,0 szybkości.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o zestawie SDK urządzeń mowy](speech-devices-sdk.md)