---
title: Skonfiguruj ustawienia serwera proxy usługi mobilności na platformie Azure na potrzeby odzyskiwania po awarii platformy Azure | Microsoft Docs
description: Zawiera szczegółowe informacje na temat sposobu konfigurowania usługi mobilności, gdy klienci używają serwera proxy w środowisku źródłowym.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 429ffcab147142ae2e96de13b7c9e1e5ee1ac7ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86133231"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>Skonfiguruj ustawienia serwera proxy usługi mobilności na platformie Azure na potrzeby odzyskiwania po awarii platformy Azure

Ten artykuł zawiera wskazówki dotyczące dostosowywania konfiguracji sieci na docelowej maszynie wirtualnej platformy Azure w przypadku replikowania i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do innego przy użyciu [Azure Site Recovery](site-recovery-overview.md).

Celem tego dokumentu jest dostarczenie instrukcji dotyczących konfigurowania ustawień serwera proxy dla usługi mobilności Azure Site Recovery w scenariuszu odzyskiwania po awarii na platformie Azure. 

Serwery proxy to bramy sieci, które zezwalają na połączenia sieciowe i nie zezwalają na nie. Zazwyczaj serwer proxy jest komputerem spoza komputera klienckiego, który próbuje uzyskać dostęp do punktów końcowych sieci. Lista obejścia pozwala klientowi na nawiązywanie połączeń bezpośrednio z punktami końcowymi bez przechodzenia przez serwer proxy. Opcjonalnie można ustawić nazwę użytkownika i hasło dla serwera proxy za pomocą administratorów sieci, aby tylko uwierzytelnieni klienci mogli korzystać z serwera proxy. 

## <a name="before-you-start"></a>Przed rozpoczęciem

Dowiedz się, jak Site Recovery zapewnia odzyskiwanie po awarii w [tym scenariuszu](azure-to-azure-architecture.md).
Zapoznaj się ze [wskazówkami dotyczącymi sieci](azure-to-azure-about-networking.md) podczas replikowania i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do innego przy użyciu [Azure Site Recovery](site-recovery-overview.md).
Upewnij się, że serwer proxy jest odpowiednio skonfigurowany w zależności od potrzeb organizacji.

## <a name="configure-the-mobility-service"></a>Konfigurowanie usługi mobilności

Usługa mobilności obsługuje tylko nieuwierzytelnione serwery proxy. Zapewnia dwa sposoby wprowadzania szczegółów serwera proxy na potrzeby komunikacji z Site Recovery punktami końcowymi. 

### <a name="method-1-auto-detection"></a>Metoda 1. wykrywanie automatycznego

Usługa mobilności wykrywa ustawienia serwera proxy z ustawień środowiska lub ustawień programu IE (tylko system Windows) podczas włączania replikacji. 

- System operacyjny Windows: podczas włączania replikacji usługa mobilności wykrywa ustawienia serwera proxy zgodnie z konfiguracją w programie Internet Explorer dla użytkownika systemu lokalnego. Aby skonfigurować serwer proxy dla lokalnego konta systemowego, administrator może używać PsExec do uruchamiania wiersza polecenia, a następnie programu Internet Explorer. 
- System operacyjny Windows: ustawienia serwera proxy są skonfigurowane jako zmienne środowiskowe http_proxy i no_proxy. 
- System operacyjny Linux: ustawienia serwera proxy są konfigurowane w/etc/profile lub/etc/environment jako zmienne środowiskowe http_proxy, no_proxy. 
- Ustawienia autowykrytego serwera proxy są zapisywane w pliku konfiguracji serwera proxy usługi mobilności ProxyInfo. conf 
- Domyślna lokalizacja ProxyInfo. conf 
    - Windows: C:\ProgramData\Microsoft witryny Azure Recovery\Config\ProxyInfo.conf 
    - Linux:/usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>Metoda 2. Udostępnianie niestandardowych ustawień serwera proxy aplikacji

W takim przypadku klient udostępnia niestandardowe ustawienia serwera proxy aplikacji w pliku konfiguracji usługi mobilności ProxyInfo. conf. Ta metoda umożliwia klientom udostępnianie serwera proxy tylko dla usługi mobilności lub innego serwera proxy dla Azure Site Recoveryej usługi mobilności niż serwer proxy (lub brak serwera proxy) dla pozostałych aplikacji na komputerze.

## <a name="proxy-template"></a>Szablon serwera proxy
ProxyInfo. conf zawiera następujący szablon [proxy] Address = http://1.2.3.4 port = 5678 BypassList = hypervrecoverymanager. windowsazure. com, Login. microsoftonline. com, BLOB. Core. Windows. NET. BypassList nie obsługuje symboli wieloznacznych, takich jak "*. windows.net", ale podawanie windows.net jest wystarczające do obejścia. 

## <a name="next-steps"></a>Następne kroki:
- Odczytaj [wskazówki dotyczące sieci](./azure-to-azure-about-networking.md)  dotyczące replikacji maszyn wirtualnych platformy Azure.
- Wdróż odzyskiwanie po awarii przez [replikowanie maszyn wirtualnych platformy Azure](./azure-to-azure-quickstart.md).
