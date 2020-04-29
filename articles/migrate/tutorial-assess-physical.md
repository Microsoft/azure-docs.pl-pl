---
title: Ocenianie serwerów fizycznych na potrzeby migracji na platformę Azure za pomocą oceny serwera Azure Migrate
description: Opisuje, jak oceniać lokalne serwery fizyczne do migracji na platformę Azure przy użyciu oceny serwera Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: b36cba18bd154cd5d14e16a9f8bf85cda6bf87a8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81535438"
---
# <a name="assess-physical-servers-with-azure-migrateserver-assessment"></a>Ocenianie serwerów fizycznych za pomocą Azure Migrate: Ocena serwera

W tym artykule pokazano, jak oceniać lokalne serwery fizyczne przy użyciu Azure Migrate: narzędzia do oceny serwera.

[Azure Migrate](migrate-services-overview.md) udostępnia centrum narzędzi, które ułatwiają odnajdywanie, ocenianie i Migrowanie aplikacji, infrastruktury i obciążeń do Microsoft Azure. Centrum obejmuje narzędzia Azure Migrate i oferty niezależnych dostawców oprogramowania (ISV) innych firm.

Ten samouczek jest drugą częścią serii, która pokazuje, jak oceniać i migrować serwery fizyczne na platformę Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Skonfiguruj projekt Azure Migrate.
> * Skonfiguruj urządzenie Azure Migrate uruchamiane lokalnie, aby ocenić serwery fizyczne.
> * Rozpocznij ciągłe wykrywanie lokalnych serwerów fizycznych. Urządzenie wysyła dane dotyczące konfiguracji i wydajności dla odnalezionych serwerów na platformę Azure.
> * Grupuj odnalezione serwery i oceniaj grupę serwerów.
> * Przejrzyj ocenę.

> [!NOTE]
> Samouczki przedstawiają najprostszą ścieżkę wdrożenia dla scenariusza, dzięki czemu można szybko skonfigurować weryfikację koncepcji. Samouczki korzystają z domyślnych opcji, jeśli jest to możliwe, i nie wyświetlają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z artykułami z instrukcjami.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) .


## <a name="prerequisites"></a>Wymagania wstępne

