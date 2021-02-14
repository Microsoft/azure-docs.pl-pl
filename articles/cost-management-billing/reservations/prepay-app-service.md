---
title: Oszczędzanie w usłudze Azure App Service za pomocą wydajności rezerwowej
description: Dowiedz się, w jaki sposób można zaoszczędzić koszty Azure App Service wystąpienia zarezerwowane Premium v3 i opłaty za wyizolowaną sygnaturę.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 02/01/2021
ms.author: banders
ms.custom: references_regions
ms.openlocfilehash: 92a315121ad8ae6fadcadbf6d531eb3e99ae69a9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374545"
---
# <a name="save-costs-with-azure-app-service-reserved-instances"></a>Oszczędzaj koszty za pomocą Azure App Service wystąpień zarezerwowanych

W tym artykule wyjaśniono, jak można zaoszczędzić przy użyciu Azure App Service wystąpień zarezerwowanych dla wystąpień Premium v3 i opłat za wydzielone sygnatury.

## <a name="save-with-premium-v3-reserved-instances"></a>Zapisz z wystąpieniami zarezerwowanymi Premium v3

Po zatwierdzeniu do wystąpienia zarezerwowanego Azure App Service Premium v3 możesz zaoszczędzić pieniądze. Rabat rezerwacji jest automatycznie stosowany do liczby uruchomionych wystąpień, które pasują do zakresu rezerwacji i atrybutów. Nie musisz przypisywać rezerwacji do wystąpienia, aby uzyskać rabaty.

## <a name="determine-the-right-reserved-instance-size-before-you-buy"></a>Określ odpowiedni rozmiar wystąpienia zarezerwowanego przed zakupem

Przed zakupieniem rezerwacji należy określić rozmiar wymaganego wystąpienia zarezerwowanego Premium v3. Poniższe sekcje ułatwią określenie rozmiaru zarezerwowanego wystąpienia Premium w wersji 3.

### <a name="use-reservation-recommendations"></a>Korzystanie z rekomendacji dotyczących rezerwacji

Możesz użyć zaleceń dotyczących rezerwacji, aby pomóc w ustaleniu zastrzeżeń, które należy zakupić.

