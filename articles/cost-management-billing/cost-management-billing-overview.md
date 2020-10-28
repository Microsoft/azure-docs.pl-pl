---
title: Omówienie funkcji Zarządzanie kosztami i rozliczenia na platformie Azure
description: Funkcja Zarządzanie kosztami i rozliczenia na platformie Azure umożliwia wykonywanie zadań administracyjnych dotyczących rozliczeń oraz pozwala zarządzać dostępem do rozliczeń w celu śledzenia kosztów. Ponadto funkcja ta umożliwia monitorowanie i kontrolowanie wydatków na platformie Azure oraz optymalizowanie użycia zasobów platformy Azure.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/26/2020
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: common
ms.custom: contentperfq2
ms.openlocfilehash: 2c533ea08534444e5bf8d8d57d585e2bf975a93b
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677387"
---
# <a name="what-is-azure-cost-management--billing"></a>Co to jest Zarządzanie kosztami i rozliczenia na platformie Azure?

Korzystając z chmury firmy Microsoft, możesz znacząco poprawić techniczną wydajność obciążeń biznesowych. Może ona również zmniejszyć koszty i obciążenie wymagane do zarządzania zasobami organizacji. Jednak możliwości biznesowe stanowią ryzyko ze względu na potencjalne marnotrawstwo i nieefektywność, które są wprowadzane do wdrożeń w chmurze. Funkcja Zarządzanie kosztami i rozliczenia na platformie Azure to zestaw narzędzi udostępnianych przez firmę Microsoft, które ułatwiają analizowanie i optymalizowanie kosztów obciążeń oraz zarządzanie nimi. Używanie tego zestawu pomaga zapewnić, że organizacja korzysta z zalet chmury.

Obciążenia platformy Azure można traktować jak światła w domu. Kiedy wychodzisz z domu, zostawiasz światła włączone? Czy możesz użyć innych, bardziej wydajnych żarówek, dzięki którym obniżysz rachunek za energię elektryczną? Czy w danym pokoju masz więcej świateł, niż potrzeba? Za pomocą funkcji Zarządzanie kosztami i rozliczenia na platformie Azure możesz zastosować ten proces myślowy do obciążeń używanych przez Twoją organizację.

W przypadku produktów i usług platformy Azure płaci się wyłącznie za użyte zasoby. W miarę tworzenia i używania zasobów platformy Azure są za nie naliczane opłaty. Ze względu na łatwość wdrażania nowych zasobów koszty obciążeń mogą znacznie się zwiększyć bez odpowiedniej analizy i monitorowania. Przy użyciu funkcji Zarządzanie kosztami i rozliczenia na platformie Azure można wykonywać następujące czynności:

- Wykonywanie zadań administracyjnych dotyczących rozliczeń takich jak opłacanie rachunków
- Zarządzanie dostępem do rozliczeń i kosztów
- Pobieranie danych dotyczących kosztów i użycia, na podstawie których wygenerowano fakturę miesięczną
- Proaktywne stosowanie analizy danych do kosztów
- Ustawianie progów wydatków
- Identyfikowanie możliwości zmian w obciążeniach, które mogą zoptymalizować wydatki

Aby dowiedzieć się więcej na temat podejścia do zarządzania kosztami w organizacji, zapoznaj się z artykułem [Azure Cost Management best practices](./costs/cost-mgt-best-practices.md) (Najlepsze rozwiązania w usłudze Azure Cost Management).

## <a name="understand-azure-billing"></a>Informacje na temat rozliczeń na platformie Azure

Funkcje rozliczeń na platformie Azure umożliwiają przeglądanie zafakturowanych kosztów i zarządzanie dostępem do informacji dotyczących rozliczeń. W większych organizacjach zadania związane z rozliczeniami są zwykle wykonywane przez zespoły ds. zamówień i finansów.

Konto rozliczeniowe jest tworzone podczas rejestrowania się w celu korzystania z platformy Azure. Konta rozliczeniowego można używać do zarządzania fakturami i płatnościami oraz do śledzenia kosztów. Możesz mieć dostęp do wielu kont rozliczeniowych. Możesz na przykład zarejestrować się na platformie Azure, aby móc pracować nad projektami osobistymi. W związku z tym może istnieć indywidualna subskrypcja platformy Azure z kontem rozliczeniowym. Dostęp jest również możliwy w ramach zawartej przez organizację umowy Enterprise Agreement lub Umowy klienta firmy Microsoft. W każdym scenariuszu używane jest oddzielne konto rozliczeniowe.

### <a name="billing-accounts"></a>Konta rozliczeniowe

W witrynie Azure Portal są obecnie obsługiwane następujące typy kont rozliczeniowych:

- **Microsoft Online Services Program** : Indywidualne konto rozliczeniowe dla programu Microsoft Online Services Program jest tworzone podczas rejestrowania się na platformie Azure za pomocą witryny internetowej platformy Azure. Na przykład po zarejestrowaniu się w celu uzyskania bezpłatnego konta platformy Azure, konta ze stawkami płatności zgodnie z rzeczywistym użyciem lub jako subskrybent programu Visual Studio.

