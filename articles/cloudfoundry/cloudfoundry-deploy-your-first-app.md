---
title: Wdrażanie pierwszej aplikacji w rozwiązaniu Cloud Foundry na platformie Microsoft Azure
description: Wdrażanie aplikacji do Cloud Foundry na platformie Azure
author: seanmck
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: c8afc4f15f421a7b60b95d09b954e7742c24c66e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95519680"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Wdrażanie pierwszej aplikacji w rozwiązaniu Cloud Foundry na platformie Microsoft Azure

[Cloud Foundry](https://cloudfoundry.org) to popularna platforma aplikacji typu "open source" dostępna w Microsoft Azure. W tym artykule pokazano, jak wdrożyć aplikację i zarządzać nią na Cloud Foundry w środowisku platformy Azure.

## <a name="create-a-cloud-foundry-environment"></a>Utwórz środowisko Cloud Foundry

Istnieje kilka opcji tworzenia środowiska Cloud Foundry na platformie Azure:

- Skorzystaj z [oferty Cloud Foundry Pivot][pcf-azuremarketplace] w witrynie Azure Marketplace, aby utworzyć standardowe środowisko obejmujące program PCF Ops Manager i Service Broker platformy Azure. Możesz znaleźć [kompletne instrukcje][pcf-azuremarketplace-pivotaldocs] dotyczące wdrażania oferty portalu Marketplace w dokumentacji przestawnej.
- Utwórz dostosowane środowisko przez [Ręczne wdrożenie Cloud Foundry Pivot][pcf-custom].
- [Wdróż pakiety Cloud Foundry typu "open source" bezpośrednio][oss-cf-bosh] przez skonfigurowanie programu [Bosh](https://bosh.io) Director, który koordynuje wdrożenie środowiska Cloud Foundry.

> [!IMPORTANT] 
> Jeśli wdrażasz PCF z poziomu portalu Azure Marketplace, zanotuj SYSTEMDOMAINURL i poświadczenia administratora wymagane do uzyskania dostępu do Menedżera aplikacji pivots, które zostały opisane w przewodniku wdrażania w portalu Marketplace. Są one konieczne do wykonania tego samouczka. W przypadku wdrożeń w witrynie Marketplace SYSTEMDOMAINURL jest w postaci `https://system.*ip-address*.cf.pcfazure.com` .

## <a name="connect-to-the-cloud-controller"></a>Nawiązywanie połączenia z kontrolerem chmury

Kontroler chmury jest głównym punktem wejścia do środowiska Cloud Foundryego do wdrażania aplikacji i zarządzania nimi. Podstawowy interfejs API kontrolera chmury (CCAPI) jest interfejsem API REST, ale jest dostępny za pomocą różnych narzędzi. W takim przypadku współdziała z nim za pomocą [interfejsu wiersza polecenia Cloud Foundry][cf-cli]. Możesz zainstalować interfejs wiersza polecenia w systemie Linux, macOS lub Windows, ale jeśli wolisz nie instalować go w ogóle, jest on dostępny wstępnie na [Azure Cloud Shell][cloudshell-docs].

Aby się zalogować, poprzedź `api` do SYSTEMDOMAINURL uzyskanego w ramach wdrożenia w portalu Marketplace. Ponieważ domyślne wdrożenie używa certyfikatu z podpisem własnym, należy również uwzględnić `skip-ssl-validation` przełącznik.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Zostanie wyświetlony monit o zalogowanie się do kontrolera chmury. Użyj poświadczeń konta administratora pozyskanych w ramach kroków wdrożenia w portalu Marketplace.

Cloud Foundry udostępnia *organizacje* i *spacje* jako przestrzenie nazw do izolowania zespołów i środowisk w ramach wdrożenia udostępnionego. Wdrożenie w witrynie PCF Marketplace obejmuje domyślny *system* organizacji i zestaw spacji utworzonych w celu zawierania składników podstawowych, takich jak usługa skalowania automatycznego i Broker usług platformy Azure. Na razie wybierz miejsce *systemowe* .


## <a name="create-an-org-and-space"></a>Tworzenie organizacji i przestrzeni

Jeśli wpiszesz `cf apps` , zobaczysz zestaw aplikacji systemowych, które zostały wdrożone w przestrzeni systemowej w organizacji systemowej. 

Należy zachować zastrzeżoną organizacji *systemową* dla aplikacji systemowych, dlatego należy utworzyć organizacji i miejsce na potrzeby aplikacji przykładowej.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Użyj polecenia Target, aby przełączyć się do nowej organizacji i przestrzeni:

```bash
cf target -o testorg -s dev
```

Teraz, podczas wdrażania aplikacji, zostanie ona automatycznie utworzona w nowej organizacji i przestrzeni. Aby potwierdzić, że w nowej organizacji/przestrzeni nie ma obecnie żadnych aplikacji, wpisz `cf apps` ponownie.

> [!NOTE] 
> Aby uzyskać więcej informacji o organizacje i miejscach oraz sposobie ich użycia w Cloud Foundry kontroli dostępu opartej na rolach (Cloud Foundry RBAC), zapoznaj się z [dokumentacją Cloud Foundry][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Wdrażanie aplikacji

Użyjmy przykładowej aplikacji Cloud Foundry o nazwie Cloud wiosennej chmury, która jest zapisywana w języku Java i oparta na [strukturze sprężyny](https://spring.io) oraz [rozruchu sprężynowego](https://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Klonowanie repozytorium w chmurze usługi Hello wiosną

Przykładowa aplikacja usługi Hello wiosną w chmurze jest dostępna w serwisie GitHub. Sklonuj go w środowisku i przejdź do nowego katalogu:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Kompilowanie aplikacji

Kompiluj aplikację przy użyciu usługi [Apache Maven](https://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Wdrażanie aplikacji za pomocą polecenia CF push

Większość aplikacji można wdrożyć do Cloud Foundry przy użyciu `push` polecenia:

```bash
cf push
```

Podczas *wypychania* aplikacji Cloud Foundry wykrywa typ aplikacji (w tym przypadku aplikację Java) i identyfikuje jej zależności (w tym przypadku struktury sprężynowe). Następnie wszystkie elementy wymagane do uruchomienia kodu w autonomicznym obrazie kontenera, znane jako *droplet*. Na koniec Cloud Foundry planuje aplikację na jednej z dostępnych maszyn w środowisku i tworzy adres URL, w którym można się z nim skontaktować, który jest dostępny w danych wyjściowych polecenia.

![Dane wyjściowe polecenia CF push][cf-push-output]

Aby wyświetlić aplikację Hello-wiosenną w chmurze, Otwórz podany adres URL w przeglądarce:

![Domyślny interfejs użytkownika chmury wiosennej][hello-spring-cloud-basic]

> [!NOTE] 
> Aby dowiedzieć się więcej na temat tego, co się dzieje `cf push` , zobacz [temat jak aplikacje są przemieszczane][cf-push-docs] w dokumentacji Cloud Foundry.

## <a name="view-application-logs"></a>Wyświetlanie dzienników aplikacji

Możesz użyć interfejsu wiersza polecenia Cloud Foundry, aby wyświetlić dzienniki dla aplikacji według nazwy:

```bash
cf logs hello-spring-cloud
```

Domyślnie polecenie Logs używa *ogona*, który wyświetla nowe dzienniki podczas pisania. Aby wyświetlić nowe dzienniki, Odśwież aplikację Hello-wiosny w chmurze w przeglądarce.

Aby wyświetlić dzienniki, które zostały już zapisane, Dodaj `recent` przełącznik:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Skalowanie aplikacji

Domyślnie `cf push` tylko tworzy pojedyncze wystąpienie aplikacji. Aby zapewnić wysoką dostępność i włączyć skalowanie w poziomie w celu uzyskania większej przepływności, zazwyczaj chcesz uruchomić więcej niż jedno wystąpienie aplikacji. Można łatwo skalować już wdrożone aplikacje za pomocą `scale` polecenia:

```bash
cf scale -i 2 hello-spring-cloud
```

Uruchomienie `cf app` polecenia w aplikacji pokazuje, że Cloud Foundry tworzy inne wystąpienie aplikacji. Po uruchomieniu aplikacji Cloud Foundry automatycznie uruchamia Równoważenie obciążenia ruchem.


## <a name="next-steps"></a>Następne kroki

- [Przeczytaj dokumentację dotyczącą Cloud Foundry][cloudfoundry-docs]
- [Skonfiguruj wtyczkę Azure DevOps Services dla Cloud Foundry][vsts-plugin]
- [Skonfiguruj dyszę Microsoft Log Analytics dla Cloud Foundry][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
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