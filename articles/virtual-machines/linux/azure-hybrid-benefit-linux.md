---
title: Korzyść użycia hybrydowego platformy Azure i maszyny wirtualne z systemem Linux
description: Dowiedz się, Korzyść użycia hybrydowego platformy Azure zaoszczędzić pieniądze na maszynach wirtualnych z systemem Linux uruchomionych na platformie Azure.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machines
ms.subservice: azure-hybrid-benefit
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 774f4be6a5aa0e0e772086c52938881c6637b261
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588194"
---
# <a name="how-azure-hybrid-benefit-applies-for-linux-virtual-machines"></a>Jak Korzyść użycia hybrydowego platformy Azure dotyczy maszyn wirtualnych z systemem Linux

Korzyść użycia hybrydowego platformy Azure to korzyść licencjonowania, która pomaga znacznie zmniejszyć koszty działania maszyn wirtualnych z systemami Red Hat Enterprise Linux (RHEL) i SUSE Linux Enterprise Server (SLES) w chmurze. Dzięki tej korzyści płacisz tylko za koszty infrastruktury maszyny wirtualnej, ponieważ subskrypcja systemu RHEL lub SLES obejmuje opłatę za oprogramowanie. Ta korzyść jest dostępna dla wszystkich obrazów platformy handlowej RHEL i SLES z płatnością zgodnie z tyg.

Korzyść użycia hybrydowego platformy Azure dla maszyn wirtualnych z systemem Linux jest teraz publicznie dostępny.

## <a name="benefit-description"></a>Opis korzyści

Za pośrednictwem usługi Korzyść użycia hybrydowego platformy Azure można migrować lokalne serwery RHEL i SLES na platformę Azure, konwertując istniejące maszyny wirtualne RHEL i SLES PAYG na platformie Azure w celu naliczania opłat za korzystanie z własnej subskrypcji (BYOS). Zazwyczaj maszyny wirtualne wdrożone na podstawie obrazów z płatnością za korzystanie z platformy Azure naliczają zarówno opłatę za infrastrukturę, jak i opłatę za oprogramowanie. Dzięki Korzyść użycia hybrydowego platformy Azure wirtualnej z płatnością zgodnie z płatnością zgodnie z modelem można przekonwertować na model rozliczeń byOS bez ponownego wdychowania, dzięki czemu można uniknąć ryzyka przestoju.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Korzyść użycia hybrydowego platformy Azure wizualizacji kosztów na maszynach wirtualnych z systemem Linux.":::

Po włączeniu korzyści na maszynie wirtualnej z programem RHEL lub SLES nie będzie już naliczana opłata za dodatkowe oprogramowanie naliczana zwykle na maszynie wirtualnej z płatnością zgodnie z płatnością zgodnie z kosztami. Zamiast tego maszyna wirtualna zacznie naliczać opłatę za korzystanie z systemu BYOS, która obejmuje tylko opłatę za sprzęt obliczeniowy i brak opłaty za oprogramowanie.

Można również przekonwertować maszynę wirtualną, dla których włączono korzyść, z powrotem na model rozliczeń z płatnością za usługi (PAYG).

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Zakres uprawnień Korzyść użycia hybrydowego platformy Azure dla maszyn wirtualnych z systemem Linux

Korzyść użycia hybrydowego platformy Azure jest dostępny dla wszystkich obrazów RHEL i SLES płatności za Azure Marketplace. Korzyści nie są jeszcze dostępne dla obrazów RHEL lub SLES BYOS ani obrazów niestandardowych z Azure Marketplace.

Azure Dedicated Host i korzyści użycia hybrydowego SQL nie kwalifikują się do Korzyść użycia hybrydowego platformy Azure, jeśli już korzystasz z korzyści z maszyn wirtualnych z systemem Linux.

## <a name="get-started"></a>Rozpoczęcie pracy

### <a name="red-hat-customers"></a>Klienci korzystający z firmy Red Hat

Korzyść użycia hybrydowego platformy Azure RHEL jest dostępny dla klientów z firmy Red Hat, którzy spełniają oba te kryteria:

- Mieć aktywne lub nieużywane subskrypcje systemu RHEL, które kwalifikują się do użycia na platformie Azure
- Włączyliśmy co najmniej jedną z tych subskrypcji do użycia na platformie Azure z [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) programem

> [!IMPORTANT]
> Upewnij się, że w programie dostępu do chmury włączono [prawidłową subskrypcję.](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)

