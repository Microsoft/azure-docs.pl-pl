---
title: Tworzenie aplikacji ASE za pomocą arm
description: Dowiedz się, jak utworzyć środowisko zewnętrzne lub App Service wewnętrznego równoważenia obciążenia przy użyciu szablonu Azure Resource Manager wewnętrznego.
author: ccompy
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 10482538c819f9713e9940cffbeb5a1f966ddcf5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832054"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Tworzenie aplikacji ASE przy użyciu szablonu Azure Resource Manager aplikacji

## <a name="overview"></a>Omówienie

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure App Service (ASE) można tworzyć za pomocą punktu końcowego dostępnego z Internetu lub punktu końcowego pod adresem wewnętrznym w sieci wirtualnej platformy Azure. Po utworzeniu za pomocą wewnętrznego punktu końcowego ten punkt końcowy jest dostarczany przez składnik platformy Azure nazywany wewnętrznym równoważeniem obciążenia (ILB). Ase na wewnętrzny adres IP jest nazywany ase wewnętrznego równoważenia obciążenia. Ase z publicznym punktem końcowym jest nazywany zewnętrznym ase. 

AsE można utworzyć przy użyciu szablonu Azure Portal szablonu Azure Resource Manager ase. Ten artykuł zawiera instrukcje i składnię potrzebne do utworzenia zewnętrznego ase lub ILB ASE z Resource Manager szablonów. Aby dowiedzieć się, jak utworzyć ase w Azure Portal, zobacz Make [an External ASE][MakeExternalASE] or Make an ILB ASE (Tworzenie zewnętrznego ase lub tworzenie [ase z równoważeniem obciążenia).][MakeILBASE]

Podczas tworzenia ase w Azure Portal można utworzyć sieć wirtualną w tym samym czasie lub wybrać wcześniejszą sieć wirtualną do wdrożenia. Podczas tworzenia aplikacji ASE na podstawie szablonu należy zacząć od: 

* Sieć Resource Manager wirtualna.
* Podsieć w tej sieci wirtualnej. Zalecamy rozmiar podsieci ase (ASE) z 256 adresami w celu uwzględnienia przyszłych potrzeb w zakresie `/24` wzrostu i skalowania. Po utworzeniu ase nie można zmienić rozmiaru.
* Identyfikator zasobu z sieci wirtualnej. Te informacje można uzyskać z Azure Portal we właściwościach sieci wirtualnej.
* Subskrypcja, w której chcesz wdrożyć.
* Lokalizacja, w której chcesz wdrożyć.

Aby zautomatyzować tworzenie aplikacji ASE:

1. Utwórz ase na podstawie szablonu. Utworzenie zewnętrznego źródła danych jest zakończone po tym kroku. W przypadku tworzenia aplikacji ASE z równoważeniem obciążenia należy wykonać jeszcze kilka czynności.

2. Po utworzeniu aplikacji ASE z równoważeniem obciążenia jest przekazywany certyfikat protokołu TLS/SSL, który odpowiada domenie tego typu.

3. Przekazany certyfikat protokołu TLS/SSL jest przypisywany do ase z równoważeniem obciążenia jako jego "domyślny" certyfikat protokołu TLS/SSL.  Ten certyfikat jest używany na użytek ruchu TLS/SSL do aplikacji w asecie z równoważeniem obciążenia, gdy korzystają ze wspólnej domeny głównej przypisanej do tego systemu (na przykład `https://someapp.mycustomrootdomain.com` ).


## <a name="create-the-ase"></a>Tworzenie ase
Szablon Resource Manager, który tworzy ase i skojarzony z nim plik parametrów, jest dostępny w [przykładzie w witrynie][quickstartasev2create] GitHub.

Jeśli chcesz ująć w asE z równoważeniem obciążenia, użyj tych przykładów Resource Manager [szablonu][quickstartilbasecreate]. Są one dostosowane do tego przypadku użycia. Większość parametrów w pliku *azuredeploy.parameters.jssą* wspólne dla tworzenia ILB ASE i zewnętrznych ase. Na poniższej liście wywoływują parametry specjalnej uwagi lub są unikatowe podczas tworzenia aplikacji ASE z równoważeniem obciążenia:

