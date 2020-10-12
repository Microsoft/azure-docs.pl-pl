---
title: Uprawnienia na platformie Azure — wskaźnik | Microsoft Docs
description: W tym artykule wyjaśniono, w jaki sposób wskaźnik platformy Azure używa kontroli dostępu opartej na rolach do przypisywania uprawnień użytkownikom i identyfikuje dozwolone akcje dla każdej roli.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2020
ms.author: yelevin
ms.openlocfilehash: b48ff1043ae8128a5cbfdcbba0548d89b5af2624
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88565846"
---
# <a name="permissions-in-azure-sentinel"></a>Uprawnienia w usłudze Azure Sentinel

W przypadku korzystania z funkcji [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) na platformie Azure wskaźnik na platformie Azure obejmuje [wbudowane role](../role-based-access-control/built-in-roles.md),   które można przypisać do użytkowników, grup i usług w systemie.

Za pomocą funkcji RBAC można tworzyć i przypisywać role w zespole operacji zabezpieczeń, aby udzielać odpowiedniego dostępu do platformy Azure. Różne role zapewniają szczegółową kontrolę nad tym, co widzą Użytkownicy platformy Azure. Role platformy Azure można przypisywać bezpośrednio w obszarze roboczym Azure wskaźnikowym (patrz Uwaga poniżej) lub w subskrypcji lub grupie zasobów, do której należy ten obszar roboczy, który będzie dziedziczyć na platformie Azure.

## <a name="roles-for-working-in-azure-sentinel"></a>Role do pracy na platformie Azure — wskaźnik

### <a name="azure-sentinel-specific-roles"></a>Role specyficzne dla usługi Azure Sentinel

Istnieją trzy dedykowane wbudowane role wskaźnikowe platformy Azure.

**Wszystkie wbudowane role funkcji wskaźnikowej platformy Azure umożliwiają dostęp do odczytu do danych w obszarze roboczym wskaźnikowego platformy Azure.**

