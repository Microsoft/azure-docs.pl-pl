---
title: Często zadawane pytania dotyczące usługi Azure Cloud Services (obsługa rozszerzona)
description: Często zadawane pytania dotyczące usługi Azure Cloud Services (obsługa rozszerzona)
ms.topic: conceptual
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 33bafac9247f007978fef568469d643f1a1098df
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383590"
---
# <a name="frequently-asked-questions-for-azure-cloud-services-extended-support"></a>Często zadawane pytania dotyczące usług Azure Cloud Services (wsparcie dodatkowe)
W tym artykule opisano często zadawane pytania dotyczące usługi Azure Cloud Services (obsługa rozszerzona).

## <a name="general"></a>Ogólne

### <a name="what-is-the-resource-name-for-cloud-services-classic--cloud-services-extended-support"></a>Co to jest nazwa zasobu dla Cloud Services (klasyczny) & Cloud Services (obsługa rozszerzona)?
- Cloud Services (klasyczny): `microsoft.classiccompute/domainnames`
- Cloud Services (obsługa rozszerzona): `microsoft.compute/cloudservices`

### <a name="what-locations-are-available-to-deploy-cloud-services-extended-support"></a>Jakie lokalizacje są dostępne do wdrożenia Cloud Services (obsługa rozszerzona)?
Cloud Services (obsługa rozszerzona) jest dostępna we wszystkich regionach chmury publicznej.

### <a name="how-does-my-quota-change"></a>Jak zmienia się limit przydziału? 
Klienci będą musieli zażądać przydziału przy użyciu takich samych procesów jak każdy inny Azure Resource Manager produkt. Limit przydziału w Azure Resource Manager jest regionalny i dla każdego regionu będzie wymagany osobne żądanie limitu przydziału.

### <a name="why-dont-i-see-a-production--staging-slot-anymore"></a>Dlaczego nie widzę już & produkcyjnych w miejscu przejściowym?
Cloud Services (obsługa rozszerzona) nie obsługuje logicznej koncepcji usługi hostowanej, która obejmuje dwa gniazda (produkcyjne & tymczasowe). Każde wdrożenie to niezależne wdrożenie usługi w chmurze (obsługa rozszerzona). Aby przetestować i przygotować nową wersję usługi w chmurze, należy wdrożyć usługę w chmurze (obsługę rozszerzoną) i oznaczyć ją jako zamienionej na adresy VIP z inną usługą w chmurze (obsługa rozszerzona)

### <a name="why-cant-i-create-an-empty-cloud-service-anymore"></a>Dlaczego nie mogę już utworzyć pustej usługi w chmurze?
Koncepcja nazw usług hostowanych już nie istnieje, nie można utworzyć pustej usługi w chmurze (obsługa rozszerzona).

### <a name="does-cloud-services-extended-support-support-resource-health-check-rhc"></a>Czy Cloud Services (obsługa rozszerzona) obsługuje Resource Health Check (systemie RHC występuje)?
Nie, Cloud Services (obsługa rozszerzona) nie obsługuje sprawdzania Resource Health (systemie RHC występuje).

### <a name="how-are-role-instance-metrics-changing"></a>Jak są zmieniane metryki wystąpień ról?
Brak zmian w metrykach wystąpienia roli. 

### <a name="how-are-web--worker-roles-changing"></a>Jak zmieniają się role procesu roboczego & sieci Web?
Nie wprowadzono żadnych zmian dotyczących projektowania, architektury ani składników ról sieci Web i procesu roboczego. 

### <a name="how-are-role-instances-changing"></a>Jak są zmieniane wystąpienia roli?
Nie wprowadzono żadnych zmian dotyczących projektowania, architektury ani składników wystąpień roli. 

### <a name="how-will-guest-os-updates-change"></a>Jak zostaną zmienione aktualizacje systemu operacyjnego gościa?
 Nie wprowadzono zmian w metodzie wdrażania. Cloud Services (klasyczny) i Cloud Services (obsługa rozszerzona) będą korzystać z tych samych aktualizacji.
 
### <a name="does-cloud-services-extended-support-support-stopped-allocated-and-stopped-deallocated-states"></a>Czy Cloud Services (obsługa rozszerzona) obsługuje Stany zatrzymane i zatrzymane bez przydziału?

Wdrożenie Cloud Services (obsługa rozszerzona) obsługuje tylko stan zatrzymany, który jest wyświetlany w Azure Portal jako "zatrzymana". Stan zatrzymania bez przydziału nie jest obsługiwany. 

### <a name="do-cloud-services-extended-support-deployments-support-scaling-across-clusters-availability-zones-and-regions"></a>Czy wdrożenia Cloud Services (obsługa rozszerzona) obsługują skalowanie między klastrami, strefami dostępności i regionami?
Wdrożenia Cloud Services (rozszerzonej pomocy technicznej) nie mogą być skalowane w wielu klastrach, strefach dostępności i regionach. 

