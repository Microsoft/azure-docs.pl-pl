---
title: Zalecenia dotyczące programu Endpoint Protection w centrach zabezpieczeń Azure
description: Sposób odnajdywania i identyfikowania rozwiązań programu Endpoint Protection w dobrej kondycji.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: memildin
ms.openlocfilehash: 1ce20deed8b26dc5f5bebf4656dd3f1c370d766f
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561232"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Ocena i zalecenia dotyczące programu Endpoint Protection w Azure Security Center

Azure Security Center zapewnia oceny kondycji [obsługiwanych](security-center-services.md#endpoint-supported) wersji rozwiązań programu Endpoint Protection. W tym artykule opisano scenariusze, które powodują Security Center w celu wygenerowania następujących dwóch zaleceń:

* **Zainstaluj rozwiązania programu Endpoint Protection na maszynie wirtualnej**
* **Rozwiązywanie problemów z kondycją programu Endpoint Protection na maszynach**

## <a name="windows-defender"></a>Windows Defender

* W Security Center zalecane jest **"Instalowanie rozwiązań programu Endpoint Protection na maszynie wirtualnej"** po uruchomieniu [Get-MpComputerStatus](/powershell/module/defender/get-mpcomputerstatus) , a wynikiem jest **AMServiceEnabled: false**

* Security Center zaleca **"Rozwiązywanie problemów z kondycją programu Endpoint Protection na maszynach" w** przypadku uruchamiania [Get-MpComputerStatus](/powershell/module/defender/get-mpcomputerstatus) i dowolnego z następujących wystąpień:

  * Każda z następujących właściwości ma wartość false:

    - **AMServiceEnabled**
    - **AntispywareEnabled**
    - **RealTimeProtectionEnabled**
    - **BehaviorMonitorEnabled**
    - **IoavProtectionEnabled**
    - **OnAccessProtectionEnabled**

  * Jeśli jedna lub obie następujące właściwości mają wartość 7 lub więcej:

    - **AntispywareSignatureAge**
    - **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Program Microsoft System Center Endpoint Protection

* Security Center zaleca się **"Zainstaluj rozwiązania programu Endpoint Protection na maszynie wirtualnej"** podczas **importowania SCEPMpModule ("$env:P zabezpieczenia Client\MpProvider\MpProvider.psd1")** i uruchamiania programu **Get-MProtComputerStatus** wyniki w **AMServiceEnabled = false**.

* Security Center zaleca **"Rozwiązywanie problemów z kondycją programu Endpoint Protection na maszynach" w** przypadku uruchamiania **Get-MprotComputerStatus** i dowolnego z następujących wystąpień:

  * Co najmniej jedna z następujących właściwości ma wartość false:

    - **AMServiceEnabled**
    - **AntispywareEnabled**
    - **RealTimeProtectionEnabled**
    - **BehaviorMonitorEnabled**
    - **IoavProtectionEnabled**
    - **OnAccessProtectionEnabled**

  * Jeśli co najmniej jedna z następujących aktualizacji podpisu jest większa lub równa 7:

    * **AntispywareSignatureAge**
    * **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* Security Center zaleca się **"Zainstaluj rozwiązania programu Endpoint Protection na maszynie wirtualnej"** , gdy którykolwiek z następujących kontroli nie zostanie spełniony:
    - **HKLM: \ SOFTWARE\TrendMicro\Deep Security Agent** istnieje
    - **HKLM: \ SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** istnieje
    - Plik **dsa_query. cmd** zostanie znaleziony w folderze instalacyjnym
    - Uruchamianie **polecenia dsa_query. cmd** z użyciem **składnika. am. Mode: wykryto agenta zabezpieczeń w usłudze Trend Micro głęboki**

## <a name="symantec-endpoint-protection"></a>Symantec Endpoint Protection
Security Center zaleca się **"Zainstaluj rozwiązania programu Endpoint Protection na maszynie wirtualnej"** , gdy którykolwiek z następujących kontroli nie zostanie spełniony:

- **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**
- **HKLM: \ Software\Symantec\Symantec punkt końcowy Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Lub

- **HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**
- **HKLM: \ Software\Wow6432Node\Symantec\Symantec punkt końcowy Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Security Center zaleca **"Rozwiązywanie problemów z kondycją programu Endpoint Protection na swoich maszynach" w** przypadku braku spełnienia następujących testów:

- Sprawdź wersję firmy Symantec >= 12: Lokalizacja rejestru: **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion "-value" ProductVersion "**
- Sprawdzanie Real-Time stanu ochrony: **HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff = = 1**
- Sprawdź stan aktualizacji sygnatury: **HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 dni**
- Sprawdź stan pełnego skanowania: **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 dni**
- Znajdź ścieżkę numeru wersji sygnatury do wersji sygnatury dla firmy Symantec 12: **ścieżki rejestru + "CurrentVersion\SharedDefs"-value "SRTSP"** 
- Ścieżka do wersji sygnatury dla firmy Symantec 14: **ścieżki rejestru + "CurrentVersion\SharedDefs\SDSDefs"-value "SRTSP"**

Ścieżki rejestru:
- **"HKLM: \ Software\Symantec\Symantec Endpoint Protection" + $Path;**
- **"HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>Program McAfee Endpoint Protection dla systemu Windows

Security Center zaleca się **"Zainstaluj rozwiązania programu Endpoint Protection na maszynie wirtualnej"** , gdy którykolwiek z następujących kontroli nie zostanie spełniony:

- **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion** istnieje
- **HKLM: \ SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

Security Center zaleca **"Rozwiązywanie problemów z kondycją programu Endpoint Protection na swoich maszynach" w** przypadku braku spełnienia następujących testów:

- Wersja McAfee: **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**
- Znajdź wersję podpisu: **HKLM: \ Software\McAfee\AVSolution\DS\DS-Value "dwContentMajorVersion"**
- Znajdź datę podpisu: **HKLM: \ Software\McAfee\AVSolution\DS\DS-Value "szContentCreationDate" >= 7 dni**
- Znajdź datę skanowania: **HKLM: \ Software\McAfee\Endpoint\AV\ODS-Value "LastFullScanOdsRunTime" >= 7 dni**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>Zabezpieczenia programu McAfee Endpoint Protection dla ochrony przed zagrożeniami dla systemu Linux 

Security Center zaleca się **"Zainstaluj rozwiązania programu Endpoint Protection na maszynie wirtualnej"** , gdy którykolwiek z następujących kontroli nie zostanie spełniony:

- Plik **/opt/iSEC/ENS/threatprevention/bin/isecav** istnieje
- **"/opt/iSEC/ENS/threatprevention/bin/isecav--Version"** Output to: **McAfee Name = McAfee Endpoint Security dla ochrony przed zagrożeniami w systemie Linux i wersji McAfee >= 10**

Security Center zaleca **"Rozwiązywanie problemów z kondycją programu Endpoint Protection na swoich maszynach" w** przypadku braku spełnienia następujących testów:

- **"/opt/iSEC/ENS/threatprevention/bin/isecav--listtask"** zwraca **szybkie skanowanie, pełne skanowanie** i oba skanowania <= 7 dni
- **"/opt/iSEC/ENS/threatprevention/bin/isecav--listtask"** zwraca liczbę **dat i czas aktualizacji aparatu** , a oba z nich <= 7 dni
- **"/opt/iSEC/ENS/threatprevention/bin/isecav--getoasconfig--Summary"** zwraca stan **skanowania dostępu**

## <a name="sophos-antivirus-for-linux"></a>Sophos Antivirus dla systemu Linux 

Security Center zaleca się **"Zainstaluj rozwiązania programu Endpoint Protection na maszynie wirtualnej"** , gdy którykolwiek z następujących kontroli nie zostanie spełniony:
- **/Opt/Sophos-AV/bin/savdstatus** pliku lub wyszukiwanie dostosowanej lokalizacji **"readlink $ (savscan)"**
- **"/opt/Sophos-AV/bin/savdstatus--Version"** zwraca Sophos Name = **Sophos Anti-Virus i sophos w wersji >= 9**

Security Center zaleca **"Rozwiązywanie problemów z kondycją programu Endpoint Protection na swoich maszynach" w** przypadku braku spełnienia następujących testów:
- **"/opt/Sophos-AV/bin/savlog--maxAge = 7 | grep-i "zaplanowane skanowanie. \* Ukończono "| tail-1 "** zwraca wartość
- **"/opt/Sophos-AV/bin/savlog--maxAge = 7 | Zakończono skanowanie grep** "| tail-1 "zwraca wartość
- **"/opt/Sophos-AV/bin/savdstatus--LastUpdate"** zwraca LastUpdate, które powinny być <= 7 dni 
- **"/opt/Sophos-AV/bin/savdstatus-v"** jest równa **"skanowanie w trakcie dostępu jest uruchomione"** 
- **"/opt/Sophos-AV/bin/savconfig Get LiveProtection"** zwraca włączone

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dzienniki rozszerzeń programu Microsoft chroniące przed złośliwym kodem są dostępne pod adresem: **%SYSTEMDRIVE%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware (lub PaaSAntimalware) \1.5.5.x (wersja #) \CommandExecution.log**

### <a name="support"></a>Pomoc techniczna

Aby uzyskać pomoc, skontaktuj się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Lub plik zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj temat [Microsoft Azure support — często zadawane pytania](https://azure.microsoft.com/support/faq/).