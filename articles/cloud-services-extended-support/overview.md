---
title: Informacje o usłudze Azure Cloud Services (obsługa rozszerzona)
description: Informacje o elementach podrzędnych elementu konfiguracji sieci w pliku konfiguracji usługi, które określają Virtual Network i wartości DNS.
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: d61314dc89d341e376de7d3904568825ad3cfb04
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2021
ms.locfileid: "106293876"
---
# <a name="about-azure-cloud-services-extended-support"></a>Informacje o usłudze Azure Cloud Services (obsługa rozszerzona)

Cloud Services (obsługa rozszerzona) to nowy model wdrażania oparty na [Azure Resource Manager](../azure-resource-manager/management/overview.md) na [platformie Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) i jest teraz ogólnie dostępny. Cloud Services (obsługa rozszerzona) ma podstawowe korzyści wynikające z zapewnienia odporności regionalnej wraz z obsługą funkcji Azure Cloud Services wdrożonej przy użyciu usługi Azure Service Manager. Oferuje on również pewne funkcje ARM, takie jak dostęp oparty na rolach i kontrola (RBAC), Tagi, zasady i obsługuje szablony wdrażania.  

Wprowadzenie tej zmiany spowoduje zmianę nazwy modelu wdrożenia opartego na usłudze Azure Service Manager dla Cloud Services [Cloud Services (klasyczny)](../cloud-services/cloud-services-choose-me.md). Będziesz mieć możliwość kompilowania i szybkiego wdrażania aplikacji i usług w sieci Web oraz w chmurze. Możesz skalować infrastrukturę usług w chmurze w oparciu o bieżące zapotrzebowanie i upewnić się, że wydajność aplikacji będzie możliwa, jednocześnie zmniejszając koszty.  

## <a name="what-does-not-change"></a>Co nie zmienia 
- Tworzysz kod, zdefiniujesz konfiguracje i wdrażasz ją na platformie Azure. Platforma Azure konfiguruje środowisko obliczeniowe, uruchamia kod, a następnie monitoruje go i utrzymuje.
- Cloud Services (obsługa rozszerzona) obsługuje również dwa typy ról, [sieci Web i procesu roboczego](../cloud-services/cloud-services-choose-me.md). Nie wprowadzono żadnych zmian dotyczących projektowania, architektury ani składników ról sieci Web i procesu roboczego. 
- Są przenoszone trzy składniki usługi w chmurze, definicji usługi (. csdef), konfiguracji usługi (. cscfg) i pakietu usługi (. cspkg), a w ich [formatach](cloud-services-model-and-package.md)nie wprowadzono żadnych zmian. 
- Do kodu środowiska uruchomieniowego nie są wymagane żadne zmiany, ponieważ płaszczyzna danych jest taka sama, a płaszczyzna kontroli jest zmieniana. 
- Wersje GuestOS platformy Azure i skojarzone aktualizacje są wyrównane z Cloud Services (klasyczne)
- Podstawowy proces aktualizacji w odniesieniu do domen aktualizacji, sposób uaktualniania, wycofywania i dozwolone zmiany usługi podczas aktualizacji nie zmieniają się

## <a name="changes-in-deployment-model"></a>Zmiany w modelu wdrażania

Do wdrażania Cloud Services (obsługa rozszerzona) wymagane są minimalne zmiany w plikach konfiguracji usługi (. cscfg) i definicji usługi (. csdef). Kod środowiska uruchomieniowego nie wymaga żadnych zmian. Jednak skrypty wdrażania muszą zostać zaktualizowane w celu wywołania nowych interfejsów API opartych na Azure Resource Manager. 

:::image type="content" source="media/overview-image-1.png" alt-text="Obraz przedstawia konfigurację klasycznej usługi w chmurze z dodaniem sekcji szablonu. ":::

Główne różnice między Cloud Services (klasyczne) i Cloud Services (obsługa rozszerzona) w odniesieniu do wdrożenia są następujące: 

- Wdrożenia Azure Resource Manager korzystają z [szablonów usługi ARM](../azure-resource-manager/templates/overview.md) , czyli pliku JavaScript Object Notation (JSON), który definiuje infrastrukturę i konfigurację projektu. Szablon używa składni deklaratywnej, która pozwala określić, co zamierzasz wdrożyć, bez konieczności pisania w tym celu sekwencji poleceń programistycznych. Konfiguracja usługi i plik definicji usługi muszą być zgodne z [szablonem ARM](../azure-resource-manager/templates/overview.md) podczas wdrażania Cloud Services (obsługa rozszerzona). Można to osiągnąć, [ręcznie tworząc szablon ARM](deploy-template.md) lub korzystając z [programu PowerShell](deploy-powershell.md), [portalu](deploy-portal.md) i [programu Visual Studio](deploy-visual-studio.md).  

