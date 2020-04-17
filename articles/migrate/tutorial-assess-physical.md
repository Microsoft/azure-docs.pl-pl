---
title: Ocena serwerów fizycznych pod kątem migracji na platformę Azure za pomocą oceny serwera migracji platformy Azure
description: W tym artykule opisano sposób oceny lokalnych serwerów fizycznych do migracji na platformę Azure przy użyciu oceny serwera migracji platformy Azure.
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: b36cba18bd154cd5d14e16a9f8bf85cda6bf87a8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535438"
---
# <a name="assess-physical-servers-with-azure-migrateserver-assessment"></a>Ocena serwerów fizycznych za pomocą programu Azure Migrate:Server Assessment

W tym artykule pokazano, jak ocenić lokalne serwery fizyczne przy użyciu narzędzia Azure Migrate:Server Assessment.

[Usługa Azure Migrate](migrate-services-overview.md) udostępnia centrum narzędzi ułatwiających odnajdywanie, ocenę i migrację aplikacji, infrastruktury i obciążeń na platformę Microsoft Azure. Centrum zawiera narzędzia migracji platformy Azure i oferty niezależnych dostawców oprogramowania innych firm (ISV).

Ten samouczek jest drugim z serii, który pokazuje, jak ocenić i migrować serwery fizyczne na platformę Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Konfigurowanie projektu migracji platformy Azure.
> * Skonfiguruj urządzenie migracji platformy Azure, które działa lokalnie w celu oceny serwerów fizycznych.
> * Rozpocznij ciągłe odnajdowanie lokalnych serwerów fizycznych. Urządzenie wysyła dane konfiguracji i wydajności dla wykrytych serwerów na platformę Azure.
> * Grupuj wykryte serwery i oceniaj grupę serwerów.
> * Przejrzyj ocenę.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrażania dla scenariusza, dzięki czemu można szybko skonfigurować weryfikacji koncepcji. Samouczki używają opcji domyślnych tam, gdzie to możliwe, i nie pokazują wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z artykułami instruktażowymi.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) przed rozpoczęciem.


## <a name="prerequisites"></a>Wymagania wstępne

