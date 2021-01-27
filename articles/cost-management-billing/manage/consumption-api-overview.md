---
title: Przegląd interfejsu API użycia platformy Azure
description: Dowiedz się, jak interfejsy API użycia platformy Azure zapewniają programowy dostęp do danych dotyczących kosztów i użycia zasobów platformy Azure.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: reference
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 4b8b24bacaee87dc9868fab1d5d071201a7215b8
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897734"
---
# <a name="azure-consumption-api-overview"></a>Przegląd interfejsu API użycia platformy Azure

Interfejsy API użycia platformy Azure zapewniają programowy dostęp do danych dotyczących kosztów i użycia zasobów platformy Azure. Te interfejsy API obecnie obsługują tylko rejestracje Enterprise i bezpośrednie subskrypcje internetowe (z kilkoma wyjątkami). Interfejsy API są stale aktualizowane do obsługi innych typów subskrypcji platformy Azure.

Interfejsy API użycia platformy Azure zapewniają dostęp do następujących elementów:
- Bezpośredni klienci internetowi i klienci korporacyjni
    - Szczegóły użycia
    - Opłaty za korzystanie z witryny Marketplace
    - Rekomendacje dotyczące rezerwacji
    - Szczegóły rezerwacji
    - Podsumowania rezerwacji
- Tylko klienci korporacyjni
    - Arkusz cen
    - Budżety
    - Salda

## <a name="usage-details-api"></a>Interfejs API szczegółów użycia

Interfejs API szczegółów użycia umożliwia uzyskanie informacji o opłatach i użyciu dla wszystkich podstawowych zasobów platformy Azure. Informacje te mają postać szczegółowych rekordów i aktualnie są udostępniane raz dziennie dla jednego miernika i każdego zasobu. Te informacje mogą służyć do sumowania kosztów wszystkich zasobów lub analizowania kosztów/użycia określonych zasobów.

Ten interfejs API zawiera następujące składniki:

