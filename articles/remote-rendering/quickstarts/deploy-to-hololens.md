---
title: Wdrażanie przykładu środowiska Unity na urządzeniu HoloLens
description: Przewodnik Szybki start pokazujący, jak uruchomić przykład środowiska Unity na urządzeniu HoloLens
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
# <a name="quickstart-deploy-unity-sample-to-hololens"></a>Szybki start: Wdrażanie przykładu środowiska Unity na urządzeniu HoloLens

W tym przewodniku Szybki start przedstawiono sposób wdrażania i uruchamiania przykładowej aplikacji środowiska Unity na urządzeniu HoloLens 2.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
>* Kompilowanie przykładowej aplikacji przewodnika Szybki start dla urządzenia HoloLens
>* Wdrażanie przykładu na urządzeniu
>* Uruchamianie przykładu na urządzeniu

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku Szybki start wdrożymy przykładowy projekt z artykułu [Szybki start: Renderowanie modelu przy użyciu środowiska Unity](render-model.md).

Upewnij się, że Twoje poświadczenia są poprawnie zapisane w scenie i możesz połączyć się z sesją z poziomu edytora Unity.

## <a name="build-the-sample-project"></a>Kompilowanie przykładowego projektu

1. Wybierz pozycję *File -> Build Settings* (Plik -> Ustawienia kompilacji).
1. Zmień wartość ustawienia *Platform* (Platforma) na **Universal Windows Platform** (Platforma uniwersalna systemu Windows)
1. Dla ustawienia *Target Device* (Urządzenie docelowe) wybierz wartość **HoloLens**
1. Dla ustawienia *Architecture* (Architektura) wybierz wartość **ARM64**
1. Dla ustawienia *Build Type* (Typ kompilacji) wybierz wartość **D3D Project** (Projekt D3D)\
    ![Ustawienia kompilacji](./media/unity-build-settings.png)
1. Wybierz pozycję **Switch to Platform** (Przełącz na platformę)
1. Po wybraniu pozycji **Build** (Skompiluj), lub Build And Run (Skompiluj i uruchom), zostanie wyświetlony monit o wybranie folderu, w którym ma być przechowywane rozwiązanie
1. Otwórz wygenerowany plik **Quickstart.sln** w programie Visual Studio
1. W konfiguracji zmień ustawienia na wartość **Wydanie** i **ARM64**
1. Przełącz tryb debugera na wartość **Maszyna zdalna**\
    ![Konfiguracja rozwiązania](media/unity-deploy-config.png)
1. Kompilowanie rozwiązania
1. W przypadku projektu „Quickstart” przejdź do pozycji *Właściwości > Debugowanie*
    1. Upewnij się, że jest aktywna konfiguracja *Wydanie*
    1. Dla ustawienia *Debuger do uruchomienia* wybierz wartość **Maszyna zdalna**
    1. Zmień ustawienie *Nazwa maszyny* na **adres IP urządzenia HoleLens**

## <a name="launch-the-sample-project"></a>Uruchamianie przykładowego projektu

1. Podłącz urządzenie HoloLens do komputera przy użyciu kabla USB.
1. Uruchom debuger w programie Visual Studio (F5). Aplikacja zostanie automatycznie wdrożona na urządzeniu.

Przykładowa aplikacja powinna zostać uruchomiona, a następnie powinna zostać rozpoczęta nowa sesja. Po krótkiej chwili sesja będzie gotowa, a przed Tobą pojawi się zdalnie wyrenderowany model.
Jeśli zechcesz uruchomić ten przykład później, będzie on teraz dostępny w menu Start urządzenia HoloLens.

## <a name="next-steps"></a>Następne kroki

W następnym przewodniku Szybki start zajmiemy się konwertowaniem modelu niestandardowego.

> [!div class="nextstepaction"]
> [Szybki start: Konwertowanie modelu do renderowania](convert-model.md)
