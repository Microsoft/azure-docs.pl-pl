---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: e26a2c214a03243d6507296c1e981706be8c56db
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81734483"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Pobierz i zainstaluj binarny klienta Istio istioctl

W powłoce opartej na programie `Invoke-WebRequest` PowerShell w systemie Windows `Expand-Archive` użyj do pobrania wersji Istio, a następnie wyodrębnij z następującymi czynnościami:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.4.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Plik `istioctl` binarny klienta działa na komputerze klienckim i umożliwia interakcję z siatką usługi Istio. Użyj następujących poleceń, aby zainstalować `istioctl` plik binarny klienta Istio w powłoce opartej na programie PowerShell w systemie Windows. Polecenia te kopiują `istioctl` plik binarny klienta do folderu Istio, a następnie udostępniają go zarówno `PATH`natychmiast (w bieżącej powłoce), jak i na stałe (w całej powłoce restartuje) za pośrednictwem pliku . Do uruchamiania tych poleceń nie trzeba mieć uprawnień z podwyższonym poziomem uprawnień (Administrator) i nie trzeba ponownie uruchamiać powłoki.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Istio\"
```