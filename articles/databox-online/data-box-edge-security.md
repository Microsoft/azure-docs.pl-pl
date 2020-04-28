---
title: Zabezpieczenia Azure Data Box Edge | Microsoft Docs
description: Opisuje funkcje zabezpieczeń i ochrony prywatności, które chronią urządzenie Azure Data Box Edge, usługę i dane lokalnie i w chmurze.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 375576dd4a7897c48474fd2af00a99084292d854
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "69970880"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Azure Data Box Edge security and data protection (Zabezpieczenia i ochrona danych w usłudze Azure Data Box Edge)

Bezpieczeństwo jest ważnym problemem w przypadku przyjęcia nowej technologii, szczególnie jeśli technologia jest używana z danymi poufnymi lub zastrzeżonymi. Azure Data Box Edge pomaga upewnić się, że tylko autoryzowane jednostki mogą wyświetlać, modyfikować i usuwać dane.

W tym artykule opisano funkcje zabezpieczeń Data Box Edge chroniące poszczególne składniki rozwiązań i przechowywane w nich dane.

Azure Data Box Edge obejmuje cztery główne składniki, które współpracują ze sobą:

- **Usługa Data Box Edge hostowana na platformie Azure**. Zasób zarządzania służący do tworzenia kolejności urządzeń, konfigurowania urządzenia, a następnie śledzenia kolejności do ukończenia.
- **Data Box Edge urządzenia**. Urządzenie transferowe, które jest wysyłane do Ciebie, aby można było zaimportować dane lokalne na platformę Azure.
- **Klienci/hosty połączeni z urządzeniem**. Klienci w infrastrukturze, którzy łączą się z urządzeniem Data Box Edge i zawierają dane, które muszą być chronione.
- **Magazyn w chmurze**. Lokalizacja na platformie Azure w chmurze, w której są przechowywane dane. Ta lokalizacja jest zazwyczaj kontem magazynu połączonym z tworzonym zasobem Data Box Edge.

## <a name="data-box-edge-service-protection"></a>Data Box Edge ochronę usługi

Usługa Data Box Edge to usługa zarządzania hostowana na platformie Azure. Usługa służy do konfigurowania urządzenia i zarządzania nim.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Data Box Edge ochrony urządzeń

Urządzenie Data Box Edge jest urządzeniem lokalnym, które pomaga przekształcić dane, przetwarzając je lokalnie, a następnie wysyłając je na platformę Azure. Twoje urządzenie:

- Wymaga klucza aktywacji w celu uzyskania dostępu do usługi Data Box Edge.
- Jest chronione przez cały czas przy użyciu hasła urządzenia.
- Jest urządzeniem zablokowanym. Kontroler BMC i system BIOS urządzenia są chronione hasłem. System BIOS jest chroniony przez ograniczony dostęp użytkownika.
- Ma włączony bezpieczny rozruch.
- Uruchamia funkcję Windows Defender Device Guard. Funkcja Device Guard pozwala uruchamiać tylko zaufane aplikacje zdefiniowane w ramach zasad integralności kodu.

### <a name="protect-the-device-via-activation-key"></a>Ochrona urządzenia za pomocą klucza aktywacji

Tylko autoryzowane urządzenie Data Box Edge może przyłączyć się do usługi Data Box Edge utworzonej w ramach subskrypcji platformy Azure. Aby autoryzować urządzenie, należy użyć klucza aktywacji w celu aktywowania urządzenia za pomocą usługi Data Box Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Aby uzyskać więcej informacji, zobacz [Uzyskiwanie klucza aktywacji](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Ochrona urządzenia za pomocą hasła

Hasła zapewniają, że dostęp do danych mają tylko autoryzowani użytkownicy. Data Box Edge urządzenia są uruchamiane w stanie zablokowanym.

Można:

- Połącz się z lokalnym interfejsem użytkownika sieci Web urządzenia za pośrednictwem przeglądarki, a następnie podaj hasło, aby zalogować się na urządzeniu.
- Zdalne łączenie się z interfejsem PowerShell urządzenia za pośrednictwem protokołu HTTP. Zdalne zarządzanie jest domyślnie włączone. Następnie można podać hasło urządzenia, aby zalogować się na urządzeniu. Aby uzyskać więcej informacji, zobacz [zdalne łączenie z urządzeniem Data Box Edge](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- [Zmień hasło](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access)przy użyciu lokalnego interfejsu użytkownika sieci Web. Jeśli zmienisz hasło, upewnij się, że wszyscy użytkownicy dostępu zdalnego nie mają problemów z logowaniem.

## <a name="protect-your-data"></a>Ochrona danych

W tej sekcji opisano funkcje zabezpieczeń Data Box Edge chroniące dane przesyłane w trakcie przesyłania i przechowywania.

### <a name="protect-data-at-rest"></a>Ochrona danych magazynowanych

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- Funkcja BitLocker XTS-AES 256-bitowe szyfrowanie jest używane do ochrony danych lokalnych.


### <a name="protect-data-in-flight"></a>Ochrona danych w locie

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Ochrona danych za pomocą kont magazynu

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Regularnie Obróć i [Synchronizuj klucze konta magazynu](data-box-edge-manage-shares.md#sync-storage-keys) , aby chronić konto magazynu przed nieautoryzowanymi użytkownikami.

## <a name="manage-personal-information"></a>Zarządzanie informacjami osobistymi

Usługa Data Box Edge zbiera informacje osobiste w następujących scenariuszach:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Aby wyświetlić listę użytkowników, którzy mogą uzyskać dostęp do udziału lub go usunąć, wykonaj kroki opisane w sekcji [Zarządzanie udziałami na Data Box Edge](data-box-edge-manage-shares.md).

Aby uzyskać więcej informacji, zapoznaj się z zasadami ochrony prywatności firmy Microsoft w [Centrum zaufania](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Następne kroki

[Wdrażanie urządzenia Data Box Edge](data-box-edge-deploy-prep.md)
