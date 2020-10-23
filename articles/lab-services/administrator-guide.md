---
title: Podręcznik administratora Azure Lab Services | Microsoft Docs
description: Ten przewodnik pomaga administratorom, którzy tworzą konta laboratorium i zarządzają nimi przy użyciu Azure Lab Services.
ms.topic: article
ms.date: 10/20/2020
ms.openlocfilehash: a39ee2cc57c8fc1497c3798759bd40d1ed2976e3
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425305"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services — Podręcznik administratora
Administratorzy IT, którzy zarządzają zasobami w chmurze University, są zwykle odpowiedzialni za skonfigurowanie konta laboratorium dla swojej szkoły. Po skonfigurowaniu konta Laboratorium Administratorzy lub wykładowcy tworzą laboratoria klas, które są zawarte w ramach konta laboratorium. Ten artykuł zawiera ogólne omówienie związanych zasobów platformy Azure oraz wskazówki dotyczące ich tworzenia.

![Widok wysokiego poziomu zasobów platformy Azure na koncie laboratorium](./media/administrator-guide/high-level-view.png)

- Laboratoria zajęć są hostowane w ramach subskrypcji platformy Azure należącej do Azure Lab Services.
- Konta laboratorium, udostępnione galerii obrazów i wersje obrazów są hostowane w ramach subskrypcji.
- Możesz mieć konto laboratorium i galerię obrazów udostępnionych w tej samej grupie zasobów. Na tym diagramie znajdują się one w różnych grupach zasobów. 

## <a name="subscription"></a>Subskrypcja
Twój Uniwersytet ma co najmniej jedną subskrypcję platformy Azure. Subskrypcja służy do zarządzania rozliczeniami i zabezpieczeniami dla wszystkich resources\services platformy Azure, które są używane w ramach tego konta, w tym kont laboratorium.

Relacja między kontem laboratorium a jego subskrypcją jest ważna, ponieważ:

- Rozliczenia są raportowane w ramach subskrypcji zawierającej konto laboratorium.
- Można udzielić użytkownikom Azure Active Directory dostęp do dzierżawy w ramach subskrypcji usługi Azure Lab Services. Możesz dodać użytkownika jako konto laboratorium owner\contributor, twórcę laboratorium klasy lub właściciela laboratorium klasowego.

Pracownie i ich maszyny wirtualne są zarządzane i hostowane dla Ciebie w ramach subskrypcji należącej do Azure Lab Services.

## <a name="resource-group"></a>Grupa zasobów
Subskrypcja zawiera co najmniej jedną grupę zasobów. Grupy zasobów służą do tworzenia grup logicznych zasobów platformy Azure, które są używane razem w ramach tego samego rozwiązania.  

Podczas tworzenia konta laboratorium należy skonfigurować grupę zasobów, która zawiera konto laboratorium. 

