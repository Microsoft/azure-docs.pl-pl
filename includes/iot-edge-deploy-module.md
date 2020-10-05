---
title: plik dołączania
description: plik dołączania
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/30/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 76c8eb7acf20d8cf68d3573defd947efbc6c3c43
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "85801740"
---
Jedną z najważniejszych funkcji usługi Azure IoT Edge jest możliwość wdrażania kodu na urządzeniach usługi IoT Edge z poziomu chmury. *Moduły IoT Edge* są pakietami wykonywalnymi wdrożonymi jako kontenery. Ta sekcja obejmuje wdrożenie wstępnie skompilowanego modułu z [sekcji modułów IoT Edge w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) bezpośrednio z poziomu usługi Azure IoT Hub.

Moduł, który wdrożysz w tej sekcji, symuluje czujnik i wysyła wygenerowane dane. Ten moduł jest przydatny, gdy rozpoczyna się pracę z usługą IoT Edge, ponieważ symulowane dane można wykorzystać przy programowaniu i testowaniu. Jeśli chcesz zobaczyć, co dokładnie robi ten moduł, możesz wyświetlić [kod źródłowy symulowanego czujnika temperatury](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Aby wdrożyć swój pierwszy moduł z witryny Azure Marketplace, wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT Hub.

1. W menu po lewej stronie w obszarze **Automatyczne zarządzanie urządzeniami**wybierz pozycję **IoT Edge**.

1. Kliknij identyfikator urządzenia na urządzeniu docelowym z listy urządzeń.

1. Na górnym pasku wybierz pozycję **Ustaw moduły**.

   ![Wybierz pozycję Ustaw moduły na stronie Szczegóły urządzenia](./media/iot-edge-deploy-module/select-set-modules.png)

1. W sekcji **IoT Edge modułów** na stronie kliknij pozycję **Dodaj** i wybierz pozycję **moduł Marketplace** z menu rozwijanego.

   ![Dodaj moduł witryny Marketplace](./media/iot-edge-deploy-module/add-marketplace-module.png)

1. W **witrynie IoT Edge Marketplace**Wyszukaj ciąg "symulowanie czujnika temperatury" i wybierz ten moduł.

1. Zwróć uwagę, że moduł SimulatedTemperatureSensor jest dodawany do sekcji modułów IoT Edge z **uruchomionym**żądanym stanem.

   Wybierz pozycję **Dalej: trasy** , aby przejść do następnego kroku kreatora.

   ![Przejdź do następnego kroku po wystawieniu modułu czujnika temperatury](./media/iot-edge-deploy-module/view-temperature-sensor-next-routes.png)

1. Na karcie **trasy** kreatora możesz określić sposób przekazywania komunikatów między modułami i IoT Hub. Trasy są konstruowane przy użyciu par nazwa/wartość. Na tej stronie powinny być widoczne dwie trasy. Trasa domyślna o nazwie **Route** wysyła wszystkie komunikaty do IoT Hub (która jest wywoływana `$upstream` ). Druga trasa o nazwie **SimulatedTemperatureSensorToIoTHub** została utworzona automatycznie po dodaniu modułu z portalu Marketplace. Ta trasa wysyła wszystkie komunikaty przeznaczone wyłącznie z modułu symulowanej temperatury do IoT Hub. Trasy domyślnej można usunąć, ponieważ jest ona nadmiarowa w tym przypadku.

   Wybierz kolejno pozycje **Dalej: przegląd + Utwórz** , aby przejść do następnego kroku kreatora.

   ![Usuń trasę domyślną, a następnie przejdź do następnego kroku](./media/iot-edge-deploy-module/delete-route-next-review-create.png)

1. Na karcie **Przegląd + tworzenie** kreatora można wyświetlić podgląd pliku JSON, który definiuje wszystkie moduły, które są wdrażane na urządzeniu IoT Edge. Zwróć uwagę, że moduł **SimulatedTemperatureSensor** jest dołączony, a także dwa moduły uruchomieniowe, **edgeAgent** i **edgeHub**. Wybierz pozycję **Utwórz** po zakończeniu przeglądania.

   Podczas przesłania nowego wdrożenia na urządzenie usługi IoT Edge do urządzenia nie jest wypychane żadne powiadomienie. Nie jest to konieczne, ponieważ urządzenie regularnie wysyła do usługi IoT Hub zapytania w celu odebrania wszelkich nowych instrukcji. Jeśli urządzenie znajdzie zaktualizowany manifest wdrażania, użyje informacji o nowym wdrożeniu, aby ściągnąć obrazy modułów z chmury, a następnie zacznie uruchamiać moduły lokalnie. Ten proces może potrwać kilka minut.

1. Po utworzeniu szczegółów wdrożenia modułu Kreator powróci do strony szczegółów urządzenia. Na stronie Szczegóły urządzenia Wyświetl stan wdrożenia na karcie **moduły** . Należy wymienić trzy moduły: $edgeAgent, $edgeHub i SimulatedTemperatureSensor. Jeśli co najmniej jeden z modułów jest wyświetlany jako określony we wdrożeniu, ale nie jako zgłaszany przez urządzenie, to urządzenie usługi IoT Edge nadal go uruchamia. Poczekaj chwilę i wybierz pozycję **Odśwież** w górnej części strony.

   ![Wyświetlanie modułu SimulatedTemperatureSensor na liście wdrożonych modułów](./media/iot-edge-deploy-module/view-deployed-modules.png)
