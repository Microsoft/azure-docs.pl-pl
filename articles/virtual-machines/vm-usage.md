---
title: Informacje o użyciu maszyny wirtualnej platformy Azure
description: Informacje o użyciu maszyny wirtualnej
services: virtual-machines
documentationcenter: ''
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 07/28/2020
ms.openlocfilehash: ba973bd5609dacf05eca842025d4e828d8a9f841
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550951"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Informacje o użyciu maszyny wirtualnej platformy Azure
Analizując dane użycia platformy Azure, można uzyskać zaawansowane informacje o zużyciu — szczegółowe informacje umożliwiające zarządzanie kosztami i alokację w całej organizacji. Ten dokument zawiera szczegółowe informacje o szczegółowe na temat użycia obliczeń na platformie Azure. Aby uzyskać więcej informacji na temat ogólnego użycia platformy Azure, przejdź do opisu [rachunku](../cost-management-billing/understand/review-individual-bill.md).

## <a name="download-your-usage-details"></a>Pobieranie szczegółowego zestawienia użycia
Aby rozpocząć, [Pobierz szczegóły użycia](../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal). Poniższa tabela zawiera definicje i przykładowe wartości użycia dla Virtual Machines wdrożonych za pośrednictwem Azure Resource Manager. Ten dokument nie zawiera szczegółowych informacji o maszynach wirtualnych wdrożonych za pośrednictwem naszego modelu klasycznego.


