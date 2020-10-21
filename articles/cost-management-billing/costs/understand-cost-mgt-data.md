---
title: Omówienie danych usługi Azure Cost Management
description: Ten artykuł pomaga lepiej zrozumieć dane zawarte w usłudze Azure Cost Management oraz częstotliwość ich przetwarzania, zbierania, pokazywania i zamykania.
author: bandersmsft
ms.author: banders
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.openlocfilehash: a9ec5fd7d6e93d4413e86b5c9433cb6c0c9b59a1
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132316"
---
# <a name="understand-cost-management-data"></a>Omówienie danych usługi Cost Management

Ten artykuł pomaga lepiej zrozumieć dane kosztów i użycia platformy Azure, które znajdują się w usłudze Azure Cost Management. Wyjaśniono w nim, jak często dane są przetwarzane, zbierane, pokazywane i zamykane. Opłaty za użycie platformy Azure są naliczane co miesiąc. Mimo że cykle rozliczeniowe są okresami miesięcznymi, daty rozpoczęcia i zakończenia cyklu różnią się w zależności od typu subskrypcji. Częstotliwość, z jaką usługa Cost Management odbiera dane użycia, różni się w zależności od różnych czynników. Takie czynniki obejmują czas potrzebny na przetworzenie danych oraz częstotliwość, z jaką usługi platformy Azure emitują użycie do systemu rozliczeniowego.

W usłudze Cost Management zawarte są dane całego użycia i wszystkich zakupów, w tym rezerwacje i oferty innych firm dla kont z umową Enterprise Agreement (EA). W przypadku kont z Umową z Klientem Microsoft i subskrypcji indywidualnych rozliczanych po stawkach płatności zgodnie z rzeczywistym zawarte są wyłącznie dane użycia z usług platformy Azure i witryny Marketplace. Pomoc techniczna i inne koszty nie są uwzględniane. Do czasu wygenerowania faktury koszty są szacunkowe i nie są uwzględniane w środkach.

Jeśli masz nową subskrypcję, nie możesz od razu korzystać z funkcji usługi Cost Management. Aby można było korzystać ze wszystkich funkcji usługi Cost Management, może upłynąć do 48 godzin.

## <a name="supported-microsoft-azure-offers"></a>Obsługiwane oferty platformy Microsoft Azure

Poniższe informacje przedstawiają aktualnie obsługiwane oferty platformy [Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) w usłudze Azure Cost Management. Oferta platformy Azure to typ posiadanej subskrypcji platformy Azure. Dane są dostępne w usłudze Cost Management od dnia określonego przez wartość **Dane dostępne od**. Jeśli oferty dla subskrypcji ulegną zmianie, koszty sprzed zmiany ofert nie będą dostępne.

