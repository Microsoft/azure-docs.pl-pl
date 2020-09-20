---
title: Ocenianie serwerów przy użyciu zaimportowanych danych serwera za pomocą narzędzia Azure Migrate Server Assessment
description: Opisuje sposób oceny serwerów lokalnych na potrzeby migracji na platformę Azure za pomocą narzędzia Azure Migrate Server Assessment przy użyciu zaimportowanych danych.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: ff7e423063859a6cdc1a4362fb030c0deb75eb32
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658703"
---
# <a name="assess-servers-by-using-imported-data"></a>Ocenianie serwerów za pomocą zaimportowanych danych

W tym artykule opisano sposób oceny serwerów lokalnych przy użyciu narzędzia [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool), importując metadane serwera w formacie wartości rozdzielanych przecinkami (CSV). Ta metoda oceny nie wymaga konfigurowania urządzenia usługi Azure Migrate w celu utworzenia oceny. Rozwiązanie przydatne jeśli:

- Chcesz utworzyć szybką, wstępną ocenę przed wdrożeniem urządzenia.
- Nie można wdrożyć urządzenia usługi Azure Migrate w organizacji.
- Nie można udostępnić poświadczeń zezwalających na dostęp do serwerów lokalnych.
- Ograniczenia zabezpieczeń uniemożliwiają zbieranie i wysyłanie danych zebranych przez urządzenie do platformy Azure. Możesz kontrolować dane, które udostępniasz w zaimportowanym pliku. Ponadto większość danych (np. podanie adresów IP) jest opcjonalna.

## <a name="before-you-start"></a>Przed rozpoczęciem

Należy pamiętać o następujących kwestiach:

- W pojedynczym pliku CSV można dodać maksymalnie 20 000 serwerów.
- Przy użyciu pliku CSV można dodać do 20 000 serwerów w projekcie usługi Azure Migrate.
- Informacje o serwerze można przekazywać wiele razy do narzędzia Server Assessment za pomocą pliku CSV.
- Zbieranie informacji o aplikacji jest przydatne w ocenie środowiska lokalnego na potrzeby migracji. Narzędzie Server Assessment obecnie jednak nie wykonuje oceny na poziomie aplikacji ani nie uwzględnia aplikacji podczas tworzenia oceny.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Konfigurowanie projektu usługi Azure Migrate.
> * Wypełnianie pliku CSV za pomocą informacji o serwerze.
> * Importowanie pliku w celu dodania informacji o serwerze do narzędzia Server Assessment.
> * Tworzenie i wyświetlanie oceny.

> [!NOTE]
> Samouczki przedstawiają najprostszą ścieżkę wdrożenia dla scenariusza, dzięki czemu można szybko skonfigurować weryfikację koncepcji. Jeśli to możliwe, samouczki używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, przejrzyj przewodniki z instrukcjami.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="set-azure-permissions-for-azure-migrate"></a>Ustawianie uprawnień platformy Azure na potrzeby usługi Azure Migrate

Twoje konto platformy Azure wymaga uprawnień do tworzenia projektu usługi Azure Migrate.

1. W witrynie Azure Portal otwórz daną subskrypcję i wybierz pozycję **Kontrola dostępu (IAM)** .
2. Na karcie **Sprawdzanie dostępu** znajdź odpowiednie konto, a następnie wybierz je, aby wyświetlić uprawnienia.
3. Upewnij się, że masz uprawnienia **współautora** lub **właściciela**.
    - Jeśli bezpłatne konto platformy Azure zostało właśnie utworzone, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, poproś właściciela o przypisanie roli.

## <a name="set-up-an-azure-migrate-project"></a>Konfigurowanie projektu usługi Azure Migrate

Aby skonfigurować nowy projekt usługi Azure Migrate:

1. W witrynie Azure Portal, w oknie **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.
3. W bloku **Omówienie**, w obszarze **Odnajdywanie, ocena i migracja serwerów** wybierz pozycję **Oceń i zmigruj serwery**.

    ![Odnajdywanie i ocenianie serwerów](./media/tutorial-assess-import/assess-migrate.png)

