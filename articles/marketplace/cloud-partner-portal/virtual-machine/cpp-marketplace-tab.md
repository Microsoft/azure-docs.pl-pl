---
title: Karta Marketplace maszyny wirtualnej w portal Cloud Partner portalu Azure Marketplace
description: Opisuje kartę Marketplace używaną podczas tworzenia oferty maszyny wirtualnej portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 2c3d316d0d2810cb2a25ffd3bc4e34cf3454c10d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146854"
---
# <a name="virtual-machine-marketplace-tab"></a>Karta Marketplace maszyny wirtualnej

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami usługi Azure Virtual Machine w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie oferty maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) w celu zarządzania zmigrowanymi ofertami.

Na karcie **Marketplace** na stronie **Nowa oferta** można zapewnić potencjalnym klientom potencjalną obsługę marketingową, sprzedażową i informacje prawne oraz umowy oraz zarządzać klientami wygenerowanymi w portalu Marketplace. Ten długi formularz jest podzielony na cztery sekcje: **Omówienie**, **artefakty marketingowe**, **Zarządzanie potencjalnymi klientami**i warunki **prawne**.


## <a name="overview-section"></a>Sekcja Omówienie
W tej sekcji wprowadzasz ogólne informacje o ofercie portalu Azure Marketplace.  Dołączona gwiazdka (*) w nazwie pola wskazuje, że jest to wymagane.

![Sekcja Przegląd karty Marketplace dla maszyn wirtualnych](./media/publishvm_008.png)

W poniższej tabeli opisano przeznaczenie i zawartość tych pól. Wymagane pola są indicted przez gwiazdkę (*).

|  **Pole**                |     **Opis**                                                          |
|  ---------                |     ---------------                                                          |
| **Tytuły\***                 | Tytuł oferty, często długi, formalna nazwa. Ten tytuł zostanie wyświetlony w widocznym miejscu w portalu Marketplace.  Maksymalna długość 50 znaków. |
| **Podsumowanie\***               | Krótki cel lub funkcja rozwiązania.  Maksymalna długość 100 znaków. |
| **Długie podsumowanie\***          | Cel lub funkcja rozwiązania.  Maksymalna długość 256 znaków. |
| **Opis\***           | Opis rozwiązania.  Maksymalna długość 3000 znaków, obsługuje proste formatowanie HTML. |
| **Kanał odsprzedawcy dostawcy Microsoft CSP\*** | Niedostępność kanału partnera dostawcy rozwiązań w chmurze (CSP) jest teraz dostępna.  Zobacz [dostawcy rozwiązań w chmurze](../../cloud-solution-providers.md) , aby uzyskać więcej informacji na temat marketingu oferty przez kanały partnerskie programu Microsoft CSP. |
| **Identyfikator marketingu\***  | Unikatowy adres URL do skojarzenia z ofertą zwykle zawiera nazwę organizacji i rozwiązania, maksymalną długość 50 znaków.  Przykład: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **Podgląd identyfikatorów subskrypcji\*** | Dodaj jeden do 100 identyfikatorów subskrypcji dla recenzentów. Te białe subskrypcje będą miały dostęp do oferty po jej opublikowaniu. |
| **Przydatne linki**          | Dodaj adresy URL do dokumentów, informacji o wersji, często zadawanych pytań i tak dalej. |
| **Sugerowane kategorie\*** | Wybierz maksymalnie dwie kategorie (2), w tym podstawową i pomocniczą kategorię (opcjonalnie). Wybierz do dwóch (2) podkategorii dla każdej kategorii podstawowej i/lub pomocniczej. Jeśli nie wybrano żadnej podkategorii, oferta nadal będzie wykrywalna tylko w wybranej kategorii. |
|  |  |


## <a name="marketing-artifacts-section"></a>Sekcja artefaktów marketingowych

Druga sekcja jest podzielona na trzy podsekcje: **logo**, **zrzut ekranu**i **wideo**. Logo są jedynymi wymaganymi artefaktami marketingowymi, ale wszystkie są zdecydowanie zalecane w przypadku najlepszych odwołań klientów. 

![Sekcja dotycząca artefaktów marketingowych na karcie Marketplace w nowej postaci dla maszyn wirtualnych](./media/publishvm_009.png)

W poniższej tabeli opisano przeznaczenie i zawartość tych pól. Wymagane pola są indicted przez gwiazdkę (*).

|  **Pole**                |     **Opis**                                                          |
|  ---------                |     ---------------                                                          |
| *Graficznych*  |  |
| **Small\***                 | Mapa bitowa 40x40 pikseli. ico                                                      |
| **Medium\***                | Mapa bitowa 90x90 pikseli. ico                                                      |
| **Large\***                 | Mapa bitowa 115x115 pikseli. ico                                                   |
| **Szerokooć\***                  | Mapa bitowa 255x115 pikseli. ico                                                    |
| **Hero**                  | Mapa bitowa 815x290.  Opcjonalne, ale nie można usunąć ikony Hero. |
| *Zrzuty ekranu*  | Opcjonalne, ale maksymalnie pięć zrzutów ekranu na jednostkę SKU. |
| **Nazwa**                  | Nazwa lub tytuł <!-- TODO - max char length? none specified in UI -->                               |
| **Obraz**                 | Obraz przechwytywania ekranu, 533x324 piksel                                         |
| *Filmy wideo*  |  |
| **Nazwa**                  | Nazwa lub tytuł  <!-- TODO - max char length? -->                              |
| **Łącze**                  | Adres URL wideo, hostowany w serwisie YouTube lub Vimeo                                        |
| **Miniaturk**             | Mapa bitowa 533x324                                                               |
|   |   |

