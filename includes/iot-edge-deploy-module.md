---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/30/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 6b9ec2017ffa5d4e4148b441aa23ed2eca6ee8b8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "99628871"
---
Jedną z kluczowych możliwości Azure IoT Edge jest wdrożenie kodu na urządzeniach IoT Edge z chmury. *Moduły IoT Edge* są pakietami wykonywalnymi wdrożonymi jako kontenery. Ta sekcja obejmuje wdrożenie wstępnie skompilowanego modułu z [sekcji modułów IoT Edge w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) bezpośrednio z usługi Azure IoT Hub.

Moduł, który wdrożysz w tej sekcji, symuluje czujnik i wysyła wygenerowane dane. Ten moduł jest przydatny, gdy rozpoczyna się pracę z usługą IoT Edge, ponieważ symulowane dane można wykorzystać przy programowaniu i testowaniu. Jeśli chcesz zobaczyć, co dokładnie robi ten moduł, możesz wyświetlić [kod źródłowy symulowanego czujnika temperatury](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Wykonaj następujące kroki, aby wdrożyć pierwszy moduł z witryny Azure Marketplace.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT Hub.

1. Z menu po lewej stronie w obszarze **Automatyczne zarządzanie urządzeniami** wybierz pozycję **IoT Edge**.

1. Wybierz identyfikator urządzenia docelowego z listy urządzeń.

1. Na górnym pasku wybierz pozycję **Ustaw moduły**.

   ![Zrzut ekranu pokazujący Wybieranie modułów zestawu.](./media/iot-edge-deploy-module/select-set-modules.png)

1. W obszarze **IoT Edge modules** Otwórz menu rozwijane **Dodaj** , a następnie wybierz pozycję **moduł witryny Marketplace**.

   ![Zrzut ekranu pokazujący menu rozwijane Dodaj.](./media/iot-edge-deploy-module/add-marketplace-module.png)

1. W **witrynie IoT Edge Portal Marketplace** Wyszukaj i wybierz `Simulated Temperature Sensor` moduł.

   Moduł zostanie dodany do sekcji modułów IoT Edge z żądanym stanem **uruchomienia** .

1. Wybierz pozycję **Dalej: trasy** , aby przejść do następnego kroku kreatora.

   ![Zrzut ekranu pokazujący przejście do następnego kroku po dodaniu modułu.](./media/iot-edge-deploy-module/view-temperature-sensor-next-routes.png)

1. Na karcie **trasy** Usuń trasę **domyślną trasy,** a następnie wybierz kolejno pozycje **Dalej: przegląd + Utwórz** , aby przejść do następnego kroku kreatora.

   >[!Note]
   >Trasy są konstruowane przy użyciu par nazw i wartości. Na tej stronie powinny być widoczne dwie trasy. Trasa domyślna ( **Route**) wysyła wszystkie komunikaty do IoT Hub (nazywanego `$upstream` ). Druga trasa, **SimulatedTemperatureSensorToIoTHub**, została utworzona automatycznie podczas dodawania modułu z witryny Azure Marketplace. Ta trasa wysyła wszystkie komunikaty z modułu symulowanej temperatury do IoT Hub. Trasy domyślnej można usunąć, ponieważ jest ona nadmiarowa w tym przypadku.

   ![Zrzut ekranu pokazujący usunięcie trasy domyślnej, a następnie przejście do następnego kroku.](./media/iot-edge-deploy-module/delete-route-next-review-create.png)

1. Przejrzyj plik JSON, a następnie wybierz pozycję **Utwórz**. Plik JSON definiuje wszystkie moduły, które są wdrażane na urządzeniu IoT Edge. Zobaczysz moduł **SimulatedTemperatureSensor** oraz dwa moduły uruchomieniowe, **edgeAgent** i **edgeHub**.

   >[!Note]
   >Podczas przesłania nowego wdrożenia na urządzenie usługi IoT Edge do urządzenia nie jest wypychane żadne powiadomienie. Nie jest to konieczne, ponieważ urządzenie regularnie wysyła do usługi IoT Hub zapytania w celu odebrania wszelkich nowych instrukcji. Jeśli urządzenie znajdzie zaktualizowany manifest wdrażania, użyje informacji o nowym wdrożeniu, aby ściągnąć obrazy modułów z chmury, a następnie zacznie uruchamiać moduły lokalnie. Ten proces może potrwać kilka minut.

1. Po utworzeniu szczegółów wdrożenia modułu Kreator powróci do strony szczegółów urządzenia. Wyświetl stan wdrożenia na karcie **moduły** .

   Powinny być widoczne trzy moduły: **$edgeAgent**, **$edgeHub** i **SimulatedTemperatureSensor**. Jeśli co najmniej jeden z modułów ma **wartość tak** w obszarze **wdrożenie** , ale nie w obszarze **zgłoszone przez urządzenie**, urządzenie IoT Edge nadal je uruchamia. Zaczekaj kilka minut, a następnie Odśwież stronę.

   ![Zrzut ekranu pokazujący symulowany czujnik temperatury na liście wdrożonych modułów.](./media/iot-edge-deploy-module/view-deployed-modules.png)