- Klienci muszą używać [Azure Key Vault](../key-vault/general/overview.md) do [zarządzania certyfikatami w Cloud Services (obsługa rozszerzona)](certificates-and-key-vault.md). Azure Key Vault pozwala bezpiecznie przechowywać poświadczenia aplikacji, takie jak wpisy tajne, klucze i certyfikaty w centralnym i bezpiecznym repozytorium chmury oraz zarządzać nimi. Aplikacje mogą uwierzytelniać się w celu Key Vault w czasie wykonywania w celu pobrania poświadczeń. 

- Wszystkie zasoby wdrożone za pomocą [Azure Resource Manager](../azure-resource-manager/templates/overview.md) muszą znajdować się w sieci wirtualnej. Sieci wirtualne i podsieci są tworzone w Azure Resource Manager przy użyciu istniejących interfejsów API Azure Resource Manager i należy się do nich odwoływać w sekcji NetworkConfiguration pliku. cscfg podczas wdrażania Cloud Services (obsługa rozszerzona).   

- Każda usługa w chmurze (obsługa rozszerzona) to pojedyncze niezależne wdrożenie. Usługi Cloud Services (obsługa rozszerzona) nie obsługują wielu gniazd w ramach jednej usługi w chmurze.  
    - Możliwość wymiany adresów VIP może być używana do wymiany między dwiema usługami w chmurze (obsługa rozszerzona). Aby przetestować i przygotować nową wersję usługi w chmurze, należy wdrożyć usługę w chmurze (obsługę rozszerzoną) i oznaczyć ją jako zamienionej na adresy VIP z inną usługą w chmurze (obsługa rozszerzona)  

- Etykieta usługi nazw domen (DNS) jest opcjonalna dla usługi w chmurze (obsługa rozszerzona). W Azure Resource Manager etykieta DNS jest właściwością publicznego zasobu adresu IP skojarzonego z usługą w chmurze. 

## <a name="migration-to-azure-resource-manager"></a>Migracja do Azure Resource Manager

Cloud Services (obsługa rozszerzona) oferuje dwie ścieżki migracji z [usługi Azure Service Manager](/powershell/azure/servicemanagement/overview) do [Azure Resource Manager](../azure-resource-manager/management/overview.md). 
1) Klienci wdrażają usługi w chmurze bezpośrednio w Azure Resource Manager a następnie usuwali starą usługę w chmurze na platformie Azure Service Manager. 
2) Migracja w miejscu obsługuje możliwość migrowania Cloud Services (klasyczny) z minimalnym czasem przestoju do Cloud Services (obsługa rozszerzona). 

### <a name="additional-migration-options"></a>Dodatkowe opcje migracji

Podczas oceniania planów migracji z Cloud Services (klasycznego) do Cloud Services (obsługa rozszerzona) możesz chcieć zbadać dodatkowe usługi platformy Azure, takie jak: [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md), [App Service](../app-service/overview.md), [azure Kubernetes Service](../aks/intro-kubernetes.md)i [Azure Service Fabric](../service-fabric/service-fabric-overview.md). Te usługi będą w dalszym ciągu korzystać z dodatkowych możliwości, a podczas Cloud Services (obsługa rozszerzona) będzie przede wszystkim zachować parzystość funkcji z Cloud Services (klasyczny). 

W zależności od aplikacji Cloud Services (obsługa rozszerzona) może wymagać znacznie mniej wysiłku do przejścia do Azure Resource Manager w porównaniu z innymi opcjami. Jeśli aplikacja nie jest rozwijana, Cloud Services (obsługa rozszerzona) jest wykonalną opcją do rozważenia, ponieważ zapewnia szybką ścieżkę migracji. Jeśli jednak aplikacja ciągle się zmienia i wymaga bardziej nowoczesnego zestawu funkcji, zapoznaj się z innymi usługami platformy Azure, aby lepiej rozwiązać bieżące i przyszłe wymagania. 

## <a name="next-steps"></a>Następne kroki
- Zapoznaj się z [wymaganiami wstępnymi](deploy-prerequisite.md) dotyczącymi wdrażania Cloud Services (obsługa rozszerzona).
- Wdróż usługę w chmurze (obsługę rozszerzoną) przy użyciu [Azure Portal](deploy-portal.md), [programu PowerShell](deploy-powershell.md), [szablonu](deploy-template.md) lub [programu Visual Studio](deploy-visual-studio.md).
- Zapoznaj się z [często zadawanymi pytaniami](faq.md) dotyczącymi Cloud Services (obsługa rozszerzona).
