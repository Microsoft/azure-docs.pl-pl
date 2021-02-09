---
title: Microsoft Azure Stack graniczny R wymagania systemowe Microsoft Docs
description: Dowiedz się więcej o wymaganiach dotyczących oprogramowania i sieci dla Azure Stack krawędzi mini R
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: alkohli
ms.openlocfilehash: 6836777c761fa2cce482cb000b6c86919e6b5613
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834072"
---
# <a name="azure-stack-edge-mini-r-system-requirements"></a>Azure Stack graniczny R wymagania systemowe

W tym artykule opisano ważne wymagania systemowe dotyczące rozwiązania "mini R" w Microsoft Azure Stack Edge i dla klientów łączących się z Azure Stack Edge mini R. Zalecamy dokładne zapoznanie się z informacjami przed wdrożeniem Azure Stack Edge mini R. W razie potrzeby można odwoływać się do tych informacji podczas wdrażania i kolejnej operacji.

Wymagania systemowe dotyczące Azure Stack krawędzi mini R obejmują:

- **Wymagania dotyczące oprogramowania dla hostów** — zawiera opis obsługiwanych platform, przeglądarek dla lokalnego interfejsu użytkownika konfiguracji, klientów SMB i wszelkich dodatkowych wymagań dla klientów, którzy uzyskują dostęp do urządzenia.
- **Wymagania dotyczące sieci dla urządzenia** — zawiera informacje o wymaganiach sieciowych dla działania urządzenia fizycznego.

## <a name="supported-os-for-clients-connected-to-device"></a>Obsługiwane systemy operacyjne dla klientów podłączonych do urządzenia

