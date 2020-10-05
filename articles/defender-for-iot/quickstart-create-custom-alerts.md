---
title: 'Szybki Start: tworzenie alertów niestandardowych'
description: Informacje o tworzeniu i przypisywaniu niestandardowych alertów urządzeń dla usługi Azure Defender for IoT Security.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2020
ms.author: mlottner
ms.openlocfilehash: 48682e465374c1a0e1fb74fc6627016696ff6d2c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947801"
---
# <a name="quickstart-create-custom-alerts"></a>Szybki Start: tworzenie alertów niestandardowych

Korzystanie z niestandardowych grup zabezpieczeń i alertów pozwala w pełni wykorzystać kompleksowe informacje o zabezpieczeniach i kategoriie urządzenia, aby zapewnić lepsze zabezpieczenia rozwiązań IoT.

## <a name="why-use-custom-alerts"></a>Dlaczego warto używać alertów niestandardowych?

Wiesz, że Twoje urządzenia IoT są najlepsze.

W przypadku klientów, którzy w pełni rozumieją oczekiwane zachowanie urządzenia, usługa Defender for IoT umożliwia przetłumaczenie tego zrozumienia na zasady zachowania urządzeń i alert dotyczący odchylenia od oczekiwanego, normalnego zachowania.

## <a name="security-groups"></a>Grupy zabezpieczeń

Grupy zabezpieczeń umożliwiają definiowanie logicznych grup urządzeń oraz zarządzanie ich stanem zabezpieczeń w scentralizowany sposób.

Te grupy mogą reprezentować urządzenia z określonym sprzętem, urządzeniami wdrożonymi w określonej lokalizacji lub dowolną inną grupą odpowiednią do konkretnych potrzeb.

Grupy zabezpieczeń są definiowane przez właściwość tag urządzenia o nazwie " **zabezpieczenia**". Domyślnie każde rozwiązanie IoT w IoT Hub ma jedną grupę zabezpieczeń o nazwie **default**. Zmień wartość właściwości Grupa **zabezpieczeń** , aby zmienić grupę zabezpieczeń urządzenia.

Na przykład:

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  },
```

Używanie grup zabezpieczeń do grupowania urządzeń w kategorie logiczne. Po utworzeniu grup Przypisz je do wybranych niestandardowych alertów, aby uzyskać najbardziej efektywne kompleksowe rozwiązanie zabezpieczeń IoT.

## <a name="customize-an-alert"></a>Dostosowywanie alertu

1. Otwórz IoT Hub a następnie wybierz pozycję **Ustawienia** w menu **zabezpieczenia** . 
1. Kliknij pozycję **alerty niestandardowe**.
1. Wybierz grupę zabezpieczeń, do której chcesz zastosować dostosowanie.
1. Kliknij przycisk **Dodaj alert niestandardowy**.
1. Wybierz niestandardowy alert z listy rozwijanej.
1. Edytuj wymagane właściwości, a następnie kliknij przycisk **OK**.
1. Pamiętaj, aby kliknąć przycisk **Zapisz**. Bez zapisywania nowego alertu alert zostanie usunięty przy następnym zamknięciu IoT Hub.

## <a name="alerts-available-for-customization"></a>Alerty dostępne do dostosowania

Usługa Defender for IoT oferuje dużą liczbę alertów, które można dostosować zgodnie z konkretnymi potrzebami. Zapoznaj się z [dostosowywalną tabelą alertów](concept-customizable-security-alerts.md) w celu uzyskania ważności alertu, źródła danych, opisu i naszych sugerowanych czynności zaradczych w przypadku odebrania każdego alertu.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak wdrożyć agenta zabezpieczeń...

> [!div class="nextstepaction"]
> [Wdrażanie agenta zabezpieczeń](how-to-deploy-agent.md)
