---
title: Jak działa Routing przy użyciu Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Zawiera opis procesów Azure Dev Spaces i sposobu działania routingu
keywords: Azure Dev Spaces, Spaces dev, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontenery
ms.openlocfilehash: 2d2c6f336222b4ae0907d6579289a8cad8d73aa6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91977973"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>Jak działa Routing przy użyciu Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces zapewnia wiele sposobów na szybkie Iterowanie i debugowanie aplikacji Kubernetes oraz współpracę z zespołem w klastrze usługi Azure Kubernetes Service (AKS). Gdy projekt zostanie uruchomiony w obszarze deweloperskim, Azure Dev Spaces zapewnia dodatkowe możliwości sieci i routingu dla projektu.

W tym artykule opisano sposób działania routingu z miejscami deweloperskimi.

## <a name="how-routing-works"></a>Jak działa Routing

Przestrzeń dev jest oparta na AKS i używa tych samych [koncepcji sieci](../aks/concepts-network.md). Azure Dev Spaces ma także scentralizowaną usługę *ingressmanager* i wdraża swój własny kontroler transferu danych w klastrze AKS. Usługa *ingressmanager* monitoruje klastry AKS z miejscami deweloperskimi i rozszerza Azure dev Spaces w klastrze za pomocą obiektów przychodzących do routingu do aplikacji. Kontener devspaces-proxy w każdym z nich dodaje `azds-route-as` nagłówek HTTP dla ruchu HTTP do obszaru dev na podstawie adresu URL. Na przykład żądanie do adresu URL *http://azureuser.s.default.serviceA.fedcba09...azds.io* może otrzymać nagłówek HTTP z `azds-route-as: azureuser` . Kontener devspaces-proxy nie zostanie dodany, `azds-route-as` Jeśli taki nagłówek już istnieje.

Gdy żądanie HTTP zostanie wysłane do usługi spoza klastra, żądanie przechodzi do kontrolera transferu danych przychodzących. Kontroler transferu danych przychodzących kieruje żądanie bezpośrednio do odpowiedniego w oparciu o jego obiekty i reguły dotyczące transferu danych przychodzących. Kontener devspaces-proxy w obszarze pod odbiera żądanie, dodaje `azds-route-as` nagłówek na podstawie adresu URL, a następnie kieruje żądanie do kontenera aplikacji.

Gdy żądanie HTTP zostanie wysłane do usługi z innej usługi w ramach klastra, żądanie najpierw przechodzi przez kontener devspaces-proxy usługi wywołującej. Kontener devspaces-proxy sprawdza żądanie HTTP i sprawdza `azds-route-as` nagłówek. Na podstawie nagłówka devspaces-proxy kontener będzie wyszukiwać adres IP usługi skojarzonej z wartością nagłówka. W przypadku znalezienia adresu IP kontener devspaces-proxy kieruje żądanie do tego adresu IP. Jeśli adres IP nie zostanie znaleziony, kontener devspaces-proxy kieruje żądanie do kontenera aplikacji nadrzędnej.

Na przykład aplikacje *Service* i *serviceB* są wdrażane w nadrzędnym obszarze deweloperskim o nazwie *default*. *Usługa* jest zależna od *serviceB* i wysyła do niej wywołania http. Użytkownik platformy Azure tworzy przestrzeń podrzędną dev na podstawie *domyślnego* obszaru o nazwie *azureuser*. Użytkownik platformy Azure wdraża także własną wersję *usługi* do ich obszaru podrzędnego. Gdy zostanie wysłane żądanie *http://azureuser.s.default.serviceA.fedcba09...azds.io* :

![Routing Azure Dev Spaces](media/how-dev-spaces-works/routing.svg)

