---
title: Zabezpieczenia Azure Data Box Gateway | Microsoft Docs
description: Opisuje funkcje zabezpieczeń i ochrony prywatności, które chronią Azure Data Box Gateway urządzeń wirtualnych, usług i danych, lokalnie i w chmurze.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 13d3809611714992f24a66a96c22074e69fba9bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98786660"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure Data Box Gateway zabezpieczenia i ochrona danych

Bezpieczeństwo jest ważnym problemem w przypadku przyjęcia nowej technologii, szczególnie jeśli technologia jest używana z danymi poufnymi lub zastrzeżonymi. Azure Data Box Gateway pomaga upewnić się, że tylko autoryzowane jednostki mogą wyświetlać, modyfikować i usuwać dane.

W tym artykule opisano funkcje zabezpieczeń Azure Data Box Gateway chroniące poszczególne składniki rozwiązań i przechowywane w nich dane.

Data Box Gateway rozwiązanie składa się z czterech głównych składników, które współpracują ze sobą:

- **Usługa Data Box Gateway hostowana na platformie Azure**. Zasób zarządzania służący do tworzenia kolejności urządzeń, konfigurowania urządzenia, a następnie śledzenia kolejności do ukończenia.
- **Data Box Gateway urządzenia**. Urządzenie wirtualne, które można udostępnić w funkcji hypervisor systemu. To urządzenie wirtualne służy do importowania danych lokalnych na platformę Azure.
- **Klienci/hosty połączeni z urządzeniem**. Klienci w infrastrukturze, którzy łączą się z urządzeniem Data Box Gateway i zawierają dane, które muszą być chronione.
- **Magazyn w chmurze**. Lokalizacja na platformie Azure w chmurze, w której są przechowywane dane. Ta lokalizacja jest zazwyczaj kontem magazynu połączonym z tworzonym zasobem Data Box Gateway.

## <a name="data-box-gateway-service-protection"></a>Data Box Gateway ochronę usługi

Usługa Data Box Gateway to usługa zarządzania hostowana na platformie Azure. Usługa służy do konfigurowania urządzenia i zarządzania nim.

[!INCLUDE [data-box-gateway-service-protection](../../includes/data-box-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Data Box Gateway ochrony urządzeń

Urządzenie Data Box Gateway jest urządzeniem wirtualnym, które jest obsługiwane w funkcji hypervisor systemu lokalnego, który jest udostępniany przez użytkownika. Urządzenie służy do przesyłania danych do platformy Azure. Twoje urządzenie:

- Potrzebuje klucza aktywacji, aby uzyskać dostęp do usługi Azure Stack EDGE Pro/Data Box Gateway.
- Jest chronione przez cały czas przy użyciu hasła urządzenia.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Ochrona urządzenia za pomocą klucza aktywacji

Tylko autoryzowane urządzenie Data Box Gateway może przyłączyć się do usługi Data Box Gateway utworzonej w ramach subskrypcji platformy Azure. Aby autoryzować urządzenie, należy użyć klucza aktywacji w celu aktywowania urządzenia za pomocą usługi Data Box Gateway.

[!INCLUDE [data-box-gateway-activation-key](../../includes/data-box-gateway-activation-key.md)]

Aby uzyskać więcej informacji, zobacz [Uzyskiwanie klucza aktywacji](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Ochrona urządzenia za pomocą hasła

Hasła zapewniają, że dostęp do danych mają tylko autoryzowani użytkownicy. Data Box Gateway urządzenia są uruchamiane w stanie zablokowanym.

Oto co możesz zrobić:

- Połącz się z lokalnym interfejsem użytkownika sieci Web urządzenia za pośrednictwem przeglądarki, a następnie podaj hasło, aby zalogować się na urządzeniu.
- Zdalne łączenie się z interfejsem programu PowerShell urządzenia za pośrednictwem protokołu HTTP. Zdalne zarządzanie jest domyślnie włączone. Następnie można podać hasło urządzenia, aby zalogować się na urządzeniu. Aby uzyskać więcej informacji, zobacz [zdalne łączenie z urządzeniem Data Box Gateway](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-gateway-password-best-practices](../../includes/data-box-gateway-password-best-practices.md)]
- [Zmień hasło](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access)przy użyciu lokalnego interfejsu użytkownika sieci Web. Jeśli zmienisz hasło, pamiętaj o poinformowaniu wszystkich użytkowników dostępu zdalnego, aby nie mieli problemów z logowaniem.

## <a name="protect-your-data"></a>Ochrona danych

W tej sekcji opisano funkcje zabezpieczeń Data Box Gateway chroniące dane przesyłane w trakcie przesyłania i przechowywania.

### <a name="protect-data-at-rest"></a>Ochrona danych magazynowanych

[!INCLUDE [data-box-gateway-data-rest](../../includes/data-box-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Ochrona danych w locie

[!INCLUDE [data-box-gateway-data-flight](../../includes/data-box-gateway-data-flight.md)]

### <a name="protect-data-using-storage-accounts"></a>Ochrona danych przy użyciu kont magazynu

[!INCLUDE [data-box-gateway-data-storage-accounts](../../includes/data-box-gateway-protect-data-storage-accounts.md)]

- Regularnie Obróć i [Synchronizuj klucze konta magazynu](data-box-gateway-manage-shares.md#sync-storage-keys) , aby chronić konto magazynu przed nieautoryzowanymi użytkownikami.

### <a name="protect-the-device-data-using-bitlocker"></a>Ochrona danych urządzenia przy użyciu funkcji BitLocker

Aby zabezpieczyć dyski wirtualne na maszynie wirtualnej Data Box Gateway, zalecamy włączenie funkcji BitLocker. Domyślnie funkcja BitLocker nie jest włączona. Aby uzyskać więcej informacji, zobacz:

- [Ustawienia szyfrowania pomocy technicznej w Menedżerze funkcji Hyper-V](/windows-server/virtualization/hyper-v/learn-more/generation-2-virtual-machine-security-settings-for-hyper-v#encryption-support-settings-in-hyper-v-manager)
- [Obsługa funkcji BitLocker na maszynie wirtualnej](https://kb.vmware.com/s/article/2036142)

## <a name="manage-personal-information"></a>Zarządzanie informacjami osobistymi

Usługa Data Box Gateway zbiera informacje osobiste w następujących scenariuszach:

[!INCLUDE [data-box-gateway-manage-personal-data](../../includes/data-box-gateway-manage-personal-data.md)]

Aby wyświetlić listę użytkowników, którzy mogą uzyskać dostęp do udziału lub go usunąć, wykonaj kroki opisane w sekcji [Zarządzanie udziałami na Data Box Gateway](data-box-gateway-manage-shares.md).

Aby uzyskać więcej informacji, zapoznaj się z zasadami ochrony prywatności firmy Microsoft w [Centrum zaufania](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Następne kroki

[Wdrażanie urządzenia Data Box Gateway](data-box-gateway-deploy-prep.md)
