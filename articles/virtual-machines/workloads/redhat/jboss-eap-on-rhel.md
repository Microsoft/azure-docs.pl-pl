---
title: Szybki start — wdrażanie platformy aplikacji JBoss Enterprise Application Platform (EAP) w systemie Red Hat Enterprise Linux (RHEL) na maszynach wirtualnych platformy Azure i w zestawach skalowania maszyn wirtualnych
description: Jak wdrażać aplikacje Java dla przedsiębiorstw przy użyciu oprogramowania Red Hat JBoss EAP na maszynach wirtualnych ZHEL platformy Azure i zestawach skalowania maszyn wirtualnych.
author: theresa-nguyen
ms.author: bicnguy
ms.date: 10/30/2020
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.custom:
- mode-api
ms.collection: linux
ms.openlocfilehash: 18cf093adad858f50b2b1fa1c97e38821bd1c949
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538676"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Wdrażanie firmowych aplikacji Java na platformie Azure przy użyciu platformy JBoss EAP w systemie Red Hat Enterprise Linux

Szablony szybkiego startu platformy Azure w tym artykule pokazują, jak wdrożyć aplikację [JBoss Enterprise Application Platform (EAP)](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) z programem [Red Hat Enterprise Linux (RHEL)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) na maszynach wirtualnych platformy Azure i w zestawach skalowania maszyn wirtualnych. Do zweryfikowania wdrożenia użyjesz przykładowej aplikacji Java. 

JBoss EAP to platforma serwera aplikacji typu open source. Zapewnia ona bezpieczeństwo, skalowalność i wydajność klasy korporacyjnej dla aplikacji Java. RHEL to platforma systemu operacyjnego typu open source. Umożliwia ona skalowanie istniejących aplikacji i wycofywanie nowych technologii we wszystkich środowiskach. 

Rozwiązania JBoss EAP i RHEL obejmują wszystko, czego potrzebujesz do tworzenia, uruchamiania i wdrażania aplikacji Java przedsiębiorstwa w dowolnym środowisku oraz zarządzania nimi. Połączenie jest rozwiązaniem typu open source dla środowisk lokalnych, wirtualnych oraz chmur prywatnych, publicznych lub hybrydowych.

## <a name="prerequisites"></a>Wymagania wstępne 

