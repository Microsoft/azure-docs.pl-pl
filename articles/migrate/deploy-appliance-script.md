---
title: Konfigurowanie urządzenia Azure Migrate za pomocą skryptu
description: Dowiedz się, jak skonfigurować urządzenie Azure Migrate za pomocą skryptu
ms.topic: how-to
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: ff05a01ad8173923ff614657d0231f743f38ba1c
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714762"
---
# <a name="set-up-an-appliance-with-a-script"></a>Konfigurowanie urządzenia za pomocą skryptu

Postępuj zgodnie z tym artykułem, [Azure Migrate urządzenie do](./migrate-appliance-architecture.md) oceny/migracji serwerów w programie VMware i funkcji Hyper-V. Uruchamiasz skrypt, aby utworzyć urządzenie i sprawdzić, czy może ono nawiązać połączenie z platformą Azure. 

Urządzenie można wdrożyć dla serwerów w programie VMware i funkcji Hyper-V przy użyciu skryptu lub szablonu, który można pobrać z Azure Portal. Użycie skryptu jest przydatne, jeśli nie możesz utworzyć urządzenia przy użyciu pobranego szablonu.

- Aby użyć szablonu, postępuj zgodnie z samouczkami dla programu [VMware](./tutorial-discover-vmware.md) lub [funkcji Hyper-V.](./tutorial-discover-hyper-v.md)
- Aby skonfigurować urządzenie dla serwerów fizycznych, można użyć tylko skryptu. Postępuj zgodnie [z tym artykułem.](how-to-set-up-appliance-physical.md)
- Aby skonfigurować urządzenie w chmurze Azure Government, postępuj zgodnie [z tym artykułem.](deploy-appliance-script-government.md)

## <a name="prerequisites"></a>Wymagania wstępne

Skrypt konfiguruje urządzenie Azure Migrate na istniejącym serwerze.

- Serwer, który będzie działać jako urządzenie, musi spełniać następujące wymagania dotyczące sprzętu i systemu operacyjnego:

Scenariusz | Wymagania
--- | ---
VMware | Windows Server 2016 z 32 GB pamięci, osiem procesorów wirtualnych, około 80 GB miejsca na dysku
Hyper-V | Windows Server 2016 z 16 GB pamięci, osiem procesorów wirtualnych, około 80 GB miejsca na dysku

