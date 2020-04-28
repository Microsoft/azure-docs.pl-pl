---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cb959b94807678187363d3132ece273584f13a0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77590723"
---
Zaoszczędź na użyciu usługi Azure Disk Storage z pojemnością zarezerwowaną. Rezerwacje usługi Azure Disk Storage w połączeniu z wystąpieniami zarezerwowanej maszyny wirtualnej platformy Azure umożliwiają obniżenie całkowitych kosztów maszyny wirtualnej.Azure Disk Storage reservations combined with Azure Reserved Virtual Machine Instances let you lower your total virtual machine (VM) costs. Rabat rezerwacji jest stosowany automatycznie do pasujących dysków w wybranym zakresie rezerwacji. Z powodu tej aplikacji automatycznej nie trzeba przypisywać rezerwacji do dysku zarządzanego, aby uzyskać rabaty.

Rabaty są stosowane co godzinę w zależności od użycia dysku. Nieużywane pojemności zarezerwowanej nie są przewożenie. Rabaty za rezerwację usługi Azure Disk Storage nie mają zastosowania do dysków niezarządzanych, dysków ultra lub zużycia obiektów blob strony.

## <a name="determine-your-storage-needs"></a>Określanie potrzeb w zakresie pamięci masowej

Przed zakupem rezerwacji należy określić potrzeby w zakresie przechowywania. Obecnie rezerwacje usługi Azure Disk Storage są dostępne tylko dla wybranych jednostek SSD premium platformy Azure. Jednostka SKU dysku SSD w wersji premium określa rozmiar i wydajność dysku.

Podczas określania potrzeb magazynu, nie myśleć o dyskach na podstawie tylko pojemności. Na przykład nie można mieć rezerwacji na dysk P40 i używać go do płacenia za dwa mniejsze dyski P30. Kupując rezerwację, kupujesz tylko rezerwację dla całkowitej liczby dysków na jednostkę SKU.

Na jednostkę SKU dysku dokonywana jest rezerwacja dysku. W rezultacie zużycie rezerwacji opiera się na jednostce jednostek SKU dysku zamiast podanego rozmiaru.

Załóżmy na przykład, że zarezerwujesz jeden dysk P40, który ma 2 TiB aprowizowanego pojemność magazynu. Załóżmy również, że można przydzielić tylko dwa dyski P30. Rezerwacja P40 w takim przypadku nie uwzględnia zużycia P30, a płacisz stawkę płatności zgodnie z rzeczywistym użyciem na dyskach P30.
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Uwagi dotyczące zakupu

Przy rozważaniu zakupu rezerwacji dysku zalecamy następujące praktyki:

- Analizuj informacje o użytkowaniu, aby określić, które rezerwacje należy kupić. Upewnij się, że śledzenie użycia w jednostkach SKU dysku zamiast aprowizowanych lub używane pojemności dysku.
- Sprawdź rezerwację dysku wraz z rezerwacją maszyny Wirtualnej. Zdecydowanie zalecamy dokonywanie rezerwacji zarówno użycia maszyny Wirtualnej, jak i użycia dysku w celu uzyskania maksymalnych oszczędności. Można rozpocząć od określenia właściwej rezerwacji maszyny Wirtualnej, a następnie oceny rezerwacji dysku. Ogólnie rzecz biorąc, będziesz mieć standardową konfigurację dla każdego z obciążeń. Na przykład serwer SQL Server może mieć dwa dyski danych P40 i jeden dysk systemu operacyjnego P30.
  
  Ten rodzaj wzorca może pomóc w określeniu zarezerwowanej kwoty, którą możesz kupić. Takie podejście może uprościć proces oceny i upewnij się, że masz wyrównany plan dla maszyny Wirtualnej i dysków. Plan zawiera zagadnienia, takie jak subskrypcje lub regiony.

## <a name="purchase-restrictions"></a>Ograniczenia zakupu

Rabaty rezerwacyjne są obecnie niedostępne w następujących okresach:

- Niezarządzane dyski lub obiekty blob strony.
- Standardowe dyski SSD lub standardowe dyski twarde (HDD).
- Jednostki SSD premium mniejsze niż P30: P1, P2, P3, P4, P6, P10, P15 i P20 SSD SSD.
- Dyski w regionach Azure Government, Azure Germany lub Azure China.

W rzadkich przypadkach platforma Azure ogranicza zakup nowych rezerwacji do podzbioru jednostek SKU dysku z powodu małej pojemności w regionie.

## <a name="buy-a-disk-reservation"></a>Kup rezerwację dysku

