---
title: 'Szybki Start: Tworzenie aplikacji dla systemu iOS'
description: Dowiedz się, jak utworzyć aplikację dla systemu iOS przy użyciu kotwic przestrzennych platformy Azure programowo w postaci SWIFT lub w języku C.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.custom: has-adal-ref
ms.openlocfilehash: 4434118313989eb8bc70f1d44e977b2df3633050
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "96009079"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Szybki Start: Tworzenie aplikacji dla systemu iOS z zakotwiczeniami przestrzennymi platformy Azure w postaci SWIFT lub w celu-C

W tym przewodniku Szybki start opisano, jak utworzyć aplikację dla systemu iOS przy użyciu usługi [Azure Spatial Anchors](../overview.md) w języku Swift lub Objective-C. Azure Spatial Anchors to usługa dla deweloperów programujących dla wielu platform, która pozwala kreować rozwiązania z rzeczywistością mieszaną z użyciem obiektów, których lokalizacja jest taka sama na różnych urządzeniach mimo upływu czasu. Gdy skończysz, będziesz mieć aplikację ARKit dla systemu iOS, która może zapisywać i przywoływać kotwicę przestrzenną.

Omawiane tematy:

> [!div class="checklist"]
> * Tworzenie konta usługi Spatial Anchors
> * Konfigurowanie identyfikatora i klucza konta usługi Spatial Anchors
> * Wdrażanie i uruchamianie na urządzeniu z systemem iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

- Maszyna macOS z włączoną obsługą dewelopera z najnowszą wersją <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> i <a href="https://cocoapods.org" target="_blank">CocoaPods</a> .
- Narzędzie git zainstalowane za pośrednictwem oprogramowania Homebrew:
  1. Wprowadź następujące polecenie jako pojedynczy wiersz w terminalu: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` . 
  1. Uruchom polecenia `brew install git` i `brew install git-lfs`.
  1. Zaktualizuj konfigurację git przy użyciu programu `git lfs install` (dla bieżącego użytkownika) lub `git lfs install --system` (dla całego systemu).
- Deweloperskie urządzenie z systemem iOS, <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">zgodne z zestawem ARKit</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Otwieranie przykładowego projektu

Użyj terminalu, aby wykonać następujące czynności.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Zainstaluj konieczne zasobniki przy użyciu programu CocoaPods:

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Przejdź do adresu `iOS/Swift/`.

```bash
cd ./iOS/Swift/
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

Przejdź do adresu `iOS/Objective-C/`.

```bash
cd ./iOS/Objective-C/
```

---

Uruchom polecenie `pod install --repo-update` w celu zainstalowania programu CocoaPods dla projektu.

Następnie otwórz plik `.xcworkspace` w programie Xcode.

> [!NOTE]
> Zapoznaj się z [krokami](#cocoapods-issues-on-macos-catalina-1015) rozwiązywania problemów, jeśli masz problemy z CocoaPod po uaktualnieniu do macOS Catalina (10,15).

# <a name="swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Konfigurowanie identyfikatora i klucza konta

Następnym krokiem jest skonfigurowanie aplikacji w taki sposób, aby korzystała z identyfikatora konta i klucza konta. Skopiowano je do edytora tekstu podczas [konfigurowania zasobów kotwic przestrzennych](#create-a-spatial-anchors-resource).

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Otwórz plik `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Znajdź pole `spatialAnchorsAccountKey` i zastąp wartość `Set me` kluczem konta.

Znajdź pole `spatialAnchorsAccountId` i zastąp wartość `Set me` identyfikatorem konta.

Znajdź `spatialAnchorsAccountDomain` pole i Zastąp `Set me` je domeną konta.

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

Otwórz plik `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Znajdź pole `SpatialAnchorsAccountKey` i zastąp wartość `Set me` kluczem konta.

Znajdź pole `SpatialAnchorsAccountId` i zastąp wartość `Set me` identyfikatorem konta.

Znajdź `SpatialAnchorsAccountDomain` pole i Zastąp `Set me` je domeną konta.

---

## <a name="deploy-the-app-to-your-ios-device"></a>Wdrażanie aplikacji na urządzeniu z systemem iOS

Podłącz urządzenie z systemem iOS do komputera Mac i ustaw **aktywny schemat** na urządzenie z systemem iOS.

![Wybieranie urządzenia](./media/get-started-ios/select-device.png)

Wybierz pozycję **Build and then run the current scheme (Skompiluj i uruchom bieżący schemat)**.

![Wdrażanie i uruchamianie](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Jeśli widzisz błąd `library not found for -lPods-SampleObjC`, prawdopodobnie został otwarty plik `.xcodeproj` zamiast `.xcworkspace`. Otwórz plik `.xcworkspace` i ponów próbę.

W programie Xcode zatrzymaj aplikację, naciskając pozycję **Stop**.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>CocoaPods problemy w macOS Catalina (10,15)

Jeśli niedawno Zaktualizowano do macOS Catalina (10,15) i CocoaPods zostały wcześniej zainstalowane, CocoaPods może być w stanie przerwania i nie można prawidłowo skonfigurować swoich zasobników i `.xcworkspace` plików projektu. Aby rozwiązać ten problem, należy ponownie zainstalować program CocoaPods, uruchamiając następujące polecenia:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="app-crashes-when-deploying-to-ios-1031-from-a-personal-provisioning-profiledeveloper-account"></a>Awarie aplikacji podczas wdrażania w systemie iOS 10.3.1 z poziomu osobistego profilu aprowizacji/konta dewelopera 

Jeśli aplikacja dla systemu iOS zostanie wdrożona w systemie iOS 10.3.1 z poziomu osobistego profilu aprowizacji/konta dewelopera, może zostać wyświetlony następujący błąd: `Library not loaded: @rpath/ADAL...` . 

W celu rozwiązania tego problemu:

- Użyj profilu aprowizacji, który nie jest osobistym profilem zespołu (płatne konto dewelopera).
- Wdróż aplikację na urządzeniu z systemem iOS z systemem iOS 13,3 lub starszym lub na jednym komputerze z systemem iOS 13,4 beta lub wydaniem wersji.
- Przeczytaj więcej na temat tego problemu w [Stack Overflow](https://stackoverflow.com/questions/60015309/running-ios-apps-causes-runtime-error-for-frameworks-code-signature-invalid).


[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Samouczek: udostępnianie kotwic przestrzennych między urządzeniami](../tutorials/tutorial-share-anchors-across-devices.md)
