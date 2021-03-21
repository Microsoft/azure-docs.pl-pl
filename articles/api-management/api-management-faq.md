---
title: Azure API Management — często zadawane pytania | Microsoft Docs
description: Poznaj odpowiedzi na często zadawane pytania, wzorce i najlepsze praktyki dotyczące usługi Azure API Management.
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
ms.openlocfilehash: eea3c8525d31a3ca551e9cbc7d21d7dde163b5cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94697989"
---
# <a name="azure-api-management-faqs"></a>Azure API Management — często zadawane pytania
Uzyskaj odpowiedzi na często zadawane pytania, wzorce oraz najlepsze rozwiązania dotyczące usługi Azure API Management.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="frequently-asked-questions"></a>Często zadawane pytania
* [Co oznacza, gdy funkcja jest w wersji zapoznawczej?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Jak mogę zabezpieczyć połączenie między bramą usługi API Management i usługami zaplecza?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Jak mogę skopiować moje wystąpienie usługi API Management do nowego wystąpienia?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Czy mogę programowo zarządzać moim wystąpieniem API Management?](#can-i-manage-my-api-management-instance-programmatically)
* [Jak dodać użytkownika do grupy administratorów?](#how-do-i-add-a-user-to-the-administrators-group)
* [Dlaczego zasady, które chcę dodać, są niedostępne w edytorze zasad?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Jak mogę skonfigurować wiele środowisk w jednym interfejsie API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Czy można używać protokołu SOAP z API Management?](#can-i-use-soap-with-api-management)
* [Czy można skonfigurować serwer autoryzacji OAuth 2,0 z zabezpieczeniami AD FS?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [Jakiej metody routingu API Management używać we wdrożeniach w wielu lokalizacjach geograficznych?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Czy można użyć szablonu Azure Resource Manager, aby utworzyć wystąpienie usługi API Management?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Czy mogę użyć certyfikatu TLS/SSL z podpisem własnym na potrzeby zaplecza?](#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)
* [Dlaczego otrzymuję błąd uwierzytelniania podczas próby sklonowania repozytorium GIT?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Czy API Management współpracuje z usługą Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Dlaczego w Menedżer zasobów stylu sieci wirtualnych jest wymagana dedykowana podsieć, gdy API Management jest w nich wdrożona?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Jaki jest minimalny rozmiar podsieci wymagany podczas wdrażania API Management w sieci wirtualnej?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Czy mogę przenieść usługę API Management z jednej subskrypcji do innej](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Czy istnieją ograniczenia dotyczące lub znane problemy z importowaniem interfejsu API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Co oznacza, gdy funkcja jest w wersji zapoznawczej?
Gdy funkcja jest w wersji zapoznawczej, oznacza to, że aktywnie szukamy opinii na temat sposobu działania tej funkcji. Funkcja w wersji zapoznawczej jest funkcjonalnie kompletna, ale istnieje możliwość, że w odpowiedzi na Opinie klientów wprowadzimy istotną zmianę. Zalecamy, aby nie zależały od funkcji, która jest w wersji zapoznawczej w środowisku produkcyjnym.

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Jak mogę zabezpieczyć połączenie między bramą usługi API Management i usługami zaplecza?
Istnieje kilka opcji zabezpieczania połączenia między bramą API Management i usługami zaplecza. Oto co możesz zrobić:

* Użyj uwierzytelniania podstawowego protokołu HTTP. Aby uzyskać więcej informacji, zobacz [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md).
* Uwierzytelniania wzajemnego protokołu TLS należy używać zgodnie z opisem w artykule [jak zabezpieczyć usługi zaplecza przy użyciu uwierzytelniania certyfikatu klienta w usłudze Azure API Management](api-management-howto-mutual-certificates.md).
* Użyj filtrowania adresów IP w usłudze zaplecza. We wszystkich warstwach API Management z wyjątkiem warstwy zużycia adres IP bramy pozostaje stały i z kilku zapisań opisanych w [artykule dokumentacji protokołu IP](api-management-howto-ip-addresses.md).
* Połącz wystąpienie API Management z usługą Azure Virtual Network.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Jak mogę skopiować moje wystąpienie usługi API Management do nowego wystąpienia?
Istnieje kilka opcji, jeśli chcesz skopiować wystąpienie API Management do nowego wystąpienia. Oto co możesz zrobić:

* Użyj funkcji tworzenia kopii zapasowych i przywracania w API Management. Aby uzyskać więcej informacji, zobacz [jak zaimplementować odzyskiwanie awaryjne przy użyciu kopii zapasowej i przywracania usługi w usłudze Azure API Management](api-management-howto-disaster-recovery-backup-restore.md).
* Utwórz własną funkcję tworzenia kopii zapasowych i przywracania za pomocą [interfejsu API REST API Management](/rest/api/apimanagement/). Za pomocą interfejsu API REST Zapisz i Przywróć jednostki z wystąpienia usługi, które chcesz.
* Pobierz konfigurację usługi za pomocą narzędzia Git, a następnie przekaż ją do nowego wystąpienia. Aby uzyskać więcej informacji, zobacz [jak zapisać i skonfigurować konfigurację usługi API Management za pomocą narzędzia Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Czy mogę programowo zarządzać moim wystąpieniem API Management?
Tak, możesz zarządzać API Management programowo przy użyciu:

* [Interfejs API REST API Management](/rest/api/apimanagement/).
* [Zestaw SDK biblioteki zarządzania usługą Microsoft Azure ApiManagement](https://aka.ms/apimsdk).
* Polecenia cmdlet programu PowerShell dotyczące [wdrażania usług](/powershell/module/wds) i [zarządzania usługami](/powershell/azure/servicemanagement/overview) .

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Jak dodać użytkownika do grupy administratorów?
Grupy Administratorzy to niezmienna Grupa systemowa. Administratorzy subskrypcji platformy Azure są członkami tej grupy. Nie można dodać użytkownika do tej grupy. Więcej informacji można znaleźć [w temacie How to Create and use Groups for Manage Developer accounts in Azure API Management](./api-management-howto-create-groups.md) .

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Dlaczego zasady, które chcę dodać, są niedostępne w edytorze zasad?
Jeśli zasady, które mają zostać dodane, są wyszarzone lub wyszarzone w edytorze zasad, upewnij się, że jesteś w prawidłowym zakresie dla zasad. Każda instrukcja zasad jest przeznaczona do użycia w określonych zakresach i sekcjach zasad. Aby zapoznać się z sekcjami zasad i zakresami zasad, zobacz sekcję użycie zasad w temacie [zasady API Management](./api-management-policies.md).

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Jak mogę skonfigurować wiele środowisk w jednym interfejsie API?
Aby skonfigurować wiele środowisk, na przykład środowisko testowe i środowisko produkcyjne, w jednym interfejsie API dostępne są dwie opcje. Oto co możesz zrobić:

* Hostowanie różnych interfejsów API w tej samej dzierżawie.
* Hostowanie tych samych interfejsów API w różnych dzierżawach.

### <a name="can-i-use-soap-with-api-management"></a>Czy można używać protokołu SOAP z API Management?
Obsługa [przekazywania protokołu SOAP](https://azure.microsoft.com/blog/soap-pass-through/) jest teraz dostępna. Administratorzy mogą zaimportować WSDL usługi SOAP, a platforma Azure API Management utworzy fronton protokołu SOAP. Dokumentacja portalu dla deweloperów, konsola testowa, zasady i analiza są dostępne dla usług SOAP.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Czy można skonfigurować serwer autoryzacji OAuth 2,0 z zabezpieczeniami AD FS?
Aby dowiedzieć się, jak skonfigurować serwer autoryzacji OAuth 2,0 z zabezpieczeniami Active Directory Federation Services (AD FS), zobacz [Korzystanie z usług AD FS w API Management](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Jakiej metody routingu API Management używać we wdrożeniach w wielu lokalizacjach geograficznych?
API Management używa [metody routingu ruchu wydajności](../traffic-manager/traffic-manager-routing-methods.md#performance) we wdrożeniach w wielu lokalizacjach geograficznych. Ruch przychodzący jest kierowany do najbliższej bramy interfejsu API. Jeśli jeden region przejdzie w tryb offline, ruch przychodzący jest automatycznie kierowany do następnej najbliższej bramy. Dowiedz się więcej o metodach routingu w [Traffic Manager metodach routingu](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Czy można użyć szablonu Azure Resource Manager, aby utworzyć wystąpienie usługi API Management?
Tak. Zobacz Szablony szybkiego startu [usługi Azure API Management](https://aka.ms/apimtemplate) .

### <a name="can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end"></a>Czy mogę użyć certyfikatu TLS/SSL z podpisem własnym na potrzeby zaplecza?
Tak. Można to zrobić za pomocą programu PowerShell lub przez bezpośrednie przesłanie do interfejsu API. Spowoduje to wyłączenie weryfikacji łańcucha certyfikatów i umożliwi korzystanie z certyfikatów z podpisem własnym lub z podpisem prywatnym podczas komunikowania się z API Management do usług zaplecza.

#### <a name="powershell-method"></a>PowerShell — Metoda ####
Użyj [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) (dla nowego zaplecza) lub [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (dla istniejących zaplecza) poleceń cmdlet programu PowerShell i ustaw `-SkipCertificateChainValidation` parametr na `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Metoda bezpośredniej aktualizacji interfejsu API ####
1. Utwórz jednostkę [zaplecza](/rest/api/apimanagement/) przy użyciu API Management.     
2. Ustaw właściwość **skipCertificateChainValidation** na **wartość true**.     
3. Jeśli nie chcesz już zezwalać na certyfikaty z podpisem własnym, Usuń jednostkę zaplecza lub ustaw właściwość **skipCertificateChainValidation** na **wartość false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Dlaczego podczas próby sklonowania repozytorium Git otrzymuję komunikat o niepowodzeniu uwierzytelniania?
W przypadku korzystania z programu Git Credential Manager lub jeśli próbujesz sklonować repozytorium Git w programie Visual Studio, możesz uruchomić w znanym problemie przy użyciu okna dialogowego poświadczenia systemu Windows. Okno dialogowe ogranicza długość hasła do 127 znaków i obcina hasło generowane przez firmę Microsoft. Pracujemy nad skróceniem hasła. Na razie użyj narzędzia Git bash, aby sklonować repozytorium git.

### <a name="does-api-management-work-with-azure-expressroute"></a>Czy API Management współpracuje z usługą Azure ExpressRoute?
Tak. API Management współpracuje z usługą Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Dlaczego w Menedżer zasobów stylu sieci wirtualnych jest wymagana dedykowana podsieć, gdy API Management jest w nich wdrożona?
Wymagania dedykowanej podsieci dla API Management wynikają z faktu, że jest on zbudowany na podstawie klasycznego modelu wdrażania (PAAS v1). Chociaż możemy wdrożyć program w Menedżer zasobów sieci wirtualnej (warstwa v2), istnieją pewne konsekwencje. Klasyczny model wdrażania na platformie Azure nie jest ściśle połączony z modelem Menedżer zasobów i dlatego jeśli utworzysz zasób w warstwie v2, warstwa w wersji 1 nie wie o tym, a mogą wystąpić problemy, takie jak API Management próbuje użyć adresu IP, który jest już przydzielony do karty sieciowej (opartej na wersji 2).
Aby dowiedzieć się więcej na temat różnic między modelami klasycznymi i Menedżer zasobówymi na platformie Azure, zapoznaj się z [różnicą w modelach wdrażania](../azure-resource-manager/management/deployment-models.md)

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Jaki jest minimalny rozmiar podsieci wymagany podczas wdrażania API Management w sieci wirtualnej?
Minimalny rozmiar podsieci wymagany do wdrożenia API Management to [/29](../virtual-network/virtual-networks-faq.md#configuration), czyli minimalny rozmiar podsieci obsługiwany przez platformę Azure.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Czy mogę przenieść usługę API Management z jednej subskrypcji do innej
Tak. Aby dowiedzieć się, jak [przenieść zasoby do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Czy istnieją ograniczenia dotyczące lub znane problemy z importowaniem interfejsu API?
[Znane problemy i ograniczenia](api-management-api-import-restrictions.md) dotyczące formatów Open API (Swagger), WSDL i WADL.
