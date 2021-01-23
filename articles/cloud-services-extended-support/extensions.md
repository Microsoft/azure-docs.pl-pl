---
title: Rozszerzenia dla Cloud Services (obsługa rozszerzona)
description: Rozszerzenia dla Cloud Services (obsługa rozszerzona)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: c2eddf75f99b751773220ae677d66fe8c09abb0e
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744829"
---
# <a name="extensions-for-cloud-services-extended-support"></a>Rozszerzenia dla Cloud Services (obsługa rozszerzona)

Rozszerzenia to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji na rolach. Można na przykład włączyć połączenie Pulpit zdalny w roli w ramach wdrożenia usługi w chmurze (obsługa rozszerzona) przy użyciu rozszerzenia Pulpit zdalny.  

## <a name="remote-desktop-extension"></a>Pulpit zdalny rozszerzenie

Pulpit zdalny umożliwia dostęp do pulpitu roli działającej na platformie Azure. Za pomocą połączenia pulpitu zdalnego można rozwiązywać problemy i diagnozować je w trakcie działania aplikacji.

Możesz włączyć Podłączanie pulpitu zdalnego w swojej roli podczas opracowywania, dołączając moduły pulpitu zdalnego w definicji usługi lub za pomocą rozszerzenia pulpitu zdalnego. 

Aby uzyskać więcej informacji, zobacz [Konfigurowanie pulpitu zdalnego na podstawie Azure Portal](enable-rdp.md)

## <a name="windows-azure-diagnostics-extension"></a>Rozszerzenie Diagnostyka Azure systemu Windows

Kluczowe metryki wydajności można monitorować dla każdej usługi w chmurze. Każda rola usługi w chmurze zbiera minimalne dane: użycie procesora, użycie sieci i użycie dysku. Jeśli usługa w chmurze ma rozszerzenie Microsoft. Azure. Diagnostics zastosowane do roli, ta rola może zbierać dodatkowe punkty danych. 

W przypadku podstawowego monitorowania dane liczników wydajności z wystąpień ról są próbkowane i zbierane w 3-minutowych interwałach. Te podstawowe dane monitorowania nie są przechowywane na koncie magazynu i nie są z nim skojarzone żadne dodatkowe koszty. 

Dzięki zaawansowanemu monitorowaniu dodatkowe metryki są próbkowane i zbierane z interwałem wynoszącym 5 minut, 1 godzina i 12 godzin. Zagregowane dane są przechowywane na koncie magazynu w tabelach i usuwane po upływie 10 dni. Używane konto magazynu jest konfigurowane przez rolę; dla różnych ról można użyć różnych kont magazynu. 

Rozszerzenie systemu Windows Diagnostyka Azure można włączyć dla Cloud Services (obsługa rozszerzona) za pomocą [programu PowerShell](deploy-powershell.md) lub [szablonu ARM](deploy-template.md)


## <a name="next-steps"></a>Następne kroki 
- Zapoznaj się z [wymaganiami wstępnymi](deploy-prerequisite.md) dotyczącymi wdrażania Cloud Services (obsługa rozszerzona).
- Zapoznaj się z [często zadawanymi pytaniami](faq.md) dotyczącymi Cloud Services (obsługa rozszerzona).
- Wdróż usługę w chmurze (obsługę rozszerzoną) przy użyciu [Azure Portal](deploy-portal.md), [programu PowerShell](deploy-powershell.md), [szablonu](deploy-template.md) lub [programu Visual Studio](deploy-visual-studio.md).