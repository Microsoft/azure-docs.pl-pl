---
title: Przegląd renderowania
description: Wprowadzenie do renderowania przy użyciu platformy Azure i przegląd możliwości renderowania Azure Batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: 9fac5d3efabc5d9f796c91d688f35e01aeefdca3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092766"
---
# <a name="rendering-using-azure"></a>Renderowanie przy użyciu platformy Azure

Renderowanie jest procesem tworzenia modeli 3D i konwertowania ich na obrazy 2D. Pliki sceny 3W są tworzone w aplikacjach, takich jak Autodesk 3ds Max, Autodesk Maya i Blender.  Renderowanie aplikacji, takich jak Autodesk Maya, Autodesk Arnold, chaos Group V-ray oraz cykle programu Blender, tworzy obrazy 2D.  Czasami pojedyncze obrazy są tworzone na podstawie plików sceny. Jednak często można modelować i renderować wiele obrazów, a następnie połączyć je w animacji.

Obciążenie renderowania jest intensywnie używane w przypadku efektów specjalnych (VFX) w branży multimedialnej i rozrywkowej. Renderowanie jest również używane w wielu innych sektorach, takich jak reklama, produkcja, handel detaliczny oraz przemysł naftowy.

Proces renderowania ma duże obciążenie; może być wiele ramek/obrazów do wyprodukowania, a każdy obraz może trwać wiele godzin.  Renderowanie jest dlatego idealnym obciążeniem przetwarzania wsadowego, które może wykorzystać platformę Azure i Azure Batch do uruchamiania wielu renderowanych równolegle.

## <a name="why-use-azure-for-rendering"></a>Dlaczego warto używać platformy Azure do renderowania?

Z wielu powodów renderowanie jest doskonale dopasowane do platformy Azure i Azure Batch:

* Zadania renderowania można podzielić na wiele kawałków, które mogą być uruchamiane równolegle przy użyciu wielu maszyn wirtualnych:
  * Animacje składają się z wielu ramek, a każda ramka może być renderowana równolegle.  Im więcej maszyn wirtualnych jest dostępnych do przetwarzania każdej ramki, tym szybsze są wszystkie ramki i animacje.
  * W przypadku niektórych programów do renderowania można podzielić pojedyncze klatki na wiele elementów, takich jak kafelki lub wycinki.  Każdy element może być renderowany oddzielnie, a następnie połączony z końcowym obrazem po zakończeniu wszystkich fragmentów.  Im więcej dostępnych maszyn wirtualnych, tym szybciej można renderować klatkę.
* Renderowanie projektów może wymagać dużej skali:
  * Pojedyncze ramki mogą być złożone i wymagać wielu godzin do renderowania, nawet w przypadku sprzętu wysokiej klasy. animacje mogą składać się z setek tysięcy ramek.  Duża ilość obliczeń jest wymagana do renderowania wysokiej jakości animacji w rozsądnym czasie.  W niektórych przypadkach ponad 100 000 rdzeni zostało użytych do równoczesnego renderowania tysięcy klatek.
* Renderowanie projektów odbywa się na podstawie projektu i wymaga różnych ilości obliczeń:
  * Przydzielaj pojemności obliczeniowej i magazynu w razie potrzeby, Skaluj je w górę lub w dół zgodnie z obciążeniem w trakcie projektu i usuń je po zakończeniu projektu.
  * Płatność za pojemność po przydzieleniu, ale nie płatność za nią, gdy nie ma żadnych obciążeń, takich jak między projektami.
  * Skorzystaj z serii ze względu na nieoczekiwane zmiany; Skalowanie wyższe, jeśli w projekcie istnieją nieoczekiwane zmiany, a te zmiany muszą zostać przetworzone zgodnie z ścisłym harmonogramem.
* Wybieraj spośród szerokiego wyboru sprzętu w zależności od aplikacji, obciążenia i przedziału czasu:
  * Istnieje szeroki wybór sprzętu dostępnego na platformie Azure, który można przydzielić i zarządzać nimi za pomocą usługi Batch.
  * W zależności od projektu wymagania mogą dotyczyć najlepszej ceny/wydajności lub najlepszej ogólnej wydajności.  Inne sceny i/lub renderowanie aplikacji będą mieć różne wymagania dotyczące pamięci.  Niektóre aplikacje renderowania mogą korzystać z procesorów GPU w celu uzyskania najlepszej wydajności lub niektórych funkcji. 
