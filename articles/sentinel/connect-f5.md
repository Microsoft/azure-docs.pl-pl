---
title: Połącz dane z F5 ASM z platformą Azure — wskaźnikiem | Microsoft Docs
description: Dowiedz się, jak za pomocą łącznika danych programu F5 ASM ściągać Dzienniki systemu. Wyświetlaj dane z F5 ASM w skoroszytach, twórz Alerty i ulepszaj badanie.
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
ms.date: 05/20/2020
ms.author: yelevin
ms.openlocfilehash: 81a0f0e01827c48518f2eb1fba062e9e6536d9df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655701"
---
# <a name="connect-f5-asm-to-azure-sentinel"></a>Połącz F5 ASM z platformą Azure wskaźnikiem

W tym artykule wyjaśniono, jak za pomocą łącznika danych programu F5 ASM można łatwo ściągać dzienniki programu F5 ASM do usługi Azure wskaźnikowej. Pozwala to na wyświetlanie danych F5 ASM w skoroszytach, korzystanie z nich do tworzenia alertów niestandardowych i uwzględnianie go w celu usprawnienia badania. Umieszczenie danych z F5 ASM na platformie Azure wskaźnikowych pozwoli Ci uzyskać więcej informacji o zabezpieczeniach aplikacji sieci Web w organizacji i poprawi możliwości operacji związanych z bezpieczeństwem. 

## <a name="configure-your-f5-asm-to-send-cef-messages"></a>Skonfiguruj F5 ASM do wysyłania komunikatów CEF

1. Postępuj zgodnie z instrukcjami w [F5 Konfigurowanie rejestrowania zdarzeń zabezpieczeń aplikacji](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html) , aby skonfigurować rejestrowanie zdalne, korzystając z następujących wskazówek:
   - Ustaw **Typ magazynu zdalnego** na **CEF**.
   - Ustaw **Protokół** na **TCP**.
   - Ustaw **adres IP** na adres IP serwera dziennika systemowego.
   - Ustaw **numer portu** na **514** lub port, który ma być używany przez agenta.
   - **Maksymalny rozmiar ciągu zapytania** można ustawić na rozmiar ustawiony w agencie.

1. Aby użyć odpowiedniego schematu w Log Analytics dla zdarzeń CEF, wyszukaj ciąg `CommonSecurityLog` .

1. Przejdź do [kroku 3: weryfikowanie łączności](connect-cef-verify.md).


## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia F5 ASM z platformą Azure wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](./tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.