---
title: Skonfiguruj urządzenie Azure Migrate w Azure Government
description: Dowiedz się, jak skonfigurować urządzenie Azure Migrate w Azure Government
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: fd97161ffa075a6165ea963ef80bfabf8904576e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81726737"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Konfigurowanie urządzenia w Azure Government 

Postępuj zgodnie z tym artykułem, aby wdrożyć [urządzenie Azure Migrate](deploy-appliance.md) dla maszyn wirtualnych VMware, maszyn wirtualnych funkcji Hyper-V i serwerów fizycznych w chmurze Azure Government. Uruchom skrypt, aby utworzyć urządzenie, i sprawdź, czy może nawiązać połączenie z platformą Azure. Jeśli chcesz skonfigurować urządzenie w chmurze publicznej, postępuj zgodnie z [tym artykułem](deploy-appliance-script.md).


> [!NOTE]
> Opcja wdrożenia urządzenia przy użyciu szablonu (dla maszyn wirtualnych VMware i maszyn wirtualnych funkcji Hyper-V) nie jest obsługiwana w Azure Government.


## <a name="prerequisites"></a>Wymagania wstępne

Skrypt konfiguruje urządzenie Azure Migrate na istniejącym komputerze fizycznym lub maszynie wirtualnej.

- Na maszynie, która będzie działać jako urządzenie musi działać system Windows Server 2016 z 32 GB pamięci, osiem procesorów wirtualnych vCPU, około 80 GB miejsca na dysku i zewnętrznym przełączniku wirtualnym. Wymaga statycznego lub dynamicznego adresu IP oraz dostępu do Internetu.
- Przed wdrożeniem urządzenia zapoznaj się ze szczegółowymi wymaganiami dotyczącymi urządzeń dla [maszyn wirtualnych VMware](migrate-appliance.md#appliance---vmware), [maszyn wirtualnych funkcji Hyper-V](migrate-appliance.md#appliance---hyper-v)i [serwerów fizycznych](migrate-appliance.md#appliance---physical).
- Nie uruchamiaj skryptu na istniejącym urządzeniu Azure Migrate.

## <a name="set-up-the-appliance-for-vmware"></a>Konfigurowanie urządzenia dla oprogramowania VMware

Aby skonfigurować urządzenie dla oprogramowania VMware, pobierz spakowany plik z Azure Portal i Wyodrębnij jego zawartość. Uruchom skrypt programu PowerShell, aby uruchomić aplikację sieci Web urządzenia. Należy skonfigurować urządzenie i skonfigurować je po raz pierwszy. Następnie należy zarejestrować urządzenie w projekcie Azure Migrate.

### <a name="download-the-script"></a>Pobierz skrypt

1.  W obszarze**serwery** >  **celów** > migracji**Azure Migrate: Ocena serwera**, kliknij przycisk **odkryj**.
2.  W obszarze **odnajdywanie** > maszyn**są zwirtualizowane maszyny?** wybierz pozycję **tak, używając funkcji hypervisor programu VMware vSphere**.
3.  Kliknij pozycję **Pobierz**, aby pobrać plik zip. 


### <a name="verify-file-security"></a>Weryfikuj zabezpieczenia plików

Przed wdrożeniem należy sprawdzić, czy spakowany plik jest bezpieczny.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla pliku spakowanego
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip MD5```

3. Sprawdź wygenerowane wartości skrótu. W przypadku najnowszej wersji urządzenia:

    **Algorytm** | **Wartość skrótu**
    --- | ---
    MD5 | 6316bcc8bc932204295bfe33f4be3949
          

### <a name="run-the-script"></a>Uruchamianie skryptu

Oto co robi skrypt:

- Instaluje agentów i aplikację sieci Web.
- Instaluje role systemu Windows, w tym usługi aktywacji systemu Windows, usług IIS i programu PowerShell ISE.
- Pobiera i instaluje moduł ponownie zapisywalny usług IIS. [Dowiedz się więcej](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klucz rejestru (HKLM) przy użyciu ustawień trwałych dla Azure Migrate.
- Tworzy pliki dzienników i konfiguracji w następujący sposób:
    - **Pliki konfiguracji**:%ProgramData%\Microsoft Azure\Config
    - **Pliki dziennika**:%ProgramData%\Microsoft Azure\Logs

Aby uruchomić skrypt:

1. Wyodrębnij spakowany plik do folderu na komputerze, który będzie hostować urządzenie. Upewnij się, że skrypt nie jest uruchamiany na komputerze na istniejącym urządzeniu Azure Migrate.
2. Uruchom program PowerShell na komputerze z uprawnieniami administratora (z podwyższonym poziomem uprawnień).
3. Zmień katalog programu PowerShell w folder zawierający zawartość wyodrębnioną z pobranego pliku spakowanego.
4. Uruchom skrypt **AzureMigrateInstaller. ps1**w następujący sposób:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>AzureMigrateInstaller.ps1 ```
5. Po pomyślnym uruchomieniu skryptu zostanie uruchomiona aplikacja sieci Web urządzenia, dzięki czemu można skonfigurować urządzenie. Jeśli napotkasz jakiekolwiek problemy, Przejrzyj dzienniki skryptów w C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Weryfikuj dostęp

Upewnij się, że urządzenie może połączyć się z adresami URL platformy Azure dla [chmur dla instytucji rządowych](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-hyper-v"></a>Konfigurowanie urządzenia dla funkcji Hyper-V

Aby skonfigurować urządzenie dla funkcji Hyper-V, pobierz spakowany plik z Azure Portal i Wyodrębnij jego zawartość. Uruchom skrypt programu PowerShell, aby uruchomić aplikację sieci Web urządzenia. Należy skonfigurować urządzenie i skonfigurować je po raz pierwszy. Następnie należy zarejestrować urządzenie w projekcie Azure Migrate.

### <a name="download-the-script"></a>Pobierz skrypt

1.  W obszarze**serwery** >  **celów** > migracji**Azure Migrate: Ocena serwera**, kliknij przycisk **odkryj**.
2.  W obszarze **odnajdywanie** > maszyn**są zwirtualizowane maszyny?** wybierz pozycję **tak, używając funkcji Hyper-V**.
3.  Kliknij pozycję **Pobierz**, aby pobrać plik zip. 


### <a name="verify-file-security"></a>Weryfikuj zabezpieczenia plików

Przed wdrożeniem należy sprawdzić, czy spakowany plik jest bezpieczny.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla pliku spakowanego
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip MD5```

3. Sprawdź wygenerowane wartości skrótu. W przypadku najnowszej wersji urządzenia:

    **Algorytm** | **Wartość skrótu**
    --- | ---
    MD5 | 717f8b9185f565006b5aff0215ecadac
          

### <a name="run-the-script"></a>Uruchamianie skryptu

Oto co robi skrypt:

- Instaluje agentów i aplikację sieci Web.
- Instaluje role systemu Windows, w tym usługi aktywacji systemu Windows, usług IIS i programu PowerShell ISE.
- Pobiera i instaluje moduł ponownie zapisywalny usług IIS. [Dowiedz się więcej](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klucz rejestru (HKLM) przy użyciu ustawień trwałych dla Azure Migrate.
- Tworzy pliki dzienników i konfiguracji w następujący sposób:
    - **Pliki konfiguracji**:%ProgramData%\Microsoft Azure\Config
    - **Pliki dziennika**:%ProgramData%\Microsoft Azure\Logs

Aby uruchomić skrypt:

1. Wyodrębnij spakowany plik do folderu na komputerze, który będzie hostować urządzenie. Upewnij się, że skrypt nie jest uruchamiany na komputerze na istniejącym urządzeniu Azure Migrate.
2. Uruchom program PowerShell na komputerze z uprawnieniami administratora (z podwyższonym poziomem uprawnień).
3. Zmień katalog programu PowerShell w folder zawierający zawartość wyodrębnioną z pobranego pliku spakowanego.
4. Uruchom skrypt **AzureMigrateInstaller. ps1**w następujący sposób:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>AzureMigrateInstaller.ps1 ``` 
5. Po pomyślnym uruchomieniu skryptu zostanie uruchomiona aplikacja sieci Web urządzenia, dzięki czemu można skonfigurować urządzenie. Jeśli napotkasz jakiekolwiek problemy, Przejrzyj dzienniki skryptów w C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Weryfikuj dostęp

Upewnij się, że urządzenie może połączyć się z adresami URL platformy Azure dla [chmur dla instytucji rządowych](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-physical-servers"></a>Konfigurowanie urządzenia dla serwerów fizycznych

Aby skonfigurować urządzenie dla oprogramowania VMware, pobierz spakowany plik z Azure Portal i Wyodrębnij jego zawartość. Uruchom skrypt programu PowerShell, aby uruchomić aplikację sieci Web urządzenia. Należy skonfigurować urządzenie i skonfigurować je po raz pierwszy. Następnie należy zarejestrować urządzenie w projekcie Azure Migrate.

### <a name="download-the-script"></a>Pobierz skrypt

1.  W obszarze**serwery** >  **celów** > migracji**Azure Migrate: Ocena serwera**, kliknij przycisk **odkryj**.
2.  W obszarze **odnajdywanie** > maszyn**są zwirtualizowane maszyny?** wybierz opcję **niezwirtualizowane/inne**.
3.  Kliknij pozycję **Pobierz**, aby pobrać plik zip. 


### <a name="verify-file-security"></a>Weryfikuj zabezpieczenia plików

Przed wdrożeniem należy sprawdzić, czy spakowany plik jest bezpieczny.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla pliku spakowanego
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5```

3. Sprawdź wygenerowane wartości skrótu. W przypadku najnowszej wersji urządzenia:

    **Algorytm** | **Wartość skrótu**
    --- | ---
    MD5 | f81c155fc4a1409901caea948713913f
          

### <a name="run-the-script"></a>Uruchamianie skryptu

Oto co robi skrypt:

- Instaluje agentów i aplikację sieci Web.
- Instaluje role systemu Windows, w tym usługi aktywacji systemu Windows, usług IIS i programu PowerShell ISE.
- Pobiera i instaluje moduł ponownie zapisywalny usług IIS. [Dowiedz się więcej](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klucz rejestru (HKLM) przy użyciu ustawień trwałych dla Azure Migrate.
- Tworzy pliki dzienników i konfiguracji w następujący sposób:
    - **Pliki konfiguracji**:%ProgramData%\Microsoft Azure\Config
    - **Pliki dziennika**:%ProgramData%\Microsoft Azure\Logs

Aby uruchomić skrypt:

1. Wyodrębnij spakowany plik do folderu na komputerze, który będzie hostować urządzenie. Upewnij się, że skrypt nie jest uruchamiany na komputerze na istniejącym urządzeniu Azure Migrate.
2. Uruchom program PowerShell na komputerze z uprawnieniami administratora (z podwyższonym poziomem uprawnień).
3. Zmień katalog programu PowerShell w folder zawierający zawartość wyodrębnioną z pobranego pliku spakowanego.
4. Uruchom skrypt **AzureMigrateInstaller. ps1**w następujący sposób:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```
5. Po pomyślnym uruchomieniu skryptu zostanie uruchomiona aplikacja sieci Web urządzenia, dzięki czemu można skonfigurować urządzenie. Jeśli napotkasz jakiekolwiek problemy, Przejrzyj dzienniki skryptów w C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Weryfikuj dostęp

Upewnij się, że urządzenie może połączyć się z adresami URL platformy Azure dla [chmur dla instytucji rządowych](migrate-appliance.md#government-cloud-urls).

## <a name="next-steps"></a>Następne kroki

Po wdrożeniu urządzenia należy skonfigurować je po raz pierwszy i zarejestrować w projekcie Azure Migrate.

- Skonfiguruj urządzenie dla programu [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Skonfiguruj urządzenie dla [funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Skonfiguruj urządzenie dla [serwerów fizycznych](how-to-set-up-appliance-physical.md).