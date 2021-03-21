---
title: Wyświetlanie i Konfigurowanie alertów ochrony DDoS dla standardu Azure DDoS Protection
description: Dowiedz się, jak wyświetlać i konfigurować alerty ochrony DDoS Azure DDoS Protection dla warstwy Standardowa.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: a5639d583d9b98f6527e47bf5db213cb191ebeb7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575304"
---
# <a name="view-and-configure-ddos-protection-alerts"></a>Wyświetlanie i konfigurowanie alertów ochrony przed atakami DDoS

Azure DDoS Protection Standard zapewnia szczegółowe informacje o atakach i wizualizacje z DDoSą analizą ataków. Klienci chroniący sieci wirtualne przed atakami DDoS mają szczegółowy wgląd w ruch związany z atakami i działania podejmowane w celu ograniczenia ataku za pośrednictwem raportów ograniczenia ataków, które &ją dzienniki przepływów ograniczenia. Bogate dane telemetryczne są udostępniane za pośrednictwem Azure Monitor, w tym szczegółowych metryk w czasie trwania ataku DDoS. Można skonfigurować alerty dla dowolnych metryk usługi Azure Monitor ujawnionych przez usługę DDoS Protection. Rejestrowanie może być dodatkowo zintegrowane z [platformą Azure](../sentinel/connect-azure-ddos-protection.md), Splunk (Azure Event Hubs), pakietem OMS log Analytics i usługą Azure Storage, aby uzyskać zaawansowaną analizę za pośrednictwem interfejsu diagnostyki Azure monitor.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie alertów w usłudze Azure Monitor
> * Konfigurowanie alertów w portalu
> * Wyświetlanie alertów w usłudze Azure Security Center
> * Weryfikowanie i testowanie alertów

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Przed wykonaniem kroków opisanych w tym samouczku należy najpierw utworzyć [Plan ochrony usługi Azure DDoS Standard](manage-ddos-protection.md) , a w sieci wirtualnej musi być włączona funkcja DDoS Protection Standard.
- DDoS monitoruje publiczne adresy IP przypisane do zasobów w ramach sieci wirtualnej. Jeśli nie masz żadnych zasobów z publicznymi adresami IP w sieci wirtualnej, musisz najpierw utworzyć zasób z publicznym adresem IP. Możesz monitorować publiczny adres IP wszystkich zasobów wdrożonych za pomocą Menedżer zasobów (nieklasyczny) wymieniony w [sieci wirtualnej dla usług platformy Azure (w](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) tym modułów równoważenia obciążenia platformy Azure, w których znajdują się maszyny wirtualne zaplecza w sieci wirtualnej), z wyjątkiem środowisk Azure App Service. Aby kontynuować pracę z tym samouczkiem, możesz szybko utworzyć maszynę wirtualną z [systemem Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .     

## <a name="configure-alerts-through-azure-monitor"></a>Konfigurowanie alertów w usłudze Azure Monitor

Za pomocą tych szablonów można skonfigurować alerty dla wszystkich publicznych adresów IP, na których włączono logowanie diagnostyczne. W związku z tym aby można było używać tych szablonów alertów, najpierw musisz mieć Log Analytics obszar roboczy z włączonymi ustawieniami diagnostycznymi. Zobacz [Wyświetlanie i Konfigurowanie rejestrowania diagnostycznego DDoS](diagnostic-logging.md).

### <a name="azure-monitor-alert-rule"></a>Reguła alertu Azure Monitor

Ta [reguła alertu Azure monitor](https://aka.ms/DDOSmitigationstatus) uruchomi proste zapytanie w celu wykrycia, kiedy występuje ograniczenie aktywnego DDoS. Oznacza to potencjalny atak. Grupy akcji mogą służyć do wywoływania akcji w wyniku alertu.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520Monitor%2520Alert%2520-%2520DDoS%2520Mitigation%2520Started%2FDDoSMitigationStarted.json)

### <a name="azure-monitor-alert-rule-with-logic-app"></a>Reguła alertu Azure Monitor z aplikacją logiki

Ten [szablon](https://aka.ms/ddosalert) służy do wdrażania niezbędnych składników wzbogaconego alertu ograniczenia DDoS: Azure monitor regułę alertu, grupę akcji i aplikację logiki. Wynikiem tego procesu jest alert e-mail ze szczegółowymi informacjami o adresie IP w obszarze ataki, w tym informacje o zasobie skojarzonym z adresem IP. Właściciel zasobu zostanie dodany jako odbiorca wiadomości e-mail wraz z zespołem ds. zabezpieczeń. Zostanie również wykonany podstawowy test dostępności aplikacji, a wyniki zostaną uwzględnione w alercie e-mail.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FDDoS%2520Mitigation%2520Alert%2520Enrichment%2FEnrich-DDoSAlert.json)

## <a name="configure-alerts-through-portal"></a>Konfigurowanie alertów w portalu

Można wybrać dowolną z dostępnych metryk ochrony DDoS, aby poinformować użytkownika o aktywnym ograniczeniu podczas ataku przy użyciu konfiguracji alertu Azure Monitor. 

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) i przejdź do planu DDoS Protection.
2. W obszarze **Monitorowanie** wybierz pozycję **Metryki**.
3. Na szarym pasku nawigacyjnym wybierz pozycję **Nowa reguła alertu**. 
4. Wprowadź lub wybierz własne wartości lub wprowadź następujące przykładowe wartości, zaakceptuj pozostałe ustawienia domyślne, a następnie wybierz pozycję **Utwórz regułę alertu**:

    |Ustawienie                  |Wartość                                                                                               |
    |---------                |---------                                                                                           |
    | Zakres                   | Wybierz pozycję **Wybierz zasób**. </br> Wybierz **subskrypcję** zawierającą publiczny adres IP, który chcesz zalogować, wybierz pozycję **publiczny adres IP** dla opcji **Typ zasobu**, a następnie wybierz konkretny publiczny adres IP, dla którego chcesz rejestrować metryki. </br> Kliknij **Gotowe**. | 
    | Warunek | Wybierz pozycję **Wybierz warunek**. </br> W obszarze Nazwa sygnału zaznacz opcję **w obszarze atak DDoS**. </br> W obszarze **operator** wybierz opcję **większe niż lub równe**. </br> W obszarze **typ agregacji** wybierz wartość **Maksymalna**. </br> W obszarze **wartość progowa** wpisz *1*. W przypadku **ataku typu DDoS lub bez** metryki **wartość 0** oznacza, że nie jesteś w trakcie ataku o wartości **1** oznacza to, że jesteś w trakcie ataku. </br> Kliknij **Gotowe**. | 
    | Akcje | Wybierz pozycję **Dodaj grupy akcji**. </br> Wybierz pozycję **Utwórz grupę akcji**. </br> W obszarze **powiadomienia** w obszarze **Typ powiadomienia** wybierz pozycję **Poczta E-mail/wiadomość SMS/wypychanie/głos**. </br> W polu **Nazwa** wprowadź _MyUnderAttackEmailAlert_. </br> Kliknij przycisk Edytuj, a następnie wybierz opcję **poczta e-mail** i dowolną spośród następujących opcji, a następnie wybierz przycisk **OK**. </br> Wybierz pozycję **Przejrzyj i utwórz**. | 
    | Szczegóły reguły alertu | W obszarze **Nazwa reguły alertu** wprowadź _MyDdosAlert_. |

W ciągu kilku minut od wykrycia ataku należy otrzymać wiadomość e-mail z Azure Monitor metryk, które wyglądają podobnie do poniższej ilustracji:

![Alert ataku](./media/manage-ddos-protection/ddos-alert.png)

Możesz również dowiedzieć się więcej o [konfigurowaniu elementów webhook](../azure-monitor/alerts/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Logic Apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) na potrzeby tworzenia alertów.

## <a name="view-alerts-in-azure-security-center"></a>Wyświetlanie alertów w usłudze Azure Security Center

Azure Security Center zawiera listę [alertów zabezpieczeń](../security-center/security-center-managing-and-responding-alerts.md)z informacjami ułatwiającymi badanie i korygowanie problemów. Dzięki tej funkcji można uzyskać ujednolicony widok alertów, w tym alerty związane z atakiem DDoS oraz działania podejmowane w celu ograniczenia ataków w czasie prawie czasu.
Istnieją dwa konkretne alerty, które będą widoczne w przypadku wykrywania ataków DDoS i łagodzenia rozwiązań:

- **Wykryto atak DDoS dla publicznego adresu IP**: ten alert jest generowany, gdy Usługa ochrony DDoS wykryje, że jeden z publicznych adresów IP jest celem ataku DDoS.
- **Atak DDoS został skorygowany dla publicznego adresu IP**: ten alert jest generowany, gdy atak na publiczny adres IP został skorygowany.
Aby wyświetlić alerty, Otwórz **Security Center** w Azure Portal. W obszarze **Ochrona przed zagrożeniami** wybierz pozycję **alerty zabezpieczeń**. Poniższy zrzut ekranu przedstawia przykład alertów ataku DDoS.

![Alert DDoS w Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

Alerty obejmują ogólne informacje o publicznym adresie IP, który jest objęty atakiem, informacjami o analizie geograficznym i zagrożeń oraz kroki zaradcze.

## <a name="validate-and-test"></a>Weryfikuj i Testuj

Aby zasymulować atak DDoS w celu zweryfikowania alertów, zobacz temat [Validate DDoS Detection](test-through-simulations.md).

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

- Konfigurowanie alertów w usłudze Azure Monitor
- Konfigurowanie alertów w portalu
- Wyświetlanie alertów w usłudze Azure Security Center
- Weryfikowanie i testowanie alertów

Aby dowiedzieć się, jak testować i symulować atak DDoS, zobacz Przewodnik dotyczący testowania symulacji:

> [!div class="nextstepaction"]
> [Testowanie przez symulacje](test-through-simulations.md)