-    **Dane dotyczące zużycia na poziomie mierników** — umożliwia wyświetlanie danych obejmujących koszt użycia, miernik emitujący opłatę oraz zasób platformy Azure, którego dotyczy ta opłata. Wszystkie rekordy szczegółów użycia są mapowane na zasobnik dzienny.
-    **Kontrola dostępu oparta na rolach (Azure RBAC)** na platformie Azure — Konfigurowanie zasad dostępu na [Azure Portal](https://portal.azure.com), [interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md) [Azure PowerShell lub poleceń cmdlet](/powershell/azure/) , aby określić, którzy użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia subskrypcji. Obiekty wywołujące muszą używać standardowych tokenów usługi Azure Active Directory na potrzeby uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczeń, Czytelnik, Właściciel lub Współautor, aby uzyskać dostęp do danych użycia dla określonej subskrypcji platformy Azure.
-    **Filtrowanie** — umożliwia przycinanie zestawu wyników interfejsu API do mniejszego zestawu rekordów szczegółów użycia przy użyciu następujących filtrów:
    - Koniec użycia / początek użycia
    - Grupa zasobów
    - Nazwa zasobu
-    **Agregowanie danych** — użycie protokołu OData umożliwia zastosowanie wyrażeń do agregowania szczegółów użycia według tagów lub właściwości filtru
-    **Użycie dla różnych typów ofert** — szczegółowe informacje dotyczące użycia są obecnie dostępne dla klientów korzystających z rejestracji Enterprise i bezpośrednich subskrypcji internetowych.

Aby uzyskać więcej informacji, zobacz specyfikację techniczną [interfejsu API szczegółów użycia](/rest/api/consumption/usagedetails).

## <a name="marketplace-charges-api"></a>Interfejs API opłat za korzystanie z witryny Marketplace

Interfejs API opłat za korzystanie z witryny Marketplace umożliwia uzyskanie danych dotyczących opłat i użycia za wszystkie zasoby witryny Marketplace (oferty innych firm na platformie Azure). Tych danych można użyć do sumowania kosztów wszystkich zasobów witryny Marketplace lub analizowania kosztów / użycia określonych zasobów.

Ten interfejs API zawiera następujące składniki:

-    **Dane dotyczące zużycia na poziomie mierników** — umożliwia wyświetlanie danych obejmujących koszt użycia witryny Marketplace, miernik emitujący opłatę oraz zasób, którego dotyczy ta opłata. Wszystkie rekordy szczegółów użycia są mapowane na zasobnik dzienny.
-    **Kontrola dostępu oparta na rolach (Azure RBAC)** na platformie Azure — Konfigurowanie zasad dostępu na [Azure Portal](https://portal.azure.com), [interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md) [Azure PowerShell lub poleceń cmdlet](/powershell/azure/) , aby określić, którzy użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia subskrypcji. Obiekty wywołujące muszą używać standardowych tokenów usługi Azure Active Directory na potrzeby uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczeń, Czytelnik, Właściciel lub Współautor, aby uzyskać dostęp do danych użycia dla określonej subskrypcji platformy Azure.
-    **Filtrowanie** — umożliwia przycinanie zestawu wyników interfejsu API do mniejszego zestawu rekordów dotyczących witryny Marketplace przy użyciu następujących filtrów:
    - Początek użycia / koniec użycia
    - Grupa zasobów
    - Nazwa zasobu
-    **Użycie dla różnych typów ofert** — informacje dotyczące witryny Marketplace są obecnie dostępne dla klientów korzystających z rejestracji Enterprise i bezpośrednich subskrypcji internetowych.

Aby uzyskać więcej informacji, zobacz specyfikację techniczną [interfejsu API opłat za korzystanie z witryny Marketplace](/rest/api/consumption/marketplaces).

## <a name="balances-api"></a>Interfejs API sald

Klienci korporacyjni mogą korzystać z interfejsu API sald, aby uzyskiwać comiesięczne podsumowanie informacji dotyczących sald, nowych zakupów, opłat za usługę Azure Marketplace, korekt i opłat za użycie nadwyżkowe. Informacje te mogą dotyczyć bieżącego okresu rozliczeniowego lub dowolnego okresu w przeszłości. Przedsiębiorstwa mogą porównywać te dane z ręcznie obliczonymi podsumowaniami opłat. Ten interfejs API nie udostępnia informacji dotyczących określonych zasobów ani zagregowanego widoku kosztów.

Ten interfejs API zawiera następujące składniki:

-    **Kontrola dostępu oparta na rolach (Azure RBAC)** na platformie Azure — Konfigurowanie zasad dostępu na [Azure Portal](https://portal.azure.com), [interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md) [Azure PowerShell lub poleceń cmdlet](/powershell/azure/) , aby określić, którzy użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia subskrypcji. Obiekty wywołujące muszą używać standardowych tokenów usługi Azure Active Directory na potrzeby uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczeń, Czytelnik, Właściciel lub Współautor, aby uzyskać dostęp do danych użycia dla określonej subskrypcji platformy Azure.
-    **Tylko klienci korporacyjni** — ten interfejs API jest dostępny tylko dla klientów z umowami EA.
    - Aby móc wywoływać ten interfejs API, klienci muszą mieć uprawnienia administratora przedsiębiorstwa

Aby uzyskać więcej informacji, zobacz specyfikację techniczną [interfejsu API sald](/rest/api/consumption/balances).

## <a name="budgets-api"></a>Interfejs API budżetów

Klienci korporacyjni mogą używać tego interfejsu API do tworzenia budżetów kosztów lub budżetów użycia dla zasobów, grup zasobów bądź mierników rozliczeń. Po ustaleniu tych informacji można skonfigurować alerty do powiadamiania o przekroczeniu progów budżetu zdefiniowanych przez użytkownika.

Ten interfejs API zawiera następujące składniki:

-    **Kontrola dostępu oparta na rolach (Azure RBAC)** na platformie Azure — Konfigurowanie zasad dostępu na [Azure Portal](https://portal.azure.com), [interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md) [Azure PowerShell lub poleceń cmdlet](/powershell/azure/) , aby określić, którzy użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia subskrypcji. Obiekty wywołujące muszą używać standardowych tokenów usługi Azure Active Directory na potrzeby uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczeń, Czytelnik, Właściciel lub Współautor, aby uzyskać dostęp do danych użycia dla określonej subskrypcji platformy Azure.
-    **Tylko klienci korporacyjni** — ten interfejs API jest dostępny tylko dla klientów z umowami EA.
-    **Powiadomienia konfigurowalne** — umożliwia określenie użytkowników, którzy zostaną powiadomieni o wyzwoleniu budżetu.
-    **Budżety na podstawie użycia lub kosztów** — umożliwia tworzenie budżetu na podstawie zużycia lub kosztów w zależności od potrzeb scenariusza.
-    **Filtrowanie** — umożliwia filtrowanie budżetu do mniejszego podzestawu zasobów przy użyciu następujących konfigurowalnych filtrów
    - Grupa zasobów
    - Nazwa zasobu
    - Miernik
-    **Konfigurowalne okresy budżetowe** — umożliwia określenie, jak często budżet ma być resetowany oraz przez jaki okres obowiązuje.

Aby uzyskać więcej informacji, zobacz specyfikację techniczną [interfejsu API budżetów](/rest/api/consumption/budgets).

## <a name="reservation-recommendations-api"></a>Interfejs API zaleceń dotyczących rezerwacji

Ten interfejs API umożliwia uzyskanie zaleceń dotyczących zakupu wystąpień zarezerwowanych maszyn wirtualnych. Zalecenia pozwalają klientom analizować oczekiwane oszczędności dotyczące kosztów i kwoty zakupu.

Ten interfejs API zawiera następujące składniki:

-    **Kontrola dostępu oparta na rolach (Azure RBAC)** na platformie Azure — Konfigurowanie zasad dostępu na [Azure Portal](https://portal.azure.com), [interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md) [Azure PowerShell lub poleceń cmdlet](/powershell/azure/) , aby określić, którzy użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia subskrypcji. Obiekty wywołujące muszą używać standardowych tokenów usługi Azure Active Directory na potrzeby uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczeń, Czytelnik, Właściciel lub Współautor, aby uzyskać dostęp do danych użycia dla określonej subskrypcji platformy Azure.
-    **Filtrowanie** — umożliwia dostosowanie wyników zaleceń przy użyciu następujących filtrów:
    - Zakres
    - Okres wsteczny
-    **Informacje o rezerwacjach dla różnych typów ofert** — informacje dotyczące rezerwacji są obecnie dostępne dla klientów korzystających z rejestracji Enterprise i bezpośrednich subskrypcji internetowych.

Aby uzyskać więcej informacji, zobacz specyfikację techniczną [interfejsu API zaleceń dotyczących rezerwacji](/rest/api/consumption/reservationrecommendations).

## <a name="reservation-details-api"></a>Interfejs API szczegółów rezerwacji

Interfejs API szczegółów rezerwacji umożliwia wyświetlanie informacji dotyczących wcześniej zakupionych rezerwacji maszyn wirtualnych, takich jak wielkość zarezerwowanego zużycia względem rzeczywistego zużycia. Dane można wyświetlić na poziomie poszczególnych maszyn wirtualnych.

Ten interfejs API zawiera następujące składniki:

-    **Kontrola dostępu oparta na rolach (Azure RBAC)** na platformie Azure — Konfigurowanie zasad dostępu na [Azure Portal](https://portal.azure.com), [interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md) [Azure PowerShell lub poleceń cmdlet](/powershell/azure/) , aby określić, którzy użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia subskrypcji. Obiekty wywołujące muszą używać standardowych tokenów usługi Azure Active Directory na potrzeby uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczeń, Czytelnik, Właściciel lub Współautor, aby uzyskać dostęp do danych użycia dla określonej subskrypcji platformy Azure.
-    **Filtrowanie** — umożliwia przycinanie zestawu wyników interfejsu API do mniejszego zestawu rezerwacji przy użyciu następujących filtrów:
    - Zakres dat
-    **Informacje o rezerwacjach dla różnych typów ofert** — informacje dotyczące rezerwacji są obecnie dostępne dla klientów korzystających z rejestracji Enterprise i bezpośrednich subskrypcji internetowych.

Aby uzyskać więcej informacji, zobacz specyfikację techniczną [interfejsu API szczegółów rezerwacji](/rest/api/consumption/reservationsdetails).

## <a name="reservation-summaries-api"></a>Interfejs API podsumowań rezerwacji

Ten interfejs API umożliwia wyświetlanie zagregowanych informacji dotyczących wcześniej zakupionych rezerwacji maszyn wirtualnych, takich jak wielkość zarezerwowanego zużycia względem rzeczywistego zużycia w agregacji.

Ten interfejs API zawiera następujące składniki:

-    **Kontrola dostępu oparta na rolach (Azure RBAC)** na platformie Azure — Konfigurowanie zasad dostępu na [Azure Portal](https://portal.azure.com), [interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md) [Azure PowerShell lub poleceń cmdlet](/powershell/azure/) , aby określić, którzy użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia subskrypcji. Obiekty wywołujące muszą używać standardowych tokenów usługi Azure Active Directory na potrzeby uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczeń, Czytelnik, Właściciel lub Współautor, aby uzyskać dostęp do danych użycia dla określonej subskrypcji platformy Azure.
-    **Filtrowanie** — umożliwia dostosowanie uzyskiwanych codziennie wyników za pomocą następującego filtru:
    - Data wykorzystania
-    **Informacje o rezerwacjach dla różnych typów ofert** — informacje dotyczące rezerwacji są obecnie dostępne dla klientów korzystających z rejestracji Enterprise i bezpośrednich subskrypcji internetowych.
-    **Agregacje dzienne lub miesięczne** — obiekty wywołujące mogą określać, czy chcą otrzymywać dane podsumowania rezerwacji codziennie, czy co miesiąc.

Aby uzyskać więcej informacji, zobacz specyfikację techniczną [interfejsu API podsumowań rezerwacji](/rest/api/consumption/reservationssummaries).

## <a name="price-sheet-api"></a>Interfejs API arkusza cen
Klient korporacyjny może użyć tego interfejsu API do pobrania cen niestandardowych wszystkich mierników. Przedsiębiorstwa mogą łączyć te dane ze szczegółami użycia oraz informacjami o użyciu witryny Marketplace, aby wykonywać obliczenia kosztów.

Ten interfejs API zawiera następujące składniki:

-    **Kontrola dostępu oparta na rolach (Azure RBAC)** na platformie Azure — Konfigurowanie zasad dostępu na [Azure Portal](https://portal.azure.com), [interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md) [Azure PowerShell lub poleceń cmdlet](/powershell/azure/) , aby określić, którzy użytkownicy lub aplikacje mogą uzyskać dostęp do danych użycia subskrypcji. Obiekty wywołujące muszą używać standardowych tokenów usługi Azure Active Directory na potrzeby uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczeń, Czytelnik, Właściciel lub Współautor, aby uzyskać dostęp do danych użycia dla określonej subskrypcji platformy Azure.
-    **Tylko klienci korporacyjni** — ten interfejs API jest dostępny tylko dla klientów z umowami EA. Klienci korzystający z bezpośrednich subskrypcji internetowych powinni korzystać z interfejsu API RateCard, jeśli chcą uzyskać informacje o cenach.

Aby uzyskać więcej informacji, zobacz specyfikację techniczną [interfejsu API arkusza cen](/rest/api/consumption/pricesheet).

## <a name="scenarios"></a>Scenariusze

Poniżej przedstawiono niektóre scenariusze, które mogą wystąpić podczas korzystania z interfejsów API użycia:

-    **Uzgadnianie faktur** — czy opłata naliczona przez firmę Microsoft jest właściwa?  Jaki jest mój rachunek i czy mogę go obliczyć samodzielnie?
-    **Opłaty krzyżowe** — znam już wysokość opłaty. Kto w mojej organizacji powinien zapłacić?
-    **Optymalizacja kosztów** — znam wysokość naliczonej opłaty… Jak zoptymalizować wydatki na platformę Azure?
-    **Śledzenie kosztów** — chcę sprawdzić, ile wydaję na platformę Azure i jak wygląda jej użycie w czasie. Jakie są trendy? Co mogę usprawnić?
-    **Poświęcane na platformę Azure w ciągu miesiąca** — jaka jest stawka mojego bieżącego miesiąca? Czy należy w jakiś sposób dopasować moje wydatki i/lub użycie platformy Azure? W jakie dni miesiąca użycie platformy Azure jest największe?
-    **Konfigurowanie alertów** — chcę skonfigurować użycie oparte na zasobach lub alerty pieniężne oparte na budżecie.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o korzystaniu z interfejsów API REST pobieranie cen dla wszystkich usług platformy Azure, zobacz [ceny detaliczne platformy Azure — omówienie](/rest/api/cost-management/retail-prices/azure-retail-prices).
