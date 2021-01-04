---
title: Lokalna Brama danych dla Azure Analysis Services | Microsoft Docs
description: Brama lokalna jest wymagana, jeśli serwer Analysis Services na platformie Azure będzie łączył się z lokalnymi źródłami danych.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6ca96f76287482a445d8a9a1cdc441333b36efbd
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739607"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Łączenie z lokalnymi źródłami danych za pomocą lokalnej bramy danych

Lokalna Brama danych zapewnia bezpieczny transfer danych między lokalnymi źródłami danych i serwerami Azure Analysis Services w chmurze. Oprócz pracy z wieloma serwerami Azure Analysis Services w tym samym regionie, Najnowsza wersja bramy działa również z Azure Logic Apps, Power BI, aplikacjami zaawansowanymi i automatyzacją. Podczas gdy instalowana Brama jest taka sama we wszystkich tych usługach, Azure Analysis Services i Logic Apps mają kilka dodatkowych kroków.

Informacje podane w tym miejscu dotyczą sposobu, w jaki Azure Analysis Services współpracuje z lokalną bramą danych. Aby dowiedzieć się więcej na temat bramy ogólnie i jak współpracuje z innymi usługami, zobacz [co to jest lokalna Brama danych?](/data-integration/gateway/service-gateway-onprem).

Aby uzyskać Azure Analysis Services, należy pobrać Instalatora z bramą po raz pierwszy jest to proces składający się z czterech części:

- **Pobierz i uruchom Instalatora** — ten krok powoduje zainstalowanie usługi bramy na komputerze w organizacji. Logujesz się również na platformie Azure przy użyciu konta w usłudze Azure AD swojej [dzierżawy](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) . Konta B2B (gość) platformy Azure nie są obsługiwane.

- **Rejestrowanie bramy** — w tym kroku należy określić nazwę i klucz odzyskiwania dla bramy i wybrać region, zarejestrować bramę w usłudze bramy w chmurze. Zasób bramy może być zarejestrowany w dowolnym regionie, ale zaleca się, aby znajdował się w tym samym regionie co serwery Analysis Services. 

- **Tworzenie zasobu bramy na platformie Azure** — w tym kroku utworzysz zasób bramy na platformie Azure.

- **Połącz zasób bramy z serwerami** — gdy masz zasób bramy, możesz rozpocząć połączenie z serwerem. Można połączyć wiele serwerów i innych zasobów, pod warunkiem, że znajdują się one w tym samym regionie.

## <a name="installing"></a>Instalowanie

W przypadku instalowania środowiska Azure Analysis Services należy wykonać kroki opisane w temacie [Instalowanie i Konfigurowanie lokalnej bramy danych dla Azure Analysis Services](analysis-services-gateway-install.md). Ten artykuł dotyczy Azure Analysis Services. Zawiera dodatkowe kroki wymagane do skonfigurowania zasobu lokalnej bramy danych na platformie Azure i połączenia serwera Azure Analysis Services z zasobem.

## <a name="connecting-to-a-gateway-resource-in-a-different-subscription"></a>Nawiązywanie połączenia z zasobem bramy w innej subskrypcji

Zalecamy utworzenie zasobu bramy platformy Azure w tej samej subskrypcji co serwer. Można jednak skonfigurować serwery do łączenia się z zasobem bramy w innej subskrypcji. Nawiązywanie połączenia z zasobem bramy w innej subskrypcji nie jest obsługiwane podczas konfigurowania istniejących ustawień serwera lub tworzenia nowego serwera w portalu, ale można go skonfigurować przy użyciu programu PowerShell. Aby dowiedzieć się więcej, zobacz [łączenie zasobu bramy z serwerem](analysis-services-gateway-install.md#connect-gateway-resource-to-server).

## <a name="ports-and-communication-settings"></a>Ustawienia portów i komunikacji

Brama tworzy połączenie wychodzące do usługi Azure Service Bus. Komunikuje się ona na portach wychodzących: TCP 443 (domyślnie), 5671, 5672, 9350 do 9354.  Brama nie wymaga portów przychodzących.

Może być konieczne dołączenie adresów IP do obszaru danych w zaporze. Możesz pobrać [listę adresów IP centrów danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=56519). Ta lista jest aktualizowana co tydzień. Na liście adresów IP centrów danych platformy Azure adresy są wymienione w notacji CIDR. Aby dowiedzieć się więcej, zobacz [Inter-Domain Routing bezklasowy](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Poniżej znajdują się w pełni kwalifikowane nazwy domen używane przez bramę.

| Nazwy domen | Porty wychodzące | Opis |
| --- | --- | --- |
| *.powerbi.com |80 |Protokół HTTP używany do pobierania instalatora. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *. login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Zaawansowane usługi kolejkowania Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350–9354 |Odbiorniki usługi Service Bus Relay korzystające z protokołu TCP (wymaga portu 443 w celu uzyskania tokenu kontroli dostępu) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |80 |Służy do testowania łączności z Internetem, gdy brama jest nieosiągalna przez usługę Power BI. |
| *.microsoftonline-p.com |443 |Używany do uwierzytelniania w zależności od konfiguracji. |
| dc.services.visualstudio.com    |443 |Używane przez AppInsights do zbierania danych telemetrycznych. |

## <a name="next-steps"></a>Następne kroki 

Następujące artykuły są zawarte w ogólnej zawartości lokalnej bramy danych, która dotyczy wszystkich usług obsługiwanych przez bramę:

* [Lokalna brama danych — często zadawane pytania](/data-integration/gateway/service-gateway-onprem-faq)   
* [Korzystanie z aplikacji lokalnej bramy danych](/data-integration/gateway/service-gateway-app)   
* [Administracja na poziomie dzierżawy](/data-integration/gateway/service-gateway-tenant-level-admin)
* [Konfigurowanie ustawień serwera proxy](/data-integration/gateway/service-gateway-proxy)   
* [Dostosowywanie ustawień komunikacji](/data-integration/gateway/service-gateway-communication)   
* [Konfigurowanie plików dziennika](/data-integration/gateway/service-gateway-log-files)   
* [Rozwiązywanie problemów](/data-integration/gateway/service-gateway-tshoot)
* [Monitorowanie i optymalizowanie wydajności bramy](/data-integration/gateway/service-gateway-performance)
