---
title: 'Dokumentacja: znane problemy & Rozwiązywanie problemów'
titleSuffix: Azure Data Science Virtual  Machine
description: Zapoznaj się z listą znanych problemów, obejść i rozwiązywania problemów dotyczących usługi Azure Data Science Virtual Machine
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: b83effa95b17d712d4019f8ab5bf13c4f02a7d2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78206524"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Znane problemy i rozwiązywanie problemów z usługą Azure Data Science Virtual Machine

Ten artykuł pomaga znaleźć i poprawić błędy lub błędy, które można napotkać podczas korzystania z usługi Azure Data Science Virtual Machine.

## <a name="python-package-installation-issues"></a>Problemy z instalacją pakietu języka Python

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Instalowanie pakietów z zależnościami przerw w systemie Linux

Użyj `sudo pip install` zamiast `pip install` podczas instalowania pakietów.

## <a name="disk-encryption-issues"></a>Problemy z szyfrowaniem dysków

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Szyfrowanie dysków kończy się niepowodzeniem na Ubuntu DSVM

Azure Disk Encryption (ADE) nie jest obecnie obsługiwany w Ubuntu DSVM. Jako obejście problemu należy rozważyć skonfigurowanie [szyfrowania po stronie serwera dla usługi Azure Managed disks](../../virtual-machines/windows/disk-encryption.md).

## <a name="tool-appears-disabled"></a>Narzędzie jest wyłączone

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Funkcja Hyper-V nie działa w systemie Windows DSVM

Funkcja Hyper-V początkowo nie działa w systemie Windows. W przypadku wydajności rozruchowej wyłączono pewne usługi. Aby włączyć funkcję Hyper-V:

1. Otwórz pasek wyszukiwania w DSVM systemu Windows
1. Wpisz "usługi"
1. Ustaw wszystkie usługi funkcji Hyper-V na "ręczne"
1. Ustaw "Automatyczne" zarządzania maszynami wirtualnymi funkcji Hyper-V

Końcowy ekran powinien wyglądać następująco:

   ![Włączanie funkcji Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

