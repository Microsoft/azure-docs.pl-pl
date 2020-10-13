---
title: Tworzenie niestandardowej sondy przy użyciu portalu
titleSuffix: Azure Application Gateway
description: Dowiedz się, jak utworzyć niestandardową sondę dla Application Gateway przy użyciu portalu
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 5dc8bf670e14d8a44b10b8093d786091791ae793
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186801"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Tworzenie niestandardowej sondy dla Application Gateway przy użyciu portalu

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager — program PowerShell](application-gateway-create-probe-ps.md)
> * [Klasyczny portal Azure — program PowerShell](application-gateway-create-probe-classic-ps.md)

W tym artykule opisano Dodawanie niestandardowej sondy kondycji do istniejącej bramy aplikacji za pomocą Azure Portal. Korzystając z sond kondycji, platforma Azure Application Gateway monitoruje kondycję zasobów w puli zaplecza.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Jeśli nie masz jeszcze bramy aplikacji, odwiedź stronę [tworzenie Application Gateway](application-gateway-create-gateway-portal.md) , aby utworzyć bramę aplikacji do pracy.

## <a name="create-probe-for-application-gateway-v2-sku"></a>Utwórz sondę dla jednostki SKU Application Gateway v2

Sondy są konfigurowane w procesie dwuetapowym za pomocą portalu. Pierwszym krokiem jest wprowadzenie wartości wymaganych do konfiguracji sondy. Drugi krok polega na przetestowaniu kondycji zaplecza przy użyciu tej konfiguracji sondowania i zapisaniu sondy. 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>Wprowadzanie właściwości sondy

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Jeśli nie masz jeszcze konta, możesz zarejestrować się w celu uzyskania [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/free)

2. W okienku Ulubione witryny Azure Portal kliknij pozycję Wszystkie zasoby. Kliknij pozycję Brama aplikacji w bloku wszystkie zasoby. Jeśli wybrana subskrypcja zawiera kilka zasobów, możesz wpisać partners.contoso.net w polu Filtruj według nazwy..., aby łatwo uzyskać dostęp do bramy aplikacji.

3. Wybierz pozycję **sondy kondycji** , a następnie wybierz pozycję **Dodaj** , aby dodać nową sondę kondycji.

   ![Dodaj nową sondę][4]

