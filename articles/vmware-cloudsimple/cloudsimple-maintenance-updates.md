---
title: CloudSimple konserwacja i aktualizacje
titleSuffix: Azure VMware Solution by CloudSimple
description: Opisuje proces usługi CloudSimple na potrzeby zaplanowanej konserwacji i aktualizacji
author: sharaths-cs
ms.author: dikamath
ms.date: 03/04/2021
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 814a901fba065e36ea3ba9e3a6fe1175365de6c5
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120712"
---
# <a name="cloudsimple-maintenance-and-updates"></a>CloudSimple konserwacja i aktualizacje

Środowisko chmury prywatnej zostało zaprojektowane w taki sposób, aby nie było single point of failure.

* Klastry ESXi są skonfigurowane z wysoką dostępnością vSphere (HA). Rozmiary klastrów mają co najmniej jeden węzeł zapasowy na potrzeby odporności.
* Nadmiarowy magazyn podstawowy jest dostarczany przez sieci vSAN, który wymaga co najmniej trzech węzłów w celu zapewnienia ochrony przed pojedynczym awarią. Sieci vSAN można skonfigurować w celu zapewnienia większej odporności dla większych klastrów.
* Maszyny wirtualne programu vCenter, PSC i NSX Manager są skonfigurowane z magazynem RAID-10, aby zapewnić ochronę przed awariami magazynu. Maszyny wirtualne są chronione przed awariami węzłów/sieci przez vSphere HA.
* Hosty ESXi mają nadmiarowe wentylatory i karty sieciowe.
* Przełączniki TOR i spin są skonfigurowane w parach HA w celu zapewnienia odporności.

CloudSimple stale monitoruje następujące maszyny wirtualne pod kątem czasu i dostępności, a także zapewnia dostępność umowy SLA:

* Hosty ESXi
* vCenter
* KOMPUTERÓW
* NSX Manager

CloudSimple również stale monitoruje następujące kwestie dotyczące niepowodzeń:

* Dyski twarde
* Fizyczne porty kart sieciowych
* Serwery
* Wentylatory
* Zasilanie
* Przełączniki
* Przełącz porty

Jeśli dysk lub węzeł ulegnie awarii, nowy węzeł zostanie automatycznie dodany do klastra programu VMware, którego dotyczy usterka, w celu natychmiastowego przywrócenia kondycji.

CloudSimple wykonuje kopię zapasową, utrzymuje i aktualizuje te elementy VMware w chmurach prywatnych:

* ESXi
* Usługi platformy vCenter
* Kontroler
* Sieci vSAN
* NSX

## <a name="back-up-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

CloudSimple Backup obejmuje:

* Nocne przyrostowe kopie zapasowe reguł vCenter, PSC i DVS.
* natywne interfejsy API programu vCenter do tworzenia kopii zapasowych składników w warstwie aplikacji.
* Automatyczna kopia zapasowa przed aktualizacją lub uaktualnieniem oprogramowania do zarządzania VMware.
* Szyfrowanie danych programu vCenter w źródle przed transferem danych przez szyfrowany kanał TLS 1.2 do platformy Azure. Dane są przechowywane w obiekcie blob platformy Azure, w którym są replikowane między regionami.

Możesz zażądać przywrócenia, otwierając [support Request](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Konserwacja

CloudSimple wykonuje kilka typów planowanej konserwacji.

### <a name="backendinternal-maintenance"></a>Zaplecze/konserwacja wewnętrzna

Ta konserwacja zazwyczaj polega na ponownym konfigurowaniu zasobów fizycznych lub instalowaniu poprawek oprogramowania. Nie ma to wpływu na normalne użycie zasobów, które są w trakcie obsługi. W przypadku nadmiarowych kart sieciowych w każdym stojaku fizycznym nie ma to wpływu na normalny ruch sieciowy i operacje chmury prywatnej. Możesz zauważyć, że wpływ na wydajność jest możliwy tylko wtedy, gdy organizacja oczekuje, że w czasie konserwacji ma być używana pełna nadmiarowa przepustowość.

### <a name="cloudsimple-portal-maintenance"></a>Obsługa portalu CloudSimple

Niektóre ograniczone przestojy usługi są wymagane, gdy jest aktualizowana płaszczyzna lub infrastruktura kontroli CloudSimple. Obecnie interwały konserwacji mogą być tak częste jak raz miesięcznie. Częstotliwość powinna odrzucać się w czasie. CloudSimple zapewnia powiadomienia o konserwacji portalu i utrzymuje interwał tak krótki jak to możliwe. W czasie konserwacji portalu następujące usługi kontynuują działanie bez żadnego wpływu:

* Aplikacja i płaszczyzny zarządzania VMware
* dostęp vCenter
* Wszystkie sieci i magazyn
* Cały ruch platformy Azure

### <a name="vmware-infrastructure-maintenance"></a>Konserwacja infrastruktury VMware

Czasami konieczne jest wprowadzenie zmian w konfiguracji infrastruktury VMware.  Obecnie te interwały mogą wystąpić co 1-2 miesięcy, ale częstotliwość powinna odrzucać się w czasie. Ten typ konserwacji można zwykle wykonać bez przerywania normalnego użycia usług CloudSimple. W czasie konserwacji programu VMware następujące usługi nadal działają bez żadnego wpływu:

* Aplikacja i płaszczyzny zarządzania VMware
* dostęp vCenter
* Wszystkie sieci i magazyn
* Cały ruch platformy Azure

## <a name="updates-and-upgrades"></a>Aktualizacje i uaktualnienia

CloudSimple jest odpowiedzialny za zarządzanie cyklem życia oprogramowania VMware (ESXi, vCenter, PSC i NSX) w chmurze prywatnej.

Aktualizacje oprogramowania obejmują:

* **Poprawki**. Poprawki zabezpieczeń lub poprawki błędów wydane przez oprogramowanie VMware.
* **Aktualizacje**. Zmiana wersji pomocniczej składnika stosu VMware.
* **Uaktualnienia**. Główna zmiana wersji składnika stosu VMware.

CloudSimple testuje krytyczną poprawkę zabezpieczeń, gdy tylko staną się dostępne z programu VMware. 

CloudSimple dostarcza kwartalne aktualizacje dla składników oprogramowania VMware. Po udostępnieniu nowej wersji głównej oprogramowania VMware CloudSimple współpracuje z klientami w celu koordynowania odpowiedniego okna obsługi w celu uaktualnienia.

## <a name="next-steps"></a>Następne kroki

[Tworzenie kopii zapasowych maszyn wirtualnych obciążeń przy użyciu Veeam](backup-workloads-veeam.md)
