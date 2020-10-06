---
title: Kupowanie rezerwacji platformy Azure
description: Uzyskaj informacje na temat ważnych kwestii, które ułatwią Ci zakup rezerwacji platformy Azure.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: d22ab9f028f0491db33d62d0a6dff8d06282f925
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91368723"
---
# <a name="buy-a-reservation"></a>Kupowanie rezerwacji

Rezerwacje platformy Azure pomagają zaoszczędzić pieniądze dzięki rocznym lub 3-letnim planom dla wielu zasobów platformy Azure. Przed zobowiązaniem się do zakupu rezerwacji zapoznaj się z poniższymi sekcjami, aby przygotować się do zakupu.

## <a name="who-can-buy-a-reservation"></a>Kto może kupić rezerwację

Aby kupić plan, musisz mieć rolę właściciela subskrypcji w subskrypcji Enterprise (MS-AZR-0017P lub MS-AZR-0148P), subskrypcji z płatnością zgodnie z rzeczywistym użyciem (MS-AZR-0003P lub MS-AZR-0023P) albo subskrypcji w ramach umowy klienta firmy Microsoft. Dostawy rozwiązań w chmurze mogą użyć witryny Azure Portal lub [Centrum partnerskiego](/partner-center/azure-reservations), aby zakupić rezerwacje platformy Azure.

Klienci korzystający z umowy Enterprise Agreement (EA) mogą ograniczyć możliwość zakupów tylko do administratorów EA, wyłączając opcję **Dodaj wystąpienia zarezerwowane** w portalu EA. Administrator EA musi być właścicielem co najmniej jednej subskrypcji EA, aby mieć możliwość zakupu rezerwacji. Ta opcja jest przydatna w przypadku przedsiębiorstw, które chcą kupować rezerwacje dla różnych centrów kosztów za pośrednictwem centralnego zespołu. Po zakupie centralne zespoły mogą dodawać do rezerwacji właścicieli centrów kosztów. Właściciele mogą następnie ograniczyć rezerwację do swoich subskrypcji. Centralny zespół nie musi mieć dostępu właściciela subskrypcji do obszaru, w którym zakupiono rezerwację.

Rabat na rezerwację dotyczy tylko zasobów skojarzonych z subskrypcjami zakupionymi w ramach umowy Enterprise, subskrypcji dostawcy usług w chmurze, umowy klienta firmy Microsoft i planów indywidualnych ze stawkami płatności zgodnie z rzeczywistym użyciem.

## <a name="scope-reservations"></a>Określanie zakresu rezerwacji

Zakres rezerwacji można ograniczyć do subskrypcji lub grup zasobów. Ustawienie zakresu rezerwacji określa, gdzie wystąpią oszczędności z rezerwacji. W przypadku określenia zakresu rezerwacji jako grupa zasobów rabaty na rezerwację dotyczą tylko tej grupy zasobów — a nie całej subskrypcji.

### <a name="reservation-scoping-options"></a>Opcje określania zakresu rezerwacji

Masz trzy opcje zakresu rezerwacji do wyboru w zależności od potrzeb:

- **Zakres pojedynczej grupy zasobów**— rabat na rezerwację jest stosowany do odpowiednich zasobów tylko w wybranej grupie zasobów.
- **Zakres pojedynczej subskrypcji**— rabat na rezerwację jest stosowany do odpowiednich zasobów w wybranej subskrypcji.
- **Zakres udostępniony** — rabat na rezerwację jest stosowany do odpowiednich zasobów w kwalifikujących się subskrypcjach w ramach kontekstu rozliczeń.
    - W przypadku klientów korzystających z umowy Enterprise Agreement kontekstem rozliczeń jest rejestracja. Zakres udostępniony rezerwacji obejmowałby wiele dzierżaw usługi Active Directory w rejestracji.
    - W przypadku klientów z Umową z Klientem Microsoft zakres rozliczeniowy to profil rozliczeniowy.
    - W przypadku indywidualnych subskrypcji ze stawkami płatności zgodnie z rzeczywistym użyciem kontekst rozliczeń stanowią wszystkie kwalifikujące się subskrypcje utworzone przez administratora konta.

Podczas stosowania rabatów na rezerwację w stosunku do użycia platforma Azure przetwarza rezerwacje w następującej kolejności:

1. Rezerwacje, których zakres jest ograniczony do grupy zasobów
2. Rezerwacje pojedynczego zakresu
3. Rezerwacje zakresu udostępnionego

