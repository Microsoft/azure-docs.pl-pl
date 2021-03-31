---
title: Zainstaluj aktualizację 5,1 na urządzeniu z serii StorSimple 8000 | Microsoft Docs
description: Wyjaśniono, jak zainstalować aktualizację z serii StorSimple 8000 5,1 na urządzeniu z serii StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/05/2020
ms.author: alkohli
ms.openlocfilehash: 5b9958f3dd497aa612a92947b8d968439ef9d0e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91575966"
---
# <a name="install-update-51-on-your-storsimple-device"></a>Zainstaluj aktualizację 5,1 na urządzeniu StorSimple

## <a name="overview"></a>Omówienie

W tym samouczku wyjaśniono, jak zainstalować aktualizację 5,1 na urządzeniu StorSimple z uruchomioną wcześniejszą wersją oprogramowania za pośrednictwem Azure Portal. <!--The hotfix method is used when you are trying to install Update 5.1 on a device running pre-Update 3 versions. The hotfix method is also used when a gateway is configured on a network interface other than DATA 0 of the StorSimple device and you are trying to update from a pre-Update 1 software version.-->

Aktualizacja 5,1 zawiera niezakłócone aktualizacje zabezpieczeń. Niezakłócone lub regularne aktualizacje można zastosować w Azure Portal <!--or by the hotfix method-->.

> [!IMPORTANT]
>
> * Aktualizacja 5,1 jest obowiązkową aktualizacją i należy ją zainstalować od razu. Aby uzyskać więcej informacji, zobacz [Informacje o wersji aktualizacji 5,1](storsimple-update51-release-notes.md).
> * Przed zainstalowaniem zestaw ręcznych i automatycznych testów jest przeprowadzany przed rozpoczęciem instalacji w celu określenia kondycji urządzenia pod kątem stanu sprzętu i połączenia sieciowego. Te wstępne sprawdzenia są wykonywane tylko w przypadku zastosowania aktualizacji z Azure Portal.
> * Jeśli chcesz zainstalować program przy użyciu metody Hotfix, skontaktuj się z [Pomoc techniczna firmy Microsoft](mailto:support@microsoft.com).

<!--
> * We strongly recommend that when updating a device running versions prior to Update 3, you install the updates using hotfix method. If you encounter any issues, [log a support ticket](storsimple-8000-contact-microsoft-support.md). 


> * We recommend that you install the software and other regular updates via the Azure portal. You should only go to the Windows PowerShell interface of the device (to install updates) if the pre-update gateway check fails in the portal. Depending upon the version you are updating from, the updates may take 4 hours (or greater) to install. The maintenance mode updates must be installed through the Windows PowerShell interface of the device. As maintenance mode updates are disruptive updates, these result in a down time for your device.


> * If running the optional StorSimple Snapshot Manager, ensure that you have upgraded your Snapshot Manager version to Update 5.1 prior to updating the device.
-->

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-51-through-the-azure-portal"></a>Zainstaluj aktualizację 5,1 za pomocą Azure Portal

Wykonaj następujące kroki, aby zaktualizować urządzenie do [aktualizacji 5,1](storsimple-update51-release-notes.md).

> [!NOTE]
> Firma Microsoft pobiera dodatkowe informacje diagnostyczne z urządzenia. W związku z tym, gdy nasz zespół operacyjny zidentyfikuje urządzenia, na których występują problemy, lepiej jest zbierać informacje z urządzenia i diagnozować problemy.

#### <a name="to-install-an-update-from-the-azure-portal"></a>Aby zainstalować aktualizację z witryny Azure Portal

1. Na stronie usługi StorSimple wybierz swoje urządzenie.

    ![Wybierz urządzenie](./media/storsimple-8000-install-update-51/update1.png)

2. Przejdź do **ustawień urządzenia ustawienia**  >  **aktualizacje urządzeń**.

    ![Zrzut ekranu przedstawiający blok ustawień z opcją aktualizacje urządzenia o nazwie out.](./media/storsimple-8000-install-update-51/update2.png)

3. Zostanie wyświetlone powiadomienie, jeśli są dostępne nowe aktualizacje. Alternatywnie w bloku **aktualizacje urządzeń** kliknij pozycję **Skanuj aktualizacje**. Zostanie utworzone zadanie skanowania dostępnych aktualizacji. Otrzymasz powiadomienie, gdy zadanie zostanie pomyślnie ukończone.

    ![Zrzut ekranu przedstawiający blok ustawień z opcją aktualizacje urządzenia o nazwie out, a blok aktualizacje urządzenia z nowymi regularnymi aktualizacjami jest dostępny komunikat o nazwie.](./media/storsimple-8000-install-update-51/update3.png)

4. Przed zastosowaniem aktualizacji na urządzeniu zalecamy przejrzenie informacji o wersji. Aby zastosować aktualizacje, kliknij przycisk **instaluj aktualizacje**. Przed zastosowaniem aktualizacji w bloku **Potwierdź regularne aktualizacje** zapoznaj się z wymaganiami wstępnymi. Zaznacz pole wyboru, aby wskazać, że wszystko jest gotowe do zaktualizowania urządzenia, a następnie kliknij przycisk **Instaluj**.

    ![Zrzut ekranu przedstawiający blok aktualizacje urządzeń z opcją Zainstaluj aktualizacje o nazwie wychodzącą i Potwierdź regularne aktualizacje z opcją zgody i opcją instalacji o nazwie out.](./media/storsimple-8000-install-update-51/update4.png)

