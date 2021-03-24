---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 03/23/2021
ms.openlocfilehash: 34d0d55ba6eb403055be96758b57b7bd0c2ab704
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104987710"
---
Aby rozwiązać problemy związane z obliczeniami, użyj odpowiedzi w czasie wykonywania agenta IoT Edge. Poniżej znajduje się lista możliwych odpowiedzi:

* 200 — OK
* 400 — konfiguracja wdrożenia jest źle sformułowana lub nieprawidłowa.
* 417 — urządzenie nie ma ustawionej konfiguracji wdrożenia.
* 412 — wersja schematu w konfiguracji wdrożenia jest nieprawidłowa.
* 406 — urządzenie IoT Edge jest w trybie offline lub nie wysyła raportów o stanie.
* 500 — Wystąpił błąd w czasie wykonywania IoT Edge.

Aby uzyskać więcej informacji, zobacz [IoT Edge Agent](../articles/iot-edge/iot-edge-runtime.md?preserve-view=true&view=iotedge-2018-06#iot-edge-agent).

Następujący błąd jest związany z usługą IoT Edge na urządzeniu Azure Stack EDGE Pro.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>Moduły obliczeniowe mają nieznany stan i nie można ich użyć

#### <a name="error-description"></a>Opis błędu

Wszystkie moduły na urządzeniu pokazują nieznany stan i nie można ich użyć. Nieznany stan utrzymuje się po ponownym uruchomieniu.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Sugerowane rozwiązanie

Usuń usługę IoT Edge, a następnie ponownie Wdróż moduły. Aby uzyskać więcej informacji, zobacz [usuwanie usługi IoT Edge](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service).


### <a name="modules-show-as-running-but-are-not-working"></a>Moduły są wyświetlane jako uruchomione, ale nie działają

#### <a name="error-description"></a>Opis błędu

Stan środowiska uruchomieniowego modułu jest wyświetlany jako uruchomiony, ale oczekiwane wyniki nie są widoczne. 

Przyczyną może być problem z konfiguracją trasy modułu, która nie działa lub `edgehub` nie ma komunikatów routingu zgodnie z oczekiwaniami. Dzienniki można sprawdzić `edgehub` . Jeśli zobaczysz, że występują błędy, takie jak Niepowodzenie połączenia z usługą IoT Hub, najbardziej typową przyczyną jest problemy z łącznością. Przyczyną mogą być problemy z łącznością, ponieważ port AMPQ używany jako domyślny port przez usługę IoT Hub na potrzeby komunikacji jest zablokowany lub serwer proxy sieci Web blokuje te komunikaty.

#### <a name="suggested-solution"></a>Sugerowane rozwiązanie

Wykonaj następujące czynności:
1. Aby rozwiązać ten problem, przejdź do zasobu IoT Hub dla urządzenia, a następnie wybierz urządzenie brzegowe. 
1. Przejdź do pozycji **Ustawianie modułów > ustawienia środowiska uruchomieniowego**. 
1. Dodaj `Upstream protocol` zmienną środowiskową i przypisz ją do wartości `AMQPWS` . Komunikaty skonfigurowane w tym przypadku są wysyłane za pośrednictwem protokołu WebSockets za pośrednictwem portu 443.

### <a name="modules-show-as-running-but-do-not-have-an-ip-assigned"></a>Moduły są wyświetlane jako uruchomione, ale nie mają przypisanego adresu IP

#### <a name="error-description"></a>Opis błędu

Stan środowiska uruchomieniowego modułu jest wyświetlany jako uruchomiony, ale aplikacja kontenera nie ma przypisanego adresu IP. 

Wynika to z faktu, że zakres adresów IP dostarczonych dla adresów IP usług zewnętrznych Kubernetes jest niewystarczający. Należy rozwinąć ten zakres, aby upewnić się, że wszystkie wdrożone kontenery lub maszyny wirtualne zostaną omówione.

#### <a name="suggested-solution"></a>Sugerowane rozwiązanie

W lokalnym interfejsie użytkownika sieci Web urządzenia wykonaj następujące czynności:
1. Przejdź do strony **obliczenia** . Wybierz port, dla którego włączono sieć obliczeniową. 
1. Wprowadź statyczny, ciągły zakres adresów IP dla **adresów IP usług zewnętrznych Kubernetes**. Potrzebujesz 1 adresu IP dla `edgehub` usługi. Ponadto wymagany jest jeden adres IP dla każdego modułu IoT Edge i dla każdej maszyny wirtualnej, która zostanie wdrożona. 
1. Wybierz przycisk **Zastosuj**. Zmieniony zakres adresów IP powinien obowiązywać od razu.

Aby uzyskać więcej informacji, zobacz [Zmienianie zewnętrznych adresów IP usługi dla kontenerów](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#change-external-service-ips-for-containers).

### <a name="configure-static-ips-for-iot-edge-modules"></a>Skonfiguruj statyczne adresy IP dla modułów IoT Edge

#### <a name="problem-description"></a>Opis problemu

Kubernetes przypisuje dynamiczne adresy IP do każdego modułu IoT Edge na urządzeniu z systemem Azure Stack Edge. Aby skonfigurować statyczne adresy IP dla modułów, wymagana jest metoda.

#### <a name="suggested-solution"></a>Sugerowane rozwiązanie

Możesz określić stałe adresy IP dla modułów IoT Edge za pośrednictwem sekcji K8s-eksperymentalnej, jak opisano poniżej: 

```yaml
{
  "k8s-experimental": {
    "serviceOptions" : {
      "loadBalancerIP" : "100.23.201.78",
      "type" : "LoadBalancer"
    }
  }
}
```
### <a name="expose-kubernetes-service-as-cluster-ip-service-for-internal-communication"></a>Uwidacznianie usługi Kubernetes jako usługi IP klastra na potrzeby komunikacji wewnętrznej

#### <a name="problem-description"></a>Opis problemu

Domyślnie typem usługi IoT jest usługa równoważenia obciążenia i przypisane zewnętrznie adresy IP. Być może nie chcesz, aby dla aplikacji był używany zewnętrzny adres IP. Może być konieczne uwidocznienie zasobników w klastrze KUbernetes, aby uzyskać dostęp jako inne magazyny, a nie jako zewnętrznie uwidocznioną usługę równoważenia obciążenia. 

#### <a name="suggested-solution"></a>Sugerowane rozwiązanie

Możesz użyć opcji tworzenia za pośrednictwem sekcji K8s — eksperymentalne. Następująca opcja usługi powinna współpracować z powiązaniami portów.

```yaml
{
"k8s-experimental": {
  "serviceOptions" : {
    "type" : "ClusterIP"
    }
  }
}
```
