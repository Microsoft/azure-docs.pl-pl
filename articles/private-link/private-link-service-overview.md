---
title: Co to jest usługa Azure Private Link Service?
description: Dowiedz się więcej o usłudze Azure Private Link Service.
services: private-link
author: sumeetmittal
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: sumi
ms.openlocfilehash: a6bbb2abe24eba96fd2c55b7aaf15ccd8ae33530
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87760955"
---
# <a name="what-is-azure-private-link-service"></a>Co to jest usługa Azure Private Link Service?

Usługa link prywatny platformy Azure to odwołanie do własnej usługi, która jest obsługiwana przez link prywatny platformy Azure. Usługa, która jest uruchomiona za [Usługa Load Balancer w warstwie Standardowa platformy Azure](../load-balancer/load-balancer-standard-overview.md) , może być włączona do prywatnego dostępu do łączy, dzięki czemu konsumenci usługi mogą uzyskać do nich dostęp prywatnie z własnych sieci wirtualnych. Klienci mogą utworzyć prywatny punkt końcowy wewnątrz swojej sieci wirtualnej i zmapować ją na tę usługę. W tym artykule objaśniono koncepcje związane z dostawcą usług. 

:::image type="content" source="./media/private-link-service-overview/consumer-provider-endpoint.png" alt-text="Przepływ pracy usługi link prywatny" border="true":::

*Rysunek: usługa prywatnego połączenia platformy Azure.*

## <a name="workflow"></a>Przepływ pracy

![Przepływ pracy usługi link prywatny](media/private-link-service-overview/private-link-service-workflow.png)


*Rysunek: przepływ pracy usługi Azure Private Link Service.*

### <a name="create-your-private-link-service"></a>Tworzenie usługi linku prywatnego

- Skonfiguruj aplikację do uruchamiania za pomocą usługi równoważenia obciążenia w sieci wirtualnej. Jeśli masz już aplikację skonfigurowaną za pomocą usługi równoważenia obciążenia, możesz pominąć ten krok.   
- Utwórz usługę linku prywatnego, która odwołuje się do powyższego modułu równoważenia obciążenia. W procesie wyboru modułu równoważenia obciążenia wybierz konfigurację adresu IP frontonu, w której chcesz odebrać ruch. Wybierz podsieć dla adresów IP translatora adresów sieciowych dla usługi linku prywatnego. Zaleca się, aby w podsieci były dostępne co najmniej osiem adresów IP translatora adresów sieciowych. Cały ruch konsumencki będzie wyglądał jako pochodzący z tej puli prywatnych adresów IP do dostawcy usług. Wybierz odpowiednie właściwości/ustawienia dla usługi łącza prywatnego.    

    > [!NOTE]
    > Usługa Azure Private link jest obsługiwana tylko w usługa Load Balancer w warstwie Standardowa. 
    
### <a name="share-your-service"></a>Udostępnianie usługi

Po utworzeniu usługi linku prywatnego platforma Azure wygeneruje globalnie unikatową nazwę monikera o nazwie "alias" na podstawie nazwy podanej dla usługi. Można udostępnić alias lub identyfikator URI zasobu usługi swoim klientom w trybie offline. Konsumenci mogą uruchomić połączenie prywatne za pomocą aliasu lub identyfikatora URI zasobu.
 
### <a name="manage-your-connection-requests"></a>Zarządzanie żądaniami połączeń

Po zainicjowaniu połączenia przez odbiorcę dostawca usług może zaakceptować lub odrzucić żądanie połączenia. Wszystkie żądania połączeń będą wyświetlane w obszarze właściwości **privateendpointconnections** w usłudze link prywatny.
 
### <a name="delete-your-service"></a>Usuwanie usługi

Jeśli usługa link prywatny nie jest już używana, możesz ją usunąć. Jednak przed usunięciem usługi upewnij się, że nie ma skojarzonych z nimi połączeń prywatnych punktów końcowych. Możesz odrzucić wszystkie połączenia i usunąć usługę.

## <a name="properties"></a>Właściwości

Usługa link prywatny określa następujące właściwości: 

