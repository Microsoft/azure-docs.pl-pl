---
title: Dołączanie serwera z obsługą usługi Azure Arc do usługi Azure wskaźnikowego
description: Dowiedz się, jak dodać serwery z obsługą usługi Azure Arc do usługi Azure wskaźnikowej i aktywnie monitorować ich stan zabezpieczeń.
ms.date: 11/16/2020
ms.topic: conceptual
ms.openlocfilehash: 2364ba72ac5b10ec4e1f433cc6d591c3ca389ecd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100584747"
---
# <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Dołączanie serwerów z obsługą usługi Azure Arc do usługi Azure wskaźnikowej

Ten artykuł ma na celu ułatwienie dołączenia serwera z obsługą usługi Azure Arc do [platformy Azure](../../sentinel/overview.md) , a następnie rozpoczęcie zbierania zdarzeń związanych z zabezpieczeniami. Usługa Azure wskaźnikowego obejmuje pojedyncze rozwiązanie do wykrywania alertów, widoczności zagrożeń, aktywnego polowania i reagowania na zagrożenia w całym przedsiębiorstwie.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania:

- [Obszar roboczy usługi Log Analytics](../../azure-monitor/logs/data-platform-logs.md). Aby uzyskać więcej informacji na temat Log Analytics obszarów roboczych, zobacz [projektowanie Azure monitor dzienników wdrożenia](../../azure-monitor/logs/design-logs-deployment.md).

- [W Twojej subskrypcji jest włączona funkcja](../../sentinel/quickstart-onboard.md)wskaźnikowa platformy Azure.

- Jesteś komputerem lub serwer jest połączony z serwerami z obsługą usługi Azure Arc.

## <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Dołączanie serwerów z obsługą usługi Azure Arc do usługi Azure wskaźnikowej

Wskaźnik platformy Azure obejmuje wiele łączników dla rozwiązań firmy Microsoft, dostępnych na bieżąco i zapewniających integrację w czasie rzeczywistym. W przypadku maszyn fizycznych i wirtualnych można zainstalować agenta Log Analytics, który zbiera dzienniki i przekazuje je do usługi Azure wskaźnikowej. Serwery z włączonymi łukiemmi obsługują wdrażanie agenta Log Analytics przy użyciu następujących metod:

- Korzystanie z platformy rozszerzeń maszyn wirtualnych.

    Ta funkcja na serwerach z obsługą usługi Azure Arc umożliwia wdrożenie rozszerzenia maszyny wirtualnej Log Analytics Agent na serwerze z systemem innym niż Azure. Rozszerzeniami maszyn wirtualnych można zarządzać przy użyciu następujących metod na maszynach hybrydowych lub serwerach zarządzanych przez serwery z obsługą ARC:

    - [Azure Portal](manage-vm-extensions-portal.md)
    - [Interfejs wiersza polecenia platformy Azure](manage-vm-extensions-cli.md)
    - [Azure PowerShell](manage-vm-extensions-powershell.md)
    - [Szablony Menedżer zasobów](manage-vm-extensions-template.md) platformy Azure

- Przy użyciu Azure Policy.

    Korzystając z tego podejścia, należy użyć wbudowanej zasady Azure Policy [Wdróż agenta log Analytics do systemu Linux lub Windows Azure Arc](../../governance/policy/samples/built-in-policies.md#monitoring) , aby przeprowadzić inspekcję, jeśli na serwerze z włączonym łukiem jest zainstalowany agent log Analytics. Jeśli Agent nie jest zainstalowany, zostanie automatycznie wdrożony przy użyciu zadania korygowania. Alternatywnie, jeśli planujesz monitorowanie maszyn przy użyciu Azure Monitor dla maszyn wirtualnych, zamiast tego użyj inicjatywy [Enable Azure monitor dla maszyn wirtualnych](../../governance/policy/samples/built-in-initiatives.md#monitoring) , aby zainstalować i skonfigurować agenta log Analytics.

Zalecamy zainstalowanie agenta Log Analytics dla systemu Windows lub Linux przy użyciu Azure Policy.

Po nawiązaniu połączenia z serwerami z funkcją Arc dane zaczynają przesyłanie strumieniowe do platformy Azure i są gotowe do rozpoczęcia pracy z usługą. Możesz wyświetlić dzienniki w [wbudowanych skoroszytach](../../sentinel/quickstart-get-visibility.md) i rozpocząć tworzenie zapytań w log Analytics, aby [zbadać dane](../../sentinel/tutorial-investigate-cases.md).

## <a name="next-steps"></a>Następne kroki

Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](../../sentinel/tutorial-detect-threats-built-in.md).