* *internalLoadBalancingMode:* w większości przypadków ustaw tę wartość na 3, co oznacza, że zarówno ruch HTTP/HTTPS na portach 80/443, jak i porty kanału sterowania/danych nasłuchiwać przez usługę FTP w środowisku ASE, zostaną powiązane z wewnętrznym adresem sieci wirtualnej z przydzielonym wewnętrznym wewnętrznym adresem sieci wirtualnej. Jeśli ta właściwość jest ustawiona na 2, tylko porty związane z usługą FTP (kanały sterowania i kanały danych) są powiązane z adresem iLB. Ruch HTTP/HTTPS pozostaje na publicznych adresach VIP.
* *dnsSuffix:* ten parametr definiuje domyślną domenę główną przypisaną do usługi ASE. W publicznej wersji usługi Azure App Service domyślną domeną główną dla wszystkich aplikacji internetowych jest *azurewebsites.net*. Ze względu na to, że środowiska ASE z wewnętrznym równoważeniem obciążenia są wewnętrzne dla sieci wirtualnej klienta, użycie domyślnej domeny głównej usługi publicznej nie ma sensu. Zamiast tego środowiska ASE z wewnętrznym równoważeniem obciążenia powinny mieć domyślną domenę główną, która ma sens do użycia w wewnętrznej sieci wirtualnej firmy. Na przykład firma Contoso Corporation może  używać domyślnej domeny głównej internal-contoso.com dla aplikacji, które mają być rozpoznawalne i dostępne tylko w sieci wirtualnej firmy Contoso. 
* *ipSslAddressCount:* ten parametr automatycznie ustawia wartość domyślną na wartość 0 w pliku *azuredeploy.js,* ponieważ ase z równoważeniem obciążenia mają tylko jeden adres iLB. Nie ma jawnych adresów IP-SSL dla ase z równoważeniem obciążenia. W związku z tym pula adresów IP-SSL dla zestawu ASE z równoważeniem obciążenia musi być ustawiona na zero. W przeciwnym razie wystąpi błąd aprowizowania. 

Poazuredeploy.parameters.js *pliku* utwórz środowiska ASE przy użyciu fragmentu kodu programu PowerShell. Zmień ścieżki plików tak, aby Resource Manager lokalizacje plików szablonu na maszynie. Pamiętaj, aby podać własne wartości dla Resource Manager wdrożenia i nazwy grupy zasobów:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Trwa około godziny, aby utworzyć ase. Następnie w portalu na liście aplikacji ASE dla subskrypcji, która wyzwoliła wdrożenie, zostanie wyświetlona w portalu.

## <a name="upload-and-configure-the-default-tlsssl-certificate"></a>Przekazywanie i konfigurowanie "domyślnego" certyfikatu TLS/SSL
Certyfikat TLS/SSL musi być skojarzony z usługą ASE jako "domyślny" certyfikat TLS/SSL używany do nawiązywania połączeń TLS z aplikacjami. Jeśli domyślny sufiks DNS w asecie jest internal-contoso.com *,* połączenie do programu wymaga certyfikatu TLS/SSL, który jest prawidłowy dla `https://some-random-app.internal-contoso.com` **.internal-contoso.com.* 

Uzyskaj prawidłowy certyfikat TLS/SSL przy użyciu wewnętrznych urzędów certyfikacji, zakupu certyfikatu od wystawcy zewnętrznego lub certyfikatu z podpisem własnym. Niezależnie od źródła certyfikatu TLS/SSL należy prawidłowo skonfigurować następujące atrybuty certyfikatu:

* **Temat:** ten atrybut musi być ustawiony na **.your-root-domain-here.com*.
* **Alternatywna nazwa podmiotu:** ten atrybut musi zawierać zarówno **.your-root-domain-here.com* i **.scm.your-root-domain-here.com*. Połączenia TLS z witryną SCM/Kudu skojarzoną z każdą aplikacją używają adresu formularza *your-app-name.scm.your-root-domain-here.com*.

Mając pod ręką prawidłowy certyfikat TLS/SSL, potrzebne są dwa dodatkowe kroki przygotowawcze. Przekonwertuj/zapisz certyfikat TLS/SSL jako plik pfx. Pamiętaj, że plik pfx musi zawierać wszystkie certyfikaty pośrednie i główne. Zabezpiecz go przy użyciu hasła.

Plik pfx należy przekonwertować na ciąg base64, ponieważ certyfikat TLS/SSL jest przekazywany przy użyciu Resource Manager szablonu. Ponieważ Resource Manager to pliki tekstowe, plik pfx należy przekonwertować na ciąg base64. W ten sposób można do niego doć jako parametr szablonu.

Użyj następującego fragmentu kodu programu PowerShell, aby:

* Wygeneruj certyfikat z podpisem własnym.
* Wyeksportuj certyfikat jako plik pfx.
* Przekonwertuj plik pfx na ciąg zakodowany w formacie base64.
* Zapisz ciąg zakodowany w formacie base64 w oddzielnym pliku. 

Ten kod programu PowerShell do kodowania base64 został dostosowany z blogu [skryptów programu PowerShell:][examplebase64encoding]

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

Po pomyślnym wygenerowaniu certyfikatu TLS/SSL i przekonwertowaniu go na ciąg zakodowany w formacie base64 użyj przykładowego szablonu Resource Manager Konfigurowanie domyślnego certyfikatu [SSL][quickstartconfiguressl] w usłudze GitHub. 

Parametry w pliku *azuredeploy.parameters.js* są wymienione tutaj:

