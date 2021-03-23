---
title: 'Create custom alerts (Szybki start: tworzenie alertów niestandardowych)'
description: Poznaj, twórz i przypisuj niestandardowe alerty urządzeń dla usługi Azure Defender for IoT Security.
ms.topic: quickstart
ms.date: 09/04/2020
ms.openlocfilehash: 836619fa200983bc91d26db4d063e8545b11a1b5
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781045"
---
# <a name="create-custom-alerts"></a>Create custom alerts (Szybki start: tworzenie alertów niestandardowych)

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

1. Wybierz pozycję **alerty niestandardowe**.

1. Wybierz grupę zabezpieczeń, do której chcesz zastosować dostosowanie.

1. Wybierz pozycję **Dodaj alert niestandardowy**.

1. Wybierz niestandardowy alert z listy rozwijanej.

1. Edytuj wymagane właściwości, a następnie wybierz **przycisk OK**.

1. Upewnij się, że wybrano pozycję **Zapisz**. Bez zapisywania nowego alertu alert zostanie usunięty przy następnym zamknięciu IoT Hub.

## <a name="alerts-available-for-customization"></a>Alerty dostępne do dostosowania

Usługa Defender for IoT oferuje dużą liczbę alertów, które można dostosować zgodnie z konkretnymi potrzebami. Zapoznaj się z [dostosowywalną tabelą alertów](concept-customizable-security-alerts.md) w celu uzyskania ważności alertu, źródła danych, opisu i sugerowanych czynności zaradczych, jeśli i po odebraniu każdego alertu.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak wdrożyć agenta zabezpieczeń...

> [!div class="nextstepaction"]
> [Wdrażanie agenta zabezpieczeń](how-to-deploy-agent.md)
