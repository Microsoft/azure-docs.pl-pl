---
title: Dystrybucje systemu Linux zatwierdzone na platformie Azure
description: Dowiedz się więcej o dystrybucji z systemem Linux na platformie Azure, w tym wskazówki dotyczące Ubuntu, CentOS, Oracle i SUSE.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: guybo
ms.openlocfilehash: 0236644930af699180cf26b4baee7bb591d8bbaa
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978938"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Zatwierdzone dystrybucje systemu Linux na platformie Azure

Partnerzy udostępniają obrazy systemu Linux w witrynie Azure Marketplace. Firma Microsoft współpracuje z różnymi społecznościami systemu Linux w celu dodania jeszcze większej liczby wersji do listy rozpowszechnianych informacji. W przypadku dystrybucji, które nie są dostępne w portalu Marketplace, zawsze możesz przełączyć swój własny system Linux, postępując zgodnie z wytycznymi w obszarze [Tworzenie i przekazywanie wirtualnego dysku twardego zawierającego system operacyjny Linux](./create-upload-generic.md).

## <a name="supported-distributions-and-versions"></a>Obsługiwane dystrybucje i wersje

W poniższej tabeli wymieniono dystrybucje systemu Linux i wersje, które są obsługiwane w systemie Azure. Aby uzyskać więcej informacji, zobacz [Pomoc techniczna dla obrazów systemu Linux w Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure).

