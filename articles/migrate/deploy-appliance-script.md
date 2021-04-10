---
title: Skonfiguruj urządzenie Azure Migrate za pomocą skryptu
description: Dowiedz się, jak skonfigurować urządzenie Azure Migrate za pomocą skryptu
ms.topic: how-to
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: c78778f9152fd4c07fb9e550e562cfef858333c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786740"
---
# <a name="set-up-an-appliance-with-a-script"></a>Konfigurowanie urządzenia za pomocą skryptu

Postępuj zgodnie z tym artykułem, aby utworzyć [urządzenie Azure Migrate](./migrate-appliance-architecture.md) do oceny/migracji serwerów w oprogramowaniu VMware oraz funkcji Hyper-V. Uruchom skrypt, aby utworzyć urządzenie, i sprawdź, czy może nawiązać połączenie z platformą Azure. 

Urządzenie można wdrożyć na serwerach w oprogramowaniu VMware i w funkcji Hyper-V przy użyciu skryptu lub przy użyciu szablonu pobranego z Azure Portal. Użycie skryptu jest przydatne, jeśli nie można utworzyć urządzenia przy użyciu pobranego szablonu.

- Aby użyć szablonu, postępuj zgodnie z samouczkami dla programu [VMware](./tutorial-discover-vmware.md) lub [funkcji Hyper-V](./tutorial-discover-hyper-v.md).
- Aby skonfigurować urządzenie dla serwerów fizycznych, można użyć tylko skryptu. Postępuj zgodnie z [tym artykułem](how-to-set-up-appliance-physical.md).
- Aby skonfigurować urządzenie w chmurze Azure Government, postępuj zgodnie z [tym artykułem](deploy-appliance-script-government.md).

## <a name="prerequisites"></a>Wymagania wstępne

Skrypt konfiguruje urządzenie Azure Migrate na istniejącym serwerze.

- Serwer, który będzie pełnić funkcję urządzenia, musi spełniać następujące wymagania dotyczące sprzętu i systemu operacyjnego:

Scenariusz | Wymagania
--- | ---
VMware | Windows Server 2016 z 32 GB pamięci, osiem procesorów wirtualnych vCPU, około 80 GB miejsca na dysku
Hyper-V | Windows Server 2016, z 16 GB pamięci, osiem procesorów wirtualnych vCPU, około 80 GB miejsca na dysku

