---
title: Skonfiguruj urządzenie Azure Migrate skalowalne w poziomie na potrzeby migracji oprogramowania VMware bez agentów
description: Dowiedz się, jak skonfigurować urządzenie Azure Migrate skalowalne w poziomie w celu migrowania maszyn wirtualnych funkcji Hyper-V.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 634eb2d22e3fa570ac9412d4fb8afd917b5c2eaa
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564011"
---
# <a name="scale-agentless-migration-of-vmware-virtual-machines-to-azure"></a>Skalowanie migracji maszyn wirtualnych VMware na platformę Azure bez wykorzystania agentów

Ten artykuł pomaga zrozumieć, jak korzystać z urządzenia skalowalnego w poziomie w celu migrowania dużej liczby maszyn wirtualnych VMware na platformę Azure przy użyciu metody bezagentowej narzędzia migracji Azure Migrate Server na potrzeby migracji maszyn wirtualnych VMware.

Korzystając z metody migracji bez agenta dla maszyn wirtualnych VMware, możesz:

- Replikacja do 300 maszyn wirtualnych z jednego serwera vCenter jednocześnie przy użyciu jednego Azure Migrate urządzenia.
- Replikacja do 500 maszyn wirtualnych z jednego serwera vCenter współbieżnie przez wdrożenie drugiego urządzenia skalowalnego w poziomie na potrzeby migracji.

W tym artykule dowiesz się, jak:

- Dodawanie urządzenia skalowalnego w poziomie w celu migrowania maszyn wirtualnych VMware bez agentów
- Przeprowadź migrację do 500 maszyn wirtualnych współbieżnie przy użyciu urządzenia skalowalnego w poziomie.

##  <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy wykonać następujące czynności:

- Utwórz projekt Azure Migrate.
- Wdróż urządzenie Azure Migrate (urządzenie podstawowe) i pełne odnajdowanie maszyn wirtualnych VMware zarządzanych przez serwer vCenter.
- Skonfiguruj replikację dla co najmniej jednej maszyny wirtualnej, która ma zostać zmigrowana.
> [!IMPORTANT]
> Aby można było dodać do migracji urządzenie skalowalne w poziomie, musisz mieć co najmniej jedną replikację maszyny wirtualnej w projekcie.

Aby dowiedzieć się, jak wykonać powyższe instrukcje, zapoznaj się z samouczkiem [Migrowanie maszyn wirtualnych VMware na platformę Azure przy użyciu metody migracji bez wykorzystania agentów](./tutorial-migrate-vmware.md).

## <a name="deploy-a-scale-out-appliance"></a>Wdrażanie urządzenia skalowalnego w poziomie

Aby dodać urządzenie skalowalne w poziomie, wykonaj czynności opisane poniżej:

1. Klikniesz pozycję **odnajdywanie**  >  **czy maszyny są zwirtualizowane?** 
1. Wybierz opcję **tak, za pomocą funkcji hypervisor VMware VSphere.**
1. W następnym kroku wybierz opcję replikacja bez wykorzystania agentów.
1. Wybierz pozycję **Skaluj w poziomie istniejące urządzenie podstawowe** w menu wybierz typ urządzenia.
1. Wybierz urządzenie podstawowe (urządzenie, za pomocą którego zostało wykonane odnajdywanie), które chcesz skalować w poziomie.

:::image type="content" source="./media/how-to-scale-out-for-migration/add-scale-out.png" alt-text="Strona odnajdywania maszyn w celu przełączenia do skalowania w poziomie":::

### <a name="1-generate-the-azure-migrate-project-key"></a>1. Wygeneruj klucz projektu Azure Migrate

1. W polu **Generuj klucz projektu Azure Migrate** Podaj nazwę sufiksu dla urządzenia skalowalnego w poziomie. Sufiks może zawierać tylko znaki alfanumeryczne i ma limit długości 14 znaków.
2. Kliknij przycisk **Wygeneruj klucz** , aby rozpocząć tworzenie wymaganych zasobów platformy Azure. Nie zamykaj strony odnajdywania podczas tworzenia zasobów.
3. Skopiuj wygenerowany klucz. Ten klucz będzie potrzebny później do ukończenia rejestracji urządzenia skalowalnego w poziomie.

