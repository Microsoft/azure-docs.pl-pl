---
title: Szybki Start — JBoss Enterprise Application (EAP) na Red Hat Enterprise Linux (RHEL) do maszyny wirtualnej platformy Azure i zestawu skalowania maszyn wirtualnych
description: Jak wdrażać aplikacje Java przedsiębiorstwa przy użyciu systemu Red Hat JBoss EAP na maszynie wirtualnej RHEL i zestawie skalowania maszyn wirtualnych.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.date: 10/30/2020
ms.openlocfilehash: d97148393d3158e38f9740d4a8f8e17dd04326d5
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135439"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Wdrażaj aplikacje Java przedsiębiorstwa na platformie Azure przy użyciu protokołu JBoss EAP na Red Hat Enterprise Linux

Te szablony szybkiego startu pokazują, jak wdrożyć [Protokół EAP JBoss](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) z [RHEL](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) na platformie Azure Virtual Machines i Virtual Machine Scale Sets. W ramach wdrożenia będziesz mieć przykładową aplikację w języku Java umożliwiającą zweryfikowanie wdrożenia. JBoss EAP to platforma serwera aplikacji open source. Zapewnia ona zabezpieczenia, skalowalność i wydajność klasy korporacyjnej dla aplikacji Java. RHEL to platforma systemu operacyjnego Open Source. Pozwala ona skalować istniejące aplikacje i wdrażać nowe technologie we wszystkich środowiskach. JBoss EAP i RHEL obejmują wszystko, czego potrzebujesz do kompilowania, uruchamiania i wdrażania aplikacji Java przedsiębiorstwa oraz zarządzania nimi w dowolnym środowisku. To doskonałe rozwiązanie typu "open source" dla środowisk lokalnych, wirtualnych i prywatnych, publicznych lub hybrydowych.

## <a name="prerequisite"></a>Wymaganie wstępne 

