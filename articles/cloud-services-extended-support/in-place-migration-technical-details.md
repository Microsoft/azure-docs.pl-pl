---
title: Szczegóły techniczne i wymagania dotyczące migracji do usługi Azure Cloud Services (obsługa rozszerzona)
description: Zawiera szczegółowe informacje techniczne i wymagania dotyczące migracji z usługi Azure Cloud Services (klasycznego) do usługi Azure Cloud Services (rozszerzona pomoc techniczna)
author: tanmaygore
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 4ff7d9aa2075b675a7ecd979c08d5621bbdd831a
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286994"
---
# <a name="technical-details-of-migrating-to-azure-cloud-services-extended-support"></a>Szczegóły techniczne migracji do usługi Azure Cloud Services (obsługa rozszerzona)   

W tym artykule omówiono szczegółowe informacje techniczne dotyczące narzędzia migracji dotyczące Cloud Services (klasyczne). 

> [!IMPORTANT]
> Migrowanie z Cloud Services (klasycznego) do Cloud Services (obsługa rozszerzona) za pomocą narzędzia migracji jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="details-about-feature--scenarios-supported-for-migration"></a>Szczegóły dotyczące funkcji/scenariuszy obsługiwanych na potrzeby migracji 

### <a name="extensions-and-plugin-migration"></a>Rozszerzenia i migracja wtyczki 
- Wszystkie włączone i obsługiwane rozszerzenia zostaną zmigrowane. 
- Wyłączone rozszerzenia nie zostaną zmigrowane. 
- Wtyczki są starszej koncepcji i powinny zostać usunięte przed migracją. Są one obsługiwane w przypadku migracji, ale po migracji, jeśli rozszerzenie musi być włączone, należy najpierw usunąć wtyczkę przed zainstalowaniem rozszerzenia. Rozszerzenia pulpitu zdalnego i rozszerzeń mają na to wpływ.   
 
### <a name="certificate-migration"></a>Migracja certyfikatów
- W Cloud Services (obsługa rozszerzona) certyfikaty są przechowywane w Key Vault. W ramach migracji tworzymy Key Vault dla klientów mających nazwę usługi w chmurze i przeniesiesz wszystkie certyfikaty z usługi Azure Service Manager do Key Vault. 
- Odwołanie do tego Key Vault jest określone w szablonie lub przesłane za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure. 

