---
title: Zarządzanie obszarami roboczymi wskaźników platformy Azure na dużą skalę
description: Dowiedz się, jak efektywnie zarządzać wskaźnikami platformy Azure na delegowanych zasobach klientów.
ms.date: 06/17/2020
ms.topic: how-to
ms.openlocfilehash: fc6d66a31ed46766bbe664fd8656792c97ff69fd
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163241"
---
# <a name="manage-azure-sentinel-workspaces-at-scale"></a>Zarządzanie obszarami roboczymi wskaźników platformy Azure na dużą skalę

Jako dostawca usług możesz dołączyć wielu dzierżawców klientów do [usługi Azure Lighthouse](../overview.md). Usługa Azure Lighthouse umożliwia dostawcom usług wykonywanie operacji na dużą skalę w wielu dzierżawach Azure Active Directory (Azure AD) jednocześnie, co sprawia, że zadania zarządzania są bardziej wydajne.

System Azure Wskaźnikowanie zapewnia obsługę analizy zabezpieczeń i analizy zagrożeń, zapewniając jedno rozwiązanie do wykrywania alertów, widoczności zagrożeń, aktywnego polowania i reagowania na zagrożenia. Usługa Azure Lighthouse umożliwia zarządzanie wieloma obszarami roboczymi wskaźników platformy Azure w różnych dzierżawach. Pozwala to na takie scenariusze jak uruchamianie zapytań w wielu obszarach roboczych lub tworzenie skoroszytów w celu wizualizowania i monitorowania danych z połączonych źródeł danych w celu uzyskania szczegółowych informacji. Adresy IP, takie jak zapytania i elementy PlayBook pozostają w dzierżawie zarządzającej, ale mogą być używane do zarządzania zabezpieczeniami w dzierżawach klientów.

Ten temat zawiera omówienie sposobu korzystania z usługi [Azure wskaźnikowania](../../sentinel/overview.md) w sposób skalowalny dla wglądu w wiele dzierżaw i zarządzanych usług zabezpieczeń.

> [!TIP]
> Chociaż odwołujemy się do dostawców usług i klientów w tym temacie, te wskazówki dotyczą również [przedsiębiorstw korzystających z usługi Azure Lighthouse do zarządzania wieloma dzierżawcami](../concepts/enterprise.md).

## <a name="architectural-considerations"></a>Zagadnienia dotyczące architektury

W przypadku dostawcy zarządzanego usługi zabezpieczeń (MSSP), który chce utworzyć ofertę typu "Security-as-a-Service" przy użyciu centrów danych platformy Azure, może być konieczne jednokrotne monitorowanie, zarządzanie i Konfigurowanie wielu obszarów roboczych usługi Azure wskaźnikowych wdrożonych w ramach poszczególnych dzierżawców klientów. Podobnie przedsiębiorstwa z wieloma dzierżawami usługi Azure AD mogą chcieć centralnie zarządzać wieloma obszarami roboczymi funkcji wskaźnikowych platformy Azure wdrożonymi w ramach swoich dzierżawców.

Ten scentralizowany model wdrożenia ma następujące zalety:

- Własność danych pozostaje dla każdej zarządzanej dzierżawy.
- Program obsługuje wymagania dotyczące przechowywania danych w granicach geograficznych.
- Zapewnia izolację danych, ponieważ dane dla wielu klientów nie są przechowywane w tym samym obszarze roboczym. 
- Zapobiega eksfiltracji danych od zarządzanych dzierżawców, pomagając zapewnić zgodność danych.
- Koszty związane są naliczone dla każdej zarządzanej dzierżawy, a nie do dzierżawy zarządzającej.
- Dane ze wszystkich źródeł danych i łączników danych, które są zintegrowane z platformą Azure (np. dzienniki aktywności usługi Azure AD, dzienniki pakietu Office 365 lub alerty ochrony przed zagrożeniami firmy Microsoft), pozostaną w ramach każdej dzierżawy klienta.
- Zmniejsza opóźnienia sieci.
- Łatwe dodawanie lub usuwanie nowych podmiotów zależnych lub klientów.

## <a name="granular-role-based-access-control-rbac"></a>Szczegółowa kontrola dostępu oparta na rolach (RBAC)

Każdą subskrypcję klienta, którą będzie zarządzać MSSP, należy [dołączyć do usługi Azure Lighthouse](onboard-customer.md). Umożliwia to Wyznaczeni użytkownikom w dzierżawie zarządzającej dostęp i wykonywanie operacji zarządzania w obszarach roboczych usługi Azure wskaźnikowych wdrożonych w dzierżawach klientów.

Podczas tworzenia autoryzacji można przypisać wbudowane role kontrolki Azure — do użytkowników, grup lub jednostek usługi w dzierżawie zarządzającej:

