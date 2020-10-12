---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: d303def85d66d5c2039b426e48186f90c2b3fff6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86277753"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Pobieranie i Instalowanie konsolidatora dwuskładnikowego klienta konsolidatora

W przypadku powłoki opartej na bash w systemie Linux lub [Windows podsystem dla systemu Linux][install-wsl]Użyj `curl` programu, aby pobrać program z konsolidatorem w następujący sposób:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

Dane `linkerd` binarne klienta są uruchamiane na komputerze klienckim i umożliwiają współdziałanie z przyutworzoną siatką usług. Użyj następujących poleceń, aby zainstalować konsolidator `linkerd` binarny klienta w bash Shell w systemie Linux lub [Windows dla systemu Linux][install-wsl]. Te polecenia kopiują dane `linkerd` binarne klienta do lokalizacji standardowego programu użytkownika w programie `PATH` .

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Jeśli chcesz, aby wiersz polecenia został zaawansowany dla konsolidatora `linkerd` binarnego klienta, skonfiguruj go w następujący sposób:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: /windows/wsl/install-win10
