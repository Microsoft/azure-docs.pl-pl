---
title: Samouczek — obniżanie kosztów platformy Azure dzięki zaleceniom
description: Ten samouczek ułatwia obniżenie kosztów platformy Azure przez wdrożenie zaleceń dotyczących optymalizacji.
author: bandersmsft
ms.author: banders
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 28f9369d044cf9b2713cb2f2ad42f744e517918b
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683287"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>Samouczek: Optymalizowanie kosztów na podstawie zaleceń

Usługa Azure Cost Management współdziała z usługą Azure Advisor podczas ustalania zaleceń dotyczących optymalizacji kosztów. Narzędzie Azure Advisor pomaga przeprowadzić optymalizację i zwiększyć wydajność, identyfikując zasoby w stanie bezczynności i niedostatecznie używane. Ten samouczek przeprowadzi Cię przez przykład, w ramach którego zidentyfikujesz nie w pełni wykorzystywane zasoby platformy Azure, a następnie podejmiesz działania w celu obniżenia kosztów.

Obejrzyj klip wideo [Optymalizacja inwestycji w chmurę w usłudze Azure Cost Management](https://www.youtube.com/watch?v=cSNPoAb-TNc), aby dowiedzieć się więcej o optymalizacji kosztów za pomocą usługi Advisor. Aby obejrzeć inne wideo, odwiedź [kanał usługi Cost Management w serwisie YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/cSNPoAb-TNc]

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetlanie zaleceń dotyczących optymalizacji kosztów, aby zidentyfikować potencjalne przypadki nieefektywnego użycia
> * Wdrażanie zalecenia przez zmianę rozmiaru maszyny wirtualnej na bardziej ekonomiczną opcję
> * Weryfikacja akcji, aby upewnić się, że rozmiar maszyny wirtualnej został pomyślnie zmieniony

## <a name="prerequisites"></a>Wymagania wstępne
Zalecenia są dostępne dla różnych zakresów i typów kont platformy Azure. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](understand-cost-mgt-data.md). Aby wyświetlać dane kosztów, musisz mieć co najmniej prawa dostępu do odczytu co najmniej jednego z poniższych zakresów. Aby uzyskać więcej informacji na temat zakresów, zobacz [Omówienie zakresów i praca z nimi](understand-work-scopes.md).

- Subskrypcja
- Grupa zasobów

Jeśli masz nową subskrypcję, nie możesz od razu korzystać z funkcji usługi Cost Management. Aby można było korzystać ze wszystkich funkcji usługi Cost Management, może upłynąć do 48 godzin. Ponadto musisz mieć aktywne maszyny wirtualne działające od co najmniej 14 dni.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com/).

## <a name="view-cost-optimization-recommendations"></a>Wyświetlanie zaleceń dotyczących optymalizacji kosztów

Aby wyświetlić zalecenia dotyczące optymalizacji kosztów subskrypcji, otwórz żądany zakres w witrynie Azure Portal i wybierz pozycję **Zalecenia doradcy**.

Aby wyświetlić zalecenia dotyczące grupy zarządzania, otwórz żądany zakres w witrynie Azure Portal i wybierz pozycję **Analiza kosztów** z menu. Użyj kapsułki **Zakres**, aby przełączyć się na inny zakres, np. grupę zarządzania. Wybierz pozycję **Zalecenia doradcy** z menu. Aby uzyskać więcej informacji na temat zakresów, zobacz [Omówienie zakresów i praca z nimi](understand-work-scopes.md).

