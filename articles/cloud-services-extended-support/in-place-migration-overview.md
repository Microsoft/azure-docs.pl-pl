---
title: Migrowanie platformy Azure Cloud Services (klasycznej) do usługi Azure Cloud Services (obsługa rozszerzona)
description: Przegląd migracji z Cloud Services (klasyczny) do usługi w chmurze (obsługa rozszerzona)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 96315899f80d0bd02ac3d2108b7cd76876025218
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287001"
---
# <a name="migrate-azure-cloud-services-classic-to-azure-cloud-services-extended-support"></a>Migrowanie platformy Azure Cloud Services (klasycznej) do usługi Azure Cloud Services (obsługa rozszerzona)

Ten artykuł zawiera omówienie narzędzia migracji obsługiwanego przez platformę oraz sposobu korzystania z niego do migrowania [Cloud Services platformy Azure (klasycznej)](../cloud-services/cloud-services-choose-me.md) do [usługi Azure Cloud Services (obsługa rozszerzona)](overview.md).

Narzędzie migracji wykorzystuje te same interfejsy API i ma takie samo środowisko jak [migracja maszyny wirtualnej (klasyczna)](https://docs.microsoft.com/azure/virtual-machines/migration-classic-resource-manager-overview). 

> [!IMPORTANT]
> Migrowanie z Cloud Services (klasycznego) do Cloud Services (obsługa rozszerzona) za pomocą narzędzia migracji jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli potrzebujesz pomocy dotyczącej migracji, zapoznaj się z następującymi zasobami: 

- [Microsoft Q&a](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html): Pomoc techniczna firmy Microsoft i społeczność dla migracji.
- [Obsługa migracji platformy Azure](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/%7B%22pesId%22:%22e79dcabe-5f77-3326-2112-74487e1e5f78%22,%22supportTopicId%22:%22fca528d2-48bd-7c9f-5806-ce5d5b1d226f%22%7D): dedykowany zespół pomocy technicznej w zakresie pomocy technicznej podczas migracji. Klienci bez pomocy technicznej mogą korzystać z [bezpłatnej pomocy technicznej](https://aka.ms/cs-migration-errors) dostępnej dla tej migracji.
- Jeśli firma/organizacja ma partnerów firmy Microsoft lub współpracuje z przedstawicielami firmy Microsoft, takimi jak architekty rozwiązań w chmurze lub menedżerami technicznymi, skontaktuj się z nimi, aby uzyskać więcej zasobów na potrzeby migracji.
- Wykonaj [tę ankietę](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR--AgudUMwJKgRGMO84rHQtUQzZYNklWUk4xOTFXVFBPOFdGOE85RUIwVC4u) , aby przekazać opinię lub zgłosić problemy do zespołu produktu Cloud Services (obsługa rozszerzona). 

## <a name="migration-benefits"></a>Zalety migracji
Migracja obsługiwana przez platformę zapewnia następujące korzyści:
- Migracja jest w pełni aranżacja przez platformę, przez przeniesienie całego wdrożenia i skojarzonych zasobów do Azure Resource Manager.
- Brak migracji przestojów.
- Łatwa i szybka migracja w porównaniu z innymi ścieżkami migracji przez zminimalizowanie zadań ręcznych. 
- Zachowuje Cloud Services adres IP i etykietę DNS w ramach migracji. 

Aby uzyskać inne korzyści i przyczynę migracji, zobacz [Cloud Services (obsługa rozszerzona)](overview.md) i [Azure Resource Manager](../azure-resource-manager/management/overview.md). 

## <a name="setup-access-for-migration"></a>Konfigurowanie dostępu do migracji

Aby przeprowadzić migrację, należy dodać jako administratora dla subskrypcji i zarejestrować wymaganych dostawców. 

1. Zaloguj się w witrynie Azure Portal.
3. W menu centrum wybierz pozycję subskrypcja. Jeśli go nie widzisz, wybierz pozycję Wszystkie usługi.
3. Znajdź odpowiedni wpis subskrypcji, a następnie poszukaj pola MY ROLE. W przypadku współadministratora wartość powinna być administratorem konta. Jeśli nie możesz dodać współadministratora, skontaktuj się z administratorem usługi lub współadministratorem, aby uzyskać subskrypcję, która ma zostać dodana.

4. Zarejestruj swoją subskrypcję dla przestrzeni nazw Microsoft. ClassicInfrastructureMigrate za pomocą [portalu](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [programu PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) lub [interfejsu wiersza polecenia](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate 
    ```
 
5. Zarejestruj swoją subskrypcję dla funkcji Cloud Services migracji w wersji zapoznawczej przy użyciu [portalu](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [programu PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) lub [interfejsu wiersza polecenia](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)

    ```powershell
    Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

6. Sprawdź stan rejestracji. Rejestracja może potrwać kilka minut. 

    ```powershell
    Get-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

## <a name="how-is-migration-for-cloud-services-classic-different-from-virtual-machines-classic"></a>Jak migracja Cloud Services (klasyczny) różni się od Virtual Machines (klasyczny)?
Usługa Azure Service Manager obsługuje dwa różne produkty obliczeniowe [: azure Virtual Machines (klasyczne)](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/tutorial-classic) i [Azure Cloud Services (klasyczne)](../cloud-services/cloud-services-choose-me.md) lub role sieci Web/proces roboczy. Te dwa produkty różnią się w zależności od typu wdrożenia, który należy do usługi w chmurze. Platforma Azure Cloud Services (klasyczna) korzysta z usługi w chmurze zawierającej wdrożenia z rolami sieci Web/procesu roboczego. Platforma Azure Virtual Machines (klasyczna) korzysta z usługi w chmurze zawierającej wdrożenia z maszynami wirtualnymi IaaS.

Lista obsługiwanych scenariuszy różni się od Cloud Services (klasyczny) i Virtual Machines (klasyczny) z powodu różnic w typach wdrożeń.

## <a name="migration-steps"></a>Kroki migracji
 
Klienci mogą migrować Cloud Services (klasyczne) wdrożenia przy użyciu tych samych czterech operacji, które są używane do migrowania Virtual Machines (klasyczne). 

1. **Sprawdź poprawność migracji** — sprawdza, czy migracja nie będzie uniemożliwiana przez wspólne nieobsługiwane scenariusze.
2. **Przygotowywanie migracji** — duplikuje metadane zasobów w Azure Resource Manager. Wszystkie zasoby są zablokowane dla operacji tworzenia/aktualizowania/usuwania, aby zapewnić synchronizację metadanych zasobów między usługą Azure Menedżer serwera i Azure Resource Manager. Wszystkie operacje odczytu będą działały przy użyciu interfejsów API zarówno Cloud Services (klasyczny), jak i Cloud Services (obsługa rozszerzona).
3. **Przerwij migrację** — usuwa metadane zasobów z Azure Resource Manager. Odblokowuje wszystkie zasoby dla operacji tworzenia/aktualizowania/usuwania.
4. **Zatwierdzanie migracji** — usuwa metadane zasobów z usługi Azure Service Manager. Odblokowuje zasób do operacji tworzenia/aktualizowania/usuwania. Przerwanie nie jest już dozwolone po próbie zatwierdzenia.

>[!NOTE]
> Przygotowywanie, przerywanie i zatwierdzanie to idempotentne, dlatego w przypadku niepowodzenia spróbuj rozwiązać ten problem.

:::image type="content" source="media/in-place-migration-1.png" alt-text="Obraz przedstawia diagram kroków związanych z migracją.":::

Aby uzyskać więcej informacji, zobacz [Omówienie migracji obsługiwanej przez platformę zasobów IaaS z klasycznej do Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)

## <a name="supported-resources-and-features-available-for-migration-associated-with-cloud-services-classic"></a>Obsługiwane zasoby i funkcje dostępne dla migracji skojarzonej z Cloud Services (wersja klasyczna)
-   Konta magazynu
-   Sieci wirtualne
-   Grupy zabezpieczeń sieci
-   Zastrzeżone publiczne adresy IP
-   Access Control punktów końcowych
-   Trasy definiowane przez użytkownika
-   Wewnętrzny moduł równoważenia obciążenia
-   Migracja certyfikatu do magazynu kluczy
-   Wtyczki i rozszerzenie (oparte na języku XML i JSON)
-   Przy zadaniach Start/on Stop
-   Wdrożenia z przyspieszoną siecią
-   Wdrożenia przy użyciu jednej lub wielu ról
-   Podstawowa usługa równoważenia obciążenia
-   Dane wejściowe, dane wejściowe wystąpienia, wewnętrzne punkty końcowe
-   Dynamiczne publiczne adresy IP
-   Nazwa DNS 
-   Reguły ruchu sieciowego
-   Sieć wirtualna Hypernet

## <a name="supported-configurations--migration-scenarios"></a>Obsługiwane konfiguracje/scenariusze migracji
Są to najważniejsze scenariusze dotyczące kombinacji zasobów, funkcji i Cloud Services. Ta lista nie jest wyczerpująca.

| Usługa | Konfigurowanie | Komentarze | 
|---|---|---|
| [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet) | Sieci wirtualne, które zawierają Azure Active Directory usługi domenowe. | Obsługiwana jest sieć wirtualna zawierająca zarówno wdrożenie usługi w chmurze, jak i usługi domenowe Azure AD. Klient najpierw musi osobno przeprowadzić migrację usług domenowych Azure AD, a następnie przeprowadzić migrację sieci wirtualnej tylko do wdrożenia usługi w chmurze. |
| Usługa w chmurze | Usługa w chmurze z wdrożeniem tylko w jednym gnieździe. | Cloud Services zawierający wdrożenie w miejscu produkcyjnym lub tymczasowym można migrować |
| Usługa w chmurze | Wdrożenie nie należy do publicznie widocznej sieci wirtualnej (domyślne wdrożenie sieci wirtualnej) | Usługa w chmurze może znajdować się w publicznie widocznej sieci wirtualnej w ukrytej sieci wirtualnej lub nie w żadnej sieci wirtualnej.  Cloud Services w ukrytej sieci wirtualnej i publicznie widoczne sieci wirtualne są obsługiwane na potrzeby migracji. Klient może użyć interfejsu API sprawdzania poprawności, aby sprawdzić, czy wdrożenie znajduje się w domyślnej sieci wirtualnej, czy nie, i w ten sposób określić, czy można przeprowadzić migrację. |
|Usługa w chmurze | Rozszerzenia XML (BGInfo, debuger programu Visual Studio, Web Deploy i zdalne debugowanie). | Wszystkie rozszerzenia XML są obsługiwane na potrzeby migracji 
| Virtual Network | Sieć wirtualna zawierająca wiele Cloud Services. | Usługa Virtual Network zawiera wiele usług w chmurze, które są obsługiwane przez migrację. Sieć wirtualna i wszystkie znajdujące się w niej Cloud Services zostaną zmigrowane ze sobą do Azure Resource Manager. |
| Virtual Network | Migracja sieci wirtualnych utworzonych za pośrednictwem portalu (wymaga użycia opcji "Grupuj zasób-grupa-Nazwa sieci wirtualnej" w pliku cscfg)  | W ramach migracji nazwa sieci wirtualnej w cscfg zostanie zmieniona tak, aby używała identyfikatora Azure Resource Manager sieci wirtualnej. (subskrypcja/subskrypcja-ID/Resource-Group/Resource-Group-Name/Resource/VNET-Name) <br><br>Aby zarządzać wdrożeniem po migracji, należy zaktualizować lokalną kopię pliku cscfg, aby rozpocząć korzystanie z identyfikatora Azure Resource Manager zamiast nazwy sieci wirtualnej. <br><br>Plik. cscfg, który używa starego schematu nazewnictwa, nie będzie przekazywać walidacji. 
| Virtual Network | Migracja wdrożenia z rolami w różnych podsieciach. | Usługa w chmurze z różnymi rolami w różnych podsieciach jest obsługiwana na potrzeby migracji. |
    

## <a name="resources-and-features-not-available-for-migration"></a>Zasoby i funkcje niedostępne do migracji
Są to najważniejsze scenariusze dotyczące kombinacji zasobów, funkcji i Cloud Services. Ta lista nie jest wyczerpująca. 

| Zasób | Następne kroki/obejście | 
|---|---|
| Reguły automatycznego skalowania | Migracja odbywa się, ale reguły są usuwane. [Ponownie utwórz reguły](https://docs.microsoft.com/azure/cloud-services-extended-support/configure-scaling) po migracji na Cloud Services (obsługa rozszerzona). | 
| Alerty | Migracja przechodzi przez, ale alerty są porzucane. [Ponownie utwórz reguły](https://docs.microsoft.com/azure/cloud-services-extended-support/enable-alerts) po migracji na Cloud Services (obsługa rozszerzona). | 
| VPN Gateway | Przed rozpoczęciem migracji Usuń VPN Gateway, a następnie ponownie utwórz VPN Gateway po zakończeniu migracji. | 
| Brama usługi Express Route (w tej samej subskrypcji tylko w Virtual Network) | Przed rozpoczęciem migracji Usuń bramę Express Route Gateway, a następnie ponownie utwórz bramę po zakończeniu migracji. | 
| limit przydziału  | Nie przeprowadzono migracji przydziału. [Zażądaj nowego przydziału](https://docs.microsoft.com/azure/azure-resource-manager/templates/error-resource-quota#solution) na Azure Resource Manager przed migracją, aby sprawdzenie poprawności zakończyło się pomyślnie. | 
| Grupy koligacji | Nieobsługiwane. Przed migracją Usuń wszystkie grupy koligacji.  | 
| Sieci wirtualne korzystające z [komunikacji równorzędnej sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)| Przed przeprowadzeniem migracji sieci wirtualnej, która jest połączona z inną siecią wirtualną, należy usunąć komunikację równorzędną, przeprowadzić migrację sieci wirtualnej do Menedżer zasobów i ponownie utworzyć komunikację równorzędną. Może to spowodować przestoje w zależności od architektury. | 
| Sieci wirtualne, które zawierają środowiska App Service | Nieobsługiwane | 
| Sieci wirtualne, które zawierają usługi HDInsight | Nieobsługiwane. 
| Sieci wirtualne zawierające wdrożenia usługi Azure API Management | Nieobsługiwane. <br><br> Aby przeprowadzić migrację sieci wirtualnej, należy zmienić sieć wirtualną wdrożenia API Management. To nie jest operacja przestoju. | 
| Klasyczne obwody usługi Express Route | Nieobsługiwane. <br><br>Przed rozpoczęciem migracji PaaS należy zmigrować te obwody do Azure Resource Manager. Aby dowiedzieć się więcej, zobacz [Przechodzenie obwodów usługi ExpressRoute z klasycznego modelu wdrażania do Menedżer zasobów](../expressroute/expressroute-howto-move-arm.md). |  
| Kontrola dostępu oparta na rolach | Po migracji należy zaktualizować identyfikator URI zasobu z zasad kontroli `Microsoft.ClassicCompute` `Microsoft.Compute` RBAC po migracji. | 
| Application Gateway | Nieobsługiwane. <br><br> Przed rozpoczęciem migracji Usuń Application Gateway, a następnie ponownie utwórz Application Gateway po zakończeniu migracji do Azure Resource Manager | 

## <a name="unsupported-configurations--migration-scenarios"></a>Nieobsługiwane konfiguracje/scenariusze migracji

| Konfiguracja/scenariusz  | Następne kroki/obejście | 
|---|---|
| Migracja niektórych starszych wdrożeń poza siecią wirtualną | Niektóre wdrożenia usługi w chmurze, które nie znajdują się w sieci wirtualnej, nie są obsługiwane na potrzeby migracji. <br><br> 1. Użyj interfejsu API sprawdzania poprawności, aby sprawdzić, czy wdrożenie kwalifikuje się do migracji. <br> 2. Jeśli są odpowiednie, wdrożenia zostaną przeniesione do Azure Resource Manager w ramach sieci wirtualnej z prefiksem "DefaultRdfeVnet" | 
| Migracja wdrożeń zawierających zarówno wdrożenie produkcyjne, jak i miejsce przejściowe przy użyciu dynamicznych adresów IP | Migracja dwóch gniazd usługi w chmurze wymaga usunięcia miejsca przejściowego. Po usunięciu miejsca przejściowego należy zmigrować miejsce produkcyjne jako niezależną usługę w chmurze (rozszerzoną obsługę) w Azure Resource Manager. Następnie wdróż ponownie środowisko przejściowe jako nową usługę w chmurze (pomoc rozszerzona) i zamień je na pierwszy z nich. | 
| Migracja wdrożeń zawierających zarówno wdrożenie produkcyjne, jak i miejsce przejściowe przy użyciu adresów Zastrzeżony adres IP | Nieobsługiwane. | 
| Migracja wdrożenia produkcyjnego i przejściowego w innej sieci wirtualnej|Migracja dwóch gniazd usługi w chmurze wymaga usunięcia miejsca przejściowego. Po usunięciu miejsca przejściowego należy zmigrować miejsce produkcyjne jako niezależną usługę w chmurze (rozszerzoną obsługę) w Azure Resource Manager. Nowe wdrożenie Cloud Services (obsługa rozszerzona) można następnie połączyć z zmigrowanym wdrożeniem z włączoną właściwością z możliwością zamiany. Pliki wdrożeń starego wdrożenia miejsca przemieszczania mogą być ponownie używane do tworzenia nowego wdrożenia z możliwością wymiany. | 
| Migracja pustej usługi w chmurze (usługa w chmurze bez wdrożenia) | Nieobsługiwane. | 
| Migracja wdrożenia zawierającego wtyczkę pulpitu zdalnego i rozszerzenia pulpitu zdalnego | Opcja 1: przed migracją Usuń wtyczkę pulpitu zdalnego. Wymaga to zmian plików wdrożenia. Następnie migracja zostanie przestawiona przez program. <br><br> Opcja 2: Usuwanie rozszerzenia pulpitu zdalnego i migrowanie wdrożenia. Po migracji, Usuń wtyczkę i zainstaluj rozszerzenie. Wymaga to zmian plików wdrożenia. <br><br> Przed migracją Usuń wtyczkę i rozszerzenie. [Wtyczki nie są zalecane](https://docs.microsoft.com/azure/cloud-services-extended-support/deploy-prerequisite#required-service-definition-file-csdef-updates) do użycia w Cloud Services (obsługa rozszerzona).| 
| Sieci wirtualne z wdrożeniem PaaS i IaaS |Nieobsługiwane <br><br> Przenieś wdrożenia PaaS lub IaaS do innej sieci wirtualnej. Spowoduje to przestoje. | 
Wdrożenia usługi w chmurze korzystające ze starszych rozmiarów ról (na przykład Small lub ExtraLarge). | Migracja zostanie ukończona, ale rozmiary ról zostaną zaktualizowane w celu używania nowoczesnych rozmiarów ról. Nie wprowadzono zmian w właściwościach kosztu lub jednostki SKU, a maszyna wirtualna nie zostanie ponownie uruchomiona dla tej zmiany. Zaktualizuj wszystkie artefakty wdrażania, aby odwoływać się do tych nowych rozmiarów nowoczesnych ról. Aby uzyskać więcej informacji, zobacz [Dostępne rozmiary maszyn wirtualnych](available-sizes.md)|
| Migracja usługi w chmurze do innej sieci wirtualnej | Nieobsługiwane <br><br> 1. przed migracją Przenieś wdrożenie do innej klasycznej sieci wirtualnej. Spowoduje to przestoje. <br> 2. Migruj nową sieć wirtualną do Azure Resource Manager. <br><br> Lub <br><br> 1. Przeprowadź migrację sieci wirtualnej do Azure Resource Manager <br>2. Przenieś usługę w chmurze do nowej sieci wirtualnej. Spowoduje to przestoje. | 
| Usługa w chmurze w sieci wirtualnej, ale nie ma przypisanej bezpośredniej podsieci | Nieobsługiwane. Środki zaradcze obejmują przeniesienie roli do podsieci, co wymaga ponownego uruchomienia roli (przestój) | 


## <a name="post-migration-changes"></a>Zmiany po migracji
Wdrożenie Cloud Services (klasycznego) jest konwertowane na wdrożenie usługi w chmurze (obsługa rozszerzona). Więcej informacji można znaleźć w [dokumentacji Cloud Services (obsługa rozszerzona)](deploy-prerequisite.md) .  

### <a name="changes-to-deployment-files"></a>Zmiany w plikach wdrożenia 

Drobne zmiany są wprowadzane w pliku. csdef i. cscfg klienta, aby pliki wdrożenia były zgodne z wymaganiami Azure Resource Manager i Cloud Services (obsługa rozszerzona). Po migracji są pobierane nowe pliki wdrożenia lub aktualizowane istniejące. Będzie to potrzebne w przypadku operacji aktualizowania/usuwania.  

- Virtual Network używa pełnego identyfikatora zasobu Azure Resource Manager zamiast nazwy zasobu w sekcji NetworkConfiguration pliku. cscfg. Na przykład `/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Network/virtualNetworks/vnet-name`. W przypadku sieci wirtualnych należących do tej samej grupy zasobów co usługa w chmurze można zaktualizować plik. cscfg z powrotem, używając tylko nazwy sieci wirtualnej.  

- Rozmiary klasyczne, takie jak małe, duże i ExtraLarge, są zastępowane przez nowe nazwy rozmiarów, Standard_A *. Nazwy rozmiarów należy zmienić na nowe nazwy w pliku. csdef. Aby uzyskać więcej informacji, zobacz [wymagania wstępne dotyczące wdrażania Cloud Services (obsługa rozszerzona)](deploy-prerequisite.md#required-service-definition-file-csdef-updates)

- Użyj interfejsu API pobierania, aby pobrać najnowszą kopię plików wdrożenia. 
    - Pobierz szablon przy użyciu [portalu](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal), [programu PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-powershell#export-resource-groups-to-templates), interfejsu [wiersza polecenia](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-cli#export-resource-groups-to-templates)i [API REST](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate) 
    - Pobierz plik. csdef za pomocą [programu PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) lub [interfejsu API REST](https://docs.microsoft.com/rest/api/compute/cloudservices/rest-get-package). 
    - Pobierz plik cscfg przy użyciu [programu PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) lub [interfejsu API REST](https://docs.microsoft.com/rest/api/compute/cloudservices/rest-get-package). 
    
 

### <a name="changes-to-customers-automation-cicd-pipeline-custom-scripts-custom-dashboards-custom-tooling-etc"></a>Zmiany w zakresie automatyzacji, ciągłej integracji i ciągłego wdrażania klienta, niestandardowe skrypty, niestandardowe pulpity nawigacyjne, niestandardowe narzędzia itd.  

Klienci muszą zaktualizować swoje narzędzia i automatyzację, aby rozpocząć korzystanie z nowych interfejsów API/poleceń w celu zarządzania ich wdrożeniem. Klient może łatwo przyjmować nowe funkcje i możliwości Azure Resource Manager/Cloud Services (obsługa rozszerzona) w ramach tej zmiany. 

- Zmiany nazw zasobów i grup zasobów po migracji
    - W ramach migracji nazwy kilku zasobów, takich jak usługa w chmurze, publiczne adresy IP itp. Te zmiany mogą wymagać odzwierciedlenia w plikach wdrożenia przed aktualizacją usługi w chmurze. [Dowiedz się więcej o zmianach nazw zasobów](in-place-migration-technical-details.md#translation-of-resources-and-naming-convention-post-migration).  

- Ponowne tworzenie zasad i zasad wymaganych do zarządzania i skalowania usług w chmurze 
    - [Reguły skalowania automatycznego](configure-scaling.md) nie są migrowane. Po migracji ponownie utwórz reguły automatycznego skalowania.  
    - Nie przeprowadzono migracji [alertów](enable-alerts.md) . Po migracji ponownie utwórz alerty.
    - Key Vault jest tworzony bez żadnych zasad dostępu. [Utwórz odpowiednie zasady](../key-vault/general/assign-access-policy-portal.md) na Key Vault, aby wyświetlić certyfikaty i zarządzać nimi. Certyfikaty będą widoczne w obszarze Ustawienia na karcie o nazwie Secret.

## <a name="next-steps"></a>Następne kroki
- [Przegląd obsługiwanej przez platformę migracji zasobów IaaS z wersji klasycznej do Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)
- Migrowanie do Cloud Services (obsługa rozszerzona) przy użyciu [Azure Portal](in-place-migration-portal.md)
- Migrowanie do Cloud Services (obsługa rozszerzona) przy użyciu [programu PowerShell](in-place-migration-powershell.md)
