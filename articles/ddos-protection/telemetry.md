---
title: Wyświetlanie i Konfigurowanie telemetrii ochrony DDoS dla standardu Azure DDoS Protection
description: Informacje o sposobie wyświetlania i konfigurowania telemetrii ochrony DDoS dla standardu Azure DDoS Protection.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: a54404dafaf1093a2e2acbde88187739bd6c9f06
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97827414"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>Wyświetlanie i konfigurowanie telemetrii ochrony przed atakami DDoS

Azure DDoS Protection Standard zapewnia szczegółowe informacje o atakach i wizualizacje z DDoSą analizą ataków. Klienci chroniący sieci wirtualne przed atakami DDoS mają szczegółowy wgląd w ruch związany z atakami i działania podejmowane w celu ograniczenia ataku za pośrednictwem raportów ograniczenia ataków, które &ją dzienniki przepływów ograniczenia. Bogate dane telemetryczne są udostępniane za pośrednictwem Azure Monitor, w tym szczegółowych metryk w czasie trwania ataku DDoS. Alerty można skonfigurować dla dowolnych metryk Azure Monitor uwidocznionych przez DDoS Protection. Rejestrowanie może być dodatkowo zintegrowane z [platformą Azure](../sentinel/connect-azure-ddos-protection.md), Splunk (Azure Event Hubs), pakietem OMS log Analytics i usługą Azure Storage, aby uzyskać zaawansowaną analizę za pośrednictwem interfejsu diagnostyki Azure monitor.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Wyświetlanie telemetrii ochrony DDoS
> * Wyświetlanie zasad łagodzenia DDoS
> * Weryfikuj i Testuj telemetrię DDoS Protection

### <a name="metrics"></a>Metryki

> [!NOTE]
> W przypadku wyświetlenia wielu opcji **agregacji** w Azure Portal są obsługiwane tylko typy agregacji wymienione w poniższej tabeli. Przepraszamy za to pomyłkę i pracujemy nad rozwiązaniem tego problemu.

Następujące [metryki](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetworkpublicipaddresses) są dostępne dla Azure DDoS Protection Standard. Te metryki można również eksportować za pośrednictwem ustawień diagnostycznych (zobacz [Wyświetlanie i Konfigurowanie rejestrowania diagnostycznego DDoS](diagnostic-logging.md)).


