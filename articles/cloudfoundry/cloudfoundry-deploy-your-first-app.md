---
title: Wdrażanie pierwszej aplikacji w rozwiązaniu Cloud Foundry na platformie Microsoft Azure
description: Wdrażanie aplikacji w usłudze Cloud Foundry platformie Azure
author: seanmck
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: 1cffc36cbd4f24bbcbb5996a323ffa963e311693
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530963"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Wdrażanie pierwszej aplikacji w rozwiązaniu Cloud Foundry na platformie Microsoft Azure

[Cloud Foundry](https://cloudfoundry.org) to popularna platforma aplikacji typu open source dostępna w Microsoft Azure. W tym artykule pokażemy, jak wdrożyć aplikację i zarządzać Cloud Foundry w środowisku platformy Azure.

## <a name="create-a-cloud-foundry-environment"></a>Tworzenie środowiska Cloud Foundry danych

Istnieje kilka opcji tworzenia środowiska Cloud Foundry na platformie Azure:

- Skorzystaj z [oferty Pivotal Cloud Foundry w][pcf-azuremarketplace] witrynie Azure Marketplace, aby utworzyć środowisko standardowe, które obejmuje program PCF Ops Manager i usługę Azure Service Broker. Pełne [instrukcje dotyczące wdrażania][pcf-azuremarketplace-pivotaldocs] oferty platformy handlowej można znaleźć w dokumentacji rozwiązania Pivotal.
- Utwórz dostosowane środowisko, [wdrażając aplikację Pivotal Cloud Foundry ręcznie.][pcf-custom]
- [Wdrażaj pakiety Cloud Foundry typu open source bezpośrednio,][oss-cf-bosh] ustawiając katalog [BOSH](https://bosh.io) — maszynę wirtualną, która koordynuje wdrażanie Cloud Foundry danych.

> [!IMPORTANT] 
> Jeśli wdrażasz program PCF z witryny Azure Marketplace, zanotuj adres SYSTEMDOMAINURL i poświadczenia administratora wymagane do uzyskania dostępu do Menedżera aplikacji pivotal, które opisano w przewodniku wdrażania platformy handlowej. Są one potrzebne do ukończenia tego samouczka. W przypadku wdrożeń na platformie handlowej adres SYSTEMDOMAINURL ma postać `https://system.*ip-address*.cf.pcfazure.com` .

## <a name="connect-to-the-cloud-controller"></a>Nawiązywanie połączenia z kontrolerem chmury

Kontroler chmury jest podstawowym punktem wejścia do Cloud Foundry do wdrażania aplikacji i zarządzania nimi. Podstawowy interfejs API kontrolera chmury (CCAPI) jest interfejsem API REST, ale jest dostępny za pośrednictwem różnych narzędzi. W tym przypadku wchodzimy z nim w interakcje za pośrednictwem interfejsu [wiersza Cloud Foundry wiersza polecenia][cf-cli]. Interfejs wiersza polecenia można zainstalować w systemie Linux, macOS lub Windows, ale jeśli w ogóle nie chcesz go instalować, jest on wstępnie zainstalowany w Azure Cloud Shell [.][cloudshell-docs]

Aby się zalogować, należy do elementu SYSTEMDOMAINURL uzyskanego z `api` wdrożenia na platformie handlowej. Ponieważ wdrożenie domyślne używa certyfikatu z podpisem własnym, należy również dołączyć `skip-ssl-validation` przełącznik.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Zostanie wyświetlony monit o zalogowanie się do kontrolera chmury. Użyj poświadczeń konta administratora uzyskanych w ramach kroków wdrażania witryny Marketplace.

Cloud Foundry zapewnia *organizacji i* *przestrzenie* jako przestrzenie nazw do izolowania zespołów i środowisk w ramach wdrożenia udostępnionego. Wdrożenie platformy handlowej PCF obejmuje domyślną organizacja systemu i zestaw przestrzeni utworzonych w celu zawierania podstawowych składników, takich jak usługa skalowania automatycznego i usługa Azure Service Broker.  Na razie wybierz przestrzeń *systemową.*


## <a name="create-an-org-and-space"></a>Tworzenie organizacji i przestrzeni

W przypadku wpisania ciągu zostanie wyświetlony zestaw aplikacji systemowych, które zostały wdrożone w przestrzeni `cf apps` systemowej w organizacji systemu. 

Organizacja systemu powinna być *zarezerwowana* dla aplikacji systemowych, więc utwórz organizacji i miejsce do przechowywania naszej przykładowej aplikacji.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Użyj docelowego polecenia, aby przełączyć się do nowej organizacji i przestrzeni:

```bash
cf target -o testorg -s dev
```

Teraz podczas wdrażania aplikacji jest ona automatycznie tworzona w nowej organizacji i przestrzeni. Aby potwierdzić, że obecnie w nowej organizacji/przestrzeni nie ma żadnych aplikacji, wpisz `cf apps` ponownie.

> [!NOTE] 
> Aby uzyskać więcej informacji na temat organizacji i przestrzeni oraz sposobu ich Cloud Foundry kontroli dostępu opartej na rolach (Cloud Foundry RBAC), zobacz dokumentację Cloud Foundry [.][cf-orgs-spaces-docs]

## <a name="deploy-an-application"></a>Wdrażanie aplikacji

Użyjmy przykładowej aplikacji Cloud Foundry o nazwie Hello Spring Cloud, która jest napisana w języku Java i oparta na platformie [Spring Framework](https://spring.io) [i Spring Boot](https://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Klonowanie repozytorium Hello Spring Cloud

Przykładowa Spring Cloud Hello jest dostępna w witrynie GitHub. Sklonuj go do swojego środowiska i zmień na nowy katalog:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Kompilowanie aplikacji

Skompilowanie aplikacji przy użyciu [narzędzia Apache Maven.](https://maven.apache.org)

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Wdrażanie aplikacji za pomocą wypychania cf

Większość aplikacji można wdrożyć w Cloud Foundry za pomocą `push` polecenia :

```bash
cf push
```

Podczas *wypychania* aplikacji program Cloud Foundry typ aplikacji (w tym przypadku aplikację Java) i identyfikuje jej zależności (w tym przypadku platformę Spring). Następnie pakuje wszystko wymagane do uruchomienia kodu w autonomicznym obrazie kontenera, znanym jako *droplet*. Na koniec Cloud Foundry aplikację na jednej z maszyn dostępnych w środowisku i tworzy adres URL, pod którym można uzyskać dostęp, który jest dostępny w danych wyjściowych polecenia.

![Dane wyjściowe polecenia wypychania cf][cf-push-output]

Aby wyświetlić aplikację hello-spring-cloud, otwórz podany adres URL w przeglądarce:

![Domyślny interfejs użytkownika dla funkcji Hello Spring Cloud][hello-spring-cloud-basic]

> [!NOTE] 
> Aby dowiedzieć się więcej na temat tego, co się dzieje podczas tego procesu, zobacz `cf push` How Applications Are [Staged (Jak][cf-push-docs] są projektowane aplikacje) w Cloud Foundry dokumentacji.

## <a name="view-application-logs"></a>Wyświetlanie dzienników aplikacji

Możesz użyć interfejsu wiersza Cloud Foundry, aby wyświetlić dzienniki aplikacji według jej nazwy:

```bash
cf logs hello-spring-cloud
```

Domyślnie polecenie logs używa tail *,* który pokazuje nowe dzienniki, gdy są zapisywane. Aby wyświetlić nowe dzienniki, odśwież aplikację hello-spring-cloud w przeglądarce.

Aby wyświetlić dzienniki, które zostały już zapisane, dodaj `recent` przełącznik:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Skalowanie aplikacji

Domyślnie program `cf push` tworzy tylko jedno wystąpienie aplikacji. Aby zapewnić wysoką dostępność i umożliwić skalowanie w poziomie w celu zapewnienia większej przepływności, zwykle chcesz uruchamiać więcej niż jedno wystąpienie aplikacji. Możesz łatwo skalować w zewnątrz już wdrożone aplikacje za pomocą `scale` polecenia :

```bash
cf scale -i 2 hello-spring-cloud
```

Uruchomienie `cf app` polecenia w aplikacji pokazuje, Cloud Foundry tworzy kolejne wystąpienie aplikacji. Po zakończeniu pracy aplikacji Cloud Foundry automatycznie rozpoczyna się równoważenie obciążenia ruchu do tej aplikacji.


## <a name="next-steps"></a>Następne kroki

- [Przeczytaj dokumentację Cloud Foundry dokumentacji][cloudfoundry-docs]
- [Konfigurowanie wtyczki Azure DevOps Services dla Cloud Foundry][vsts-plugin]
- [Konfigurowanie dokańczania usługi Microsoft Log Analytics dla Cloud Foundry][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/ops-manager/2-10/install/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: ../cloud-shell/overview.md
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: https://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png
