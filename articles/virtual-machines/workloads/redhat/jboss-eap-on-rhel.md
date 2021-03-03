---
title: Szybki Start — wdrażanie JBoss platformy aplikacji dla przedsiębiorstw (EAP) na Red Hat Enterprise Linux (RHEL) na maszynach wirtualnych platformy Azure i w zestawach skalowania maszyn wirtualnych
description: Jak wdrażać aplikacje Java dla przedsiębiorstw przy użyciu Red Hat JBoss EAP na maszynach wirtualnych Azure RHEL i w zestawach skalowania maszyn wirtualnych.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.date: 10/30/2020
ms.openlocfilehash: 4027e9c336d938c3877e56b0eb59edea6ac1d992
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671970"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Wdrażanie firmowych aplikacji Java na platformie Azure przy użyciu platformy JBoss EAP w systemie Red Hat Enterprise Linux

Szablony szybkiego startu platformy Azure w tym artykule przedstawiają sposób wdrażania usługi [JBoss Enterprise Application Platform (EAP)](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) z usługą [Red Hat Enterprise Linux (RHEL)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) na maszynach wirtualnych platformy Azure i w zestawach skalowania maszyn wirtualnych. Użyjesz przykładowej aplikacji Java do zweryfikowania wdrożenia. 

JBoss EAP to platforma serwera aplikacji open source. Zapewnia ona zabezpieczenia, skalowalność i wydajność klasy korporacyjnej dla aplikacji Java. RHEL to platforma systemu operacyjnego Open Source. Pozwala ona skalować istniejące aplikacje i wdrażać nowe technologie we wszystkich środowiskach. 

JBoss EAP i RHEL obejmują wszystko, co jest potrzebne do kompilowania, uruchamiania i wdrażania aplikacji Java przedsiębiorstwa oraz zarządzania nimi w dowolnym środowisku. Kombinacja to rozwiązanie "open source" dla środowisk lokalnych, wirtualnych i prywatnych, publicznych lub hybrydowych.

## <a name="prerequisites"></a>Wymagania wstępne 

