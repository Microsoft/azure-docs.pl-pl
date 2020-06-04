---
title: Skonfiguruj urządzenie Azure Migrate za pomocą skryptu
description: Dowiedz się, jak skonfigurować urządzenie Azure Migrate za pomocą skryptu
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 20dbe4ba3b1b4858cb7022bb72129ee419ea2540
ms.sourcegitcommit: 79508e58c1f5c58554378497150ffd757d183f30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84331988"
---
# <a name="set-up-an-appliance-with-a-script"></a>Konfigurowanie urządzenia za pomocą skryptu

Postępuj zgodnie z tym artykułem, aby utworzyć [urządzenie Azure Migrate](deploy-appliance.md) do oceny/migracji maszyn wirtualnych VMware oraz maszyn wirtualnych funkcji Hyper-V. Uruchom skrypt, aby utworzyć urządzenie, i sprawdź, czy może nawiązać połączenie z platformą Azure. 

Możesz wdrożyć urządzenie dla maszyn wirtualnych VMware i funkcji Hyper-V za pomocą skryptu lub przy użyciu szablonu pobranego z Azure Portal. Użycie skryptu jest przydatne, jeśli nie można utworzyć maszyny wirtualnej przy użyciu pobranego szablonu.

- Aby użyć szablonu, postępuj zgodnie z samouczkami dla programu [VMware](tutorial-prepare-vmware.md) lub [funkcji Hyper-V](tutorial-prepare-hyper-v.md).
- Aby skonfigurować urządzenie dla serwerów fizycznych, można użyć tylko skryptu. Postępuj zgodnie z [tym artykułem](how-to-set-up-appliance-physical.md).
- Aby skonfigurować urządzenie w chmurze Azure Government, postępuj zgodnie z [tym artykułem](deploy-appliance-script-government.md).

## <a name="prerequisites"></a>Wymagania wstępne

Skrypt konfiguruje urządzenie Azure Migrate na istniejącym komputerze fizycznym lub maszynie wirtualnej.

- Na maszynie, która będzie działać jako urządzenie musi działać system Windows Server 2016 z 32 GB pamięci, osiem procesorów wirtualnych vCPU, około 80 GB miejsca na dysku i zewnętrznym przełączniku wirtualnym. Wymaga statycznego lub dynamicznego adresu IP oraz dostępu do Internetu.
- Przed wdrożeniem urządzenia zapoznaj się ze szczegółowymi wymaganiami dotyczącymi urządzeń dla [maszyn wirtualnych VMware](migrate-appliance.md#appliance---vmware), [maszyn wirtualnych funkcji Hyper-V](migrate-appliance.md#appliance---hyper-v)i [serwerów fizycznych](migrate-appliance.md#appliance---physical).
- Nie uruchamiaj skryptu na istniejącym urządzeniu Azure Migrate.

## <a name="set-up-the-appliance-for-vmware"></a>Konfigurowanie urządzenia dla oprogramowania VMware

Aby skonfigurować urządzenie dla oprogramowania VMware, pobierz spakowany plik z Azure Portal i Wyodrębnij jego zawartość. Uruchom skrypt programu PowerShell, aby uruchomić aplikację sieci Web urządzenia. Należy skonfigurować urządzenie i skonfigurować je po raz pierwszy. Następnie należy zarejestrować urządzenie w projekcie Azure Migrate.

### <a name="download-the-script"></a>Pobierz skrypt

1.  W obszarze serwery **celów migracji**  >  **Servers**  >  **Azure Migrate: Ocena serwera**, kliknij przycisk **odkryj**.
2.  W obszarze **odnajdywanie**maszyn  >  **są zwirtualizowane maszyny?** wybierz pozycję **tak, używając funkcji hypervisor programu VMware vSphere**.
3.  Kliknij pozycję **Pobierz**, aby pobrać plik zip. 


### <a name="verify-file-security"></a>Weryfikuj zabezpieczenia plików

Przed wdrożeniem należy sprawdzić, czy spakowany plik jest bezpieczny.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla pliku spakowanego
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
3. Sprawdź najnowszą wersję i skrypt dla chmury publicznej platformy Azure:

    **Algorytm** | **Pobierz** | **SHA256**
    --- | --- | ---
    VMware (10,9 GB) | [Najnowsza wersja](https://aka.ms/migrate/appliance/vmware) | cacbdaef927fe5477fa4e1f494fcb7203cbd6b6ce7402b79f234bc0fe69663dd



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
4. Uruchom skrypt **AzureMigrateInstaller. ps1**w następujący sposób:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. Po pomyślnym uruchomieniu skryptu zostanie uruchomiona aplikacja sieci Web urządzenia, dzięki czemu można skonfigurować urządzenie. Jeśli napotkasz jakiekolwiek problemy, Przejrzyj dzienniki skryptów w C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Weryfikuj dostęp

Upewnij się, że urządzenie może połączyć się z adresami URL platformy Azure dla chmury [publicznej](migrate-appliance.md#public-cloud-urls) .

## <a name="set-up-the-appliance-for-hyper-v"></a>Konfigurowanie urządzenia dla funkcji Hyper-V

Aby skonfigurować urządzenie dla funkcji Hyper-V, pobierz spakowany plik z Azure Portal i Wyodrębnij jego zawartość. Uruchom skrypt programu PowerShell, aby uruchomić aplikację sieci Web urządzenia. Należy skonfigurować urządzenie i skonfigurować je po raz pierwszy. Następnie należy zarejestrować urządzenie w projekcie Azure Migrate.

### <a name="download-the-script"></a>Pobierz skrypt

1.  W obszarze serwery **celów migracji**  >  **Servers**  >  **Azure Migrate: Ocena serwera**, kliknij przycisk **odkryj**.
2.  W obszarze **odnajdywanie**maszyn  >  **są zwirtualizowane maszyny?** wybierz pozycję **tak, używając funkcji Hyper-V**.
3.  Kliknij pozycję **Pobierz**, aby pobrać plik zip. 


### <a name="verify-file-security"></a>Weryfikuj zabezpieczenia plików

Przed wdrożeniem należy sprawdzić, czy spakowany plik jest bezpieczny.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla pliku spakowanego
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Sprawdź najnowszą wersję i skrypt dla chmury publicznej platformy Azure:

    **Scenariusz** | **Pobierz** | **SHA256**
    --- | --- | ---
    Funkcja Hyper-V (8,93 MB) | [Najnowsza wersja](https://aka.ms/migrate/appliance/hyperv) |  572be425ea0aca69a9aa8658c950bc319b2bdbeb93b440577264500091c846a1

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
4. Uruchom skrypt **AzureMigrateInstaller. ps1**w następujący sposób:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Po pomyślnym uruchomieniu skryptu zostanie uruchomiona aplikacja sieci Web urządzenia, dzięki czemu można skonfigurować urządzenie. Jeśli napotkasz jakiekolwiek problemy, Przejrzyj dzienniki skryptów w C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Weryfikuj dostęp

Upewnij się, że urządzenie może połączyć się z adresami URL platformy Azure dla chmury [publicznej](migrate-appliance.md#public-cloud-urls) .

## <a name="next-steps"></a>Następne kroki

Po wdrożeniu urządzenia należy skonfigurować je po raz pierwszy i zarejestrować w projekcie Azure Migrate.

- Skonfiguruj urządzenie dla programu [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Skonfiguruj urządzenie dla [funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