### <a name="logo-guidelines"></a>Wytyczne dotyczące logo

<!-- TD: It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Wszystkie logo przekazane do portal Cloud Partner powinny postępować zgodnie z wytycznymi:

*  Projekt platformy Azure ma prostą paletę kolorów. Zachowaj niewielką liczbę kolorów podstawowych i pomocniczych w logo.
*  Kolory motywu Azure Portal są białe i czarne. W związku z tym Unikaj używania tych kolorów jako koloru tła logo. Użyj pewnego koloru, który sprawia, że logo będzie widoczne w Azure Portal. Zalecamy proste kolory podstawowe. Jeśli używasz przezroczystego tła, upewnij się, że logo/tekst nie są białe lub czarne lub niebieskie.
*  Nie używaj tła gradientu w logo.
*  Unikaj umieszczania tekstu — nawet nazwy firmy lub marki — w logo. Wygląd i zachowanie logo powinny być "płaskie" i nie powinno się unikać gradientów.
*  Nie rozciągaj logo.

#### <a name="hero-logo"></a>Logo Duży obraz

Logo Hero jest opcjonalne; Po przekazaniu nie można usunąć ikony Hero.  Ikona logo Hero powinna być zgodna z wytycznymi:

*  Czarne, białe i przezroczyste tła nie są dozwolone dla ikon Hero.
*  Należy unikać używania dowolnego koloru jasnego jako tła ikony Hero.  Nazwa wyświetlana wydawcy, tytuł planu i długi podsumowanie oferty są wyświetlane w białym kolorze czcionki i muszą zostać wystawione w tle.
*  Unikaj używania większości tekstu podczas projektowania logo Hero.  Nazwa wydawcy, tytuł planu, podsumowanie oferty długiej i przycisk Utwórz są osadzane programowo wewnątrz ikony Hero, gdy lista oferty. 
* Dołącz nieużywany prostokąt po prawej stronie ikony Hero o rozmiarze 415x100 pikseli i przesunięciu 370 pikseli z lewej strony.  

Na przykład następująca ikona Hero jest dla Azure Container Service.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Przykładowa ikona Hero dla Azure Container Service](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Przykład informacji marketingowych 

Na poniższej ilustracji przedstawiono sposób wyświetlania informacji marketingowych na stronie głównej produktu Microsoft Windows Server.

![Przykładowa strona produktu dla systemu Microsoft Windows Server](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Sekcja zarządzanie potencjalnymi klientami

Trzecia sekcja umożliwia zbieranie potencjalnych klientów wygenerowanych z ofert portalu Azure Marketplace. Oferuje on następujące opcje magazynu (z listy rozwijanej) dla informacji o potencjalnych klientach.

* **Brak** — nie są zbierane informacje o kliencie domyślnym.
* Tabela Azure — zapisywana w tabeli platformy Azure określona przez parametry połączenia.
* Dynamics CRM Online — Zapisano w wystąpieniu usługi [Microsoft Dynamics 365 online](https://dynamics.microsoft.com/) , określonym przez adres URL i poświadczenia uwierzytelniania.
* Punkt końcowy HTTPS — zapisany w określonym punkcie końcowym HTTPS jako ładunek JSON.
* Program Market — zapisany w określonym wystąpieniu programu [Marketo](https://www.marketo.com/) , określony przez identyfikator serwera, identyfikator Munchkin i identyfikator formularza.
* Salesforce — zapisywana w bazie danych [usług Salesforce](https://www.salesforce.com/) , określona przez identyfikator obiektu.

Po pomyślnym opublikowaniu oferty połączenie z liderem zostanie sprawdzone i klient testowy zostanie automatycznie wysłany do skonfigurowanego miejsca docelowego. Informacje o potencjalnych klientach powinny być ciągle zarządzane, a te ustawienia należy natychmiast zaktualizować, gdy zostaną wprowadzone zmiany w architekturze zarządzania klientami.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Sekcja prawna

W tej ostatniej sekcji można podać wymagane dokumenty prawne wymagane dla każdej oferty.  

|  **Pole**                    |     **Opis**                                        |
|  ---------                    |     ---------------                                        |
| **Adres URL zasad ochrony prywatności\***      | Adres URL dla opublikowanych zasad ochrony prywatności                          |
| **Używać standardowej umowy?\***  |   |
| **Warunki użytkowania\***            | zasady jako zwykły tekst lub prosty kod HTML.                       |
|  |  |


## <a name="next-steps"></a>Następne kroki

Na następnej karcie [Pomoc techniczna](./cpp-support-tab.md) zapewnisz klientom pomoc techniczną dla Twojej oferty.
