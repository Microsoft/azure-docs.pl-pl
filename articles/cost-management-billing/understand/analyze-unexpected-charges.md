---
title: Analizowanie nieoczekiwanych opłat za platformę Azure
description: Dowiedz się, jak analizować nieoczekiwane opłaty za subskrypcję platformy Azure.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 6d5198f47b6089730ff345eb12ab522fc5ce58a1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575377"
---
# <a name="analyze-unexpected-charges"></a>Analizowanie nieoczekiwanych opłat

Infrastruktura zasobów w chmurze utworzona dla organizacji jest prawdopodobnie złożona. Na platformie Azure danego typu zasobu mogą dotyczyć różne typy opłat. Zasoby platformy Azure mogą należeć do różnych zespołów w organizacji i mogą być objęte różnymi typami modeli rozliczeń, które stosują się do różnych zasobów. Aby lepiej zrozumieć opłaty, rozpocznij ich analizowanie przy użyciu jednej lub kilku strategii opisanych w poniższych sekcjach.

## <a name="review-invoice-for-resource-responsible-for-charge"></a>Przegląd faktury dla zasobu odpowiedzialnego za opłaty

Sposób kupowania usług platformy Azure pomaga określić metodologię i narzędzia dostępne na potrzeby identyfikowania zasobu skojarzonego z opłatą. Aby określić metodologię, która dotyczy Ciebie, najpierw [określ typ oferty platformy Azure](../costs/understand-cost-mgt-data.md#determine-your-offer-type). Następnie określ swoją kategorię klienta na liście [obsługiwanych ofert platformy Azure](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers).

Poniższe artykuły zawierają szczegółowe kroki przeglądania rachunku na podstawie typu klienta. W każdym artykule znajdują się instrukcje dotyczące pobierania pliku CSV zawierającego dane użycia i szczegóły kosztów dla danego okresu rozliczeniowego.

- [Proces przeglądania rachunku dla płatności zgodnie z rzeczywistym użyciem](review-individual-bill.md#charges)
- [Proces przeglądania rachunku dla umowy Enterprise Agreement](review-enterprise-agreement-bill.md)
- [Proces przeglądania dla Umowy z Klientem Microsoft](review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Proces przeglądania dla umowy Microsoft Partner Agreement](review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

Rachunek platformy Azure agreguje miesięczne opłaty dla _poszczególnych mierników_. Mierniki są używane do śledzenia użycia zasobu w czasie i obliczenia rachunku. Podczas tworzenia pojedynczego zasobu platformy Azure, takiego jak maszyna wirtualna, zostanie dla niego utworzone co najmniej jedno wystąpienie miernika.

Przefiltruj plik CSV użycia na podstawie wartości _MeterName_ znajdującej się na rachunku do analizy, aby zobaczyć wszystkie pozycje dotyczące miernika. Wartość _InstanceID_ dla pozycji odpowiada rzeczywistemu zasobowi platformy Azure, który wygenerował opłatę.

Po zidentyfikowaniu zasobu można użyć funkcji analizy kosztów w usłudze Azure Cost Management, aby dalej analizować koszty związane z zasobem. Aby dowiedzieć się więcej o korzystaniu z analizy kosztów, zobacz [Rozpoczęcie analizowania kosztów](../costs/quick-acm-cost-analysis.md).

## <a name="review-invoiced-charges-in-cost-analysis"></a>Przeglądanie zafakturowanych kosztów w analizie kosztów

Aby wyświetlić szczegóły faktury w witrynie Azure Portal, przejdź do analizy kosztów dla zakresu skojarzonego z analizowaną fakturą. Wybierz widok **Szczegóły faktury**. Szczegóły faktury przedstawiają opłaty, które są widoczne na fakturze.

[![Przykład pokazujący szczegóły faktury](./media/analyze-unexpected-charges/invoice-details.png)](./media/analyze-unexpected-charges/invoice-details.png#lightbox)

Przeglądając szczegóły faktury, możesz zidentyfikować usługę z nieoczekiwanymi kosztami i określić, które zasoby są bezpośrednio skojarzone z zasobem w analizie kosztów. Jeśli na przykład chcesz przeanalizować opłaty za usługę Virtual Machines, przejdź do widoku **Skumulowany koszt**. Następnie ustaw stopień szczegółowości na **Codziennie**, ustaw filtr opłat **Nazwa usługi: Virtual Machines** i pogrupuj opłaty według wartości **Zasób**.

[![Przykład przedstawiający skumulowane koszty maszyn wirtualnych](./media/analyze-unexpected-charges/virtual-machines.png)](./media/analyze-unexpected-charges/virtual-machines.png#lightbox)

## <a name="identify-spikes-in-cost-over-time"></a>Identyfikowanie skoków kosztów w czasie

Czasem możesz nie wiedzieć, jakie ostatnio poniesione koszty spowodowały zmiany w opłatach na rachunku. Aby poznać zmiany, możesz użyć analizy kosztów do [wyświetlenia dziennego lub miesięcznego podziału kosztów w czasie](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month). Po utworzeniu widoku pogrupuj opłaty według **usługi** lub **zasobu**, aby zidentyfikować zmiany. Możesz także zmienić widok na wykres **liniowy**, aby lepiej zwizualizować dane.

![Przykład przedstawiający koszty w ramach analizy kosztów](./media/analyze-unexpected-charges/costs-over-time.png)

## <a name="determine-resource-pricing-and-billing-model"></a>Określanie ceny zasobu i modelu rozliczeń

Pojedynczy zasób może spowodować naliczenie opłat w ramach wielu produktów i usług platformy Azure. Aby dowiedzieć się więcej o cenach dla każdej usługi platformy Azure, zobacz [cennik platformy Azure według produktu](https://azure.microsoft.com/pricing/#product-pricing). Na przykład pojedyncza maszyna wirtualna utworzona na platformie Azure może mieć utworzone następujące mierniki do śledzenia jej użycia. Każdego może dotyczyć inna cena.

- Godziny obliczeniowe
- Godziny korzystania z adresu IP
- Transfer danych przychodzących
- Transfer danych wychodzących
- Dysk zarządzany w warstwie Standardowa
- Operacje dysków zarządzanych w warstwie Standardowa
- We/Wy w warstwie Standardowa — dysk
- We/Wy w warstwie Standardowa — odczyt blokowych obiektów blob
- We/Wy w warstwie Standardowa — zapis blokowych obiektów blob
- Standardowe we/wy — usuwanie dla blokowych obiektów blob

Po utworzeniu maszyny wirtualnej każdy miernik rozpoczyna emitowanie rekordów użycia. Użycie i cena miernika są śledzone w systemie pomiarów platformy Azure. Liczniki, które zostały użyte do obliczenia rachunku, są pokazywane w pliku CSV użycia.

## <a name="find-people-responsible-for-the-resource-and-engage"></a>Znajdowanie osób odpowiedzialnych za zasób i kontaktowanie się z nimi

Często zespół odpowiedzialny za dany zasób będzie wiedzieć o zmianach wprowadzonych dla zasobu. Skontaktowanie się z nim jest przydatne podczas określania opłat, które mogą się pojawić. Na przykład zespół będący właścicielem mógł niedawno utworzyć zasób, zaktualizować jego jednostkę SKU (zmieniając w związku z tym współczynnik zasobu) lub zwiększyć obciążenie zasobu z powodu zmian w kodzie. Kontynuuj czytanie kolejnych sekcji, aby uzyskać informacje na temat innych technik określania właściciela zasobu.

### <a name="analyze-the-audit-logs-for-the-resource"></a>Analizowanie dzienników inspekcji dla zasobu

Jeśli masz uprawnienia do wyświetlania zasobu, powinno to także wystarczyć do uzyskania dostępu do jego dzienników inspekcji. Przejrzyj dzienniki, aby znaleźć użytkownika odpowiedzialnego za ostatnie zmiany w zasobie. Aby dowiedzieć się więcej, zobacz [Wyświetlanie i pobieranie zdarzeń dziennika aktywności platformy Azure](../../azure-monitor/essentials/activity-log.md#view-the-activity-log).

### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>Analizowanie uprawnień użytkowników do nadrzędnego zakresu zasobu

Osoby z dostępem do zapisu do subskrypcji lub grupy zasobów zwykle mają informacje o utworzonych zasobach. Powinny być w stanie wyjaśnić przeznaczenie zasobu lub wskazać osobę, która je zna. Aby zidentyfikować osoby z uprawnieniami do zakresu subskrypcji, zobacz [Sprawdzanie dostępu użytkownika do zasobów platformy Azure](../../role-based-access-control/check-access.md). Podobnego procesu możesz użyć dla grup zasobów.

## <a name="get-help-to-identify-charges"></a>Uzyskiwanie pomocy w związku z identyfikowaniem opłat

Jeśli powyższe strategie są już Ci znane i nadal nie rozumiesz źródła opłaty lub potrzebujesz innej pomocy dotyczącej problemu z rozliczeniem, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się na temat sposobu [optymalizowania inwestycji w chmurę za pomocą usługi Azure Cost Management](../costs/cost-mgt-best-practices.md).