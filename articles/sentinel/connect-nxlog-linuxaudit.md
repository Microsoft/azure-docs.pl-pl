---
title: Łączenie danych NXLog LinuxAudit z platformą Azure Microsoft Docs
description: Dowiedz się, jak używać łącznika danych NXLog LinuxAudit do ściągania dzienników LinuxAudit do platformy Azure. Wyświetlaj dane LinuxAudit w skoroszytach, twórz Alerty i ulepszaj badanie.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 2010b21a3cdb81f2b2aa4180f87857862cd02bf5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700940"
---
# <a name="connect-your-nxlog-linuxaudit-to-azure-sentinel"></a>Połącz swoje NXLog LinuxAudit z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik NXLog LinuxAudit jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

Łącznik [NXLog LinuxAudit](https://nxlog.co/documentation/nxlog-user-guide/im_linuxaudit.html) pozwala łatwo eksportować zdarzenia zabezpieczeń systemu Linux do funkcji wskaźnikowej platformy Azure w czasie rzeczywistym, dzięki czemu możesz uzyskać wgląd w działanie serwera nazw domen w całej organizacji. Moduł NXLog LinuxAudit obsługuje niestandardowe reguły inspekcji i zbiera dzienniki bez inspekcji ani innego oprogramowania do obsługi miejsca użytkownika. Adresy IP i grupy/identyfikatory użytkowników są rozpoznawane jako odpowiadające im nazwy, co sprawia, że dzienniki [inspekcji systemu Linux](https://nxlog.co/documentation/nxlog-user-guide/linux-audit.html) są bardziej zrozumiałe dla analityków zabezpieczeń. Integracja między programem NXLog LinuxAudit i platformą Azure jest ułatwiona za pośrednictwem interfejsu API REST.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="configure-and-connect-nxlog-linuxaudit"></a>Konfigurowanie i łączenie NXLog LinuxAudit

NXLog można skonfigurować do wysyłania zdarzeń w formacie JSON bezpośrednio do platformy Azure.

1. W portalu wskaźnikowym platformy Azure kliknij pozycję **Łączniki danych** i wybierz pozycję Łącznik **NXLog LinuxAudit** .

1. Wybierz pozycję **Otwórz stronę łącznika**.

1. Aby skonfigurować przekazywanie za pośrednictwem interfejsu API REST, wykonaj instrukcje krok po kroku w temacie Integracja *podręcznika użytkownika NXLog* [Microsoft Azure wskaźnikiem](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) .

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji  **dzienniki niestandardowe** w tabeli *LinuxAudit_CL* .

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób korzystania z usługi NXLog LinuxAudit w celu pozyskiwania zdarzeń zabezpieczeń systemu Linux w usłudze Azure wskaźnikowej. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:

- Dowiedz się [, jak uzyskać wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
