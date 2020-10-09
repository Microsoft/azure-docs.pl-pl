---
title: Rozwiązywanie problemów z błędami rozruchu na platformie Azure Linux Virtual Machines | Microsoft Docs
description: Ten artykuł pomaga połączyć się z artykułami w celu rozwiązywania problemów z błędami rozruchowymi w usłudze Azure Linux Virtual Machines.
services: virtual-machines-linux
documentationCenter: ''
author: vilibert
manager: spogge
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2019
ms.author: vilibert
ms.openlocfilehash: 37cb201751f72918838efe5837aa0e357d483f24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74408744"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>Rozwiązywanie problemów z błędami rozruchu usługi Azure Linux Virtual Machines

W tym artykule wymieniono typowe błędy rozruchowe, które mogą pojawić się podczas uruchamiania maszyny wirtualnej z systemem Linux w Microsoft Azure. Aby uzyskać więcej informacji o błędach, zobacz artykuły w sekcji **błędy i rozwiązania rozruchowe** .

## <a name="boot-errors-and-solutions"></a>Błędy i rozwiązania rozruchowe

* [GRUB](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>Następne kroki

- [Konsola szeregowa VM](serial-console-linux.md)

Rozwiązywanie problemów z maszyną wirtualną z systemem Linux przez dołączenie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania przy użyciu platformy Azure:

- [Naprawa maszyny wirtualnej platformy Azure](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 Wymiana dysków — można to zrobić automatycznie przy użyciu dowolnego z tych funkcji:
- [Skrypty odzyskiwania dla powłoki PowerShell](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [Skrypty odzyskiwania bash](https://github.com/sribs/azure-support-scripts)

- [Interfejs wiersza polecenia](troubleshoot-recovery-disks-linux.md)
- [Azure Portal](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>Wideo z wymianą dysku:

Jeśli nie masz dostępu do GRUB Obejrzyj [ten](https://youtu.be/m5t0GZ5oGAc) film wideo i zobacz, jak można łatwo zautomatyzować procedurę wymiany dysku w celu odzyskania maszyny wirtualnej

## <a name="unofficial-solution"></a>Nieoficjalne rozwiązanie

Można również spróbować odzyskać maszynę wirtualną za pomocą nieobsługiwanego skryptu BETA [ALAR](https://github.com/malachma/azure-auto-recover)