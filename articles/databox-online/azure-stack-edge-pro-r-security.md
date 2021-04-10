---
title: Azure Stack krawędź Pro R zabezpieczenia | Microsoft Docs
description: W tym artykule opisano funkcje zabezpieczeń i ochrony prywatności, które chronią Azure Stack Azure Stack Edge przy użyciu urządzeń i usług, usługi i danych w chmurze.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: bd90a16c09dce65115cea2f097d18f2e0ced931a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632037"
---
# <a name="security-and-data-protection-for-azure-stack-edge-pro-r-and-azure-stack-edge-mini-r"></a>Bezpieczeństwo i ochrona danych dla Azure Stack EDGE Pro R i Azure Stack Edge mini R

[!INCLUDE [applies-to-pro-r-and-mini-r--skus](../../includes/azure-stack-edge-applies-to-pro-r-mini-r-sku.md)]

Bezpieczeństwo jest ważnym problemem w przypadku przyjęcia nowej technologii, szczególnie jeśli technologia jest używana z danymi poufnymi lub zastrzeżonymi. Azure Stack EDGE Pro R i Azure Stack Edge w wersji mini R pomaga zapewnić, że tylko autoryzowane jednostki mogą wyświetlać, modyfikować i usuwać dane.

W tym artykule opisano funkcje zabezpieczeń w programie Azure Stack EDGE Pro R i Azure Stack Edge, które pomagają chronić poszczególne składniki rozwiązań i przechowywane w nich dane.

Rozwiązanie składa się z czterech głównych składników, które współpracują ze sobą:

- **Usługa Azure Stack Edge hostowana w chmurze publicznej lub Azure Governmentowej platformy Azure**. Zasób zarządzania służący do tworzenia kolejności urządzeń, konfigurowania urządzenia, a następnie śledzenia kolejności do ukończenia.
- **Azure Stack urządzenia** z niewytrzymałą krawędzią. Wzmocnione, fizyczne urządzenie, które jest dostarczane do Ciebie, aby można było zaimportować dane lokalne do chmury publicznej lub usług Azure Government platformy Azure. Urządzenie może być Azure Stack EDGE Pro R lub Azure Stack Edge mini R.
- **Klienci/hosty połączeni z urządzeniem**. Klienci w infrastrukturze, którzy łączą się z urządzeniem i zawierają dane, które muszą być chronione.
- **Magazyn w chmurze**. Lokalizacja na platformie Azure w chmurze, w której są przechowywane dane. Ta lokalizacja jest zazwyczaj kontem magazynu połączonym z utworzonym zasobem Azure Stack Edge.

## <a name="service-protection"></a>Ochrona usługi

Usługa Edge Azure Stack to usługa zarządzania hostowana na platformie Azure. Usługa służy do konfigurowania urządzenia i zarządzania nim.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-service-protection.md)]

## <a name="device-protection"></a>Ochrona urządzenia

To urządzenie jest urządzeniem lokalnym, które pomaga przekształcić dane, przetwarzając je lokalnie, a następnie wysyłając je na platformę Azure. Twoje urządzenie:

- Potrzebuje klucza aktywacji, aby uzyskać dostęp do usługi Azure Stack Edge.
- Jest chronione przez cały czas przy użyciu hasła urządzenia.
- Jest urządzeniem zablokowanym. Kontroler zarządzania płytą główną (BMC) urządzenia i system BIOS są chronione hasłem. Kontroler BMC jest chroniony przez ograniczony dostęp użytkownika.
- Ma włączony bezpieczny rozruch, który gwarantuje, że urządzenie zostanie uruchomione tylko przy użyciu zaufanego oprogramowania udostępnianego przez firmę Microsoft.
- Uruchamia kontrolę aplikacji w programie Windows Defender (WDAC). WDAC umożliwia uruchamianie tylko zaufanych aplikacji, które zostały zdefiniowane w zasadach integralności kodu.
- Ma Trusted Platform Module (TPM), który wykonuje funkcje związane z zabezpieczeniami. W każdym przypadku moduł TPM zarządza i chroni wpisy tajne oraz dane, które muszą zostać utrwalone na urządzeniu.
- Na urządzeniu są otwierane tylko wymagane porty i wszystkie inne porty są blokowane. Aby uzyskać więcej informacji, zobacz listę [wymagań dotyczących portów dla urządzenia](azure-stack-edge-pro-r-system-requirements.md) .
- Jest rejestrowany cały dostęp do sprzętu urządzenia oraz oprogramowania. 
    - W przypadku oprogramowania urządzenia są zbierane domyślne Dzienniki zapory dla ruchu przychodzącego i wychodzącego z urządzenia. Te dzienniki są powiązane z pakietem pomocy technicznej.
    - W przypadku sprzętu urządzenia wszystkie zdarzenia obudowy urządzenia, takie jak otwieranie i zamykanie obudowy urządzenia, są rejestrowane na urządzeniu.

    Aby uzyskać więcej informacji na temat określonych dzienników, które zawierają zdarzenia włamania dotyczące sprzętu i oprogramowania oraz sposób pobierania dzienników, przejdź do obszaru [zbieranie zaawansowanych dzienników zabezpieczeń](azure-stack-edge-gpu-troubleshoot.md).


