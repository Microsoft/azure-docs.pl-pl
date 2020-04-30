---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: e26a2c214a03243d6507296c1e981706be8c56db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81734483"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Pobierz i zainstaluj dane binarne klienta Istio istioctl

W przypadku powłoki opartej na programie PowerShell w systemie `Invoke-WebRequest` Windows użyj polecenia, aby pobrać wersję Istio, `Expand-Archive` a następnie wyodrębnij ją w następujący sposób:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.4.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Dane `istioctl` binarne klienta są uruchamiane na komputerze klienckim i umożliwiają współdziałanie z siatką usługi Istio. Użyj następujących poleceń, aby zainstalować dane binarne `istioctl` klienta Istio w powłoce opartej na programie PowerShell w systemie Windows. Te polecenia kopiują `istioctl` dane binarne klienta do folderu Istio, a następnie udostępniają je natychmiast (w bieżącej powłoce) i trwale (w ramach ponownych uruchomień powłoki) za pośrednictwem `PATH`. Nie musisz mieć podniesionych uprawnień (Administrator) do uruchamiania tych poleceń i nie musisz ponownie uruchamiać powłoki.

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