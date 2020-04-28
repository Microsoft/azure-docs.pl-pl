---
title: Planowanie migracji z klasycznego do usługi Azure Resource Manager
description: Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 10ae2e1a85d5250e4da836c6f57e3619befd9330
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865928"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager

> [!IMPORTANT]
> Obecnie około 90% maszyn wirtualnych IaaS korzysta z [usługi Azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) Od 28 lutego 2020 r. klasyczne maszyny wirtualne zostały przestarzałe i zostaną całkowicie wycofane 1 marca 2023 r. [Dowiedz się więcej]( https://aka.ms/classicvmretirement) o tym uszczukaniu i [o tym, jak wpływa na Ciebie](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

Usługa Azure Resource Manager oferuje wiele niesamowitych funkcji, ale bardzo ważne jest zaplanowanie podróży migracji, aby upewnić się, że wszystko przebiega sprawnie. Spędzanie czasu na planowaniu zapewni, że nie wystąpią problemy podczas wykonywania działań migracji.

Istnieją cztery ogólne fazy podróży migracyjnej:<br>

![Fazy migracji](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Planowanie

### <a name="technical-considerations-and-tradeoffs"></a>Względy techniczne i kompromisy

W zależności od rozmiaru wymagań technicznych, lokalizacji geograficznych i praktyk operacyjnych warto wziąć pod uwagę:

1. Dlaczego usługa Azure Resource Manager jest pożądana dla Twojej organizacji?  Jakie są biznesowe powody migracji?
2. Jakie są techniczne powody usługi Azure Resource Manager?  Co (jeśli istnieją) dodatkowe usługi platformy Azure, które chcesz wykorzystać?
3. Która aplikacja (lub zestawy maszyn wirtualnych) jest uwzględniona w migracji?
4. Które scenariusze są obsługiwane w interfejsie API migracji?  Przejrzyj [nieobsługiwały funkcje i konfiguracje](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations).
5. Czy twoje zespoły operacyjne będą teraz obsługiwać aplikacje/maszyny wirtualne zarówno w usłudze Classic, jak i w usłudze Azure Resource Manager?
6. Jak (jeśli w ogóle) usługa Azure Resource Manager zmienia procesy wdrażania, zarządzania, monitorowania i raportowania maszyn wirtualnych?  Czy skrypty wdrażania muszą zostać zaktualizowane?
7. Jaki jest plan komunikacji, aby ostrzegać zainteresowane strony (użytkowników końcowych, właścicieli aplikacji i właścicieli infrastruktury)?
8. W zależności od złożoności środowiska, czy powinien istnieć okres konserwacji, w którym aplikacja jest niedostępna dla użytkowników końcowych i właścicieli aplikacji?  Jeśli tak, na jak długo?
9. Jaki jest plan szkoleń, aby upewnić się, że interesariusze są kompetentni i sprawni w usłudze Azure Resource Manager?
10. Co to jest plan zarządzania programem lub zarządzania projektami dla migracji?
11. Jakie są terminy migracji usługi Azure Resource Manager i innych powiązanych map drogowych technologii?  Czy są one optymalnie wyrównane?

### <a name="patterns-of-success"></a>Wzorce sukcesu

Odnoszący sukcesy klienci mają szczegółowe plany, w których powyższe pytania są omawiane, dokumentowane i regulowane.  Zapewnienie, że plany migracji są szeroko przekazywane sponsorom i zainteresowanym stronom.  Wyposaż się w wiedzę na temat opcji migracji; odczyt tego dokumentu migracji, który jest ustawiony poniżej, jest wysoce zalecany.

* [Omówienie migracji zasobów usługi IaaS obsługiwanej przez platformę z klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrowanie zasobów usługi IaaS z klasycznego do usługi Azure Resource Manager za pomocą programu PowerShell](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrowanie zasobów usługi IaaS z klasycznego do usługi Azure Resource Manager za pomocą interfejsu wiersza polecenia](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Narzędzia społecznościowe pomagające w migracji zasobów IaaS z klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przejrzyj najczęściej zadawane pytania dotyczące migracji zasobów usługi IaaS z klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Pułapki, których należy unikać

- Nieplanowanie.  Udowodniono kroki technologiczne tej migracji, a wynik jest przewidywalny.
- Założenie, że interfejs API migracji obsługiwanej przez platformę będzie uwzględniać wszystkie scenariusze. Przeczytaj [nieobsługiwane funkcje i konfiguracje,](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) aby dowiedzieć się, jakie scenariusze są obsługiwane.
- Nie planowanie potencjalnej awarii aplikacji dla użytkowników końcowych.  Zaplanuj wystarczającą ilość buforu, aby odpowiednio ostrzec użytkowników końcowych o potencjalnie niedostępnym czasie aplikacji.


## <a name="lab-test"></a>Test laboratoryjny

**Replikowanie środowiska i migracja testowa**
  > [!NOTE]
  > Dokładna replikacja istniejącego środowiska jest wykonywana przy użyciu narzędzia współtworzonego przez społeczność, które nie jest oficjalnie obsługiwane przez pomoc techniczną firmy Microsoft. W związku z tym jest to **krok opcjonalny,** ale jest to najlepszy sposób, aby dowiedzieć się problemy bez dotykania środowisk produkcyjnych. Jeśli użycie narzędzia wniesionego przez społeczność nie jest opcją, przeczytaj o rekomendacji Validate/Prepare/Abort Dry Run poniżej.
  >

  Przeprowadzenie testu laboratoryjnego dokładnego scenariusza (obliczeń, sieci i magazynu) jest najlepszym sposobem zapewnienia płynnej migracji. Pomoże to zapewnić:

- Całkowicie oddzielne laboratorium lub istniejące środowisko nieprodukcyjne do testowania. Zaleca się całkowicie oddzielne laboratorium, które można migrować wielokrotnie i może być destrukcyjnie modyfikowane.  Skrypty do zbierania/nawilżania metadanych z rzeczywistych subskrypcji są wymienione poniżej.
- Dobrym pomysłem jest utworzenie laboratorium w osobnej subskrypcji. Powodem jest to, że laboratorium będzie wielokrotnie niszczona, a posiadanie oddzielnej, izolowanej subskrypcji zmniejszy prawdopodobieństwo, że coś prawdziwego zostanie przypadkowo usunięte.

  Można to osiągnąć za pomocą narzędzia AsmMetadataParser. [Przeczytaj więcej o tym narzędziu tutaj](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Wzorce sukcesu

Poniżej przedstawiono problemy wykryte w wielu większych migracji. Nie jest to wyczerpująca lista i należy odwołać się do [nieobsługiconych funkcji i konfiguracji,](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) aby uzyskać więcej szczegółów.  Możesz lub nie może wystąpić te problemy techniczne, ale jeśli nie rozwiązania tych przed podjęciem próby migracji zapewni płynniejsze środowisko.

- **Wykonaj sprawdzanie poprawności/przygotowanie/przerwanie przebiegu suchego** — jest to prawdopodobnie najważniejszy krok, aby zapewnić sukces migracji klasycznej do usługi Azure Resource Manager. Interfejs API migracji ma trzy główne kroki: Sprawdzanie poprawności, Przygotowanie i zatwierdzanie. Sprawdź poprawność odczyta stan środowiska klasycznego i zwróci wynik wszystkich problemów. Jednak ponieważ niektóre problemy mogą istnieć w stosie usługi Azure Resource Manager, Sprawdź poprawność nie będzie przechwytywać wszystko. Następnym krokiem w procesie migracji, Przygotowanie pomoże ujawnić te problemy. Przygotowanie spowoduje przeniesienie metadanych z klasycznego do usługi Azure Resource Manager, ale nie zatwierdzi przeniesienia i nie usunie ani nie zmieni niczego po stronie klasycznej. Przebieg suchy polega na przygotowaniu migracji, a następnie przerwaniu **(niezatwierdzania)** przygotowania migracji. Celem sprawdzania poprawności/przygotowania/przerwania przebiegu na sucho jest wyświetlenie wszystkich metadanych w stosie usługi Azure Resource Manager, zbadanie go *(programowo lub w portalu)* i sprawdzenie, czy wszystko jest poprawnie migrowane i działa przez problemy techniczne.  Da to również poczucie czasu trwania migracji, dzięki czemu można odpowiednio zaplanować przestoje.  Sprawdzanie poprawności/przygotowanie/przerwanie nie powoduje żadnych przestojów użytkownika; w związku z tym nie jest uciążliwy dla użycia aplikacji.
  - Poniższe elementy będą musiały zostać rozwiązane przed suchym biegiem, ale test na sucho również bezpiecznie wypłuka te kroki przygotowania, jeśli zostaną pominięte. Podczas migracji w przedsiębiorstwie odkryliśmy, że suchy przebieg jest bezpiecznym i nieocenionym sposobem zapewnienia gotowości do migracji.
  - Po uruchomieniu przygotowania płaszczyzna sterowania (operacje zarządzania platformy Azure) zostanie zablokowana dla całej sieci wirtualnej, więc nie można ww przypadku żadnych zmian w metadanych maszyny Wirtualnej podczas sprawdzania poprawności/przygotowania/przerwania.  Ale w przeciwnym razie wszelkie funkcje aplikacji (RD, użycie maszyny Wirtualnej, itp.) będzie bez zmian.  Użytkownicy maszyn wirtualnych nie będą wiedzieć, że praca na sucho jest wykonywana.

- **Obwody trasy ekspresowej i SIEĆ VPN**. Obecnie nie można migrować bram trasy ekspresowej z łączami autoryzacji bez przestojów. Aby uzyskać obejście, zobacz [Migrowanie obwodów usługi ExpressRoute i skojarzonych sieci wirtualnych z klasycznego do modelu wdrażania Menedżera zasobów](../../expressroute/expressroute-migration-classic-resource-manager.md).

- **Rozszerzenia maszyn wirtualnych** — rozszerzenia maszyny wirtualnej są potencjalnie jedną z największych przeszkód do migracji uruchomionych maszyn wirtualnych. Korygowanie rozszerzeń maszyn wirtualnych może potrwać ponad 1-2 dni, więc odpowiednio zaplanuj.  Działający agent platformy Azure jest potrzebny do raportowania stanu rozszerzenia maszyny Wirtualnej uruchomionych maszyn wirtualnych. Jeśli stan powróci jako zły dla uruchomionej maszyny Wirtualnej, spowoduje to zatrzymanie migracji. Sam agent nie musi być w stanie roboczym, aby włączyć migrację, ale jeśli rozszerzenia istnieją na maszynie Wirtualnej, to zarówno agent roboczy, jak i wychodząca łączność internetowa (z systemem DNS) będą potrzebne do migracji, aby przejść do przodu.
  - Jeśli łączność z serwerem DNS zostanie utracona podczas migracji, wszystkie rozszerzenia maszyn wirtualnych z wyjątkiem bginfo w wersji 1. \* należy najpierw usunąć z każdej maszyny Wirtualnej przed przygotowaniem migracji, a następnie ponownie dodać go z powrotem do maszyny Wirtualnej po migracji usługi Azure Resource Manager.  **Jest to tylko dla maszyn wirtualnych, które są uruchomione.**  Jeśli maszyny wirtualne są zatrzymane cofnięto alokację, rozszerzenia maszyn wirtualnych nie muszą być usuwane.

  > [!NOTE]
  > Wiele rozszerzeń, takich jak diagnostyka platformy Azure i monitorowanie centrum zabezpieczeń, zainstaluje się ponownie po migracji, więc ich usunięcie nie stanowi problemu.

  - Ponadto upewnij się, że sieciowe grupy zabezpieczeń nie ograniczają wychodzącego dostępu do Internetu. Może się to zdarzyć w przypadku niektórych konfiguracji sieciowych grup zabezpieczeń. Wychodzący dostęp do Internetu (i DNS) jest potrzebny do migracji rozszerzeń maszyn wirtualnych do usługi Azure Resource Manager.
  - Istnieją dwie wersje rozszerzenia BGInfo i są nazywane wersjami 1 i 2.  

      - Jeśli maszyna wirtualna używa rozszerzenia BGInfo w wersji 1, możesz pozostawić to rozszerzenie w stanie takim, w jakim jest. Interfejs API migracji pomija to rozszerzenie. Rozszerzenie BGInfo można dodać po migracji.
      - Jeśli maszyna wirtualna używa rozszerzenia BGInfo opartego na UJ, maszyna wirtualna została utworzona przy użyciu portalu Azure. Interfejs API migracji zawiera to rozszerzenie w migracji do usługi Azure Resource Manager, pod warunkiem, że agent działa i ma wychodzący dostęp do Internetu (i DNS).

  - **Wariant korygowania 1**. Jeśli wiesz, że maszyny wirtualne nie będą miały wychodzącego dostępu do Internetu, działającej usługi DNS i pracujących agentów platformy Azure na maszynach wirtualnych, a następnie odinstaluj wszystkie rozszerzenia maszyn wirtualnych w ramach migracji przed przygotowaniem, a następnie zainstaluj ponownie rozszerzenia maszyn wirtualnych po migracji.
  - **Opcja korygucyjna 2**. Jeśli rozszerzenia maszyn wirtualnych są zbyt duże przeszkody, inną opcją jest zamknięcie/deallokować wszystkie maszyny wirtualne przed migracją. Migruj maszyny wirtualne z przydziałem, a następnie uruchom je ponownie po stronie usługi Azure Resource Manager. Zaletą w tym miejscu jest, że rozszerzenia maszyn wirtualnych będzie migrować. Wadą jest to, że wszystkie publiczne stoi wirtualne adresy IP zostaną utracone (może to być non-starter) i oczywiście maszyny wirtualne zostaną zamknięte powodując znacznie większy wpływ na aplikacje robocze.

    > [!NOTE]
    > Jeśli zasady usługi Azure Security Center jest skonfigurowany względem uruchomionej maszyny wirtualnej są migrowane, zasady zabezpieczeń należy zatrzymać przed usunięciem rozszerzeń, w przeciwnym razie rozszerzenie monitorowania zabezpieczeń zostanie ponownie zainstalowany na maszynie Wirtualnej po jej usunięciu.

- **Zestawy dostępności** — aby sieć wirtualna (vNet) została zmigrowana do usługi Azure Resource Manager, wdrożenie klasyczne (tj. usługa w chmurze) zawiera maszyny wirtualne muszą być w jednym zestawie dostępności lub maszyny wirtualne nie muszą być w żadnym zestawie dostępności. Posiadanie więcej niż jednego zestawu dostępności w usłudze w chmurze nie jest zgodne z usługą Azure Resource Manager i spowoduje wstrzymanie migracji.  Ponadto nie może być niektóre maszyny wirtualne w zestawie dostępności, a niektóre maszyny wirtualne nie w zestawie dostępności. Aby rozwiązać ten problem, należy skorygować lub przetasować usługę w chmurze.  Odpowiednio zaplanować, ponieważ może to być czasochłonne.

- **Wdrożenia ról sieci Web i procesu roboczego** — usługi w chmurze zawierające role sieci web i proces roboczy nie mogą migrować do usługi Azure Resource Manager. Aby przeprowadzić migrację zawartości ról sieci web i procesów procesowych, należy przeprowadzić migrację samego kodu do nowszych usług aplikacji PaaS (ta dyskusja wykracza poza zakres tego dokumentu). Jeśli chcesz opuścić role sieci web/procesu roboczego, ale przeprowadzić migrację klasycznych maszyn wirtualnych do modelu wdrażania Menedżera zasobów, role sieci web/procesu roboczego muszą najpierw zostać usunięte z sieci wirtualnej przed rozpoczęciem migracji.  Typowym rozwiązaniem jest po prostu przenieść wystąpienia roli sieci web/procesu roboczego do oddzielnej klasycznej sieci wirtualnej, która jest również połączona z obwodem usługi ExpressRoute. W przypadku pierwszego ponownego wdrażania utwórz nową klasyczną sieć wirtualną, przenieś/ponownie wdrożyć role sieci web/procesu roboczego do tej nowej sieci wirtualnej, a następnie usuń wdrożenia z przenoszonej sieci wirtualnej. Nie są wymagane żadne zmiany kodu. Nowa funkcja [komunikacji równorzędnej sieci wirtualnej](../../virtual-network/virtual-network-peering-overview.md) może służyć do równoważenia ze sobą klasycznej sieci wirtualnej zawierającej role sieci web/procesu roboczego i innych sieci wirtualnych w tym samym regionie platformy Azure, takich jak migrowana sieć**wirtualna (po zakończeniu migracji sieci wirtualnej, ponieważ nie można migrować sieci wirtualnych równorzędnych),** zapewniając tym samym możliwościom bez utraty wydajności i bez opóźnień/przepustowości. Biorąc pod uwagę dodanie [komunikacji równorzędnej sieci wirtualnej,](../../virtual-network/virtual-network-peering-overview.md)wdrożenia ról sieci web/procesu roboczego można teraz łatwo złagodzić i nie blokować migracji do usługi Azure Resource Manager.

- **Przydziały usługi Azure Resource Manager** — regiony platformy Azure mają oddzielne przydziały/limity zarówno dla klasycznego, jak i usługi Azure Resource Manager. Mimo że w scenariuszu migracji nowy sprzęt nie jest zużywany *(zamieniamy istniejące maszyny wirtualne z klasycznego na usługę Azure Resource Manager),* przydziały usługi Azure Resource Manager nadal muszą być w miejscu z wystarczającą pojemnością przed rozpoczęciem migracji. Poniżej wymieniono główne ograniczenia, które widzieliśmy, powodując problemy.  Otwórz bilet pomocy technicznej przydziału, aby podnieść limity.

    > [!NOTE]
    > Te limity muszą zostać podniesione w tym samym regionie, w jakim bieżące środowisko ma zostać zmigrowane.
    >

  - Interfejsy sieciowe
  - Moduły równoważenia obciążenia
  - Publiczne adresy IP
  - Statyczne publiczne wiadomości IP
  - Rdzenie
  - Grupy zabezpieczeń sieci
  - Tabele tras

    Możesz sprawdzić bieżące przydziały usługi Azure Resource Manager przy użyciu następujących poleceń z najnowszą wersją programu Azure PowerShell.



    **Oblicza** *(rdzenie, zestawy dostępności)*

    ```powershell
    Get-AzVMUsage -Location <azure-region>
    ```

    **Sieć** *(sieci wirtualne, statyczne adresy IP, publiczne adresy IP, grupy zabezpieczeń sieci, interfejsy sieciowe, moduły równoważenia obciążenia, tabele tras)*

    ```powershell
    Get-AzUsage /subscriptions/<subscription-id>/providers/Microsoft.Network/locations/<azure-region> -ApiVersion 2016-03-30 | Format-Table
    ```

    **Magazyn** *(konto magazynu)*

    ```powershell
    Get-AzStorageUsage
    ```

- **Limity ograniczania przepustowości interfejsu API usługi Azure Resource Manager** — jeśli masz wystarczająco duże środowisko (np. > 400 maszyn wirtualnych w sieci wirtualnej), może trafić domyślne limity ograniczania przepustowości interfejsu API dla zapisów (obecnie) `1200 writes/hour`w usłudze Azure Resource Manager. Przed rozpoczęciem migracji należy podnieść bilet pomocy technicznej, aby zwiększyć ten limit dla subskrypcji.


- **Aprowizowanie stanu maszyny Wirtualnej po przesuniętych po przesuwizowaniu** — jeśli którakolwiek maszyna wirtualna `provisioning timed out`ma stan , należy rozwiązać problem przed migracją. Jedynym sposobem, aby to zrobić, jest przestoje przez deprovisioning/reprovisioning maszyny Wirtualnej (usunąć go, zachować dysk i ponownie utworzyć maszynę wirtualną).

- **Status maszyny wirtualnej RoleStateUnknown** — jeśli `role state unknown` migracja zostanie zatrzymana z powodu komunikatu o błędzie, sprawdź maszynę wirtualną za pomocą portalu i upewnij się, że jest uruchomiona. Ten błąd zazwyczaj zniknie samodzielnie (nie jest wymagane korygowanie) po kilku minutach i jest często `start`typu `stop` `restart` przejściowego często postrzegane podczas maszyny wirtualnej , operacje. **Zalecana praktyka:** ponownie spróbuj migracji po kilku minutach.

- **Klaster sieci szkieletowej nie istnieje** — w niektórych przypadkach niektóre maszyny wirtualne nie mogą być migrowane z różnych nieparzystych powodów. Jednym z tych znanych przypadków jest, jeśli maszyna wirtualna została niedawno utworzona (w ciągu ostatniego tygodnia lub tak) i stało się wylądować klastra platformy Azure, który nie jest jeszcze wyposażony dla obciążeń usługi Azure Resource Manager.  Pojawi się błąd, `fabric cluster does not exist` który mówi i maszyny Wirtualnej nie można migrować. Oczekiwanie na kilka dni zwykle rozwiąże ten konkretny problem, ponieważ klaster wkrótce włączy usługę Azure Resource Manager. Jednak jednym z natychmiastowych `stop-deallocate` obejść jest do maszyny Wirtualnej, a następnie kontynuować migrację i uruchomić maszynę wirtualną kopii zapasowej w usłudze Azure Resource Manager po migracji.

### <a name="pitfalls-to-avoid"></a>Pułapki, których należy unikać

- Nie należy podejmować skrótów i pominąć weryfikacji/przygotować/przerwać migracje przebiegu suchego.
- Większość, jeśli nie wszystkie, potencjalnych problemów pojawi się podczas sprawdzania poprawności / przygotowania / przerwania kroków.

## <a name="migration"></a>Migracja

### <a name="technical-considerations-and-tradeoffs"></a>Względy techniczne i kompromisy

Teraz jesteś gotowy, ponieważ przepracowałeś znane problemy ze środowiskiem.

W przypadku rzeczywistych migracji warto rozważyć:

1. Planowanie i planowanie sieci wirtualnej (najmniejszej jednostki migracji) z rosnącym priorytetem.  Najpierw wykonaj proste sieci wirtualne i postępuj z bardziej skomplikowanymi sieciami wirtualnymi.
2. Większość klientów będzie miała środowiska nieprodukcyjne i produkcyjne.  Zaplanuj produkcję jako ostatnią.
3. **(OPCJONALNIE)** Zaplanuj przestoje konserwacji z dużą ilością bufora na wypadek wystąpienia nieoczekiwanych problemów.
4. Komunikuj się z zespołami pomocy technicznej i wyrównaj się z zespołami pomocy technicznej w przypadku wystąpienia problemów.

### <a name="patterns-of-success"></a>Wzorce sukcesu

Wskazówki techniczne z sekcji _Test laboratorium_ należy rozważyć i złagodzone przed rzeczywistą migrację.  Z odpowiednich testów migracji jest rzeczywiście nie zdarzenia.  W środowiskach produkcyjnych może być przydatne dodatkowe wsparcie, takie jak zaufany partner firmy Microsoft lub usługi Microsoft Premier.

### <a name="pitfalls-to-avoid"></a>Pułapki, których należy unikać

Nie w pełni testowanie może powodować problemy i opóźnienia w migracji.  

## <a name="beyond-migration"></a>Poza migracją

### <a name="technical-considerations-and-tradeoffs"></a>Względy techniczne i kompromisy

Teraz, gdy jesteś w usłudze Azure Resource Manager, maksymalizuj platformę.  Przeczytaj [omówienie usługi Azure Resource Manager,](../../azure-resource-manager/management/overview.md) aby dowiedzieć się więcej o dodatkowych korzyściach.

Rzeczy do rozważenia:

- Łączenie migracji z innymi działaniami.  Większość klientów decyduje się na okno konserwacji aplikacji.  Jeśli tak, możesz użyć tego przestoju, aby włączyć inne funkcje usługi Azure Resource Manager, takie jak szyfrowanie i migracja do dysków zarządzanych.
- Ponowne przeanalizowanie przyczyn technicznych i biznesowych usługi Azure Resource Manager; włączyć dodatkowe usługi dostępne tylko w usłudze Azure Resource Manager, które mają zastosowanie do środowiska.
- Zmodernizuj swoje środowisko dzięki usługom PaaS.

### <a name="patterns-of-success"></a>Wzorce sukcesu

Bądź celowy na jakie usługi chcesz teraz włączyć w usłudze Azure Resource Manager.  Wielu klientów znaleźć poniżej atrakcyjne dla swoich środowisk platformy Azure:

- [Kontrola dostępu oparta na rolach](../../role-based-access-control/overview.md).
- [Szablony usługi Azure Resource Manager ułatwiają i są bardziej kontrolowane wdrażanie.](../../azure-resource-manager/templates/overview.md)
- [Tagi](../../azure-resource-manager/management/tag-resources.md).
- [Kontrola aktywności](../../azure-resource-manager/management/view-activity-logs.md)
- [Zasady platformy Azure](../../governance/policy/overview.md)

### <a name="pitfalls-to-avoid"></a>Pułapki, których należy unikać

Pamiętaj, dlaczego ta podróż migracji klasyczna do usługi Azure Resource Manager została rozpoczęta.  Jakie były pierwotne powody biznesowe? Czy udało ci się osiągnąć powód biznesowy?


## <a name="next-steps"></a>Następne kroki

* [Omówienie migracji zasobów usługi IaaS obsługiwanej przez platformę z klasycznej do usługi Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrowanie zasobów usługi IaaS z klasycznego do usługi Azure Resource Manager za pomocą programu PowerShell](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrowanie zasobów usługi IaaS z klasycznego do usługi Azure Resource Manager za pomocą interfejsu wiersza polecenia](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migracja klasycznej bramy sieci VPN do Menedżera zasobów](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-classic-resource-manager-migration)
* [Migrowanie obwodów usługi ExpressRoute i skojarzonych sieci wirtualnych z klasycznego do modelu wdrażania Menedżera zasobów](https://docs.microsoft.com/azure/expressroute/expressroute-migration-classic-resource-manager)
* [Narzędzia społecznościowe pomagające w migracji zasobów IaaS z klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Przejrzyj najczęściej zadawane pytania dotyczące migracji zasobów usługi IaaS z klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
