---
title: Wdrażanie przykładu środowiska Unity na komputerze stacjonarnym
description: Przewodnik Szybki Start pokazujący, jak uzyskać przykład aparatu Unity na komputerze stacjonarnym
author: christophermanthei
ms.author: chmant
ms.date: 03/20/2020
ms.topic: quickstart
ms.openlocfilehash: 26531e80292a53400671ac3507b47371c1fbbcad
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88796820"
---
# <a name="quickstart-deploy-unity-sample-to-desktop"></a>Szybki Start: wdrażanie przykładu aparatu Unity na pulpicie

Ten przewodnik Szybki Start przedstawia sposób wdrażania i uruchamiania przykładowej aplikacji szybkiego startu dla aparatu Unity na komputerze stacjonarnym.

W tym przewodniku szybki start dowiesz się, jak:

> [!div class="checklist"]
>
>* Tworzenie przykładowej aplikacji szybkiego startu dla komputerów stacjonarnych
>* Wdróż przykład na komputerze
>* Uruchamianie przykładu na komputerze

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku szybki start wykonamy wdrożenie przykładowego projektu z poziomu [przewodnika Szybki Start: renderowanie modelu z użyciem aparatu Unity](render-model.md).

Upewnij się, że poświadczenia są poprawnie zapisane w scenie i możesz połączyć się z sesją z poziomu edytora aparatu Unity.

## <a name="disable-virtual-reality-support"></a>Wyłącz obsługę wirtualnej rzeczywistości

Tylko proste aplikacje klasyczne są obecnie obsługiwane na komputerze stacjonarnym, więc nie trzeba wyłączać obsługi.

1. Otwórz *Edytowanie ustawień projektu >...*
1. Wybierz **odtwarzacz** po lewej stronie.
1. Wybierz kartę **ustawienia platforma uniwersalna systemu Windows** .
1. Rozwiń **Ustawienia XR**.
1. Wyłączono funkcję **wirtualnej rzeczywistości obsługiwanej**. \
    ![ustawienia odtwarzacza](./media/unity-disable-xr.png)
1. W obszarze *Ustawienia XR*rozwiń pozycję **Ustawienia publikowania**.
1. Upewnij się, że w obszarze **obsługiwane rodziny urządzeń**jest zaznaczone pole wyboru **pulpit** .

## <a name="build-the-sample-project"></a>Kompilowanie przykładowego projektu

1. Otwórz *plik > ustawienia kompilacji*.
1. Zmień *platformę* na **platforma uniwersalna systemu Windows** (**autonomiczny komputer** jest również obsługiwany, ale nie jest tutaj używany, zobacz [ograniczenia dotyczące platformy](../reference/limits.md#platform-limitations)).
1. Ustaw *urządzenie docelowe* na **komputer**.
1. Ustaw *architekturę* na **x86**.
1. Ustaw *typ kompilacji* na **projekt D3D**. \
  ![Ustawienia kompilacji](./media/unity-build-settings-pc.png)
1. Wybierz pozycję **Przełącz na platformę**.
1. Podczas naciskania **kompilacji** (lub kompilowania i uruchamiania) zostanie wyświetlony monit o wybranie folderu, w którym ma być przechowywane rozwiązanie.
1. Otwórz wygenerowanego **przewodnika Szybki Start** dla programu Visual Studio.
1. Zmień konfigurację na **wydanie** i **x86**.
1. Przełącz tryb debugera na **komputer lokalny**. \
  ![Konfiguracja rozwiązania](./media/unity-deploy-config-pc.png)
1. Skompiluj rozwiązanie.

## <a name="launch-the-sample-project"></a>Uruchamianie przykładowego projektu

Uruchom Debuger w programie Visual Studio (F5). Aplikacja zostanie automatycznie wdrożona na komputerze.

Przykładowa aplikacja powinna zostać uruchomiona, a następnie uruchomiona nowa sesja. Po czasie sesja jest gotowa, a zdalnie renderowany model pojawi się przed ty.
Jeśli chcesz uruchomić próbkę później, możesz ją również znaleźć z menu Start.

## <a name="next-steps"></a>Następne kroki

W następnym przewodniku szybki start Przyjrzyjmy się konwertowaniu modelu niestandardowego.

> [!div class="nextstepaction"]
> [Szybki Start: konwertowanie modelu do renderowania](convert-model.md)
