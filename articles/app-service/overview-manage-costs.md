---
title: Zaplanuj zarządzanie kosztami App Service
description: Dowiedz się, jak planować i zarządzać kosztami Azure App Service przy użyciu analizy kosztów w Azure Portal.
ms.custom: subject-cost-optimization
ms.service: app-service
ms.topic: how-to
ms.date: 01/01/2021
ms.openlocfilehash: de55a708ab78844fd8e834db0bd88e12dd66885a
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98186481"
---
# <a name="plan-and-manage-costs-for-azure-app-service"></a>Planowanie i zarządzanie kosztami Azure App Service

<!-- Check out the following published examples:
- [https://docs.microsoft.com/azure/cosmos-db/plan-manage-costs](https://docs.microsoft.com/azure/cosmos-db/plan-manage-costs)
- [https://docs.microsoft.com/azure/storage/common/storage-plan-manage-costs](https://docs.microsoft.com/azure/storage/common/storage-plan-manage-costs)
- [https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost](https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost)
-->

<!-- Note for Azure service writer: Links to Cost Management articles are full URLS with the ?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn campaign suffix. Leave those URLs intact. They're used to measure traffic to Cost Management articles.
-->

<!-- Note for Azure service writer: Modify the following for your service. -->

W tym artykule opisano sposób planowania i zarządzania kosztami Azure App Service. Najpierw należy skorzystać z kalkulatora cen platformy Azure, aby pomóc w zaplanowaniu App Service kosztów przed dodaniem zasobów usługi do oszacowania kosztów. Następnie podczas dodawania zasobów platformy Azure Przejrzyj szacowane koszty. Po rozpoczęciu korzystania z App Service zasobów należy używać funkcji [Cost Management](https://docs.microsoft.com/azure/cost-management-billing/) do ustawiania budżetów i monitorowania kosztów. Możesz również przejrzeć prognozowane koszty i zidentyfikować trendy wydatków, aby identyfikować obszary, w których może być konieczne działanie. Koszty Azure App Service są tylko częścią miesięcznych kosztów rachunku na korzystanie z platformy Azure. Chociaż w tym artykule wyjaśniono, jak planować i zarządzać kosztami App Service, opłaty są naliczane za wszystkie usługi i zasoby platformy Azure używane w ramach subskrypcji platformy Azure, w tym usługi innych firm.

## <a name="relevant-costs-for-app-service"></a>Odpowiednie koszty App Service

App Service działa w infrastrukturze platformy Azure, która narasta koszt. Ważne jest, aby zrozumieć, że dodatkowa infrastruktura może naliczać koszt. Ten koszt należy zarządzać po wprowadzeniu zmian w wdrożonych zasobach.

### <a name="costs-that-accrue-with-azure-app-service"></a>Koszty naliczane za pomocą Azure App Service

W zależności od funkcji, która jest używana w App Service, można utworzyć następujące zasoby naliczane kosztowo:

- **Plan App Service**  Wymagane do hostowania aplikacji App Service.
- **Warstwa izolowana**  Dla środowiska App Service jest wymagany [Virtual Network](/azure/virtual-network/) .
- **Kopia zapasowa**  Do tworzenia kopii zapasowych wymagane jest [konto magazynu](/azure/storage/) .
- **Dzienniki diagnostyczne**  Możesz wybrać [konto magazynu](/azure/storage/) jako opcję rejestrowania lub zintegrować z [usługą Azure log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md).
- **App Service certyfikaty**  Certyfikaty zakupione na platformie Azure muszą być utrzymywane w [Azure Key Vault](/azure/key-vault/).

Inne zasoby dotyczące kosztów App Service są (zobacz [App Service cennika](https://azure.microsoft.com/pricing/details/app-service/) , aby uzyskać szczegółowe informacje):

- [Domeny App Service](manage-custom-dns-buy-domain.md)  W ramach subskrypcji jest naliczana opłata za rejestrację domeny na rok, jeśli włączysz automatyczne odnawianie.
- [App Service certyfikaty](configure-ssl-certificate.md#import-an-app-service-certificate)  Opłata jednorazowa w momencie zakupu. Jeśli masz wiele domen poddomen do zabezpieczenia, możesz obniżyć koszty, kupując jeden certyfikat wieloznaczny zamiast wielu standardowych certyfikatów.
- [Powiązania certyfikatu opartego na protokole IP](configure-ssl-bindings.md#create-binding)  Powiązanie jest konfigurowane dla certyfikatu na poziomie aplikacji. Koszt jest naliczany dla każdego powiązania. W przypadku warstwy **standardowa** i powyżej pierwsze wiązanie oparte na adresie IP nie jest obciążane.

### <a name="costs-that-might-accrue-after-resource-deletion"></a>Koszty, które mogą naliczać po usunięciu zasobów

Po usunięciu wszystkich aplikacji w planie App Service plan nadal naliczane są opłaty na podstawie skonfigurowanej warstwy cenowej i liczby wystąpień. Aby uniknąć niepotrzebnych opłat, Usuń plan lub Skaluj go do warstwy **bezpłatna** .

Po usunięciu zasobów Azure App Service zasoby z pokrewnych usług platformy Azure mogą nadal istnieć. W dalszym ciągu naliczane są koszty, dopóki nie zostaną usunięte. Na przykład:

- Virtual Network, który został utworzony dla warstwy **izolowanej** App Service plan
- Konta magazynu utworzone w celu przechowywania kopii zapasowych lub dzienników diagnostycznych
- Key Vault utworzone w celu przechowywania certyfikatów App Service
- Rejestruj obszary nazw analityczne, które zostały utworzone w celu wysłania dzienników diagnostycznych
- [Rezerwacje wystąpienia lub sygnatury](#azure-reservations) dla App Service, które jeszcze nie wygasły

### <a name="using-monetary-credit-with-azure-app-service"></a>Korzystanie z środków pieniężnych w Azure App Service

Opłaty za Azure App Service są naliczane za zobowiązania pieniężne z tytułu umowy EA. Nie można jednak użyć kredytu zobowiązania pieniężnego w ramach umowy EA, aby uregulować opłaty za produkty i usługi innych firm, w tym te z portalu Azure Marketplace.

## <a name="estimate-costs"></a>Szacowanie kosztów

Łatwym sposobem oszacowania i optymalizacji kosztów App Service jest użycie [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/).

Aby skorzystać z kalkulatora cen, kliknij przycisk **App Service** na karcie **produkty** . Następnie przewiń w dół do pracy z kalkulatorem. Poniższy zrzut ekranu przedstawia przykład i nie odzwierciedla bieżących cen.

![Przykład przedstawiający szacowany koszt w kalkulatorze cen platformy Azure](media/overview-manage-costs/pricing-calculator.png)

### <a name="review-estimated-costs-in-the-azure-portal"></a>Przeglądanie szacowanych kosztów w witrynie Azure Portal

Podczas tworzenia aplikacji App Service lub planu App Service można zobaczyć szacowane koszty.

Aby utworzyć aplikację i wyświetlić szacowaną cenę:

1. Na stronie Tworzenie przewiń w dół do **App Service plan**, a następnie kliknij pozycję **Utwórz nowy**.
1. Określ nazwę i kliknij przycisk **OK**.
1. Obok pozycji **jednostka SKU i rozmiar** kliknij pozycję **Zmień rozmiar**.
1. Zapoznaj się z szacowaną ceną wyświetlaną w podsumowaniu. Poniższy zrzut ekranu przedstawia przykład i nie odzwierciedla bieżących cen.

    ![Przegląd szacowanego kosztu dla każdej warstwy cenowej w portalu](media/overview-manage-costs/pricing-estimates.png)

Jeśli subskrypcja platformy Azure ma limit wydatków, platforma Azure nie pozwala na korzystanie z wydatków w wysokości. Twoje środki są używane podczas tworzenia i używania zasobów platformy Azure. Po osiągnięciu limitu kredytu wdrożone zasoby są wyłączone dla pozostałej części okresu rozliczeniowego. Nie możesz zmienić limitu kredytowego, ale możesz go usunąć. Aby uzyskać więcej informacji na temat limitów wydatków, zobacz [limit wydatków platformy Azure](../billing/billing-spending-limit.md).

## <a name="optimize-costs"></a>Optymalizowanie kosztów

Na poziomie podstawowym App Service aplikacje są naliczone zgodnie z planem App Service, który je obsługuje. Koszty związane z wdrożeniem App Service zależą od kilku głównych czynników:

- **Warstwa cenowa**  W innym przypadku nazywanym jednostką SKU planu App Service. Wyższe warstwy zapewniają większą liczbę rdzeni procesora CPU, pamięci, magazynu lub funkcji lub ich kombinację.
- Dedykowane warstwy **liczby wystąpień** (podstawowa i powyżej) mogą być skalowane w poziomie, a każde wystąpienie skalowalne w poziomie naliczy koszty.
- **Opłata za pieczęć**  W warstwie izolowanej jest naliczana płaska opłata za środowisko App Service, niezależnie od liczby obsługiwanych aplikacji lub wystąpień procesów roboczych.

Plan App Service może obsługiwać więcej niż jedną aplikację. W zależności od wdrożenia można zaoszczędzić koszty obsługujące więcej aplikacji w jednym planie App Service (np. hostowanie aplikacji w mniej App Serviceych planach).

Aby uzyskać szczegółowe informacje, zobacz [Omówienie planu App Service](overview-hosting-plans.md)

### <a name="non-production-workloads"></a>Obciążenia nieprodukcyjne

Aby przetestować App Service lub swoje rozwiązanie podczas naliczania kosztu niskiego lub minimalnego, możesz zacząć od użycia dwóch warstw cenowych poziomu wejścia — **bezpłatnych** i **udostępnionych**, które są hostowane w wystąpieniach udostępnionych. Aby przetestować aplikację na dedykowanych wystąpieniach o lepszej wydajności, możesz przeprowadzić uaktualnienie do warstwy **podstawowa** , która obsługuje zarówno aplikacje systemu Windows, jak i Linux. 

> [!NOTE]
> Cennik dotyczący tworzenia **i testowania platformy Azure**  W celu przetestowania obciążeń przedprodukcyjnych, które wymagają wyższych warstw (wszystkie warstwy z wyjątkiem **izolowanych**), Subskrybenci programu Visual Studio mogą również korzystać z [cen usługi Azure Dev/Test](https://azure.microsoft.com/pricing/dev-test/), które oferują znaczne rabaty.
>
> Warstwy **bezpłatna** i **współdzielona** , a także rabaty cenowe dotyczące platformy Azure — tworzenie i testowanie nie są objęte umową SLA finansowo.

### <a name="production-workloads"></a>Obciążenia produkcyjne

Obciążenia produkcyjne są dostarczane z zaleceniem dedykowanej warstwy cenowej **standardowej** lub wyższej. Cena jest naliczana w przypadku wyższych warstw, ale zapewnia większą gęstość aplikacji i magazynów oraz bardziej wydajny sprzęt, co zapewnia większą ilość zasobów dla każdego wystąpienia obliczeniowego. Jest to tłumaczenie na mniejszą liczbę wystąpień dla tej samej liczby aplikacji i dlatego niższy koszt. W rzeczywistości **wersja Premium v3** (najwyższa **nieizolowana** warstwa) to najbardziej ekonomiczna metoda obsługi aplikacji w odpowiedniej skali. Aby zwiększyć oszczędności, możesz uzyskać głębokie rabaty w ramach [rezerwacji Premium v3](#azure-reservations).

> [!NOTE]
> **Wersja Premium v3** obsługuje zarówno kontenery systemu Windows, jak i Linux. 

Po wybraniu warstwy cenowej należy zminimalizować wystąpienia bezczynności. W przypadku wdrożenia skalowalnego w poziomie można tracić pieniądze za nieużywane wystąpienia obliczeniowe. Należy [skonfigurować skalowanie](../azure-monitor/platform/autoscale-get-started.md)automatyczne, dostępne w warstwie **standardowa** i powyżej. Tworząc harmonogramy skalowania w poziomie, a także reguły skalowania w poziomie oparte na metrykach, płacisz tylko za wystąpienia, które naprawdę potrzebujesz w danym momencie.

### <a name="azure-reservations"></a>Rezerwacje platformy Azure

Jeśli planujesz używać znanej minimalnej liczby wystąpień obliczeniowych przez jeden rok lub więcej, należy skorzystać z zalet warstwy **Premium v3** i zwiększyć koszt wystąpienia w sposób drastyczny przez zarezerwowanie tych wystąpień w ciągu 1 roku lub 3-letniego. Miesięczne oszczędności kosztów mogą wynosić nawet 55% na wystąpienie. Możliwe są dwa typy rezerwacji:

- **System Windows (lub platforma niezależny od)**  Może dotyczyć wystąpień systemu Windows lub Linux w ramach subskrypcji.
- **Specyficzne dla systemu Linux**  Dotyczy tylko wystąpień systemu Linux w ramach subskrypcji.

Cennik wystąpienia zarezerwowanego ma zastosowanie do odpowiednich wystąpień w ramach subskrypcji, do liczby zarezerwowanych wystąpień. Wystąpienia zarezerwowane są rozliczeniami i nie są powiązane z określonymi wystąpieniami obliczeniowymi. Jeśli uruchomisz mniejszą liczbę wystąpień niż zarezerwujesz w dowolnym momencie okresu rezerwacji, nadal płacisz za wystąpienia zarezerwowane. Jeśli w dowolnym momencie w trakcie okresu rezerwacji uruchomisz więcej wystąpień niż zarezerwujesz, naliczanie standardowego kosztu naliczonego dla dodatkowych wystąpień jest płatne.

**Izolowana** warstwa (środowisko App Service) obsługuje również roczną i 3-letnią rezerwację w obniżonych cenach. Aby uzyskać więcej informacji, zobacz [jak rabaty rezerwacji mają zastosowanie do Azure App Service wyizolowanych sygnatur](../cost-management-billing/reservations/reservation-discount-app-service-isolated-stamp.md).

## <a name="monitor-costs"></a>Monitorowanie kosztów

Gdy korzystasz z zasobów platformy Azure z App Service, naliczane są koszty. Koszty jednostkowe użycia zasobów platformy Azure różnią się w zależności od interwału czasu (sekundy, minuty, godziny i dni). Po rozpoczęciu App Service zostanie naliczone koszty i będzie można zobaczyć koszty w [analizie kosztów](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Korzystając z analizy kosztów, można wyświetlać App Service koszty na wykresach i tabelach dla różnych interwałów czasu. Kilka przykładów jest dziennie, bieżącego i poprzedniego miesiąca oraz roku. Możesz również wyświetlić koszty związane z budżetami i prognozowanymi kosztami. Przełączenie na dłuższe widoki w czasie może pomóc w identyfikowaniu trendów wydatków. I zobaczysz, gdzie mogły wystąpić przespędzanie. Jeśli utworzono budżety, możesz również łatwo zobaczyć, gdzie są przekraczane.
    
Aby wyświetlić koszty App Service w analizie kosztów:

1. Zaloguj się do witryny Azure Portal.
2. Otwórz zakres w Azure Portal a następnie wybierz pozycję **Analiza kosztów** w menu. Na przykład przejdź do pozycji **subskrypcje**, wybierz subskrypcję z listy, a następnie wybierz pozycję  **Analiza kosztów** w menu. Wybierz **zakres** , aby przełączyć się do innego zakresu w analizie kosztów.
3. Domyślnie koszt usług jest pokazywany na pierwszym wykresie pierścieniowym. Wybierz obszar na wykresie z etykietą App Service.

Rzeczywiste koszty miesięczne są wyświetlane po pierwszym otwarciu analizy kosztów. Oto przykład pokazujący wszystkie miesięczne koszty użycia.

![Przykład przedstawiający skumulowane koszty subskrypcji](media/overview-manage-costs/all-costs.png)

Aby zawęzić koszty pojedynczej usługi, na przykład App Service, wybierz pozycję **Dodaj filtr** , a następnie wybierz pozycję **nazwa usługi**. Następnie wybierz pozycję **App Service**.

Oto przykład przedstawiający koszty tylko App Service.

![Przykład przedstawiający skumulowane koszty usługi ServiceName](media/overview-manage-costs/service-specific-costs.png)

W poprzednim przykładzie zobaczysz bieżący koszt usługi. Pokazywane są również koszty według regionów platformy Azure i kosztów App Service według grupy zasobów. W tym miejscu możesz dowiedzieć się o kosztach własnych.

## <a name="create-budgets"></a>Tworzenie budżetów

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

W celu zarządzania kosztami można utworzyć [budżety](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oraz [alerty](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn), które automatycznie powiadamiają uczestników o anomaliach w wydatkach i o ryzyku nadmiernych wydatków. Alerty są oparte na wydatkach w porównaniu z budżetem i progami kosztów. Budżety i alerty są tworzone dla subskrypcji platformy Azure i grup zasobów, dzięki czemu są one przydatne jako część ogólnej strategii monitorowania kosztów. 

Budżety można utworzyć za pomocą filtrów dla określonych zasobów lub usług na platformie Azure, jeśli chcesz mieć większą szczegółowość w monitorowaniu. Filtry ułatwiają zagwarantowanie, że nie zostaną przypadkowo utworzone nowe zasoby, które będą kosztowały dodatkowe pieniądze. Aby uzyskać więcej informacji o opcjach filtru dostępnych podczas tworzenia budżetu, zobacz [Opcje grup i filtrów](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Eksportowanie danych kosztów

Możesz również [wyeksportować dane kosztu](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) do konta magazynu. Jest to przydatne, gdy potrzebne są inne osoby, które przewyższają analizę danych pod kątem kosztów. Na przykład zespół finansowy może analizować dane przy użyciu programu Excel lub Power BI. Możesz wyeksportować koszty codziennie, co tydzień lub miesięcznie i ustawić niestandardowy zakres dat. Eksportowanie danych o kosztach jest zalecanym sposobem na pobranie z nich zbiorów.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o tym, jak ceny współdziałają z usługą Azure Storage. Zobacz [cennik App Service](https://azure.microsoft.com/pricing/details/app-service/).
- Dowiedz się [, jak zoptymalizować inwestycję w chmurę za pomocą Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Dowiedz się więcej o zarządzaniu kosztami przy użyciu [analizy kosztów](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Dowiedz się więcej na temat [zapobiegania nieoczekiwanym kosztom](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Zapoznaj się z [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) kurs uczenia z przewodnikiem.

<!-- Insert links to other articles that might help users save and manage costs for you service here.

Create a table of contents entry for the article in the How-to guides section where appropriate. -->