1. Kontroler transferu danych przychodzących wyszukuje adres IP pod względem adresu URL, który jest *usługą. azureuser*.
1. Kontroler transferu danych przychodzących znajduje adres IP pod względem obszaru dev użytkownika platformy Azure i kieruje żądanie do *usługi Service. azureuser* pod.
1. Kontener devspaces-proxy w usłudze a *. azureuser* pod odbiera żądanie i dodaje `azds-route-as: azureuser` jako nagłówek HTTP.
1. Kontener devspaces-proxy w usłudze a *. azureuser* pod względem trasy żądania do kontenera aplikacji *usługi* Service. *azureuser* pod.
1. Aplikacja *usługi* w *usłudze Service. azureuser* pod wywołuje wywołanie *serviceB*. Aplikacja *usługi* również zawiera kod, który zachowuje istniejący `azds-route-as` nagłówek, w tym przypadku `azds-route-as: azureuser` .
1. Kontener devspaces-proxy w usłudze a *. azureuser* pod odbiera żądanie i wyszukuje adres IP *serviceB* na podstawie wartości `azds-route-as` nagłówka.
1. Kontener devspaces-proxy w usłudze a *. azureuser* pod nie znalazł adresu IP dla *serviceB. azureuser*.
1. Kontener devspaces-proxy w usłudze a *. azureuser* pod Szukam adresu IP dla *serviceB* w obszarze nadrzędnym, który jest *serviceB. default*.
1. Kontener devspaces-proxy w usłudze a *. azureuser* pod znajduje adres IP dla *serviceB. default* i kieruje żądanie do *serviceB. default* .
1. Kontener devspaces-proxy w *serviceB. default* pod odbiera żądanie i kieruje żądanie do kontenera aplikacji *serviceB* w *serviceB. default* pod.
1. Aplikacja *serviceB* w *serviceB. default* pod zwraca odpowiedź do *usługi. azureuser* pod.
1. Kontener devspaces-proxy w usłudze a *. azureuser* pod otrzymuje odpowiedź i kieruje odpowiedź do kontenera aplikacji *usługi Service* *. azureuser* pod.
1. Aplikacja *Service* a otrzymuje odpowiedź, a następnie zwraca własną odpowiedź.
1. Kontener devspaces-proxy w usłudze a *. azureuser* ' otrzymuje odpowiedź od kontenera aplikacji *usługi* i kieruje odpowiedź do oryginalnego obiektu wywołującego poza klastrem.

Wszystkie inne ruchy TCP, które nie są przekazywane przez protokół HTTP, za pośrednictwem kontrolera transferu danych przychodzących i kontenerów devspaces-proxy bez modyfikacji.

## <a name="sharing-a-dev-space"></a>Udostępnianie obszaru deweloperskiego

Podczas pracy z zespołem możesz udostępnić miejsce deweloperskie w całym zespole i utworzyć pochodne miejsca dev. Miejsce dev może być używane przez każdego, kto ma dostęp współautora do grupy zasobów obszaru dev.

Możesz również utworzyć nowe miejsce dev, które jest tworzone na podstawie innego miejsca dev. Gdy tworzysz pochodną przestrzeń dev, etykieta *azds.IO/Parent-Space=Parent-Space-Name* jest dodawana do przestrzeni nazw pochodnego obszaru dev. Ponadto wszystkie aplikacje z nadrzędnego miejsca deweloperskiego są udostępniane za pomocą pochodnego obszaru dev. W przypadku wdrożenia zaktualizowanej wersji aplikacji do pochodnego obszaru dev, będzie ona istniała tylko w pochodnym obszarze dev, a nadrzędna przestrzeń dev pozostanie bez zmian. Możesz mieć maksymalnie trzy poziomy *pochodnych lub miejsc do magazynowania.*

Pochodna przestrzeń dev również umożliwia inteligentne kierowanie żądań między własnymi aplikacjami i aplikacjami udostępnionymi z jego elementu nadrzędnego. Routing działa przez próbę skierowania żądania do aplikacji w pochodnym obszarze dev i powrocie do aplikacji udostępnionej z nadrzędnego obszaru dev. Routing powróci do aplikacji udostępnionej w obszarze nadrzędnym, jeśli aplikacja nie znajduje się w przestrzeni nadrzędnej.

Na przykład:
* *Domyślna* przestrzeń dev ma aplikacje *Service* i *serviceB*.
* Przestrzeń dev *azureuser* jest pochodną *domyślnego*.
* Zaktualizowana wersja *usługi Service* . została wdrożona w *azureuser*.

W przypadku korzystania z programu *azureuser* wszystkie żądania do *usługi Service* i będą kierowane do zaktualizowanej wersji w *azureuser*. Żądanie do *serviceB* najpierw będzie kierowane do *azureuser* wersji *serviceB*. Ponieważ nie istnieje, zostanie on rozesłany do *domyślnej* wersji *serviceB*. Jeśli *azureuser* wersja *usługi Service* of zostanie usunięta, wszystkie żądania do *usługi* będą powracać do korzystania z *domyślnej* wersji *usługi*.

## <a name="next-steps"></a>Następne kroki

Aby zobaczyć przykład, jak Azure Dev Spaces używa routingu, aby zapewnić szybką iterację i programowanie, zobacz [jak zdalne debugowanie kodu za pomocą Azure dev Spaces działa][how-it-works-remote-debugging].


[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md