4. W obszarze **Wprowadzenie** wybierz pozycję **Dodaj narzędzia**.
5. W obszarze **Projekt migracji**wybierz subskrypcję platformy Azure i utwórz grupę zasobów, jeśli jej nie masz.
6. W obszarze **Szczegóły projektu** określ nazwę projektu i lokalizację geograficzną, w której chcesz utworzyć projekt. Więcej informacji:

    - Przejrzyj obsługiwane lokalizacje geograficzne [chmur publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [chmur dla instytucji rządowych](migrate-support-matrix.md#supported-geographies-azure-government).
    - Podczas przeprowadzania migracji można wybrać dowolny region docelowy.

    ![Tworzenie projektu usługi Azure Migrate](./media/tutorial-assess-import/migrate-project.png)

7. Wybierz pozycję **Dalej**.
8. W obszarze **Wybierz narzędzie oceny** wybierz pozycję **Azure Migrate: Ocena serwera** > **Dalej**.

    ![Tworzenie oceny usługi Azure Migrate](./media/tutorial-assess-import/assessment-tool.png)

9. W obszarze **Wybierz narzędzie migracji** wybierz pozycję **Pomiń na razie dodawanie narzędzia migracji** > **Dalej**.
10. W obszarze **Przegląd i dodawanie narzędzi** przejrzyj ustawienia, a następnie wybierz pozycję **Dodaj narzędzia**.
11. Zaczekaj kilka minut, aż projekt usługi Azure Migrate zostanie wdrożony. Nastąpi przekierowanie do strony projektu. Jeśli nie widzisz projektu, możesz uzyskać do niego dostęp z obszaru **Serwery** na pulpicie nawigacyjnym usługi Azure Migrate.

## <a name="prepare-the-csv"></a>Przygotowywanie pliku CSV

Pobierz szablon CSV i dodaj do niego informacje o serwerze.

### <a name="download-the-template"></a>Pobieranie szablonu

1. W obszarze **Cele migracji** > **Serwery** > **Azure Migrate: Server Assessment** wybierz pozycję **Odnajdź**.
2. W obszarze **Odnajdywanie maszyn** wybierz pozycję **Importuj przy użyciu pliku CSV**.
3. Wybierz pozycję **Pobierz**, aby pobrać szablon CSV. Alternatywnie możesz [pobrać go bezpośrednio](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Pobieranie szablonu CSV](./media/tutorial-assess-import/download-template.png)

### <a name="add-server-information"></a>Dodawanie informacji o serwerze

Zbierz dane serwera i dodaj je do pliku CSV.

- Aby zebrać dane, można je wyeksportować z narzędzi używanych do zarządzania serwerem lokalnym, takich jak VMware vSphere lub baza danych zarządzania konfiguracją (CMDB).
- Aby przejrzeć przykładowe dane, pobierz nasz [przykładowy plik](https://go.microsoft.com/fwlink/?linkid=2108405).

Poniższa tabela zawiera podsumowanie pól plików do wypełnienia:

**Nazwa pola** | **Obowiązkowe** | **Szczegóły**
--- | --- | ---
**Nazwa serwera** | Yes | Zalecamy określenie w pełni kwalifikowanej nazwy domeny (FQDN).
**Adres IP** | Nie | Adres serwera.
**Rdzenie** | Yes | Liczba rdzeni procesora przydzielonych do serwera.
**Pamięć** | Yes | Całkowita ilość pamięci RAM (w MB) przydzielona do serwera.
**Nazwa systemu operacyjnego** | Yes | System operacyjny serwera. <br/> Nazwy systemów operacyjnych, które są zgodne lub zawierają nazwy z [tej](#supported-operating-system-names) listy, są rozpoznawane w trakcie oceny.
**Wersja systemu operacyjnego** | Nie | Wersja systemu operacyjnego serwera.
**Architektura systemu operacyjnego** | Nie | Architektura systemu operacyjnego serwera <br/> Prawidłowe wartości to: x64, x86, amd64, 32-bitowy lub 64-bitowy
**Liczba dysków** | Nie | Niepotrzebna, jeśli podano szczegóły dysku.
**Rozmiar dysku 1**  | Nie | Maksymalny rozmiar dysku (w GB).<br/>Możesz dodać szczegóły dla większej liczby dysków, [dodając kolumny](#add-multiple-disks) w szablonie. Można dodać maksymalnie osiem dysków.
**Operacje odczytu dysku 1** | Nie | Operacje odczytu dysku na sekundę.
**Operacje zapisu dysku 1** | Nie | Operacje zapisu dysku na sekundę.
**Przepływność odczytu dysku 1** | Nie | Dane odczytane z dysku na sekundę (w MB na sekundę).
**Przepływność zapisu dysku 1** | Nie | Dane zapisywane na dysku na sekundę (w MB na sekundę).
**Procent użycia procesora CPU** | Nie | Wartość procentowa użycia procesora CPU.
**Procent użycia pamięci** | Nie | Wartość procentowa użycia pamięci RAM.
**Łączna liczba operacji odczytu dysku** | Nie | Operacje odczytu na sekundę dla wszystkich połączonych dysków. <br/> Użyj tego pola, jeśli nie możesz dostarczyć danych na poziomie dysku. 
**Łączna liczba operacji zapisu na dysku** | Nie | Operacje zapisu na sekundę dla wszystkich połączonych dysków. <br/> Użyj tego pola, jeśli nie możesz dostarczyć danych na poziomie dysku.
**Łączna przepływność odczytu dysków** | Nie | Dane odczytane ze wszystkich dysków (w MB na sekundę). <br/> Użyj tego pola, jeśli nie możesz dostarczyć danych na poziomie dysku. 
**Łączna przepływność zapisu dysków** | Nie | Dane zapisywane na wszystkich dyskach ( w MB na sekundę). <br/> Użyj tego pola, jeśli nie możesz dostarczyć danych na poziomie dysku.
**Przepływność ruchu przychodzącego w sieci** | Nie | Dane odebrane przez serwer (w MB na sekundę).
**Przepływność ruchu wychodzącego w sieci** | Nie | Dane przesyłane przez serwer (w MB na sekundę).
**Typ oprogramowania układowego** | Nie | Oprogramowanie układowe serwera. Wartości to „BIOS” lub „UEFI”.
**Adres MAC**| Nie | Adres MAC serwera.


### <a name="add-operating-systems"></a>Dodawanie systemów operacyjnych

Narzędzie oceny rozpoznaje określone nazwy systemów operacyjnych. Każda określona nazwa musi dokładnie odpowiadać jednemu z ciągów na [liście obsługiwanych nazw](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Dodawanie wielu dysków

Szablon zawiera domyślne pola dla pierwszego dysku. Możesz dodać podobne kolumny dla maksymalnie ośmiu dysków.

Na przykład aby określić wszystkie pola dla drugiego dysku, dodaj następujące kolumny:

- Rozmiar dysku 2
- Operacje odczytu dysku 2
- Operacje zapisu dysku 2
- Przepływność odczytu dysku 2
- Przepływność zapisu na dysku 2

Jeśli nie jesteś w stanie dostarczyć danych na poziomie dysku, możesz podać dane wydajności dysku na serwer przy użyciu poniższych pól. Aby uzyskać szczegółowe informacje na temat każdego pola, zobacz [tę](#add-server-information) sekcję.
- Łączna liczba operacji odczytu dysku
- Łączna liczba operacji zapisu na dysku
- Łączna przepływność odczytu dysków
- Łączna przepływność zapisu dysków

## <a name="import-the-server-information"></a>Importowanie informacji o serwerze

Po dodaniu informacji do szablonu CSV zaimportuj serwery do narzędzia Server Assessment.

1. W usłudze Azure Migrate, w obszarze **Odnajdywanie maszyn** przejdź do ukończonego szablonu.
2. Wybierz pozycję **Importuj**.
3. Zostanie wyświetlony stan importowania.
    - Jeśli w stanie pojawiły się ostrzeżenia, możesz je naprawić lub kontynuować bez ich rozwiązywania.
    - Aby poprawić dokładność oceny, popraw informacje o serwerze zgodnie z opisem w ostrzeżeniach.
    - Aby wyświetlić i naprawić ostrzeżenia, wybierz pozycję **Pobierz plik CSV ze szczegółami ostrzeżenia**. Ta operacja pobiera plik CSV wraz z ostrzeżeniami. Przejrzyj ostrzeżenia i rozwiąż problemy zgodnie z wymaganiami.
    - Jeśli w stanie pojawią się błędy, co powoduje, że stan importu wskazuje **Niepowodzenie**, należy naprawić te błędy przed kontynuowaniem importowania:
        1. Pobierz plik CSV, który zawiera teraz szczegóły błędu.
        1. Przejrzyj błędy i rozwiąż je w razie potrzeby. 
        1. Ponownie przekaż zmodyfikowany plik.
4. Gdy stan importowania ma wartość **Ukończono**, informacje o serwerze zostały zaimportowane.

## <a name="update-server-information"></a>Aktualizowanie informacji o serwerze

Możesz zaktualizować informacje dotyczące serwera, importując ponownie dane serwera przy użyciu tej samej **nazwy serwera**. Nie można zmodyfikować pola **Nazwa serwera**. Usuwanie serwerów nie jest obecnie obsługiwane.

## <a name="verify-servers-in-the-portal"></a>Weryfikowanie serwerów w portalu

Aby sprawdzić, czy serwery są wyświetlane w witrynie Azure Portal po wykonaniu operacji odnajdywania:

1. Otwórz pulpit nawigacyjny usługi Azure Migrate.
2. Na stronie **Azure Migrate — Serwery** > **Azure Migrate: Server Assessment** wybierz ikonę, która wyświetla liczbę **odnalezionych serwerów**.
3. Wybierz kartę **Na podstawie importu**.

## <a name="set-up-and-run-an-assessment"></a>Konfigurowanie i uruchamianie oceny

Za pomocą narzędzia Server Assessment można utworzyć dwa typy ocen.


**Typ oceny** | **Szczegóły**
--- | --- 
**Maszyna wirtualna platformy Azure** | Oceny umożliwiające migrację serwerów lokalnych do maszyn wirtualnych platformy Azure. <br/><br/> Przy użyciu tego typu oceny można ocenić lokalne [maszyny wirtualne VMware](how-to-set-up-appliance-vmware.md), [maszyny wirtualne funkcji Hyper-V](how-to-set-up-appliance-hyper-v.md) i [serwery fizyczne](how-to-set-up-appliance-physical.md) pod kątem migracji na platformę Azure.
**Rozwiązanie Azure VMware (AVS)** | Oceny umożliwiające migrację serwerów lokalnych do usługi [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Przy użyciu tego typu oceny można ocenić lokalne [maszyny wirtualne VMware](how-to-set-up-appliance-vmware.md) pod kątem migracji do usługi Azure VMware Solution (AVS).[Dowiedz się więcej](concepts-azure-vmware-solution-assessment-calculation.md)

### <a name="sizing-criteria"></a>Kryterium określania rozmiaru

Narzędzie Server Assessment oferuje dwie opcje kryteriów określania rozmiaru:

**Kryterium określania rozmiaru** | **Szczegóły** | **Dane**
--- | --- | ---
**Na podstawie wydajności** | Oceny zawierające rekomendacje na podstawie zebranych danych dotyczących wydajności | **Ocena maszyny wirtualnej platformy Azure**: Rekomendacja dotycząca rozmiaru maszyny wirtualnej jest oparta na danych użycia procesora i pamięci.<br/><br/> Rekomendacja dotycząca typu dysku (dysk HDD/SSD w warstwie Standardowa lub dyski zarządzane w warstwie Premium) jest oparta na liczbie operacji we/wy na sekundę i przepływności dysków lokalnych.<br/><br/> **Ocena usługi Azure VMware Solution (AVS)** : Rekomendacja dotycząca węzłów usługi AVS jest oparta na danych użycia procesora i pamięci.
**Zgodnie ze środowiskiem lokalnym** | Oceny, które nie wykorzystują danych wydajności w celu wydawania rekomendacji. | **Ocena maszyny wirtualnej platformy Azure**: Rekomendacja dotycząca rozmiaru maszyny wirtualnej jest oparta na lokalnym rozmiarze maszyny wirtualnej<br/><br> Zalecany typ dysku jest określany na podstawie tego, co zostało wybrane w ustawieniu typu magazynu na potrzeby oceny.<br/><br/> **Ocena usługi Azure VMware Solution (AVS)** : Rekomendacja dotycząca węzłów usługi AVS jest oparta na lokalnym rozmiarze maszyny wirtualnej.


Aby uruchomić ocenę:

1. Przejrzyj [najlepsze rozwiązania](best-practices-assessment.md) dotyczące tworzenia ocen.
2. Na karcie **Serwery**, na kafelku **Azure Migrate: Server Assessment** wybierz pozycję **Oceń**.

    ![Ocena](./media/tutorial-assess-physical/assess.png)

3. W obszarze **Ocena serwerów** określ nazwę oceny i wybierz typ **oceny** jako *Maszyna wirtualna platformy Azure*, jeśli zamierzasz wykonać oceny maszyn wirtualnych platformy Azure lub *Azure VMware Solution (AVS)* , jeśli zamierzasz wykonać oceny usługi AVS.

    ![Podstawowe informacje dotyczące oceny](./media/how-to-create-assessment/assess-servers-azurevm.png)

4. W obszarze **Źródło odnajdywania** wybierz pozycję **Maszyny dodane za pośrednictwem opcji importowania do usługi Azure Migrate**.

5. Wybierz pozycję **Wyświetl wszystko**, aby sprawdzić właściwości oceny.

    ![Właściwości oceny](./media/tutorial-assess-physical/view-all.png)

6. Kliknij pozycję **Dalej**, aby **wybrać maszyny do oceny**. W obszarze **Wybierz lub utwórz grupę** wybierz pozycję **Utwórz nową** i określ nazwę grupy. Grupa zbiera jedną lub więcej maszyn wirtualnych w celu oceny.
7. W opcji **Dodaj maszyny do grupy** wybierz serwery, które mają zostać dodane do grupy.
8. Kliknij przycisk **Dalej**, aby przejść do pozycji **Przejrzyj i utwórz ocenę**, aby przejrzeć szczegóły dotyczące oceny.
9. Kliknij pozycję **Utwórz ocenę**, aby utworzyć grupę, a następnie uruchomić ocenę.

    ![Tworzenie oceny](./media/tutorial-assess-physical/assessment-create.png)

9. Po utworzeniu oceny możesz ją wyświetlić w pozycji **Serwery** > **Azure Migrate: Server Assessment** > **Oceny**.
10. Wybierz polecenie **Eksportuj ocenę**, aby pobrać ocenę jako plik programu Microsoft Excel.

Aby dowiedzieć się więcej na temat oceny usługi **Azure VMware Solution (AVS)** , zobacz [tutaj](how-to-create-azure-vmware-solution-assessment.md). 

## <a name="review-an-azure-vm-assessment"></a>Przegląd oceny maszyny wirtualnej platformy Azure

Ocena maszyny wirtualnej platformy Azure opisuje:

- **Gotowość na platformę Azure**: Czy serwery są odpowiednie do migracji na platformę Azure.
- **Miesięczne szacowanie kosztów**: Szacowane miesięczne koszty obliczeń i magazynu na potrzeby uruchamiania serwerów na platformie Azure.
- **Miesięczne szacowanie kosztów magazynu**: Szacowane koszty magazynu dyskowego po migracji.

### <a name="view-an-assessment"></a>Widok oceny

1. W pozycji **Cele migracji** > **Serwery** wybierz pozycję **Oceny** w obszarze **Azure Migrate: Server Assessment**.
2. W pozycji **Oceny** wybierz ocenę, aby ją otworzyć.

    ![Podsumowanie oceny](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Przegląd gotowości na platformę Azure

1. W pozycji **Gotowość na platformę Azure** określ, czy serwery są gotowe do migracji na platformę Azure.
2. Sprawdź stan:
    - **Gotowe na platformę Azure**: Usługa Azure Migrate zaleca oszacowanie rozmiaru maszyny wirtualnej i kosztów dla maszyn wirtualnych w ocenie.
    - **Gotowe warunkowo**: Pokazuje problemy i sugerowane rozwiązanie ich.
    - **Niegotowe na platformę Azure**: Pokazuje problemy i sugerowane rozwiązanie ich.
    - **Nieznana gotowość**: Usługa Azure Migrate nie może ocenić gotowości ze względu na problemy z dostępnością danych.

3. Wybierz stan **Gotowość na platformę Azure**. Możesz wyświetlić szczegóły dotyczące gotowości serwera i przejść do szczegółów serwera, aby je zobaczyć, w tym ustawienia obliczeń, magazynu i sieci.

### <a name="review-cost-details"></a>Przegląd szczegółów dotyczących kosztów

Ten widok przedstawia szacowane koszty zasobów obliczeniowych i magazynowych w przypadku korzystania z maszyn wirtualnych na platformie Azure. Można:

- Zapoznać się z miesięcznymi kosztami obliczeń i magazynu. Koszty są agregowane dla wszystkich serwerów w ocenianej grupie.

    - Oszacowania kosztów opierają się na rekomendacjach dotyczących rozmiaru maszyny i jej dysków oraz na właściwościach.
    - Wyświetlane są szacowane miesięczne koszty obliczeń i magazynu.
    - Szacowany koszt dotyczy korzystania z lokalnych serwerów jako maszyn wirtualnych w ramach rozwiązania typu IaaS (infrastruktura jako usługa). Narzędzie Server Assessment nie uwzględnia kosztów rozwiązań typu PaaS (platforma jako usługa) i SaaS (oprogramowanie jako usługa).

- Przejrzyj miesięczne szacowane koszty magazynu. Ten widok przedstawia zagregowane koszty magazynu dla ocenianej grupy, podzielone między różnymi typami dysków magazynu.
- Przejdź do szczegółów poszczególnych maszyn wirtualnych.

> [!NOTE]
> Oceny zaufania nie są przypisywane do ocen serwerów zaimportowanych do narzędzia Server Assessment przy użyciu pliku CSV.

## <a name="supported-operating-system-names"></a>Obsługiwane nazwy systemów operacyjnych

Nazwy systemów operacyjnych podane w pliku CSV muszą być zgodne lub zawierać nazwy z tej listy. Jest to konieczne, aby określone nazwy były rozpoznane w trakcie oceny jako prawidłowe.

<!-- BEGIN A - H -->

:::row:::
   :::column span="2":::
      **A – H**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Apple Mac OS X 10
   :::column-end:::
   :::column span="":::
      Asianux 3<br/>
      Asianux 4<br/>
      Asianux 5
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      CentOS<br/>
      CentOS 4/5
   :::column-end:::
   :::column span="":::
      CoreOS Linux
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Debian GNU/Linux 4<br/>
      Debian GNU/Linux 5<br/>
      Debian GNU/Linux 6<br/>
      Debian GNU/Linux 7<br/>
      Debian GNU/Linux 8
   :::column-end:::
   :::column span="":::
      FreeBSD
   :::column-end:::
:::row-end:::

<!-- BEGIN I - R -->

:::row:::
   :::column span="2":::
      **I – R**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      IBM OS/2
   :::column-end:::
   :::column span="":::
      MS-DOS
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Novell NetWare 5<br/>
      Novell NetWare 6
   :::column-end:::
   :::column span="":::
      Oracle Linux<br/>
       Oracle Linux 4/5<br/>
      Oracle Solaris 10<br/>
       Oracle Solaris 11
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Red Hat Enterprise Linux 2<br/>
      Red Hat Enterprise Linux 3<br/>
      Red Hat Enterprise Linux 4<br/>
      Red Hat Enterprise Linux 5<br/>
      Red Hat Enterprise Linux 6<br/>
      Red Hat Enterprise Linux 7<br/>
      Red Hat Fedora
   :::column-end:::
:::row-end:::

<!-- BEGIN S - T -->

:::row:::
   :::column span="2":::
      **S – T**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      SCO OpenServer 5<br/>
      SCO OpenServer 6<br/>
      SCO UnixWare 7
   :::column-end:::
   :::column span="":::
      Serenity Systems eComStation 1<br/>
      Serenity Systems eComStation 2
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Sun Microsystems Solaris 8<br/>
      Sun Microsystems Solaris 9
   :::column-end:::
   :::column span="":::
      SUSE Linux Enterprise 10<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE Linux Enterprise 12<br/>
      SUSE Linux Enterprise 8/9<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE openSUSE
   :::column-end:::
:::row-end:::

<!-- BEGIN U - Z -->
:::row:::
   :::column span="2":::
      **U – Z**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Ubuntu Linux
   :::column-end:::
   :::column span="":::
      VMware ESXi 4<br/>
      VMware ESXi 5<br/>
      VMware ESXi 6
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Windows 10<br/>
      Windows 2000<br/>
      Windows 3<br/>
      Windows 7<br/>
      Windows 8<br/>
      Windows 95<br/>
      Windows 98<br/>
      Windows NT<br/>
      Windows Server (R) 2008<br/>
      Windows Server 2003
   :::column-end:::
   :::column span="":::
      Windows Server 2008<br/>
      Windows Server 2008 z dodatkiem R2<br/>
      Windows Server 2012<br/>
      Windows Server 2012 z dodatkiem R2<br/>
      Windows Server 2016<br/>
      Windows Server 2019<br/>
      Windows Server Threshold<br/>
      Windows Vista<br/>
      Windows Web Server 2008 R2<br/>
      Windows XP Professional
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Importowanie serwerów do narzędzia Azure Migrate: Server Assessment przy użyciu pliku CSV.
> * Tworzenie i przegląd oceny.

Teraz [wdróż urządzenie](./migrate-appliance.md), aby uzyskać precyzyjne oceny i zebrać serwery w grupy w celu uzyskania dokładniejszej oceny przy użyciu [analizy zależności](./concepts-dependency-visualization.md).
