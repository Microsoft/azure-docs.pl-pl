---
title: Przedpłata za dedykowane hosty platformy Azure, aby zaoszczędzić pieniądze
description: Dowiedz się, jak kupić wystąpienia zarezerwowane dedykowanych hostów platformy Azure, aby zaoszczędzić na kosztach obliczeniowych.
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: d7af95b9fe2f6d31faa239985f8e8165fd968372
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978598"
---
# <a name="save-costs-with-azure-dedicated-host-reservations"></a>Oszczędność kosztów dzięki dedykowanym zastrzeżeń hosta na platformie Azure

Po zatwierdzeniu do zarezerwowanego wystąpienia dedykowanych hostów platformy Azure Możesz zaoszczędzić pieniądze. Rabat rezerwacji jest automatycznie stosowany do liczby uruchomionych dedykowanych hostów, które pasują do zakresu rezerwacji i atrybutów. Nie musisz przypisywać rezerwacji do dedykowanego hosta, aby uzyskać rabaty. Zakup wystąpienia zarezerwowanego obejmuje tylko część obliczeniową użycia i zawiera koszty licencjonowania oprogramowania. Zapoznaj się z [omówieniem dedykowanych hostów platformy Azure dla maszyn wirtualnych](./dedicated-hosts.md).

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>Przed zakupem Określ odpowiednią dedykowaną jednostkę SKU hosta


Przed zakupieniem rezerwacji należy określić dedykowanego hosta, którego potrzebujesz. Jednostka SKU jest definiowana dla dedykowanego hosta reprezentującego serię maszyn wirtualnych i typ. 

