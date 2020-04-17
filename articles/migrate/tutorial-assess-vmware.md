---
title: Ocena maszyn wirtualnych VMware za pomocą oceny serwera migracji platformy Azure
description: W tym artykule opisano sposób oceny lokalnych maszyn wirtualnych VMware do migracji na platformę Azure przy użyciu oceny serwera migracji usługi Azure.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: bd9e6b5923207297b1aa70a67052a7796b901781
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535370"
---
# <a name="assess-vmware-vms-with-server-assessment"></a>Ocenianie maszyn wirtualnych programu VMware przy użyciu narzędzia do oceny serwera

W tym artykule pokazano, jak ocenić lokalne maszyny wirtualne VMware (VMs), przy użyciu narzędzia [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool)


Ten samouczek jest drugim z serii, który pokazuje, jak oceniać i migrować maszyny wirtualne VMware na platformę Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Konfigurowanie projektu migracji platformy Azure.
> * Skonfiguruj urządzenie migracji platformy Azure, które działa lokalnie w celu oceny maszyn wirtualnych.
> * Rozpocznij ciągłe odnajdowanie lokalnych maszyn wirtualnych. Urządzenie wysyła dane konfiguracji i wydajności dla wykrytych maszyn wirtualnych na platformie Azure.
> * Grupa odnaleziono maszyny wirtualne i ocenić grupę maszyn wirtualnych.
> * Przejrzyj ocenę.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrażania dla scenariusza, dzięki czemu można szybko skonfigurować weryfikacji koncepcji. Samouczki używają opcji domyślnych tam, gdzie to możliwe, i nie pokazują wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z artykułami instruktażowymi.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

