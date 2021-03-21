---
title: Topologie przepływu wywołań w usłudze Azure Communications Services
titleSuffix: An Azure Communication Services concept document
description: Dowiedz się więcej na temat topologii przepływu wywołań w usłudze Azure Communications Services.
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 526e3a1e4eeb6ef6a31a33498241d9a7443cca35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103490640"
---
# <a name="call-flow-topologies"></a>Topologie przepływu wywołań
W tym artykule opisano topologie przepływu wywołań usług Azure Communication Services. To świetny artykuł, który można sprawdzić, jeśli jesteś klientem korporacyjnym integrującym usługi komunikacyjne w sieci, którą zarządzasz. Aby uzyskać wprowadzenie do przepływów wywołań usług komunikacyjnych, odwiedź [dokumentację koncepcyjną przepływów wywołań](./call-flows.md).

## <a name="background"></a>Tło

### <a name="network-concepts"></a>Pojęcia dotyczące sieci

Przed rozpoczęciem przeglądania topologii przepływu wywołań zdefiniujemy pewne terminy, które są określane w całym dokumencie.

**Sieć klienta** zawiera wszystkie segmenty sieci, którymi zarządzasz. Mogą to być sieci przewodowe i bezprzewodowe w biurze lub między biurami, centrami danych i dostawcami usług internetowych.

Sieć klienta ma zwykle kilka obwodów sieciowych z zaporami i/lub serwerami proxy, które wymuszają zasady zabezpieczeń Twojej organizacji. Zalecamy wykonanie [kompleksowej oceny sieci](/microsoftteams/3-envision-evaluate-my-environment) , aby zapewnić optymalną wydajność i jakość rozwiązania do komunikacji.

**Sieć usług komunikacyjnych** to segment sieci obsługujący usługi Azure Communications Services. Ta sieć jest zarządzana przez firmę Microsoft i jest dystrybuowana na całym świecie z krawędziami blisko większości sieci klientów. Ta sieć odpowiada za przekazywanie transportowe, przetwarzanie multimediów dla wywołań grup oraz inne składniki, które obsługują zaawansowaną komunikację z multimediami w czasie rzeczywistym.

### <a name="types-of-traffic"></a>Typy ruchu

Usługi komunikacyjne są oparte głównie na dwóch typach ruchu: **multimedia w czasie rzeczywistym** i **sygnalizowanie**.

**Nośniki** w czasie rzeczywistym są przesyłane przy użyciu protokołu transportowego w czasie rzeczywistym (RTP). Ten protokół obsługuje przesyłanie danych audio, wideo i ekranu. Te dane są wrażliwe na problemy z opóźnieniami w sieci. Chociaż istnieje możliwość przesłania multimediów w czasie rzeczywistym przy użyciu protokołu TCP lub HTTP, zalecamy użycie protokołu UDP jako protokół warstwy transportowej do obsługi wysokiej wydajności środowiska użytkownika końcowego. Ładunki multimedialne przesyłane za pośrednictwem protokołu RTP są zabezpieczone za pomocą SRTP.

Użytkownicy rozwiązania usług komunikacyjnych będą łączyć się z usługami za pomocą ich urządzeń klienckich. Komunikacja między tymi urządzeniami i serwerami jest obsługiwana przez **sygnalizowanie**. Na przykład: wywołania inicjacji i rozmowy w czasie rzeczywistym są obsługiwane przez sygnalizowanie między urządzeniami i usługą. W przypadku większości sygnałów ruch używa protokołu HTTPS, chociaż w niektórych scenariuszach SIP może być używany jako protokół ruchu sieciowego. Chociaż ruch tego typu jest mniej wrażliwy na opóźnienia, oznacza to, że użytkownicy rozwiązania mają wrażenie przyjemnego środowiska użytkownika końcowego.

### <a name="interoperability-restrictions"></a>Ograniczenia dotyczące współdziałania

Multimedia przepływające za poorednictwem usług komunikacyjnych są ograniczone w następujący sposób:

**Przekaźniki nośników innych firm.** Współdziałanie z przekaźnikami multimedialnymi innych firm nie jest obsługiwane. Jeśli jeden z punktów końcowych multimediów to usługi komunikacyjne, multimedia mogą przepływać wyłącznie na natywne przekaźniki multimediów firmy Microsoft, w tym te, które obsługują usługi Microsoft Teams i Skype dla firm.

Kontroler graniczny sesji innej firmy (SBC) na granicy z PSTN powinien kończyć strumień RTP/RTCP, zabezpieczony przy użyciu SRTP i nie przekazuje go do następnego przeskoku. Przekazanie przepływu do następnego przeskoku może nie być zrozumiałe.

