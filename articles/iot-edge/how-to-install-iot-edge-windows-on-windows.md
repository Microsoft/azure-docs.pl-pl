---
title: Instalowanie Azure IoT Edge dla systemu Windows | Microsoft Docs
description: Instalowanie Azure IoT Edge kontenerów systemu Windows na urządzeniach z systemem Windows
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: kgremban
ms.openlocfilehash: c24389a1957f9e0cfb23e3bb5b8604c34e57a915
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102609519"
---
# <a name="install-and-manage-azure-iot-edge-with-windows-containers"></a>Instalowanie Azure IoT Edge z kontenerami systemu Windows i zarządzanie nimi

Środowisko uruchomieniowe Azure IoT Edge to włączenie urządzenia do urządzenia IoT Edge. Po skonfigurowaniu urządzenia przy użyciu środowiska uruchomieniowego usługi IoT Edge można rozpocząć wdrażanie na nim logiki biznesowej z chmury. Aby dowiedzieć się więcej, zobacz [Omówienie środowiska uruchomieniowego Azure IoT Edge i jego architektury](iot-edge-runtime.md).

Aby skonfigurować urządzenie IoT Edge, należy wykonać dwie czynności. Pierwszym krokiem jest zainstalowanie środowiska uruchomieniowego i jego zależności. Drugim krokiem jest połączenie urządzenia ze swoją tożsamością w chmurze i skonfigurowanie uwierzytelniania przy użyciu IoT Hub.

W tym artykule przedstawiono procedurę instalowania środowiska uruchomieniowego Azure IoT Edge z kontenerami systemu Windows. Jeśli zamierzasz używać kontenerów systemu Linux na urządzeniu z systemem Windows, zapoznaj się z artykułem [Azure IoT Edge dla systemu Linux w systemie Windows](how-to-install-iot-edge-on-windows.md) .