* Konto platformy Azure z aktywną subskrypcją. Aby uzyskać subskrypcję platformy Azure, Aktywuj środki na korzystanie z [platformy Azure dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub [Utwórz bezpłatne konto](https://azure.microsoft.com/pricing/free-trial).

* JBoss instalację protokołu EAP. Musisz mieć konto Red Hat z uprawnieniem do zarządzania subskrypcją Red Hat (RHSM) dla JBoss protokołu EAP. To uprawnienie umożliwia pobranie przetestowanej i certyfikowanej wersji protokołu EAP JBoss firmy Red Hat.  

  Jeśli nie masz uprawnień do protokołu EAP, przed rozpoczęciem Uzyskaj [subskrypcję ewaluacyjną JBoss EAP](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) . Aby utworzyć nową subskrypcję Red Hat, przejdź do [portalu Red Hat Customer Portal](https://access.redhat.com/) i skonfiguruj konto.

* [Interfejs wiersza polecenia platformy Azure](/cli/azure/overview).

* Opcje RHEL. Wybierz pozycję płatność zgodnie z rzeczywistym użyciem lub Przenieś własną subskrypcję (BYOS). W przypadku BYOS należy aktywować obraz [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold przed wdrożeniem szablonu szybkiego startu.

## <a name="java-ee-and-jakarta-ee-application-migration"></a>Migracja aplikacji Java EE i Dżakarta EE

### <a name="migrate-to-jboss-eap"></a>Migrowanie do JBoss EAP
JBoss EAP 7,2 i 7,3 są certyfikowanymi implementacjami specyfikacji Java Enterprise Edition (Java EE) 8 i Dżakarta EE 8. JBoss EAP udostępnia wstępnie skonfigurowane opcje dla funkcji, takich jak klastrowanie wysokiej dostępności (HA), obsługa komunikatów i buforowanie rozproszone. Umożliwia ona również użytkownikom pisanie, wdrażanie i uruchamianie aplikacji przy użyciu różnych interfejsów API i usług udostępnianych przez protokół EAP JBoss.  

Aby uzyskać więcej informacji na temat JBoss EAP, zobacz [wprowadzenie do JBoss protokołu eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) lub [wprowadzenie do JBoss EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index).

 #### <a name="applications-on-jboss-eap"></a>Aplikacje na JBoss EAP

* **Aplikacje usług sieci Web**. Usługi sieci Web zapewniają standardowy sposób współpracy między aplikacjami oprogramowania. Każda aplikacja może działać na różnych platformach i strukturach. Te usługi sieci Web ułatwiają komunikację wewnętrzną i heterogeniczną podsystemu. 

  Aby dowiedzieć się więcej, zobacz [opracowywanie aplikacji usług sieci Web w protokole eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) lub [opracowywanie aplikacji usług sieci Web na protokole EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index).

* **Aplikacje języka Java dla przedsiębiorstw (EJB)**. EJB 3,2 to interfejs API służący do tworzenia rozproszonych, transakcyjnych, bezpiecznych i przenośnych aplikacji Java EE i Dżakarta EE. EJB używa składników po stronie serwera o nazwie fasola przedsiębiorstwa do implementacji logiki biznesowej aplikacji w oddzielnym sposób, który zachęca do ponownego użycia. 

  Aby dowiedzieć się więcej, zobacz [opracowywanie aplikacji EJB w protokole eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) lub [opracowywanie aplikacji EJB na protokole EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index).

* **Aplikacje w trybie hibernacji**. Deweloperzy i Administratorzy mogą opracowywać i wdrażać interfejsy API trwałości Java (JPA) i aplikacje w trybie hibernacji przy użyciu protokołu JBoss EAP. Hibernacja to struktura mapowania obiektów relacyjnych dla języka Java. Zapewnia ona strukturę mapowania modelu domeny zorientowanego obiektowo do relacyjnej bazy danych, dzięki czemu aplikacje mogą uniknąć bezpośredniej interakcji z bazą danych. 

  Program hibernacji programu Entity Manager implementuje interfejsy programowania i reguły cyklu życia zgodnie z definicją w [specyfikacji JPA 2,1](https://www.jcp.org/en/jsr/overview). Wraz z adnotacjami hibernacji ta otoka implementuje kompletne (i autonomiczne) rozwiązanie JPA na początku systemu w przypadku dorosłych. 
  
  Aby dowiedzieć się więcej o hibernacji, zobacz [JPA on eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) lub  [trwałości Dżakarta w protokole EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api).

#### <a name="red-hat-migration-toolkit-for-applications"></a>Zestaw narzędzi do migracji Red Hat dla aplikacji
[Red Hat Migration Toolkit for Applications (MTA)](https://developers.redhat.com/products/mta/overview) to narzędzie do migracji serwerów aplikacji Java. Za pomocą tego narzędzia można przeprowadzić migrację z innego serwera aplikacji do JBoss protokołu EAP. Współpracuje z wtyczkami IDE w celu przeszukania [środowiska IDE](https://www.eclipse.org/ide/), [obszarów roboczych Red Hat CodeReady](https://developers.redhat.com/products/codeready-workspaces/overview)i [Visual Studio Code](https://code.visualstudio.com/docs/languages/java) dla języka Java. 

MTA to bezpłatne narzędzie typu "open source", które umożliwia:
* Automatyzuje analizę aplikacji.
* Obsługuje szacowanie nakładu pracy.
* Przyspiesza migrację kodu.
* Obsługuje kontenerach.
* Integruje się z konstruktorem obciążeń platformy Azure.

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>Migrowanie JBoss EAP z lokalnego na platformę Azure
Oferta platformy Azure Marketplace JBoss EAP w usłudze RHEL zainstaluje i udostępni na maszynach wirtualnych platformy Azure mniej niż 20 minut. Możesz uzyskać dostęp do tych ofert z [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/).

Ta oferta platformy Azure Marketplace obejmuje różne kombinacje wersji protokołu EAP i RHEL do obsługi Twoich wymagań. JBoss EAP jest zawsze BYOS, ale w przypadku systemu operacyjnego RHEL można wybrać opcję BYOS lub PAYG. Oferta portalu Azure Marketplace obejmuje opcje planu JBoss EAP na RHEL jako autonomiczne lub klastrowane maszyny wirtualne:

* JBoss EAP 7,2 na maszynie wirtualnej RHEL 7,7 (PAYG)
* JBoss EAP 7,2 na maszynie wirtualnej RHEL 8,0 (PAYG)
* JBoss EAP 7,3 na maszynie wirtualnej RHEL 8,0 (PAYG)
* JBoss EAP 7,2 na maszynie wirtualnej RHEL 7,7 (BYOS)
* JBoss EAP 7,2 na maszynie wirtualnej RHEL 8,0 (BYOS)
* JBoss EAP 7,3 na maszynie wirtualnej RHEL 8,0 (BYOS)

Wraz z ofertami w portalu Azure Marketplace można korzystać z szablonów szybkiego startu, aby rozpocząć pracę z migracją na platformę Azure. Te Przewodniki Szybki Start obejmują prebudowane szablony i skrypty Azure Resource Manager (ARM) do wdrażania protokołu EAP JBoss na RHEL w różnych konfiguracjach i kombinacjach wersji. Masz:

* Moduł równoważenia obciążenia.
* Prywatny adres IP na potrzeby równoważenia obciążenia i maszyn wirtualnych.
* Sieć wirtualna z pojedynczą podsiecią.
* Konfiguracja maszyny wirtualnej (klaster lub autonomiczna).
* Przykładowa aplikacja w języku Java.

Architektura rozwiązania dla tych szablonów obejmuje:

* JBoss EAP na autonomicznej maszynie wirtualnej RHEL.
* JBoss protokół EAP na wielu maszynach wirtualnych RHEL.
* JBoss protokół EAP przy użyciu zestawów skalowania maszyn wirtualnych platformy Azure.

#### <a name="linux-workload-migration-for-jboss-eap"></a>Migracja obciążenia systemu Linux dla JBoss EAP
Konstruktor obciążeń platformy Azure upraszcza proces sprawdzania koncepcji, oceny i migracji lokalnych aplikacji Java na platformę Azure. Konstruktor obciążeń integruje się z narzędziem odnajdywania Azure Migrate, aby identyfikować serwery JBoss EAP. Następnie dynamicznie generuje rozwiązania ansible element PlayBook do wdrożenia serwera JBoss protokołu EAP. Używa narzędzia Red Hat MTA do migrowania serwerów z innych serwerów aplikacji do JBoss protokołu EAP. 

Do uproszczenia migracji należą:
1. **Ocena**. Oceń JBoss klastry EAP przy użyciu maszyny wirtualnej platformy Azure lub zestawu skalowania maszyn wirtualnych.
1. **Ocena**. Skanuj aplikacje i infrastrukturę.
1. **Konfiguracja infrastruktury**. Utwórz profil obciążenia.
1. **Wdrażanie i testowanie**. Wdrażanie, migrowanie i testowanie obciążenia.
1. **Konfiguracja po wdrożeniu**. Integruj z danymi, monitorowaniem, zabezpieczeniami, kopiami zapasowymi i innymi.

## <a name="server-configuration-choice"></a>Wybór konfiguracji serwera

W przypadku wdrażania maszyny wirtualnej RHEL można wybrać opcję PAYG lub BYOS. Obrazy z [witryny Azure Marketplace](https://azuremarketplace.microsoft.com) domyślnie do PAYG. Wdróż maszynę wirtualną BYOS-Type RHEL, jeśli masz własny obraz RHEL systemu operacyjnego. Przed wdrożeniem maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych upewnij się, że konto usługi RHSM ma uprawnienia BYOS za pośrednictwem dostępu do chmury.

JBoss EAP ma zaawansowane możliwości zarządzania oraz zapewnia funkcje i interfejsy API dla aplikacji. Te funkcje zarządzania różnią się w zależności od trybu operacyjnego używanego do uruchamiania JBoss EAP. Jest on obsługiwany w systemach RHEL i Windows Server. JBoss EAP oferuje autonomiczny Tryb operacyjny serwera do zarządzania wystąpieniami dyskretnymi. Oferuje również tryb operacyjny domeny zarządzanej do zarządzania grupami wystąpień z jednego punktu kontrolnego. 

> [!NOTE]
> JBoss domeny zarządzane przez protokół EAP nie są obsługiwane w Microsoft Azure ponieważ usługi infrastruktury platformy Azure zarządzają funkcją HA. 

Zmienna środowiskowa `EAP_HOME` oznacza ścieżkę do instalacji JBoss EAP. Użyj następującego polecenia, aby uruchomić usługę JBoss EAP w trybie autonomicznym:

```
$EAP_HOME/bin/standalone.sh
```
    
Ten skrypt uruchamiania używa pliku EAP_HOME/bin/Standalone.conf w celu ustawienia niektórych preferencji domyślnych, takich jak opcje JVM. Ustawienia w tym pliku można dostosować. JBoss EAP używa pliku konfiguracji standalone.xml do uruchamiania w trybie autonomicznym domyślnie, ale można go uruchomić przy użyciu innego trybu. 

Aby uzyskać szczegółowe informacje o dostępnych autonomicznych plikach konfiguracji i sposobach ich użycia, zobacz [pliki konfiguracji serwera autonomicznego dla plików konfiguracji protokołu eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) lub [autonomicznego serwera dla protokołu EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files). 

Aby rozpocząć JBoss EAP z inną konfiguracją, użyj `--server-config` argumentu. Na przykład:
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
Aby uzyskać pełną listę wszystkich dostępnych argumentów skryptu uruchamiania i ich przeznaczenia, użyj `--help` argumentu. Aby uzyskać więcej informacji, zobacz [argumenty środowiska uruchomieniowego serwera w przypadku protokołu eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) lub [argumentów środowiska uruchomieniowego serwera w protokole EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime).
    
JBoss EAP może również współpracować z trybem klastra. Obsługa komunikatów klastra protokołu EAP JBoss umożliwia grupowanie serwerów komunikatów protokołu EAP JBoss w celu udostępniania obciążenia przetwarzania komunikatów. Każdy aktywny węzeł w klastrze jest aktywnym serwerem komunikatów protokołu EAP JBoss, który zarządza własnymi komunikatami i obsługuje własne połączenia. Aby dowiedzieć się więcej, zobacz [Omówienie klastrów w temacie eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) lub [ Omówienie klastrów w protokole EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview). 

## <a name="support-and-subscription-notes"></a>Uwagi dotyczące pomocy technicznej i subskrypcji
Te szablony szybkiego startu są oferowane w następujący sposób: 

- System operacyjny RHEL jest oferowany jako PAYG lub BYOS za pośrednictwem modelu obrazów Red Hat Gold.
- JBoss EAP jest oferowany tylko jako BYOS.

#### <a name="using-rhel-os-with-the-payg-model"></a>Korzystanie z systemu operacyjnego RHEL z modelem PAYG

Domyślnie te szablony szybkiego startu korzystają z obrazu na żądanie RHEL 7,7 lub 8,0 PAYG z witryny Azure Marketplace. Obrazy PAYG mają dodatkową RHELą subskrypcję co godzinę w stosunku do normalnych kosztów obliczeniowych, sieci i magazynu. W tym samym czasie wystąpienie jest zarejestrowane w Twojej subskrypcji Red Hat. Oznacza to, że będziesz korzystać z jednego z uprawnień użytkownika. 

Ten obraz PAYG będzie prowadzić do "podwójnego rozliczania". Możesz uniknąć tego problemu, tworząc własny obraz RHEL. Aby dowiedzieć się więcej, przeczytaj artykuł z bazy wiedzy Red Hat [jak udostępnić maszynę wirtualną RHEL dla Microsoft Azure](https://access.redhat.com/articles/uploading-rhel-image-to-azure). Lub Aktywuj swój obraz [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold.

Aby uzyskać szczegółowe informacje na temat cen maszyn wirtualnych PAYG, zobacz [Cennik usługi Red Hat Enterprise Linux](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/). Aby korzystać z RHEL w modelu PAYG, musisz mieć subskrypcję platformy Azure z określoną metodą płatności dla [RHEL 7,7 w witrynie Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) lub [RHEL 8,0 w witrynie Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM). Oferty te wymagają określenia metody płatności w ramach subskrypcji platformy Azure.

#### <a name="using-rhel-os-with-the-byos-model"></a>Używanie systemu operacyjnego RHEL z modelem BYOS

Aby korzystać z BYOS dla systemu operacyjnego RHEL, musisz mieć prawidłową subskrypcję Red Hat z uprawnieniami do korzystania z systemu operacyjnego RHEL na platformie Azure. Przed wdrożeniem systemu operacyjnego RHEL z modelem BYOS należy spełnić następujące wymagania wstępne:

1. Upewnij się, że masz uprawnienia do RHEL systemu operacyjnego i JBoss protokołu EAP dołączone do Twojej subskrypcji Red Hat.
2. Autoryzuj swój identyfikator subskrypcji platformy Azure, aby korzystać z obrazów RHEL BYOS. Postępuj zgodnie z [dokumentacją dotyczącą zarządzania subskrypcją Red Hat](https://access.redhat.com/documentation/red_hat_subscription_management/1/) , aby ukończyć proces, który obejmuje następujące kroki:

   1. Włącz Microsoft Azure jako dostawcę na pulpicie nawigacyjnym usługi Red Hat Cloud Access.

   1. Dodaj identyfikatory subskrypcji platformy Azure.

   1. Włącz nowe produkty na potrzeby dostępu do chmury na Microsoft Azure.
    
   1. Aktywuj obrazy Red Hat Gold dla Twojej subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [obrazy Red Hat Gold na Microsoft Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/cloud-access-gold-images_cloud-access#proc_using-gold-images-azure_cloud-access).

   1. Zaczekaj, aż obrazy Red Hat Gold będą dostępne w Twojej subskrypcji platformy Azure. Te obrazy są zwykle dostępne w ciągu trzech godzin od złożenia.
    
3. Zaakceptuj warunki i postanowienia witryny Azure Marketplace dotyczące obrazów RHEL BYOS. Ten proces można wykonać, uruchamiając następujące polecenia interfejsu wiersza poleceń platformy Azure. Aby uzyskać więcej informacji, zobacz artykuły z [RHEL BYOS Gold w dokumentacji platformy Azure](./byos.md) . Ważne jest, aby uruchomić najnowszą wersję interfejsu wiersza polecenia platformy Azure.

   1. Otwórz sesję interfejsu wiersza polecenia platformy Azure i Uwierzytelnij się przy użyciu konta platformy Azure. Aby uzyskać pomoc, zobacz [Logowanie za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).

   1. Sprawdź, czy w Twojej subskrypcji są dostępne obrazy RHEL BYOS, uruchamiając następujące polecenie interfejsu wiersza polecenia. Jeśli nie otrzymasz żadnych wyników w tym miejscu, upewnij się, że subskrypcja platformy Azure została aktywowana dla obrazów RHEL BYOS.
   
      ```
      az vm image list --offer rhel-byos --all
      ```

   1. Uruchom następujące polecenie, aby zaakceptować odpowiednio warunki systemu Azure Marketplace dotyczące RHEL 7,7 BYOS i RHEL 8,0 BYOS:
      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
      ``` 

      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
      ``` 
   
Twoja subskrypcja jest teraz gotowa do wdrożenia RHEL 7,7 lub 8,0 BYOS na maszynach wirtualnych platformy Azure.

#### <a name="using-jboss-eap-with-the-byos-model"></a>Korzystanie z JBoss EAP z modelem BYOS

JBoss EAP jest dostępny tylko w systemie Azure za pomocą modelu BYOS. Podczas wdrażania tego szablonu należy podać poświadczenia RHSM wraz z IDENTYFIKATORem puli RHSM z prawidłowymi uprawnieniami protokołu EAP. Jeśli nie masz uprawnień do protokołu EAP, przed rozpoczęciem Uzyskaj [subskrypcję ewaluacyjną JBoss EAP](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) .

## <a name="deployment-options"></a>Opcje wdrożenia

Szablon można wdrożyć w następujący sposób:

- **Program PowerShell**. Wdróż szablon, uruchamiając następujące polecenia: 

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
 
  Informacje o instalowaniu i konfigurowaniu Azure PowerShell można znaleźć w [dokumentacji programu PowerShell](/powershell/azure/).  

- **Interfejs wiersza polecenia platformy Azure**. Wdróż szablon, uruchamiając następujące polecenia:

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az deployment group create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

  Aby uzyskać szczegółowe informacje na temat instalowania i konfigurowania interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia](/cli/azure/install-azure-cli).

- Użycie witryny **Azure Portal**. Możesz wdrożyć do Azure Portal, przechodząc do szablonów szybkiego startu platformy Azure zgodnie z opisem w następnej sekcji. Po wybraniu przewodnika Szybki Start wybierz przycisk **Wdróż na platformie Azure** lub **Przeglądaj w serwisie GitHub** .

## <a name="azure-quickstart-templates"></a>Szablony przewodników Szybki start platformy Azure

Możesz rozpocząć od użycia jednego z następujących szablonów szybkiego startu dla JBoss EAP na RHEL, który spełnia cel wdrożenia:

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> JBoss protokół EAP na RHEL (autonomiczna maszyna wirtualna)</a>. Spowoduje to wdrożenie aplikacji sieci Web o nazwie JBoss-EAP na platformie Azure do JBoss protokołu EAP 7,2 lub 7,3 uruchomionego na maszynie wirtualnej RHEL 7,7 lub 8,0.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> JBoss protokół EAP w RHEL (klastrowane, wiele maszyn wirtualnych)</a>. Spowoduje to wdrożenie aplikacji sieci Web o nazwie EAP-Session-Replication w klastrze JBoss z protokołem EAP 7,2 lub 7,3 działającym na *n* liczbach maszyn wirtualnych RHEL 7,7 lub 8,0. Użytkownik decyduje o *n* wartości. Wszystkie maszyny wirtualne są dodawane do puli zaplecza modułu równoważenia obciążenia.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> JBoss protokół EAP w RHEL (klastrowane, zestaw skalowania maszyn wirtualnych)</a>. Spowoduje to wdrożenie aplikacji sieci Web o nazwie EAP-Session-Replication w klastrze JBoss EAP 7,2 lub 7,3 z systemem RHEL 7,7 lub 8,0.

## <a name="resource-links"></a>Linki zasobów

* [Korzyść użycia hybrydowego platformy Azure](../../windows/hybrid-use-benefit-licensing.md)
* [Konfigurowanie aplikacji Java dla Azure App Service](../../../app-service/configure-language-java.md)
* [JBoss EAP na platformie Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [JBoss EAP w systemie Azure App Service Linux](../../../app-service/quickstart-java.md)
* [Wdróż JBoss EAP na Azure App Service](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [JBoss EAP 7,2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/).
* Dowiedz się więcej o [JBoss EAP 7,3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/).
* Dowiedz się więcej o [zarządzaniu subskrypcją Red Hat](https://access.redhat.com/products/red-hat-subscription-management).
* Dowiedz się więcej [na temat obciążeń firmy Red Hat na platformie Azure](./overview.md).
* Wdróż [JBoss EAP na maszynie wirtualnej RHEL lub zestawu skalowania maszyn wirtualnych w witrynie Azure Marketplace](https://aka.ms/AMP-JBoss-EAP).
* Wdróż [JBoss EAP na maszynie wirtualnej RHEL lub zestawu skalowania maszyn wirtualnych na podstawie szablonów szybkiego startu platformy Azure](https://aka.ms/Quickstart-JBoss-EAP).