|Właściwość |Wyjaśnienie  |
|---------|---------|
|Stan aprowizacji (provisioningState)  |Właściwość tylko do odczytu, która wyświetla bieżący stan aprowizacji dla usługi linku prywatnego. Odpowiednie Stany aprowizacji: "Usuwanie; Awarii Powiodło się Aktualizowanie ". Gdy stan aprowizacji to "powodzenie", pomyślnie Zainicjowano obsługę linku prywatnego.        |
|Alias (alias)     | Alias jest globalnie unikatowym ciągiem tylko do odczytu dla usługi. Ułatwia on maskowanie danych klienta usługi i w tym samym czasie tworzy łatwą do udostępnienia nazwę usługi. Podczas tworzenia usługi linku prywatnego platforma Azure generuje alias dla usługi, który można udostępnić klientom. Klienci mogą używać tego aliasu, aby zażądać połączenia z usługą.          |
|Widoczność (widoczność)     | Widoczność to właściwość, która kontroluje ustawienia ekspozycji usługi link prywatny. Dostawcy usług mogą zdecydować się na ograniczenie podatności usługi na subskrypcje z uprawnieniami kontroli dostępu opartej na rolach (RBAC), ograniczonym zestawem subskrypcji lub wszystkimi subskrypcjami platformy Azure.          |
|Autozatwierdzanie (autozatwierdzanie)    |   Automatyczne zatwierdzanie kontroluje zautomatyzowany dostęp do usługi łącza prywatnego. Subskrypcje określone na liście automatycznego zatwierdzania są zatwierdzane automatycznie po zażądaniu połączenia od prywatnych punktów końcowych w tych subskrypcjach.          |
|Konfiguracja adresu IP frontonu Load Balancer (loadBalancerFrontendIpConfigurations)    |    Usługa link prywatny jest powiązana z adresem IP frontonu usługa Load Balancer w warstwie Standardowa. Cały ruch przychodzący do usługi będzie docierał do frontonu modułu równoważenia obciążenia. Można skonfigurować reguły modułu równoważenia obciążenia, aby skierować ten ruch do odpowiednich pul zaplecza, w których są uruchomione aplikacje. Konfiguracje adresów IP frontonu modułu równoważenia obciążenia różnią się od konfiguracji protokołu IP NAT.      |
|Konfiguracja protokołu IP translatora adresów sieciowych (Ipconfiguration)    |    Ta właściwość odnosi się do konfiguracji protokołu IP NAT (translator adresów sieciowych) dla usługi link prywatny. Adres IP NAT można wybrać z dowolnej podsieci w sieci wirtualnej dostawcy usług. Usługa link prywatny Wykonuje translację NAT po stronie docelowej na ruch związany z linkiem prywatnym. Dzięki temu nie występuje konflikt adresów IP między źródłem (po stronie klienta) a miejscem docelowym (dostawcy usług). Po stronie docelowej (po stronie dostawcy usług) adres IP translatora adresów sieciowych będzie wyświetlany jako źródłowy IP dla wszystkich pakietów odebranych przez usługę i docelowy adres IP dla wszystkich pakietów wysłanych przez usługę.       |
|Połączenia prywatnych punktów końcowych (privateEndpointConnections)     |  Ta właściwość zawiera listę prywatnych punktów końcowych łączących się z usługą link prywatny. Wiele prywatnych punktów końcowych może połączyć się z tą samą usługą łącza prywatnego, a dostawca usług może kontrolować stan poszczególnych prywatnych punktów końcowych.        |
|Serwer proxy TCP v2 (EnableProxyProtocol)     |  Ta właściwość umożliwia dostawcy usług użycie protokołu TCP proxy v2 do pobrania informacji o połączeniu z klientem usługi. Dostawca usług jest odpowiedzialny za konfigurowanie konfiguracji odbiorników, aby mogli analizować nagłówek protokołu proxy w wersji 2.        |
|||


### <a name="details"></a>Szczegóły

- Dostęp do usługi link prywatny można uzyskać z zatwierdzonych prywatnych punktów końcowych w dowolnym regionie publicznym. Prywatny punkt końcowy może być osiągalny z tej samej sieci wirtualnej, z regionalnie równorzędną sieci wirtualnych, globalnie równorzędną sieci wirtualnych i lokalnie przy użyciu prywatnych połączeń sieci VPN lub ExpressRoute. 
 
- Podczas tworzenia usługi linku prywatnego tworzony jest interfejs sieciowy dla cyklu życia zasobu. Ten interfejs nie jest zarządzany przez klienta.
 
