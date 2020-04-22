---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 1a023475de1ce2891916807632d9ee15e382326c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81737015"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Pobierz i zainstaluj plik binarny klienta linkerd

W powłoce opartej na programie `Invoke-WebRequest` PowerShell w systemie Windows użyj do pobrania wersji Linkerd w następujący sposób:

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

Plik `linkerd` binarny klienta działa na komputerze klienckim i umożliwia interakcję z siatką usługi Linkerd. Użyj następujących poleceń, aby zainstalować `linkerd` plik binarny klienta Linkerd w powłoce opartej na programie PowerShell w systemie Windows. Polecenia te kopiują `linkerd` plik binarny klienta do folderu Linkerd, a następnie udostępniają go zarówno `PATH`natychmiast (w bieżącej powłoce), jak i na stałe (w całej powłoce restartuje) za pośrednictwem pliku . Do uruchamiania tych poleceń nie trzeba mieć uprawnień z podwyższonym poziomem uprawnień (Administrator) i nie trzeba ponownie uruchamiać powłoki.

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Linkerd\"
```