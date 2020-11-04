---
title: Połącz dane CEF z podglądem platformy Azure — Wersja zapoznawcza | Microsoft Docs
description: Połącz rozwiązanie zewnętrzne, które wysyła komunikaty Common Event format (CEF) do funkcji wskaźnikowej platformy Azure przy użyciu maszyny z systemem Linux jako usługi przesyłania dalej dzienników.
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
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: e09b44504623516d41b6d310a82e78619477367c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93304986"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Łączenie rozwiązania zewnętrznego przy użyciu typowego formatu zdarzeń

Po podłączeniu rozwiązania zewnętrznego, które wysyła komunikaty CEF, istnieją trzy kroki, które należy wykonać, aby nawiązać połączenie z platformą Azure — wskaźnik:

Krok 1. [łączenie CEF przez wdrożenie dziennika systemowego/CEF Forwarder](connect-cef-agent.md) krok 2: [wykonywanie kroków specyficznych dla rozwiązania](connect-cef-solution-config.md) krok 3: [Weryfikowanie łączności](connect-cef-verify.md)

W tym artykule opisano, jak działa połączenie, zawiera wymagania wstępne i przedstawiono kroki wdrażania agenta na rozwiązaniach zabezpieczeń, które wysyłają komunikaty w formacie Common Event format (CEF) na podstawie dziennika systemowego. 

> [!NOTE] 
> Dane są przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.

Aby to połączenie było możliwe, należy wdrożyć serwer usługi przesyłania dalej dziennika systemowego w celu zapewnienia obsługi komunikacji między urządzeniem i wskaźnikiem kontrolnym platformy Azure.  Serwer składa się z dedykowanej maszyny z systemem Linux (maszyna wirtualna lub lokalna) z zainstalowanym agentem Log Analytics dla systemu Linux. 

Na poniższym diagramie opisano konfigurację w przypadku maszyny wirtualnej z systemem Linux na platformie Azure:

 ![CEF na platformie Azure](./media/connect-cef/cef-syslog-azure.png)

Ta konfiguracja będzie również dostępna w przypadku korzystania z maszyny wirtualnej w innej chmurze lub na maszynie lokalnej: 

 ![CEF lokalnie](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Upewnij się, że skonfigurowano zabezpieczenia maszyny zgodnie z zasadami zabezpieczeń organizacji. Można na przykład skonfigurować sieć do dopasowania do zasad zabezpieczeń sieci firmowej i zmienić porty i protokoły w demoum, aby dostosować je do swoich wymagań. Aby ulepszyć konfigurację zabezpieczeń komputera, można użyć następujących instrukcji:  [bezpieczna maszyna wirtualna na platformie Azure](../virtual-machines/security-policy.md), [najlepsze rozwiązania dotyczące zabezpieczeń sieci](../security/fundamentals/network-best-practices.md).

Aby można było korzystać z komunikacji TLS między źródłem dziennika systemowego a usługą przesyłania dalej dziennika systemu, należy skonfigurować demona dziennika systemowego (rsyslog lub dziennika systemowego) do komunikacji w protokole TLS: [szyfrowanie ruchu dziennika systemu przy użyciu protokołu TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [szyfrowanie komunikatów dzienników przy użyciu protokołu TLS — dziennik systemowy-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).
 
## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że maszyna z systemem Linux używana jako usługa przesyłania dalej dzienników ma jeden z następujących systemów operacyjnych:

- 64-bitowa
  - CentOS 7 i 8, w tym wersje podrzędne (nie 6)
  - Amazon Linux 2017,09
  - Oracle Linux 7
  - Red Hat Enterprise Linux (RHEL) Server 7 i 8, w tym wersje podrzędne (nie 6)
  - Debian GNU/Linux 8, 9 i 10
  - Ubuntu Linux 14,04 LTS, 16,04 LTS i 18,04 LTS
  - SUSE Linux Enterprise Server 12, 15

- 32-bitowa
  - CentOS 7 i 8, w tym wersje podrzędne (nie 6)
  - Oracle Linux 7
  - Red Hat Enterprise Linux (RHEL) Server 7 i 8, w tym wersje podrzędne (nie 6)
  - Debian GNU/Linux 8, 9 i 10
  - Ubuntu Linux 14,04 LTS i 16,04 LTS
 
- Wersje demona
  - Dziennik systemu — NG: 2,1-3.22.1
  - Rsyslog: V8
  
- Obsługiwane są specyfikacje RFC dziennika systemowego
  - Dziennik systemowy RFC 3164
  - Dziennik systemowy RFC 5424
 
Upewnij się, że komputer spełnia również następujące wymagania: 

- Uprawnienia
  - Musisz mieć podwyższony poziom uprawnień (sudo) na swojej maszynie. 

- Wymagania dotyczące oprogramowania
  - Upewnij się, że na maszynie jest uruchomiony Język Python 2,7.

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób zbierania dzienników CEF z rozwiązań i urządzeń zabezpieczających przez platformę Azure. Aby dowiedzieć się, jak połączyć rozwiązanie z platformą Azure, zobacz następujące artykuły:

- Krok 1. [łączenie CEF przez wdrożenie dziennika systemowego/CEF usługi przesyłania dalej](connect-cef-agent.md)
- Krok 2. [wykonanie kroków specyficznych dla rozwiązania](connect-cef-solution-config.md)
- Krok 3. [Weryfikowanie łączności](connect-cef-verify.md)

Aby dowiedzieć się więcej na temat tego, co należy zrobić z danymi zebranymi na platformie Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats.md).

