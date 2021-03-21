---
title: Instalowanie Microsoft Office kontenerów aplikacji FSLogix w programie Virtual Desktop systemu Windows — Azure
description: Jak utworzyć kontener aplikacji FSLogix przy użyciu edytora reguł aplikacji przy użyciu pakietu Office na pulpicie wirtualnym systemu Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7d66d370f321323ec1aeb45ad0fe628904b14fe6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101747147"
---
# <a name="install-microsoft-office-using-fslogix-application-containers"></a>Instalowanie Microsoft Office przy użyciu kontenerów aplikacji FSLogix

Microsoft Office można instalować szybko i wydajnie przy użyciu kontenera aplikacji FSLogix jako szablonu dla innych maszyn wirtualnych w puli hostów.

Oto dlaczego używanie kontenera aplikacji FSLogix może przyspieszyć instalację:

- Odciążanie aplikacji pakietu Office do kontenera aplikacji zmniejsza wymagania dotyczące rozmiaru dysku C.
- Migawki lub kopie zapasowe maszyny wirtualnej zajmują mniej zasobów.
- Posiadanie zautomatyzowanego potoku za pomocą aktualizacji pojedynczego obrazu ułatwia aktualizowanie maszyn wirtualnych.
- Wystarczy tylko jeden obraz, aby zainstalować pakiet Office (i inne aplikacje) na wszystkich maszynach wirtualnych w ramach wdrożenia pulpitu wirtualnego systemu Windows.

W tym artykule przedstawiono sposób konfigurowania kontenera aplikacji FSLogix w pakiecie Office.

## <a name="requirements"></a>Wymagania

Aby skonfigurować Edytor reguł, należy wykonać następujące czynności:

- Maszyna wirtualna z systemem Windows bez zainstalowanego pakietu Office
- kopia pakietu Office
- kopia FSLogix zainstalowana we wdrożeniu
- udział sieciowy, do którego wszystkie maszyny wirtualne w puli hostów mają dostęp tylko do odczytu

## <a name="install-office"></a>Instalowanie pakietu Office

Aby zainstalować pakiet Office na dysku VHD lub VHDX, Włącz Remote Desktop Protocol na maszynie wirtualnej, a następnie postępuj zgodnie z instrukcjami w temacie [Instalowanie pakietu Office na głównym obrazie dysku VHD](install-office-on-wvd-master-image.md). W przypadku instalowania programu upewnij się, że używasz [odpowiednich licencji](overview.md#requirements).

>[!NOTE]
>Pulpit wirtualny systemu Windows wymaga aktywacji komputera udziału (SCA).

## <a name="install-fslogix"></a>Zainstaluj FSLogix

Aby zainstalować FSLogix i Edytor reguł, postępuj zgodnie z instrukcjami podanymi w temacie [pobieranie i Instalowanie FSLogix](/fslogix/install-ht).

## <a name="create-and-prepare-a-vhd-to-store-office"></a>Tworzenie i przygotowywanie wirtualnego dysku twardego do przechowywania pakietu Office

Następnie musisz utworzyć i przygotować obraz VHD, aby użyć edytora reguł w:

1. Otwórz wiersz polecenia jako administrator. Uruchom następujące polecenie:

    ```cmd
        taskkill /F /IM MicrosoftEdge.exe /T
    ```

    >[!NOTE]
    > Pamiętaj, aby pozostawić puste spacje, które są widoczne w tym poleceniu.

2. Następnie uruchom następujące polecenie:

    ```cmd
    sc queryex type=service state=all | find /i "ClickToRunSvc"
    ```
    
   Jeśli znajdziesz usługę, uruchom ponownie maszynę wirtualną przed przejściem do kroku 3.

    ```cmd
    net stop ClickToRunSvc
    ```

3. Następnie przejdź do pozycji **Program Files**  >  **FSLogix**  >  **Apps** i uruchom następujące polecenie, aby utworzyć docelowy dysk VHD:

    ```cmd
    frx moveto-vhd -filename <path to network share>\office.vhdx -src "C:\Program Files\Microsoft Office" -size-mbs 5000 
    ```

    Wirtualny dysk twardy utworzony za pomocą tego polecenia powinien zawierać folder C: \\ Program Files \\ Microsoft Office.

    >[!NOTE]
    >Jeśli zobaczysz jakiekolwiek błędy, Odinstaluj pakiet Office i zacznij od kroku 1.

## <a name="configure-the-rule-editor"></a>Konfigurowanie edytora reguł

Teraz, po przygotowaniu obrazu, należy skonfigurować Edytor reguł i utworzyć plik do przechowywania reguł.

1. Przejdź do pozycji **Program Files**  >  **FSLogix**  >  **Apps** i uruchom **RuleEditor.exe**.

2. Wybierz pozycję **plik**  >  **Nowy**  >  **Utwórz** , aby utworzyć nowy zestaw reguł, a następnie Zapisz ten zestaw reguł w folderze lokalnym.

3. Wybierz pozycję **pusty zestaw reguł**, a następnie wybierz przycisk **OK**.

4. Wybierz przycisk **+**. Spowoduje to otwarcie okna **Dodawanie reguły** . Spowoduje to zmianę opcji w oknie dialogowym **Dodawanie reguły** .

5. Z menu rozwijanego wybierz pozycję **reguła kontenera aplikacji (VHD)**.

6. Wprowadź **C: \\ Program Files \\ Microsoft Office** w polu **folder** .

7. W polu **plik dysku** wybierz pozycję **\<path\> \\ Office. VHD** z sekcji **Tworzenie docelowego wirtualnego dysku twardego** .

8. Wybierz przycisk **OK**.

9. Przejdź do folderu roboczego w lokalizacji **C: \\ Użytkownicy \\ \<username\> \\ dokumentują \\ zestawy reguł FSLogix** i Szukaj plików. FRx i. FXa. Należy przenieść te pliki do folderu reguł znajdującego się w folderze **C: \\ Program Files \\ FSLogix \\ Apps \\ Rules** , aby reguły mogli rozpocząć pracę.

10. Wybierz pozycję **Zastosuj reguły do systemu** , aby reguły zaczęły obowiązywać.

     >[!NOTE]
     > Należy zastosować pliki reguł aplikacji, które będą musiały być wszystkie hosty sesji.

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz dowiedzieć się więcej na temat FSLogix, zapoznaj się z [dokumentacją FSLogix](/fslogix/).