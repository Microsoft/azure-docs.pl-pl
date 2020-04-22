---
title: Konfigurowanie urządzenia migracji platformy Azure w usłudze Azure dla instytucji rządowych
description: Dowiedz się, jak skonfigurować urządzenie migracji platformy Azure w usłudze Azure dla instytucji rządowych
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: fd97161ffa075a6165ea963ef80bfabf8904576e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726737"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Konfigurowanie urządzenia w usłudze Azure Government 

W tym [artykule](deploy-appliance.md) należy wdrożyć urządzenie migracji platformy Azure dla maszyn wirtualnych, maszyn wirtualnych funkcji Hyper-V i serwerów fizycznych w chmurze platformy Azure dla instytucji rządowych. Uruchom skrypt, aby utworzyć urządzenie i sprawdź, czy można połączyć się z platformą Azure. Jeśli chcesz skonfigurować urządzenie w chmurze publicznej, postępuj zgodnie z [tym artykułem](deploy-appliance-script.md).


> [!NOTE]
> Opcja wdrażania urządzenia przy użyciu szablonu (dla maszyn wirtualnych VMware i maszyn wirtualnych funkcji Hyper-V) nie jest obsługiwana w usłudze Azure Government.


## <a name="prerequisites"></a>Wymagania wstępne

Skrypt konfiguruje urządzenie migracji platformy Azure na istniejącym komputerze fizycznym lub maszynie wirtualnej.

