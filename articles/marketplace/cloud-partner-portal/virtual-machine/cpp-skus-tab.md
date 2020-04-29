---
title: Karta jednostki SKU maszyny wirtualnej w portal Cloud Partner portalu Azure Marketplace
description: Opisuje kartę jednostki SKU używane podczas tworzenia oferty maszyny wirtualnej w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 6ca2ebf9d02c43c3fe6bb3abec6bc0d5815fac74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143012"
---
# <a name="virtual-machine-skus-tab"></a>Karta jednostki SKU maszyny wirtualnej

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami usługi Azure Virtual Machine w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie oferty maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) w celu zarządzania zmigrowanymi ofertami.

Karta **jednostki SKU** nowej strony **oferty** umożliwia utworzenie co najmniej jednej jednostki SKU i skojarzenie ich z nową ofertą.  Różne jednostki SKU mogą odróżnić rozwiązanie przez zestawy funkcji, typy obrazów maszyn wirtualnych, przepływność lub skalowalność, modele rozliczeń lub inne cechy.


## <a name="create-a-sku"></a>Tworzenie jednostki SKU

Początkowo Nowa oferta nie będzie miała skojarzonych jednostek SKU, więc utworzysz ją, klikając pozycję **Nowa jednostka SKU**.

![Przycisk nowej jednostki SKU na karcie Nowa oferta dla maszyn wirtualnych](./media/publishvm_005.png)

<br/>

Zostanie wyświetlone okno dialogowe **Nowa jednostka SKU** .  Wprowadź identyfikator nowej jednostki SKU, a następnie kliknij przycisk **OK**. (Zobacz poniżej, aby poznać konwencje nazewnictwa identyfikatorów).  Na karcie **jednostki SKU** zostaną wyświetlone pola dostępne do edycji.    Dołączona gwiazdka (*) w nazwie pola wskazuje, że jest to wymagane.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![Karta SKU w formularzu nowej oferty dla maszyn wirtualnych](./media/publishvm_006.png)