- [Ukończ pierwszy samouczek](tutorial-prepare-vmware.md) z tej serii. Jeśli nie, instrukcje w tym samouczku nie będzie działać.
- Oto, co powinieneś zrobić w pierwszym samouczku:
    - [Przygotuj platformę Azure](tutorial-prepare-vmware.md#prepare-azure) do pracy z programem Azure Migrate.
    - [Przygotuj VMware do oceny.](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) Obejmuje to sprawdzanie ustawień VMware, konfigurowanie konta, którego usługa Azure Migrate może używać do uzyskiwania dostępu do serwera vCenter Server.
    - [Sprawdź,](tutorial-prepare-vmware.md#verify-appliance-settings-for-assessment) czego potrzebujesz, aby wdrożyć urządzenie migracji platformy Azure do oceny VMware.

## <a name="set-up-an-azure-migrate-project"></a>Konfigurowanie projektu migracji platformy Azure

Skonfiguruj nowy projekt migracji platformy Azure w następujący sposób:

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
1. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.
1. W **obszarze Przegląd**w obszarze **Odnajdowanie i migrowanie serwerów**wybierz pozycję **Oceń i migruj serwery**.

   ![Przycisk do oceny i migracji serwerów](./media/tutorial-assess-vmware/assess-migrate.png)

1. W **obszarze Wprowadzenie**wybierz pozycję Dodaj **narzędzia**.
1. W obszarze **Projekt migracji**wybierz subskrypcję platformy Azure i utwórz grupę zasobów, jeśli jej nie masz.     
1. W **obszarze Szczegóły projektu**określ nazwę projektu i lokalizację geograficzną, w której chcesz utworzyć projekt. Przejrzyj obsługiwane obszary geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Pola dla nazwy projektu i regionu](./media/tutorial-assess-vmware/migrate-project.png)

1. Wybierz opcję **Dalej**.
1. W **narzędziu do oceny Wybierz**wybierz pozycję Azure **Migrate: Server Assessment** > **Next**.

   ![Wybór narzędzia Ocena serwera](./media/tutorial-assess-vmware/assessment-tool.png)

1. W obszarze **Wybierz narzędzie migracji** wybierz pozycję **Pomiń na razie dodawanie narzędzia migracji** > **Dalej**.
1. W **obszarze Recenzja + dodawanie narzędzi**przejrzyj ustawienia i wybierz pozycję Dodaj **narzędzia**.
1. Zaczekaj kilka minut, aż projekt usługi Azure Migrate zostanie wdrożony. Nastąpi przekierowanie do strony projektu. Jeśli nie widzisz projektu, możesz uzyskać do niego dostęp z obszaru **Serwery** na pulpicie nawigacyjnym usługi Azure Migrate.

## <a name="set-up-the-azure-migrate-appliance"></a>Konfigurowanie urządzenia migracji platformy Azure

Usługa Azure Migrate:Server Assessment używa lekkiego urządzenia migracji platformy Azure. Urządzenie wykonuje odnajdowanie maszyn wirtualnych i wysyła metadane maszyny Wirtualnej i dane o wydajności do usługi Azure Migrate. Urządzenie można skonfigurować na wiele sposobów.

- Skonfiguruj na maszynie wirtualnej VMware przy użyciu pobranego szablonu OVA. Jest to metoda używana w tym samouczku.
- Skonfiguruj na maszynie wirtualnej VMware lub na komputerze fizycznym za pomocą skryptu instalatora programu PowerShell. [Ta metoda](deploy-appliance-script.md) powinna być używana, jeśli nie można skonfigurować maszyny Wirtualnej przy użyciu szablonu OVA lub jeśli jesteś w usłudze Azure Government.

Po utworzeniu urządzenia można sprawdzić, czy można połączyć się z oceną migracji:serwera platformy Azure, skonfigurować go po raz pierwszy i zarejestrować go w projekcie migracji platformy Azure.


### <a name="download-the-ova-template"></a>Pobierz szablon OVA

1. W **obszarze** > Cele migracji**Serwery** > **platformy Azure migrate: Ocena serwera**wybierz pozycję **Odkryj**.
1. W **discover maszyny** > **Czy twoje maszyny są zwirtualizowane?**, wybierz **Tak, z VMWare vSphere hypervisor**.
1. Wybierz **pobierz,** aby pobrać plik szablonu OVA.

   ![Wybory do pobierania pliku OVA](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Weryfikowanie zabezpieczeń

Przed wdrożeniem pliku OVA sprawdź, czy plik ova jest bezpieczny:

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
1. Uruchom następujące polecenie, aby wygenerować skrót dla pliku OVA:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Przykład użycia: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

W przypadku wersji 2.19.07.30 wygenerowany skrót powinien być zgodny z tymi wartościami:

**Algorytm** | **Wartość skrótu**
--- | ---
MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b55eb46999c2fad9d2ebc808540c

### <a name="create-the-appliance-vm"></a>Tworzenie maszyny wirtualnej urządzenia

Zaimportuj pobrany plik i utwórz maszynę wirtualną:

1. W konsoli klienta vSphere wybierz pozycję Szablon**OVF wdrażania** **plików** > .

   ![Polecenie Menu służące do wdrażania szablonu OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

1. W Kreatorze wdrażania szablonu OVF > **source**określ lokalizację pliku OVA.
1. W **obszarze Nazwa** i **lokalizacja**określ przyjazną nazwę maszyny Wirtualnej. Wybierz obiekt zapasów, w którym będzie obsługiwana maszyna wirtualna.
1. W **obszarze Host/Cluster**określ hosta lub klaster, na którym będzie uruchamiana maszyna wirtualna.
1. W **magazynie**określ miejsce docelowe magazynu dla maszyny Wirtualnej.
1. W obszarze **Disk Format** (Format dysku) określ typ i rozmiar dysku.
1. W **obszarze Mapowanie sieci**określ sieć, z którą zostanie nawiązyczana maszyna wirtualna. Sieć potrzebuje łączności z Internetem, aby wysłać metadane do oceny serwera migracji platformy Azure.
1. Przejrzyj i potwierdź ustawienia, a następnie wybierz pozycję **Zakończ**.

## <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że maszyna wirtualna urządzenia może łączyć się z adresami URL platformy Azure dla chmur [publicznych](migrate-appliance.md#public-cloud-urls) i [rządowych.](migrate-appliance.md#government-cloud-urls)

### <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie po raz pierwszy.

> [!NOTE]
> Jeśli urządzenie zostanie skonfigurowane przy użyciu [skryptu programu PowerShell](deploy-appliance-script.md) zamiast pobranej funkcji OVA, pierwsze dwa kroki w tej procedurze nie są istotne.

1. W konsoli klienta vSphere kliknij prawym przyciskiem myszy maszynę wirtualną, a następnie wybierz polecenie **Otwórz konsolę**.
1. Podaj język, strefę czasową i hasło urządzenia.
1. Otwórz przeglądarkę na dowolnym komputerze, który może połączyć się z maszyną wirtualną, i otwórz adres URL aplikacji internetowej urządzenia: **https:// nazwę urządzenia lub adres*IP:* 44368**.

   Alternatywnie możesz otworzyć aplikację z pulpitu urządzenia, wybierając skrót do aplikacji.
1. W aplikacji sieci web > **Konfigurowanie wymagań wstępnych**wykonaj następujące czynności:
   - **Licencja**: Zaakceptuj postanowienia licencyjne i przeczytaj informacje innych firm.
   - **Łączność:** Aplikacja sprawdza, czy maszyna wirtualna ma dostęp do Internetu. Jeśli maszyna wirtualna używa serwera proxy:
     - Wybierz **ustawienia serwera proxy**i określ adres http://ProxyIPAddress serwera http://ProxyFQDNproxy i port nasłuchiwania w formularzu lub .
     - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
     - Obsługiwane są tylko serwery proxy HTTP.
   - **Synchronizacja czasu:** czas na urządzeniu powinien być zsynchronizowany z czasem korzystania z Internetu, aby odnajdowanie działało poprawnie.
   - **Zainstaluj aktualizacje:** Urządzenie zapewnia, że najnowsze aktualizacje są zainstalowane.
   - **Zainstaluj zestaw VDDK:** Urządzenie sprawdza, czy jest zainstalowany zestaw VMWare vSphere Virtual Disk Development Kit (VDDK). Jeśli nie jest zainstalowany, pobierz program VDDK 6.7 z VMware i wyodrębnij pobraną zawartość zip do określonej lokalizacji na urządzeniu.

     Migracja serwera migracji z usługą Azure używa VDDK do replikowania maszyn podczas migracji na platformę Azure.       

### <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie za pomocą usługi Azure Migrate

1. Wybierz **pozycję Zaloguj**się . Jeśli nie jest wyświetlany, upewnij się, że wyłączono blokowanie wyskakujących wyskakujących w przeglądarce.
1. Na nowej karcie zaloguj się przy użyciu nazwy użytkownika i hasła platformy Azure.
   
   Logowanie za pomocą numeru PIN nie jest obsługiwane.
1. Po pomyślnym zalogowaniu wróć do aplikacji sieci web.
1. Wybierz subskrypcję, w której został utworzony projekt migracji platformy Azure, a następnie wybierz projekt.
1. Określ nazwę urządzenia. Nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
1. Wybierz pozycję **Zarejestruj**.


## <a name="start-continuous-discovery"></a>Rozpocznij ciągłe odnajdowanie

Urządzenie musi połączyć się z serwerem vCenter, aby odnajdować dane dotyczące konfiguracji i wydajności maszyn wirtualnych.

### <a name="specify-vcenter-server-details"></a>Określanie szczegółów programu vCenter Server
1. W **obszarze Określ szczegóły serwera vCenter**określ nazwę (FQDN) lub adres IP wystąpienia serwera vCenter Server. Można opuścić port domyślny lub określić port niestandardowy, na którym nasłuchuje serwer vCenter Server.
2. W **obszarze Nazwa użytkownika** i **Hasło**określ poświadczenia konta serwera vCenter Server używane do odnajdywać maszyny wirtualne w wystąpieniu serwera vCenter Server. 

    - Powinieneś mieć skonfigurować konto z wymaganymi uprawnieniami w [poprzednim samouczku](tutorial-prepare-vmware.md#set-up-an-account-for-assessment).
    - Jeśli chcesz odnajdować zakres do określonych obiektów VMware (vCenter Server centrów danych, klastrów, folder klastrów, hosty, folder hostów lub poszczególnych maszyn wirtualnych.), zapoznaj się z instrukcjami w [tym artykule,](set-discovery-scope.md) aby ograniczyć konto używane przez usługę Azure Migrate.

3. Wybierz **opcję Sprawdź poprawność połączenia,** aby upewnić się, że urządzenie może łączyć się z serwerem vCenter Server.
4. W **discover aplikacji i zależności na maszynach wirtualnych,** opcjonalnie kliknij przycisk Dodaj **poświadczenia**i określ system operacyjny, dla którego poświadczenia są odpowiednie, a poświadczenia nazwy użytkownika i hasła. Następnie kliknij przycisk **Dodaj**.

    - Opcjonalnie można dodać poświadczenia w tym miejscu, jeśli utworzono konto do użycia dla [funkcji odnajdywania aplikacji](how-to-discover-applications.md)lub [funkcji analizy zależności bez agenta.](how-to-create-group-machine-dependencies-agentless.md)
    - Jeśli nie korzystasz z tych funkcji, możesz pominąć to ustawienie.
    - Przejrzyj poświadczenia potrzebne do [odnajdowania aplikacji](migrate-support-matrix-vmware.md#application-discovery)lub [analizy bez agenta.](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)

5. **Zapisz i rozpocznij odnajdowanie**, aby rozpocząć odnajdowanie maszyn wirtualnych.

Odnajdowanie działa w następujący sposób:
- Trwa około 15 minut odnalezione metadane maszyny Wirtualnej do wyświetlenia w portalu.
- Odnajdowanie zainstalowanych aplikacji, ról i funkcji zajmuje trochę czasu. Czas trwania zależy od liczby wykrytych maszyn wirtualnych. W przypadku 500 maszyn wirtualnych trwa około godziny, aby spis aplikacji był wyświetlany w portalu migracji platformy Azure.

### <a name="verify-vms-in-the-portal"></a>Weryfikowanie maszyn wirtualnych w portalu

Po odnajdowaniu można sprawdzić, czy maszyny wirtualne są wyświetlane w witrynie Azure portal:

1. Otwórz pulpit nawigacyjny migracji platformy Azure.
1. W **usłudze Azure Migrate — serwery** > **Usługi Azure Migrate: Server Assessment**wybierz ikonę, która wyświetla liczbę **odnalezionych serwerów**.

## <a name="set-up-an-assessment"></a>Konfigurowanie oceny

Za pomocą usługi Azure Migrate Server Assessment można utworzyć dwa typy ocen:

**Ocena** | **Szczegóły** | **Dane**
--- | --- | ---
**Oparte na wydajności** | Oceny na podstawie zebranych danych dotyczących wydajności | **Zalecany rozmiar maszyny Wirtualnej:** Na podstawie danych wykorzystania procesora i pamięci.<br/><br/> **Zalecany typ dysku (dysk zarządzany standardowy lub premium):** na podstawie usług We/Wy i przepływności dysków lokalnych.
**Jako lokalnie** | Oceny oparte na wielkości lokalnej | **Zalecany rozmiar maszyny Wirtualnej:** Na podstawie lokalnego rozmiaru maszyny Wirtualnej.<br/><br> **Zalecany typ dysku:** Na podstawie ustawienia typu magazynu wybranego dla oceny.

## <a name="run-an-assessment"></a>Uruchamianie oceny

Przeprowadzić ocenę w następujący sposób:

1. Zapoznaj się z [najlepszymi rozwiązaniami w](best-practices-assessment.md) zakresie tworzenia ocen.
1. Na karcie **Serwery** na kafelku **Azure Migrate: Server Assessment** wybierz pozycję **Oceń**.

   ![Lokalizacja przycisku Oceń](./media/tutorial-assess-vmware/assess.png)

1. W **obszarze Oceń serwery**określ nazwę oceny.
1. Wybierz pozycję **Wyświetl wszystkie**, a następnie przejrzyj właściwości oceny.

   ![Właściwości oceny](./media/tutorial-assess-vmware/view-all.png)

1. W **obszarze Zaznacz lub utwórz grupę**wybierz pozycję **Utwórz nowy**i określ nazwę grupy. Grupa gromadzi jedną lub więcej maszyn wirtualnych razem do oceny.
1. W **obszarze Dodaj maszyny do grupy**wybierz maszyny wirtualne, które mają być dodane do grupy.
1. Wybierz **pozycję Utwórz ocenę,** aby utworzyć grupę i uruchomić ocenę.

   ![Ocena serwerów](./media/tutorial-assess-vmware/assessment-create.png)

1. Po utworzeniu oceny wyświetl ją w **obszarze Serwery** > **Migracji platformy Azure:** > **Oceny**oceny serwera .
1. Wybierz **opcję Eksportuj ocenę,** aby pobrać ją jako plik programu Excel.

## <a name="review-an-assessment"></a>Przegląd oceny

W ocenie opisano:

- **Gotowość platformy Azure:** Czy maszyny wirtualne nadają się do migracji na platformę Azure.
- **Szacowanie kosztów miesięcznych:** szacowane miesięczne koszty obliczeń i magazynu związane z uruchamianiem maszyn wirtualnych na platformie Azure.
- **Miesięczne szacowanie kosztów magazynowania:** Szacowane koszty magazynu dysków po migracji.

Aby wyświetlić ocenę:

1. W **obszarze** > Cele migracji**Serwery**wybierz pozycję **Oceny** w obszarze **Migracja platformy Azure: Ocena serwera**.
1. W **obszarze Oceny**wybierz ocenę, aby ją otworzyć.

   ![Podsumowanie oceny](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Przejrzyj gotowość platformy Azure

1. W **gotowości platformy Azure**sprawdź, czy maszyny wirtualne są gotowe do migracji na platformę Azure.
1. Przejrzyj stan maszyny Wirtualnej:
    - **Gotowe do platformy Azure:** używane, gdy usługa Azure Migrate zaleca rozmiar maszyny Wirtualnej i szacowane koszty dla maszyn wirtualnych w ocenie.
    - **Gotowy z warunkami:** Pokazuje problemy i sugerowane środki zaradcze.
    - **Nie jest gotowy na platformę Azure:** pokazuje problemy i sugerowane rozwiązania.
    - **Gotowość nieznana:** używane, gdy usługa Azure Migrate nie może ocenić gotowości z powodu problemów z dostępnością danych.

1. Wybierz stan **gotowości platformy Azure.** Można wyświetlić szczegóły gotowości maszyny Wirtualnej. Można również przejść do szczegółów, aby wyświetlić szczegóły maszyny Wirtualnej, w tym ustawienia obliczeniowe, magazynowe i sieciowe.

### <a name="review-cost-details"></a>Przejrzyj szczegóły kosztów

Podsumowanie oceny przedstawia szacowany koszt obliczeń i magazynu uruchamiania maszyn wirtualnych na platformie Azure. Koszty są agregowane dla wszystkich maszyn wirtualnych w ocenianej grupie. Można przejść do szczegółów, aby wyświetlić szczegóły kosztów dla określonych maszyn wirtualnych.

> [!NOTE]
> Szacunki kosztów są oparte na zaleceniach dotyczących rozmiaru komputera, jego dysków i jego właściwości. Szacunki są przeznaczone do uruchamiania lokalnych maszyn wirtualnych jako maszyn wirtualnych IaaS. Ocena programu Azure Migrate Server nie uwzględnia kosztów paas lub SaaS.

Zagregowane koszty magazynowania dla ocenianej grupy są dzielone na różne typy dysków magazynu. 

### <a name="review-confidence-rating"></a>Przegląd oceny zaufania

Ocena programu Azure Migrate Server przypisuje ocenę zaufania do oceny opartej na wydajności, od jednej gwiazdki (najniższej) do pięciu gwiazdek (najwyższa).

![Ocena zaufania](./media/tutorial-assess-vmware/confidence-rating.png)

Ocena zaufania pomaga oszacować wiarygodność zaleceń dotyczących rozmiaru oceny. Ocena jest oparta na dostępności punktów danych potrzebnych do obliczenia oceny:

**Dostępność punktów danych** | **Ocena zaufania**
--- | ---
0%–20% | 1 gwiazdka
21%–40% | 2 gwiazdki
41%–60% | 3 gwiazdki
61%–80% | 4 gwiazdki
81%–100% | 5 gwiazdek

[Dowiedz się więcej o najlepszych praktykach](best-practices-assessment.md#best-practices-for-confidence-ratings) dotyczących klasyfikacji ufności.

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowałeś urządzenie migracji platformy Azure. Utworzono również i przejrzano ocenę.

Aby dowiedzieć się, jak przeprowadzić migrację maszyn wirtualnych VMware na platformę Azure przy użyciu migracji serwera migracji usługi Azure, przejdź do trzeciego samouczka z tej serii:

> [!div class="nextstepaction"]
> [Migrowanie maszyn wirtualnych VMware](./tutorial-migrate-vmware.md)