>[!NOTE]
>Azure IoT Edge z kontenerami systemu Windows nie będą obsługiwane począwszy od wersji 1,2 Azure IoT Edge.
>
>Rozważ użycie nowej metody uruchamiania IoT Edge na urządzeniach z systemem Windows [Azure IoT Edge dla systemu Linux w systemie Windows](iot-edge-for-linux-on-windows.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Urządzenie z systemem Windows

  IoT Edge z kontenerami systemu Windows wymaga systemu Windows w wersji 1809/kompilacja 17763, który jest najnowszą [kompilacją długoterminowej pomocy technicznej systemu Windows](/windows/release-information/). Należy zapoznać się z [listą obsługiwanych systemów](support.md#operating-systems) , aby uzyskać listę obsługiwanych jednostek SKU.

* [Zarejestrowany identyfikator urządzenia](how-to-register-device.md)

  Jeśli urządzenie zostało zarejestrowane z uwierzytelnianiem przy użyciu klucza symetrycznego, przygotuj parametry połączenia urządzenia.

  Jeśli urządzenie zostało zarejestrowane z uwierzytelnianiem za pomocą certyfikatu X. 509 z podpisem własnym, mają co najmniej jeden certyfikat tożsamości, który został użyty do zarejestrowania urządzenia i jego zgodnego prywatnego klucza prywatnego dostępnego na urządzeniu.

## <a name="install-a-container-engine"></a>Instalowanie aparatu kontenera

Azure IoT Edge polega na tym, że środowisko uruchomieniowe kontenera zgodnego ze sterownikiem OCI, takie jak [Moby](https://github.com/moby/moby). Aparat oparty na Moby, który jest zawarty w skrypcie instalacji. Nie ma dodatkowych kroków, które należy wykonać, aby zainstalować aparat.

## <a name="install-the-iot-edge-security-daemon"></a>Instalowanie demona zabezpieczeń IoT Edge

1. Uruchom program PowerShell jako administrator.

   Użyj sesji AMD64 programu PowerShell, a nie programu PowerShell (x86). Jeśli nie masz pewności, który typ sesji jest używany, uruchom następujące polecenie:

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Uruchom polecenie [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) , które wykonuje następujące zadania:

   * Sprawdza, czy komputer z systemem Windows jest w obsługiwanej wersji.
   * Włącza funkcję Containers.
   * Pobiera aparat Moby i środowisko uruchomieniowe IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

3. Jeśli zostanie wyświetlony monit, uruchom ponownie urządzenie.

Instalując IoT Edge na urządzeniu, można użyć dodatkowych parametrów, aby zmodyfikować proces, w tym:

* Bezpośredni ruch do przechodzenia przez serwer proxy
* Wskaż Instalatorowi lokalny katalog instalacji w trybie offline.

Aby uzyskać więcej informacji o tych dodatkowych parametrach, zobacz [skrypty programu PowerShell dla IoT Edge z kontenerami systemu Windows](reference-windows-scripts.md).

## <a name="provision-the-device-with-its-cloud-identity"></a>Inicjowanie obsługi administracyjnej urządzenia przy użyciu tożsamości w chmurze

Teraz, gdy aparat kontenera i środowisko uruchomieniowe IoT Edge są zainstalowane na urządzeniu, możesz przejść do następnego kroku, czyli skonfigurować urządzenie przy użyciu tożsamości i informacji o uwierzytelnianiu w chmurze.

Wybierz następną sekcję w zależności od typu uwierzytelniania, którego chcesz użyć:

* [Opcja 1. uwierzytelnianie przy użyciu kluczy symetrycznych](#option-1-authenticate-with-symmetric-keys)
* [Opcja 2: uwierzytelnianie za pomocą certyfikatów X. 509](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>Opcja 1. uwierzytelnianie przy użyciu kluczy symetrycznych

W tym momencie środowisko uruchomieniowe IoT Edge jest zainstalowane na urządzeniu z systemem Windows i należy udostępnić je swoje informacje o tożsamości i uwierzytelnianiu w chmurze.

W tej sekcji omówiono procedurę aprowizacji urządzenia z uwierzytelnianiem przy użyciu klucza symetrycznego. Urządzenie powinno zostać zarejestrowane w IoT Hub i pobrane parametry połączenia z informacji o urządzeniu. W przeciwnym razie wykonaj czynności opisane w temacie [Rejestrowanie urządzenia IoT Edge w IoT Hub](how-to-register-device.md).

1. Na urządzeniu IoT Edge Uruchom program PowerShell jako administrator.

2. Użyj polecenia [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) , aby skonfigurować środowisko uruchomieniowe IoT Edge na komputerze. Polecenie domyślnie umożliwia ręczne Inicjowanie obsługi przy użyciu kontenerów systemu Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Jeśli pobrano skrypt IoTEdgeSecurityDaemon.ps1 na urządzenie w celu instalacji w trybie offline lub określonej wersji, należy się upewnić, aby odwołać się do lokalnej kopii skryptu.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Po wyświetleniu monitu podaj parametry połączenia urządzenia pobrane w poprzedniej sekcji. Parametry połączenia urządzenia kojarzą urządzenie fizyczne z IDENTYFIKATORem urządzenia w IoT Hub i udostępniają informacje o uwierzytelnianiu.

   Parametry połączenia urządzenia mają następujący format i nie powinny zawierać znaków cudzysłowu: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

W przypadku ręcznego aprowizacji urządzenia można użyć dodatkowych parametrów, aby zmodyfikować proces, w tym:

* Bezpośredni ruch do przechodzenia przez serwer proxy
* Zadeklaruj określony obraz kontenera edgeAgent i podaj poświadczenia, jeśli znajduje się on w rejestrze prywatnym

Aby uzyskać więcej informacji o tych dodatkowych parametrach, zobacz [skrypty programu PowerShell dla IoT Edge z kontenerami systemu Windows](reference-windows-scripts.md).

### <a name="option-2-authenticate-with-x509-certificates"></a>Opcja 2: uwierzytelnianie za pomocą certyfikatów X. 509

W tym momencie środowisko uruchomieniowe IoT Edge jest zainstalowane na urządzeniu z systemem Windows i należy udostępnić je swoje informacje o tożsamości i uwierzytelnianiu w chmurze.

W tej sekcji omówiono procedurę aprowizacji urządzenia za pomocą uwierzytelniania certyfikatu X. 509. Urządzenie powinno zostać zarejestrowane w IoT Hub, co zapewnia odciski palców zgodne z certyfikatem i kluczem prywatnym znajdującym się na urządzeniu IoT Edge. W przeciwnym razie wykonaj czynności opisane w temacie [Rejestrowanie urządzenia IoT Edge w IoT Hub](how-to-register-device.md).

1. Na urządzeniu IoT Edge Uruchom program PowerShell jako administrator.

2. Użyj polecenia [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) , aby skonfigurować środowisko uruchomieniowe IoT Edge na komputerze.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Jeśli pobrano skrypt IoTEdgeSecurityDaemon.ps1 na urządzenie w celu instalacji w trybie offline lub określonej wersji, należy się upewnić, aby odwołać się do lokalnej kopii skryptu.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Po wyświetleniu monitu podaj następujące informacje:

   * **IotHubHostName**: Nazwa hosta Centrum IoT, z którym zostanie nawiązane połączenie. Na przykład `{IoT hub name}.azure-devices.net`.
   * **DeviceID**: identyfikator podany podczas rejestrowania urządzenia.
   * **X509IdentityCertificate**: ścieżka bezwzględna do certyfikatu tożsamości na urządzeniu. Na przykład `C:\path\identity_certificate.pem`.
   * **X509IdentityPrivateKey**: ścieżka bezwzględna do pliku klucza prywatnego dla podanego certyfikatu tożsamości. Na przykład `C:\path\identity_key.pem`.

W przypadku ręcznego aprowizacji urządzenia można użyć dodatkowych parametrów, aby zmodyfikować proces, w tym:

* Bezpośredni ruch do przechodzenia przez serwer proxy
* Zadeklaruj określony obraz kontenera edgeAgent i podaj poświadczenia, jeśli znajduje się on w rejestrze prywatnym

Aby uzyskać więcej informacji o tych dodatkowych parametrach, zobacz [skrypty programu PowerShell dla IoT Edge z kontenerami systemu Windows](reference-windows-scripts.md).

## <a name="offline-or-specific-version-installation-optional"></a>Instalacja w trybie offline lub określona wersja (opcjonalnie)

Kroki opisane w tej sekcji dotyczą scenariuszy nieuwzględnionych w standardowych krokach instalacji. Może to obejmować:

* Zainstaluj IoT Edge w trybie offline
* Zainstaluj wersję Release Candidate
* Zainstaluj wersję inną niż Najnowsza

Podczas instalacji są pobierane trzy pliki:

* Skrypt programu PowerShell, który zawiera instrukcje dotyczące instalacji
* Microsoft Azure IoT Edge CAB, który zawiera demo IoT Edge Security daemon (iotedged), aparat kontenera Moby i interfejs wiersza polecenia Moby
* Instalator pakietu redystrybucyjnego programu Visual C++ (środowisko uruchomieniowe VC)

Jeśli urządzenie będzie w trybie offline w trakcie instalacji lub jeśli chcesz zainstalować określoną wersję IoT Edge, możesz pobrać te pliki przed czasem do urządzenia. Po zakończeniu instalacji wskaż skrypt instalacji znajdujący się w katalogu zawierającym pobrane pliki. Instalator najpierw sprawdza ten katalog, a następnie pobiera tylko składniki, które nie zostały odnalezione. Jeśli wszystkie pliki są dostępne w trybie offline, możesz zainstalować program bez połączenia z Internetem.

1. Najnowsze pliki instalacyjne IoT Edge wraz z poprzednimi wersjami znajdują się w artykule [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases).

2. Znajdź wersję, którą chcesz zainstalować, i pobierz następujące pliki z sekcji **Assets** informacji o wersji na urządzeniu IoT:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab z kanału wydania 1,1.

   Ważne jest, aby użyć skryptu programu PowerShell z tej samej wersji co używany plik cab, ponieważ funkcje zmieniają się w celu obsługi funkcji w każdej wersji.

3. Jeśli pobrany plik cab ma sufiks architektury, Zmień nazwę pliku na tylko **Microsoft-Azure-IoTEdge.cab**.

4. Opcjonalnie można pobrać Instalatora dla Visual C++ pakiet redystrybucyjny. Na przykład skrypt programu PowerShell używa tej wersji: [vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Zapisz Instalatora w tym samym folderze na urządzeniu IoT jako pliki IoT Edge.

5. Aby zainstalować program za pomocą składników trybu [offline, należy](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) uzyskać kopię lokalną skryptu programu PowerShell.

6. Uruchom polecenie [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) z `-OfflineInstallationPath` parametrem. Podaj ścieżkę bezwzględną do katalogu plików. Na przykład

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   Polecenie wdrożenia użyje wszystkich składników znalezionych w lokalnym katalogu plików. Jeśli brakuje pliku cab lub Instalatora Visual C++, spróbuje je pobrać.

## <a name="update-iot-edge"></a>IoT Edge aktualizacji

Użyj `Update-IoTEdge` polecenia, aby zaktualizować demona zabezpieczeń. Skrypt automatycznie pobiera najnowszą wersję demona zabezpieczeń.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge
```

Uruchomienie Update-IoTEdge polecenie powoduje usunięcie i zaktualizowanie demona zabezpieczeń z urządzenia wraz z dwoma obrazami kontenera środowiska uruchomieniowego. Plik config. YAML jest przechowywany na urządzeniu, a także dane z aparatu kontenera Moby. Przechowywanie informacji o konfiguracji oznacza, że nie trzeba podawać parametrów połączenia ani informacji o usłudze Device Provisioning dla urządzenia ponownie w trakcie procesu aktualizacji.

Jeśli chcesz zaktualizować do określonej wersji demona zabezpieczeń, Znajdź wersję z kanału wydania 1,1, dla którego chcesz określić [wersje IoT Edge](https://github.com/Azure/azure-iotedge/releases). W tej wersji Pobierz plik **Microsoft-Azure-IoTEdge.cab** . Następnie użyj parametru, `-OfflineInstallationPath` Aby wskazać lokalizację pliku lokalnego. Na przykład:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>`-OfflineInstallationPath`Parametr szuka pliku o nazwie **Microsoft-Azure-IoTEdge.cab** w podanym katalogu. Zmień nazwę pliku, aby usunąć sufiks architektury, jeśli go zawiera.

Jeśli chcesz zaktualizować urządzenie w trybie offline, Znajdź wersję, dla której chcesz określić wersje [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases). W tej wersji Pobierz pliki *IoTEdgeSecurityDaemon.ps1* i *Microsoft-Azure-IoTEdge.cab* . Ważne jest, aby użyć skryptu programu PowerShell z tej samej wersji co używany plik cab, ponieważ funkcje zmieniają się w celu obsługi funkcji w każdej wersji.

Jeśli pobrany plik cab ma sufiks architektury, Zmień nazwę pliku na tylko **Microsoft-Azure-IoTEdge.cab**.

Aby zaktualizować program za pomocą składników trybu [offline, należy](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) uzyskać kopię lokalną skryptu programu PowerShell. Następnie użyj `-OfflineInstallationPath` parametru jako części `Update-IoTEdge` polecenia i podaj ścieżkę bezwzględną do katalogu plików. Na przykład

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

Aby uzyskać więcej informacji na temat opcji aktualizacji, użyj polecenia `Get-Help Update-IoTEdge -full` lub zapoznaj się z [skryptami programu PowerShell w celu IoT Edge z kontenerami systemu Windows](reference-windows-scripts.md).

## <a name="uninstall-iot-edge"></a>Odinstaluj IoT Edge

Jeśli chcesz usunąć instalację IoT Edge z urządzenia, użyj następujących poleceń.

Jeśli chcesz usunąć instalację IoT Edge z urządzenia z systemem Windows, użyj polecenia [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) w oknie administracyjnym programu PowerShell. To polecenie usuwa środowisko uruchomieniowe IoT Edge wraz z istniejącą konfiguracją i danymi aparatu Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Aby uzyskać więcej informacji o opcjach odinstalowywania, użyj polecenia `Get-Help Uninstall-IoTEdge -full` .

## <a name="next-steps"></a>Następne kroki

Kontynuuj [wdrażanie modułów IoT Edge](how-to-deploy-modules-portal.md) , aby dowiedzieć się, jak wdrażać moduły na urządzeniu.
