---
title: Utwórz kontroler danych usługi Azure Arc w Azure Portal
description: Utwórz kontroler danych usługi Azure Arc w Azure Portal
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 9c928040aa2ff5a6ebfb7102c03450d3d7297b59
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101686482"
---
# <a name="create-an-azure-arc-data-controller-in-the-azure-portal"></a>Utwórz kontroler danych usługi Azure Arc w Azure Portal

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="introduction"></a>Wprowadzenie

Za pomocą Azure Portal można utworzyć kontroler danych usługi Azure Arc.

Wiele środowisk tworzenia usługi Azure Arc rozpoczyna się w Azure Portal, nawet jeśli zasób, który ma zostać utworzony lub zarządzany, znajduje się poza infrastrukturą platformy Azure. Wzorzec środowiska użytkownika w takich przypadkach, szczególnie w przypadku braku bezpośredniej łączności między platformą Azure i środowiskiem, to użycie Azure Portal do wygenerowania skryptu, który można następnie pobrać i wykonać w środowisku w celu nawiązania bezpiecznego połączenia z powrotem z platformą Azure. Na przykład serwery z obsługą usługi Azure Arc są zgodne z tym wzorcem w celu [utworzenia serwerów z włączonym łukiem](../servers/onboard-portal.md).

Na razie, ponieważ wersja zapoznawcza obsługuje tylko pośredni tryb połączony usług danych z obsługą usługi Azure ARC, można użyć Azure Portal do wygenerowania notesu, który można następnie pobrać i uruchomić w Azure Data Studio względem klastra Kubernetes. W przyszłości, gdy będzie dostępny tryb bezpośrednio połączony, można zainicjować obsługę administracyjną kontrolera danych bezpośrednio z Azure Portal. Więcej informacji na temat [trybów łączności](connectivity.md)można znaleźć w artykule.

## <a name="use-the-azure-portal-to-create-an-azure-arc-data-controller"></a>Użyj Azure Portal, aby utworzyć kontroler danych usługi Azure Arc

Wykonaj poniższe kroki, aby utworzyć kontroler danych usługi Azure ARC przy użyciu Azure Portal i Azure Data Studio.

1. Najpierw Zaloguj się do [portalu Azure Portal Marketplace](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/azure%20arc%20data%20controller).  Wyniki wyszukiwania w witrynie Marketplace zostaną przefiltrowane w celu wyświetlenia "kontrolera danych Azure ARC".
2. Jeśli pierwszy krok nie wprowadził kryteriów wyszukiwania. Wprowadź wartość w polu Wyniki wyszukiwania, a następnie kliknij pozycję "Azure Arc Data Controller".
3. Wybierz kafelek kontroler danych platformy Azure z witryny Marketplace.
4. Kliknij przycisk **Utwórz** .
5. Zapoznaj się z wymaganiami dotyczącymi tworzenia kontrolera danych usługi Azure Arc i zainstalowania dowolnego brakującego wstępnie wymaganego oprogramowania, takiego jak Azure Data Studio i polecenia kubectl.
6. Kliknij przycisk **szczegóły kontrolera danych** .
7. Wybierz subskrypcję, grupę zasobów i lokalizację platformy Azure, tak jak w przypadku każdego innego zasobu, który ma zostać utworzony w Azure Portal. W takim przypadku wybrana lokalizacja platformy Azure będzie zawierać metadane dotyczące zasobu, które będą przechowywane.  Sam zasób zostanie utworzony w dowolnej wybranej infrastrukturze. Nie musi ona znajdować się w infrastrukturze platformy Azure.
8. Wprowadź nazwę kontrolera danych.
9. Wybierz tryb łączności dla kontrolera danych. Dowiedz się więcej o [trybach i wymaganiach związanych z łącznością](./connectivity.md). 

   > [!NOTE] 
   > W przypadku wybrania trybu łączności **bezpośredniej** upewnij się, że poświadczenia jednostki usługi są ustawiane za pomocą zmiennych środowiskowych, zgodnie z opisem w temacie [Tworzenie nazwy głównej usługi](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal). 

1. Wybierz profil konfiguracji wdrożenia.
1. Kliknij przycisk **Otwórz w programie Azure Studio** .
1. Na następnym ekranie zobaczysz podsumowanie wybranych opcji i wygenerowany Notes.  Możesz kliknąć przycisk **Pobierz Notes aprowizacji** , aby pobrać Notes.

   > [!IMPORTANT]
   > Na platformie Azure Red Hat OpenShift lub Red Hat OpenShift kontenera należy zastosować ograniczenie kontekstu zabezpieczeń przed utworzeniem kontrolera danych. Postępuj zgodnie z instrukcjami w obszarze [Zastosuj ograniczenie kontekstu zabezpieczeń dla usług danych z obsługą usługi Azure Arc w systemie OpenShift](how-to-apply-security-context-constraint.md).

1. Otwórz Notes w Azure Data Studio i kliknij przycisk **Uruchom wszystko** w górnej części ekranu.
1. Postępuj zgodnie z monitami i instrukcjami w notesie, aby ukończyć tworzenie kontrolera danych.

## <a name="monitoring-the-creation-status"></a>Monitorowanie stanu tworzenia

Tworzenie kontrolera potrwa kilka minut. Postęp można monitorować w innym oknie terminalu przy użyciu następujących poleceń:

> [!NOTE]
>  W przykładowych poleceniach przyjęto założenie, że utworzono kontroler danych i przestrzeń nazw Kubernetes o nazwie "ARC".  Jeśli użyto innej nazwy obszaru nazw/kontrolera danych, można zastąpić "ARC" nazwą.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Możesz również sprawdzić stan tworzenia dowolnego określonego obszaru pod, uruchamiając polecenie podobne do poniższego.  Jest to szczególnie przydatne w przypadku rozwiązywania problemów.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Rozwiązywanie problemów z tworzeniem

W przypadku wystąpienia problemów z tworzeniem należy zapoznać się z [przewodnikiem rozwiązywania problemów](troubleshoot-guide.md).