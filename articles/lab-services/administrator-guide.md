---
title: Podręcznik administratora Azure Lab Services | Microsoft Docs
description: Ten przewodnik pomaga administratorom, którzy tworzą konta laboratorium i zarządzają nimi przy użyciu Azure Lab Services.
ms.topic: article
ms.date: 10/20/2020
ms.openlocfilehash: 3ad3ee38a6c08a6af85822d76012cc6dfc34ff4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462477"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services — Podręcznik administratora
Administratorzy IT, którzy zarządzają zasobami w chmurze University, są zwykle odpowiedzialni za skonfigurowanie konta laboratorium dla swojej szkoły. Po skonfigurowaniu konta Laboratorium Administratorzy lub wykładowcy tworzą laboratoria, które są zawarte w ramach tego konta. Ten artykuł zawiera ogólne omówienie zasobów platformy Azure, które są wykorzystywane, oraz wskazówki dotyczące ich tworzenia.

![Diagram wysokiego poziomu widoku zasobów platformy Azure na koncie laboratorium.](./media/administrator-guide/high-level-view.png)

- Laboratoria są hostowane w ramach subskrypcji platformy Azure, która jest własnością Azure Lab Services.
- Konta laboratorium, udostępnione galerii obrazów i wersje obrazów są hostowane w ramach subskrypcji.
- Możesz mieć konto laboratorium i galerię obrazów udostępnionych w tej samej grupie zasobów. Na tym diagramie znajdują się one w różnych grupach zasobów.

Aby uzyskać więcej informacji na temat architektury, zobacz temat [Architektura architektury Labs](./classroom-labs-fundamentals.md).

## <a name="subscription"></a>Subskrypcja
Twoje uczelnie mogą mieć co najmniej jedną subskrypcję platformy Azure. Subskrypcje służą do zarządzania rozliczeniami i zabezpieczeniami dla wszystkich zasobów i usług platformy Azure, które są używane w ramach tego konta, w tym kont laboratorium.

Relacja między kontem laboratorium a jego subskrypcją jest ważna, ponieważ:

- Rozliczenia są raportowane w ramach subskrypcji zawierającej konto laboratorium.
- Można przyznać użytkownikom w ramach dzierżawy Azure Active Directory (Azure AD) dostęp do Azure Lab Services. Użytkownika można dodać jako właściciela konta laboratorium lub współpracownika albo jako twórcę lub właściciela laboratorium.

Laboratoria i ich maszyny wirtualne są zarządzane i hostowane w ramach subskrypcji należącej do Azure Lab Services.

## <a name="resource-group"></a>Grupa zasobów
Subskrypcja zawiera co najmniej jedną grupę zasobów. Grupy zasobów służą do tworzenia grup logicznych zasobów platformy Azure, które są używane razem w ramach tego samego rozwiązania.  

Podczas tworzenia konta laboratorium należy skonfigurować grupę zasobów, która zawiera konto laboratorium. 

