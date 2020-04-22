---
title: Dane zbierane po otwarciu sprawy dla usługi Microsoft Azure Automation| Dokumenty firmy Microsoft
description: W tym artykule opisano niektóre informacje, które należy zebrać przed otwarciem sprawy dla usługi Azure Automation z pomocą techniczną platformy Microsoft Azure.
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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679402"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Dane, które należy zebrać podczas otwierania przypadku dla usługi Microsoft Azure Automation

W tym artykule opisano niektóre informacje, które należy zebrać przed otwarciem sprawy dla usługi Azure Automation z pomocą techniczną platformy Microsoft Azure. Informacje te nie są wymagane do otwarcia sprawy. Może to jednak pomóc firmie Microsoft w rozwiązaniu problemu szybciej. Ponadto po otwarciu sprawy inżynier pomocy technicznej może zostać poproszony o podanie tych danych.

## <a name="basic-data"></a>Dane podstawowe

Zbieranie podstawowych danych opisanych w artykule [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)bazy wiedzy Knowledge Base — jak przechwytywać diagnostykę skryptową usługi Azure Automation.

## <a name="data-for-update-management-issues-on-linux"></a>Dane dotyczące problemów z zarządzaniem aktualizacjami w systemie Linux

1. Oprócz elementów wymienionych w KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)uruchom następujące narzędzie do zbierania dzienników:

   [Moduł zbierający dzienniki agenta systemu OMS Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Kompresuj zawartość folderu **/var/opt/microsoft/omsagent/run/automationworker/,** a następnie wyślij skompresowany plik do pomocy technicznej platformy Azure.
 
3. Sprawdź, czy identyfikator obszaru roboczego, który raportuje agent usługi Log Analytics dla systemu Linux, jest taki sam jak identyfikator obszaru roboczego monitorowanego pod kątem aktualizacji.

## <a name="data-for-update-management-issues-on-windows"></a>Dane dotyczące problemów z zarządzaniem aktualizacjami w systemie Windows

1. Zbieraj dane dotyczące przedmiotów wymienionych w [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Wyeksportuj następujące dzienniki zdarzeń do formatu EVTX:

   * System
   * Aplikacja
   * Zabezpieczenia
   * Operations Manager
   * Microsoft-SMA/Operacyjna

3. Sprawdź, czy identyfikator obszaru roboczego, który zgłasza agent, jest taki sam jak identyfikator obszaru roboczego monitorowanego przez aktualizacje systemu Windows.

## <a name="data-for-job-issues"></a>Dane dotyczące problemów z pracą

1. Zbieraj dane dotyczące przedmiotów wymienionych w [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Zbieranie identyfikatora zadania dla zadania, które ma problem:

   1. W witrynie Azure portal przejdź do **witryny Konta automatyzacji**.
   2. Wybierz konto automatyzacji, które rozwiązujesz problemy, i zanotuj nazwę.
   3. Wybierz pozycję **Zadania**.
   4. Wybierz zadanie, które rozwiązujesz problemy.
   5. W okienku Podsumowanie zadania poszukaj wartości GUID w **identyfikatorze zadania**.

   ![Identyfikator zadania w okienku podsumowania zadania](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. Zbierz przykład skryptu, który jest uruchomiony.

4. Zbieranie plików dziennika:

   1. W witrynie Azure portal przejdź do **witryny Konta automatyzacji**.
   2. Wybierz konto automatyzacji, które rozwiązujesz problemy.
   3. Wybierz pozycję **Zadania**.
   4. Wybierz zadanie, które rozwiązujesz problemy.
   5. Wybierz **pozycję Wszystkie dzienniki**.
   6. W okienku wynikowym zebrać dane.

   ![Dane wymienione w obszarze Wszystkie dzienniki](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>Dane dotyczące problemów z modułem

Oprócz [podstawowych elementów danych](#basic-data)należy zebrać następujące informacje:

* Kroki, które zostały następnie, tak, że problem może być odtworzony.
* Zrzuty ekranu z komunikatami o błędach.
* Zrzuty ekranu z bieżącymi modułami i ich numerami wersji.

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [forów platformy Azure](https://azure.microsoft.com/support/forums/).
* Połącz [@AzureSupport](https://twitter.com/azuresupport)się z oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Złóż zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.
