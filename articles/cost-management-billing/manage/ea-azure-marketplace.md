---
title: Azure Marketplace
description: Tu opisano, jak klienci z umową EA mogą korzystać z witryny Azure Marketplace.
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: banders
ms.openlocfilehash: 0f2d3c830f27eec9f521e6f79ac8dce3bce818e9
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89442568"
---
# <a name="azure-marketplace"></a>Azure Marketplace

W tym artykule wyjaśniono, jak klienci z umową EA i partnerzy mogą wyświetlać opłaty na platformie handlowej i włączać zakupy w witrynie Azure Marketplace.

## <a name="azure-marketplace-for-ea-customers"></a>Witryna Azure Marketplace dla klientów umowy EA

W przypadku klientów bezpośrednich opłaty związane z witryną Azure Marketplace są widoczne w witrynie Azure Enterprise Portal. Zakupy w witrynie Azure Marketplace i użycie są rozliczane kwartalnie lub miesięcznie poza przedpłatą za platformę Azure jako zaległości.

Klienci pośredni mogą znaleźć swoje subskrypcje witryny Azure Marketplace na stronie **Zarządzanie subskrypcjami** w witrynie Azure Enterprise Portal, lecz ceny będą ukryte. Aby uzyskać informacje o opłatach związanych z witryną Azure Marketplace, klienci powinni skontaktować się z dostawcą rozwiązań licencjonowania (LSP, Licensing Solutions Provider).

Nowe cykliczne miesięczne lub roczne zakupy w witrynie Azure Marketplace są całkowicie rozliczane w okresie, w którym dokonano zakupów w tej witrynie. Te pozycje zostaną odnowione automatycznie w następnym okresie w dniu oryginalnego zakupu.

Istniejące miesięczne opłaty cykliczne będą w dalszym ciągu odnawiane w pierwszym dniu każdego miesiąca kalendarzowego. Opłaty roczne będą odnawiane w rocznicę daty zakupu.

Niektóre usługi odsprzedawców innych firm dostępne w witrynie Azure Marketplace używają teraz salda przedpłaty za platformę Azure w ramach umowy Enterprise Agreement (EA). Wcześniej te usługi były rozliczane poza przedpłatą za platformę Azure umowy EA i na oddzielnej fakturze. Przedpłata za platformę Azure w ramach umowy EA dla tych usług w witrynie Azure Marketplace upraszcza zarządzanie zakupami i płatnościami klientów. Aby uzyskać pełną listę usług, które obecnie używają przedpłaty za platformę Azure, zobacz [aktualizację z 6 marca 2018 r. w witrynie platformy Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/).

### <a name="partners"></a>Partnerzy

Dostawca LSP może pobrać cennik witryny Azure Marketplace ze strony arkusza cen w witrynie Azure Enterprise Portal. Wybierz link **Cennik witryny Marketplace** w prawym górnym rogu. Cennik witryny Azure Marketplace zawiera wszystkie dostępne usługi i ich ceny.

Ab pobrać cennik:

1. W witrynie Azure Enterprise Portal przejdź do obszaru **Raporty** > **Arkusz cen**.
1. W prawym górnym rogu odszukaj link do cennika witryny Azure Marketplace pod Twoją nazwą użytkownika.
1. Kliknij prawym przyciskiem myszy link i wybierz polecenie **Zapisz element docelowy jako**.
1. W oknie **Zapisywanie** zmień tytuł dokumentu na `AzureMarketplacePricelist.zip`, co spowoduje zmianę pliku xlsx w plik zip.
1. Po zakończeniu pobierania będziesz mieć plik zip z cennikiem dla określonego kraju.
1. Dostawcy LSP powinni odwoływać się do pliku konkretnego kraju w celu określenia cen specyficznych dla kraju. Dostawcy LSP powinni sprawdzać jednostki SKU, które są całkiem nowe lub wycofane, za pomocą karty **Powiadomienia**.
1. Zmiany cen są rzadkie. W przypadku ich wystąpienia dostawcy LSP zostaną powiadomieni o podwyżkach oraz o zmianach kursów wymiany z 30-dniowym wyprzedzeniem za pomocą wiadomości e-mail.
1. Dostawcy LSP dostają jedną fakturę dla każdej kombinacji rejestracji, dostawcy ISV i kwartału.