- Serwer wymaga również zewnętrznego przełącznika wirtualnego. Wymaga statycznego lub dynamicznego adresu IP oraz dostępu do Internetu.
- Przed wdrożeniem urządzenia zapoznaj się ze szczegółowymi wymaganiami dotyczącymi urządzeń dla [serwerów w oprogramowaniu VMware](migrate-appliance.md#appliance---vmware) [w funkcji Hyper-V](migrate-appliance.md#appliance---hyper-v).
- Nie uruchamiaj skryptu na istniejącym urządzeniu Azure Migrate.

## <a name="set-up-the-appliance-for-vmware"></a>Konfigurowanie urządzenia dla oprogramowania VMware

Aby skonfigurować urządzenie dla oprogramowania VMware, pobierz spakowany plik o nazwie AzureMigrateInstaller-Server-Public.zip z portalu lub z tego [miejsca](https://go.microsoft.com/fwlink/?linkid=2140334)i Wyodrębnij zawartość. Uruchom skrypt programu PowerShell, aby uruchomić aplikację sieci Web urządzenia. Należy skonfigurować urządzenie i skonfigurować je po raz pierwszy. Następnie należy zarejestrować urządzenie w programie Project.

### <a name="verify-file-security"></a>Weryfikuj zabezpieczenia plików

Przed wdrożeniem należy sprawdzić, czy spakowany plik jest bezpieczny.

1. Na serwerze, do którego pobrano plik, Otwórz okno polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla pliku spakowanego
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-Public.zip SHA256```
3. Sprawdź najnowszą wersję i skrypt dla chmury publicznej platformy Azure:

    **Algorytm** | **Pobieranie** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Najnowsza wersja](https://go.microsoft.com/fwlink/?linkid=2116601) | 85b74d93dfcee43412386141808d82147916330e6669df94c7969fe1b3d0fe72

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

1. Wyodrębnij spakowany plik do folderu na serwerze, który będzie hostować urządzenie. Upewnij się, że skrypt nie jest uruchamiany na istniejącym urządzeniu Azure Migrate.
2. Uruchom program PowerShell na serwerze z uprawnieniami administratora (z podwyższonym poziomem uprawnień).
3. Zmień katalog programu PowerShell w folder zawierający zawartość wyodrębnioną z pobranego pliku spakowanego.
4. Uruchom skrypt **AzureMigrateInstaller.ps1** w następujący sposób:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario VMware ```
  
5. Po pomyślnym uruchomieniu skryptu zostanie uruchomiona aplikacja sieci Web urządzenia, dzięki czemu można skonfigurować urządzenie. Jeśli napotkasz jakiekolwiek problemy, Przejrzyj dzienniki skryptów w C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Weryfikuj dostęp

Upewnij się, że urządzenie może połączyć się z adresami URL platformy Azure dla chmury [publicznej](migrate-appliance.md#public-cloud-urls) .

## <a name="set-up-the-appliance-for-hyper-v"></a>Konfigurowanie urządzenia dla funkcji Hyper-V

Aby skonfigurować urządzenie dla funkcji Hyper-V, pobierz spakowany plik o nazwie AzureMigrateInstaller-Server-Public.zip z portalu lub z tego [miejsca](https://go.microsoft.com/fwlink/?linkid=2105112)i Wyodrębnij zawartość. Uruchom skrypt programu PowerShell, aby uruchomić aplikację sieci Web urządzenia. Należy skonfigurować urządzenie i skonfigurować je po raz pierwszy. Następnie należy zarejestrować urządzenie w programie Project.


### <a name="verify-file-security"></a>Weryfikuj zabezpieczenia plików

Przed wdrożeniem należy sprawdzić, czy spakowany plik jest bezpieczny.

1. Na serwerze, do którego pobrano plik, Otwórz okno polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla pliku spakowanego
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-HyperV.zip SHA256```

3. Sprawdź najnowszą wersję i skrypt dla chmury publicznej platformy Azure:

    **Scenariusz** | **Pobieranie** | **SHA256**
    --- | --- | ---
    Funkcja Hyper-V (85,8 MB) | [Najnowsza wersja](https://go.microsoft.com/fwlink/?linkid=2116657) |  9bbef62e2e22481eda4b77c7fdf05db98c3767c20f0a873114fb0dcfa6ed682a

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

1. Wyodrębnij spakowany plik do folderu na serwerze, który będzie hostować urządzenie. Upewnij się, że skrypt nie jest uruchamiany na istniejącym urządzeniu Azure Migrate.
2. Uruchom program PowerShell na serwerze z uprawnieniami administratora (z podwyższonym poziomem uprawnień).
3. Zmień katalog programu PowerShell w folder zawierający zawartość wyodrębnioną z pobranego pliku spakowanego.
4. Uruchom skrypt **AzureMigrateInstaller.ps1** w następujący sposób:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Po pomyślnym uruchomieniu skryptu zostanie uruchomiona aplikacja sieci Web urządzenia, dzięki czemu można skonfigurować urządzenie. Jeśli napotkasz jakiekolwiek problemy, Przejrzyj dzienniki skryptów w C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Weryfikuj dostęp

Upewnij się, że urządzenie może połączyć się z adresami URL platformy Azure dla chmury [publicznej](migrate-appliance.md#public-cloud-urls) .

## <a name="next-steps"></a>Następne kroki

Po wdrożeniu urządzenia należy skonfigurować je po raz pierwszy i zarejestrować w programie Project.

- Skonfiguruj urządzenie dla programu [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance).
- Skonfiguruj urządzenie dla [funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).