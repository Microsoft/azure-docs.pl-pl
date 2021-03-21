---
title: Przegląd App Service Environment
description: Omówienie App Service Environment
author: ccompy
ms.assetid: 3d37f007-d6f2-4e47-8e26-b844e47ee919
ms.topic: article
ms.date: 03/02/2021
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 23b23340550ded3642d19500270f06cfb6faf8cb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735101"
---
# <a name="app-service-environment-overview"></a>Przegląd App Service Environment 

> [!NOTE]
> Ten artykuł dotyczy App Service Environment v3 (wersja zapoznawcza)
> 

Azure App Service Environment to funkcja usługi Azure App Service udostępniająca w pełni izolowane, dedykowane środowisko do bezpiecznego uruchamiania aplikacji usługi App Service w dużej skali. Ta funkcja umożliwia hostowanie:

- Aplikacji internetowych systemu Windows
- Aplikacji internetowych systemu Linux
- Kontenery platformy Docker
- Funkcje

Środowiska App Service Environment (ASE) są odpowiednie dla obciążeń aplikacji o następujących wymaganiach:

- Wysoka Skala.
- Izolacja i bezpieczny dostęp do sieci.
- Wysoki poziom użycia pamięci.
- Wysokie żądania na sekundę (RPS pliku). Można utworzyć wiele środowisk ASE w jednym regionie świadczenia usługi Azure lub w wielu regionach świadczenia usługi Azure. Ta elastyczność sprawia, że środowisk ASE idealnie nadaje się do skalowania aplikacji bezstanowych z wysokim RPS pliku wymaganiem.

Aplikacje hosta ASE są używane tylko przez jednego klienta i w jednym z ich sieci wirtualnych. Klienci mają precyzyjną kontrolę nad przychodzącym i wychodzącym ruchem sieciowym aplikacji. Aplikacje mogą nawiązywać bezpieczne szybkie połączenia za pośrednictwem sieci VPN z zasobami lokalnymi firmy.

ASEv3 z własną warstwą cenową, wyizolowaną v2.
Środowiska App Service w wersji 3 zapewniają otoczenie w celu zabezpieczenia aplikacji w podsieci sieci i zapewnia własne prywatne wdrożenie Azure App Service.
Za pomocą wielu środowisk ASE można realizować skalowanie w poziomie. Dostęp aplikacji działających w środowiskach ASE może być kontrolowany za pomocą bram na urządzeniach nadrzędnych, takich jak zapory aplikacji internetowych (WAF). Aby uzyskać więcej informacji, zobacz temat Zapora aplikacji internetowej (WAF).

## <a name="usage-scenarios"></a>Scenariusze użycia

App Service Environment ma wiele przypadków użycia, w tym:

- Wewnętrzne aplikacje biznesowe
- Aplikacje, które wymagają więcej niż 30 wystąpień ASP
- Pojedynczy system dzierżawców spełniający wymagania wewnętrzne dotyczące zgodności lub zabezpieczeń
- Hosting aplikacji izolowanych przez sieć
- Aplikacje wielowarstwowe

Istnieje wiele funkcji sieciowych, które umożliwiają aplikacjom z wielodostępności App Service dostęp do zasobów izolowanych przez sieć lub przełączać się do sieci. Te funkcje są włączane na poziomie aplikacji.  W środowisku ASE nie ma dodatkowej konfiguracji dla aplikacji, które mają być w sieci wirtualnej. Aplikacje są wdrażane w izolowanym środowisku sieciowym, które jest już w sieci wirtualnej. W oparciu o aplikacje izolowane sieci hostingowe, jest to również system z jedną dzierżawą. Nie ma innych klientów korzystających ze środowiska ASE. Jeśli naprawdę potrzebujesz kompletnego wątku izolacji, możesz również wdrożyć środowisko ASE na dedykowanym sprzęcie. Między hostingiem izolowanych aplikacji sieci, jedną dzierżawą i możliwością 

## <a name="dedicated-environment"></a>Dedykowane środowisko
Środowisko ASE jest przeznaczone wyłącznie dla jednej subskrypcji i może hostować 200 ogólnych wystąpień planu App Service w wielu planach App Service. Słowo "wystąpienie" odnosi się do App Service skalowania w poziomie. Każde wystąpienie jest równoważne roli procesu roboczego. Chociaż środowisko ASE może mieć 200 całkowitej liczby wystąpień, jeden izolowany plan App Service w wersji 2 może zawierać 100 wystąpień. Środowisko ASE może przechowywać dwa App Service plany z 100 wystąpieniami w każdym, 200 App Serviceych planach z jednym wystąpieniem, lub wszystko między nimi.

Środowisko ASE składa się z frontonów i procesów roboczych. Frontony są odpowiedzialne za zakończenia połączeń HTTP/HTTPS i automatyczne równoważenie obciążenia żądań aplikacji w środowisku ASE. Frontony są automatycznie dodawane w przypadku skalowania w poziomie planów usługi App Service w środowisku ASE.

