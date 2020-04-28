---
title: Instalowanie interfejsu wiersza polecenia DC/OS | Microsoft Docs
description: Instalowanie interfejsu wiersza polecenia DC/OS.
services: container-service
documentationcenter: ''
author: rgardler
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Kontenery, mikrousługi, DC/OS, Azure
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/10/2016
ms.author: rogardle
ms.openlocfilehash: e2601d5200f0b8ebcfb856bffb871f01b3acb215
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67183879"
---
> [!NOTE]
> Te informacje dotyczą pracy z klastrami usługi ACS opartymi na kontrolerach domeny/systemach operacyjnych. Nie trzeba wykonywać tych czynności w przypadku klastrów usługi ACS opartych na aplikacji Swarm.
> 
> 

Najpierw [nawiąż połączenie z klastrem usługi ACS opartym na kontrolerze domeny/systemie operacyjnym](../articles/container-service/container-service-connect.md). Następnie możesz zainstalować interfejs wiersza polecenia DC/OS na komputerze klienckim przy użyciu poniższych poleceń:

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

Jeśli używasz starszej wersji środowiska Python, mogą zostać wyświetlone ostrzeżenia „InsecurePlatformWarnings”. Możesz je bezpiecznie zignorować.

Aby rozpocząć pracę bez ponownego uruchamiania powłoki, uruchom polecenie:

```bash
source ~/.bashrc
```

Ta czynność nie będzie konieczna w przypadku uruchomienia nowych powłok.

Teraz można potwierdzić, że interfejs wiersza polecenia został zainstalowany:

```bash
dcos --help
```