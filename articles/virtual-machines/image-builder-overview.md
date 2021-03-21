---
title: Dowiedz się więcej o usłudze Azure Image Builder (wersja zapoznawcza)
description: Dowiedz się więcej o usłudze Azure Image Builder dla maszyn wirtualnych na platformie Azure.
author: danielsollondon
ms.author: danis
ms.date: 03/05/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: references_regions
ms.reviewer: cynthn
ms.openlocfilehash: 20bb6925f859d497046eb42bbafb5264826b77b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604070"
---
# <a name="preview-azure-image-builder-overview"></a>Wersja zapoznawcza: Omówienie usługi Azure Image Builder

Standardowe obrazy maszyn wirtualnych (VM) umożliwiają organizacjom Migrowanie do chmury i zapewnianie spójności we wdrożeniach. Obrazy zwykle obejmują wstępnie zdefiniowane ustawienia zabezpieczeń i konfiguracji oraz niezbędne oprogramowanie. Skonfigurowanie własnego potoku obrazu wymaga czasu, infrastruktury i konfiguracji, ale z użyciem konstruktora obrazów maszyn wirtualnych platformy Azure, po prostu zapewnią konfigurację obrazu, przesłanie go do usługi, a obraz zostanie skompilowany i rozdystrybuowany.
 
