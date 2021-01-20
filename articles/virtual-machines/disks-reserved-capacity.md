---
title: Optymalizowanie kosztów Azure Disk Storage przy użyciu rezerwacji
description: Dowiedz się więcej na temat kupowania Azure Disk Storage rezerwacje, aby zaoszczędzić koszty na dyskach zarządzanych w warstwie Premium SSD.
author: roygara
ms.author: rogarana
ms.date: 01/30/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: db6575894904e6ced2d4be48fec5961f5b8b8a54
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602635"
---
# <a name="reduce-costs-with-azure-disks-reservation"></a>Obniżenie kosztów dzięki rezerwacji dysków platformy Azure

Zapisz użycie Azure Disk Storage przy użyciu zarezerwowanej pojemności. Azure Disk Storage rezerwacje połączone z Azure Reserved Virtual Machine Instances umożliwiają obniżenie całkowitych kosztów maszyn wirtualnych (VM). Rabat rezerwacji jest automatycznie stosowany do zgodnych dysków w wybranym zakresie rezerwacji. Ze względu na tę automatyczną aplikację nie trzeba przypisywać do dysku zarządzanego rezerwacji w celu uzyskania rabatów.

Rabaty są stosowane co godzinę w zależności od użycia dysku. Niewykorzystana pojemność zastrzeżona nie jest przenoszone. Rabaty zarezerwowane Azure Disk Storage nie dotyczą dysków niezarządzanych, Ultra Disk ani zużycia obiektów BLOB stron.

## <a name="determine-your-storage-needs"></a>Określanie potrzeb związanych z magazynem

Przed zakupem rezerwacji należy określić potrzeby związane z magazynem. Obecnie rezerwacje Azure Disk Storage są dostępne tylko dla wybranych jednostek SKU platformy Azure Premium SSD. Jednostka SKU warstwy SSD w warstwie Premium określa rozmiar i wydajność dysku.

Podczas określania potrzeb związanych z magazynem nie należy myśleć o dyskach w oparciu o pojemność. Na przykład nie można zarezerwować dysku P40 i używać go do płacenia za dwa mniejsze dyski P30. Podczas zakupu rezerwacji można kupić tylko rezerwację dla łącznej liczby dysków na jednostkę SKU.

Rezerwacja dysku jest wykonywana na jednostkę SKU dysku. W związku z tym użycie rezerwacji jest zależne od jednostki jednostek SKU dysku zamiast podanego rozmiaru.

Załóżmy na przykład, że zarezerwujesz jeden dysk P40 o pojemności 2 TiB zainicjowanej pamięci masowej. Załóżmy również, że przypisujesz tylko dwa dyski P30. Rezerwacja P40 w tym przypadku nie uwzględnia zużycia P30 i płatność według stawki płatność zgodnie z rzeczywistym użyciem na dyskach P30.
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Uwagi dotyczące zakupu

Podczas rozważania zakupu rezerwacji dysku zalecamy stosowanie następujących metod:

- Analizuj informacje o użyciu, aby pomóc w ustaleniu, które rezerwacje należy zakupić. Należy pamiętać o śledzeniu użycia w jednostkach SKU dysku zamiast zainicjowanej lub zajętej pojemności dysku.
- Sprawdź rezerwację dysku wraz z rezerwacją maszyny wirtualnej. Zdecydowanie zalecamy, aby rezerwacje dotyczące użycia maszyn wirtualnych i użycia dysku były maksymalne. Możesz rozpocząć od określenia odpowiedniej rezerwacji maszyny wirtualnej, a następnie oszacować rezerwację dysku. Ogólnie rzecz biorąc, będziesz mieć standardową konfigurację dla każdego z obciążeń. Na przykład serwer SQL Server może mieć dwa dyski danych P40 i jeden dysk systemu operacyjnego P30.
  
  Ten rodzaj wzorca może pomóc w ustaleniu zarezerwowanej ilości, którą można zakupić. Takie podejście może uprościć proces oceny i upewnić się, że masz wyrównany plan dla maszyny wirtualnej i dysków. Plan zawiera zagadnienia, takie jak subskrypcje lub regiony.

## <a name="purchase-restrictions"></a>Ograniczenia zakupu

Rabaty rezerwacji są obecnie niedostępne dla następujących:

- Dyski niezarządzane lub stronicowe obiekty blob.
- Standardowe dysków SSD dyski twarde (HDD).
- SSD w warstwie Premium jednostki SKU mniejsze niż P30: P1, P2, P3, P4, P6, P10, P15 i P20 SSD SKU.
- Dyski w Azure Government, na platformie Azure (Niemcy) lub na platformie Azure (Chiny).

W rzadkich przypadkach platforma Azure ogranicza zakup nowych rezerwacji do podzbioru jednostek SKU dysku z powodu niskiej wydajności w regionie.

## <a name="buy-a-disk-reservation"></a>Kupowanie rezerwacji dysku

