---
title: Nawiązywanie połączenia poza danymi beSECURE zabezpieczeń w usłudze Azure wskaźnikowej | Microsoft Docs
description: Dowiedz się, w jaki sposób używać łącznika danych beSECURE z zabezpieczeniami do ściągania dzienników beSECURE do usługi Azure wskaźnikowej. Wyświetlaj dane beSECURE w skoroszytach, twórz Alerty i ulepszaj badanie.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: f6fd6920fafe4c1080cb5539e4e0222d9d6e18cd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93103025"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>Łączenie poza beSECUREem zabezpieczeń z platformą Azure — wskaźnikiem

> [!IMPORTANT]
> Łącznik danych beSECURE z zabezpieczeniami na platformie Azure jest obecnie w publicznej wersji zapoznawczej. Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Za pomocą łącznika usługi Security beSECURE można z łatwością połączyć wszystkie dzienniki rozwiązań zabezpieczeń beSECURE z obszarem danych platformy Azure, aby wyświetlić pulpity nawigacyjne, utworzyć niestandardowe alerty i poprawić badanie. Integracja między programem beSECURE i platformą Azure ze wskaźnikami korzysta z interfejsu API REST.

> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

## <a name="configure-and-connect-besecure"></a>Konfigurowanie i łączenie beSECURE

beSECURE można zintegrować z dziennikami i eksportować je bezpośrednio do platformy Azure.

1. W portalu usługi Azure wskaźnikowego kliknij pozycję **Łączniki danych** i wybierz pozycję **poza zabezpieczeniami beSECURE (wersja zapoznawcza)** , a następnie **Otwórz stronę łącznik** .

1. Wykonaj poniższe kroki, aby skonfigurować rozwiązanie beSECURE do wysyłania wyników skanowania, stanu skanowania i dzienników dziennika inspekcji do usługi Azure wskaźnikowej.

    **Dostęp do menu integracja:**
    1. Kliknij opcję menu "więcej"

    1. Wybierz serwer

    1. Wybierz integrację

    1. Włączanie usługi Azure Sentinel

    **Podaj beSECURE z ustawieniami wskaźnikiem na platformie Azure.**
      - Skopiuj wartości *Identyfikator obszaru roboczego* i *klucz podstawowy* ze strony łącznika wskaźnikowego platformy Azure, wklej je w konfiguracji beSECURE, a następnie kliknij przycisk **Modyfikuj** .

## <a name="find-your-data"></a>Znajdowanie danych

Po pomyślnym nawiązaniu połączenia dane pojawiają się w **dziennikach** w sekcji **CustomLogs** w co najmniej jednej z następujących tabel:
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

Aby wykonać zapytanie dotyczące dzienników beSECURE w Log Analytics, wprowadź jedną z powyższych nazw tabel w górnej części okna zapytania.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności
Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać do 20 minut.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia beSECURE z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.
