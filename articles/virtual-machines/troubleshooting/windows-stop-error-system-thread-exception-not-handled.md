---
title: Błąd zatrzymania systemu Windows — wyjątek wątku systemowego 0x0000007E nie został obsłużony
description: Ten artykuł zawiera kroki rozwiązywania problemów, w których system operacyjny gościa napotyka problem i chce ponownie uruchomić maszynę wirtualną platformy Azure. Komunikat będzie mieć stan "wyjątek wątku systemowego nie został obsłużony".
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/04/2020
ms.author: v-mibufo
ms.openlocfilehash: beb658474e49f9b47900d8481dab0424dae8c5be
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661395"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Błąd zatrzymania systemu Windows — wyjątek wątku systemowego 0x0000007E nie został obsłużony

Ten artykuł zawiera kroki rozwiązywania problemów, w których system operacyjny gościa napotyka problem, i próbuje ponownie uruchomić maszynę wirtualną platformy Azure. Wyświetlany jest komunikat o błędzie "wyjątek wątku systemowego nie został obsłużony".

## <a name="symptoms"></a>Objawy

W przypadku korzystania z [diagnostyki rozruchu](./boot-diagnostics.md) w celu wyświetlenia zrzutu ekranu danych wyjściowych maszyny wirtualnej zobaczysz, że system Windows musi zostać ponownie uruchomiony przy użyciu kodu zatrzymania "wyjątek wątku systemowego" lub "0x0000007E".

![Zrzut ekranu przedstawiający system Windows zablokowany podczas rozruchu z "komputerem napotkał problem i musi zostać uruchomiony ponownie. Zostanie ponownie uruchomiony. " komunikat o błędzie i "wyjątek wątku SYSTEMowego nie został OBSŁUŻONy".](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>Przyczyna

Nie można określić przyczyny, dopóki nie zostanie przeanalizowany plik zrzutu pamięci. Kontynuuj zbieranie pliku zrzutu pamięci.

## <a name="solution"></a>Rozwiązanie

> [!TIP]
> Jeśli masz najnowszą kopię zapasową maszyny wirtualnej, możesz spróbować [przywrócić maszynę wirtualną z kopii zapasowej](../../backup/backup-azure-arm-restore-vms.md) , aby rozwiązać problem z rozruchem.


Aby rozwiązać ten problem, należy najpierw zebrać plik zrzutu pamięci dla awarii, a następnie wysłać plik do pomocy technicznej firmy Microsoft. Aby zebrać plik zrzutu, postępuj zgodnie z instrukcjami podanymi w dwóch następnych sekcjach.

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Dołącz dysk systemu operacyjnego do nowej maszyny wirtualnej naprawy

1. Aby przygotować maszynę wirtualną naprawy, wykonaj kroki 1-3 [poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md).
1. Nawiąż połączenie z maszyną wirtualną naprawy przy użyciu Podłączanie pulpitu zdalnego.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Lokalizowanie pliku zrzutu i przesyłanie biletu pomocy technicznej

1. Na stronie Naprawa maszyny wirtualnej przejdź do folderu systemu Windows na dołączonym dysku systemu operacyjnego. Na przykład, jeśli litera dysku przypisana do dołączonego dysku systemu operacyjnego jest oznaczona jako *F*, przejdź do `F:\Windows` .
1. Wyszukaj plik *Memory. dmp* , a następnie [Prześlij bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) z dołączonym plikiem zrzutu pamięci.
1. Jeśli masz problemy z lokalizowaniem pliku *Memory. dmp* , postępuj zgodnie z przewodnikiem, aby [wygenerować plik zrzutu awaryjnego przy użyciu wywołań przerwań, które nie są maskowane (NMI)](/windows/client-management/generate-kernel-or-complete-crash-dump).

Aby uzyskać więcej informacji o wywołaniach NMI, zobacz [NMIe wywołania w podręczniku użytkownika konsoli szeregowej platformy Azure](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) .

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rozwiązywanie problemów z rozruchem maszyn wirtualnych platformy Azure](./boot-error-troubleshoot.md)
