---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: phillipgibson
ms.openlocfilehash: f99db628b08084ca750816c00a6892e877e90efc
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080372"
---
## <a name="download-and-install-the-osm-client-binary"></a>Pobierz i zainstaluj dane binarne klienta OSM

W bash Shell w systemie Linux lub [Windows podsystem dla systemu Linux][install-wsl]Użyj programu, `curl` Aby pobrać wersję OSM, a następnie wyodrębnij w `tar` następujący sposób:

```bash
# Specify the OSM version that will be leveraged throughout these instructions
OSM_VERSION=v0.8.2

curl -sL "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-linux-amd64.tar.gz" | tar -vxzf -
```

Dane `osm` binarne klienta są uruchamiane na komputerze klienckim i umożliwiają zarządzanie OSM w KLASTRZE AKS. Użyj następujących poleceń, aby zainstalować `osm` plik binarny klienta OSM w powłoce opartym na bash w [podsystemie Linux lub Windows dla systemu Linux][install-wsl]. Te polecenia kopiują dane `osm` binarne klienta do lokalizacji standardowego programu użytkownika w programie `PATH` .

```bash
sudo mv ./linux-amd64/osm /usr/local/bin/osm
sudo chmod +x /usr/local/bin/osm
```

Można sprawdzić, czy `osm` Biblioteka kliencka została prawidłowo dodana do ścieżki i jej numeru wersji za pomocą następującego polecenia.

```
osm version
```

<!-- LINKS - external -->

[install-wsl]: /windows/wsl/install-win10