* Maszyny wirtualne o niskim priorytecie obniżają koszty:
  * Maszyny wirtualne o niskim priorytecie są dostępne dla dużego rabatu w porównaniu z regularnymi maszynami wirtualnymi na żądanie i są odpowiednie dla niektórych typów zadań.
  * Maszyny wirtualne o niskim priorytecie mogą być przydzielone przez Azure Batch przy użyciu usługi Batch, która zapewnia elastyczność, w jaki sposób są one używane do określania szerokiego zestawu wymagań.  Pule usługi Batch mogą składać się z maszyn wirtualnych zarówno dedykowanych, jak i o niskim priorytecie, dzięki czemu można w dowolnym momencie zmienić kombinację typów maszyn wirtualnych.

## <a name="options-for-rendering-on-azure"></a>Opcje renderowania na platformie Azure

Istnieją różne możliwości platformy Azure, które mogą być używane do renderowania obciążeń.  Jakie możliwości są używane w zależności od istniejącego środowiska i wymagań.

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>Istniejące lokalne środowisko renderowania korzystające z aplikacji do zarządzania renderowaniem

Najbardziej typowym przypadkiem jest istnienie istniejącej lokalnej farmy renderowania, która jest zarządzana przez aplikację do zarządzania renderowaniem, taką jak PipelineFX Qube, funkcja renderowania lub Thinkbox.  Wymaganie ma na celu zwiększenie pojemności lokalnej farmy renderowania przy użyciu maszyn wirtualnych platformy Azure.

Oprogramowanie do zarządzania renderowaniem ma wbudowaną pomoc techniczną platformy Azure lub udostępniamy wtyczki, które dodają pomoc techniczną platformy Azure. Aby uzyskać więcej informacji na temat obsługiwanych menedżerów renderowania i włączonej funkcjonalności, zobacz artykuł dotyczący [używania menedżerów renderowania](./batch-rendering-render-managers.md).

### <a name="custom-rendering-workflow"></a>Niestandardowy przepływ pracy renderowania

Wymaganie dotyczące maszyn wirtualnych umożliwia rozbudowa istniejącej farmy renderowania.  Pule Azure Batch mogą alokować dużą liczbę maszyn wirtualnych, zezwalać na używanie maszyn wirtualnych o niskim priorytecie i dynamicznie skalować je za pomocą pełnych maszyn wirtualnych, a także zapewniać Licencjonowanie za korzystanie z usługi dla popularnych aplikacji do renderowania.

### <a name="no-existing-render-farm"></a>Brak istniejącej farmy renderowania

Na stacjach roboczych klienta mogą być wykonywane renderowanie, ale obciążenie renderowania zwiększa się i trwa zbyt długo, aby korzystać wyłącznie z pojemności stacji roboczej.  Azure Batch może być używany do przydzielania na żądanie obliczeń farmy renderowania oraz planowania zadań renderowania do farmy renderowania platformy Azure.

## <a name="azure-batch-rendering-capabilities"></a>Możliwości renderowania Azure Batch

Azure Batch umożliwia uruchamianie obciążeń równoległych na platformie Azure.  Umożliwia tworzenie dużej liczby maszyn wirtualnych, na których aplikacje są instalowane i uruchamiane, oraz zarządzanie nimi.  Zapewnia również kompleksowe funkcje planowania zadań do uruchamiania wystąpień tych aplikacji, co zapewnia przypisanie zadań do maszyn wirtualnych, kolejkowania, monitorowania aplikacji i tak dalej.

Azure Batch jest używany dla wielu obciążeń, ale dostępne są następujące funkcje, które ułatwiają i przyspieszają uruchamianie obciążeń renderowania.

* Obrazy maszyn wirtualnych ze wstępnie zainstalowanymi aplikacjami graficznymi i renderowania:
  * Dostępne są obrazy maszyn wirtualnych portalu Azure Marketplace, które zawierają popularne aplikacje grafiki i renderowania, unikając konieczności samodzielnego instalowania aplikacji lub tworzenia własnych obrazów niestandardowych przy użyciu zainstalowanych aplikacji. 
