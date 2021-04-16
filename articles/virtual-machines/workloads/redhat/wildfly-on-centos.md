---
title: Przewodnik Szybki start — oprogramowanie WildFly w systemie CentOS
description: Wdrażanie aplikacji Java na maszynie wirtualnej z systemem CentOS na maszynie wirtualnej z systemem CentOS
author: Theresa-Nguyen
ms.author: bicnguy
ms.date: 10/23/2020
ms.assetid: 7aa21ef8-9cfb-43e0-bfda-3f10a2a2f3ef
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.custom:
- mode-api
ms.collection: linux
ms.openlocfilehash: bad9331a6d9a0c0936b1458e3aa4a84559e4a107
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533556"
---
# <a name="quickstart-wildfly-on-centos-8"></a>Szybki start: system WildFly w systemie CentOS 8

W tym przewodniku Szybki start pokazano, jak wdrożyć autonomiczny węzeł wildfly maszyny wirtualnej z systemem CentOS 8. Jest to idealne rozwiązanie do tworzenia i testowania aplikacji Java dla przedsiębiorstw na platformie Azure. Subskrypcja serwera aplikacji nie jest wymagana do wdrożenia tego przewodnika Szybki start.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. Jeśli nie masz subskrypcji platformy Azure, możesz aktywować korzyści dla subskrybentów [MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub [utworzyć bezpłatne konto.](https://azure.microsoft.com/pricing/free-trial)

## <a name="use-case"></a>Przypadek użycia

WildFly to idealne rozwiązanie do tworzenia i testowania aplikacji Java dla przedsiębiorstw na platformie Azure. Listy technologii dostępnych w profilach konfiguracji serwera WildFly 18 są dostępne w przewodniku konfiguracji serwera [WildFly Wprowadzenie .](https://docs.wildfly.org/18/Getting_Started_Guide.html#getting-started-with-wildfly)

Można użyć wildfly w trybie autonomicznym lub klastra na przypadek użycia. Możesz zapewnić wysoką dostępność krytycznych aplikacji Jakarta EE firmy WildFly w klastrze węzłów. Wprowadzić niewielką liczbę zmian konfiguracji aplikacji, a następnie wdrożyć aplikację w klastrze. Aby dowiedzieć się więcej na ten temat, zapoznaj się z przewodnikiem po wysokiej dostępności serwera [WildFly.](https://docs.wildfly.org/18/High_Availability_Guide.html)

## <a name="configuration-choice"></a>Wybór konfiguracji

Oprogramowanie WildFly można  uruchomić w trybie autonomicznego serwera — wystąpienie serwera autonomicznego jest niezależnym procesem, podobnie jak wystąpienie serwera aplikacji JBoss Application Server (AS) 3, 4, 5 lub 6. Wystąpienia autonomiczne można uruchamiać za pośrednictwem standalone.sh lub standalone.bat uruchamiania skryptów. W przypadku więcej niż jednego wystąpienia autonomicznego użytkownik jest odpowiedzialny za koordynowanie zarządzania wieloma serwerami na serwerach.

Możesz również uruchomić wystąpienie serwera WildFly z konfiguracją alternatywną przy użyciu plików konfiguracji dostępnych w folderze konfiguracji.

Poniżej przedstawiono pliki konfiguracji serwera autonomicznego:

- standalone.xml (ustawienie domyślne) — ta konfiguracja jest plikiem domyślnym używanym do uruchamiania wystąpienia wildfly. Zawiera on certyfikowaną konfigurację profilu internetowego Jakarta z wymaganymi technologiami.
   
- standalone-ha.xml — certyfikowana konfiguracja profilu internetowego Dżaarta EE 8 o wysokiej dostępności (ukierunkowana na aplikacje internetowe).
   
- standalone-full.xml — certyfikowana konfiguracja platformy Jakarta EE Platform 8 z uwzględnieniem wszystkich technologii wymaganych do hostowania aplikacji Jakarta EE.

- standalone-full-ha.xml — certyfikowana konfiguracja platformy Jakarta EE Platform 8 o wysokiej dostępności do hostowania aplikacji Jakarta EE.

Aby uruchomić autonomiczny serwer WildFly z inną podaną konfiguracją, użyj argumentu --server-config z plikiem server-config.

Aby na przykład użyć platformy Jakarta EE Platform 8 z możliwościami klastrowania, użyj następującego polecenia:

```
./standalone.sh --server-config=standalone-full-ha.xml
```

Aby dowiedzieć się więcej na temat konfiguracji, zapoznaj się z przewodnikiem po Wprowadzenie [WildFly.](https://docs.wildfly.org/18/Getting_Started_Guide.html#wildfly-10-configurations)

## <a name="licensing-support-and-subscription-notes"></a>Informacje o licencjonowaniu, pomocy technicznej i subskrypcji

Obraz systemu Azure CentOS 8 to obraz maszyny wirtualnej z płatnością zgodnie z platformą Pay-As-You-Go (PAYG) i nie wymaga od użytkownika uzyskania licencji. Przy pierwszym uruchamianiu maszyny wirtualnej licencja systemu operacyjnego maszyny wirtualnej zostanie automatycznie aktywowana i będzie naliczana stawka godzinowa. Jest to dodatkowe stawki godzinowe dla maszyn wirtualnych firmy Microsoft z systemem Linux. Kliknij pozycję [Cennik maszyny wirtualnej z systemem Linux,](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#linux) aby uzyskać szczegółowe informacje. Oprogramowanie WildFly można pobrać i używać bezpłatnie i nie wymaga subskrypcji ani licencji oprogramowania Red Hat.

## <a name="how-to-consume"></a>Jak korzystać z

Szablon można wdrożyć na następujące trzy sposoby:

- Użyj programu PowerShell — wd wdrażaj szablon, uruchamiając następujące polecenia: (Zapoznaj się z [Azure PowerShell,](/powershell/azure/) aby uzyskać informacje na temat instalowania i konfigurowania Azure PowerShell).

    ```
    New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```
    
- Użyj interfejsu wiersza polecenia platformy Azure — wd wdrażaj szablon, uruchamiając następujące polecenia: (Zapoznaj się z międzyplatformowym wierszem polecenia platformy Azure, aby uzyskać szczegółowe informacje na temat instalowania i konfigurowania interfejsu azure [cross-platform](/cli/azure/install-azure-cli) Command-Line Interface).

    ```
    az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    az deployment group create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```

- Użyj Azure Portal — wd wdrażaj szablon, klikając <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fwildfly-standalone-centos8%2Fazuredeploy.json" target="_blank">tutaj</a> i logjąc się do Azure Portal.

## <a name="arm-template"></a>Szablon ARM

<a href="https://github.com/Azure/azure-quickstart-templates/tree/master/wildfly-standalone-centos8" target="_blank"> WildFly 18 on CentOS 8 (autonomiczna</a> maszyna wirtualna) — jest to szablon szybkiego startu, który tworzy autonomiczny węzeł WildFly 18.0.1.Final na maszynie wirtualnej z systemem CentOS 8 w grupie zasobów, który zawiera prywatny adres IP maszyny wirtualnej, Virtual Network i konto magazynu diagnostyki. Wdraża również przykładową aplikację Java o nazwie JBoss-EAP na platformie Azure na platformie WildFly.

## <a name="resource-links"></a>Linki zasobów

* Dowiedz się więcej o [wildfly 18](https://docs.wildfly.org/18/)
* Dowiedz się więcej o [dystrybucjach systemu Linux na platformie Azure](../../linux/endorsed-distros.md)
* [Dokumentacja platformy Azure dla deweloperów języka Java](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Następne kroki

W przypadku środowiska produkcyjnego zapoznaj się z szablonami ARM red hat JBoss EAP platformy Azure:

Autonomiczna maszyna wirtualna RHEL z przykładową aplikacją:

*  <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-standalone-rhel" target="_blank"> JBoss EAP on RHEL (autonomiczna maszyna wirtualna)</a>

Klastrowane maszyny wirtualne RHEL z przykładową aplikacją:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-multivm-rhel" target="_blank"> JBoss EAP on RHEL (klastrowane maszyny wirtualne)</a>

Klastrowany zestaw skalowania maszyn wirtualnych RHEL z przykładową aplikacją:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-vmss-rhel" target="_blank"> JBoss EAP w RHEL (klastrowany zestaw skalowania maszyn wirtualnych)</a>
