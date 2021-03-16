---
title: Informacje o urządzeniu audio usługi Azure Percept
description: Dowiedz się więcej o usłudze Azure Percept audio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 8f57c63868d6f70094cf040a92c0124b46477758
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103490198"
---
# <a name="introduction-to-azure-percept-audio"></a>Wprowadzenie do usługi Azure Percept audio

Usługa Azure Percept audio to urządzenie do akcesoriów, które dodaje możliwości mowy do usługi Azure Percept DK. Zawiera wstępnie skonfigurowany procesor audio i tablicę liniową z czterema mikrofonami, dzięki czemu można zastosować polecenia głosowe, wykrywania trendów słów kluczowych i odmowę oddzielania pól do lokalnych urządzeń nasłuchujących przy użyciu usługi Azure Cognitive Services. Usługa Azure Percept audio umożliwia producentom urządzeń rozszerzenie platformy Azure Percept DK poza funkcję Vision na nowe, inteligentne urządzenia aktywowane z dźwiękiem. Jest ona zintegrowana z platformą Azure Percept DK, Azure Percept Studio i innymi usługami Azure Edge Management. Jest ona dostępna do zakupu w [Sklepie Microsoft Online](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

> [!div class="nextstepaction"]
> [Kup teraz](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

:::image type="content" source="./media/overview-azure-percept-audio/percept-audio.png" alt-text="Urządzenie audio usługi Azure Percept.":::

## <a name="azure-percept-audio-components"></a>Składniki audio usługi Azure Percept

Usługa Azure Percept audio zawiera następujące główne składniki:

- Gotowe do produkcji urządzenie audio usługi Azure Percept (SoM) z czterema procesorami Array i przetwarzaniem audio wykonane przez koder-dekoder XMOS
- Tablica deweloperów (międzywykonawca) (w tym 2 przyciski, 3. diody LED, Micro USB i 3,5 mm gniazda audio)
- Wymagane kable: kabel FPC, USB Micro Type-B do USB-A
- Karta powitalna
- Płyta montażowa mechaniczna z integracją instalacji z serii 80/20 1010

## <a name="compute-capabilities"></a>Możliwości obliczeniowe 

Usługa Azure Percept audio przekazuje dane wejściowe audio przez stos mowy, który jest uruchamiany na PROCESORze tablicy nośnej platformy Azure Percept DK w sposób hybrydowy w chmurze. W związku z tym, usługa Azure Percept Audio wymaga tablicy nośnej z systemem operacyjnym, który obsługuje stos mowy, aby można było go wykonać. 

Przetwarzanie odbywa się w następujący sposób: 

- Azure Percept audio: przechwytuje i konwertuje dźwięk, a następnie wysyła je do gniazda DK i audio.

- Azure Percept DK: stos mowy wykonuje anulowanie tworzenia i ECHA, a następnie przetwarza przychodzące audio, aby zoptymalizować mowę. Następnie wykonuje wykrywania trendów słowa kluczowego.

- Chmura: przetwarza polecenia i frazy języka naturalnego, weryfikację słów kluczowych i przeszkolenie. 

- Offline: Jeśli urządzenie jest w trybie offline, wykryje słowo kluczowe i przechwytuje dane telemetryczne stanu połączenia internetowego. Zwiększoną fałszywą stawkę za zaakceptowanie słowa kluczowego wykrywania trendów można zaobserwować, ponieważ nie można wykonać weryfikacji słowa kluczowego w chmurze. 

## <a name="getting-started"></a>Wprowadzenie

- [Złóż platformę Azure Percept DK](./quickstart-percept-dk-unboxing.md)
- [Ukończ działanie Instalatora platformy Azure Percept DK](./quickstart-percept-dk-set-up.md)
- [Łączenie urządzenia audio usługi Azure Percept z Devkit](./quickstart-percept-audio-setup.md)

## <a name="build-a-no-code-prototype"></a>Kompilowanie prototypu bez kodu

Twórz [rozwiązanie do obsługi mowy bez kodu](./tutorial-no-code-speech.md) w [usłudze Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) przy użyciu szablonów asystenta głosowego usługi Azure Percept dla scenariuszy Hotelarstwo, opieki zdrowotnej, spisu i motoryzacyjnych.

### <a name="manage-your-no-code-speech-solution"></a>Zarządzanie rozwiązaniem mowy bez kodu

- [Konfigurowanie asystenta głosowego w usłudze IoT Hub](./how-to-manage-voice-assistant.md)
- [Konfigurowanie asystenta głosowego w usłudze Azure Percept Studio](./how-to-configure-voice-assistant.md)
- [Rozwiązywanie problemów z usługą Azure Percept audio](./troubleshoot-audio-accessory-speech-module.md)

## <a name="additional-technical-information"></a>Dodatkowe informacje techniczne

- [Arkusz danych audio usługi Azure Percept](./azure-percept-audio-datasheet.md)
- [Zachowanie przycisku i diody LED](./audio-button-led-behavior.md)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Kupowanie urządzenia audio usługi Azure Percept w Sklepie Microsoft Online](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
