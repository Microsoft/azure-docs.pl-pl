---
title: Wywołania przepływów w usłudze Azure Communications Services
titleSuffix: An Azure Communication Services concept document
description: Dowiedz się więcej o przepływach wywołań w usłudze Azure Communications Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 0efe9cc3bad599d4ab621fef7308a807cc5e9967
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485990"
---
# <a name="call-flow-basics"></a>Podstawowe informacje o przepływie wywołań

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Poniższa sekcja zawiera omówienie przepływów wywołań w usłudze Azure Communications Services. Sygnalizowanie i przepływy multimediów zależą od typów wywołań wykonywanych przez użytkowników. Przykłady typów wywołań obejmują metodę VoIP jeden-do-jednego, Sieć PSTN typu jeden-do-jednego oraz wywołania grup zawierające kombinację połączeń VoIP i połączonej z PSTN. Przejrzyj [typy wywołań](./voice-video-calling/about-call-types.md).

## <a name="about-signaling-and-media-protocols"></a>Informacje o sygnalizowaniu i protokołach multimediów

Po nawiązaniu połączenia równorzędnego lub grupowego dwa protokoły są używane w tle — HTTP (REST) do sygnalizowania i SRTP na nośniku.

Sygnalizowanie między bibliotekami klienta lub między bibliotekami klienckimi i kontrolerami sygnałów usług komunikacyjnych jest obsługiwane przy użyciu protokołu HTTP REST (TLS). W przypadku usługi Real-Time Media (RTP) preferowany jest protokół UDP (User Datagram). Jeśli korzystanie z protokołu UDP jest uniemożliwione przez zaporę, Biblioteka klienta będzie używać Transmission Control Protocol (TCP) dla nośnika.

Przejrzyjmy protokoły sygnalizujące i multimedialne w różnych scenariuszach.

## <a name="call-flow-cases"></a>Przypadki przepływu wywołań

### <a name="case-1-voip-where-a-direct-connection-between-two-devices-is-possible"></a>Przypadek 1: VoIP, w którym możliwe jest bezpośrednie połączenie między dwoma urządzeniami

W przypadku połączeń VoIP lub wideo jedno-do-jednego, ruch preferuje najbardziej bezpośrednią ścieżkę. "Ścieżka bezpośrednia" oznacza, że jeśli dwie biblioteki klienckie mogą się połączyć bezpośrednio, nawiążą one bezpośrednie połączenie. Jest to zazwyczaj możliwe, gdy dwie biblioteki klienckie znajdują się w tej samej podsieci (na przykład w podsieci 192.168.1.0/24) lub dwie, gdy urządzenia na żywo w podsieci, które mogą zobaczyć siebie nawzajem (biblioteki klienckie w podsieci 10.10.0.0/16 i 192.168.1.0/24 mogą się wzajemnie komunikować).

:::image type="content" source="./media/call-flows/about-voice-case-1.png" alt-text="Diagram przedstawiający bezpośrednie połączenie VOIP między użytkownikami i usługami komunikacyjnymi.":::

### <a name="case-2-voip-where-a-direct-connection-between-devices-is-not-possible-but-where-connection-between-nat-devices-is-possible"></a>Przypadek 2: VoIP, w którym nie jest możliwe bezpośrednie połączenie między urządzeniami, ale w przypadku, gdy jest możliwe połączenie między urządzeniami NAT

Jeśli dwa urządzenia znajdują się w podsieciach, które nie mogą nawiązywać połączeń nawzajem (na przykład Alicja działa z domu i Roberta działa z biura domowego), ale połączenie między urządzeniami NAT jest możliwe, biblioteki klienta po stronie klienta będą nawiązywać połączenia za pośrednictwem urządzeń NAT.

W przypadku programu Alicja będzie to translator adresów sieciowych w sklepie kawowym i dla Roberta, który będzie translatorem adresów sieciowych biura domowego. Urządzenie Alicja wyśle adres zewnętrzny jego translatora adresów sieciowych i Robert będzie tego samego. Biblioteki klienckie zawierają informacje o adresach zewnętrznych z usługi STUN (narzędzia do przechodzenia do sesji dla translatora adresów sieciowych), które są bezpłatnie dostępne dla usług Azure Communication Services. Logika, która obsługuje uzgadnianie między Alicja i Robert, jest wbudowana w udostępnione biblioteki klienta usługi Azure Communications Services. (Nie jest wymagana żadna dodatkowa konfiguracja)

:::image type="content" source="./media/call-flows/about-voice-case-2.png" alt-text="Diagram przedstawiający wywołanie VOIP korzystające z połączenia STUN.":::

### <a name="case-3-voip-where-neither-a-direct-nor-nat-connection-is-possible"></a>Przypadek 3: VoIP, gdzie nie jest możliwe połączenie bezpośrednie ani z translatorem adresów sieciowych

