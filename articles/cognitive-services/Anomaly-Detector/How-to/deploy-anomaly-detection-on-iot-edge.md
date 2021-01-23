---
title: Uruchom wykrywacz anomalii na IoT Edge
titleSuffix: Azure Cognitive Services
description: Wdróż moduł wykrywania anomalii w IoT Edge.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: mbullwin
ms.openlocfilehash: 370883b90902dad5a6e222897e68e1d3e9dd2acf
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98738019"
---
# <a name="deploy-an-anomaly-detector-module-to-iot-edge"></a>Wdróż moduł wykrywania anomalii w usłudze IoT Edge

Dowiedz się, jak wdrożyć moduł [wykrywania anomalii](../anomaly-detector-container-howto.md) Cognitive Services na urządzeniu IoT Edge. Po wdrożeniu w IoT Edge moduł jest uruchamiany w IoT Edge wraz z innymi modułami jako wystąpieniami kontenerów. Przedstawia to dokładne interfejsy API jako wystąpienie kontenera wykrywania anomalii działające w standardowym środowisku kontenera Docker. 

## <a name="prerequisites"></a>Wymagania wstępne

* Użyj subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).
* Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).
* [IoT Hub](../../../iot-hub/iot-hub-create-through-portal.md) i [IoT Edge](../../../iot-edge/quickstart-linux.md) urządzenie.

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

## <a name="deploy-the-anomaly-detection-module-to-the-edge"></a>Wdrażanie modułu wykrywania anomalii na krawędzi

1. W Azure Portal wprowadź **wykrywanie anomalii na IoT Edge** do wyszukiwania i Otwórz wynik w witrynie Azure Marketplace.
2. Spowoduje to przejście do Azure Portal [urządzeń docelowych dla IoT Edge stronie modułu](https://portal.azure.com/#create/azure-cognitive-service.edge-anomaly-detector). Podaj następujące wymagane informacje.

    1. Wybierz subskrypcję.

    1. Wybierz IoT Hub.

    1. Wybierz pozycję **Znajdź urządzenie** i znajdź urządzenie IoT Edge.

3. Wybierz przycisk **Utwórz**.

4. Wybierz moduł **AnomalyDetectoronIoTEdge** .

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/iot-edge-modules.png" alt-text="Obraz interfejsu użytkownika modułów IoT Edge z wyróżnionym linkiem AnomalyDetectoronIoTEdge z czerwonym prostokątem wskazującym, że jest to element do wybrania.":::

5. Przejdź do obszaru **Zmienne środowiskowe** i podaj następujące informacje.

    1.  Zachowaj wartość Zaakceptuj dla ustawienia **Umowa Licencyjna Użytkownika Oprogramowania (EULA)**.

    1. Wypełnij pole **Rozliczenia**, używając punktu końcowego usługi Cognitive Services.

    1. Wypełnij pole **ApiKey**, używając klucza interfejsu API usługi Cognitive Services.

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/environment-variables.png" alt-text="Zmienne środowiskowe z czerwonymi polami wokół obszarów, które wymagają wartości do wypełnienia dla punktów końcowych i klucza interfejsu API":::

6. Wybierz pozycję **Aktualizuj**

7. Wybierz pozycję **Dalej: trasy** , aby zdefiniować trasę. Zdefiniuj, że wszystkie komunikaty ze wszystkich modułów mają być przekazywane do usługi Azure IoT Hub.

8. Wybierz pozycję **Dalej: Przeglądanie i tworzenie**. Możesz wyświetlić podgląd pliku JSON definiującego wszystkie moduły, które zostaną wdrożone na urządzeniu usługi IoT Edge.
    
9. Wybierz pozycję **Utwórz**, aby rozpocząć wdrażanie modułu.

10. Po zakończeniu wdrażania modułu wróć do strony IoT Edge w usłudze IoT Hub. Wybierz urządzenie z listy urządzeń usługi IoT Edge, aby wyświetlić jego szczegóły.

11. Przewiń w dół i przejrzyj wymienione moduły. Sprawdź, czy stan środowiska uruchomieniowego jest uruchomiony dla nowego modułu. 

Aby rozwiązać problem ze stanem środowiska uruchomieniowego urządzenia IoT Edge, zapoznaj się z [przewodnikiem rozwiązywania problemów](../../../iot-edge/troubleshoot.md)

## <a name="test-anomaly-detector-on-an-iot-edge-device"></a>Testowanie wykrywania anomalii na urządzeniu IoT Edge

Prześlesz wywołanie HTTP do urządzenia usługi Azure IoT Edge, na którym jest uruchomiony kontener usługi Azure Cognitive Services. Kontener zapewnia interfejsy API punktu końcowego oparte na protokole REST. Użyj hosta, `http://<your-edge-device-ipaddress>:5000` , dla interfejsów API modułu.

Jeśli urządzenie brzegowe nie zezwala jeszcze na komunikację przychodzącą na porcie 5000, należy utworzyć nową **regułę portu wejściowego**. 

Dla maszyny wirtualnej platformy Azure można ją ustawić w obszarze Ustawienia **maszyny wirtualnej**  >    >  **sieci**  >  **reguła portu przychodzącego**  >  **Dodaj regułę portu przychodzącego**.

Istnieje kilka sposobów, aby sprawdzić, czy moduł jest uruchomiony. Znajdź *zewnętrzny adres IP* i uwidoczniony port urządzenia brzegowego, a następnie otwórz ulubioną przeglądarkę internetową. Użyj różnych adresów URL żądań poniżej, aby sprawdzić, czy kontener jest uruchomiony. Przykładowe adresy URL żądania wymienione poniżej to `http://<your-edge-device-ipaddress:5000` , ale konkretny kontener może się różnić. Pamiętaj, że musisz użyć *zewnętrznego adresu IP* urządzenia brzegowego.

| Adres URL żądania | Przeznaczenie |
|:-------------|:---------|
| `http://<your-edge-device-ipaddress>:5000/` | Kontener zawiera stronę główną. |
| `http://<your-edge-device-ipaddress>:5000/status` | Zażądano również przy POBIERAniu, sprawdza, czy klucz API użyty do uruchomienia kontenera jest prawidłowy bez powodowania zapytania punktu końcowego. To żądanie może służyć do [sondowania na żywo i gotowości](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)Kubernetes. |
| `http://<your-edge-device-ipaddress>:5000/swagger` | Kontener zawiera pełen zestaw dokumentacji dla punktów końcowych oraz funkcję **Wypróbuj**. Korzystając z tej funkcji, można wprowadzić ustawienia w formie HTML opartej na sieci Web i utworzyć zapytanie bez konieczności pisania kodu. Po powrocie zapytania zostanie podane przykładowe ZWINIĘCIE polecenia, aby przedstawić nagłówki HTTP i format treści, który jest wymagany. |

![Strona główna kontenera](../../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z tematem [Instalowanie i uruchamianie kontenerów](../anomaly-detector-container-configuration.md) na potrzeby ściągania obrazu kontenera i uruchamiania kontenera
* Przegląd [konfigurowania kontenerów](../anomaly-detector-container-configuration.md) dla ustawień konfiguracji
* [Dowiedz się więcej o usłudze interfejsu API wykrywania anomalii](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)