Rezerwacji usługi Azure Disk Storage można kupić za pośrednictwem [witryny Azure portal](https://portal.azure.com/). Możesz zapłacić za rezerwację z góry lub z miesięcznymi płatnościami. Aby uzyskać więcej informacji o zakupach z [płatnościami miesięcznymi, zobacz Rezerwacje zakupów z płatnościami miesięcznymi](../articles/cost-management-billing/reservations/monthly-payments-reservations.md).

Wykonaj następujące kroki, aby kupić pojemność rezerwową:

1. Przejdź do okienka [Rezerwacje zakupu](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) w witrynie Azure portal.

1. Wybierz **dyski zarządzane platformy Azure,** aby kupić rezerwację.

    ![Okienko do zakupu rezerwacji](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. Określ wymagane wartości opisane w poniższej tabeli:

   |Element  |Opis  |
   |---------|---------|
   |**Zakres**   |  Ile subskrypcji może korzystać z korzyści rozliczeniowych skojarzonych z rezerwacją. Ta wartość określa również, jak rezerwacja jest stosowana do określonych subskrypcji. <br/><br/> Jeśli wybierzesz **udostępnione,** rabat rezerwacji jest stosowany do pojemności usługi Azure Storage w każdej subskrypcji w kontekście rozliczeń. Kontekst rozliczeń jest oparty na tym, jak zarejestrowałeś się na platformie Azure. Dla klientów korporacyjnych zakresem udostępnionym jest rejestracja i obejmuje wszystkie subskrypcje w ramach rejestracji. W przypadku klientów korzystających z płatności zgodnie z rzeczywistymi oczekiwaniami zakres współużytkowane obejmuje wszystkie indywidualne subskrypcje ze stawkami płatności zgodnie z rzeczywistymu po drodze utworzonymi przez administratora konta.  <br/><br/>  Jeśli wybierzesz **pojedynczą subskrypcję,** rabat rezerwacji zostanie zastosowany do pojemności usługi Azure Storage w wybranej subskrypcji. <br/><br/> Jeśli wybierzesz **pojedynczą grupę zasobów,** rabat rezerwacji zostanie zastosowany do pojemności usługi Azure Storage w wybranej subskrypcji i w wybranej grupie zasobów tej subskrypcji. <br/><br/> Zakres rezerwacji można zmienić po zakupie rezerwacji.  |
   |**Subskrypcja**  | Subskrypcja używana do płacenia za rezerwację usługi Azure Storage. Metoda płatności w wybranej subskrypcji jest używana do obciążania kosztów. Subskrypcja musi być jednym z następujących typów:<br/><ul><li> Enterprise Agreement (numery oferty MS-AZR-0017P i MS-AZR-0148P). W przypadku subskrypcji Enterprise opłaty są odejmowane od salda zobowiązań pieniężnych rejestracji lub naliczane jako nadmierne.</li><br/><li>Subskrypcja indywidualna ze stawkami płatności zgodnie z rzeczywistymi opłatami (numery oferty MS-AZR-0003P i MS-AZR-0023P). W przypadku pojedynczej subskrypcji ze stawkami płatności zgodnie z rzeczywistym obciążeniem opłaty są naliczane na kartę kredytową lub metodę płatności faktury w ramach subskrypcji.</li></ul>    |
   | **Dyski** | Jednostka SKU, którą chcesz utworzyć. |
   | **Region** | Region, w którym obowiązuje rezerwacja. |
   | **Częstotliwość rozliczeń** | Jak często konto jest rozliczane za rezerwację. Opcje obejmują **miesięczne** i **z góry**. |

    ![Okienko wyboru produktu, który chcesz kupić.png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. Po określeniu wartości rezerwacji portal Azure wyświetla koszt. Portal pokazuje również procent rabatu w stosunku do rozliczeń zgodnie z rzeczywistymu posuwu. Wybierz **pozycję Dalej,** aby przejść do okienka **Rezerwacje zakupu.**

1. W okienku **Rezerwacje zakupu** możesz nazwać rezerwację i wybrać całkowitą liczbę rezerwacji, które chcesz wprowadzić. Liczba rezerwacji jest mapowana na liczbę dysków. Na przykład, jeśli chcesz zarezerwować sto dysków, wprowadź **wartość Ilość** **100**.

1. Przejrzyj całkowity koszt rezerwacji.

    ![Okienko Rezerwacje zakupu](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

Po zakupie rezerwacji jest ona automatycznie stosowana do wszystkich istniejących zasobów magazynu dysków, które odpowiadają warunkom rezerwacji. Jeśli nie utworzono jeszcze żadnych zasobów magazynu dysku, rezerwacja ma zastosowanie przy każdym utworzeniu zasobu, który pasuje do warunków rezerwacji. W obu przypadkach okres rezerwacji rozpoczyna się natychmiast po pomyślnym zakupie.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Możesz anulować, wymienić lub zwrócić rezerwacje w określonych ograniczeniach. Aby uzyskać więcej informacji, zobacz [Samoobsługowe wymiany i zwroty pieniędzy za rezerwacje platformy Azure.](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations)

## <a name="expiration-of-a-reservation"></a>Wygaśnięcie rezerwacji

Po wygaśnięciu rezerwacji każda pojemność usługi Azure Disk Storage używana w ramach tej rezerwacji jest rozliczana według stawki płatności zgodnie z rzeczywistym użyciem. Rezerwacje nie są odnawiane automatycznie.

Otrzymasz powiadomienie e-mail na 30 dni przed wygaśnięciem rezerwacji i ponownie w dniu wygaśnięcia. Aby kontynuować korzystanie z oszczędności kosztów, które zapewnia rezerwacja, odnowi ją nie później niż w dniu wygaśnięcia.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Co to jest Azure Reservations?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Omówienie stosowania rabatu za rezerwację do usługi Azure Disk Storage](../articles/cost-management-billing/reservations/understand-disk-reservations.md)
