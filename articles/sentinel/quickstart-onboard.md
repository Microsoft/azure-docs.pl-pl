---
title: 'Szybki Start: dołączanie na platformie Azure — wskaźnik'
description: W tym przewodniku szybki start dowiesz się, jak przejść na platformę Azure, a następnie, a następnie połączyć źródła danych.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 10/14/2020
ms.custom: references_regions
ms.openlocfilehash: 107806b6372360e501fe9cc0a21d25cff5cc35f9
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2020
ms.locfileid: "95248649"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Szybki start: wdrażanie usługi Azure Sentinel

W tym przewodniku szybki start dowiesz się, jak dołączyć wskaźnik do platformy Azure. 

Aby przenieść wskaźnik na platformę Azure, musisz najpierw włączyć wskaźnik na platformie Azure, a następnie połączyć źródła danych. Na platformie Azure jest dostępnych wiele łączników dla rozwiązań firmy Microsoft, które są dostępne na bieżąco i zapewniają integrację w czasie rzeczywistym, w tym Microsoft 365 Defender (dawniej chronione przed zagrożeniami firmy Microsoft), źródła Microsoft 365 (w tym Office 365), Azure AD, Microsoft Defender for Identity (dawniej Azure ATP), Microsoft Cloud App Security, alerty usługi Azure Defender z Azure Security Center i inne. Ponadto istnieją wbudowane łączniki do szerszego ekosystemu zabezpieczeń dla rozwiązań firm innych niż Microsoft. Do łączenia źródeł danych z platformą Azure (CEF), dziennikiem systemu 

Po nawiązaniu połączenia ze źródłami danych wybierz jedną z galerii ze szczegółowymi skoroszytami utworzonymi na podstawie danych. Te skoroszyty mogą być łatwo dostosowane do Twoich potrzeb.

