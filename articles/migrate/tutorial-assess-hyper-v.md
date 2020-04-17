---
title: Ocena maszyn wirtualnych funkcji Hyper V pod kątem migracji na platformę Azure za pomocą usługi Azure Migrate | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób oceny lokalnych maszyn wirtualnych funkcji Hyper V do migracji na platformę Azure przy użyciu oceny serwera migracji usługi Azure.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: c627902268af3a91e172223c1741dd24ea21fa92
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535455"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Ocena maszyn wirtualnych funkcji Hyper V za pomocą oceny serwera migracji platformy Azure

W tym artykule pokazano, jak ocenić lokalne maszyny wirtualne z programem Hyper V przy użyciu narzędzia [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool)


Ten samouczek jest drugim z serii, który pokazuje, jak oceniać i migrować maszyny wirtualne funkcji Hyper-V na platformę Azure. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie projektu migracji platformy Azure.
> * Konfigurowanie i rejestrowanie urządzenia migracji platformy Azure.
> * Rozpocznij ciągłe odnajdowanie lokalnych maszyn wirtualnych.
> * Grupa odkryła maszyny wirtualne i oceń grupę.
> * Przejrzyj ocenę.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrażania dla scenariusza, dzięki czemu można szybko skonfigurować weryfikacji koncepcji. Samouczki używają opcji domyślnych tam, gdzie to możliwe, i nie pokazują wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z artykułami instruktażowymi.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) przed rozpoczęciem.


## <a name="prerequisites"></a>Wymagania wstępne

