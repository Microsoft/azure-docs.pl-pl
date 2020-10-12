---
title: Tworzenie środowiska ASE przy użyciu ARM
description: Dowiedz się, jak utworzyć zewnętrzne lub ILB środowisko App Service przy użyciu szablonu Azure Resource Manager.
author: ccompy
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a6b9ba8c3f72b070ad1ec9ade9684a182e2dea08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962404"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Tworzenie środowiska ASE przy użyciu szablonu Azure Resource Manager

## <a name="overview"></a>Omówienie

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Środowiska Azure App Service (środowisk ASE) można utworzyć za pomocą punktu końcowego dostępnego z Internetu lub punktu końcowego na adresie wewnętrznym w sieci wirtualnej platformy Azure. Po utworzeniu z wewnętrznym punktem końcowym ten punkt końcowy jest dostarczany przez składnik platformy Azure o nazwie wewnętrzny moduł równoważenia obciążenia (ILB). Środowisko ASE na wewnętrznym adresie IP nosi nazwę ILB ASE. Środowisko ASE z publicznym punktem końcowym jest nazywane zewnętrznym środowiskiem ASE. 

Środowisko ASE można utworzyć przy użyciu szablonu Azure Portal lub Azure Resource Manager. W tym artykule przedstawiono kroki i składnię, które należy wykonać, aby utworzyć zewnętrzne środowisko ASE lub ILB ASE z szablonami Menedżer zasobów. Aby dowiedzieć się, jak utworzyć środowisko ASE w Azure Portal, zobacz [Tworzenie zewnętrznego środowiska ASE][MakeExternalASE] lub [Tworzenie ILB środowiska ASE][MakeILBASE].

Podczas tworzenia środowiska ASE w Azure Portal można utworzyć sieć wirtualną w tym samym czasie lub wybrać istniejącą sieć wirtualną do wdrożenia. Podczas tworzenia środowiska ASE na podstawie szablonu należy zacząć od: 

* Menedżer zasobów sieci wirtualnej.
* Podsieć w tej sieci wirtualnej. Zalecamy używanie podsieci środowiska ASE z `/24` 256 adresami, aby sprostać przyszłym potrzebom wzrostu i skalowania. Po utworzeniu środowiska ASE nie można zmienić jego rozmiaru.
* Identyfikator zasobu z sieci wirtualnej. Te informacje można uzyskać z Azure Portal w obszarze właściwości sieci wirtualnej.
* Subskrypcja, w ramach której ma zostać wdrożone.
* Lokalizacja, w której ma zostać wdrożone.

Aby zautomatyzować tworzenie środowiska ASE:

1. Utwórz środowisko ASE na podstawie szablonu. Jeśli tworzysz zewnętrzny środowisko ASE, po wykonaniu tego kroku skończysz pracę. Jeśli tworzysz ILB ASE, możesz wykonać kilka czynności.

2. Po utworzeniu ILB ASE zostanie przekazany certyfikat TLS/SSL zgodny z domeną ILB ASE.

3. Przekazany certyfikat TLS/SSL jest przypisywany do ILB ASE jako "domyślny" certyfikat TLS/SSL.  Ten certyfikat jest używany na potrzeby ruchu TLS/SSL do aplikacji w ILB ASE, gdy używają wspólnej domeny głównej przypisanej do środowiska ASE (na przykład `https://someapp.mycustomrootdomain.com` ).


## <a name="create-the-ase"></a>Tworzenie środowiska ASE
Menedżer zasobów szablon, który tworzy środowisko ASE i skojarzony z nim plik parametrów, jest dostępny na [przykład][quickstartasev2create] w witrynie GitHub.

Jeśli chcesz utworzyć ILB ASE, Użyj tych [przykładów][quickstartilbasecreate]szablonu Menedżer zasobów. Są one stosowane do tego przypadku użycia. Większość parametrów w *azuredeploy.parameters.js* pliku są wspólne dla tworzenia ILB środowisk ASE i zewnętrznych środowisk ASE. Poniższa lista wywołuje Parametry specjalne uwagi lub są unikatowe, gdy tworzysz ILB ASE:

* *internalLoadBalancingMode*: w większości przypadków należy ustawić tę wartość na 3, co oznacza, że zarówno ruch http/https na portach 80/443, jak i porty kontroli/kanału danych nasłuchune przez usługę FTP w środowisku ASE, będą powiązane z adresem wewnętrznym sieci wirtualnej przydzielonej przez ILB. Jeśli ta właściwość ma wartość 2, tylko porty powiązane z usługą FTP (zarówno kanały kontroli i danych) są powiązane z adresem ILB. Ruch HTTP/HTTPS pozostaje w publicznym wirtualnym adresie IP.
* *dnsSuffix*: ten parametr definiuje domyślną domenę główną, która jest przypisana do środowiska ASE. W publicznej odmianie Azure App Service domyślną domeną główną dla wszystkich aplikacji sieci Web jest *azurewebsites.NET*. Ponieważ ILB ASE jest wewnętrzny dla sieci wirtualnej klienta, nie ma sensu używania domyślnej domeny głównej usługi publicznej. Zamiast tego, ILB ASE powinien mieć domyślną domenę główną, która ma Sense w przypadku użycia w wewnętrznej sieci wirtualnej firmy. Na przykład firma Contoso Corporation może używać domyślnej domeny głównej *Internal-contoso.com* dla aplikacji, które mają być rozpoznawalne i dostępne tylko w sieci wirtualnej firmy Contoso. 
* *ipSslAddressCount*: ten parametr automatycznie przyjmuje wartość 0 w *azuredeploy.jsw* pliku, ponieważ ILB środowisk ASE ma tylko jeden adres ILB. Brak jawnych adresów IP-SSL dla ILB ASE. W związku z tym Pula adresów IP-SSL dla ILB ASE musi mieć wartość zero. W przeciwnym razie wystąpi błąd aprowizacji. 