>[!IMPORTANT] 
> Aby uzyskać informacje o opłatach naliczanych podczas korzystania z platformy Azure, zobacz [Cennik usługi Azure — wskaźnik](https://azure.microsoft.com/pricing/details/azure-sentinel/).

## <a name="global-prerequisites"></a>Ogólne wymagania wstępne

- Aktywną subskrypcję platformy Azure, jeśli jej nie masz, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

- Log Analytics obszar roboczy. Dowiedz się [, jak utworzyć obszar roboczy log Analytics](../azure-monitor/learn/quick-create-workspace.md). Aby uzyskać więcej informacji na temat Log Analytics obszarów roboczych, zobacz [projektowanie Azure monitor dzienników wdrożenia](../azure-monitor/platform/design-logs-deployment.md).

- Aby włączyć wskaźnik na platformie Azure, musisz mieć uprawnienia współautora do subskrypcji, w której znajduje się obszar roboczy wskaźnik platformy Azure. 
- Aby korzystać z platformy Azure, musisz mieć uprawnienia współautora lub czytelnika w grupie zasobów, do której należy obszar roboczy.
- Do nawiązania połączenia z określonymi źródłami danych mogą być konieczne dodatkowe uprawnienia.
- Azure Sentinel to usługa płatna. Aby uzyskać informacje o cenach, zobacz [Informacje o wskaźniku platformy Azure](https://go.microsoft.com/fwlink/?linkid=2104058).

### <a name="geographical-availability-and-data-residency"></a>Dostępność geograficzna i dane miejsca do zamieszkania

- Wskaźnik "Azure" może działać w obszarach roboczych w większości [regionów log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) z wyjątkiem regionów Chińska i Niemcy (suwerenne). Czasami nowe regiony Log Analytics mogą chwilę potrwać, aby dołączyć do usługi wskaźnikowej. 

- Dane generowane przez wskaźnik na platformie Azure, takie jak zdarzenia, zakładki i reguły analityczne, mogą zawierać niektóre dane klienta pochodzące z Log Analytics obszarów roboczych klienta. Te dane generowane przez wskaźnik na platformie Azure są zapisywane w lokalizacji geograficznej wymienionej w poniższej tabeli, zgodnie z lokalizacją geograficzną, w której znajduje się obszar roboczy:

    | Obszar roboczy obszaru roboczego | Azure wskaźnikowego — wygenerowana geograficzna danych |
    | --- | --- |
    | Stany Zjednoczone<br>Indie<br>Brazylia<br>Afryka<br>Korea | Stany Zjednoczone |
    | Europa<br>Francja<br>Szwajcaria | Europa |
    | Australia | Australia |
    | Zjednoczone Królestwo | Zjednoczone Królestwo |
    | Kanada | Kanada |
    | Japonia | Japonia |
    |

## <a name="enable-azure-sentinel"></a>Włącz platformę Azure — wskaźnik <a name="enable"></a>

1. Zaloguj się w witrynie Azure Portal. Upewnij się, że wybrano subskrypcję, w której tworzony jest wskaźnik "Azure".

1. Wyszukaj i wybierz pozycję **wskaźnik na platformie Azure**.

   ![Wyszukiwanie usług](./media/quickstart-onboard/search-product.png)

1. Wybierz pozycję **Dodaj**.

1. Wybierz obszar roboczy, którego chcesz użyć, lub Utwórz nowy. Możesz uruchomić wskaźnik platformy Azure na więcej niż jednym obszarze roboczym, ale dane są izolowane do jednego obszaru roboczego.

   ![Wybierz obszar roboczy](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Domyślne obszary robocze utworzone przez Azure Security Center nie będą wyświetlane na liście; nie można zainstalować na nich wskaźnikiem na platformie Azure.
   >

   >[!IMPORTANT]
   >
   > - Po wdrożeniu w obszarze roboczym centrum kontrolne platformy Azure **nie obsługuje obecnie** przeniesienia tego obszaru roboczego do innych grup zasobów lub subskrypcji. 
   >
   >   Jeśli obszar roboczy został już przeniesiony, wyłącz wszystkie aktywne reguły w obszarze **Analiza** i włącz je ponownie po pięciu minutach. Ta sytuacja powinna być skuteczna w większości przypadków, ale w celu przeprowadzonej iteracji nie jest to obsługiwane i realizowane na własne ryzyko.

1. Wybierz pozycję **Dodaj wskaźnik platformy Azure**.

## <a name="connect-data-sources"></a>Łączenie ze źródłami danych

Na platformie Azure wskaźnik pozyskiwania danych z usług i aplikacji jest pobierany przez połączenie z usługą i przekazywanie zdarzeń i dzienników do funkcji wskaźnikowej platformy Azure. W przypadku maszyn fizycznych i wirtualnych można zainstalować agenta Log Analytics, który zbiera dzienniki i przekazuje je do usługi Azure wskaźnikowej. W przypadku zapór i serwerów proxy usługa Azure wskaźnikowa instaluje agenta Log Analytics na serwerze dziennika systemu Linux, z którego Agent zbiera pliki dziennika i przekazuje je do usługi Azure wskaźnikowej. 
 
1. Z menu głównego wybierz pozycję **Łączniki danych**. Spowoduje to otwarcie galerii łączników danych.

1. Galeria to lista wszystkich źródeł danych, z którymi można nawiązać połączenie. Wybierz źródło danych, a następnie przycisk **Otwórz stronę łącznika** .

1. Na stronie łącznika są wyświetlane instrukcje dotyczące konfigurowania łącznika oraz dodatkowe instrukcje, które mogą być konieczne.<br>
Na przykład w przypadku wybrania **Azure Active Directory** źródła danych, które umożliwia strumieniowe przesyłanie dzienników z usługi Azure AD do platformy Azure, możesz wybrać typ dzienników do pobrania i/lub dzienniki inspekcji w sieci WAN. <br> Postępuj zgodnie z instrukcjami instalacji lub [zapoznaj się z odpowiednim przewodnikiem połączenia](connect-data-sources.md) , aby uzyskać więcej informacji. Aby uzyskać informacje na temat łączników danych, zobacz [łączenie usług firmy Microsoft](connect-data-sources.md).

1. Na karcie **następne kroki** na stronie łączniku są wyświetlane odpowiednie wbudowane skoroszyty, przykładowe zapytania i szablony reguł analizy, które towarzyszą łącznikowi danych. Można ich użyć jako-lub zmodyfikować — w dowolny sposób można natychmiast uzyskać interesujące informacje w danych. <br>

Po nawiązaniu połączenia ze źródłami danych rozpocznie się przesyłanie strumieniowe danych do platformy Azure. Możesz wyświetlić dzienniki w [wbudowanych skoroszytach](quickstart-get-visibility.md) i rozpocząć tworzenie zapytań w log Analytics, aby [zbadać dane](tutorial-investigate-cases.md).

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono informacje na temat dołączania i łączenia źródeł danych z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).
- Przesyłaj strumieniowo dane ze [wspólnych urządzeń w formacie zdarzenia](connect-common-event-format.md) do platformy Azure.
