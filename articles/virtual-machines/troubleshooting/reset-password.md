---
title: Jak zresetować hasło lokalnego systemu Linux na maszynach wirtualnych platformy Azure | Microsoft Docs
description: Wprowadź kroki resetowania lokalnego hasła systemu Linux na maszynie wirtualnej platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 08/20/2019
ms.author: delhan
ms.openlocfilehash: c6bfd5b9ff3626593916533f27c5c2755cebcb13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87028485"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Jak zresetować lokalne hasło systemu Linux na maszynach wirtualnych platformy Azure

W tym artykule wprowadzono kilka metod resetowania lokalnych haseł maszyn wirtualnych z systemem Linux. Jeśli konto użytkownika wygasło lub po prostu chcesz utworzyć nowe konto, możesz użyć następujących metod, aby utworzyć nowe konto administratora lokalnego i ponownie uzyskać dostęp do maszyny wirtualnej.

## <a name="symptoms"></a>Objawy

Nie można zalogować się do maszyny wirtualnej i zostanie wyświetlony komunikat informujący o niepoprawnym użyciu hasła. Ponadto nie można użyć VMAgent do zresetowania hasła na Azure Portal.

## <a name="manual-password-reset-procedure"></a>Procedura ręcznego resetowania hasła

> [!NOTE]
> Poniższe kroki nie mają zastosowania do maszyny wirtualnej z dyskiem niezarządzanym.

1. Utwórz migawkę dysku systemu operacyjnego z zaatakowaną maszyną wirtualną, Stwórz dysk na podstawie migawki, a następnie Dołącz dysk do maszyny wirtualnej rozwiązywania problemów. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z maszyną wirtualną z systemem Windows przez dołączenie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania przy użyciu Azure Portal](troubleshoot-recovery-disks-portal-linux.md).

2. Nawiąż połączenie z maszyną wirtualną rozwiązywania problemów przy użyciu Pulpit zdalny.

3.  Uruchom następujące polecenie SSH na maszynie wirtualnej rozwiązywania problemów, aby zostać użytkownikiem.

    ```bash
    sudo su
    ```

4.  Uruchom narzędzia **fdisk-l** lub Sprawdź dzienniki systemu, aby znaleźć nowo dołączony dysk. Znajdź nazwę dysku do zainstalowania. Następnie na maszynie wirtualnej czasowej Sprawdź odpowiedni plik dziennika.

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    Poniżej przedstawiono przykładowe dane wyjściowe polecenia grep:

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  Utwórz punkt instalacji o nazwie **tempmount**.

    ```bash
    mkdir /tempmount
    ```

6.  Zainstaluj dysk systemu operacyjnego w punkcie instalacji. Zwykle konieczna jest instalacja *sdc1* lub *sdc2*. Będzie to zależało od partycji hostingu w katalogu */etc* na uszkodzonym dysku komputera.

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  Utwórz kopie podstawowych plików poświadczeń przed wprowadzeniem jakichkolwiek zmian:

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  Zresetuj wymagane hasło użytkownika:

    ```bash
    passwd <<USER>> 
    ```

9.  Przenieś zmodyfikowane pliki do odpowiedniej lokalizacji na dysku uszkodzonej maszyny.

    ```bash
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ```

10. Wróć do katalogu głównego i Odinstaluj dysk.

    ```bash
    cd /
    umount /tempmount
    ```

11. W Azure Portal Odłącz dysk od maszyny wirtualnej rozwiązywania problemów.

12. [Zmień dysk systemu operacyjnego dla maszyny wirtualnej, której to dotyczy](troubleshoot-recovery-disks-portal-linux.md#swap-the-os-disk-for-the-vm).

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z maszyną wirtualną Azure przez dołączenie dysku systemu operacyjnego do innej maszyny wirtualnej](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Interfejs wiersza polecenia platformy Azure: jak usunąć i ponownie wdrożyć maszynę wirtualną z dysku VHD](/archive/blogs/linuxonazure/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd)