* Konto platformy Azure z aktywną subskrypcją. Aby uzyskać subskrypcję platformy Azure, aktywuj środki na korzystanie z platformy [Azure Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) subskrybentów lub utwórz [bezpłatne konto.](https://azure.microsoft.com/pricing/free-trial)

* Instalacja oprogramowania JBoss EAP. Musisz mieć konto red hat z uprawnieniami do zarządzania subskrypcjami red hat (RHSM) dla JBoss EAP. To uprawnienie umożliwia pobranie przetestowanej i certyfikowanej wersji protokołu EAP firmy Red Hat.  

  Jeśli nie masz uprawnień protokołu EAP, przed rozpoczęciem uzyskaj subskrypcję ewaluacyjną [JBoss EAP.](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) Aby utworzyć nową subskrypcję usługi Red Hat, przejdź do portalu klienta [firmy Red Hat](https://access.redhat.com/) i skonfiguruj konto.

* Interfejs [wiersza polecenia platformy Azure](/cli/azure/overview).

* Opcje RHEL. Wybierz płatność zgodnie z potrzebami (PAYG) lub bring-your-own-subscription (BYOS). W systemie BYOS musisz aktywować aplikację [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold Image przed wdrożeniem szablonu Szybkiego startu.

## <a name="java-ee-and-jakarta-ee-application-migration"></a>Migracja aplikacji Java EE i Jakarta EE

### <a name="migrate-to-jboss-eap"></a>Migrowanie do protokołu EAP JBoss
JBoss EAP 7.2 i 7.3 to certyfikowane implementacje specyfikacji Java Enterprise Edition (Java EE) 8 i Jakarta EE 8. Funkcja JBoss EAP udostępnia wstępnie skonfigurowane opcje funkcji, takich jak klastrowanie o wysokiej dostępności, obsługa komunikatów i rozproszone buforowanie. Umożliwia również użytkownikom pisanie, wdrażanie i uruchamianie aplikacji przy użyciu różnych interfejsów API i usług zapewnianych przez JBoss EAP.  

Aby uzyskać więcej informacji na temat JBoss EAP, zobacz Wprowadzenie do [JBoss EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) lub Wprowadzenie do [JBoss EAP 7.3.](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index)

 #### <a name="applications-on-jboss-eap"></a>Aplikacje w JBoss EAP

* **Aplikacje usług internetowych.** Usługi sieci Web zapewniają standardowy sposób współdziałania między aplikacjami. Każda aplikacja może działać na różnych platformach i platformach. Te usługi sieci Web ułatwiają komunikację między podsystemami wewnętrznymi i heterogenicznymi. 

  Aby dowiedzieć się więcej, zobacz Developing Web Services Applications on EAP 7.2 (Tworzenie aplikacji usług internetowych w witrynie [EAP 7.2)](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) lub Developing Web Services Applications on EAP 7.3 (Tworzenie aplikacji usług internetowych [w witrynie EAP 7.3).](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index)

* **Aplikacje Enterprise Java Beans (EJB).** EJB 3.2 to interfejs API do tworzenia rozproszonych, transakcyjnych, bezpiecznych i przenośnych aplikacji Java EE i Jakarta EE. Usługa EJB używa składników po stronie serwera o nazwie Enterprise Beans w celu zaimplementowania logiki biznesowej aplikacji w sposób oddzielony, co zachęca do ponownego użycia. 

  Aby dowiedzieć się więcej, zobacz [Developing EJB Applications on EAP 7.2 (Tworzenie aplikacji EJB dla protokołu EAP 7.2)](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) lub [Developing EJB Applications on EAP 7.3 (Tworzenie aplikacji EJB w 7.3).](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index)

* **Hibernacja aplikacji.** Deweloperzy i administratorzy mogą opracowywać i wdrażać aplikacje Java Persistence API (JPA) i Hibernate przy użyciu protokołu EAP JBoss. Hibernate Core to środowisko mapowania obiektowo-relacyjne dla języka Java. Zapewnia platformę do mapowania zorientowanego obiektowo modelu domeny na relacyjną bazę danych, dzięki czemu aplikacje mogą uniknąć bezpośredniej interakcji z bazą danych. 

  Hibernate Entity Manager implementuje interfejsy programowania i reguły cyklu życia zgodnie ze specyfikacją [JPA 2.1.](https://www.jcp.org/en/jsr/overview) Wraz z adnotacjami hibernacji ta otoka implementuje kompletne (i autonomiczne) rozwiązanie JPA na podstawie dojrzałego rdzenia hibernacji. 
  
  Aby dowiedzieć się więcej na temat hibernacji, zobacz [JPA na EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) lub [Jakarta Persistence on EAP 7.3 (Trwałość Jakarta w przypadku protokołu EAP 7.3).](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api)

#### <a name="red-hat-migration-toolkit-for-applications"></a>Red Hat Migration Toolkit for Applications
[Red Hat Migration Toolkit for Applications (MTA)](https://developers.redhat.com/products/mta/overview) to narzędzie do migracji serwerów aplikacji Java. To narzędzie umożliwia migrację z innego serwera aplikacji do serwera JBoss EAP. Działa z wtyczkami IDE dla środowisk [IDE Eclipse,](https://www.eclipse.org/ide/) [obszarami roboczymi Red Hat CodeReady](https://developers.redhat.com/products/codeready-workspaces/overview)i Visual Studio Code [dla](https://code.visualstudio.com/docs/languages/java) języka Java. 

MTA to bezpłatne narzędzie typu open source, które:
* Automatyzuje analizę aplikacji.
* Obsługuje szacowanie nakładu pracy.
* Przyspiesza migrację kodu.
* Obsługuje konteneryzację.
* Integruje się z usługą Azure Workload Builder.

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>Migrowanie protokołu JBoss EAP ze środowisk lokalnych na platformę Azure
Ta Azure Marketplace JBoss EAP w systemie RHEL zainstaluje i aprowizuje maszyny wirtualne platformy Azure w mniej niż 20 minut. Dostęp do tych ofert można uzyskać na [stronie Azure Marketplace](https://azuremarketplace.microsoft.com/).

Ta Azure Marketplace obejmuje różne kombinacje wersji protokołu EAP i RHEL w celu obsługi wymagań. JBoss EAP jest zawsze BYOS, ale w przypadku systemu operacyjnego RHEL można wybrać między BYOS lub PAYG. Ta Azure Marketplace obejmuje opcje planu dla protokołu EAP JBoss w RHEL jako autonomicznych lub klastrowanych maszyn wirtualnych:

* JBoss EAP 7.2 na maszynie wirtualnej RHEL 7.7 (PŁATNOŚĆ)
* JBoss EAP 7.2 na maszynie wirtualnej RHEL 8.0 (PŁATNOŚĆ)
* JBoss EAP 7.3 na maszynie wirtualnej RHEL 8.0 (PŁATNOŚĆ)
* JBoss EAP 7.2 na maszynie wirtualnej RHEL 7.7 (BYOS)
* JBoss EAP 7.2 na maszynie wirtualnej RHEL 8.0 (BYOS)
* JBoss EAP 7.3 na maszynie wirtualnej RHEL 8.0 (BYOS)

Oprócz ofert Azure Marketplace możesz użyć szablonów szybkiego startu, aby rozpocząć swoją podróż po migracji na platformę Azure. Te przewodniki Szybki start obejmują wstępnie utworzone szablony Azure Resource Manager (ARM) i skrypty do wdrażania protokołu EAP JBoss w programie RHEL w różnych konfiguracjach i kombinacjach wersji. Będziesz mieć:

* Równoważenie obciążenia.
* Prywatny adres IP do równoważenia obciążenia i maszyn wirtualnych.
* Sieć wirtualna z jedną podsiecią.
* Konfiguracja maszyny wirtualnej (klastra lub autonomicznej).
* Przykładowa aplikacja Java.

Architektura rozwiązań dla tych szablonów obejmuje:

* JBoss EAP na autonomicznej maszynie wirtualnej ZHEL.
* JBoss EAP klastrowane na wielu RHEL maszyn wirtualnych.
* JBoss EAP clustered through Azure virtual machine scale sets (JBoss EAP klastrowany za pośrednictwem zestawów skalowania maszyn wirtualnych platformy Azure).

#### <a name="linux-workload-migration-for-jboss-eap"></a>Migracja obciążenia systemu Linux dla protokołu EAP JBoss
Narzędzie Azure Workload Builder upraszcza proces weryfikacji koncepcji, oceny i migracji lokalnych aplikacji Java na platformę Azure. Narzędzie Workload Builder integruje się z narzędziem Azure Migrate Discovery w celu identyfikowania serwerów JBoss EAP. Następnie dynamicznie generuje podręcznik rozwiązania Ansible dla wdrożenia serwera JBoss EAP. Używa narzędzia Red Hat MTA do migrowania serwerów z innych serwerów aplikacji do serwera JBoss EAP. 

Kroki upraszczania migracji obejmują:
1. **Ocena**. Oceń klastry JBoss EAP przy użyciu maszyny wirtualnej platformy Azure lub zestawu skalowania maszyn wirtualnych.
1. **Ocena**. Skanuj aplikacje i infrastrukturę.
1. **Konfiguracja infrastruktury**. Utwórz profil obciążenia.
1. **Wdrażanie i testowanie.** Wdrażanie, migrowanie i testowanie obciążenia.
1. **Konfiguracja po wdrożeniu.** Integracja z danymi, monitorowaniem, zabezpieczeniami, kopiami zapasami i nie tylko.

## <a name="server-configuration-choice"></a>Wybór konfiguracji serwera

W przypadku wdrażania maszyny wirtualnej RHEL można wybrać płatność według(PAYG) lub BYOS (BYOS). Obrazy z [Azure Marketplace](https://azuremarketplace.microsoft.com) są domyślnie opłacane w płatnościach. Wdrażanie maszyny wirtualnej RHEL typu BYOS, jeśli masz własny obraz systemu operacyjnego RHEL. Przed wdrożeniem maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych upewnij się, że twoje konto Cloud Access SYSTEMU BYOS za pośrednictwem usługi .

JBoss EAP ma zaawansowane możliwości zarządzania oraz zapewnia funkcje i interfejsy API dla swoich aplikacji. Te możliwości zarządzania różnią się w zależności od trybu operacyjnego, którego używasz do uruchamiania protokołu EAP JBoss. Jest on obsługiwany w systemach RHEL i Windows Server. JBoss EAP oferuje autonomiczny tryb operacyjny serwera do zarządzania dyskretnych wystąpień. Oferuje również tryb operacyjny domeny zarządzanej do zarządzania grupami wystąpień z jednego punktu kontrolnego. 

> [!NOTE]
> Domeny zarządzane przez JBoss EAP nie są obsługiwane w usłudze Microsoft Azure ponieważ usługi infrastruktury platformy Azure zarządzają funkcją wysokiej jakości. 

Zmienna `EAP_HOME` środowiskowa określa ścieżkę do instalacji JBoss EAP. Użyj następującego polecenia, aby uruchomić usługę JBoss EAP w trybie autonomicznym:

```
$EAP_HOME/bin/standalone.sh
```
    
Ten skrypt uruchamiania używa pliku EAP_HOME/bin/standalone.conf do ustawienia niektórych preferencji domyślnych, takich jak opcje JVM. Ustawienia można dostosować w tym pliku. JBoss EAP używa pliku konfiguracji standalone.xml, aby domyślnie uruchomić w trybie autonomicznym, ale można użyć innego trybu, aby go uruchomić. 

Aby uzyskać szczegółowe informacje na temat dostępnych autonomicznych plików konfiguracji i sposobu ich używania, zobacz Pliki konfiguracji serwera autonomicznego dla protokołu [EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) lub Pliki konfiguracji serwera autonomicznego dla protokołu [EAP 7.3.](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) 

Aby uruchomić JBoss EAP z inną konfiguracją, użyj `--server-config` argumentu . Na przykład:
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
Aby uzyskać pełną listę wszystkich dostępnych argumentów skryptu uruchamiania i ich celów, użyj `--help` argumentu . Aby uzyskać więcej informacji, zobacz [Server Runtime Arguments on EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) or Server Runtime Arguments on EAP 7.3 (Argumenty środowiska uruchomieniowego serwera w przypadku protokołu [EAP 7.2) lub Server Runtime Arguments on EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime)(Argumenty środowiska uruchomieniowego serwera w wersji EAP 7.3).
    
JBoss EAP może również działać w trybie klastra. Obsługa komunikatów klastra JBoss EAP umożliwia grupowanie serwerów obsługi komunikatów JBoss EAP w celu udostępniania obciążenia przetwarzania komunikatów. Każdy aktywny węzeł w klastrze jest aktywnym serwerem obsługi komunikatów EAP JBoss, który zarządza własnymi komunikatami i obsługuje własne połączenia. Aby dowiedzieć się więcej, zobacz [Clusters Overview on EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) (Omówienie klastrów w przypadku protokołu EAP 7.2) lub [ Clusters Overview on EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview)(Omówienie klastrów w przypadku protokołu EAP 7.3). 

## <a name="support-and-subscription-notes"></a>Uwagi dotyczące pomocy technicznej i subskrypcji
Te szablony szybkiego startu są oferowane w następujący sposób: 

- System operacyjny RHEL jest oferowany jako model płatności zgodnie z płatnością zgodnie z płatnością (PAYG) lub BYOS (BYOS) za pośrednictwem modelu Red Hat Gold Image.
- JBoss EAP jest oferowana tylko jako BYOS.

#### <a name="using-rhel-os-with-the-payg-model"></a>Używanie systemu operacyjnego RHEL z modelem płatności zgodnie z płatnością zgodnie z modelem

Domyślnie w tych szablonach Szybkiego startu jest domyślnie Azure Marketplace RHEL 7.7 lub 8.0 z systemu Azure Marketplace. Obrazy z płatnością godzinową mają dodatkową opłatę godzinową za subskrypcję RHEL na podstawie normalnych kosztów obliczeń, sieci i magazynu. W tym samym czasie wystąpienie jest rejestrowane w subskrypcji red hat. Oznacza to, że będziesz używać jednego z uprawnień. 

Ten obraz płatności za próg spowoduje "podwójne rozliczenia". Ten problem można uniknąć, budowania własnego obrazu systemu RHEL. Aby dowiedzieć się więcej, zapoznaj się z artykułem Bazy wiedzy systemu Red Hat Jak aprowizować maszynę wirtualną [ZHEL dla systemu Microsoft Azure.](https://access.redhat.com/articles/uploading-rhel-image-to-azure) Lub [aktywuj Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold Image.

Aby uzyskać szczegółowe informacje na temat cennika maszyn wirtualnych z płatnością [Red Hat Enterprise Linux, zobacz cennik usługi](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/). Aby korzystać z systemu RHEL w modelu płatności zgodnie z płatnością zgodnie z płatnością, musisz mieć subskrypcję platformy Azure z określoną metodą płatności dla systemu [RHEL 7.7](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) w systemie Azure Marketplace lub [RHEL 8.0](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM)w systemie Azure Marketplace . Te oferty wymagają, aby w subskrypcji platformy Azure została określona metoda płatności.

#### <a name="using-rhel-os-with-the-byos-model"></a>Używanie systemu operacyjnego RHEL z modelem BYOS

Aby używać systemu BYOS dla systemu operacyjnego RHEL, musisz mieć prawidłową subskrypcję Red Hat z uprawnieniami do korzystania z systemu operacyjnego RHEL na platformie Azure. Przed wdrożeniem systemu operacyjnego RHEL z modelem BYOS należy spełnić następujące wymagania wstępne:

1. Upewnij się, że masz uprawnienia RHEL OS i JBoss EAP dołączone do subskrypcji red hat.
2. Autoryzowanie identyfikatora subskrypcji platformy Azure do używania obrazów systemu RHEL BYOS. Postępuj zgodnie z [dokumentacją dotyczącą zarządzania subskrypcjami](https://access.redhat.com/documentation/red_hat_subscription_management/1/) systemu Red Hat, aby ukończyć proces, który obejmuje następujące kroki:

   1. Włącz Microsoft Azure jako dostawcę na pulpicie nawigacyjnym Red Hat Cloud Access nawigacyjnym.

   1. Dodaj identyfikatory subskrypcji platformy Azure.

   1. Włącz nowe produkty dla Cloud Access na Microsoft Azure.
    
   1. Aktywuj usługę Red Hat Gold Images dla subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Red Hat Gold Images on Microsoft Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/cloud-access-gold-images_cloud-access#proc_using-gold-images-azure_cloud-access).

   1. Poczekaj, aż obrazy Red Hat Gold będą dostępne w ramach subskrypcji platformy Azure. Te obrazy są zwykle dostępne w ciągu trzech godzin od przesłania.
    
3. Zaakceptuj warunki Azure Marketplace obrazów systemu RHEL BYOS. Możesz ukończyć ten proces, uruchamiając następujące polecenia interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [dokumentację RHEL BYOS Gold Images in Azure (Złote obrazy systemu RHEL BYOS na platformie Azure).](./byos.md) Ważne jest, aby uruchamiać najnowszą wersję interfejsu wiersza polecenia platformy Azure.

   1. Otwórz sesję interfejsu wiersza polecenia platformy Azure i uwierzytelnij się przy użyciu konta platformy Azure. Aby uzyskać pomoc, zobacz [Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure.](/cli/azure/authenticate-azure-cli)

   1. Sprawdź, czy obrazy systemu RHEL BYOS są dostępne w twojej subskrypcji, uruchamiając następujące polecenie interfejsu wiersza polecenia. Jeśli nie otrzymasz tutaj żadnych wyników, upewnij się, że subskrypcja platformy Azure została aktywowana dla obrazów systemu RHEL BYOS.
   
      ```
      az vm image list --offer rhel-byos --all
      ```

   1. Uruchom następujące polecenie, aby zaakceptować Azure Marketplace odpowiednio RHEL 7.7 BYOS i RHEL 8.0 BYOS:
      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
      ``` 

      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
      ``` 
   
Twoja subskrypcja jest teraz gotowa do wdrożenia systemu RHEL 7.7 lub 8.0 BYOS na maszynach wirtualnych platformy Azure.

#### <a name="using-jboss-eap-with-the-byos-model"></a>Używanie protokołu EAP JBoss z modelem BYOS

JBoss EAP jest dostępny na platformie Azure tylko za pośrednictwem modelu BYOS. Podczas wdrażania tego szablonu należy podać poświadczenia programu RHSM wraz z identyfikatorem puli RHSM z prawidłowymi uprawnieniami protokołu EAP. Jeśli nie masz uprawnień EAP, przed rozpoczęciem uzyskaj subskrypcję ewaluacyjną [JBoss EAP.](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation)

## <a name="deployment-options"></a>Opcje wdrożenia

Szablon można wdrożyć w następujący sposób:

- **Program PowerShell**. Wd wdrażaj szablon, uruchamiając następujące polecenia: 

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
 
  Aby uzyskać informacje na temat instalowania i konfigurowania Azure PowerShell, zobacz [dokumentację programu PowerShell](/powershell/azure/).  

- **Interfejs wiersza polecenia platformy Azure**. Wd wdrażaj szablon, uruchamiając następujące polecenia:

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az deployment group create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

  Aby uzyskać szczegółowe informacje na temat instalowania i konfigurowania interfejsu wiersza polecenia platformy Azure, [zobacz Instalowanie interfejsu wiersza polecenia](/cli/azure/install-azure-cli).

- Użycie witryny **Azure Portal**. Możesz wdrożyć je w Azure Portal, przechodząc do szablonów szybkiego startu platformy Azure, jak wspomniano w następnej sekcji. Po zakończeniu przewodnika Szybki start wybierz przycisk Deploy to Azure (Wd wdrażaj na platformie **Azure)** **lub Browse on GitHub (Przeglądaj w witrynie GitHub).**

## <a name="azure-quickstart-templates"></a>Szablony przewodników Szybki start platformy Azure

Możesz rozpocząć od użycia jednego z następujących szablonów Szybkiego startu dla aplikacji JBoss EAP w programie RHEL, który spełnia cel wdrożenia:

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/">JBoss EAP on RHEL (standalone VM) (Autonomiczna maszyna wirtualna) JBoss EAP on RHEL (autonomiczna maszyna wirtualna).</a> Spowoduje to wdrożenie aplikacji internetowej o nazwie JBoss-EAP na platformie Azure do aplikacji JBoss EAP 7.2 lub 7.3 działającej na maszynie wirtualnej z systemem RHEL 7.7 lub 8.0.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> JBoss EAP on RHEL (klastrowane, wiele maszyn wirtualnych)</a>. Spowoduje to wdrożenie aplikacji internetowej o nazwie eap-session-replication w klastrze JBoss EAP 7.2 lub 7.3 uruchomionym na n liczbie maszyn wirtualnych ZHEL 7.7 lub 8.0.  Użytkownik decyduje o *wartości n.* Wszystkie maszyny wirtualne są dodawane do puli zasobów serwera równoważenia obciążenia.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> JBoss EAP on RHEL (klastrowany zestaw skalowania maszyn wirtualnych)</a>. Spowoduje to wdrożenie aplikacji internetowej o nazwie eap-session-replication w klastrze JBoss EAP 7.2 lub 7.3 z systemem RHEL 7.7 lub 8.0.

## <a name="resource-links"></a>Linki zasobów

* [Korzyść użycia hybrydowego platformy Azure](../../windows/hybrid-use-benefit-licensing.md)
* [Konfigurowanie aplikacji Java dla Azure App Service](../../../app-service/configure-language-java.md)
* [JBoss EAP na Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [JBoss EAP w systemie Azure App Service Linux](../../../app-service/quickstart-java.md)
* [Wdrażanie protokołu EAP JBoss na Azure App Service](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [JBoss EAP 7.2.](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/)
* Dowiedz się więcej o [JBoss EAP 7.3.](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/)
* Dowiedz się więcej o [zarządzaniu subskrypcjami systemu Red Hat.](https://access.redhat.com/products/red-hat-subscription-management)
* Dowiedz się więcej o [obciążeniach systemu Red Hat na platformie Azure.](./overview.md)
* Wdrażanie [protokołu EAP JBoss na maszynie wirtualnej z](https://aka.ms/AMP-JBoss-EAP)systemu RHEL lub zestawie skalowania maszyn wirtualnych z Azure Marketplace .
* Wdrażanie [protokołu JBoss EAP na maszynie wirtualnej z](https://aka.ms/Quickstart-JBoss-EAP)system RHEL lub zestawie skalowania maszyn wirtualnych z szablonów szybkiego startu platformy Azure.
