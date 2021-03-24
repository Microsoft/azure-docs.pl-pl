---
title: Informacje o urządzeniu audio usługi Azure Percept
description: Dowiedz się więcej o usłudze Azure Percept audio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: 7c80159607947dbe7ed17181beac610fa0c202c5
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104956098"
---
# <a name="introduction-to-azure-percept-audio"></a>Wprowadzenie do usługi Azure Percept audio

Azure Percept audio to urządzenie do akcesoriów, które dodaje funkcje mowy AI do [usługi Azure PERCEPT DK](./overview-azure-percept-dk.md). Zawiera wstępnie skonfigurowany procesor audio i tablicę liniową z czterema mikrofonami, która umożliwia korzystanie z poleceń głosowych, słów kluczowych wykrywania trendów i daleko pól z pomocą Cognitive Services platformy Azure. Jest ona zintegrowana z platformą Azure Percept DK, [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)i innymi usługami Azure Edge Management. Usługa Azure Percept audio jest dostępna do zakupu w [Sklepie Microsoft Online](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

> [!div class="nextstepaction"]
> [Kup teraz](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

</br>

> [!VIDEO https://www.youtube.com/embed/Qj8NGn-7s5A]

## <a name="azure-percept-audio-components"></a>Składniki audio usługi Azure Percept

Usługa Azure Percept audio zawiera następujące główne składniki:

- Gotowe do produkcji urządzenie audio Percept (SoM) na platformie Azure z czterema mikrofonami i przetwarzaniem audio za pośrednictwem kodera XMOS
- Tablica deweloperów (międzyetapowa): 2. przyciski, 3. diody LED, Micro USB i 3,5 mm gniazda audio
- Wymagane kable: kabel FPC, USB Micro Type-B do USB-A
- Karta powitalna
- Płyta montażowa mechaniczna z integracją instalacji z serii 80/20 1010

## <a name="compute-capabilities"></a>Możliwości obliczeniowe 

Usługa Azure Percept audio przekazuje dane wejściowe audio przez stos mowy, który działa na PROCESORze na platformie Azure Percept DK Board w trybie hybrydowej chmury. W związku z tym, usługa Azure Percept Audio wymaga tablicy nośnej z systemem operacyjnym, który obsługuje stos mowy, aby można było go wykonać. 

Przetwarzanie audio odbywa się w następujący sposób: 

- Azure Percept audio: przechwytuje i konwertuje dźwięk, a następnie wysyła je do gniazda DK i audio.

- Azure Percept DK: stos mowy wykonuje anulowanie tworzenia i ECHA, a następnie przetwarza przychodzące audio, aby zoptymalizować mowę. Po przetworzeniu wykonuje słowo kluczowe wykrywania trendów.

- Chmura: przetwarza polecenia i frazy języka naturalnego, weryfikację słów kluczowych i przeszkolenie. 

- Offline: Jeśli urządzenie jest w trybie offline, wykryje słowo kluczowe i przechwytuje dane telemetryczne stanu połączenia internetowego. Zwiększoną fałszywą stawkę za zaakceptowanie słowa kluczowego wykrywania trendów można zaobserwować, ponieważ nie można wykonać weryfikacji słowa kluczowego w chmurze. 

## <a name="getting-started"></a>Wprowadzenie

- [Złóż platformę Azure Percept DK](./quickstart-percept-dk-unboxing.md)
- [Łączenie urządzenia audio usługi Azure Percept z Devkit](./quickstart-percept-audio-setup.md)
- [Ukończ działanie Instalatora platformy Azure Percept DK](./quickstart-percept-dk-set-up.md)

## <a name="build-a-no-code-prototype"></a>Kompilowanie prototypu bez kodu

Twórz [rozwiązanie do obsługi mowy bez kodu](./tutorial-no-code-speech.md) w [usłudze Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) przy użyciu szablonów asystenta głosowego usługi Azure Percept dla scenariuszy Hotelarstwo, opieki zdrowotnej, spisu i motoryzacyjnych.

### <a name="manage-your-no-code-speech-solution"></a>Zarządzanie rozwiązaniem mowy bez kodu

- [Konfigurowanie asystenta głosowego w usłudze Azure Percept Studio](./how-to-manage-voice-assistant.md)
- [Konfigurowanie asystenta głosowego w usłudze IoT Hub](./how-to-configure-voice-assistant.md)
- [Rozwiązywanie problemów z usługą Azure Percept audio](./troubleshoot-audio-accessory-speech-module.md)

## <a name="additional-technical-information"></a>Dodatkowe informacje techniczne

- [Arkusz danych audio usługi Azure Percept](./azure-percept-audio-datasheet.md)
- [Zachowanie przycisku i diody LED](./audio-button-led-behavior.md)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Kupowanie urządzenia audio usługi Azure Percept w Sklepie Microsoft Online](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