W poniższej tabeli opisano przeznaczenie, zawartość i formatowanie tych pól.  Wymagane pola są indicted przez gwiazdkę (*).

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **Pole**       |     **Opis**                                                          |
|  ---------       |     ---------------                                                          |
|  *Ustawienia jednostki SKU*   |    |
| **IDENTYFIKATOR JEDNOSTKI SKU\***       | Identyfikator dla tej jednostki SKU.  Ta nazwa ma maksymalnie 50 znaków składających się z małych znaków alfanumerycznych lub kresek (-), ale nie może kończyć się kreską.  Nie można go modyfikować po opublikowaniu oferty.  |
|  *Szczegóły jednostki SKU*   |  |
| **Tytuły\***        | Przyjazna nazwa oferty do wyświetlania w portalu Marketplace. Maksymalna długość 50 znaków. |
| **Podsumowanie\***      | Zwięzły opis oferty do wyświetlania w portalu Marketplace. Maksymalna długość 100 znaków. |
| **Opis\***  | Tekst opisu, który zawiera bardziej szczegółowy opis oferty.  <!-- TD: max len/guidance? 3k characters -->  |
| **Ukryj tę jednostkę SKU\*** | Wskazuje, czy jednostka SKU powinna być widoczna w portalu Marketplace dla klientów.  Możesz chcieć ukryć jednostkę SKU, jeśli chcesz, aby była dostępna tylko za pośrednictwem szablonów rozwiązań, a nie do zakupu osobno.  Może to również być przydatne podczas wstępnego testowania lub dla ofert tymczasowych lub sezonowych. |
| **Dostępność w chmurze\*** | Określa chmurę, w której powinna być dostępna jednostka SKU.  Wartość domyślna to publiczna wersja platformy Azure.  Microsoft Azure Government jest chmurą dla instytucji rządowych, która ma kontrolowany dostęp do instytucji rządowych Stanów Zjednoczonych, stanowych, lokalnych lub plemienne oraz ich certyfikowanych partnerów.  Aby uzyskać więcej informacji o chmurze dla instytucji rządowych, zobacz [Zapraszamy do Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome). |
| **Czy jest to prywatna jednostka SKU?\*** | Wskazuje, czy jednostka SKU jest prywatna, czy publiczna. Wartość domyślna to **no** (Public).  Aby uzyskać więcej informacji, zobacz [publiczne i prywatne jednostki SKU](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md). |
| **Dostępność kraju/regionu\*** | Określa kraje lub regiony świata, które będą dostępne do zakupu. Wybierz co najmniej jeden region/kraj. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *Cennik*   |  |
| **Model licencji\***| Standardowy model rozliczeń do użycia.  W przypadku wybrania **comiesięcznego rozliczanej jednostki SKU opartej na użyciu**zostanie otwarta sekcja zgodnie z informacjami o cenach na poziomie podstawowym oraz o tym, czy ma być oferowany bezpłatny okres próbny.  Ta sekcja umożliwia również eksportowanie i importowanie tego harmonogramu cen do programu Excel. Aby uzyskać więcej informacji, zobacz [Opcje rozliczeń w portalu Azure Marketplace](../../billing-options-azure-marketplace.md). | 
|  *Obrazy maszyn wirtualnych*   |  |
| **Rodzina systemów operacyjnych\*** | Wskazuje, czy maszyna wirtualna jest oparta na systemie Windows, czy Linux. |
| **Wybierz typ systemu operacyjnego** | Określony dostawca lub wydanie określonego systemu operacyjnego. |
| **Przyjazna nazwa systemu operacyjnego\*** | Nazwa systemu operacyjnego, który ma być wyświetlany klientom.  |
| **Zalecane rozmiary maszyn wirtualnych\*** | Umożliwia wybór maksymalnie sześciu zalecanych rozmiarów maszyn wirtualnych z listy ustandaryzowanej.  Ta lista jest przenoszona do Azure Portal i Microsoft Marketplace.  Pierwszy rozmiar maszyny wirtualnej na tej liście jest prawidłowy (dla tej subskrypcji klienta, regionu, strefy itp.) jest ustawiony jako domyślny dla tego potencjalnego klienta.  Użytkownik może zmienić ten rozmiar na dowolny zgodny z obrazem rozwiązania. | 
| **Otwarte porty**| Porty do otwarcia i protokołu do obsługi jednostki SKU.  Te konfiguracje muszą być zgodne z siecią wirtualną skonfigurowaną dla sieci maszyny wirtualnej rozwiązania. Te ustawienia są stosowane podczas wdrażania maszyny wirtualnej. Ustawienia portów można jednak modyfikować po opublikowaniu jednostki SKU. Aby uzyskać więcej informacji, zobacz [Jak otwierać porty do maszyny wirtualnej z portalu Azure](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal). <br/>Następujące domyślne mapowania sieci są dodawane do wszystkich maszyn wirtualnych. &emsp;Windows: 3389-> 3389 TCP, 5986-> 5986 TCP; &emsp; Linux: 22 — > 22, TCP (SSH). |
| **Wersja dysku**  | Skojarzona maszyna wirtualna rozwiązania określona przez numer wersji dysku i adres URL dysku. Wersja dysku musi mieć format [wersji semantycznej](https://semver.org/) : `<major>.<minor>.<patch>`.  Adres URL to identyfikator URI sygnatury dostępu współdzielonego utworzony dla wirtualnego dysku twardego systemu operacyjnego.  Mimo że można dodać do ośmiu wersji dysku na jednostkę SKU, tylko najwyższy numer wersji dysku dla jednostki SKU będzie widoczny w witrynie Azure Marketplace. Pozostałe wersje będą widoczne tylko za pośrednictwem interfejsów API.  <!--TD: Add more specific link to API --> <br/> Sekcja **nowe miejsce na dysku z danymi** umożliwia dołączenie do maszyny wirtualnej maksymalnie 15 dysków danych.  Ta konfiguracja nie może być modyfikowana po opublikowaniu jednostki SKU z daną wersją maszyny wirtualnej i skojarzonymi dyskami z danymi.  Jeśli dodatkowe wersje maszyn wirtualnych zostaną dodane do jednostki SKU, muszą one również obsługiwać tę samą liczbę dysków danych. <br/> Jeśli nie utworzono obrazów maszyn wirtualnych opartych na platformie Azure, możesz dodać to pole później.  Aby uzyskać informacje na temat tworzenia skojarzonego zasobu maszyny wirtualnej, zobacz sekcję [Tworzenie zasobów technicznych maszyn wirtualnych](./cpp-create-technical-assets.md).  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> Kliknij przycisk **Zapisz** , aby zapisać postęp. Na następnej karcie należy określić, czy oferta obsługuje [dysk testowy](./cpp-test-drive-tab.md).


## <a name="additional-pricing-considerations"></a>Dodatkowe zagadnienia dotyczące cen

Opisany powyżej model cen jest podstawowym opisem.  Zmiany te są zmieniane i mogą mieć wpływ lokalne lub regionalne regulacje podatkowe oraz zasady cenowe firmy Microsoft. 

### <a name="new-core-sizes-added-on-722019"></a>Nowe rozmiary podstawowe dodane do 7/2/2019

Wydawca maszyn wirtualnych został powiadomiony 2 lipca 2019 o dodawaniu nowych cen dla nowych rozmiarów maszyn wirtualnych platformy Azure (na podstawie liczby rdzeni).  Nowe ceny są dla wielkości rdzeni 10, 44, 48, 60, 120, 208 i 416.  W przypadku istniejącej maszyny wirtualnej nowe ceny dla tych rozmiarów rdzeni zostały automatycznie obliczone na podstawie bieżących cen.  Wydawcy mają do 1 sierpnia 2019 w celu przejrzenia dodatkowych cen i wprowadzenia wymaganych zmian.  Po tej dacie, jeśli nie została jeszcze ponownie opublikowana przez wydawcę, automatycznie obliczone ceny dla tych nowych rozmiarów podstawowych zaczną obowiązywać.


### <a name="simplified-currency-pricing"></a>Uproszczone ceny walutowe

Od września 1 2018 nową sekcję o nazwie **uproszczone ceny waluty** zostaną dodane do portalu. Firma Microsoft usprawnia działalność biznesową w witrynie Azure Marketplace, zapewniając bardziej przewidywalne ceny i kolekcje od klientów na całym świecie. Ta ulepszona oferta obejmie zmniejszenie liczby walut, w których klienci są rozliczani.  Aby uzyskać więcej informacji, zobacz temat [Aktualizowanie istniejącej oferty maszyny wirtualnej w witrynie Azure Marketplace](./cpp-update-existing-offer.md).


### <a name="additional-information-on-taxes-and-prices"></a>Dodatkowe informacje na temat podatków i cen

* Firma Microsoft klasyfikuje niektóre kraje/regiony jako kraje przestawione *podatkiem*.  W takich krajach/regionach firma Microsoft zbiera podatki od klientów, a następnie płaci podatki od instytucji rządowych.  W innych krajach/regionach partnerzy są zwykle odpowiedzialni za pobieranie podatków od klientów i opłacanie podatków dla instytucji rządowych. Jeśli zdecydujesz się sprzedawać w tych samych krajach/regionach, musisz mieć możliwość obliczania i płacenia podatków lokalnych.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* Ceny nie są zmieniane po przeprowadzeniu oferty na żywo. Można jednak nadal dodawać lub usuwać obsługiwane regiony. 
* Firma Microsoft obciąża standardowe opłaty za korzystanie z maszyn wirtualnych platformy Azure, oprócz opłat zaplanowanych dla jednostki SKU.
* Ceny są ustawiane dla wszystkich regionów w walucie lokalnej dla dostępnych stawek walutowych w momencie Ustawienia cen.  <!-- TD: Meaning? - Offer created, published, other? -->
* Aby ustawić cenę każdego regionu osobno, należy wyeksportować arkusz cen, zastosować Cennik niestandardowy, a następnie zaimportować. 


## <a name="next-steps"></a>Następne kroki

Opcjonalnie możesz określić informacje o [stacji testowej](./cpp-test-drive-tab.md) , Jeśli obsługujesz tę funkcję. w przeciwnym razie podajesz [dane dla](./cpp-marketplace-tab.md) swojej oferty.
