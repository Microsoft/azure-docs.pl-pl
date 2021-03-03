---
title: Rozwiązywanie problemów z modułami audio i Speech usługi Azure Percept
description: Uzyskaj porady dotyczące rozwiązywania problemów w przypadku niektórych typowych problemów występujących podczas korzystania z platformy
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 1e2ad920afb55066f07430568f976154f6d7cae1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679632"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Rozwiązywanie problemów z modułem audio i mowy na platformie Azure Percept

Skorzystaj z poniższych wskazówek, aby rozwiązywać problemy z aplikacją asystenta głosowego.

## <a name="collecting-speech-module-logs"></a>Zbieranie dzienników modułów mowy

Aby uruchomić te polecenia, [Połącz się z punktem dostępu do usługi Azure Percept Wi-Fi DK i Połącz się z zestawem deweloperskim za pośrednictwem protokołu SSH](./how-to-ssh-into-percept-dk.md) , a następnie wprowadź polecenia w terminalu SSH.

```console
 iotedge logs azureearspeechclientmodule
```

Aby przekierować wszystkie dane wyjściowe do pliku. txt w celu dalszej analizy, należy użyć następującej składni:

```console
[command] > [file name].txt
```

Po przekierowaniu danych wyjściowych do pliku txt Skopiuj plik na komputer hosta za pośrednictwem punktu połączenia usługi:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[ścieżka do pliku hosta lokalnego] odwołuje się do lokalizacji na komputerze hosta, do której chcesz skopiować plik txt. [nazwa użytkownika zdalnego] to nazwa użytkownika SSH wybrana podczas [instalacji](./quickstart-percept-dk-set-up.md). Jeśli nie skonfigurowano logowania SSH w trakcie OOBE, zdalna nazwa użytkownika jest katalogiem głównym.

## <a name="checking-runtime-status-of-the-speech-module"></a>Sprawdzanie stanu środowiska uruchomieniowego modułu mowy

Sprawdź, czy stan środowiska uruchomieniowego **azureearspeechclientmodule** jest wyświetlany jako **uruchomiony**. Aby zlokalizować stan środowiska uruchomieniowego modułów urządzenia, Otwórz [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) i przejdź do obszaru **wszystkie zasoby**  ->  **\<your IoT hub>**  ->  **IoT Edge**  ->  **\<your device ID>** . Kliknij kartę **moduły** , aby wyświetlić stan środowiska uruchomieniowego wszystkich zainstalowanych modułów.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Strona urządzenia brzegowego w Azure Portal.":::

Jeśli stan środowiska uruchomieniowego **azureearspeechclientmodule** nie jest wymieniony jako **uruchomiony**, kliknij pozycję **Ustaw moduły**  ->  **azureearspeechclientmodule**. Na stronie **Ustawienia modułu** Ustaw **żądany stan** na **uruchomiony** , a następnie kliknij przycisk **Aktualizuj**.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/firmware-desired-status-stopped.png" alt-text="Ustawianie ekranu modułów w Azure Portal.":::

## <a name="understanding-ear-som-led-indicators"></a>Omówienie wskaźników LED na uszach SoM

Możesz użyć wskaźników LED, aby zrozumieć, z jakim stanem jest urządzenie. Zwykle trwa około 2 minut, aż moduł zostanie całkowicie zainicjowany po *włączeniu funkcji włączania*. Ponieważ przechodzi przez kroki inicjowania, zobaczysz:

1. 1 lewy zielony sygnalizator — urządzenie jest włączone. 
2. 1 lewe zielone światło i centralny dioda LED migania zielonego — uwierzytelnianie jest w toku. 
3. Wszystkie trzy diody LED zmienią się na niebieski, gdy urządzenie zostanie uwierzytelnione i będzie gotowe do użycia.

|Stan diody LED                  |Wyczyść stan modelu SoM            |
|----------------------------|---------------------------|
|1x zielony (lewy dioda LED)         |Włącz |
|1x zielony (lewy dioda LED) <br> ismigocze — kolor zielony (centralny DIODa) |trwa uwierzytelnianie |
|3. wyłączone                      |Zakończono inicjowanie |
|3. niebieskie                     |gotowe do użycia |
|3. miganie niebieski            |rozpoznane słowo kluczowe |
|niebieskie wyścigi wyścigowe              |rozpatrywan |
|3. czerwone                      |głos |

## <a name="next-steps"></a>Następne kroki

Zobacz [ogólny przewodnik rozwiązywania problemów](./troubleshoot-dev-kit.md) , aby uzyskać więcej informacji na temat rozwiązywania problemów z platformą Azure Percept DK.