4. Na stronie **Dodawanie sondy kondycji** wprowadź wymagane informacje dotyczące sondy, a po zakończeniu wybierz **przycisk OK**.

   |**Ustawienie** | **Wartość** | **Szczegóły**|
   |---|---|---|
   |**Nazwa**|customProbe|Ta wartość jest przyjazną nazwą dla sondy, która jest dostępna w portalu.|
   |**Protokół**|HTTP lub HTTPS | Protokół używany przez sondę kondycji. |
   |**Host**|co contoso.com|Ta wartość jest nazwą hosta wirtualnego (inną niż nazwa hosta maszyny wirtualnej) działającą na serwerze aplikacji. Sonda jest wysyłana do \<protocol\> :// \<host name\> :\<port\>/\<urlPath\>|
   |**Wybierz nazwę hosta z ustawień protokołu HTTP zaplecza**|Tak lub Nie|Ustawia nagłówek *hosta* w sondie na nazwę hosta z ustawień http, z którym jest skojarzona ta sonda. Specjalne wymaganie w przypadku wielodostępnych punktów końcowych, takich jak usługa Azure App Service. [Dowiedz się więcej](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**Wybierz port z ustawień protokołu HTTP zaplecza**| Tak lub Nie|Ustawia *port* sondy kondycji na port z ustawień http, z którymi jest skojarzona ta sonda. Jeśli wybierzesz opcję nie, możesz wprowadzić niestandardowy port docelowy do użycia. |
   |**Port**| 1-65535 | Niestandardowy port do użycia w sondach kondycji | 
   |**Ścieżka**|lub dowolna prawidłowa ścieżka|Pozostała część pełnego adresu URL dla sondy niestandardowej. Prawidłowa ścieżka zaczyna się od znaku "/". Dla domyślnej ścieżki http: \/ /contoso.com samo użycie "/" |
   |**Interwał (s)**|30|Częstotliwość uruchamiania sondy w celu sprawdzenia kondycji. Nie zaleca się ustawiania wartości mniejszej niż 30 sekund.|
   |**Limit czasu (w sekundach)**|30|Czas, przez jaki sonda czeka przed upływem limitu czasu. Jeśli prawidłowa odpowiedź nie zostanie odebrana w tym okresie, sonda zostanie oznaczona jako niepowodzenie. Interwał limitu czasu musi być wystarczająco duży, aby można było wykonać wywołanie http, aby zapewnić dostępność strony kondycji zaplecza. Należy zauważyć, że wartość limitu czasu nie powinna być większa niż wartość "Interval" użyta w tym ustawieniu sondy lub wartość "Request timeout" w ustawieniach HTTP, która zostanie skojarzona z tą sondą.|
   |**Próg złej kondycji**|3|Liczba kolejnych nieudanych prób w złej kondycji. Próg można ustawić na wartość 1 lub większą.|
   |**Użyj warunków dopasowywania sondy**|Tak lub Nie|Domyślnie odpowiedź HTTP (S) o kodzie stanu z zakresu od 200 do 399 jest traktowana w dobrej kondycji. Można zmienić akceptowalny zakres kodu odpowiedzi zaplecza lub treści odpowiedzi zaplecza. [Dowiedz się więcej](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|
   |**Ustawienia protokołu HTTP**|wybór z listy rozwijanej|Sonda zostanie skojarzona z ustawieniami protokołu HTTP wybranymi tutaj i w związku z tym będzie monitorować kondycję tej puli zaplecza, która jest skojarzona z wybranym ustawieniem protokołu HTTP. Użyje tego samego portu dla żądania sondowania jako, który jest używany w wybranym ustawieniu protokołu HTTP. Można wybrać tylko te ustawienia protokołu HTTP, które nie są skojarzone z żadną inną sondą niestandardową. <br>Należy zauważyć, że tylko te ustawienia protokołu HTTP są dostępne dla skojarzenia, które mają ten sam protokół, co protokół wybrany w tej konfiguracji sondowania i mają ten sam stan dla *nazwy wybranego hosta z przełącznika ustawienia http zaplecza* .|
   
   > [!IMPORTANT]
   > Sonda będzie monitorować kondycję zaplecza tylko wtedy, gdy jest skojarzona z co najmniej jednym ustawieniem HTTP. Będzie monitorować zasoby zaplecza tych pul zaplecza, które są skojarzone z ustawieniami HTTP, z którymi jest skojarzona ta sonda. Żądanie sondowania zostanie wysłane jako \<protocol\> :// \<hostName\> : \<port\> / \<urlPath\> .

### <a name="test-backend-health-with-the-probe"></a>Testowanie kondycji zaplecza za pomocą sondy

Po wprowadzeniu właściwości sondy można sprawdzić kondycję zasobów zaplecza, aby sprawdzić, czy konfiguracja sondy jest poprawna i czy zasoby zaplecza działają zgodnie z oczekiwaniami.

1. Wybierz **test** i zanotuj wynik sondy. Brama aplikacji testuje kondycję wszystkich zasobów zaplecza w pulach zaplecza skojarzonych z ustawieniami protokołu HTTP używanymi dla tej sondy. 

   ![Kondycja zaplecza testowego][5]

2. Jeśli istnieją jakieś zasoby zaplecza w złej kondycji, Sprawdź kolumnę **szczegóły** , aby poznać przyczynę nieprawidłowego stanu zasobu. Jeśli zasób został oznaczony jako w złej kondycji z powodu nieprawidłowej konfiguracji sondowania, wybierz link Wróć **do sondy** i edytuj konfigurację sondy. W przeciwnym razie, jeśli zasób został oznaczony jako w złej kondycji ze względu na problem z zapleczem, Rozwiąż problemy z zasobem zaplecza, a następnie ponownie przetestuj zaplecza, wybierając link Wróć **do sondy** i wybierz pozycję **Testuj**.

   > [!NOTE]
   > Możesz zapisać sondę nawet w przypadku zasobów zaplecza w złej kondycji, ale nie jest to zalecane. Wynika to z faktu, że Application Gateway nie przekaże żądań do serwerów zaplecza z puli zaplecza, które zostały uznane za niezdrowe przez sondę. Jeśli nie ma żadnych prawidłowych zasobów w puli zaplecza, nie będzie można uzyskać dostępu do aplikacji i zostanie zwrócony błąd HTTP 502.

   ![Wyświetl wynik sondy][6]

3. Wybierz pozycję **Dodaj** , aby zapisać sondę. 

## <a name="create-probe-for-application-gateway-v1-sku"></a>Utwórz sondę dla jednostki SKU Application Gateway v1

Sondy są konfigurowane w procesie dwuetapowym za pomocą portalu. Pierwszym krokiem jest utworzenie sondy. W drugim kroku należy dodać sondę do ustawień protokołu HTTP zaplecza bramy aplikacji.

### <a name="create-the-probe"></a><a name="createprobe"></a>Tworzenie sondy

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Jeśli nie masz jeszcze konta, możesz zarejestrować się w celu uzyskania [bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/free)

2. W okienku Ulubione witryny Azure Portal wybierz pozycję **Wszystkie zasoby**. Na stronie **wszystkie zasoby** Wybierz bramę aplikacji. Jeśli wybrana subskrypcja zawiera kilka zasobów, możesz wpisać partners.contoso.net w polu Filtruj według nazwy..., aby łatwo uzyskać dostęp do bramy aplikacji.

3. Wybierz pozycję **sondy** , a następnie wybierz pozycję **Dodaj** , aby dodać sondę.

   ![Dodaj blok sondy z informacjami wypełnionymi][1]

4. W bloku **Dodawanie sondy kondycji** wprowadź wymagane informacje dotyczące sondy, a po zakończeniu wybierz **przycisk OK**.

   |**Ustawienie** | **Wartość** | **Szczegóły**|
   |---|---|---|
   |**Nazwa**|customProbe|Ta wartość jest przyjazną nazwą dla sondy, która jest dostępna w portalu.|
   |**Protokół**|HTTP lub HTTPS | Protokół używany przez sondę kondycji. |
   |**Host**|co contoso.com|Ta wartość jest nazwą hosta wirtualnego (inną niż nazwa hosta maszyny wirtualnej) działającą na serwerze aplikacji. Sonda jest wysyłana do (protokół)://(nazwa hosta):(port z httpsetting)/urlPath.  Ma to zastosowanie w przypadku, gdy na Application Gateway jest skonfigurowana wiele witryn. Jeśli Application Gateway jest skonfigurowany dla jednej lokacji, wprowadź wartość "127.0.0.1".|
   |**Wybierz nazwę hosta z ustawień protokołu HTTP zaplecza**|Tak lub Nie|Ustawia nagłówek *hosta* w sondie na nazwę hosta zasobu zaplecza w puli zaplecza skojarzoną z USTAWIENIEm http, z którym jest skojarzona ta sonda. Specjalne wymaganie w przypadku wielodostępnych punktów końcowych, takich jak usługa Azure App Service. [Dowiedz się więcej](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**Ścieżka**|lub dowolna prawidłowa ścieżka|Pozostała część pełnego adresu URL dla sondy niestandardowej. Prawidłowa ścieżka zaczyna się od znaku "/". Dla domyślnej ścieżki http: \/ /contoso.com samo użycie "/" |
   |**Interwał (s)**|30|Częstotliwość uruchamiania sondy w celu sprawdzenia kondycji. Nie zaleca się ustawiania wartości mniejszej niż 30 sekund.|
   |**Limit czasu (w sekundach)**|30|Czas, przez jaki sonda czeka przed upływem limitu czasu. Jeśli prawidłowa odpowiedź nie zostanie odebrana w tym okresie, sonda zostanie oznaczona jako niepowodzenie. Interwał limitu czasu musi być wystarczająco duży, aby można było wykonać wywołanie http, aby zapewnić dostępność strony kondycji zaplecza. Należy zauważyć, że wartość limitu czasu nie powinna być większa niż wartość "Interval" użyta w tym ustawieniu sondy lub wartość "Request timeout" w ustawieniach HTTP, która zostanie skojarzona z tą sondą.|
   |**Próg złej kondycji**|3|Liczba kolejnych nieudanych prób w złej kondycji. Próg można ustawić na wartość 1 lub większą.|
   |**Użyj warunków dopasowywania sondy**|Tak lub Nie|Domyślnie odpowiedź HTTP (S) o kodzie stanu z zakresu od 200 do 399 jest traktowana w dobrej kondycji. Można zmienić akceptowalny zakres kodu odpowiedzi zaplecza lub treści odpowiedzi zaplecza. [Dowiedz się więcej](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|

   > [!IMPORTANT]
   > Nazwa hosta nie jest taka sama jak nazwa serwera. Ta wartość jest nazwą hosta wirtualnego uruchomionego na serwerze aplikacji. Sonda jest wysyłana do \<protocol\> :// \<hostName\> :\<port from http settings\>/\<urlPath\>

### <a name="add-probe-to-the-gateway"></a>Dodawanie sondy do bramy

Teraz, gdy sonda została utworzona, należy ją dodać do bramy. Ustawienia sondy są ustawiane w ustawieniach protokołu HTTP zaplecza bramy aplikacji.

1. Kliknij pozycję **Ustawienia http** w bramie aplikacji, aby wyświetlić blok konfiguracja, a następnie kliknij bieżące ustawienia protokołu HTTP zaplecza wymienione w oknie.

   ![okno ustawień https][2]

2. Na stronie ustawienia **appGatewayBackEndHttpSettings** zaznacz pole wyboru **Użyj sondy niestandardowej** i wybierz sondę utworzoną w sekcji [Tworzenie sondy](#createprobe) na liście rozwijanej **sonda niestandardowa** .
   Po zakończeniu kliknij przycisk **Zapisz** , a ustawienia zostaną zastosowane.

## <a name="next-steps"></a>Następne kroki

Wyświetlanie kondycji zasobów zaplecza określonych przez sondę przy użyciu [widoku kondycja zaplecza](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health).

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