- [Czytnik wskaźnikowy platformy Azure](../../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Obiekt odpowiadający wskaźnikowi platformy Azure](../../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Współautor wskaźnikowego platformy Azure](../../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Możesz również przypisać dodatkowe wbudowane role do wykonywania dodatkowych funkcji. Aby uzyskać informacje na temat określonych ról, które mogą być używane z platformą Azure — wskaźnikiem, zobacz [uprawnienia na platformie Azure](../../sentinel/roles.md).

Po dodaniu klientów Wyznaczeni użytkownicy mogą logować się do dzierżawy zarządzającej i [uzyskiwać bezpośredni dostęp do obszaru roboczego wskaźnikowego platformy Azure dla klienta](../../sentinel/multiple-tenants-service-providers.md) przy użyciu przypisanych ról.

## <a name="view-and-manage-incidents-across-workspaces"></a>Wyświetlaj zdarzenia w obszarach roboczych i zarządzaj nimi

Jeśli zarządzasz zasobami wskaźnikowymi platformy Azure dla wielu klientów, możesz wyświetlać zdarzenia i zarządzać nimi w wielu obszarach roboczych jednocześnie dla wielu dzierżawców. Aby uzyskać więcej informacji, zobacz artykuł [Pracuj z zdarzeniami w wielu obszarach roboczych jednocześnie](../../sentinel/multiple-workspace-view.md) i [zwiększaj wskaźnik platformy Azure dla obszarów roboczych i dzierżawców](../../sentinel/extend-sentinel-across-workspaces-tenants.md).

> [!NOTE]
> Upewnij się, że użytkownicy w dzierżawie zarządzającej mają przypisane uprawnienia do odczytu i zapisu dla wszystkich obszarów roboczych, którymi zarządzasz. Jeśli użytkownik ma uprawnienia do odczytu w niektórych obszarach roboczych, komunikaty ostrzegawcze mogą być wyświetlane w przypadku wybrania incydentów w tych obszarach roboczych, a użytkownik nie będzie mógł modyfikować tych incydentów ani żadnych innych wybranych z nimi (nawet jeśli masz uprawnienia do innych użytkowników).

## <a name="configure-playbooks-for-mitigation"></a>Konfigurowanie elementy PlayBook na potrzeby ograniczenia

[Elementy PlayBook](../../sentinel/tutorial-respond-threats-playbook.md) może służyć do automatycznego rozwiązywania problemów w przypadku wyzwolenia alertu. Te elementy PlayBook mogą być uruchamiane ręcznie lub mogą być uruchamiane automatycznie po wyzwoleniu określonych alertów. Elementy PlayBook można wdrożyć w dzierżawie zarządzającej lub dzierżawie klienta przy użyciu procedur odpowiedzi skonfigurowanych w oparciu o użytkowników dzierżawy, którzy będą musieli podejmować działania w odpowiedzi na zagrożenia bezpieczeństwa.

## <a name="create-cross-tenant-workbooks"></a>Tworzenie skoroszytów między dzierżawcami

[Azure monitor skoroszyty na platformie Azure — wskaźnik](../../sentinel/overview.md#workbooks) pomocy, aby wizualizować i monitorować dane z połączonych źródeł danych, aby uzyskać szczegółowe informacje. Możesz użyć wbudowanych szablonów skoroszytów w wskaźniku kontrolnym platformy Azure albo utworzyć niestandardowe skoroszyty dla swoich scenariuszy.

Możesz wdrażać skoroszyty w dzierżawie zarządzającej i tworzyć pulpity nawigacyjne na skalę na potrzeby monitorowania i wykonywania zapytań dotyczących danych w dzierżawach klientów. Aby uzyskać więcej informacji, zobacz [monitorowanie między obszarami roboczymi](../../sentinel/extend-sentinel-across-workspaces-tenants.md#using-cross-workspace-workbooks). Należy pamiętać, że niektóre funkcje [nie są obsługiwane w wielu obszarach roboczych](../../sentinel/extend-sentinel-across-workspaces-tenants.md#whats-not-supported-across-workspaces).

Skoroszyty można również wdrażać bezpośrednio w poszczególnych dzierżawach, którymi można zarządzać w scenariuszach specyficznych dla danego klienta.

## <a name="run-queries-across-azure-sentinel-workspaces"></a>Uruchamianie zapytań w obszarach roboczych wskaźnikowych platformy Azure

Można utworzyć i zapisać zapytania Log Analytics na potrzeby wykrywania zagrożeń centralnie w dzierżawie zarządzającej. Te zapytania można następnie uruchomić we wszystkich obszarach roboczych Azure wskaźnikowych dla klientów, korzystając z operatora Union i wyrażenia obszaru roboczego (). Aby uzyskać więcej informacji, zobacz [zapytania między obszarami roboczymi](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-querying).

## <a name="use-automation-for-cross-workspace-management"></a>Używanie automatyzacji do zarządzania między obszarami roboczymi

Za pomocą usługi Automation można zarządzać wieloma obszarami roboczymi usługi Azure wskaźnikami i konfigurować [zapytania łowieckie](../../sentinel/hunting.md), elementy playbook i skoroszyty. Aby uzyskać więcej informacji, zobacz [Zarządzanie między obszarami roboczymi przy użyciu usługi Automation](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-management-using-automation).

Należy pamiętać, że niektóre funkcje [nie są obecnie obsługiwane w wielu obszarach roboczych](../../sentinel/extend-sentinel-across-workspaces-tenants.md#whats-not-supported-across-workspaces).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [platformie Azure wskaźnikowej](../../sentinel/overview.md).
- Zapoznaj się z [cennikiem usługi Azure wskaźnikiem](https://azure.microsoft.com/pricing/details/azure-sentinel/).
- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md).

