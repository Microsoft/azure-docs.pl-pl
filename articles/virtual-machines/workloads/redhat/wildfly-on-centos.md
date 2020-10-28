---
title: Szybki Start — WildFly na CentOS
description: Wdrażanie aplikacji Java do WildFly na maszynie wirtualnej CentOS
author: Theresa-Nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.assetid: 7aa21ef8-9cfb-43e0-bfda-3f10a2a2f3ef
ms.date: 10/23/2020
ms.openlocfilehash: 875d04751475d1d5236e9f15fbca585cdc9b1ab0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897684"
---
# <a name="quickstart-wildfly-on-centos-8"></a>Szybki Start: WildFly na CentOS 8

W tym przewodniku szybki start pokazano, jak wdrożyć autonomiczny węzeł WildFly maszyny wirtualnej CentOS 8. Doskonale nadaje się do tworzenia i testowania aplikacji Java dla przedsiębiorstw na platformie Azure. Subskrypcja serwera aplikacji nie jest wymagana do wdrożenia tego przewodnika Szybki Start.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. Jeśli nie masz subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub [bezpłatnie utworzyć konto](https://azure.microsoft.com/pricing/free-trial).

## <a name="use-case"></a>Przypadek użycia

WildFly doskonale nadaje się do tworzenia i testowania aplikacji Java dla przedsiębiorstw na platformie Azure. Listę technologii dostępnych w WildFly 18 profilach konfiguracji serwera są dostępne w [podręczniku WildFly wprowadzenie](https://docs.wildfly.org/18/Getting_Started_Guide.html#getting-started-with-wildfly).

Możesz użyć WildFly w trybie autonomicznym lub klastra na potrzeby użycia. Możesz zapewnić wysoką dostępność krytycznych aplikacji programu Dżakarta w systemie WildFly w klastrze węzłów. Wprowadź niewielką liczbę zmian konfiguracji aplikacji, a następnie wdróż aplikację w klastrze. Aby dowiedzieć się więcej na ten temat, zapoznaj się z [przewodnikiem High Availability WildFly](https://docs.wildfly.org/18/High_Availability_Guide.html).

## <a name="configuration-choice"></a>Wybór konfiguracji

WildFly można uruchomić w trybie **serwera autonomicznego** — autonomiczne wystąpienie serwera jest procesem niezależnym, podobnie jak serwer aplikacji JBoss (jako) 3, 4, 5 lub 6. Wystąpienia autonomiczne można uruchomić za pomocą skryptów uruchamiania standalone.sh lub standalone.bat. W przypadku więcej niż jednego wystąpienia autonomicznego użytkownik jest odpowiedzialny za koordynowanie zarządzania wieloma serwerami na serwerach.

Można również uruchomić wystąpienie WildFly z konfiguracją alternatywną przy użyciu plików konfiguracyjnych dostępnych w folderze konfiguracyjnym.

Poniżej przedstawiono pliki konfiguracji serwera autonomicznego:

- standalone.xml (domyślnie) — Ta konfiguracja jest domyślnym plikiem używanym do uruchamiania wystąpienia WildFly. Zawiera on konfigurację profilu sieci Web z certyfikatem Dżakarta z wymaganymi technologiami.
   
- standalone-ha.xml-Dżakarta EE sieć Web Profile 8 certyfikowana konfiguracja o wysokiej dostępności (w aplikacjach sieci Web).
   
- Konfiguracja z certyfikatem programu standalone-full.xml-Dżakarta EE platform 8, w tym wszystkich technologii wymaganych do hostowania aplikacji programu Dżakarta EE.

- standalone-full-ha.xml-Dżakarta EE platform 8 z certyfikatem o wysokiej dostępności do hostingu aplikacji programu Dżakarta EE.

Aby uruchomić autonomiczny serwer WildFly z inną podaną konfiguracją, użyj argumentu--Server-config z plikiem Server-config.

Na przykład, aby użyć programu Dżakarta EE platformy 8 z możliwościami klastrowania, użyj następującego polecenia:

`./standalone.sh --server-config=standalone-full-ha.xml`

Aby dowiedzieć się więcej o konfiguracjach, zapoznaj się z [podręcznikiem wprowadzenie WildFly](https://docs.wildfly.org/18/Getting_Started_Guide.html#wildfly-10-configurations).

## <a name="licensing-support-and-subscription-notes"></a>Uwagi dotyczące licencjonowania, pomocy technicznej i subskrypcji

Obraz platformy Azure CentOS 8 to obraz maszyny wirtualnej z opcją płatność zgodnie z rzeczywistym użyciem, który nie wymaga od użytkownika uzyskania licencji. Przy pierwszym uruchomieniu maszyny wirtualnej licencja na system operacyjny maszyny wirtualnej zostanie automatycznie aktywowana i zostanie naliczona opłata za godzinę. Jest to dodatkowe stawki za godzinę maszyn wirtualnych firmy Microsoft dla systemu Linux. Aby uzyskać szczegółowe informacje, kliknij pozycję [Cennik maszyny wirtualnej systemu Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#linux) . WildFly jest bezpłatny do pobrania i użycia i nie wymaga licencji na korzystanie z rozwiązania Red Hat.

## <a name="how-to-consume"></a>Jak korzystać z

Szablon można wdrożyć na następujące trzy sposoby:

- Użyj programu PowerShell — Wdróż szablon, uruchamiając następujące polecenia: (Wyewidencjonuj [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) , aby uzyskać informacje dotyczące instalowania i konfigurowania Azure PowerShell).

    `New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment`

    `New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json`
    
- Korzystanie z interfejsu wiersza polecenia platformy Azure — Wdróż szablon, uruchamiając następujące polecenia: (w ramach [międzyplatformowego wiersza poleceń platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) znajdują się szczegółowe informacje dotyczące instalowania i konfigurowania interfejsu Command-Line międzyplatformowego platformy Azure).

    `az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment`

    `az group deployment create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json`

- Użyj Azure Portal — Wdróż szablon, klikając <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fwildfly-standalone-centos8%2Fazuredeploy.json" target="_blank">tutaj</a> i logując się do Azure Portal.

## <a name="arm-template"></a>Szablon ARM

<a href="https://github.com/Azure/azure-quickstart-templates/tree/master/wildfly-standalone-centos8" target="_blank"> WildFly 18 w CentOS 8 (autonomiczna maszyna wirtualna)</a> — jest to szablon szybkiego startu, który tworzy autonomiczny węzeł WildFly 18.0.1. Final na maszynie wirtualnej CentOS 8 w grupie zasobów (RG), która zawiera prywatny adres IP dla maszyny wirtualnej, Virtual Network i konta magazynu diagnostyki. Wdraża również przykładową aplikację Java o nazwie JBoss-EAP na platformie Azure w systemie WildFly.

## <a name="resource-links"></a>Linki zasobów

* Dowiedz się więcej o [WildFly 18](https://wildfly.org/18)
* Dowiedz się więcej o [dystrybucji systemu Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)
* [Dokumentacja dla deweloperów platformy Azure dla języka Java](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Następne kroki

W środowisku produkcyjnym zapoznaj się z szablonami ARM usługi Red Hat JBoss EAP Azure:

Autonomiczna maszyna wirtualna RHEL z przykładową aplikacją:

*  <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-standalone-rhel" target="_blank"> JBoss EAP on RHEL (autonomiczna maszyna wirtualna)</a>

Klastrowane maszyny wirtualne RHEL z przykładową aplikacją:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-multivm-rhel" target="_blank"> JBoss EAP on RHEL (klastrowane maszyny wirtualne)</a>

Klastrowany zestaw skalowania maszyn wirtualnych RHEL z przykładową aplikacją:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-vmss-rhel" target="_blank"> JBoss EAP on RHEL (klastrowany zestaw skalowania maszyn wirtualnych)</a>
