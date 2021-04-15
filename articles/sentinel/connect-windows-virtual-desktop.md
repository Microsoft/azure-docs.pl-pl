---
title: Łączenie Windows Virtual Desktop z Azure Sentinel | Microsoft Docs
description: Dowiedz się, jak połączyć Windows Virtual Desktop danych z Azure Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2021
ms.author: bagol
ms.openlocfilehash: a835ea7b5e79ecc9b2d26dc6955984d0d0ff2906
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107380299"
---
# <a name="connect-windows-virtual-desktop-data-to-azure-sentinel"></a>Łączenie Windows Virtual Desktop danych z Azure Sentinel

W tym artykule opisano sposób monitorowania Windows Virtual Desktop (WVD) przy użyciu Azure Sentinel.

Na przykład monitorowanie środowisk Windows Virtual Desktop umożliwia zapewnienie większej liczby zdalnych prac przy użyciu zwirtualizowanych pulpitów przy zachowaniu poziomu bezpieczeństwa organizacji.

## <a name="windows-virtual-desktop-data-in-azure-sentinel"></a>Windows Virtual Desktop danych w Azure Sentinel

Windows Virtual Desktop dane w Azure Sentinel obejmują następujące typy:


|Dane  |Opis  |
|---------|---------|
|**Dzienniki zdarzeń systemu Windows**     |  Dzienniki zdarzeń systemu Windows ze środowiska WVD są przesyłane strumieniowo do obszaru roboczego usługi Log Analytics z obsługą usługi Azure Sentinel w taki sam sposób jak dzienniki zdarzeń systemu Windows z innych maszyn z systemem Windows spoza środowiska usługi WVD. <br><br>Zainstaluj agenta usługi Log Analytics na maszynie z systemem Windows i skonfiguruj dzienniki zdarzeń systemu Windows do wysłania do obszaru roboczego usługi Log Analytics.<br><br>Aby uzyskać więcej informacji, zobacz:<br>- [Instalowanie agenta usługi Log Analytics na komputerach z systemem Windows](/azure/azure-monitor/agents/agent-windows)<br>- [Zbieranie źródeł danych dziennika zdarzeń systemu Windows za pomocą agenta usługi Log Analytics](/azure/azure-monitor/agents/data-sources-windows-events)<br>- [Łączenie zdarzeń zabezpieczeń systemu Windows](connect-windows-security-events.md)       |
|**Alerty usługi Microsoft Defender dla punktu końcowego (MDE)**     |  Aby skonfigurować rozwiązanie MDE Windows Virtual Desktop, należy użyć tej samej procedury, co w przypadku każdego innego punktu końcowego systemu Windows. <br><br>Aby uzyskać więcej informacji, zobacz: <br>- [Konfigurowanie wdrożenia usługi Microsoft Defender dla punktu końcowego](/windows/security/threat-protection/microsoft-defender-atp/production-deployment)<br>- [Łączenie danych z usługi Microsoft 365 Defender do Azure Sentinel](connect-microsoft-365-defender.md)       |
|**Windows Virtual Desktop diagnostyki**     | Windows Virtual Desktop diagnostyki to funkcja usługi PaaS Windows Virtual Desktop, która rejestruje informacje za każdym razem, gdy ktoś przypisany do Windows Virtual Desktop korzysta z tej usługi. <br><br>Każdy dziennik zawiera informacje o Windows Virtual Desktop roli użytkownika, wszystkie komunikaty o błędach wyświetlane podczas sesji, informacje o dzierżawie i informacje o użytkowniku. <br><br>Funkcja diagnostyki tworzy dzienniki aktywności zarówno dla działań użytkownika, jak i administracyjnych. <br><br>Aby uzyskać więcej informacji, zobacz [Używanie usługi Log Analytics na potrzeby funkcji diagnostyki w Windows Virtual Desktop](/azure/virtual-desktop/virtual-desktop-fall-2019/diagnostics-log-analytics-2019).        |
|     |         |

## <a name="connect-windows-virtual-desktop-data"></a>Łączenie Windows Virtual Desktop danych

Aby rozpocząć Windows Virtual Desktop danych do Azure Sentinel, skorzystaj z instrukcji z Windows Virtual Desktop dokumentacji.

Aby uzyskać więcej informacji, zobacz Push Windows Virtual Desktop data to your Log Analytics workspace (Wypychanie danych [do obszaru roboczego usługi Log Analytics).](/azure/virtual-desktop/diagnostics-log-analytics)

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia uruchom zapytania w chmurze Azure Sentinel względem danych usługi Log Analytics.

Zobacz na przykład przykładowe zapytania z [dokumentacji Windows Virtual Desktop .](/azure/virtual-desktop/diagnostics-log-analytics)


Azure Sentinel udostępnia również wbudowane zapytania w obszarze **Dzienniki**  >  **ogólne**  >  **USŁUGI WINDOWS VIRTUAL DESKTOP:**

[![Windows Virtual Desktop wbudowane zapytania w Azure Sentinel. ](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png)](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png#lightbox)

## <a name="next-steps"></a>Następne kroki


Aby uzyskać więcej informacji, zobacz [Azure Monitor for Windows Virtual Desktop słownika](/azure/virtual-desktop/azure-monitor-glossary).
