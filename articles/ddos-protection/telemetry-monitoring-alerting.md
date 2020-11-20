---
title: Wyświetlanie i konfigurowanie telemetrii ochrony przed atakami DDoS
description: Dowiedz się, jak wyświetlać i konfigurować telemetrię ochrony DDoS.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 5c80e5e611c275c2a2262963aa0759075fca836b
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989398"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>Wyświetlanie i konfigurowanie telemetrii ochrony przed atakami DDoS

Azure DDoS Protection Standard zapewnia szczegółowe informacje o atakach i wizualizacje z DDoSą analizą ataków. Klienci chroniący sieci wirtualne przed atakami DDoS mają szczegółowy wgląd w ruch związany z atakami i działania podejmowane w celu ograniczenia ataku za pośrednictwem raportów ograniczenia ataków, które &ją dzienniki przepływów ograniczenia. Bogate dane telemetryczne są udostępniane za pośrednictwem Azure Monitor, w tym szczegółowych metryk w czasie trwania ataku DDoS. Alerty można skonfigurować dla dowolnych metryk Azure Monitor uwidocznionych przez DDoS Protection. Rejestrowanie może być dodatkowo zintegrowane z [platformą Azure](../sentinel/connect-azure-ddos-protection.md), Splunk (Azure Event Hubs), pakietem OMS log Analytics i usługą Azure Storage, aby uzyskać zaawansowaną analizę za pośrednictwem interfejsu diagnostyki Azure monitor.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie alertów dotyczących metryk ochrony DDoS
> * Korzystanie z telemetrii ochrony DDoS
> * Wyświetlanie zasad łagodzenia DDoS
> * Wyświetlanie alertów dotyczących ochrony DDoS w Azure Security Center

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Przed wykonaniem kroków opisanych w tym samouczku należy najpierw utworzyć [Plan ochrony programu Azure DDoS Standard](manage-ddos-protection.md).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Konfigurowanie alertów dotyczących metryk ochrony DDoS

Można wybrać dowolną z dostępnych metryk ochrony DDoS, aby poinformować użytkownika o aktywnym ograniczeniu podczas ataku przy użyciu konfiguracji alertu Azure Monitor. Gdy warunki są spełnione, określony adres otrzymuje wiadomość e-mail z alertem:

