---
title: Przygotowanie portalu dla macierzy wirtualnej StorSimple
description: Pierwszy samouczek dotyczący wdrażania macierzy wirtualnej StorSimple obejmuje przygotowanie Azure Portal
author: alkohli
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c1a3d0b05f04ad8c182d428843479160666b96ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87070326"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>Wdróż wirtualną macierz StorSimple — Przygotuj Azure Portal

![Diagram przedstawiający kroki, które są niezbędne do wdrożenia macierzy wirtualnej. Pierwszy krok ma etykietę wprowadzenie i jest wyróżniony.](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)


## <a name="overview"></a>Omówienie

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Jest to pierwszy artykuł z serii samouczków wdrażania wymaganych do całkowitego wdrożenia macierzy wirtualnej jako serwera plików lub serwera iSCSI przy użyciu modelu Menedżer zasobów. W tym artykule opisano przygotowania wymagane do utworzenia i skonfigurowania usługi StorSimple Menedżer urządzeń przed zainicjowaniem obsługi wirtualnej macierzy. Ten artykuł zawiera również łącza do listy kontrolnej konfiguracji wdrożenia i wymagań wstępnych dotyczących konfiguracji.

Do ukończenia procesu instalacji i konfiguracji niezbędne są uprawnienia administratora. Zalecamy zapoznanie się z listą kontrolną konfiguracji wdrożenia przed rozpoczęciem. Przygotowanie portalu zajmuje mniej niż 10 minut.

Informacje publikowane w tym artykule dotyczą wdrażania tablic wirtualnych StorSimple w chmurze Azure Portal i Microsoft Azure Government.

### <a name="get-started"></a>Wprowadzenie
Przepływ pracy wdrożenia polega na przygotowaniu portalu, aprowizacji macierzy wirtualnej w środowisku zwirtualizowanym i zakończeniu instalacji. Aby rozpocząć pracę z wdrożeniem macierzy wirtualnej StorSimple jako serwerem plików lub serwerem iSCSI, należy zapoznać się z poniższymi zasobami.

#### <a name="deployment-articles"></a>Artykuły dotyczące wdrażania

Aby wdrożyć tablicę wirtualną StorSimple, zapoznaj się z następującymi artykułami w określonej kolejności.