![Zalecenia doradcy dotyczące zarządzania kosztami wyświetlane w witrynie Azure Portal](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

Lista zaleceń wskazuje nieefektywne użycie zasobów lub pokazuje zalecenia dotyczące zakupów, które mogą pomóc w uzyskaniu dalszych oszczędności. Zsumowana wartość w polu **Potencjalne roczne oszczędności** pokazuje łączną kwotę, którą można zaoszczędzić w przypadku zamknięcia lub cofnięcia przydziału wszystkich maszyn wirtualnych spełniających reguły zaleceń. Jeśli nie chcesz ich zamykać, rozważ zmianę ich rozmiarów na tańszą jednostkę SKU maszyny wirtualnej.

Kategorię **Wpływ** wraz z wartością **Potencjalne roczne oszczędności** zaprojektowano, aby pomóc w identyfikacji tych zaleceń, które zapewnią największe oszczędności.

Zalecenia o wysokim wpływie to m.in.:
- [Kup wystąpienia zarezerwowane maszyn wirtualnych w celu zaoszczędzenia pieniędzy w porównaniu z płatnością zgodnie z rzeczywistym użyciem](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [Zoptymalizuj wydatki na maszyny wirtualne przez zmianę rozmiaru lub zamknięcie nie w pełni wykorzystywanych wystąpień](../../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Używaj usługi Standard Storage do przechowywania migawek funkcji Dyski zarządzane](../../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

Zalecenia o średnim wpływie to m.in.:
- [Usuń potoki usługi Azure Data Factory, które kończą się niepowodzeniem](../../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Zmniejsz koszty, eliminując nieaprowizowane obwody usługi ExpressRoute](../../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Zmniejsz koszty, usuwając lub ponownie konfigurując bezczynne bramy sieci wirtualnej](../../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>Wdrażanie zalecenia

Usługa Azure Advisor monitoruje użycie maszyn wirtualnych przez siedem dni, a następnie identyfikuje nie w pełni wykorzystywane maszyny wirtualne. Maszyny wirtualne, których wykorzystanie procesora CPU wynosi pięć procent lub mniej, a użycie sieci siedem megabajtów lub mniej przez co najmniej cztery dni, są uznawane za maszyny wirtualne o niskim wykorzystaniu.

Ustawienie użycia procesora w wysokości 5% lub mniej to wartość domyślna, ale to ustawienie można dostosować. Aby uzyskać więcej informacji na temat dostosowywania ustawienia, zobacz [Konfigurowanie reguły średniego wykorzystania CPU lub zalecenia dotyczącego niskiego użycia maszyny wirtualnej](../../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation).

Chociaż w niektórych scenariuszach niskie wykorzystanie jest zgodne z projektem, często można zaoszczędzić pieniądze, zmieniając rozmiar maszyn wirtualnych na mniej kosztowne. Rzeczywiste oszczędności mogą się różnić w przypadku wybrania akcji zmiany rozmiaru. Przejdźmy teraz przez przykład zmiany rozmiaru maszyny wirtualnej.

Na liście zaleceń kliknij zalecenie **Ustaw prawidłowy rozmiar nie w pełni wykorzystywanych maszyn wirtualnych lub zamknij je**. Z listy kandydatów wybierz maszynę wirtualną, której rozmiar chcesz zmienić, a następnie kliknij ją. Zostaną wyświetlone szczegóły maszyny wirtualnej, aby można było zweryfikować metryki wykorzystania. Wartość **potencjalne roczne oszczędności** wskazuje, ile można zaoszczędzić w przypadku zamknięcia lub usunięcia maszyny wirtualnej. Zmiana rozmiaru maszyny wirtualnej prawdopodobnie pozwoli zaoszczędzić pieniądze, ale nie pełną kwotę potencjalnych rocznych oszczędności.

![Przykład szczegółów zalecenia](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

W szczegółach maszyny wirtualnej sprawdź jej wykorzystanie, aby potwierdzić, że jest odpowiednim kandydatem do zmiany rozmiaru.

![Przykładowe szczegóły maszyny wirtualnej przedstawiające historyczne wykorzystanie](./media/tutorial-acm-opt-recommendations/vm-details.png)

Zanotuj obecny rozmiar maszyny wirtualnej. Po upewnieniu się, że rozmiar maszyny wirtualnej powinien zostać zmieniony, zamknij szczegóły maszyny wirtualnej, aby wyświetlić listę maszyn wirtualnych.

Na liście kandydatów do zamknięcia lub zmiany rozmiaru wybierz pozycję **Zmień rozmiar _&lt;ŹródłowaJednostkaSKUMaszynyWirtualnej&gt;_ na _&lt;DocelowaJednostkaSKUMaszynyWirtualnej&gt;_** .
![Przykładowe zalecenie z opcją zmiany rozmiaru maszyny wirtualnej](./media/tutorial-acm-opt-recommendations/resize-vm.png)

Następnie zostanie wyświetlona lista dostępnych opcji zmiany rozmiaru. Wybierz tę, która zapewni najlepszą wydajność i będzie najbardziej ekonomiczna w danym scenariuszu. Opcja w poniższym przykładzie spowoduje zmianę rozmiaru z **Standard_D8s_v3** na **Standard_D2s_v3**.

![Przykładowa lista dostępnych rozmiarów maszyn wirtualnych, z której można wybrać rozmiar](./media/tutorial-acm-opt-recommendations/choose-size.png)

Po wybraniu odpowiedniego rozmiaru kliknij przycisk **Zmień rozmiar**, aby wykonać akcję zmiany rozmiaru.

Zmiana rozmiaru wymaga ponownego uruchomienia aktywnie działającej maszyny wirtualnej. Jeśli maszyna wirtualna znajduje się w środowisku produkcyjnym, zalecamy uruchomienie operacji zmiany rozmiaru po godzinach pracy. Zaplanowanie ponownego uruchomienia może zmniejszyć zakłócenia pracy spowodowane przez chwilową niedostępność.

## <a name="verify-the-action"></a>Weryfikowanie akcji

Po pomyślnym ukończeniu zmiany rozmiaru maszyny wirtualnej zostanie wyświetlone powiadomienie platformy Azure.

![Powiadomienie o pomyślnej zmianie rozmiaru maszyny wirtualnej](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetlanie zaleceń dotyczących optymalizacji kosztów, aby zidentyfikować potencjalne przypadki nieefektywnego użycia
> * Wdrażanie zalecenia przez zmianę rozmiaru maszyny wirtualnej na bardziej ekonomiczną opcję
> * Weryfikacja akcji, aby upewnić się, że rozmiar maszyny wirtualnej został pomyślnie zmieniony

Zapoznaj się z artykułem Najlepsze rozwiązania dotyczące zarządzania kosztami, jeśli jeszcze tego nie zrobiono. Zawiera on ogólne wskazówki i reguły, które należy wziąć pod uwagę w celu ułatwienia zarządzania kosztami.

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dotyczące zarządzania kosztami](cost-mgt-best-practices.md)