Aby rozpocząć korzystanie z korzyści dla systemu Red Hat:

1. Włącz co najmniej jedną kwalifikującą się subskrypcję RHEL do użycia na platformie Azure przy użyciu [interfejsu](https://access.redhat.com/management/cloud)Red Hat Cloud Access klienta .

   Subskrypcje platformy Azure, które są zapewniane podczas Red Hat Cloud Access procesu włączania, będą wówczas mieć możliwość korzystania z tej Korzyść użycia hybrydowego platformy Azure usługi.
1. Zastosuj Korzyść użycia hybrydowego platformy Azure do dowolnej istniejącej maszyny wirtualnej Z PŁATNOŚCIĄ RHEL i wszystkich nowych maszyn wirtualnych RHEL wdrażanych z Azure Marketplace payg. Aby w celu włączenia korzyści Azure Portal interfejsu wiersza polecenia platformy Azure, możesz użyć interfejsu wiersza polecenia platformy Azure.
1. Wykonaj zalecane [następne kroki dotyczące](https://access.redhat.com/articles/5419341) konfigurowania źródeł aktualizacji dla maszyn wirtualnych RHEL i wytycznych dotyczących zgodności subskrypcji RHEL.


### <a name="suse-customers"></a>Klienci korzystający z usług SUSE

Aby rozpocząć korzystanie z korzyści dla oprogramowania SUSE:

1. Zarejestruj się w programie SUSE Public Cloud Program.
1. Zastosuj korzyść do nowo utworzonych lub istniejących maszyn wirtualnych za pośrednictwem interfejsu Azure Portal lub interfejsu wiersza polecenia platformy Azure.
1. Zarejestruj maszyny wirtualne, które otrzymują korzyść, z oddzielnym źródłem aktualizacji.

## <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>Włączanie i wyłączanie korzyści w Azure Portal

Możesz włączyć korzyść dla istniejących maszyn wirtualnych, odwiedzając opcję **Konfiguracja** po lewej stronie i korzystając z poniższych kroków. Możesz włączyć tę korzyść na nowych maszynach wirtualnych podczas tworzenia maszyny wirtualnej.

### <a name="azure-portal-example-to-enable-the-benefit-for-an-existing-vm"></a>Azure Portal przykład, aby włączyć korzyść dla istniejącej maszyny wirtualnej:
1. Odwiedź [Microsoft Azure Portal](https://portal.azure.com/)
1. Przejdź do strony "Tworzenie maszyny wirtualnej" w portalu.
 ![AHB podczas tworzenia maszyny wirtualnej](./media/azure-hybrid-benefit/create-vm-ahb.png)
1. Kliknij pole wyboru, aby włączyć konwersję AHB i używać licencji dostępu do chmury.
 ![Pole wyboru AHB podczas tworzenia maszyny wirtualnej](./media/azure-hybrid-benefit/create-vm-ahb-checkbox.png)
1. Tworzenie maszyny wirtualnej zgodnie z następnym zestawem instrukcji
1. Sprawdź blok **Konfiguracja,** aby zobaczyć włączoną opcję. 
![Blok Konfiguracja usługi AHB po utworzeniu](./media/azure-hybrid-benefit/create-configuration-blade.png)

### <a name="azure-portal-example-to-enable-the-benefit-during-creation-of-vm"></a>Azure Portal przykład, aby włączyć korzyść podczas tworzenia maszyny wirtualnej:
1. Odwiedź [Microsoft Azure Portal](https://portal.azure.com/)
1. Otwórz stronę Maszyna wirtualna, na której chcesz zastosować konwersję.
1. Przejdź do **opcji Konfiguracja** po lewej stronie. Zostanie wyświetlony sekcja Licencjonowanie. Aby włączyć konwersję AHB, zaznacz przycisk radiowy "Tak" i zaznacz pole wyboru Potwierdzenie.
![Blok Konfiguracja usługi AHB po utworzeniu](./media/azure-hybrid-benefit/create-configuration-blade.png)

>[!NOTE]
> Jeśli utworzono  migawkę niestandardową lub obraz udostępniony **(SIG)** obrazu z witryny Marketplace z płatnościami za korzystanie z systemu RHEL lub SLES, możesz włączyć obsługę tylko za pomocą interfejsu wiersza polecenia platformy Azure Korzyść użycia hybrydowego platformy Azure. Jest to znane ograniczenie i obecnie nie ma osi czasu, aby udostępnić tę funkcję również w witrynie Azure Portal.

## <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Włączanie i wyłączanie korzyści w interfejsie wiersza polecenia platformy Azure

Możesz użyć polecenia `az vm update` , aby zaktualizować istniejące maszyny wirtualne. W przypadku maszyn wirtualnych RHEL uruchom polecenie z `--license-type` parametrem `RHEL_BYOS` . W przypadku maszyn wirtualnych SLES uruchom polecenie z `--license-type` parametrem `SLES_BYOS` .

### <a name="cli-example-to-enable-the-benefit"></a>Przykład interfejsu wiersza polecenia umożliwiający włączenie korzyści
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>Przykład interfejsu wiersza polecenia w celu wyłączenia korzyści
Aby wyłączyć korzyść, użyj `--license-type` wartości `None` :

```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>Przykład interfejsu wiersza polecenia umożliwiający włączenie korzyści na dużej liczbie maszyn wirtualnych
Aby włączyć korzyści na dużej liczbie maszyn wirtualnych, możesz użyć `--ids` parametru w interfejsie wiersza polecenia platformy Azure:

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file that contains a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

W poniższych przykładach przedstawiono dwie metody uzyskiwania listy identyfikatorów zasobów: jedną na poziomie grupy zasobów i jedną na poziomie subskrypcji.

```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="apply-the-azure-hybrid-benefit-at-vm-create-time"></a>Stosowanie Korzyść użycia hybrydowego platformy Azure podczas tworzenia maszyny wirtualnej
Oprócz zastosowania tej Korzyść użycia hybrydowego platformy Azure do istniejących maszyn wirtualnych z płatnością zgodnie z rzeczywistym rzeczywistym zastosowaniem można wywołać ją podczas tworzenia maszyny wirtualnej. Korzyści wynikające z tego są trzy:
- Maszyny wirtualne z płatnością według płatności i według systemu BYOS można aprowizowania przy użyciu tego samego obrazu i procesu.
- Umożliwia to przyszłe zmiany trybu licencjonowania, coś, co nie jest dostępne w przypadku obrazu tylko dla systemu BYOS lub jeśli używasz własnej maszyny wirtualnej.
- Maszyna wirtualna będzie domyślnie połączona z infrastrukturą aktualizacji systemu Red Hat (RHUI, Red Hat Update Infrastructure), aby zapewnić jej aktualne i bezpieczne działanie. Zaktualizowany mechanizm można zmienić w dowolnym momencie po wdrożeniu.

## <a name="check-the-azure-hybrid-benefit-status-of-a-vm"></a>Sprawdzanie Korzyść użycia hybrydowego platformy Azure stanu maszyny wirtualnej
Stan maszyny wirtualnej Korzyść użycia hybrydowego platformy Azure można wyświetlić przy użyciu interfejsu wiersza polecenia platformy Azure lub usługi Azure Instance Metadata Service.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W tym celu `az vm get-instance-view` możesz użyć polecenia . Poszukaj pola `licenseType` w odpowiedzi. Jeśli pole `licenseType` istnieje, a wartość to `RHEL_BYOS` lub , `SLES_BYOS` maszyna wirtualna ma włączoną korzyść.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

Z poziomu samej maszyny wirtualnej możesz odpytać atestowane metadane w usłudze Azure Instance Metadata Service, aby określić wartość maszyny `licenseType` wirtualnej. Wartość `licenseType` lub `RHEL_BYOS` będzie `SLES_BYOS` wskazywać, że maszyna wirtualna ma włączoną korzyść. [Dowiedz się więcej o atestowanych metadanych.](./instance-metadata-service.md#attested-data)

## <a name="compliance"></a>Zgodność

### <a name="red-hat"></a>Red Hat

Klienci korzystający z Korzyść użycia hybrydowego platformy Azure RHEL zgadzają się [](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) na [](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) standardowe postanowienia prawne i zasady zachowania poufności informacji skojarzone z ofertami Azure Marketplace RHEL.

Klienci korzystający z Korzyść użycia hybrydowego platformy Azure systemu RHEL mają trzy opcje dostarczania aktualizacji oprogramowania i poprawek do tych maszyn wirtualnych:

- [Infrastruktura aktualizacji systemu Red Hat](../workloads/redhat/redhat-rhui.md) (opcja domyślna)
- Red Hat Satellite Server
- Red Hat Subscription Manager

Klienci, którzy wybiorą opcję FIRMWAREUI, mogą nadal używać systemu RHUI jako głównego źródła aktualizacji dla maszyn wirtualnych z Korzyść użycia hybrydowego platformy Azure RHEL bez dołączania subskrypcji RHEL do tych maszyn wirtualnych. Klienci, którzy wybiorą opcję RHUI, są odpowiedzialni za zapewnienie zgodności subskrypcji RHEL.

Klienci, którzy wybiorą serwer Red Hat Satellite Server lub Menedżera subskrypcji Red Hat, powinni usunąć konfigurację systemu RHUI, a następnie dołączyć subskrypcję systemu RHEL z włączoną obsługą systemu Cloud Access do maszyn wirtualnych Korzyść użycia hybrydowego platformy Azure RHEL.  

Aby uzyskać więcej informacji o zgodności subskrypcji systemu Red Hat, aktualizacjach oprogramowania i źródłach dla maszyn wirtualnych z programem Korzyść użycia hybrydowego platformy Azure RHEL, zobacz artykuł red Hat na temat używania subskrypcji [RHEL](https://access.redhat.com/articles/5419341)z programem Korzyść użycia hybrydowego platformy Azure .

### <a name="suse"></a>SUSE

Aby użyć usługi Korzyść użycia hybrydowego platformy Azure dla maszyn wirtualnych SLES i uzyskać informacje na temat przechodzenia z systemu SLES PAYG do systemu BYOS lub przechodzenia z systemu SLES BYOS do systemu PAYG, zobacz [SUSE Linux Enterprise](https://www.suse.com/c/suse-linux-enterprise-and-azure-hybrid-benefit/)i Korzyść użycia hybrydowego platformy Azure . 

## <a name="azure-hybrid-benefit-on-reserved-instances-is-in-preview"></a>Korzyść użycia hybrydowego platformy Azure w wystąpieniach zarezerwowanych jest w wersji zapoznawczej

Rezerwacje platformy Azure (Azure Reserved Virtual Machine Instances) pomagają zaoszczędzić pieniądze, zobowiązyjąc się do 1-letniego lub 3-letniego planu dla wielu produktów. Więcej informacji na temat wystąpień [zarezerwowanych można znaleźć tutaj.](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations) Ten Korzyść użycia hybrydowego platformy Azure jest dostępny w wersji zapoznawczej dla wystąpień zarezerwowanych maszyn [wirtualnych.](https://review.docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations#charges-covered-by-reservation) Oznacza to, że jeśli zakupiono koszty zasobów obliczeniowych w obniżonej stawce przy użyciu wystąpienia RI, można zastosować korzyść AHB do kosztów licencjonowania dla systemu RHEL i SUSE na jej podstawie. Kroki stosowania korzyści AHB dla wystąpienia zarezerwowanego pozostają dokładnie takie same jak w przypadku zwykłej maszyny wirtualnej.
![AHB dla interfejsów API](./media/azure-hybrid-benefit/reserved-instances.png)

>[!NOTE]
>Jeśli masz już zakupione rezerwacje oprogramowania RHEL lub SUSE PAYG na platformie Azure Marketplace, zaczekaj na ukończenie czasu korzystania z rezerwacji przed rozpoczęciem Korzyść użycia hybrydowego platformy Azure..


## <a name="frequently-asked-questions"></a>Często zadawane pytania
*Pytanie: Czy mogę używać typu licencji z `RHEL_BYOS` obrazem SLES lub odwrotnie?*

A: Nie, nie można. Próba wprowadzenia typu licencji, który niepoprawnie pasuje do dystrybucji uruchomionej na maszynie wirtualnej, nie spowoduje zaktualizowania żadnych metadanych rozliczeń. Jeśli jednak przypadkowo wprowadzeniu niewłaściwego typu licencji zostanie ponownie zaktualizowana maszyna wirtualna do prawidłowego typu licencji, korzyść będzie nadal włączyć.

*Pytanie: Zarejestrowano mnie w Red Hat Cloud Access, ale nadal nie można włączyć korzyści na moich maszynych wirtualnych ZHEL. Co mam zrobić?*

A: Propagowanie rejestracji subskrypcji z systemu Red Hat na platformę Azure Red Hat Cloud Access może zająć trochę czasu. Jeśli błąd będzie nadal wyświetlany po jednym dniu biznesowym, skontaktuj się z pomocą techniczną firmy Microsoft.

*Pytanie: Wdrożono maszynę wirtualną przy użyciu "złotego obrazu" systemu BYOS systemu RHEL. Czy mogę przekonwertować rozliczenia dla tych obrazów z BYOS na płatność według płatności za pomocą konta?*

A: Nie, nie można. Korzyść użycia hybrydowego platformy Azure obsługuje konwersję tylko w przypadku obrazów z płatnością zgodnie z ty.

*Pytanie: Mój własny obraz systemu RHEL został przekazany ze środowisk lokalnych (za pośrednictwem Azure Migrate, Azure Site Recovery lub w inny sposób) na platformę Azure. Czy mogę przekonwertować rozliczenia dla tych obrazów z BYOS na płatność według płatności za pomocą konta?*

A: Nie, nie można. Funkcja Korzyść użycia hybrydowego platformy Azure jest obecnie dostępna tylko dla obrazów RHEL i SLES w Azure Marketplace. 

*Pytanie: Mój własny obraz systemu RHEL został przekazany ze środowisk lokalnych (za pośrednictwem Azure Migrate, Azure Site Recovery lub w inny sposób) na platformę Azure. Czy muszę zrobić coś, aby korzystać z zalet Korzyść użycia hybrydowego platformy Azure?*

A: Nie, nie. Obrazy systemu RHEL, które są już traktowane jako byOS, są naliczane tylko za koszty infrastruktury platformy Azure. Odpowiadasz za koszty subskrypcji RHEL, tak samo jak w przypadku środowisk lokalnych. 

*Pytanie: Czy można używać Korzyść użycia hybrydowego platformy Azure na Azure Marketplace RHEL i SLES SAP?*

Odpowiedź: Tak, możesz. Można użyć typu licencji dla maszyn wirtualnych RHEL i konwersji maszyn wirtualnych wdrożonych z Azure Marketplace `RHEL_BYOS` `SLES_BYOS` RHEL i SLES SAP.

*Pytanie: Czy można używać funkcji Korzyść użycia hybrydowego platformy Azure w zestawach skalowania maszyn wirtualnych dla RHEL i SLES?*

Odpowiedź: Tak, Korzyść użycia hybrydowego platformy Azure w zestawach skalowania maszyn wirtualnych dla systemu RHEL i SLES jest w wersji zapoznawczej. Więcej informacji [na temat tej korzyści i sposobu jej używania można znaleźć tutaj.](/azure/virtual-machine-scale-sets/azure-hybrid-benefit-linux) 

*Pytanie: Czy można używać Korzyść użycia hybrydowego platformy Azure wystąpień zarezerwowanych dla RHEL i SLES?*

Odpowiedź: Tak, Korzyść użycia hybrydowego platformy Azure wystąpienia zarezerwowanego dla systemu RHEL i systemu SLES jest w wersji zapoznawczej. Więcej informacji [na temat tej korzyści i sposobu jej używania można znaleźć tutaj.](#azure-hybrid-benefit-on-reserved-instances-is-in-preview)

*Pytanie: Czy można używać Korzyść użycia hybrydowego platformy Azure na maszynie wirtualnej wdrożonej na SQL Server obrazach systemu RHEL?*

A: Nie, nie można. Nie ma planu obsługi tych maszyn wirtualnych.

*Pytanie: czy mogę używać Korzyść użycia hybrydowego platformy Azure subskrypcji wirtualnego centrum danych RHEL?*

A: Nie, nie można. Wirtualne centrum danych nie jest w ogóle obsługiwane na platformie Azure, w tym AHB.  
 

## <a name="common-problems"></a>Typowe problemy
W tej sekcji wymieniono typowe problemy, które można napotkać, oraz kroki zaradcze.

| Błąd | Ograniczanie ryzyka |
| ----- | ---------- |
| "Nie można ukończyć akcji, ponieważ nasze rekordy pokazują, że nie włączono pomyślnie Red Hat Cloud Access subskrypcji platformy Azure...". | Aby korzystać z zalet maszyn wirtualnych RHEL, należy najpierw zarejestrować subskrypcje platformy Azure w [usłudze Red Hat Cloud Access](https://access.redhat.com/management/cloud).

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się, jak tworzyć i aktualizować maszyny wirtualne oraz dodawać typy licencji (RHEL_BYOS, SLES_BYOS) dla maszyn wirtualnych Korzyść użycia hybrydowego platformy Azure użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/vm)
