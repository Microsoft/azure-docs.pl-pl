---
title: Wdróż urządzenie z serii StorSimple 8000 w Azure Portal
description: W tym artykule opisano kroki i najlepsze rozwiązania dotyczące wdrażania urządzenia StorSimple 8000 Series z aktualizacją Update 3 lub nowszą oraz usługi Menedżer urządzeń StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: alkohli
ms.openlocfilehash: 2c9feb1131f6d2d0eb75ac71e27dc46c226c52c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94961060"
---
# <a name="deploy-your-on-premises-storsimple-device-update-3-and-later"></a>Wdrażanie lokalnego urządzenia StorSimple (z aktualizacją Update 3 lub nowszą)

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Omówienie
Witamy w procesie wdrażania urządzenia Microsoft Azure StorSimple. Te samouczki wdrażania dotyczą urządzenia StorSimple 8000 Series z aktualizacją Update 3 lub nowszą. W tej serii samouczków uwzględniono listę kontrolną i wymagania wstępne dotyczące konfiguracji oraz szczegółowe opisy kroków konfiguracji dla urządzenia StorSimple.

Informacje podane w tych samouczkach mają zastosowanie po zapoznaniu się ze środkami ostrożności i dopiero wówczas, gdy urządzenie StorSimple zostało rozpakowane i zamontowane w stojaku oraz podłączono do niego kable. Jeśli nadal trzeba wykonać te zadania, należy rozpocząć od sprawdzenia [środków ostrożności](storsimple-8000-safety.md). Wykonaj instrukcje dotyczące rozpakowania określonego urządzenia, zamontowania go w stojaku oraz podłączania do niego kabli.

* [Rozpakowywanie, montowanie w stojaku i podłączanie kabli do urządzenia 8100](storsimple-8100-hardware-installation.md)
* [Rozpakowywanie, montowanie w stojaku i podłączanie kabli do urządzenia 8600](storsimple-8600-hardware-installation.md)

Do ukończenia procesu instalacji i konfiguracji niezbędne są uprawnienia administratora. Przed rozpoczęciem warto przejrzeć listę kontrolną dotyczącą konfiguracji. Proces wdrażania i konfiguracji może potrwać pewien czas.