- Usługa link prywatny musi być wdrożona w tym samym regionie, w którym znajduje się sieć wirtualna i usługa Load Balancer w warstwie Standardowa.  
 
- Dostęp do pojedynczej usługi linku prywatnego można uzyskać z wielu prywatnych punktów końcowych należących do różnych sieci wirtualnych, subskrypcji i/lub Active Directory dzierżawców. Połączenie jest nawiązywane za pomocą przepływu pracy połączenia. 
 
- Na tym samym usługa Load Balancer w warstwie Standardowa można utworzyć wiele usług łączy prywatnych przy użyciu różnych konfiguracji adresu IP frontonu. Istnieją limity dotyczące liczby prywatnych usług linków, które można utworzyć na usługa Load Balancer w warstwie Standardowa i na subskrypcję. Aby uzyskać szczegółowe informacje, zobacz [limity platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).
 
- Z usługą linku prywatnego może być połączona więcej niż jedna konfiguracja adresu IP NAT. Wybranie więcej niż jednej konfiguracji protokołu IP NAT może ułatwić dostawcom usług skalowanie. Obecnie dostawcy usług mogą przypisywać maksymalnie osiem adresów IP translatora adresów sieciowych na usługę łącza prywatnego. Każdy adres IP translatora adresów sieciowych umożliwia przypisanie więcej portów dla połączeń TCP, a tym samym skalowanie w poziomie. Po dodaniu wielu adresów IP translatora adresów sieciowych do usługi link prywatny nie można usunąć adresów IP translatora adresów sieciowych. Jest to wykonywane w celu upewnienia się, że podczas usuwania adresów IP NAT nie wpłynie to na aktywne połączenia.


## <a name="alias"></a>Alias

**Alias** jest globalnie unikatową nazwą usługi. Ułatwia on maskowanie danych klienta usługi i w tym samym czasie tworzy łatwą do udostępnienia nazwę usługi. Podczas tworzenia usługi linku prywatnego platforma Azure generuje alias dla usługi, który można udostępnić klientom. Klienci mogą używać tego aliasu, aby zażądać połączenia z usługą.

Alias składa się z trzech części: *prefiks*. *Identyfikator GUID*. *Sufiks*

- Prefiks jest nazwą usługi. Możesz wybrać swój prefiks. Po utworzeniu "aliasu" nie można go zmienić, dlatego należy odpowiednio zaznaczyć swój prefiks.  
- Identyfikator GUID zostanie dostarczony przez platformę. Dzięki temu nazwa powinna być globalnie unikatowa. 
- Sufiks jest dołączany przez platformę Azure: *region*. Azure. privatelinkservice 

Kompletny alias:  *prefiks*. {GUID}. *region*. Azure. privatelinkservice  

## <a name="control-service-exposure"></a>Kontrola ekspozycji usługi

Usługa link prywatny udostępnia opcje umożliwiające sterowanie ekspozycją usługi za pomocą ustawienia "widoczność". Usługa może być prywatna do użycia przez różne sieci wirtualnych (tylko uprawnienia RBAC), ograniczać ekspozycję do ograniczonego zestawu zaufanych subskrypcji lub udostępniać je publicznie, aby wszystkie subskrypcje platformy Azure mogły żądać połączeń w usłudze linku prywatnego. Ustawienia widoczności decydują o tym, czy konsument może połączyć się z usługą. 

## <a name="control-service-access"></a>Kontrola dostępu do usługi

Konsumenci mający wpływ (kontrolowany przez ustawienie widoczności) do usługi linku prywatnego mogą utworzyć prywatny punkt końcowy w swoich sieci wirtualnych i zażądać połączenia z usługą linku prywatnego. Połączenie prywatnego punktu końcowego zostanie utworzone w stanie "oczekiwanie" w obiekcie usługi linku prywatnego. Dostawca usług jest odpowiedzialny za działanie w ramach żądania połączenia. Możesz zatwierdzić połączenie, odrzucić połączenie lub usunąć połączenie. Tylko zatwierdzone połączenia mogą wysyłać ruch do usługi łącza prywatnego.

