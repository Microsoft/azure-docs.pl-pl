---
title: Rozwiązywanie problemów z usługą Azure Percept audio i modułem mowy
description: Uzyskaj wskazówki dotyczące rozwiązywania problemów z usługą Azure Percept audio i azureearspeechclientmodule
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: c4fc7d7564ecd30326fbec832639b2a81d55e6d5
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605658"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Rozwiązywanie problemów z modułem audio i mowy na platformie Azure Percept

Skorzystaj z poniższych wskazówek, aby rozwiązywać problemy z aplikacją asystenta głosowego.

## <a name="collecting-speech-module-logs"></a>Zbieranie dzienników modułów mowy

Aby uruchomić te polecenia, Użyj protokołu [SSH do zestawu deweloperów](./how-to-ssh-into-percept-dk.md) i wprowadź polecenia do monitu klienta SSH.

Zbierz dzienniki modułów mowy:

```console
sudo iotedge logs azureearspeechclientmodule
```

Aby przekierować dane wyjściowe do pliku txt w celu dalszej analizy, należy użyć następującej składni:

```console
sudo [command] > [file name].txt
```

Zmień uprawnienia pliku txt, aby można było go skopiować:

```console
sudo chmod 666 [file name].txt
```

Po przekierowaniu danych wyjściowych do pliku txt Skopiuj plik na komputer hosta za pośrednictwem punktu połączenia usługi:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[ścieżka do pliku hosta lokalnego] odwołuje się do lokalizacji na komputerze hosta, do której chcesz skopiować plik txt. [nazwa użytkownika zdalnego] to nazwa użytkownika SSH wybrana podczas [instalacji](./quickstart-percept-dk-set-up.md).

## <a name="checking-runtime-status-of-the-speech-module"></a>Sprawdzanie stanu środowiska uruchomieniowego modułu mowy

Sprawdź, czy stan środowiska uruchomieniowego **azureearspeechclientmodule** jest wyświetlany jako **uruchomiony**. Aby zlokalizować stan środowiska uruchomieniowego modułów urządzenia, Otwórz [Azure Portal](https://portal.azure.com/) i przejdź do obszaru **wszystkie zasoby**  ->  **[Twoje Centrum IoT Hub]**  ->  **IoT Edge**  ->  **[identyfikator urządzenia]**. Kliknij kartę **moduły** , aby wyświetlić stan środowiska uruchomieniowego wszystkich zainstalowanych modułów.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Strona urządzenia brzegowego w Azure Portal.":::

Jeśli stan środowiska uruchomieniowego **azureearspeechclientmodule** nie jest wymieniony jako **uruchomiony**, kliknij pozycję **Ustaw moduły**  ->  **azureearspeechclientmodule**. Na stronie **Ustawienia modułu** Ustaw **żądany stan** na **uruchomiony** , a następnie kliknij przycisk **Aktualizuj**.

## <a name="understanding-ear-som-led-indicators"></a>Omówienie wskaźników LED na uszach SoM

Możesz użyć wskaźników LED, aby zrozumieć, z jakim stanem jest urządzenie. Zwykle trwa około 2 minut, aż moduł zostanie całkowicie zainicjowany po przypisaniu urządzenia na urządzeniu. Po wykonaniu kroków inicjalizacji zobaczysz:

1. Wyśrodkuj biały na (statyczny): urządzenie jest włączone.
2. Wyśrodkuj biały dioda LED (miganie): uwierzytelnianie jest w toku.
3. Wszystkie trzy diody LED zmienią się na niebieski, gdy urządzenie zostanie uwierzytelnione i będzie gotowe do użycia.

|BRANŻ|Stan diody LED|Wyczyść stan modelu SoM|
|---|---------|--------------|
|L02|1x biały, statyczny na|Włącz |
|L02|1x biały, 0,5 Hz miga|Trwa uwierzytelnianie |
|L01 & L02 & L03|3. niebieskie, static on|Oczekiwanie na słowo kluczowe|
|L01 & L02 & L03|Dioda LED miga, 20fps |Nasłuchiwanie lub mówienie|
|L01 & L02 & L03|Wyścigi tablicowe diod LED, 20fps|Myśleć|
|L01 & L02 & L03|3. czerwony, statyczny na |Głos|

## <a name="next-steps"></a>Następne kroki

Zobacz [ogólny przewodnik rozwiązywania problemów](./troubleshoot-dev-kit.md) , aby uzyskać więcej informacji na temat rozwiązywania problemów z platformą Azure Percept DK.