* Licencjonowanie za użycie na potrzeby aplikacji do renderowania:
  * Oprócz płacenia za maszyny wirtualne obliczeniowe można wybrać opcję zapłaty za aplikacje, co pozwala uniknąć konieczności kupowania licencji i potencjalnie konfigurowania serwera licencji dla aplikacji.  Płatność za użycie oznacza również, że można uwzględnić różne i nieoczekiwane obciążenie, ponieważ nie istnieje stała liczba licencji.
  * Istnieje również możliwość używania wstępnie zainstalowanych aplikacji z własnymi licencjami i używania licencjonowania płatnego za użycie. W tym celu zazwyczaj instalujesz serwer licencji lokalny lub oparty na platformie Azure i używasz sieci wirtualnej platformy Azure do łączenia puli renderowania z serwerem licencji.
* Wtyczki dla aplikacji do projektowania i modelowania klientów:
  * Dodatki plug-in umożliwiają użytkownikom końcowym korzystanie z Azure Batch bezpośrednio z aplikacji klienckiej, takich jak Autodesk Maya, dzięki czemu można tworzyć pule, przesyłać zadania i korzystać z większej pojemności obliczeniowej w celu szybszego renderowania.
* Integracja z programem Render Manager:
  * Azure Batch jest zintegrowany z aplikacjami do zarządzania renderowaniem lub wtyczki są dostępne w celu zapewnienia Azure Batch integracji.

Istnieje kilka sposobów używania Azure Batch, które mają zastosowanie również do Azure Batch renderowania.

* Interfejsy API:
  * Napisz kod przy użyciu [rest](/rest/api/batchservice), [.NET](/dotnet/api/overview/azure/batch), [Python](/python/api/overview/azure/batch), [Java](/java/api/overview/azure/batch)lub innych obsługiwanych interfejsów API.  Deweloperzy mogą integrować możliwości Azure Batch z istniejącymi aplikacjami lub przepływami pracy, zarówno w chmurze, jak i lokalnie.  Na przykład [wtyczka Autodesk Maya](https://github.com/Azure/azure-batch-maya) wykorzystuje interfejs API usługi Batch Python do wywołania usługi Batch, tworzenia pul i zarządzania nimi, przesyłania zadań i zadań oraz monitorowania stanu.
* Narzędzia wiersza polecenia:
  * [Wiersz polecenia platformy Azure](/cli/azure/) lub [Azure PowerShell](/powershell/azure/) może służyć do tworzenia skryptów w usłudze Batch.
  * W szczególności obsługa szablonu interfejsu wiersza polecenia w usłudze Batch ułatwia tworzenie pul i przesyłanie zadań.
* Interfejsów użytkownika
  * [Batch Explorer](https://github.com/Azure/BatchExplorer) to międzyplatformowe narzędzie klienta, które umożliwia również zarządzanie i monitorowanie kont wsadowych, ale zapewnia kilka bogatszych możliwości w porównaniu z interfejsem użytkownika Azure Portal.  Zestaw puli i szablonów zadań są udostępniane dla każdej obsługiwanej aplikacji i może służyć do łatwego tworzenia pul i przesyłania zadań.
  * Azure Portal może służyć do monitorowania Azure Batch i zarządzania nimi.
* Wtyczka aplikacji klienta:
  * Dostępne są wtyczki zezwalające na używanie renderowania wsadowego bezpośrednio w aplikacjach do projektowania i modelowania klientów. Wtyczki głównie wywołują aplikację Batch Explorer z informacjami kontekstowymi dotyczącymi bieżącego modelu 3W.
  * Dostępne są następujące wtyczki:
    * [Azure Batch Maya](https://github.com/Azure/azure-batch-maya)
    * [3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [Programem Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Wprowadzenie do renderowania Azure Batch

Aby wypróbować Azure Batch, zobacz następujące samouczki wstępne:

* [Używanie Batch Explorer do renderowania sceny programu Blender](./tutorial-rendering-batchexplorer-blender.md)
* [Użyj interfejsu wiersza polecenia Batch, aby renderować maksymalną scenę Autodesk 3ds](./tutorial-rendering-cli.md)

## <a name="next-steps"></a>Następne kroki

Określ listę aplikacji do renderowania i wersje zawarte w obrazach maszyn wirtualnych portalu Azure Marketplace w [tym artykule](./batch-rendering-applications.md).
