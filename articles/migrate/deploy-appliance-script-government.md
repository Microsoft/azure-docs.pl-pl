---
title: Konfigurowanie urządzenia Azure Migrate w Azure Government
description: Dowiedz się, jak skonfigurować urządzenie Azure Migrate w Azure Government
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: b0bc2b69a4a1ec31cfa560d51920378fe1ab52b8
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714798"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Konfigurowanie urządzenia w Azure Government 

Postępuj zgodnie z tym artykułem, aby wdrożyć [urządzenie Azure Migrate dla](./migrate-appliance-architecture.md) serwerów w środowisku VMware, serwerów na funkcji Hyper-V i serwerów fizycznych w Azure Government chmurze. Uruchamiasz skrypt, aby utworzyć urządzenie i sprawdzić, czy może ono nawiązać połączenie z platformą Azure. Jeśli chcesz skonfigurować urządzenie w chmurze publicznej, postępuj zgodnie z [tym artykułem.](deploy-appliance-script.md)


> [!NOTE]
> Opcja wdrażania urządzenia przy użyciu szablonu (w przypadku serwerów w środowisku VMware i środowisku funkcji Hyper-V) nie jest obsługiwana w Azure Government.


## <a name="prerequisites"></a>Wymagania wstępne

Skrypt konfiguruje urządzenie Azure Migrate na istniejącym serwerze fizycznym lub na serwerze zwirtualizowanym.

