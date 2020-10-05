---
title: Wdrażanie przykładu środowiska Unity na urządzeniu HoloLens
description: Przewodnik Szybki Start pokazujący, jak uzyskać próbkę Unity na urządzenie HoloLens
author: jakrams
ms.author: jakras
ms.date: 02/14/2020
ms.topic: quickstart
ms.openlocfilehash: 3eec935d0a25f9510cd9a2f6e00b7ac22756e697
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88796803"
---
# <a name="quickstart-deploy-unity-sample-to-hololens"></a>Szybki Start: wdrażanie przykładu aparatu Unity w usłudze HoloLens

Ten przewodnik Szybki Start przedstawia sposób wdrażania i uruchamiania przykładowej aplikacji do szybkiego startu dla aparatu Unity 2.

W tym przewodniku szybki start dowiesz się, jak:

> [!div class="checklist"]
>
>* Tworzenie przykładowej aplikacji szybkiego startu dla urządzeń HoloLens
>* Wdróż przykład na urządzeniu
>* Uruchamianie przykładu na urządzeniu

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku szybki start wykonamy wdrożenie przykładowego projektu z poziomu [przewodnika Szybki Start: renderowanie modelu z użyciem aparatu Unity](render-model.md).

Upewnij się, że poświadczenia są poprawnie zapisane w scenie i możesz połączyć się z sesją z poziomu edytora aparatu Unity.

## <a name="build-the-sample-project"></a>Kompilowanie przykładowego projektu

1. Otwórz *plik > ustawienia kompilacji*.
1. Zmień *platformę* na **platforma uniwersalna systemu Windows**
1. Ustawianie *urządzenia docelowego na urządzenie* **HoloLens**
1. Ustaw *architekturę* na **arm64**
1. Ustaw *typ kompilacji* na **projekt D3D**\
    ![Ustawienia kompilacji](./media/unity-build-settings.png)
1. Wybierz pozycję **Przełącz na platformę**
1. Podczas naciskania **kompilacji** (lub kompilowania i uruchamiania) zostanie wyświetlony monit o wybranie folderu, w którym ma być przechowywane rozwiązanie
1. Otwórz wygenerowanego **przewodnika Szybki Start** dla programu Visual Studio
1. Zmień konfigurację na **Release** i **arm64**
1. Przełącz tryb debugera na **maszynę zdalną**\
    ![Konfiguracja rozwiązania](media/unity-deploy-config.png)
1. Kompilowanie rozwiązania
1. Dla projektu "Szybki Start" przejdź do *właściwości > debugowanie*
    1. Upewnij się, że *wersja* konfiguracji jest aktywna
    1. Ustaw *debuger do uruchomienia* na **maszynie zdalnej**
    1. Zmień *nazwę maszyny* na **adres IP HoleLens**

## <a name="launch-the-sample-project"></a>Uruchamianie przykładowego projektu

1. Podłącz urządzenie HoloLens przy użyciu kabla USB do komputera.
1. Uruchom Debuger w programie Visual Studio (F5). Aplikacja zostanie automatycznie wdrożona na urządzeniu.

Przykładowa aplikacja powinna zostać uruchomiona, a następnie uruchomiona nowa sesja. Po czasie sesja jest gotowa, a zdalnie renderowany model pojawi się przed ty.
Jeśli chcesz uruchomić próbkę później, możesz również znaleźć ją z menu Start usługi HoloLens teraz.

## <a name="next-steps"></a>Następne kroki

W następnym przewodniku szybki start Przyjrzyjmy się konwertowaniu modelu niestandardowego.

> [!div class="nextstepaction"]
> [Szybki Start: konwertowanie modelu do renderowania](convert-model.md)
