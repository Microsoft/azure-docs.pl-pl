---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 8/27/2020
ms.author: rgarcia
ms.openlocfilehash: 50e4799f09322eab05b4f8ddf7004c2e0078fdab
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971493"
---
## <a name="android"></a>[Android](#tab/Android)

Przykład Java dla systemu Android obsługuje udostępnianie między urządzeniami.

W Android Studio Otwórz plik *Shared. Java* w folderze Samples. 

Wprowadź adres URL skopiowany w poprzednim kroku (z wdrożenia aplikacji sieci Web ASP.NET Azure) jako wartość dla `SharingAnchorsServiceUrl` w pliku *Shared. Java* . 

Zastąp `index.html` adres URL adresem `api/anchors` . Powinien wyglądać następująco: `https://<app_name>.azurewebsites.net/api/anchors` .

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

Przykładowy cel-C dla systemu iOS obsługuje udostępnianie między urządzeniami.

Otwórz plik *SharedDemoViewController. m* w folderze Samples. 

Wprowadź adres URL uzyskany w poprzednim kroku (z wdrożenia aplikacji sieci Web ASP.NET Azure) jako wartość elementu `SharingAnchorsServiceUrl` w pliku *SharedDemoViewController. m* . 

Zastąp `index.html` adres URL adresem `api/anchors` . Powinien wyglądać następująco: `https://<app_name>.azurewebsites.net/api/anchors` .

Wdróż aplikację na urządzeniu. 

Po uruchomieniu aplikacji wybierz opcję naciśnij, **Aby rozpocząć udostępnianie udostępnione** , a następnie postępuj zgodnie z instrukcjami w aplikacji. Możesz wybrać pozycję **naciśnij, aby zlokalizować kotwicę według numeru zakotwiczenia** lub **nacisnąć pozycję, aby utworzyć kotwicę i zapisać ją w usłudze**.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Przykłady platformy Xamarin Android i iOS obsługują udostępnianie na różnych urządzeniach.

Otwórz plik *AccountDetails.cs* w folderze Samples. 

Wprowadź adres URL uzyskany w poprzednim kroku (z wdrożenia aplikacji sieci Web ASP.NET Azure) jako wartość elementu `AnchorSharingServiceUrl` w pliku *AccountDetails.cs* . 

Zastąp `index.html` adres URL adresem `api/anchors` . Powinien wyglądać następująco: `https://<app_name>.azurewebsites.net/api/anchors` .

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Konfigurowanie urządzenia z systemem Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Konfigurowanie urządzenia z systemem iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

W okienku **projekt** przejdź do `Assets\AzureSpatialAnchors.Examples\Resources` . 

Wybierz pozycję **SpatialAnchorSamplesConfig**. Następnie w okienku **Inspektor** wprowadź `Sharing Anchors Service` adres URL (z wdrożenia aplikacji sieci Web ASP.NET Azure) jako wartość dla `Base Sharing Url` . Zastąp element `index.html` pytaniem `api/anchors`. Powinien wyglądać następująco: `https://<app_name>.azurewebsites.net/api/anchors` .

Zapisz scenę, wybierając pozycję **plik**  >  **Zapisz**.

## <a name="deploy-to-your-device"></a>Wdrażanie na urządzeniu

### <a name="deploy-to-an-android-device"></a>Wdrażanie na urządzeniu z systemem Android

Zaloguj się na urządzeniu z systemem Android i połącz je z komputerem przy użyciu kabla USB.

Otwórz obszar **Build Settings (Ustawienia kompilacji)**, wybierając pozycje **File (Plik)** > **Build Settings (Ustawienia kompilacji)**.

W obszarze **sceny w kompilacji**upewnij się, że obok każdej sceny znajduje się znacznik wyboru.

Upewnij się, że **projekt eksportu** nie ma znacznika wyboru. Wybierz opcję **Kompiluj i uruchom**. Zostanie wyświetlony monit o zapisanie pliku *. apk* . Możesz wybrać dla niego dowolną nazwę.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Wdrażanie na urządzeniu z systemem iOS

Otwórz obszar **Build Settings (Ustawienia kompilacji)**, wybierając pozycje **File (Plik)** > **Build Settings (Ustawienia kompilacji)**.

W obszarze **sceny w kompilacji**upewnij się, że obok każdej sceny znajduje się znacznik wyboru.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

W programie Xcode Zatrzymaj aplikację, wybierając pozycję **Zatrzymaj**.
