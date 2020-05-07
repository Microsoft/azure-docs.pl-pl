---
title: Zabezpieczenia Azure Stack Edge | Microsoft Docs
description: Opisuje funkcje zabezpieczeń i ochrony prywatności, które chronią Azure Stack urządzenia brzegowego, usługi i danych lokalnie i w chmurze.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 3d567ec4d760be24fdbb79ff85bd6db0eb4a66c8
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82570072"
---
# <a name="azure-stack-edge-security-and-data-protection"></a>Azure Stack Edge zabezpieczenia i ochrona danych

Bezpieczeństwo jest ważnym problemem w przypadku przyjęcia nowej technologii, szczególnie jeśli technologia jest używana z danymi poufnymi lub zastrzeżonymi. Azure Stack Edge pomaga upewnić się, że tylko autoryzowane jednostki będą mogły wyświetlać, modyfikować i usuwać dane.

W tym artykule opisano funkcje zabezpieczeń Azure Stack Edge, które pomagają chronić poszczególne składniki rozwiązań i przechowywane w nich dane.

Azure Stack Edge składa się z czterech głównych komponentów, które współpracują ze sobą:

- **Usługa Azure Stack Edge hostowana na platformie Azure**. Zasób zarządzania służący do tworzenia kolejności urządzeń, konfigurowania urządzenia, a następnie śledzenia kolejności do ukończenia.
- **Azure Stack Urządzenie brzegowe**. Urządzenie transferowe, które jest wysyłane do Ciebie, aby można było zaimportować dane lokalne na platformę Azure.
- **Klienci/hosty połączeni z urządzeniem**. Klienci w infrastrukturze, którzy łączą się z urządzeniem Azure Stack Edge i zawierają dane, które muszą być chronione.
- **Magazyn w chmurze**. Lokalizacja na platformie Azure w chmurze, w której są przechowywane dane. Ta lokalizacja jest zazwyczaj kontem magazynu połączonym z utworzonym zasobem Azure Stack Edge.

## <a name="azure-stack-edge-service-protection"></a>Azure Stacka Ochrona usługi Edge

Usługa Edge Azure Stack to usługa zarządzania hostowana na platformie Azure. Usługa służy do konfigurowania urządzenia i zarządzania nim.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="azure-stack-edge-device-protection"></a>Azure Stack ochronę urządzenia brzegowego

Urządzenie brzegowe Azure Stack jest urządzeniem lokalnym, które pomaga przekształcić dane, przetwarzając je lokalnie, a następnie wysyłając je na platformę Azure. Twoje urządzenie:

- Potrzebuje klucza aktywacji, aby uzyskać dostęp do usługi Azure Stack Edge.
- Jest chronione przez cały czas przy użyciu hasła urządzenia.
- Jest urządzeniem zablokowanym. Kontroler BMC i system BIOS urządzenia są chronione hasłem. System BIOS jest chroniony przez ograniczony dostęp użytkownika.
- Ma włączony bezpieczny rozruch.
- Uruchamia funkcję Windows Defender Device Guard. Funkcja Device Guard pozwala uruchamiać tylko zaufane aplikacje zdefiniowane w ramach zasad integralności kodu.

### <a name="protect-the-device-via-activation-key"></a>Ochrona urządzenia za pomocą klucza aktywacji

Tylko autoryzowane Azure Stack Urządzenie brzegowe może dołączyć do usługi Azure Stack Edge utworzonej w ramach subskrypcji platformy Azure. Aby autoryzować urządzenie, należy użyć klucza aktywacji w celu aktywowania urządzenia za pomocą usługi Azure Stack Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Aby uzyskać więcej informacji, zobacz [Uzyskiwanie klucza aktywacji](azure-stack-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Ochrona urządzenia za pomocą hasła

Hasła zapewniają, że dostęp do danych mają tylko autoryzowani użytkownicy. Urządzenia brzegowe Azure Stack rozruchu w stanie zablokowanym.

Można:

- Połącz się z lokalnym interfejsem użytkownika sieci Web urządzenia za pośrednictwem przeglądarki, a następnie podaj hasło, aby zalogować się na urządzeniu.
- Zdalne łączenie się z interfejsem PowerShell urządzenia za pośrednictwem protokołu HTTP. Zdalne zarządzanie jest domyślnie włączone. Następnie można podać hasło urządzenia, aby zalogować się na urządzeniu. Aby uzyskać więcej informacji, zobacz [zdalne łączenie z urządzeniem Azure Stack Edge](azure-stack-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- [Zmień hasło](azure-stack-edge-manage-access-power-connectivity-mode.md#manage-device-access)przy użyciu lokalnego interfejsu użytkownika sieci Web. Jeśli zmienisz hasło, upewnij się, że wszyscy użytkownicy dostępu zdalnego nie mają problemów z logowaniem.

## <a name="protect-your-data"></a>Ochrona danych

W tej sekcji opisano Azure Stack funkcje zabezpieczeń brzegowych chroniące dane przesyłane i przechowywane.

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

[Wdrażanie urządzenia brzegowego Azure Stack](azure-stack-edge-deploy-prep.md)
