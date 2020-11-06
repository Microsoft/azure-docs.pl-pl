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
ms.openlocfilehash: dd18d69009b9c150c4c12a755e9060cd5dfaccae
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424228"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Błąd zatrzymania systemu Windows — wyjątek wątku systemowego 0x0000007E nie został obsłużony

Ten artykuł zawiera kroki rozwiązywania problemów, w których system operacyjny gościa napotyka problem i chce ponownie uruchomić maszynę wirtualną platformy Azure. Komunikat będzie mieć stan "wyjątek wątku systemowego nie został obsłużony".

## <a name="symptoms"></a>Objawy

W przypadku korzystania z [diagnostyki rozruchu](./boot-diagnostics.md) w celu wyświetlenia zrzutu ekranu maszyny wirtualnej zobaczysz, że zrzut ekranu przedstawia ponowne uruchomienie systemu Windows, gdy **wyjątek wątku SYSTEMowego zatrzymania nie jest obsługiwany** lub kod błędu **0x0000007E**.

![Zrzut ekranu przedstawia system Windows zablokowany podczas rozruchu z komunikatem: "komputer napotkał problem i wymaga ponownego uruchomienia. Zostanie ponownie uruchomiony. " Kod zatrzymania: "wyjątek wątku SYSTEMowego nie jest obsługiwany"](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>Przyczyna

Nie można określić przyczyny, dopóki nie zostanie przeanalizowany plik zrzutu pamięci. Kontynuuj zbieranie pliku zrzutu pamięci.

## <a name="solution"></a>Rozwiązanie

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
> [Rozwiązywanie problemów z rozruchem maszyn wirtualnych platformy Azure](./boot-error-troubleshoot.md)