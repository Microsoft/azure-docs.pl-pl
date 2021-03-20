---
title: Azure Data Box Disk Rozwiązywanie problemów dotyczących odblokowywania dysków | Microsoft Docs
description: Informacje o przepływach pracy rozwiązywania problemów dotyczących narzędzia do odblokowywania z programem Azure Data Box Disk. Zapoznaj się z błędami narzędzia odblokowywania Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 08/05/2020
ms.author: alkohli
ms.openlocfilehash: 866cf01243983863292ada0b086f8f5b2f94e412
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87925565"
---
# <a name="troubleshoot-disk-unlocking-issues-in-azure-data-box-disk"></a>Rozwiązywanie problemów dotyczących odblokowywania dysków w Azure Data Box Disk

Ten artykuł ma zastosowanie do Microsoft Azure Data Box Disk i opisuje przepływy pracy służące do rozwiązywania problemów podczas korzystania z narzędzia do odblokowywania. 


<!--## Query activity logs

Use the activity logs to find who unlocked and accessed the disks. Your Data Box Disk arrive on your premises in a locked state. You can use the device credentials available in the Azure portal for your order to unlock them.  

To figure out who accessed the **Device credentials** blade, you can query the Activity logs.  Any action that involves accessing **Device details > Credentials** blade is logged into the activity logs as `ListCredentials` action.

![Query Activity logs](media/data-box-logs/query-activity-log-1.png)-->


## <a name="data-box-disk-unlock-tool-errors"></a>Błędy narzędzia do odblokowywania dysków Data Box Disk


| Komunikat o błędzie/działanie narzędzia      | Zalecenia                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| The current .NET Framework is not supported. The supported versions are 4.5 and later.<br><br>Narzędzie kończy działanie, wyświetlając komunikat.  | Program .NET 4.5 nie jest zainstalowany. Zainstaluj program .NET 4.5 lub nowszą wersję na komputerze hosta, na którym uruchomiono narzędzie do odblokowywania dysków Data Box.                                                                            |
| Could not unlock or verify any volumes. Contact Microsoft Support.  <br><br>Za pomocą narzędzia nie można odblokować lub sprawdzić żadnego zablokowanego dysku. | Narzędzie nie mogło odblokować żadnego z zablokowanych dysków przy użyciu podanego klucza dostępu. Skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać informacje o kolejnych krokach.                                                |
| Following volumes are unlocked and verified. <br>Volume drive letters: E:<br>Could not unlock any volumes with the following passkeys: werwerqomnf, qwerwerqwdfda <br><br>Narzędzie odblokowało niektóre dyski i wyświetla listę liter dysków, które udało się odblokować, i tych, których nie udało się odblokować.| Częściowe powodzenie. Nie udało się odblokować niektórych dysków przy użyciu podanego klucza dostępu. Skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać informacje o kolejnych krokach. |
| Could not find locked volumes. Verify disk received from Microsoft is connected properly and is in locked state.          | Narzędzie nie odnajduje żadnych zablokowanych dysków. Dyski są już odblokowane lub nie zostały wykryte. Upewnij się, że dyski są podłączone i zablokowane. <br> <br>Ten błąd może pojawić się również w przypadku sformatowania dysków. W przypadku sformatowania dysków nie można ich używać. Skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać informacje o kolejnych krokach.                                                          |
| Fatal error: Invalid parameter<br>Parameter name: invalid_arg<br>USAGE:<br>DataBoxDiskUnlock /PassKeys:<passkey_list_separated_by_semicolon><br><br>Example: DataBoxDiskUnlock /PassKeys:passkey1;passkey2;passkey3<br>Example: DataBoxDiskUnlock /SystemCheck<br>Example: DataBoxDiskUnlock /Help<br><br>/PassKeys:       Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.<br>/Help:           This option provides help on cmdlet usage and examples.<br>/SystemCheck:    This option checks if your system meets the requirements to run the tool.<br><br>Press any key to exit. | Wprowadzono nieprawidłowy parametr. Jedyne dozwolone parametry to/SystemCheck,/PassKey i/help.|


## <a name="unlock-issues-for-disks-when-using-a-windows-client"></a>Odblokuj problemy dotyczące dysków podczas korzystania z klienta systemu Windows

W tej sekcji przedstawiono niektóre najczęstsze problemy występujące podczas wdrażania Data Box Disk podczas korzystania z klienta systemu Windows do kopiowania danych.

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>Problem: nie można odblokować dysku na podstawie funkcji BitLocker
 
**Przyczyna** 

W oknie dialogowym funkcji BitLocker użyto hasła i próba odblokowania dysku za pośrednictwem okna dialogowego dysków odblokowywania funkcją BitLocker. Nie będzie to zadziałało.

**Rozwiązanie**

Aby odblokować dyski urządzenie Data Box, należy użyć narzędzia odblokowywanie Data Box Disk i podać hasło w Azure Portal. Aby uzyskać więcej informacji, przejdź do [samouczka: rozpakowywanie, łączenie i odblokowywanie Azure Data Box Disk](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey).
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>Problem: nie można odblokować lub zweryfikować niektórych woluminów. Contact Microsoft Support.
 
**Przyczyna**

W dzienniku błędów może zostać wyświetlony następujący błąd i nie będzie można odblokować ani zweryfikować niektórych woluminów.

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
Oznacza to, że najkorzystniej brakuje odpowiedniej wersji programu Windows PowerShell na kliencie systemu Windows.

**Rozwiązanie**

Możesz zainstalować program [Windows PowerShell](https://www.microsoft.com/download/details.aspx?id=54616) w wersji 5,0 i ponownie wykonać operację.
 
Jeśli nadal nie możesz odblokować woluminów, Skopiuj dzienniki z folderu, w którym znajduje się narzędzie do odblokowywania Data Box Disk i [Pomoc techniczna firmy Microsoft kontaktów](data-box-disk-contact-microsoft-support.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [rozwiązywać problemy z walidacją](data-box-disk-troubleshoot.md).
