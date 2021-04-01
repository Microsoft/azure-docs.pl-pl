---
title: Informacje o łączności z siecią pulpitu wirtualnego systemu Windows
titleSuffix: Azure
description: Informacje o łączności z siecią pulpitu wirtualnego systemu Windows
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: e4149864e16196b695d38a8c46ab5af835453412
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99221214"
---
# <a name="understanding-windows-virtual-desktop-network-connectivity"></a>Informacje o łączności z siecią pulpitu wirtualnego systemu Windows

Pulpit wirtualny systemu Windows umożliwia hostowanie sesji klientów na hostach sesji działających na platformie Azure. Firma Microsoft zarządza częścią usług w imieniu klienta i zapewnia bezpieczne punkty końcowe do łączenia klientów i hostów sesji. Na poniższym diagramie przedstawiono ogólne omówienie połączeń sieciowych używanych przez pulpit wirtualny systemu Windows

:::image type="content" source="media/windows-virtual-desktop-network-connections.svg" alt-text="Diagram połączeń sieci pulpitów wirtualnych systemu Windows" lightbox="media/windows-virtual-desktop-network-connections.svg":::

## <a name="session-connectivity"></a>Łączność sesji

Pulpit wirtualny systemu Windows używa Remote Desktop Protocol (RDP) do zapewniania funkcji zdalnego wyświetlania i wprowadzania danych za pośrednictwem połączeń sieciowych. Protokół RDP początkowo został zwolniony z systemem Windows NT 4,0 Terminal Server Edition i ciągle został rozwijający się za pomocą każdej wersji systemu Microsoft Windows i systemu Windows Server. Od początku protokół RDP opracowany jako niezależny od podstawowego stosu transportowego, a dzisiaj obsługuje wiele typów transportu.

## <a name="reverse-connect-transport"></a>Transport łączenia zwrotnego

Pulpit wirtualny systemu Windows korzysta z transportu odwrotnego do ustanawiania sesji zdalnej i do przenoszenia ruchu RDP. W przeciwieństwie do lokalnych wdrożeń Usługi pulpitu zdalnego funkcja transportu odwrotnego nie korzysta z odbiornika TCP do odbierania przychodzących połączeń RDP. Zamiast tego używa łączności wychodzącej do infrastruktury pulpitów wirtualnych systemu Windows za pośrednictwem połączenia HTTPS.

## <a name="session-host-communication-channel"></a>Kanał komunikacji hosta sesji

Po uruchomieniu hosta sesji usług pulpitu wirtualnego systemu Windows usługa modułu ładującego Pulpit zdalny Agent ustanawia kanał komunikacji trwałej brokera pulpitu wirtualnego systemu Windows. Ten kanał komunikacyjny jest warstwą na podstawie połączenia bezpiecznego Transport Layer Security (TLS) i służy jako magistrala wymiany komunikatów usługi między hostem sesji a infrastrukturą pulpitu wirtualnego systemu Windows.

## <a name="client-connection-sequence"></a>Sekwencja połączenia klienta

Sekwencja połączeń klienta opisana poniżej:

1. Korzystanie z obsługiwanego przez użytkownika klienta klasycznego systemu Windows subskrybowanie obszaru roboczego pulpitów wirtualnych systemu Windows
2. Azure Active Directory uwierzytelnia użytkownika i zwraca token używany do wyliczania zasobów dostępnych dla użytkownika
3. Klient przekazuje token do usługi subskrypcji kanału wirtualnego źródła systemu Windows
4. Usługa subskrypcji kanału wirtualnego systemu Windows sprawdza poprawność tokenu
5. Usługa subskrypcji źródła danych pulpitu wirtualnego systemu Windows przekazuje listę dostępnych pulpitów i funkcji RemoteApp z powrotem do klienta w formie konfiguracji połączenia podpisanego cyfrowo
6. Klient przechowuje konfigurację połączenia dla każdego dostępnego zasobu w zestawie plików RDP.
7. Gdy użytkownik wybierze zasób do połączenia, klient użyje skojarzonego pliku RDP i ustanowi połączenie Secure TLS 1,2 z najbliższym wystąpieniem bramy usług pulpitu wirtualnego systemu Windows i przekaże Informacje o połączeniu
8. Brama usług pulpitu wirtualnego systemu Windows sprawdza poprawność żądania i prosi brokera usług pulpitu wirtualnego systemu Windows o aranżację połączenia
9. Broker pulpitu wirtualnego systemu Windows identyfikuje hosta sesji i używa utworzonego wcześniej stałego kanału komunikacyjnego do zainicjowania połączenia
10. Stos Pulpit zdalny inicjuje połączenie TLS 1,2 z tym samym wystąpieniem bramy usług pulpitu wirtualnego systemu Windows, które jest używane przez klienta
11. Po powiązaniu klienta i hosta sesji z bramą Brama rozpoczyna przekazywanie danych pierwotnych między obydwoma punktami końcowymi, co spowoduje ustanowienie podstawowego transportowego połączenia z serwerem RDP.
12. Po ustawieniu transportu podstawowego klient uruchamia uzgadnianie protokołu RDP.

## <a name="connection-security"></a>Zabezpieczenia połączeń

Protokół TLS 1,2 jest używany dla wszystkich połączeń inicjowanych od klientów i hostów sesji do składników infrastruktury pulpitów wirtualnych systemu Windows. Pulpit wirtualny systemu Windows używa tych samych szyfrów TLS 1,2 co [drzwi platformy Azure](../frontdoor/front-door-faq.md#what-are-the-current-cipher-suites-supported-by-azure-front-door). Ważne jest, aby upewnić się, że zarówno komputery klienckie, jak i hosty sesji mogą korzystać z tych szyfrów.
W przypadku transportowego łączenia zwrotnego zarówno klient, jak i Host sesji nawiązują połączenie z bramą usług pulpitu wirtualnego systemu Windows. Po ustanowieniu połączenia TCP klient lub Host sesji sprawdza poprawność certyfikatu bramy usług pulpitu wirtualnego systemu Windows.
Po ustanowieniu transportu podstawowego protokół RDP ustanawia zagnieżdżone połączenie TLS między klientem i hostem sesji przy użyciu certyfikatów hosta sesji. Domyślnie certyfikat używany na potrzeby szyfrowania RDP jest generowany automatycznie przez system operacyjny podczas wdrażania. W razie potrzeby klienci mogą wdrażać centralne certyfikaty zarządzane przez urząd certyfikacji przedsiębiorstwa. Więcej informacji o konfigurowaniu certyfikatów znajduje się w [dokumentacji systemu Windows Server](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o wymaganiach dotyczących przepustowości dla pulpitu wirtualnego systemu Windows, zobacz [Opis wymagań dotyczących przepustowości Remote Desktop Protocol (RDP) dla pulpitu wirtualnego systemu Windows](rdp-bandwidth.md).
* Aby rozpocząć pracę z usługą Quality of Service (QoS) dla pulpitu wirtualnego systemu Windows, zobacz [implementacja Quality of Service (QoS) dla pulpitu wirtualnego systemu Windows](rdp-quality-of-service-qos.md).