- Serwer wymaga również zewnętrznego przełącznika wirtualnego. Wymaga statycznego lub dynamicznego adresu IP. 
- Przed wdrożeniem urządzenia przejrzyj szczegółowe wymagania dotyczące urządzeń dla serwerów [vMware](migrate-appliance.md#appliance---vmware)w [funkcji Hyper-V.](migrate-appliance.md#appliance---hyper-v)
- Nie uruchamiaj skryptu na istniejącym urządzeniu Azure Migrate chmurze.

## <a name="set-up-the-appliance-for-vmware"></a>Konfigurowanie urządzenia dla oprogramowania VMware

Aby skonfigurować urządzenie dla oprogramowania VMware, należy pobrać plik zip o nazwie [](https://go.microsoft.com/fwlink/?linkid=2140334)AzureMigrateInstaller-Server-Public.zip z portalu lub tutaj i wyodrębnić zawartość. Aby uruchomić aplikację internetową urządzenia, należy uruchomić skrypt programu PowerShell. Urządzenie jest konfigurowane i konfigurowane po raz pierwszy. Następnie zarejestruj urządzenie w projekcie.

### <a name="verify-file-security"></a>Weryfikowanie zabezpieczeń plików

Przed wdrożeniem pliku zip sprawdź, czy jest on bezpieczny.

1. Na serwerze, na który został pobrany plik, otwórz okno polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót pliku zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-Public.zip SHA256```
3. Sprawdź najnowszą wersję urządzenia i skrypt dla chmury publicznej Azure:

    **Algorytm** | **Pobieranie** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Najnowsza wersja](https://go.microsoft.com/fwlink/?linkid=2116601) | 85b74d93dfcee43412386141808d82147916330e6669df94c7969fe1b3d0fe72

### <a name="run-the-script"></a>Uruchamianie skryptu

Oto, co robi skrypt:

- Instaluje agentów i aplikację internetową.
- Instaluje role systemu Windows, w tym usługę aktywacji systemu Windows, usługi IIS i program PowerShell ISE.
- Pobiera i instaluje moduł Zamiejscowy do ponownego instalowania usług IIS. [Dowiedz się więcej](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klucz rejestru (HKLM) za pomocą trwałych ustawień Azure Migrate.
- Tworzy pliki dziennika i konfiguracji w następujący sposób:
    - **Pliki konfiguracji:**%ProgramData%\Microsoft Azure\Config
    - **Pliki dziennika:**%ProgramData%\Microsoft Azure\Logs

Aby uruchomić skrypt:

1. Wyodrębnij plik zip do folderu na serwerze, który będzie hostować urządzenie. Upewnij się, że nie uruchamiasz skryptu na istniejącym Azure Migrate urządzeniu.
2. Uruchom program PowerShell na serwerze z uprawnieniami administratora (z podwyższonym poziomem uprawnień).
3. Zmień katalog programu PowerShell na folder zawierający zawartość wyodrębnioną z pobranego pliku zip.
4. Uruchom skrypt **AzureMigrateInstaller.ps1** w następujący sposób:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario VMware ```
  
5. Po pomyślnym uruchomieniu skryptu zostanie uruchomiona aplikacja internetowa urządzenia, aby można było skonfigurować urządzenie. Jeśli wystąpią jakiekolwiek problemy, przejrzyj dzienniki skryptów w pliku C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Weryfikowanie dostępu

Upewnij się, że urządzenie może łączyć się z adresami URL platformy Azure dla [chmury publicznej.](migrate-appliance.md#public-cloud-urls)

## <a name="set-up-the-appliance-for-hyper-v"></a>Konfigurowanie urządzenia dla funkcji Hyper-V

Aby skonfigurować urządzenie dla funkcji Hyper-V, należy pobrać plik zip o nazwie [](https://go.microsoft.com/fwlink/?linkid=2105112)AzureMigrateInstaller-Server-Public.zip z portalu lub tutaj i wyodrębnić jego zawartość. Uruchom skrypt programu PowerShell, aby uruchomić aplikację internetową urządzenia. Urządzenie jest konfigurowane i konfigurowane po raz pierwszy. Następnie zarejestruj urządzenie w projekcie.


### <a name="verify-file-security"></a>Weryfikowanie zabezpieczeń plików

Przed wdrożeniem pliku zip sprawdź, czy jest on bezpieczny.

1. Na serwerze, na który został pobrany plik, otwórz okno polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót pliku zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-HyperV.zip SHA256```

3. Sprawdź najnowszą wersję urządzenia i skrypt dla chmury publicznej platformy Azure:

    **Scenariusz** | **Pobieranie** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [Najnowsza wersja](https://go.microsoft.com/fwlink/?linkid=2116657) |  9bbef62e2e22481eda4b77c7fdf05db98c3767c20f0a873114fb0dcfa6ed682a

### <a name="run-the-script"></a>Uruchamianie skryptu

Oto, co robi skrypt:

- Instaluje agentów i aplikację internetową.
- Instaluje role systemu Windows, w tym usługę aktywacji systemu Windows, usługi IIS i program PowerShell ISE.
- Pobiera i instaluje moduł Zamiejscowy do ponownego instalowania usług IIS. [Dowiedz się więcej](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klucz rejestru (HKLM) za pomocą trwałych ustawień Azure Migrate.
- Tworzy pliki dziennika i konfiguracji w następujący sposób:
    - **Pliki konfiguracji:**%ProgramData%\Microsoft Azure\Config
    - **Pliki dziennika:**%ProgramData%\Microsoft Azure\Logs

Aby uruchomić skrypt:

1. Wyodrębnij plik zip do folderu na serwerze, który będzie hostować urządzenie. Upewnij się, że nie uruchamiasz skryptu na istniejącym Azure Migrate urządzeniu.
2. Uruchom program PowerShell na serwerze z uprawnieniami administratora (z podwyższonym poziomem uprawnień).
3. Zmień katalog programu PowerShell na folder zawierający zawartość wyodrębnioną z pobranego pliku zip.
4. Uruchom skrypt **AzureMigrateInstaller.ps1** w następujący sposób:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Po pomyślnym uruchomieniu skrypt uruchamia aplikację internetową urządzenia, aby można było skonfigurować urządzenie. Jeśli wystąpią jakiekolwiek problemy, przejrzyj dzienniki skryptów w pliku C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Weryfikowanie dostępu

Upewnij się, że urządzenie może łączyć się z adresami URL platformy Azure dla [chmury publicznej.](migrate-appliance.md#public-cloud-urls)

## <a name="next-steps"></a>Następne kroki

Po wdrożeniu urządzenia należy je skonfigurować po raz pierwszy i zarejestrować je w projekcie.

- Skonfiguruj urządzenie dla oprogramowania [VMware.](how-to-set-up-appliance-vmware.md#4-configure-the-appliance)
- Skonfiguruj urządzenie dla funkcji [Hyper-V.](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)