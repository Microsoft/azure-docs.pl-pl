---
title: 'Scenariusz: niestandardowa izolacja sieci wirtualnych i gałęzi'
titleSuffix: Azure Virtual WAN
description: Scenariusze routingu — Zapobiegaj dostępowi wybranych sieci wirtualnych i gałęzi do siebie nawzajem
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: wellee
ms.openlocfilehash: e8e5a5a1b9325f40fdd51133155a0daffaa55a7b
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99408497"
---
# <a name="scenario-custom-isolation-for-virtual-networks-and-branches"></a>Scenariusz: niestandardowa izolacja sieci wirtualnych i gałęzi

Podczas pracy z routingiem wirtualnego koncentratora sieci WAN jest dość kilka dostępnych scenariuszy. W niestandardowym scenariuszu izolacji zarówno dla sieci wirtualnych (sieci wirtualnych), jak i gałęzi, celem jest uniknięcie określonego zestawu sieci wirtualnych z dotarciem do innego zestawu sieci wirtualnych. Podobnie gałęzie (VPN/ER/User VPN) mogą uzyskać dostęp tylko do określonych zestawów sieci wirtualnych.

Wprowadzamy również dodatkowe wymagania polegające na tym, że Zapora platformy Azure powinna sprawdzić gałąź do sieci wirtualnej i rozgałęzić do ruchu sieciowego, ale **nie**  do ruchu w sieci wirtualnej.  

Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Projekt

Aby ustalić, ile tabel tras będzie potrzebnych, można utworzyć macierz łączności. W tym scenariuszu będzie wyglądać podobnie do poniższego, gdzie każda komórka reprezentuje, czy źródło (wiersz) może komunikować się z miejscem docelowym (kolumna):

| Źródło | Do:| *Niebieska sieci wirtualnych* | *Red sieci wirtualnych* | *Czerwone gałęzie*| *Niebieskie gałęzie*| 
|---|---|---|---|---|---|
| **Niebieska sieci wirtualnych** |   &#8594;|   Direct     |           |   |  AzFW|
| **Red sieci wirtualnych**  |   &#8594;|              |   Direct  |  AzFW  | 
| **Czerwone gałęzie**   |   &#8594;|   |   AzFW  |  Direct | Direct
| **Niebieskie gałęzie**| &#8594;| AzFW  |   |Direct   | Direct

Każda z komórek w poprzedniej tabeli zawiera opis, czy wirtualne połączenie sieci WAN ("od", po stronie przepływu, nagłówki wierszy) komunikuje się z miejscem docelowym (po stronie "do" przepływu, nagłówki kolumn w kursywie). **Bezpośrednie** oznacza przepływ ruchu bezpośrednio za pośrednictwem wirtualnej sieci WAN, podczas gdy **AzFW** oznacza, że ruch jest sprawdzany przez zaporę platformy Azure przed przekazaniem do miejsca docelowego. Pusty wpis oznacza, że przepływ jest blokowany w konfiguracji.

W takim przypadku dwie tabele tras dla sieci wirtualnych są wymagane do osiągnięcia celu izolacji sieci wirtualnej bez zapory platformy Azure w ścieżce. Te tabele tras będą wywoływać **RT_BLUE** i **RT_RED**.

Ponadto gałęzie muszą być zawsze skojarzone z  **domyślną** tabelą tras. Aby zapewnić, że ruch do i z gałęzi jest sprawdzany przez zaporę platformy Azure, dodajemy trasy statyczne w **domyślnej** **RT_RED** i **RT_BLUE** tabele tras wskazujące ruch do zapory platformy Azure i skonfiguruj reguły sieciowe w celu zezwalania na żądany ruch. Gwarantujemy również, że gałęzie **nie** są propagowane do **RT_BLUE** i **RT_RED**.

W związku z tym jest to ostateczny projekt:

* Niebieskie sieci wirtualne:
  * Skojarzona tabela tras: **RT_BLUE**
  * Propagowanie do tabel tras: **RT_BLUE**
* Czerwone sieci wirtualne:
  * Skojarzona tabela tras: **RT_RED**
  * Propagowanie do tabel tras: **RT_RED** 
* Gałęzi
  * Skojarzona tabela tras: **Domyślna**
  * Propagowanie do tabel tras: **domyślne**
* Trasy statyczne:
    * **Domyślna tabela tras**: Virtual Network miejscami do adresów przy użyciu zapory platformy Azure w następnym przeskoku
    * **RT_RED**: 0.0.0.0/0 za pomocą usługi Azure Next skoku
    * **RT_BLUE**: 0.0.0.0/0 za pomocą usługi Azure Next skoku
* Reguły sieci zapory:
    * **Zezwalaj na** **prefiks źródła** reguły: **prefiks adresu docelowego** dla niebieskich adresów gałęzi: niebieskie prefiksy sieci wirtualnej 
    * **Zezwalaj na**  **prefiks źródła** reguły: czerwony prefiks adresów gałęzi adres **docelowy**: Red prefiksy sieci wirtualnej

