---
title: plik dołączany
description: plik dołączany
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/03/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: afeae4af9b41bf434b26833a3bd927118a4697ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183457"
---
**Wymagania dotyczące konfiguracji/serwera przetwarzania na potrzeby replikacji serwera fizycznego**

**Składnik** | **Wymaganie** 
--- | ---
**USTAWIENIA SPRZĘTU** | 
Rdzenie procesora CPU | 8 
Pamięć RAM | 16 GB
Liczba dysków | 3, w tym dysk systemu operacyjnego, dysk pamięci podręcznej serwera przetwarzania i dysk przechowywania na potrzeby powrotu po awarii 
Wolne miejsce na dysku (pamięć podręczna serwera przetwarzania) | 600 GB
Wolne miejsce na dysku (dysk przechowywania) | 600 GB
 | 
**USTAWIENIA OPROGRAMOWANIA** | 
System operacyjny | Windows Server 2012 z dodatkiem R2 <br> Windows Server 2016
Ustawienia regionalne systemu operacyjnego | Angielski (en-us)
Role systemu Windows Server | Nie należy włączać tych ról: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Zasady grupy | Nie włączaj tych zasad grupy: <br> -Zapobiegaj dostępowi do wiersza polecenia. <br> — Uniemożliwia dostęp do narzędzi do edytowania rejestru. <br> — Logika zaufania dla plików załączników. <br> — Włącz wykonywanie skryptu. <br> [Dowiedz się więcej](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Brak istniejącej domyślnej witryny sieci Web <br> — Żadna istniejąca witryna sieci Web/aplikacja nie nasłuchuje na porcie 443 <br>-Włącz  [uwierzytelnianie anonimowe](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Włącz ustawienie [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) .
Typ adresu IP | Static 
| 
**USTAWIENIA DOSTĘPU** | 
MYSQL | Serwer MySQL powinien być zainstalowany na serwerze konfiguracji. Można zainstalować go ręcznie lub Site Recovery można go zainstalować podczas wdrażania. Aby zainstalować Site Recovery, sprawdź, czy komputer może nawiązać połączenie http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi .
Adresy URL | Serwer konfiguracji musi mieć dostęp do tych adresów URL (bezpośrednio lub za pośrednictwem serwera proxy):<br/><br/> Azure AD: `login.microsoftonline.com` ; `login.microsoftonline.us` ; `*.accesscontrol.windows.net`<br/><br/> Transfer danych replikacji: `*.backup.windowsazure.com` ; `*.backup.windowsazure.us`<br/><br/> Zarządzanie replikacją `*.hypervrecoverymanager.windowsazure.com` : `*.hypervrecoverymanager.windowsazure.us` ; `https://management.azure.com` ; `*.services.visualstudio.com`<br/><br/> Dostęp do magazynu: `*.blob.core.windows.net` ; `*.blob.core.usgovcloudapi.net`<br/><br/> Synchronizacja czasu: `time.nist.gov` ; `time.windows.com`<br/><br/> Dane telemetryczne (opcjonalnie): `dc.services.visualstudio.com`
Zapora | Reguły zapory oparte na adresach IP powinny zezwalać na komunikację z adresami URL platformy Azure. Aby uprościć i ograniczyć zakres adresów IP, zalecamy użycie filtrowania adresów URL.<br/><br/>**Dla komercyjnych adresów IP:**<br/><br/>-Zezwalaj na [zakresy adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)oraz port HTTPS (443).<br/><br/> -Zezwalaj na zakresy adresów IP w regionie zachodnie stany USA (używane do zarządzania Access Control i tożsamością).<br/><br/> -Zezwalaj na zakresy adresów IP dla regionu świadczenia usługi Azure w ramach subskrypcji, aby obsługiwać adresy URL, które są konieczne do Azure Active Directory, kopii zapasowej, replikacji i magazynu.<br/><br/> **W przypadku adresów IP instytucji rządowych:**<br/><br/> -Zezwalaj na Azure Government zakresy adresów IP centrum danych i port HTTPS (443).<br/><br/> -Zezwalaj na zakresy adresów IP dla wszystkich regionów US Gov (Wirginia, Texas, Arizona i Iowa) w celu obsługi adresów URL wymaganych dla Azure Active Directory, kopii zapasowej, replikacji i magazynu.
Porty | Zezwalaj na 443 (aranżacja kanału kontroli)<br/><br/> Zezwalaj na 9443 (transport danych) 


**Wymagania dotyczące ustalania wielkości serwera konfiguracji/przetwarzania**

**Procesor CPU** | **Pamięć** | **Dysk pamięci podręcznej** | **Szybkość zmian danych** | **Zreplikowane maszyny**
--- | --- | --- | --- | ---
8 procesorów wirtualnych vCPU<br/><br/> 2 gniazda * 4 rdzenie \@ 2,5 GHz | 16GB | 300 GB | 500 GB lub mniej | Maszyny < 100
12 procesorów wirtualnych vCPU<br/><br/> 2 SOCKS * 6 rdzeni \@ 2,5 GHz | 18 GB | 600 GB | 500 GB — 1 TB | 100 do 150 maszyn
16 procesorów wirtualnych vCPU<br/><br/> 2 SOCKS * 8 rdzeni \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 – 200 maszyn