Zakres można zaktualizować w dowolnym momencie po zakupie rezerwacji. W tym celu należy przejść do rezerwacji, kliknąć pozycję **Konfiguracja** i ponownie określić zakres. Zmiana zakresu rezerwacji nie jest transakcją handlową. Okres rezerwacji nie jest zmieniany. Aby uzyskać więcej informacji o aktualizowaniu zakresu, [zobacz Aktualizowanie zakresu po zakupie rezerwacji.](manage-reserved-vm-instance.md#change-the-reservation-scope)

![Przykład pokazujący zmianę zakresu rezerwacji](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="discounted-subscription-and-offer-types"></a>Subskrypcje z rabatem i typy ofert

Rabaty na rezerwacje dotyczą następujących kwalifikujących się subskrypcji i typów ofert.

- Umowy Enterprise Agreement (oferty o numerach: MS-AZR-0017P i MS-AZR-0148P)
- Subskrypcje w ramach umów klienta firmy Microsoft
- Indywidualne plany ze stawką płatności zgodnie z rzeczywistym użyciem (oferty o numerach: MS-AZR-0003P i MS-AZR-0023P)
- Subskrypcje CSP

Zasoby działające w ramach subskrypcji z innymi typami ofert nie otrzymują rabatu na rezerwację.

## <a name="purchase-reservations"></a>Zakup rezerwacji

Rezerwacje można kupić w witrynie Azure Portal, interfejsach API, programie PowerShell i interfejsie wiersza polecenia. Przeczytaj następujące artykuły, które Cię dotyczą, gdy wszystko będzie gotowe do zakupu rezerwacji:

- [App Service](prepay-app-service-isolated-stamp.md)
- [Azure Cache for Redis](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [Data Explorer](/azure/data-explorer/pricing-reserved-capacity)
- [Disk Storage](../../virtual-machines/disks-reserved-capacity.md)
- [Dedicated Host](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [Plany oprogramowania](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Storage](../../storage/blobs/storage-blob-reserved-capacity.md)
- [SQL Database](../../azure-sql/database/reserved-capacity-overview.md)
- [Azure Database for PostgreSQL](../../postgresql/concept-reserved-pricing.md)
- [Azure Database for MySQL](../../mysql/concept-reserved-pricing.md)
- [Azure Database for MariaDB](../../mariadb/concept-reserved-pricing.md)
- [Azure Synapse Analytics](prepay-sql-data-warehouse-charges.md)
- [Maszyny wirtualne](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

## <a name="buy-reservations-with-monthly-payments"></a>Kupowanie rezerwacji platformy Azure z płatnościami miesięcznymi

Możesz płacić za rezerwacje przy użyciu płatności miesięcznych. W przeciwieństwie do zakupu z góry, gdy płacisz pełną kwotę, opcja płatności miesięcznych umożliwia równe podzielenie łącznego kosztu rezerwacji między wszystkie miesiące w danym okresie. Łączny koszt rezerwacji w przypadku płatności z góry i miesięcznych rat jest taki sam, a wybór płatności miesięcznych nie pociąga za sobą dodatkowych opłat.

Jeśli rezerwacja została zakupiona w ramach umowy klienta firmy Microsoft, miesięczna kwota płatności może różnić się w zależności od obowiązującego w danym miesiącu rynkowego kursu wymiany waluty lokalnej.

Miesięczne płatności nie są dostępne w przypadku następujących rozwiązań: Databricks, rezerwacje systemu SUSE Linux, plany oprogramowania Red Hat i licencje Azure Red Hat OpenShift.

### <a name="view-payments-made"></a>Wyświetlanie dokonanych płatności

Możesz wyświetlić płatności, które zostały dokonane przy użyciu interfejsów API, danych użycia i analizy kosztów. W przypadku rezerwacji płaconych miesięcznie wartość częstotliwości jest pokazywana jako **cykliczne** w przypadku danych użycia i interfejsu API opłat za rezerwację. W przypadku rezerwacji zapłaconych z góry wartość jest wyświetlana jako **jednorazowa**.

Analiza kosztów pokazuje w widoku domyślnym zakupy miesięczne. Zastosuj filtr **zakup** w polu **Typ opłaty** i **cykliczne** w polu **Częstotliwość**, aby zobaczyć wszystkie zakupy. Aby wyświetlić tylko rezerwacje, zastosuj filtr **Rezerwacja**.

![Przykład przedstawiający koszty zakupu rezerwacji w analizie kosztów](./media/prepare-buy-reservation/cost-analysis.png)

### <a name="exchange-and-refunds"></a>Wymiana i zwroty

Podobnie jak w przypadku innych rezerwacji, można uzyskiwać zwroty rezerwacji zakupionych w ramach rozliczeń miesięcznych lub wymieniać te rezerwacje. 

Podczas wymiany rezerwacji opłacanej miesięcznie łączny koszt okresu istnienia nowego zakupu powinien być większy niż pozostałe płatności, które są anulowane dla zwróconej rezerwacji. Nie istnieją inne limity ani opłaty dotyczące wymian. Możesz wymienić rezerwację płatną z góry, aby kupić nową rezerwację, która jest rozliczana miesięcznie. Jednak wartość okresu istnienia nowej rezerwacji powinna być większa niż proporcjonalna wartość zwracanej rezerwacji.

Jeśli anulujesz rezerwację opłacaną miesięcznie, anulowane przyszłe płatności są wliczane do limitu zwrotu wynoszącego 50 000 USD.

Aby uzyskać więcej informacji o wymianach i zwrotach, zobacz temat [Self-service exchanges and refunds for Azure Reservations](exchange-and-refund-azure-reservations.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="reservation-notifications"></a>Powiadomienia dotyczące rezerwacji

W zależności od tego, jak płacisz za subskrypcję platformy Azure, powiadomienia o rezerwacji są wysyłane pocztą e-mail do następujących użytkowników w organizacji. Wysyłane są powiadomienia dotyczące różnych zdarzeń, takich jak: 

- Zakup
- Zbliżające się wygaśnięcie rezerwacji
- Wygaśnięcie
- Odnowienie
- Anulowanie
- Zmiana zakresu

W przypadku klientów z subskrypcjami EA:

- Powiadomienia są wysyłane tylko do kontaktów dla powiadomień ustawionych w ramach umowy EA.
- Użytkownicy dodani do rezerwacji przy użyciu uprawnienia RBAC platformy Azure (Zarządzanie dostępem i tożsamościami) nie otrzymują żadnych powiadomień e-mail.

W przypadku klientów z subskrypcjami indywidualnymi:

- Kupujący otrzymuje powiadomienie o zakupie.
- W momencie zakupu właściciel konta rozliczenia subskrypcji otrzymuje powiadomienie o zakupie.
- Właściciel konta otrzymuje wszystkie inne powiadomienia.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie rezerwacjami zasobów platformy Azure](manage-reserved-vm-instance.md)