- Komputer, który będzie działać jako urządzenie, musi być uruchomiony w systemie Windows Server 2016, z 32 GB pamięci, ośmioma procesorami wirtualnymi, około 80 GB pamięci masowej na dysku i zewnętrznym przełącznikiem wirtualnym. Wymaga statycznego lub dynamicznego adresu IP i dostępu do Internetu.
- Przed wdrożeniem urządzenia należy zapoznać się ze szczegółowymi wymaganiami dotyczącymi urządzeń [VMware vmware,](migrate-appliance.md#appliance---vmware) [maszyn wirtualnych hyper-V](migrate-appliance.md#appliance---hyper-v)i [serwerów fizycznych.](migrate-appliance.md#appliance---physical)
- Nie uruchamiaj skryptu na istniejącym urządzeniu migracji platformy Azure.

## <a name="set-up-the-appliance-for-vmware"></a>Konfigurowanie urządzenia dla VMware

Aby skonfigurować urządzenie dla VMware, pobierz spakowany plik z witryny Azure portal i wyodrębnisz zawartość. Uruchom skrypt programu PowerShell, aby uruchomić aplikację internetową urządzenia. Skonfiguruj urządzenie i konfiguruj go po raz pierwszy. Następnie zarejestruj urządzenie w projekcie migracji platformy Azure.

### <a name="download-the-script"></a>Pobierz skrypt

1.  W **obszarze** > Cele migracji**Serwery** > **usługi Azure Migrate: Ocena serwera**kliknij przycisk **Odkryj**.
2.  W **discover maszyny** > **Czy twoje maszyny są zwirtualizowane?**, wybierz **Tak, z VMWare vSphere hypervisor**.
3.  Kliknij **przycisk Pobierz**, aby pobrać spakowany plik. 


### <a name="verify-file-security"></a>Weryfikowanie zabezpieczeń plików

Przed wdrożeniem pliku jest bezpieczny, zanim go wdrożysz.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla spakowany plik
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip MD5```

3. Sprawdź wygenerowane wartości skrótu. Najnowsza wersja urządzenia:

    **Algorytm** | **Wartość skrótu**
    --- | ---
    MD5 | 6316bcc8bc932204295bfe33f4be3949
          

### <a name="run-the-script"></a>Uruchamianie skryptu

Oto, co robi skrypt:

- Instaluje agentów i aplikację sieci web.
- Instaluje role systemu Windows, w tym usługę aktywacji systemu Windows, usługi IIS i program PowerShell ISE.
- Pobiera i instaluje moduł wielokrotnego zapisu usługi IIS. [Dowiedz się więcej](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klucz rejestru (HKLM), z trwałymi ustawieniami dla migracji platformy Azure.
- Tworzy pliki dziennika i konfiguracji w następujący sposób:
    - **Pliki konfiguracyjne:**%ProgramData%\Microsoft Azure\Config
    - **Pliki dziennika:**%ProgramData%\Microsoft Azure\Logs

Aby uruchomić skrypt:

1. Wyodrębnij spakowany plik do folderu na komputerze, w który będzie obsługiwał urządzenie. Upewnij się, że skrypt nie jest uruchamiany na komputerze na istniejącym urządzeniu migracji platformy Azure.
2. Uruchom program PowerShell na komputerze z uprawnieniami administratora (podwyższone).
3. Zmień katalog programu PowerShell na folder zawierający zawartość wyodrębnione z pobranego pliku spakowane.
4. Uruchom skrypt **AzureMigrateInstaller.ps1**w następujący sposób:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>AzureMigrateInstaller.ps1 ```
5. Po pomyślnym uruchomieniu skryptu uruchamia aplikację internetową urządzenia, dzięki czemu można skonfigurować urządzenie. Jeśli wystąpią jakiekolwiek problemy, przejrzyj dzienniki skryptów w witrynie C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Weryfikowanie dostępu

Upewnij się, że urządzenie może łączyć się z adresami URL platformy Azure dla [chmur rządowych](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-hyper-v"></a>Konfigurowanie urządzenia dla funkcji Hyper-V

Aby skonfigurować urządzenie dla funkcji Hyper-V, pobierz spakowany plik z witryny Azure portal i wyodrębnisz zawartość. Uruchom skrypt programu PowerShell, aby uruchomić aplikację internetową urządzenia. Skonfiguruj urządzenie i konfiguruj go po raz pierwszy. Następnie zarejestruj urządzenie w projekcie migracji platformy Azure.

### <a name="download-the-script"></a>Pobierz skrypt

1.  W **obszarze** > Cele migracji**Serwery** > **usługi Azure Migrate: Ocena serwera**kliknij przycisk **Odkryj**.
2.  W **discover maszyny** > **Czy twoje maszyny są zwirtualizowane?**, wybierz **Tak, z Hyper-V**.
3.  Kliknij **przycisk Pobierz**, aby pobrać spakowany plik. 


### <a name="verify-file-security"></a>Weryfikowanie zabezpieczeń plików

Przed wdrożeniem pliku jest bezpieczny, zanim go wdrożysz.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla spakowany plik
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip MD5```

3. Sprawdź wygenerowane wartości skrótu. Najnowsza wersja urządzenia:

    **Algorytm** | **Wartość skrótu**
    --- | ---
    MD5 | 717f8b9185f565006b5aff0215ecadac
          

### <a name="run-the-script"></a>Uruchamianie skryptu

Oto, co robi skrypt:

- Instaluje agentów i aplikację sieci web.
- Instaluje role systemu Windows, w tym usługę aktywacji systemu Windows, usługi IIS i program PowerShell ISE.
- Pobiera i instaluje moduł wielokrotnego zapisu usługi IIS. [Dowiedz się więcej](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klucz rejestru (HKLM), z trwałymi ustawieniami dla migracji platformy Azure.
- Tworzy pliki dziennika i konfiguracji w następujący sposób:
    - **Pliki konfiguracyjne:**%ProgramData%\Microsoft Azure\Config
    - **Pliki dziennika:**%ProgramData%\Microsoft Azure\Logs

Aby uruchomić skrypt:

1. Wyodrębnij spakowany plik do folderu na komputerze, w który będzie obsługiwał urządzenie. Upewnij się, że skrypt nie jest uruchamiany na komputerze na istniejącym urządzeniu migracji platformy Azure.
2. Uruchom program PowerShell na komputerze z uprawnieniami administratora (podwyższone).
3. Zmień katalog programu PowerShell na folder zawierający zawartość wyodrębnione z pobranego pliku spakowane.
4. Uruchom skrypt **AzureMigrateInstaller.ps1**w następujący sposób:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>AzureMigrateInstaller.ps1 ``` 
5. Po pomyślnym uruchomieniu skryptu uruchamia aplikację internetową urządzenia, dzięki czemu można skonfigurować urządzenie. Jeśli wystąpią jakiekolwiek problemy, przejrzyj dzienniki skryptów w witrynie C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Weryfikowanie dostępu

Upewnij się, że urządzenie może łączyć się z adresami URL platformy Azure dla [chmur rządowych](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-physical-servers"></a>Konfigurowanie urządzenia dla serwerów fizycznych

Aby skonfigurować urządzenie dla VMware, pobierz spakowany plik z witryny Azure portal i wyodrębnisz zawartość. Uruchom skrypt programu PowerShell, aby uruchomić aplikację internetową urządzenia. Skonfiguruj urządzenie i konfiguruj go po raz pierwszy. Następnie zarejestruj urządzenie w projekcie migracji platformy Azure.

### <a name="download-the-script"></a>Pobierz skrypt

1.  W **obszarze** > Cele migracji**Serwery** > **usługi Azure Migrate: Ocena serwera**kliknij przycisk **Odkryj**.
2.  W **Discover maszyny** > **Czy twoje maszyny są zwirtualizowane?**, wybierz nie **zwirtualizowany/Inne**.
3.  Kliknij **przycisk Pobierz**, aby pobrać spakowany plik. 


### <a name="verify-file-security"></a>Weryfikowanie zabezpieczeń plików

Przed wdrożeniem pliku jest bezpieczny, zanim go wdrożysz.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla spakowany plik
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5```

3. Sprawdź wygenerowane wartości skrótu. Najnowsza wersja urządzenia:

    **Algorytm** | **Wartość skrótu**
    --- | ---
    MD5 | f81c155fc4a1409901caea948713913f
          

### <a name="run-the-script"></a>Uruchamianie skryptu

Oto, co robi skrypt:

- Instaluje agentów i aplikację sieci web.
- Instaluje role systemu Windows, w tym usługę aktywacji systemu Windows, usługi IIS i program PowerShell ISE.
- Pobiera i instaluje moduł wielokrotnego zapisu usługi IIS. [Dowiedz się więcej](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klucz rejestru (HKLM), z trwałymi ustawieniami dla migracji platformy Azure.
- Tworzy pliki dziennika i konfiguracji w następujący sposób:
    - **Pliki konfiguracyjne:**%ProgramData%\Microsoft Azure\Config
    - **Pliki dziennika:**%ProgramData%\Microsoft Azure\Logs

Aby uruchomić skrypt:

1. Wyodrębnij spakowany plik do folderu na komputerze, w który będzie obsługiwał urządzenie. Upewnij się, że skrypt nie jest uruchamiany na komputerze na istniejącym urządzeniu migracji platformy Azure.
2. Uruchom program PowerShell na komputerze z uprawnieniami administratora (podwyższone).
3. Zmień katalog programu PowerShell na folder zawierający zawartość wyodrębnione z pobranego pliku spakowane.
4. Uruchom skrypt **AzureMigrateInstaller.ps1**w następujący sposób:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```
5. Po pomyślnym uruchomieniu skryptu uruchamia aplikację internetową urządzenia, dzięki czemu można skonfigurować urządzenie. Jeśli wystąpią jakiekolwiek problemy, przejrzyj dzienniki skryptów w witrynie C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Weryfikowanie dostępu

Upewnij się, że urządzenie może łączyć się z adresami URL platformy Azure dla [chmur rządowych](migrate-appliance.md#government-cloud-urls).

## <a name="next-steps"></a>Następne kroki

Po wdrożeniu urządzenia należy skonfigurować go po raz pierwszy i zarejestrować go w projekcie migracji platformy Azure.

- Skonfiguruj urządzenie dla [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Skonfiguruj urządzenie dla [funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Skonfiguruj urządzenie dla [serwerów fizycznych](how-to-set-up-appliance-physical.md).