---
title: 'Szybki Start: dołączanie na platformie Azure — wskaźnik'
description: Dowiedz się, jak zbierać dane na platformie Azure — zgodnie z tym dokumentem szybkiego startu.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 149ac61e4737faa81e55fac3e07133c73bbc00d5
ms.sourcegitcommit: 20e246e86e25d63bcd521a4b4d5864fbc7bad1b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2020
ms.locfileid: "84488124"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Szybki Start: tablica wskaźnikowa platformy Azure

W tym przewodniku szybki start dowiesz się, jak dołączyć wskaźnik do platformy Azure. 

Aby przenieść wskaźnik na platformę Azure, musisz najpierw włączyć wskaźnik na platformie Azure, a następnie połączyć źródła danych. Wskaźnik platformy Azure obejmuje wiele łączników dla rozwiązań firmy Microsoft, dostępnych poza platformą i zapewniania integracji w czasie rzeczywistym, w tym rozwiązań ochrony przed zagrożeniami firmy Microsoft, źródeł Microsoft 365, takich jak Office 365, Azure AD, Azure ATP i Microsoft Cloud App Security. Ponadto istnieją wbudowane łączniki do szerszego ekosystemu zabezpieczeń dla rozwiązań firm innych niż Microsoft. Możesz również użyć typowego formatu zdarzeń, dziennika systemowego lub REST-API, aby połączyć źródła danych z platformą Azure.  

Po nawiązaniu połączenia ze źródłami danych wybierz jedną z galerii ze szczegółowymi skoroszytami utworzonymi na podstawie danych. Te skoroszyty mogą być łatwo dostosowane do Twoich potrzeb.

>[!IMPORTANT] 
> Aby uzyskać informacje o opłatach naliczanych podczas korzystania z platformy Azure, zobacz [Cennik usługi Azure — wskaźnik](https://azure.microsoft.com/pricing/details/azure-sentinel/).
  

## <a name="global-prerequisites"></a>Ogólne wymagania wstępne

- Aktywną subskrypcję platformy Azure, jeśli jej nie masz, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

- Log Analytics obszar roboczy. Dowiedz się [, jak utworzyć obszar roboczy log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). Aby uzyskać więcej informacji na temat Log Analytics obszarów roboczych, zobacz [projektowanie Azure monitor dzienników wdrożenia](../azure-monitor/platform/design-logs-deployment.md).

- Aby włączyć wskaźnik na platformie Azure, musisz mieć uprawnienia współautora do subskrypcji, w której znajduje się obszar roboczy wskaźnik platformy Azure. 
- Aby korzystać z platformy Azure, musisz mieć uprawnienia współautora lub czytelnika w grupie zasobów, do której należy obszar roboczy.
- Do nawiązania połączenia z określonymi źródłami danych mogą być konieczne dodatkowe uprawnienia.
- Azure — Wskaźnikowanie jest płatną usługą. Aby uzyskać informacje o cenach, zobacz [Informacje o wskaźniku platformy Azure](https://go.microsoft.com/fwlink/?linkid=2104058).
 
## <a name="enable-azure-sentinel"></a>Włącz platformę Azure — wskaźnik<a name="enable"></a>

1. Zaloguj się do witryny Azure Portal. Upewnij się, że wybrano subskrypcję, w której tworzony jest wskaźnik "Azure".

1. Wyszukaj i wybierz pozycję **wskaźnik na platformie Azure**.

   ![search](./media/quickstart-onboard/search-product.png)

1. Wybierz pozycję **Dodaj**.

1. Wybierz obszar roboczy, którego chcesz użyć, lub Utwórz nowy. Możesz uruchomić wskaźnik platformy Azure na więcej niż jednym obszarze roboczym, ale dane są izolowane do jednego obszaru roboczego.

   ![search](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Domyślne obszary robocze utworzone przez Azure Security Center nie będą wyświetlane na liście; nie można zainstalować na nich wskaźnikiem na platformie Azure.
   > - Wskaźnik "Azure" może działać w obszarach roboczych w dowolnym [regionie log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) z wyjątkiem regionów Chińska i Niemcy (suwerenne). Dane generowane przez wskaźnik na platformie Azure (takie jak zdarzenia, zakładki i reguły alertów, które mogą zawierać niektóre dane klienta pochodzące z tych obszarów roboczych) są zapisywane w Europie (dla obszarów roboczych znajdujących się w Europie), w Australii (dla obszarów roboczych znajdujących się w Australii) lub w regionie Wschodnie stany USA (dla obszarów roboczych znajdujących się w innym regionie).

   >[!IMPORTANT]
   > - Po włączeniu funkcji wskaźnikowej platformy Azure w obszarze roboczym przeniesienie tego obszaru roboczego do innych grup zasobów lub subskrypcji nie jest obsługiwane. Jeśli musisz przenieść obszar roboczy, skontaktuj się z pomocą techniczną, aby uzyskać wskazówki.

1. Wybierz pozycję **Dodaj wskaźnik platformy Azure**.
  

## <a name="connect-data-sources"></a>Łączenie ze źródłami danych

Usługa Azure wskaźnikowego tworzy połączenie z usługami i aplikacjami przez połączenie się z usługą i przekazanie zdarzeń i dzienników do funkcji wskaźnikowej platformy Azure. W przypadku maszyn i maszyn wirtualnych można zainstalować agenta wskaźnikowego platformy Azure, który zbiera dzienniki i przekazuje je do usługi Azure wskaźnikowej. W przypadku zapór i serwerów proxy usługa Azure użytks wykorzystuje serwer dziennika systemu Linux. Agent jest zainstalowany na nim i z którego Agent zbiera pliki dziennika i przekazuje je do usługi Azure wskaźnikowej. 
 
1. Kliknij pozycję **Łączniki danych**.
1. Istnieje kafelek dla każdego źródła danych, które można połączyć.<br>
Na przykład kliknij pozycję **Azure Active Directory**. Jeśli połączysz to źródło danych, przesyłasz strumieniowo wszystkie dzienniki z usługi Azure AD do usługi Azure wskaźnikowej. Możesz wybrać typ dzienników sieci WAN do pobrania i/lub dzienników inspekcji. <br>
Na dole wskaźnik na platformie Azure obejmuje zalecenia dotyczące tego, które skoroszyty należy zainstalować dla każdego łącznika, dzięki czemu można natychmiast uzyskać interesujące informacje w danych. <br> Postępuj zgodnie z instrukcjami instalacji lub [zapoznaj się z odpowiednim przewodnikiem połączenia](connect-data-sources.md) , aby uzyskać więcej informacji. Aby uzyskać informacje na temat łączników danych, zobacz [łączenie usług firmy Microsoft](connect-data-sources.md).

Po nawiązaniu połączenia ze źródłami danych rozpocznie się przesyłanie strumieniowe danych do platformy Azure. Możesz wyświetlić dzienniki na [wbudowanych pulpitach nawigacyjnych](quickstart-get-visibility.md) i rozpocząć tworzenie zapytań w log Analytics, aby [zbadać dane](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Następne kroki
Ten dokument zawiera informacje o łączeniu źródeł danych z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- Przesyłaj strumieniowo dane ze [wspólnych urządzeń w formacie zdarzenia](connect-common-event-format.md) do platformy Azure.