> [!NOTE]
> Informacje na temat wdrażania urządzenia StorSimple opublikowane w witrynie platformy Microsoft Azure w sieci Web dotyczą tylko urządzeń z serii StorSimple 8000. Aby uzyskać pełne informacje na temat urządzeń z serii 7000, przejdź do: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com) . Informacje dotyczące wdrażania urządzeń z serii 7000 można znaleźć w [przewodniku Szybki start do systemu StorSimple](http://onlinehelp.storsimple.com/111_Appliance/). 


## <a name="deployment-steps"></a>Kroki wdrażania
Wykonaj wymagane kroki, aby skonfigurować urządzenie StorSimple i połączyć je z usługą Menedżer urządzeń StorSimple. Oprócz kroków wymaganych istnieją również opcjonalne kroki i procedury, które być może trzeba będzie wykonać podczas wdrażania. W szczegółowych instrukcjach dotyczących wdrażania wskazano, w którym momencie należy wykonać poszczególne kroki opcjonalne.

| Krok | Opis |
| --- | --- |
| **WYMAGANY** |Te czynności muszą zostać wykonane w ramach przygotowań do przyszłego wdrożenia. |
| [Lista kontrolna konfiguracji wdrożenia](#deployment-configuration-checklist) |Ta lista kontrolna umożliwia zbieranie i rejestrowanie informacji przed wdrożeniem i w jego trakcie. |
| [Wymagania wstępne dotyczące wdrażania](#deployment-prerequisites) |Służą do sprawdzania, czy środowisko jest gotowe do przeprowadzenia wdrożenia. |
|  | |
| **WDROŻENIE KROK PO KROKU** |Te kroki są wymagane do wdrożenia urządzenia StorSimple w środowisku produkcyjnym. |
| [Krok 1. Tworzenie nowej usługi](#step-1-create-a-new-service) |Skonfiguruj magazyn i zarządzanie chmurą dla danego urządzenia StorSimple. *Pomiń ten krok, jeśli masz istniejącą usługę dla innych urządzeń StorSimple*. |
| [Krok 2. Pobieranie klucza rejestracji usługi](#step-2-get-the-service-registration-key) |Użyj tego klucza do zarejestrowania urządzenia StorSimple i połączenia go z usługą zarządzania. |
| [Krok 3. Konfigurowanie i rejestrowanie urządzenia za pomocą program Windows PowerShell dla usługi StorSimple](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |Aby ukończyć instalację za pomocą usługi zarządzania, połącz urządzenie z siecią i zarejestruj je przy użyciu platformy Azure. |
| [Krok 4. Przeprowadzenie minimalnej konfiguracji urządzenia](#step-4-complete-minimum-device-setup)</br>[Najlepsze rozwiązanie: aktualizacja urządzenia StorSimple](#scan-for-and-apply-updates) |Skorzystaj z usługi zarządzania, aby skonfigurować urządzenie i umożliwić przechowywanie w nim danych. |
| [Krok 5. Tworzenie kontenera woluminów](#step-5-create-a-volume-container) |Utwórz kontener, aby umożliwić inicjowanie obsługi woluminów. Kontener woluminów obejmuje ustawienia konta magazynu, przepustowości i szyfrowania wszystkich woluminów, które zawiera. |
| [Krok 6. Tworzenie woluminu](#step-6-create-a-volume) |Aprowizuj woluminy magazynu na urządzeniu StorSimple dla swoich serwerów. |
| [Krok 7. Instalowanie, inicjowanie i formatowanie woluminu](#step-7-mount-initialize-and-format-a-volume)</br>[Opcjonalne: Konfigurowanie funkcji MPIO](storsimple-8000-configure-mpio-windows-server.md) |Połącz serwery z magazynem iSCSI udostępnianym przez urządzenie. Możesz opcjonalnie skonfigurować wielościeżkowe wejście/wyjście, aby upewnić się, że serwery tolerują błędy linków, sieci i interfejsu. |
| [Krok 8. Tworzenie kopii zapasowej](#step-8-take-a-backup) |Skonfiguruj zasady tworzenia kopii zapasowej, aby chronić dane. |
|  | |
| **INNE PROCEDURY** |Te procedury mogą okazać się potrzebne podczas wdrażania rozwiązania. |
| [Konfigurowanie nowego konta magazynu dla usługi](#configure-a-new-storage-account-for-the-service) | |
| [Łączenie z konsolą szeregową urządzenia przy użyciu programu PuTTY](#use-putty-to-connect-to-the-device-serial-console) | |
| [Pobieranie nazwy IQN hosta z systemem Windows Server](#get-the-iqn-of-a-windows-server-host) | |
| [Utwórz ręczną kopię zapasową](#create-a-manual-backup) | |


## <a name="deployment-configuration-checklist"></a>Lista kontrolna dotycząca konfiguracji wdrożenia
Przed wdrożeniem urządzenia należy zebrać informacje w celu skonfigurowania oprogramowania na urządzeniu StorSimple. Wcześniejsze przygotowanie niektórych z tych informacji usprawnia proces wdrażania urządzenia StorSimple w środowisku użytkownika. Ta lista kontrolna umożliwia notowanie niezbędnych szczegółów konfiguracji podczas wdrażania urządzenia.

* [Pobieranie listy kontrolnej dotyczącej konfiguracji wdrożenia urządzenia StorSimple](https://www.microsoft.com/download/details.aspx?id=49159)

## <a name="deployment-prerequisites"></a>Wymagania wstępne dotyczące wdrożenia
Poniższe sekcje zawierają opis wymagań wstępnych dotyczących konfiguracji usługi Menedżer urządzeń StorSimple oraz urządzenia StorSimple.

### <a name="for-the-storsimple-device-manager-service"></a>Usługa Menedżer urządzeń StorSimple
Przed rozpoczęciem upewnij się, że:

* Masz konto Microsoft z poświadczeniami dostępu.
* Masz konto magazynu platformy Microsoft Azure z poświadczeniami dostępu.
* Subskrypcja platformy Microsoft Azure dla usługi Menedżer urządzeń StorSimple została włączona. Subskrypcję należy zakupić w ramach umowy [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/).
* Masz dostęp do oprogramowania służącego do emulacji terminala, takiego jak PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Urządzenie w centrum danych
Przed skonfigurowaniem urządzenia upewnij się, że urządzenie zostało całkowicie rozpakowane i zamontowane w stojaku oraz podłączono wszystkie kable umożliwiające dostęp do zasilania, sieci i dostęp szeregowy, zgodnie z opisem w części

* [Rozpakowywanie, montowanie w stojaku i podłączanie kabli do urządzenia 8100](storsimple-8100-hardware-installation.md)
* [Rozpakowywanie, montowanie w stojaku i podłączanie kabli do urządzenia 8600](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>Sieć w centrum danych
Przed rozpoczęciem upewnij się, że:

* Porty w zaporze centrum danych zostały otwarte w celu zezwolenia na ruch związany z interfejsem iSCSI i chmurą, zgodnie z opisem w temacie [Networking requirements for your StorSimple device](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device) (Wymagania dotyczące pracy w sieci dla urządzenia StorSimple).

## <a name="step-by-step-deployment"></a>Wdrożenie krok po kroku
Poniższe instrukcje krok po kroku dotyczą wdrażania urządzenia StorSimple w centrum danych.

## <a name="step-1-create-a-new-service"></a>Krok 1. Tworzenie nowej usługi
Usługa Menedżer urządzeń StorSimple umożliwia zarządzanie wieloma urządzeniami StorSimple. Wykonaj poniższe kroki, aby utworzyć wystąpienie usługi Menedżer urządzeń StorSimple.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]

> [!IMPORTANT]
> Jeśli nie włączono automatycznego tworzenia konta magazynu przy użyciu usługi, po pomyślnym utworzeniu usługi musisz utworzyć co najmniej jedno konto magazynu. To konto magazynu jest używane podczas tworzenia kontenera woluminów.
>
> * Jeśli nie utworzono automatycznie konta magazynu, przejdź do kroku [Konfigurowanie nowego konta magazynu dla usługi](#configure-a-new-storage-account-for-the-service) w celu uzyskania szczegółowych informacji.
> * Jeśli włączono automatyczne tworzenie konta magazynu, przejdź do części [Krok 2. Pobieranie klucza rejestracji usługi](#step-2-get-the-service-registration-key).


## <a name="step-2-get-the-service-registration-key"></a>Krok 2. Pobieranie klucza rejestracji usługi
Po skonfigurowaniu i uruchomieniu usługi Menedżer urządzeń StorSimple musisz pobrać klucz rejestracji usługi. Ten klucz służy do rejestrowania urządzenia StorSimple i łączenia go z usługą.

Wykonaj poniższe czynności w witrynie Azure Portal.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Krok 3. Konfigurowanie i rejestrowanie urządzenia za pomocą programu Windows PowerShell dla urządzenia StorSimple
Użyj programu Windows PowerShell dla urządzenia StorSimple do przeprowadzenia konfiguracji początkowej urządzenia StorSimple, jak opisano w poniższej procedurze. Do wykonania tego kroku niezbędne jest użycie oprogramowania do emulacji terminala. Aby uzyskać więcej informacji, zobacz część [Łączenie z konsolą szeregową urządzenia przy użyciu programu PuTTY](#use-putty-to-connect-to-the-device-serial-console).

[!INCLUDE [storsimple-8000-configure-and-register-device-u2](../../includes/storsimple-8000-configure-and-register-device-u2.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Krok 4. Przeprowadzenie minimalnej konfiguracji urządzenia
Do przeprowadzenia minimalnej konfiguracji urządzenia StorSimple konieczne jest: 

* Podaj przyjazną nazwę dla urządzenia.
* Ustaw strefę czasową urządzenia.
* Przypisanie stałych adresów IP do obu kontrolerów.

Aby przeprowadzić minimalną konfigurację urządzenia, wykonaj poniższe kroki w witrynie Azure Portal.

[!INCLUDE [storsimple-8000-complete-minimum-device-setup-u2](../../includes/storsimple-8000-complete-minimum-device-setup-u2.md)]

Po ukończeniu minimalnej konfiguracji urządzenia najlepszym rozwiązaniem jest [wyszukanie i zastosowanie najnowszych aktualizacji](#scan-for-and-apply-updates).

## <a name="step-5-create-a-volume-container"></a>Krok 5. Tworzenie kontenera woluminów
Kontener woluminów obejmuje ustawienia konta magazynu, przepustowości i szyfrowania wszystkich woluminów, które zawiera. Kontener woluminów należy utworzyć przed zainicjowaniem woluminów na urządzeniu StorSimple.

Wykonaj poniższe czynności w witrynie Azure Portal, aby utworzyć kontener woluminów.

[!INCLUDE [storsimple-8000-create-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Krok 6. Tworzenie woluminu
Po utworzeniu kontenera woluminów możesz zainicjować obsługę woluminu magazynu w urządzeniu StorSimple dla serwerów. Wykonaj poniższe czynności w witrynie Azure Portal, aby utworzyć wolumin.

> [!IMPORTANT]
> Za pomocą usługi Menedżer urządzeń StorSimple można tworzyć woluminy inicjowane w pełni i elastycznie. Nie można jednak tworzyć woluminów inicjowanych częściowo.


[!INCLUDE [storsimple-8000-create-volume](../../includes/storsimple-8000-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Krok 7. Instalowanie, inicjowanie i formatowanie woluminu
Poniższe kroki wykonuje się na hoście systemu Windows Server.

> [!IMPORTANT]
> * W celu zapewnienia dużej dostępności rozwiązania StorSimple warto przed skonfigurowaniem usługi iSCSI skonfigurować wielościeżkowe wejście/wyjście (MPIO) na serwerach hosta (opcjonalnie). Dzięki skonfigurowaniu wielościeżkowego wejścia/wyjścia (MPIO) na serwerach hosta można mieć pewność, że serwery tolerują błędy linków, sieci lub interfejsu.
> * Instrukcje dotyczące instalowania i konfigurowania usługi iSCSI i wielościeżkowego wejścia/wyjścia (MPIO) na hoście systemu Windows Server można znaleźć w temacie [Configure MPIO for your StorSimple device](storsimple-8000-configure-mpio-windows-server.md) (Konfigurowanie wielościeżkowego wejścia/wyjścia dla urządzenia StorSimple). Informacje te dotyczą również kroków opisujących instalowanie, inicjowanie i formatowanie woluminów StorSimple.
> * Instrukcje dotyczące instalowania i konfigurowania usługi iSCSI i wielościeżkowego wejścia/wyjścia (MPIO) na hoście systemu Linux można znaleźć w temacie [Configure MPIO for your StorSimple Linux host](storsimple-configure-mpio-on-linux.md) (Konfigurowanie wielościeżkowego wejścia/wyjścia dla hosta z systemem Linux urządzenia StorSimple).

Jeśli nie chcesz konfigurować wielościeżkowego wejścia/wyjścia (MPIO), wykonaj poniższe kroki, aby zainstalować, zainicjować i sformatować woluminy StorSimple na hoście systemu Windows Server.

[!INCLUDE [storsimple-8000-mount-initialize-format-volume](../../includes/storsimple-8000-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Krok 8. Tworzenie kopii zapasowej
Kopie zapasowe oferują ochronę woluminów w określonym momencie i udoskonalają możliwości odzyskiwania przy równoczesnym zminimalizowaniu czasów przywracania. W urządzeniu StorSimple można wykonywać dwa typy kopii zapasowych: migawki lokalne i migawki w chmurze. Kopie obu typów można tworzyć jako **zaplanowane** lub **ręczne**.

Wykonaj poniższe czynności w witrynie Azure Portal, aby utworzyć zaplanowaną kopię zapasową.

[!INCLUDE [storsimple-8000-take-backup](../../includes/storsimple-8000-take-backup.md)]

Kopię zapasową można utworzyć ręcznie w dowolnym momencie. Informacje na temat procedur można znaleźć w części [Ręczne tworzenie kopii zapasowej](#create-a-manual-backup).

Konfiguracja urządzenia została ukończona.

## <a name="configure-a-new-storage-account-for-the-service"></a>Konfigurowanie nowego konta magazynu dla usługi
Jest to krok opcjonalny, który należy wykonać tylko, jeśli nie włączono automatycznego tworzenia konta magazynu przy użyciu usługi. Do utworzenia kontenera woluminów StorSimple wymagane jest konto magazynu platformy Microsoft Azure.

Instrukcje krok po kroku dotyczące tworzenia konta usługi Azure Storage w innym regionie można znaleźć w temacie [About Azure Storage Accounts](../storage/common/storage-account-create.md) (Informacje o kontach usługi Azure Storage).

Wykonaj poniższe kroki w witrynie Azure Portal na stronie **usługi Menedżer urządzeń StorSimple**.

[!INCLUDE [storsimple-8000-configure-new-storage-account-u2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Łączenie z konsolą szeregową urządzenia przy użyciu programu PuTTY
Aby nawiązać połączenie z programem Windows PowerShell dla urządzenia StorSimple, należy użyć oprogramowania do emulacji terminala, takiego jak PuTTY. Programu PuTTY można używać w przypadku uzyskiwania dostępu do urządzenia bezpośrednio za pośrednictwem konsoli szeregowej lub przez otwarcie sesji telnet z komputera zdalnego.

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Wyszukiwanie aktualizacji i ich stosowanie
Aktualizowanie urządzenia może potrwać kilka godzin. Aby uzyskać szczegółowe instrukcje dotyczące sposobu instalowania najnowszej aktualizacji, przejdź do artykułu [Instalowanie aktualizacji Update 5](storsimple-8000-install-update-5.md).


## <a name="get-the-iqn-of-a-windows-server-host"></a>Pobieranie nazwy IQN hosta z systemem Windows Server
Wykonaj poniższe kroki, aby pobrać kwalifikowaną nazwę iSCSI (IQN) hosta z systemem Windows, na którym uruchomiono system Windows Server® 2012.

[!INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Ręczne tworzenie kopii zapasowej
Wykonaj poniższe kroki w witrynie Azure Portal, aby na żądanie ręcznie utworzyć kopię zapasową pojedynczego woluminu w urządzeniu StorSimple.

[!INCLUDE [Create a manual backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="view-the-pinout-diagram-for-serial-cable-for-storsimple"></a>Wyświetlanie schematu połączeń kabla szeregowego dla urządzenia StorSimple
Poniższy schemat połączeń może być używany na potrzeby kabla konsoli szeregowej urządzenia StorSimple.

W tym miejscu łącznik żeński DB9 to P1, a łącznik 3,5 mm to P2.

![Schemat połączeń 1 dla kabla konsoli szeregowej urządzenia StorSimple](./media/storsimple-8000-deployment-walkthrough-u2/pinout-diagram1.png)

Końcówka gniazda stereo jest traktowana jako PIN 3 RX, środek to PIN 2 TX, a podstawa to PIN 1 GND, jak pokazano na poniższym schemacie.

![Schemat połączeń 2 dla kabla konsoli szeregowej urządzenia StorSimple](./media/storsimple-8000-deployment-walkthrough-u2/pinout-diagram2.png)



## <a name="next-steps"></a>Następne kroki
* [Configure a StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md) (Konfigurowanie urządzenia StorSimple w chmurze).
* [Zarządzanie urządzeniem StorSimple przy użyciu usługi StorSimple Device Manager](storsimple-8000-manager-service-administration.md).