[!INCLUDE [Supported OS for clients connected to device](../../includes/azure-stack-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Obsługiwane protokoły dla klientów uzyskujących dostęp do urządzenia

[!INCLUDE [Supported protocols for clients accessing device](../../includes/azure-stack-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Obsługiwane konta magazynu

[!INCLUDE [Supported storage accounts](../../includes/azure-stack-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-edge-storage-accounts"></a>Obsługiwane konta magazynu Edge

Następujące konta magazynu brzegowego są obsługiwane w interfejsie REST urządzenia. Konta magazynu brzegowego są tworzone na urządzeniu. Aby uzyskać więcej informacji, zobacz [konta magazynu Edge](azure-stack-edge-j-series-manage-storage-accounts.md#about-edge-storage-accounts)

|Typ  |Konto magazynu  |Komentarze  |
|---------|---------|---------|
|Standardowa     |GPv1: blokowy obiekt BLOB         |         |


* Stronicowe obiekty blob i Azure Files nie są obecnie obsługiwane.

## <a name="supported-local-azure-resource-manager-storage-accounts"></a>Obsługiwane konta magazynu lokalnego Azure Resource Manager

Te konta magazynu są za pośrednictwem lokalnych interfejsów API urządzenia, gdy nawiązano połączenie z lokalną Azure Resource Manager. Obsługiwane są następujące konta magazynu:

|Typ  |Konto magazynu  |Komentarze  |
|---------|---------|---------|
|Standardowa     |GPv1: blokowy obiekt BLOB, stronicowy obiekt BLOB         | Typ jednostki SKU to Standard_LRS        |
|Premium   |GPv1: blokowy obiekt BLOB, stronicowy obiekt BLOB         |Typ jednostki SKU to Premium_LRS         |


## <a name="supported-storage-types"></a>Obsługiwane typy magazynu

[!INCLUDE [Supported storage types](../../includes/azure-stack-edge-gateway-supported-storage-types.md)]


## <a name="supported-browsers-for-local-web-ui"></a>Obsługiwane przeglądarki dla lokalnego interfejsu użytkownika sieci Web

[!INCLUDE [Supported browsers for local web UI](../../includes/azure-stack-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Wymagania dotyczące portów sieciowych

### <a name="port-requirements-for-azure-stack-edge-mini-r"></a>Wymagania dotyczące portów dla Azure Stack Edge mini R

Poniższa tabela zawiera listę portów, które należy otworzyć w zaporze, aby umożliwić obsługę ruchu SMB, chmury lub zarządzania. W tej tabeli *w programie lub w* *ruchu przychodzącym* odwołuje się do kierunku, w którym klient przychodzący żąda dostępu do urządzenia. *Out* lub *wychodzący* odnosi się do kierunku, w którym urządzenie Azure Stack Edge mini R wysyła dane zewnętrznie, poza wdrożeniem, na przykład wychodzące do Internetu.

[!INCLUDE [Port configuration for device](../../includes/azure-stack-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Wymagania dotyczące portów dla IoT Edge

Azure IoT Edge umożliwia komunikację wychodzącą z lokalnego urządzenia brzegowego do chmury platformy Azure przy użyciu obsługiwanych protokołów IoT Hub. Komunikacja przychodząca jest wymagana tylko w przypadku określonych scenariuszy, w których usługa Azure IoT Hub musi wypchnąć komunikaty do urządzenia Azure IoT Edge (na przykład w chmurze do obsługi komunikatów dotyczących urządzeń).

W poniższej tabeli przedstawiono konfigurację portów dla serwerów hostującym środowisko uruchomieniowe Azure IoT Edge:

| Numer portu. | Do lub do zewnątrz | Zakres portów | Wymagane | Wskazówki |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Out       | Sieć WAN        | Tak      | Wychodzące otwarte dla IoT Edge aprowizacji. Ta konfiguracja jest wymagana w przypadku używania skryptów ręcznych lub usługi Azure IoT Device Provisioning Service (DPS).|

Aby uzyskać pełne informacje, przejdź do [reguły konfiguracji zapory i portów dla IoT Edge wdrożenia](../iot-edge/troubleshoot.md).

## <a name="url-patterns-for-firewall-rules"></a>Wzorce adresów URL dla reguł zapory

Administratorzy sieci mogą często konfigurować zaawansowane reguły zapory na podstawie wzorców adresów URL, aby filtrować ruch przychodzący i wychodzący. Urządzenie Azure Stack Edge mini R i usługa zależą od innych aplikacji firmy Microsoft, takich jak Azure Service Bus, Azure Active Directory Access Control, konta magazynu i serwery Microsoft Update. Wzorce adresów URL skojarzone z tymi aplikacjami mogą służyć do konfigurowania reguł zapory. Ważne jest, aby zrozumieć, że wzorce adresów URL skojarzone z tymi aplikacjami mogą się zmieniać. Te zmiany wymagają od administratora sieci monitorowania i aktualizowania reguł zapory dla Azure Stack krawędzi mini R jako i w razie potrzeby.

Zalecamy ustawienie reguł zapory dla ruchu wychodzącego, na podstawie Azure Stack w większości przypadków stałego adresu IP usługi Edge. Można jednak użyć poniższych informacji, aby ustawić zaawansowane reguły zapory, które są potrzebne do tworzenia bezpiecznych środowisk.

> [!NOTE]
> - Adresy IP urządzeń (źródłowych) powinny zawsze być ustawione na wszystkie interfejsy sieciowe obsługujące chmurę.
> - Docelowe adresy IP powinny być ustawiane na [zakresy adresów IPv4 centrum danych platformy Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Wzorce adresów URL dla funkcji bramy

[!INCLUDE [URL patterns for firewall](../../includes/azure-stack-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Wzorce adresów URL dla funkcji obliczeniowej

| Wzorzec adresu URL                      | Składnik lub funkcjonalność                     |   
|----------------------------------|---------------------------------------------|
| https: \/ /MCR.Microsoft.com<br></br>https:// \* . CDN.MSCR.IO | Rejestr kontenerów firmy Microsoft (wymagany)               |
| https:// \* . azurecr.IO                     | Rejestry kontenerów osobistych i innych firm (opcjonalnie) | 
| https:// \* . Azure-Devices.NET              | Dostęp IoT Hub (wymagany)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Wzorce adresów URL dla bramy dla Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/azure-stack-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Wzorce adresów URL dla obliczeń dla Azure Government

| Wzorzec adresu URL                      | Składnik lub funkcjonalność                     |  
|----------------------------------|---------------------------------------------|
| https: \/ /MCR.Microsoft.com<br></br>https:// \* . CDN.MSCR.com | Rejestr kontenerów firmy Microsoft (wymagany)               |
| https:// \* . Azure-Devices.us              | Dostęp IoT Hub (wymagany)           |
| https:// \* . azurecr.us                    | Rejestry kontenerów osobistych i innych firm (opcjonalnie) | 

## <a name="internet-bandwidth"></a>Przepustowość internetowa

[!INCLUDE [Internet bandwidth](../../includes/azure-stack-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Uwagi dotyczące ustalania wielkości obliczeń

Korzystaj z Twojego środowiska podczas opracowywania i testowania rozwiązania, aby upewnić się, że na urządzeniu Azure Stack Edge mini R jest wystarczająca pojemność i uzyskasz optymalną wydajność z poziomu urządzenia.

Czynniki, które należy wziąć pod uwagę, obejmują:

- **Specyficzne dla kontenerów** — Pomyśl o następujących kwestiach.

    - Jaki jest Twój kontener? Ile pamięci, magazynu i procesora CPU jest zużywanych przez kontener?
    - Ile kontenerów znajduje się w obciążeniu? Może istnieć wiele lekkich kontenerów, a niektóre z nich wymagają dużej ilości zasobów.
    - Jakie zasoby są przydzieleni do tych kontenerów, a jakie zasoby zużywają (wpływ)?
    - Ile warstw ma udział w kontenerach? Obrazy kontenerów to zbiór plików zorganizowanych w stos warstw. W przypadku obrazu kontenera należy określić liczbę warstw i ich rozmiary do obliczenia zużycia zasobów.
    - Czy istnieją kontenery nieużywane? Zatrzymany kontener nadal zajmuje miejsce na dysku.
    - W którym języku są zapisywane swoje kontenery?
- **Rozmiar przetworzonych danych** — ilość danych, które będą przetwarzane przez kontenery? Czy te dane będą zużywać miejsce na dysku, czy dane zostaną przetworzone w pamięci?
- **Oczekiwana wydajność** — jakie są odpowiednie charakterystyki wydajności rozwiązania? 

Aby zrozumieć i udoskonalić wydajność rozwiązania, można użyć:

- Metryki obliczeń dostępne w Azure Portal. Przejdź do zasobu Azure Stack Edge, a następnie przejdź do pozycji **monitorowanie > metryki**. Zapoznaj się z wartością procentową **wykorzystania pamięci obliczeniowej** i **szybkością obliczeniową (%)** w celu zrozumienia dostępnych zasobów i sposobu ich wykorzystania przez zasoby.
- Polecenia monitorowania dostępne za pośrednictwem interfejsu programu PowerShell urządzenia, takie jak:

    - `dkr` Statystyka umożliwiająca uzyskanie strumienia na żywo statystyk użycia zasobów. Polecenie obsługuje procesor CPU, użycie pamięci, limit pamięci i metryki operacji we/wy sieci.
    - `dkr system df` , aby uzyskać informacje dotyczące ilości użytego miejsca na dysku. 
    - `dkr image [prune]` Aby oczyścić nieużywane obrazy i zwolnić miejsce.
    - `dkr ps --size` do wyświetlania przybliżonego rozmiaru działającego kontenera. 

    Aby uzyskać więcej informacji na temat dostępnych poleceń, przejdź do pozycji [ Debuguj Kubernetes problemy](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge).

Na koniec upewnij się, że masz pewność, że Twoje rozwiązanie jest sprawdzane w zestawie danych, i wykorzystaj wydajność na Azure Stack Edge w systemie.

## <a name="next-step"></a>Następny krok

- [Wdrażanie Azure Stack Edge](azure-stack-edge-placeholder.md)
