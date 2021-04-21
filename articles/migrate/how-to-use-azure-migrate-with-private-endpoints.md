---
title: Używanie Azure Migrate z prywatnymi punktami końcowymi
description: Użyj Azure Migrate linku prywatnego, aby odnajdywać, oceniać i migrować przy użyciu linku prywatnego.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 04/07/2020
ms.openlocfilehash: 82811c731c158d970d7ec2c2350a0cba106f6a67
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835492"
---
# <a name="using-azure-migrate-with-private-endpoints"></a>Używanie Azure Migrate z prywatnymi punktami końcowymi  

W tym artykule opisano, jak używać usługi Azure Migrate do odnajdywania, oceniania i migrowania serwerów za pośrednictwem sieci prywatnej przy użyciu [linku prywatnego platformy Azure.](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) 

Za pomocą narzędzi [Azure Migrate:](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-discovery-and-assessment-tool) odnajdywanie i ocena [oraz Azure Migrate:](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool) narzędzia migracji serwera można nawiązywać prywatne i bezpieczne połączenie z usługą Azure Migrate za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute lub połączenia sieci VPN typu lokacja-lokacja przy użyciu łącza prywatnego platformy Azure. 

Metoda łączności z prywatnym punktem końcowym jest zalecana, gdy istnieje organizacyjne wymaganie dostępu do usługi Azure Migrate i innych zasobów platformy Azure bez przechodzenia przez sieci publiczne. Możesz również użyć obsługi łącza prywatnego, aby użyć istniejących obwodów prywatnej komunikacji równorzędnej ExpressRoute w celu lepszej przepustowości lub opóźnień. 

## <a name="support-requirements"></a>Wymagania dotyczące pomocy technicznej 

### <a name="required-permissions"></a>Wymagane uprawnienia

**Uprawnienia współautora i administratora dostępu** użytkowników **lub** właściciela subskrypcji. 

### <a name="supported-scenarios-and-tools"></a>Obsługiwane scenariusze i narzędzia