Procesy robocze to role, które hostują aplikacje klienta. Procesy robocze są dostępne w trzech stałych rozmiarach:

- Dwa vCPU/8 GB pamięci RAM
- Cztery vCPU/16 GB pamięci RAM
- Osiem vCPU/32 GB pamięci RAM

Klienci nie muszą zarządzać frontonami i pracownikami. Cała infrastruktura jest automatycznie. Gdy w środowisku ASE są tworzone lub skalowane plany usługi App Service, wymagane elementy infrastruktury są odpowiednio dodawane lub usuwane.

Istnieje opłata za izolowane wystąpienia planu App Service w wersji 2. Jeśli w środowisku ASE nie ma żadnych planów App Service, zostanie naliczona opłata, ponieważ istniała jedna App Service plan z jednym wystąpieniem dwóch podstawowych procesów roboczych.

## <a name="virtual-network-support"></a>Obsługa sieci wirtualnej
Funkcja ASE to wdrożenie Azure App Service bezpośrednio do Azure Resource Manager sieci wirtualnej klienta. Środowisko ASE zawsze istnieje w podsieci sieci wirtualnej. Za pomocą funkcji zabezpieczeń sieci wirtualnych można sterować przychodzącą i wychodzącą komunikacją sieciową używanych aplikacji.

Sieciowe grupy zabezpieczeń ograniczają komunikację sieciową przychodzącą do podsieci, w której znajduje się środowisko ASE. Za pomocą tych grup można uruchamiać aplikacje za urządzeniami i usługami nadrzędnymi, takimi jak zapora aplikacji internetowych i sieciowi dostawcy SaaS.

Aplikacje często muszą również uzyskiwać dostęp do zasobów firmy, takich jak wewnętrzne bazy danych i usługi internetowe. Jeśli środowisko ASE wdrożono w sieci wirtualnej mającej połączenie VPN z siecią lokalną, aplikacje w tym środowisku mają dostęp do zasobów lokalnych. Jest tak niezależnie od tego, czy połączenie VPN jest typu lokacja-lokacja, czy Azure ExpressRoute.

## <a name="preview"></a>Wersja zapoznawcza
App Service Environment V3 jest w publicznej wersji zapoznawczej.  Niektóre funkcje są dodawane w trakcie postępu w wersji zapoznawczej. Obecne ograniczenia ASEv3 obejmują:

- Brak możliwości skalowania planu App Service poza wystąpieniami 50
- Brak możliwości pobrania kontenera z rejestru prywatnego
- Niezdolność do obecnie nieobsługiwanego App Service funkcji do przechodzenia przez sieć wirtualną klienta
- Brak zewnętrznego modelu wdrażania z dostępnym punktem końcowym z Internetu
- Brak obsługi wiersza polecenia (AZ CLI i PowerShell)
- Brak możliwości uaktualnienia z ASEv2 do ASEv3
- Brak obsługi FTP
- Brak obsługi niektórych funkcji App Service przechodzących przez sieć wirtualną klienta. Tworzenie kopii zapasowej/przywracanie, Key Vault odwołań w ustawieniach aplikacji, korzystanie z prywatnego rejestru kontenerów i rejestrowanie diagnostyczne do magazynu nie będzie działać z punktami końcowymi usługi ani prywatnymi punktami końcowymi
    
### <a name="asev3-preview-architecture"></a>Architektura wersji zapoznawczej ASEv3
W programie ASEv3 w wersji zapoznawczej środowisko ASE będzie używać prywatnych punktów końcowych do obsługi ruchu przychodzącego. Prywatny punkt końcowy zostanie zamieniony na usługi równoważenia obciążenia w wersji załadunkowej. W wersji zapoznawczej środowisko ASE nie będzie wbudowane w obsługę punktu końcowego dostępnego dla Internetu. Do tego celu można dodać Application Gateway. Środowisko ASE wymaga zasobów w dwóch podsieciach.  Ruch przychodzący będzie przepływać przez prywatny punkt końcowy. Prywatny punkt końcowy może być umieszczony w dowolnej podsieci, tak długo, jak ma dostępny adres, który może być używany przez prywatne punkty końcowe.  Podsieć wychodząca musi być pusta i delegowana do firmy Microsoft. Web/hostingEnvironments. W przypadku użycia przez środowisko ASE nie można używać podsieci wychodzącej dla żadnych innych elementów.

W przypadku korzystania z programu ASEv3 nie ma wymagań dotyczących sieci przychodzących lub wychodzących w podsieci środowiska ASE. Ruch można kontrolować za pomocą sieciowych grup zabezpieczeń i tabel tras i ma wpływ tylko na ruch aplikacji. Nie usuwaj prywatnego punktu końcowego skojarzonego z Twoim środowiskiem ASE, ponieważ nie można cofnąć tej akcji. Prywatny punkt końcowy używany dla środowiska ASE jest używany dla wszystkich aplikacji w środowisku ASE. 