* Konto platformy Azure z aktywną subskrypcją. Aby uzyskać subskrypcję platformy Azure, Aktywuj środki na korzystanie z [platformy Azure dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub [Utwórz bezpłatne konto](https://azure.microsoft.com/pricing/free-trial).

* Instalacja protokołu EAP JBoss — musisz mieć konto Red Hat z uprawnieniem usługi Red Hat Subscription Management (RHSM) dla JBoss EAP. To uprawnienie umożliwia pobranie przetestowanej i certyfikowanej wersji protokołu EAP JBoss firmy Red Hat.  Jeśli nie masz uprawnień do protokołu EAP, przed rozpoczęciem Uzyskaj [subskrypcję ewaluacyjną JBoss EAP](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) . Aby utworzyć nową subskrypcję Red Hat, przejdź do [portalu Red Hat Customer Portal](https://access.redhat.com/) i skonfiguruj konto.
F
* [Interfejs Command-Line platformy Azure](https://docs.microsoft.com/cli/azure/overview).

* Opcje RHEL — wybierz kolejno pozycje płatność zgodnie z rzeczywistym użyciem (PAYG) lub Przenieś własną subskrypcję (BYOS). W przypadku BYOS należy aktywować obraz [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold przed wdrożeniem szablonu szybkiego startu.

## <a name="java-ee--jakarata-ee-application-migration"></a>Migracja aplikacji Java EE/Jakarata EE

### <a name="migrate-to-jboss-eap"></a>Migrowanie do JBoss EAP
JBoss EAP 7,2 i 7,3 są certyfikowanymi implementacjami specyfikacji Java Enterprise Edition (Java EE) 8 i Dżakarta EE 8. JBoss EAP udostępnia wstępnie skonfigurowane opcje dla funkcji, takich jak klastrowanie wysokiej dostępności (HA), obsługa komunikatów i buforowanie rozproszone. Umożliwia ona również użytkownikom pisanie, wdrażanie i uruchamianie aplikacji przy użyciu różnych interfejsów API i usług udostępnianych przez protokół EAP JBoss.  Aby uzyskać dodatkowe informacje na temat JBoss EAP, zapoznaj [się z artykułem wprowadzenie do JBoss protokołu eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) lub [wprowadzenie do JBoss EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index).

 #### <a name="applications-on-jboss-eap"></a>Aplikacje na JBoss EAP

* Aplikacje usług sieci Web — usługi sieci Web zapewniają standardowy sposób współdziałania między różnymi aplikacjami oprogramowania. Każda aplikacja może działać na różnych platformach i strukturach. Te usługi sieci Web ułatwiają komunikację wewnętrzną i heterogeniczną podsystemu. Aby dowiedzieć się więcej, odwiedź stronę [opracowywania aplikacji usług sieci Web w protokole eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) lub [opracowywanie aplikacji usług sieci Web na protokole EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index).

* Aplikacje języka Java dla przedsiębiorstw (EJB) — EJB 3,2 to interfejs API służący do tworzenia rozproszonych, transakcyjnych, bezpiecznych i przenośnych aplikacji platformy Java EE i Dżakarta EE. EJB używa składników po stronie serwera o nazwie fasola przedsiębiorstwa do implementacji logiki biznesowej aplikacji w oddzielnym sposób, który zachęca do ponownego użycia. Aby dowiedzieć się więcej, odwiedź stronę [opracowywanie aplikacji EJB na protokole eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) lub [opracowywanie aplikacji EJB na protokole EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index).

* Aplikacje w trybie hibernacji — deweloperzy i Administratorzy mogą opracowywać i wdrażać aplikacje/Hibernate API trwałości języka Java (JPA) przy użyciu protokołu EAP JBoss. Hibernacja to struktura mapowania obiektów relacyjnych dla języka Java. Zapewnia ona strukturę mapowania modelu domeny zorientowanego obiektowo na relacyjną bazę danych, co umożliwia aplikacjom uniknięcie bezpośredniej interakcji z bazą danych. Program hibernacji programu Entity Manager implementuje interfejsy programowania i reguły cyklu życia zgodnie z definicją w [specyfikacji JPA 2,1](https://www.jcp.org/en/jsr/overview). Wraz z adnotacjami hibernacji ta otoka implementuje kompletne (i autonomiczne) rozwiązanie JPA na początku systemu w przypadku dorosłych. Aby dowiedzieć się więcej o hibernacji, odwiedź stronę [JPA na temat trwałości protokołu eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) lub  [z użyciem protokołu EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api).

#### <a name="red-hat-migration-toolkit-for-applications-mta"></a>Zestaw narzędzi do migracji Red Hat dla aplikacji (MTA)
[Red Hat Migration Toolkit for Applications (MTA)](https://developers.redhat.com/products/mta/overview) to narzędzie do migracji serwerów aplikacji Java. Za pomocą tego narzędzia można przeprowadzić migrację z innego serwera aplikacji do JBoss protokołu EAP. Współpracuje z wtyczkami IDE w celu przeszukania [środowiska IDE](https://www.eclipse.org/ide/), [obszarów roboczych Red Hat CodeReady](https://developers.redhat.com/products/codeready-workspaces/overview)i [Visual Studio Code (vs Code)](https://code.visualstudio.com/docs/languages/java) dla języka Java.  MTA to bezpłatne narzędzie typu "open source", które umożliwia:
* Automatyzacja analizy aplikacji
* Szacowanie nakładu pracy pomocy technicznej
* Przyspieszanie migracji kodu
* Obsługa kontenerach
* Integruje się z konstruktorem obciążeń platformy Azure

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>Migrowanie JBoss EAP z lokalnego na platformę Azure
Oferta platformy Azure Marketplace JBoss EAP w usłudze RHEL zainstaluje i udostępni na maszynach wirtualnych platformy Azure mniej niż 20 minut. Możesz uzyskać dostęp do tych ofert z [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/)

Ta oferta Marketplace obejmuje różne kombinacje wersji protokołu EAP i RHEL w celu obsługi Twoich wymagań. JBoss EAP zawsze jest przyłączany do subskrypcji (BYOS), ale w przypadku systemu operacyjnego RHEL można wybrać opcję BYOS lub płatność zgodnie z rzeczywistym użyciem (PAYG). Oferta Azure Marketplace obejmuje opcje planu JBoss EAP na RHEL jako autonomiczne lub klastrowane maszyny wirtualne:

* JBoss EAP 7,2 na maszynie wirtualnej RHEL 7,7 (PAYG)
* JBoss EAP 7,2 na maszynie wirtualnej RHEL 8,0 (PAYG)
* JBoss EAP 7,3 na maszynie wirtualnej RHEL 8,0 (PAYG)
* JBoss EAP 7,2 na maszynie wirtualnej RHEL 7,7 (BYOS)
* JBoss EAP 7,2 na maszynie wirtualnej RHEL 8,0 (BYOS)
* JBoss EAP 7,3 na maszynie wirtualnej RHEL 8,0 (BYOS)

Wraz z ofertami w witrynie Azure Marketplace dostępne są szablony szybkiego startu, które umożliwiają rozpoczęcie pracy z migracją na platformę Azure. Te Przewodniki Szybki Start obejmują wstępnie skompilowane szablony Azure Resource Manager (ARM) i skrypt służący do wdrażania protokołu EAP JBoss na RHEL w różnych konfiguracjach i kombinacjach wersji. Masz:

* Load Balancer (LB)
* Prywatny adres IP na potrzeby równoważenia obciążenia i maszyn wirtualnych
* Virtual Network (VNET) z jedną podsiecią
* Konfiguracja maszyny wirtualnej (klaster lub autonomiczna)
* Przykładowa aplikacja w języku Java

Architektura rozwiązania dla tych szablonów obejmuje:

* JBoss EAP na autonomicznej maszynie wirtualnej RHEL
* JBoss protokół EAP na wielu maszynach wirtualnych RHEL
* JBoss klastrowane protokołu EAP przy użyciu zestawu skalowania maszyn wirtualnych platformy Azure

#### <a name="linux-workload-migration-for-jboss-eap"></a>Migracja obciążenia systemu Linux dla JBoss EAP
Konstruktor obciążeń platformy Azure upraszcza proces weryfikacji koncepcji, oceny i migracji lokalnych aplikacji Java na platformę Azure. Konstruktor obciążeń integruje się z narzędziem Azure Migrate Discovery, aby identyfikować serwery JBoss EAP. Następnie zostanie dynamicznie wygenerowany rozwiązania ansible element PlayBook dla wdrożenia JBoss protokołu EAP. Za pomocą narzędzia Red Hat MTA można migrować serwery z innych serwerów aplikacji do JBoss protokołu EAP. Do uproszczenia migracji należą:
* Obliczanie — JBoss klastrów EAP przy użyciu maszyny wirtualnej platformy Azure lub zestawu skalowania maszyn wirtualnych
* Ocena — skanowanie aplikacji i infrastruktury
* Konfiguracja infrastruktury — tworzy profil obciążenia
* Wdrażanie i testowanie — wdrażanie, migrowanie i testowanie obciążeń
* Konfiguracja po wdrożeniu — integruje się z danymi, monitorowaniem, zabezpieczeniami, tworzeniem kopii zapasowych i innymi

## <a name="server-configuration-choice"></a>Wybór konfiguracji serwera

W przypadku wdrażania maszyny wirtualnej RHEL można wybrać jedną z opcji PAYG lub BYOS. Obrazy w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com) są domyślne do PAYG. Wdróż maszynę wirtualną typu BYOS RHEL, jeśli masz własny obraz RHEL systemu operacyjnego. Przed wdrożeniem maszyn wirtualnych lub zestawu skalowania maszyn wirtualnych upewnij się, że konto RHSM ma uprawnienia BYOS za pośrednictwem usługi F Cloud Access.

JBoss EAP ma zaawansowane możliwości zarządzania, a także udostępnia funkcje i interfejsy API dla aplikacji. Te funkcje zarządzania różnią się w zależności od trybu operacyjnego używanego do uruchamiania JBoss EAP. Jest on obsługiwany w systemach RHEL i Windows Server. JBoss EAP oferuje autonomiczny Tryb operacyjny serwera do zarządzania wystąpieniami dyskretnymi. Oferuje również tryb operacyjny domeny zarządzanej do zarządzania grupami wystąpień z jednego punktu kontrolnego. Uwaga: JBoss domeny zarządzane przy użyciu protokołu EAP nie są obsługiwane w Microsoft Azure ponieważ funkcja wysokiej dostępności (HA) jest zarządzana przez usługi infrastruktury platformy Azure. Zmienna środowiskowa o nazwie *EAP_HOME* jest używana w celu określenia ścieżki do instalacji JBoss EAP.

**Uruchom JBoss EAP jako serwer autonomiczny** — następujące polecenie umożliwia uruchomienie usługi EAP w trybie autonomicznym.

```
$EAP_HOME/bin/standalone.sh
```
    
Ten skrypt uruchamiania używa pliku EAP_HOME/bin/Standalone.conf w celu ustawienia niektórych preferencji domyślnych, takich jak opcje JVM. Ustawienia można dostosować w tym pliku. JBoss EAP używa pliku konfiguracji standalone.xml do uruchamiania w trybie autonomicznym domyślnie, ale można go uruchomić przy użyciu innego trybu. Aby uzyskać szczegółowe informacje o dostępnych autonomicznych plikach konfiguracji i sposobach ich użycia, zapoznaj się z tematem [pliki konfiguracji serwera autonomicznego dla plików konfiguracji protokołu eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) lub [autonomicznego serwera dla protokołu EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) . Aby rozpocząć JBoss EAP z inną konfiguracją, użyj argumentu--Server-config. Przykład:
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
Aby zapoznać się z pełną listą wszystkich argumentów skryptu uruchamiania i ich przeznaczenia, użyj argumentu--help lub sprawdź [argumenty środowiska uruchomieniowego serwera w programie eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) lub [w argumentach środowiska wykonawczego serwera w sekcji EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) .
    
JBoss EAP może również współpracować z trybem klastra. Zapoznaj się z [omówieniem klastrów w temacie eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) lub [ Omówienie klastrów w protokole EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview) , aby dowiedzieć się więcej. Obsługa komunikatów klastra protokołu EAP JBoss umożliwia grupowanie serwerów komunikatów protokołu EAP JBoss w celu udostępniania obciążenia przetwarzania komunikatów. Każdy aktywny węzeł w klastrze jest aktywnym serwerem komunikatów protokołu EAP JBoss, który zarządza własnymi komunikatami i obsługuje własne połączenia.

## <a name="support-and-subscription-notes"></a>Uwagi dotyczące pomocy technicznej i subskrypcji
Te szablony szybkiego startu są oferowane w następujący sposób: 

- System operacyjny RHEL jest oferowany jako PAYG lub BYOS za pośrednictwem modelu obrazów Red Hat Gold
- JBoss EAP jest oferowany tylko jako BYOS

#### <a name="using-rhel-os-with-payg-model"></a>Korzystanie z systemu operacyjnego RHEL z modelem PAYG

Domyślnie te szablony szybkiego startu korzystają z obrazu na żądanie RHEL 7,7 lub 8,0 PAYG z galerii platformy Azure. Obrazy PAYG będą mieć dodatkową RHELą subskrypcję co godzinę w stosunku do normalnych kosztów obliczeniowych, sieci i magazynu. W tym samym czasie wystąpienie zostanie zarejestrowane w Twojej subskrypcji Red Hat. Oznacza to, że będziesz korzystać z jednego z uprawnień użytkownika. Ten obraz PAYG będzie prowadzić do "podwójnego rozliczania". Możesz uniknąć tego problemu, tworząc własny obraz RHEL. Aby dowiedzieć się więcej, przeczytaj ten artykuł z bazy wiedzy Red Hat (KB) na temat [udostępniania maszyny wirtualnej RHEL dla Microsoft Azure](https://access.redhat.com/articles/uploading-rhel-image-to-azure). Lub Aktywuj swój obraz [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold.

Zapoznaj się z [cennikiem Red Hat Enterprise Linux](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/) , aby uzyskać szczegółowe informacje o cenach maszyn wirtualnych PAYG. Aby korzystać z RHEL w modelu PAYG, potrzebna jest subskrypcja platformy Azure z określoną metodą płatności dla [RHEL 7,7 Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) lub [RHEL 8,0 Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM). Oferty te wymagają określenia metody płatności w ramach subskrypcji platformy Azure.

#### <a name="using-rhel-os-with-byos-model"></a>Używanie systemu operacyjnego RHEL z modelem BYOS

Aby korzystać z BYOS dla systemu operacyjnego RHEL, musisz mieć prawidłową subskrypcję Red Hat z uprawnieniami do korzystania z systemu operacyjnego RHEL na platformie Azure. Przed wdrożeniem tego szablonu szybkiego startu należy spełnić następujące wymagania wstępne:

1. Upewnij się, że masz uprawnienia do RHEL systemu operacyjnego i JBoss protokołu EAP dołączone do Twojej subskrypcji Red Hat.
2. Autoryzuj swój identyfikator subskrypcji platformy Azure, aby korzystać z obrazów RHEL BYOS. Postępuj zgodnie z [dokumentacją RHSM (Red Hat Subscription Management)](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs) , aby ukończyć proces, który obejmuje następujące kroki:

    2,1 Włącz Microsoft Azure jako dostawcę na pulpicie nawigacyjnym usługi Red Hat Cloud Access.

    2,2 Dodawanie identyfikatorów subskrypcji platformy Azure.

    2,3. Włącz nowe produkty na potrzeby dostępu do chmury na Microsoft Azure.
    
    2,4. Aktywuj obrazy Red Hat Gold dla Twojej subskrypcji platformy Azure. Aby uzyskać więcej informacji, zapoznaj się z rozdziałem na temat [włączania i konserwowania subskrypcji](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure) w celu uzyskania dostępu do chmury.

    2,5 Zaczekaj, aż obrazy Red Hat Gold będą dostępne w Twojej subskrypcji platformy Azure. Te złote obrazy są zwykle dostępne w ciągu 3 godzin od złożenia.
    
3. Zaakceptuj warunki i postanowienia witryny Azure Marketplace dotyczące obrazów RHEL BYOS. Ten proces można wykonać, uruchamiając polecenia interfejsu CLI platformy Azure Command-Line, jak podano poniżej. Aby uzyskać więcej informacji, zobacz artykuł [RHEL BYOS Gold w dokumentacji platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos) , aby uzyskać więcej szczegółów. Ważne jest, aby uruchomić najnowszą wersję interfejsu wiersza polecenia platformy Azure.

    3,1 Uruchom sesję interfejsu wiersza polecenia platformy Azure i Uwierzytelnij się przy użyciu konta platformy Azure. Aby uzyskać pomoc, zobacz [Logowanie za pomocą interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli) . Przed przejściem do programu upewnij się, że korzystasz z najnowszej wersji interfejsu wiersza polecenia platformy Azure.

    3,2 Upewnij się, że w Twojej subskrypcji są dostępne obrazy RHEL BYOS, uruchamiając następujące polecenie interfejsu wiersza polecenia. Jeśli nie masz żadnych wyników w tym miejscu, zapoznaj się z tematem #2 i upewnij się, że subskrypcja platformy Azure została aktywowana dla obrazów RHEL BYOS.
   
   ```
   az vm image list --offer rhel-byos --all
   ```

    3,3 Uruchom następujące polecenie, aby zaakceptować odpowiednio warunki witryny Marketplace dotyczące RHEL 7,7 BYOS i RHEL 8,0 BYOS.
   ```
   az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
   ``` 

   ```
   az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
   ``` 
   
4. Twoja subskrypcja jest teraz gotowa do wdrożenia RHEL 7,7 lub 8,0 BYOS na maszynach wirtualnych platformy Azure.

#### <a name="using-jboss-eap-with-byos-model"></a>Korzystanie z JBoss EAP z modelem BYOS

JBoss EAP jest dostępny tylko w systemie Azure za pomocą modelu BYOS. Podczas wdrażania tego szablonu należy podać poświadczenia RHSM wraz z IDENTYFIKATORem puli RHSM z prawidłowymi uprawnieniami protokołu EAP. Jeśli nie masz uprawnień do protokołu EAP, przed rozpoczęciem Uzyskaj [subskrypcję ewaluacyjną JBoss EAP](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) .

## <a name="how-to-consume"></a>Jak korzystać z

Szablon można wdrożyć na trzy sposoby:

- Użyj programu PowerShell — Wdróż szablon, uruchamiając następujące polecenia: (Wyewidencjonuj [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) , aby uzyskać informacje dotyczące instalowania i konfigurowania Azure PowerShell).

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
    
- Korzystanie z interfejsu wiersza polecenia platformy Azure — Wdróż szablon, uruchamiając następujące polecenia: (Aby uzyskać szczegółowe informacje na temat instalowania i konfigurowania interfejsu wiersza polecenia platformy Azure, zobacz [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) ).

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az group deployment create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

- Użyj Azure Portal — możesz wdrożyć do Azure Portal, przechodząc do *szablonów szybkiego startu platformy Azure* , zgodnie z poniższą sekcją. Po wybraniu przewodnika Szybki Start kliknij pozycję **Wdróż na platformie Azure** lub **Przeglądaj w serwisie GitHub** .

## <a name="azure-quickstart-templates"></a>Szablony szybkiego startu platformy Azure

Możesz zacząć od jednego z szablonów szybkiego startu JBoss EAP na kombinację RHEL, która spełnia cel wdrożenia. Poniżej znajduje się lista dostępnych szablonów szybkiego startu.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> JBoss EAP on RHEL (autonomiczna maszyna wirtualna)</a> — spowoduje to wdrożenie aplikacji sieci Web o nazwie JBoss-EAP na platformie Azure do JBoss EAP 7,2 lub 7,3 działającej na maszynie wirtualnej RHEL 7,7 lub 8,0.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> JBoss EAP on RHEL (klastrowane, wiele maszyn wirtualnych)</a> — spowoduje to wdrożenie aplikacji sieci Web o nazwie EAP-Session-Replication w KLASTRZE JBoss EAP 7,2 lub 7,3 z numerem RHEL 7,7 lub 8,0. Wartość "n" jest ustalana przez użytkownika. Wszystkie maszyny wirtualne są dodawane do puli zaplecza Load Balancer.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> JBoss EAP on RHEL (klastrowane, zestaw skalowania maszyn wirtualnych)</a> — spowoduje to wdrożenie aplikacji sieci Web o nazwie EAP-Session-Replication w KLASTRZE JBoss EAP 7,2 lub 7,3 działającym na RHEL 7,7 lub 8,0 wystąpienia zestawu skalowania maszyn wirtualnych.

## <a name="resource-links"></a>Linki do zasobów:

* [Korzyści z używania hybrydowej platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)
* [Konfigurowanie aplikacji Java dla Azure App Service](https://docs.microsoft.com/azure/app-service/configure-language-java)
* [JBoss EAP na platformie Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [JBoss EAP w systemie Azure App Service Linux](https://docs.microsoft.com/azure/app-service/quickstart-java) Start
* [Jak wdrożyć JBoss EAP na Azure App Service](https://github.com/JasonFreeberg/jboss-on-app-service) samouczku

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [JBoss EAP 7,2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/)
* Dowiedz się więcej o [JBoss EAP 7,3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/)
* Dowiedz się więcej o [zarządzaniu subskrypcją Red Hat](https://access.redhat.com/products/red-hat-subscription-management)
* Microsoft docs dla [Red Hat na platformie Azure](https://aka.ms/rhel-docs)
* Wdróż [JBoss EAP na maszynie wirtualnej RHEL/zestawie skalowania maszyn wirtualnych w witrynie Azure Marketplace](https://aka.ms/AMP-JBoss-EAP)
* Wdróż [JBoss EAP na maszynie wirtualnej RHEL/zestawie skalowania maszyn wirtualnych na platformie Azure — szybki start](https://aka.ms/Quickstart-JBoss-EAP)