Jeśli jedno lub oba urządzenia klienckie znajdują się za symetrycznym translatorem adresów sieciowych, wymagana jest oddzielna usługa w chmurze przekazująca nośnik między dwiema bibliotekami klienckimi. Ta usługa jest wywoływana (przechodzenie przy użyciu przekaźników wokół translatora adresów sieciowych) i jest również udostępniana przez usługi komunikacyjne. Biblioteka klienta wywołująca usługi komunikacyjne automatycznie korzysta z WŁĄCZania usług na podstawie wykrytych warunków sieci. Opłaty za korzystanie z usługi Microsoft skręć są naliczone osobno.

:::image type="content" source="./media/call-flows/about-voice-case-3.png" alt-text="Diagram przedstawiający wywołanie VOIP, które wykorzystuje połączenie z połączeniem.":::

### <a name="case-4-group-calls-with-pstn"></a>Przypadek 4: wywołania grupowe za pomocą sieci PSTN

Zarówno sygnalizowanie, jak i multimedia dla wywołań PSTN używają zasobu telefonii usługi Azure Communications Services. Ten zasób jest wzajemnie połączony z innymi przewoźnikami.

Ruch multimediów PSTN przez składnik o nazwie procesor multimediów.

:::image type="content" source="./media/call-flows/about-voice-pstn.png" alt-text="Diagram przedstawiający wywołanie grupy PSTN przy użyciu usług komunikacyjnych.":::

> [!NOTE]
> W przypadku tych, które zapoznają się z przetwarzaniem multimediów, nasz procesor multimedialny jest również odwracany do agenta użytkownika, zgodnie z definicją w [dokumencie RFC 3261 SIP:](https://tools.ietf.org/html/rfc3261)calling Protocol. Kontroler sygnalizujący usługi Azure Communication Services to implementacja firmy Microsoft serwera proxy SIP na ten sam dokument.

W przypadku wywołań grup, multimediów i sygnałów zawsze przepływów za pośrednictwem zaplecza usług komunikacyjnych platformy Azure. Audio i/lub wideo ze wszystkich uczestników są mieszane w składniku procesora multimediów. Wszystkie elementy członkowskie wywołania grupy wysyłają strumienie audio i/lub wideo do procesora multimediów, które zwracają strumienie multimediów mieszanych.

Domyślny protokół w czasie rzeczywistym (RTP) dla wywołań grup to UDP (User Datagram Protocol).

> [!NOTE]
> Procesor multimediów może pełnić rolę jednostki kontroli MultiPoint (MIKROKONTROLERY) lub jednostki przekierowania selektywnego (SFU)

:::image type="content" source="./media/call-flows/about-voice-group-calls.png" alt-text="Diagram przedstawiający przepływ procesu multimediów UDP w ramach usług komunikacyjnych.":::

Jeśli Biblioteka klienta nie może używać protokołu UDP dla multimediów z powodu ograniczeń zapory, zostanie podjęta próba użycia Transmission Control Protocol (TCP). Należy pamiętać, że składnik procesora multimediów wymaga protokołu UDP, więc w takim przypadku usługa komunikacji zostanie dodana do wywołania grupy, aby przetłumaczyć protokół TCP na UDP. W takim przypadku naliczanie opłat będzie naliczane, chyba że funkcje WYŁĄCZania nie zostaną wyłączone ręcznie.

:::image type="content" source="./media/call-flows/about-voice-group-calls-2.png" alt-text="Diagram przedstawiający przepływ procesu multimediów TCP w ramach usług komunikacyjnych.":::

### <a name="case-5-communication-services-client-library-and-microsoft-teams-in-a-scheduled-teams-meeting"></a>Przypadek 5: Biblioteka klienta usług komunikacyjnych i zespół firmy Microsoft w zaplanowanym spotkaniu zespołów

Sygnalizowanie przepływów przez kontroler sygnalizujący. Nośniki są przesyłane przez procesor multimediów. Kontroler sygnalizujący i procesor nośników są współużytkowane przez usługi komunikacyjne i Microsoft Teams.

:::image type="content" source="./media/call-flows/teams-communication-services-meeting.png" alt-text="Diagram przedstawiający bibliotekę klienta usług komunikacyjnych i klienta zespołów w zaplanowanym spotkaniu zespołów.":::



## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do wywoływania](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Następujące dokumenty mogą być interesujące:

- Dowiedz się więcej o [typach wywołań](../concepts/voice-video-calling/about-call-types.md)
- Informacje o [architekturze klient-serwer](./client-and-server-architecture.md)
- Informacje o [topologii przepływu wywołań](./detailed-call-flows.md)