Konstruktor obrazów maszyn wirtualnych platformy Azure (Azure Image Builder) umożliwia rozpoczęcie pracy z obrazem platformy Azure Marketplace opartym na systemie Windows lub Linux, istniejącymi obrazami niestandardowymi i rozpoczęciem dodawania własnych dostosowań. Ponieważ Konstruktor obrazów jest oparty na [HashiCorp Packer](https://packer.io/) , zobaczysz inne podobieństwa, ale masz korzyść usługi zarządzanej. Możesz również określić, gdzie mają być hostowane obrazy, w [galerii udostępnionych obrazów systemu Azure](shared-image-galleries.md)jako obraz zarządzany lub dysk VHD.

> [!IMPORTANT]
> Usługa Azure Image Builder jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Funkcje w wersji zapoznawczej

W przypadku wersji zapoznawczej te funkcje są obsługiwane:

- Tworzenie obrazów bazowych, które obejmują minimalne zabezpieczenia i konfiguracje firmowe, oraz pozwala na dalsze dostosowywanie działów.
- Integracja aplikacji podstawowych, aby maszyna wirtualna mogła korzystać z obciążeń po utworzeniu lub dodać konfiguracje do obsługi obrazów pulpitu wirtualnego systemu Windows.
- Stosowanie poprawek istniejących obrazów, Konstruktor obrazów umożliwi ciągłe poprawianie istniejących obrazów niestandardowych.
- Połącz konstruktora obrazów z istniejącymi sieciami wirtualnymi, aby można było łączyć się z istniejącymi serwerami konfiguracji (DSC, Chef, Puppet itp.), udziałami plików lub innymi serwerami/usługami routingu.
- Integracja z galerią udostępnionych obrazów systemu Azure pozwala na globalne dystrybuowanie, wersję i skalowanie obrazów oraz zapewnia system zarządzania obrazami.
- Integracja z istniejącymi potokami kompilacji obrazu, po prostu Wywołaj konstruktora obrazu z potoku lub Użyj prostego zadania programu Image Builder dla systemu Azure DevOps.
- Migruj istniejący potok dostosowywania obrazu do platformy Azure. Aby dostosować obrazy, Użyj istniejących skryptów, poleceń i procesów.
- Tworzenie obrazów w formacie VHD do obsługi Azure Stack.
 

## <a name="regions"></a>Regiony
Usługa Azure Image Builder będzie dostępna w wersji zapoznawczej w tych regionach. Obrazy można rozpowszechniać poza tymi regionami.
- East US
- Wschodnie stany USA 2
- Zachodnio-środkowe stany USA
- Zachodnie stany USA
- Zachodnie stany USA 2
- Europa Północna
- West Europe

## <a name="os-support"></a>Obsługa systemu operacyjnego
Program AIB obsługuje obrazy podstawowej systemu operacyjnego Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7,6, 7,7
- CentOS 7,6, 7,7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise — wiele sesji/Professional
- System Windows 2016
- System Windows 2019

## <a name="how-it-works"></a>Jak to działa

Konstruktor obrazów maszyn wirtualnych platformy Azure to w pełni zarządzana usługa platformy Azure, która jest dostępna dla dostawcy zasobów platformy Azure. Podaj konfigurację usługi, która określa obraz źródłowy, dostosowanie do wykonania i miejsce, do którego ma zostać rozesłany nowy obraz, na poniższym diagramie przedstawiono przepływ pracy wysokiego poziomu:

![Rysunek koncepcyjny procesu narzędzia Azure Image Builder przedstawiający źródła (Windows/Linux), dostosowania (powłoka, program PowerShell, ponowne uruchomienie systemu Windows & Update, Dodawanie plików) i dystrybucję globalną za pomocą Galerii udostępnionych obrazów platformy Azure](./media/image-builder-overview/image-builder-flow.png)

Konfiguracje szablonów można przekazać przy użyciu programu PowerShell, AZ CLI, szablonów ARM i przy użyciu zadania DevOps konstruktora obrazów maszyn wirtualnych platformy Azure, po przesłaniu go do usługi utworzymy zasób szablonu obrazu. Po utworzeniu zasobu szablon obrazu zobaczysz tymczasową grupę zasobów utworzoną w ramach subskrypcji, w formacie: IT_ \<DestinationResourceGroup> _\<TemplateName>_ \( GUID). Tymczasowa Grupa zasobów zawiera pliki i skrypty, do których odwołuje się plik, powłoka, dostosowanie programu PowerShell we właściwości ScriptURI.

Aby uruchomić kompilację, zostanie ona wywołana `Run` dla zasobu szablon obrazu, a następnie zostanie wdrożona dodatkowa zasobów dla kompilacji, na przykład maszyna wirtualna, Sieć, dysk, karta sieciowa itd. Jeśli tworzysz obraz bez używania istniejącego konstruktora obrazów sieci wirtualnej, zostanie również wdrożony publiczny adres IP i sieciowej grupy zabezpieczeń, usługa nawiąże połączenie z maszyną wirtualną kompilacji przy użyciu protokołu SSH lub WinRM. W przypadku wybrania istniejącej sieci wirtualnej usługa zostanie wdrożona przy użyciu linku prywatnego platformy Azure, a publiczny adres IP nie jest wymagany, aby uzyskać więcej szczegółowych informacji na temat sieci konstruktora obrazów. Przejrzyj [szczegóły](./linux/image-builder-networking.md).

Gdy kompilacja zakończy się, wszystkie zasoby zostaną usunięte, z wyjątkiem grupy zasobów tymczasowych i konta magazynu, aby je usunąć, zostanie usunięty zasób szablonu obrazu lub można pozostawić je w tym miejscu, aby ponownie uruchomić kompilację.

W tej dokumentacji znajduje się wiele przykładów i przewodników krok po kroku, które odwołują się do szablonów i rozwiązań konfiguracyjnych w [repozytorium GitHub usługi Azure Image Builder](https://github.com/azure/azvmimagebuilder).

### <a name="move-support"></a>Obsługa przenoszenia
Zasób szablonu obrazu jest niezmienny i zawiera linki do zasobów i grupy zasobów tymczasowych, w związku z czym typ zasobu nie obsługuje przenoszenia. Jeśli chcesz przenieść zasób szablonu obrazu, upewnij się, że masz kopię szablonu konfiguracji (Wyodrębnij istniejącą konfigurację z zasobu, jeśli go nie masz), Utwórz nowy zasób szablonu obrazu w nowej grupie zasobów o nowej nazwie i usuń poprzedni zasób szablonu obrazu. 

## <a name="permissions"></a>Uprawnienia
Gdy zarejestrujesz się w usłudze (AIB), spowoduje to przyznanie usługi AIB uprawnienia do tworzenia i usuwania tymczasowej grupy zasobów (IT_ *) oraz do dodawania do niej zasobów, które są wymagane dla kompilacji obrazu. Jest to realizowane przez główną nazwę usługi AIB (SPN), która jest dostępna w Twojej subskrypcji podczas pomyślnej rejestracji.

Aby umożliwić programowi Azure VM Image Builder dystrybuowanie obrazów do obrazów zarządzanych lub do galerii obrazów udostępnionych, należy utworzyć tożsamość przypisaną przez użytkownika platformy Azure, która ma uprawnienia do odczytu i zapisu obrazów. W przypadku uzyskiwania dostępu do usługi Azure Storage wymagane są uprawnienia do odczytu kontenerów prywatnych i publicznych.

Uprawnienia są wyjaśnione bardziej szczegółowo dla [programu PowerShell](./linux/image-builder-permissions-powershell.md)i [AZ CLI](./linux/image-builder-permissions-cli.md).

## <a name="costs"></a>Koszty
Podczas tworzenia, kompilowania i przechowywania obrazów przy użyciu usługi Azure Image Builder naliczane są koszty obliczeń, sieci i magazynu. Koszty te są podobne do kosztów ponoszonych w ramach ręcznego tworzenia obrazów niestandardowych. W przypadku zasobów opłata zostanie naliczona zgodnie z stawką za platformę Azure. 

Podczas procesu tworzenia obrazu pliki są pobierane i przechowywane w `IT_<DestinationResourceGroup>_<TemplateName>` grupie zasobów, co spowoduje powstanie małych kosztów magazynowania. Jeśli nie chcesz ich zachować, Usuń **szablon obrazu** po skompilowaniu obrazu.
 
Konstruktor obrazów tworzy maszynę wirtualną przy użyciu D1v2 rozmiaru maszyny wirtualnej oraz magazynu i sieci potrzebnych dla maszyny wirtualnej. Te zasoby będą trwać na czas trwania procesu kompilacji i zostaną usunięte po zakończeniu tworzenia obrazu przez konstruktora obrazu. 
 
Program Azure Image Builder dystrybuuje obraz do wybranych regionów, co może spowodować naliczenie opłat za ruch wychodzący z sieci.

## <a name="hyper-v-generation"></a>Generowanie funkcji Hyper-V
Konstruktor obrazów obecnie obsługuje tylko natywnie tworzenie obrazów generacji funkcji Hyper-V (Gen1) 1 w galerii obrazów udostępnionych (SIG) systemu Azure lub w obrazie zarządzanym. 
 
## <a name="next-steps"></a>Następne kroki 
 
Aby wypróbować Konstruktor obrazów platformy Azure, zapoznaj się z artykułami dotyczącymi tworzenia obrazów systemu [Linux](./linux/image-builder.md) lub [Windows](./windows/image-builder.md) .