> [!NOTE]
> Ponieważ wszystkie gałęzie muszą być skojarzone z domyślną tabelą tras, a także do propagowania do tego samego zestawu tabel routingu, wszystkie gałęzie będą miały ten sam profil łączności. Innymi słowy, nie można zastosować koncepcji czerwona/niebieska dla sieci wirtualnych do gałęzi. Aby jednak uzyskać niestandardowe Routing dla gałęzi, można przekazywać ruch z gałęzi do zapory platformy Azure.

> [!NOTE]
> Zapora platformy Azure domyślnie odmówi ruch w modelu zerowym zaufania. Jeśli nie ma jawnej reguły **zezwalania** zgodnej z sprawdzanym pakietem, Zapora platformy Azure porzuci pakiet.

Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).



## <a name="workflow"></a><a name="architecture"></a>Utworzonego

Na **rysunku 1** znajdują się niebieskie i czerwone sieci wirtualnych oraz gałęzie, które mogą uzyskać dostęp do niebieskich lub czerwonych sieci wirtualnych.

* Sieci wirtualnych połączone z niebieską siecią mogą dotrzeć do siebie nawzajem i mogą dotrzeć do wszystkich połączeń Blue Branch (VPN/ER/P2S). Na diagramie niebieską gałęzią jest lokacja sieci VPN typu lokacja-lokacja.
* Sieci wirtualnych z czerwonymi połączeniami mogą się komunikować nawzajem i mogą dotrzeć do wszystkich połączeń Red Branch (VPN/ER/P2S). Na diagramie czerwona gałąź to użytkownicy sieci VPN typu punkt-lokacja.

Podczas konfigurowania routingu należy wziąć pod uwagę następujące czynności.

1. Utwórz dwie niestandardowe tabele tras w Azure Portal, **RT_BLUE** i **RT_RED** w celu dostosowania ruchu do tych sieci wirtualnych.
2. W przypadku tabeli tras **RT_BLUE** Zastosuj następujące ustawienia, aby upewnić się, że na niebiesko sieci wirtualnych poznasz prefiksy adresów wszystkich innych niebieskich sieci wirtualnych.:
   * **Skojarzenie**: zaznacz wszystkie niebieskie sieci wirtualnych.
   * **Propagacja**: zaznacz wszystkie niebieskie sieci wirtualnych.
3. Powtórz te same czynności dla **RT_REDj** tabeli tras dla Red sieci wirtualnych.
4. Udostępnianie zapory platformy Azure w wirtualnej sieci WAN. Aby uzyskać więcej informacji na temat zapory platformy Azure w wirtualnym koncentratorze sieci WAN, zobacz [Konfigurowanie zapory platformy Azure w wirtualnym koncentratorze sieci WAN](howto-firewall.md).
5. Dodanie trasy statycznej do **domyślnej** tabeli tras koncentratora wirtualnego powoduje kierowanie całego ruchu przeznaczonego dla przestrzeni adresowych sieci wirtualnej (niebieski i czerwony) do zapory platformy Azure. Ten krok zapewnia, że wszystkie pakiety z gałęzi zostaną wysłane do zapory platformy Azure w celu przeprowadzenia inspekcji.
    * Przykład: **prefiks docelowy**: 10.0.0.0/24 **Następny przeskok**: Zapora platformy Azure
    >[!NOTE]
    > Ten krok można również wykonać za pomocą Menedżera zapory, wybierając opcję "bezpieczny ruch prywatny". Spowoduje to dodanie trasy dla wszystkich prywatnych adresów IP RFC1918, które mają zastosowanie do sieci wirtualnych i rozgałęzień. Musisz ręcznie dodać w dowolnych gałęziach lub sieciach wirtualnych, które nie są zgodne z RFC1918. 

6. Dodaj trasę statyczną do **RT_RED** i **RT_BLUE** skierować cały ruch do zapory platformy Azure. Ten krok zapewnia, że sieci wirtualnych nie będzie mógł bezpośrednio uzyskiwać dostępu do gałęzi. Tego kroku nie można wykonać za pomocą Menedżera zapory, ponieważ te sieci wirtualne nie są skojarzone z domyślną tabelą tras.
    * Przykład: **prefiks docelowy**: 0.0.0.0/0 **Następny przeskok**: Zapora platformy Azure

    > [!NOTE]
    > Routing jest wykonywany przy użyciu najdłuższych pasujących do prefiksów (LPM). W związku z tym trasy statyczne 0.0.0.0/0 **nie** będą preferowane w porównaniu z dokładnymi prefiksami, które istnieją w **BLUE_RT** i **RED_RT**. W związku z tym ruch wewnątrz sieci wirtualnej nie będzie sprawdzany przez zaporę platformy Azure.

Spowoduje to zmianę konfiguracji routingu, jak pokazano na poniższej ilustracji.

**Rysunek 1** 
 [ ![ Rysunek 1 ](./media/routing-scenarios/custom-branch-vnet/custom-branch.png)](./media/routing-scenarios/custom-branch-vnet/custom-branch.png#lightbox)

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).
* Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
