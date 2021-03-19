---
title: Azure Stack brzeg Pro zabezpieczenia | Microsoft Docs
description: W tym artykule opisano funkcje zabezpieczeń i ochrony prywatności, które chronią urządzenia, usługi i dane w systemie Azure Stack Edge w środowisku lokalnym i w chmurze.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: b5c5143c7bc92487aeabc3cdc1afea5061466027
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90903159"
---
# <a name="azure-stack-edge-pro-security-and-data-protection"></a>Azure Stack brzeg Pro zabezpieczenia i ochrona danych

Bezpieczeństwo jest ważnym problemem w przypadku przyjęcia nowej technologii, szczególnie jeśli technologia jest używana z danymi poufnymi lub zastrzeżonymi. Azure Stack EDGE Pro pomaga upewnić się, że tylko autoryzowane jednostki będą mogły wyświetlać, modyfikować i usuwać dane.

W tym artykule opisano funkcje zabezpieczeń programu Edge w wersji Azure Stack brzegowej, które pomagają chronić poszczególne składniki rozwiązań i przechowywane w nich dane.

Azure Stack EDGE Pro obejmuje cztery główne składniki, które współpracują ze sobą:

- **Usługa Azure Stack Edge hostowana na platformie Azure**. Zasób zarządzania służący do tworzenia kolejności urządzeń, konfigurowania urządzenia, a następnie śledzenia kolejności do ukończenia.
- **Azure Stack Edge na urządzeniu**. Urządzenie transferowe, które jest wysyłane do Ciebie, aby można było zaimportować dane lokalne na platformę Azure.
- **Klienci/hosty połączeni z urządzeniem**. Klienci w infrastrukturze, którzy łączą się z urządzeniem Azure Stack EDGE Pro i zawierają dane, które muszą być chronione.
- **Magazyn w chmurze**. Lokalizacja na platformie Azure w chmurze, w której są przechowywane dane. Ta lokalizacja jest zazwyczaj kontem magazynu połączonym z utworzonym zasobem Azure Stack Edge.

## <a name="azure-stack-edge-service-protection"></a>Azure Stacka Ochrona usługi Edge

Usługa Edge Azure Stack to usługa zarządzania hostowana na platformie Azure. Usługa służy do konfigurowania urządzenia i zarządzania nim.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="azure-stack-edge-pro-device-protection"></a>Azure Stack Edge — ochrona urządzeń

Urządzenie Azure Stack EDGE Pro to urządzenie lokalne, które pomaga przekształcić dane, przetwarzając je lokalnie, a następnie wysyłając je na platformę Azure. Twoje urządzenie:

- Potrzebuje klucza aktywacji, aby uzyskać dostęp do usługi Azure Stack Edge.
- Jest chronione przez cały czas przy użyciu hasła urządzenia.
- Jest urządzeniem zablokowanym. Kontroler BMC i system BIOS urządzenia są chronione hasłem. System BIOS jest chroniony przez ograniczony dostęp użytkownika.
- Ma włączony bezpieczny rozruch.
- Uruchamia funkcję Windows Defender Device Guard. Funkcja Device Guard pozwala uruchamiać tylko zaufane aplikacje zdefiniowane w ramach zasad integralności kodu.

### <a name="protect-the-device-via-activation-key"></a>Ochrona urządzenia za pomocą klucza aktywacji

Do przyłączenia do usługi Azure Stack Edge, która została utworzona w ramach subskrypcji platformy Azure, może być dołączane tylko autoryzowane urządzenie Azure Stack Edge. Aby autoryzować urządzenie, należy użyć klucza aktywacji w celu aktywowania urządzenia za pomocą usługi Azure Stack Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Aby uzyskać więcej informacji, zobacz [Uzyskiwanie klucza aktywacji](azure-stack-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Ochrona urządzenia za pomocą hasła

Hasła zapewniają, że dostęp do danych mają tylko autoryzowani użytkownicy. Azure Stack nastąpi rozruch urządzeń brzegowych Pro w stanie zablokowanym.

Oto co możesz zrobić:

- Połącz się z lokalnym interfejsem użytkownika sieci Web urządzenia za pośrednictwem przeglądarki, a następnie podaj hasło, aby zalogować się na urządzeniu.
- Zdalne łączenie się z interfejsem PowerShell urządzenia za pośrednictwem protokołu HTTP. Zdalne zarządzanie jest domyślnie włączone. Następnie można podać hasło urządzenia, aby zalogować się na urządzeniu. Aby uzyskać więcej informacji, zobacz [zdalne nawiązywanie połączenia z urządzeniem Azure Stack EDGE Pro](azure-stack-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- [Zmień hasło](azure-stack-edge-manage-access-power-connectivity-mode.md#manage-device-access)przy użyciu lokalnego interfejsu użytkownika sieci Web. Jeśli zmienisz hasło, upewnij się, że wszyscy użytkownicy dostępu zdalnego nie mają problemów z logowaniem.

## <a name="protect-your-data"></a>Ochrona danych

Ta sekcja zawiera Azure Stack opis funkcji zabezpieczeń programu Edge w programie, które chronią dane w trakcie przesyłania i przechowywania.

### <a name="protect-data-at-rest"></a>Ochrona danych magazynowanych

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- Funkcja BitLocker XTS-AES 256-bitowe szyfrowanie jest używane do ochrony danych lokalnych.


### <a name="protect-data-in-flight"></a>Ochrona danych w locie

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Ochrona danych za pomocą kont magazynu

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Regularnie Obróć i [Synchronizuj klucze konta magazynu](azure-stack-edge-manage-shares.md#sync-storage-keys) , aby chronić konto magazynu przed nieautoryzowanymi użytkownikami.

## <a name="manage-personal-information"></a>Zarządzanie informacjami osobistymi

Usługa Azure Stack Edge zbiera dane osobowe w następujących scenariuszach:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Aby wyświetlić listę użytkowników, którzy mogą uzyskać dostęp do udziału lub go usunąć, wykonaj kroki opisane w sekcji [Zarządzanie udziałami na Azure Stack Edge](azure-stack-edge-manage-shares.md).

Aby uzyskać więcej informacji, zapoznaj się z zasadami ochrony prywatności firmy Microsoft w [Centrum zaufania](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Następne kroki

[Wdrażanie urządzenia z Azure Stack EDGE Pro](azure-stack-edge-deploy-prep.md)
