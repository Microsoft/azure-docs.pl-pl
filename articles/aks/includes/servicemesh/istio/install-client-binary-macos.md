---
author: paulbouwer
ms.topic: include
ms.date: 10/02/2020
ms.author: pabouwer
ms.openlocfilehash: 5dc9686e4a9994a085cc9f4a4631e66b05d7949d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666727"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Pobierz i zainstaluj dane binarne klienta Istio istioctl

W bash Shell na macOS, użyj `curl` do pobrania wersji Istio, a następnie wyodrębnij w `tar` następujący sposób:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.7.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istioctl-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

Dane `istioctl` binarne klienta są uruchamiane na komputerze klienckim i umożliwiają zainstalowanie Istio i zarządzanie nim w KLASTRZE AKS. Użyj następujących poleceń, aby zainstalować dane `istioctl` binarne klienta Istio w powłoce opartym na bash na macOS. Te polecenia kopiują dane `istioctl` binarne klienta do lokalizacji standardowego programu użytkownika w programie `PATH` .

```bash
sudo mv ./istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Jeśli chcesz, aby dane binarne klienta Istio były uzupełniane `istioctl` , skonfiguruj je w następujący sposób:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```