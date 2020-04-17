---
title: Konfigurowanie urządzenia migracji platformy Azure za pomocą skryptu
description: Dowiedz się, jak skonfigurować urządzenie migracji platformy Azure za pomocą skryptu
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: faed7f96ea8c1850af5523d35f9f891011a48df8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537716"
---
# <a name="set-up-an-appliance-with-a-script"></a>Konfigurowanie urządzenia za pomocą skryptu

W tym artykule opisano sposób [konfigurowania urządzenia migracji platformy Azure](deploy-appliance.md) przy użyciu skryptu instalatora programu PowerShell dla maszyn wirtualnych VMware i maszyn wirtualnych funkcji Hyper-V. Jeśli chcesz skonfigurować urządzenie dla serwerów fizycznych, zapoznaj się [z tym artykułem](how-to-set-up-appliance-physical.md).


Urządzenie można wdrożyć przy użyciu kilku metod:


- Korzystanie z szablonu dla maszyn wirtualnych VMware (OVA) lub maszyn wirtualnych z użyciem funkcji Hyper-V (VHD).
- Korzystanie ze skryptu. Jest to metoda opisana w tym artykule. Skrypt zawiera:
    - Alternatywa dla konfigurowania urządzenia przy użyciu szablonu OVA, do oceny i bezagentowej migracji maszyn wirtualnych VMware.
    - Alternatywa dla konfigurowania urządzenia przy użyciu szablonu VHD do oceny i migracji maszyn wirtualnych funkcji Hyper-V.
    - Do oceny serwerów fizycznych (lub maszyn wirtualnych, które mają być migrowane jako serwery fizyczne), skrypt jest jedyną metodą konfigurowania urządzenia.
    - Sposób wdrażania urządzenia w usłudze Azure Dla Instytucji Rządowych.


Po utworzeniu urządzenia można sprawdzić, czy można połączyć się z usługą Azure Migrate. Następnie skonfiguruj urządzenie po raz pierwszy i zarejestruj go w projekcie migracji platformy Azure.


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
    - Przykład użycia w chmurze publicznej:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
    - Przykład użycia chmury rządów:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip```

3. Sprawdź wygenerowane wartości skrótu:

    - Dla chmury publicznej (dla najnowszej wersji urządzenia):

        **Algorytm** | **Wartość skrótu**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cdd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

    - Dla platformy Azure dla instytucji rządowych (dla najnowszej wersji urządzenia):

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
4. Uruchom skrypt **AzureMigrateInstaller.ps1**w następujący sposób:
    - Dla chmury publicznej:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
    - Dla platformy Azure dla instytucji rządowych:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>AzureMigrateInstaller.ps1 ```
   
5. Po pomyślnym uruchomieniu skryptu uruchamia aplikację internetową urządzenia, dzięki czemu można skonfigurować urządzenie. Jeśli wystąpią jakiekolwiek problemy, przejrzyj dzienniki skryptów w witrynie C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Weryfikowanie dostępu

Upewnij się, że urządzenie może łączyć się z adresami URL platformy Azure dla [publicznych](migrate-appliance.md#public-cloud-urls) i [chmur rządowych](migrate-appliance.md#government-cloud-urls.


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
    - Przykład użycia w chmurze publicznej:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
    - Przykład użycia chmury rządów:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip MD5```

3. Sprawdź wygenerowane wartości skrótu:

    - Dla chmury publicznej (dla najnowszej wersji urządzenia):

        **Algorytm** | **Wartość skrótu**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cdd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

    - Dla platformy Azure dla instytucji rządowych (dla najnowszej wersji urządzenia):

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
4. Uruchom skrypt **AzureMigrateInstaller.ps1**w następujący sposób:
    - Dla chmury publicznej:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
    - Dla platformy Azure dla instytucji rządowych:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>AzureMigrateInstaller.ps1 ```
   
5. Po pomyślnym uruchomieniu skryptu uruchamia aplikację internetową urządzenia, dzięki czemu można skonfigurować urządzenie. Jeśli wystąpią jakiekolwiek problemy, przejrzyj dzienniki skryptów w witrynie C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Weryfikowanie dostępu

Upewnij się, że urządzenie może łączyć się z adresami URL platformy Azure dla [publicznych](migrate-appliance.md#public-cloud-urls) i [chmur rządowych](migrate-appliance.md#government-cloud-urls.



## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o konfigurowaniu urządzenia za pomocą szablonu lub serwerów fizycznych, zapoznaj się z tymi artykułami:

- Skonfiguruj urządzenie dla [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Skonfiguruj urządzenie dla [funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Skonfiguruj urządzenie dla [serwerów fizycznych](how-to-set-up-appliance-physical.md).