| **#** | **W tym kroku** | **To zrobić...** | **I Użyj tych dokumentów.** |
| --- | --- | --- | --- |
| 1. |**Skonfiguruj Azure Portal** |Utwórz i skonfiguruj usługę StorSimple Menedżer urządzeń przed zainicjowaniem obsługi wirtualnej macierzy StorSimple. |[Przygotowywanie portalu](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**Inicjowanie obsługi administracyjnej macierzy** |W przypadku funkcji Hyper-V należy zainicjować obsługę i nawiązać połączenie z wirtualną tablicą StorSimple w systemie hosta z uruchomioną funkcją Hyper-V w systemie Windows Server 2012 R2, Windows Server 2012 lub Windows Server 2008 R2. <br></br> <br></br> W przypadku oprogramowania VMware Zainicjuj obsługę i Połącz się z wirtualną tablicą StorSimple w systemie hosta z systemem VMware ESXi 5,0, 5,5, 6,0 lub 6,5.<br></br> |[Udostępnianie macierzy wirtualnej w funkcji Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Inicjowanie obsługi administracyjnej macierzy w oprogramowaniu VMware](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**Konfigurowanie macierzy wirtualnej** |W przypadku serwera plików wykonaj konfigurację początkową, zarejestruj serwer plików StorSimple i Ukończ konfigurację urządzenia. Następnie można aprowizować udziały SMB. <br></br> <br></br> W przypadku serwera iSCSI wykonaj konfigurację początkową, zarejestruj serwer iSCSI StorSimple i Ukończ konfigurację urządzenia. Następnie można zainicjować obsługę woluminów iSCSI. |[Skonfiguruj macierz wirtualną jako serwer plików](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Skonfiguruj macierz wirtualną jako serwer iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Teraz możesz rozpocząć konfigurowanie witryny Azure Portal.

## <a name="configuration-checklist"></a>Lista kontrolna konfiguracji

Lista kontrolna konfiguracji zawiera informacje, które należy zebrać przed skonfigurowaniem oprogramowania w macierzy wirtualnej StorSimple. Wcześniejsze przygotowanie tych informacji ułatwia proces wdrażania urządzenia StorSimple w środowisku użytkownika. W zależności od tego, czy Macierz wirtualna StorSimple jest wdrażana jako serwer plików czy serwer iSCSI, potrzebna jest jedna z poniższych list kontrolnych.

* Pobierz [listę kontrolną konfiguracji serwera plików macierzy wirtualnej StorSimple](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Pobierz [listę kontrolną konfiguracji serwera iSCSI macierzy wirtualnej StorSimple](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Wymagania wstępne

Poniżej znajdziesz wymagania wstępne dotyczące konfiguracji usługi StorSimple Menedżer urządzeń, macierzy wirtualnej StorSimple i sieci centrum danych.

### <a name="for-the-storsimple-device-manager-service"></a>Usługa Menedżer urządzeń StorSimple

Przed rozpoczęciem upewnij się, że:

* Masz konto Microsoft z poświadczeniami dostępu.
* Masz konto magazynu platformy Microsoft Azure z poświadczeniami dostępu.
* Subskrypcja Microsoft Azure powinna być włączona dla usługi StorSimple Menedżer urządzeń.

### <a name="for-the-storsimple-virtual-array"></a>Dla macierzy wirtualnej StorSimple

Przed wdrożeniem macierzy wirtualnej upewnij się, że:

* Masz dostęp do systemu hosta z uruchomioną funkcją Hyper-V w systemie Windows Server 2008 R2 lub nowszym lub oprogramowaniu VMware (ESXi 5,0, 5,5, 6,0 lub 6,5), którego można użyć do udostępnienia urządzenia.
* System hosta może przeznaczyć następujące zasoby, aby udostępnić tablicę wirtualną:
  
  * Co najmniej 4 rdzenie.
  * Co najmniej 8 GB pamięci RAM. Jeśli planujesz skonfigurowanie macierzy wirtualnej jako serwera plików, 8 GB obsługuje pliki 2 000 000. Do obsługi 2-4 milionów plików potrzebna jest 16 GB pamięci RAM.
  * Jeden interfejs sieciowy.
  * Dysk wirtualny 500 GB dla danych systemu.

### <a name="for-the-datacenter-network"></a>Sieć centrum danych

Przed rozpoczęciem upewnij się, że:

* Sieć w centrum danych jest skonfigurowana zgodnie z wymaganiami dotyczącymi sieci dla urządzenia StorSimple. Aby uzyskać więcej informacji, zobacz [wymagania systemowe macierzy wirtualnej StorSimple](storsimple-ova-system-requirements.md).
* Macierz wirtualna StorSimple ma dedykowaną przepustowość internetową 5 MB/s (lub więcej). Ta przepustowość nie powinna być współużytkowana z żadną inną aplikacją.

## <a name="step-by-step-preparation"></a>Przygotowanie krok po kroku

Wykonaj następujące instrukcje krok po kroku, aby przygotować Portal dla usługi StorSimple Menedżer urządzeń.

## <a name="step-1-create-a-new-service"></a>Krok 1. Tworzenie nowej usługi

Pojedyncze wystąpienie usługi StorSimple Menedżer urządzeń może zarządzać wieloma StorSimple wirtualnymi. Wykonaj poniższe kroki, aby utworzyć wystąpienie usługi Menedżer urządzeń StorSimple. Jeśli masz istniejącą usługę StorSimple Menedżer urządzeń do zarządzania macierzami wirtualnymi, Pomiń ten krok i przejdź do [kroku 2: Pobieranie klucza rejestracji usługi](#step-2-get-the-service-registration-key).

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

> [!IMPORTANT]
> Jeśli nie włączono automatycznego tworzenia konta magazynu przy użyciu usługi, po pomyślnym utworzeniu usługi musisz utworzyć co najmniej jedno konto magazynu.
> 
> * Jeśli nie utworzono automatycznie konta magazynu, przejdź do kroku [Konfigurowanie nowego konta magazynu dla usługi](#optional-step-configure-a-new-storage-account-for-the-service) w celu uzyskania szczegółowych informacji.
> * Jeśli włączono automatyczne tworzenie konta magazynu, przejdź do części [Krok 2. Pobieranie klucza rejestracji usługi](#step-2-get-the-service-registration-key).
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>Krok 2. Pobieranie klucza rejestracji usługi

Po skonfigurowaniu i uruchomieniu usługi Menedżer urządzeń StorSimple musisz pobrać klucz rejestracji usługi. Ten klucz służy do rejestrowania urządzenia StorSimple i łączenia go z usługą.

Wykonaj poniższe czynności w witrynie [Azure Portal](https://portal.azure.com/).

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> Klucz rejestracji usługi służy do rejestrowania wszystkich urządzeń z systemem StorSimple Menedżer urządzeń, które muszą zostać zarejestrowane w usłudze StorSimple Menedżer urządzeń.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>Krok 3. Pobieranie obrazu macierzy wirtualnej

Po skonfigurowaniu klucza rejestracji usługi należy pobrać odpowiedni obraz macierzy wirtualnej, aby zainicjować obsługę macierzy wirtualnej w systemie hosta. Obrazy macierzy wirtualnych są specyficzne dla systemu operacyjnego i można je pobrać ze strony Szybki start w Azure Portal.

> [!IMPORTANT]
> Oprogramowanie uruchomione w macierzy wirtualnej StorSimple może być używane tylko z usługą StorSimple Menedżer urządzeń.
> 
> 

Wykonaj poniższe czynności w witrynie [Azure Portal](https://portal.azure.com/).

#### <a name="to-get-the-virtual-array-image"></a>Aby uzyskać obraz macierzy wirtualnej

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/). 
2. W Azure Portal kliknij pozycję **przeglądaj > menedżerów urządzeń StorSimple**.
3. Wybierz istniejącą usługę StorSimple Menedżer urządzeń. W bloku **StorSimple Menedżer urządzeń** kliknij pozycję **Szybki Start**. 
4. Kliknij link odpowiadający obrazowi, który chcesz pobrać z centrum pobierania Microsoft. Pliki obrazów mają rozmiar około 4,8 GB.
   
   * Plik VHDX dla funkcji Hyper-V w systemie Windows Server 2012 lub nowszym
   * Wirtualny dysk twardy dla funkcji Hyper-V w systemie Windows Server 2008 R2 lub nowszym
   * VMDK dla oprogramowania VMWare ESXi 5,0, 5,5, 6,0 lub 6,5
5. Pobierz i rozpakuj plik na lokalnym dysku. Zanotuj lokalizację rozpakowanego pliku.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Krok opcjonalny: Konfigurowanie nowego konta magazynu dla usługi

Ten krok jest opcjonalny i należy go wykonać tylko wtedy, gdy nie włączono automatycznego tworzenia konta magazynu przy użyciu usługi.

Jeśli musisz utworzyć konto usługi Azure Storage w innym regionie, zobacz [temat jak utworzyć konto magazynu](../storage/common/storage-account-create.md) , aby uzyskać instrukcje krok po kroku.

Aby dodać istniejące konto magazynu Microsoft Azure, wykonaj następujące czynności w [Azure Portal](https://ms.portal.azure.com/) na stronie usługi StorSimple Menedżer urządzeń.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Aby dodać poświadczenia konta magazynu, które ma tę samą subskrypcję platformy Azure co usługa Menedżer urządzeń

1. Przejdź do usługi Menedżer urządzeń, wybierz i kliknij ją dwukrotnie. Spowoduje to otwarcie bloku **Przegląd** .
2. Wybierz pozycję **poświadczenia konta magazynu** w sekcji **Konfiguracja** .
3. Kliknij pozycję **Dodaj**.
4. W bloku **Dodawanie konta magazynu** wykonaj następujące czynności:
   
   1. W obszarze **subskrypcja**wybierz pozycję **bieżące**.
   
   2. Podaj nazwę konta usługi Azure Storage.
   
   3. Wybierz pozycję **Włącz** , aby utworzyć bezpieczny kanał na potrzeby komunikacji sieciowej między urządzeniem StorSimple a chmurą. Wybierz opcję **Wyłącz** tylko wtedy, gdy Pracujesz w chmurze prywatnej.
   
   4. Kliknij pozycję **Dodaj**. Po pomyślnym utworzeniu konta magazynu otrzymasz powiadomienie.<br></br>
   
      ![Dodawanie istniejącego poświadczenia konta magazynu](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Następny krok

Następnym krokiem jest zainicjowanie obsługi maszyny wirtualnej dla macierzy wirtualnej StorSimple. W zależności od systemu operacyjnego hosta szczegółowe instrukcje znajdują się w temacie:

* [Obsługa StorSimple macierzy wirtualnej w funkcji Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [Inicjowanie obsługi wirtualnej macierzy StorSimple w oprogramowaniu VMware](storsimple-virtual-array-deploy2-provision-vmware.md)

