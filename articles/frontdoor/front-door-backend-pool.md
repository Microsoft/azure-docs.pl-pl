---
title: Frontony i pule zaplecza na platformie Azure — drzwi Microsoft Docs
description: W tym artykule opisano, jakie zaplecze i pule zaplecza są zgodne z konfiguracją drzwi.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 4beba141fec7a819df52e4c3a669312a4ad76998
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449291"
---
# <a name="backends-and-backend-pools-in-azure-front-door"></a>Frontony i pule zaplecza na platformie Azure — drzwi
W tym artykule opisano zagadnienia dotyczące sposobu mapowania wdrożenia aplikacji sieci Web za pomocą drzwi platformy Azure. Wyjaśniono również różne terminologie używane w konfiguracji z drzwiami wstępnymi wokół frontonu aplikacji.

## <a name="backends"></a>Zaplecza
Zaplecze odwołuje się do wdrożenia aplikacji sieci Web w regionie. Drzwi z przodu obsługują zarówno zasoby platformy Azure, jak i spoza platformy Azure w puli zaplecza. Aplikacja może znajdować się w lokalnym centrum danych lub znajdować się w innym dostawcy chmury.

Tylne punkty końcowe odnoszą się do nazwy hosta lub publicznego adresu IP aplikacji, która obsługuje żądania klientów. Zastąp nie należy mylić z warstwą bazy danych, warstwą magazynowania i tak dalej. Zaplecze powinno być wyświetlane jako publiczny punkt końcowy dla zaplecza aplikacji. Po dodaniu zaplecza do puli zaplecza z drzwiami przednimi, należy również dodać następujące elementy:

- **Typ hosta zaplecza**. Typ zasobu, który ma zostać dodany. Drzwi tylne obsługują funkcję autowykrywania aplikacji zakończyłą się za pomocą usługi App Service, usługi w chmurze lub magazynu. Jeśli chcesz użyć innego zasobu na platformie Azure lub nawet z zapleczem innym niż Azure, wybierz opcję **host niestandardowy**.

    >[!IMPORTANT]
    >Podczas konfiguracji interfejsy API nie sprawdzają, czy zaplecze nie są dostępne w środowiskach z przodu. Upewnij się, że tylne drzwi mogą dotrzeć do zaplecza.

- **Nazwa subskrypcji i hosta zaplecza**. Jeśli nie wybrano **niestandardowego hosta** dla typu hosta zaplecza, wybierz zaplecze, wybierając odpowiednią subskrypcję i odpowiednią nazwę hosta zaplecza w interfejsie użytkownika.

