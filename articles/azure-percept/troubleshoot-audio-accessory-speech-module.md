---
title: Rozwiązywanie problemów z usługą Azure Percept audio i modułem mowy
description: Uzyskaj wskazówki dotyczące rozwiązywania problemów z usługą Azure Percept audio i azureearspeechclientmodule
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: f34013bdb14481bfe872a9b3c4234d603bc2d7ec
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102635573"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Rozwiązywanie problemów z modułem audio i mowy na platformie Azure Percept

Skorzystaj z poniższych wskazówek, aby rozwiązywać problemy z aplikacją asystenta głosowego.

## <a name="collecting-speech-module-logs"></a>Zbieranie dzienników modułów mowy

Aby uruchomić te polecenia, [Połącz się z punktem dostępu do usługi Azure Percept Wi-Fi DK i Połącz się z zestawem deweloperskim za pośrednictwem protokołu SSH](./how-to-ssh-into-percept-dk.md) , a następnie wprowadź polecenia w terminalu SSH.

```console
sudo iotedge logs azureearspeechclientmodule
```

Aby przekierować wszystkie dane wyjściowe do pliku. txt w celu dalszej analizy, należy użyć następującej składni:

```console
sudo [command] > [file name].txt
```

Po przekierowaniu danych wyjściowych do pliku txt Skopiuj plik na komputer hosta za pośrednictwem punktu połączenia usługi:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[ścieżka do pliku hosta lokalnego] odwołuje się do lokalizacji na komputerze hosta, do której chcesz skopiować plik txt. [zdalna nazwa użytkownika] to nazwa użytkownika SSH wybrana podczas [korzystania ze środowiska](./quickstart-percept-dk-set-up.md). Jeśli nie skonfigurowano logowania SSH podczas korzystania z platformy Azure Percept DK, zdalna nazwa użytkownika to root.

## <a name="checking-runtime-status-of-the-speech-module"></a>Sprawdzanie stanu środowiska uruchomieniowego modułu mowy

Sprawdź, czy stan środowiska uruchomieniowego **azureearspeechclientmodule** jest wyświetlany jako **uruchomiony**. Aby zlokalizować stan środowiska uruchomieniowego modułów urządzenia, Otwórz [Azure Portal](https://portal.azure.com/) i przejdź do obszaru **wszystkie zasoby**  ->  **\<your IoT hub>**  ->  **IoT Edge**  ->  **\<your device ID>** . Kliknij kartę **moduły** , aby wyświetlić stan środowiska uruchomieniowego wszystkich zainstalowanych modułów.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Strona urządzenia brzegowego w Azure Portal.":::

Jeśli stan środowiska uruchomieniowego **azureearspeechclientmodule** nie jest wymieniony jako **uruchomiony**, kliknij pozycję **Ustaw moduły**  ->  **azureearspeechclientmodule**. Na stronie **Ustawienia modułu** Ustaw **żądany stan** na **uruchomiony** , a następnie kliknij przycisk **Aktualizuj**.

## <a name="understanding-ear-som-led-indicators"></a>Omówienie wskaźników LED na uszach SoM

Możesz użyć wskaźników LED, aby zrozumieć, z jakim stanem jest urządzenie. Zwykle trwa około 2 minut, aż moduł zostanie całkowicie zainicjowany po *włączeniu funkcji włączania*. Ponieważ przechodzi przez kroki inicjowania, zobaczysz:

1. DIODa świetlna 1 centralnego centrum — urządzenie jest włączone.
2. 1 środkowe dioda LED świecą migania — uwierzytelnianie jest w toku.
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
