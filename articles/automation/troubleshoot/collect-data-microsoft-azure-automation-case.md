---
title: Dane do zebrania po otwarciu przypadku do automatyzacji Microsoft Azure | Microsoft Docs
description: W tym artykule opisano niektóre informacje, które należy zebrać przed otwarciem przypadku Azure Automation z obsługą Microsoft Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: v-miegge
ms.openlocfilehash: e1d5d791a58f301991819b41757b9021f6e30fc0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679402"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Dane, które należy zebrać podczas otwierania przypadku dla usługi Microsoft Azure Automation

W tym artykule opisano niektóre informacje, które należy zebrać przed otwarciem przypadku Azure Automation z obsługą Microsoft Azure. Te informacje nie są wymagane do otwarcia przypadku. Może jednak pomóc firmie Microsoft w szybszym rozwiązywaniu problemów. Ponadto użytkownik może zostać poproszony o te dane przez inżyniera pomocy technicznej po otwarciu przypadku.

## <a name="basic-data"></a>Dane podstawowe

Zbierz podstawowe dane opisane w artykule bazy wiedzy [4034605 — jak przechwytywać Azure Automation diagnostykę z obsługą skryptów](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

## <a name="data-for-update-management-issues-on-linux"></a>Dane dotyczące Update Management problemów w systemie Linux

1. Oprócz elementów, które są wymienione w KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), uruchom następujące narzędzie do zbierania dzienników:

   [Moduł zbierający dzienników agenta pakietu OMS Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Kompresuj zawartość folderu **/var/opt/Microsoft/omsagent/Run/automationworker/** , a następnie Wyślij skompresowany plik do pomocy technicznej platformy Azure.
 
3. Sprawdź, czy identyfikator obszaru roboczego, do którego są raportowane Agent Log Analytics dla systemu Linux, jest taki sam jak identyfikator obszaru roboczego monitorowanego pod kątem aktualizacji.

## <a name="data-for-update-management-issues-on-windows"></a>Dane dotyczące Update Management problemów w systemie Windows

1. Zbierz dane dla elementów wymienionych w [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Wyeksportuj następujące dzienniki zdarzeń do formatu EVTX:

   * System
   * Aplikacja
   * Zabezpieczenia
   * Operations Manager
   * Microsoft — SMA/Operational

3. Sprawdź, czy identyfikator obszaru roboczego, do którego jest raportowany przez agenta, jest taki sam jak identyfikator obszaru roboczego monitorowany przez aktualizacje systemu Windows.

## <a name="data-for-job-issues"></a>Dane dotyczące problemów z zadaniami

1. Zbierz dane dla elementów wymienionych w [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Zbierz identyfikator zadania dla zadania, które ma problem:

   1. W Azure Portal przejdź do pozycji **konta usługi Automation**.
   2. Wybierz konto usługi Automation, które ma być rozwiązywane, i zanotuj jego nazwę.
   3. Wybierz pozycję **zadania**.
   4. Wybierz zadanie, które ma być rozwiązywane.
   5. W okienku Podsumowanie zadania Znajdź wartość identyfikatora GUID w polu **Identyfikator zadania**.

   ![Identyfikator zadania w okienku podsumowania zadania](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. Zbierz próbkę uruchomionego skryptu.

4. Zbierz pliki dziennika:

   1. W Azure Portal przejdź do pozycji **konta usługi Automation**.
   2. Wybierz konto usługi Automation, którego będziesz rozwiązywać problemy.
   3. Wybierz pozycję **zadania**.
   4. Wybierz zadanie, które ma być rozwiązywane.
   5. Wybierz pozycję **wszystkie dzienniki**.
   6. W efekcie okienku Zbierz dane.

   ![Dane wymienione w obszarze wszystkie dzienniki](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>Dane dotyczące problemów z modułami

Oprócz [podstawowych elementów danych](#basic-data)należy zebrać następujące informacje:

* Wykonane kroki, aby można było odtworzyć problem.
* Zrzuty ekranu przedstawiające wszystkie komunikaty o błędach.
* Zrzuty ekranu dla bieżących modułów i numery wersji.

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Nawiąż [@AzureSupport](https://twitter.com/azuresupport)połączenie z kontem oficjalnego Microsoft Azure, aby zwiększyć komfort obsługi klienta, łącząc społeczność platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.
* Zaplikowanie zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.
