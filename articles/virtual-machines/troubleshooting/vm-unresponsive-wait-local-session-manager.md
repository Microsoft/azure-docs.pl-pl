---
title: Maszyna wirtualna nie odpowiada podczas oczekiwania na lokalną usługę menedżera sesji
description: Ten artykuł zawiera kroki rozwiązywania problemów, w których system operacyjny gościa zablokuje oczekiwanie na zakończenie przetwarzania przez Menedżera sesji lokalnej podczas uruchamiania maszyny wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/22/2020
ms.author: v-mibufo
ms.openlocfilehash: 8af8d7695c48c6ac682109bb38935e98921fa9e4
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681911"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager-service"></a>Maszyna wirtualna nie odpowiada podczas oczekiwania na lokalną usługę menedżera sesji

W tym artykule przedstawiono kroki rozwiązywania problemów, w których system operacyjny gościa jest zablokowany podczas oczekiwania na zakończenie przetwarzania przez Menedżera sesji lokalnej podczas uruchamiania maszyny wirtualnej platformy Azure.

## <a name="symptoms"></a>Objawy

Gdy używasz [diagnostyki rozruchu](./boot-diagnostics.md) do wyświetlania zrzutu ekranu danych wyjściowych maszyny wirtualnej, zobaczysz, że zrzut ekranu wyświetla monit o komunikat "Czekaj na lokalny Menedżer sesji".

![Zrzut ekranu przedstawiający zablokowany system operacyjny gościa w systemie Windows Server 2012 R2 z komunikatem "Czekaj na lokalny Menedżer sesji".](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>Przyczyna

Może być wiele powodów, aby maszyna wirtualna mogła zawieszać się, aż Menedżer sesji lokalnej. Jeśli ten problem będzie się powtarzać, należy zebrać zrzut pamięci do analizy.

## <a name="solution"></a>Rozwiązanie

W niektórych przypadkach po prostu oczekiwanie na zakończenie procesu program rozwiąże problem. Jeśli maszyna wirtualna nie odpowiada i pozostaje na ekranie oczekiwania przez ponad godzinę, należy zebrać zrzut pamięci, a następnie skontaktować się z pomocą techniczną firmy Microsoft.

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
> [Rozwiązywanie problemów z rozruchem maszyn wirtualnych platformy Azure](boot-error-troubleshoot.md)