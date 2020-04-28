---
title: PRZESTARZAŁE Włącz dostęp do aplikacji kontenera DC/OS platformy Azure
description: Jak włączyć publiczny dostęp do kontenerów DC/OS w Azure Container Service.
services: container-service
author: sauryadas
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3e4ba15fa1925ca40ad7760acbd14331fbdd1343
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "61457388"
---
# <a name="deprecated-enable-public-access-to-an-azure-container-service-application"></a>PRZESTARZAŁE Włącz publiczny dostęp do aplikacji Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Każdy kontener DC/OS w [puli agentów publicznych](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) ACS jest automatycznie ujawniany w Internecie. Domyślnie porty **80**, **443**, **8080** są otwarte, a wszystkie (publiczne) kontenery nasłuchu na tych portach są dostępne. W tym artykule pokazano, jak otworzyć więcej portów dla aplikacji w Azure Container Service.

## <a name="open-a-port-portal"></a>Otwórz port (Portal)
Najpierw musimy otworzyć żądany port.

1. Zaloguj się do portalu.
2. Znajdź grupę zasobów, do której wdrożono Azure Container Service.
3. Wybierz moduł równoważenia obciążenia agenta (o nazwie podobnej do **xxxx-Agent-lb-xxxx**).
   
    ![Moduł równoważenia obciążenia usługi kontenera platformy Azure](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Kliknij pozycję **sondy** , a następnie **Dodaj**.
   
    ![Sondy modułu równoważenia obciążenia usługi kontenera platformy Azure](./media/container-service-enable-public-access/add-probe.png)
5. Wypełnij formularz sondy i kliknij przycisk **OK**.
   
   | Pole | Opis |
   | --- | --- |
   | Nazwa |Opisowa nazwa sondy. |
   | Port |Port kontenera do przetestowania. |
   | Ścieżka |(W trybie HTTP) Względna ścieżka witryny sieci Web do sondowania. HTTPS nie jest obsługiwany. |
   | Interval |Czas między próbami sondowania (w sekundach). |
   | Próg złej kondycji |Liczba kolejnych prób sondy przed uwzględnieniem złej kondycji kontenera. |
6. Z powrotem we właściwościach modułu równoważenia obciążenia agenta kliknij pozycję **reguły równoważenia obciążenia** , a następnie **Dodaj**.
   
    ![Reguły modułu równoważenia obciążenia usługi kontenera platformy Azure](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Wypełnij formularz modułu równoważenia obciążenia i kliknij przycisk **OK**.
   
   | Pole | Opis |
   | --- | --- |
   | Nazwa |Opisowa nazwa modułu równoważenia obciążenia. |
   | Port |Publiczny port przychodzący. |
   | Port zaplecza |Wewnętrzny port publiczny kontenera, do którego ma być kierowany ruch. |
   | Pula zaplecza |Kontenery w tej puli będą elementem docelowym dla tego modułu równoważenia obciążenia. |
   | Sonda |Sonda użyta do określenia, czy obiekt docelowy w **puli zaplecza** jest w dobrej kondycji. |
   | Trwałość sesji |Określa, w jaki sposób ruch z klienta powinien być obsługiwany przez czas trwania sesji.<br><br>**Brak**: kolejne żądania z tego samego klienta mogą być obsługiwane przez dowolny kontener.<br>**Adres IP klienta**: kolejne żądania z tego samego adresu IP klienta są obsługiwane przez ten sam kontener.<br>**Adres IP klienta i protokół**: kolejne żądania z tej samej kombinacji adresów IP i protokołów klienta są obsługiwane przez ten sam kontener. |
   | Limit czasu bezczynności |(Tylko protokół TCP) W minutach czas na utrzymanie klienta TCP/HTTP jest otwarty bez polegania na komunikatach *Keep-Alive* . |

## <a name="add-a-security-rule-portal"></a>Dodawanie reguły zabezpieczeń (Portal)
Następnie musimy dodać regułę zabezpieczeń, która kieruje ruch z otwartego portu przez zaporę.

1. Zaloguj się do portalu.
2. Znajdź grupę zasobów, do której wdrożono Azure Container Service.
3. Wybierz **publiczną** grupę zabezpieczeń agenta (o nazwie podobnej do **xxxx-Agent-Public-sieciowej grupy zabezpieczeń-xxxx**).
   
    ![Grupa zabezpieczeń sieci usługi kontenera platformy Azure](./media/container-service-enable-public-access/agent-nsg.png)
4. Wybierz pozycję **reguły zabezpieczeń dla ruchu przychodzącego** , a następnie **Dodaj**.
   
    ![Reguły sieciowej grupy zabezpieczeń usługi kontenera platformy Azure](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Wypełnij regułę zapory, aby zezwolić na port publiczny, i kliknij przycisk **OK**.
   
   | Pole | Opis |
   | --- | --- |
   | Nazwa |Opisowa nazwa reguły zapory. |
   | Priorytet |Ranga priorytetu dla reguły. Im niższa wartość, tym wyższy priorytet. |
   | Element źródłowy |Ogranicz zakres przychodzących adresów IP do dozwolonych lub zabronionych przez tę regułę. Użyj **dowolnego** , aby nie określać ograniczenia. |
   | Usługa |Wybierz zestaw wstępnie zdefiniowanych usług, dla których jest stosowana ta reguła zabezpieczeń. W przeciwnym razie użyj **niestandardowych** , aby utworzyć własne. |
   | Protocol (Protokół) |Ogranicz ruch na podstawie **protokołu TCP** lub **UDP**. Użyj **dowolnego** , aby nie określać ograniczenia. |
   | Zakres portów |Gdy **Usługa** jest **niestandardowa**, określa zakres portów, na które ma wpływ ta reguła. Można użyć jednego portu, takiego jak **80**, lub zakresu, takiego jak **1024-1500**. |
   | Akcja |Zezwalaj lub Odmawiaj ruchu spełniający kryteria. |

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o różnicach między [publicznymi i prywatnymi agentami DC/OS](container-service-dcos-agents.md).

Przeczytaj więcej informacji [na temat zarządzania kontenerami DC/OS](container-service-mesos-marathon-ui.md).