**Serwery proxy SIP innych firm.** Usługi komunikacyjne sygnalizujące okno dialogowe z usługą SBC i/lub bramą innej firmy mogą przechodzić przez natywne serwery proxy SIP firmy Microsoft (podobnie jak zespoły). Współdziałanie z serwerami proxy SIP innych firm nie jest obsługiwane.

**B2BUA innych firm (lub SBC).** Przesyłanie strumieniowe usług komunikacyjnych do i z sieci PSTN zostało przerwane przez firmę SBC innej firmy. Współdziałanie z usługą SBC innej firmy w sieci usług komunikacyjnych (w przypadku, gdy firma SBC koryguje dwa punkty końcowe usług komunikacyjnych) nie jest obsługiwana.

### <a name="unsupported-technologies"></a>Nieobsługiwane technologie

**Sieć VPN.** Usługi komunikacyjne nie obsługują przesyłania multimediów przez sieci VPN. Jeśli użytkownicy korzystają z klientów sieci VPN, klient powinien podzielić i skierować ruch związany z multimediami w ramach połączenia spoza sieci VPN, jak określono w temacie [Włączanie nośnika programu Lync do pomijania tunelu VPN.](https://techcommunity.microsoft.com/t5/skype-for-business-blog/enabling-lync-media-to-bypass-a-vpn-tunnel/ba-p/620210)

*Korygując. Mimo że tytuł wskazuje Lync, ma zastosowanie do usług i zespołów komunikacyjnych platformy Azure.*

**Kształty pakietów.** Wycinanie pakietów, inspekcja pakietów lub urządzenia kształtowania pakietów nie są obsługiwane i mogą znacząco obniżać jakość.

### <a name="call-flow-principles"></a>Zasady przepływu wywołań

Istnieją cztery ogólne zasady, które stanowią podstawę przepływów wywołań usług komunikacyjnych:

* **Pierwszy uczestnik wywołania grupy usług komunikacyjnych określa region, w którym jest hostowane wywołanie**. Istnieją wyjątki od tej reguły w niektórych topologiach, które opisano poniżej.
* **Punkt końcowy nośnika używany do obsługi wywołania usług komunikacyjnych jest wybierany w oparciu o wymagania dotyczące przetwarzania multimediów** i nie ma to wpływ na liczbę uczestników wywołania. Na przykład połączenie punkt-punkt może używać punktu końcowego multimediów w chmurze do przetwarzania nośników do transkrypcji lub nagrywania, podczas gdy wywołanie z dwoma uczestnikami nie może używać żadnych punktów końcowych multimediów. Wywołania grup będą używać punktu końcowego multimediów do mieszania i routingu. Ten punkt końcowy jest wybierany w oparciu o region, w którym jest hostowane wywołanie. Ruch multimedialny wysłany z klienta do punktu końcowego multimediów może być kierowany bezpośrednio lub może korzystać z przekaźnika transportowego na platformie Azure, jeśli wymagają tego ograniczenia zapory sieciowej klienta.
* **Ruch multimedialny dla wywołań równorzędnych korzysta z najbardziej bezpośredniej trasy, która jest dostępna**, przy założeniu, że wywołanie nie wymaga punktu końcowego nośnika w chmurze. Preferowana trasa jest bezpośrednia dla zdalnego elementu równorzędnego (klienta). Jeśli trasa bezpośrednia jest niedostępna, jedno lub więcej przekaźników transportu przekaże ruch. Ruch związany z multimediami, nie powinien działać na serwerach podrzędnych, takich jak kształty pakietów, serwery sieci VPN lub inne funkcje, które mogą opóźniać przetwarzanie i obniżyć wydajność środowiska użytkownika końcowego.
* **Sygnalizowanie ruchu zawsze przechodzi do dowolnego serwera, który znajduje się najbliżej użytkownika.**

Aby dowiedzieć się więcej na temat szczegółowych informacji na temat wybranej ścieżki nośnika, zapoznaj się z [dokumentacją koncepcyjną przepływów wywołań](./call-flows.md).


## <a name="call-flows-in-various-topologies"></a>Wywołania przepływów w różnych topologiach

### <a name="communication-services-internet"></a>Usługi komunikacyjne (Internet)

Ta topologia jest używana przez klientów korzystających z usług komunikacyjnych z chmury bez żadnych wdrożeń lokalnych, takich jak SIP Interface. W tej topologii ruch do i z usług komunikacyjnych jest przepływem przez Internet.

:::image type="content" source="./media/call-flows/detailed-flow-general.png" alt-text="Topologia usług komunikacyjnych platformy Azure.":::

*Rysunek 1. topologia usługi komunikacyjnej*

Kierunek strzałek na powyższym diagramie odzwierciedla kierunek inicjacji komunikacji, która ma wpływ na łączność na obrzeżach przedsiębiorstwa. W przypadku protokołu UDP dla nośnika pierwsze pakiety mogą przepływać w odwrotnym kierunku, ale te pakiety mogą być blokowane do momentu przepływania pakietów w innym kierunku.

Opisy przepływu:
* Flow 2 * — reprezentuje przepływ zainicjowany przez użytkownika w sieci klienta do Internetu w ramach środowiska usług komunikacyjnych użytkownika. Przykłady tych przepływów obejmują przesyłanie multimediów w systemie DNS i komunikacji równorzędnej.
* Flow 2 — reprezentuje przepływ zainicjowany przez zdalnego użytkownika usług komunikacji mobilnej z siecią VPN do sieci klienta.
* Flow 3 — reprezentuje przepływ zainicjowany przez zdalnego użytkownika usług łączności mobilnej do punktów końcowych usług komunikacyjnych.
* Flow 4 — reprezentuje przepływ zainicjowany przez użytkownika w sieci klienta do usług komunikacyjnych.
* Flow 5 — reprezentuje przepływ multimediów równorzędnych między jednym użytkownikiem usług komunikacyjnych i innym w sieci klienta.
* Flow 6 — reprezentuje przepływ multimediów równorzędnych między zdalnym użytkownikiem usług łączności mobilnej a innym zdalnym użytkownikiem usług komunikacji mobilnej przez Internet.

### <a name="use-case-one-to-one"></a>Przypadek użycia: jeden do jednego

Wywołania jedno-do-jednego korzystają ze wspólnego modelu, w którym obiekt wywołujący uzyska zestaw kandydatów składający się z adresów IP/portów, w tym lokalnego, przekaźnika i zwrotnego (publicznego adresu IP klienta widzianego przez kandydata). Obiekt wywołujący wysyła tych kandydatów do osoby wywoływanej; wywołana Strona uzyskuje również podobny zestaw kandydatów i wysyła je do obiektu wywołującego. Komunikaty o sprawdzaniu łączności STUN są używane do znajdowania ścieżek nośnika osoby wywołującej/wywoływanej i wybierana jest Najlepsza ścieżka robocza. Nośniki (to znaczy pakiety RTP/RTCP zabezpieczone za pomocą SRTP) są następnie wysyłane przy użyciu wybranej pary kandydatów. Przekaźnik transportu jest wdrażany w ramach usług Azure Communications Services.

Jeśli lokalny adres IP/kandydatów portów lub kandydatów zwrotnych mają łączność, zostanie wybrana ścieżka bezpośrednia między klientami (lub przy użyciu translatora adresów sieciowych) dla nośnika. Jeśli klienci znajdują się w sieci klienta, należy wybrać ścieżkę bezpośrednią. Wymaga to bezpośredniej łączności UDP w sieci klienta. Jeśli klienci są zarówno użytkownikami w chmurze Nomadic, a następnie w zależności od translatora adresów sieciowych/zapory, nośnik może korzystać z łączności bezpośredniej.

Jeśli jeden klient jest wewnętrzny w sieci klienta i jeden klient jest zewnętrzny (na przykład użytkownik w chmurze mobilnej), jest mało prawdopodobne, że komunikacja bezpośrednia między kandydatami lokalnymi lub zwrotnymi nie działa. W takim przypadku opcją jest użycie jednego z kandydatów usługi transport Relay z dowolnego klienta (na przykład klient wewnętrzny uzyskał kandydata do przekazywania z przekaźnika transportowego na platformie Azure; Klient zewnętrzny musi mieć możliwość wysyłania pakietów STUN/RTP/RTCP do przekaźnika transportowego). Innym rozwiązaniem jest to, że wewnętrzny klient wysyła do kandydata usługi Relay uzyskanego przez klienta chmury mobilnej. Mimo że łączność z protokołem UDP dla multimediów jest zdecydowanie zalecana, obsługiwany jest protokół TCP.

**Ogólne czynności:**
1.  Użytkownik usług komunikacyjnych rozpoznaje nazwę domeny adresu URL (DNS) przy użyciu przepływu 2.
2.  Użytkownik A przydziela port przekaźnika multimediów w usłudze Flow transport Relay przy użyciu przepływu 4.
3.  Użytkownik usługi komunikacyjnej A wysyła "zaproszenie" z kandydatami lodu przy użyciu przepływu 4 do usług komunikacyjnych.
4.  Usługi komunikacyjne powiadamiają użytkownika B przy użyciu przepływu 4.
5.  Użytkownik B przypisuje Port usługi Media Relay dla zespołów transport Relay przy użyciu przepływu 4.
6.  Użytkownik B wysyła "odpowiedź" z kandydatami lodu przy użyciu przepływu 4, który jest przekazywany z powrotem do użytkownika A przy użyciu przepływu 4.
7.  Użytkownik A i użytkownik B wywołają testy łączności lodu i wybrano najlepszą dostępną ścieżkę nośnika (zobacz Diagramy poniżej dla różnych przypadków użycia).
8.  Obaj użytkownicy wysyłają dane telemetryczne do usług komunikacyjnych za pomocą usługi Flow 4.

### <a name="customer-network-intranet"></a>Sieć klienta (intranet)

:::image type="content" source="./media/call-flows/one-to-one-internal.png" alt-text="Przepływ ruchu w sieci klienta.":::

*Rysunek 2 — w sieci klienta*

W kroku 7 wybrano opcję przepływ multimediów równorzędnych 5.

Ta transmisja multimedialna jest dwukierunkowa. Kierunek przepływu 5 wskazuje, że jedna strona inicjuje komunikację z perspektywy łączności. W tym przypadku nie ma znaczenia, który kierunek jest używany, ponieważ oba punkty końcowe znajdują się w sieci klienta.

### <a name="customer-network-to-external-user-media-relayed-by-teams-transport-relay"></a>Klient sieci dla użytkowników zewnętrznych (nośnik przekazany przez zespoły transport Relay)

:::image type="content" source="./media/call-flows/one-to-one-via-relay.png" alt-text="Jeden do jednego przepływu wywołań za pośrednictwem przekaźnika.":::

*Rysunek 3. Klient sieci dla użytkowników zewnętrznych (nośnik przekazany przez usługę Azure transport Relay)*

W kroku 7 jest zaznaczone polecenie Flow 4 (od sieci klienta do usług komunikacyjnych) i przepływu 3 (od zdalnego użytkownika usług komunikacji mobilnej do usługi Azure Communications Services).

Te przepływy są przekazywane przez zespoły transportowe w ramach platformy Azure.

Ta transmisja multimedialna jest dwukierunkowa. Kierunek wskazuje, która strona inicjuje komunikację z perspektywy łączności. W takim przypadku przepływy są używane do sygnalizowania i mediów przy użyciu różnych protokołów i adresów transportowych.

### <a name="customer-network-to-external-user-direct-media"></a>Klient z siecią dla użytkowników zewnętrznych (nośnik bezpośredni)

:::image type="content" source="./media/call-flows/one-to-one-with-extenal.png" alt-text="Jeden do jednego przepływu wywołań z zewnętrznym użytkownikiem.":::

*Rysunek 4. Klient sieci dla użytkowników zewnętrznych (nośnik bezpośredni)*

W kroku 7 wybrano pozycję przepływ 2 (od sieci klienta do elementu równorzędnego klienta przez Internet).

Bezpośredni nośnik ze zdalnym użytkownikiem mobilnym (nieprzekazanym przez platformę Azure) jest opcjonalny. Innymi słowy, można zablokować tę ścieżkę, aby wymusić ścieżkę nośnika za pomocą przekaźnika transportowego na platformie Azure.

Ta transmisja multimedialna jest dwukierunkowa. Kierunek przepływu 2 do zdalnego użytkownika mobilnego wskazuje, że jedna strona inicjuje komunikację z perspektywy łączności.

### <a name="vpn-user-to-internal-user-media-relayed-by-teams-transport-relay"></a>Użytkownik sieci VPN do użytkownika wewnętrznego (nośnik przekazany przez zespoły transport Relay)

:::image type="content" source="./media/call-flows/vpn-to-internal-via-relay.png" alt-text="Jeden do jednego przepływu wywołań z użytkownikiem sieci VPN za pośrednictwem przekaźnika.":::

*Rysunek 5. użytkownik sieci VPN do użytkownika wewnętrznego (nośnik przekazany przez Azure Relay*

Sygnalizowanie sieci VPN do sieci klienta korzysta z przepływu 2 *. Sygnalizowanie między siecią klienta i platformą Azure korzysta z usługi Flow 4. Jednak nośnik pomija sieć VPN i jest kierowany przy użyciu przepływów 3 i 4 za pośrednictwem usługi Azure Media Relay.

### <a name="vpn-user-to-internal-user-direct-media"></a>Użytkownik sieci VPN do użytkownika wewnętrznego (nośnik bezpośredni)

:::image type="content" source="./media/call-flows/vpn-to-internal-direct-media.png" alt-text="Jeden do jednego przepływu wywołań (użytkownik wewnętrzny) z siecią VPN z nośnikiem bezpośrednim":::

*Rysunek 6 — użytkownik wewnętrzny (nośnik bezpośredni)*

Sygnalizowanie sieci VPN do sieci klienta korzysta z przepływu 2 ". Sygnalizowanie między siecią klienta i platformą Azure korzysta z usługi Flow 4. Jednak nośnik pomija sieć VPN i jest kierowany przy użyciu przepływu 2 z sieci klienta do Internetu.

Ta transmisja multimedialna jest dwukierunkowa. Kierunek przepływu 2 dla zdalnego użytkownika mobilnego wskazuje, że jedna strona inicjuje komunikację z perspektywy łączności.

### <a name="vpn-user-to-external-user-direct-media"></a>Użytkownik zewnętrzny (nośnik bezpośredni)

:::image type="content" source="./media/call-flows/vpn-user-to-external-user.png" alt-text="Jeden do jednego przepływu wywołań (użytkownik zewnętrzny) z siecią VPN z nośnikiem bezpośrednim":::

*Rysunek 7 — użytkownik zewnętrzny (nośnik bezpośredni)*

Sygnalizowanie użytkownika sieci VPN do sieci klienta korzysta z przepływu 2 * i przepływu 4 na platformie Azure. Jednak nośnik pomija sieć VPN i jest kierowany przy użyciu przepływu 6.

Ta transmisja multimedialna jest dwukierunkowa. Kierunek przepływu 6 dla zdalnego użytkownika mobilnego wskazuje, że jedna strona inicjuje komunikację z perspektywy łączności.

### <a name="use-case-communication-services-client-to-pstn-through-communication-services-trunk"></a>Przypadek użycia: klient usługi komunikacyjnej do sieci PSTN przez magistralę usług komunikacyjnych

Usługi komunikacyjne umożliwiają umieszczanie i otrzymywanie wywołań z publicznej sieci telefonicznej (PSTN). Jeśli magistrala PSTN jest połączona przy użyciu numerów telefonów dostarczonych przez usługi komunikacyjne, nie ma specjalnych wymagań dotyczących łączności dla tego przypadku użycia. Jeśli chcesz połączyć własny lokalny magistralę PSTN z usługami Azure Communications Services, możesz użyć interfejsu SIP (dostępnego w CY2021).

:::image type="content" source="./media/call-flows/acs-to-pstn.png" alt-text="Jedno do jednego wywołanie z uczestnikiem sieci PSTN":::

*Rysunek 8 — użytkownik usług komunikacyjnych w sieci PSTN za pomocą magistrali Azure*

W takim przypadku sygnalizowanie i nośniki z sieci klienta do usługi Azure use Flow 4.

### <a name="use-case-communication-services-group-calls"></a>Przypadek użycia: wywołania grup usług komunikacyjnych

Usługa Azure Communication Services (VBSS) jest częścią usługi łączności audio/wideo/ekranu. Ma publiczny adres IP, który musi być dostępny z sieci klienta i musi być dostępny z poziomu klienta w chmurze Nomadic. Każdy klient/punkt końcowy musi mieć możliwość nawiązania połączenia z usługą.

Klienci wewnętrzni uzyskają dostęp do elementów lokalnych, zwrotnych i przekazywania w taki sam sposób, jak opisano w przypadku wywołań jeden-do-jednego. Klienci będą wysyłać tych kandydatów do usługi w ramach zaproszenia. Usługa nie korzysta z przekaźnika, ponieważ ma publicznie dostępny adres IP, więc odpowiada przy użyciu swojego lokalnego adresu IP kandydata. Klient i usługa sprawdzają łączność w taki sam sposób, jak opisane dla wywołań jeden-do-jednego.

:::image type="content" source="./media/call-flows/acs-group-calls.png" alt-text="Wywołanie grupy OACS":::

*Rysunek 9 — wywołania grup usług komunikacyjnych*

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do wywoływania](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Następujące dokumenty mogą być interesujące:

- Dowiedz się więcej o [typach wywołań](../concepts/voice-video-calling/about-call-types.md)
- Informacje o [architekturze klient-serwer](./client-and-server-architecture.md)