**Wdrożenie** | **Szczegóły** | **Narzędzia** 
--- | --- | ---
**Odnajdywanie i ocena** | Przekonuj bez agenta odnajdywanie i ocenę serwerów działających na dowolnej platformie — platformach funkcji hypervisor, takich jak [VMware vSphere](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware) lub [Microsoft Hyper-V,](https://docs.microsoft.com/azure/migrate/tutorial-discover-hyper-v)chmurach publicznych, takich jak [AWS](https://docs.microsoft.com/azure/migrate/tutorial-discover-aws) lub [GCP,](https://docs.microsoft.com/azure/migrate/tutorial-discover-gcp)a nawet serwerach bez systemu [plików.](https://docs.microsoft.com/azure/migrate/tutorial-discover-physical) | Azure Migrate: odnajdywanie i ocena  <br/> 
**Zapasy oprogramowania** | Odnajdowanie aplikacji, ról i funkcji działających na maszynach wirtualnych VMware. | Azure Migrate: odnajdywanie i ocena  
**Wizualizacja zależności** | Funkcja analizy zależności umożliwia identyfikowanie i zrozumienie zależności między serwerami. <br/> [Wizualizacja zależności bez agenta jest](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies-agentless) obsługiwana natywnie Azure Migrate obsługi linków prywatnych. <br/>[Wizualizacja zależności opartej na agentach](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) wymaga łączności z Internetem. [Dowiedz się,](https://docs.microsoft.com/azure/azure-monitor/logs/private-link-security) jak używać prywatnych punktów końcowych do wizualizacji zależności opartej na agentach. | Azure Migrate: odnajdywanie i ocena |
**Migracja** | Wykonaj migracje funkcji [Hyper-V](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v) bez agenta lub użyj podejścia opartego na agentach, aby przeprowadzić migrację maszyn wirtualnych [VMware,](./tutorial-migrate-vmware-agent.md)maszyn wirtualnych funkcji [Hyper-V,](./tutorial-migrate-physical-virtual-machines.md)serwerów [fizycznych,](./tutorial-migrate-physical-virtual-machines.md)maszyn wirtualnych uruchomionych na platformie [AWS,](./tutorial-migrate-aws-virtual-machines.md)maszyn wirtualnych działających na platformie [GCP](https://docs.microsoft.com/azure/migrate/tutorial-migrate-gcp-virtual-machines)lub maszyn wirtualnych uruchomionych na innym dostawcy wirtualizacji. | Azure Migrate: migracja serwera
 
>[!Note]
>
> [Migracje oprogramowania VMware](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware) bez agentów wymagają dostępu do Internetu lub łączności za pośrednictwem komunikacji równorzędnej experessRoute firmy Microsoft. <br/> [Dowiedz się,](https://docs.microsoft.com/azure/migrate/replicate-using-expressroute) jak używać prywatnych punktów końcowych do wykonywania replikacji za pośrednictwem prywatnej komunikacji równorzędnej usługi ExpressRoute lub połączenia sieci VPN typu lokacja-lokacja (S2S).  <br/><br/> 
   
#### <a name="other-integrated-tools"></a>Inne zintegrowane narzędzia

Niektóre narzędzia do migracji mogą nie być w stanie przekazać danych użycia do projektu Azure Migrate, jeśli dostęp do sieci publicznej jest wyłączony. Projekt Azure Migrate należy skonfigurować tak, aby zezwalał na odbieranie danych z innych ofert firmy Microsoft lub niezależnych dostawców oprogramowania [(ISV)](https://docs.microsoft.com/azure/migrate/migrate-services-overview#isv-integration) ruchu ze wszystkich sieci. 


Aby włączyć dostęp do sieci publicznej dla projektu Azure Migrate, przejdź do strony właściwości Azure Migrate na stronie Azure Portal, wybierz pozycję **Nie** i wybierz **pozycję Zapisz.** 

![Diagram przedstawiający sposób zmiany trybu dostępu do sieci.](./media/how-to-use-azure-migrate-with-private-endpoints/migration-project-properties.png)

### <a name="other-considerations"></a>Inne zagadnienia   

**Zagadnienia do rozważenia** | **Szczegóły**
--- | --- 
**Cennik** | Aby uzyskać informacje o cenach, zobacz [Cennik obiektów blob platformy Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/) i Cennik usługi Azure Private [Link.](https://azure.microsoft.com/pricing/details/private-link/)  
**Wymagania dotyczące sieci wirtualnej** | Punkt końcowy bramy usługi ExpressRoute/sieci VPN powinien znajdować się w wybranej sieci wirtualnej lub podłączonej do niego sieci wirtualnej. W sieci wirtualnej może być potrzebnych około 15 adresów IP.  

## <a name="create-a-project-with-private-endpoint-connectivity"></a>Tworzenie projektu z łącznością prywatnego punktu końcowego

Użyj tego [artykułu,](https://docs.microsoft.com/azure/migrate/create-manage-projects#create-a-project-for-the-first-time) aby skonfigurować nowy projekt Azure Migrate projektu. 

> [!Note]
> Nie można zmienić metody łączności na łączność prywatnego punktu końcowego dla istniejących Azure Migrate projektów.

W sekcji **Konfiguracja** zaawansowana podaj poniższe szczegóły, aby utworzyć prywatny punkt końcowy dla Azure Migrate projektu.
- W **opcji Metoda łączności** wybierz pozycję Prywatny punkt **końcowy**. 
- W **opcji Wyłącz dostęp do publicznego punktu końcowego** zachowaj ustawienie domyślne  **Nie**. Niektóre narzędzia do migracji mogą nie być w stanie przekazać danych użycia do projektu Azure Migrate, jeśli dostęp do sieci publicznej jest wyłączony. [Dowiedz się więcej.](#other-integrated-tools)
- W **subskrypcji sieci wirtualnej** wybierz subskrypcję sieci wirtualnej prywatnego punktu końcowego. 
- W **sieci wirtualnej** wybierz sieć wirtualną dla prywatnego punktu końcowego. Urządzenie Azure Migrate i inne składniki oprogramowania, które muszą połączyć się z projektem Azure Migrate, muszą znajdować się w tej sieci lub podłączonej sieci wirtualnej.
- W **podsieci** wybierz podsieć prywatnego punktu końcowego. 

Wybierz przycisk **Utwórz**. Zaczekaj kilka minut, aż projekt usługi Azure Migrate zostanie wdrożony. Nie zamykaj tej strony, gdy trwa tworzenie projektu.

![Tworzenie projektu](./media/how-to-use-azure-migrate-with-private-endpoints/create-project.png)

    
Powoduje to utworzenie projektu migracji i dołączenie do niego prywatnego punktu końcowego. 

## <a name="discover-and-assess-servers-for-migration-using-azure-private-link"></a>Odnajdywanie i ocenianie serwerów do migracji przy użyciu linku prywatnego platformy Azure 

### <a name="set-up-the-azure-migrate-appliance"></a>Konfigurowanie urządzenia Azure Migrate wirtualnego 

1. Na **stronie**  >  **Odnajdywanie maszyn Czy maszyny są zwirtualizowane?** wybierz typ serwera.
2. W **Azure Migrate klucza projektu** podaj nazwę Azure Migrate wirtualnego. 
3. Wybierz **pozycję Generuj** klucz, aby utworzyć wymagane zasoby platformy Azure. 

    > [!Important]
    > Nie zamykaj strony Odnajdowanie maszyn podczas tworzenia zasobów.  
    - W tym kroku program Azure Migrate magazyn kluczy, konto magazynu, magazyn usługi Recovery Services (tylko w przypadku migracji oprogramowania VMware bez agenta) oraz kilka zasobów wewnętrznych i dołącza prywatny punkt końcowy do każdego zasobu. Prywatne punkty końcowe są tworzone w sieci wirtualnej wybranej podczas tworzenia projektu.  
    - Po utworzeniu prywatnych punktów końcowych rekordy zasobów CNAME systemu DNS dla zasobów usługi Azure Migrate zostaną zaktualizowane do aliasu w poddomenie z prefiksem "privatelink". Domyślnie program Azure Migrate także prywatną strefę DNS odpowiadającą poddomenie "privatelink" dla każdego typu zasobu i wstawia rekordy DNS A dla skojarzonych prywatnych punktów końcowych. Dzięki temu urządzenie Azure Migrate i inne składniki oprogramowania, które znajduje się w sieci źródłowej, mogą dotrzeć do punktów końcowych Azure Migrate zasobów na prywatnych adresach IP.  
    - Azure Migrate również tożsamość [zarządzaną](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) dla projektu migracji i przyznaje tożsamości zarządzanej uprawnienia do bezpiecznego uzyskiwania dostępu do konta magazynu.  

4. Po pomyślnym wygenerowaniu klucza skopiuj jego szczegóły, aby skonfigurować i zarejestrować urządzenie.   

#### <a name="download-the-appliance-installer-file"></a>Pobieranie pliku instalatora urządzenia  

Azure Migrate: odnajdywanie i ocena używają lekkiego Azure Migrate wirtualnego. Urządzenie przeprowadza odnajdywanie serwera i wysyła metadane konfiguracji i wydajności serwera do Azure Migrate.

Aby skonfigurować urządzenie, pobierz plik zip zawierający skrypt instalatora z portalu. Skopiuj plik zip na serwerze, który będzie hostował urządzenie. Po pobraniu pliku zip sprawdź zabezpieczenia plików i uruchom skrypt instalatora, aby wdrożyć urządzenie. 

Poniżej znajdują się linki do pobierania dla każdego scenariusza z ich wartościami skrótu:

Scenariusz | Link pobierania | Wartość skrótu
--- | --- | ---
Hyper-V | [AzureMigrateInstaller-HyperV-Public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160557) | 17EFA01E3A7683F1CE2A08E3A9197A27D8BD2CC03C3AB5C6E00E4261A822BDB3
Fizyczny | [AzureMigrateInstaller-Physical-Public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160558) | 01028F92C2095452F2DDCB89986CDC1F177AAC58E150A5B219A69CF1B7DA3BE0
VMware | [AzureMigrateInstaller-VMware-public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160648) | 66D3217AEC1DE51D84EC608B22BDDA605EC9C4FBAB06FC69FEC985886627C224
Skalowanie w zewnątrz oprogramowania VMware | [AzureMigrateInstaller-VMware-Public-Scaleout-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160811) | 42C1E8D5CF428E35E5B98E4E7465DDD08439F0FD5C319340CE3E3ADC3DC1717A6

#### <a name="verify-security"></a>Weryfikowanie zabezpieczeń

Przed wdrożeniem pliku zip sprawdź, czy jest on bezpieczny.

1. Otwórz okno polecenia administratora na serwerze, na który pobrano plik.
2. Uruchom następujące polecenie, aby wygenerować skrót pliku zip

    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład użycia chmury publicznej: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-public-PrivateLink.zip SHA256 ```

3.  Sprawdź najnowszą wersję urządzenia, porównując wartości skrótu z powyższej tabeli.

Upewnij się, że [](https://docs.microsoft.com/azure/migrate/migrate-appliance) serwer spełnia wymagania sprzętowe dla wybranego scenariusza (VMware/Hyper-V/Fizyczne lub inne) i może łączyć się z wymaganymi adresami URL platformy Azure [—](./migrate-appliance.md#public-cloud-urls-for-private-link-connectivity) chmurami publicznymi [i](./migrate-appliance.md#government-cloud-urls-for-private-link-connectivity) dla instytucji rządowych.


#### <a name="run-the-script"></a>Uruchamianie skryptu

1. Wyodrębnij plik zip do folderu na serwerze, który będzie hostować urządzenie. 
2. Uruchom program PowerShell na maszynie z uprawnieniami administratora (podwyższonym poziomem uprawnień).
3. Zmień katalog programu PowerShell na folder zawierający zawartość wyodrębnioną z pobranego pliku zip.
4. Uruchom skrypt **AzureMigrateInstaller.ps1** w następujący sposób:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-public-PrivateLink> .\AzureMigrateInstaller.ps1```
   
5. Po pomyślnym uruchomieniu skryptu uruchamia on menedżera konfiguracji urządzenia, aby można było skonfigurować urządzenie. Jeśli wystąpią jakiekolwiek problemy, przejrzyj dzienniki skryptów w pliku C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="configure-the-appliance-and-start-continuous-discovery"></a>Konfigurowanie urządzenia i uruchamianie ciągłego odnajdywania

Otwórz przeglądarkę na dowolnej maszynie, która może połączyć się z serwerem urządzenia, i otwórz adres URL menedżera konfiguracji urządzenia: `https://appliance name or IP address: 44368` . Alternatywnie możesz otworzyć menedżera konfiguracji z pulpitu serwera urządzenia, wybierając skrót dla menedżera konfiguracji.

#### <a name="set-up-prerequisites"></a>Konfigurowanie wymagań wstępnych

1. Przeczytaj informacje o innych firmach i zaakceptuj **postanowienia licencyjne.**    
 
2. W programie Configuration Manager > **konfigurowanie wymagań wstępnych** wykonaj następujące czynności:
   - **Łączność:** urządzenie sprawdza dostęp do wymaganych adresów URL. Jeśli serwer używa serwera proxy:
     - Wybierz **pozycję Skonfiguruj serwer proxy,** aby określić adres serwera proxy lub port `http://ProxyIPAddress` `http://ProxyFQDN` nasłuchiwania.
     - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia. Obsługiwane są tylko serwery proxy HTTP.
     - Jeśli chcesz, możesz dodać listę adresów URL/IP, które powinny pomijać serwer proxy. Jeśli używasz prywatnej komunikacji równorzędnej usługi ExpressRoute, upewnij się, że te [adresy URL zostały pominięte.](https://docs.microsoft.com/azure/migrate/replicate-using-expressroute#configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations)
     - Musisz wybrać pozycję **Zapisz,** aby zarejestrować konfigurację, jeśli zaktualizowano szczegóły serwera proxy lub dodano adresy URL/IP w celu obejścia serwera proxy.
     
        > [!Note]
        > Jeśli podczas sprawdzania łączności występuje błąd z linkiem aka.ms/*, ale nie chcesz, aby urządzenie uzyskujeło dostęp do tego adresu URL przez Internet, musisz wyłączyć usługę automatycznej aktualizacji na urządzeniu, wykonaj kroki opisane [**tutaj.**](https://docs.microsoft.com/azure/migrate/migrate-appliance#turn-off-auto-update) Po wyłączeniu automatycznej aktualizacji sprawdzanie łączności adresu URL aka.ms/* zostanie pominięte. 

   - **Synchronizacja czasu:** czas na urządzeniu powinien być zsynchronizowany z czasem internetowym, aby odnajdywanie działało prawidłowo.
   - **Zainstaluj aktualizacje:** urządzenie zapewnia zainstalowanie najnowszych aktualizacji. Po zakończeniu sprawdzania możesz  wybrać pozycję Wyświetl usługi urządzeń, aby wyświetlić stan i wersje usług uruchomionych na serwerze urządzenia.
        > [!Note]
        > Jeśli wybrano wyłączenie usługi automatycznej aktualizacji na urządzeniu, możesz ręcznie zaktualizować usługi urządzenia, aby pobrać najnowsze wersje usług, zgodnie z instrukcjami w [**tym miejscu.**](https://docs.microsoft.com/azure/migrate/migrate-appliance#manually-update-an-older-version)
   - **Zainstaluj zestaw VDDK:**(wymagane tylko w przypadku urządzenia _VMware)_ Urządzenie sprawdza, czy VMware vSphere zestaw Virtual Disk Development Kit (VDDK). Jeśli nie jest zainstalowany, pobierz zestaw VDDK 6.7 z oprogramowania VMware i wyodrębnij pobraną zawartość zip do określonej lokalizacji na urządzeniu, zgodnie z instrukcjami instalacji **.**

#### <a name="register-the-appliance-and-start-continuous-discovery"></a>Rejestrowanie urządzenia i uruchamianie ciągłego odnajdywania

Po zakończeniu sprawdzania wymagań wstępnych wykonaj następujące kroki, aby zarejestrować urządzenie i uruchomić ciągłe odnajdywanie dla odpowiednich scenariuszy: maszyny wirtualne [VMware,](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware#register-the-appliance-with-azure-migrate)maszyny wirtualne funkcji [Hyper-V,](https://docs.microsoft.com/azure/migrate/tutorial-discover-hyper-v#register-the-appliance-with-azure-migrate)serwery [fizyczne,](https://docs.microsoft.com/azure/migrate/tutorial-discover-physical#register-the-appliance-with-azure-migrate)maszyny wirtualne [platformy AWS](https://docs.microsoft.com/azure/migrate/tutorial-discover-aws#register-the-appliance-with-azure-migrate)i [maszyny wirtualne GCP.](https://docs.microsoft.com/azure/migrate/tutorial-discover-gcp#register-the-appliance-with-azure-migrate)


>[!Note]
> Jeśli podczas rejestracji urządzenia lub podczas uruchamiania odnajdywania masz problemy z rozpoznawaniem nazw  DNS, upewnij się, że zasoby usługi Azure Migrate utworzone w kroku Generuj klucz w portalu są dostępne z serwera lokalnego host Azure Migrate wirtualnego. [Dowiedz się więcej na temat weryfikowania łączności sieciowej.](#troubleshoot-network-connectivity)

### <a name="assess-your-servers-for-migration-to-azure"></a>Ocena serwerów pod celu migracji na platformę Azure
Po zakończeniu odnajdywania oceń serwery (maszyny wirtualne[VMware,](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware-azure-vm)maszyny wirtualne funkcji [Hyper-V,](https://docs.microsoft.com/azure/migrate/tutorial-assess-hyper-v)serwery [fizyczne,](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware-azure-vm)maszyny wirtualne [AWS,](https://docs.microsoft.com/azure/migrate/tutorial-assess-aws)maszyny wirtualne [GCP)](https://docs.microsoft.com/azure/migrate/tutorial-assess-gcp)pod względem migracji do maszyn wirtualnych platformy Azure lub usługi Azure VMware Solution (AVS) przy użyciu narzędzia Azure Migrate: odnajdywanie i ocena. 

Maszyny lokalne można [również](https://docs.microsoft.com/azure/migrate/tutorial-discover-import#prepare-the-csv) ocenić za pomocą narzędzia Azure Migrate: odnajdywanie i ocena przy użyciu zaimportowanego pliku wartości rozdzielanych przecinkami (CSV).   

## <a name="migrate-servers-to-azure-using-azure-private-link"></a>Migrowanie serwerów na platformę Azure przy użyciu linku prywatnego platformy Azure

W poniższych sekcjach opisano kroki wymagane do używania usługi Azure Migrate [z](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) prywatnymi punktami końcowymi na potrzeby migracji przy użyciu prywatnej komunikacji równorzędnej usługi ExpressRoute lub połączeń sieci VPN.  

W tym artykule przedstawiono ścieżkę wdrożenia weryfikacji koncepcji dla replikacji opartych na agentach w celu migrowania maszyn wirtualnych [VMware,](./tutorial-migrate-vmware-agent.md)maszyn wirtualnych funkcji [Hyper-V,](./tutorial-migrate-physical-virtual-machines.md)serwerów [fizycznych,](./tutorial-migrate-physical-virtual-machines.md)maszyn wirtualnych działających na platformie [AWS,](./tutorial-migrate-aws-virtual-machines.md)maszyn wirtualnych uruchomionych na platformie [GCP](https://docs.microsoft.com/azure/migrate/tutorial-migrate-gcp-virtual-machines)lub maszyn wirtualnych uruchomionych na innym dostawcy wirtualizacji przy użyciu prywatnych punktów końcowych platformy Azure. Podobnego podejścia można użyć do przeprowadzania migracji funkcji [Hyper-V](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v) bez agentów przy użyciu łącza prywatnego.

>[!Note]
>[Migracje oprogramowania VMware bez agenta wymagają](https://docs.microsoft.com/azure/migrate/tutorial-assess-physical) dostępu do Internetu lub łączności za pośrednictwem komunikacji równorzędnej ExperessRoute firmy Microsoft. 

### <a name="set-up-a-replication-appliance-for-migration"></a>Konfigurowanie urządzenia replikacji do migracji 

Na poniższym diagramie przedstawiono przepływ pracy replikacji opartej na agentach z prywatnymi punktami końcowymi przy użyciu Azure Migrate: Server Migration.  

![Architektura replikacji](./media/how-to-use-azure-migrate-with-private-endpoints/replication-architecture.png)

Narzędzie używa urządzenia replikacji do replikowania serwerów na platformę Azure. Skorzystaj z tego [artykułu, aby przygotować i skonfigurować maszynę dla urządzenia replikacji. ](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#prepare-a-machine-for-the-replication-appliance)

Po skonfigurowaniu urządzenia replikacji użyj poniższych instrukcji, aby utworzyć zasoby wymagane do migracji. 

1. Na **stronie Odnajdywanie maszyn** Czy maszyny są  >  **zwirtualizowane?** wybierz pozycję Nie **zwirtualizowane/inne.**
2. W **obszarze Region docelowy** wybierz i potwierdź region platformy Azure, do którego chcesz migrować maszyny.
3. Wybierz **pozycję Utwórz zasoby,** aby utworzyć wymagane zasoby platformy Azure. Nie zamykaj strony podczas tworzenia zasobów.   
    - Powoduje to utworzenie magazynu usługi Recovery Services w tle i włączenie tożsamości zarządzanej dla magazynu. Magazyn usługi Recovery Services to jednostka, która zawiera informacje o replikacji serwerów i służy do wyzwalania operacji replikacji.  
    - Jeśli projekt Azure Migrate ma łączność z prywatnym punktem końcowym, dla magazynu usługi Recovery Services jest tworzony prywatny punkt końcowy. Powoduje to dodanie pięciu w pełni kwalifikowanych nazw prywatnych (FQDN) do prywatnego punktu końcowego, po jednej dla każdej mikrousługi połączonej z magazynem usługi Recovery Services.   
    - Pięć nazw domen jest sformatowanych w tym wzorcu: <br/> _{Vault-ID}-asr-pod01-{type}-. {docelowy kod geograficzny}_. privatelink.siterecovery.windowsazure.com  
    - Domyślnie program Azure Migrate automatycznie tworzy prywatną strefę DNS i dodaje rekordy DNS A dla mikrousług magazynu usługi Recovery Services. Prywatna strefa DNS jest następnie połączona z siecią wirtualną prywatnego punktu końcowego. Dzięki temu lokalne urządzenie replikacji może rozpoznać w pełni kwalifikowane nazwy domen jako ich prywatne adresy IP.

4. Przed zarejestrowaniem urządzenia replikacji upewnij się, że sieci FQDN łącza prywatnego magazynu są dostępne z maszyny hostowania urządzenia replikacji. [Dowiedz się więcej na temat weryfikowania łączności sieciowej.](#troubleshoot-network-connectivity) 

5. Po zweryfikowaniu łączności pobierz plik instalacji i klucza urządzenia, uruchom proces instalacji i zarejestruj urządzenie w Azure Migrate. Zapoznaj się ze [szczegółowymi krokami tutaj.](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#set-up-the-replication-appliance) Po skonfigurowaniu urządzenia replikacji postępuj zgodnie z tymi instrukcjami, aby zainstalować usługę [mobilności](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#install-the-mobility-service) na maszynach, które chcesz migrować. 

### <a name="replicate-servers-to-azure-using-azure-private-link"></a>Replikowanie serwerów na platformę Azure przy użyciu linku prywatnego platformy Azure 

Teraz wykonaj następujące [kroki, aby](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#replicate-machines) wybrać serwery do replikacji.  

W **ustawieniach** replikacji docelowej konto magazynu pamięci podręcznej/replikacji umożliwia wybranie konta magazynu do replikacji za pośrednictwem  >    >  linku prywatnego.  

Jeśli projekt Azure Migrate ma łączność z prywatnym punktem końcowym, musisz udzielić uprawnień tożsamości zarządzanej magazynu usługi  [Recovery Services,](#grant-access-permissions-to-the-recovery-services-vault) aby uzyskać dostęp do konta magazynu wymaganego przez Azure Migrate.   

Ponadto, aby włączyć replikacje za pośrednictwem łącza prywatnego, [utwórz prywatny punkt końcowy dla konta magazynu.](#create-a-private-endpoint-for-the-storage-account-optional)

#### <a name="grant-access-permissions-to-the-recovery-services-vault"></a>Udzielanie uprawnień dostępu do magazynu usługi Recovery Services

Tożsamość zarządzana magazynu usługi Recovery Services wymaga uprawnień do uwierzytelnionego dostępu do konta magazynu pamięci podręcznej/replikacji. 

Skorzystaj z poniższych wskazówek, aby zidentyfikować magazyn usługi Recovery Services utworzony przez Azure Migrate i udzielić wymaganych uprawnień. 

**_Identyfikowanie magazynu usługi Recovery Services i identyfikatora obiektu tożsamości zarządzanej_**

Szczegóły magazynu usługi Recovery Services można znaleźć na stronie właściwości Azure Migrate: Migracja **serwera.** 

1. Przejdź do centrum **Azure Migrate wybierz** pozycję **Przegląd** na kafelku Azure Migrate: Migracja serwera.

    ![Strona Przegląd w centrum Azure Migrate danych](./media/how-to-use-azure-migrate-with-private-endpoints/hub-overview.png)

2. W okienku po lewej stronie wybierz pozycję **Właściwości.** Zanotuj nazwę magazynu usługi Recovery Services i identyfikator tożsamości zarządzanej. Magazyn będzie miał prywatny _punkt końcowy_ jako typ łączności **i** _inny_ jako **typ replikacji**. Te informacje będą potrzebne podczas zapewniania dostępu do magazynu.
      
    ![Azure Migrate: Strona właściwości migracji serwera](./media/how-to-use-azure-migrate-with-private-endpoints/vault-info.png)

**_Przyznawanie wymaganych uprawnień dostępu do konta magazynu_**

 Tożsamość zarządzana magazynu musi mieć przyznane następujące uprawnienia roli na koncie magazynu wymaganym do replikacji.  W takim przypadku należy utworzyć konto magazynu z wyprzedzeniem.

>[!Note]
> W przypadku migrowania maszyn wirtualnych funkcji Hyper-V na platformę Azure przy użyciu łącza prywatnego należy udzielić dostępu zarówno do konta magazynu replikacji, jak i konta magazynu pamięci podręcznej. 

Uprawnienia roli różnią się w zależności od typu konta magazynu.

- Resource Manager magazynu (typ standardowy):
  - [Współautor](../role-based-access-control/built-in-roles.md#contributor) _i_
  - [Współautor danych obiektu blob usługi Storage](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Resource Manager magazynu (typ Premium):
  - [Współautor](../role-based-access-control/built-in-roles.md#contributor) _i_
  - [Właściciel danych obiektu blob usługi Storage](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

1. Przejdź do konta magazynu replikacji/pamięci podręcznej wybranego do replikacji. Wybierz **pozycję Kontrola dostępu (IAM)** w okienku po lewej stronie. 

1. W sekcji **Dodawanie przypisania roli** wybierz pozycję **Dodaj**:

   ![Dodaj przypisanie roli](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment.png)


1. Na stronie **Dodawanie przypisania roli** w polu **Rola** wybierz odpowiednią rolę z wymienionej powyżej listy uprawnień. Wprowadź zanotioną wcześniej nazwę magazynu i wybierz pozycję **Zapisz**.

    ![Zapewnianie dostępu opartego na rolach](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment-select-role.png)

4. Oprócz tych uprawnień należy również zezwolić na dostęp do zaufanych usług firmy Microsoft. Jeśli dostęp do sieci jest ograniczony do wybranych sieci, wybierz pozycję **Zezwalaj** zaufanym usługi firmy Microsoft dostęp do tego konta magazynu w sekcji **Wyjątki** na **karcie** Sieć. 

![Zezwalaj na usługi firmy Microsoft dla konta magazynu](./media/how-to-use-azure-migrate-with-private-endpoints/exceptions.png)


### <a name="create-a-private-endpoint-for-the-storage-account-optional"></a>Tworzenie prywatnego punktu końcowego dla konta magazynu (opcjonalnie)

Aby replikować przy użyciu usługi ExpressRoute z prywatną komunikacji równorzędnej, utwórz prywatny punkt [końcowy](https://docs.microsoft.com/azure/private-link/tutorial-private-endpoint-storage-portal#create-storage-account-with-a-private-endpoint) dla kont magazynu pamięci podręcznej/replikacji (docelowe podzadanie: **_obiekt blob_**). 

>[!Note]
>
> - Prywatne punkty końcowe można tworzyć tylko na Ogólnego przeznaczenia magazynu w wersji 2 (GPv2). Aby uzyskać informacje o cenach, zobacz [Cennik stronicowych obiektów blob platformy Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/) i Cennik [linku prywatnego platformy Azure](https://azure.microsoft.com/pricing/details/private-link/)

Prywatny punkt końcowy dla konta magazynu powinien zostać utworzony w tej samej sieci wirtualnej co prywatny punkt końcowy projektu Azure Migrate lub w innej sieci wirtualnej podłączonej do tej sieci. 

Wybierz **pozycję Tak** i zintegruj z prywatną strefą DNS. Prywatna strefa DNS ułatwia kierowanie połączeń z sieci wirtualnej do konta magazynu za pośrednictwem łącza prywatnego. Wybranie **opcji Tak** powoduje automatyczne połączenie strefy DNS z siecią wirtualną i dodanie rekordów DNS do rozpoznawania nowych i w pełni kwalifikowanych nazw domen. Dowiedz się więcej o [prywatnych strefach DNS.](https://docs.microsoft.com/azure/dns/private-dns-overview)

Jeśli użytkownik tworzący prywatny punkt końcowy jest również właścicielem konta magazynu, prywatny punkt końcowy zostanie automatycznie zatwierdzony. W przeciwnym razie właściciel konta magazynu musi zatwierdzić prywatny punkt końcowy do użycia. Aby zatwierdzić lub odrzucić żądane połączenie prywatnego punktu końcowego, przejdź do tematu Połączenia prywatnego **punktu** końcowego w obszarze **Sieć** na stronie konta magazynu.

Przed podjęciem decyzji sprawdź stan prywatnego połączenia z punktem końcowym. 

![Stan zatwierdzenia prywatnego punktu końcowego](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection-state.png)

Po utworzeniu prywatnego punktu końcowego użyj listy rozwijanej w ustawieniach replikowania miejsca docelowego Konto magazynu pamięci podręcznej, aby wybrać konto magazynu do replikacji za pośrednictwem  >    >   łącza prywatnego.  

Upewnij się, że lokalne urządzenie replikacji ma łączność sieciową z kontem magazynu w jego prywatnym punkcie końcowym. [Dowiedz się więcej na temat weryfikowania łączności sieciowej.](#troubleshoot-network-connectivity)

>[!Note]
>
> - W przypadku migracji maszyn wirtualnych funkcji Hyper-V na platformę Azure, jeśli konto magazynu replikacji jest typu _Premium,_ należy wybrać inne konto magazynu typu _Standardowa_ dla konta magazynu pamięci podręcznej. W takim przypadku należy utworzyć prywatne punkty końcowe dla konta magazynu replikacji i pamięci podręcznej.  

Następnie postępuj zgodnie z tymi [instrukcjami, aby przejrzeć i rozpocząć replikację](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#replicate-machines) [oraz przeprowadzić migrację.](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#run-a-test-migration)  

## <a name="troubleshoot-network-connectivity"></a>Rozwiązywanie problemów z łącznością sieciową 

### <a name="validate-private-endpoints-configuration"></a>Weryfikowanie konfiguracji prywatnych punktów końcowych 

Upewnij się, że prywatny punkt końcowy jest stanem zatwierdzonym.  

1. Przejdź do Azure Migrate właściwości odnajdywania i oceny oraz migracji serwera.
2. Strona właściwości zawiera listę prywatnych punktów końcowych i FQDN linków prywatnych, które zostały automatycznie utworzone przez Azure Migrate.  

3. Wybierz prywatny punkt końcowy, który chcesz zdiagnozować.  
    1. Sprawdź, czy stan połączenia to Zatwierdzone.
    2. Jeśli połączenie jest w stanie Oczekiwanie, należy je zatwierdzić.
    3. Możesz również przejść do zasobu prywatnego punktu końcowego i sprawdzić, czy sieć wirtualna odpowiada sieci wirtualnej prywatnego punktu końcowego projektu migracji. 

    ![Wyświetlanie połączenia prywatnego punktu końcowego](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection.png)

### <a name="verify-dns-resolution"></a>Weryfikowanie rozpoznawania nazw DNS 

Urządzenie lokalne (lub dostawca replikacji) będzie mieć dostęp do zasobów usługi Azure Migrate przy użyciu ich w pełni kwalifikowanych nazw domen linków prywatnych (FQDN). W celu rozpoznania prywatnego adresu IP prywatnych punktów końcowych ze środowiska źródłowego mogą być wymagane dodatkowe ustawienia DNS. [Ten artykuł zawiera opis](https://docs.microsoft.com/azure/private-link/private-endpoint-dns#on-premises-workloads-using-a-dns-forwarder) scenariuszy konfiguracji dns, które mogą pomóc w rozwiązywaniu problemów z łącznością sieciową.  

Aby zweryfikować połączenie łącza prywatnego, wykonaj rozpoznawanie nazw DNS punktów końcowych zasobów usługi Azure Migrate (nazw FQDN zasobów łącza prywatnego) z serwera lokalnego hostującym urządzenie migracji i upewnij się, że jest ono rozpoznane jako prywatny adres IP. Szczegóły prywatnego punktu końcowego i informacje o nazwach FQDN zasobu linku prywatnego są dostępne na stronach właściwości Odnajdywanie i ocena i Migracja serwera. Wybierz **pozycję Pobierz ustawienia DNS,** aby wyświetlić listę.   

 ![Azure Migrate: Właściwości odnajdywania i oceny](./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png)

 ![Azure Migrate: Właściwości migracji serwera](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties.png)

Przykładowy przykład rozpoznawania nazw DNS w nazwach FQDN linku prywatnego konta magazynu.  

- Wprowadź _nslookup<storage-account-name>_.blob.core.windows.net.  Zastąp <storage-account-name> nazwą konta magazynu używanego na Azure Migrate.  

    Zostanie wyświetlony komunikat podobny do tego:  

   ![Przykład rozpoznawania nazw DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

- Prywatny adres IP 10.1.0.5 jest zwracany dla konta magazynu. Ten adres należy do podsieci sieci wirtualnej prywatnego punktu końcowego.   

Rozpoznawanie nazw DNS dla innych artefaktów Azure Migrate można sprawdzić, korzystając z podobnego podejścia.   

Jeśli rozpoznawanie nazw DNS jest nieprawidłowe, wykonaj następujące kroki:  

- Jeśli używasz niestandardowego systemu DNS, przejrzyj niestandardowe ustawienia DNS i sprawdź, czy konfiguracja dns jest poprawna. Aby uzyskać wskazówki, zobacz [Omówienie prywatnego punktu końcowego: konfiguracja DNS.](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration)
- Jeśli korzystasz z serwerów DNS dostarczanych przez platformę Azure, zapoznaj się z sekcją poniżej, aby uzyskać więcej informacji na temat rozwiązywania problemów.  

> [!Tip]
> Rekordy DNS środowiska źródłowego można ręcznie zaktualizować, edytując plik hostów DNS na urządzeniu lokalnym przy użyciu nazw FQDN zasobów linku prywatnego i skojarzonych z nimi prywatnych adresów IP. Ta opcja jest zalecana tylko do testowania. <br/>  


### <a name="validate-the-private-dns-zone"></a>Weryfikowanie Prywatna strefa DNS danych   
Jeśli rozpoznawanie nazw DNS nie działa zgodnie z opisem w poprzedniej sekcji, może to być problem z Prywatna strefa DNS strefie.  

#### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Upewnij się, że istnieje wymagany Prywatna strefa DNS strefy zasobów  
Domyślnie program Azure Migrate także prywatną strefę DNS odpowiadającą poddomenie "privatelink" dla każdego typu zasobu. Prywatna strefa DNS zostanie utworzona w tej samej grupie zasobów platformy Azure co grupa zasobów prywatnego punktu końcowego. Grupa zasobów platformy Azure powinna zawierać zasoby prywatnej strefy DNS w następującym formacie:
- privatelink.vaultcore.azure.net magazynu kluczy 
- privatelink.blob.core.windows.net dla konta magazynu
- privatelink.siterecovery.windowsazure.com magazynu usługi Recovery Services (w przypadku replikacji funkcji Hyper-V i agentów)
- privatelink.prod.migration.windowsazure.com — migrowanie projektu, projektu oceny i lokacji odnajdywania.   

Prywatna strefa DNS zostanie automatycznie utworzona przez Azure Migrate (z wyjątkiem konta magazynu pamięci podręcznej/replikacji wybranego przez użytkownika). Możesz zlokalizować powiązaną prywatną strefę DNS, przechodząc do strony prywatnego punktu końcowego i wybierając konfiguracje DNS. Prywatna strefa DNS powinna zostać wyświetlony w sekcji prywatnej integracji DNS. 

![Zrzut ekranu konfiguracji DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration.png)  

Jeśli strefa DNS nie istnieje (jak pokazano poniżej), utwórz [nowy zasób Prywatna strefa DNS Strefie DNS.](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal)  

![Tworzenie strefy Prywatna strefa DNS danych](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone.png) 

#### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>Upewnij się, Prywatna strefa DNS strefa jest połączona z siecią wirtualną  
Prywatna strefa DNS powinna być połączona z siecią wirtualną zawierającą prywatny punkt końcowy zapytania DNS w celu rozpoznania prywatnego adresu IP punktu końcowego zasobu. Jeśli prywatna strefa DNS nie jest połączona z prawidłową Virtual Network, każde rozpoznawanie nazw DNS z tej sieci wirtualnej zignoruje prywatną strefę DNS.   

Przejdź do zasobu prywatnej strefy DNS w Azure Portal i wybierz linki sieci wirtualnej z menu po lewej stronie. Powinny zostać połączone sieci wirtualne.

![Wyświetlanie linków sieci wirtualnej](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links.png) 

Spowoduje to wyświetlanie listy linków, z których każdy ma nazwę sieci wirtualnej w subskrypcji. Sieć wirtualna zawierająca zasób prywatnego punktu końcowego musi być wymieniona tutaj. W innym [przypadku postępuj zgodnie z tym artykułem,](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) aby połączyć prywatną strefę DNS z siecią wirtualną.    

Gdy prywatna strefa DNS zostanie połączona z siecią wirtualną, żądania DNS pochodzące z sieci wirtualnej będą szukać rekordów DNS w prywatnej strefie DNS. Jest to wymagane do poprawnego rozpoznawania adresów w sieci wirtualnej, w której utworzono prywatny punkt końcowy.   

#### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-records"></a>Upewnij się, że prywatna strefa DNS zawiera odpowiednie rekordy A 

Przejdź do prywatnej strefy DNS, której chcesz użyć do rozwiązywania problemów. Strona Przegląd zawiera wszystkie rekordy DNS dla tej prywatnej strefy DNS. Sprawdź, czy dla zasobu istnieje rekord DNS A. Wartość rekordu A (adres IP) musi być prywatnym adresem IP zasobów. Jeśli znajdziesz rekord A z nieprawidłowym adresem IP, musisz usunąć nieprawidłowy adres IP i dodać nowy. Zaleca się usunięcie całego rekordu A i dodanie nowego rekordu oraz opróżnienie systemu DNS na lokalnym urządzeniu źródłowym.   

Przykład ilustrujący rekord DNS A konta magazynu w prywatnej strefie DNS:

![Rekordy DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-a-records.png)   

Przykład ilustrujący mikrousługi magazynu usługi Recovery Services rekordów DNS A w prywatnej strefie DNS: 

![Rekordy DNS dla magazynu usługi Recovery Services](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records.png)   

>[!Note]
> Po usunięciu lub zmodyfikowaniu rekordu A maszyna może nadal być rozpoznawiona jako stary adres IP, ponieważ wartość czasu wygaśnięcia (time to live) mogła jeszcze nie wygasła.  

#### <a name="other-things-that-may-affect-private-link-connectivity"></a>Inne rzeczy, które mogą mieć wpływ na łączność z linkami prywatnymi  

Jest to niewyczerpująca lista elementów, które można znaleźć w zaawansowanych lub złożonych scenariuszach: 

- Ustawienia zapory , albo Azure Firewall połączone z siecią wirtualną, albo niestandardowe rozwiązanie zapory wdrażane na maszynie urządzenia.  
- Komunikacja równorzędna sieci, która może mieć wpływ na to, które serwery DNS są używane i jak jest przekierowyny ruch.  
- Rozwiązania bramy niestandardowej (NAT) mogą mieć wpływ na sposób rozsyłania ruchu, w tym ruch z zapytań DNS.

Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem rozwiązywania problemów z łącznością prywatnego punktu końcowego.](https://docs.microsoft.com/azure/private-link/troubleshoot-private-endpoint-connectivity)  

## <a name="next-steps"></a>Następne kroki 
- [Ukończ proces migracji i](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#complete-the-migration) zapoznaj się z [najlepszymi rozwiązaniami](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#post-migration-best-practices)po migracji.