---
title: Dystrybucje linuksa zatwierdzone na platformie Azure
description: Dowiedz się więcej o systemie Linux w dystrybucjach zatwierdzonych przez platformę Azure, w tym wytycznych dotyczących Ubuntu, CentOS, Oracle i SUSE.
services: virtual-machines-linux
documentationcenter: ''
author: gbowerman
manager: gwallace
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/04/2019
ms.author: guybo
ms.openlocfilehash: ed7755251feb04a5f811d6ed96b00a347fba8994
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605922"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Zatwierdzone dystrybucje linuksa na platformie Azure
Partnerzy udostępniają obrazy systemu Linux w portalu Azure Marketplace. Współpracujemy z różnymi społecznościami Linuksa, aby dodać jeszcze więcej smaków do listy zatwierdzonych dystrybucji. W międzyczasie, dla dystrybucji, które nie są dostępne w Marketplace, zawsze można przynieść własny Linuks, postępują zgodnie z wytycznymi w [Tworzenie i przesłać wirtualny dysk twardy, który zawiera system operacyjny Linux](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

## <a name="supported-distributions-and-versions"></a>Obsługiwane dystrybucje i wersje
W poniższej tabeli wymieniono dystrybucje i wersje systemu Linux, które są obsługiwane na platformie Azure. Więcej szczegółowych informacji na temat obsługi systemu Linux i technologii open source na platformie Azure można znaleźć w witrynie Obsługa obrazów systemu Linux na [platformie Microsoft Azure.](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)

Sterowniki usług integracji systemu Linux (LIS) dla funkcji Hyper-V i Platformy Azure to moduły jądra, które firma Microsoft współtworzy bezpośrednio do jądra systemu Linux.  Niektóre sterowniki LIS są domyślnie wbudowane w jądro dystrybucji. Starsze dystrybucje oparte na red hat enterprise (RHEL)/CentOS są dostępne jako oddzielne pobieranie w [linux integration services w wersji 4.2 dla funkcji Hyper-V i platformy Azure.](https://www.microsoft.com/download/details.aspx?id=55106) Zobacz [wymagania jądra systemu Linux,](create-upload-generic.md#linux-kernel-requirements) aby uzyskać więcej informacji na temat sterowników LIS.

Agent systemu Azure Linux jest już wstępnie zainstalowany na obrazach portalu Azure Marketplace i jest zazwyczaj dostępny w repozytorium pakietów dystrybucji. Kod źródłowy można znaleźć na [GitHub](https://github.com/azure/walinuxagent).


| Dystrybucja | Wersja | Sterowniki | Agent |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3+, 7.0+, 8.0+ |CentOS 6.3: [LIS pobierz](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4+: W jądrze |Pakiet: W [repozytorium](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) pod "WALinuxAgent" <br/>Kod źródłowy: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |W jądrze |Kod źródłowy: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9+, 8.2+, 9, 10 |W jądrze |Pakiet: W repozytorium pod "waagent" <br/>Kod źródłowy: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |W jądrze |Pakiet: W repozytorium pod "WALinuxAgent" <br/>Kod źródłowy: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 7.1+, 8.0+ |W jądrze |Pakiet: W repozytorium pod "WALinuxAgent" <br/>Kod źródłowy: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES dla SAP<br>11 SP4<br>12 sp1+<br>15|W jądrze |Pakiet:<p> dla 11 w [chmurze:Narzędzia](https://build.opensuse.org/project/show/Cloud:Tools) repo<br>dla 12 zawarte w module "Chmura publiczna" w ramach "python-azure-agent"<br/>Kod źródłowy: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE Leap 42.2+ |W jądrze |Pakiet: W [chmurze:Repozytorium narzędzi](https://build.opensuse.org/project/show/Cloud:Tools) w obszarze "python-azure-agent" <br/>Kod źródłowy: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04+ ** <sup>1</sup>** |W jądrze |Pakiet: W repo pod "walinuxagent" <br/>Kod źródłowy: [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>** Informacje o rozszerzonej obsłudze Ubuntu 12.04 i 14.04 można znaleźć tutaj: [Ubuntu Extended Security Maintenance](https://www.ubuntu.com/esm).


## <a name="image-update-cadence"></a>Rytm aktualizacji obrazu
Platforma Azure wymaga, aby wydawcy zatwierdzonych dystrybucji systemu Linux regularnie aktualizował swoje obrazy w portalu Azure Marketplace za pomocą najnowszych poprawek i poprawek zabezpieczeń, co kwartał lub szybciej. Zaktualizowane obrazy w portalu Azure Marketplace są automatycznie dostępne dla klientów jako nowe wersje jednostki SKU obrazu. Więcej informacji o znajdowaniu obrazów systemu Linux: [Znajdowanie obrazów maszyn wirtualnych systemu Linux w portalu Azure Marketplace](https://docs.microsoft.com/azure/virtual-machines/linux/cli-ps-findimage).

### <a name="additional-links"></a>Dodatkowe linki
 - [Cykl życia obrazów w chmurze publicznej SUSE](https://www.suse.com/c/suse-public-cloud-image-life-cycle/)

## <a name="azure-tuned-kernels"></a>Jądra dostrojone na platformie Azure

Platforma Azure ściśle współpracuje z różnymi zatwierdzonymi dystrybucjami systemu Linux w celu optymalizacji obrazów opublikowanych w portalu Azure Marketplace. Jednym z aspektów tej współpracy jest rozwój "dostrojonych" jąder Linuksa, które są zoptymalizowane pod kątem platformy Azure i dostarczane jako w pełni obsługiwane składniki dystrybucji systemu Linux. Jądra azure-tuned zawierają nowe funkcje i ulepszenia wydajności i szybciej (zazwyczaj kwartalnie) rytm w porównaniu do domyślnych lub ogólnych jąder, które są dostępne w dystrybucji.

W większości przypadków te jądra są wstępnie zainstalowane na obrazach domyślnych w portalu Azure Marketplace, a więc klienci platformy Azure natychmiast uzyskają korzyści z tych zoptymalizowanych jąder. Więcej informacji na temat tych jąder dostrojonych do platformy Azure można znaleźć w następujących łączach:

 - CentOS Azure-Tuned Kernel - Dostępne za pośrednictwem Wirtualizacji CentOS SIG - [Więcej informacji](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
 - Debian Cloud Kernel - Dostępne z obrazem "backports" Debiana 10 i Debiana 9 na platformie Azure - [Więcej informacji](https://wiki.debian.org/Cloud/MicrosoftAzure)
 - SLES Azure-Tuned Kernel - [Więcej informacji](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
 - Ubuntu Azure-Tuned Kernel - [Więcej informacji](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)


## <a name="partners"></a>Partnerzy

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Ze strony internetowej CoreOS:

*CoreOS został zaprojektowany z myślą o bezpieczeństwie, spójności i niezawodności. Zamiast instalować pakiety za pośrednictwem yum lub apt, CoreOS używa kontenerów Linuksa do zarządzania usługami na wyższym poziomie abstrakcji. Kod pojedynczej usługi i wszystkie zależności są pakowane w kontenerze, który można uruchomić na jednym lub wielu komputerach CoreOS.*

### <a name="credativ"></a>Credativ ( Credativ )
[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ to niezależna firma konsultingowo-usługowa, która specjalizuje się w opracowywaniu i wdrażaniu profesjonalnych rozwiązań przy użyciu wolnego oprogramowania. Jako wiodący specjaliści open source, Credativ ma międzynarodowe uznanie z wielu działów IT, które korzystają z ich wsparcia. We współpracy z Microsoft, Credativ przygotowuje obecnie odpowiednie obrazy Debiana 8 (Jessie) i Debiana przed 7 (Wheezy). Oba obrazy są specjalnie zaprojektowane do pracy na platformie Azure i można nimi łatwo zarządzać za pośrednictwem platformy. Credativ będzie również wspierać długoterminową konserwację i aktualizację obrazów Debiana na platformie Azure za pośrednictwem centrów pomocy technicznej Open Source.

### <a name="oracle"></a>Oracle
[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Strategia Oracle polega na oferuje szeroką gamę rozwiązań dla chmur publicznych i prywatnych. Strategia daje klientom wybór i elastyczność w sposobie wdrażania oprogramowania Oracle w chmurach Oracle i innych chmurach. Współpraca Oracle z firmą Microsoft umożliwia klientom wdrażanie oprogramowania Oracle w chmurach publicznych i prywatnych firmy Microsoft z ufnością w certyfikację i wsparcie ze strony Oracle.  Zaangażowanie i inwestycje Oracle w rozwiązania Oracle w zakresie chmury publicznej i prywatnej pozostają niezmienione.

### <a name="red-hat"></a>Red Hat
[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Red Hat, wiodący na świecie dostawca rozwiązań open source, pomaga ponad 90% firm z listy Fortune 500 rozwiązywać wyzwania biznesowe, dostosowywać swoje strategie informatyczne i biznesowe oraz przygotowywać się na przyszłość technologii. Red Hat robi to, zapewniając bezpieczne rozwiązania za pośrednictwem otwartego modelu biznesowego i niedrogiego, przewidywalnego modelu subskrypcji.

### <a name="suse"></a>SUSE
[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server na platformie Azure to sprawdzona platforma, która zapewnia najwyższą niezawodność i bezpieczeństwo w chmurze obliczeniowej. Wszechstronna platforma Linux firmy SUSE bezproblemowo integruje się z usługami w chmurze platformy Azure, zapewniając łatwe w zarządzaniu środowisko chmury. Dzięki ponad 9200 certyfikowanym aplikacjom od ponad 1800 niezależnych dostawców oprogramowania dla suse Linux Enterprise Server, SUSE zapewnia, że obciążenia uruchomione w centrum danych mogą być bezpiecznie wdrażane na platformie Azure.

### <a name="canonical"></a>Canonical
[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Inżynieria kanoniczna i otwarte zarządzanie społecznością napędzają sukces Ubuntu w zakresie przetwarzania klienta, serwera i chmury obliczeniowej, który obejmuje osobiste usługi w chmurze dla konsumentów. Wizja Canonical ujednoliconej, bezpłatnej platformy w Ubuntu, od telefonu do chmury, zapewnia rodzinę spójnych interfejsów dla telefonu, tabletu, telewizora i pulpitu. Ta wizja sprawia, że Ubuntu jest pierwszym wyborem dla różnych instytucji, od dostawców chmury publicznej po twórców elektroniki użytkowej i jest ulubionym wśród poszczególnych technologów.

Dzięki deweloperom i centrom inżynieryjnym na całym świecie firma Canonical jest wyjątkowo przygotowana do współpracy z producentami sprzętu, dostawcami treści i programistami, aby wprowadzić rozwiązania Ubuntu na rynek dla komputerów, serwerów i urządzeń przenośnych.