- [Ukończ](tutorial-prepare-hyper-v.md) pierwszy samouczek z tej serii. Jeśli nie, instrukcje w tym samouczku nie będzie działać.
- Oto, co powinieneś zrobić w pierwszym samouczku:
    - [Przygotuj platformę Azure](tutorial-prepare-hyper-v.md#prepare-azure) do pracy z programem Azure Migrate.
    - Przygotuj środowisko [Hyper-V](tutorial-prepare-hyper-v.md#prepare-hyper-v-for-assessment) i ocenę maszyn wirtualnych.
    - [Sprawdź,](tutorial-prepare-hyper-v.md#prepare-for-appliance-deployment) czego potrzebujesz, aby wdrożyć urządzenie migracji platformy Azure do oceny funkcji Hyper-V.

## <a name="set-up-an-azure-migrate-project"></a>Konfigurowanie projektu migracji platformy Azure

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W wynikach wyszukiwania wybierz pozycję **Azure Migrate**.
3. W obszarze **Omówienie**, **Odnajdywanie, ocena i migracja serwerów** kliknij pozycję **Oceń i zmigruj serwery**.

    ![Odkrywanie i ocenianie serwerów](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. W obszarze **Wprowadzenie** kliknij pozycję **Dodaj narzędzia**.
5. Na karcie **Migruj projekt** wybierz subskrypcję platformy Azure i utwórz grupę zasobów, jeśli jej nie masz.
6. W **obszarze Szczegóły projektu**określ nazwę projektu i region, w którym chcesz utworzyć projekt. Przejrzyj obsługiwane obszary geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [rządowych](migrate-support-matrix.md#supported-geographies-azure-government).

    - Region projektu jest używany tylko do przechowywania metadanych zebranych z lokalnych maszyn wirtualnych.
    - Podczas migracji maszyn wirtualnych można wybrać inny region docelowy platformy Azure. Wszystkie regiony platformy Azure są obsługiwane dla docelowego migracji.

    ![Tworzenie projektu migracji platformy Azure](./media/tutorial-assess-hyper-v/migrate-project.png)

7. Kliknij przycisk **Dalej**.
8. W **narzędziu do oceny Wybierz**wybierz pozycję Azure **Migrate: Server Assessment** > **Next**.

    ![Tworzenie projektu migracji platformy Azure](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. W obszarze **Wybierz narzędzie migracji** wybierz pozycję **Pomiń na razie dodawanie narzędzia migracji** > **Dalej**.
10. W **obszarze Recenzja + dodawanie narzędzi**przejrzyj ustawienia i kliknij pozycję Dodaj **narzędzia**.
11. Zaczekaj kilka minut, aż projekt usługi Azure Migrate zostanie wdrożony. Nastąpi przekierowanie do strony projektu. Jeśli nie widzisz projektu, możesz uzyskać do niego dostęp z obszaru **Serwery** na pulpicie nawigacyjnym usługi Azure Migrate.

## <a name="set-up-the-azure-migrate-appliance"></a>Konfigurowanie urządzenia migracji platformy Azure


Usługa Azure Migrate:Server Assessment używa lekkiego urządzenia migracji platformy Azure. Urządzenie wykonuje odnajdowanie maszyn wirtualnych i wysyła metadane maszyny Wirtualnej i dane o wydajności do usługi Azure Migrate. Urządzenie można skonfigurować na wiele sposobów.

- Skonfiguruj na maszynie wirtualnej funkcji Hyper-V przy użyciu pobranego dysku VHD z programem Hyper-V. Jest to metoda używana w tym samouczku.
- Skonfiguruj na maszynie wirtualnej funkcji Hyper-V lub na komputerze fizycznym za pomocą skryptu instalatora programu PowerShell. [Ta metoda](deploy-appliance-script.md) powinna być używana, jeśli nie można skonfigurować maszyny Wirtualnej przy użyciu dysku wirtualnego lub jeśli jesteś w usłudze Azure Government.

Po utworzeniu urządzenia można sprawdzić, czy można połączyć się z oceną migracji:serwera platformy Azure, skonfigurować go po raz pierwszy i zarejestrować go w projekcie migracji platformy Azure.

### <a name="download-the-vhd"></a>Pobierz dysk VHD

Pobierz spakowany szablon VHD dla urządzenia.

1. W **obszarze** > Cele migracji**Serwery** > **usługi Azure Migrate: Ocena serwera**kliknij przycisk **Odkryj**.
2. W **discover maszyny** > **Czy twoje maszyny są zwirtualizowane?**, kliknij **przycisk Tak, z Hyper-V**.
3. Kliknij **przycisk Pobierz,** aby pobrać plik VHD.

    ![Pobieranie maszyny wirtualnej](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Weryfikowanie zabezpieczeń

Przed wdrożeniem pliku jest bezpieczny, zanim go wdrożysz.

1. Na maszynie, na którą pobrano plik, otwórz okno wiersza polecenia administratora.

2. Uruchom następujące polecenie programu PowerShell, aby wygenerować skrót dla pliku ZIP
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Przykład użycia: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  W przypadku urządzenia w wersji 2.19.07.30 wygenerowany skrót powinien być zgodny z tymi ustawieniami.

  **Algorytm** | **Wartość skrótu**
  --- | ---
  MD5 | 29a7531f32bcf69f32d964fa5ae950bc
  SHA256 | 37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31

### <a name="create-the-appliance-vm"></a>Tworzenie maszyny wirtualnej urządzenia

Zaimportuj pobrany plik i utwórz maszynę wirtualną.

1. Po pobraniu spakowany plik VHD do hosta funkcji Hyper-V, na którym zostanie umieszczona maszyna wirtualna urządzenia, wyodrębnić spakowany plik.
    - W wyodrębnionej lokalizacji plik rozpakowuje się do folderu o nazwie **AzureMigrateAppliance_VersionNumber**.
    - Ten folder zawiera podfolder, zwany także **AzureMigrateAppliance_VersionNumber**.
    - Ten podfolder zawiera trzy kolejne podfoldery - **migawki,** **wirtualne dyski twarde**i **maszyny wirtualne.**

2. Otwórz Menedżera funkcji Hyper-V. W **akcji**kliknij pozycję **Importuj maszynę wirtualną**.

    ![Wdrażanie dysku VHD](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. W Kreatorze importu maszyny wirtualnej > **przed rozpoczęciem**kliknij przycisk **Dalej**.
3. W **obszarze Znajdź folder**wybierz folder Maszyny **wirtualne.** Następnie kliknij przycisk **Dalej**.
1. W **obszarze Wybierz maszynę wirtualną**kliknij przycisk **Dalej**.
2. W **2014 r. kliknij**pozycję **Kopiuj maszynę wirtualną (utwórz nowy unikatowy identyfikator).** Następnie kliknij przycisk **Dalej**.
3. W **obszarze Wybierz miejsce docelowe**pozostaw ustawienie domyślne. Kliknij przycisk **Dalej**.
4. W **folderach magazynu**pozostaw ustawienie domyślne. Kliknij przycisk **Dalej**.
5. W **obszarze Wybierz sieć**określ przełącznik wirtualny, którego będzie używać maszyna wirtualna. Przełącznik wymaga łączności z Internetem, aby wysłać dane na platformę Azure. [Dowiedz się więcej](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) o tworzeniu przełącznika wirtualnego.
6. W **podsumowaniu**przejrzyj ustawienia. Następnie kliknij przycisk **Zakończ**.
7. W Menedżerze funkcji Hyper-V > **maszyn wirtualnych**uruchom maszynę wirtualną.


## <a name="verify-appliance-access-to-azure"></a>Weryfikowanie dostępu urządzenia do platformy Azure

Upewnij się, że maszyna wirtualna urządzenia może łączyć się z adresami URL platformy Azure dla chmur [publicznych](migrate-appliance.md#public-cloud-urls) i [rządowych.](migrate-appliance.md#government-cloud-urls)

### <a name="configure-the-appliance"></a>Konfigurowanie urządzenia

Skonfiguruj urządzenie po raz pierwszy.

> [!NOTE]
> Jeśli urządzenie zostanie skonfigurowane przy użyciu [skryptu programu PowerShell](deploy-appliance-script.md) zamiast pobranego dysku VHD, pierwsze dwa kroki w tej procedurze nie są istotne.

1. W programie Hyper-V Manager > **maszyn wirtualnych**kliknij prawym przyciskiem myszy maszynę wirtualną > **Connect**.
2. Podaj język, strefę czasową i hasło urządzenia.
3. Otwórz przeglądarkę na dowolnym komputerze, który może połączyć się z maszyną wirtualną, i otwórz adres URL aplikacji internetowej urządzenia: **https:// nazwę urządzenia lub adres*IP:* 44368**.

   Alternatywnie możesz otworzyć aplikację z pulpitu urządzenia, klikając skrót do aplikacji.
1. W aplikacji sieci web > **Konfigurowanie wymagań wstępnych**wykonaj następujące czynności:
    - **Licencja**: Zaakceptuj postanowienia licencyjne i przeczytaj informacje innych firm.
    - **Łączność:** Aplikacja sprawdza, czy maszyna wirtualna ma dostęp do Internetu. Jeśli maszyna wirtualna używa serwera proxy:
      - Kliknij pozycję **Ustawienia serwera proxy**i określ adres http://ProxyIPAddress http://ProxyFQDNserwera proxy i port nasłuchiwania w formularzu lub .
      - Jeśli serwer proxy wymaga uwierzytelnienia, wprowadź poświadczenia.
      - Obsługiwane są tylko serwery proxy HTTP.
    - **Synchronizacja czasu:** czas jest weryfikowany. Czas na urządzeniu powinien być zsynchronizowany z czasem internetowym, aby odnajdowanie maszyn wirtualnych działało poprawnie.
    - **Zainstaluj aktualizacje:** Ocena programu Azure Migrate Server sprawdza, czy urządzenie ma zainstalowane najnowsze aktualizacje.

### <a name="register-the-appliance-with-azure-migrate"></a>Zarejestruj urządzenie za pomocą usługi Azure Migrate

1. Kliknij **pozycję Zaloguj**się . Jeśli nie jest wyświetlany, upewnij się, że wyłączono blokowanie wyskakujących wyskakujących w przeglądarce.
2. Na nowej karcie zaloguj się przy użyciu poświadczeń platformy Azure.
    - Zaloguj się przy użyciu swojej nazwy użytkownika i hasła.
    - Logowanie za pomocą numeru PIN nie jest obsługiwane.
3. Po pomyślnym zalogowaniu wróć do aplikacji sieci web.
4. Wybierz subskrypcję, w której został utworzony projekt migracji platformy Azure. Następnie wybierz projekt.
5. Określ nazwę urządzenia. Nazwa powinna być alfanumeryczna z 14 znakami lub mniej.
6. Kliknij **pozycję Zarejestruj**.


### <a name="delegate-credentials-for-smb-vhds"></a>Delegowanie poświadczeń dla identyfikatorów VHD SMB

Jeśli używasz wirtualnych identyfikatorów na małych i średnich przedsiębiorstwach, należy włączyć delegowanie poświadczeń z urządzenia do hostów funkcji Hyper-V. Wymaga to następujących czynności:

- Włącz każdy host do działania jako pełnomocnik dla urządzenia. Jeśli po samouczki w kolejności, zrobiłeś to w poprzednim samouczku, kiedy przygotowane Hyper-V do oceny i migracji. Należy skonfigurować credssp dla hostów [ręcznie](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts)lub przez [uruchomienie skryptu,](tutorial-prepare-hyper-v.md#prepare-with-a-script) który to robi.
- Włącz delegowanie credssp, aby urządzenie migracji platformy Azure działało jako klient, delegując poświadczenia do hosta.

Włącz urządzenie w następujący sposób:

#### <a name="option-1"></a>Opcja 1

Na maszynie Wirtualnej urządzenia uruchom to polecenie. HyperVHost1/HyperVHost2 są przykładowymi nazwami hostów.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
```

Przykład: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>Opcja 2

Alternatywnie, należy to zrobić w edytorze lokalnych zasad grupy na urządzeniu:

1. W**okolicy konfiguracji komputera** **lokalnego** > kliknij pozycję**Delegowanie poświadczeń****systemu** >  **szablonów administracyjnych** > .
2. Kliknij dwukrotnie pozycję **Zezwalaj na delegowanie nowych poświadczeń**i wybierz pozycję **Włączone**.
3. W **obszarze Opcje**kliknij pozycję **Pokaż**i dodaj do listy każdy host funkcji Hyper-V, który chcesz odkryć, z **wsmanem/** jako prefiksem.
4. Następnie w obszarze **Delegowanie poświadczeń**kliknij dwukrotnie pozycję **Zezwalaj na delegowanie nowych poświadczeń za pomocą uwierzytelniania serwera tylko dla technologii NTLM**. Ponownie dodaj do listy każdy host funkcji Hyper-V, który chcesz odkryć, z **wsman/** jako prefiksem.

## <a name="start-continuous-discovery"></a>Rozpocznij ciągłe odnajdowanie

Połącz się z urządzenia do hostów lub klastrów funkcji Hyper-V i rozpocznij odnajdowanie maszyn wirtualnych.

1. W **obszarze Nazwa użytkownika** i **Hasło**określ poświadczenia konta, których urządzenie będzie używać do odnajdywać maszyny wirtualne. Określ przyjazną nazwę poświadczeń i kliknij przycisk **Zapisz szczegóły**.
2. Kliknij **pozycję Dodaj hosta**i określ szczegóły hosta/klastra funkcji Hyper-V.
3. Kliknij pozycję **Validate** (Waliduj). Po weryfikacji wyświetlana jest liczba maszyn wirtualnych, które można odnajdować na każdym hoście/klastrze.
    - Jeśli sprawdzanie poprawności nie powiedzie się dla hosta, przejrzyj błąd, najeżdżając kursorem na ikonę w kolumnie **Stan.** Rozwiązywanie problemów i sprawdzanie poprawności ponownie.
    - Aby usunąć hosty lub klastry, zaznacz > **Usuń**.
    - Nie można usunąć określonego hosta z klastra. Można usunąć tylko cały klaster.
    - Można dodać klaster, nawet jeśli występują problemy z określonymi hostami w klastrze.
4. Po weryfikacji kliknij przycisk **Zapisz i rozpocznij odnajdowanie,** aby rozpocząć proces odnajdywania.

To rozpoczyna odnajdowanie. Trwa około 1,5 minuty na hoście dla metadanych odnalezionych serwerów do wyświetlenia w witrynie Azure portal.

### <a name="verify-vms-in-the-portal"></a>Weryfikowanie maszyn wirtualnych w portalu

Po zakończeniu odnajdywania można sprawdzić, czy maszyny wirtualne są wyświetlane w portalu.

1. Otwórz pulpit nawigacyjny migracji platformy Azure.
2. W **obszarze Migracja platformy Azure — serwery, strona** > **Migracja platformy Azure: Ocena serwera,** kliknij ikonę, która wyświetla liczbę **odnalezionych serwerów**.

## <a name="set-up-an-assessment"></a>Konfigurowanie oceny

Istnieją dwa typy ocen, które można uruchomić przy użyciu oceny serwera migracji usługi Azure.

**Ocena** | **Szczegóły** | **Dane**
--- | --- | ---
**Oparte na wydajności** | Oceny na podstawie zebranych danych dotyczących wydajności | **Zalecany rozmiar maszyny Wirtualnej:** Na podstawie danych wykorzystania procesora i pamięci.<br/><br/> **Zalecany typ dysku (dysk zarządzany standardowy lub premium):** na podstawie usług We/Wy i przepływności dysków lokalnych.
**Jako lokalnie** | Oceny oparte na rozmiarze lokalnym. | **Zalecany rozmiar maszyny Wirtualnej:** Na podstawie lokalnego rozmiaru maszyny Wirtualnej<br/><br> **Zalecany typ dysku:** Na podstawie ustawienia typu magazynu wybranego dla oceny.



### <a name="run-an-assessment"></a>Uruchamianie oceny

Przeprowadzić ocenę w następujący sposób:

1. Zapoznaj się z [najlepszymi rozwiązaniami w](best-practices-assessment.md) zakresie tworzenia ocen.
2. W **obszarze Migracja na** > **platformę Azure: Ocena serwera**kliknij przycisk **Oceń**.

    ![Ocena](./media/tutorial-assess-hyper-v/assess.png)

3. W **obszarze Oceniaj serwery**określ nazwę oceny.
4. Kliknij pozycję **Wyświetl wszystko**, aby sprawdzić właściwości oceny.

    ![Właściwości oceny](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. W **obszarze Zaznacz lub utwórz grupę**wybierz pozycję **Utwórz nowy**i określ nazwę grupy. Grupa gromadzi jedną lub więcej maszyn wirtualnych razem do oceny.
4. W **obszarze Dodaj maszyny do grupy**wybierz maszyny wirtualne, które mają być dodane do grupy.
5. Kliknij **przycisk Utwórz ocenę,** aby utworzyć grupę, a następnie uruchom ocenę.

    ![Tworzenie oceny](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Po utworzeniu oceny wyświetl ją w obszarze **Serwery** > **Migracji platformy Azure: Ocena serwera**.
7. Kliknij polecenie **Eksportuj ocenę**, aby pobrać ocenę jako plik programu Excel.


## <a name="review-an-assessment"></a>Przegląd oceny

W ocenie opisano:

- **Gotowość platformy Azure:** Czy maszyny wirtualne nadają się do migracji na platformę Azure.
- **Szacowanie kosztów miesięcznych:** szacowane miesięczne koszty obliczeń i magazynu związane z uruchamianiem maszyn wirtualnych na platformie Azure.
- **Miesięczne szacowanie kosztów magazynowania:** Szacowane koszty magazynu dysków po migracji.


### <a name="view-an-assessment"></a>Wyświetlanie oceny

1. W **celach** >  migracji**Serwery** > **Usługi Azure Migrate: Ocena serwera**, kliknij pozycję **Oceny**.
2. W **ocenie**kliknij na ocenę, aby ją otworzyć.

    ![Podsumowanie oceny](./media/tutorial-assess-hyper-v/assessment-summary.png)


### <a name="review-azure-readiness"></a>Przejrzyj gotowość platformy Azure

1. W **gotowości platformy Azure**sprawdź, czy maszyny wirtualne są gotowe do migracji na platformę Azure.
2. Przejrzyj stan maszyny Wirtualnej:
    - **Gotowe do platformy Azure:** Usługa Azure Migrate zaleca rozmiar maszyny Wirtualnej i szacowane koszty dla maszyn wirtualnych w ocenie.
    - **Gotowy z warunkami:** Pokazuje problemy i sugerowane środki zaradcze.
    - **Nie jest gotowy na platformę Azure:** pokazuje problemy i sugerowane rozwiązania.
    - **Gotowość nieznany:** używane, gdy usługa Azure Migrate nie może ocenić gotowości z powodu problemów z dostępnością danych.

2. Kliknij stan **gotowości platformy Azure.** Można wyświetlić szczegóły gotowości maszyny Wirtualnej i przejść do szczegółów, aby wyświetlić szczegóły maszyny Wirtualnej, w tym ustawienia obliczeniowe, magazynu i sieci.

### <a name="review-cost-details"></a>Przejrzyj szczegóły kosztów

Ten widok zawiera szacowany koszt obliczeń i magazynu uruchamiania maszyn wirtualnych na platformie Azure.

1. Przejrzyj miesięczne koszty obliczeń i magazynu. Koszty są agregowane dla wszystkich maszyn wirtualnych w ocenianej grupie.

    - Szacunki kosztów są oparte na zaleceniach dotyczących rozmiaru komputera oraz jego dyskach i właściwościach.
    - Wyświetlane są szacowane miesięczne koszty obliczeń i magazynu.
    - Szacowanie kosztów jest do uruchamiania lokalnych maszyn wirtualnych jako maszyny wirtualne IaaS. Ocena programu Azure Migrate Server nie uwzględnia kosztów paas lub SaaS.

2. Możesz przejrzeć miesięczne szacunki kosztów magazynowania. W tym widoku są wyświetlane zagregowane koszty magazynowania dla ocenianej grupy, podzielone na różne typy dysków magazynu.
3. Można przejść do szczegółów, aby wyświetlić szczegółowe informacje dotyczące określonych maszyn wirtualnych.


### <a name="review-confidence-rating"></a>Przegląd oceny zaufania

Podczas uruchamiania ocen opartych na wydajności do oceny jest przypisywana ocena zaufania.

![Ocena zaufania](./media/tutorial-assess-hyper-v/confidence-rating.png)

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

Przejdź do trzeciego samouczka z tej serii, aby dowiedzieć się, jak przeprowadzić migrację maszyn wirtualnych funkcji Hyper-V na platformę Azure za pomocą migracji serwera migracji usługi Azure.

> [!div class="nextstepaction"]
> [Migrowanie maszyn wirtualnych funkcji Hyper-V](./tutorial-migrate-hyper-v.md)
