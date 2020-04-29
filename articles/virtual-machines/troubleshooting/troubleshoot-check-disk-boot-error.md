---
title: Sprawdzanie systemu plików podczas uruchamiania maszyny wirtualnej platformy Azure | Microsoft Docs
description: Dowiedz się, jak rozwiązać problem polegający na tym, że maszyna wirtualna pokazuje sprawdzanie systemu plików podczas rozruchu | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: 86938c582745cb0759eda9cd0693f407471a0529
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77921491"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>W systemie Windows jest wyświetlany komunikat "sprawdzanie systemu plików" podczas uruchamiania maszyny wirtualnej platformy Azure

W tym artykule opisano błąd "sprawdzanie systemu plików", który może wystąpić podczas uruchamiania maszyny wirtualnej z systemem Windows w Microsoft Azure.


## <a name="symptom"></a>Objaw 

Nie uruchomiono maszyny wirtualnej z systemem Windows. Po sprawdzeniu zrzutów ekranu rozruchowego w ramach [diagnostyki rozruchu](boot-diagnostics.md)zobaczysz, że proces sprawdzania dysku (chkdsk. exe) jest uruchomiony przy użyciu jednego z następujących komunikatów:

- Skanowanie i naprawa dysku (C:)
- Sprawdzanie systemu plików na dysku C:

## <a name="cause"></a>Przyczyna

Jeśli w systemie plików zostanie znaleziony błąd systemu plików NTFS, system Windows sprawdzi i naprawi spójność dysku przy następnym ponownym uruchomieniu. Zwykle zdarza się to, gdy maszyna wirtualna miała jakiekolwiek nieoczekiwane ponowne uruchomienie lub jeśli proces zamykania maszyny wirtualnej został nagle przerwany.

## <a name="solution"></a>Rozwiązanie 

System Windows będzie uruchamiany normalnie po zakończeniu procesu sprawdzania dysku. Jeśli maszyna wirtualna jest zablokowana w procesie sprawdzania dysku, spróbuj uruchomić polecenie Sprawdź dysk w maszynie wirtualnej w trybie offline:
1.  Utwórz migawkę dysku systemu operacyjnego z zaatakowaną maszyną wirtualną jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [migawka dysku](../windows/snapshot-copy-managed-disk.md).
2.  [Dołącz dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania](troubleshoot-recovery-disks-portal-windows.md).  
3.  Na maszynie wirtualnej odzyskiwania Uruchom polecenie Sprawdź dysk na dołączonym dysku systemu operacyjnego. W poniższym przykładzie litera sterownika dołączonego dysku systemu operacyjnego jest w wersji E: 
        
        chkdsk E: /f
4.  Po zakończeniu sprawdzania dysku Odłącz dysk od maszyny wirtualnej odzyskiwania, a następnie ponownie podłącz dysk do maszyny wirtualnej, której to dotyczy, jako dysku systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z maszyną wirtualną z systemem Windows przez dołączenie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania](troubleshoot-recovery-disks-portal-windows.md).