- [Ukończ](tutorial-prepare-physical.md) pierwszy samouczek z tej serii. Jeśli nie, instrukcje w tym samouczku nie będzie działać.
- Oto, co powinieneś zrobić w pierwszym samouczku:
    - [Konfigurowanie uprawnień platformy Azure](tutorial-prepare-physical.md) dla migracji platformy Azure.
    - [Przygotuj serwery fizyczne](tutorial-prepare-physical.md#prepare-for-physical-server-assessment) do oceny. Należy zweryfikować wymagania dotyczące urządzeń. Należy również skonfigurować konto do odnajdowania serwera fizycznego. Wymagane porty powinny być dostępne i należy pamiętać o adresach URL potrzebnych do uzyskania dostępu do platformy Azure.




## <a name="set-up-an-azure-migrate-project"></a>Konfigurowanie projektu migracji platformy Azure

Skonfiguruj nowy projekt usługi Azure Migrate w następujący sposób.

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.
3. W obszarze **Omówienie**, **Odnajdywanie, ocena i migracja serwerów** kliknij pozycję **Oceń i zmigruj serwery**.

    ![Odkrywanie i ocenianie serwerów](./media/tutorial-assess-physical/assess-migrate.png)

4. W obszarze **Wprowadzenie** kliknij pozycję **Dodaj narzędzia**.
5. W obszarze **Projekt migracji**wybierz subskrypcję platformy Azure i utwórz grupę zasobów, jeśli jej nie masz.  
6. W **obszarze Szczegóły projektu**określ nazwę projektu i lokalizację geograficzną, w której chcesz utworzyć projekt. Przejrzyj obsługiwane obszary geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

    - Geografia projektu jest używana tylko do przechowywania metadanych zebranych z serwerów lokalnych.
    - Podczas przeprowadzania migracji można wybrać dowolny region docelowy.

    ![Tworzenie projektu migracji platformy Azure](./media/tutorial-assess-physical/migrate-project.png)


7. Kliknij przycisk **Dalej**.
8. W **narzędziu do oceny Wybierz**wybierz pozycję Azure **Migrate: Server Assessment** > **Next**.

    ![Tworzenie projektu migracji platformy Azure](./media/tutorial-assess-physical/assessment-tool.png)

9. W obszarze **Wybierz narzędzie migracji** wybierz pozycję **Pomiń na razie dodawanie narzędzia migracji** > **Dalej**.
10. W **obszarze Recenzja + dodawanie narzędzi**przejrzyj ustawienia i kliknij pozycję Dodaj **narzędzia**.
11. Zaczekaj kilka minut, aż projekt usługi Azure Migrate zostanie wdrożony. Nastąpi przekierowanie do strony projektu. Jeśli nie widzisz projektu, możesz uzyskać do niego dostęp z obszaru **Serwery** na pulpicie nawigacyjnym usługi Azure Migrate.


## <a name="set-up-the-appliance"></a>Konfigurowanie urządzenia

Migracja platformy Azure: Ocena serwera uruchamia lekkie urządzenie.

- To urządzenie wykonuje odnajdowanie serwera fizycznego i wysyła metadane serwera i dane wydajności do oceny serwera migracji usługi Azure.
- Aby skonfigurować urządzenie, które:
    - Pobierz spakowany plik za pomocą skryptu instalatora migracji usługi Azure z witryny Azure portal.
    - Wyodrębnij zawartość z pliku spakowane. Uruchom konsolę programu PowerShell z uprawnieniami administracyjnymi.
    - Wykonaj skrypt programu PowerShell, aby uruchomić aplikację internetową urządzenia.
    - Skonfiguruj urządzenie po raz pierwszy i zarejestruj go w projekcie migracji platformy Azure.
- Można skonfigurować wiele urządzeń dla jednego projektu migracji platformy Azure. We wszystkich urządzeniach można odnajdyć dowolną liczbę serwerów fizycznych. Na urządzenie można wykryć maksymalnie 250 serwerów.

### <a name="download-the-installer-script"></a>Pobierz skrypt instalatora

Pobierz spakowany plik urządzenia.

1. W **obszarze** > Cele migracji**Serwery** > **usługi Azure Migrate: Ocena serwera**kliknij przycisk **Odkryj**.
2. W **discover maszyny** > **Czy twoje maszyny są zwirtualizowane?**, kliknij nie **zwirtualizowany/Inne**.
3. Kliknij **przycisk Pobierz,** aby pobrać spakowany plik.

    ![Pobierz instalatora](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Weryfikowanie zabezpieczeń

Przed wdrożeniem pliku jest bezpieczny, zanim go wdrożysz.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla spakowany plik:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykład użycia w chmurze publicznej:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
    - Przykład użycia chmury rządów:```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Sprawdź wartości skrótu:
 
    - Dla chmury publicznej (dla najnowszej wersji urządzenia):

        **Algorytm** | **Wartość skrótu**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cdd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

    - Dla platformy Azure dla instytucji rządowych (dla najnowszej wersji urządzenia):

        **Algorytm** | **Wartość skrótu**
          --- | ---
          MD5 | f81c155fc4a1409901caea948713913f

### <a name="run-the-azure-migrate-installer-script"></a>Uruchamianie skryptu instalatora migracji platformy Azure

Skrypt instalatora wykonuje następujące czynności:

- Instaluje agentów i aplikację sieci web do wykrywania i oceny serwera fizycznego.
- Zainstaluj role systemu Windows, w tym Usługę aktywacji systemu Windows, usługi IIS i program PowerShell ISE.
- Pobierz i zainstaluje moduł wielokrotnego zapisu usługi IIS. [Dowiedz się więcej](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klucz rejestru (HKLM) ze szczegółami ustawień trwałych dla migracji platformy Azure.
- Tworzy następujące pliki pod ścieżką:
    - **Pliki konfiguracyjne:**%Programdata%\Microsoft Azure\Config
    - **Pliki dziennika:**%Programdata%\Microsoft Azure\Logs

Uruchom skrypt w następujący sposób:

1. Wyodrębnij spakowany plik do folderu na serwerze, w który będzie obsługiwał urządzenie.  Upewnij się, że skrypt nie jest uruchamiany na komputerze na istniejącym urządzeniu migracji platformy Azure.
2. Uruchom program PowerShell na powyższym serwerze z uprawnieniami administracyjnymi (podwyższonymi).
3. Zmień katalog programu PowerShell na folder, w którym zawartość została wyodrębniona z pobranego pliku spakowanym.
4. Uruchom skrypt o nazwie **AzureMigrateInstaller.ps1,** uruchamiając następujące polecenie:

    - Dla chmury publicznej:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - Dla platformy Azure dla instytucji rządowych:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    Skrypt uruchomi aplikację internetową urządzenia po pomyślnym zakończeniu.

Jeśli natkniesz się na jakiekolwiek problemy, możesz uzyskać dostęp do dzienników skryptów w witrynie C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log w celu rozwiązania problemu.

### <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że urządzenie może łączyć się z adresami URL platformy Azure dla chmur [publicznych](migrate-appliance.md#public-cloud-urls) i [rządowych.](migrate-appliance.md#government-cloud-urls)


### <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie po raz pierwszy.

1. Otwórz przeglądarkę na dowolnym komputerze, który może połączyć się z urządzeniem, i otwórz adres URL aplikacji internetowej urządzenia: **https:// nazwę urządzenia lub adres*IP:* 44368**.

   Alternatywnie możesz otworzyć aplikację z pulpitu, klikając skrót do aplikacji.
2. W aplikacji sieci web > **Konfigurowanie wymagań wstępnych**wykonaj następujące czynności:
    - **Licencja**: Zaakceptuj postanowienia licencyjne i przeczytaj informacje innych firm.
    - **Łączność:** Aplikacja sprawdza, czy serwer ma dostęp do Internetu. Jeśli serwer używa serwera proxy:
        - Kliknij pozycję **Ustawienia serwera proxy**i określ adres http://ProxyIPAddress http://ProxyFQDNserwera proxy i port nasłuchiwania w formularzu lub .
        - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
        - Obsługiwane są tylko serwery proxy HTTP.
    - **Synchronizacja czasu:** czas jest weryfikowany. Czas na urządzeniu powinien być zsynchronizowany z czasem korzystania z Internetu, aby odnajdowanie serwera działało poprawnie.
    - **Zainstaluj aktualizacje:** Ocena programu Azure Migrate Server sprawdza, czy urządzenie ma zainstalowane najnowsze aktualizacje.

### <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie za pomocą usługi Azure Migrate

1. Kliknij **pozycję Zaloguj**się . Jeśli nie jest wyświetlany, upewnij się, że wyłączono blokowanie wyskakujących wyskakujących w przeglądarce.
2. Na nowej karcie zaloguj się przy użyciu poświadczeń platformy Azure.
    - Zaloguj się przy użyciu swojej nazwy użytkownika i hasła.
    - Logowanie się przy pomocy numeru PIN nie jest obsługiwane.
3. Po pomyślnym zalogowaniu wróć do aplikacji sieci web.
4. Wybierz subskrypcję, w której został utworzony projekt migracji platformy Azure. Następnie wybierz projekt.
5. Określ nazwę urządzenia. Nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
6. Kliknij **pozycję Zarejestruj**.


## <a name="start-continuous-discovery"></a>Rozpocznij ciągłe odnajdowanie

Teraz połącz się z urządzenia do serwerów fizycznych, które mają zostać wykryte, i rozpocznij odnajdowanie.

1. Kliknij **przycisk Dodaj poświadczenia,** aby określić poświadczenia konta używane przez urządzenie do odnajdywać serwery.  
2. Określ **system operacyjny**, przyjazną nazwę poświadczeń oraz nazwę użytkownika i hasło. Następnie kliknij przycisk **Dodaj**.
Można dodać jeden zestaw poświadczeń dla serwerów Windows i Linux.
4. Kliknij **przycisk Dodaj serwer**i określ szczegóły serwera — adres FQDN/IP i przyjazną nazwę poświadczeń (jeden wpis na wiersz), aby połączyć się z serwerem.
3. Kliknij pozycję **Validate** (Waliduj). Po weryfikacji zostanie wyświetlona lista serwerów, które można odnajdować.
    - Jeśli sprawdzanie poprawności nie powiedzie się dla serwera, przejrzyj błąd, najeżdżając kursorem na ikonę w kolumnie **Stan.** Rozwiązywanie problemów i sprawdzanie poprawności ponownie.
    - Aby usunąć serwer, zaznacz opcję > **Usuń**.
4. Po weryfikacji kliknij przycisk **Zapisz i rozpocznij odnajdowanie,** aby rozpocząć proces odnajdywania.

To rozpoczyna odnajdowanie. Trwa około 1,5 minuty na serwer dla metadanych odnalezionego serwera do wyświetlenia w witrynie Azure portal.

### <a name="verify-servers-in-the-portal"></a>Weryfikowanie serwerów w portalu

Po odnajdowaniu można sprawdzić, czy serwery są wyświetlane w witrynie Azure portal.

1. Otwórz pulpit nawigacyjny migracji platformy Azure.
2. W **obszarze Migracja platformy Azure — serwery, strona** > **Migracja platformy Azure: Ocena serwera,** kliknij ikonę, która wyświetla liczbę **odnalezionych serwerów**.

## <a name="set-up-an-assessment"></a>Konfigurowanie oceny

Istnieją dwa typy ocen, które można utworzyć za pomocą usługi Azure Migrate: Server Assessment.

**Ocena** | **Szczegóły** | **Dane**
--- | --- | ---
**Oparte na wydajności** | Oceny na podstawie zebranych danych dotyczących wydajności | **Zalecany rozmiar maszyny Wirtualnej:** Na podstawie danych wykorzystania procesora i pamięci.<br/><br/> **Zalecany typ dysku (dysk zarządzany standardowy lub premium):** na podstawie usług We/Wy i przepływności dysków lokalnych.
**Jako lokalnie** | Oceny oparte na rozmiarze lokalnym. | **Zalecany rozmiar maszyny Wirtualnej:** Na podstawie rozmiaru serwera lokalnego<br/><br> **Zalecany typ dysku:** Na podstawie ustawienia typu magazynu wybranego dla oceny.


### <a name="run-an-assessment"></a>Uruchamianie oceny

Przeprowadzić ocenę w następujący sposób:

1. Zapoznaj się z [najlepszymi rozwiązaniami w](best-practices-assessment.md) zakresie tworzenia ocen.
2. Na karcie **Serwery** w obszarze **Migracja platformy Azure:** kafelek Ocena serwera kliknij pozycję **Oceń**.

    ![Ocena](./media/tutorial-assess-physical/assess.png)

2. W **obszarze Oceń serwery**określ nazwę oceny.
3. Kliknij pozycję **Wyświetl wszystko**, aby sprawdzić właściwości oceny.

    ![Właściwości oceny](./media/tutorial-assess-physical/view-all.png)

3. W **obszarze Zaznacz lub utwórz grupę**wybierz pozycję **Utwórz nowy**i określ nazwę grupy. Grupa gromadzi jeden lub więcej serwerów razem do oceny.
4. W **obszarze Dodaj maszyny do grupy**wybierz serwery, które chcesz dodać do grupy.
5. Kliknij **przycisk Utwórz ocenę,** aby utworzyć grupę, a następnie uruchom ocenę.

    ![Tworzenie oceny](./media/tutorial-assess-physical/assessment-create.png)

6. Po utworzeniu oceny wyświetl ją w **obszarze Serwery** > **Migracji platformy Azure:** > **Oceny**oceny serwera .
7. Kliknij polecenie **Eksportuj ocenę**, aby pobrać ocenę jako plik programu Excel.



## <a name="review-an-assessment"></a>Przegląd oceny

W ocenie opisano:

- **Gotowość platformy Azure:** Czy serwery nadają się do migracji na platformę Azure.
- **Szacowanie kosztów miesięcznych:** szacowane miesięczne koszty obliczeń i magazynu związane z uruchamianiem serwerów na platformie Azure.
- **Miesięczne szacowanie kosztów magazynowania:** Szacowane koszty magazynu dysków po migracji.

### <a name="view-an-assessment"></a>Wyświetlanie oceny

1. W **obszarze** >  Cele migracji**Serwery**kliknij pozycję **Oceny** w obszarze **Migracja platformy Azure: Ocena serwera**.
2. W **ocenie**kliknij na ocenę, aby ją otworzyć.

    ![Podsumowanie oceny](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Przejrzyj gotowość platformy Azure

1. W **gotowości platformy Azure**sprawdź, czy serwery są gotowe do migracji na platformę Azure.
2. Przejrzyj stan:
    - **Gotowe do platformy Azure:** Usługa Azure Migrate zaleca rozmiar maszyny Wirtualnej i szacowane koszty dla maszyn wirtualnych w ocenie.
    - **Gotowy z warunkami:** Pokazuje problemy i sugerowane środki zaradcze.
    - **Nie jest gotowy na platformę Azure:** pokazuje problemy i sugerowane rozwiązania.
    - **Gotowość nieznany:** używane, gdy usługa Azure Migrate nie może ocenić gotowości z powodu problemów z dostępnością danych.

2. Kliknij stan **gotowości platformy Azure.** Można wyświetlić szczegóły gotowości serwera i przejść do szczegółów, aby wyświetlić szczegóły serwera, w tym ustawienia obliczeniowe, magazynowe i sieciowe.



### <a name="review-cost-details"></a>Przejrzyj szczegóły kosztów

Ten widok zawiera szacowany koszt obliczeń i magazynu uruchamiania maszyn wirtualnych na platformie Azure.

1. Przejrzyj miesięczne koszty obliczeń i magazynu. Koszty są agregowane dla wszystkich serwerów w ocenianej grupie.

    - Szacunki kosztów są oparte na zaleceniach dotyczących rozmiaru komputera oraz jego dyskach i właściwościach.
    - Wyświetlane są szacowane miesięczne koszty obliczeń i magazynu.
    - Szacowanie kosztów służy do uruchamiania serwerów lokalnych jako maszyn wirtualnych IaaS. Ocena programu Azure Migrate Server nie uwzględnia kosztów paas lub SaaS.

2. Możesz przejrzeć miesięczne szacunki kosztów magazynowania. W tym widoku są wyświetlane zagregowane koszty magazynowania dla ocenianej grupy, podzielone na różne typy dysków magazynu.
3. Można przejść do szczegółów dla określonych serwerów.


### <a name="review-confidence-rating"></a>Przegląd oceny zaufania

Podczas uruchamiania ocen opartych na wydajności do oceny jest przypisywana ocena zaufania.

![Ocena zaufania](./media/tutorial-assess-physical/confidence-rating.png)

- Przyznawana jest ocena od 1 gwiazdki (najniższa) do 5 gwiazdek (najwyższa).
- Ocena zaufania pomaga oszacować wiarygodność zaleceń dotyczących rozmiaru dostarczonych w ocenie.
- Ocena zaufania jest oparta na dostępności punktów danych potrzebnych do obliczenia oceny.

Oceny ufności dla oceny są następujące.

**Dostępność punktów danych** | **Ocena zaufania**
--- | ---
0%–20% | 1 gwiazdka
21%–40% | 2 gwiazdki
41%–60% | 3 gwiazdki
61%–80% | 4 gwiazdki
81%–100% | 5 gwiazdek

[Dowiedz się więcej](best-practices-assessment.md#best-practices-for-confidence-ratings) o sprawdzonych praktykach dotyczących klasyfikacji ufności.


## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie urządzenia migracji platformy Azure
> * Stworzono i przejrzano ocenę

Przejdź do trzeciego samouczka z tej serii, aby dowiedzieć się, jak przeprowadzić migrację serwerów fizycznych na platformę Azure za pomocą usługi Azure Migrate: Migracja serwera.

> [!div class="nextstepaction"]
> [Migrowanie serwerów fizycznych](./tutorial-migrate-physical-virtual-machines.md)
