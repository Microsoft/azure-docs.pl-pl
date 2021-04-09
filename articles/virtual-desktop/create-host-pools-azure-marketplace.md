---
title: Pula hostów usług pulpitu wirtualnego systemu Windows Azure Portal — Azure
description: Jak utworzyć pulę hostów pulpitu wirtualnego systemu Windows przy użyciu Azure Portal.
author: Heidilohr
ms.topic: tutorial
ms.custom: references_regions
ms.date: 03/10/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 60566b95447c1b69fb257435f45a11524ac5d8b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102617351"
---
# <a name="tutorial-create-a-host-pool-with-the-azure-portal"></a>Samouczek: Tworzenie puli hostów przy użyciu Azure Portal

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md). Wszystkie obiekty utworzone za pomocą pulpitu wirtualnego systemu Windows (klasyczny) nie mogą być zarządzane za pomocą Azure Portal.

Pule hostów to zbiór co najmniej jednej identycznej maszyny wirtualnej w środowiskach klasycznych systemu Windows. Każda pula hostów może zawierać grupę aplikacji, z którą użytkownicy mogą wchodzić w pracę, tak jak na pulpicie fizycznym.

Ten artykuł przeprowadzi Cię przez proces instalacji tworzenia puli hostów dla środowiska pulpitu wirtualnego systemu Windows za pomocą Azure Portal. Ta metoda zapewnia interfejs użytkownika oparty na przeglądarce w celu utworzenia puli hostów w programie Virtual Desktop systemu Windows, utworzenia grupy zasobów z maszynami wirtualnymi w ramach subskrypcji platformy Azure, przyłączenia tych maszyn wirtualnych do domeny Azure Active Directory (AD) i zarejestrowania maszyn wirtualnych za pomocą pulpitu wirtualnego systemu Windows.

## <a name="prerequisites"></a>Wymagania wstępne

Musisz wprowadzić następujące parametry, aby utworzyć pulę hostów:

- Nazwa obrazu maszyny wirtualnej
- Konfiguracja maszyny wirtualnej
- Właściwości domeny i sieci
- Właściwości puli hostów pulpitu wirtualnego systemu Windows

Należy również znać następujące kwestie:

- Miejsce źródłowe obrazu, którego chcesz użyć, to. Czy pochodzi ona z galerii platformy Azure lub jest obrazem niestandardowym?
- Poświadczenia dołączania do domeny.

Upewnij się również, że zarejestrowano dostawcę zasobów Microsoft. DesktopVirtualization. Jeśli jeszcze tego nie zrobiono, przejdź do pozycji **subskrypcje**, wybierz nazwę subskrypcji, a następnie wybierz pozycję **dostawcy zasobów**. Wyszukaj pozycję DesktopVirtualization, wybierz pozycję Microsoft. DesktopVirtualization, a następnie wybierz pozycję Zarejestruj.

Podczas tworzenia puli hostów pulpitu wirtualnego systemu Windows przy użyciu szablonu Azure Resource Manager można utworzyć maszynę wirtualną z poziomu galerii platformy Azure, obrazu zarządzanego lub niezarządzanego obrazu. Aby dowiedzieć się więcej na temat tworzenia obrazów maszyn wirtualnych, zobacz [Przygotowywanie wirtualnego dysku twardego systemu Windows lub dysku VHDX do przekazania do platformy Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) i [Tworzenie zarządzanego obrazu maszyny wirtualnej na platformie Azure](../virtual-machines/windows/capture-image-resource.md).

