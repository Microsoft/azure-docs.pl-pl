---
title: Zarezerwowana pojemność w Azure Cosmos DB do optymalizowania kosztów
description: Dowiedz się, jak kupić Azure Cosmos DB zarezerwowaną pojemność, aby zaoszczędzić na kosztach obliczeniowych.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 27725b1a3dd6059010ce67977c39891a012c037e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097638"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Optymalizacja kosztów z użyciem pojemności zarezerwowanej w usłudze Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Pojemność zarezerwowana usługi Azure Cosmos DB pomaga zaoszczędzić pieniądze dzięki zobowiązaniu do korzystania z rezerwacji zasobów usługi Azure Cosmos DB przez jeden rok lub trzy lata. Dzięki pojemności zarezerwowanej usługi Azure Cosmos DB możesz uzyskać rabat na przepływność zaprowizowaną na potrzeby zasobów usługi Cosmos DB. Przykładami zasobów są bazy danych i kontenery (tabele, kolekcje i wykresy).

Azure Cosmos DB zarezerwowana pojemność może znacząco obniżyć koszty Cosmos DB &mdash; do 65% w regularnych cenach przy użyciu jednego roku lub trzech lat zobowiązania z góry. Zarezerwowana pojemność zapewnia rabat na rozliczenia i nie wpływa na stan czasu wykonywania zasobów Azure Cosmos DB.

Azure Cosmos DB zarezerwowana pojemność obejmuje przepływność zainicjowaną dla zasobów. Nie dotyczy opłat za magazyn i sieć. Po zakupieniu rezerwacji opłaty za przepływność, które pasują do atrybutów rezerwacji, nie są już naliczane według stawek płatności zgodnie z rzeczywistym użyciem. Aby uzyskać więcej informacji na temat rezerwacji, zobacz artykuł dotyczący [zastrzeżeń na platformie Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md) .

