---
title: Azure API Management — często zadawane pytania | Microsoft Docs
description: Poznaj odpowiedzi na często zadawane pytania, wzorce i najlepsze rozwiązania w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1b6a138317d0cc2e10e893d1969f9d5452064d8f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813744"
---
# <a name="azure-api-management-faqs"></a>Azure API Management często zadawane pytania
Uzyskaj odpowiedzi na często zadawane pytania, wzorce i najlepsze rozwiązania dotyczące usługi Azure API Management.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="frequently-asked-questions"></a>Często zadawane pytania
* [Co to znaczy, że funkcja jest w wersji zapoznawczej?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Jak mogę zabezpieczyć połączenie między bramą usługi API Management i usługami zaplecza?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Jak mogę skopiować moje wystąpienie usługi API Management do nowego wystąpienia?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Czy mogę zarządzać API Management programowo?](#can-i-manage-my-api-management-instance-programmatically)
* [Jak dodać użytkownika do grupy administratorów?](#how-do-i-add-a-user-to-the-administrators-group)
* [Dlaczego zasady, które chcę dodać, są niedostępne w edytorze zasad?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Jak mogę skonfigurować wiele środowisk w jednym interfejsie API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Czy można używać protokołu SOAP z API Management?](#can-i-use-soap-with-api-management)
* [Czy można skonfigurować serwer autoryzacji OAuth 2.0 z AD FS zabezpieczeń?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [Jakiej metody routingu API Management używać we wdrożeniach w wielu lokalizacjach geograficznych?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Czy mogę użyć szablonu Azure Resource Manager, aby utworzyć API Management usługi?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Czy mogę użyć certyfikatu TLS/SSL z podpisem własnym dla serwera końcowego?](#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)
* [Dlaczego otrzymuję błąd uwierzytelniania podczas próby sklonowania repozytorium GIT?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Czy API Management z Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Dlaczego wymagamy dedykowanej podsieci w sieciach wirtualnych w Resource Manager sieci wirtualnych w API Management są w nich wdrażane?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Jaki jest minimalny rozmiar podsieci wymagany podczas wdrażania API Management sieci wirtualnej?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Czy mogę przenieść usługę API Management z jednej subskrypcji do innej](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Czy istnieją ograniczenia dotyczące lub znane problemy z importowaniem interfejsu API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Co to znaczy, że funkcja jest w wersji zapoznawczej?
Gdy funkcja jest w wersji zapoznawczej, oznacza to, że aktywnie poszukujemy opinii na temat sposobu, w jaki funkcja działa dla Ciebie. Funkcja w wersji zapoznawczej jest funkcjonalnie ukończona, ale istnieje możliwość, że w odpowiedzi na opinie klientów dokonamy istotnej zmiany. Zalecamy, aby nie polegać na funkcji, która jest dostępna w wersji zapoznawczej w środowisku produkcyjnym.

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Jak mogę zabezpieczyć połączenie między bramą usługi API Management i usługami zaplecza?
Dostępnych jest kilka opcji zabezpieczania połączenia między bramą API Management a usługami back-end. Oto co możesz zrobić:

* Użyj uwierzytelniania podstawowego protokołu HTTP. Aby uzyskać więcej informacji, zobacz [Importowanie i publikowanie pierwszego interfejsu API.](import-and-publish.md)
* Użyj wzajemnego uwierzytelniania protokołu TLS zgodnie z opisem w tesłudze Jak zabezpieczyć usługi za pomocą uwierzytelniania certyfikatu klienta w [usłudze Azure API Management](api-management-howto-mutual-certificates.md).
* Użyj filtrowania adresów IP w usłudze back-end. We wszystkich warstwach usługi API Management z wyjątkiem warstwy Zużycie adres IP bramy pozostaje stały, z kilkoma zastrzeżeniami opisanymi w artykule dokumentacji [adresu IP](api-management-howto-ip-addresses.md).
* Połącz wystąpienie API Management z usługą Azure Virtual Network.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Jak mogę skopiować moje wystąpienie usługi API Management do nowego wystąpienia?
Jeśli chcesz skopiować wystąpienie API Management do nowego wystąpienia, masz kilka opcji. Oto co możesz zrobić:

* Użyj funkcji tworzenia kopii zapasowej i przywracania w API Management. Aby uzyskać więcej informacji, zobacz How to implement disaster recovery by using service backup and restore in Azure API Management (Jak zaimplementować odzyskiwanie po awarii przy użyciu funkcji tworzenia kopii zapasowej i przywracania [usługi w usłudze Azure API Management).](api-management-howto-disaster-recovery-backup-restore.md)
* Utwórz własną funkcję tworzenia kopii zapasowej i przywracania przy użyciu API Management [API REST.](/rest/api/apimanagement/) Użyj interfejsu API REST, aby zapisać i przywrócić jednostki z wystąpienia usługi, którego potrzebujesz.
* Pobierz konfigurację usługi przy użyciu usługi Git, a następnie przekaż ją do nowego wystąpienia. Aby uzyskać więcej informacji, [zobacz How to save and configure your API Management service configuration by using Git](api-management-configuration-repository-git.md)(Jak zapisać i skonfigurować konfigurację usługi API Management Git).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Czy mogę zarządzać wystąpieniem API Management programowo?
Tak, można zarządzać API Management programowo przy użyciu:

* Interfejs [API REST API Management .](/rest/api/apimanagement/)
* Zestaw SDK Microsoft Azure [ApiManagement Service Management Library](https://aka.ms/apimsdk).
* Polecenia [cmdlet](/powershell/module/wds) programu PowerShell [wdrażania](/powershell/azure/servicemanagement/overview) usług i zarządzania usługami.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Jak dodać użytkownika do grupy administratorów?
Grupy administratorów to niezmienna grupa systemowa. Administratorzy subskrypcji platformy Azure są członkami tej grupy. Nie można dodać użytkownika do tej grupy. Aby uzyskać więcej informacji, zobacz How to create and use groups to manage developer accounts in Azure API Management (Jak tworzyć grupy i używać ich do zarządzania kontami deweloperów [w usłudze Azure API Management).](./api-management-howto-create-groups.md)

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Dlaczego zasady, które chcę dodać, są niedostępne w edytorze zasad?
Jeśli zasady, które chcesz dodać, są wyszarzone lub zacienione w edytorze zasad, upewnij się, że jesteś we właściwym zakresie zasad. Każda instrukcja zasad jest przeznaczona do użycia w określonych zakresach i sekcjach zasad. Aby przejrzeć sekcje zasad i zakresy zasad, zobacz sekcję Użycie zasad w [te API Management zasad.](./api-management-policies.md)

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Jak mogę skonfigurować wiele środowisk w jednym interfejsie API?
Aby skonfigurować wiele środowisk, na przykład środowisko testowe i środowisko produkcyjne, w jednym interfejsie API masz dwie opcje. Oto co możesz zrobić:

* Hostowanie różnych interfejsów API w tej samej dzierżawie.
* Hostuj te same interfejsy API w różnych dzierżawach.

### <a name="can-i-use-soap-with-api-management"></a>Czy można używać protokołu SOAP z API Management?
[Obsługa przekazania protokołu SOAP](https://azure.microsoft.com/blog/soap-pass-through/) jest teraz dostępna. Administratorzy mogą importować dane WSDL usługi SOAP, a usługa Azure API Management utworzy frontonie protokołu SOAP. Dokumentacja portalu dla deweloperów, konsola testowa, zasady i analiza są dostępne dla usług PROTOKOŁU SOAP.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Czy można skonfigurować serwer autoryzacji OAuth 2.0 z AD FS zabezpieczeń?
Aby dowiedzieć się, jak skonfigurować serwer autoryzacji OAuth 2.0 z zabezpieczeniami usługi Active Directory Federation Services (AD FS), zobacz Korzystanie z usług [ADFS](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/)w systemie API Management .

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Jakiej metody routingu API Management używać we wdrożeniach w wielu lokalizacjach geograficznych?
API Management używa metody [routingu ruchu o wydajności we](../traffic-manager/traffic-manager-routing-methods.md#performance) wdrożeniach w wielu lokalizacjach geograficznych. Ruch przychodzący jest przekierowyny do najbliższej bramy interfejsu API. Jeśli jeden region przejdzie w tryb offline, ruch przychodzący zostanie automatycznie przekierowyny do następnej najbliższej bramy. Dowiedz się więcej o metodach routingu [w Traffic Manager routingu.](../traffic-manager/traffic-manager-routing-methods.md)

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Czy mogę użyć szablonu Azure Resource Manager do utworzenia API Management usługi?
Tak. Zobacz szablony [szybkiego startu usługi Azure API Management Service.](https://aka.ms/apimtemplate)

### <a name="can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end"></a>Czy mogę użyć certyfikatu TLS/SSL z podpisem własnym dla serwera końcowego?
Tak. Można to zrobić za pomocą programu PowerShell lub bezpośrednio przesyłając do interfejsu API. Spowoduje to wyłączenie weryfikacji łańcucha certyfikatów i umożliwi korzystanie z certyfikatów z podpisem własnym lub z podpisem prywatnym podczas komunikacji API Management z usługami back end.

#### <a name="powershell-method"></a>Metoda programu PowerShell ####
Użyj poleceń [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) cmdlet programu PowerShell (dla nowego środowiska back end) lub (dla istniejącego zakańcowego) i ustaw [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) `-SkipCertificateChainValidation` parametr na wartość `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Metoda bezpośredniej aktualizacji interfejsu API ####
1. Tworzenie [jednostki zaplecza](/rest/api/apimanagement/) przy użyciu API Management.     
2. Ustaw właściwość **skipCertificateChainValidation na** **wartość true.**     
3. Jeśli nie chcesz już zezwalać na certyfikaty z podpisem własnym, usuń jednostkę Backend lub ustaw właściwość **skipCertificateChainValidation** na **wartość false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Dlaczego podczas próby sklonowania repozytorium Git otrzymuję komunikat o niepowodzeniu uwierzytelniania?
Jeśli używasz narzędzia Git Menedżer poświadczeń lub jeśli próbujesz sklonować repozytorium Git przy użyciu usługi Visual Studio, może wystąpić znany problem z oknom dialogowym poświadczeń systemu Windows. Okno dialogowe ogranicza długość hasła do 127 znaków i obcina hasło wygenerowane przez firmę Microsoft. Pracujemy nad skróceniem hasła. Na razie sklonuj repozytorium Git za pomocą powłoki Git Bash.

### <a name="does-api-management-work-with-azure-expressroute"></a>Czy API Management z Azure ExpressRoute?
Tak. API Management współpracuje z Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Dlaczego wymagamy dedykowanej podsieci w sieciach wirtualnych w Resource Manager sieci wirtualnych w API Management są w nich wdrażane?
Wymaganie dotyczące dedykowanej podsieci API Management wynika z faktu, że jest ona zbudowana na klasycznym modelu wdrażania (warstwa PAAS V1). Chociaż możemy wdrożyć w Resource Manager wirtualnej (warstwa V2), istnieją do tego konsekwencje. Klasyczny model wdrażania na platformie Azure nie jest ściśle powiązany z modelem Resource Manager, dlatego jeśli utworzysz zasób w warstwie V2, warstwa V1 nie wie o tym i mogą wystąpić problemy, takie jak API Management próba użycia adresu IP, który jest już przydzielony do karty sieciowej (wbudowanej w wersji 2).
Aby dowiedzieć się więcej na temat różnic między modelami klasycznymi i Resource Manager na platformie Azure, zapoznaj się z [różnicą w modelach wdrażania.](../azure-resource-manager/management/deployment-models.md)

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Jaki jest minimalny rozmiar podsieci wymagany podczas wdrażania API Management sieci wirtualnej?
Minimalny rozmiar podsieci wymagany do wdrożenia API Management podsieci [to /29](../virtual-network/virtual-networks-faq.md#configuration), co jest minimalnym rozmiarem podsieci, który obsługuje platforma Azure.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Czy mogę przenieść usługę API Management z jednej subskrypcji do innej
Tak. Aby dowiedzieć się, jak to zrobić, [zobacz Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Czy istnieją ograniczenia dotyczące lub znane problemy z importowaniem interfejsu API?
[Znane problemy i ograniczenia dotyczące](api-management-api-import-restrictions.md) formatów Open API (Swagger), WSDL i WADL.