### <a name="protect-the-device-via-activation-key"></a>Ochrona urządzenia za pomocą klucza aktywacji

Do przyłączenia do usługi Azure Stack Edge, która została utworzona w ramach subskrypcji platformy Azure, może być dołączane tylko autoryzowane Azure Stack Edge i Azure Stack Edge. Aby autoryzować urządzenie, należy użyć klucza aktywacji w celu aktywowania urządzenia za pomocą usługi Azure Stack Edge.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-activation-key.md)]

Aby uzyskać więcej informacji, zobacz [Uzyskiwanie klucza aktywacji](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Ochrona urządzenia za pomocą hasła

Hasła zapewniają, że dostęp do danych mają tylko autoryzowani użytkownicy. Azure Stack w celu zablokowania uruchamiania urządzeń z systemem EDGE Pro.

Oto co możesz zrobić:

- Połącz się z lokalnym interfejsem użytkownika sieci Web urządzenia za pośrednictwem przeglądarki, a następnie podaj hasło, aby zalogować się na urządzeniu.
- Zdalne łączenie się z interfejsem PowerShell urządzenia za pośrednictwem protokołu HTTP. Zdalne zarządzanie jest domyślnie włączone. Zdalne zarządzanie jest również skonfigurowane pod kątem używania wystarczającej liczby czynności administracyjnych (JEA) w celu ograniczenia możliwości, które użytkownicy mogą wykonywać. Następnie można podać hasło urządzenia, aby zalogować się na urządzeniu. Aby uzyskać więcej informacji, zobacz [zdalne nawiązywanie połączenia z urządzeniem](azure-stack-edge-gpu-connect-powershell-interface.md).
- Lokalny użytkownik graniczny na urządzeniu ma ograniczony dostęp do urządzenia w celu konfiguracji początkowej i rozwiązywania problemów. Do zasobów w chmurze można uzyskać dostęp do obciążeń obliczeniowych uruchomionych na urządzeniu, transferu danych i magazynu z portalu publicznego lub administracji centralnej platformy Azure.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-password-best-practices.md)]
- [Zmień hasło](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#change-device-password)przy użyciu lokalnego interfejsu użytkownika sieci Web. Jeśli zmienisz hasło, upewnij się, że wszyscy użytkownicy dostępu zdalnego nie mają problemów z logowaniem.

### <a name="establish-trust-with-the-device-via-certificates"></a>Ustanawianie relacji zaufania z urządzeniem za pośrednictwem certyfikatów

Azure Stack urządzenia z niezrównaną krawędzią pozwala korzystać z własnych certyfikatów i instalować je do użycia dla wszystkich publicznych punktów końcowych. Aby uzyskać więcej informacji, przejdź do pozycji [Przekaż certyfikat](azure-stack-edge-gpu-manage-certificates.md#upload-certificates). Aby zapoznać się z listą wszystkich certyfikatów, które można zainstalować na urządzeniu, przejdź do obszaru [Zarządzanie certyfikatami na urządzeniu](azure-stack-edge-gpu-manage-certificates.md).

- Podczas konfigurowania obliczeń na urządzeniu tworzone jest urządzenie IoT i urządzenie IoT Edge. Do tych urządzeń są automatycznie przypisywane klucze dostępu symetrycznego. Ze względów bezpieczeństwa te klucze są regularnie obracane za pośrednictwem usługi IoT Hub.

## <a name="protect-your-data"></a>Ochrona danych

W tej sekcji opisano funkcje zabezpieczeń chroniące dane przesyłane w trakcie przesyłania i przechowywania.

### <a name="protect-data-at-rest"></a>Ochrona danych magazynowanych

Wszystkie dane przechowywane na urządzeniu są szyfrowane podwójnie, dostęp do danych jest kontrolowany i Po zdezaktywowaniu urządzenia dane są bezpiecznie usuwane z dysków danych.

#### <a name="double-encryption-of-data"></a>Podwójne szyfrowanie danych

Dane na dyskach są chronione przez dwie warstwy szyfrowania:

- Pierwsza warstwa szyfrowania to szyfrowanie BitLocker XTS-AES 256-bitowe na woluminach danych.
- Druga warstwa to dyski twarde z wbudowanym szyfrowaniem.
- Wolumin systemu operacyjnego zawiera funkcję BitLocker jako pojedynczą warstwę szyfrowania.

> [!NOTE]
> Dysk systemu operacyjnego ma jedną warstwę szyfrowanie oprogramowania funkcji BitLocker XTS-AES-256.

Po aktywowaniu urządzenia zostanie wyświetlony monit o zapisanie pliku klucza zawierającego klucze odzyskiwania, które ułatwiają odzyskiwanie danych z urządzenia, jeśli urządzenie nie zostanie uruchomione. Plik zawiera dwa klucze:

- Jeden klucz odzyskuje konfigurację urządzenia na woluminach systemu operacyjnego.
<!-- - Second key is to unlock the BitLocker on the data disks. -->
- Drugi klucz odblokowuje szyfrowanie sprzętu na dyskach danych.

> [!IMPORTANT]
> Zapisz plik klucza w bezpiecznej lokalizacji poza urządzeniem. Jeśli urządzenie nie zostanie uruchomione i nie masz klucza, może to spowodować utratę danych.

- W niektórych scenariuszach odzyskiwania zostanie wyświetlony monit o zapisanie pliku klucza, który został zapisany. 
<!--- If a node isn't booting up, you will need to perform a node replacement. You will have the option to swap the data disks from the failed node to the new node. For a 4-node device, you won't need a key file. For a 1-node device, you will be prompted to provide a key file.-->

#### <a name="restricted-access-to-data"></a>Ograniczony dostęp do danych

Dostęp do danych przechowywanych w udziałach i kontach magazynu jest ograniczony.

- Klienci protokołu SMB, którzy uzyskują dostęp do danych udostępniania, potrzebują poświadczeń użytkownika skojarzonych z udziałem. Te poświadczenia są definiowane podczas tworzenia udziału.
- Klienci NFS, którzy uzyskują dostęp do udziału, muszą mieć jawnie dodany adres IP podczas tworzenia udziału.
- Konta magazynu brzegowego tworzone na urządzeniu są lokalne i są chronione przez szyfrowanie na dyskach danych. Konta usługi Azure Storage, do których są mapowane konta magazynu brzegowego, są chronione przez subskrypcję i 2 512-bitowe klucze dostępu do magazynu skojarzone z kontem magazynu brzegowego (te klucze są inne niż te skojarzone z kontami usługi Azure Storage). Aby uzyskać więcej informacji, zobacz [Ochrona danych na kontach magazynu](#protect-data-in-storage-accounts).
- Funkcja BitLocker XTS-AES 256-bitowe szyfrowanie jest używane do ochrony danych lokalnych.

#### <a name="secure-data-erasure"></a>Bezpieczne wymazywanie danych

Gdy urządzenie przejdzie do twardej resetowania, na urządzeniu jest wykonywane bezpieczne czyszczenie danych. Bezpieczne czyszczenie wykonuje dane wymazywane na dyskach przy użyciu narzędzia NIST SP 800-88r1 przeczyszczania.

### <a name="protect-data-in-flight"></a>Ochrona danych w locie

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-data-flight.md)]

### <a name="protect-data-in-storage-accounts"></a>Ochrona danych na kontach magazynu

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-protect-data-storage-accounts.md)]

- Regularnie Obróć i [Synchronizuj klucze konta magazynu](azure-stack-edge-gpu-manage-storage-accounts.md) , aby chronić konto magazynu przed nieautoryzowanymi użytkownikami.

## <a name="manage-personal-information"></a>Zarządzanie informacjami osobistymi

Usługa Azure Stack Edge zbiera dane osobowe w następujących scenariuszach:

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-manage-personal-data.md)]

Aby wyświetlić listę użytkowników, którzy mogą uzyskać dostęp do udziału lub go usunąć, wykonaj kroki opisane w sekcji [Zarządzanie udziałami na Azure Stack Edge](azure-stack-edge-gpu-manage-shares.md).

Aby uzyskać więcej informacji, zapoznaj się z zasadami ochrony prywatności firmy Microsoft w [Centrum zaufania](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Następne kroki

[Wdrażanie urządzenia z Azure Stack EDGE Pro R](azure-stack-edge-gpu-deploy-prep.md)
