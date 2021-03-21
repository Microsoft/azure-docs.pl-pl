---
title: Pakowanie istniejącego pliku wykonywalnego na platformę Azure Service Fabric
description: Dowiedz się więcej o pakowaniu istniejącej aplikacji jako pliku wykonywalnego gościa, dzięki czemu można ją wdrożyć w klastrze Service Fabric.
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 8b808d092001196a4d2150e44d508e031db95554
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017750"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Wdróż istniejący plik wykonywalny w Service Fabric
Możesz uruchomić dowolny typ kodu, taki jak Node.js, Java lub C++ na platformie Azure Service Fabric jako usługa. Service Fabric odnosi się do tych typów usług jako plików wykonywalnych gościa.

Pliki wykonywalne gościa są traktowane Service Fabric jak usługi bezstanowe. W związku z tym są one umieszczane w węzłach w klastrze, na podstawie dostępności i innych metryk. W tym artykule opisano, jak spakować i wdrożyć plik wykonywalny gościa w klastrze Service Fabric przy użyciu programu Visual Studio lub narzędzia wiersza polecenia.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Zalety uruchamiania pliku wykonywalnego gościa w Service Fabric
Istnieje kilka zalet, aby uruchomić plik wykonywalny gościa w klastrze Service Fabric:

* Wysoka dostępność. Aplikacje działające w Service Fabric są wykonywane o wysokiej dostępności. Service Fabric zapewnia, że wystąpienia aplikacji są uruchomione.
* Monitorowanie kondycji. Service Fabric monitorowania kondycji wykrywa, czy aplikacja jest uruchomiona i zawiera informacje diagnostyczne w przypadku wystąpienia błędu.   
* Zarządzanie cyklem życia aplikacji. Oprócz udostępniania uaktualnień bez przestojów Service Fabric zapewnia automatyczne wycofanie do poprzedniej wersji, jeśli podczas uaktualniania zgłoszono złe zdarzenie kondycji.    
* Gęstooci. W klastrze można uruchamiać wiele aplikacji, co eliminuje konieczność uruchamiania poszczególnych aplikacji na własnym sprzęcie.
* Wykrywalność: przy użyciu REST można wywołać usługę nazewnictwa Service Fabric, aby znaleźć inne usługi w klastrze. 

## <a name="samples"></a>Samples
* [Przykład dla pakowania i wdrażania pliku wykonywalnego gościa](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Przykład dwóch plików wykonywalnych gościa (C# i NodeJS) komunikujących się za pośrednictwem usługi nazewnictwa przy użyciu REST](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Omówienie plików manifestu aplikacji i usługi
W ramach wdrażania pliku wykonywalnego gościa warto zrozumieć Service Fabric pakowanie i model wdrażania zgodnie z opisem w artykule [model aplikacji](service-fabric-application-model.md). Model pakietów Service Fabric opiera się na dwóch plikach XML: w manifestach aplikacji i usług. Definicja schematu dla plików ApplicationManifest.xml i ServiceManifest.xml jest instalowana z zestawem SDK Service Fabric w *folderze C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Manifest aplikacji** Manifest aplikacji jest używany do opisywania aplikacji. Zawiera on listę usług, które go tworzą, oraz inne parametry, które są używane do definiowania, jak należy wdrożyć co najmniej jedną usługę, na przykład liczbę wystąpień.

  W Service Fabric aplikacja jest jednostką wdrożenia i uaktualnienia. Aplikację można uaktualnić jako pojedynczą jednostkę, w której są zarządzane potencjalne błędy i potencjalne wycofywanie. Service Fabric gwarantuje, że proces uaktualniania zakończy się pomyślnie lub, Jeśli uaktualnienie nie powiedzie się, nie opuszcza aplikacji w stanie nieznany lub niestabilny.
* **Manifest usługi** Manifest usługi opisuje składniki usługi. Obejmuje to dane, takie jak nazwa i typ usługi oraz jej kod i konfiguracja. Manifest usługi zawiera również kilka dodatkowych parametrów, których można użyć do skonfigurowania usługi po jej wdrożeniu.

## <a name="application-package-file-structure"></a>Struktura pliku pakietu aplikacji
Aby wdrożyć aplikację do Service Fabric, aplikacja powinna być zgodna ze wstępnie zdefiniowaną strukturą katalogów. Poniżej przedstawiono przykład tej struktury.

```
|-- ApplicationPackageRoot
    |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```

ApplicationPackageRoot zawiera plik ApplicationManifest.xml, który definiuje aplikację. Podkatalog dla każdej usługi zawartej w aplikacji jest używany do przechowywania wszystkich artefaktów wymaganych przez usługę. Podkatalogi to ServiceManifest.xml i, zazwyczaj:

* *Kod*. Ten katalog zawiera kod usługi.
* *Konfiguracja*. Ten katalog zawiera plik Settings.xml (i inne pliki, w razie potrzeby), do którego usługa może uzyskać dostęp w czasie wykonywania w celu pobrania określonych ustawień konfiguracji.
* *Dane*. Jest to dodatkowy katalog do przechowywania dodatkowych danych lokalnych, których może potrzebować usługa. Dane powinny być używane do przechowywania tylko danych tymczasowych. Service Fabric nie kopiuje ani nie replikuje zmian do katalogu danych, jeśli usługa musi zostać przeniesiona (na przykład podczas pracy w trybie failover).

> [!NOTE]
> Nie trzeba tworzyć `config` katalogów i, `data` Jeśli nie są one potrzebne.
>
>

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami dotyczącymi pokrewnych informacji i zadań.
* [Wdrażanie aplikacji wykonywalnej gościa](service-fabric-deploy-existing-app.md)
* [Wdrażanie wielu aplikacji wykonywalnych gości](./service-fabric-deploy-existing-app.md)
* [Tworzenie pierwszej aplikacji wykonywalnej gościa przy użyciu programu Visual Studio](quickstart-guest-app.md)
* [Przykład dotyczący pakowania i wdrażania pliku wykonywalnego gościa](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), w tym link do wersji wstępnej narzędzia pakowanie
* [Przykład dwóch plików wykonywalnych gościa (C# i NodeJS) komunikujących się za pośrednictwem usługi nazewnictwa przy użyciu REST](https://github.com/Azure-Samples/service-fabric-containers)