### <a name="enabling-azure-marketplace-purchases"></a>Włączanie zakupów w witrynie Azure Marketplace

Administratorzy przedsiębiorstwa mogą „wyłączać” lub „włączać” zakupy w witrynie Azure Marketplace dla wszystkich subskrypcji platformy Azure w ramach rejestracji. Jeśli administrator przedsiębiorstwa wyłączy zakupy i istnieją subskrypcje platformy Azure, które już mają subskrypcje witryny Azure Marketplace, to te subskrypcje nie zostaną anulowane i pozostaną bez zmian.

Mimo że klienci mogą konwertować bezpośrednie subskrypcje platformy Azure na umowy Azure EA, kojarząc je ze swoją rejestracją w witrynie Azure Enterprise Portal, ta akcja nie powoduje automatycznej konwersji subskrypcji podrzędnych.

Aby włączyć zakupy w witrynie Azure Marketplace:

1. Zaloguj się w witrynie Azure Enterprise Portal jako administrator przedsiębiorstwa.
1. Przejdź do pozycji **Manage** (Zarządzaj).
1. W obszarze **Szczegóły rejestracji** wybierz ikonę ołówka obok pozycji **Azure Marketplace**.
1. Przełącz odpowiednio opcję **Włączone/Wyłączone** lub **Tylko jednostki SKU Bezpłatna/BYOL**.
1. Wybierz pozycję **Zapisz**.

> [!NOTE]
> Opcja Tylko jednostki SKU Bezpłatna/BYOL powoduje ograniczenie zakupów i nabywania jednostek SKU witryny Azure Marketplace do jednostek SKU bezpłatnych i typu BYOL.

### <a name="services-billed-hourly-for-azure-ea"></a>Usługi rozliczane godzinowo dla umowy Azure EA

Następujące usługi są rozliczane godzinowo w ramach umowy Enterprise Agreement zamiast według stawki miesięcznej w ramach programu MOSP:

- Application Delivery Network
- Zapora aplikacji internetowej

### <a name="azure-remoteapp"></a>Azure RemoteApp

Jeśli masz umowę Enterprise Agreement, płacisz za usługę Azure RemoteApp na podstawie poziomu cen umowy Enterprise Agreement. Nie są naliczane dodatkowe opłaty. Cena w wersji standardowej obejmuje początkowe 40 godzin. Cena w wersji nieograniczonej obejmuje początkowe 80 godzin. Usługa RemoteApp nie generuje użycia przez 80 godzin.

## <a name="azure-marketplace-faq"></a>Witryna Azure Marketplace — często zadawane pytania

W tej sekcji wyjaśniono, jak przedpłata za platformę Azure może mieć zastosowanie do niektórych usług odsprzedawców zewnętrznych w witrynie Azure Marketplace.

### <a name="what-changed-with-azure-marketplace-services-and-azure-ea-prepayment"></a>Co się zmieniło w związku z usługami witryny Azure Marketplace i przedpłatą za platformę Azure w ramach umowy EA?

Od 1 marca 2018 niektóre usługi odsprzedawców zewnętrznych wykorzystują przedpłatę za platformę Azure w ramach umowy EA. Z wyjątkiem wystąpień zarezerwowanych maszyn wirtualnych platformy Azure (RI, Reserved Instance), wcześniej te usługi były rozliczane poza przedpłatą za platformę Azure w ramach umowy EA i na oddzielnej fakturze.

