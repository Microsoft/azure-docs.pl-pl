---
title: Dostęp do panelu sterowania avere vFXT — Azure
description: Jak nawiązać połączenie z klastrem vFXT i panelem sterowania avere w przeglądarce w celu skonfigurowania vFXT avere
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: 79e7c5db2a2c445ae740a21744a0bdfe0736c01a
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342437"
---
# <a name="access-the-vfxt-cluster"></a>Uzyskiwanie dostępu do klastra vFXT

Aby dostosować ustawienia klastra i monitorować klaster, użyj panelu sterowania avere. Avere Panel sterowania jest interfejsem graficznym opartym na przeglądarce.

Ponieważ klaster vFXT znajduje się w prywatnej sieci wirtualnej, należy utworzyć tunel SSH lub użyć innej metody w celu uzyskania dostępu do adresu IP zarządzania klastrem.

Istnieją dwa podstawowe kroki:

1. Utwórz połączenie między stacją roboczą a prywatną siecią wirtualną
1. Załaduj Panel sterowania klastra w przeglądarce internetowej

> [!NOTE]
> W tym artykule przyjęto założenie, że został ustawiony publiczny adres IP na kontrolerze klastra lub na innej maszynie wirtualnej w sieci wirtualnej klastra. W tym artykule opisano sposób korzystania z tej maszyny wirtualnej jako hosta w celu uzyskania dostępu do klastra. Jeśli używasz sieci VPN lub ExpressRoute do dostępu do sieci wirtualnej, Pomiń, aby [nawiązać połączenie z panelem sterowania avere](#connect-to-the-avere-control-panel-in-a-browser).

Przed połączeniem upewnij się, że para klucz publiczny/prywatny SSH użyta podczas tworzenia kontrolera klastra jest zainstalowana na komputerze lokalnym. Zapoznaj się z dokumentacją kluczy SSH dla [systemu Windows](../virtual-machines/linux/ssh-from-windows.md) lub [Linux](../virtual-machines/linux/mac-create-ssh-keys.md) , jeśli potrzebujesz pomocy. Jeśli użyto hasła zamiast klucza publicznego, po nawiązaniu połączenia zostanie wyświetlony monit o wprowadzenie go.

## <a name="create-an-ssh-tunnel"></a>Tworzenie tunelu SSH

Tunel SSH można utworzyć z wiersza polecenia systemu klienckiego opartego na systemie Linux lub Windows 10.

Użyj polecenia tunelowania SSH w tej postaci:

SSH-L *local_port*:*cluster_mgmt_ip*: 443 *controller_username* \@ *controller_public_IP*

To polecenie nawiązuje połączenie z adresem IP zarządzania klastrem za pomocą adresu IP kontrolera klastra.

Przykład:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

Uwierzytelnianie jest wykonywane automatycznie, jeśli do utworzenia klastra użyto klucza publicznego SSH, a odpowiedni klucz jest zainstalowany w systemie klienta. Jeśli użyto hasła, system wyświetli monit o jego wprowadzenie.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Łączenie z panelem sterowania avere w przeglądarce

Ten krok powoduje użycie przeglądarki sieci Web w celu nawiązania połączenia z narzędziem konfiguracji w klastrze vFXT.

* W przypadku połączenia tunelowego SSH Otwórz przeglądarkę internetową i przejdź do `https://127.0.0.1:8443` .

  Podczas tworzenia tunelu nawiązano połączenie z adresem IP klastra, więc wystarczy użyć adresu IP localhost w przeglądarce. Jeśli użyto portu lokalnego innego niż 8443, zamiast tego należy użyć numeru portu.

* Jeśli używasz sieci VPN lub ExpressRoute do uzyskiwania dostępu do klastra, przejdź do adresu IP zarządzania klastrem w przeglądarce. Przykład: ``https://203.0.113.51``

W zależności od przeglądarki może być konieczne kliknięcie przycisku **Zaawansowane** i upewnienie się, że jest bezpiecznie, aby przechodzić do strony.

Wprowadź nazwę użytkownika `admin` i hasło administracyjne podane podczas tworzenia klastra.

![Zrzut ekranu przedstawiający stronę logowania avere z nazwą użytkownika admin i hasłem](media/avere-vfxt-gui-login.png)

Kliknij przycisk **Zaloguj** lub naciśnij klawisz Enter na klawiaturze.

## <a name="next-steps"></a>Następne kroki

Po zalogowaniu się do panelu sterowania klastra Włącz [obsługę](avere-vfxt-enable-support.md).