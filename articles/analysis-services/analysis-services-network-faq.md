---
title: Często zadawane pytania dotyczące Analysis Services łączności sieciowej | Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania dotyczące Analysis Services łączności sieciowej.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b60cf34e8efed2ed63b6e35cfaf7445edb701610
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "82838505"
---
# <a name="frequently-asked-questions-about-analysis-services-network-connectivity"></a>Często zadawane pytania dotyczące łączności sieciowej usług Analysis Services

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące łączenia się z kontami magazynu, źródłami danych, zaporami i adresami IP.

## <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

**Pytanie** — jak utworzyć kopię zapasową i przywrócić przy użyciu konta magazynu, które znajduje się za zaporą?   
**Odpowiedź** — Azure Analysis Services nie używa stałych adresów IP ani tagów usługi. Zakres adresów IP używanych przez serwery Analysis Services może należeć do zakresu adresów IP w *regionie świadczenia usługi Azure*. Ponieważ adresy IP serwerów są zmienne i mogą ulec zmianie w czasie, reguły zapory muszą zezwalać na cały zakres adresów IP regionów platformy Azure, w których znajduje się serwer.

**Pytanie** — moje konto usługi Azure Storage znajduje się w innym regionie niż mój serwer Analysis Services. Jak mogę skonfigurować ustawienia zapory konta magazynu?   
**Odpowiedź** — Jeśli konto magazynu znajduje się w innym regionie, skonfiguruj ustawienia zapory konta magazynu, aby zezwolić na dostęp z **wybranych sieci**. W obszarze **zakres adresów** zapory Określ zakres adresów IP dla regionu, w którym znajduje się serwer Analysis Services. Aby uzyskać zakresy adresów IP dla regionów świadczenia usługi Azure, zobacz [zakres adresów IP i Tagi usług platformy Azure — chmura publiczna](https://www.microsoft.com/download/details.aspx?id=56519). Konfigurowanie ustawień zapory konta magazynu w celu zezwalania na dostęp ze wszystkich sieci jest obsługiwane, jednak wybranie opcji wybrane sieci i określenie zakresu adresów IP jest preferowane. 

**Pytanie** — moje konto usługi Azure Storage znajduje się w tym samym regionie, w którym znajduje się serwer Analysis Services. Jak można skonfigurować ustawienia zapory konta magazynu?   
**Odpowiedź** — ponieważ serwer Analysis Services i konto magazynu znajdują się w tym samym regionie, komunikacja między nimi odbywa się przy użyciu wewnętrznych zakresów adresów IP, dlatego Konfigurowanie zapory do używania wybranych sieci i określanie zakresu adresów IP nie jest obsługiwane. Jeśli zasady organizacji wymagają zapory, należy ją skonfigurować tak, aby zezwalała na dostęp ze wszystkich sieci.


## <a name="data-source-connections"></a>Połączenia ze źródłami danych

**Pytanie** — mam sieć wirtualną dla mojego systemu źródła danych. Jak zezwolić serwerom Analysis Services na dostęp do bazy danych z sieci wirtualnej?   
**Odpowiedź** — Azure Analysis Services nie może dołączyć do sieci wirtualnej. Najlepszym rozwiązaniem jest zainstalowanie i skonfigurowanie lokalnej bramy danych w sieci wirtualnej, a następnie skonfigurowanie serwerów Analysis Services przy użyciu właściwości serwera **AlwaysUseGateway** . Aby dowiedzieć się więcej, zobacz [Korzystanie z bramy dla źródeł danych w usłudze Azure Virtual Network (VNET)](analysis-services-vnet-gateway.md).

**Pytanie** — mam źródłową bazę danych za zaporą. Jak skonfigurować zaporę, aby umożliwić serwerowi Analysis Services dostęp do niego?   
**Odpowiedź** — Azure Analysis Services nie używa stałych adresów IP ani tagów usługi. Zakres adresów IP używanych przez serwery Analysis Services może należeć do zakresu adresów IP w *regionie świadczenia usługi Azure*. Musisz podać *pełny zakres* adresów IP dla regionu platformy Azure serwera w regułach zapory dla źródłowej bazy danych. Inne, a potencjalnie bardziej bezpieczne, alternatywą jest skonfigurowanie lokalnej bramy danych. Następnie można skonfigurować serwery Analysis Services przy użyciu [Właściwości serwera AlwaysUseGateway](analysis-services-vnet-gateway.md#configure-alwaysusegateway-property), a następnie upewnić się, że lokalna Brama danych ma adres IP dozwolony przez reguły zapory źródła danych.

## <a name="azure-apps-with-ip-address"></a>Aplikacje platformy Azure z adresem IP

**Pytanie** — używam aplikacji opartej na platformie Azure (na przykład Azure Functions, Azure Data Factory) z adresem IP, który zmienia się na bieżąco. Jak mogę zarządzać regułami zapory Azure Analysis Services, aby dynamicznie zezwalać na adres IP, na którym jest wykonywana moja aplikacja?   
**Odpowiedź** — Azure Analysis Services nie obsługuje prywatnych linków, sieci wirtualnych ani tagów usługi. Istnieją rozwiązania typu Open Source (na przykład https://github.com/mathwro/Scripts/blob/master/Azure/AllowAzure-AnalysisServer.ps1) wykrywające adres IP aplikacji klienckiej oraz automatyczne i tymczasowe aktualizowanie reguł zapory.


## <a name="next-steps"></a>Następne kroki

[Instalowanie i Konfigurowanie lokalnej bramy danych](analysis-services-gateway-install.md)   
[Łączenie z lokalnymi źródłami danych za pomocą lokalnej bramy danych](analysis-services-gateway.md)   
[Używanie bramy dla źródeł danych w usłudze Azure Virtual Network (VNet)](analysis-services-vnet-gateway.md)
