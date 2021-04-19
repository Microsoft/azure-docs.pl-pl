---
title: Łączenie Microsoft Defender for Identity (dawniej Azure ATP) z usługą Azure Sentinel| Microsoft Docs
description: Dowiedz się, jak przesyłać strumieniowo dzienniki Microsoft Defender for Identity (dawniej Azure Advanced Threat Protection) (ATP) do usługi Azure Sentinel jednym kliknięciem.
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 7a26091d4985b6fdb17120c6fd70476a750c94a9
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714125"
---
# <a name="connect-data-from-microsoft-defender-for-identity-formerly-azure-advanced-threat-protection"></a>Łączenie danych z usługi Microsoft Defender for Identity (dawniej Azure Advanced Threat Protection)

> [!IMPORTANT]
> Łącznik Microsoft Defender for Identity danych w Azure Sentinel jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule opisano sposób przesyłania strumieniowego alertów zabezpieczeń [z Microsoft Defender for Identity](/azure-advanced-threat-protection/what-is-atp) do Azure Sentinel. 

Aby przesyłać dalej alerty dotyczące kondycji oprócz alertów zabezpieczeń, zintegruj Microsoft Defender for Identity z serwerem Syslog. Aby uzyskać więcej informacji, zobacz [dokumentację Microsoft Defender for Identity .](/defender-for-identity/setting-syslog) 

## <a name="prerequisites"></a>Wymagania wstępne

- Użytkownik z uprawnieniami administratora globalnego lub administratora zabezpieczeń
- Musisz być klientem wersji zapoznawczej aplikacji i Microsoft Defender for Identity włączyć integrację między Microsoft Defender for Identity i Microsoft Cloud App Security. Aby uzyskać więcej informacji, zobacz [Microsoft Defender for Identity Integration](/cloud-app-security/mdi-integration).

## <a name="connect-to-microsoft-defender-for-identity"></a>Nawiązywanie połączenia z Microsoft Defender for Identity

Upewnij się, Microsoft Defender for Identity w wersji zapoznawczej [jest włączona w sieci.](/azure-advanced-threat-protection/install-atp-step1)
Jeśli Microsoft Defender for Identity i pozyszsz dane, podejrzane alerty można łatwo przesyłać strumieniowo do Azure Sentinel. Rozpoczęcie przesyłania strumieniowego alertów do usługi Azure Sentinel może potrwać do 24 godzin.


1. Aby nawiązać Microsoft Defender for Identity z Azure Sentinel, należy najpierw włączyć integrację między Microsoft Defender for Identity i Microsoft Cloud App Security. Aby uzyskać informacje na temat tego, jak to zrobić, [zobacz Microsoft Defender for Identity integracji](/cloud-app-security/mdi-integration).

1. W Azure Sentinel pozycję **Łączniki danych,** a następnie kliknij **kafelek Microsoft Defender for Identity (wersja zapoznawcza).**

1. Możesz wybrać, czy chcesz, aby alerty z Microsoft Defender for Identity automatycznie generować zdarzenia w Azure Sentinel automatycznie. W **obszarze Tworzenie zdarzeń wybierz** pozycję **Włącz,** aby włączyć domyślną regułę analityczna, która automatycznie tworzy zdarzenia na podstawie alertów generowanych w połączonej usłudze zabezpieczeń. Następnie możesz edytować tę regułę w obszarze **Analiza,** a następnie **aktywne reguły.**

1. Kliknij przycisk **Połącz**.

1. Aby użyć odpowiedniego schematu usługi Log Analytics dla alertów Microsoft Defender for Identity, wyszukaj **securityalert**.

> [!NOTE]
> Jeśli alerty są większe niż 30 KB, Azure Sentinel przestanie wyświetlać pole Jednostki w alertach.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie opisano sposób nawiązywania połączenia Microsoft Defender for Identity z Azure Sentinel. Aby dowiedzieć się więcej o Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, [jak uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Wprowadzenie do [wykrywania zagrożeń za pomocą Azure Sentinel](tutorial-detect-threats-built-in.md).
