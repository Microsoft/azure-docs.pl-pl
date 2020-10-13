---
title: Tworzenie pakietu dla pomocy technicznej serii StorSimple 8000
description: Dowiedz się, jak tworzyć, odszyfrowywać i edytować pakiet pomocy technicznej dla urządzenia z serii StorSimple 8000.
author: alkohli
ms.service: storsimple
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 4a847b273472ecc9d2aaa3993ec9d88aa46f2e7f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973873"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Utwórz pakiet pomocy technicznej dla serii StorSimple 8000 i Zarządzaj nim

## <a name="overview"></a>Omówienie

Pakiet pomocy technicznej StorSimple to łatwy w użyciu mechanizm, który gromadzi wszystkie odpowiednie dzienniki, aby ułatwić pomoc techniczna firmy Microsoft Rozwiązywanie problemów z urządzeniami StorSimple. Zebrane dzienniki są szyfrowane i kompresowane.

Ten samouczek zawiera instrukcje krok po kroku dotyczące tworzenia pakietu dla urządzenia z serii StorSimple 8000 i zarządzania nim. Jeśli pracujesz z wirtualną tablicą StorSimple, przejdź do pozycji [Generuj pakiet dzienników](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="create-a-support-package"></a>Tworzenie pakietu dla pomocy technicznej

W niektórych przypadkach należy ręcznie utworzyć pakiet pomocy technicznej za pomocą program Windows PowerShell dla usługi StorSimple. Na przykład:

* Jeśli musisz usunąć poufne informacje z plików dziennika przed udostępnieniem pomoc techniczna firmy Microsoft.
* Jeśli masz problemy z przekazywaniem pakietu ze względu na problemy z łącznością.

Możesz udostępnić ręcznie wygenerowany pakiet pomocy technicznej pomoc techniczna firmy Microsoft za pośrednictwem poczty e-mail. Wykonaj następujące kroki, aby utworzyć pakiet pomocy technicznej w program Windows PowerShell dla usługi StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Aby utworzyć pakiet pomocy technicznej w program Windows PowerShell dla usługi StorSimple

1. Aby rozpocząć sesję programu Windows PowerShell jako administrator na komputerze zdalnym, który jest używany do nawiązywania połączenia z urządzeniem StorSimple, wprowadź następujące polecenie:
   
    `Start PowerShell`
2. W sesji programu Windows PowerShell Połącz się z konsolą SSAdmin urządzenia:
   
   1. W wierszu polecenia wprowadź:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. W otwartym oknie dialogowym wprowadź hasło administratora urządzenia. Domyślne hasło to _Password1_.
     
      ![Okno dialogowe poświadczeń programu PowerShell](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Wybierz przycisk **OK**.
   4. W wierszu polecenia wprowadź:
     
      `Enter-PSSession $MS`
3. W otwartej sesji wprowadź odpowiednie polecenie.
   
   * W przypadku udziałów sieciowych chronionych hasłem wpisz:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       Zostanie wyświetlony monit o hasło i hasło szyfrowania (ponieważ pakiet pomocy technicznej jest szyfrowany). Pakiet pomocy technicznej jest następnie tworzony w folderze domyślnym (nazwa urządzenia dołączona z bieżącą datą i godziną).
   * W przypadku udziałów, które nie są chronione hasłem, parametr nie jest wymagany `-Credential` . Wprowadź następujące dane:
     
       `Export-HcsSupportPackage`
     
       Pakiet pomocy technicznej jest tworzony dla obu kontrolerów w folderze domyślnym. Pakiet to zaszyfrowany, skompresowany plik, który można wysłać do pomoc techniczna firmy Microsoft w celu rozwiązywania problemów. Aby uzyskać więcej informacji, zobacz [Contact pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Parametry polecenia cmdlet Export-HcsSupportPackage

Przy użyciu polecenia cmdlet Export-HcsSupportPackage można użyć następujących parametrów.

| Parametr | Wymagane/Opcjonalne | Opis |
| --- | --- | --- |
| `-Path` |Wymagane |Służy do zapewnienia lokalizacji udostępnionego folderu sieciowego, w którym znajduje się pakiet pomocy technicznej. |
| `-EncryptionPassphrase` |Wymagane |Użyj, aby podać hasło, aby pomóc w zaszyfrowaniu pakietu dla pomocy technicznej. |
| `-Credential` |Opcjonalne |Użyj, aby podać poświadczenia dostępu do udostępnionego folderu sieciowego. |
| `-Force` |Opcjonalne |Użyj, aby pominąć etap potwierdzania hasła szyfrowania. |
| `-PackageTag` |Opcjonalne |Użyj, aby określić katalog w *ścieżce* , w którym jest umieszczany pakiet pomocy technicznej. Wartość domyślna to [nazwa urządzenia] — [bieżąca data i godzina: RRRR-MM-DD-HH-mm-SS]. |
| `-Scope` |Opcjonalne |Określ jako **klaster** (domyślnie), aby utworzyć pakiet dla obu kontrolerów. Jeśli chcesz utworzyć pakiet tylko dla bieżącego kontrolera, określ pozycję **kontroler**. |

## <a name="edit-a-support-package"></a>Edytuj pakiet pomocy technicznej

Po wygenerowaniu pakietu dla pomocy technicznej może być konieczne edytowanie pakietu w celu usunięcia poufnych informacji. Może to obejmować nazwy woluminów, adresy IP urządzeń i nazwy kopii zapasowych z plików dziennika.

> [!IMPORTANT]
> Można edytować tylko pakiet pomocy technicznej, który został wygenerowany za pomocą program Windows PowerShell dla usługi StorSimple. Nie można edytować pakietu utworzonego w Azure Portal za pomocą usługi StorSimple Menedżer urządzeń.

Aby edytować pakiet pomocy technicznej przed przekazaniem go do witryny pomoc techniczna firmy Microsoft, należy najpierw odszyfrować pakiet pomocy technicznej, edytować pliki, a następnie ponownie go zaszyfrować. Wykonaj poniższe kroki.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Aby edytować pakiet pomocy technicznej w program Windows PowerShell dla usługi StorSimple

1. Wygeneruj pakiet pomocy technicznej zgodnie z wcześniejszym opisem w artykule [Aby utworzyć pakiet pomocy technicznej w program Windows PowerShell dla usługi StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Pobierz skrypt](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokalnie na komputerze klienckim.
3. Zaimportuj moduł programu Windows PowerShell. Określ ścieżkę do folderu lokalnego, w którym pobrano skrypt. Aby zaimportować moduł, wprowadź:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Wszystkie pliki są plikami *AES* , które są skompresowane i szyfrowane. Aby zdekompresować i odszyfrować pliki, wpisz:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Należy pamiętać, że rzeczywiste rozszerzenia plików są teraz wyświetlane dla wszystkich plików.
   
    ![Edytuj pakiet pomocy technicznej](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. Po wyświetleniu monitu o hasło szyfrowania wprowadź hasło użyte podczas tworzenia pakietu dla pomocy technicznej.
   
    ```powershell
    cmdlet Open-HcsSupportPackage at command pipeline position 1

    Supply values for the following parameters:EncryptionPassphrase: ****
    ```
6. Przejdź do folderu, który zawiera pliki dziennika. Ponieważ pliki dziennika są teraz dekompresowane i odszyfrowywane, będą mieć oryginalne rozszerzenia plików. Zmodyfikuj te pliki, aby usunąć wszystkie informacje specyficzne dla klienta, takie jak nazwy woluminów i adresy IP urządzeń, a następnie Zapisz pliki.
7. Zamknij pliki, aby skompresować je za pomocą gzip i zaszyfrować je za pomocą algorytmu AES-256. Jest to szybkość i bezpieczeństwo transferu pakietu pomocy technicznej przez sieć. Aby skompresować i zaszyfrować pliki, wprowadź następujące polecenie:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Edytuj pakiet pomocy technicznej 2](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. Po wyświetleniu monitu podaj hasło szyfrowania dla zmodyfikowanego pakietu dla pomocy technicznej.
   
    ```powershell
    cmdlet Close-HcsSupportPackage at command pipeline position 1
    Supply values for the following parameters:EncryptionPassphrase: ****
    ```
9. Zapisz nowe hasło, tak aby można było udostępnić je pomoc techniczna firmy Microsoft na żądanie.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Przykład: edytowanie plików w pakiecie pomocy technicznej w udziale chronionym hasłem

Poniższy przykład pokazuje, jak odszyfrować, edytować i ponownie zaszyfrować pakiet pomocy technicznej.

```powershell
PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

cmdlet Open-HcsSupportPackage at command pipeline position 1

Supply values for the following parameters:

EncryptionPassphrase: ****

PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

cmdlet Close-HcsSupportPackage at command pipeline position 1

Supply values for the following parameters:

EncryptionPassphrase: ****

PS C:\WINDOWS\system32>
```

## <a name="next-steps"></a>Następne kroki

* [Informacje o informacjach zebranych w pakiecie pomocy technicznej](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Informacje dotyczące [rozwiązywania problemów z wdrożeniem urządzeń przy użyciu pakietów pomocy technicznej i dzienników urządzeń](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Dowiedz się [, jak zarządzać urządzeniem StorSimple przy użyciu usługi StorSimple Menedżer urządzeń](storsimple-8000-manager-service-administration.md).