- **Umowa Enterprise Agreement** : Konto rozliczeniowe dla umowy Enterprise Agreement jest tworzone, gdy organizacja podpisuje umowę Enterprise Agreement (EA) w celu korzystania z platformy Azure.

- **Umowa klienta firmy Microsoft** : Konto rozliczeniowe dla umowy klienta firmy Microsoft jest tworzone, gdy organizacja współpracuje z przedstawicielem firmy Microsoft w celu podpisania umowy klienta firmy Microsoft. Niektórzy klienci w wybranych regionach, którzy zarejestrują się za pomocą witryny internetowej Azure w celu utworzenia konta ze stawkami płatności zgodnie z rzeczywistym użyciem lub dokonają podwyższenia poziomu swojego bezpłatnego konta platformy Azure, mogą mieć również konto rozliczeniowe do umowy klienta firmy Microsoft.

### <a name="scopes-for-billing-accounts"></a>Zakresy kont rozliczeniowych
Zakres to węzeł w ramach konta rozliczeniowego umożliwiający wyświetlanie rozliczeń i zarządzanie nimi. Jest to miejsce, w którym zarządzasz danymi rozliczeniowymi, płatnościami i fakturami oraz przeprowadzasz ogólne czynności dotyczące zarządzania kontami.

#### <a name="microsoft-online-services-program"></a>Microsoft Online Services Program

|Zakres  |Definicja  |
|---------|---------|
|Konto billingowe     | Reprezentuje pojedynczego właściciela (administratora konta) co najmniej jednej subskrypcji platformy Azure. Administrator konta jest autoryzowany do wykonywania różnych zadań rozliczeniowych, takich jak tworzenie subskrypcji, wyświetlanie faktur lub zmiana rozliczeń dla subskrypcji.  |
|Subskrypcja     |  Reprezentuje zgrupowanie zasobów platformy Azure. W zakresie subskrypcji są generowane faktury. Ma swoje formy płatności za faktury.|

#### <a name="enterprise-agreement"></a>Enterprise Agreement

|Zakres  |Definicja  |
|---------|---------|
|Konto billingowe    | Reprezentuje rejestrację umów Enterprise Agreement. W zakresie konta rozliczeniowego są generowane faktury. W skład jego struktury wchodzą działy i konta rejestracji.  |
|Dział     |  Opcjonalne zgrupowanie kont rejestracji.      |
|Konto rejestracji     |  Reprezentuje pojedynczego właściciela konta. W zakresie konta rejestracji są tworzone subskrypcje platformy Azure.  |

#### <a name="microsoft-customer-agreement"></a>Umowa klienta firmy Microsoft

|Zakres  |Zadania  |
|---------|---------|
|Konto billingowe     |   Reprezentuje umowę klienta dla wielu produktów i usług firmy Microsoft. W skład struktury konta rozliczeniowego wchodzą profile rozliczeniowe i sekcje faktur.   |
|Profil rozliczeniowy     |  Reprezentuje fakturę i formy płatności za nią. W tym zakresie jest generowana faktura. Profil rozliczeniowy może zawierać wiele sekcji faktur.      |
|Sekcja faktury     |   Reprezentuje grupę kosztów na fakturze. Z zakresem sekcji faktury są skojarzone subskrypcje i inne zakupy.    |

## <a name="understand-azure-cost-management"></a>Omówienie usługi Azure Cost Management

Mimo że te czynności są powiązane, rozliczanie różni się od zarządzania kosztami. Rozliczanie jest procesem fakturowania klientów za towary lub usługi oraz zarządzania relacjami komercyjnymi.

Usługa Cost Management przedstawia koszty i wzorce użycia w organizacji za pomocą zaawansowanej analizy. Raporty w usłudze Cost Management pokazują koszty użycia usług platformy Azure i ofert innych firm z portalu Marketplace. Koszty są oparte na wynegocjowanych cenach i współczynniku rezerwacji oraz uwzględniają rabaty z tytułu Korzyści użycia hybrydowego platformy Azure. Zbiorczo raporty przedstawiają wewnętrzne i zewnętrzne koszty użycia i opłat w witrynie Azure Marketplace. Inne opłaty, takie jak zakupy rezerwacji, pomoc techniczna i podatki, nie są jeszcze uwzględniane w raportach. Raporty pomagają w zrozumieniu wydatków i użycia zasobów, a także ułatwiają wyszukiwanie nietypowych wydatków. Dostępna jest również analiza predykcyjna. Usługa Cost Management używa grup zarządzania, budżetów i rekomendacji platformy Azure, aby w czytelny sposób przedstawić, jak wydatki są zorganizowane i jak można zmniejszyć koszty.