Rezerwacje Azure Disk Storage można zakupić za pomocą [Azure Portal](https://portal.azure.com/). Płatność za rezerwację można wykonać z góry lub w przypadku płatności miesięcznych. Aby uzyskać więcej informacji na temat kupowania miesięcznych płatności, zobacz [kupowanie rezerwacji z płatnościami miesięcznymi](../cost-management-billing/reservations/prepare-buy-reservation.md#buy-reservations-with-monthly-payments).

Wykonaj następujące kroki, aby zakupić zastrzeżoną pojemność:

1. Przejdź do okienka [rezerwacje zakupów](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) w Azure Portal.

1. Wybierz pozycję **Azure Managed disks** , aby kupić rezerwację.

    ![Okienko do kupowania rezerwacji](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. Określ wymagane wartości opisane w poniższej tabeli:

   |Element  |Opis  |
   |---------|---------|
   |**Zakres**   |  Liczby subskrypcji mogą korzystać z zalet rozliczeń skojarzonych z rezerwacją. Ta wartość określa również sposób zastosowania rezerwacji do określonych subskrypcji. <br/><br/> W przypadku wybrania opcji **udostępnione** rabat zostanie zastosowany do pojemności usługi Azure Storage w każdej subskrypcji w kontekście rozliczania. Kontekst rozliczania jest oparty na sposobie rejestracji w usłudze Azure. W przypadku klientów korporacyjnych zakresem udostępnionym jest rejestracja i uwzględnianie wszystkich subskrypcji w ramach rejestracji. W przypadku klientów z systemem płatność zgodnie z rzeczywistym użyciem zakres udostępniony obejmuje wszystkie poszczególne subskrypcje z opłatami z rabatem zgodnie z rzeczywistym użyciem utworzonymi przez administratora konta.  <br/><br/>  W przypadku wybrania opcji **Pojedyncza subskrypcja** rabat zostanie zastosowany do pojemności usługi Azure Storage w ramach wybranej subskrypcji. <br/><br/> W przypadku wybrania opcji **pojedynczy zasób** rabat zostanie zastosowany do pojemności usługi Azure Storage w ramach wybranej subskrypcji i grupy zasobów wybranej subskrypcji. <br/><br/> Można zmienić zakres rezerwacji po zakupie rezerwacji.  |
   |**Subskrypcja**  | Subskrypcja używana do płacenia za rezerwację usługi Azure Storage. Metoda płatności w ramach wybranej subskrypcji jest używana w celu naliczania kosztów. Subskrypcja musi być jednym z następujących typów:<br/><ul><li> Umowa Enterprise (numery oferty MS-AZR-0017P i MS-AZR-0148P). W przypadku subskrypcji przedsiębiorstwa opłaty są naliczane od przedpłaty za subskrypcję platformy Azure (wcześniej nazywanej zobowiązaniem pieniężnym) lub za użycie.</li><br/><li>Indywidualna subskrypcja z stawką płatność zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P i MS-AZR-0023P). W przypadku pojedynczej subskrypcji z stawką płatności zgodnie z rzeczywistym użyciem opłaty są naliczane za kartę kredytową lub formę płatności faktury dla subskrypcji.</li></ul>    |
   | **Dyski** | Jednostka SKU, którą chcesz utworzyć. |
   | **Region** | Region, w którym rezerwacja jest obowiązująca. |
   | **Częstotliwość rozliczeń** | Jak często konto jest rozliczane dla rezerwacji. Opcje obejmują **co miesiąc** i z **góry**. |

    ![Okienko do wybierania produktu, który chcesz purchase.png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. Po określeniu wartości dla rezerwacji Azure Portal zostanie wyświetlony koszt. W portalu jest również pokazywana wartość procentowa rabatu w porównaniu z płatnościami zgodnie z rzeczywistym użyciem. Wybierz pozycję **dalej** , aby przejść do okienka **rezerwacje zakupu** .

1. W okienku **rezerwacje zakupu** możesz nazwać rezerwację i wybrać łączną liczbę rezerwacji, które chcesz wprowadzić. Liczba rezerwacji jest mapowana na liczbę dysków. Jeśli na przykład chcesz zarezerwować setki dysków, wprowadź wartość w polu **ilość** **100**.

1. Sprawdź łączny koszt rezerwacji.

    ![Okienko rezerwacje zakupów](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

Po zakupieniu rezerwacji zostanie ona automatycznie zastosowana do wszystkich istniejących zasobów Disk Storage, które pasują do postanowień rezerwacji. Jeśli nie utworzono jeszcze żadnych zasobów Disk Storage, rezerwacja ma zastosowanie zawsze, gdy utworzysz zasób pasujący do postanowień rezerwacji. W obu przypadkach termin rezerwacji rozpoczyna się natychmiast po pomyślnym zakupie.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

W ramach pewnych ograniczeń można anulować, wymienić lub refundować rezerwacje. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="expiration-of-a-reservation"></a>Wygaśnięcie rezerwacji

Po wygaśnięciu rezerwacji wszystkie Azure Disk Storage pojemności używanej w ramach tej rezerwacji są rozliczane według stawki płatności zgodnie z rzeczywistym użyciem. Rezerwacje nie są odnawiane automatycznie.

Otrzymasz powiadomienie e-mail za 30 dni przed wygaśnięciem rezerwacji i ponownym upływem daty wygaśnięcia. Aby nadal korzystać z zalet oszczędności kosztów rezerwacji, Odnów ją nie później niż data wygaśnięcia.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Co to są rezerwacje platformy Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Zapoznaj się ze sposobem stosowania rabatu rezerwacji do Azure Disk Storage](../cost-management-billing/reservations/understand-disk-reservations.md)