| Metryka | Nazwa wyświetlana metryki | Jednostka | Typ agregacji | Opis |
| --- | --- | --- | --- | --- |
| ByteCount | Liczba bajtów | Liczba | Łącznie | Łączna liczba bajtów przesłanych w okresie |
| BytesDroppedDDoS | Bajty przychodzące opuszczone DDoS | BytesPerSecond | Maksimum | Bajty przychodzące opuszczone DDoS| 
| BytesForwardedDDoS | Przekazane bajty przychodzące DDoS | BytesPerSecond | Maksimum | Przekazane bajty przychodzące DDoS |
| BytesInDDoS | Bajty przychodzące DDoS | BytesPerSecond | Maksimum | Bajty przychodzące DDoS |
| DDoSTriggerSYNPackets | Pakiety przychodzących SYN wyzwalające łagodzenie DDoS | CountPerSecond | Maksimum | Pakiety przychodzących SYN wyzwalające łagodzenie DDoS |
| DDoSTriggerTCPPackets | Przychodzące pakiety TCP do wyzwalania łagodzenia DDoS | CountPerSecond | Maksimum | Przychodzące pakiety TCP do wyzwalania łagodzenia DDoS |
| DDoSTriggerUDPPackets | Przychodzące pakiety UDP do wyzwalania łagodzenia DDoS | CountPerSecond | Maksimum | Przychodzące pakiety UDP do wyzwalania łagodzenia DDoS |
| IfUnderDDoSAttack | W obszarze atak DDoS | Liczba | Maksimum | W obszarze atak DDoS |
| PacketCount | Liczba pakietów | Liczba | Łącznie | Łączna liczba pakietów wysłanych w czasie |
| PacketsDroppedDDoS | Odrzucone pakiety przychodzące DDoS | CountPerSecond | Maksimum | Odrzucone pakiety przychodzące DDoS |
| PacketsForwardedDDoS | Przekazane pakiety przychodzące DDoS | CountPerSecond | Maksimum | Przekazane pakiety przychodzące DDoS |
| PacketsInDDoS | Pakiety przychodzące DDoS | CountPerSecond | Maksimum | Pakiety przychodzące DDoS |
| SynCount | Liczba SYN | Liczba | Łącznie | Łączna liczba pakietów SYN wysłanych w czasie |
| TCPBytesDroppedDDoS | Liczba porzuconych bajtów przychodzących protokołu TCP DDoS | BytesPerSecond | Maksimum | Liczba porzuconych bajtów przychodzących protokołu TCP DDoS |
| TCPBytesForwardedDDoS | Przekazane DDoS przychodzące bajty TCP | BytesPerSecond | Maksimum | Przekazane DDoS przychodzące bajty TCP |
| TCPBytesInDDoS | Przychodzące bajty TCP DDoS | BytesPerSecond | Maksimum | Przychodzące bajty TCP DDoS |
| TCPPacketsDroppedDDoS | Liczba porzuconych pakietów TCP przychodzących DDoS | CountPerSecond | Maksimum | Liczba porzuconych pakietów TCP przychodzących DDoS |
| TCPPacketsForwardedDDoS | Przychodzące pakiety TCP przesłane dalej DDoS | CountPerSecond | Maksimum | Przychodzące pakiety TCP przesłane dalej DDoS |
| TCPPacketsInDDoS | Przychodzące pakiety TCP DDoS | CountPerSecond | Maksimum | Przychodzące pakiety TCP DDoS |
| UDPBytesDroppedDDoS | Liczba porzuconych bajtów przychodzących UDP DDoS | BytesPerSecond | Maksimum | Liczba porzuconych bajtów przychodzących UDP DDoS |
| UDPBytesForwardedDDoS | Przekazane przychodzące bajty UDP DDoS | BytesPerSecond | Maksimum | Przekazane przychodzące bajty UDP DDoS |
| UDPBytesInDDoS | Przychodzące bajty UDP DDoS | BytesPerSecond | Maksimum | Przychodzące bajty UDP DDoS |
| UDPPacketsDroppedDDoS | Liczba porzuconych pakietów przychodzących UDP DDoS | CountPerSecond | Maksimum | Liczba porzuconych pakietów przychodzących UDP DDoS |
| UDPPacketsForwardedDDoS | Przychodzące pakiety UDP DDoS przesłane dalej | CountPerSecond | Maksimum | Przychodzące pakiety UDP DDoS przesłane dalej |
| UDPPacketsInDDoS | Przychodzące pakiety UDP DDoS | CountPerSecond | Maksimum | Przychodzące pakiety UDP DDoS |
| VipAvailability | Dostępność ścieżki danych | Liczba | Średnia | Średnia dostępność adresów IP na czas trwania |

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Przed wykonaniem kroków opisanych w tym samouczku należy najpierw utworzyć [Plan ochrony usługi Azure DDoS Standard](manage-ddos-protection.md) , a w sieci wirtualnej musi być włączona funkcja DDoS Protection Standard.
- DDoS monitoruje publiczne adresy IP przypisane do zasobów w ramach sieci wirtualnej. Jeśli nie masz żadnych zasobów z publicznymi adresami IP w sieci wirtualnej, musisz najpierw utworzyć zasób z publicznym adresem IP. Możesz monitorować publiczny adres IP wszystkich zasobów wdrożonych za pomocą Menedżer zasobów (nieklasyczny) wymieniony w [sieci wirtualnej dla usług platformy Azure (w](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) tym modułów równoważenia obciążenia platformy Azure, w których znajdują się maszyny wirtualne zaplecza w sieci wirtualnej), z wyjątkiem środowisk Azure App Service i VPN Gateway platformy Azure. Aby kontynuować pracę z tym samouczkiem, możesz szybko utworzyć maszynę wirtualną z [systemem Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .  

## <a name="view-ddos-protection-telemetry"></a>Wyświetlanie telemetrii ochrony DDoS

Dane telemetryczne dla ataku są udostępniane za pośrednictwem Azure Monitor w czasie rzeczywistym. Dane telemetryczne są dostępne tylko dla czasu, w którym publiczny adres IP jest objęty ograniczeniem. Nie widzisz danych telemetrycznych przed usunięciem ataku lub po nim.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) i przejdź do planu DDoS Protection.
2. W obszarze **Monitorowanie** wybierz pozycję **Metryki**.
3. Wybierz **zakres**. Wybierz **subskrypcję** zawierającą publiczny adres IP, który chcesz zalogować, wybierz pozycję **publiczny adres IP** dla opcji **Typ zasobu**, a następnie wybierz konkretny publiczny adres IP, dla którego chcesz rejestrować metryki, a następnie wybierz pozycję **Zastosuj**.
4. Wybierz typ **agregacji** jako **maksimum**.