Zacznij od przechodzenia przez obsługiwane rozmiary [maszyny wirtualnej z systemem Windows](./sizes.md) lub [Linux](./sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , aby zidentyfikować serię maszyn wirtualnych.

Następnie sprawdź, czy jest on obsługiwany na dedykowanych hostach platformy Azure. Na stronie [cennika dedykowanych hostów platformy Azure](https://aka.ms/ADHPricing) znajduje się kompletna lista jednostek SKU dedykowanych hostów, ich informacji o procesorach i różnych opcji cenowych (w tym wystąpień zarezerwowanych).

Możesz znaleźć kilka jednostek SKU obsługujących serię maszyn wirtualnych (z różnymi typami). Zidentyfikuj najlepszą jednostkę SKU, porównując pojemność hosta (liczbę procesorów wirtualnych vCPU). Należy pamiętać, że będzie można zastosować rezerwację do wielu jednostek SKU dedykowanych hostów obsługujących tę samą serię maszyn wirtualnych (na przykład DSv3_Type1 i DSv3_Type2), ale nie między różnymi seriami maszyn wirtualnych (np. DSv3 i ESv3).



## <a name="purchase-restriction-considerations"></a>Zagadnienia związane z ograniczeniami zakupu

Wystąpienia zarezerwowane są dostępne dla większości dedykowanych rozmiarów hosta z pewnymi wyjątkami.

Rabaty rezerwacji nie mają zastosowania w przypadku następujących czynności:

- **Chmury**   -Rezerwacje nie są dostępne do zakupu w regionach Niemcy i Chiny.

- **Niewystarczające limity przydziału**   -Rezerwacja, która jest objęta zakresem jednej subskrypcji, musi mieć przydziały vCPU dostępne w subskrypcji dla nowego wystąpienia zarezerwowanego. Jeśli na przykład subskrypcja docelowa ma limit przydziału równy 10 procesorów wirtualnych vCPU dla serii DSv3, nie można zakupić dedykowanych hostów zastrzeżeń obsługujących tę serię. Sprawdzanie przydziału dla rezerwacji obejmuje maszyny wirtualne i hosty dedykowane już wdrożone w subskrypcji. Aby rozwiązać ten problem, można [utworzyć żądanie zwiększenia limitu przydziału](../azure-portal/supportability/resource-manager-core-quotas-request.md)   .

- **Ograniczenia pojemności**   W rzadkich przypadkach platforma Azure ogranicza zakup nowych rezerwacji dla podzbioru dedykowanych jednostek SKU hosta z powodu niskiej wydajności w regionie.

## <a name="buy-a-reservation"></a>Kupowanie rezerwacji

Można zakupić zarezerwowane wystąpienie dedykowanego wystąpienia hosta platformy Azure w [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Płatność za rezerwację z [góry lub miesięczna płatność](../cost-management-billing/reservations/prepare-buy-reservation.md). Te wymagania dotyczą kupowania zastrzeżonego dedykowanego wystąpienia hosta:

- Musisz mieć rolę właściciela dla co najmniej jednej subskrypcji EA lub subskrypcji z stawką płatność zgodnie z rzeczywistym użyciem.

- W przypadku subskrypcji z umową EA należy włączyć opcję **Dodaj wystąpienia zarezerwowane**   w [portalu EA](https://ea.azure.com/). Jeśli to ustawienie jest wyłączone, wymagane są uprawnienia administratora EA dla subskrypcji.

- W przypadku programu Cloud Solution Provider (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą kupować rezerwacje.

Aby kupić wystąpienie:

1. Zaloguj się do witryny  [Azure Portal](https://portal.azure.com/).

2. Wybierz pozycję **wszystkie**   \>  **rezerwacje**usług.

3. Wybierz pozycję **Dodaj**,   Aby zakupić nową rezerwację, a następnie kliknij pozycję **dedykowane hosty**.

4. Podaj wartości w wymaganych polach. Uruchomione wystąpienia dedykowanych hostów, które pasują do wybranych atrybutów, kwalifikują się do uzyskania rabatu rezerwacji. Rzeczywista liczba dedykowanych wystąpień hosta, które pobiera rabat, zależy od wybranego zakresu i ilości.

Jeśli masz umowę EA, możesz użyć **opcji Dodaj więcej**,   Aby szybko dodać kolejne wystąpienia. Opcja jest niedostępna dla innych typów subskrypcji.

| **Pole**           | **Opis**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Subskrypcja        | Subskrypcja używana do płacenia za rezerwację. Kosztami rezerwacji jest obciążana forma płatności za subskrypcję. Typ subskrypcji musi być umową Enterprise Agreement (Numer oferty: MS-AZR-0017P lub MS-AZR-0148P) lub umowa klienta firmy Microsoft lub indywidualna subskrypcja z stawką płatność zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P). Opłaty są odliczane od salda zobowiązania pieniężnego (jeśli jest dostępne) lub naliczane jako nadwyżka. W przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty są naliczane za kartę kredytową lub formę płatności faktury dla subskrypcji. |
| Zakres               | Zakres rezerwacji może obejmować jedną subskrypcję lub wiele subskrypcji (zakres udostępniony). W przypadku wybrania opcji:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Region              | Region świadczenia usługi Azure objęty rezerwacją.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Dedykowany rozmiar hosta | Rozmiar dedykowanych wystąpień hosta.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Okres                | Jeden rok lub trzy lata.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Liczba            | Liczba wystąpień zakupionych w ramach rezerwacji. Ilość to liczba uruchomionych dedykowanych wystąpień hosta, które mogą uzyskać rabat rozliczeń.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- Zakres pojedynczej **grupy zasobów**   — Stosuje rabat rezerwacji do zasobów pasujących tylko w wybranej grupie zasobów.

- Zakres pojedynczej **subskrypcji**   — Stosuje rabat rezerwacji do pasujących zasobów w wybranej subskrypcji.

- **Zakres udostępniony**   — Stosuje rabat rezerwacji do pasujących zasobów w uprawnionych subskrypcjach, które znajdują się w kontekście rozliczeń. W przypadku klientów z umowami EA kontekst rozliczania to rejestracja. W przypadku indywidualnych subskrypcji ze stawkami płatności zgodnie z rzeczywistym użyciem kontekst rozliczeń stanowią wszystkie kwalifikujące się subskrypcje utworzone przez administratora konta.

## <a name="usage-data-and-reservation-utilization"></a>Dane użycia i użycie rezerwacji

Dane użycia mają wynikową cenę równą zero za użycie, którego dotyczy rabat rezerwacji. Można sprawdzić, które wystąpienie maszyny wirtualnej otrzymało rabat rezerwacji dla każdej rezerwacji.

Aby uzyskać więcej informacji na temat sposobu wyświetlania rabatów rezerwacji w danych użycia, zobacz [Opis użycia usługi Azure Reservation na potrzeby rejestracji w przedsiębiorstwie](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md),   Jeśli jesteś klientem z umową EA. Jeśli masz pojedynczą subskrypcję, zobacz [Opis użycia usługi Azure Reservation dla subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Zmiana rezerwacji po zakupie

W zakupionej rezerwacji można wprowadzać następujące zmiany:

- Aktualizacja zakresu rezerwacji

- Elastyczność rozmiaru wystąpienia (jeśli dotyczy)

- Własność

Można również podzielić rezerwację na mniejsze fragmenty i scalić już rezerwacje. Żadna ze zmian nie powoduje nowej transakcji komercyjnej lub zmiana daty zakończenia rezerwacji.

Po zakupie nie można wprowadzać następujących typów zmian bezpośrednio:

- Istniejący region rezerwacji

- SKU

- Liczba

- Czas trwania

Istnieje jednak możliwość *wymiany*   rezerwacji, jeśli chcesz wprowadzić zmiany.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz samoobsługowe [weksle i zwroty dla Azure Reservations](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy,  [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zarządzać rezerwacją, zobacz [zarządzanie Azure Reservations](../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

- [Co to są rezerwacje platformy Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)

- [Using Azure Dedicated Hosts (Używanie hostów usługi Azure Dedicated Host)](./dedicated-hosts.md)

- [Dedicated Hosts Pricing (Ceny hostów usługi Azure Dedicated Host)](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Zarządzanie rezerwacjami na platformie Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)

- [Jak jest stosowany rabat na rezerwacje](../cost-management-billing/manage/understand-vm-reservation-charges.md)

- [Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)

- [Koszty oprogramowania systemu Windows nieuwzględniane w przypadku wystąpień zarezerwowanych](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)

- [Rezerwacje platformy Azure w programie Cloud Solution Provider w Centrum partnerskim](/partner-center/azure-reservations)