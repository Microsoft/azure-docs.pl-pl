---
author: cynthn
ms.author: cynthn
ms.date: 08/03/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: daberry
ms.openlocfilehash: a5a201a9f993db2be00645d8d60a11c5be9cdbe0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89304034"
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

Obsługa obrazów ISO RHEL nie jest już obsługiwana.

## <a name="how-it-works"></a>Jak to działa


![Koncepcyjne rysowanie programu Azure Image Builder](./media/virtual-machines-image-builder-overview/image-builder.png)

Konstruktor obrazów platformy Azure to w pełni zarządzana usługa platformy Azure, która jest dostępna dla dostawcy zasobów platformy Azure. Proces programu Azure Image Builder zawiera trzy główne części: Źródło, dostosowywanie i dystrybucję. są one reprezentowane w szablonie. Na poniższym diagramie przedstawiono składniki wraz z niektórymi właściwościami. 
 


**Proces konstruktora obrazów** 

![Koncepcyjne rysowanie procesu usługi Azure Image Builder](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Utwórz szablon obrazu jako plik JSON. Ten plik JSON zawiera informacje o źródle, dostosowywaniu i dystrybucji obrazu. Istnieje wiele przykładów w [repozytorium GitHub usługi Azure Image Builder](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Prześlij go do usługi, spowoduje to utworzenie artefaktu szablonu obrazu w określonej grupie zasobów. W tle program Image Builder pobierze obraz źródłowy lub plik ISO oraz skrypty zgodnie z wymaganiami. Są one przechowywane w oddzielnej grupie zasobów, która jest tworzona automatycznie w ramach subskrypcji, w formacie: IT_ \<DestinationResourceGroup> _ \<TemplateName> . 
1. Po utworzeniu szablonu obrazu można utworzyć obraz. W kompilatorze obrazów w tle program używa szablonu i plików źródłowych do tworzenia maszyny wirtualnej (rozmiar domyślny: Standard_D1_v2), Sieć, publiczny adres IP, sieciowej grupy zabezpieczeń i magazyn w \<DestinationResourceGroup> \<TemplateName> grupie zasobów IT_ _.
1. W ramach tworzenia obrazu Konstruktor obrazów dystrybuuje obraz zgodnie z szablonem, a następnie usuwa dodatkowe zasoby w IT_ \<DestinationResourceGroup> _ \<TemplateName> grupy zasobów, która została utworzona dla tego procesu.


## <a name="permissions"></a>Uprawnienia
Gdy zarejestrujesz się w usłudze (AIB), spowoduje to przyznanie usługi AIB uprawnienia do tworzenia i usuwania tymczasowej grupy zasobów (IT_ *) oraz do dodawania do niej zasobów, które są wymagane dla kompilacji obrazu. Jest to realizowane przez główną nazwę usługi AIB (SPN), która jest dostępna w Twojej subskrypcji podczas pomyślnej rejestracji.

Aby umożliwić programowi Azure VM Image Builder dystrybuowanie obrazów do obrazów zarządzanych lub do galerii obrazów udostępnionych, należy utworzyć tożsamość przypisaną przez użytkownika platformy Azure, która ma uprawnienia do odczytu i zapisu obrazów. W przypadku uzyskiwania dostępu do usługi Azure Storage wymagane są uprawnienia do odczytu prywatnych kontenerów.

Początkowo należy wykonać czynności opisane w dokumentacji [zarządzanej tożsamości przypisanej przez użytkownika platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli) na temat tworzenia tożsamości.

Jeśli masz tożsamość, którą chcesz udzielić uprawnień IT, aby to zrobić, możesz użyć niestandardowej definicji roli platformy Azure, a następnie przypisać tożsamość zarządzaną przypisaną przez użytkownika do korzystania z definicji roli niestandardowej.

Szczegółowo wyjaśniono [uprawnienia, a](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibPermissions.md#azure-vm-image-builder-permissions-explained-and-requirements)przykłady pokazują, jak to jest zaimplementowane.

> [!Note]
> Wcześniej z AIB można użyć nazwy SPN AIB i przyznać uprawnienia SPN do grup zasobów obrazu. Nie jest ona przenoszona z tego modelu, aby umożliwić korzystanie z przyszłych funkcji. Z 26 maja 2020, Konstruktor obrazów nie akceptuje szablonów, które nie mają tożsamości przypisanej do użytkownika, istniejące szablony muszą zostać przesłane ponownie do usługi przy użyciu [tożsamości użytkownika](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#identity). W przykładach w tym przykładzie pokazano, jak utworzyć tożsamość przypisaną przez użytkownika i dodać ją do szablonu. Więcej informacji można znaleźć w tej [dokumentacji](https://github.com/danielsollondon/azvmimagebuilder#service-updates-and-latest-release-information) dotyczącej tej zmiany i wydań aktualizacji.

## <a name="costs"></a>Koszty
Podczas tworzenia, kompilowania i przechowywania obrazów przy użyciu usługi Azure Image Builder naliczane są koszty obliczeń, sieci i magazynu. Koszty te są podobne do kosztów ponoszonych w ramach ręcznego tworzenia obrazów niestandardowych. W przypadku zasobów opłata zostanie naliczona zgodnie z stawką za platformę Azure. 

Podczas procesu tworzenia obrazu pliki są pobierane i przechowywane w `IT_<DestinationResourceGroup>_<TemplateName>` grupie zasobów, co spowoduje powstanie małych kosztów magazynowania. Jeśli nie chcesz ich zachować, Usuń **szablon obrazu** po skompilowaniu obrazu.
 
Konstruktor obrazów tworzy maszynę wirtualną przy użyciu D1v2 rozmiaru maszyny wirtualnej oraz magazynu i sieci potrzebnych dla maszyny wirtualnej. Te zasoby będą trwać na czas trwania procesu kompilacji i zostaną usunięte po zakończeniu tworzenia obrazu przez konstruktora obrazu. 
 
Program Azure Image Builder dystrybuuje obraz do wybranych regionów, co może spowodować naliczenie opłat za ruch wychodzący z sieci.

## <a name="hyper-v-generation"></a>Generowanie funkcji Hyper-V
Konstruktor obrazów obecnie obsługuje tylko natywnie tworzenie obrazów generacji funkcji Hyper-V (Gen1) 1 w galerii obrazów udostępnionych (SIG) systemu Azure lub w obrazie zarządzanym. Jeśli chcesz utworzyć obrazy Gen2, musisz użyć źródłowego obrazu Gen2 i rozesłać do dysku VHD. Następnie należy utworzyć obraz zarządzany na podstawie wirtualnego dysku twardego i wprowadzić go do SIG jako obraz Gen2.
 
## <a name="next-steps"></a>Następne kroki 
 
Aby wypróbować Konstruktor obrazów platformy Azure, zapoznaj się z artykułami dotyczącymi tworzenia obrazów systemu [Linux](../articles/virtual-machines/linux/image-builder.md) lub [Windows](../articles/virtual-machines/windows/image-builder.md) .
 
