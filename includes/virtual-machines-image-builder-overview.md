---
author: cynthn
ms.author: cynthn
ms.date: 01/23/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 658910dc4291375c7b2ab22e88c599b970b885af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80419113"
---
Standardowe obrazy maszyn wirtualnych (VM) umożliwiają organizacjom Migrowanie do chmury i zapewnianie spójności we wdrożeniach. Obrazy zwykle obejmują wstępnie zdefiniowane ustawienia zabezpieczeń i konfiguracji oraz niezbędne oprogramowanie. Skonfigurowanie własnego potoku obrazu wymaga czasu, infrastruktury i konfiguracji, ale za pomocą konstruktora obrazów maszyn wirtualnych platformy Azure wystarczy utworzyć prostą konfigurację opisującą obraz, przesłać ją do usługi, a obraz został skompilowany i rozdystrybuowany.
 
Konstruktor obrazów maszyn wirtualnych platformy Azure (Azure Image Builder) umożliwia rozpoczęcie pracy z obrazem platformy Azure Marketplace opartym na systemie Windows lub Linux, istniejącymi obrazami niestandardowymi lub Red Hat Enterprise Linux (RHEL) ISO i rozpoczęciem dodawania własnych dostosowań. Ponieważ Konstruktor obrazów jest oparty na programie [HashiCorp Packer](https://packer.io/), można także zaimportować istniejące skrypty aprowizacji powłoki programu Packer. Możesz również określić, gdzie mają być hostowane obrazy, w [galerii udostępnionych obrazów systemu Azure](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)jako obraz zarządzany lub dysk VHD.

> [!IMPORTANT]
> Usługa Azure Image Builder jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Funkcje w wersji zapoznawczej

W przypadku wersji zapoznawczej te funkcje są obsługiwane:

- Tworzenie złota obrazów bazowych, które obejmują minimalne zabezpieczenia i konfiguracje firmowe, oraz pozwalające wydziałom dostosować je do swoich potrzeb.
- Stosowanie poprawek istniejących obrazów, Konstruktor obrazów umożliwi ciągłe poprawianie istniejących obrazów niestandardowych.
- Połącz konstruktora obrazów z istniejącymi sieciami wirtualnymi, aby można było łączyć się z istniejącymi serwerami konfiguracji (DSC, Chef, Puppet itp.), udziałami plików lub innymi serwerami/usługami routingu.
- Integracja z galerią udostępnionych obrazów systemu Azure pozwala na globalne dystrybuowanie, wersję i skalowanie obrazów oraz zapewnia system zarządzania obrazami.
- Integracja z istniejącymi potokami kompilacji obrazu, po prostu Wywołaj konstruktora obrazu z potoku lub Użyj prostego zadania programu Image Builder dla systemu Azure DevOps.
- Migruj istniejący potok dostosowywania obrazu do platformy Azure. Aby dostosować obrazy, Użyj istniejących skryptów, poleceń i procesów.
- Tworzenie obrazów w formacie VHD.
 

## <a name="regions"></a>Regiony
Usługa Azure Image Builder będzie dostępna w wersji zapoznawczej w tych regionach. Obrazy można rozpowszechniać poza tymi regionami.
- Wschodnie stany USA
- Wschodnie stany USA 2
- Zachodnio-środkowe stany USA
- Zachodnie stany USA
- Zachodnie stany USA 2
- Europa Północna
- Europa Zachodnia

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

Pomoc techniczna dotycząca RHEL obrazów ISO jest przestarzała. Aby uzyskać więcej informacji, zapoznaj się z dokumentacją szablonu.

## <a name="how-it-works"></a>Jak to działa


![Koncepcyjne rysowanie programu Azure Image Builder](./media/virtual-machines-image-builder-overview/image-builder.png)

Konstruktor obrazów platformy Azure to w pełni zarządzana usługa platformy Azure, która jest dostępna dla dostawcy zasobów platformy Azure. Proces programu Azure Image Builder zawiera trzy główne części: Źródło, dostosowywanie i dystrybucję. są one reprezentowane w szablonie. Na poniższym diagramie przedstawiono składniki wraz z niektórymi właściwościami. 
 


**Proces konstruktora obrazów** 

![Koncepcyjne rysowanie procesu usługi Azure Image Builder](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Utwórz szablon obrazu jako plik JSON. Ten plik JSON zawiera informacje o źródle, dostosowywaniu i dystrybucji obrazu. Istnieje wiele przykładów w [repozytorium GitHub usługi Azure Image Builder](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Prześlij go do usługi, spowoduje to utworzenie artefaktu szablonu obrazu w określonej grupie zasobów. W tle program Image Builder pobierze obraz źródłowy lub plik ISO oraz skrypty zgodnie z wymaganiami. Są one przechowywane w oddzielnej grupie zasobów, która jest tworzona automatycznie w ramach subskrypcji, w formacie: IT_\<DestinationResourceGroup>_\<templateName>. 
1. Po utworzeniu szablonu obrazu można utworzyć obraz. W konstruktorze obrazu tła program używa szablonu i plików źródłowych do tworzenia maszyny wirtualnej (rozmiar domyślny: Standard_D1_v2), Sieć, publiczny adres IP, sieciowej grupy zabezpieczeń i magazyn w grupie zasobów\<IT_ DestinationResourceGroup\<>_ templateName>.
1. W ramach tworzenia obrazu Konstruktor obrazów dystrybuuje obraz zgodnie z szablonem, a następnie usuwa dodatkowe zasoby w IT_\<DestinationResourceGroup>_\<templateName> grupy zasobów, która została utworzona dla tego procesu.


## <a name="permissions"></a>Uprawnienia

Aby umożliwić programowi Azure VM Image Builder dystrybuowanie obrazów do obrazów zarządzanych lub do galerii obrazów udostępnionych, należy podać uprawnienia współautora dla usługi "Konstruktor obrazów maszyn wirtualnych platformy Azure" (Identyfikator aplikacji: cf32a0cc-373c-47c9-9156-0db11f6a6dfc) w grupach zasobów. 

Jeśli używasz istniejącej niestandardowej obrazu zarządzanego lub wersji obrazu, Konstruktor obrazów platformy Azure będzie potrzebować co najmniej dostępu czytelnika do tych grup zasobów.

Dostęp można przypisywać przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

Dostęp można przypisywać przy użyciu programu PowerShell:

```azurePowerShell-interactive
New-AzRoleAssignment -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 -Scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName> -RoleDefinitionName Contributor
```


Jeśli nie można znaleźć konta usługi, może to oznaczać, że subskrypcja, do której dodawane jest przypisanie roli, nie została jeszcze zarejestrowana dla dostawcy zasobów.


## <a name="costs"></a>Koszty
Podczas tworzenia, kompilowania i przechowywania obrazów przy użyciu usługi Azure Image Builder naliczane są koszty obliczeń, sieci i magazynu. Koszty te są podobne do kosztów ponoszonych w ramach ręcznego tworzenia obrazów niestandardowych. W przypadku zasobów opłata zostanie naliczona zgodnie z stawką za platformę Azure. 

Podczas procesu tworzenia obrazu pliki są pobierane i przechowywane w grupie `IT_<DestinationResourceGroup>_<TemplateName>` zasobów, co spowoduje powstanie małych kosztów magazynowania. Jeśli nie chcesz ich zachować, Usuń **szablon obrazu** po skompilowaniu obrazu.
 
Konstruktor obrazów tworzy maszynę wirtualną przy użyciu D1v2 rozmiaru maszyny wirtualnej oraz magazynu i sieci potrzebnych dla maszyny wirtualnej. Te zasoby będą trwać na czas trwania procesu kompilacji i zostaną usunięte po zakończeniu tworzenia obrazu przez konstruktora obrazu. 
 
Program Azure Image Builder dystrybuuje obraz do wybranych regionów, co może spowodować naliczenie opłat za ruch wychodzący z sieci.
 
## <a name="next-steps"></a>Następne kroki 
 
Aby wypróbować Konstruktor obrazów platformy Azure, zapoznaj się z artykułami dotyczącymi tworzenia obrazów systemu [Linux](../articles/virtual-machines/linux/image-builder.md) lub [Windows](../articles/virtual-machines/windows/image-builder.md) .
 
 