### <a name="how-can-i-get-the-deployment-id-for-my-cloud-service-extended-support"></a>Jak uzyskać identyfikator wdrożenia dla usługi w chmurze (obsługa rozszerzona)
Do identyfikatora wdrożenia alias prywatny można uzyskać dostęp za pomocą interfejsu API [CloudServiceInstanceView](https://docs.microsoft.com/rest/api/compute/cloudservices/getinstanceview#cloudserviceinstanceview) . Jest ona również dostępna na Azure Portal w bloku rola i wystąpienia usługi w chmurze (obsługa rozszerzona)

### <a name="are-there-any-pricing-differences-between-cloud-services-classic-and-cloud-services-extended-support"></a>Czy istnieją różnice cenowe między Cloud Services (klasyczny) i Cloud Services (obsługa rozszerzona)?
Cloud Services (obsługa rozszerzona) używa publicznych adresów IP Azure Key Vault i Basic (ARM).Klienci wymagający certyfikatów muszą używać Azure Key Vault do zarządzania certyfikatami ([Dowiedz się więcej](https://azure.microsoft.com/pricing/details/key-vault/) o Azure Key Vault cenach).   Każdy publiczny adres IP dla Cloud Services (obsługa rozszerzona) jest rozliczany osobno ([Dowiedz się więcej](https://azure.microsoft.com/pricing/details/ip-addresses/) o cenniku publicznego adresu IP). 
## <a name="resources"></a>Zasoby 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-resource-group"></a>Jakie zasoby są połączone z wdrożeniem Cloud Services (obsługa rozszerzona) w tej samej grupie zasobów?
Usługi równoważenia obciążenia, sieciowe grupy zabezpieczeń i tabele tras muszą być aktywne w tym samym regionie i grupie zasobów. 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-region"></a>Jakie zasoby są połączone z wdrożeniem Cloud Services (obsługa rozszerzona) w tym samym regionie?
Key Vault, Sieć wirtualna, publiczne adresy IP, sieciowe grupy zabezpieczeń i tabele tras muszą się znajdować w tym samym regionie.

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-virtual-network"></a>Jakie zasoby są połączone z wdrożeniem Cloud Services (obsługa rozszerzona) w tej samej sieci wirtualnej?
Publiczne adresy IP, usługi równoważenia obciążenia, sieciowe grupy zabezpieczeń i tabele tras muszą być aktywne w tej samej sieci wirtualnej. 

## <a name="deployment-files"></a>Pliki wdrożenia 

### <a name="how-can-i-use-a-template-to-deploy-or-manage-my-deployment"></a>Jak mogę użyć szablonu do wdrożenia mojego wdrożenia lub zarządzania nim?
Pliki szablonów i parametrów mogą być przesyłane jako parametry przy użyciu REST, PowerShell i interfejsu wiersza polecenia. Można je również przekazać przy użyciu Azure Portal.  

### <a name="do-i-need-to-maintain-four-files-now-template-parameter-csdef-cscfg"></a>Czy muszę teraz obsługiwać cztery pliki? (szablon, parametr, csdef, cscfg)
Pliki szablonów i parametrów są używane tylko na potrzeby automatyzacji wdrażania. Podobnie jak Cloud Services (klasyczny), można ręcznie utworzyć zasoby zależne, a następnie wdrożyć Cloud Services (rozszerzoną obsługę) przy użyciu programu PowerShell, poleceń interfejsu wiersza polecenia lub za pośrednictwem portalu z istniejącą csdef.

### <a name="how-does-my-application-code-change-on-cloud-services-extended-support"></a>Jak zmienia się kod aplikacji na Cloud Services (obsługa rozszerzona)
Nie są wymagane żadne zmiany w kodzie aplikacji spakowane w cspkg. Twoje istniejące aplikacje będą nadal działały tak jak wcześniej. 

### <a name="does-cloud-services-extended-support-allow-ctp-package-format"></a>Czy Cloud Services (obsługa rozszerzona) zezwala na format pakietu CTP?
Format pakietu CTP nie jest obsługiwany w Cloud Services (obsługa rozszerzona). Umożliwia jednak limit rozmiaru pakietu rozszerzonego wynoszący 800 MB

## <a name="migration"></a>Migracja

### <a name="will-cloud-services-extended-support-mitigate-the-failures-due-to-allocation-failures"></a>Czy Cloud Services (obsługa rozszerzona) pozwala wyeliminować błędy spowodowane błędami alokacji?
Nie, wdrożenia usługi w chmurze (obsługa rozszerzona) są powiązane z klastrem, takim jak Cloud Services (klasyczny). W związku z tym błędy alokacji będą nadal istnieć, Jeśli klaster jest zapełniony. 

### <a name="when-do-i-need-to-migrate"></a>Kiedy należy przeprowadzić migrację? 
Oszacowanie czasu wymaganego i migracji złożoności zależy od zakresu zmiennych. Planowanie jest najbardziej skutecznym krokiem do zrozumienia zakresu pracy, zablokowanych i złożoności migracji.

## <a name="networking"></a>Sieć 

### <a name="why-cant-i-create-a-deployment-without-virtual-network"></a>Dlaczego nie mogę utworzyć wdrożenia bez sieci wirtualnej?
Sieci wirtualne są wymagane do wdrożenia na Azure Resource Manager. Wdrożenie Cloud Services (obsługa rozszerzona) musi znajdować się w sieci wirtualnej. 

### <a name="why-am-i-now-seeing-so-many-networking-resources"></a>Dlaczego widzę teraz wiele zasobów sieciowych? 
W Azure Resource Manager składniki wdrożenia Cloud Services (rozszerzonej pomocy technicznej) są udostępniane jako zasób w celu zapewnienia lepszej widoczności i lepszej kontroli. Ten sam typ zasobów został użyty w Cloud Services (klasyczny), ale został po prostu ukryty. Przykładem takiego zasobu jest publiczna Load Balancer, która jest teraz jawnym zasobem "tylko do odczytu" tworzonym automatycznie przez platformę

### <a name="what-restrictions-apply-for-a-subnet-with-respective-to-cloud-services-extended-support"></a>Jakie ograniczenia dotyczą podsieci z odpowiednimi Cloud Services (obsługa rozszerzona)?
Podsieć zawierająca wdrożenia Cloud Services (rozszerzonej pomocy technicznej) nie może być udostępniana z wdrożeniami z innych produktów obliczeniowych, takich jak Virtual Machines, Virtual Machines zestawów skalowania, Service Fabric itp.

### <a name="what-ip-allocation-methods-are-supported-on-cloud-services-extended-support"></a>Jakie metody alokacji adresów IP są obsługiwane w usługach w chmurze (obsługa rozszerzona)?
Cloud Services (obsługa rozszerzona) obsługuje dynamiczne & statyczne metody alokacji adresów IP. Statyczne adresy IP są przywoływane jako zastrzeżone IP w pliku cscfg.

### <a name="why-am-i-getting-charged-for-ip-addresses"></a>Dlaczego otrzymuję opłaty za adresy IP?
Klienci są rozliczani za korzystanie z adresów IP w Cloud Services (obsługa rozszerzona), tak jak użytkownicy są rozliczani za adresy IP skojarzone z maszynami wirtualnymi. 

### <a name="can-i-use-a-dns-name-with-cloud-services-extended-support"></a>Czy mogę użyć nazwy DNS z Cloud Services (obsługa rozszerzona)? 
Tak. Cloud Services (obsługa rozszerzona) może również mieć nazwę DNS. W przypadku Azure Resource Manager etykieta DNS jest opcjonalną właściwością publicznego adresu IP przypisanego do usługi w chmurze. Format nazwy DNS dla wdrożeń opartych na Azure Resource Manager to `<userlabel>.<region>.cloudapp.azure.com`

### <a name="can-i-update-or-change-the-virtual-network-reference-for-an-existing-cloud-service-extended-support"></a>Czy mogę zaktualizować lub zmienić odwołanie do sieci wirtualnej dla istniejącej usługi w chmurze (obsługa rozszerzona)? 
Nie. Odwołanie do sieci wirtualnej jest obowiązkowe podczas tworzenia usługi w chmurze. W przypadku istniejącej usługi w chmurze nie można zmienić odwołania do sieci wirtualnej. Przestrzeń adresową sieci wirtualnej można zmodyfikować przy użyciu interfejsów API sieci wirtualnej. 

## <a name="certificates--key-vault"></a>& certyfikatów Key Vault

### <a name="why-do-i-need-to-manage-my-certificates-on-cloud-services-extended-support"></a>Dlaczego muszę zarządzać moimi certyfikatami w Cloud Services (obsługa rozszerzona)?
Cloud Services (obsługa rozszerzona) przyjęła ten sam proces jak w przypadku innych ofert obliczeniowych, w których certyfikaty znajdują się w magazynach kluczy zarządzanych przez klienta. Dzięki temu klienci mają pełną kontrolę nad kluczami tajnymi & certyfikatów. 

### <a name="can-i-use-one-key-vault-for-all-my-deployments-in-all-regions"></a>Czy mogę używać jednego Key Vault dla wszystkich wdrożeń we wszystkich regionach?
Nie. Key Vault jest zasobem regionalnym, a klienci potrzebują jednego Key Vault w każdym regionie. Jednak jeden Key Vault może być używany dla wszystkich wdrożeń w danym regionie.

## <a name="next-steps"></a>Następne kroki
Aby rozpocząć korzystanie z Cloud Services (obsługa rozszerzona), zobacz [wdrażanie usługi w chmurze (obsługa rozszerzona) przy użyciu programu PowerShell](deploy-powershell.md)