### <a name="service-configuration-and-service-definition-files"></a>Pliki definicji usługi i usługi
- Pliki. cscfg i. csdef muszą zostać zaktualizowane dla Cloud Services (obsługa rozszerzona) z drobnymi zmianami. 
- Nazwy zasobów, takich jak sieć wirtualna i jednostki SKU maszyny wirtualnej, są różne. Zobacz [translacja zasobów i Konwencja nazewnictwa po migracji](#translation-of-resources-and-naming-convention-post-migration)
- Klienci mogą pobrać nowe wdrożenia przy użyciu [programu PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) i [interfejsu API REST](https://docs.microsoft.com/rest/api/compute/cloudservices/get). 

### <a name="cloud-service-and-deployments"></a>Usługa w chmurze i wdrożenia
- Każde wdrożenie Cloud Services (obsługa rozszerzona) to niezależna usługa w chmurze. Wdrożenie nie jest już pogrupowane w usłudze w chmurze przy użyciu miejsc.
- Jeśli masz dwa gniazda w usłudze w chmurze (klasyczne), musisz usunąć jedno gniazdo (tymczasowe) i użyć narzędzia migracji, aby przenieść drugie miejsce (produkcyjne) do Azure Resource Manager. 
- Publiczny adres IP w ramach wdrożenia usługi w chmurze pozostaje taki sam po migracji do Azure Resource Manager i jest udostępniany jako podstawowy adres IP jednostki SKU (dynamiczny lub statyczny). 
- Nazwa i domena DNS (cloudapp.azure.net) dla zmigrowanej usługi w chmurze pozostają takie same. 

### <a name="virtual-network-migration"></a>Migracja sieci wirtualnej
- Jeśli wdrożenie Cloud Services znajduje się w sieci wirtualnej, podczas migracji wszystkie Cloud Services i skojarzone zasoby sieci wirtualnej są migrowane ze sobą. 
- Po migracji Sieć wirtualna jest umieszczana w innej grupie zasobów niż usługa w chmurze. 
- W przypadku sieci wirtualnych z wieloma Cloud Services każda usługa w chmurze jest migrowana jedna po drugiej. 

### <a name="migration-of-deployments-not-in-a-virtual-network"></a>Migracja wdrożeń poza siecią wirtualną
- W 2017 platforma Azure zaczęła automatycznie tworzyć nowe wdrożenia (bez określonej przez klienta sieci wirtualnej) do utworzonej platformy jako domyślnej sieci wirtualnej. Te domyślne sieci wirtualne są ukryte dla klientów.   
- W ramach migracji ta domyślna Sieć wirtualna zostanie udostępniona klientom w Azure Resource Manager. Aby zarządzać wdrożeniem w Azure Resource Manager lub zaktualizować je, klienci muszą dodać informacje o tej sieci wirtualnej w sekcji NetworkConfiguration pliku. cscfg.    
- Domyślna Sieć wirtualna, gdy jest migrowana do Azure Resource Manager, znajduje się w tej samej grupie zasobów co usługa w chmurze.
- Cloud Services utworzone przed tym terminem nie będą w żadnej sieci wirtualnej i nie można przeprowadzić migracji przy użyciu narzędzia. Rozważ ponowne wdrożenie tych Cloud Services bezpośrednio w programie Azure Resource Manager. 
- Aby sprawdzić, czy wdrożenie kwalifikuje się do migracji, uruchom polecenie Sprawdź poprawność interfejsu API we wdrożeniu. Wynik sprawdzania poprawności interfejsu API będzie zawierał komunikat o błędzie w sposób jawny wskazujący, że to wdrożenie kwalifikuje się do migracji.     

### <a name="load-balancer"></a>Load Balancer   
- W przypadku usługi w chmurze korzystającej z publicznego punktu końcowego usługa równoważenia obciążenia utworzona przez platformę skojarzoną z usługą w chmurze jest narażona na subskrypcję klienta w Azure Resource Manager. Moduł równoważenia obciążenia jest zasobem tylko do odczytu, a aktualizacje są ograniczone tylko za pomocą plików konfiguracji usługi (cscfg) i usług definicji usługi (. csdef). 

### <a name="key-vault"></a>Key Vault
- W ramach migracji platforma Azure automatycznie tworzy nową Key Vault i migruje wszystkie certyfikaty do niej. Narzędzie nie pozwala na korzystanie z istniejących Key Vault. 
- Cloud Services (obsługa rozszerzona) wymaga Key Vault znajdującego się w tym samym regionie i w ramach subskrypcji. Ten Key Vault jest tworzony automatycznie w ramach migracji. 


## <a name="translation-of-resources-and-naming-convention-post-migration"></a>Tłumaczenie zasobów i Konwencja nazewnictwa po migracji
W ramach migracji nazwy zasobów są zmieniane, a kilka Cloud Services funkcje są ujawniane jako zasoby Azure Resource Manager. Tabela zawiera podsumowanie zmian specyficznych dla migracji Cloud Services.

| Cloud Services (klasyczny) <br><br> Nazwa zasobu | Cloud Services (klasyczny) <br><br> Składnia| Cloud Services (obsługa rozszerzona) <br><br> Nazwa zasobu| Cloud Services (obsługa rozszerzona) <br><br> Składnia | 
|---|---|---|---|
| Usługa w chmurze | `cloudservicename` | Nieskojarzone| Nieskojarzone |
| Wdrożenie (utworzono Portal) <br><br> Wdrożenie (nie utworzono portalu)  | `deploymentname` | Cloud Services (obsługa rozszerzona) | `deploymentname` |  
| Virtual Network | `vnetname` <br><br> `Group resourcegroupname vnetname` <br><br> Nieskojarzone |  Virtual Network (nie utworzono portalu) <br><br> Virtual Network (utworzono Portal) <br><br> Sieci wirtualne (domyślnie) | `vnetname` <br><br> `group-resourcegroupname-vnetname` <br><br> `DefaultRdfevirtualnetwork_vnetid`|
| Nieskojarzone | Nieskojarzone | Key Vault | `cloudservicename` | 
| Nieskojarzone | Nieskojarzone | Grupa zasobów dla wdrożeń usług w chmurze | `cloudservicename-migrated` | 
| Nieskojarzone | Nieskojarzone | Grupa zasobów dla Virtual Network | `vnetname-migrated` <br><br> `group-resourcegroupname-vnetname-migrated`|
| Nieskojarzone | Nieskojarzone | Publiczny adres IP (dynamiczny) | `cloudservicenameContractContract` | 
| Nazwa Zastrzeżony adres IP | `reservedipname` | Zastrzeżony adres IP (nie utworzono portalu) <br><br> Zastrzeżony adres IP (utworzono Portal) | `reservedipname` <br><br> `group-resourcegroupname-reservedipname` | 
| Nieskojarzone| Nieskojarzone | Load Balancer | `deploymentname-lb`|



## <a name="migration-issues-and-how-to-handle-them"></a>Problemy z migracją oraz sposób ich obsługi. 

### <a name="migration-stuck-in-an-operation-for-a-long-time"></a>Migracja została zablokowana w operacji przez długi czas. 
- Zatwierdzanie, przygotowywanie i przerywanie może zająć dużo czasu w zależności od liczby wdrożeń. Operacje będą przekroczyć limit czasu po 24 godzinach.   
- Operacje zatwierdzania, przygotowywania i przerywania są idempotentne. Większość problemów jest fixable przez ponawianie próby. Mogą występować błędy przejściowe, które mogą odejść od kilku minut, zalecamy ponowną próbę po przerwie. W przypadku migrowania przy użyciu Azure Portal, gdy operacja jest zablokowana w stanie "w toku", użyj programu PowerShell, aby ponowić próbę wykonania operacji. 
- Skontaktuj się z pomocą techniczną, aby ułatwić migrację lub wycofanie wdrożenia z zaplecza. 

### <a name="migration-failed-in-an-operation"></a>Migracja nie powiodła się w operacji. 
- Jeśli sprawdzanie poprawności nie powiodło się, jest to spowodowane tym, że wdrożenie lub Sieć wirtualna zawiera nieobsługiwany scenariusz/funkcję/zasób. Użyj listy nieobsługiwanych scenariuszy, aby znaleźć informacje o pracy w dokumentach.  
- Najpierw przygotowywanie operacji powoduje sprawdzenie poprawności, łącznie z pewnymi kosztownymi walidacjami (nieobjętymi sprawdzaniem poprawności). Niepowodzenie przygotowania może być spowodowane nieobsługiwanym scenariuszem. Znajdź scenariusz i obejścia w dokumentach publicznych. Należy wywołać metodę Abort, aby wrócić do oryginalnego stanu i odblokować wdrożenie dla operacji Updates i DELETE.
- Jeśli przerwanie nie powiodło się, spróbuj ponownie wykonać operację. Jeśli ponowna próba nie powiedzie się, skontaktuj się z pomocą techniczną.
- Jeśli zatwierdzenie nie powiodło się, spróbuj ponownie wykonać operację. Jeśli próba nie powiedzie się, skontaktuj się z pomocą techniczną. Nawet w przypadku niepowodzenia zatwierdzania wdrożenie nie powinno być problemem z płaszczyzną danych. Wdrożenie powinno być w stanie obsługiwać ruch klientów bez jakiegokolwiek problemu. 

### <a name="portal-refreshed-after-prepare-experience-restarted-and-commit-or-abort-not-visible-anymore"></a>Odświeżanie portalu po przygotowaniu. Środowisko uruchomione ponownie i zatwierdź lub przerwanie nie jest już widoczne. 
- Portal przechowuje informacje o migracji lokalnie i w związku z tym po odświeżeniu, rozpocznie się od zweryfikowania fazy, nawet jeśli usługa w chmurze jest w fazie przygotowania.  
- Możesz użyć portalu, aby przejść ponownie i przygotować kroki w celu udostępnienia przycisku Przerwij i zatwierdź. Nie spowoduje to żadnych błędów.
- Klienci mogą użyć programu PowerShell lub interfejsu API REST, aby przerwać lub zatwierdzić. 

### <a name="how-much-time-can-the-operations-takebr"></a>Ile czasu mogą robić operacje?<br>
Walidacja została zaprojektowana tak, aby była szybka. Przygotowanie jest najdłużej uruchomione i trwa pewien czas w zależności od łącznej liczby wystąpień ról, które są migrowane. Przerwanie i zatwierdzanie może być również czasochłonne, ale w porównaniu z przygotowaniem zajmie mniej czasu. Wszystkie operacje będą przekroczyć limit czasu po 24 godzinach. 
