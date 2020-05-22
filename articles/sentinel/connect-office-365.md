---
title: Łączenie danych pakietu Office 365 z usługą Azure wskaźnikowego | Microsoft Docs
description: Dowiedz się, jak połączyć dane pakietu Office 365 z platformą Azure — wskaźnikiem.
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
ms.date: 02/12/2020
ms.author: yelevin
ms.openlocfilehash: c3e63063b3ea4e7fba3997ddd645aa59fe857488
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758575"
---
# <a name="connect-data-from-office-365-logs"></a>Łączenie danych z dzienników pakietu Office 365



Dzienniki inspekcji z [pakietu Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) można przesyłać strumieniowo do usługi Azure wskaźnikowej przy użyciu jednego kliknięcia. Dzienniki inspekcji z pakietu Office 365 można przesyłać strumieniowo do obszaru roboczego wskaźnikowego platformy Azure w ramach tej samej dzierżawy. Łącznik dziennika aktywności pakietu Office 365 zapewnia wgląd w bieżące działania użytkownika. Zostaną wyświetlone informacje o różnych akcjach użytkownika, administratora, systemu i zasad oraz zdarzeniach z pakietu Office 365. Łącząc dzienniki pakietu Office 365 z platformą Azure, możesz użyć tych danych do wyświetlania pulpitów nawigacyjnych, tworzenia niestandardowych alertów i ulepszania procesu badania.

> [!IMPORTANT]
> Jeśli masz licencję E3, zanim będzie można uzyskać dostęp do danych za pomocą interfejsu API działania zarządzania pakietu Office 365, musisz włączyć ujednolicone rejestrowanie inspekcji dla organizacji pakietu Office 365. W tym celu należy włączyć dziennik inspekcji pakietu Office 365. Aby uzyskać instrukcje, zobacz [Włączanie lub wyłączanie wyszukiwania w dzienniku inspekcji pakietu Office 365](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). Aby uzyskać więcej informacji, zobacz [Dokumentacja interfejsu API działania zarządzania pakietu Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz być administratorem globalnym lub administratorem zabezpieczeń w swojej dzierżawie.
- Dzierżawca musi mieć włączoną funkcję ujednoliconej inspekcji. Dzierżawcy z pakietem Office 365 E3 lub E5 mają ujednoliconą inspekcję włączoną domyślnie. <br>Jeśli dzierżawa nie ma żadnej z tych licencji, należy włączyć ujednoliconą inspekcję w dzierżawie przy użyciu jednej z następujących metod:
    - [Za pomocą polecenia cmdlet Set-AdminAuditLogConfig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) i włączania parametru "UnifiedAuditLogIngestionEnabled").
    - [Za pomocą interfejsu użytkownika Centrum zgodności & zabezpieczeń](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).
   
   > [!NOTE]
   > Obecnie Łącznik danych usługi O365 automatycznie przechwytuje programy Exchange i SharePoint działania, jak wspomniano na stronie łącznika w sekcji typy danych. Zalecamy sprawdzenie [tego artykułu na wypadek, gdyby zespoły wymagały inspekcji danych i ochrony zespołów przy użyciu badania wskaźnikowego](https://techcommunity.microsoft.com/t5/azure-sentinel/protecting-your-teams-with-azure-sentinel/ba-p/1265761). 

## <a name="connect-to-office-365"></a>Łączenie z usługą Office 365

1. W obszarze wskaźnik platformy Azure wybierz pozycję **Łączniki danych** , a następnie kliknij kafelek **Office 365** .

2. Jeśli jeszcze tego nie zrobiono, możesz to zrobić, przechodząc do bloku **Łączniki danych** i wybierając pozycję Łącznik **pakietu Office 365** . W tym miejscu możesz kliknąć **stronę Otwórz łącznik** i w obszarze Konfiguracja z etykietą **Konfiguracja** zaznacz wszystkie dzienniki aktywności pakietu Office 365, które chcesz połączyć z usługą Azure — wskaźnikiem. 
   > [!NOTE]
   > Jeśli masz już połączenie z wieloma dzierżawcami w wcześniej obsługiwanej wersji łącznika pakietu Office 365 na platformie Azure — wskaźnikiem, będziesz mieć możliwość wyświetlania i modyfikowania dzienników zbieranych z poszczególnych dzierżawców. Nie będzie można dodać kolejnych dzierżawców, ale można usunąć wcześniej dodanych dzierżawców.
3. Aby użyć odpowiedniego schematu w Log Analytics dla dzienników pakietu Office 365, Wyszukaj pozycję **Office**.


## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączenia między pakietem Office 365 a wskaźnikiem kontrolnym platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).

