---
title: Jak Cloud Foundry integruje się z platformą Azure | Microsoft Docs
description: Opisuje, jak Cloud Foundry mogą korzystać z usług platformy Azure w celu ulepszenia środowiska korporacyjnego
services: virtual-machines-linux
documentationcenter: ''
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: f3b84ba1c3571e3660d1d71a0167a7489c6ec4ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82145125"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Integracja usługi Cloud Foundry z platformą Azure

[Cloud Foundry](https://docs.cloudfoundry.org/) jest platformą PaaS działającą na platformie Cloud Providers IaaS. Oferuje spójne środowisko wdrażania aplikacji między dostawcami chmury. Można ją również zintegrować z różnymi usługami platformy Azure, korzystając z wysokiej jakości korporacyjnej, skalowalności i oszczędności kosztów.
Istnieją [6 podsystemów Cloud Foundry](https://docs.cloudfoundry.org/concepts/architecture/), które mogą być elastycznie skalowane w trybie online, w tym: Routing, uwierzytelnianie, zarządzanie cyklem życia aplikacji, zarządzanie usługami, obsługa komunikatów i monitorowanie. Dla każdego podsystemu można skonfigurować Cloud Foundry do korzystania z usługi platformy Azure z korespondentem. 

![Cloud Foundry w architekturze integracji platformy Azure](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. Wysoka dostępność i skalowalność
### <a name="managed-disk"></a>Dysk zarządzany
Bosh używa platformy Azure CPI (interfejsu dostawcy usług w chmurze) do tworzenia i usuwania tych dysków. Domyślnie są używane dyski niezarządzane. Klient musi ręcznie utworzyć konta magazynu, a następnie skonfigurować konta w plikach manifestu CF. Wynika to z ograniczenia liczby dysków na konto magazynu.
Teraz [dysk zarządzany](https://azure.microsoft.com/services/managed-disks/) jest dostępny, oferuje zarządzany bezpieczny i niezawodny magazyn dyskowy dla maszyn wirtualnych. Klient nie musi już obsługiwać konta magazynu na potrzeby skalowania i wysokiej dostępności. Platforma Azure automatycznie organizuje dyski. Niezależnie od tego, czy jest to nowe czy istniejące wdrożenie, usługa Azure CPI będzie obsługiwać tworzenie i migrację dysku zarządzanego podczas wdrażania CF. Jest on obsługiwany z PCF 1,11. Możesz również zapoznać się z tematem wskazówki dotyczące [dysku zarządzanego](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) w Cloud Foundry "open source". 
### <a name="availability-zone-"></a>Strefa dostępności *
Jako platforma aplikacji natywnych w chmurze Cloud Foundry jest zaprojektowana z użyciem [czterech poziomów wysokiej dostępności](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html). Podczas gdy pierwsze trzy poziomy błędów oprogramowania mogą być obsługiwane przez sam system CF, odporność na uszkodzenia platformy jest zapewniana przez dostawców chmury. Kluczowe składniki CF powinny być chronione przy użyciu rozwiązania o wysokiej dostępności dla dostawcy chmury. Obejmuje to GoRouters, Diego mózgów, bazę danych CF i kafelki usług. Domyślnie [zestaw dostępności platformy Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) jest używany do odporności na uszkodzenia między klastrami w centrum danych.
Jest to dobry nowy, więc [strefa dostępności platformy Azure](https://docs.microsoft.com/azure/availability-zones/az-overview ) jest teraz wydawana, co zapewnia odporność na uszkodzenia na następny poziom, z nadmiarowością małego opóźnienia w centrach danych.
Strefa dostępności platformy Azure osiąga HA dzięki umieszczeniu zestawu maszyn wirtualnych w 2 centrach danych, każdy zestaw maszyn wirtualnych jest nadmiarowy do innych zestawów. Jeśli jedna strefa nie działa, inne zestawy są nadal aktywne, odizolowane od awarii.
> [!NOTE] 
> Strefa dostępności platformy Azure nie jest jeszcze oferowana dla wszystkich regionów, zapoznaj się z najnowszym [ogłoszeniem dotyczącym listy obsługiwanych regionów](https://docs.microsoft.com/azure/availability-zones/az-overview). W przypadku Cloud Foundry Open Source Sprawdź, czy [strefa dostępności platformy Azure ma wskazówki dotyczące Cloud Foundry Open Source](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone).

## <a name="2-network-routing"></a>2. Routing sieciowy
Domyślnie usługa równoważenia obciążenia Azure w warstwie Podstawowa jest używana w przypadku żądań przychodzących interfejsów API/aplikacji CF, przekazując je do Gorouters. Składniki CF, takie jak Diego mózg, MySQL, ERT, mogą również zrównoważyć ruch dla dużej dostępności, korzystając z modułu równoważenia obciążenia. Platforma Azure udostępnia również zestaw w pełni zarządzanych rozwiązań do równoważenia obciążenia. Jeśli szukasz zakończenia protokołu TLS/SSL ("odciążanie protokołu SSL") lub na żądanie HTTP/HTTPS żądania przetwarzania warstwy aplikacji, weź pod uwagę Application Gateway. Aby zapewnić wysoką dostępność i skalowalność równoważenia obciążenia w warstwie 4, rozważ użycie standardowego modułu równoważenia obciążenia.
### <a name="azure-application-gateway-"></a>Azure Application Gateway *
[Usługa Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) oferuje różne możliwości równoważenia obciążenia warstwy 7, w tym odciążanie protokołu SSL, kompleksową obsługę protokołu TLS, zaporę aplikacji sieci Web, koligację sesji na podstawie plików cookie i wiele więcej. Application Gateway można [skonfigurować w Cloud Foundry Open Source](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). W przypadku usługi PCF Sprawdź  [Informacje o wersji PCF 2,1](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) dla testu ZK.

### <a name="azure-standard-load-balancer-"></a>Azure usługa Load Balancer w warstwie Standardowa *
Azure Load Balancer to moduł równoważenia obciążenia warstwy 4. Służy do dystrybuowania ruchu między wystąpieniami usług w zestawie o zrównoważonym obciążeniu. Wersja Standard zapewnia [Zaawansowane funkcje](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) w wersji podstawowej. Na przykład 1. Maksymalny limit puli zaplecza jest wywoływany z 100 do 1000 maszyn wirtualnych.  2. Punkty końcowe obsługują teraz wiele zestawów dostępności zamiast jednego zestawu dostępności.  3. Dodatkowe funkcje, takie jak porty HA, bogatsze dane monitorowania i tak dalej. Jeśli przenosisz do strefy dostępności platformy Azure, wymagany jest standardowy moduł równoważenia obciążenia. W przypadku nowego wdrożenia zalecamy rozpoczęcie pracy z usługą Azure usługa Load Balancer w warstwie Standardowa. 

## <a name="3-authentication"></a>3. uwierzytelnianie 
[Cloud Foundry konto użytkownika i uwierzytelnianie](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) to centralna usługa zarządzania tożsamościami dla CF i różnych składników. [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) to usługa zarządzania tożsamościami w chmurze firmy Microsoft. Domyślnie architektura UAA jest używana na potrzeby uwierzytelniania Cloud Foundry. Jako zaawansowaną opcję architektura UAA obsługuje również usługę Azure AD jako zewnętrzny magazyn użytkowników. Użytkownicy usługi Azure AD mogą uzyskiwać dostęp do Cloud Foundry przy użyciu tożsamości LDAP bez konta Cloud Foundry. Wykonaj następujące kroki, aby [skonfigurować usługę Azure AD dla architektury UAA w PCF](https://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. magazyn danych dla systemu Cloud Foundry środowiska uruchomieniowego
Cloud Foundry oferuje doskonałą rozszerzalność do korzystania z usług Azure elementu BlobStore lub Azure MySQL/PostgreSQL dla magazynu systemu w środowisku uruchomieniowym aplikacji.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Usługa Azure elementu BlobStore dla Cloud Foundry Cloud Controller elementu BlobStore
Cloud Controller elementu BlobStore to krytyczny magazyn danych dla buildpacks, kropel, pakietów i pul zasobów. Domyślnie serwer systemu plików NFS jest używany na potrzeby elementu BlobStore kontrolera chmury. Aby uniknąć single point of failure, Użyj usługi Azure Blob Storage jako magazynu zewnętrznego. Zapoznaj się z [dokumentacją Cloud Foundryową](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) dla tła i [opcje w obszarze Cloud Foundry pivotd](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>Baza danych MySQL/PostgreSQL jako Cloud Foundry Elastic Run Time Database *
W przypadku środowiska uruchomieniowego w środowisku CF wymagane są dwa główne bazy danych systemu:
#### <a name="ccdb"></a>CCDB 
Baza danych kontrolera chmury.  Kontroler chmury udostępnia punkty końcowe interfejsu API REST klientom dostępu do systemu. CCDB przechowuje tabele dla organizacje, spacji, usług, ról użytkowników i nie tylko dla kontrolera chmury.
#### <a name="uaadb"></a>UAADB 
Baza danych dla konta użytkownika i uwierzytelniania. Przechowuje dane związane z uwierzytelnianiem użytkownika, na przykład zaszyfrowane nazwy użytkowników i hasła.

Domyślnie może być używana lokalna baza danych systemu (MySQL). W przypadku wysokiej dostępności i skalowania korzystaj z usługi Azure Managed MySQL lub PostgreSQL Services. Poniżej przedstawiono instrukcje [włączania usługi Azure MySQL/PostgreSQL dla CCDB, UAADB i innych systemowych baz danych przy użyciu Cloud Foundry typu open source](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Otwórz Service Broker
Usługa Azure Service Broker oferuje spójny interfejs do zarządzania dostępem aplikacji do usług platformy Azure. Nowe [otwarte Service Broker dla projektu platformy Azure](https://github.com/Azure/open-service-broker-azure) stanowią pojedynczy i prosty sposób dostarczania usług do aplikacji w Cloud Foundry, OpenShift i Kubernetes. Aby uzyskać instrukcje dotyczące wdrażania w witrynie PCF, zobacz artykuł [Azure Open Service Broker for PCF](https://pivotal.io/platform/services-marketplace/data-management/microsoft-azure) .

## <a name="6-metrics-and-logging"></a>6. metryki i rejestrowanie
Dysza usługi Azure Log Analytics jest składnikiem Cloud Foundry, który przekazuje metryki z [Cloud Foundry Loggregator Firehose](https://docs.cloudfoundry.org/loggregator/architecture.html) do [dzienników Azure monitor](https://azure.microsoft.com/services/log-analytics/). Za pomocą dysz można zbierać, wyświetlać i analizować dane dotyczące kondycji systemu CF i wydajności w wielu wdrożeniach.
Kliknij [tutaj](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) , aby dowiedzieć się, jak wdrożyć dyszę usługi Azure log Analytics w środowisku Cloud Foundry Open Source, a następnie uzyskać dostęp do danych z konsoli Dzienniki Azure monitor. 
> [!NOTE]
> Od PCF 2,0 metryki kondycji BOSH dla maszyn wirtualnych są domyślnie przekazywane do Firehose Loggregator i są zintegrowane z konsolą dzienników Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="7-cost-saving"></a>7. oszczędności kosztów
### <a name="cost-saving-for-devtest-environments"></a>Oszczędność kosztów w środowiskach deweloperskich/testowych
#### <a name="b-series-"></a>Seria B: *
Chociaż serie maszyn wirtualnych w języku F i D były często zalecane dla Cloud Foundry środowiska produkcyjnego, nowy " [szeregowy" Seria B](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) oferuje nowe opcje. Maszyny wirtualne z serii B są idealnym rozwiązaniem w przypadku obciążeń, które nie wymagają pełnej wydajności procesora CPU, takich jak serwery sieci Web, małe bazy danych i środowiska deweloperskie i testowe. Te obciążenia zwykle mają wymagania dotyczące wydajności. Jest to $0.012/Hour (B1) w porównaniu do wartości $0,05/Hour (F1). Aby uzyskać szczegółowe informacje, zapoznaj się z pełną listą rozmiarów i [cen](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) [maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general) . 
#### <a name="managed-standard-disk"></a>Zarządzany dysk standardowy: 
Dyski w warstwie Premium są zalecane do niezawodnej wydajności w środowisku produkcyjnym.  W przypadku [dysku zarządzanego](https://azure.microsoft.com/services/managed-disks/)magazyn w warstwie Standardowa może również zapewniać podobną niezawodność z inną wydajnością. W przypadku obciążeń, które nie są zależne od wydajności, takich jak środowisko deweloperskie/testowe lub niekrytyczne, zarządzane dyski standardowe oferują alternatywną opcję z niższym kosztem.  
### <a name="cost-saving-in-general"></a>Ogólne oszczędności kosztów 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Znaczne oszczędności kosztów maszyn wirtualnych przy użyciu rezerwacji platformy Azure: 
Dzisiaj wszystkie maszyny wirtualne CF są rozliczane przy użyciu cen "na żądanie", nawet jeśli środowiska zwykle pozostają na czas nieokreślony. Teraz można zarezerwować maszynę wirtualną w okresie 1 lub 3-letnim i uzyskać rabaty w wysokości 45-65%. Rabaty są stosowane w systemie rozliczeń bez zmian w środowisku. Aby uzyskać szczegółowe informacje, zobacz [jak działają rezerwacje platformy Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Zarządzany dysk w warstwie Premium o mniejszych rozmiarach: 
Dyski zarządzane obsługują mniejsze rozmiary dysków, na przykład P4 (32 GB) i P6 (64 GB) dla dysków w warstwie Premium i standardowa. W przypadku małych obciążeń możesz zaoszczędzić Koszt podczas migrowania z dysków w warstwie Premium do zarządzanych dysków Premium.
#### <a name="use-azure-first-party-services"></a>Korzystanie z usług pierwszej firmy platformy Azure: 
Skorzystanie z zalet usługi platformy Azure w pierwszej kolejności spowoduje obniżenie kosztów administracji długoterminowej, a także o HA i niezawodność wymienionej w powyższych sekcjach. 

Przestawianie przestawne zakończyło się [MAŁĄ ERTą](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) dla klientów PCF, składniki znajdują się w zaledwie 4 maszynach wirtualnych, co działa do 2500 wystąpień aplikacji. Wersja próbna jest teraz dostępna w ramach [platformy Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>Następne kroki
Funkcje integracji platformy Azure są najpierw dostępne w [Cloud Foundry Open Source](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/), zanim będą dostępne w Cloud Foundryach Pivot. Funkcje oznaczone gwiazdką * nadal nie są dostępne za pomocą PCF. Integracja Cloud Foundry z Azure Stack nie jest objęta tym dokumentem.
Aby uzyskać pomoc techniczną PCF na funkcjach oznaczonych gwiazdką * lub Cloud Foundry integrację z usługą Azure Stack, skontaktuj się z menedżerem Pivot i konto Microsoft w celu uzyskania najnowszego stanu. 

