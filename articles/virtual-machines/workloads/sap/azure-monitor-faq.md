---
title: Często zadawane pytania — Azure Monitor dla rozwiązań SAP | Microsoft Docs
description: W tym artykule znajdziesz odpowiedzi na często zadawane pytania dotyczące Azure Monitor rozwiązań SAP.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 4b84f07f637b0a8925dec96c8c609101247ffd64
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377128"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>Azure Monitor dla rozwiązań SAP — często zadawane pytania (wersja zapoznawcza)
## <a name="frequently-asked-questions"></a>Często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące Azure Monitor rozwiązań SAP.  

 - **Czy muszę płacić za Azure Monitor dla rozwiązań SAP?**  
Nie ma opłaty licencyjnej za Azure Monitor dla rozwiązań SAP.  
Jednak klienci są odpowiedzialni za poniesienie kosztów składników zarządzanej grupy zasobów.  

 - **W których regionach ta usługa jest dostępna w publicznej wersji zapoznawczej?**  
W publicznej wersji zapoznawczej ta usługa będzie dostępna w regionach Wschodnie stany USA 2, Zachodnie stany USA 2, Wschodnie stany USA i Europa Zachodnia.  

 - **Czy muszę podać uprawnienia, aby umożliwić wdrożenie zarządzanej grupy zasobów w subskrypcji?**  
Nie, jawne uprawnienia nie są wymagane.  

 - **Gdzie znajduje się maszyna wirtualna modułu zbierającego?**  
W momencie wdrażania zasobu Azure Monitor dla rozwiązań SAP zalecamy klientom wybranie tej samej sieci wirtualnej do monitorowania zasobu jako SAP HANA serwera. W związku z tym zaleca się, aby maszyna wirtualna modułu zbierającego znajdowała się w tej samej sieci wirtualnej co SAP HANA serwera. Jeśli klienci będą korzystać z bazy danych spoza platformy HANA, maszyna wirtualna modułu zbierającego będzie znajdować się w tej samej sieci wirtualnej co baza danych spoza platformy HANA.  

 - **Które wersje platformy HANA są obsługiwane?**  
HANA 1.0 SPS 12 (wersja 120 lub wyższa) i HANA 2.0 SPS03 lub wyższa  

 - **Które konfiguracje wdrażania platformy HANA są obsługiwane?**  
Obsługiwane są następujące konfiguracje:
   - Jeden węzeł (skalowanie w górę) i wiele węzłów (skalowanie w górę)  
   - Pojedynczy kontener bazy danych (HANA 1.0 SPS 12) i wiele kontenerów baz danych (HANA 1.0 SPS 12 lub HANA 2.0)  
   - Automatyczny tryb failover hosta (n+1) i moduł HSR  

 - **Które SQL Server są obsługiwane?**  
SQL Server 2012 z dodatkiem SP4 lub wyższym.  

 - **Które SQL Server konfiguracji są obsługiwane?**  
Obsługiwane są następujące konfiguracje:
   - Wystąpienia autonomiczne domyślne lub nazwane na maszynie wirtualnej  
   - Wystąpienia klastrowane lub wystąpienia w konfiguracji AlwaysOn, gdy jest to nazwa wirtualna zasobu klastra lub nazwa odbiornika AlwaysOn. Obecnie nie są zbierane metryki specyficzne dla klastra lub alwayson    
   - Azure SQL Database (PAAS) nie jest obecnie obsługiwane  

 - **Co się stanie w przypadku przypadkowego usunięcia zarządzanej grupy zasobów?**  
Zarządzana grupa zasobów jest domyślnie zablokowana. W związku z tym prawdopodobieństwo przypadkowego usunięcia zarządzanej grupy zasobów przez klientów jest pomniejszone.  
Jeśli klient usunie zarządzaną grupę zasobów, Azure Monitor dla rozwiązań SAP przestanie działać. Klient będzie musiał wdrożyć nowy zasób usługi Azure Monitor dla rozwiązań SAP i zacząć od nowa.  

 - **Jakich ról potrzebuję w subskrypcji platformy Azure, aby wdrożyć Azure Monitor dla rozwiązań SAP zasobów?**  
Rola współautora.  

 - **Jaka jest umowa SLA dla tego produktu?**  
Wersje zapoznawcze są wykluczone z umów dotyczących poziomu usług. Przeczytaj pełny okres licencji za pośrednictwem Azure Monitor dla rozwiązań SAP marketplace.  

 - **Czy mogę monitorować całe swoje krajobrazy za pomocą tego rozwiązania?**  
Obecnie można monitorować bazę danych HANA, podstawową infrastrukturę, klaster wysokiej dostępności, program Microsoft SQL Server, dostępność oprogramowania SAP Netweaver i metryki dostępności wystąpienia aplikacji SAP w publicznej wersji zapoznawczej.  

 - **Czy ta usługa zastępuje menedżera rozwiązań SAP?**  
Nie. Klienci nadal mogą używać programu SAP Solution Manager do monitorowania procesów biznesowych.  

 - **Jaka jest wartość tej usługi w przeciwieństwie do tradycyjnych rozwiązań, takich jak SAP HANA Panel/Studio?**  
Azure Monitor dla rozwiązań SAP nie jest specyficzna dla bazy danych HANA. Azure Monitor dla rozwiązań SAP obsługuje również program AnyDB.  

- **Które wersje oprogramowania SAP NetWeaver są obsługiwane?**  
OPROGRAMOWANIE SAP NetWeaver 7.0 lub wyższe.  

- **Które konfiguracje oprogramowania SAP NetWeaver są obsługiwane?**  
Obsługuje konfiguracje ABAP, Java i SAP NetWeaver Application Server z dwoma stosami.

- **Dlaczego należy wyłączyć ochronę metod monitorowania aplikacji SAP NetWeaver?**  
W wersjach SAP >= 7.3 większość metod usługi internetowej jest domyślnie chroniona. Aby pobrać metryki dostępności i wydajności przez wywołanie tych metod, należy wyłączyć ochronę następujących metod: GetQueueStatistic, ABAPGetWPTable, GetProcessList, EnqGetStatistic i GetSystemInstancelist.

- **Czy istnieje ryzyko związane z cofaniem ochrony webmethodów SAPCONTROL?**  
Ogólnie rzecz biorąc, niechronienie elementów webmethod [](https://launchpad.support.sap.com/#/notes/1439348)SAPCONTROL nie stanowi zagrożenia dla bezpieczeństwa, jednak jeśli klienci chcą ograniczyć/uniemożliwić dostęp do niechronionych elementów webmethod za pośrednictwem portów serwera (5XX13/5XX14) serwera sapstartsrv, można to zrobić, dodając filtr w liście ACL (SAP Access Control List), w uwagach [systemu operacyjnego](https://service.sap.com/sap/support/notes/1495075) opisano wymaganą konfigurację, aby to osiągnąć. 

- **Czy muszę ponownie uruchomić wystąpienia sap po przeprowadzeniu konfiguracji systemu w celu skonfigurowania dostawcy SAP NetWeaver?**  
Tak, po zmianie konfiguracji systemu SAP w przypadku niechronionych metod należy ponownie uruchomić odpowiednie systemy SAP, aby upewnić się, że zmiany konfiguracji zostały zaktualizowane.  

## <a name="next-steps"></a>Następne kroki

- Utwórz swoją pierwszą Azure Monitor zasobów rozwiązań SAP.
