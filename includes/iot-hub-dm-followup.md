---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-hub
ms.service: iot-hub
author: robinsh
ms.topic: include
ms.date: 02/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7f1f7d6f9ab6036fbcfcd1d19e175302bbd1a2a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87298838"
---
## <a name="customize-and-extend-the-device-management-actions"></a>Dostosowywanie i zwiększanie akcji zarządzania urządzeniami

Twoje rozwiązania IoT mogą rozszerzać zdefiniowany zestaw wzorców zarządzania urządzeniami lub włączać wzorce niestandardowe przy użyciu sznurka urządzenia i podstawowych metod metody z chmury do urządzenia. Inne przykłady akcji związanych z zarządzaniem urządzeniami obejmują Resetowanie do ustawień fabrycznych, aktualizowanie oprogramowania układowego, aktualizowanie aplikacji, zarządzanie urządzeniami, zarządzanie siecią i łączność oraz szyfrowanie danych.

## <a name="device-maintenance-windows"></a>Okna obsługi urządzeń

Zazwyczaj można skonfigurować urządzenia do wykonywania działań w czasie, które minimalizują przerwy i przestoje. Okna obsługi urządzeń są często używanymi wzorcami do definiowania czasu, w którym urządzenie ma zaktualizować swoją konfigurację. Rozwiązania zaplecza mogą korzystać z odpowiednich właściwości sznurka urządzenia, aby definiować i aktywować zasady na urządzeniu, które umożliwiają przeprowadzenie okna obsługi. Gdy urządzenie otrzyma zasady okna obsługi, może użyć raportowanej właściwości sznurka urządzenia, aby zgłosić stan zasad. Aplikacja zaplecza może następnie użyć zapytań bliźniaczych dla urządzeń, aby zaświadczyć zgodność urządzeń i poszczególnych zasad.

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto metody bezpośredniej do wyzwolenia zdalnego ponownego uruchomienia na urządzeniu. Zostały użyte zgłoszone właściwości do raportowania czasu ostatniego ponownego uruchomienia z urządzenia i zbadane sznurek urządzenia w celu odnalezienia czasu ostatniego ponownego uruchomienia urządzenia w chmurze.

Aby nadal zacząć korzystać z wzorców IoT Hub i zarządzania urządzeniami, takimi jak zdalne przez aktualizację oprogramowania układowego, zobacz artykuł [Jak przeprowadzić aktualizację oprogramowania układowego](../articles/iot-hub/tutorial-firmware-update.md).

Aby dowiedzieć się, jak rozłożyć rozwiązanie IoT i zaplanować wywołania metod na wielu urządzeniach, zobacz [Planowanie i emitowanie zadań](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md).