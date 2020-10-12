---
title: Przerwano proces systemowy stanu błędu zatrzymania systemu Windows
description: W tym artykule przedstawiono procedurę rozwiązywania problemów, gdy system operacyjny napotyka błąd zatrzymania 0xC000021A, co uniemożliwia rozruch maszyny wirtualnej platformy Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 037d0858-4611-4707-bd46-cc8085d011ed
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: v-mibufo
ms.openlocfilehash: b07033f96402edc24edd51de57661603e57472bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91347766"
---
# <a name="windows-stop-error---0xc000021a-status-system-process-terminated"></a>Błąd zatrzymania systemu Windows — Przerwano proces systemowy stanu 0xC000021A

Ten artykuł zawiera kroki rozwiązywania problemów, w których system operacyjny (OS) napotyka błąd zatrzymania 0xC000021A, który utrzymuje rozruch maszyny wirtualnej platformy Azure.

## <a name="symptom"></a>Objaw

W przypadku korzystania z [diagnostyki rozruchu](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) w celu wyświetlenia zrzutu ekranu maszyny wirtualnej zrzut ekranu wyświetla komunikat informujący, że system operacyjny napotkał błąd podczas rozruchu, z następującym komunikatem:

**Komputer napotkał problem i wymaga ponownego uruchomienia. Właśnie zbieramy pewne informacje o błędzie, a następnie można uruchomić ponownie. (ukończono # #%) Jeśli chcesz dowiedzieć się więcej, możesz przeszukać w trybie online w późniejszym czasie dla tego błędu: 0xC000021a**.

  ![Rysunek 1 przedstawia kod błędu #0xC000021A z komunikatem "komputer uruchomił się i wymaga ponownego uruchomienia. Właśnie zbieramy pewne informacje o błędzie, a następnie można uruchomić ponownie.](./media/windows-stop-error-system-process-terminated/1-pc-problem-restart.png)

## <a name="cause"></a>Przyczyna

0xC000021A błędu oznacza **STATUS_SYSTEM_PROCESS_TERMINATED**.

Ten błąd występuje, gdy proces krytyczny, taki jak WinLogon (winlogon.exe) lub podsystem Run-Time serwera klienta (csrss.exe) zakończy się niepowodzeniem. Gdy jądro wykryje, że jedna z tych usług została zatrzymana, zgłasza błąd **zatrzymania 0xC000021A** . Ten błąd może mieć kilka przyczyn, w tym:

- Zainstalowano niezgodne pliki systemowe.
- Instalacja aktualizacji dodatku Service Pack lub KB nie powiodła się.
- Program kopii zapasowej używany do przywracania dysku twardego nie przywraca prawidłowo plików, które mogły być używane.
- Zainstalowano niezgodny program innej firmy.

## <a name="solution"></a>Rozwiązanie

### <a name="collect-the-memory-dump-file"></a>Zbierz plik zrzutu pamięci

Aby rozwiązać ten problem, konieczne będzie przeanalizowanie zrzutu awaryjnego. Zbierz plik zrzutu pamięci dla pomocy technicznej dotyczącej awarii i kontaktu. Aby zebrać plik zrzutu, wykonaj następujące kroki:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Dołącz dysk systemu operacyjnego do nowej maszyny wirtualnej naprawy

1.  Wykonaj kroki 1-3 [poleceń naprawy maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) , aby przygotować maszynę wirtualną naprawy.
2.  Korzystając z **Podłączanie pulpitu zdalnego**, Połącz się z maszyną wirtualną naprawy.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Lokalizowanie pliku zrzutu i przesyłanie biletu pomocy technicznej

1.  Na stronie napraw maszynę wirtualną przejdź do folderu systemu Windows na dołączonym dysku systemu operacyjnego. Jeśli litera sterownika przypisana do dołączonego dysku systemu operacyjnego to F, przejdź do F:\Windows.
2.  Zlokalizuj plik Memory. dmp, a następnie [Prześlij bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) przy użyciu pliku zrzutu pamięci.
3.  Jeśli masz problemy z lokalizowaniem pliku Memory. dmp, możesz zamiast tego użyć [wywołań przerwań, które nie są maskowane (NMI) w konsoli szeregowej](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) . Możesz postępować zgodnie z przewodnikiem [generowania pliku zrzutu awaryjnego za pomocą wywołań NMI dostępnym w tym miejscu](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać dalsze informacje dotyczące rozwiązywania problemów, zobacz [Rozwiązywanie typowych błędów rozruchu](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-error-troubleshoot) lub [Rozwiązywanie problemów z maszyną wirtualną z systemem Windows przez dołączenie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-windows). Należy również zapoznać się z [tematem jak używać diagnostyki rozruchu do rozwiązywania problemów z maszyną wirtualną](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics).
- Aby uzyskać więcej informacji o korzystaniu z Menedżer zasobów, zobacz [Azure Resource Manager omówienie](https://docs.microsoft.com/azure/azure-resource-manager/management/overview).
- Jeśli nie możesz nawiązać połączenia z maszyną wirtualną, zobacz [Rozwiązywanie problemów z połączeniami RDP z maszyną wirtualną platformy Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection).
