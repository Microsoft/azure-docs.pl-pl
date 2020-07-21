---
title: Rozruch maszyny wirtualnej z systemem Linux w usłudze grub
description: Nie można uruchomić maszyny wirtualnej, ponieważ maszyna wirtualna została wprowadzona w konsoli ratowniczej
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/28/2019
ms.author: tiag
ms.openlocfilehash: 7774ea5138e6d77547e386531e573457cd6d2862
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86525946"
---
# <a name="linux-vm-boots-to-grub-rescue"></a>Rozruch maszyny wirtualnej z systemem Linux w usłudze grub

Określono, że maszyna wirtualna (VM) została dodana do konsoli ratowniczej. Ten problem występuje, gdy maszyna wirtualna z systemem Linux ma ostatnio zastosowane zmiany jądra, takie jak uaktualnienie jądra, i nie jest już prawidłowo uruchamiana z powodu błędów jądra podczas procesu rozruchu. Podczas procesu rozruchu, gdy moduł ładujący rozruchu próbuje zlokalizować jądro systemu Linux i przełączać do niego kontrolę rozruchu, maszyna wirtualna przechodzi do konsoli ratowniczej, gdy nastąpi niepowodzenie.

Jeśli okaże się, że nie można połączyć się z maszyną wirtualną w przyszłości, można wyświetlić zrzut ekranu maszyny wirtualnej za pomocą bloku diagnostyki rozruchu w Azure Portal. Może to pomóc zdiagnozować problem i określić, czy przyczyną jest podobny błąd rozruchu.

## <a name="recommended-steps"></a>Zalecane czynności

Postępuj zgodnie z poniższą procedurą zaradczą w zależności od otrzymanego błędu:

### <a name="error---unknown-filesystem"></a>Błąd — nieznany system plików

* Jeśli zostanie wyświetlony błąd **nieznany system plików**, ten błąd może wynikać z uszkodzenia systemu plików na partycji rozruchowej lub nieprawidłowej konfiguracji jądra.

   * W przypadku problemów z systemem plików wykonaj kroki opisane w artykule [odzyskiwanie systemu Linux: nie można przeprowadzić połączenia z maszyną wirtualną z systemem Linux z powodu błędów systemu plików (fsck, węzłów i)](/archive/blogs/linuxonazure/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes).
   * W przypadku problemów jądra postępuj zgodnie z instrukcjami w artykule [odzyskiwanie systemu Linux: Ręczne rozwiązywanie problemów z rozruchem, związanych z problemami jądra](/archive/blogs/linuxonazure/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems)lub [odzyskiwaniem systemu Linux: Rozwiązywanie problemów z rozruchem z powodu problemów z jądrem za pomocą chroot](/archive/blogs/linuxonazure/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot).
   
### <a name="error---file-not-found"></a>Błąd — nie znaleziono pliku

* Jeśli otrzymujesz **błąd błędu 15: nie znaleziono pliku lub początkowy dysk RAM** lub **plik oryginalnych initrd/initramfs**, wykonaj poniższe kroki.

    * Dla brakującego pliku `/boot/grub2/grub.cfg` lub `initrd/initramfs` Kontynuuj przy użyciu następującego procesu:

    1. Upewnij się `/etc/default/grub` , że istnieją poprawne/odpowiednie ustawienia. Jeśli nie wiesz, które są ustawienia domyślne, możesz zaewidencjonować działającą maszynę wirtualną.

    2. Następnie uruchom następujące polecenie, aby ponownie wygenerować jego konfigurację:`grub2-mkconfig -o /boot/grub2/grub.cfg`

   * Jeśli brakuje pliku `/boot/grub/menu.lst` , ten błąd jest przeznaczony dla starszych wersji systemu operacyjnego (**RHEL 6. x**, **CentOS 6. x** i **Ubuntu 14,04**), aby polecenia mogły się różnić. Aby upewnić się, że zostaną podane poprawne polecenia, musisz uruchomić stary serwer i przetestować go.

### <a name="error---no-such-partition"></a>Błąd — Brak partycji

* Jeśli wystąpi błąd **nie ma takiej partycji**, zapoznaj się z [scenariuszem przypadku: "nie ma takiej partycji" podczas próby uruchomienia maszyny wirtualnej po próbie zwiększenia dysku systemu operacyjnego](/archive/blogs/shwetanayak/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive).

### <a name="error---grubcfg-file-not-found"></a>Błąd — nie znaleziono pliku grub. cfg

* Jeśli otrzymujesz błąd **/Boot/grub2/grub.cfg nie znaleziono pliku**, wykonaj poniższe kroki.

    * Dla brakującego pliku `/boot/grub2/grub.cfg` lub `initrd/initramfs` Kontynuuj przy użyciu następującego procesu:

    1. Upewnij się `/etc/default/grub` , że istnieją poprawne/odpowiednie ustawienia. Jeśli nie wiesz, które są ustawienia domyślne, możesz zaewidencjonować działającą maszynę wirtualną.

    2. Następnie uruchom następujące polecenie, aby ponownie wygenerować jego konfigurację: `grub2-mkconfig -o /boot/grub2/grub.cfg` .

   * Jeśli brakuje pliku `/boot/grub/menu.lst` , ten błąd jest przeznaczony dla starszych wersji systemu operacyjnego (**RHEL 6. x**, **CentOS 6. x** i **Ubuntu 14,04**), aby polecenia mogły odroczyć. Uruchom stary serwer i przetestuj go, aby upewnić się, że podano prawidłowe polecenia.

## <a name="next-steps"></a>Następne kroki

* [Omówienie agenta maszyny wirtualnej platformy Azure](../extensions/agent-windows.md)
* [Rozszerzenia i funkcje maszyny wirtualnej dla systemu Windows](../extensions/features-windows.md)