5. Zostanie rozpoczęty zestaw testów wymagań wstępnych. Testy te obejmują:
   
   * **Testy kondycji kontrolera** w celu sprawdzenia, czy oba kontrolery urządzeń są w dobrej kondycji i w trybie online.
   * **Testy kondycji składnika sprzętowego** w celu sprawdzenia, czy wszystkie składniki sprzętowe Twojego urządzenia StorSimple są w dobrej kondycji.
   * **Testy interfejsu DATA 0** w celu sprawdzenia, czy interfejs DATA 0 jest włączony na Twoim urządzeniu. Jeśli ten interfejs nie jest włączony, należy go włączyć i ponowić próbę.

     Aktualizacja jest pobierana i instalowana tylko wtedy, gdy wszystkie testy zostały zakończone pomyślnie. Zobaczysz powiadomienie o trwających testach. Jeśli wyewidencjonowanie nie powiedzie się, otrzymasz przyczyny niepowodzenia. Rozwiąż te problemy, a następnie spróbuj ponownie wykonać operację. Jeśli nie będziesz w stanie samodzielnie rozwiązać tych problemów, konieczne może być skontaktowanie się z pomocą techniczną firmy Microsoft.

7. Po pomyślnym zakończeniu sprawdzenia zostanie utworzone zadanie aktualizacji. Otrzymasz powiadomienie o pomyślnym utworzeniu zadania aktualizacji.
   
    ![Zrzut ekranu przedstawiający powiadomienie "Uruchamianie zadania aktualizacji oprogramowania".](./media/storsimple-8000-install-update-51/update6.png)
   
    Następnie aktualizacja jest stosowania do Twojego urządzenia.

9. Aktualizacja zajmuje kilka godzin. Aby w dowolnym momencie wyświetlić szczegóły zadania, wybierz zadanie aktualizacji i kliknij pozycję **Szczegóły**.

    ![Zrzut ekranu przedstawiający blok aktualizacje urządzenia z opcją Pobierz i zainstaluj aktualizacje oprogramowania w toku o nazwie z i bloku Zainstaluj aktualizacje.](./media/storsimple-8000-install-update-51/update8.png)

     Możesz również monitorować postęp zadania aktualizacji z **ustawień urządzenia > zadania**. W bloku **zadania** można zobaczyć postęp aktualizacji.

     ![Zrzut ekranu przedstawiający blok ustawień z zadaniami o identyfikatorze out i blokiem zadania pokazujących postęp aktualizacji.](./media/storsimple-8000-install-update-51/update7.png)

10. Po zakończeniu zadania przejdź do **ustawień urządzenia, > aktualizacje urządzeń**. Wersja oprogramowania powinna zostać zaktualizowana.


Sprawdź, czy na urządzeniu jest uruchomiona **StorSimple 8000 Series Update 5,1 (6.3.9600.17885)**. **Data ostatniej aktualizacji** powinna zostać zmodyfikowana.
<!-- 5.1 - KB 4542887-->

<!--You will now see that the Maintenance mode updates are available (this message might continue to be displayed for up to 24 hours after you install the updates). The steps to install maintenance mode update are detailed in the next section.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## Install Update 5.1 as a hotfix

The software versions that can be upgraded using the hotfix method are:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1
* Update 4
* Update 5

> [!NOTE]
> The recommended method to install Update 5.1 is through the Azure portal when trying to update from Update 3 and later version. When updating a device running versions prior to Update 3, use this procedure. You can also use this procedure if you fail the gateway check when trying to install the updates through the Azure portal. The check fails when you have a gateway assigned to a non-DATA 0 network interface and your device is running a software version earlier than Update 1.

The hotfix method involves the following three steps:

1. Download the hotfixes from the Microsoft Update Catalog.
2. Install and verify the regular mode hotfixes.
3. Install and verify the maintenance mode hotfix.

#### Download updates for your device

You must download and install the following hotfixes in the prescribed order and the suggested folders:

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Software update<br> Download both _HcsSoftwareUpdate.exe_ and _CisMSDAgent.exe_ |Regular <br></br>Non-disruptive |~ 25 mins |FirstOrderUpdate|

If updating from a device running Update 4, you only need to install the OS cumulative updates as second order updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |OS cumulative updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|

If installing from a device running Update 3 or earlier, install the following in addition to the cumulative updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI driver and firmware updates <br> USM firmware update (version 3.38) |Regular <br></br>Non-disruptive |~ 3 hrs <br> (includes 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |OS security updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |OS updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|



You may also need to install disk firmware updates on top of all the updates shown in the preceding tables. You can verify whether you need the disk firmware updates by running the `Get-HcsFirmwareVersion` cmdlet. If you are running these firmware versions: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, then you do not need to install these updates.

| Order | KB | Description | Update type | Install time | Install in folder|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Disk firmware |Maintenance <br></br>Disruptive |~ 30 mins | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * If updating from Update 4, the total install time is close to 4 hours.
> * Before using this procedure to apply the update, make sure that both the device controllers are online and all the hardware components are healthy.

Perform the following steps to download and install the hotfixes.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]
-->
<!--
[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]
-->

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wersji Update 5,1](storsimple-update51-release-notes.md).
