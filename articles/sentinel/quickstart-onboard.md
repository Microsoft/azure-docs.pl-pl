---
title: 'Szybki start: wbudowany w usłudze Azure Sentinel'
description: Dowiedz się, jak zbierać dane w usłudze Azure Sentinel, wykonując ten dokument przewodnika Szybki start.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 12/05/2019
ms.openlocfilehash: bcb6cd7393419fb8fc75decb00a8f4165d15668c
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025275"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Szybki start: wbudowany program Azure Sentinel

W tym przewodniku Szybki start dowiedz się, jak ować na pokładzie usługi Azure Sentinel. 

Aby urządzenie Azure Sentinel było wbudowane, należy najpierw włączyć usługę Azure Sentinel, a następnie połączyć źródła danych. Usługa Azure Sentinel jest dostępna z wieloma łącznikami dla rozwiązań firmy Microsoft, dostępnymi po wyjęciu z pudełka i zapewniającymi integrację w czasie rzeczywistym, w tym rozwiązaniami microsoft threat protection, źródłami usługi Microsoft 365, w tym usługą Office 365, usługą Azure AD, azure ATP i microsoft cloud app security i wieloma usługami. Ponadto istnieją wbudowane łączniki do szerszego ekosystemu zabezpieczeń dla rozwiązań innych firm. Można również użyć wspólnego formatu zdarzeń, Syslog lub REST-API, aby połączyć źródła danych z usługą Azure Sentinel.  

Po połączeniu źródeł danych wybierz z galerii profesjonalnie utworzonych skoroszytów, które powierzchniowe są oparte na danych. Te skoroszyty można łatwo dostosować do twoich potrzeb.

>[!IMPORTANT] 
> Aby uzyskać informacje na temat opłat poniesionych podczas korzystania z usługi Azure Sentinel, zobacz [Cennik wartowania platformy Azure](https://azure.microsoft.com/pricing/details/azure-sentinel/).
  

## <a name="global-prerequisites"></a>Globalne wymagania wstępne

- Aktywna subskrypcja platformy Azure, jeśli jej nie masz, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

- Obszar roboczy analizy dzienników. Dowiedz się, jak [utworzyć obszar roboczy usługi Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). Aby uzyskać więcej informacji na temat obszarów roboczych usługi Log Analytics, zobacz [Projektowanie wdrożenia dzienników usługi Azure Monitor](../azure-monitor/platform/design-logs-deployment.md).

- Aby włączyć usługę Azure Sentinel, potrzebujesz uprawnień współautora do subskrypcji, w której znajduje się obszar roboczy usługi Azure Sentinel. 
- Aby korzystać z usługi Azure Sentinel, potrzebujesz uprawnień współautora lub czytelnika do grupy zasobów, do której należy obszar roboczy.
- Dodatkowe uprawnienia mogą być potrzebne do łączenia określonych źródeł danych.
- Usługa Azure Sentinel to płatna usługa. Aby uzyskać informacje o cenach, zobacz [Informacje o usłudze Azure Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058).
 
## <a name="enable-azure-sentinel"></a>Włącz usługę Azure Sentinel<a name="enable"></a>

1. Zaloguj się do Portalu Azure. Upewnij się, że jest zaznaczona subskrypcja, w której jest tworzony program Azure Sentinel.

1. Wyszukaj i wybierz **usługę Azure Sentinel**.

   ![search](./media/quickstart-onboard/search-product.png)

1. Wybierz pozycję **Dodaj**.

1. Wybierz obszar roboczy, którego chcesz użyć, lub utwórz nowy. Usługę Azure Sentinel można uruchomić w więcej niż jednym obszarze roboczym, ale dane są izolowane do pojedynczego obszaru roboczego.

   ![search](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Domyślne obszary robocze utworzone przez usługę Azure Security Center nie będą wyświetlane na liście; nie można zainstalować na nich usługi Azure Sentinel.
   > - Usługa Azure Sentinel może działać w obszarach roboczych w dowolnym [regionie ga usługi Log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) z wyjątkiem regionów Chiny, Niemcy i Azure Government. Dane generowane przez usługę Azure Sentinel (takie jak zdarzenia, zakładki i reguły alertów, które mogą zawierać niektóre dane klientów pochodzące z tych obszarów roboczych) są zapisywane w Europie Zachodniej (dla obszarów roboczych zlokalizowanych w Europie) lub wschodnich stanach ZJEDNOCZONYch (dla wszystkich obszarów roboczych opartych na stanach Zjednoczonych, a także w każdym innym regionie z wyjątkiem Europy).

1. Wybierz **pozycję Dodaj wartownika platformy Azure**.
  

## <a name="connect-data-sources"></a>Łączenie ze źródłami danych

Usługa Azure Sentinel tworzy połączenie z usługami i aplikacjami, łącząc się z usługą i przesyłając dalej zdarzenia i dzienniki do usługi Azure Sentinel. W przypadku maszyn i maszyn wirtualnych można zainstalować agenta wartownika platformy Azure, który zbiera dzienniki i przekazuje je do usługi Azure Sentinel. W przypadku zapór i serwerów proxy usługa Azure Sentinel korzysta z serwera Syslog systemu Linux. Agent jest zainstalowany na nim i z którego agent zbiera pliki dziennika i przekazuje je do usługi Azure Sentinel. 
 
1. Kliknij **pozycję Łączniki danych**.
2. Istnieje kafelek dla każdego źródła danych, które można połączyć.<br>
Na przykład kliknij pozycję **Azure Active Directory**. Jeśli połączysz to źródło danych, przesyłasz strumieniowo wszystkie dzienniki z usługi Azure AD do usługi Azure Sentinel. Można wybrać typ dzienników, które wan, aby uzyskać - logi logowania i/lub inspekcji dzienników. <br>
U dołu usługi Azure Sentinel zawiera zalecenia dotyczące skoroszytów, które należy zainstalować dla każdego łącznika, dzięki czemu można natychmiast uzyskać interesujące informacje na temat danych. <br> Aby uzyskać więcej informacji, należy postępować zgodnie z instrukcjami instalacji lub [zapoznać się z odpowiednim przewodnikiem po połączeniu.](connect-data-sources.md) Aby uzyskać informacje o łącznikach danych, zobacz [Łączenie usług firmy Microsoft](connect-data-sources.md).

Po nawiązaniu połączenia źródeł danych dane rozpoczynają przesyłanie strumieniowe do usługi Azure Sentinel i są gotowe do rozpoczęcia pracy. Można wyświetlić dzienniki we [wbudowanych pulpitach nawigacyjnych](quickstart-get-visibility.md) i rozpocząć tworzenie zapytań w usłudze Log Analytics w celu [zbadania danych.](tutorial-investigate-cases.md)



## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się o łączeniu źródeł danych z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).
- Przesyłaj strumieniowo dane z [urządzeń common event format](connect-common-event-format.md) do usługi Azure Sentinel.
