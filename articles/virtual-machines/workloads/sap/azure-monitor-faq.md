---
title: Często zadawane pytania — Azure Monitor dla rozwiązań SAP | Microsoft Docs
description: W tym artykule przedstawiono odpowiedzi na często zadawane pytania dotyczące usługi Azure monitor dla rozwiązań SAP.
author: rdeltcheva
ms.service: virtual-machines
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: f584601c2dcbea989f1b68d9fbac5a7f53a59d86
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91994220"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>Azure monitor for SAP — często zadawane pytania (wersja zapoznawcza)
## <a name="frequently-asked-questions"></a>Często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure monitor dla rozwiązań SAP.  

 - **Czy muszę płacisz za Azure Monitor dla rozwiązań SAP?**  
Za korzystanie z rozwiązań SAP nie jest naliczana opłata licencyjna za Azure Monitor.  
Jednak klienci są zobowiązani do ponoszenia kosztów składników zarządzanej grupy zasobów.  

 - **W jakich regionach ta usługa jest dostępna w publicznej wersji zapoznawczej?**  
W publicznej wersji zapoznawczej ta usługa będzie dostępna w regionach Wschodnie stany USA 2, zachodnie stany USA 2, Wschodnie stany USA i Europa Zachodnia.  

 - **Czy muszę podać uprawnienia zezwalające na wdrożenie zarządzanej grupy zasobów w mojej subskrypcji?**  
Nie, jawne uprawnienia nie są wymagane.  

 - **Gdzie znajduje się maszyna wirtualna modułu zbierającego?**  
W czasie wdrażania Azure Monitor dla zasobów rozwiązań SAP zalecamy, aby klienci wybierali tę samą sieć wirtualną dla zasobu monitorowania jako serwer SAP HANA. Z tego powodu zaleca się, aby maszyna wirtualna modułu zbierającego znajdować się w tej samej sieci wirtualnej co SAP HANA Server. Jeśli klienci korzystają z bazy danych innej niż HANA, maszyna wirtualna modułu zbierającego będzie znajdować się w tej samej sieci wirtualnej co baza danych innej niż HANA.  

 - **Które wersje platformy HANA są obsługiwane?**  
HANA 1,0 SPS 12 (Rev. 120 lub nowszy) oraz HANA 2,0 SPS03 lub nowszy  

 - **Które konfiguracje wdrożenia platformy HANA są obsługiwane?**  
Obsługiwane są następujące konfiguracje:
   - Pojedynczy węzeł (skalowanie w górę) i wiele węzłów (skalowanie w poziomie)  
   - Kontener pojedynczej bazy danych (HANA 1,0 SPS 12) i wiele kontenerów bazy danych (HANA 1,0 SPS 12 lub HANA 2,0)  
   - Przełączenie w tryb failover (n + 1) i HSR  

 - **Które wersje SQL Server są obsługiwane?**  
SQL Server 2012 z dodatkiem SP4 lub nowszym.  

 - **Które konfiguracje SQL Server są obsługiwane?**  
Obsługiwane są następujące konfiguracje:
   - Domyślne lub nazwane wystąpienia autonomiczne na maszynie wirtualnej  
   - Wystąpienia klastrowane lub wystąpienia w konfiguracji AlwaysOn przy użyciu nazwy wirtualnej zasobu klastra lub nazwy odbiornika AlwaysOn. Obecnie nie są zbierane żadne metryki specyficzne dla klastra ani funkcji AlwaysOn    
   - Azure SQL Database (PAAS) nie jest obecnie obsługiwana  

 - **Co się stanie w przypadku przypadkowego usunięcia zarządzanej grupy zasobów?**  
Zarządzana Grupa zasobów jest domyślnie zablokowana. W związku z tym ryzyko przypadkowego usunięcia zarządzanej grupy zasobów przez klientów to minuscule.  
Jeśli klient usunie zarządzaną grupę zasobów, Azure Monitor dla rozwiązań SAP przestanie działać. Klient będzie musiał wdrożyć nowe Azure Monitor dla zasobów rozwiązań SAP i zacząć od nowa.  

 - **Jakie role są potrzebne w mojej subskrypcji platformy Azure w celu wdrożenia Azure Monitor dla zasobu rozwiązań SAP?**  
Rola współautor.  

 - **Co to jest umowa SLA dotycząca tego produktu?**  
Wersje zapoznawcze są wykluczone z umów dotyczących poziomu usług. Przeczytaj pełną licencję za pomocą narzędzia Azure Monitor for SAP Solutions Marketplace Image.  

 - **Czy mogę monitorować cały poziom w tym rozwiązaniu?**  
Obecnie możesz monitorować bazę danych platformy HANA, podstawową infrastrukturę, klaster o wysokiej dostępności i program Microsoft SQL Server w publicznej wersji zapoznawczej.  

 - **Czy ta usługa zastępuje Menedżera rozwiązań SAP?**  
Nie. Klienci nadal mogą korzystać z Menedżera rozwiązań SAP do monitorowania procesów firmowych.  

 - **Jaka jest wartość tej usługi w porównaniu z tradycyjnymi rozwiązaniami, takimi jak SAP HANA Panel sterowania/Studio?**  
Azure Monitor dla rozwiązań SAP nie jest specyficzna dla bazy danych HANA. Azure Monitor dla rozwiązań SAP obsługuje również AnyDB.  

## <a name="next-steps"></a>Następne kroki

- Utwórz pierwszy Azure Monitor dla zasobu rozwiązań SAP.
