---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a142de62f1ab7046d47c29753863b16864d97536
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075623"
---
Klucze szyfrowania danych usługi są używane do szyfrowania poufnych danych klienta, takich jak poświadczenia konta magazynu, które są wysyłane z usługi StorSimple Manager do urządzenia StorSimple. Należy okresowo zmienić te klucze, jeśli organizacja IT ma kluczowe zasady rotacji na urządzeniach magazynujących. Proces zmiany klucza może się nieco różnić w zależności od tego, czy istnieje jedno urządzenie lub wiele urządzeń zarządzanych przez usługę StorSimple Manager. Aby uzyskać więcej informacji, przejdź do [StorSimple zabezpieczenia i ochrona danych](../articles/storsimple/storsimple-8000-security.md).

Zmiana klucza szyfrowania danych usługi jest procesem trójwymiarowym:

1. Za pomocą skryptów programu Windows PowerShell dla Azure Resource Manager Autoryzuj urządzenie, aby zmienić klucz szyfrowania danych usługi.
2. Przy użyciu program Windows PowerShell dla usługi StorSimple zainicjuj zmianę klucza szyfrowania danych usługi.
3. Jeśli masz więcej niż jedno urządzenie StorSimple, zaktualizuj klucz szyfrowania danych usługi na innych urządzeniach.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Krok 1. używanie skryptu programu Windows PowerShell do autoryzacji urządzenia do zmiany klucza szyfrowania danych usługi
Zazwyczaj administrator urządzenia będzie żądać, aby administrator usługi autoryzuje urządzenie w celu zmiany kluczy szyfrowania danych usługi. Następnie administrator usługi autoryzuje urządzenie, aby zmienić klucz.

Ten krok jest wykonywany przy użyciu skryptu opartego na Azure Resource Manager. Administrator usługi może wybrać urządzenie, które ma uprawnienia do autoryzacji. Urządzenie jest następnie autoryzowane do uruchomienia procesu zmiany klucza szyfrowania danych usługi. 

Aby uzyskać więcej informacji o używaniu skryptu, przejdź do [Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Które urządzenia mogą być autoryzowane, aby można było zmienić klucze szyfrowania danych usługi?
Aby można było autoryzować zmiany klucza szyfrowania danych usługi, urządzenie musi spełniać następujące kryteria:

* Urządzenie musi być w trybie online, aby można było kwalifikować się do autoryzacji zmiany klucza szyfrowania danych usługi.
* Możesz autoryzować to samo urządzenie ponownie po 30 minutach, jeśli zmiana klucza nie została zainicjowana.
* Można autoryzować inne urządzenie, pod warunkiem, że zmiana klucza nie została zainicjowana przez wcześniej autoryzowane urządzenie. Gdy nowe urządzenie zostanie autoryzowane, stare urządzenie nie może zainicjować zmiany.
* Nie można autoryzować urządzenia, gdy trwa Przerzucanie klucza szyfrowania danych usługi.
* Możesz autoryzować urządzenie, gdy niektóre z urządzeń zarejestrowanych w usłudze przekroczą szyfrowanie, a inne nie. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Krok 2. użycie program Windows PowerShell dla usługi StorSimple do zainicjowania zmiany klucza szyfrowania danych usługi
Ten krok jest wykonywany w interfejsie program Windows PowerShell dla usługi StorSimple na autoryzowanym urządzeniu StorSimple.

> [!NOTE]
> Nie można wykonać żadnych operacji w Azure Portal usługi StorSimple Manager, dopóki nie zostanie zakończone Przerzucanie kluczy.


Jeśli używasz konsoli szeregowej urządzenia do nawiązywania połączenia z interfejsem programu Windows PowerShell, wykonaj następujące czynności.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Aby zainicjować zmianę klucza szyfrowania danych usługi
1. Wybierz opcję 1, aby zalogować się z pełnymi prawami dostępu.
2. W wierszu polecenia wpisz polecenie:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Po pomyślnym ukończeniu tego polecenia cmdlet otrzymasz nowy klucz szyfrowania danych usługi. Skopiuj i Zapisz ten klucz do użycia w kroku 3 tego procesu. Ten klucz zostanie użyty do zaktualizowania wszystkich pozostałych urządzeń zarejestrowanych w usłudze StorSimple Manager.
   
   > [!NOTE]
   > Ten proces musi zostać zainicjowany w ciągu czterech godzin od autoryzowania urządzenia StorSimple.
   > 
   > 
   
   Ten nowy klucz jest następnie wysyłany do usługi w celu wypychania do wszystkich urządzeń zarejestrowanych w usłudze. Zostanie wyświetlony alert na pulpicie nawigacyjnym usługi. Usługa spowoduje wyłączenie wszystkich operacji na zarejestrowanych urządzeniach, a następnie administrator urządzenia będzie musiał zaktualizować klucz szyfrowania danych usługi na innych urządzeniach. Jednak systemy I/OS (hosty wysyłające dane do chmury) nie zostaną zakłócone.
   
   Jeśli masz pojedyncze urządzenie zarejestrowane w usłudze, proces przerzucania jest teraz zakończony i możesz pominąć następny krok. Jeśli masz wiele urządzeń zarejestrowanych w usłudze, przejdź do kroku 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Krok 3. aktualizowanie klucza szyfrowania danych usługi na innych urządzeniach StorSimple
Te kroki należy wykonać w interfejsie programu Windows PowerShell urządzenia StorSimple, jeśli istnieje wiele urządzeń zarejestrowanych w usłudze StorSimple Manager. Aby zaktualizować wszystkie pozostałe urządzenia StorSimple zarejestrowane w usłudze StorSimple Manager, należy użyć klucza uzyskanego w kroku 2.

Wykonaj następujące kroki, aby zaktualizować szyfrowanie danych usługi na urządzeniu.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Aby zaktualizować klucz szyfrowania danych usługi na urządzeniach fizycznych
1. Użyj program Windows PowerShell dla usługi StorSimple, aby nawiązać połączenie z konsolą programu. Wybierz opcję 1, aby zalogować się z pełnymi prawami dostępu.
2. W wierszu polecenia wpisz:  `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Podaj klucz szyfrowania danych usługi uzyskany w [kroku 2: użyj program Windows PowerShell dla usługi StorSimple do zainicjowania zmiany klucza szyfrowania danych usługi](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Aby zaktualizować klucz szyfrowania danych usługi na wszystkich urządzeniach chmury 8010/8020
1. Pobierz i skonfiguruj [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) skrypt programu PowerShell. 
2. Otwórz program PowerShell i w wierszu polecenia wpisz:  `Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Ten skrypt zapewni, że klucz szyfrowania danych usługi jest ustawiony na wszystkich urządzeniach chmury 8010/8020 w ramach Menedżera urządzeń.