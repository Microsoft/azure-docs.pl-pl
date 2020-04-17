---
title: Oceniaj serwery przy użyciu importowanych danych serwera za pomocą oceny serwera migracji usługi Azure
description: W tym artykule opisano sposób oceny serwerów lokalnych do migracji na platformę Azure za pomocą usługi Azure Migrate Server Assessment przy użyciu importowanych danych.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 9e6b451901da00243c458c06d51ba19fb64c0fdc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81530457"
---
# <a name="assess-servers-by-using-imported-data"></a>Ocenianie serwerów przy użyciu importowanych danych

W tym artykule wyjaśniono, jak oceniać serwery lokalne za pomocą narzędzia [Azure Migrate: Server Assessment,](migrate-services-overview.md#azure-migrate-server-assessment-tool) importując metadane serwera w formacie csv (comma-separated). Ta metoda oceny nie wymaga konfigurowania urządzenia migracji platformy Azure w celu utworzenia oceny. Jest to przydatne, jeśli:

- Przed wdrożeniem urządzenia należy utworzyć szybką, wstępną ocenę.
- Nie można wdrożyć urządzenia migracji platformy Azure w organizacji.
- Nie można udostępniać poświadczeń, które zezwalają na dostęp do serwerów lokalnych.
- Ograniczenia zabezpieczeń uniemożliwiają zbieranie i wysyłanie danych zebranych przez urządzenie na platformę Azure. Można kontrolować dane udostępniane w importowanym pliku. Ponadto większość danych (na przykład dostarczanie adresów IP) jest opcjonalna.

## <a name="before-you-start"></a>Przed rozpoczęciem

Należy pamiętać o następujących punktach:

- W jednym pliku CSV można dodać maksymalnie 20 000 serwerów.
- Za pomocą pliku CSV można dodać maksymalnie 20 000 serwerów w projekcie migracji platformy Azure.
- Informacje o serwerze można przesyłać do oceny serwera wiele razy za pomocą pliku CSV.
- Zbieranie informacji o aplikacji jest przydatne w ocenie środowiska lokalnego do migracji. Jednak ocena serwera nie wykonuje obecnie oceny na poziomie aplikacji ani nie bierze pod uwagę aplikacji podczas tworzenia oceny.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Konfigurowanie projektu migracji platformy Azure.
> * Wypełnij plik CSV informacjami o serwerze.
> * Zaimportuj plik, aby dodać informacje o serwerze do oceny serwera.
> * Tworzenie i przeglądanie oceny.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrażania dla scenariusza, dzięki czemu można szybko skonfigurować dowód koncepcji. Samouczki używają opcji domyślnych tam, gdzie to możliwe, i nie pokazują wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z poradnikami.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) przed rozpoczęciem.

## <a name="set-azure-permissions-for-azure-migrate"></a>Ustawianie uprawnień platformy Azure dla migracji platformy Azure

Twoje konto platformy Azure potrzebuje uprawnień do tworzenia projektu migracji platformy Azure.

1. W witrynie Azure portal otwórz subskrypcję i wybierz **pozycję Kontrola dostępu (IAM)**.
2. W **obszarze Sprawdź dostęp**znajdź odpowiednie konto, a następnie wybierz je, aby wyświetlić uprawnienia.
3. Upewnij się, że masz uprawnienia **współautora** lub **właściciela.**
    - Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, współpracuj z właścicielem, aby przypisać tę rolę.

## <a name="set-up-an-azure-migrate-project"></a>Konfigurowanie projektu migracji platformy Azure

Aby skonfigurować nowy projekt migracji platformy Azure:

1. W witrynie Azure portal w obszarze **Wszystkie usługi**wyszukaj usługę **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.
3. W **obszarze Przegląd**w obszarze **Odnajdowanie i migrowanie serwerów**wybierz pozycję **Oceń i migruj serwery**.

    ![Odkrywanie i ocenianie serwerów](./media/tutorial-assess-import/assess-migrate.png)

