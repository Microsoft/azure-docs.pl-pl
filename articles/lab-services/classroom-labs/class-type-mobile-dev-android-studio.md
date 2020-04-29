---
title: Skonfiguruj laboratorium, aby nauczyć się opracowywania aplikacji mobilnych za pomocą Android Studio
titleSuffix: Azure Lab Services
description: Dowiedz się, jak skonfigurować laboratorium do uczenia się klasy opracowywania aplikacji mobilnych dla danych, która używa Android Studio.  W artykule omówiono także zmiany wprowadzane podczas korzystania z Android Studio na maszynie wirtualnej na platformie Azure.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 0c257589a2e93ac4c15a639e7156d0c0944b033c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76849788"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>Skonfiguruj laboratorium, aby nauczyć się opracowywania aplikacji mobilnych dla danych za pomocą Android Studio

W tym artykule opisano sposób konfigurowania wstępnej klasy opracowywania aplikacji mobilnych.  Ta klasa koncentruje się na aplikacjach mobilnych systemu Android, które można opublikować w [sklep Google Play](https://play.google.com/store/apps).  Studenci mogą dowiedzieć się, jak używać [Android Studio](https://developer.android.com/studio) do kompilowania aplikacji.  [Emulator programu Visual Studio dla systemu Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) służy do lokalnego testowania aplikacji.

## <a name="lab-configuration"></a>Konfiguracja laboratorium

Aby rozpocząć pracę z tym laboratorium, musisz zacząć korzystać z subskrypcji platformy Azure i konta laboratorium. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) . Po otrzymaniu subskrypcji platformy Azure Możesz utworzyć nowe konto laboratorium w Azure Lab Services. Aby uzyskać więcej informacji na temat tworzenia nowego konta laboratorium, zobacz [Samouczek dotyczący konfigurowania konta laboratorium](tutorial-setup-lab-account.md).  Możesz również użyć istniejącego konta laboratorium.

Postępuj zgodnie z [samouczkiem Konfigurowanie laboratorium klasy](tutorial-setup-classroom-lab.md) , aby utworzyć nowe laboratorium, a następnie Zastosuj następujące ustawienia:

| Rozmiar maszyny wirtualnej | Obraz |
| -------------------- | ----- |
| Średni (Wirtualizacja zagnieżdżona) | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>Konfiguracja komputera szablonu