Po wprowadzeniu *azuredeploy.parameters.jsw* pliku Utwórz środowisko ASE przy użyciu fragmentu kodu programu PowerShell. Zmień ścieżki plików tak, aby były zgodne z lokalizacjami plików szablonu Menedżer zasobów na komputerze. Pamiętaj, aby podać własne wartości nazwy wdrożenia Menedżer zasobów i nazwy grupy zasobów:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Tworzenie środowiska ASE trwa około godzinę. Następnie środowisko ASE zostanie wyświetlone w portalu na liście środowisk ASE dla subskrypcji, która wyzwoliła wdrożenie.

## <a name="upload-and-configure-the-default-tlsssl-certificate"></a>Przekazywanie i Konfigurowanie domyślnego certyfikatu TLS/SSL
Certyfikat TLS/SSL musi być skojarzony z środowiskiem ASE jako "domyślny" certyfikat TLS/SSL, który jest używany do ustanawiania połączeń TLS z aplikacjami. Jeśli domyślny sufiks DNS środowiska ASE to *Internal-contoso.com*, połączenie z `https://some-random-app.internal-contoso.com` wymaga certyfikatu TLS/SSL, który jest prawidłowy dla **. Internal-contoso.com*. 

Uzyskaj prawidłowy certyfikat TLS/SSL przy użyciu wewnętrznych urzędów certyfikacji, kupując certyfikat od zewnętrznego wystawcy lub korzystając z certyfikatu z podpisem własnym. Niezależnie od źródła certyfikatu TLS/SSL należy prawidłowo skonfigurować następujące atrybuty certyfikatu:

* **Podmiot**: ten atrybut musi być ustawiony na **. your-root-Domain-here.com*.
* **Alternatywna nazwa podmiotu**: ten atrybut musi zawierać zarówno **. your-root-Domain-here.com* , jak i **. SCM.your-root-Domain-here.com*. Połączenia TLS z witryną SCM/kudu skojarzoną z każdą aplikacją używają adresu formularza *Your-App-Name.SCM.your-root-Domain-here.com*.

Mając na ręką prawidłowy certyfikat TLS/SSL, wymagane są dwa dodatkowe kroki przygotowawcze. Przekonwertuj/Zapisz certyfikat TLS/SSL jako plik PFX. Należy pamiętać, że plik PFX musi zawierać wszystkie certyfikaty pośrednie i główne. Zabezpiecz go przy użyciu hasła.

Plik. pfx należy przekonwertować na ciąg Base64, ponieważ certyfikat TLS/SSL jest przekazywany przy użyciu szablonu Menedżer zasobów. Ponieważ szablony Menedżer zasobów są plikami tekstowymi, plik PFX musi być konwertowany na ciąg w formacie base64. W ten sposób można go uwzględnić jako parametr szablonu.

Użyj następującego fragmentu kodu programu PowerShell, aby:

* Wygeneruj certyfikat z podpisem własnym.
* Wyeksportuj certyfikat jako plik PFX.
* Przekonwertuj plik pfx na ciąg szyfrowany algorytmem Base64.
* Zapisz ciąg szyfrowany algorytmem Base64 w oddzielnym pliku. 

Ten kod programu PowerShell dla kodowania base64 został dostosowany z [blogu skrypty programu PowerShell][examplebase64encoding]:

```powershell
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

Po pomyślnym wygenerowaniu i przekonwertowaniu certyfikatu TLS/SSL na ciąg szyfrowany algorytmem Base64 Użyj przykładu Menedżer zasobów szablon [konfiguruje domyślny certyfikat SSL][quickstartconfiguressl] w serwisie GitHub. 

Parametry w *azuredeploy.parameters.jsw* pliku są wymienione tutaj:

* *appServiceEnvironmentName*: Nazwa KONFIGUROWANEgo ILB środowiska ASE.
* *existingAseLocation*: ciąg tekstowy zawierający region platformy Azure, w którym WDROŻONO ILB ASE.  Na przykład: "Południowo-środkowe stany USA".
* *pfxBlobString*: zakodowany w based64 ciąg reprezentacji pliku PFX. Użyj podanego wcześniej fragmentu kodu i skopiuj ciąg zawarty w pliku "exportedcert. pfx. B64". Wklej ją jako wartość atrybutu *pfxBlobString* .
* *hasło*: hasło użyte do zabezpieczenia pliku PFX.
* *certificateThumbprint*: odcisk palca certyfikatu. Jeśli ta wartość zostanie pobrana z programu PowerShell (na przykład *$Certificate. Odcisk palca* ze starszego fragmentu kodu), można użyć wartości jako. Jeśli skopiujesz wartość z okna dialogowego certyfikat systemu Windows, pamiętaj, aby rozdzielić spacje. *CertificateThumbprint* powinna wyglądać podobnie do AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName*: przyjazny identyfikator ciągu używany do wybrania tożsamości certyfikatu. Nazwa jest używana jako część unikatowego identyfikatora Menedżer zasobów jednostki *Microsoft. Web/Certificates* , która reprezentuje certyfikat TLS/SSL. Nazwa *musi* kończyć się następującym sufiksem: \_ yourASENameHere_InternalLoadBalancingASE. Azure Portal używa tego sufiksu jako wskaźnika, który służy do zabezpieczania środowiska ASE z włączoną obsługą ILB.

Poniżej przedstawiono skrócony przykład *azuredeploy.parameters.jsw* programie:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "appServiceEnvironmentName": {
      "value": "yourASENameHere"
    },
    "existingAseLocation": {
      "value": "East US 2"
    },
    "pfxBlobString": {
      "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
    },
    "password": {
      "value": "PASSWORDGOESHERE"
    },
    "certificateThumbprint": {
      "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
    },
    "certificateName": {
      "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
    }
  }
}
```

Po wprowadzeniu *azuredeploy.parameters.jsw* pliku Skonfiguruj domyślny certyfikat TLS/SSL przy użyciu fragmentu kodu programu PowerShell. Zmień ścieżki plików tak, aby pasowały do lokalizacji plików szablonów Menedżer zasobów znajdujących się na komputerze. Pamiętaj, aby podać własne wartości nazwy wdrożenia Menedżer zasobów i nazwy grupy zasobów:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Zastosowanie zmiany trwa około 40 minut na fronton środowiska ASE. Na przykład w przypadku domyślnego rozmiaru ASE, który używa dwóch frontonów, szablon zajmuje około godziny i 20 minut. Gdy szablon jest uruchomiony, środowisko ASE nie może skalować.  

Po zakończeniu szablonu aplikacje w ILB ASE mogą być dostępne za pośrednictwem protokołu HTTPS. Połączenia są zabezpieczane przy użyciu domyślnego certyfikatu TLS/SSL. Domyślny certyfikat TLS/SSL jest używany, gdy aplikacje na ILB ASE są rozkierowane przy użyciu kombinacji nazwy aplikacji i domyślnej nazwy hosta. Na przykład program `https://mycustomapp.internal-contoso.com` używa domyślnego certyfikatu TLS/SSL dla **. Internal-contoso.com*.

Jednak podobnie jak aplikacje działające w publicznej usłudze wielodostępnej, deweloperzy mogą konfigurować niestandardowe nazwy hostów dla poszczególnych aplikacji. Mogą również konfigurować unikatowe powiązania certyfikatu TLS/SSL SNI dla poszczególnych aplikacji.

## <a name="app-service-environment-v1"></a>Środowisko usługi App Service — wersja 1 ##
Środowisko App Service Environment występuje w dwóch wersjach: ASEv1 i ASEv2. Podane wcześniej informacje dotyczyły wersji 2 — ASEv2. W tej sekcji przedstawiono różnice między środowiskami ASEv1 i ASEv2.

W programie środowiska asev1 wszystkie zasoby są zarządzane ręcznie. Obejmuje to frontony, procesy robocze oraz adresy IP używane do obsługi połączeń SSL opartych na protokole IP. Aby można było skalować plan App Service, należy skalować pulę procesów roboczych, w której ma być hostowana.

W przypadku środowiska ASEv1 używany jest inny model cenowy niż w przypadku środowiska ASEv2. W przypadku środowiska ASEv1 płacisz za każdy przydzielony procesor vCPU. Obejmuje to procesorów wirtualnych vCPU, które są używane dla frontonów lub procesów roboczych, które nie obsługują żadnych obciążeń. W przypadku środowiska ASEv1 domyślny rozmiar w skali maksymalnej środowiska ASE to 55 hostów łącznie. Obejmuje to frontony i procesy robocze. Jedną z zalet środowiska ASEv1 jest to, że można je wdrożyć w klasycznej sieci wirtualnej oraz w sieci wirtualnej usługi Resource Manager. Aby dowiedzieć się więcej na temat środowiska ASEv1, zobacz [App Service Environment v1 introduction][ASEv1Intro] (Wprowadzenie do środowiska App Service Environment w wersji 1).

Aby utworzyć środowiska asev1 za pomocą szablonu Menedżer zasobów, zobacz [Tworzenie ILB ASE V1 z szablonem Menedżer zasobów][ILBASEv1Template].


<!--Links-->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../../app-service/configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md