4. W **obszarze Wprowadzenie**wybierz pozycję Dodaj narzędzia **.**
5. W obszarze **Projekt migracji**wybierz subskrypcję platformy Azure i utwórz grupę zasobów, jeśli jej nie masz.
6. W **programie PROJECT DETAILS**określ nazwę projektu i lokalizację geograficzną, w której chcesz utworzyć projekt. Więcej informacji:

    - Przejrzyj obsługiwane obszary geograficzne dla chmur [publicznych](migrate-support-matrix.md#supported-geographies-public-cloud) i [rządowych](migrate-support-matrix.md#supported-geographies-azure-government).
    - Podczas przeprowadzania migracji można wybrać dowolny region docelowy.

    ![Tworzenie projektu migracji platformy Azure](./media/tutorial-assess-import/migrate-project.png)

7. Wybierz opcję **Dalej**.
8. W **narzędziu do oceny Wybierz**wybierz pozycję Azure **Migrate: Server Assessment** > **Next**.

    ![Tworzenie oceny migracji platformy Azure](./media/tutorial-assess-import/assessment-tool.png)

9. W obszarze **Wybierz narzędzie migracji** wybierz pozycję **Pomiń na razie dodawanie narzędzia migracji** > **Dalej**.
10. W **obszarze Recenzja + dodawanie narzędzi**przejrzyj ustawienia, a następnie wybierz pozycję Dodaj **narzędzia**.
11. Zaczekaj kilka minut, aż projekt usługi Azure Migrate zostanie wdrożony. Następnie zostaniesz przesuń do strony projektu. Jeśli nie widzisz projektu, możesz uzyskać do niego dostęp z obszaru **Serwery** na pulpicie nawigacyjnym usługi Azure Migrate.

## <a name="prepare-the-csv"></a>Przygotowanie pliku CSV

Pobierz szablon CSV i dodaj do niego informacje o serwerze.

### <a name="download-the-template"></a>Pobieranie szablonu

1. W **obszarze** > Cele migracji**Serwery** > **platformy Azure migrate: Ocena serwera**wybierz pozycję **Odkryj**.
2. W **obszarze Discover maszyny**wybierz pozycję **Importuj przy użyciu pliku CSV**.
3. Wybierz **pobierz,** aby pobrać szablon CSV. Alternatywnie, można [go pobrać bezpośrednio](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Pobierz szablon CSV](./media/tutorial-assess-import/download-template.png)

### <a name="add-server-information"></a>Dodawanie informacji o serwerze

Zbierz dane serwera i dodaj je do pliku CSV.

- Aby zebrać dane, można je wyeksportować z narzędzi używanych do zarządzania serwerem lokalnym, takich jak VMware vSphere lub bazy danych zarządzania konfiguracją (CMDB).
- Aby przejrzeć przykładowe dane, pobierz [nasz przykładowy plik](https://go.microsoft.com/fwlink/?linkid=2108405).

W poniższej tabeli podsumowano pola plików do wypełnienia:

**Nazwa pola** | **Obowiązkowy** | **Szczegóły**
--- | --- | ---
**Nazwa serwera** | Tak | Zalecamy określenie w pełni kwalifikowanej nazwy domeny (FQDN).
**Adres IP** | Nie | Adres serwera.
**Rdzenie** | Tak | Liczba rdzeni procesora przydzielonych do serwera.
**Pamięci** | Tak | Całkowita pamięć RAM w MB przydzielona do serwera.
**Nazwa systemu operacyjnego** | Tak | System operacyjny serwera. <br/> Nazwy systemów operacyjnych, które pasują lub zawierają nazwy na [tej](#supported-operating-system-names) liście są rozpoznawane przez ocenę.
**Wersja systemu operacyjnego** | Nie | Wersja systemu operacyjnego serwera.
**Liczba dysków** | Nie | Nie jest potrzebne, jeśli podano szczegółowe informacje o poszczególnych dyskach.
**Rozmiar dysku 1**  | Nie | Maksymalny rozmiar dysku w GB.<br/>Szczegóły można dodać, aby uzyskać więcej dysków, [dodając kolumny](#add-multiple-disks) w szablonie. Można dodać maksymalnie osiem dysków.
**Operacje odczytu dysku 1** | Nie | Operacje odczytu dysku na sekundę.
**Operacje zapisu dysku 1** | Nie | Operacje zapisu dysku na sekundę.
**Przepustowość odczytu dysku 1** | Nie | Dane odczytywane z dysku na sekundę w MB na sekundę.
**Przepływność zapisu dysku 1** | Nie | Dane zapisywane na dysku na sekundę, w MB na sekundę.
**Procent wykorzystania procesora** | Nie | Procent używanego procesora CPU.
**Procent wykorzystania pamięci** | Nie | Procent użytej pamięci RAM.
**Całkowita liczba dysków odczytanych operacji** | Nie | Operacje odczytu dysku na sekundę.
**Całkowita liczba operacji zapisu dysków** | Nie | Operacje zapisu dysku na sekundę.
**Całkowita przepustowość odczytu dysków** | Nie | Dane odczytywane z dysku w MB na sekundę.
**Całkowita przepustowość zapisu dysków** | Nie | Dane zapisywane na dysku w MB na sekundę.
**Przepustowość sieci** | Nie | Dane odebrane przez serwer w MB na sekundę.
**Przepustowość wyjścia sieci** | Nie | Dane przesyłane przez serwer w MB na sekundę.
**Typ oprogramowania układowego** | Nie | Oprogramowanie układowe serwera. Wartości mogą być "BIOS" lub "UEFI".
**Adres MAC**| Nie | Adres MAC serwera.


### <a name="add-operating-systems"></a>Dodawanie systemów operacyjnych

Ocena rozpoznaje określone nazwy systemów operacyjnych. Każda określona nazwa musi dokładnie odpowiadać jednemu z ciągów na [liście obsługiwanych nazw](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Dodawanie wielu dysków

Szablon zawiera pola domyślne dla pierwszego dysku. Można dodać podobne kolumny dla maksymalnie ośmiu dysków.

Na przykład, aby określić wszystkie pola dla drugiego dysku, dodaj następujące kolumny:

- Rozmiar dysku 2
- Operacje odczytu dysku 2
- Operacje zapisu dysku 2
- Przepustowość odczytu dysku 2
- Przepływność zapisu dysku 2


## <a name="import-the-server-information"></a>Importowanie informacji o serwerze

Po dodaniu informacji do szablonu CSV zaimportuj serwery do oceny serwera.

1. W usłudze Azure Migrate w **obszarze Odnajdowanie maszyn**przejdź do ukończonego szablonu.
2. Wybierz pozycję **Import**.
3. Zostanie wyświetlony stan importu.
    - Jeśli ostrzeżenia są wyświetlane w stanie, można je naprawić lub kontynuować bez ich adresowania.
    - Aby poprawić dokładność oceny, popraw informacje o serwerze zgodnie z sugestią w ostrzeżeniach.
    - Aby wyświetlić i naprawić ostrzeżenia, wybierz **pozycję Pobierz szczegóły ostrzeżenia . CSV**. Ta operacja pobiera plik CSV z ostrzeżeniami. Przejrzyj ostrzeżenia i w razie potrzeby rozwiąż problemy.
    - Jeśli w stanie pojawią się błędy, aby stan importu **był nieudany,** należy je naprawić, aby można było kontynuować importowanie:
        1. Pobierz plik CSV, który teraz zawiera szczegóły błędu.
        1. W razie potrzeby przejrzyj i rozwiąj problem z błędami. 
        1. Ponownie przekaż zmodyfikowany plik.
4. Po **zakończeniu**stanu importu zaimportowano informacje o serwerze.

## <a name="update-server-information"></a>Aktualizowanie informacji o serwerze

Informacje dotyczące serwera można zaktualizować, ponownie importując dane dla serwera o tej samej **nazwie serwera.** Nie można zmodyfikować pola **Nazwa serwera.** Usuwanie serwerów nie jest obecnie obsługiwane.

## <a name="verify-servers-in-the-portal"></a>Weryfikowanie serwerów w portalu

Aby sprawdzić, czy serwery są wyświetlane w witrynie Azure portal po odnajdowaniu:

1. Otwórz pulpit nawigacyjny migracji platformy Azure.
2. Na stronie **Azure Migrate — Servers** > **Azure Migrate: Server Assessment** wybierz ikonę, która wyświetla liczbę **odnalezionych serwerów**.
3. Wybierz kartę **Importowanie na podstawie.**

## <a name="set-up-and-run-an-assessment"></a>Konfigurowanie i uruchamianie oceny

Za pomocą oceny serwera można utworzyć dwa typy ocen.

**Typ oceny** | **Szczegóły** | **Dane**
--- | --- | ---
**Oparte na wydajności** | Oceny na podstawie określonych wartości danych wydajności. | **Zalecany rozmiar maszyny Wirtualnej:** Na podstawie danych użycia procesora i pamięci.<br/><br/> **Zalecany typ dysku (dysk zarządzany standardowy lub premium):** na podstawie wejścia/wyjścia na sekundę (IOPS) i przepływności dysków lokalnych.
**Jako lokalnie** | Oceny oparte na rozmiarze lokalnym. | **Zalecany rozmiar maszyny Wirtualnej:** Na podstawie określonego rozmiaru serwera.<br/><br> **Zalecany typ dysku:** Na podstawie ustawienia typu magazynu wybranego dla oceny.

Aby przeprowadzić ocenę:

1. Zapoznaj się z [najlepszymi rozwiązaniami w](best-practices-assessment.md) zakresie tworzenia ocen.
2. Na karcie **Serwery** na kafelku **Azure Migrate: Server Assessment** wybierz pozycję **Oceń**.

    ![Ocena](./media/tutorial-assess-physical/assess.png)

3. W **obszarze Oceń serwery**określ nazwę oceny.
4. W **źródle odnajdowania**wybierz **pozycję Maszyny dodane przez importowanie do usługi Azure Migrate**.
5. Wybierz **pozycję Wyświetl wszystkie,** aby przejrzeć właściwości oceny.

    ![Właściwości oceny](./media/tutorial-assess-physical/view-all.png)

6. W **obszarze Zaznacz lub utwórz grupę**wybierz pozycję **Utwórz nowy**i określ nazwę grupy. Grupa gromadzi jedną lub więcej maszyn wirtualnych razem do oceny.
7. W **obszarze Dodaj maszyny do grupy**wybierz serwery, które chcesz dodać do grupy.
8. Wybierz **pozycję Utwórz ocenę,** aby utworzyć grupę, a następnie uruchom ocenę.

    ![Tworzenie oceny](./media/tutorial-assess-physical/assessment-create.png)

9. Po utworzeniu oceny wyświetl ją w **obszarze Serwery** > **Migracji platformy Azure:** > **Oceny**oceny serwera .
10. Wybierz **opcję Eksportuj ocenę,** aby pobrać ją jako plik programu Microsoft Excel.

## <a name="review-an-assessment"></a>Przegląd oceny

W ocenie opisano:

- **Gotowość platformy Azure:** Czy serwery nadają się do migracji na platformę Azure.
- **Szacowane miesięczne koszty:** Szacowane miesięczne koszty obliczeń i magazynowania związane z uruchamianiem serwerów na platformie Azure.
- **Miesięczne szacowanie kosztów magazynowania:** Szacowane koszty magazynu dysków po migracji.

### <a name="view-an-assessment"></a>Wyświetlanie oceny

1. W **obszarze** > Cele migracji**Serwery**wybierz pozycję **Oceny** w obszarze **Migracja platformy Azure: Ocena serwera**.
2. W **obszarze Oceny**wybierz ocenę, aby ją otworzyć.

    ![Podsumowanie oceny](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Przejrzyj gotowość platformy Azure

1. W **gotowości platformy Azure**określ, czy serwery są gotowe do migracji na platformę Azure.
2. Przejrzyj stan:
    - **Gotowe do platformy Azure:** Usługa Azure Migrate zaleca rozmiar maszyny Wirtualnej i szacowane koszty dla maszyn wirtualnych w ocenie.
    - **Gotowy z warunkami**: Pokazuje problemy i sugerowane środki zaradcze.
    - **Nie jest gotowy na platformę Azure:** pokazuje problemy i sugerowane rozwiązania.
    - **Gotowość nieznana:** Migracja platformy Azure nie może ocenić gotowości z powodu problemów z dostępnością danych.

3. Wybierz stan **gotowości platformy Azure.** Można wyświetlić szczegóły gotowości serwera i przejść do szczegółów, aby wyświetlić szczegóły serwera, w tym ustawienia obliczeniowe, magazynowe i sieciowe.

### <a name="review-cost-details"></a>Przejrzyj szczegóły kosztów

Ten widok zawiera szacowany koszt obliczeń i magazynu uruchamiania maszyn wirtualnych na platformie Azure. Możesz:

- Przejrzyj miesięczne koszty obliczeń i magazynu. Koszty są agregowane dla wszystkich serwerów w ocenianej grupie.

    - Szacunki kosztów są oparte na zaleceniach dotyczących rozmiaru komputera oraz jego dyskach i właściwościach.
    - Wyświetlane są szacowane miesięczne koszty obliczeń i magazynu.
    - Kosztorys jest do uruchamiania serwerów lokalnych jako infrastruktury jako usługi (IaaS) maszyny wirtualne. Server Assessment nie bierze pod uwagę kosztów platformy jako usługi (PaaS) ani kosztów oprogramowania jako usługi (SaaS).

- Przejrzyj miesięczne szacunki kosztów magazynowania. W tym widoku są wyświetlane zagregowane koszty magazynowania dla ocenianej grupy, podzielone między różne typy dysków magazynu.
- Przejdź do szczegółów, aby wyświetlić szczegółowe informacje dotyczące określonych maszyn wirtualnych.

> [!NOTE]
> Oceny ufności nie są przypisywane do ocen serwerów importowanych do oceny serwera przy użyciu pliku CSV.

## <a name="supported-operating-system-names"></a>Obsługiwane nazwy systemów operacyjnych

<!-- BEGIN A - H -->

:::row:::
   :::column span="2":::
      **Od - H**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Apple Mac OS X 10
   :::column-end:::
   :::column span="":::
      Asianux 3 (właśc.<br/>
      Asianux 4 (właśc.<br/>
      Asianux 5 (właśc.
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
      **I - R**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      System operacyjny IBM/2
   :::column-end:::
   :::column span="":::
      Ms-dos
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Nowal NetWare 5<br/>
      Nowal NetWare 6
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
      Czerwona Czapka Fedory
   :::column-end:::
:::row-end:::

<!-- BEGIN S - T -->

:::row:::
   :::column span="2":::
      **S - T**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Sco OpenServer 5<br/>
      Serwer otwarty SCO 6<br/>
      SCO UnixWare 7
   :::column-end:::
   :::column span="":::
      Serenity Systems eComStation 1<br/>
      Serenity Systems eComStation 2
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Mikrosystemy słoneczne Solaris 8<br/>
      Mikrosystemy słoneczne Solaris 9
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
      **U - Z**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Ubuntu Linux
   :::column-end:::
   :::column span="":::
      Oprogramowanie VMware ESXi 4<br/>
      VMware ESXi 5<br/>
      Oprogramowanie VMware ESXi 6
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Windows 10<br/>
      Windows 2000<br/>
      System Windows 3<br/>
      Windows 7<br/>
      Windows 8<br/>
      System Windows 95<br/>
      Windows 98<br/>
      Windows NT<br/>
      Windows Server (R) 2008<br/>
      Windows Server 2003
   :::column-end:::
   :::column span="":::
      Windows Server 2008<br/>
      Windows Server 2008 R2<br/>
      Windows Server 2012<br/>
      Windows Server 2012 R2<br/>
      Windows Server 2016<br/>
      Windows Server 2019<br/>
      Próg systemu Windows Server<br/>
      Windows Vista<br/>
      Windows Web Server 2008 R2<br/>
      Windows XP Professional
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Zaimportowane serwery do usługi Azure Migrate: Server Assessment przy użyciu pliku CSV.
> * Utworzono i przejrzano ocenę.

Teraz [należy wdrożyć urządzenie](./migrate-appliance.md) w celu dokładniejszych ocen i zebrać serwery razem w grupach w celu głębszej oceny przy użyciu [analizy zależności.](./concepts-dependency-visualization.md)