- [Czytnik wskaźnikowy platformy Azure](../role-based-access-control/built-in-roles.md#azure-sentinel-reader) może wyświetlać dane, zdarzenia, skoroszyty i inne zasoby wskaźnikowe platformy Azure.

- [Obiekt odpowiadający wskaźnikowi platformy Azure](../role-based-access-control/built-in-roles.md#azure-sentinel-responder) może, oprócz powyższych, zarządzać zdarzeniami (przypisywać, odrzucać itp.).

- [Współautorzy danych platformy Azure](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor) może, oprócz powyższych, tworzyć i edytować skoroszyty, reguły analizy i inne zasoby wskaźnikowe platformy Azure.

> [!NOTE]
>
> - Aby uzyskać najlepsze wyniki, te role należy przypisać do **grupy zasobów** , która zawiera obszar roboczy wskaźnik platformy Azure. Dzięki temu role będą miały zastosowanie do wszystkich zasobów wdrożonych w celu obsługi platformy Azure, ponieważ te zasoby powinny również zostać umieszczone w tej samej grupie zasobów.
>
> - Innym rozwiązaniem jest przypisanie ról bezpośrednio w **obszarze roboczym** Azure wskaźnikowym. W takim przypadku należy również przypisać te same role do **zasobu rozwiązania** SecurityInsights w tym obszarze roboczym. Konieczne może być również przypisanie ich do innych zasobów i konieczność stałego zarządzania przypisaniami ról w zasobach.

### <a name="additional-roles-and-permissions"></a>Dodatkowe role i uprawnienia

Użytkownikom z określonymi wymaganiami dotyczącymi zadań może być konieczne przypisanie dodatkowych ról lub określonych uprawnień w celu wykonania ich zadań.

- Praca z usługą elementy PlayBook w celu zautomatyzowania odpowiedzi na zagrożenia

    Wskaźnik platformy Azure używa **elementy PlayBook** do automatycznej reakcji na zagrożenia. Elementy PlayBook są oparte na **Azure Logic Apps**i są osobnym zasobem platformy Azure. Można przypisać do określonych członków zespołu ds. operacji zabezpieczeń, który może używać Logic Apps do operacji aranżacji zabezpieczeń, automatyzacji i odpowiedzi (o). Rola [współautor aplikacji logiki](../role-based-access-control/built-in-roles.md#logic-app-contributor) lub rola [operatora aplikacji logiki](../role-based-access-control/built-in-roles.md#logic-app-operator) służy do przypisywania jawnego uprawnienia do korzystania z elementy PlayBook.

- Łączenie źródeł danych z platformą Azure — wskaźnikiem

    Aby użytkownik mógł dodać **Łączniki danych**, należy przypisać uprawnienia do zapisu użytkownika w obszarze roboczym wskaźnik platformy Azure. Należy również zwrócić uwagę na wymagane dodatkowe uprawnienia dla każdego łącznika, jak wymieniono na odpowiedniej stronie łącznika.

- Użytkownicy-Goście przypisujący zdarzenia

    Jeśli użytkownik-Gość musi mieć możliwość przypisania zdarzeń, a nie roli obiektu odpowiadającego wskaźnikowi usługi Azure, należy również przypisać rolę [czytnika katalogów](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers). Należy pamiętać, że ta rola *nie* jest rolą RBAC platformy Azure, ale ma rolę **Azure Active Directory** , a użytkownicy w zwykłych (nie Gości) mają domyślnie przypisaną tę rolę. 

Aby zapoznać się z porównaniem równoległym, zobacz [poniższą tabelę](#roles-and-allowed-actions).

### <a name="other-roles-you-might-see-assigned"></a>Inne role, które mogą zostać wyświetlone

Przy przypisywaniu ról platformy Azure specyficznych dla platformy Azure można korzystać z innych ról platformy Azure i Log Analytics, które mogą zostać przypisane do użytkowników w innych celach. Należy pamiętać, że te role udzielają szerszego zestawu uprawnień, który obejmuje dostęp do obszaru roboczego wskaźnikowego platformy Azure i innych zasobów:

- **Role platformy Azure:** [właściciel](../role-based-access-control/built-in-roles.md#owner), [współautor](../role-based-access-control/built-in-roles.md#contributor)i [czytelnik](../role-based-access-control/built-in-roles.md#reader). Role platformy Azure umożliwiają dostęp do wszystkich zasobów platformy Azure, w tym Log Analytics obszarów roboczych i zasobów wskaźnikowych platformy Azure.

- **Role log Analytics:** [Log Analytics współautor](../role-based-access-control/built-in-roles.md#log-analytics-contributor) i [log Analytics Reader](../role-based-access-control/built-in-roles.md#log-analytics-reader). Role Log Analytics udzielą dostępu do Log Analytics obszarów roboczych. 

Na przykład użytkownik, któremu przypisano rolę **czytnika** punktów kontrolnych platformy Azure, ale nie ma roli **współautor wskaźnikowego platformy Azure** , nadal będzie mógł edytować elementy na platformie Azure, jeśli przypisze rolę **współautora** na poziomie platformy Azure. W związku z tym, jeśli chcesz udzielić uprawnień użytkownikowi tylko w wskaźniku kontrolnym platformy Azure, należy uważnie usunąć wcześniejsze uprawnienia tego użytkownika i upewnić się, że nie wszystkie wymagane prawa dostępu do innego zasobu.

## <a name="roles-and-allowed-actions"></a>Role i dozwolone akcje

W poniższej tabeli podsumowano role i dozwolone działania w usłudze Azure Sentinel. 

| Rola | Tworzenie i uruchamianie podręczników| Tworzenie i edytowanie skoroszytów, reguł analizy i innych zasobów usługi Azure Sentinel | Zarządzanie zdarzeniami (odrzucanie, przypisywanie itp.) | Wyświetl dane, zdarzenia, skoroszyty i inne zasoby dotyczące platformy Azure |
|---|---|---|---|---|
| Czytelnik usługi Azure Sentinel | -- | -- | -- | &#10003; |
| Obiekt odpowiadający usługi Azure Sentinel | -- | -- | &#10003; | &#10003; |
| Współautor usługi Azure Sentinel | -- | &#10003; | &#10003; | &#10003; |
| Współautor Azure wskaźnikowego i współautor aplikacji logiki | &#10003; | &#10003; | &#10003; | &#10003; |

## <a name="custom-roles-and-advanced-rbac"></a>Role niestandardowe i Zaawansowana kontrola RBAC

- Oprócz, lub zamiast, przy użyciu wbudowanych ról platformy Azure, można utworzyć role niestandardowe platformy Azure dla platformy Azure. Role niestandardowe platformy Azure na potrzeby kontroli dostępu platformy Azure są tworzone w taki sam sposób, jak w przypadku innych [niestandardowych ról RBAC](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) platformy Azure, na podstawie [określonych uprawnień do platformy Azure](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) — [zasoby i usługi Azure log Analytics](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights).

- Możesz użyć zaawansowanej kontroli dostępu opartej na rolach Log Analytics w danych w obszarze roboczym wskaźnikowego platformy Azure. Dotyczy to zarówno RBAC opartego na typie danych, jak i opartego na zasobach RBAC. Aby uzyskać więcej informacji na temat ról Log Analytics, zobacz [Zarządzanie danymi dzienników i obszarami roboczymi w programie Azure monitor](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions).

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób pracy z rolami dla użytkowników wskaźnikowych platformy Azure oraz tego, co każda rola umożliwia użytkownikom.

* [Blog dotyczący platformy Azure](https://aka.ms/azuresentinelblog). Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