Sterowniki systemu Linux Integration Services (LIS) dla funkcji Hyper-V i platformy Azure to moduły jądra, które firma Microsoft bezpośrednio współużytkuje z nadrzędnym jądrem systemu Linux. Niektóre sterowniki LIS są domyślnie wbudowane w jądro dystrybucji. Starsze dystrybucje oparte na systemie Red Hat Enterprise (RHEL)/CentOS są dostępne jako osobne pobieranie w systemie [Linux Integration Services w wersji 4,2 dla funkcji Hyper-V i platformy Azure](https://www.microsoft.com/download/details.aspx?id=55106). Aby uzyskać więcej informacji, zobacz [wymagania jądra systemu Linux](create-upload-generic.md#linux-kernel-requirements).

Agent systemu Azure Linux jest już wstępnie zainstalowany w obrazach portalu Azure Marketplace i jest zazwyczaj dostępny w repozytorium pakietu dystrybucji. Kod źródłowy można znaleźć w witrynie [GitHub](https://github.com/azure/walinuxagent).

| Dystrybucja | Wersja | Sterowniki | Agent |
| --- | --- | --- | --- |
| CentOS przez oprogramowanie nieautoryzowane Wave |CentOS 6. x, 7. x, 8. x |CentOS 6,3: [pobieranie lis](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4 +: w jądrze |Pakiet: w [repozytorium](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) w obszarze "WALinuxAgent" <br/>Kod źródłowy: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/)<p> CoreOS jest teraz [koniec okresu](https://coreos.com/os/eol/) ważności z 26 maja 2020. |Nie jest już dostępne | | |
| Debian według credativ |8.x, 9.x |W jądrze |Pakiet: w repozytorium w obszarze "waagent" <br/>Kod źródłowy: [GitHub](https://github.com/Azure/WALinuxAgent) |
|Flatcar kontener systemu Linux przez Kinvolk| Pro, stabilny, beta| W jądrze | wa-Linux — Agent jest już zainstalowany w/usr/share/OEM/bin/waagent |
| Oracle Linux przez firmę Oracle |6.x, 7.x, 8.x |W jądrze |Pakiet: w repozytorium w obszarze "WALinuxAgent" <br/>Kod źródłowy: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| [Red Hat Enterprise Linux przez Red Hat](../workloads/redhat/overview.md) |6.x, 7.x, 8.x |W jądrze |Pakiet: w repozytorium w obszarze "WALinuxAgent" <br/>Kod źródłowy: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise przez SUSE |SLES/SLES dla oprogramowania SAP 11. x, 12. x, 15. x <br/> [Cykl życia obrazu chmury publicznej SUSE](https://www.suse.com/c/suse-public-cloud-image-life-cycle/) |W jądrze |Package<p> 11 w [chmurze: repozytorium narzędzi](https://build.opensuse.org/project/show/Cloud:Tools)<br>w przypadku 12 uwzględnionych w module "chmura publiczna" w obszarze "Python-Azure-Agent"<br/>Kod źródłowy: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE przez SUSE |openSUSE Leap 15.x |W jądrze |Pakiet: w [chmurze: narzędzia](https://build.opensuse.org/project/show/Cloud:Tools) repozytorium w obszarze "Python-Azure-Agent" <br/>Kod źródłowy: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu przez kanoniczny |Serwer Ubuntu i pakiet Pro. 16. x, 18. x, 20. x<p>Informacje o rozszerzonej obsłudze Ubuntu 12,04 i 14,04 można znaleźć tutaj: [Ubuntu Extended Security Maintenance](https://www.ubuntu.com/esm). |W jądrze |Pakiet: w repozytorium w obszarze "walinuxagent" <br/>Kod źródłowy: [GitHub](https://github.com/Azure/WALinuxAgent) |

## <a name="image-update-cadence"></a>Erze aktualizacji obrazów

System Azure wymaga, aby wydawcy zatwierdzonych dystrybucji systemu Linux regularnie aktualizować swoje obrazy w portalu Azure Marketplace przy użyciu najnowszych poprawek i poprawek zabezpieczeń, co kwartał lub szybciej erze. Zaktualizowane obrazy w portalu Marketplace są automatycznie dostępne dla klientów jako nowe wersje jednostki SKU obrazu. Więcej informacji o sposobach znajdowania obrazów systemu Linux: [Znajdowanie obrazów maszyn wirtualnych z systemem Linux w portalu Azure Marketplace](./cli-ps-findimage.md).

## <a name="azure-tuned-kernels"></a>Jądra dostosowane do platformy Azure

Platforma Azure współpracuje ściśle z różnymi rozpowszechnianymi dystrybucjami systemu Linux w celu optymalizacji obrazów opublikowanych w portalu Azure Marketplace. Jednym z aspektów tej współpracy jest opracowywanie "dostrojonych" jądra systemu Linux, które są zoptymalizowane pod kątem platformy Azure i dostarczane jako w pełni obsługiwane składniki dystrybucji w systemie Linux. Azure-Tuned jądra zawierają nowe funkcje i ulepszenia wydajności oraz szybciej (zazwyczaj co kwartał) erze w porównaniu z domyślnymi lub ogólnymi jądrami, które są dostępne w ramach dystrybucji.

W większości przypadków te jądra są wstępnie zainstalowane na domyślnych obrazach w portalu Azure Marketplace, dzięki czemu klienci będą natychmiast mogli korzystać z tych zoptymalizowanych jądra. Więcej informacji na temat tych Azure-Tuned jądra można znaleźć w następujących linkach:

- [CentOS Azure-Tuned jądro dostępne za pośrednictwem SIG wirtualizacji CentOS](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
- [Debian jądro w chmurze — dostępne za pomocą obrazu Debian 10 i Debian 9 "na platformie Azure](https://wiki.debian.org/Cloud/MicrosoftAzure)
- [SLES Azure-Tuned jądro](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
- [Ubuntu Azure-Tuned jądro](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)
- [Kontener Flatcar Linux Pro](https://azuremarketplace.microsoft.com/marketplace/apps/kinvolk.flatcar_pro)

## <a name="partners"></a>Partnerzy

### <a name="coreos"></a>CoreOS

CoreOS zaplanowano [koniec okresu ważności](https://coreos.com/os/eol/) do 26 maja 2020.
Firma Microsoft ma dwa (2) kanały migracji dla użytkowników CoreOS.

- Flatcar przez Kinvolk (zobacz wpis "Flatcar Container Linux to Kinvolk").
- [Podstawowy system operacyjny Fedora](https://docs.fedoraproject.org/en-US/fedora-coreos/provisioning-azure/) (klienci muszą przekazać własny obraz. Oto [Dokumentacja dotycząca migracji](https://docs.fedoraproject.org/en-US/fedora-coreos/migrate-cl/)).

### <a name="credativ"></a>Credativ

[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ to niezależna firma konsultingowa i usług, która specjalizacje opracowywania i wdrażania profesjonalnych rozwiązań przy użyciu bezpłatnego oprogramowania. Jako wiodące specjaliści z branży Open Source credativ mają międzynarodowyą funkcję rozpoznawania wielu działów IT korzystających z pomocy technicznej. W połączeniu z firmą Microsoft credativ aktualnie przygotowuje odpowiednie obrazy Debian dla Debian 8 (Jessie) i Debian przed 7 (wheezy). Oba obrazy są specjalnie przeznaczone do uruchamiania na platformie Azure i mogą być łatwo zarządzane za pośrednictwem platformy. Credativ również będzie obsługiwał długoterminową konserwację i aktualizację obrazów Debian dla platformy Azure za pomocą Centrum pomocy technicznej typu open source.

### <a name="kinvolk"></a>Kinvolk
[https://www.kinvolk.io/flatcar-container-linux/](https://www.kinvolk.io/flatcar-container-linux/)

Kinvolk to firma znajdująca się w kontenerze Flatcar w systemie Linux, która kontynuuje pierwotną wizję CoreOS dla minimalnej, niezmiennej i autoaktualizacji podstaw dla aplikacji kontenerowych. Co najmniej dystrybucji, Flatcar zawiera tylko te pakiety, które są wymagane do wdrożenia kontenerów. Niezmienny system plików gwarantuje spójność i bezpieczeństwo, podczas gdy jego możliwości autoaktualizacji umożliwiają zawsze aktualną wersję z najnowszymi poprawkami zabezpieczeń. 

Kontener Flatcar w systemie Linux jest objęty globalnym zespołem systemu Linux i specjalistów ds. technologii kontenerów, którzy oferują opcjonalną komercyjną subskrypcję pomocy technicznej, która obejmuje 24x7 odpowiedzi, zabezpieczenia i alerty techniczne oraz wyłącznych obrazów zoptymalizowanych pod kątem platformy Azure, w tym kanału wsparcia długoterminowego.


### <a name="oracle"></a>Oracle

[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Strategia firmy Oracle polega na zaoferowaniu szerokiego portfolio rozwiązań dla chmur publicznych i prywatnych. Strategia ta umożliwia klientom wybór i elastyczność wdrażania oprogramowania Oracle w chmurach Oracle i innych chmurach. Partnerstwo firmy Oracle z firmą Microsoft umożliwia klientom wdrażanie oprogramowania Oracle w chmurach publicznych i prywatnych firmy Microsoft z wiarygodnością certyfikacji i pomocy technicznej firmy Oracle.  Zobowiązania firmy Oracle i inwestycje w rozwiązania chmury publicznej i prywatnej w systemie Oracle nie są zmieniane.

### <a name="red-hat"></a>Red Hat

[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Wiodący na świecie dostawca rozwiązań typu "open source", Red Hat pomaga więcej niż 90% firmy listy fortunee 500 rozwiązywać problemy biznesowe, wyrównać swoje strategie IT i strategię biznesową, a także przygotować się na przyszłość technologii. Firma Red Hat osiąga to, dostarczając bezpieczne rozwiązania za pomocą otwartego modelu biznesowego i przystępnego, przewidywalnego modelu subskrypcji.

### <a name="suse"></a>SUSE

[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server na platformie Azure to sprawdzona platforma, która zapewnia najwyższą niezawodność i bezpieczeństwo w chmurze obliczeniowej. Uniwersalna platforma Linux zapewnia bezproblemowe integrację z usługami w chmurze platformy Azure w celu zapewnienia łatwego zarządzania środowiskiem w chmurze. W przypadku ponad 9 200 certyfikowanych aplikacji z ponad 1 800 niezależnych dostawców oprogramowania dla SUSE Linux Enterprise Server SUSE zapewnia, że obciążenia działające w centrum danych mogą być bezpiecznie wdrożone na platformie Azure.

### <a name="canonical"></a>Canonical

[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Inżynieria kanoniczna i otwarta społeczność zarządzania Wspólnotą Ubuntu sukces w zakresie rozwiązań klienta, serwera i chmury, w tym osobistych usług w chmurze dla klientów. Wykanoniczna funkcja ujednoliconej, wolnej platformy w Ubuntu, od telefonu do chmury, zapewnia rodzinę spójnych interfejsów dla telefonów, tabletów, programów telewizyjnych i komputerów stacjonarnych. Ta wizja sprawia, że Ubuntu pierwszy wybór dla różnorodnych instytucji od dostawców chmury publicznej do osób tworzących odbiorców elektronicznych i ulubionych między indywidualnym technologists.

W przypadku deweloperów i centrów inżynieryjnych na całym świecie, kanoniczny jest jednoznacznie umiejscowiony do partnera z markami sprzętowymi, dostawcami zawartości i programistami oprogramowania, aby Ubuntu rozwiązania na rynek dla komputerów, serwerów i urządzeń podręcznych.