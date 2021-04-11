---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: phillipgibson
ms.openlocfilehash: 1c7dbe9bb6cf28f4dee2c4e9aa036410fadbbddf
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080388"
---
## <a name="download-and-install-the-osm-client-binary"></a>Pobierz i zainstaluj dane binarne klienta OSM

W powłoce opartym na bash, użyj `curl` programu, aby pobrać wersję OSM, a następnie wyodrębnij ją w `tar` następujący sposób:

```bash
# Specify the OSM version that will be leveraged throughout these instructions
OSM_VERSION=v0.8.2

curl -sL "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-darwin-amd64.tar.gz" | tar -vxzf -
```

Dane `osm` binarne klienta są uruchamiane na komputerze klienckim i umożliwiają zarządzanie OSM w KLASTRZE AKS. Użyj następujących poleceń, aby zainstalować dane `osm` binarne klienta OSM w powłoce opartym na bash. Te polecenia kopiują dane `osm` binarne klienta do lokalizacji standardowego programu użytkownika w programie `PATH` .

```bash
sudo mv ./darwin-amd64/osm /usr/local/bin/osm
sudo chmod +x /usr/local/bin/osm
```

Można sprawdzić, czy `osm` Biblioteka kliencka została prawidłowo dodana do ścieżki i jej numeru wersji za pomocą następującego polecenia.

```
osm version
```
