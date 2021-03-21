---
title: Microsoft Azure Data Box Gateway wymagania systemowe | Microsoft Docs
description: Dowiedz się więcej o wymaganiach dotyczących oprogramowania i sieci dla Azure Data Box Gateway
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: e7c8653b39a3e0333ff6e98783a6e9a1437dba22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739215"
---
# <a name="azure-data-box-gateway-system-requirements"></a>Wymagania systemowe Azure Data Box Gateway

W tym artykule opisano ważne wymagania systemowe dotyczące rozwiązania Data Box Gateway Microsoft Azure i dla klientów nawiązujących połączenie z Azure Data Box Gateway. Zalecamy dokładne zapoznanie się z informacjami przed wdrożeniem Data Box Gateway, a następnie odwoływanie się do niego w miarę potrzeb podczas wdrażania i kolejnej operacji. 

Wymagania systemowe dla Data Box Gateway urządzenia wirtualnego obejmują:

- **Wymagania dotyczące oprogramowania dla hostów** — zawiera opis obsługiwanych platform, przeglądarek dla lokalnego interfejsu użytkownika konfiguracji, klientów SMB i wszelkich dodatkowych wymagań dla hostów, które łączą się z urządzeniem.
- **Wymagania dotyczące sieci dla urządzenia** — zawiera informacje o wymaganiach sieciowych dla operacji urządzenia wirtualnego.


## <a name="specifications-for-the-virtual-device"></a>Specyfikacje dla urządzenia wirtualnego

Podstawowy system hosta dla Data Box Gateway jest w stanie przeznaczyć następujące zasoby, aby udostępnić urządzenie wirtualne:

| Specyfikacje                                          | Opis              |
|---------------------------------------------------------|--------------------------|
| Procesory wirtualne (rdzenie)   | Minimum 4 |
| Pamięć  | Minimum 8 GB. Zdecydowanie zalecamy co najmniej 16 GB. |
| Dostępność|Jeden węzeł|
| Dyski| Dysk systemu operacyjnego: 250 GB <br> Dysk danych: minimum 2 TB, alokowany elastycznie, wymagane dyski SSD|
| Interfejsy sieciowe|Co najmniej 1 wirtualny interfejs sieciowy|


## <a name="supported-os-for-clients-connected-to-device"></a>Obsługiwane systemy operacyjne dla klientów podłączonych do urządzenia

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Obsługiwane protokoły dla klientów uzyskujących dostęp do urządzenia

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-virtualization-platforms-for-device"></a>Obsługiwane platformy wirtualizacji dla urządzenia

| **System operacyjny/platforma**  |**Versions** (Wersje)   |**Uwagi**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016 <br> 2019 |         |
|VMware ESXi     | 6.0 <br> 6.5 <br> 6.7       |Narzędzia VMware nie są obsługiwane.         |


## <a name="supported-storage-accounts"></a>Obsługiwane konta magazynu

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]


## <a name="supported-storage-types"></a>Obsługiwane typy magazynu

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Obsługiwane przeglądarki dla lokalnego interfejsu użytkownika sieci Web

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Wymagania dotyczące portów sieciowych

Poniższa tabela zawiera listę portów, które należy otworzyć w zaporze, aby umożliwić obsługę ruchu SMB, chmury lub zarządzania. W tej tabeli *w programie lub w* *ruchu przychodzącym* odwołuje się do kierunku, w którym klient przychodzący żąda dostępu do urządzenia. *Out* lub *wychodzący* odnosi się do kierunku, w którym urządzenie Data Box Gateway wysyła dane zewnętrznie, poza wdrożeniem: na przykład wychodzące do Internetu.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="url-patterns-for-firewall-rules"></a>Wzorce adresów URL dla reguł zapory

Administratorzy sieci mogą często konfigurować zaawansowane reguły zapory na podstawie wzorców adresów URL, aby filtrować ruch przychodzący i wychodzący. Urządzenie Data Box Gateway i Usługa Data Box Gateway zależą od innych aplikacji firmy Microsoft, takich jak Azure Service Bus, Azure Active Directory Access Control, konta magazynu i serwery Microsoft Update. Wzorce adresów URL skojarzone z tymi aplikacjami mogą służyć do konfigurowania reguł zapory. Ważne jest, aby zrozumieć, że wzorce adresów URL skojarzone z tymi aplikacjami mogą się zmieniać. To z kolei wymaga od administratora sieci monitorowania i aktualizowania reguł zapory dla Data Box Gateway, jak i w razie potrzeby.

Zalecamy ustawienie reguł zapory dla ruchu wychodzącego na podstawie Data Box Gateway stałych adresów IP, w większości przypadków. Można jednak użyć poniższych informacji, aby ustawić zaawansowane reguły zapory, które są potrzebne do tworzenia bezpiecznych środowisk.

> [!NOTE]
> - Adresy IP urządzeń (źródłowych) powinny zawsze być ustawione na wszystkie interfejsy sieciowe obsługujące chmurę.
> - Docelowe adresy IP powinny być ustawiane na [zakresy adresów IPv4 centrum danych platformy Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-azure-government"></a>Wzorce adresów URL dla Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Przepustowość internetowa

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Następny krok

* [Wdrażanie Azure Data Box Gateway](data-box-gateway-deploy-prep.md)