Witryny Azure Portal i różnych interfejsów API można używać do automatyzacji eksportu w celu zintegrowania danych kosztów z procesami i systemami zewnętrznymi. Dostępna jest również funkcja zautomatyzowanego eksportu danych rozliczeń oraz raporty zaplanowane.

Obejrzyj wideo z przeglądem usługi Azure Cost Management, aby zapoznać się z krótkim omówieniem, jak usługa Azure Cost Management może pomóc w zaoszczędzeniu pieniędzy na platformie Azure. Aby obejrzeć inne wideo, odwiedź [kanał usługi Cost Management w serwisie YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

### <a name="plan-and-control-expenses"></a>Planowanie i kontrolowanie kosztów

Usługa Cost Management ułatwia planowanie i kontrolowanie kosztów, oferując następujące funkcje: analiza kosztów, budżety, rekomendacje i eksportowanie danych zarządzania kosztami.

Analiza kosztów pozwala na eksplorowanie i analizowanie kosztów organizacyjnych. Można wyświetlić zagregowane koszty według organizacji, aby dowiedzieć się, gdzie występują koszty, i zidentyfikować trendy wydatków. Ponadto zakumulowane koszty w czasie można przeglądać w celu oszacowania miesięcznych, kwartalnych, a nawet rocznych trendów kosztów w odniesieniu do budżetu.

Budżety ułatwiają planowanie i spełnianie założeń odpowiedzialności finansowej w organizacji. Pomagają one zapobiegać przekroczeniom limitów lub progów kosztów. Budżety mogą również ułatwiać informowanie innych osób o ich wydatkach, aby aktywnie zarządzać kosztami. I można przy ich użyciu zobaczyć, jak kształtują się wydatki w czasie.

Rekomendacje pokazują, jak można przeprowadzić optymalizację i zwiększyć wydajność, identyfikując zasoby w stanie bezczynności i niedostatecznie używane. Mogą również dotyczyć tańszych opcji zasobów. Pracując z rekomendacjami, zmieniasz sposób używania zasobów, aby zaoszczędzić pieniądze. Aby rozpocząć pracę, należy najpierw zapoznać się z rekomendacjami dotyczącymi optymalizacji kosztów, aby zidentyfikować potencjalne przypadki nieefektywnego użycia. Następnie należy skorzystać z rekomendacji, aby zmodyfikować użycie zasobów platformy Azure do tańszej opcji. Na koniec należy zweryfikować wykonane akcje, aby upewnić się, że wprowadzanie zmian zakończyło się powodzeniem.

Jeśli używasz systemów zewnętrznych do uzyskiwania dostępu do danych zarządzania kosztami lub ich przeglądania, możesz łatwo wyeksportować dane z platformy Azure. Możesz również ustawić dzienny zaplanowany eksport w formacie CSV i przechowywać pliki danych w usłudze Azure Storage. Dane będą w tej sytuacji dostępne z systemu zewnętrznego.

### <a name="cloudyn-deprecation"></a>Wycofywanie rozwiązania Cloudyn

Cloudyn to usługa platformy Azure powiązana z usługą Cost Management, która zostanie wycofana do końca 2020 r. Istniejące funkcje usługi Cloudyn są zintegrowane bezpośrednio z witryną Azure Portal, gdzie tylko jest to możliwe. W tej chwili nie ma żadnych nowych klientów, ale produkt będzie nadal obsługiwany do czasu całkowitego wycofania.
 
### <a name="additional-azure-tools"></a>Dodatkowe narzędzia platformy Azure

Platforma Azure oferuje inne narzędzia, które nie są częścią zestawu funkcji Zarządzanie kosztami i rozliczenia na platformie Azure. Pełnią one jednak ważną rolę w procesie zarządzania kosztami. Aby dowiedzieć się więcej o tych narzędziach, skorzystaj z poniższych linków.

- [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) — to narzędzie umożliwia szacowanie początkowych kosztów chmury.
- [Usługa Azure Migrate](/azure/migrate/migrate-services-overview) — umożliwia ocenianie bieżącego obciążenia centrum danych w celu uzyskania szczegółowych informacji o potrzebach w zakresie zastępczego rozwiązania platformy Azure.
- [Usługa Azure Advisor](../advisor/advisor-overview.md) — umożliwia zidentyfikowanie nieużywanych maszyn wirtualnych i zapoznanie się z rekomendacjami w zakresie zakupów wystąpień zarezerwowanych platformy Azure.
- [Korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) — umożliwia oszczędzanie dzięki użyciu bieżących licencji systemu Windows Server lub programu SQL Server dla maszyn wirtualnych na platformie Azure.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już funkcje Zarządzanie kosztami i rozliczenia, następnym krokiem jest rozpoczęcie pracy z tą usługą.

- Zacznij korzystać z usługi Azure Cost Management, aby [analizować koszty](./costs/quick-acm-cost-analysis.md).
- Możesz również dowiedzieć się więcej na temat [najlepszych rozwiązań w usłudze Azure Cost Management](./costs/cost-mgt-best-practices.md).