- [Wykonaj](tutorial-prepare-physical.md) pierwszy samouczek z tej serii. Jeśli tego nie zrobisz, instrukcje podane w tym samouczku nie będą działały.
- Oto co należy zrobić w pierwszym samouczku:
    - [Skonfiguruj uprawnienia platformy Azure](tutorial-prepare-physical.md) dla Azure Migrate.
    - [Przygotuj serwery fizyczne](tutorial-prepare-physical.md#prepare-for-physical-server-assessment) do oceny. Wymagania dotyczące urządzenia należy zweryfikować. Należy również mieć skonfigurowane konto do odnajdowania serwera fizycznego. Wymagane porty powinny być dostępne i należy mieć świadomość adresów URL potrzebnych do uzyskania dostępu do platformy Azure.




## <a name="set-up-an-azure-migrate-project"></a>Konfigurowanie projektu Azure Migrate

Skonfiguruj nowy projekt usługi Azure Migrate w następujący sposób.

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.
3. W obszarze **Omówienie**, **Odnajdywanie, ocena i migracja serwerów** kliknij pozycję **Oceń i zmigruj serwery**.

    ![Odnajdywanie i ocenianie serwerów](./media/tutorial-assess-physical/assess-migrate.png)

4. W obszarze **Wprowadzenie** kliknij pozycję **Dodaj narzędzia**.
5. W obszarze **Projekt migracji**wybierz subskrypcję platformy Azure i utwórz grupę zasobów, jeśli jej nie masz.  
6. W obszarze **szczegóły projektu**Określ nazwę projektu i lokalizację geograficzną, w której chcesz utworzyć projekt. Przejrzyj obsługiwane lokalizacje geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [instytucji rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

    - Lokalizacja geograficzna projektu służy tylko do przechowywania metadanych zebranych z serwerów lokalnych.
    - Podczas przeprowadzania migracji można wybrać dowolny region docelowy.

    ![Tworzenie projektu Azure Migrate](./media/tutorial-assess-physical/migrate-project.png)


7. Kliknij przycisk **Dalej**.
8. W **narzędziu Wybierz ocenę**wybierz pozycję **Azure Migrate: Ocena** > serwera**dalej**.

    ![Tworzenie projektu Azure Migrate](./media/tutorial-assess-physical/assessment-tool.png)

9. W obszarze **Wybierz narzędzie migracji** wybierz pozycję **Pomiń na razie dodawanie narzędzia migracji** > **Dalej**.
10. W oknie **Recenzja + Dodawanie narzędzi**przejrzyj ustawienia, a następnie kliknij pozycję **Dodaj narzędzia**.
11. Zaczekaj kilka minut, aż projekt usługi Azure Migrate zostanie wdrożony. Nastąpi przekierowanie do strony projektu. Jeśli nie widzisz projektu, możesz uzyskać do niego dostęp z obszaru **Serwery** na pulpicie nawigacyjnym usługi Azure Migrate.


## <a name="set-up-the-appliance"></a>Konfigurowanie urządzenia

Azure Migrate: Ocena serwera uruchamia lekkie urządzenie.

- To urządzenie wykonuje odnajdywanie serwera fizycznego i wysyła metadane serwera i dane wydajności do oceny serwera Azure Migrate.
- Aby skonfigurować urządzenie:
    - Pobierz spakowany plik ze skryptem Instalatora Azure Migrate z Azure Portal.
    - Wyodrębnij zawartość z pliku spakowanego. Uruchom konsolę programu PowerShell z uprawnieniami administracyjnymi.
    - Wykonaj skrypt programu PowerShell, aby uruchomić aplikację sieci Web urządzenia.
    - Skonfiguruj urządzenie po raz pierwszy i zarejestruj je w projekcie Azure Migrate.
- Dla jednego projektu Azure Migrate można skonfigurować wiele urządzeń. Na wszystkich urządzeniach można odnajdywać dowolną liczbę serwerów fizycznych. Na urządzenie można odnaleźć maksymalnie 250 serwerów.

### <a name="download-the-installer-script"></a>Pobierz skrypt Instalatora

Pobierz spakowany plik dla urządzenia.

1. W obszarze**serwery** >  **celów** > migracji**Azure Migrate: Ocena serwera**, kliknij przycisk **odkryj**.
2. W obszarze **odnajdywanie** > maszyn**są zwirtualizowane maszyny?** kliknij pozycję **niezwirtualizowane/inne**.
3. Kliknij pozycję **Pobierz** , aby pobrać plik zip.

    ![Pobierz instalatora](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Weryfikuj zabezpieczenia

Przed wdrożeniem należy sprawdzić, czy spakowany plik jest bezpieczny.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.
2. Uruchom następujące polecenie, aby wygenerować skrót dla pliku spakowanego:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Przykładowe użycie chmury publicznej:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
    - Przykładowe użycie w chmurze dla instytucji rządowych:```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Weryfikuj wartości skrótu:
 
    - W przypadku chmury publicznej (dla najnowszej wersji urządzenia):

        **Algorytm** | **Wartość skrótu**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cdd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

    - W przypadku platformy Azure dla instytucji rządowych (dla najnowszej wersji urządzenia):

        **Algorytm** | **Wartość skrótu**
          --- | ---
          MD5 | f81c155fc4a1409901caea948713913f

### <a name="run-the-azure-migrate-installer-script"></a>Uruchom skrypt Instalatora Azure Migrate

Skrypt Instalatora wykonuje następujące czynności:

- Instaluje agentów i aplikację sieci Web na potrzeby odnajdywania i oceny serwera fizycznego.
- Zainstaluj role systemu Windows, w tym usługi aktywacji systemu Windows, usług IIS i programu PowerShell ISE.
- Pobierz i zainstaluj moduł, który ma zostać przezapisywalny usług IIS. [Dowiedz się więcej](https://www.microsoft.com/download/details.aspx?id=7435).
- Aktualizuje klucz rejestru (HKLM) z trwałymi ustawieniami ustawień dla Azure Migrate.
- Tworzy następujące pliki w ścieżce:
    - **Pliki konfiguracji**:%ProgramData%\Microsoft Azure\Config
    - **Pliki dziennika**:%ProgramData%\Microsoft Azure\Logs

Uruchom skrypt w następujący sposób:

1. Wyodrębnij spakowany plik do folderu na serwerze, który będzie hostować urządzenie.  Upewnij się, że skrypt nie jest uruchamiany na komputerze na istniejącym urządzeniu Azure Migrate.
2. Uruchom program PowerShell na powyższym serwerze z uprawnieniami administracyjnymi (z podwyższonym poziomem uprawnień).
3. Zmień katalog programu PowerShell do folderu, w którym zawartość została wyodrębniona z pobranego pliku spakowanego.
4. Uruchom skrypt o nazwie **AzureMigrateInstaller. ps1** , uruchamiając następujące polecenie:

    - W przypadku chmury publicznej:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - Dla Azure Government:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    Po pomyślnym zakończeniu działania skryptu zostanie uruchomiona aplikacja sieci Web urządzenia.

Jeśli występują problemy, możesz uzyskać dostęp do dzienników skryptów w witrynie C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log w celu rozwiązywania problemów.

### <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że urządzenie może połączyć się z adresami URL platformy Azure dla chmur [publicznych](migrate-appliance.md#public-cloud-urls) i dla [instytucji rządowych](migrate-appliance.md#government-cloud-urls) .


### <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie po raz pierwszy.

1. Otwórz przeglądarkę na dowolnym komputerze, który może nawiązać połączenie z urządzeniem, a następnie otwórz adres URL aplikacji sieci Web urządzenia: **https://*Nazwa urządzenia lub adres IP*: 44368**.

   Możesz też otworzyć aplikację z poziomu pulpitu, klikając skrót do aplikacji.
2. W aplikacji internetowej > **skonfigurować wymagania wstępne**, wykonaj następujące czynności:
    - **Licencja**: zaakceptuj postanowienia licencyjne i przeczytaj informacje o innych firmach.
    - **Łączność**: aplikacja sprawdza, czy serwer ma dostęp do Internetu. Jeśli serwer używa serwera proxy:
        - Kliknij pozycję **Ustawienia serwera proxy**i określ adres serwera proxy i port nasłuchujący w formularzu http://ProxyIPAddress lub http://ProxyFQDN.
        - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
        - Obsługiwane są tylko serwery proxy HTTP.
    - **Synchronizacja czasu**: godzina została zweryfikowana. Czas na urządzeniu powinien być zsynchronizowany z czasem internetowym w celu poprawnego działania funkcji odnajdywania serwerów.
    - **Instalowanie aktualizacji**: ocena serwera Azure Migrate sprawdza, czy na urządzeniu zainstalowano najnowsze aktualizacje.

### <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie w Azure Migrate

1. Kliknij przycisk **Zaloguj**. Jeśli ta wartość nie jest wyświetlana, upewnij się, że w przeglądarce wyłączono blokowanie wyskakujących okienek.
2. Na nowej karcie Zaloguj się przy użyciu poświadczeń platformy Azure.
    - Zaloguj się przy użyciu nazwy użytkownika i hasła.
    - Logowanie przy użyciu numeru PIN nie jest obsługiwane.
3. Po pomyślnym zalogowaniu Wróć do aplikacji sieci Web.
4. Wybierz subskrypcję, w której został utworzony projekt Azure Migrate. Następnie wybierz projekt.
5. Określ nazwę urządzenia. Nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
6. Kliknij pozycję **zarejestruj**.


## <a name="start-continuous-discovery"></a>Uruchom odnajdywanie ciągłe

Teraz nawiąż połączenie z urządzeniem z serwerami fizycznymi, które mają zostać odnalezione, i Uruchom odnajdywanie.

1. Kliknij pozycję **Dodaj poświadczenia** , aby określić poświadczenia konta, które będą używane przez urządzenie do odnajdywania serwerów.  
2. Określ **system operacyjny**, przyjazną nazwę dla poświadczeń oraz nazwę użytkownika i hasło. Następnie kliknij przycisk **Dodaj**.
Można dodać jeden zestaw poświadczeń dla serwerów z systemami Windows i Linux.
4. Kliknij przycisk **Dodaj serwer**i określ szczegóły serwera — nazwa FQDN/adres IP i przyjazna nazwa poświadczenia (jeden wpis na wiersz), aby połączyć się z serwerem.
3. Kliknij pozycję **Validate** (Waliduj). Po sprawdzeniu poprawności zostanie wyświetlona lista serwerów, które mogą zostać odnalezione.
    - Jeśli walidacja nie powiedzie się dla serwera, przejrzyj błąd, umieszczając kursor nad ikoną w kolumnie **stan** . Usuń problemy i ponownie sprawdź poprawność.
    - Aby usunąć serwer, wybierz pozycję > **Usuń**.
4. Po sprawdzeniu poprawności kliknij przycisk **Zapisz i Rozpocznij odnajdywanie** , aby rozpocząć proces odnajdywania.

Spowoduje to uruchomienie odnajdywania. Aby metadane wykrytego serwera pojawiły się w Azure Portal, zajmie około 1,5 minut na serwer.

### <a name="verify-servers-in-the-portal"></a>Weryfikowanie serwerów w portalu

Po przeprowadzeniu odnajdywania można sprawdzić, czy serwery są wyświetlane w Azure Portal.

1. Otwórz pulpit nawigacyjny Azure Migrate.
2. W **Azure Migrate serwery** > **Azure Migrate: Strona Ocena serwera** kliknij ikonę, która wyświetla liczbę **odnalezionych serwerów**.

## <a name="set-up-an-assessment"></a>Konfigurowanie oceny

Istnieją dwa typy ocen, które można utworzyć przy użyciu Azure Migrate: Ocena serwera.

**Ocena** | **Szczegóły** | **Dane**
--- | --- | ---
**Oparta na wydajności** | Oceny oparte na zebranych danych wydajności | **Zalecany rozmiar maszyny wirtualnej**: na podstawie danych użycia procesora CPU i pamięci.<br/><br/> **Zalecany typ dysku (dysk zarządzany w warstwie Standardowa lub Premium)**: w zależności od liczby operacji we/wy na sekundę i przepływności dysków lokalnych.
**Jako lokalne** | Oceny oparte na wymiarach lokalnych. | **Zalecany rozmiar maszyny wirtualnej**: na podstawie rozmiaru lokalnego serwera<br/><br> **Zalecany typ dysku**: na podstawie ustawienia typu magazynu wybieranego do oceny.


### <a name="run-an-assessment"></a>Uruchamianie oceny

Uruchom ocenę w następujący sposób:

1. Zapoznaj się z [najlepszymi rozwiązaniami](best-practices-assessment.md) dotyczącymi tworzenia ocen.
2. Na karcie **serwery** w **Azure Migrate: kafelek Ocena serwera** kliknij pozycję **Oceń**.

    ![Ocena](./media/tutorial-assess-physical/assess.png)

2. W obszarze **ocenianie serwerów**Określ nazwę oceny.
3. Kliknij pozycję **Wyświetl wszystko**, aby sprawdzić właściwości oceny.

    ![Właściwości oceny](./media/tutorial-assess-physical/view-all.png)

3. W obszarze **Wybierz lub Utwórz grupę**wybierz pozycję **Utwórz nową**, a następnie określ nazwę grupy. Grupa zbiera jeden lub więcej serwerów w celu oceny.
4. W obszarze **Dodawanie maszyn do grupy**wybierz serwery, które mają zostać dodane do grupy.
5. Kliknij pozycję **Utwórz ocenę** , aby utworzyć grupę, i uruchom ocenę.

    ![Tworzenie oceny](./media/tutorial-assess-physical/assessment-create.png)

6. Po utworzeniu oceny Wyświetl ją w obszarze **serwery** > **Azure Migrate:** > **oceny**oceny serwera.
7. Kliknij polecenie **Eksportuj ocenę**, aby pobrać ocenę jako plik programu Excel.



## <a name="review-an-assessment"></a>Przegląd oceny

Ocena zawiera opis:

- **Gotowość platformy Azure**: czy serwery są odpowiednie do migracji na platformę Azure.
- **Oszacowanie kosztów miesięcznych**: szacowane miesięczne koszty obliczeń i magazynowania na potrzeby uruchamiania serwerów na platformie Azure.
- **Oszacowanie kosztu miesięcznego magazynu**: szacowane koszty magazynu dyskowego po migracji.

### <a name="view-an-assessment"></a>Wyświetlanie oceny

1. W obszarze >  **serwery** **celów migracji**kliknij pozycję **oceny** w **Azure Migrate: Ocena serwera**.
2. W obszarze **oceny**kliknij ocenę, aby go otworzyć.

    ![Podsumowanie oceny](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Przegląd gotowości platformy Azure

1. W obszarze **gotowość do platformy Azure**Sprawdź, czy serwery są gotowe do migracji na platformę Azure.
2. Sprawdź stan:
    - **Gotowe do platformy Azure**: Azure Migrate zaleca rozmiar maszyny wirtualnej i oszacowania kosztów dla maszyn wirtualnych w ocenie.
    - **Gotowe warunki**: pokazuje problemy i sugerowane korygowanie.
    - **Nie gotowy na platformę Azure**: zawiera problemy i sugerowane korygowanie.
    - **Nieznane gotowość**: używany, gdy Azure Migrate nie może ocenić gotowości ze względu na problemy z dostępnością danych.

2. Kliknij stan **gotowości platformy Azure** . Możesz wyświetlić szczegóły gotowości serwera i przejść do szczegółów, aby zobaczyć szczegóły serwera, w tym ustawienia obliczeń, magazynu i sieci.



### <a name="review-cost-details"></a>Przejrzyj szczegóły kosztów

Ten widok przedstawia szacowany koszt obliczeń i magazynu dla uruchomionych maszyn wirtualnych na platformie Azure.

1. Zapoznaj się z miesięcznymi kosztami obliczeniowymi i magazynem. Koszty są agregowane dla wszystkich serwerów w ocenianej grupie.

    - Oszacowania kosztów opierają się na zaleceń dotyczących rozmiaru komputera oraz jego dyskach i właściwościach.
    - Wyświetlane są szacowane miesięczne koszty obliczeń i magazynu.
    - Oszacowanie kosztów służy do uruchamiania serwerów lokalnych jako maszyn wirtualnych IaaS. Ocena serwera Azure Migrate nie uwzględnia kosztów PaaS ani SaaS.

2. Możesz przejrzeć szacunkowe oszacowanie kosztów magazynowania. Ten widok przedstawia zagregowane koszty magazynu dla ocenianej grupy, podzielone na różne typy dysków magazynu.
3. Możesz przejść do szczegółów, aby zobaczyć szczegóły określonych serwerów.


### <a name="review-confidence-rating"></a>Przegląd oceny zaufania

Po uruchomieniu ocen opartych na wydajności, do oceny jest przypisywany rating zgodności.

![Ocena zaufania](./media/tutorial-assess-physical/confidence-rating.png)

- Nadawana jest Ocena z 1 – gwiazdka (najmniejsza) do 5-gwiazdka (najwyższa).
- Ocena zaufania pomaga oszacować niezawodność zaleceń dotyczących rozmiaru zapewnianych przez ocenę.
- Ocena zaufania jest oparta na dostępności punktów danych potrzebnych do obliczenia oceny.

Klasyfikacje zaufania dla oceny są następujące.

**Dostępność punktu danych** | **Ocena zaufania**
--- | ---
0%–20% | 1 gwiazdka
21%–40% | 2 gwiazdki
41%–60% | 3 gwiazdki
61%–80% | 4 gwiazdki
81%–100% | 5 gwiazdek

[Dowiedz się więcej](best-practices-assessment.md#best-practices-for-confidence-ratings) o najlepszych rozwiązaniach dotyczących klasyfikacji zaufania.


## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie urządzenia Azure Migrate
> * Tworzenie i przeglądanie oceny

Przejdź do trzeciego samouczka z serii, aby dowiedzieć się, jak przeprowadzić migrację serwerów fizycznych na platformę Azure za pomocą Azure Migrate: Migracja serwera.

> [!div class="nextstepaction"]
> [Migrowanie serwerów fizycznych](./tutorial-migrate-physical-virtual-machines.md)
