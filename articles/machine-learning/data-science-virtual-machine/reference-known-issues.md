---
title: 'Dokumentacja: znane problemy & Rozwiązywanie problemów'
titleSuffix: Azure Data Science Virtual  Machine
description: Zapoznaj się z listą znanych problemów, obejść i rozwiązywania problemów dotyczących usługi Azure Data Science Virtual Machine
services: machine-learning
ms.service: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: bcd75347f91109967ac48427ca0b8855c11b7d9b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656860"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Znane problemy i rozwiązywanie problemów z usługą Azure Data Science Virtual Machine

Ten artykuł pomaga znaleźć i poprawić błędy lub błędy, które można napotkać podczas korzystania z usługi Azure Data Science Virtual Machine.

## <a name="python-package-installation-issues"></a>Problemy z instalacją pakietu języka Python

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Instalowanie pakietów z zależnościami przerw w systemie Linux

Użyj `sudo pip install` zamiast `pip install` podczas instalowania pakietów.

## <a name="disk-encryption-issues"></a>Problemy z szyfrowaniem dysków

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Szyfrowanie dysków kończy się niepowodzeniem na Ubuntu DSVM

Azure Disk Encryption (ADE) nie jest obecnie obsługiwany w Ubuntu DSVM. Jako obejście problemu należy rozważyć skonfigurowanie [szyfrowania po stronie serwera dla usługi Azure Managed disks](../../virtual-machines/disk-encryption.md).

## <a name="tool-appears-disabled"></a>Narzędzie jest wyłączone

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Funkcja Hyper-V nie działa w systemie Windows DSVM

Funkcja Hyper-V początkowo nie działa w systemie Windows. W przypadku wydajności rozruchowej wyłączono pewne usługi. Aby włączyć funkcję Hyper-V:

1. Otwórz pasek wyszukiwania w DSVM systemu Windows
1. Wpisz "usługi"
1. Ustaw wszystkie usługi funkcji Hyper-V na "ręczne"
1. Ustaw "Automatyczne" zarządzania maszynami wirtualnymi funkcji Hyper-V

Końcowy ekran powinien wyglądać następująco:

   ![Włączanie funkcji Hyper-V](./media/workaround/hyperv-enable-dsvm.png)