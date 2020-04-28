---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/03/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: afeae4af9b41bf434b26833a3bd927118a4697ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183457"
---
**Wymagania dotyczące konfiguracji/przetwarzania serwera dla replikacji serwera fizycznego**

**Składnik** | **Wymaganie** 
--- | ---
**USTAWIENIA SPRZĘTU** | 
Rdzenie procesora CPU | 8 
Pamięć RAM | 16 GB
Liczba dysków | 3, w tym dysk systemu operacyjnego, dysk pamięci podręcznej serwera przetwarzania i dysk przechowywania po awarii 
Wolne miejsce na dysku (pamięć podręczna serwera przetwarzania) | 600 GB
Wolne miejsce na dysku (dysk retencyjny) | 600 GB
 | 
**USTAWIENIA OPROGRAMOWANIA** | 
System operacyjny | Windows Server 2012 R2 <br> Windows Server 2016
Ustawienia regionalne systemu operacyjnego | Angielski (en-us)
Role systemu Windows Server | Nie włączaj tych ról: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Zasady grupy | Nie włączaj tych zasad grupy: <br> - Uniemożliwić dostęp do wiersza polecenia. <br> - Uniemożliwić dostęp do narzędzi do edycji rejestru. <br> - Logika zaufania dla załączników plików. <br> - Włącz wykonywanie skryptów. <br> [Dowiedz się więcej](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Brak istniejącej wcześniej domyślnej strony internetowej <br> - Brak istniejącej strony internetowej / nasłuchiwania aplikacji na porcie 443 <br>- Włącz [uwierzytelnianie anonimowe](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Włącz ustawienie [FastCGI.](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)
Typ adresu IP | Statyczny 
| 
**USTAWIENIA DOSTĘPU** | 
Mysql | MySQL powinien być zainstalowany na serwerze konfiguracji. Można zainstalować ręcznie lub Site Recovery można zainstalować go podczas wdrażania. Aby program Site Recovery mógł się http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msizainstalować, sprawdź, czy urządzenie może osiągnąć .
Adresy URL | Serwer konfiguracji potrzebuje dostępu do tych adresów URL (bezpośrednio lub za pośrednictwem serwera proxy):<br/><br/> Usługa Azure `login.microsoftonline.com`AD: ; `login.microsoftonline.us`;`*.accesscontrol.windows.net`<br/><br/> Transfer danych replikacji: `*.backup.windowsazure.com`;`*.backup.windowsazure.us`<br/><br/> Zarządzanie replikacją: `*.hypervrecoverymanager.windowsazure.com`; `*.hypervrecoverymanager.windowsazure.us`; `https://management.azure.com`;`*.services.visualstudio.com`<br/><br/> Dostęp do `*.blob.core.windows.net`pamięci masowej: ;`*.blob.core.usgovcloudapi.net`<br/><br/> Synchronizacja czasu: `time.nist.gov`;`time.windows.com`<br/><br/> Telemetria (opcjonalnie):`dc.services.visualstudio.com`
Zapora | Reguły zapory oparte na adresie IP powinny zezwalać na komunikację z adresami URL platformy Azure. Aby uprościć i ograniczyć zakresy adresów IP, zalecamy użycie filtrowania adresów URL.<br/><br/>**W przypadku komercyjnych ip:**<br/><br/>- Zezwól [na zakresy adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)i port HTTPS (443).<br/><br/> - Zezwalaj na zakresy adresów IP dla zachodnich stanów USA (używane do kontroli dostępu i zarządzania tożsamościami).<br/><br/> - Zezwalaj na zakresy adresów IP dla regionu platformy Azure subskrypcji, aby obsługiwać adresy URL potrzebne do usługi Azure Active Directory, kopii zapasowej, replikacji i magazynu.<br/><br/> **Dla instytucji IP rządu:**<br/><br/> - Zezwalaj na zakresy adresów IP centrum danych platformy Azure i port HTTPS (443).<br/><br/> - Zezwalaj na zakresy adresów IP dla wszystkich regionów gov usa (Virginia, Texas, Arizona i Iowa), aby obsługiwać adresy URL potrzebne do usługi Azure Active Directory, kopii zapasowej, replikacji i magazynu.
Porty | Zezwalaj na 443 (aranżacja kanału sterowania)<br/><br/> Zezwalaj na 9443 (Transport danych) 


**Wymagania dotyczące rozmiaru serwera konfiguracji/procesu**

**Procesora** | **Pamięci** | **Dysk pamięci podręcznej** | **Szybkość zmiany danych** | **Zreplikowane maszyny**
--- | --- | --- | --- | ---
8 procesorów wirtualnych<br/><br/> 2 gniazda * 4 \@ rdzenie 2,5 GHz | 16 GB | 300 GB | 500 GB lub mniej | < 100 maszyn
12 procesorów wirtualnych<br/><br/> 2 skarpetki * \@ 6 rdzeni 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | Od 100 do 150 maszyn
16 procesorów wirtualnych<br/><br/> 2 skarpetki * \@ 8 rdzeni 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 -200 maszyn

