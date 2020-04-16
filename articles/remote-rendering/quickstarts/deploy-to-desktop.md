---
title: Wdrażanie przykładu środowiska Unity na komputerze stacjonarnym
description: Przewodnik Szybki start, który pokazuje, jak pobrać przykład Unity na komputer stacjonarny
author: christophermanthei
ms.author: chmant
ms.date: 03/20/2020
ms.topic: quickstart
ms.openlocfilehash: 95993a54d321c9581d35f12b56d60e4ea052e505
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415718"
---
# <a name="quickstart-deploy-unity-sample-to-desktop"></a>Szybki start: wdrażanie próbki Unity na pulpicie

Ten przewodnik Szybki start obejmuje sposób wdrażania i uruchamiania przykładowej aplikacji szybki start dla unity na komputerze stacjonarnym.

W tym przewodniku Szybki start dowiesz się, jak:

> [!div class="checklist"]
>
>* Tworzenie przykładowej aplikacji szybki start dla komputerów stacjonarnych
>* Wdrażanie próbki na komputerze
>* Uruchamianie próbki na komputerze

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku Szybki start wdrożymy przykładowy projekt z [programu Szybki start: Renderowanie modelu z unity](render-model.md).

Upewnij się, że poświadczenia są poprawnie zapisane ze sceną i można połączyć się z sesją z poziomu edytora Unity.

## <a name="disable-virtual-reality-support"></a>Wyłącz obsługę wirtualnej rzeczywistości

Tylko płaskie aplikacje klasyczne są obecnie obsługiwane na pulpicie, więc obsługa VR musi być wyłączona.

1. Otwórz *pozycję Edytuj > ustawienia projektu...*
1. Wybierz **opcję Gracz** po lewej stronie.
1. Wybierz kartę **Ustawienia uniwersalnej platformy systemu Windows.**
1. Rozwiń **ustawienia XR**.
1. Wyłącz **obsługiwana wirtualna rzeczywistość**.
    ![ustawienia odtwarzacza](./media/unity-disable-xr.png)
1. Powyżej *ustawień XR*rozwiń ustawienia **publikowania**.
1. W **obsługiwanych rodzinach urządzeń**upewnij się, że **komputer stacjonarny** jest zaznaczony.

## <a name="build-the-sample-project"></a>Tworzenie przykładowego projektu

1. Otwórz *ustawienia kompilacji > pliku*.
1. Zmień *platformę* na **uniwersalną platformę systemu Windows**.
1. Ustaw *urządzenie docelowe* na **PC**.
1. Ustaw *architekturę* na **x86**.
1. Ustaw *typ kompilacji* na **D3D Project**.
  ![Ustawienia kompilacji](./media/unity-build-settings-pc.png)
1. Wybierz **pozycję Przełącz na platformę**.
1. Po **naciśnięciu przycisku Build** (lub "Build And Run") zostaniesz poproszony o wybranie folderu, w którym rozwiązanie powinno być przechowywane.
1. Otwórz wygenerowany plik **Szybki start.sln** w programie Visual Studio.
1. Zmień konfigurację na **Release** i **x86**.
1. Przełącz tryb debugera na **komputer lokalny**.
  ![Konfiguracja rozwiązania](./media/unity-deploy-config-pc.png)
1. Zbuduj rozwiązanie (F7).

> [!WARNING]
> Upewnij się, że wybierz **x86**. **x64** nie jest obecnie obsługiwany, patrz [ograniczenia platformy](../reference/limits.md#platform-limitations).

## <a name="launch-the-sample-project"></a>Uruchamianie przykładowego projektu

Uruchom debugera w programie Visual Studio (F5). Zostanie ona automatycznie wdrożona aplikacji na komputerze.

Przykładowa aplikacja powinna zostać uruchomiona, a następnie rozpocząć nową sesję. Po pewnym czasie sesja jest gotowa, a zdalnie renderowany model pojawi się przed tobą.
Jeśli chcesz uruchomić próbkę po raz drugi później, możesz również znaleźć ją w menu Start teraz.

## <a name="next-steps"></a>Następne kroki

W następnym przewodniku Szybki start przyjrzymy się konwersji modelu niestandardowego.

> [!div class="nextstepaction"]
> [Szybki start: konwertowanie modelu do renderowania](convert-model.md)