Rozszerzyliśmy korzystanie z przedpłaty za platformę Azure pod kątem niektórych często kupowanych usług w witrynie Azure Marketplace publikowanych przez dostawców zewnętrznych. Przedpłata za platformę Azure w ramach umowy EA dla tych usług w witrynie Azure Marketplace upraszcza klientom zarządzanie zakupami i płatnościami.

### <a name="why-did-we-make-this-change"></a>Dlaczego wprowadziliśmy tę zmianę?

Klienci nieustannie szukają dodatkowych sposobów korzystania z płatności z góry wniesionych w postaci przedpłaty za platformę Azure. Klienci często prosili o wprowadzenie tej zmiany i ma ona wpływ na znaczną część klientów witryny Azure Marketplace.

### <a name="how-do-you-benefit"></a>Jak na tym zyskasz?

Ta zmiana zapewnia klientom prostsze rozliczenia i możliwość lepszego wykorzystywania przedpłaty za platformę Azure w ramach umowy EA. Dzięki temu, że te usługi są objęte przedpłatą za platformę Azure, przedpłata za platformę Azure w ramach umowy EA zyskuje na wartości.

### <a name="what-azure-marketplace-services-use-azure-ea-prepayment-and-how-do-i-know"></a>Jakie usługi witryny Azure Marketplace zużywają przedpłatę za platformę Azure w ramach umowy EA i jak to ustalić?

Gdy kupujesz usługę, w przypadku której można wykorzystać przedpłatę za platformę Azure, w witrynie Azure Marketplace jest prezentowane oświadczenie o zrzeczeniu się odpowiedzialności. Obsługiwane są niektóre usługi publikowane przez firmy Red Hat, SUSE, Autodesk i Oracle. Obecnie za usługi o podobnych nazwach publikowane przez innych dostawców nie można zapłacić, wykorzystując przedpłatę za platformę Azure. Pełna lista jest dostępna na końcu sekcji często zadawanych pytań.

### <a name="what-if-my-azure-ea-prepayment-runs-out"></a>Co zrobić, jeśli przedpłata za platformę Azure w ramach umowy EA wyczerpie się?

Jeśli wykorzystasz całą swoją przedpłatę za platformę Azure i pojawi się u Ciebie nadwyżka, opłaty związane z tymi usługami pojawią się na następnej fakturze nadwyżkowej wraz z pozostałym użyciem usług. Przed zmianą wprowadzoną 1 marca 2018 r. opłaty te były fakturowane z innymi usługami witryny Azure Marketplace.

### <a name="why-dont-all-azure-marketplaces-consume-azure-ea-prepayment"></a>Dlaczego nie wszystkie usługi witryny Azure Marketplace zużywają przedpłatę za platformę Azure w ramach umowy EA?

Często pracujemy nad zapewnieniem dla klientów najlepszego środowiska związanego z przedpłatą za platformę Azure w ramach umowy EA. Ta zmiana dotyczy dużej liczby klientów i znaczącej części sum wydawanych w witrynie Azure Marketplace. Inne usługi mogą zostać dodane w przyszłości.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Jak wpływa to na rejestracje pośrednie i partnerów?

Nie ma to wpływu na klientów rejestracji pośredniej ani partnerów. Te usługi są objęte tymi samymi możliwościami zastosowania narzutu przez partnera co inne usługi oparte na użyciu. Jedyną zmianą jest to, że opłaty są uwzględniane w innej fakturze, a opłaty są odejmowane od przedpłaty za platformę Azure w ramach umowy EA klienta.

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-azure-ea-prepayment"></a>Czy istnieje lista usług witryny Azure Marketplace, które zużywają przedpłatę za platformę Azure w ramach umowy EA?

Określone oferty z witryny Azure Marketplace mogą zużywać środki przedpłaty za platformę Azure. Sprawdź [usługi dostawców zewnętrznych zużywające przedpłatę za platformę Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment), aby uzyskać pełną listę produktów biorących udział w tym programie.


## <a name="next-steps"></a>Następne kroki

- Uzyskaj więcej informacji na temat [cen](ea-pricing-overview.md).