Grupa zasobów jest również wymagana podczas tworzenia [galerii obrazów udostępnionych](#shared-image-gallery). Możesz wybrać opcję umieszczenia konta laboratorium i udostępnionej galerii obrazów w dwóch oddzielnych grupach zasobów, która jest typowa, jeśli planujesz udostępnienie galerii obrazów w różnych rozwiązaniach. Można też zdecydować się na umieszczenie ich w tej samej grupie zasobów.

Podczas tworzenia konta laboratorium można automatycznie utworzyć i dołączyć udostępnioną galerię obrazów.  Ta opcja spowoduje utworzenie w oddzielnych grupach zasobów konta laboratorium i udostępnionej galerii obrazów. To zachowanie zostanie wyświetlone w przypadku korzystania z kroków opisanych w tym samouczku: [Konfigurowanie galerii obrazów udostępnionych w momencie tworzenia konta laboratorium](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). Obraz w górnej części tego artykułu używa również tej konfiguracji. 

Zalecamy nadanie inwestycji przed zaplanowaniem struktury grup zasobów, ponieważ *nie* jest możliwe Zmiana grupy zasobów konta laboratorium lub udostępnionej galerii obrazów po jej utworzeniu. Jeśli musisz zmienić grupę zasobów dla tych zasobów, musisz usunąć i utworzyć ponownie konto laboratorium and\or udostępnionej galerii obrazów.

## <a name="lab-account"></a>Konto laboratorium

Konto laboratorium służy jako kontener dla jednej lub większej liczby laboratoriów zajęć. Gdy rozpoczynasz pracę z Azure Lab Services, często ma ono tylko jedno konto laboratorium. W miarę skalowania użycia laboratorium możesz później utworzyć więcej kont laboratorium.

Na poniższej liście przedstawiono scenariusze, w których może być korzystne więcej niż jedno konto laboratorium:

- **Zarządzanie różnymi wymaganiami dotyczącymi zasad w laboratoriach zajęć**

    Podczas konfigurowania konta laboratorium należy ustawić zasady, które mają zastosowanie do *wszystkich* laboratoriów klasy na koncie laboratorium, takich jak:
    - Sieć wirtualna platformy Azure z zasobami udostępnionymi, do których może uzyskać dostęp laboratorium klasy. Na przykład może istnieć zestaw klas laboratoriów, które wymagają dostępu do udostępnionego zestawu danych w ramach sieci wirtualnej.
    - Obrazy maszyn wirtualnych, które mogą być używane przez laboratorium zajęć do tworzenia maszyn wirtualnych. Na przykład może istnieć zestaw klas laboratoriów, które wymagają dostępu do obrazu portalu Marketplace w witrynie [Data Science VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) .

    Jeśli posiadasz laboratoria, które mają unikatowe wymagania dotyczące zasad ze sobą, może być korzystne utworzenie oddzielnych kont laboratorium do oddzielnego zarządzania tymi laboratoriami.

- **Oddzielanie budżetu według konta laboratorium**
  
    Zamiast zgłaszać wszystkie koszty laboratorium z wykorzystaniem jednego konta laboratorium, może być konieczne bardziej przejrzysty budżet. Można na przykład utworzyć konta laboratorium dla działu matematycznego, działu nauki komputerowego i tak dalej, aby rozdzielić budżet między działy.  Następnie można wyświetlić koszt poszczególnych poszczególnych kont laboratorium przy użyciu [Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview).

- **Izolowanie laboratoriów pilotażowych z active\production Labs**
  
    Mogą wystąpić sytuacje, w których chcesz przeprowadzić pilotażowe zmiany zasad dla konta laboratorium bez potencjalnie wpływu na active\production Labs. W tym scenariuszu tworzenie oddzielnego konta laboratorium do celów pilotażowych pozwala na odizolowanie zmian. 

## <a name="classroom-lab"></a>Laboratorium zajęć

Laboratorium zajęć zawiera maszyny wirtualne, które są przypisane do pojedynczego ucznia.  Ogólnie rzecz biorąc, można oczekiwać:

- Ma jedno laboratorium klasy dla każdej klasy.
- Utwórz nowy zestaw klas Labs dla każdego semestru (lub dla każdego przedziału czasowego, który jest oferowany dla klasy). Zwykle w przypadku klas, które mają ten sam obraz, należy użyć [udostępnionej galerii obrazów](#shared-image-gallery) do ponownego użycia obrazów w laboratoriach i semestrach.

Podczas określania sposobu tworzenia struktury laboratoriów zajęć należy wziąć pod uwagę następujące kwestie:

- **Wszystkie maszyny wirtualne w laboratorium zajęć są wdrażane przy użyciu tego samego obrazu, który został opublikowany**

    W związku z tym, jeśli dysponujesz klasą, która wymaga publikacji różnych obrazów laboratoryjnych, należy utworzyć osobne laboratorium dla każdej z nich.
  
- **Limit przydziału użycia jest ustawiany na poziomie laboratorium i ma zastosowanie do wszystkich użytkowników w laboratorium**

    Aby ustawić różne przydziały dla użytkowników, należy utworzyć osobne laboratoria klasy. Jednak po ustawieniu limitu przydziału można dodać więcej godzin do określonego użytkownika.
  
- **Harmonogram uruchamiania lub zamykania jest ustawiany na poziomie laboratorium i ma zastosowanie do wszystkich maszyn wirtualnych w laboratorium**

    Podobnie jak w poprzednim punkcie, jeśli trzeba ustawić różne harmonogramy dla użytkowników, należy utworzyć osobne laboratoria zajęć.

Domyślnie każde laboratorium klasy będzie miało własną sieć wirtualną.  Jeśli jest włączona Komunikacja równorzędna sieci wirtualnych, każde laboratorium klasy będzie miało własną podsieć równorzędną do określonej sieci wirtualnej.

## <a name="shared-image-gallery"></a>Galeria obrazów udostępnionych

Udostępniona Galeria obrazów jest dołączona do konta laboratorium i służy jako centralne repozytorium do przechowywania obrazów. Obraz jest zapisywany w galerii, gdy nauczycieli wybierze eksport z maszyny wirtualnej szablonu laboratorium klasowego. Za każdym razem, gdy nauczycieli wprowadza zmiany w szablonie maszyny wirtualnej i eksportu, nowe wersje obrazu są zapisywane podczas zachowywania poprzednich wersji.

Instruktorzy mogą publikować wersję obrazu z galerii udostępnionych obrazów podczas tworzenia nowego laboratorium zajęć. Mimo że Galeria przechowuje wiele wersji obrazu, wykładowcy mogą wybrać tylko najnowszą wersję podczas tworzenia laboratorium.

Udostępniona Galeria obrazów jest opcjonalnym zasobem, który nie jest potrzebny do natychmiastowego rozpoczęcia pracy z kilkoma pracowniami. Jednak używanie galerii obrazów udostępnionych ma wiele korzyści, które są przydatne podczas skalowania do większej ilości laboratoriów:

- **Umożliwia zapisywanie wersji obrazu maszyny wirtualnej szablonu i zarządzanie nimi**

    Warto utworzyć obraz niestandardowy lub wprowadzić zmiany (oprogramowanie, konfiguracja itp.) do obrazu z galerii publicznej witryny Marketplace.  Na przykład typowe dla nauczycieli wymagają zainstalowania różnych software\tooling. Zamiast wymagać od uczniów ręcznego instalowania tych wymagań wstępnych, różne wersje obrazu maszyny wirtualnej mogą zostać wyeksportowane do galerii obrazów udostępnionych. Te wersje obrazu mogą być następnie używane podczas tworzenia nowych laboratoriów zajęć.
- **Włącza sharing\reuse szablonu obrazów maszyn wirtualnych w laboratoriach zajęć**

    Możesz zapisać i ponownie użyć obrazu, aby nie trzeba było konfigurować obrazu od podstaw za każdym razem, gdy tworzysz nowe laboratorium zajęć. Na przykład jeśli jest oferowanych wiele klas, które wymagają tego samego obrazu, ten obraz należy utworzyć tylko raz i wyeksportować do galerii obrazów udostępnionych, aby można go było udostępnić w laboratoriach z klasą.
- **Zapewnia dostępność obrazu za poorednictwem replikacji**

    Gdy zapisujesz do galerii obrazów udostępnionych z laboratorium zajęć, obraz jest automatycznie replikowany do innych [regionów w tej samej lokalizacji geograficznej](https://azure.microsoft.com/global-infrastructure/regions/). W przypadku awarii dla regionu, opublikowanie obrazu w laboratorium zajęć nie wpłynie na to, że można użyć repliki obrazu z innego regionu.  Publikowanie maszyn wirtualnych z wielu replik może być również pomocne w wydajności.

Aby logicznie grupować obrazy udostępnione, masz kilka opcji:

- Utwórz wiele udostępnionych galerii obrazów. Każde konto laboratorium może łączyć się tylko z jedną udostępnioną galerią obrazów, dlatego ta opcja będzie wymagała również utworzenia wielu kont laboratorium.
- Można też użyć jednej udostępnionej galerii obrazów, która jest współużytkowana przez wiele kont laboratorium. W takim przypadku każde konto laboratorium może włączyć tylko te obrazy, które mają zastosowanie do laboratoriów klasy, które zawiera.

## <a name="naming"></a>Nazewnictwo

Po rozpoczęciu pracy z Azure Lab Services zalecamy ustanowienie konwencji nazewnictwa dla grup zasobów, kont laboratorium, laboratoriów zajęć i galerii obrazów udostępnionych. Chociaż ustanowione konwencje nazewnictwa będą unikatowe dla potrzeb organizacji, w poniższej tabeli przedstawiono ogólne wytyczne.

| Typ zasobu | Rola | Sugerowany wzorzec | Przykłady |
| ------------- | ---- | ----------------- | -------- | 
| Grupa zasobów | Zawiera jedno lub więcej kont laboratorium i jedną lub więcej udostępnionych galerii obrazów | \<organization short name\>-\<environment\>-RG<ul><li>**Krótka nazwa organizacji** określa nazwę organizacji obsługiwaną przez grupę zasobów</li><li>**Środowisko** identyfikuje środowisko dla zasobu, na przykład pilotażowy lub produkcyjny</li><li>**RG** oznacza typ zasobu: Grupa zasobów.</li></ul> | contosouniversitylabs — RG<br/>contosouniversitylabs-pilotaż-RG<br/>contosouniversitylabs — prod-RG |
| Konto laboratorium | Zawiera co najmniej jedną Labs | \<organization short name\>-\<environment\>-La<ul><li>**Krótka nazwa organizacji** określa nazwę organizacji obsługiwaną przez grupę zasobów</li><li>**Środowisko** identyfikuje środowisko dla zasobu, na przykład pilotażowy lub produkcyjny</li><li>**La** oznacza typ zasobu: konto laboratorium.</li></ul> | contosouniversitylabs — La<br/>mathdeptlabs — La<br/>sciencedeptlabs-pilotaż-La<br/>sciencedeptlabs — prod-La |
| Laboratorium zajęć | Zawiera co najmniej jedną maszynę wirtualną |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>**Nazwa klasy** identyfikuje nazwę klasy obsługiwanej przez laboratorium.</li><li>**Przedział czasu** określa przedział czasu, w którym jest oferowana Klasa.</li>**Identyfikator edukacji** identyfikuje nauczycieli, który jest właścicielem laboratorium.</li></ul> | CS1234-fall2019-jankowalski<br/>CS1234-spring2019-jankowalski |
| Galeria obrazów udostępnionych | Zawiera co najmniej jedną wersję obrazu maszyny wirtualnej | \<organization short name\>zdjęć | contosouniversitylabsgallery |

Aby uzyskać więcej informacji na temat nazewnictwa innych zasobów platformy Azure, zobacz [konwencje nazewnictwa dla zasobów platformy Azure](/azure/architecture/best-practices/naming-conventions).

## <a name="regionslocations"></a>Regions\locations

Podczas konfigurowania zasobów Azure Lab Services "wymagane jest podanie regionu (lub lokalizacji) centrum danych, które będzie hostować zasób. Poniżej znajdują się szczegółowe informacje o tym, jak region ma wpływ na wszystkie zasoby, które są wykorzystywane podczas konfigurowania laboratorium.

### <a name="resource-group"></a>Grupa zasobów

Region określa centrum danych, w którym są przechowywane informacje o grupie zasobów. Zasoby platformy Azure zawarte w grupie zasobów mogą znajdować się w różnych regionach z ich poziomu nadrzędnego.

### <a name="lab-account"></a>Konto laboratorium

Lokalizacja konta laboratorium wskazuje region, w którym znajduje się ten zasób.  

### <a name="classroom-lab"></a>Laboratorium zajęć

Lokalizacja, w której znajduje się laboratorium klasy, różni się w zależności od następujących czynników:

  - **Konto laboratorium jest połączone z siecią wirtualną (VNet)**
  
    Konto laboratorium może być połączone [z siecią wirtualną](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) , gdy znajdują się w tym samym regionie.  Gdy konto laboratorium jest połączone z siecią wirtualną, laboratorium klasowe są tworzone automatycznie w tym samym regionie co konto laboratorium i Sieć wirtualna.

    > [!NOTE]
    > Gdy konto laboratorium jest połączone z siecią wirtualną, ustawienie Zezwalaj na wybór przez **twórcę laboratorium do wybrania lokalizacji laboratorium** jest wyłączone. Dodatkowe informacje na temat tego ustawienia można znaleźć w artykule: [Zezwalaj na twórcę laboratorium w celu wybrania lokalizacji dla laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location).
    
  - * * Żadna Sieć wirtualna nie jest w trybie komunikacji równorzędnej **_, a_* twórcy laboratorium nie mogą wybierać location_ laboratoryjnych *
  
    Gdy **nie** ma sieci wirtualnej z siecią wirtualną przy użyciu konta laboratorium, *a* [twórcy laboratorium **nie** mogą wybrać lokalizacji laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location), laboratoria klasy są automatycznie tworzone w regionie, który ma dostępną pojemność maszyny wirtualnej.  W Azure Lab Services szuka dostępności w [regionach, które znajdują się w tej samej lokalizacji geograficznej co konto laboratorium](https://azure.microsoft.com/global-infrastructure/regions).

  - * * Żadna Sieć wirtualna nie ma komunikacji równorzędnej **_, a_* twórcy laboratorium mogą wybrać laboratorium location_ *
       
    Jeśli nie ma **żadnej** komunikacji równorzędnej sieci wirtualnej, a [twórcy laboratorium nie mogą wybrać lokalizacji laboratorium](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location), lokalizacje, które mogą zostać wybrane przez twórcę laboratorium, są oparte na dostępnej pojemności.

> [!NOTE]
> Aby zapewnić wystarczającą pojemność maszyny wirtualnej dla regionu, ważne jest, aby najpierw zażądać pojemności za pomocą konta laboratorium lub podczas tworzenia laboratorium.

Ogólna zasada polega na ustawieniu regionu zasobu, który znajduje się najbliżej jego użytkowników. W przypadku laboratoriów stacjonarnych oznacza to utworzenie laboratorium klas najbliższego uczniom. W przypadku kursów online, w których studenci znajdują się na całym świecie, należy użyć najlepszych orzeczeń do utworzenia laboratorium klasy, które znajduje się centralnie. Lub Podziel klasę na wiele laboratoriów o klasie w oparciu o region uczniów.

### <a name="shared-image-gallery"></a>Galeria obrazów udostępnionych

Region wskazuje region źródłowy, w którym jest przechowywana pierwsza wersja obrazu przed automatyczną replikacją do regionów docelowych.

## <a name="vm-sizing"></a>Rozmiar maszyny wirtualnej

Gdy Administratorzy lub twórcy laboratorium tworzą laboratorium zajęć, mogą wybrać spośród następujących rozmiarów maszyn wirtualnych na podstawie potrzeb ich klasy. Należy pamiętać, że dostępne rozmiary obliczeń zależą od regionu, w którym znajduje się konto w laboratorium:

| Rozmiar | Specyfikacje | Seria | Sugerowane użycie |
| ---- | ----- | ------ | ------------- |
| Mały| <ul><li>2 rdzenie</li><li>3,5 GB PAMIĘCI RAM</li> | [Standardowa_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Ten rozmiar najlepiej nadaje się w przypadku wiersza polecenia, otwierania przeglądarki sieci Web, serwerów sieci Web o małym ruchu, małych i średnich baz danych. |
| Średniaa | <ul><li>4 rdzenie</li><li>7 GB PAMIĘCI RAM</li> | [Standardowa_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Ten rozmiar jest najlepiej dostosowany do relacyjnych baz danych, buforowania w pamięci i analizy. |
| Średni (Wirtualizacja zagnieżdżona) | <ul><li>4 rdzenie</li><li>16 GB PAMIĘCI RAM</li></ul> | [Standardowa_D4s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | Ten rozmiar jest najlepiej dostosowany do relacyjnych baz danych, buforowania w pamięci i analizy.
| Duży | <ul><li>8 rdzeni</li><li>16 GB PAMIĘCI RAM</li></ul>  | [Standardowa_A8_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | Ten rozmiar najlepiej nadaje się w przypadku aplikacji wymagających szybszych procesorów CPU, lepszej wydajności dysków lokalnych, dużych baz danych i dużych pamięci podręcznych pamięci.  Ten rozmiar obsługuje również wirtualizację zagnieżdżoną. |
| Duże (Wirtualizacja zagnieżdżona) | <ul><li>8 rdzeni</li><li>32 GB pamięci RAM</li></ul>  | [Standard_D8s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | Ten rozmiar najlepiej nadaje się w przypadku aplikacji wymagających szybszych procesorów CPU, lepszej wydajności dysków lokalnych, dużych baz danych i dużych pamięci podręcznych pamięci. |
| Mały procesor GPU (wizualizacja) | <ul><li>6 rdzeni</li><li>56 GB PAMIĘCI RAM</li>  | [Standardowa_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Ten rozmiar najlepiej nadaje się do zdalnej wizualizacji, przesyłania strumieniowego, gier, kodowania przy użyciu struktur, takich jak OpenGL i DirectX. |
| Mały procesor GPU (obliczenia) | <ul><li>6 rdzeni</li><li>56 GB PAMIĘCI RAM</li></ul>  | [Standardowa_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Ten rozmiar najlepiej nadaje się w przypadku aplikacji intensywnie korzystających z komputerów, takich jak sztuczna inteligencja i uczenie głębokie. |
| Średni procesor GPU (wizualizacja) | <ul><li>12 rdzeni</li><li>112 GB PAMIĘCI RAM</li></ul>  | [Standardowa_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Ten rozmiar najlepiej nadaje się do zdalnej wizualizacji, przesyłania strumieniowego, gier, kodowania przy użyciu struktur, takich jak OpenGL i DirectX. |

## <a name="manage-identity"></a>Zarządzanie tożsamością

Za pomocą [kontroli dostępu opartej na rolach (Azure RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)można przypisać następujące role, aby zapewnić dostęp do kont laboratorium i laboratoriów zajęć:

- **Właściciel konta laboratorium**

    Administrator tworzący konto laboratorium jest automatycznie dodawany do roli **właściciela** konta laboratorium.  Administrator, który ma przypisaną rolę **właściciela** , może:
     - Zmień ustawienia konta laboratorium.
     - Nadaj innym administratorom dostęp do konta laboratorium jako właściciele lub Współautorzy.
     - Nadaj nauczycielom dostęp do laboratoriów stacjonarnych jako twórców, właścicieli lub współautorów.
     - Twórz wszystkie Pracowniki klas w ramach konta laboratorium i zarządzaj nimi.

- **Współautor konta laboratorium**

    Administrator, do którego przypisano rolę **współautor** , może:
    - Zmień ustawienia konta laboratorium.
    - Twórz wszystkie Pracowniki klas w ramach konta laboratorium i zarządzaj nimi.

    *Nie mogą* jednak dać innym użytkownikom dostępu do kont laboratorium lub laboratoriów zajęć.

- **Twórca laboratorium klasy**

    Aby utworzyć laboratoria zajęć na koncie laboratorium, nauczycieli musi być członkiem roli **twórcy laboratorium** .  Gdy nauczycieli tworzy laboratorium zajęć, są one automatycznie dodawane jako właściciel laboratorium.  Zapoznaj się z samouczkiem dotyczącym [dodawania użytkownika do roli **twórca laboratorium** ](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role). 

- **Owner\contributor Lab**
  
    Nauczycieli może wyświetlać i zmieniać ustawienia laboratorium zajęć, gdy są członkami roli **właściciela** lub **współautora** laboratorium; muszą być również członkami roli **czytelnik** konta laboratorium.

    Kluczowa różnica między rolami **właściciela** i **współautora** laboratorium polega na tym, że współautor *nie może* przyznać innym użytkownikom dostępu do zarządzania laboratorium.

    Ponadto nauczycieli *nie może* tworzyć nowych laboratoriów klasy, chyba że są one również członkami roli **twórca laboratorium** .

- **Galeria obrazów udostępnionych**

    Po dołączeniu udostępnionej galerii obrazów do konta laboratorium, konto Lab owners\contributors i Lab creators\owners\contributors są automatycznie udzielane dostępu do wyświetlania i zapisywania obrazów w galerii.

Oto kilka porad ułatwiających Przypisywanie ról:
   - Zazwyczaj tylko administratorzy powinni być członkami ról **właściciela** konta laboratorium lub **współautora** . może być więcej niż jeden owner\contributor.
   - Aby dać nauczycieli możliwość tworzenia nowych laboratoriów zajęć i zarządzania przez nich laboratoriami, wystarczy przypisać dostęp do roli **twórca laboratorium** .
   - Aby dać nauczycieli możliwość zarządzania określonymi laboratoriami, ale *nie* ma możliwości tworzenia nowych laboratoriów; należy przypisać dostęp do roli **właściciel** lub **współautor** dla każdej z laboratoriów klasy, którymi będą zarządzać.  Na przykład możesz chcieć zezwolić zarówno profesor, jak i Asystentowi nauczania na Współtworzenie laboratorium klasy.  Zapoznaj się z przewodnikiem, jak [dodać użytkownika jako właściciela do laboratorium zajęć](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner).

## <a name="pricing"></a>Cennik

### <a name="azure-lab-services"></a>Azure Lab Services

Cennik Azure Lab Services został opisany w następującym artykule: [Azure Lab Services Cennik](https://azure.microsoft.com/pricing/details/lab-services/).

Należy również wziąć pod uwagę ceny udostępnionej galerii obrazów, jeśli planujesz jej używać do przechowywania wersji obrazów i zarządzania nimi. 

### <a name="shared-image-gallery"></a>Galeria obrazów udostępnionych

Tworzenie galerii obrazów udostępnionych i dołączanie jej do konta laboratorium jest bezpłatne. Koszty nie są naliczane, dopóki nie zapiszesz wersji obrazu w galerii. Zazwyczaj ceny za używanie udostępnionej galerii obrazów są dość nieznaczne, ale ważne jest, aby zrozumieć, jak jest ona obliczana, ponieważ nie jest uwzględniona w cenach Azure Lab Services.  

#### <a name="storage-charges"></a>Opłaty za magazyn

Aby przechowywać wersje obrazów, w galerii obrazów udostępnionych są używane standardowe dyski twarde dysk twardy. Rozmiar używanego dysku zarządzanego przez dysk twardy zależy od rozmiaru przechowywanego obrazu. Zapoznaj się z następującym artykułem, aby wyświetlić Cennik: [Zarządzanie dyskami zarządzanymi](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>Opłaty za replikację i ruch wychodzący z sieci

Gdy zapisujesz wersję obrazu przy użyciu maszyny wirtualnej szablonu laboratorium klasy, Azure Lab Services najpierw zapisuje ją w regionie źródłowym, a następnie automatycznie replikuje wersję obrazu źródłowego do co najmniej jednego regionu docelowego. Należy pamiętać, że Azure Lab Services automatycznie replikuje wersję obrazu źródłowego do wszystkich [regionów docelowych w obszarze geograficznym](https://azure.microsoft.com/global-infrastructure/regions/) , w którym znajduje się laboratorium klasowe. Na przykład jeśli laboratorium zajęć znajduje się w regionie geograficznym USA, wersja obrazu jest replikowana do każdego z ośmiu regionów istniejących w Stanach Zjednoczonych

Opłata za ruch wychodzący sieci występuje, gdy wersja obrazu jest replikowana z regionu źródłowego do dodatkowych regionów docelowych. Opłata jest naliczana na podstawie rozmiaru wersji obrazu, gdy dane obrazu są początkowo przenoszone z regionu źródłowego.  Aby uzyskać szczegółowe informacje o cenach, zapoznaj się z następującym artykułem: [szczegóły cennika dotyczącego przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/).

Klienci [rozwiązań edukacyjnych](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) mogą zrezygnować z płacenia opłat wychodzących. Aby dowiedzieć się więcej, skontaktuj się z menedżerem konta.  Aby uzyskać więcej informacji, zobacz sekcję **często zadawanych pytań** w połączonym dokumencie, w tym pytanie "Jakie programy transferu danych istnieją dla klientów akademickich I jak mam zakwalifikować?".

#### <a name="pricing-example"></a>Przykład cen

Aby podsumowanie opisane powyżej ceny, przyjrzyjmy się przykładowi zapisywania obrazu maszyny wirtualnej z szablonami w galerii obrazów udostępnionych. Założono następujące scenariusze:

- Masz jeden niestandardowy obraz maszyny wirtualnej.
- Zapisujesz dwie wersje obrazu.
- Twoje laboratorium znajduje się w Stanach Zjednoczonych, które zawierają łącznie osiem regionów.
- Każda wersja obrazu ma rozmiar 32 GB; w związku z tym cena dysku zarządzanego przez dysk twardy wynosi $1,54 miesięcznie.

Łączny koszt jest szacowany jako:

Liczba obrazów × liczba wersji × liczba replik × cena dysku zarządzanego

W tym przykładzie kosztem jest:

1 obraz niestandardowy (32 GB) x 2 wersje x 8 USA regiony x $1,54 = $24,64 miesięcznie

#### <a name="cost-management"></a>Zarządzanie kosztami

Ważne jest, aby administrator konta laboratorium mógł zarządzać kosztami przez zwykłe usuwanie niepotrzebnych wersji obrazu z galerii. 

Nie należy usuwać replikacji do określonych regionów w celu zmniejszenia kosztów (Ta opcja istnieje w galerii obrazów udostępnionych). Zmiany replikacji mogą mieć niekorzystny wpływ na możliwość publikowania maszyn wirtualnych przez usługę Azure Lab z obrazów zapisanych w galerii obrazów udostępnionych.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z samouczkiem, aby uzyskać instrukcje krok po kroku dotyczące tworzenia konta laboratorium i laboratorium: [Konfigurowanie przewodnika](tutorial-setup-lab-account.md)