Po zakończeniu tworzenia maszyny szablonu [Uruchom maszynę i Połącz się z nią](how-to-create-manage-template.md#update-a-template-vm) , aby wykonać następujące zadania:

1. Dodaj rolę funkcji Hyper-V
2. Pobierz i zainstaluj środowisko Java.  
3. Pobierz i zainstaluj Emulator programu Visual Studio dla systemu Android.
4. Pobierz i Zainstaluj Android Studio.
5. Skonfiguruj Emulator programu Visual Studio dla Android Studio.

## <a name="add-hyper-v-role"></a>Dodaj rolę funkcji Hyper-V

Funkcja Hyper-V musi być włączona dla pomyślnej instalacji emulatora programu Visual Studio dla systemu Android.  Postępuj zgodnie z instrukcjami zawartymi w artykule [jak włączyć wirtualizację zagnieżdżoną w szablonie maszyny wirtualnej](how-to-enable-nested-virtualization-template-vm.md) .

## <a name="install-java"></a>Instalowanie języka Java

Android Studio wymaga języka Java.  Wykonaj poniższe kroki, aby pobrać najnowszą wersję środowiska Java.

1. Przejdź do [strony pobierania środowiska Java](https://www.java.com/download/). Kliknij przycisk **Pobierz Java** .
2. Na stronie 64-bitowej systemu Windows dla środowiska Java kliknij przycisk **Zgadzam się i rozpocznij pobieranie bezpłatne**.
3. Gdy zostanie wyświetlony Instalator **ustawień Java** , kliknij przycisk **Instaluj**.
4. Zaczekaj na zmianę tytułu Instalatora na **konfigurację języka Java — Zakończ**.  Kliknij przycisk **Zamknij** .

## <a name="install-visual-studio-emulator-for-android"></a>Zainstaluj program Visual Studio Emulator dla systemu Android

Aby przetestować aplikację dla systemu Android lokalnie, musi ona używać zwirtualizowanej wersji urządzenia z systemem Android.  Istnieje kilka dostępnych emulatorów systemu Android, które pozwolą deweloperowi na testowanie aplikacji z ich maszyn.  Korzystamy z emulatora programu Visual Studio dla systemu Android, ponieważ jest to emulator obsługujący wirtualizację zagnieżdżoną.  Ponieważ maszyna wirtualna usługi laboratoryjnej jest już maszyną wirtualną, potrzebujemy emulatora obsługującego wirtualizację zagnieżdżoną.  Wbudowany emulator dla Android Studio nie obsługuje wirtualizacji zagnieżdżonej.  Aby zobaczyć, które emulatory obsługują wirtualizację zagnieżdżoną, zobacz [przyspieszanie sprzętowe dla wydajności emulatora (funkcja Hyper-V & HAXM)](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration).

Skorzystaj z poniższych instrukcji, aby pobrać i zainstalować Emulator programu Visual Studio dla systemu Android.

1. Przejdź do strony głównej [programu Visual Studio Emulator for Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) .
2. Kliknij przycisk **Pobierz emulator** .
3. Po pobraniu pliku vs_emulatorsetup. exe Uruchom plik wykonywalny.
4. Po wyświetleniu okna dialogowego Instalatora programu Visual Studio kliknij przycisk **Instaluj** .
5. Poczekaj na zakończenie działania Instalatora.  Kliknij przycisk **Uruchom ponownie teraz** , aby ponownie uruchomić komputer i zakończyć instalację.

Najpierw uruchom emulator przed wdrożeniem aplikacji przy użyciu Android Studio.  Aby uzyskać więcej informacji na temat emulatora programu Visual Studio dla systemu Android, zobacz [Visual Studio Emulator for Android — dokumentacja](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android).

## <a name="install-android-studio"></a>Zainstaluj Android Studio

Postępuj zgodnie z poniższymi instrukcjami, aby pobrać i zainstalować [Android Studio](https://developer.android.com/studio).

1. Przejdź do [strony pobierania Android Studio](https://developer.android.com/studio#downloads).  
    > [!NOTE]
    > Ta witryna nie obsługuje programu Internet Explorer.
2. Kliknij pakiet Android Studio pliku wykonywalnego systemu Windows (64-bitowy).
3. Zapoznaj się z postanowieniami prawnymi zapisanymi w wyskakującym okienku.  Gdy wszystko będzie gotowe do kontynuowania, zaznacz pole wyboru **Przeczytaj i zaakceptuj powyższe warunki i** postanowienia, a następnie kliknij przycisk **Pobierz Android Studio dla systemu Windows** .
4. Po pobraniu pliku wykonywalnego instalatora Android Studio należy uruchomić plik wykonywalny.
5. Na stronie **Zapraszamy do konfiguracji Android Studio** instalatora Instalatora **Android Studio** kliknij przycisk **dalej**.
6. Na stronie **Ustawienia konfiguracji** kliknij przycisk **dalej**.
7. Na stronie **Wybierz folder menu Start** kliknij przycisk **Instaluj**.
8. Poczekaj na zakończenie instalacji.
9. Na stronie **Instalacja ukończona** kliknij przycisk **dalej**.
10. Na stronie **ukończenie instalacji Android Studio** .  Kliknij przycisk **Zakończ**.
11. Android Studio zostanie automatycznie uruchomiona po zakończeniu instalacji.
12. W oknie dialogowym **Importowanie ustawień systemu Android z...** wybierz pozycję nie **Importuj ustawień**. Kliknij przycisk **OK**.
13. Na stronie **powitalnej** **Kreatora instalacji Android Studio**kliknij przycisk **dalej**.
14. Na stronie **Typ instalacji** wybierz pozycję **standardowa**. Kliknij przycisk **Dalej**.
15. Na stronie **Wybieranie motywu interfejsu użytkownika** wybierz żądany motyw. Kliknij przycisk **Dalej**.
16. Na stronie **Weryfikowanie ustawień** kliknij przycisk **dalej**.
17. Na stronie **Pobieranie składników** poczekaj na pobranie wszystkich składników.  Kliknij przycisk **Zakończ**.

    > [!IMPORTANT]
    > Oczekuje się, że instalacja HAXM kończy się niepowodzeniem.  HAXM nie obsługuje wirtualizacji zagnieżdżonej, co oznacza, że w tym artykule zainstalowano Emulator programu Visual Studio dla systemu Android.

18. Okno dialogowe **Witamy w Android Studio** zostanie wyświetlone po zakończeniu pracy Kreatora instalacji.

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>Konfigurowanie Android Studio i emulatora programu Visual Studio dla systemu Android

Android Studio jest prawie gotowe do użycia.  Nadal musimy powiedzieć Emulator programu Visual Studio dla systemu Android, w którym zainstalowano Android SDK.  Dzięki temu wszystkie emulatory działające w programie Visual Studio dla systemu Android będą widoczne jako elementy docelowe wdrożenia na potrzeby debugowania Android Studio.

Musimy ustawić określony klucz rejestru, aby powiedzieć Emulator programu Visual Studio dla systemu Android, w którym znajduje się zestaw SDK systemu Android.  Aby ustawić wymagany klucz rejestru, uruchom poniższy skrypt.  Poniższy skrypt programu PowerShell zakłada domyślną lokalizację instalacji zestawu Android SDK.  Jeśli zestaw SDK systemu Android został zainstalowany w innej lokalizacji, należy zmodyfikować wartość `$androidSdkPath` przed uruchomieniem skryptu.

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> Uruchom ponownie Emulator programu Visual Studio dla systemu Android i Android Studio, aby było używane nowe ustawienie.

Uruchom wymaganą wersję w emulatorze programu Visual Studio.  Będzie ona wyświetlana jako element docelowy wdrożenia aplikacji systemu Android w programie Android Studio.  Minimalna wersja projektu Android Studio musi obsługiwać wersję uruchomioną w emulatorze programu Visual Studio dla systemu Android.  Teraz możesz przystąpić do tworzenia i debugowania projektów przy użyciu Android Studio i programu Visual Studio Emulator dla systemu Android.  Jeśli masz jakieś problemy, zobacz temat Rozwiązywanie problemów z emulatorem systemu Android.

## <a name="cost"></a>Koszty

Jeśli chcesz oszacować koszt tego laboratorium, możesz skorzystać z poniższego przykładu.
W przypadku klasy 25 studentów z upływem 20 godzin planowanego czasu zajęć i 10 godzin przydziału dla prac domowych lub przydziałów cena za laboratorium będzie równa  

25 studentów \* (20 harmonogramów + 10) godz. * 55 jednostek laboratoryjnych * 0,01 USD za godzinę = 412,5 USD

Więcej szczegółowych informacji na temat cen można znaleźć w temacie [Azure Lab Services Cennik](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Następne kroki

Następne kroki są wspólne do konfigurowania dowolnego laboratorium.

- [Tworzenie szablonu i zarządzanie nim](how-to-create-manage-template.md)
- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustaw przydział](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Linki do rejestracji w wiadomościach e-mail z uczniami](how-to-configure-student-usage.md#send-invitations-to-users)