* *appServiceEnvironmentName:* nazwa skonfigurowanego wewnętrznego konta usługi ASE.
* *existingAseLocation:* ciąg tekstowy zawierający region platformy Azure, w którym wdrożono ase z równoważeniem obciążenia.  Na przykład: "Południowo-środkowe usa".
* *pfxBlobString:* reprezentacja ciągu w formacie based64 pliku pfx. Użyj przedstawionego wcześniej fragmentu kodu i skopiuj ciąg zawarty w pliku "exportedcert.pfx.b64". Wklej go jako wartość *atrybutu pfxBlobString.*
* *password:* hasło używane do zabezpieczania pliku pfx.
* *certificateThumbprint:* odcisk palca certyfikatu. Jeśli ta wartość zostanie pobrana z programu PowerShell (na przykład *$certificate. Odcisk palca* z wcześniejszego fragmentu kodu) umożliwia użycie wartości bez jej użycia. Jeśli skopiujemy wartość z okna dialogowego Certyfikat systemu Windows, pamiętaj, aby rozebrać dodatkowe spacje. *CertificateThumbprint* powinien wyglądać podobnie do AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName:* przyjazny identyfikator ciągu, który został przez Użytkownika użyty do tożsamości certyfikatu. Nazwa jest używana jako część unikatowego Resource Manager jednostki *Microsoft.Web/certificates,* która reprezentuje certyfikat TLS/SSL. Nazwa musi *kończyć* się następującym sufiksem: \_ yourASENameHere_InternalLoadBalancingASE. Jednostka Azure Portal używa tego sufiksu jako wskaźnika, że certyfikat jest używany do zabezpieczania asE z obsługą równoważenia obciążenia.

Poniżej przedstawiono skrócony *przykładazuredeploy.parameters.jso:*

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

Poazuredeploy.parameters.js *pliku* skonfiguruj domyślny certyfikat TLS/SSL przy użyciu fragmentu kodu programu PowerShell. Zmień ścieżki plików tak, aby dopasować Resource Manager plików szablonu na komputerze. Pamiętaj, aby podać własne wartości dla Resource Manager wdrożenia i nazwy grupy zasobów:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Zastosowanie zmiany na frontonie ASE trwa około 40 minut. Na przykład w przypadku zestawu ASE o rozmiarze domyślnym, który używa dwóch frontony, szablon zajmuje około godziny i 20 minut. Gdy szablon jest uruchomiony, nie można skalować tego szablonu.  

Po zakończeniu pracy szablonu dostęp do aplikacji w asecie z równoważeniem obciążenia można uzyskać za pośrednictwem protokołu HTTPS. Połączenia są zabezpieczone przy użyciu domyślnego certyfikatu protokołu TLS/SSL. Domyślny certyfikat protokołu TLS/SSL jest używany, gdy aplikacje w zestawie ASE z równoważeniem obciążenia są adresowane przy użyciu kombinacji nazwy aplikacji i domyślnej nazwy hosta. Na przykład `https://mycustomapp.internal-contoso.com` program używa domyślnego certyfikatu TLS/SSL dla **.internal-contoso.com*.

Jednak podobnie jak aplikacje uruchamiane w publicznej usłudze wielodostępnej, deweloperzy mogą konfigurować niestandardowe nazwy hostów dla poszczególnych aplikacji. Mogą również konfigurować unikatowe powiązania certyfikatów SNI TLS/SSL dla poszczególnych aplikacji.

## <a name="app-service-environment-v1"></a>Środowisko usługi App Service — wersja 1 ##
Środowisko App Service Environment występuje w dwóch wersjach: ASEv1 i ASEv2. Podane wcześniej informacje dotyczyły wersji 2 — ASEv2. W tej sekcji przedstawiono różnice między środowiskami ASEv1 i ASEv2.

W przypadku konta ASEv1 wszystkie zasoby są zarządzane ręcznie. Obejmuje to frontony, procesy robocze oraz adresy IP używane do obsługi połączeń SSL opartych na protokole IP. Przed skalą w App Service planu należy skalować w zewnątrz pulę procesów roboczych, która ma być hostem.

W przypadku środowiska ASEv1 używany jest inny model cenowy niż w przypadku środowiska ASEv2. W przypadku środowiska ASEv1 płacisz za każdy przydzielony procesor vCPU. Obejmuje to procesory wirtualne używane dla frontony lub pracowników, które nie hostują żadnych obciążeń. W przypadku środowiska ASEv1 domyślny rozmiar w skali maksymalnej środowiska ASE to 55 hostów łącznie. Obejmuje to frontony i procesy robocze. Jedną z zalet środowiska ASEv1 jest to, że można je wdrożyć w klasycznej sieci wirtualnej oraz w sieci wirtualnej usługi Resource Manager. Aby dowiedzieć się więcej na temat środowiska ASEv1, zobacz [App Service Environment v1 introduction][ASEv1Intro] (Wprowadzenie do środowiska App Service Environment w wersji 1).

Aby utworzyć konto ASEv1 przy użyciu szablonu Resource Manager, zobacz [Create an ILB ASE v1 with a Resource Manager template][ILBASEv1Template](Tworzenie usługi ASE z równoważeniem obciążenia w wersji 1 przy Resource Manager szablonu).


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
[NSGs]: ../../virtual-network/network-security-groups-overview.md
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