Grupa zasobów jest również wymagana podczas tworzenia [galerii obrazów udostępnionych](#shared-image-gallery). Możesz umieścić konto laboratorium i galerię obrazów udostępnionych w tej samej grupie zasobów lub w dwóch oddzielnych grupach zasobów. Jeśli planujesz udostępnienie galerii obrazów w różnych rozwiązaniach, warto wykonać te drugie podejście.

Podczas tworzenia konta laboratorium można automatycznie utworzyć i dołączyć udostępnioną galerię obrazów.  Ta opcja spowoduje utworzenie w oddzielnych grupach zasobów konta laboratorium i udostępnionej galerii obrazów. To zachowanie zostanie wyświetlone po wykonaniu kroków opisanych w samouczku [Konfigurowanie udostępnionego obrazu w momencie tworzenia konta laboratorium](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation) . Na początku tego artykułu zostanie użyta ta konfiguracja. 

Zalecamy zainwestowanie czasu z góry, aby zaplanować strukturę grup zasobów, ponieważ *nie* jest możliwe Zmiana grupy zasobów konta laboratorium lub udostępnionej galerii obrazów po jej utworzeniu. Jeśli musisz zmienić grupę zasobów dla tych zasobów, musisz usunąć i ponownie utworzyć swoje konto laboratorium lub udostępnioną galerię obrazów.

## <a name="lab-account"></a>Konto laboratorium

Konto laboratorium służy jako kontener dla jednej lub kilku laboratoriów. Gdy rozpoczynasz pracę z Azure Lab Services, najprawdopodobniej jest to jedno konto laboratorium. W miarę skalowania użycia laboratorium możesz później utworzyć więcej kont laboratorium.

Na poniższej liście przedstawiono scenariusze, w których może być korzystne więcej niż jedno konto laboratorium:

- **Zarządzanie różnymi wymaganiami dotyczącymi zasad w laboratoriach**

    Podczas konfigurowania konta laboratorium należy ustawić zasady, które mają zastosowanie do *wszystkich* laboratoriów w ramach konta laboratorium, takie jak:
    - Sieć wirtualna platformy Azure z udostępnionymi zasobami dostępnymi do laboratorium. Na przykład może istnieć zestaw laboratoriów, które wymagają dostępu do udostępnionego zestawu danych w ramach sieci wirtualnej.
    - Obrazy maszyn wirtualnych, których laboratoria mogą używać do tworzenia maszyn wirtualnych. Na przykład może istnieć zestaw laboratoriów, które potrzebują dostępu do [Data Science VM na potrzeby](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) obrazu systemu Linux Azure Marketplace.

    Jeśli każda z laboratoriów ma unikatowe wymagania dotyczące zasad, może być korzystne utworzenie oddzielnych kont laboratorium do zarządzania każdym laboratorium osobno.

- **Przypisywanie oddzielnego budżetu do każdego konta laboratorium**
  
    Zamiast zgłaszać wszystkie koszty laboratorium za pomocą jednego konta laboratorium, może być konieczne bardziej wyraźnie przydzielenie budżetu. Na przykład można utworzyć osobne konta laboratorium dla działu matematycznego, działu nauki komputerowego i tak dalej, aby dystrybuować budżet między działami.  Następnie można wyświetlić koszt poszczególnych poszczególnych kont laboratorium przy użyciu [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md).

- **Izolowanie laboratoriów pilotażowych od aktywnych i produkcyjnych laboratoriów**
  
    Mogą wystąpić sytuacje, w których chcesz zmienić zasady pilotażowe dla konta laboratorium bez wpływu na aktywną lub produkcyjną laboratoriów. W tym scenariuszu tworzenie oddzielnego konta laboratorium do celów pilotażowych pozwala na odizolowanie zmian. 

## <a name="lab"></a>Laboratorium

Laboratorium zawiera maszyny wirtualne, które są przypisane do pojedynczego ucznia.  Ogólnie rzecz biorąc, można oczekiwać:

- Ma jedno laboratorium dla każdej klasy.
- Utwórz nowy zestaw laboratoriów dla każdego z semestrów, kwartałów lub innych systemów akademickich, z których korzystasz. W przypadku klas, które muszą korzystać z tego samego obrazu, należy użyć [udostępnionej galerii obrazów](#shared-image-gallery). W ten sposób możesz ponownie wykorzystać obrazy w laboratoriach i okresach akademickich.

Podczas określania sposobu tworzenia struktury laboratoriów należy wziąć pod uwagę następujące kwestie:

- **Wszystkie maszyny wirtualne w laboratorium są wdrażane przy użyciu tego samego obrazu, który został opublikowany**

    W związku z tym, jeśli masz klasę, która wymaga opublikowania w tym samym czasie różnych obrazów laboratoryjnych, należy utworzyć osobne laboratorium dla każdego obrazu.
  
- **Limit przydziału użycia jest ustawiany na poziomie laboratorium i ma zastosowanie do wszystkich użytkowników w laboratorium**

    Aby ustawić różne przydziały dla użytkowników, należy utworzyć osobne laboratoria. Można jednak dodać więcej godzin do określonych użytkowników po ustawieniu limitu przydziału.
  
- **Harmonogram uruchamiania lub zamykania jest ustawiany na poziomie laboratorium i ma zastosowanie do wszystkich maszyn wirtualnych w laboratorium**

    Podobnie jak w przypadku ustawienia limitu przydziału, jeśli trzeba ustawić różne harmonogramy dla użytkowników, należy utworzyć oddzielne laboratorium dla każdego harmonogramu.

Domyślnie każde laboratorium ma własną sieć wirtualną.  W przypadku włączenia komunikacji równorzędnej sieci wirtualnej każde laboratorium będzie miało własną podsieć równorzędną z określoną siecią wirtualną.

## <a name="shared-image-gallery"></a>Galeria obrazów udostępnionych

Udostępniona Galeria obrazów jest dołączona do konta laboratorium i służy jako centralne repozytorium do przechowywania obrazów. Obraz jest zapisywany w galerii, gdy nauczycieli zdecyduje się wyeksportować go z maszyny wirtualnej szablonu laboratorium. Za każdym razem, gdy nauczycieli wprowadza zmiany do szablonu maszyny wirtualnej i eksportuje je, nowe wersje obrazu są zapisywane i są zachowywane poprzednie wersje.

Instruktorzy mogą publikować wersję obrazu z galerii udostępnionych obrazów podczas tworzenia nowego laboratorium. Mimo że Galeria przechowuje wiele wersji obrazu, wykładowcy mogą wybrać tylko najnowszą wersję podczas tworzenia laboratorium.

Udostępniona Galeria obrazów jest opcjonalnym zasobem, który może nie być konieczny natychmiast, jeśli zaczynasz od kilku laboratoriów. Jednak Galeria obrazów udostępnionych oferuje wiele korzyści, które są przydatne podczas skalowania w górę do dodatkowych laboratoriów:

- **Możesz zapisywać wersje obrazu maszyny wirtualnej szablonu i zarządzać nimi**

    Warto utworzyć obraz niestandardowy lub wprowadzić zmiany (oprogramowanie, konfigurację itd.) do obrazu z publicznej galerii portalu Azure Marketplace.  Na przykład typowe dla nauczycieli są wymagane do zainstalowania innego oprogramowania lub narzędzi. Zamiast wymagać od uczniów ręcznego instalowania tych wymagań wstępnych, różne wersje obrazu maszyny wirtualnej mogą zostać wyeksportowane do galerii obrazów udostępnionych. Te wersje obrazów można następnie użyć podczas tworzenia nowych laboratoriów.

- **Możesz udostępniać i ponownie używać szablonów maszyn wirtualnych w laboratoriach**

    Możesz zapisać i ponownie użyć obrazu, aby nie trzeba było go konfigurować od podstaw za każdym razem, gdy tworzysz nowe laboratorium. Na przykład jeśli wiele klas musi korzystać z tego samego obrazu, można je utworzyć raz i wyeksportować do galerii obrazów udostępnionych, aby można je było udostępnić w laboratoriach.

- **Dostępność obrazu jest zapewniana przez automatyczną replikację**

    Po zapisaniu obrazu z laboratorium do udostępnionej galerii obrazów jest on automatycznie replikowany do innych [regionów w tej samej lokalizacji geograficznej](https://azure.microsoft.com/global-infrastructure/regions/). Jeśli wystąpiła awaria dla regionu, publikowanie obrazu w laboratorium nie ma żadnego oddziaływania, ponieważ można użyć repliki obrazu z innego regionu.  Publikowanie maszyn wirtualnych z wielu replik może być również pomocne w wydajności.

Aby logicznie grupować obrazy udostępnione, można wykonać jedną z następujących czynności:

- Utwórz wiele udostępnionych galerii obrazów. Każde konto laboratorium może łączyć się tylko z jedną udostępnioną galerią obrazów, dlatego ta opcja wymaga również utworzenia wielu kont laboratorium.
- Użyj pojedynczej udostępnionej galerii obrazów, która jest współużytkowana przez wiele kont laboratorium. W takim przypadku każde konto laboratorium może włączyć tylko obrazy, które mają zastosowanie do laboratoriów na tym koncie.

## <a name="naming"></a>Nazewnictwo

Po rozpoczęciu pracy z Azure Lab Services zalecamy ustanowienie konwencji nazewnictwa dla grup zasobów, kont laboratorium, laboratoriów oraz udostępnionej galerii obrazów. Chociaż ustanowione konwencje nazewnictwa będą unikatowe dla potrzeb organizacji, w poniższej tabeli przedstawiono ogólne wytyczne:

| Typ zasobu | Rola | Sugerowany wzorzec | Przykłady |
| ------------- | ---- | ----------------- | -------- | 
| Grupa zasobów | Zawiera jedno lub więcej kont laboratorium i jedną lub więcej udostępnionych galerii obrazów | \<organization short name\>-\<environment\>-RG<ul><li>**Krótka nazwa organizacji** identyfikuje nazwę organizacji obsługiwaną przez grupę zasobów.</li><li>**Środowisko** identyfikuje środowisko dla zasobu, na przykład *pilotażowe* lub *produkcyjne*.</li><li>**RG** oznacza *grupę zasobów* typu zasobu.</li></ul> | contosouniversitylabs — RG<br/>contosouniversitylabs-pilotaż-RG<br/>contosouniversitylabs — prod-RG |
| Konto laboratorium | Zawiera co najmniej jedną Labs | \<organization short name\>-\<environment\>-La<ul><li>**Krótka nazwa organizacji** identyfikuje nazwę organizacji obsługiwaną przez grupę zasobów.</li><li>**Środowisko** identyfikuje środowisko dla zasobu, na przykład *pilotażowe* lub *produkcyjne*.</li><li>**La** oznacza *konto laboratorium* typu zasobu.</li></ul> | contosouniversitylabs — La<br/>mathdeptlabs — La<br/>sciencedeptlabs-pilotaż-La<br/>sciencedeptlabs — prod-La |
| Laboratorium | Zawiera co najmniej jedną maszynę wirtualną |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>**Nazwa klasy** identyfikuje nazwę klasy obsługiwanej przez laboratorium.</li><li>**Przedział czasu** określa przedział czasu, w którym jest oferowana Klasa.</li>**Identyfikator nauczycieli** identyfikuje nauczycieli, który jest właścicielem laboratorium.</li></ul> | CS1234-fall2019-jankowalski<br/>CS1234-spring2019-jankowalski |
| Galeria obrazów udostępnionych | Zawiera co najmniej jedną wersję obrazu maszyny wirtualnej | \<organization short name\>zdjęć | contosouniversitylabsgallery |

Aby uzyskać więcej informacji o nazewnictwie innych zasobów platformy Azure, zobacz [konwencje nazewnictwa dla zasobów platformy Azure](/azure/architecture/best-practices/naming-conventions).

## <a name="regionslocations"></a>Regions\locations

Po skonfigurowaniu zasobów Azure Lab Services należy podać region lub lokalizację centrum danych, które będzie hostować zasoby. W następnych sekcjach opisano, w jaki sposób region lub lokalizacja może mieć wpływ na każdy zasób, który jest związany z konfigurowaniem laboratorium.

### <a name="resource-group"></a>Grupa zasobów

Region określa centrum danych, w którym są przechowywane informacje o grupie zasobów. Zasoby platformy Azure zawarte w grupie zasobów mogą znajdować się w innym regionie niż jego element nadrzędny.

### <a name="lab-account"></a>Konto laboratorium

Lokalizacja konta laboratorium wskazuje region, w którym znajduje się zasób.  

### <a name="lab"></a>Laboratorium

Lokalizacja, w której istnieje laboratorium, różni się w zależności od następujących czynników:

  - **Konto laboratorium jest połączone z siecią wirtualną**
  
    [Konto laboratorium można połączyć z siecią wirtualną](./how-to-connect-peer-virtual-network.md) , gdy znajdują się one w tym samym regionie.  Gdy konto laboratorium jest połączone z siecią wirtualną, laboratoria są tworzone automatycznie w tym samym regionie co konto laboratorium i Sieć wirtualna.

    > [!NOTE]
    > W przypadku komunikacji równorzędnej z siecią wirtualną przy użyciu konta laboratorium opcja **Zezwalaj na wybór laboratorium dla programu Lab Creator** jest wyłączona. Aby uzyskać więcej informacji, zobacz [Zezwalanie na twórcę laboratorium do wybierania lokalizacji dla laboratorium](./allow-lab-creator-pick-lab-location.md).
    
  - **Żadna Sieć wirtualna nie jest połączona za pomocą komunikacji równorzędnej *, a* twórcy laboratorium nie mogą wybrać lokalizacji laboratorium**
  
    Gdy *żadna* Sieć wirtualna nie jest połączona z kontem laboratorium, a [twórcy laboratorium *nie mogą* wybrać lokalizacji laboratorium](./allow-lab-creator-pick-lab-location.md), laboratoria są automatycznie tworzone w regionie, który ma dostępną pojemność maszyny wirtualnej.  W Azure Lab Services szuka dostępności w [regionach, które znajdują się w tej samej lokalizacji geograficznej co konto laboratorium](https://azure.microsoft.com/global-infrastructure/regions).

  - **Żadna Sieć wirtualna nie jest połączona za pomocą komunikacji równorzędnej *, a* twórcy laboratorium mogą wybrać lokalizację laboratorium**
       
    Gdy *żadna* Sieć wirtualna nie jest połączona za pomocą komunikacji równorzędnej, a [twórcy laboratorium nie *mogą* wybrać lokalizacji laboratorium](./allow-lab-creator-pick-lab-location.md), lokalizacje, które mogą zostać wybrane przez twórcę laboratorium, zależą od dostępnej pojemności.

> [!NOTE]
> Aby zapewnić, że region ma wystarczającą pojemność maszyny wirtualnej, ważne jest, aby najpierw zażądać pojemności przy użyciu konta laboratorium podczas tworzenia laboratorium.

Ogólna zasada polega na ustawieniu regionu zasobu, który znajduje się najbliżej jego użytkowników. W przypadku laboratoriów oznacza to utworzenie laboratorium, które jest najbliższe uczniom. W przypadku kursów online, których studenci znajdują się na całym świecie, Skorzystaj z najlepszych orzeczeń, aby utworzyć laboratorium, które znajduje się centralnie. Można też podzielić klasę na wiele laboratoriów zgodnie z regionami uczniów.

## <a name="vm-sizing"></a>Rozmiar maszyny wirtualnej

Gdy Administratorzy lub twórcy laboratorium tworzą laboratorium, mogą wybrać różne rozmiary maszyn wirtualnych, w zależności od potrzeb swojej klasy. Należy pamiętać, że dostępność rozmiaru obliczeń zależy od regionu, w którym znajduje się konto laboratorium.

| Rozmiar | Specyfikacje | Seria | Sugerowane użycie |
| ---- | ----- | ------ | ------------- |
| Mały| <ul><li>2 &nbsp; rdzenie</li><li>Pamięć RAM 3,5 gigabajtów (GB)</li> | [Standardowa_A2_v2](../virtual-machines/av2-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Najlepiej dopasowane do wiersza polecenia, otwierania przeglądarki sieci Web, serwerów sieci Web o niewielkim ruchu, małych i średnich baz danych. |
| Śred. | <ul><li>4 &nbsp; rdzenie</li><li>7 &nbsp; GB &nbsp; pamięci RAM</li> | [Standardowa_A4_v2](../virtual-machines/av2-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Najlepiej dopasowane do relacyjnych baz danych, buforowania w pamięci i analizy. |
| Średni (Wirtualizacja zagnieżdżona) | <ul><li>4 &nbsp; rdzenie</li><li>16 &nbsp; GB &nbsp; pamięci RAM</li></ul> | [Standardowa_D4s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#dsv3-series) | Najlepiej dopasowane do relacyjnych baz danych, buforowania w pamięci i analizy.
| Duży | <ul><li>8 &nbsp; rdzeni</li><li>16 &nbsp; GB &nbsp; pamięci RAM</li></ul>  | [Standardowa_A8_v2](../virtual-machines/av2-series.md) | Najlepiej dopasowane do aplikacji wymagających szybszych procesorów, lepszej wydajności dysków lokalnych, dużych baz danych i dużych pamięci podręcznych pamięci.  Ten rozmiar obsługuje również wirtualizację zagnieżdżoną. |
| Duże (Wirtualizacja zagnieżdżona) | <ul><li>8 &nbsp; rdzeni</li><li>32 &nbsp; GB &nbsp; pamięci RAM</li></ul>  | [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#dsv3-series) | Najlepiej dopasowane do aplikacji wymagających szybszych procesorów, lepszej wydajności dysków lokalnych, dużych baz danych i dużych pamięci podręcznych pamięci. |
| Mały procesor GPU (wizualizacja) | <ul><li>6 &nbsp; rdzeni</li><li>56 &nbsp; GB &nbsp; pamięci RAM</li>  | [Standardowa_NV6](../virtual-machines/nv-series.md) | Najlepiej dopasowane do zdalnej wizualizacji, przesyłania strumieniowego, gier i kodowania przy użyciu platform, takich jak OpenGL i DirectX. |
| Mały procesor GPU (obliczenia) | <ul><li>6 &nbsp; rdzeni</li><li>56 &nbsp; GB &nbsp; pamięci RAM</li></ul>  | [Standardowa_NC6](../virtual-machines/nc-series.md) |Najlepiej dopasowane do aplikacji intensywnie korzystających z komputerów, takich jak AI i uczenie głębokie. |
| Średni procesor GPU (wizualizacja) | <ul><li>12 &nbsp; rdzeni</li><li>112 &nbsp; GB &nbsp; pamięci RAM</li></ul>  | [Standardowa_NV12](../virtual-machines/nv-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Najlepiej dopasowane do zdalnej wizualizacji, przesyłania strumieniowego, gier i kodowania przy użyciu platform, takich jak OpenGL i DirectX. |

## <a name="manage-identity"></a>Zarządzanie tożsamością

Korzystając z [kontroli dostępu opartej na rolach (RBAC) na platformie Azure](../role-based-access-control/overview.md) w celu uzyskiwania dostępu do kont laboratorium i laboratoriów, można przypisać następujące role:

- **Właściciel** konta laboratorium

    Administrator, który tworzy konto laboratorium, ma automatycznie przypisaną rolę właściciela konta laboratorium. Rola właściciela może:
     - Zmień ustawienia konta laboratorium.
     - Udziel innym administratorom dostępu do konta laboratorium jako właściciela lub współautora.
     - Przyznaj nauczycielom dostęp do laboratoriów jako twórca, właściciel lub współautor.
     - Twórz Wszystkie laboratoria na koncie laboratorium i zarządzaj nimi.

- **Współautor** konta laboratorium

    Administrator, który ma przypisaną rolę współautor, może:
    - Zmień ustawienia konta laboratorium.
    - Twórz Wszystkie laboratoria na koncie laboratorium i zarządzaj nimi.

    Jednak współautor *nie może* przyznać innym użytkownikom dostępu do kont laboratorium lub laboratoriów.

- **Twórca laboratorium**

    Aby utworzyć laboratoria na koncie laboratorium, nauczycieli musi być członkiem roli twórca laboratorium.  Nauczycieli, który tworzy laboratorium, jest automatycznie dodawany jako właściciel laboratorium. Aby uzyskać więcej informacji, zobacz [Dodawanie użytkownika do roli twórca laboratorium](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role). 

- **Właściciel** lub **współautor** laboratorium
  
    Nauczycieli w roli właściciela laboratorium lub współautora mogą wyświetlać i zmieniać ustawienia laboratorium. Osoba musi być również członkiem roli czytelnik konta laboratorium.

    Kluczową różnicą między rolą właściciela laboratorium i współautorów jest to, że tylko właściciel może udzielić innym użytkownikom dostępu do zarządzania laboratorium. Współautor *nie może* udzielić innym użytkownikom dostępu do zarządzania laboratorium.

- **Galeria obrazów udostępnionych**

    Po dołączeniu udostępnionej galerii obrazów do konta laboratorium, właściciele konta laboratorium i współautorzy oraz twórcy laboratorium, właściciele laboratorium i współautorzy laboratorium mają automatycznie udzielony dostęp do wyświetlania i zapisywania obrazów w galerii.

Podczas przypisywania ról pomocne są następujące porady:

   - Zwykle tylko administratorzy powinni być członkami roli właściciela konta laboratorium lub współautora. Konto laboratorium może mieć więcej niż jednego właściciela lub współautora.
   - Aby dać nauczycielom możliwość tworzenia nowych laboratoriów i zarządzania nimi, należy przypisać im tylko rolę twórcy laboratorium.
   - Aby dać wykładowcom możliwość zarządzania określonymi laboratoriami, ale *nie* możliwość tworzenia nowych laboratoriów, należy przypisać im rolę właściciel lub współautor dla każdego laboratorium, które będą zarządzane. Na przykład możesz chcieć, aby profesor i asystent nauczania mogli współistnieć z laboratorium. Aby uzyskać więcej informacji, zobacz [Dodawanie właścicieli do laboratorium](./how-to-add-user-lab-owner.md).

## <a name="pricing"></a>Ceny

### <a name="azure-lab-services"></a>Azure Lab Services

Aby dowiedzieć się więcej o cenach, zobacz [cennik Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).


### <a name="shared-image-gallery"></a>Galeria obrazów udostępnionych

Należy również wziąć pod uwagę Cennik usługi galerii obrazów udostępnionych, jeśli planujesz używać udostępnionych galerii obrazów do przechowywania wersji obrazów i zarządzania nimi. 

Tworzenie galerii obrazów udostępnionych i dołączanie jej do konta laboratorium jest bezpłatne. Koszt nie jest naliczany do momentu zapisania wersji obrazu w galerii. Ceny za korzystanie z galerii obrazów udostępnionych są zwykle dość nieznaczne, ale ważne jest, aby zrozumieć, jak jest obliczana, ponieważ nie jest ona uwzględniona w cenach Azure Lab Services.  

#### <a name="storage-charges"></a>Opłaty za magazyn

Aby przechowywać wersje obrazów, udostępniona Galeria obrazów używa dysków zarządzanych w standardowym dysku twardym (dysk twardy). Rozmiar używanego dysku zarządzanego przez dysk twardy zależy od rozmiaru przechowywanego obrazu. Aby dowiedzieć się więcej o cenach, zobacz [Cennik dysków zarządzanych](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>Opłaty za replikację i ruch wychodzący z sieci

Gdy zapisujesz wersję obrazu przy użyciu maszyny wirtualnej szablonu laboratorium, Azure Lab Services najpierw zapisuje ją w regionie źródłowym, a następnie automatycznie replikuje wersję obrazu źródłowego do co najmniej jednego regionu docelowego. 

Należy pamiętać, że Azure Lab Services automatycznie replikuje wersję obrazu źródłowego do wszystkich [regionów docelowych w obszarze geograficznym](https://azure.microsoft.com/global-infrastructure/regions/) , w którym znajduje się laboratorium. Na przykład jeśli laboratorium znajduje się w lokalizacji geograficznej USA, wersja obrazu jest replikowana do każdego z ośmiu regionów, które znajdują się w Stanach Zjednoczonych.

Opłata za ruch wychodzący sieci występuje, gdy wersja obrazu jest replikowana z regionu źródłowego do dodatkowych regionów docelowych. Opłata jest naliczana na podstawie rozmiaru wersji obrazu, gdy dane obrazu są początkowo przenoszone z regionu źródłowego.  Aby uzyskać szczegółowe informacje o cenach, zobacz [szczegóły cennika dotyczącego przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/).

Opłaty za ruch wychodzący mogą zostać uchylone dla klientów [rozwiązań edukacyjnych](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) . Aby dowiedzieć się więcej, skontaktuj się z kierownikiem ds. klientów. 

Aby uzyskać więcej informacji, zobacz "Jakie programy transferu danych istnieją dla klientów akademickich i jak mam zakwalifikować"? w sekcji często zadawane pytania na stronie [programy dla instytucji edukacyjnych](https://azure.microsoft.com/pricing/details/bandwidth/) .

#### <a name="pricing-example"></a>Przykład cen

Przyjrzyjmy się przykładowi kosztu zapisania obrazu maszyny wirtualnej szablonu do galerii obrazów udostępnionych. Założono następujące scenariusze:

- Masz jeden niestandardowy obraz maszyny wirtualnej.
- Zapisujesz dwie wersje obrazu.
- Twoje laboratorium znajduje się w Stanach Zjednoczonych, które zawierają łącznie osiem regionów.
- Każda wersja obrazu ma rozmiar 32 GB; w związku z tym cena dysku zarządzanego przez dysk twardy wynosi $1,54 miesięcznie.

Łączny koszt miesięcznie jest szacowany jako:

* *Liczba obrazów liczba wersji z numerem liczba &times; &times; replik &times; Cena dysku zarządzanego = łączny koszt na miesiąc*

W tym przykładzie kosztem jest:

* 1 obraz niestandardowy (32 GB) &times; 2 wersje &times; 8 regiony USA &times; $1,54 = $24,64 miesięcznie

> [!NOTE]
> Poprzednie obliczenie jest tylko na przykład. Obejmuje to koszty magazynowania związane z korzystaniem z galerii obrazów udostępnionych i *nie* obejmuje kosztów wychodzących. Aby uzyskać aktualną cenę usługi Storage, zobacz [Cennik usługi Managed disks](https://azure.microsoft.com/en-us/pricing/details/managed-disks/).

#### <a name="cost-management"></a>Zarządzanie kosztami

Ważne jest, aby administratorzy kont laboratorium mogli zarządzać kosztami, przez zwykłe usuwanie niepotrzebnych wersji obrazu z galerii. 

Nie należy usuwać replikacji do określonych regionów w celu zmniejszenia kosztów, chociaż ta opcja istnieje w galerii obrazów udostępnionych. Zmiany replikacji mogą mieć niekorzystny wpływ na możliwość Azure Lab Services publikowania maszyn wirtualnych z obrazów zapisanych w galerii obrazów udostępnionych.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat konfigurowania laboratoriów i zarządzania nimi, zobacz:

- [Przewodnik konfigurowania konta laboratorium](account-setup-guide.md)  
- [Przewodnik po konfiguracji laboratorium](setup-guide.md)  
- [Zarządzanie kosztami dla laboratoriów](cost-management-guide.md)  
- [Używanie Azure Lab Services w zespołach](lab-services-within-teams-overview.md)
