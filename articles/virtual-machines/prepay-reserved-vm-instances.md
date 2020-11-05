---
title: Przedpłata za usługę Azure Virtual Machines w celu oszczędności pieniędzy
description: Dowiedz się, jak kupić Azure Reserved Virtual Machine Instances, aby zaoszczędzić na kosztach obliczeniowych.
author: vikramdesai01
manager: vikramdesai01
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/30/2017
ms.author: vikdesai
ms.openlocfilehash: 5df56b304463ab981026fbe0c82d831d83d702c3
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93380267"
---
# <a name="save-costs-with-azure-reserved-vm-instances-linux"></a>Oszczędność kosztów dzięki Azure Reserved VM Instances (Linux)



Po zatwierdzeniu do wystąpienia zarezerwowanego maszyny wirtualnej platformy Azure Możesz zaoszczędzić pieniądze. Rabat rezerwacji jest automatycznie stosowany do liczby uruchomionych maszyn wirtualnych, które pasują do zakresu rezerwacji i atrybutów. Nie musisz przypisywać rezerwacji do maszyny wirtualnej, aby uzyskać rabaty. Zakup wystąpienia zarezerwowanego obejmuje tylko część obliczeniową użycia maszyny wirtualnej. W przypadku maszyn wirtualnych z systemem Windows licznik użycia jest podzielony na dwa oddzielne liczniki. Istnieje licznik obliczeniowy, który jest taki sam jak licznik systemu Linux i miernik adresów IP w systemie Windows. Opłaty, które zobaczysz po dokonaniu zakupu, dotyczą tylko kosztów obliczeń. Opłaty nie obejmują kosztów oprogramowania systemu Windows. Aby uzyskać więcej informacji o kosztach oprogramowania, zobacz [koszty oprogramowania nieuwzględnione w Azure Reserved VM Instances](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>Ustalanie odpowiedniego rozmiaru maszyny wirtualnej przed zakupem

Przed zakupieniem rezerwacji należy określić wymagany rozmiar maszyny wirtualnej. Poniższe sekcje ułatwią określenie odpowiedniego rozmiaru maszyny wirtualnej.

### <a name="use-reservation-recommendations"></a>Korzystanie z rekomendacji dotyczących rezerwacji

Możesz użyć zaleceń dotyczących rezerwacji, aby pomóc w ustaleniu zastrzeżeń, które należy zakupić.

- Zalecenia dotyczące zakupu i zalecana ilość są wyświetlane podczas zakupu wystąpienia zarezerwowanego maszyny wirtualnej w Azure Portal.
- Azure Advisor zapewnia rekomendacje zakupu dla poszczególnych subskrypcji.  
- Możesz użyć interfejsów API, aby uzyskać zalecenia dotyczące zakupu zarówno dla zakresu udostępnionego, jak i dla jednej subskrypcji. Aby uzyskać więcej informacji, zobacz [interfejsy API rekomendacji wystąpień zarezerwowanych dla klientów korporacyjnych](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- W przypadku klientów z Umowa Enterprise (EA) i Microsoft Customer Agreement (MCA) można zakupić zalecenia dotyczące współdzielonych i pojedynczych zakresów subskrypcji, korzystając z [pakietu zawartości Azure Consumption Insights Power BI](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Usługi, dla których są stosowane rabaty na rezerwacje maszyn wirtualnych

Rezerwacje maszyn wirtualnych mogą dotyczyć użycia maszyn wirtualnych emitowanego przez różne usługi — nie tylko w przypadku wdrożeń maszyn wirtualnych. Zasoby, dla których są stosowane rabaty na rezerwację, zmieniają się w zależności od ustawienia elastyczności rozmiaru wystąpienia.

#### <a name="instance-size-flexibility-setting"></a>Ustawienie elastyczności rozmiaru wystąpienia

Ustawienie elastyczności rozmiaru wystąpienia określa, dla których usług są stosowane rabaty na wystąpienia zarezerwowane.

Niezależnie od tego, czy to ustawienie jest włączone czy wyłączone, rabaty na rezerwację są automatycznie stosowane do każdego pasującego użycia maszyn wirtualnych, dla którego wartość *ConsumedService* to `Microsoft.Compute`. Należy zatem sprawdzić dane użycia dla wartości *ConsumedService*. Oto niektóre przykłady:

- Maszyny wirtualne
- Zestawy skalowania maszyn wirtualnych
- Usługa kontenera
- Wdrożenia usługi Azure Batch (w trybie subskrypcji użytkownika)
- Azure Kubernetes Service (AKS)
- Service Fabric

Jeśli to ustawienie jest włączone, rabaty na rezerwację są automatycznie stosowane do pasującego użycia maszyn wirtualnych, które ma dowolną z następujących wartości *ConsumedService* :

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Sprawdź wartość *ConsumedService* w danych użycia, aby ustalić, czy użycie kwalifikuje się do zastosowania rabatów na rezerwację.

Aby uzyskać więcej informacji o elastyczności rozmiaru wystąpienia, zobacz artykuł [Elastyczność rozmiaru maszyny wirtualnej z zarezerwowanymi wystąpieniami maszyn wirtualnych](reserved-vm-instance-size-flexibility.md).

### <a name="analyze-your-usage-information"></a>Analizowanie informacji o użyciu

Analizuj informacje o użyciu, aby pomóc w ustaleniu, które rezerwacje należy zakupić. Dane użycia są dostępne w pliku użycia i interfejsach API. Należy używać ich razem w celu określenia rezerwacji do zakupu. Sprawdź wystąpienia maszyn wirtualnych o wysokim poziomie użycia codziennie, aby określić liczbę rezerwacji do zakupu. Należy unikać `Meter` podkategorii i `Product` pól w danych użycia. Nie rozróżniają one rozmiarów maszyn wirtualnych korzystających z magazynu Premium Storage. Jeśli te pola są używane do określenia rozmiaru maszyny wirtualnej dla zakupu rezerwacji, można zakupić niewłaściwy rozmiar. Nie zostanie wyświetlony oczekiwany rabat rezerwacji. Zamiast tego zapoznaj się z `AdditionalInfo` polem w pliku użycia lub interfejsie API użycia, aby określić prawidłowy rozmiar maszyny wirtualnej.

Twój plik użycia pokazuje opłaty według okresu rozliczeniowego i dziennego użycia. Aby uzyskać informacje na temat pobierania pliku użycia, zobacz [Wyświetlanie i pobieranie użycia platformy Azure oraz opłat](../cost-management-billing/understand/download-azure-daily-usage.md). Następnie za pomocą informacji o pliku użycia można [określić, jakie rezerwacji należy kupić](../cost-management-billing/reservations/determine-reservation-purchase.md).

### <a name="purchase-restriction-considerations"></a>Zagadnienia związane z ograniczeniami zakupu

Wystąpienia zarezerwowane maszyn wirtualnych są dostępne w przypadku większości niektórych wyjątków maszyn wirtualnych. Rabaty zarezerwowane nie dotyczą następujących maszyn wirtualnych:

- Seria **maszyn wirtualnych** — seria A, Av2 lub seria G.

- **Wersja zapoznawcza lub promocja maszyn wirtualnych** — wszystkie maszyny wirtualne lub rozmiary, które są w wersji zapoznawczej lub wykorzystują miernik promocyjny.

- **Chmury** — rezerwacje nie są dostępne do zakupu w regionach Niemcy i Chiny.

- **Niewystarczające limity przydziału** — rezerwacja w zakresie jednej subskrypcji musi mieć przydziały vCPU dostępne w subskrypcji dla nowego wystąpienia RI. Jeśli na przykład subskrypcja docelowa ma limit przydziału równy 10 procesorów wirtualnych vCPU dla serii D, nie można zakupić rezerwacji dla 11 Standard_D1 wystąpień. Sprawdzanie przydziału dla rezerwacji obejmuje maszyny wirtualne już wdrożone w subskrypcji. Na przykład jeśli subskrypcja ma przydział 10 procesorów wirtualnych vCPU dla serii D i ma dwa wystąpienia standard_D1 wdrożonych, można kupić rezerwację dla 10 standard_D1 wystąpień w tej subskrypcji. Aby rozwiązać ten problem, można [utworzyć żądanie zwiększenia oferty](../azure-portal/supportability/resource-manager-core-quotas-request.md) .

- **Ograniczenia pojemności** — w rzadkich przypadkach platforma Azure ogranicza zakup nowych rezerwacji dla podzbioru rozmiarów maszyn wirtualnych z powodu niskiej wydajności w regionie.

## <a name="buy-a-reserved-vm-instance"></a>Kupowanie wystąpienia zarezerwowanego maszyny wirtualnej

Możesz kupić wystąpienie zarezerwowane maszyny wirtualnej w [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Płatność za rezerwację jest wnoszona [z góry lub w ratach miesięcznych](../cost-management-billing/reservations/monthly-payments-reservations.md).
Te wymagania dotyczą kupowania zarezerwowanych wystąpień maszyn wirtualnych:

- Musisz mieć rolę właściciela dla co najmniej jednej subskrypcji EA lub subskrypcji z stawką płatność zgodnie z rzeczywistym użyciem.
- W przypadku subskrypcji z umową EA należy włączyć opcję **Dodaj wystąpienia zarezerwowane** w [portalu EA](https://ea.azure.com/). Jeśli to ustawienie jest wyłączone, wymagane są uprawnienia administratora EA dla subskrypcji.
- W przypadku programu Cloud Solution Provider (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą kupować rezerwacje.

Aby kupić wystąpienie:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **Wszystkie usługi** > **Rezerwacje**.
1. Wybierz pozycję **Dodaj** , aby zakupić nową rezerwację, a następnie kliknij pozycję **Maszyna wirtualna**.
1. Podaj wartości w wymaganych polach. Uruchomione wystąpienia maszyn wirtualnych pasujące do wybranych atrybutów kwalifikują się do uzyskania rabatu rezerwacji. Rzeczywista liczba wystąpień maszyn wirtualnych objętych rabatem zależy od wybranego zakresu i wybranej ilości.

Jeśli masz umowę EA, możesz użyć **opcji Dodaj więcej** , aby szybko dodać kolejne wystąpienia. Opcja jest niedostępna dla innych typów subskrypcji.


| Pole      | Opis|
|------------|--------------|
|Subskrypcja|Subskrypcja używana do płacenia za rezerwację. Kosztami rezerwacji jest obciążana forma płatności za subskrypcję. Typ subskrypcji musi być umową Enterprise Agreement (Numer oferty: MS-AZR-0017P lub MS-AZR-0148P) lub umowa klienta firmy Microsoft lub indywidualna subskrypcja z stawką płatność zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P). Opłaty są odliczane od salda zobowiązania pieniężnego (jeśli jest dostępne) lub naliczane jako nadwyżka. W przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty są naliczane za kartę kredytową lub formę płatności faktury dla subskrypcji.|    
|Zakres       |Zakres rezerwacji może obejmować jedną subskrypcję lub wiele subskrypcji (zakres udostępniony). W przypadku wybrania opcji: <ul><li>**Zakres pojedynczej grupy zasobów** — rabat na rezerwację jest stosowany do odpowiednich zasobów tylko w wybranej grupie zasobów.</li><li>**Zakres pojedynczej subskrypcji** — rabat na rezerwację jest stosowany do odpowiednich zasobów w wybranej subskrypcji.</li><li>**Zakres udostępniony** — rabat na rezerwację jest stosowany do odpowiednich zasobów w kwalifikujących się subskrypcjach w ramach kontekstu rozliczeń. W przypadku klientów z umowami EA kontekst rozliczania to rejestracja. W przypadku indywidualnych subskrypcji ze stawkami płatności zgodnie z rzeczywistym użyciem kontekst rozliczeń stanowią wszystkie kwalifikujące się subskrypcje utworzone przez administratora konta.</li></ul>|
|Region (Region)    |Region świadczenia usługi Azure objęty rezerwacją.|    
|Rozmiar maszyny wirtualnej     |Rozmiar wystąpień maszyn wirtualnych.|
|Optymalizuj pod kątem     |Domyślnie wybrana jest elastyczność rozmiaru wystąpienia maszyny wirtualnej. Kliknij pozycję **Ustawienia zaawansowane** , aby zmienić elastyczność rozmiaru wystąpienia, aby zastosować rabat rezerwacji do innych maszyn wirtualnych w tej samej [grupie rozmiarów maszyn wirtualnych](reserved-vm-instance-size-flexibility.md). Priorytet pojemności powoduje przydzielanie priorytetów pojemności centrum danych dla wdrożeń. Oferuje ona dodatkowe zaufanie do uruchamiania wystąpień maszyn wirtualnych, gdy ich potrzebujesz. Priorytet pojemności jest dostępny tylko wtedy, gdy zakresem rezerwacji jest pojedyncza subskrypcja. |
|Termin        |Jeden rok lub trzy lata. Okres 5-letni jest dostępny tylko dla maszyn wirtualnych HBv2.|
|Liczba    |Liczba wystąpień zakupionych w ramach rezerwacji. Ilość to liczba uruchomionych wystąpień maszyn wirtualnych, które mogą uzyskać rabat rozliczeń. Jeśli na przykład w regionie Wschodnie stany USA są uruchomione 10 Standard_D2 maszyn wirtualnych, należy określić liczbę jako 10, aby zmaksymalizować korzyść dla wszystkich uruchomionych maszyn wirtualnych. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Dane użycia i użycie rezerwacji

Dane użycia mają wynikową cenę równą zero za użycie, którego dotyczy rabat rezerwacji. Można sprawdzić, które wystąpienie maszyny wirtualnej otrzymało rabat rezerwacji dla każdej rezerwacji.

Aby uzyskać więcej informacji na temat sposobu wyświetlania rabatów rezerwacji w danych użycia, zobacz [Opis użycia usługi Azure Reservation na potrzeby rejestracji w przedsiębiorstwie](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) , jeśli jesteś klientem z umową EA. Jeśli masz pojedynczą subskrypcję, zobacz [Opis użycia usługi Azure Reservation dla subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Zmiana rezerwacji po zakupie

W zakupionej rezerwacji można wprowadzać następujące zmiany:

- Aktualizacja zakresu rezerwacji
- Elastyczność rozmiaru wystąpienia (jeśli dotyczy)
- Własność

Można również podzielić rezerwację na mniejsze fragmenty i scalić już rezerwacje. Żadna ze zmian nie powoduje nowej transakcji komercyjnej lub zmiana daty zakończenia rezerwacji.

Po zakupie nie można wprowadzać następujących typów zmian bezpośrednio:

- Istniejący region rezerwacji
- Jednostka SKU
- Liczba
- Czas trwania

Istnieje jednak możliwość *wymiany* rezerwacji, jeśli chcesz wprowadzić zmiany.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak zarządzać wystąpieniem zarezerwowanym, zobacz temat [Manage Azure Reservations](../cost-management-billing/reservations/manage-reserved-vm-instance.md) (Zarządzanie wystąpieniami zarezerwowanymi na platformie Azure).
- Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:
    - [Co to są rezerwacje platformy Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
    - [Zarządzanie rezerwacjami na platformie Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
    - [Jak jest stosowany rabat na rezerwacje](../cost-management-billing/manage/understand-vm-reservation-charges.md)
    - [Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
    - [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    - [Koszty oprogramowania systemu Windows nieuwzględniane w przypadku wystąpień zarezerwowanych](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)
    - [Rezerwacje platformy Azure w programie Cloud Solution Provider w Centrum partnerskim](https://docs.microsoft.com/partner-center/azure-reservations)