- Na serwerze, który będzie działać jako urządzenie, musi działać system Windows Server 2016 z 32 GB pamięci, osiem procesorów wirtualnych, około 80 GB miejsca na dysku i zewnętrzny przełącznik wirtualny. Wymaga statycznego lub dynamicznego adresu IP. 
- Przed wdrożeniem urządzenia przejrzyj szczegółowe wymagania dotyczące urządzeń dla serwerów [vMware,](migrate-appliance.md#appliance---vmware)funkcji [Hyper-V](migrate-appliance.md#appliance---hyper-v)i [serwerów fizycznych.](migrate-appliance.md#appliance---physical)
- Nie uruchamiaj skryptu na istniejącym urządzeniu Azure Migrate chmurze.

## <a name="set-up-the-appliance-for-vmware"></a>Konfigurowanie urządzenia dla oprogramowania VMware

Aby skonfigurować urządzenie dla oprogramowania VMware, należy pobrać plik zip z Azure Portal i wyodrębnić jego zawartość. Uruchom skrypt programu PowerShell, aby uruchomić aplikację internetową urządzenia. Urządzenie jest konfigurowane i konfigurowane po raz pierwszy. Następnie zarejestruj urządzenie w projekcie.

### <a name="download-the-script"></a>Pobieranie skryptu

1. Na **stronie Cele migracji** systemu Windows, Linux i serwerów SQL  >    >  **Azure Migrate: odnajdywanie i ocena** kliknij pozycję **Odnajdz.**
2. Na **stronie Odnajdywanie serwera** Czy serwery są  >  **zwirtualizowane?** wybierz pozycję Tak, VMware vSphere funkcji **hypervisor.**
3. Kliknij **pozycję** Pobierz, aby pobrać plik zip.

### <a name="verify-file-security"></a>Weryfikowanie zabezpieczeń plików

Przed wdrożeniem pliku zip sprawdź, czy jest on bezpieczny.

1. Na serwerze, na który został pobrany plik, otwórz okno polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót pliku zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. Sprawdź najnowszą wersję urządzenia i wartość skrótu:

    **Algorytm** | **Pobieranie** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Najnowsza wersja](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca


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

1. Wyodrębnij plik zip do folderu na serwerze, który będzie hostować urządzenie. Upewnij się, że nie uruchamiasz skryptu na serwerze z istniejącym Azure Migrate urządzeniem.
2. Uruchom program PowerShell na serwerze z uprawnieniami administratora (podwyższonym poziomem uprawnień).
3. Zmień katalog programu PowerShell na folder zawierający zawartość wyodrębnioną z pobranego pliku zip.
4. Uruchom skrypt **AzureMigrateInstaller.ps1** w następujący sposób:
    
    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>.\AzureMigrateInstaller.ps1 ```
1. Po pomyślnym uruchomieniu skryptu zostanie uruchomiona aplikacja internetowa urządzenia, aby można było skonfigurować urządzenie. Jeśli wystąpią jakiekolwiek problemy, przejrzyj dzienniki skryptów w pliku C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Weryfikowanie dostępu

Upewnij się, że urządzenie może łączyć się z adresami URL platformy Azure w [chmurach dla instytucji rządowych.](migrate-appliance.md#government-cloud-urls)


## <a name="set-up-the-appliance-for-hyper-v"></a>Konfigurowanie urządzenia dla funkcji Hyper-V

Aby skonfigurować urządzenie dla funkcji Hyper-V, należy pobrać plik zip z Azure Portal i wyodrębnić jego zawartość. Uruchom skrypt programu PowerShell, aby uruchomić aplikację internetową urządzenia. Urządzenie jest konfigurowane i konfigurowane po raz pierwszy. Następnie zarejestruj urządzenie w projekcie.

### <a name="download-the-script"></a>Pobieranie skryptu

1.  Na **stronie Cele migracji** systemu Windows, Linux i serwerów SQL  >    >  **Azure Migrate: odnajdywanie i ocena** kliknij pozycję **Odnajdz.**
2.  W **odnajdywania**  >  **serwerów czy serwery są zwirtualizowane?**, wybierz opcję **Tak, z funkcji Hyper-V.**
3.  Kliknij **pozycję Pobierz,** aby pobrać plik zip. 


### <a name="verify-file-security"></a>Weryfikowanie zabezpieczeń plików

Przed wdrożeniem pliku zip sprawdź, czy jest on bezpieczny.

1. Na serwerze, na który został pobrany plik, otwórz okno polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót pliku zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. Sprawdź najnowszą wersję urządzenia i wartość skrótu:

    **Scenariusz** | **Pobieranie** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [Najnowsza wersja](https://go.microsoft.com/fwlink/?linkid=2140424) |  db5311de3d1d4a1167183a94e8347456db9c5749c7332ff2eb4b777798765e48

          

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

1. Wyodrębnij plik zip do folderu na serwerze, który będzie hostować urządzenie. Upewnij się, że nie uruchamiasz skryptu na serwerze z istniejącym Azure Migrate urządzeniem.
2. Uruchom program PowerShell na serwerze z uprawnieniami administratora (podwyższonym poziomem uprawnień).
3. Zmień katalog programu PowerShell na folder zawierający zawartość wyodrębnioną z pobranego pliku zip.
4. Uruchom skrypt **AzureMigrateInstaller.ps1** w następujący sposób: 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>.\AzureMigrateInstaller.ps1 ``` 
1. Po pomyślnym uruchomieniu skrypt uruchamia aplikację internetową urządzenia, aby można było skonfigurować urządzenie. Jeśli wystąpią jakiekolwiek problemy, przejrzyj dzienniki skryptów w pliku C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Weryfikowanie dostępu

Upewnij się, że urządzenie może łączyć się z adresami URL platformy Azure w [chmurach dla instytucji rządowych.](migrate-appliance.md#government-cloud-urls)


## <a name="set-up-the-appliance-for-physical-servers"></a>Konfigurowanie urządzenia dla serwerów fizycznych

Aby skonfigurować urządzenie dla oprogramowania VMware, należy pobrać plik zip z Azure Portal i wyodrębnić jego zawartość. Aby uruchomić aplikację internetową urządzenia, należy uruchomić skrypt programu PowerShell. Urządzenie jest konfigurowane i konfigurowane po raz pierwszy. Następnie zarejestruj urządzenie w projekcie.

### <a name="download-the-script"></a>Pobieranie skryptu

1. Na **stronie Cele migracji** systemu Windows, Linux i serwerów SQL  >    >  **Azure Migrate: odnajdywanie i ocena** kliknij pozycję **Odnajdz.**
2. Na **stronie Odnajdywanie serwerów** Czy serwery są  >  **zwirtualizowane?** wybierz pozycję Nie **zwirtualizowane/inne.**
3. Kliknij **pozycję Pobierz,** aby pobrać plik zip.

### <a name="verify-file-security"></a>Weryfikowanie zabezpieczeń plików

Przed wdrożeniem pliku zip sprawdź, czy jest on bezpieczny.

1. Na serwerach, na które pobrano plik, otwórz okno polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót pliku zip
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. Sprawdź najnowszą wersję urządzenia i wartość skrótu:

    **Scenariusz** | **Pobierz** _ | _ *Wartość skrótu**
    --- | --- | ---
    Fizyczne (85 MB) | [Najnowsza wersja](https://go.microsoft.com/fwlink/?linkid=2140338) | cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f
          

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

1. Wyodrębnij plik zip do folderu na serwerze, który będzie hostować urządzenie. Upewnij się, że nie uruchamiasz skryptu na serwerze z istniejącym Azure Migrate urządzeniem.
2. Uruchom program PowerShell na serwerze z uprawnieniami administratora (podwyższonym poziomem uprawnień).
3. Zmień katalog programu PowerShell na folder zawierający zawartość wyodrębnioną z pobranego pliku zip.
4. Uruchom skrypt **AzureMigrateInstaller.ps1** w następujący sposób:

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```
1. Po pomyślnym uruchomieniu skrypt uruchamia aplikację internetową urządzenia, aby można było skonfigurować urządzenie. Jeśli wystąpią jakiekolwiek problemy, przejrzyj dzienniki skryptów w pliku C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Weryfikowanie dostępu

Upewnij się, że urządzenie może łączyć się z adresami URL platformy Azure w [chmurach dla instytucji rządowych.](migrate-appliance.md#government-cloud-urls)

## <a name="next-steps"></a>Następne kroki

Po wdrożeniu urządzenia należy skonfigurować je po raz pierwszy i zarejestrować je w projekcie.

- Skonfiguruj urządzenie dla oprogramowania [VMware.](how-to-set-up-appliance-vmware.md#4-configure-the-appliance)
- Skonfiguruj urządzenie dla funkcji [Hyper-V.](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)
- Skonfiguruj urządzenie dla serwerów [fizycznych.](how-to-set-up-appliance-physical.md)