- **Nagłówek hosta zaplecza**. Wartość nagłówka hosta wysyłana do zaplecza dla każdego żądania. Aby uzyskać więcej informacji, zobacz [nagłówek hosta zaplecza](#hostheader).

- **Priorytet**. Przypisywanie priorytetów do różnych zaplecza, gdy chcesz użyć podstawowego zaplecze usługi dla całego ruchu. Ponadto należy podać kopie zapasowe, jeśli podstawowy lub zapasowy frontonu są niedostępne. Aby uzyskać więcej informacji, zobacz [priorytet](front-door-routing-methods.md#priority).

- **Waga**. Przypisz wagi do różnych założonych punktów końcowych, aby dystrybuować ruch między zestawami założonymi (nawet lub zgodnie z współczynnikim wagi). Aby uzyskać więcej informacji, zobacz [wagi](front-door-routing-methods.md#weighted).

### <a name="backend-host-header"></a><a name = "hostheader"></a>Nagłówek hosta zaplecza

Żądania przekazywane przez tylne drzwi do zaplecza zawierają pole nagłówka hosta używane przez zaplecze do pobrania zasobu dostosowanego. Wartość tego pola zazwyczaj pochodzi z identyfikatora URI zaplecza, który ma nagłówek i port hosta.

Na przykład żądanie wykonane dla elementu `www.contoso.com` będzie miało nagłówek hosta www.contoso.com. Jeśli do skonfigurowania zaplecza używasz Azure Portal, wartością domyślną tego pola jest nazwa hosta zaplecza. Jeśli zaplecze to contoso-westus.azurewebsites.net, w Azure Portal wartość autowypełniana dla nagłówka hosta zaplecza będzie contoso-westus.azurewebsites.net. Jeśli jednak użyjesz szablonów Azure Resource Manager lub innej metody bez jawnego ustawienia tego pola, drzwi z przodu wyślą nazwę hosta przychodzącego jako wartość dla nagłówka hosta. Jeśli żądanie dotyczyło \. contoso.com www, a zaplecze jest contoso-westus.azurewebsites.NET, który ma puste pole nagłówka, drzwiczki z przodu spowodują ustawienie nagłówka hosta jako \. contoso.com www.

Większość zapleców aplikacji (Azure Web Apps, BLOB Storage i Cloud Services) wymaga, aby nagłówek hosta był zgodny z domeną zaplecza. Jednak Host frontonu, który jest przesyłany do zaplecza, będzie używać innej nazwy hosta, takiej jak www.contoso.net.

Jeśli zaplecze wymaga, aby nagłówek hosta był zgodny z nazwą hosta zaplecza, upewnij się, że nagłówek hosta zaplecza zawiera nazwę hosta zaplecza.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Konfigurowanie nagłówka hosta zaplecza dla zaplecza

Aby skonfigurować pole **nagłówka hosta zaplecza** dla zaplecza w sekcji Pula zaplecza:

1. Otwórz zasób z drzwiami wstępnymi i wybierz pulę zaplecza z zapleczem do skonfigurowania.

2. Dodaj zaplecze, jeśli nie zostało to zrobione, lub Edytuj istniejący.

3. W polu Nagłówek hosta wewnętrznej bazy danych ustaw wartość niestandardową lub pozostaw to pole puste. Nazwa hosta dla żądania przychodzącego będzie używana jako wartość nagłówka hosta.

## <a name="backend-pools"></a>Pule zaplecza
Pula zaplecza w drzwiach zewnętrznych odwołuje się do zestawu punktów końcowych, które odbierają podobny ruch dla swojej aplikacji. Innymi słowy, jest to logiczna Grupa wystąpień aplikacji na świecie, która odbiera ten sam ruch i reaguje z oczekiwanym zachowaniem. Te punkty końcowe są wdrażane w różnych regionach lub w tym samym regionie. Wszystkie aukcje załączone mogą być w trybie wdrożenia aktywny/aktywny lub co jest zdefiniowane jako Konfiguracja aktywna/pasywna.

Pula zaplecza definiuje, w jaki sposób różne zaplecza powinny być oceniane przez sondy kondycji. Definiuje także sposób równoważenia obciążenia między nimi.

### <a name="health-probes"></a>Sondy kondycji
Drzwi z przodu wysyłają okresowe żądania sondowania HTTP/HTTPS do każdego ze skonfigurowanych frontonów. Żądania sondowania określają bliskość i kondycję każdego zaplecza do równoważenia obciążenia żądaniami użytkowników końcowych. Ustawienia sondowania kondycji dla puli zaplecza definiują, w jaki sposób sondować stan kondycji zapleczy aplikacji. Dla konfiguracji równoważenia obciążenia dostępne są następujące ustawienia:

- **Path**: adres URL używany do żądania sondowania dla wszystkich zaplecza w puli wewnętrznej bazy danych. Na przykład jeśli jeden z zacontoso-westus.azurewebsites.netów jest ustawiony na wartość/Probe/test.aspx, a następnie w środowiskach czołowych, przy założeniu, że protokół jest ustawiony na HTTP, program wyśle żądania sondowania kondycji do protokołu HTTP \: //contoso-westus.azurewebsites.NET/Probe/test.aspx.

- **Protokół**: określa, czy wysyłać żądania sondy kondycji z czołowych drzwi do frontonu przy użyciu protokołu HTTP lub https.

- **Metoda**: Metoda http, która ma być używana do wysyłania sond kondycji. Opcje obejmują GET lub głowy (domyślnie).
    > [!NOTE]
    > W celu obniżenia obciążenia i ponoszenia kosztów na potrzeby punktów końcowych zaleca się używanie żądań głównych dla sond kondycji.

- **Interwał (w sekundach)**: określa częstotliwość sond kondycji do zakończenia lub interwałów, w których każde ze środowisk przed drzwiami wysyła sondę.

    >[!NOTE]
    >Aby przyspieszyć przełączanie w tryb failover, ustaw wartość interwał na niższą. Im niższa wartość, tym wyższego natężenia głośności sondy kondycji. Na przykład, jeśli interwał jest ustawiony na 30 sekund z wypowiedzią, 100 czołowe drzwi są globalnie, każde zaplecze otrzyma około 200 żądań sondowania na minutę.

Aby uzyskać więcej informacji, zobacz [sondy kondycji](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Ustawienia równoważenia obciążenia
Ustawienia równoważenia obciążenia dla puli zaplecza definiują, jak oceniane są sondy kondycji. Te ustawienia określają, czy zaplecze jest w dobrej kondycji. Sprawdzają także, jak równoważyć obciążenie ruchu między różnymi zadziałami w puli zaplecza. Dla konfiguracji równoważenia obciążenia dostępne są następujące ustawienia:

- **Rozmiar próbki**. Identyfikuje liczbę próbek sond kondycji, które należy wziąć pod uwagę w przypadku oceny kondycji zaplecza.

- **Pomyślny rozmiar próbki**. Definiuje rozmiar próbki, jak wspomniano powyżej, liczbę pomyślnych próbek wymaganych do wywołania zaplecza w dobrej kondycji. Załóżmy na przykład, że interwał sondy kondycji z przodu wynosi 30 sekund, rozmiar próbki wynosi 5, a rozmiar próbki powiódł się 3. Za każdym razem, gdy analizujemy sondy kondycji dla zaplecza, analizujemy pięć ostatnich próbek powyżej 150 sekund (5 x 30). Co najmniej trzy pomyślne sondy są wymagane do zadeklarować zaplecze jako dobra kondycja.

- **Czułość opóźnienia (dodatkowe opóźnienie)**. Określa, czy chcesz, aby drzwi frontonu wysyłali żądanie do zaplecza w ramach zakresu czułości pomiaru opóźnienia, czy też przesłać żądanie do najbliższej wewnętrznej bazy danych.

Aby uzyskać więcej informacji, zobacz [najmniej metody routingu opartego na opóźnieniu](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Następne kroki

- [Tworzenie profilu frontu drzwi](quickstart-create-front-door.md)
- [Jak działają tylne drzwi](front-door-routing-architecture.md)