Możesz kupić Azure Cosmos DB zarezerwowaną pojemność z [Azure Portal](https://portal.azure.com). Płatność za rezerwację jest wnoszona [z góry lub w ratach miesięcznych](../cost-management-billing/reservations/prepare-buy-reservation.md). Aby kupić pojemność zarezerwowaną:

* Musisz być w roli właściciela dla co najmniej jednej subskrypcji przedsiębiorstwa lub indywidualnej, która ma stawki płatność zgodnie z rzeczywistym użyciem.  
* W przypadku subskrypcji Enterprise w witrynie [EA Portal](https://ea.azure.com) musi być włączona opcja **Dodaj wystąpienia zarezerwowane** . Jeśli to ustawienie jest wyłączone, musisz być administratorem EA w subskrypcji.
* W przypadku programu Cloud Solution Provider (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą kupować Azure Cosmos DB zarezerwowaną pojemność.

## <a name="determine-the-required-throughput-before-purchase"></a>Określ wymaganą przepływność przed zakupem

Rozmiar zakupu zarezerwowanej pojemności powinien opierać się na łącznej ilości przepływności, z której istniejące lub wkrótce wdrożone zasoby Azure Cosmos DB będą używane w godzinach. Na przykład: zakup 30 000 RU/s zarezerwowany, jeśli jest to spójny wzorzec użycia godzinowego. W tym przykładzie każda zainicjowana przepływność powyżej 30 000 RU/s będzie rozliczana według stawki płatności zgodnie z rzeczywistym użyciem. Jeśli przewidziana przepływność jest mniejsza niż 30 000 RU/s w ciągu godziny, zostanie zużyta dodatkowa pojemność zarezerwowana dla tej godziny.

Obliczamy zalecenia dotyczące zakupu w oparciu o wzorzec użycia godzinowego. Jest analizowane użycie w ciągu ostatnich 7, 30 i 60 dni, a zakup zarezerwowanej pojemności, która maksymalizuje oszczędności, jest zalecany. Zalecane rozmiary rezerwacji można wyświetlić w Azure Portal, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).  

2. Wybierz kolejno pozycje **wszystkie usługi**  >  **rezerwacje**  >  **Dodaj** .

3. W okienku **rezerwacje zakupu** wybierz pozycję **Azure Cosmos DB** .

4. Wybierz kartę **zalecaną** , aby wyświetlić zalecane rezerwacje:

Zalecenia można filtrować według następujących atrybutów:

- **Okres** (1 rok lub 3 lata)
- **Częstotliwość rozliczeń** (co miesiąc lub z góry)
- **Typ przepływności** (ru/s vs wieloregionowe zapis ru/s)

Ponadto możesz ograniczyć zakres zaleceń w ramach jednej grupy zasobów, pojedynczej subskrypcji lub całej rejestracji na platformie Azure. 

Oto przykładowe zalecenie:

:::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png" alt-text="Zalecenia dotyczące pojemności zastrzeżonej":::

To zalecenie zakupu 30 000 RU/s oznacza, że w ciągu 3-letnich rezerwacji rozmiar rezerwacji to 30 000 RU/s maksymalizuje oszczędności. W takim przypadku zalecenie jest obliczane na podstawie 30-dniowego użycia Azure Cosmos DB. Jeśli klient oczekuje, że w ciągu ostatnich 30 dni użytkowania Azure Cosmos DB będzie reprezentatywne w przyszłości, zwiększy oszczędności, kupując 30 000 rezerwacji RU/s.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Kupowanie Azure Cosmos DB zarezerwowanej pojemności

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).  

2. Wybierz kolejno pozycje **wszystkie usługi**  >  **rezerwacje**  >  **Dodaj** .  

3. W okienku **rezerwacje zakupu** wybierz **Azure Cosmos DB** , aby kupić nową rezerwację.  

4. Wypełnij pola wymagane zgodnie z opisem w poniższej tabeli:

   :::image type="content" source="./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png" alt-text="Zalecenia dotyczące pojemności zastrzeżonej":::

   |Pole  |Opis  |
   |---------|---------|
   |Zakres   |   Opcja, która kontroluje, ile subskrypcji może korzystać z zalet rozliczeń skojarzonych z rezerwacją. Kontroluje również sposób zastosowania rezerwacji do określonych subskrypcji. <br/><br/>  W przypadku wybrania opcji **udostępnione** rabat jest stosowany do Azure Cosmos DB wystąpień, które są uruchamiane w dowolnej subskrypcji w kontekście rozliczania. Kontekst rozliczania jest oparty na sposobie rejestracji w usłudze Azure. W przypadku klientów korporacyjnych zakresem udostępnionym jest rejestracja i uwzględnianie wszystkich subskrypcji w ramach rejestracji. W przypadku klientów z systemem płatność zgodnie z rzeczywistym użyciem zakresem udostępnionym są wszystkie indywidualne subskrypcje, które są tworzone przez administratora konta.  <br/><br/>  W przypadku wybrania opcji **Pojedyncza subskrypcja** rabat zostanie zastosowany do Azure Cosmos DB wystąpień w ramach wybranej subskrypcji. <br/><br/> W przypadku wybrania opcji **pojedyncze grupy zasobów** rabat rezerwacji zostanie zastosowany do Azure Cosmos DB wystąpień w ramach wybranej subskrypcji i wybranej grupy zasobów w ramach tej subskrypcji. <br/><br/> Można zmienić zakres rezerwacji po zakupie zarezerwowanej pojemności.  |
   |Subskrypcja  |   Subskrypcja, która jest używana do płacenia za Azure Cosmos DB zarezerwowaną pojemność. Metoda płatności w ramach wybranej subskrypcji jest używana w celu naliczania kosztów. Subskrypcja musi być jednym z następujących typów: <br/><br/>  Umowa Enterprise (numery ofert: MS-AZR-0017P lub MS-AZR-0148P): w przypadku subskrypcji przedsiębiorstwa opłaty są naliczane od salda zobowiązania pieniężnego rejestracji lub opłaty są naliczane jako nadwyżkowe. <br/><br/> Indywidualna subskrypcja z stawką płatności zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P): w przypadku pojedynczej subskrypcji z stawką płatności zgodnie z rzeczywistym użyciem opłaty są naliczane za kartę kredytową lub formę płatności faktury dla subskrypcji.    |
   | Grupa zasobów | Grupa zasobów, w której jest stosowany rabat zarezerwowanej pojemności. |
   |Termin  |   Jeden rok lub trzy lata.   |
   |Typ przepływności   |  Obsługa przepływności jest obsługiwana jako jednostki żądania. Możesz kupić rezerwację dla zainicjowanej przepływności dla obu konfiguracji — zapisu w jednym regionie, a także zapisów w wielu regionach. Typ przepływności ma dwie wartości do wyboru: 100 RU/s na godzinę i 100 wiele regionów zapisów RU/s na godzinę.|
   | Zarezerwowane jednostki pojemności| Ilość przepływności, która ma zostać zarezerwowana. Tę wartość można obliczyć, określając przepływność potrzebną dla wszystkich zasobów Cosmos DB (na przykład baz danych lub kontenerów) na region. Następnie można je pomnożyć przez liczbę regionów, które zostaną skojarzone z bazą danych Cosmos. Na przykład: Jeśli masz pięć regionów z 1 000 000 RU/s w każdym regionie, wybierz 5 000 000 RU/s na potrzeby zakupu zdolności rezerwacji. |