| **Kategoria**  | **Nazwa oferty** | **Identyfikator limitu przydziału** | **Numer oferty** | **Dane dostępne od** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | Maj 2014<sup>1</sup> |
| **Enterprise Agreement (EA)** | Enterprise — tworzenie i testowanie                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | Maj 2014<sup>1</sup> |
| **Enterprise Agreement (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | Maj 2014<sup>1</sup> |
| **Umowa klienta firmy Microsoft** | [Plan platformy Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | Nie dotyczy | Marzec 2019<sup>3</sup> |
| **Umowa klienta firmy Microsoft** | [Plan platformy Microsoft Azure na potrzeby tworzenia i testowania](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | Nie dotyczy | Marzec 2019<sup>3</sup> |
| **Umowa z Klientem Microsoft obsługiwana przez partnerów** | Plan platformy Microsoft Azure | CSP_2015-05-01, CSP_MG_2017-12-01, and CSPDEVTEST_2018-05-01<br><br>Ten sam identyfikator limitu przydziału jest używany dla Umowy z Klientem Microsoft i starszych subskrypcji CSP. Obecnie obsługiwane są tylko subskrypcje z Umową z Klientem Microsoft. | Nie dotyczy | Październik 2019 r. |
| **Microsoft Developer Network (MSDN)** | [Platformy MSDN](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2 października 2018<sup>2</sup> |
| **Płatność zgodnie z rzeczywistym użyciem** | [Płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2 października 2018<sup>2</sup> |
| **Płatność zgodnie z rzeczywistym użyciem** | [Płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2 października 2018<sup>2</sup> |
| **Płatność zgodnie z rzeczywistym użyciem** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2 października 2018<sup>2</sup> |
| **Płatność zgodnie z rzeczywistym użyciem** | [Bezpłatna wersja próbna](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2 października 2018<sup>2</sup> |
| **Płatność zgodnie z rzeczywistym użyciem** | [Azure w ramach programu licencjonowania Open](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2 października 2018<sup>2</sup> |
| **Płatność zgodnie z rzeczywistym użyciem** | Azure — dostęp próbny<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P | 2 października 2018<sup>2</sup> |
| **Program Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2 października 2018<sup>2</sup> |
| **Program Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2 października 2018<sup>2</sup> |
| **Program Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2 października 2018<sup>2</sup> |
| **Program Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2 października 2018<sup>2</sup> |
| **Program Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2 października 2018<sup>2</sup> |

_<sup>**1**</sup> Aby uzyskać dane sprzed maja 2014 r., odwiedź [portal Azure Enterprise](https://ea.azure.com)._

_<sup>**2**</sup> Aby uzyskać dane sprzed 2 października 2018 r., odwiedź [Centrum konta platformy Azure](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> Umowy z Klientem Microsoft pojawiły się w marcu 2019 r. i nie ma żadnych danych historycznych sprzed tej daty._

_<sup>**4**</sup> Dane historyczne dla subskrypcji bazujących na środkach i opłacanych z góry mogą nie być zgodne z Twoją fakturą. Zobacz [Dane historyczne mogą nie odpowiadać fakturze](#historical-data-might-not-match-invoice) poniżej._

Następujące oferty nie są jeszcze obsługiwane:

| Kategoria  | **Nazwa oferty** | **Identyfikator limitu przydziału** | **Numer oferty** |
| --- | --- | --- | --- |
| **Azure (Niemcy)** | [Azure (Niemcy) — płatność zgodnie z rzeczywistym użyciem](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | US Government — płatność zgodnie z rzeczywistym użyciem | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P |
| **Dostawca rozwiązań w chmurze (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Dostawca rozwiązań w chmurze (CSP)** | Azure Government — dostawca rozwiązań w chmurze                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Dostawca rozwiązań w chmurze (CSP)** | Azure (Niemcy) w programie CSP dla chmury Microsoft Cloud (Niemcy)   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Płatność zgodnie z rzeczywistym użyciem**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Płatność zgodnie z rzeczywistym użyciem** | [Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Płatność zgodnie z rzeczywistym użyciem**                 | [Dostęp sponsorowany Microsoft Azure.](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Plany pomocy technicznej** | Pomoc techniczna Standard                    | Default_2014-09-01 | MS-AZR-0041P |
| **Plany pomocy technicznej** | Pomoc techniczna Professional Direct         | Default_2014-09-01 | MS-AZR-0042P |
| **Plany pomocy technicznej** | Pomoc techniczna Developer                   | Default_2014-09-01 | MS-AZR-0043P |
| **Plany pomocy technicznej** | Pomoc techniczna Germany                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Plany pomocy technicznej** | Pomoc techniczna Azure Government Standard   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Plany pomocy technicznej** | Pomoc techniczna Azure Government Pro-Direct | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Plany pomocy technicznej** | Pomoc techniczna Azure Government Developer  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

### <a name="free-trial-to-pay-as-you-go-upgrade"></a>Uaktualnienie bezpłatnej wersji próbnej do oferty z płatnością zgodnie z rzeczywistym użyciem

Aby uzyskać informacje o dostępności usług warstwy Bezpłatna po przeprowadzeniu uaktualnienia z bezpłatnej wersji próbnej do cennika oferty z płatnością zgodnie z rzeczywistym użyciem, zobacz [Często zadawane pytania dotyczące bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/free-account-faq/).

### <a name="determine-your-offer-type"></a>Określanie typu oferty

Jeśli nie widzisz danych dla subskrypcji i chcesz określić, czy Twoja subskrypcja jest objęta obsługiwanymi ofertami, możesz zweryfikować, czy subskrypcja jest obsługiwana. Aby zweryfikować, czy subskrypcja platformy Azure jest obsługiwana, zaloguj się do witryny [Azure Portal](https://portal.azure.com). Następnie wybierz pozycję **Wszystkie usługi** w okienku menu po lewej stronie. Z listy usług wybierz pozycję **Subskrypcje**. Z menu listy subskrypcji wybierz subskrypcję, którą chcesz zweryfikować. Twoja subskrypcja zostanie pokazana na karcie Przegląd i zobaczysz pozycje **Oferta** oraz **Identyfikator oferty**. Na poniższej ilustracji przedstawiono przykładowy raport.

![Przykład karty Przegląd dla subskrypcji z pozycjami Oferta oraz Identyfikator oferty](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Koszty uwzględniane w usłudze Cost Management

W poniższych tabelach przedstawiono dane, które są lub nie są uwzględniane w usłudze Cost Management. Do momentu wygenerowania faktury wszystkie koszty są szacunkowe. Pokazane koszty nie obejmują środków bezpłatnych i przedpłaconych.

| **Uwzględniane** | **Nie uwzględniane** |
| --- | --- |
| Użycie usługi platformy Azure<sup>5</sup>        | Opłaty za pomoc techniczną — aby uzyskać więcej informacji, zobacz [Objaśnienie faktury](../understand/understand-invoice.md). |
| Użycie oferty z platformy handlowej<sup>6</sup> | Podatki — aby uzyskać więcej informacji, zobacz [Objaśnienie faktury](../understand/understand-invoice.md). |
| Zakupy na platformie handlowej<sup>6</sup>      | Środki — aby uzyskać więcej informacji, zobacz [Objaśnienie faktury](../understand/understand-invoice.md). |
| Zakupy rezerwacji<sup>7</sup>      |  |
| Amortyzacja zakupów rezerwacji<sup>7</sup>      |  |

_<sup>**5**</sup> Użycie usługi platformy Azure jest oparte na rezerwacjach i cenach negocjowanych._

_<sup>**6**</sup> Zakupy na platformie handlowej nie są w tej chwili dostępne dla ofert MSDN i Visual Studio._

_<sup>**7**</sup> Zakupy rezerwacji są w tej chwili dostępne tylko dla kont z umową Enterprise Agreement (EA) i Umową z Klientem Microsoft._

## <a name="how-tags-are-used-in-cost-and-usage-data"></a>Używanie tagów w danych dotyczących kosztów i użycia

Usługa Azure Cost Management odbiera tagi w ramach każdego rekordu użycia przesłanego przez poszczególne usługi. Do tych tagów mają zastosowanie następujące ograniczenia:

- Tagi muszą być stosowane bezpośrednio do zasobów i nie są niejawnie dziedziczone z nadrzędnej grupy zasobów.
- Tagi są obsługiwane tylko w przypadku zasobów wdrożonych w grupach zasobów.
- Niektóre wdrożone zasoby mogą nie obsługiwać tagów lub mogą nie mieć tagów w danych użycia — zobacz [Obsługa tagów dla zasobów platformy Azure](../../azure-resource-manager/management/tag-support.md).
- Tagi zasobów są uwzględniane w danych użycia tylko wtedy, gdy tag jest zastosowany — tagi nie są stosowane do danych historycznych.
- Tagi zasobów stają się dostępne w usłudze Cost Management wyłącznie po odświeżeniu danych — zobacz [Aktualizacje oraz przechowywanie danych dotyczących kosztów i użycia](#cost-and-usage-data-updates-and-retention).
- Tagi zasobów są dostępne w usłudze Cost Management tylko wtedy, gdy zasób jest aktywny/uruchomiony i tworzy rekordy użycia (np. nie wtedy, gdy cofnięto przydział maszyny wirtualnej).
- Zarządzanie tagami wymaga dostępu do każdego zasobu na poziomie współautora.
- Zarządzanie zasadami tagów wymaga dostępu na poziomie właściciela lub współautora zasad do grupy zarządzania, subskrypcji lub grupy zasobów.
    
Jeśli nie widzisz konkretnego tagu w usłudze Cost Management, weź pod uwagę następujące kwestie:

- Czy tag został zastosowany bezpośrednio do zasobu?
- Czy tag został zastosowany ponad 24 godziny temu? Zobacz [Aktualizacje oraz przechowywanie danych dotyczących kosztów i użycia](#cost-and-usage-data-updates-and-retention)
- Czy typ zasobu obsługuje tagi? Następujące typy zasobów nie obsługują tagów w danych użycia od 1 grudnia 2019 r. Zobacz [Obsługa tagów dla zasobów platformy Azure](../../azure-resource-manager/management/tag-support.md), aby uzyskać pełną listę obsługiwanych tagów.
    - Katalogi usługi Azure Active Directory B2C
    - Azure Bastion
    - Zapory usługi Azure Firewall
    - Azure NetApp Files
    - Fabryka danych
    - Databricks
    - Moduły równoważenia obciążenia
    - Network Watcher
    - Notification Hubs
    - Service Bus
    - Time Series Insights
    
Oto kilka porad dotyczących pracy z tagami:

- Z wyprzedzeniem zaplanuj i zdefiniuj strategię tagowania, która pozwala na podział kosztów według organizacji, aplikacji, środowiska itp.
- Za pomocą usługi Azure Policy skopiuj tagi grupy zasobów do poszczególnych zasobów i wymuś strategię tagowania.
- Użyj interfejsu API tagów w połączeniu z elementem Query lub UsageDetails, aby uzyskać wszystkie koszty na podstawie bieżących tagów.


## <a name="cost-and-usage-data-updates-and-retention"></a>Aktualizacje oraz przechowywanie danych dotyczących kosztów i użycia

Dane dotyczące kosztów i użycia zwykle są dostępne w witrynie Azure Portal w części Zarządzanie kosztami i rozliczenia oraz za pośrednictwem [interfejsów API obsługi](../index.yml) w ciągu 8–24 godzin. Podczas przeglądania kosztów trzeba mieć na uwadze następujące kwestie:

- Każda usługa platformy Azure (na przykład Storage, Compute i SQL) emituje użycie w różnych odstępach czasu — dane niektórych usług mogą być widoczne wcześniej niż inne.
- Szacowane opłaty za bieżący okres rozliczeniowy są aktualizowane sześć razy dziennie.
- Szacowane opłaty za bieżący okres rozliczeniowy mogą ulec zmianie w miarę wzrostu użycia.
- Każda aktualizacja jest zbiorcza i obejmuje wszystkie elementy wierszy i informacje z poprzedniej aktualizacji.
- Platforma Azure finalizuje lub _zamyka_ bieżący okres rozliczeniowy w ciągu maksymalnie 72 godzin (trzech dni kalendarzowych) po zakończeniu okresu rozliczeniowego.

W poniższych przykładach pokazano, jak okresy rozliczeniowe mogą zostać zakończone:

* Subskrypcje z umową Enterprise Agreement (EA) — jeśli miesiąc rozliczeniowy kończy się 31 marca, szacowane opłaty są aktualizowane nawet przez jeszcze 72 godziny. W tym przykładzie jest to północ (UTC) 4 kwietnia.
* Subskrypcje z płatnością zgodnie z rzeczywistym użyciem — jeśli miesiąc rozliczeniowy kończy się 15 maja, szacowane opłaty mogą być aktualizowane przez jeszcze 72 godziny. W tym przykładzie jest to północ (UTC) 19 maja.

Gdy dane dotyczące kosztów i użycia staną się dostępne w obszarze Zarządzanie kosztami i rozliczenia, będą przechowywane przez co najmniej 7 lat.

### <a name="rerated-data"></a>Dane ponownego przeliczania

Bez względu na to, czy do pobierania danych używasz [interfejsów API usługi Cost Management](../index.yml), usługi Power BI lub witryny Azure Portal, do czasu zamknięcia faktury możesz spodziewać się ponownego przeliczenia opłat z bieżącego okresu rozliczeniowego, a tym samym ich zmiany.

## <a name="cost-rounding"></a>Zaokrąglanie kosztów

Koszty wyświetlone w usłudze Cost Management są zaokrąglane. Koszty zwrócone przez interfejs API zapytań nie są zaokrąglane. Przykład:

- Analiza kosztów w witrynie Azure Portal — opłaty są zaokrąglane przy użyciu standardowych reguł zaokrąglania: wartości większe niż 0,5 są zaokrąglane w górę, w przeciwnym razie koszty są zaokrąglane w dół. Zaokrąglanie jest wykonywane tylko wtedy, gdy wartości są wyświetlane. Zaokrąglanie nie jest wykonywane podczas przetwarzania i agregowania danych. Na przykład analiza kosztów agreguje koszty w następujący sposób:
  -    Opłata 1: 0,004 USD
  - Opłata 2: 0,004 USD
  -    Renderowana opłata zagregowana: 0,004 + 0,004 = 0,008. Wyświetlana opłata to 0,01 USD.
- Zapytania API — opłaty są pokazywane z ośmioma miejscami dziesiętnymi, a zaokrąglanie nie jest wykonywane.

## <a name="historical-data-might-not-match-invoice"></a>Dane historyczne mogą nie być zgodne z fakturą

Dane historyczne dla ofert opartych na środkach i płatnych z góry mogą nie być zgodne z fakturą. W przypadku niektórych ofert z płatnością zgodnie z rzeczywistym użyciem platformy Azure, MSDN i Visual Studio do faktur mogą być stosowane środki na korzystanie z platformy Azure i płatności z góry. Jednak dane historyczne widoczne w usłudze Cost Management opierają się wyłącznie na szacowanych opłatach za zużycie. Dane historyczne w usłudze Cost Management nie obejmują płatności i środków. W związku z tym dane historyczne pokazywane dla następujących ofert mogą nie być do końca zgodne z fakturą.

- Azure for Students (MS-AZR-0170P)
- Azure w ramach programu licencjonowania Open (MS-AZR-0111P)
- Azure — dostęp próbny (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Bezpłatna wersja próbna (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Zobacz też

- Jeśli pierwszy przewodnik Szybki start dla usługi Cost Management nie został jeszcze przez Ciebie ukończony, przeczytaj go w temacie [Rozpoczęcie analizowania kosztów](./quick-acm-cost-analysis.md).