Nazwy metryk składają się z różnych typów pakietów i bajtów zamiast pakietów, a podstawowa konstrukcja nazw tagów w każdej metryce w następujący sposób:

- **Porzucona nazwa tagu** (na przykład **pakiety przychodzące opuszczone DDoS**): liczba pakietów porzuconych/wyczyszczonych przez system ochrony DDoS.
- **Nazwa tagu przekazanego** (na przykład **pakiety przychodzące przesyłane dalej DDoS**): liczba pakietów przesłanych przez system DDoS do docelowego adresu VIP — ruch, który nie został przefiltrowany.
- **Brak nazwy tagu** (na przykład **pakiety przychodzące DDoS**): całkowita liczba pakietów, które zostały dołączone do systemu kontroli, reprezentująca sumę pakietów porzuconych i przesłanych dalej.

## <a name="view-ddos-mitigation-policies"></a>Wyświetlanie zasad łagodzenia DDoS

DDoS Protection standard stosuje trzy zasady ograniczania z autodostrajania (TCP SYN, TCP & UDP) dla każdego publicznego adresu IP chronionego zasobu w sieci wirtualnej, w której włączono DDoS. Progi zasad można wyświetlić, wybierając  **przychodzące pakiety TCP, aby wyzwolić DDoS łagodzenie** i **przychodzące pakiety UDP do wyzwalania metryk ograniczenia DDoS** z typem **agregacji** jako "Max", jak pokazano na poniższej ilustracji:

![Wyświetlanie zasad ograniczenia](./media/manage-ddos-protection/view-mitigation-policies.png)

Progi zasad są konfigurowane przy użyciu profilowania sieci opartych na usłudze Azure Machine Learning. Tylko wtedy, gdy próg zasad został naruszony, nastąpi ograniczenie DDoS dla adresu IP w ramach ataku.

## <a name="validate-and-test"></a>Weryfikuj i Testuj

Aby zasymulować atak DDoS w celu zweryfikowania telemetrii ochrony DDoS, zobacz [Weryfikowanie wykrywania DDoS](test-through-simulations.md).

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

- Konfigurowanie alertów dotyczących metryk ochrony DDoS
- Wyświetlanie telemetrii ochrony DDoS
- Wyświetlanie zasad łagodzenia DDoS
- Weryfikuj i Testuj telemetrię DDoS Protection

Aby dowiedzieć się, jak skonfigurować raporty łagodzenia ataków i dzienniki przepływów, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Wyświetl i skonfiguruj rejestrowanie diagnostyczne DDoS](diagnostic-logging.md)