5. Po wypełnieniu formularza jest obliczana cena wymagana do zakupu zarezerwowanej pojemności. W danych wyjściowych jest również wyświetlany procent rabatu uzyskany z wybranymi opcjami. Następnie kliknij pozycję **Wybierz** .

6. W okienku **rezerwacje zakupu** Przejrzyj rabat i cenę rezerwacji. Ta cena rezerwacji dotyczy zasobów Azure Cosmos DB z obsługą przepływności we wszystkich regionach.  

   :::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png" alt-text="Zalecenia dotyczące pojemności zastrzeżonej":::

7. Wybierz pozycję **Recenzja + Kup** , a następnie **Kup teraz** . Po pomyślnym zakupie zostanie wyświetlona następująca strona:

Po zakupieniu rezerwacji zostanie ona natychmiast zastosowana do wszystkich istniejących zasobów Azure Cosmos DB, które pasują do warunków rezerwacji. Jeśli nie masz żadnych istniejących zasobów Azure Cosmos DB, rezerwacja zostanie zastosowana podczas wdrażania nowego wystąpienia Cosmos DB, które pasuje do warunków rezerwacji. W obu przypadkach okres rezerwacji rozpoczyna się natychmiast po pomyślnym zakupie.

Po wygaśnięciu rezerwacji Azure Cosmos DB wystąpienia będą nadal działać i są rozliczane według zwykłych stawek płatności zgodnie z rzeczywistym użyciem.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz temat [Self-service exchanges and refunds for Azure Reservations](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) (Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure).

## <a name="next-steps"></a>Następne kroki

Rabat rezerwacji jest automatycznie stosowany do zasobów Azure Cosmos DB, które pasują do zakresu rezerwacji i atrybutów. Zakres rezerwacji można zaktualizować za pomocą Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API.

*  Aby dowiedzieć się, jak zarezerwowane rabaty pojemności są stosowane do Azure Cosmos DB, zobacz [Opis rabatu rezerwacji platformy Azure](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md).

* Więcej szczegółów na temat rezerwacji platformy Azure można znaleźć w następujących artykułach:

   * [Co to są rezerwacje platformy Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [Zarządzanie rezerwacjami platformy Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [Rezerwacje platformy Azure w programie partnerskim programu Partner Center](/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).