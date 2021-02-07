---
title: Zwiększaj wskaźnik platformy Azure dla obszarów roboczych i dzierżawców | Microsoft Docs
description: Jak wysyłać zapytania i analizować dane między obszarami roboczymi i dzierżawcami przy użyciu funkcji wskaźnikowej platformy Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2020
ms.author: yelevin
ms.openlocfilehash: dfd8f94dc7ab4e428647b8f44a86a2cd06634e2a
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807245"
---
# <a name="extend-azure-sentinel-across-workspaces-and-tenants"></a>Rozszerzanie usługi Azure Sentinel na obszary robocze i dzierżawy

## <a name="the-need-to-use-multiple-azure-sentinel-workspaces"></a>Potrzeba użycia wielu obszarów roboczych usługi Azure wskaźnikowej

Platforma Azure — Wskaźnikowanie jest tworzona na podstawie obszaru roboczego Log Analytics. Należy zauważyć, że pierwszym krokiem w celu dołączenia do platformy Azure wskaźnikowej jest wybranie obszaru roboczego Log Analytics, który ma być używany w tym celu.

Korzystając z jednego obszaru roboczego, można w pełni wykorzystać możliwości usługi Azure Sentinel. Nawet Dlatego istnieją pewne okoliczności, które mogą wymagać posiadania wielu obszarów roboczych. W poniższej tabeli wymieniono niektóre z tych sytuacji i, jeśli to możliwe, zawarto sugestie dotyczące spełnienia wymagań z jednym obszarem roboczym:

