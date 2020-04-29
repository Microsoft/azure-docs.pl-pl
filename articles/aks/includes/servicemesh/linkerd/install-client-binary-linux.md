---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 7a22256c88f9cee3ce62c68a2de4a5974d76f026
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77593736"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Pobieranie i Instalowanie konsolidatora dwuskładnikowego klienta konsolidatora

W przypadku powłoki opartej na bash w systemie Linux lub [Windows podsystem dla systemu Linux][install-wsl]Użyj `curl` programu, aby pobrać program z konsolidatorem w następujący sposób:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

Dane `linkerd` binarne klienta są uruchamiane na komputerze klienckim i umożliwiają współdziałanie z przyutworzoną siatką usług. Użyj następujących poleceń, aby zainstalować konsolidator binarny `linkerd` klienta w bash Shell w systemie Linux lub [Windows dla systemu Linux][install-wsl]. Te polecenia kopiują `linkerd` dane binarne klienta do lokalizacji standardowego programu użytkownika w programie `PATH`.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Jeśli chcesz, aby wiersz polecenia został zaawansowany dla konsolidatora binarnego `linkerd` klienta, skonfiguruj go w następujący sposób:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10