---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: 785008633318ee6670170d66cba96bd5853f9ed9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188469"
---
## <a name="install-speech-sdk"></a>Instalowanie zestawu Speech SDK

Zestawu Speech SDK dla systemu Linux można używać do kompilowania aplikacji 64-bitowych i 32-bitowych. Wymagane biblioteki i pliki nagłówkowe można pobrać jako plik tar ze strony https://aka.ms/csspeech/linuxbinary.

Pobierz i zainstaluj zestaw SDK w następujący sposób:

1. Wybierz katalog, do którego pliki zestawów Speech SDK powinny zostać wyodrębnione, i ustaw zmienną środowiskową `SPEECHSDK_ROOT`, aby wskazywała na ten katalog. Ta zmienna ułatwia odwoływanie się do katalogu w przyszłych poleceniach. Jeśli na przykład chcesz używać katalogu `speechsdk` w katalogu macierzystym, użyj polecenia podobnego do poniższego:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Utwórz katalog, jeśli jeszcze nie istnieje.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Pobierz i wyodrębnij archiwum `.tar.gz` zawierające pliki binarne zestawu Speech SDK:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Zweryfikuj zawartość katalogu najwyższego poziomu wyodrębnionego pakietu:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Wykaz katalogów powinien zawierać pliki licencji i powiadomienia innych firm, a także katalog `include` zawierający pliki nagłówkowe (`.h`) i katalog `lib` zawierający biblioteki.

   [!INCLUDE [Linux Binary Archive Content](~/includes/cognitive-services-speech-service-linuxbinary-content.md)]