| Wymaganie | Opis | Sposoby zmniejszania liczby obszarów roboczych |
|-------------|-------------|--------------------------------|
| Jurysdykcja i zgodność z przepisami | Obszar roboczy jest powiązany z określonym regionem. Jeśli dane muszą być przechowywane w różnych [lokalizacje geograficzneach platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) w celu spełnienia wymagań prawnych, należy podzielić je na osobne obszary robocze. |  |
| Własność danych | Granice własności danych, na przykład przez podmioty zależne lub firmy stowarzyszone, są lepiej nakreślone przy użyciu oddzielnych obszarów roboczych. |  |
| Wiele dzierżawców platformy Azure | Usługa Azure — wskaźnik wiedzy obsługuje zbieranie danych z zasobów firmy Microsoft i platformy Azure SaaS tylko w ramach Azure Active Directory własnej granicy dzierżawy usługi Azure AD. Zatem każda dzierżawa usługi Azure AD wymaga oddzielnego obszaru roboczego. |  |
| Szczegółowa kontrola dostępu do danych | Aby uzyskać dostęp do niektórych danych zebranych przez wskaźnik na platformie Azure, organizacja może wymagać zezwolenia na różne grupy w organizacji lub poza nią. Na przykład:<br><ul><li>Dostęp właścicieli zasobów do danych odnoszących się do ich zasobów</li><li>Regionalny lub Socy dostęp do danych istotnych dla ich części organizacji</li></ul> | Korzystanie z [zasobów RBAC na platformie Azure](https://techcommunity.microsoft.com/t5/azure-sentinel/controlling-access-to-azure-sentinel-data-resource-rbac/ba-p/1301463) lub na [poziomie tabeli Azure RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) |
| Ustawienia przechowywania szczegółowego | W przeszłości wiele obszarów roboczych była jedynym sposobem ustawiania różnych okresów przechowywania dla różnych typów danych. Nie jest to już potrzebne w wielu przypadkach dzięki wprowadzeniu ustawień przechowywania poziomu tabeli. | Korzystanie z [ustawień przechowywania na poziomie tabeli](https://techcommunity.microsoft.com/t5/azure-sentinel/new-per-data-type-retention-is-now-available-for-azure-sentinel/ba-p/917316) lub Automatyzowanie [usuwania danych](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data) |
| Podział rozliczeń | Umieszczając obszary robocze w osobnych subskrypcjach, można je rozliczać na różne strony. | Raportowanie użycia i naliczanie krzyżowe |
| Starsza architektura | Korzystanie z wielu obszarów roboczych może być wykonane z projektu historycznego, który uwzględnia ograniczenia lub najlepsze rozwiązania, które nie są już spełnione. Może to być również arbitralna decyzja projektowa, którą można zmienić, aby lepiej dostosować się do wymagań usługi Azure Sentinel.<br><br>Przykłady obejmują:<br><ul><li>Używanie domyślnego obszaru roboczego dla subskrypcji podczas wdrażania Azure Security Center</li><li>Konieczność stosowania szczegółowych ustawień kontroli dostępu lub przechowywania, dla których rozwiązania są stosunkowo nowe</li></ul> | Zmiana architektury obszarów roboczych |

### <a name="managed-security-service-provider-mssp"></a>Dostawca zarządzanej usługi zabezpieczeń (MSSP)

Szczególnym przypadkiem użycia, który stanowi upoważnienie wielu obszarów roboczych, jest MSSP usługi Azure wskaźnikowej. W takim przypadku wiele sytuacji, w których nie wszystkie powyższe wymagania, mają zastosowanie w przypadku wielu obszarów roboczych, między dzierżawcami, najlepszych rozwiązań. MSSP może korzystać z [usługi Azure Lighthouse](../lighthouse/overview.md) w celu rozbudowy funkcji obejmujących wiele obszarów roboczych platformy Azure.

## <a name="azure-sentinel-multiple-workspace-architecture"></a>Architektura usługi Azure Sentinel z wieloma obszarami roboczymi

Jak wynika z powyższych wymagań, istnieją przypadki, w których wiele obszarów roboczych usługi Azure wskaźnikowych, potencjalnie w ramach dzierżaw Azure Active Directory (Azure AD), musi być centralnie monitorowane i zarządzane przez pojedynczy SOC.

- Usługa MSSP platformy Azure.

- Globalny SOC obsługujący wiele podmiotów zależnych, z których każdy ma własny lokalny SOC.

- SOC monitorujący wiele dzierżawców usługi Azure AD w organizacji.

Aby rozwiązać ten wymóg, wskaźnik platformy Azure obejmuje możliwości wielu obszarów roboczych, które umożliwiają Centralne monitorowanie, konfigurację i zarządzanie, zapewniając pojedyncze okienko szkła dla wszystkich elementów objętych przez SOC, jak pokazano na poniższym diagramie.

:::image type="content" source="media/extend-sentinel-across-workspaces-tenants/cross-workspace-architecture.png" alt-text="Architektura między obszarami roboczymi":::

Ten model oferuje znaczące korzyści w stosunku do w pełni scentralizowanego modelu, w którym wszystkie dane są kopiowane do jednego obszaru roboczego:

- Elastyczne przypisanie roli do globalnych i lokalnych SOC, lub do MSSP jego klientów.

- Mniejsze wyzwania dotyczące własności danych, prywatności danych i zgodności z przepisami.

- Minimalne opóźnienia sieci i koszty.

- Łatwe dołączanie i odłączanie nowych podmiotów zależnych lub klientów.

W poniższych sekcjach wyjaśnimy, jak korzystać z tego modelu, a zwłaszcza:

- Centralnie Monitoruj wiele obszarów roboczych, potencjalnie między dzierżawcami, dostarczając SOC z jednym okienkiem szklanym.

- Centralne Konfigurowanie i zarządzanie wieloma obszarami roboczymi, potencjalnie między dzierżawcami, przy użyciu automatyzacji.

## <a name="cross-workspace-monitoring"></a>Monitorowanie między obszarami roboczymi

### <a name="manage-incidents-on-multiple-workspaces"></a>Zarządzanie zdarzeniami w wielu obszarach roboczych

Centrum kontrolne platformy Azure obsługuje [wiele obszarów roboczych](./multiple-workspace-view.md) , które ułatwiają Centralne monitorowanie zdarzeń i zarządzanie nimi w wielu obszarach roboczych. Scentralizowany widok zdarzeń umożliwia bezpośrednie zarządzanie zdarzeniami lub przechodzenie do szczegółów zdarzenia w kontekście obszaru roboczego źródłowego.

### <a name="cross-workspace-querying"></a>Zapytania między obszarami roboczymi

Na platformie Azure badanie obejmuje wykonywanie zapytań dotyczących [wielu obszarów roboczych w jednym zapytaniu](../azure-monitor/log-query/cross-workspace-query.md), co pozwala na wyszukiwanie i skorelowanie danych z wielu obszarów roboczych w jednym zapytaniu. 

- Użyj [wyrażenia Workspace ()](../azure-monitor/log-query/workspace-expression.md) , aby odwołać się do tabeli w innym obszarze roboczym. 
- Użyj [operatora Union](/azure/data-explorer/kusto/query/unionoperator?pivots=azuremonitor) obok wyrażenia obszaru roboczego (), aby zastosować zapytanie między tabelami w wielu obszarach roboczych.

Przy użyciu zapisanych [funkcji](../azure-monitor/log-query/functions.md) można uprościć zapytania między obszarami roboczymi. Na przykład, jeśli odwołanie do obszaru roboczego jest długie, możesz chcieć zapisać wyrażenie `workspace("customer-A's-hard-to-remember-workspace-name").SecurityEvent` jako funkcję o nazwie `SecurityEventCustomerA` . Następnie można pisać zapytania jako `SecurityEventCustomerA | where ...` .

Funkcja może również uprościć powszechnie używaną Unię. Można na przykład zapisać następujące wyrażenie jako funkcję o nazwie `unionSecurityEvent` :

`union workspace(“hard-to-remember-workspace-name-1”).SecurityEvent, workspace(“hard-to-remember-workspace-name-2”).SecurityEvent`

Następnie można napisać zapytanie w obu obszarach roboczych, zaczynając od `unionSecurityEvent | where ...` .

#### <a name="cross-workspace-analytics-rules"></a>Reguły analizy między obszarami roboczymi<a name="scheduled-alerts"></a>
<!-- Bookmark added for backward compatibility with old heading -->
Zapytania między obszarami roboczymi można teraz dołączać do zaplanowanych reguł analizy, z uwzględnieniem następujących ograniczeń:

- Pojedyncze zapytanie może zawierać maksymalnie 15 obszarów roboczych.
- Wszystkie obszary robocze, do których odwołuje się zapytanie, muszą zostać wdrożone na platformie Azure.

> [!NOTE] 
> Wykonanie zapytania dotyczącego wielu obszarów roboczych w ramach tego samego zapytania może mieć wpływ na wydajność i dlatego jest zalecane tylko wtedy, gdy logika wymaga tej funkcji.

#### <a name="cross-workspace-workbooks"></a>Skoroszyty między obszarami roboczymi<a name="using-cross-workspace-workbooks"></a>
<!-- Bookmark added for backward compatibility with old heading -->
[Skoroszyty](./overview.md#workbooks) zapewniają pulpity nawigacyjne i aplikacje na platformie Azure wskaźnikowej. Podczas pracy z wieloma obszarami roboczymi zapewniają one monitorowanie i akcje w różnych obszarach roboczych.

Skoroszyty mogą udostępniać zapytania między obszarami roboczymi w jednej z trzech metod, z których każdy ma zależeć do różnych poziomów wiedzy użytkowników końcowych:

| Metoda  | Opis | Kiedy należy używać? |
|---------|-------------|--------------------|
| Zapisuj zapytania między obszarami roboczymi | Twórca skoroszytu może zapisywać zapytania między obszarami roboczymi (opisane powyżej) w skoroszycie. | Ta opcja umożliwia twórcom skoroszytów ochronę użytkownika w całości ze struktury obszaru roboczego. |
| Dodawanie selektora obszaru roboczego do skoroszytu | Twórca skoroszytu może zaimplementować selektor obszaru roboczego jako część skoroszytu, zgodnie z opisem w [tym miejscu](https://techcommunity.microsoft.com/t5/azure-sentinel/making-your-azure-sentinel-workbooks-multi-tenant-or-multi/ba-p/1402357). | Ta opcja zapewnia użytkownikowi kontrolę nad obszarami roboczymi wyświetlanymi w skoroszycie przy użyciu łatwego w użyciu pola listy rozwijanej. |
| Interaktywnie Edytuj skoroszyt | Zaawansowany użytkownik modyfikuje istniejący skoroszyt może edytować w nim zapytania, wybierając docelowe obszary robocze przy użyciu selektora obszaru roboczego w edytorze. | Ta opcja umożliwia użytkownikowi zaawansowanemu łatwe modyfikowanie istniejących skoroszytów do pracy z wieloma obszarami roboczymi. |
|

#### <a name="cross-workspace-hunting"></a>Polowa między obszarami roboczymi

Wskaźnik "Azure" obejmuje wstępnie załadowane przykłady zapytań, które umożliwiają rozpoczęcie pracy i zapoznanie się z tabelami oraz językiem zapytań. Te wbudowane zapytania polowania są opracowywane przez badaczy zabezpieczeń firmy Microsoft w sposób ciągły, dodając nowe zapytania i dostrajając istniejące zapytania, aby zapewnić punkt wejścia do wyszukiwania nowych wykryć i zidentyfikować oznaki włamania, które mogły zostać usunięte przez narzędzia zabezpieczeń.  

Możliwości polowania między obszarami roboczymi umożliwiają administratorom zagrożeń tworzenie nowych zapytań polowających lub dostosowywanie istniejących, aby uwzględnić wiele obszarów roboczych, przy użyciu operatora Union i wyrażenia Workspace (), jak pokazano powyżej.

## <a name="cross-workspace-management-using-automation"></a>Zarządzanie między obszarami roboczymi przy użyciu automatyzacji

Aby skonfigurować wiele obszarów roboczych usługi Azure wskaźnikowych i zarządzać nimi, należy zautomatyzować korzystanie z interfejsu API zarządzania wskaźnikami platformy Azure. Aby uzyskać więcej informacji na temat automatyzowania wdrożenia zasobów usługi Azure wskaźnikowego, w tym reguł alertów, zapytań łowieckich, skoroszytów i elementy PlayBook, zobacz [rozszerzanie wskaźnikowego platformy Azure: interfejsy API, integracja i Automatyzacja zarządzania](https://techcommunity.microsoft.com/t5/azure-sentinel/extending-azure-sentinel-apis-integration-and-management/ba-p/1116885).

Zobacz też [wdrażanie i zarządzanie wskaźnikiem na platformie Azure jako kod](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928) i [łączenie usługi Azure Lighthouse z możliwościami DevOpsą](https://techcommunity.microsoft.com/t5/azure-sentinel/combining-azure-lighthouse-with-sentinel-s-devops-capabilities/ba-p/1210966) platformy Azure, w ramach skonsolidowanej, współtworzonej przez społeczność metodologii do zarządzania systemem Azure wskaźnikowego jako kodu oraz do wdrażania i konfigurowania zasobów z prywatnego repozytorium GitHub. 

## <a name="managing-workspaces-across-tenants-using-azure-lighthouse"></a>Zarządzanie obszarami roboczymi w dzierżawach przy użyciu usługi Azure Lighthouse

Jak wspomniano powyżej, w wielu scenariuszach różne obszary robocze Azure wskaźnikowego mogą znajdować się w różnych dzierżawach usługi Azure AD. Możesz użyć [usługi Azure Lighthouse](../lighthouse/overview.md) , aby rozłożyć wszystkie działania między obszarami roboczymi między granicami dzierżawy, dzięki czemu użytkownicy w dzierżawie zarządzającej pracują nad obszarami roboczymi usługi Azure wskaźnikami we wszystkich dzierżawców. Po dołączeniu usługi Azure Lighthouse należy użyć [selektora katalogów i subskrypcji](./multiple-tenants-service-providers.md#how-to-access-azure-sentinel-in-managed-tenants) w [Azure Portal, aby](../lighthouse/how-to/onboard-customer.md)wybrać wszystkie subskrypcje zawierające obszary robocze, którymi chcesz zarządzać, aby upewnić się, że będą one dostępne w różnych selektorach obszarów roboczych w portalu.

W przypadku korzystania z usługi Azure Lighthouse zaleca się utworzenie grupy dla każdej roli wskaźnikowej platformy Azure i delegowanie uprawnień z poszczególnych dzierżawców do tych grup.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób rozszerzania możliwości usługi Azure wskaźnikowego na wiele obszarów roboczych i dzierżawców. Aby uzyskać praktyczne wskazówki dotyczące implementowania architektury między obszarami roboczymi platformy Azure, zobacz następujące artykuły:

- Dowiedz się, jak [korzystać z wielu dzierżawców](./multiple-tenants-service-providers.md) na platformie Azure — wskaźnik przy użyciu usługi Azure Lighthouse.
- Dowiedz się, jak bezproblemowo [wyświetlać zdarzenia w wielu obszarach roboczych i zarządzać nimi](./multiple-workspace-view.md) .