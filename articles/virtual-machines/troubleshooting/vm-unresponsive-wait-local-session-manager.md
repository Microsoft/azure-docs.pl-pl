---
title: Maszyna wirtualna nie odpowiada podczas oczekiwania na lokalny Menedżer sesji
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
ms.openlocfilehash: ae3d35bea1c75c797447100316ceab9a27fcbe48
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424236"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager"></a>Maszyna wirtualna nie odpowiada podczas oczekiwania na lokalny Menedżer sesji

Ten artykuł zawiera kroki rozwiązywania problemów, w których system operacyjny gościa zablokuje oczekiwanie na zakończenie przetwarzania przez Menedżera sesji lokalnej podczas uruchamiania maszyny wirtualnej platformy Azure.

## <a name="symptoms"></a>Objawy

W przypadku korzystania z [diagnostyki rozruchu](./boot-diagnostics.md) do wyświetlania zrzutu ekranu maszyny wirtualnej zobaczysz, że zrzut ekranu wyświetli monit z komunikatem: " *czekaj na lokalny Menedżer sesji* "

![Zrzut ekranu przedstawia system operacyjny gościa zablokowany z komunikatem "Czekaj na Menedżera sesji lokalnej" w systemie Windows Server 2012 R2.](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>Przyczyna

Może istnieć wiele przyczyn, dla których maszyna wirtualna oczekuje na zablokowanie przez Menedżera sesji lokalnej. Jeśli oczekiwanie na problem z menedżerem sesji lokalnej jest trwały, należy zebrać zrzut pamięci do analizy.

## <a name="solution"></a>Rozwiązanie

W niektórych przypadkach po prostu czekaj na ukończenie procesu, który zakończy proces. Jeśli maszyna wirtualna jest zawieszona na ekranie oczekiwania przez ponad godzinę, należy zebrać zrzut pamięci, a następnie skontaktować się z pomocą techniczną firmy Microsoft.

### <a name="collect-the-memory-dump-file"></a>Zbierz plik zrzutu pamięci

Aby rozwiązać ten problem, należy najpierw zebrać plik zrzutu pamięci dla awarii, a następnie skontaktować się z pomocą techniczną przy użyciu pliku zrzutu pamięci. Aby zebrać plik zrzutu, wykonaj następujące kroki:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Dołącz dysk systemu operacyjnego do nowej maszyny wirtualnej naprawy

1. Wykonaj [kroki 1-3 poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) , aby przygotować maszynę wirtualną naprawy.
2. Nawiąż połączenie z maszyną wirtualną naprawy przy użyciu Podłączanie pulpitu zdalnego.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Lokalizowanie pliku zrzutu i przesyłanie biletu pomocy technicznej

1. Na stronie Naprawa maszyny wirtualnej przejdź do folderu systemu Windows na dołączonym dysku systemu operacyjnego. Jeśli litera sterownika przypisana do dołączonego dysku systemu operacyjnego jest oznaczona jako *F* , należy przejść do `F:\Windows` .
2. Zlokalizuj plik **Memory. dmp** , a następnie [Prześlij bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) przy użyciu pliku zrzutu pamięci.
3. Jeśli masz problemy z lokalizowaniem pliku **Memory. dmp** , postępuj zgodnie z przewodnikiem, aby [wygenerować plik zrzutu awaryjnego przy użyciu wywołań przerwań, które nie są maskowane (NMI)](/windows/client-management/generate-kernel-or-complete-crash-dump).

Aby uzyskać więcej informacji o wywołaniach NMI, zobacz [wywołania przerwań niemaskowanych (NMI) w podręczniku użytkownika konsoli szeregowej](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) .

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rozwiązywanie problemów z rozruchem maszyn wirtualnych platformy Azure](boot-error-troubleshoot.md)