Jeśli nie masz jeszcze subskrypcji platformy Azure, przed rozpoczęciem wykonywania tych instrukcji upewnij się, że zostało [utworzone konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="begin-the-host-pool-setup-process"></a>Rozpocznij proces instalacji puli hostów

Aby rozpocząć tworzenie nowej puli hostów:

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com/).
   
   >[!NOTE]
   > Jeśli logujesz się do portalu US Gov, przejdź do [https://portal.azure.us/](https://portal.azure.us/) zamiast tego.

2. Na pasku wyszukiwania przejdź do **pulpitu wirtualnego systemu Windows** , a następnie Znajdź i wybierz pozycję **pulpit wirtualny systemu Windows** w obszarze usługi.

3. Na stronie Omówienie **pulpitu wirtualnego systemu Windows** wybierz pozycję **Utwórz pulę hostów**.

4. Na karcie **podstawy** Wybierz poprawną subskrypcję w obszarze Szczegóły projektu.

5. Wybierz pozycję **Utwórz nowy** , aby utworzyć nową grupę zasobów, lub wybierz istniejącą grupę zasobów z menu rozwijanego.

6. Wprowadź unikatową nazwę puli hostów.

7. W polu Lokalizacja wybierz region, w którym chcesz utworzyć pulę hostów z menu rozwijanego.

   W obszarze geograficznym platformy Azure skojarzonym z wybranymi regionami są przechowywane metadane dla tej puli hostów i powiązanych z nią obiektów. Upewnij się, że wybierasz regiony wewnątrz obszaru geograficznego, w którym mają być przechowywane metadane usługi.

     > [!div class="mx-imgBorder"]
     > ![Zrzut ekranu przedstawiający Azure Portal wyświetlania pola Location z wybraną lokalizacją Wschodnie stany USA. Obok pola jest wyświetlany tekst "metadane będą przechowywane w regionie Wschodnie stany USA".](media/portal-location-field.png)
  
   >[!NOTE]
   > Jeśli chcesz utworzyć pulę hostów w [obsługiwanym regionie](data-locations.md) poza stanem USA, należy ponownie zarejestrować dostawcę zasobów. Po ponownym zarejestrowaniu należy zobaczyć inne regiony na liście rozwijanej, aby wybrać lokalizację. Dowiedz się, jak ponownie zarejestrować się w artykule dotyczącym rozwiązywania problemów z [tworzeniem puli hostów](troubleshoot-set-up-issues.md#i-only-see-us-when-setting-the-location-for-my-service-objects) .

8. W obszarze Typ puli hostów wybierz, czy pula hostów ma być **osobista** , czy w **puli**.

    - W przypadku wybrania opcji **osobiste** wybierz opcję **automatycznie** lub **bezpośrednio** w polu Typ przypisania.

      > [!div class="mx-imgBorder"]
      > ![Zrzut ekranu przedstawiający menu rozwijane pole typu przypisania. Użytkownik wybrał automatyczne.](media/assignment-type-field.png)

9.  W przypadku wybrania **puli** wprowadź następujące informacje:

     - W polu **limit liczby sesji** wprowadź maksymalną liczbę użytkowników, które mają być zrównoważone obciążenie, na jeden Host sesji.
     - W obszarze **algorytm równoważenia obciążenia** wybierz pozycję szerokość pierwszej lub głębokość — na podstawie wzorca użycia.

       > [!div class="mx-imgBorder"]
       > ![Zrzut ekranu pola Typ przypisania z wybranym "pulą w puli". Użytkownik kursoruje wskaźnik myszy nad szerokością w menu rozwijanym równoważenia obciążenia.](media/pooled-assignment-type.png)

10. Wybierz pozycję **Dalej: Virtual Machines >**.

11. Jeśli masz już utworzone maszyny wirtualne i chcesz korzystać z nich w nowej puli hostów, wybierz pozycję **nie**, wybierz pozycję **Dalej: obszar roboczy >** i przejdź do sekcji [Informacje o obszarze roboczym](#workspace-information) . Jeśli chcesz utworzyć nowe maszyny wirtualne i zarejestrować je w nowej puli hostów, wybierz pozycję **tak**.

Po wykonaniu pierwszej części przejdźmy do następnej części procesu instalacji, w której utworzysz maszynę wirtualną.

## <a name="virtual-machine-details"></a>Szczegóły maszyny wirtualnej

Teraz, gdy korzystamy z pierwszej części, musisz skonfigurować maszynę wirtualną.

Aby skonfigurować maszynę wirtualną w ramach procesu instalacji puli hostów:

1. W obszarze **Grupa zasobów** wybierz grupę zasobów, w której chcesz utworzyć maszyny wirtualne. Może to być inna grupa zasobów niż ta, która została użyta dla puli hostów.

2. Następnie podaj **prefiks nazwy** , aby nazwać maszyny wirtualne tworzone przez proces instalacji. Sufiks będzie `-` z liczbami zaczynającymi się od 0.

3. Wybierz **lokalizację maszyny wirtualnej** , w której chcesz utworzyć maszyny wirtualne. Mogą być takie same lub różne od regionu wybranego dla puli hostów.
   
4. Następnie wybierz opcję dostępność, która najlepiej odpowiada Twoim potrzebom. Aby dowiedzieć się więcej o tym, która opcja jest dla Ciebie odpowiednia, zobacz [Opcje dostępności dla maszyn wirtualnych na platformie Azure](../virtual-machines/availability.md) i [naszych często zadawanych pytań](faq.md#which-availability-option-is-best-for-me).
   
   > [!div class="mx-imgBorder"]
   > [Zrzut ekranu przedstawiający menu rozwijane strefa dostępności. Zaznaczona jest opcja "strefa dostępności".](media/availability-zone.png)

5. Następnie wybierz obraz, który ma zostać użyty do utworzenia maszyny wirtualnej. Możesz wybrać opcję **obiekt BLOB** **galerii** lub magazynu.

    - W przypadku wybrania opcji **Galeria** wybierz z menu rozwijanego jeden z zalecanych obrazów:

      - Wiele sesji systemu Windows 10 Enterprise, wersja 1909
      - Wiele sesji systemu Windows 10 Enterprise, wersja 1909 + Microsoft 365 aplikacje
      - Windows Server 2019 Datacenter
      - Wiele sesji systemu Windows 10 Enterprise, wersja 2004
      - Wiele sesji systemu Windows 10 Enterprise, wersja 2004 + Microsoft 365 aplikacje

      Jeśli nie widzisz żądanego obrazu, wybierz pozycję **Pokaż wszystkie obrazy**, co umożliwi wybranie innego obrazu w galerii lub obrazu dostarczonego przez firmę Microsoft i innych wydawców. Upewnij się, że wybrany obraz jest jednym z [obsługiwanych obrazów systemu operacyjnego](overview.md#supported-virtual-machine-os-images).

      > [!div class="mx-imgBorder"]
      > ![Zrzut ekranu przedstawiający witrynę Marketplace z listą obrazów wyświetlanych od firmy Microsoft.](media/marketplace-images.png)

      Możesz również przejść do **pozycji moje elementy** i wybrać obraz niestandardowy, który został już przekazany.

      > [!div class="mx-imgBorder"]
      > ![Zrzut ekranu przedstawiający kartę Moje elementy.](media/my-items.png)

    - W przypadku wybrania **obiektu BLOB Storage** możesz użyć własnej kompilacji obrazu za pomocą funkcji Hyper-V lub na maszynie wirtualnej platformy Azure. Wystarczy wprowadzić lokalizację obrazu w obiekcie blob magazynu jako identyfikator URI.
   
   Lokalizacja obrazu jest niezależna od opcji dostępności, ale odporność strefy obrazu Określa, czy ten obraz może być używany z strefą dostępności. W przypadku wybrania strefy dostępności podczas tworzenia obrazu upewnij się, że używasz obrazu z galerii z włączonym odpornością strefy. Aby dowiedzieć się więcej na temat opcji odporności strefy, której należy użyć, zobacz [często zadawane pytania](faq.md#which-availability-option-is-best-for-me).

6. Następnie wybierz **rozmiar maszyny wirtualnej** , której chcesz użyć. Domyślny rozmiar można zachować jako-lub wybierz **Zmień rozmiar** , aby zmienić rozmiar. W przypadku wybrania opcji **Zmień rozmiar** w wyświetlonym oknie Wybierz rozmiar maszyny wirtualnej odpowiedni dla danego obciążenia.

7. W obszarze **Liczba maszyn wirtualnych** podaj liczbę maszyn wirtualnych, które chcesz utworzyć dla puli hostów.

    >[!NOTE]
    >Proces instalacji może utworzyć do 400 maszyn wirtualnych podczas konfigurowania puli hostów, a każdy proces instalacji maszyn wirtualnych tworzy cztery obiekty w grupie zasobów. Ponieważ proces tworzenia nie sprawdza limitu przydziału subskrypcji, upewnij się, że liczba wprowadzonych maszyn wirtualnych znajduje się w granicach maszyny wirtualnej i interfejsów API platformy Azure dla Twojej grupy zasobów i subskrypcji. Po zakończeniu tworzenia puli hostów można dodać więcej maszyn wirtualnych.

8. Wybierz rodzaje dysków systemu operacyjnego, które mają być używane przez maszyny wirtualne: SSD w warstwie Standardowa, SSD w warstwie Premium lub HDD w warstwie Standardowa.

9. W obszarze Sieć i zabezpieczenia wybierz **sieć wirtualną** i **podsieć** , w której chcesz umieścić maszyny wirtualne, które tworzysz. Upewnij się, że sieć wirtualna może połączyć się z kontrolerem domeny, ponieważ należy przyłączyć maszyny wirtualne wewnątrz sieci wirtualnej do domeny. Serwery DNS wybranej sieci wirtualnej powinny być skonfigurowane do korzystania z adresu IP kontrolera domeny.

10. Wybierz typ grupy zabezpieczeń: **podstawowy**, **Zaawansowany** lub **Brak**.

    W przypadku wybrania opcji **podstawowa** musisz wybrać, czy ma zostać otwarty dowolny port wejściowy. W przypadku wybrania opcji **tak** wybierz z listy standardowych portów, aby zezwolić na połączenia przychodzące.

    >[!NOTE]
    >W celu zapewnienia większego bezpieczeństwa zalecamy, aby nie otwierać publicznych portów przychodzących.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu strony grupy zabezpieczeń, w którym jest wyświetlana lista dostępnych portów w menu rozwijanym.](media/available-ports.png)

    W przypadku wybrania opcji **Zaawansowane** wybierz istniejącą grupę zabezpieczeń sieci, która została już skonfigurowana.

11. Następnie wybierz, czy maszyny wirtualne mają być przyłączone do określonej domeny, czy jednostki organizacyjnej. Jeśli wybierzesz opcję **tak**, określ domenę do przyłączenia. Opcjonalnie możesz dodać konkretną jednostkę organizacyjną, w której mają znajdować się maszyny wirtualne. Jeśli wybierzesz opcję **nie**, maszyny wirtualne zostaną przyłączone do domeny pasującej do sufiksu **UPN domeny usługi AD**.

    - Po określeniu jednostki organizacyjnej upewnij się, że używasz pełnej ścieżki (nazwy wyróżniającej) i bez znaków cudzysłowu.

12. W obszarze konto administratora domeny wprowadź poświadczenia dla domena usługi Active Directory administratora wybranej sieci wirtualnej. To konto nie może mieć włączonej usługi uwierzytelniania wieloskładnikowego (MFA). Podczas dołączania do domeny Azure Active Directory Domain Services (Azure AD DS) konto musi należeć do grupy administratorów kontrolera domeny usługi Azure AD, a hasło konta musi działać na platformie Azure AD DS.

13. Wybierz pozycję **Dalej: obszar roboczy >**.

Dzięki temu jesteśmy gotowi do rozpoczęcia następnej fazy konfigurowania puli hostów: rejestrowanie grupy aplikacji w obszarze roboczym.

## <a name="workspace-information"></a>Informacje o obszarze roboczym

Proces instalacji puli hostów domyślnie tworzy grupę aplikacji klasycznych. Aby Pula hostów działała zgodnie z założeniami, należy opublikować tę grupę aplikacji dla użytkowników lub grup użytkowników, a grupa aplikacji musi zostać zarejestrowana w obszarze roboczym.

Aby zarejestrować grupę aplikacji klasycznych w obszarze roboczym:

1. Wybierz pozycję **Tak**.

   Jeśli wybierzesz opcję **nie**, możesz zarejestrować grupę aplikacji później, ale zalecamy przeprowadzenie rejestracji obszaru roboczego, gdy tylko będzie to możliwe, aby Pula hostów działała prawidłowo.

2. Następnie wybierz, czy chcesz utworzyć nowy obszar roboczy, czy wybrać z istniejących obszarów roboczych. Tylko obszary robocze utworzone w tej samej lokalizacji, w której znajduje się pula hostów, będą mogły zarejestrować grupę aplikacji.

3. Opcjonalnie możesz wybrać pozycję **Next (dalej): tagi >**.

    W tym miejscu możesz dodać tagi, aby można było grupować obiekty za pomocą metadanych, aby ułatwić administratorom wykonywanie zadań.

4. Gdy skończysz, wybierz pozycję **Przegląd + Utwórz**.

     >[!NOTE]
     >Proces przegląd + tworzenie weryfikacji nie sprawdza, czy hasło spełnia standardy zabezpieczeń lub czy architektura jest poprawna, dlatego należy sprawdzić ewentualne problemy z jednym z tych elementów.

5. Przejrzyj informacje o wdrożeniu, aby upewnić się, że wszystko wygląda poprawnie. Gdy wszystko będzie gotowe, wybierz przycisk **Utwórz**. Spowoduje to uruchomienie procesu wdrażania, który tworzy następujące obiekty:

     - Twoja nowa pula hostów.
     - Grupa aplikacji klasycznych.
     - Obszar roboczy, jeśli wybierzesz go utworzyć.
     - W przypadku wybrania opcji zarejestrowania grupy aplikacji klasycznej rejestracja zostanie zakończona.
     - Maszyny wirtualne, jeśli wybrano ich tworzenie, które są przyłączone do domeny i zarejestrowane w nowej puli hostów.
     - Link do pobierania dla szablonu usługi Azure Resource Management na podstawie konfiguracji.

Wszystko gotowe!

## <a name="run-the-azure-resource-manager-template-to-provision-a-new-host-pool"></a>Uruchom szablon Azure Resource Manager, aby zainicjować obsługę nowej puli hostów

Jeśli wolisz korzystać z zautomatyzowanego procesu, [Pobierz szablon Azure Resource Manager](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates) , aby zamiast tego zainicjować obsługę nowej puli hostów.

>[!NOTE]
>Jeśli używasz zautomatyzowanego procesu do kompilowania środowiska, potrzebujesz najnowszej wersji pliku JSON konfiguracji. Plik JSON można znaleźć [tutaj](https://wvdportalstorageblob.blob.core.windows.net/galleryartifacts?restype=container&comp=list).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy została utworzona Pula hostów, możesz ją wypełnić za pomocą programów RemoteApp. Aby dowiedzieć się więcej na temat zarządzania aplikacjami w systemie Windows Virtual Desktop, przejdź do naszego następnego samouczka:

> [!div class="nextstepaction"]
> [Samouczek zarządzania grupami aplikacji](./manage-app-groups.md)