### <a name="2-download-the-installer-for-the-scale-out-appliance"></a>2. Pobierz Instalatora dla urządzenia skalowalnego w poziomie

W obszarze **pobierz Azure Migrate urządzenie** kliknij pozycję  **Pobierz**. Musisz pobrać skrypt Instalatora programu PowerShell, aby wdrożyć urządzenie skalowalne w poziomie na istniejącym serwerze z systemem Windows Server 2016 i z wymaganą konfiguracją sprzętu (32 GB pamięci RAM, 8 procesorów wirtualnych vCPU, około 80 GB magazynu dyskowego i dostępu do Internetu, bezpośrednio lub za pomocą serwera proxy).
:::image type="content" source="./media/how-to-scale-out-for-migration/download-scale-out.png" alt-text="Pobierz skrypt dla urządzenia skalowalnego w poziomie":::

> [!TIP]
> Możesz sprawdzić sumę kontrolną pobranego pliku zip, wykonując następujące czynności:
>
> 1. Otwórz wiersz polecenia jako administrator
> 2. Uruchom następujące polecenie, aby wygenerować skrót dla pliku spakowanego:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykładowe użycie chmury publicznej: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
> 3. Pobierz najnowszą wersję Instalatora urządzenia skalowalnego w poziomie z portalu, Jeśli obliczona wartość skrótu nie jest zgodna z tym ciągiem: e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

### <a name="3-run-the-azure-migrate-installer-script"></a>3. Uruchom skrypt Instalatora Azure Migrate
Skrypt Instalatora wykonuje następujące czynności:

- Instaluje agenta bramy i Menedżera konfiguracji urządzenia, aby wykonywać bardziej współbieżne replikacje serwera.
- Zainstaluj role systemu Windows, w tym usługi aktywacji systemu Windows, usług IIS i programu PowerShell ISE.
- Pobierz i zainstaluj moduł, który ma zostać przezapisywalny usług IIS. [Dowiedz się więcej](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klucz rejestru (HKLM) z trwałymi ustawieniami ustawień dla Azure Migrate.
- Tworzy następujące pliki w ścieżce:
    - **Pliki konfiguracji**:%ProgramData%\Microsoft Azure\Config
    - **Pliki dziennika**:%ProgramData%\Microsoft Azure\Logs

Uruchom skrypt w następujący sposób:

1. Wyodrębnij plik zip do folderu na serwerze, który będzie obsługiwał urządzenie skalowalne w poziomie.  Upewnij się, że skrypt nie jest uruchamiany na serwerze z istniejącym urządzeniem Azure Migrate.
2. Uruchom program PowerShell na powyższym serwerze z uprawnieniami administracyjnymi (z podwyższonym poziomem uprawnień).
3. Zmień katalog programu PowerShell do folderu, w którym zawartość została wyodrębniona z pobranego pliku zip.
4. Uruchom skrypt o nazwie **AzureMigrateInstaller.ps1**  przy użyciu następującego polecenia:

    - W przypadku chmury publicznej: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```

    Po zakończeniu wykonywania skrypt uruchomi Menedżera konfiguracji urządzenia.

Jeśli występują problemy, możesz uzyskać dostęp do dzienników skryptów w: <br/> C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>sygnatura czasowa</em>. log w celu rozwiązywania problemów.


### <a name="4-configure-the-appliance"></a>4. Skonfiguruj urządzenie

Przed rozpoczęciem upewnij się, że [te punkty końcowe platformy Azure](migrate-appliance.md#public-cloud-urls) są dostępne z poziomu urządzenia skalowalnego w poziomie.

- Otwórz przeglądarkę na dowolnym komputerze, który może nawiązać połączenie z serwerem skalowalnym w poziomie, a następnie otwórz adres URL menedżera konfiguracji urządzenia: **https://*skalowalne w poziomie nazwę lub adres IP urządzenia*: 44368**.

   Alternatywnie można otworzyć program Configuration Manager z pulpitu serwera urządzenia skalowalnego w poziomie przy użyciu skrótu do programu Configuration Manager.
- Zaakceptuj **postanowienia licencyjne** i przeczytaj informacje o innych firmach.
- W programie Configuration Manager > **skonfigurować wymagania wstępne**, wykonaj następujące czynności:
   - **Łączność**: Urządzenie sprawdza, czy serwer ma dostęp do Internetu. Jeśli serwer używa serwera proxy:
     1. Kliknij pozycję **Skonfiguruj serwer proxy** , aby określić adres serwera proxy (w postaci http://ProxyIPAddress lub na http://ProxyFQDN) porcie nasłuchu.
     2. Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
     3. Obsługiwane są tylko serwery proxy HTTP.
     4. Jeśli dodano szczegóły serwera proxy lub wyłączono serwer proxy i/lub uwierzytelnianie, kliknij przycisk **Zapisz** , aby ponownie uruchomić sprawdzanie łączności.
   - **Synchronizacja czasu**: czas na urządzeniu powinien być zsynchronizowany z czasem Internetu, aby odnajdywanie działało prawidłowo.
   - **Zainstaluj aktualizacje**: urządzenie zapewnia zainstalowanie najnowszych aktualizacji. Po zakończeniu sprawdzania można kliknąć pozycję **Wyświetl usługi urządzenia** , aby zobaczyć stan i wersje usług uruchomionych na serwerze urządzeń.
   - **Zainstaluj VDDK**: Urządzenie sprawdza, czy zainstalowano pakiet VMware vSphere Virtual Disk Development Kit (VDDK). Jeśli nie jest zainstalowany, Pobierz VDDK 6,7 z programu VMware i Wyodrębnij zawartość pliku zip do określonej lokalizacji na urządzeniu, zgodnie z **instrukcjami dotyczącymi instalacji** na ekranie Configuration Manager urządzenia.


### <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie w Azure Migrate

1. Wklej **klucz projektu Azure Migrate** skopiowany z portalu. Jeśli nie masz klucza, przejdź do pozycji **Ocena serwera> odkryj> zarządzanie istniejącymi urządzeniami**, wybierz nazwę urządzenia podstawowego, Znajdź urządzenie skalowalne w poziomie skojarzone z działem IT i skopiuj odpowiedni klucz.
1. Do uwierzytelnienia w systemie Azure potrzebny będzie kod urządzenia. Kliknięcie przycisku **Zaloguj** spowoduje otwarcie modalnego kodu urządzenia, jak pokazano poniżej.
:::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Modalne wyświetlanie kodu urządzenia":::

1. Kliknij pozycję **Kopiuj kod & login** , aby skopiować kod urządzenia i otworzyć monit logowania platformy Azure na nowej karcie przeglądarki. Jeśli ta wartość nie jest wyświetlana, upewnij się, że w przeglądarce wyłączono blokowanie wyskakujących okienek.
1. Na nowej karcie Wklej kod urządzenia i zaloguj się przy użyciu nazwy użytkownika i hasła platformy Azure.
   
   Logowanie przy użyciu numeru PIN nie jest obsługiwane.
3. W przypadku nagłego zamknięcia karty logowania bez logowania, należy odświeżyć kartę przeglądarki w Menedżerze konfiguracji urządzenia, aby ponownie włączyć przycisk Zaloguj.
1. Po pomyślnym zalogowaniu Wróć do poprzedniej karty przy użyciu Menedżera konfiguracji urządzeń.
1. Jeśli konto użytkownika platformy Azure używane do rejestrowania ma odpowiednie uprawnienia do zasobów platformy Azure utworzonych podczas generowania klucza, Rejestracja urządzenia zostanie zainicjowana.
:::image type="content" source="./media/how-to-scale-out-for-migration/registration-scale-out.png" alt-text="Panel 2 w Menedżerze konfiguracji urządzenia":::

#### <a name="import-appliance-configuration-from-primary-appliance"></a>Importuj konfigurację urządzenia z urządzenia podstawowego

Aby ukończyć rejestrację urządzenia skalowalnego w poziomie, kliknij przycisk **Importuj** , aby uzyskać wymagane pliki konfiguracji z urządzenia podstawowego.

1. Kliknięcie przycisku **Importuj** powoduje otwarcie okna podręcznego z instrukcjami dotyczącymi sposobu importowania niezbędnych plików konfiguracji z urządzenia podstawowego.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-modal-scale-out.png" alt-text="Zaimportuj modalną konfigurację":::
1. Zaloguj się (pulpit zdalny) do urządzenia podstawowego i wykonaj następujące polecenia programu PowerShell:

    ``` PS cd 'C:\Program Files\Microsoft Azure Appliance Configuration Manager\Scripts\PowerShell' ```
    
    ``` PS .\ExportConfigFiles.ps1 ```

1. Skopiuj plik zip utworzony przez uruchomienie powyższych poleceń do urządzenia skalowalnego w poziomie. Plik zip zawiera pliki konfiguracji, które są konieczne do zarejestrowania urządzenia skalowalnego w poziomie.
1. W oknie podręcznym otwartym w poprzednim kroku wybierz lokalizację skopiowanego pliku zip konfiguracji, a następnie kliknij przycisk **Zapisz**.

Po pomyślnym zaimportowaniu plików Rejestracja urządzenia skalowalnego w poziomie zostanie zakończona i zostanie wyświetlona sygnatura czasowa ostatniego pomyślnego importowania. Możesz również wyświetlić szczegóły rejestracji, klikając przycisk **Wyświetl szczegóły**.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-success.png" alt-text="Zrzut ekranu przedstawia rejestrowanie urządzenia skalowalnego w poziomie przy użyciu projektu Azure Migrate.":::

W tym momencie należy ponownie sprawdzić, czy urządzenie skalowalne w poziomie może połączyć się z serwerem vCenter. Kliknij przycisk **Sprawdź** ponownie, aby sprawdzić poprawność łączności vCenter Server z urządzenia skalowalnego w poziomie.
:::image type="content" source="./media/how-to-scale-out-for-migration/view-sources.png" alt-text="Zrzut ekranu przedstawia poświadczenia widoku i źródła odnajdywania do zweryfikowania.":::

> [!IMPORTANT]
> Jeśli edytujesz poświadczenia vCenter Server na urządzeniu podstawowym, upewnij się, że pliki konfiguracji zostały ponownie zaimportowane na urządzenie skalowalne w poziomie, aby uzyskać najnowszą konfigurację i kontynuować wszystkie bieżące replikacje.<br/> Jeśli urządzenie skalowalne w poziomie nie jest dłużej potrzebne, należy wyłączyć urządzenie skalowalne w poziomie. [**Dowiedz się więcej**](./common-questions-appliance.md) na temat sposobu wyłączania urządzenia skalowalnego w poziomie, gdy nie jest to konieczne.

## <a name="replicate"></a>Replikowanie

1. Po zarejestrowaniu urządzenia skalowalnego w poziomie na kafelku Azure Migrate: Migracja serwera kliknij pozycję **replikacja**.

2.  Postępuj zgodnie z instrukcjami na ekranie, aby rozpocząć replikowanie większej liczby maszyn wirtualnych. 

W przypadku urządzenia skalowalnego w poziomie można teraz replikować 500 maszyn wirtualnych jednocześnie. Można również migrować maszyny wirtualne w partiach 200 za pośrednictwem Azure Portal.

Narzędzie do migracji Azure Migrate Server zajmie się dystrybucją maszyn wirtualnych między podstawowym i skalowalnym w poziomie urządzeniem na potrzeby replikacji. Po zakończeniu replikacji można przeprowadzić migrację maszyn wirtualnych.

> [!TIP]
> Zalecamy Migrowanie maszyn wirtualnych w partiach 200 w celu uzyskania optymalnej wydajności, jeśli chcesz migrować dużą liczbę maszyn wirtualnych.
  
## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono następujące informacje:
- Jak skonfigurować urządzenie skalowalne w poziomie
- Jak replikować maszyny wirtualne przy użyciu urządzenia skalowalnego w poziomie


[Dowiedz się więcej](./tutorial-migrate-vmware.md) na temat migrowania serwerów na platformę Azure przy użyciu programu Azure Migrate: serwer narzędzia do migracji.