| Pole | Znaczenie | Przykładowe wartości | 
|---|---|---|
| Data wykorzystania | Data użycia zasobu | `11/23/2017` |
| Meter ID | Określa usługę najwyższego poziomu, do której należy to użycie| `Virtual Machines`|
| Meter Sub-Category | Identyfikator miernika, za którego została naliczona opłata. <br><br> W przypadku użycia godzin obliczeniowych istnieje miernik dla każdego rozmiaru maszyny wirtualnej + system operacyjny (Windows, system inny niż Windows) + region. <br><br> W przypadku użycia oprogramowania w warstwie Premium istnieje miernik dla każdego typu oprogramowania. Większość obrazów oprogramowania w warstwie Premium ma różne liczniki dla każdego rozmiaru rdzenia. Aby uzyskać więcej informacji, odwiedź [stronę z cennikiem obliczania](https://azure.microsoft.com/pricing/details/virtual-machines/) .</li></ul>| `2005544f-659d-49c9-9094-8e0aea1be3a5`|
| Meter Name| Jest to specyficzne dla każdej usługi platformy Azure. W przypadku obliczeń jest to zawsze "Godziny obliczeniowe".| `Compute Hours`|
| Meter Region| Określa lokalizację centrum danych pewnych usług, które są wyceniane na podstawie lokalizacji centrum danych.|  `JA East`|
| Jednostka| Identyfikuje jednostkę, w której rozliczana jest usługa. Zasoby obliczeniowe są rozliczane na godzinę.| `Hours`|
| Zużyte| Ilość zasobu wykorzystana w danym dniu. W przypadku obliczeń opłata jest naliczana za każdą minutę, gdy maszyna wirtualna działała przez daną godzinę (do 6 cyfr dziesiętnych dokładności).| `1, 0.5`|
| Resource Location  | Identyfikuje centrum danych, w którym jest uruchamiany zasób.| `JA East`|
| Consumed Service | Użyta usługa platformy Azure.| `Microsoft.Compute`|
| Grupa zasobów | Grupa zasobów, w której działa wdrożony zasób. Aby uzyskać więcej informacji, zobacz [Azure Resource Manager przegląd.](../azure-resource-manager/management/overview.md)|`MyRG`|
| Instance ID | Identyfikator zasobu. Identyfikator zawiera nazwę, która została określona dla zasobu, gdy został on utworzony. W przypadku maszyn wirtualnych identyfikator wystąpienia będzie zawierać identyfikatory subskrypcji, ResourceGroupName i VMName (lub nazwę zestawu skalowania dla użycia zestawu skalowania).| `/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1`<br><br>lub<br><br>`/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1`|
| Tagi| Tag przypisany do zasobu. Użyj tagów, aby zgrupować rekordy rozliczeń. Dowiedz się, jak oznaczyć Virtual Machines przy użyciu [interfejsu wiersza polecenia](./tag-cli.md) lub [programu PowerShell](./tag-portal.md) , który jest dostępny tylko dla Menedżer zasobów maszyn wirtualnych.| `{"myDepartment":"RD","myUser":"myName"}`|
| Dodatkowe informacje | Metadane dotyczące konkretnej usługi. W przypadku maszyn wirtualnych w polu dodatkowe informacje są wypełniane następujące dane: <br><br> Obraz, który został uruchomiony. Znajdź pełną listę obsługiwanych ciągów poniżej w obszarze typy obrazów.<br><br> Typ usługi: wdrożony rozmiar.<br><br> VMName: Nazwa maszyny wirtualnej. To pole jest wypełniane tylko dla maszyn wirtualnych z zestawem skalowania. Jeśli potrzebujesz nazwy maszyny wirtualnej dla maszyn wirtualnych zestawu skalowania, możesz ją znaleźć w powyższym ciągu identyfikatora wystąpienia.<br><br> UsageType: określa typ użycia, który reprezentuje.<br><br> ComputeHR to użycie godzin obliczeniowych dla źródłowej maszyny wirtualnej, takie jak Standard_D1_v2.<br><br> ComputeHR_SW to opłata za oprogramowanie w warstwie Premium, jeśli maszyna wirtualna korzysta z oprogramowania Premium, takiego jak Microsoft R Server. | Virtual Machines<br>`{"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR"}`<br><br>Virtual Machine Scale Sets<br> `{"ImageType":"Canonical","ServiceType":"Standard_DS1_v2","VMName":"myVM1", "UsageType":"ComputeHR"}`<br><br>Oprogramowanie w warstwie Premium<br> `{"ImageType":"","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR_SW"}` |

## <a name="image-type"></a>Typ obrazu
W przypadku niektórych obrazów w galerii platformy Azure typ obrazu jest wypełniany w polu dodatkowe informacje. Dzięki temu użytkownicy mogą zrozumieć i śledzić te, które zostały wdrożone na ich maszynach wirtualnych. Następujące wartości, które są wypełniane w tym polu na podstawie wdrożonego obrazu:
- BitRock 
- FreeBSD kanoniczny 
- Otwórz logikę 
- Oracle 
- SLES dla SAP 
- Wersja zapoznawcza SQL Server 14 w systemie Windows Server 2012 R2 Preview 
- SUSE
- SUSE Premium
- Urządzenie w chmurze StorSimple 
- Red Hat
- Business Applications firmy Red Hat dla oprogramowania SAP     
- Red Hat dla SAP HANA 
- BYOL klienta systemu Windows 
- BYOL systemu Windows Server 
- Wersja zapoznawcza systemu Windows Server 

## <a name="service-type"></a>Typ usługi
Pole Typ usługi w polu dodatkowe informacje odpowiada dokładnemu rozmiarowi wdrożonej maszyny wirtualnej. W przypadku maszyn wirtualnych magazynu w warstwie Premium (opartych na dyskach SSD) i maszyn wirtualnych z systemem innym niż Premium (na DYSKach twardych) są naliczane opłaty. W przypadku wdrożenia rozmiaru opartego na dyskach SSD, takiego jak standardowa \_ DS2 \_ v2, rozmiar inny niż SSD ( `Standard\_D2\_v2 VM` ) zostanie wyświetlony w kolumnie licznik Sub-Category i rozmiar SSD ( `Standard\_DS2\_v2` ) w polu dodatkowe informacje.

## <a name="region-names"></a>Nazwy regionów
Nazwa regionu wypełniana w polu Lokalizacja zasobu w szczegółach użycia różni się od nazwy regionu używanej w Azure Resource Manager. Poniżej znajduje się mapowanie między wartościami regionu:

| **Nazwa regionu Menedżer zasobów** | **Lokalizacja zasobu w szczegółach użycia** |
|---|---|
| australiaeast |Australia Wschodnia|
| australiasoutheast | Australia Południowo-Wschodnia|
| brazilsouth | Brazylia Południowa|
| CanadaCentral | Kanada Środkowa|
| CanadaEast | Kanada Wschodnia|
| CentralIndia | Indie Środkowe|
| centralus | Central US|
| chinaeast | Chiny Wschodnie|
| chinanorth | Chiny Północne|
| eastasia | Azja Wschodnia|
| eastus | East US|
| eastus2 | Wschodnie stany USA 2|
| GermanyCentral | Niemcy środkowe|
| GermanyNortheast | DE północny|
| japaneast | Japonia Wschodnia|
| japanwest | Japonia Zachodnia|
| KoreaCentral | Korea Środkowa|
| KoreaSouth | Korea Południowa|
| northcentralus | Północno-środkowe stany USA|
| northeurope | Europa Północna|
| southcentralus | South Central US|
| southeastasia | Southeast Asia|
| SouthIndia | Indie Południowe|
| UKNorth | Północne stany USA|
| uksouth | Południowe Zjednoczone Królestwo|
| UKSouth2 | Południowe Zjednoczone Królestwo 2|
| ukwest | Zachodnie Zjednoczone Królestwo|
| USDoDCentral | US DoD (region środkowy)|
| USDoDEast | US DoD (region wschodni)|
| USGovArizona | USGov Arizona|
| usgoviowa | USGov Iowa|
| USGovTexas | USGov Teksas|
| usgovvirginia | USGov Wirginia|
| Zachodnio-środkowe stany USA | Zachodnio-środkowe stany USA|
| westeurope | West Europe|
| WestIndia | Indie Zachodnie|
| westus | Zachodnie stany USA|
| westus2 | Zachodnie stany USA 2|


## <a name="virtual-machine-usage-faq"></a>Często zadawane pytania dotyczące użycia maszyn wirtualnych
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Jakie zasoby są rozliczone w przypadku wdrażania maszyny wirtualnej?    
Maszyny wirtualne uzyskują koszty dotyczące samej maszyny wirtualnej, dowolnego oprogramowania w warstwie Premium uruchomionego na maszynie wirtualnej, dysku account\managed magazynu skojarzonego z maszyną wirtualną oraz transferów przepustowości sieci z maszyny wirtualnej.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Jak sprawdzić, czy maszyna wirtualna używa Korzyść użycia hybrydowego platformy Azure w woluminie CSV użycia?
W przypadku wdrażania przy użyciu [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/)jest naliczana stawka maszyn wirtualnych bez systemu Windows, ponieważ jest ona używana do chmury. Na rachunku można rozróżnić, które Menedżer zasobów maszyny wirtualne są uruchomione Korzyść użycia hybrydowego platformy Azure, ponieważ mają one "Windows \_ Server BYOL" lub "Windows \_ Client BYOL" w kolumnie ImageType.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Jak podstawowe i standardowe typy maszyn wirtualnych różnią się w woluminie CSV użycia?
Oferowane są zarówno podstawowe, jak i standardowe maszyny wirtualne z serii A. W przypadku wdrożenia podstawowej maszyny wirtualnej w podkategorii miernika będzie ona mieć ciąg "podstawowa". Jeśli wdrożono maszynę wirtualną z serii A, rozmiar maszyny wirtualnej jest wyświetlany jako "maszyna wirtualna a1", ponieważ wartość domyślna to standardowa. Aby dowiedzieć się więcej o różnicach między warstwami podstawowa i standardowa, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/virtual-machines/).
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>Co to są rozmiary ExtraSmall, małe, średnie, duże i ExtraLarge?
ExtraSmall-ExtraLarge to starsze nazwy standardowego \_ a0 — standard \_ a4. W przypadku klasycznych rekordów użycia maszyn wirtualnych można zobaczyć, że ta konwencja jest używana, jeśli te rozmiary zostały wdrożone.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Jaka jest różnica między regionem zliczania i lokalizacją zasobu?
Region miernika jest skojarzony z miernikiem. W przypadku niektórych usług platformy Azure, które korzystają z jednej ceny dla wszystkich regionów, pole region miernika może być puste. Jednak ponieważ maszyny wirtualne mają dedykowane ceny na region dla Virtual Machines, to pole jest wypełniane. Podobnie lokalizacja zasobu dla Virtual Machines jest lokalizacją, w której wdrożono maszynę wirtualną. Regiony platformy Azure w obu polach są takie same, chociaż mogą mieć inną konwencję ciągu dla nazwy regionu.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Dlaczego wartość ImageType jest pusta w polu dodatkowe informacje?
Pole ImageType jest wypełniane tylko dla podzestawu obrazów. Jeśli nie wdrożono jednego z obrazów powyżej, wartość ImageType jest pusta.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Dlaczego VMName jest pusta w dodatkowych informacjach?
VMName jest wypełniana tylko w polu dodatkowe informacje dla maszyn wirtualnych w zestawie skalowania. Wartość pola InstanceID zawiera nazwę maszyny wirtualnej dla maszyn wirtualnych z zestawem nieskalowanym.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>Co oznacza ComputeHR w polu UsageType w informacjach dodatkowych?
ComputeHR oznacza godzinę obliczeniową, która reprezentuje zdarzenie użycia dla bazowego kosztu infrastruktury. Jeśli parametr UsageType ma wartość ComputeHR \_ SW, zdarzenie użycia reprezentuje opłatę za oprogramowanie w warstwie Premium dla maszyny wirtualnej.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Jak mogę sprawdzić, czy opłata jest naliczana za oprogramowanie w warstwie Premium?
Podczas analizowania obrazu maszyny wirtualnej najlepiej odpowiadającego Twoim potrzebom należy zapoznać się z [portalem Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute). Obraz ma stawkę planu oprogramowania. Jeśli dla stawki zostanie wyświetlona wartość "bezpłatna", nie ma dodatkowych kosztów dla oprogramowania. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Jaka jest różnica między Microsoft. ClassicCompute i Microsoft. COMPUTE w używanej usłudze?
Microsoft. ClassicCompute reprezentuje zasoby klasyczne wdrożone za pośrednictwem Service Manager platformy Azure. W przypadku wdrażania za pośrednictwem Menedżer zasobów, firma Microsoft. COMPUTE zostanie uzupełniona o użycie usługi. Dowiedz się więcej na temat [modeli wdrażania platformy Azure](../azure-resource-manager/management/deployment-models.md).
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Dlaczego pole InstanceID jest puste w przypadku użycia maszyny wirtualnej?
W przypadku wdrożenia za pomocą klasycznego modelu wdrażania ciąg InstanceID nie jest dostępny.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Dlaczego Tagi moich maszyn wirtualnych nie przepływają do szczegółów użycia?
Tagi przepływają tylko do pliku CSV użycia tylko dla Menedżer zasobów maszyn wirtualnych. Klasyczne Tagi zasobów nie są dostępne w szczegółach użycia.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Jak ilość zużyta może być większa niż 24 godziny dziennie?
W modelu klasycznym rozliczenia dla zasobów są agregowane na poziomie usługi w chmurze. Jeśli masz więcej niż jedną maszynę wirtualną w usłudze w chmurze, która używa tego samego miernika rozliczeniowego, użycie jest agregowane razem. Maszyny wirtualne wdrożone za pośrednictwem Menedżer zasobów są rozliczane na poziomie maszyny wirtualnej, więc ta agregacja nie będzie miała zastosowania.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Dlaczego Cennik nie są dostępne dla rozmiarów DS/FS/GS/LS na stronie cennika?
Maszyny wirtualne z obsługą magazynu w warstwie Premium są rozliczane w taki sam sposób, jak maszyny wirtualne z obsługą magazynu innego niż Premium. Różnią się tylko koszty związane z magazynem. Aby uzyskać więcej informacji, odwiedź [stronę z cennikiem usługi Storage](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/) .

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat szczegółowych informacji dotyczących użycia, zobacz [Opis rachunku na Microsoft Azure.](../cost-management-billing/understand/review-individual-bill.md)