1. Wybierz pozycję **wszystkie usługi** u góry, po lewej stronie portalu.
2. Wprowadź wartość *monitor* w polu **Filtr** . Gdy **monitor** pojawi się w wynikach, wybierz go.
3. Wybierz pozycję **metryki** w obszarze **usługi udostępnione**.
4. Wprowadź lub wybierz własne wartości lub wprowadź następujące przykładowe wartości, zaakceptuj pozostałe ustawienia domyślne, a następnie wybierz przycisk **OK**:

    |Ustawienie                  |Wartość                                                                                               |
    |---------                |---------                                                                                           |
    |Nazwa                     | Wprowadź _MyDdosAlert_.                                                                                |
    |Subskrypcja             | Wybierz subskrypcję zawierającą publiczny adres IP, dla którego chcesz otrzymywać alerty.        |
    |Grupa zasobów           | Wybierz grupę zasobów zawierającą publiczny adres IP, dla którego chcesz otrzymywać alerty.      |
    |Zasób                 | Wybierz publiczny adres IP, który zawiera publiczny adres IP, dla którego chcesz otrzymywać alerty. DDoS monitoruje publiczne adresy IP przypisane do zasobów w ramach sieci wirtualnej. Jeśli nie masz żadnych zasobów z publicznymi adresami IP w sieci wirtualnej, musisz najpierw utworzyć zasób z publicznym adresem IP. Możesz monitorować publiczny adres IP wszystkich zasobów wdrożonych za pomocą Menedżer zasobów (nieklasyczny) wymienionych w [sieci wirtualnej dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), z wyjątkiem środowisk Azure App Service i platformy Azure VPN Gateway. Aby kontynuować pracę z tym samouczkiem, możesz szybko utworzyć maszynę wirtualną z [systemem Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .                   |
    |Metryka                   | Wybierz opcję **w obszarze atak DDoS**.                                                                |
    |Próg                | 1- **1** oznacza, że jesteś w trakcie ataku. **0** oznacza, że nie jesteś w trakcie ataku.                         |
    |Okres                   | Wybierz dowolną wybraną wartość.                                                                   |
    |Powiadom pocztą E-mail         | Zaznacz pole wyboru.                                                                                 |
    |Dodatkowy administrator | Wprowadź swój adres e-mail, jeśli nie jesteś właścicielem, współautorem lub czytelnikem poczty e-mail dla subskrypcji. |

    W ciągu kilku minut wykrywania ataków otrzymasz wiadomość e-mail od Azure Monitor metryk, które wyglądają podobnie do poniższej ilustracji:

    ![Alert ataku](./media/manage-ddos-protection/ddos-alert.png)


Aby zasymulować atak DDoS w celu zweryfikowania alertu, zobacz [Validate DDoS Detection](test-through-simulations.md).

Możesz również dowiedzieć się więcej o [konfigurowaniu elementów webhook](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Logic Apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) na potrzeby tworzenia alertów.

## <a name="use-ddos-protection-telemetry"></a>Korzystanie z telemetrii ochrony DDoS

Dane telemetryczne dla ataku są udostępniane za pośrednictwem Azure Monitor w czasie rzeczywistym. Dane telemetryczne są dostępne tylko dla czasu, w którym publiczny adres IP jest objęty ograniczeniem. Nie widzisz danych telemetrycznych przed usunięciem ataku lub po nim.

1. Wybierz pozycję **wszystkie usługi** u góry, po lewej stronie portalu.
2. Wprowadź wartość *monitor* w polu **Filtr** . Gdy **monitor** pojawi się w wynikach, wybierz go.
3. Wybierz pozycję **metryki** w obszarze **usługi udostępnione**.
4. Wybierz **subskrypcję** i **grupę zasobów** , która zawiera publiczny adres IP, dla którego chcesz użyć danych telemetrycznych.
5. Wybierz pozycję **publiczny adres IP** dla opcji **Typ zasobu**, a następnie wybierz konkretny publiczny adres IP, dla którego chcesz użyć danych telemetrycznych.
6. Seria **dostępnych metryk** pojawia się po lewej stronie ekranu. Te metryki, po wybraniu, są przedstawiane na wykresie **metryk Azure monitor** na ekranie przegląd.
7. Wybierz typ **agregacji** jako **maksimum**

Nazwy metryk składają się z różnych typów pakietów i bajtów zamiast pakietów, a podstawowa konstrukcja nazw tagów w każdej metryce w następujący sposób:

- **Porzucona nazwa tagu** (na przykład **pakiety przychodzące opuszczone DDoS**): liczba pakietów porzuconych/wyczyszczonych przez system ochrony DDoS.
- **Nazwa tagu przekazanego** (na przykład **pakiety przychodzące przesyłane dalej DDoS**): liczba pakietów przesłanych przez system DDoS do docelowego adresu VIP — ruch, który nie został przefiltrowany.
- **Brak nazwy tagu** (na przykład **pakiety przychodzące DDoS**): całkowita liczba pakietów, które zostały dołączone do systemu kontroli, reprezentująca sumę pakietów porzuconych i przesłanych dalej.

Ta [reguła alertu Azure monitor](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20Monitor%20Alert%20-%20DDoS%20Mitigation%20Started) uruchomi proste zapytanie w celu wykrycia, kiedy występuje ograniczenie aktywnego DDoS. Aby zasymulować atak DDoS na potrzeby weryfikowania telemetrii, zobacz [Weryfikowanie DDoS Detection](test-through-simulations.md). 

## <a name="view-ddos-mitigation-policies"></a>Wyświetlanie zasad łagodzenia DDoS

DDoS Protection standard stosuje trzy zasady ograniczania z autodostrajania (TCP SYN, TCP & UDP) dla każdego publicznego adresu IP chronionego zasobu w sieci wirtualnej, w której włączono DDoS. Progi zasad można wyświetlić, wybierając  **przychodzące pakiety TCP, aby wyzwolić DDoS łagodzenie** i **przychodzące pakiety UDP do wyzwalania metryk ograniczenia DDoS** z typem **agregacji** jako "Max", jak pokazano na poniższej ilustracji:

![Wyświetlanie zasad ograniczenia](./media/manage-ddos-protection/view-mitigation-policies.png)

Progi zasad są konfigurowane przy użyciu profilowania sieci opartych na usłudze Azure Machine Learning. Tylko wtedy, gdy próg zasad został naruszony, nastąpi ograniczenie DDoS dla adresu IP w ramach ataku.

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Wyświetlanie alertów dotyczących ochrony DDoS w Azure Security Center

Azure Security Center zawiera listę [alertów zabezpieczeń](../security-center/security-center-managing-and-responding-alerts.md)z informacjami ułatwiającymi badanie i korygowanie problemów. Dzięki tej funkcji można uzyskać ujednolicony widok alertów, w tym alerty związane z atakiem DDoS oraz działania podejmowane w celu ograniczenia ataków w czasie prawie czasu.
Istnieją dwa konkretne alerty, które będą widoczne w przypadku wykrywania ataków DDoS i łagodzenia rozwiązań:

- **Wykryto atak DDoS dla publicznego adresu IP**: ten alert jest generowany, gdy Usługa ochrony DDoS wykryje, że jeden z publicznych adresów IP jest celem ataku DDoS.
- **Atak DDoS został skorygowany dla publicznego adresu IP**: ten alert jest generowany, gdy atak na publiczny adres IP został skorygowany.
Aby wyświetlić alerty, Otwórz **Security Center** w Azure Portal. W obszarze **Ochrona przed zagrożeniami** wybierz pozycję **alerty zabezpieczeń**. Poniższy zrzut ekranu przedstawia przykład alertów ataku DDoS.

![Alert DDoS w Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

Alerty obejmują ogólne informacje o publicznym adresie IP, który jest objęty atakiem, informacjami o analizie geograficznym i zagrożeń oraz kroki zaradcze.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

- Konfigurowanie alertów dotyczących metryk ochrony DDoS
- Korzystanie z telemetrii ochrony DDoS
- Wyświetlanie zasad łagodzenia DDoS
- Wyświetlanie alertów dotyczących ochrony DDoS w Azure Security Center

Aby dowiedzieć się, jak skonfigurować raporty łagodzenia ataków i dzienniki przepływów, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Konfigurowanie raportów ograniczenia skutków ataków DDoS i dzienników przepływów](reports-and-flow-logs.md)