- Zalecenia dotyczące zakupu i zalecana ilość są wyświetlane podczas zakupu wystąpienia zarezerwowanego Premium V3 w Azure Portal.
- Azure Advisor zapewnia rekomendacje zakupu dla poszczególnych subskrypcji.
- Możesz użyć interfejsów API, aby uzyskać zalecenia dotyczące zakupu zarówno dla zakresu udostępnionego, jak i dla jednej subskrypcji. Aby uzyskać więcej informacji, zobacz [interfejsy API rekomendacji wystąpień zarezerwowanych dla klientów korporacyjnych](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- W przypadku klientów z Enterprise Agreement (EA) i Microsoft Customer Agreement (MCA) można zakupić zalecenia dotyczące współdzielonych i pojedynczych zakresów subskrypcji, korzystając z [pakietu zawartości Azure Consumption Insights Power BI](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="analyze-your-usage-information"></a>Analizowanie informacji o użyciu

Analizuj informacje o użyciu, aby pomóc w ustaleniu, które rezerwacje należy zakupić. Dane użycia są dostępne w pliku użycia i interfejsach API. Należy używać ich razem w celu określenia rezerwacji do zakupu. Sprawdź wystąpienia Premium v3, które codziennie mają duże użycie, aby określić liczbę rezerwacji do zakupu.

Twój plik użycia pokazuje opłaty według okresu rozliczeniowego i dziennego użycia. Aby uzyskać informacje na temat pobierania pliku użycia, zobacz [Wyświetlanie i pobieranie użycia platformy Azure oraz opłat](../understand/download-azure-daily-usage.md). Następnie za pomocą informacji o pliku użycia można [określić, jakie rezerwacji należy kupić](determine-reservation-purchase.md).


## <a name="buy-a-premium-v3-reserved-instance"></a>Kupowanie zarezerwowanego wystąpienia Premium v3

W [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)można zakupić zarezerwowane wystąpienie Premium w wersji 3. Płatność za rezerwację jest wnoszona [z góry lub w ratach miesięcznych](prepare-buy-reservation.md). Te wymagania dotyczą kupowania zarezerwowanego wystąpienia Premium v3:

- Musisz mieć rolę właściciela dla co najmniej jednej subskrypcji EA lub subskrypcji z stawką płatność zgodnie z rzeczywistym użyciem.
- W przypadku subskrypcji z umową EA należy włączyć opcję **Dodaj wystąpienia zarezerwowane** w [portalu EA](https://ea.azure.com/). Jeśli to ustawienie jest wyłączone, wymagane są uprawnienia administratora EA dla subskrypcji.
- W przypadku programu Cloud Solution Provider (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą kupować rezerwacje.

Aby kupić wystąpienie:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **Wszystkie usługi** > **Rezerwacje**.
3. Wybierz pozycję **Dodaj** , aby zakupić nową rezerwację, a następnie kliknij pozycję **wystąpienie**.
4. Podaj wartości w wymaganych polach. Uruchomione wystąpienia zarezerwowane Premium v3, które pasują do wybranych atrybutów, kwalifikują się do rabatu rezerwacji. Rzeczywista liczba wystąpień zarezerwowanych Premium v3, które pobierają rabat, zależy od wybranego zakresu i ilości.

Jeśli masz umowę EA, możesz użyć **opcji Dodaj więcej** , aby szybko dodać kolejne wystąpienia. Opcja jest niedostępna dla innych typów subskrypcji.

| **Pole** | **Opis** |
|---|---|
| Subskrypcja | Subskrypcja używana do płacenia za rezerwację. Kosztami rezerwacji jest obciążana forma płatności za subskrypcję. Typ subskrypcji musi być umową Enterprise Agreement (Numer oferty: MS-AZR-0017P lub MS-AZR-0148P) lub umowa klienta firmy Microsoft lub indywidualna subskrypcja z stawką płatność zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P). Opłaty są odliczane od salda zobowiązania pieniężnego (jeśli jest dostępne) lub naliczane jako nadwyżka. W przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty są naliczane za kartę kredytową lub formę płatności faktury dla subskrypcji. |
| Zakres | Zakres rezerwacji może obejmować jedną subskrypcję lub wiele subskrypcji (zakres udostępniony). W przypadku wybrania opcji: <ul><li>**Zakres pojedynczej grupy zasobów** — rabat na rezerwację jest stosowany do odpowiednich zasobów tylko w wybranej grupie zasobów. </li><li>**Zakres pojedynczej subskrypcji** — rabat na rezerwację jest stosowany do odpowiednich zasobów w wybranej subskrypcji.</li><li>**Zakres udostępniony** — rabat na rezerwację jest stosowany do odpowiednich zasobów w kwalifikujących się subskrypcjach w ramach kontekstu rozliczeń. W przypadku klientów z umowami EA kontekst rozliczania to rejestracja. W przypadku indywidualnych subskrypcji ze stawkami płatności zgodnie z rzeczywistym użyciem kontekst rozliczeń stanowią wszystkie kwalifikujące się subskrypcje utworzone przez administratora konta.</li></ul> |
| Region (Region) | Region świadczenia usługi Azure objęty rezerwacją. |
| Zarezerwowany rozmiar wystąpienia Premium v3 | Rozmiar zarezerwowanych wystąpień Premium v3. |
| Okres | Jeden rok lub trzy lata. Okres 5-letni jest dostępny tylko dla wystąpień zarezerwowanych HBv2 Premium v3. |
| Liczba | Liczba wystąpień zakupionych w ramach rezerwacji. Ilość to liczba zarezerwowanych wystąpień Premium v3, które mogą uzyskać rabat rozliczeń. Jeśli na przykład \_ w regionie Wschodnie stany USA używasz 10 standardowych wystąpień usługi D2 Premium w wersji 3, należy określić liczbę jako 10, aby zmaksymalizować korzyść dla wszystkich wystąpień zarezerwowanych dla systemu Premium v3. |

## <a name="save-with-isolated-stamp-fees"></a>Zapisz z opłatami za wyizolowaną sygnaturę

Możesz zaoszczędzić pieniądze na podatku od czynności cywilnoprawnych za korzystanie z usługi Azure App Service w izolowanym środowisku, zobowiązując się do użycia zarezerwowanych sygnatur przez okres trzech lat. Aby kupić wydajność rezerwową objętą podatkiem od czynności cywilnoprawnych w izolowanym środowisku, należy wybrać region świadczenia usługi Azure, w którym zostanie wdrożona sygnatura, oraz liczbę kupowanych sygnatur.

Gdy wykupisz rezerwację, za użycie w ramach podatku od czynności cywilnoprawnych w izolowanym środowisku zgodne z atrybutami rezerwacji nie będą naliczane opłaty po stawkach płatności zgodnie z rzeczywistym użyciem. Rezerwacja jest stosowana automatycznie do izolowanych sygnatur, które są zgodne z zakresem i regionem wydajności rezerwowej. Nie musisz przypisywać rezerwacji do izolowanej sygnatury. Rezerwacja nie dotyczy procesów roboczych, dlatego opłaty za inne zasoby związane z sygnaturą są naliczane oddzielnie.

Gdy wydajność rezerwowa wygaśnie, izolowane sygnatury nadal będą działać, ale będą za nie naliczane opłaty po stawkach płatności zgodnie z rzeczywistym użyciem. Rezerwacje nie są odnawiane automatycznie.

## <a name="determine-the-right-isolated-stamp-reservation-to-purchase"></a>Określenie rezerwacji właściwej sygnatury oddzielonej do zakupu

Kupując rezerwację, zobowiązujesz się, że w ciągu kolejnych trzech lat będziesz używać ilości zarezerwowanych. Sprawdź dane dotyczące użycia, aby określić, ilu izolowanych sygnatur usługi App Service stale używasz i ilu możesz używać w przyszłości.

Ponadto upewnij się, że wiesz, jak izolowana sygnatura emituje miernik systemu Linux lub Windows.

- Pusta izolowana sygnatura domyślnie emituje miernik sygnatur systemu Windows. Na przykład bez wdrożonych procesów roboczych. W dalszym ciągu emituje ten miernik, jeśli procesy robocze systemu Windows są wdrożone w sygnaturze.
- Miernik zostaje zmieniony na miernik sygnatur systemu Linux w przypadku wdrożenia procesu roboczego systemu Linux.
- Jeśli wdrożone są zarówno procesy robocze systemu Linux, jak i Windows, sygnatura emituje miernik systemu Windows.

Zatem w czasie trwania sygnatury miernik sygnatur może być zmieniany na miernik systemu Windows lub na miernik systemu Linux.

Jeśli masz co najmniej jeden proces roboczy systemu Windows w sygnaturze, kup rezerwacje sygnatur systemu Windows. Jedyna sytuacja, w której należy kupić rezerwację sygnatury systemu Linux ma miejsce, gdy planujesz mieć w sygnaturze _tylko_ procesy robocze systemu Linux.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Kupowanie wydajności rezerwowej izolowanej sygnatury

Wydajność rezerwową izolowanej sygnatury można kupić w witrynie [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D). Płatność za rezerwację jest wnoszona [z góry lub w ratach miesięcznych](./prepare-buy-reservation.md). Do zakupu wydajności rezerwowej potrzebna jest rola właściciela co najmniej w jednej subskrypcji Enterprise lub w subskrypcji indywidualnej z ratami płatności zgodnie z rzeczywistym użyciem.

- W przypadku subskrypcji Enterprise w witrynie [EA portal](https://ea.azure.com/) musi być włączona opcja **Dodaj wystąpienia zarezerwowane**. Jeśli to ustawienie jest wyłączone, musisz być administratorem EA.
- W przypadku programu Cloud Solution Provider (CSP) wydajność rezerwową usługi Azure Synapse Analytics mogą kupić tylko agenci administracyjni lub agenci sprzedaży.

**Aby dokonać zakupu:**

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Wybierz subskrypcję. Z listy **Subskrypcja** wybierz subskrypcję, w ramach której jest opłacana wydajność rezerwowa. Kosztami wydajności rezerwowej jest obciążana forma płatności subskrypcji. Wymagany typ subskrypcji to Enterprise Agreement (numery ofert: MS-AZR-0017P lub MS-AZR-0148P) albo Płatność zgodnie z rzeczywistym użyciem (numer ofert: MS-AZR-0003P lub MS-AZR-0023P) lub Subskrypcja CSP.
    - W przypadku subskrypcji dla przedsiębiorstw opłaty są odliczane od salda opłaty z góry za platformę Azure (wcześniej nazywanej zobowiązaniem pieniężnym) rejestracji lub naliczane jako nadwyżka.
    - W przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty obciążają kartę kredytową lub metodę płatności faktury powiązaną z subskrypcją.
1. Za pomocą listy **Zakres** wybierz zakres subskrypcji.
    - **Zakres pojedynczej grupy zasobów** — rabat na rezerwację jest stosowany do odpowiednich zasobów tylko w wybranej grupie zasobów.
    - **Zakres pojedynczej subskrypcji** — rabat na rezerwację jest stosowany do odpowiednich zasobów w wybranej subskrypcji.
    - **Zakres udostępniony** — rabat na rezerwację jest stosowany do odpowiednich zasobów w kwalifikujących się subskrypcjach w ramach kontekstu rozliczeń. W przypadku klientów korzystających z umowy Enterprise Agreement kontekstem rozliczeń jest rejestracja. W przypadku indywidualnych subskrypcji ze stawkami płatności zgodnie z rzeczywistym użyciem kontekst rozliczeń stanowią wszystkie kwalifikujące się subskrypcje utworzone przez administratora konta.
1. Wybierz pozycję **Region**, aby wskazać region świadczenia usługi Azure, którego ma dotyczyć wydajność rezerwowa.
1. Wybierz typ Plan izolowany, a następnie kliknij pozycję **Wybierz**.  
    ![Przykład ](./media/prepay-app-service/app-service-isolated-stamp-select.png)
1. Wprowadź liczbę izolowanych sygnatur usługi App Service, które mają zostać zarezerwowane. Na przykład liczba trzy daje trzy zarezerwowane sygnatury na region. Kliknij pozycję **Next: Przejrzyj i kup**.
1. Przejrzyj i kliknij pozycję **Kup teraz**.

Po zakupie przejdź do pozycji [Rezerwacje](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade), aby wyświetlić stan zakupu i monitorować go w dowolnym momencie.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zwrot kosztów rezerwacji

Rezerwacje można anulować, wymieniać lub zwracać, jednak obowiązują przy tym pewne ograniczenia. Aby uzyskać więcej informacji, zobacz temat [Samoobsługowe wymiany i zwroty kosztów dla rezerwacji platformy Azure](exchange-and-refund-azure-reservations.md).

## <a name="discount-application-shown-in-usage-data"></a>Zastosowanie rabatu widoczne w danych dotyczących użycia

Dane użycia mają wynikową cenę równą zero za użycie, którego dotyczy rabat rezerwacji. Dane użycia pokazują rabat rezerwacji dla każdego wystąpienia sygnatury w każdej rezerwacji.

Aby uzyskać więcej informacji na temat tego, jak jest wyświetlany rabat rezerwacji w danych użycia, zobacz [Pobieranie kosztów i użycia rezerwacji w ramach umowy Enterprise Agreement](understand-reserved-instance-usage-ea.md), jeśli jesteś klientem z umową Enterprise Agreement (EA). W przeciwnym razie zobacz [Informacje na temat użycia rezerwacji platformy Azure dla indywidualnej subskrypcji z płatnością zgodnie z rzeczywistym użyciem](understand-reserved-instance-usage.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:
  - [Czym są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
  - [Omówienie sposobu stosowania rabatu rezerwacji izolowanej sygnatury usługi Azure App Service](reservation-discount-app-service.md)
  - [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)