Akcję zatwierdzania połączeń można zautomatyzować za pomocą właściwości automatycznego zatwierdzania w usłudze link prywatny. Automatyczne zatwierdzanie umożliwia dostawcom usług wstępne zatwierdzenie zestawu subskrypcji w celu automatycznego dostępu do ich usługi. Klienci będą musieli udostępnić swoje subskrypcje w trybie offline dla dostawców usług, aby dodać je do listy autozatwierdzania. Autozatwierdzanie jest podzbiorem tablicy widoczności. Widoczność kontroluje ustawienia ekspozycji, podczas gdy autozatwierdzanie kontroluje ustawienia zatwierdzenia dla usługi. Jeśli klient zażąda połączenia z subskrypcji na liście automatycznego zatwierdzania, połączenie zostanie automatycznie zatwierdzone i zostanie nawiązane połączenie. Dostawcy usług nie muszą już ręcznie zatwierdzać żądania. Z drugiej strony, jeśli klient zażąda połączenia z subskrypcji w tablicy widoczność, a nie w tablicy autozatwierdzania, żądanie zostanie wysłane do dostawcy usługi, ale dostawca usług musi ręcznie zatwierdzić połączenia.

## <a name="getting-connection-information-using-tcp-proxy-v2"></a>Uzyskiwanie informacji o połączeniu przy użyciu serwera proxy TCP v2

W przypadku korzystania z usługi link prywatny źródłowy adres IP pakietów pochodzących z prywatnego punktu końcowego jest tłumaczony przez adres sieciowy (NAT) po stronie dostawcy usługi przy użyciu adresu IP NAT przydzielony z sieci wirtualnej dostawcy. W związku z tym aplikacje otrzymują przypisany adres IP translatora adresów sieciowych zamiast rzeczywistego źródłowego adresu IP konsumentów usługi. Jeśli aplikacja wymaga rzeczywistego źródłowego adresu IP po stronie klienta, możesz włączyć protokół proxy w usłudze i pobrać informacje z nagłówka protokołu proxy. Oprócz źródłowego adresu IP nagłówek protokołu proxy również zawiera LinkID prywatnego punktu końcowego. Kombinacja źródłowych adresów IP i LinkID może pomóc dostawcom usług w unikatowym identyfikowaniu swoich klientów. Więcej informacji o protokole proxy można znaleźć [tutaj](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt). 

Te informacje są kodowane przy użyciu niestandardowego wektora długości typu (TLV) w następujący sposób:

Szczegóły niestandardowego TLV:

|Pole |Długość (oktety)  |Opis  |
|---------|---------|----------|
|Type  |1        |PP2_TYPE_AZURE (0xEE)|
|Długość  |2      |Długość wartości|
|Wartość  |1     |PP2_SUBTYPE_AZURE_PRIVATEENDPOINT_LINKID (0x01)|
|  |4        |UINT32 (4 bajty) reprezentujący LINKID prywatnego punktu końcowego. Zakodowane w formacie little endian.|

 > [!NOTE]
 > Dostawca usług jest odpowiedzialny za upewnienie się, że usługa znajdująca się za standardowym modułem równoważenia obciążenia jest skonfigurowana do analizowania nagłówka protokołu proxy zgodnie ze [specyfikacją](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt) , gdy protokół proxy jest włączony w usłudze linku prywatnego. Żądanie zakończy się niepowodzeniem, jeśli ustawienie protokołu proxy jest włączone w usłudze link prywatny, ale usługa dostawcy usług nie jest skonfigurowana do analizowania nagłówka. Podobnie żądanie zakończy się niepowodzeniem, jeśli usługa dostawcy usług oczekuje nagłówka protokołu proxy, gdy to ustawienie nie zostanie włączone w usłudze link prywatny. Po włączeniu ustawienia protokołu proxy nagłówek protokołu proxy również będzie uwzględniał sondy kondycji protokołu HTTP/TCP z hosta do maszyn wirtualnych zaplecza, nawet jeśli w nagłówku nie będzie żadnych informacji o kliencie. 

## <a name="limitations"></a>Ograniczenia

Poniżej przedstawiono znane ograniczenia dotyczące korzystania z usługi link prywatny:
- Obsługiwane tylko na usługa Load Balancer w warstwie Standardowa 
- Obsługuje tylko ruch IPv4
- Obsługuje tylko ruch TCP

## <a name="next-steps"></a>Następne kroki
- [Tworzenie usługi linku prywatnego przy użyciu Azure PowerShell](create-private-link-service-powershell.md)
- [Tworzenie prywatnej usługi linkowej przy użyciu interfejsu wiersza polecenia platformy Azure](create-private-link-service-cli.md)
