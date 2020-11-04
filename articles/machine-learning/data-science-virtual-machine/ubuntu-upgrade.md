---
title: Jak uaktualnić Data Science Virtual Machine do Ubuntu 18,04
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się, jak uaktualnić z CentOS i Ubuntu 16,04 do najnowszej Ubuntu 18,04 Data Science Virtual Machine.
keywords: uczenie głębokie, AI, narzędzia do nauki o danych, maszyna wirtualna do nauki o danych, proces nauki danych zespołu
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 5b98384d4d735f4c124c6af40d6edbff896900ce
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320984"
---
# <a name="upgrade-your-data-science-virtual-machine-to-ubuntu-1804"></a>Uaktualnianie maszyny Data Science Virtual Machine do systemu Ubuntu 18.04

Jeśli masz Data Science Virtual Machine, na którym działa Starsza wersja, taka jak Ubuntu 16,04 lub CentOS, należy migrować DSVM do Ubuntu 18,04. Migrowanie zapewni uzyskanie najnowszych poprawek systemu operacyjnego, sterowników, wstępnie zainstalowanego oprogramowania i wersji biblioteki. Ten dokument zawiera informacje na temat migracji ze starszych wersji programu Ubuntu lub z programu CentOS. 

## <a name="prerequisites"></a>Wymagania wstępne

- Znajomość protokołu SSH i wiersza polecenia systemu Linux

## <a name="overview"></a>Omówienie

Istnieją dwa możliwe sposoby migracji:

- Migracja w miejscu, nazywana również migracją tego samego serwera. Ta migracja uaktualnia istniejącą MASZYNę wirtualną bez tworzenia nowej maszyny wirtualnej. Migracja w miejscu to łatwiejszy sposób migracji z Ubuntu 16,04 do Ubuntu 18,04.
- Migracja równoległa (Side-by-Side) nazywana również migracją między serwerami. Ta migracja przenosi dane z istniejącej maszyny wirtualnej na nowo utworzoną MASZYNę wirtualną. Migracja równoległa jest metodą migracji z CentOS do Ubuntu 18,04. Możesz preferować migrację równoległą do uaktualnienia między wersjami Ubuntu, jeśli uważasz, że stara instalacja stała się niepotrzebnie.

## <a name="snapshot-your-vm-in-case-you-need-to-roll-back"></a>Tworzenie migawek maszyny wirtualnej na wypadek konieczności wycofania

W Azure Portal Użyj paska wyszukiwania, aby znaleźć funkcję **migawek** . 

:::image type="content" source="media/ubuntu_upgrade/azure-portal-search-bar.png" alt-text="Zrzut ekranu przedstawiający Azure Portal i pasek wyszukiwania, z wyróżnionymi * * migawkami * *":::

1. Wybierz pozycję **Dodaj** , co spowoduje przejście do strony **Tworzenie migawki** . Wybierz subskrypcję i grupę zasobów maszyny wirtualnej. W polu **region** wybierz ten sam region, w którym znajduje się magazyn docelowy. Wybierz dysk magazynu DSVM i dodatkowe opcje tworzenia kopii zapasowej. **HDD w warstwie Standardowa** jest odpowiednim typem magazynu dla tego scenariusza tworzenia kopii zapasowych.

:::image type="content" source="media/ubuntu_upgrade/create-snapshot-options.png" alt-text="Zrzut ekranu przedstawiający opcje tworzenia migawek":::

2. Gdy wszystkie szczegóły są wypełnione i są prawidłowe, wybierz kolejno pozycje **Przegląd + Utwórz** , aby zweryfikować i utworzyć migawkę. Po pomyślnym zakończeniu migawki zostanie wyświetlony komunikat z informacją, że wdrożenie zostało zakończone.

## <a name="in-place-migration"></a>Migracja w miejscu

W przypadku migrowania starszej wersji Ubuntu można przeprowadzić migrację w miejscu. Ta migracja nie powoduje utworzenia nowej maszyny wirtualnej i ma mniejszą liczbę kroków niż migracja równoległa.  Jeśli chcesz wykonać migrację równoległą (Side-by-Side), ponieważ potrzebujesz większej kontroli lub ponieważ przeprowadzasz migrację z innej dystrybucji, takiej jak CentOS, przejdź do sekcji [migracja Side-by-Side](#side-by-side-migration) . 

1. Na Azure Portal Rozpocznij pracę z DSVM i zaloguj się przy użyciu protokołu SSH. Aby to zrobić, wybierz pozycję **Połącz** i **SSH** , a następnie postępuj zgodnie z instrukcjami połączenia. 

1. Po nawiązaniu połączenia z sesją terminalową na DSVM Uruchom następujące polecenie uaktualnienia:

    ```bash
    sudo do-release-upgrade
    ```

Proces uaktualniania zajmie trochę czasu. Po przekroczeniu tego programu program będzie pytał o zgodę na ponowne uruchomienie maszyny wirtualnej. Odpowiedź **tak**. Po ponownym uruchomieniu systemu nastąpi odłączenie od sesji SSH.

### <a name="if-necessary-regenerate-ssh-keys"></a>W razie potrzeby Wygeneruj ponownie klucze SSH

> [!IMPORTANT] 
> Po uaktualnieniu i ponownym uruchomieniu programu może być konieczne ponowne wygenerowanie kluczy SSH.

Po uaktualnieniu i ponownym uruchomieniu maszyny wirtualnej spróbuj uzyskać do niej dostęp za pośrednictwem protokołu SSH. Adres IP mógł ulec zmianie podczas ponownego rozruchu, więc potwierdź go przed podjęciem próby nawiązania połączenia.

Jeśli zostanie wyświetlony błąd **Identyfikacja hosta zdalnego została zmieniona** , należy ponownie wygenerować poświadczenia protokołu SSH.

:::image type="content" source="media/ubuntu_upgrade/remote-host-warning.png" alt-text="Zrzut ekranu programu PowerShell przedstawiający ostrzeżenie o zmianie identyfikacji hosta zdalnego":::

Aby to zrobić, na komputerze lokalnym Uruchom polecenie:

```bash
ssh-keygen -R "your server hostname or ip"
```

Teraz powinno być możliwe nawiązanie połączenia przy użyciu protokołu SSH. Jeśli nadal występują problemy, na stronie **Połącz** postępuj zgodnie z linkiem, aby **rozwiązać problemy z łącznością SSH**.

## <a name="side-by-side-migration"></a>Migracja równoległa

W przypadku migrowania z programu CentOS lub instalacji czystej systemu operacyjnego można przeprowadzić migrację równoległą. Ten typ migracji zawiera więcej kroków, ale zapewnia kontrolę nad tym, które pliki są przenoszone.

Migracja z innych systemów opartych na tym samym zestawie pakietów źródłowych nadrzędnych powinna być stosunkowo prosta, na przykład [często zadawanych pytań/CentOS3](https://wiki.centos.org/FAQ/CentOS3).

Możesz zdecydować się na uaktualnienie części systemu operacyjnego w systemie plików i pozostawienie katalogów użytkowników, np `/home` . na miejscu. W przypadku pozostawienia starych katalogów macierzystych użytkownika w miejscu należy oczekiwać niektórych problemów z menu GNOME/KDE i innych elementów pulpitu. Najłatwiejszym rozwiązaniem jest utworzenie nowych kont użytkowników i zainstalowanie starych katalogów znajdujących się w innym miejscu w systemie plików w celu odwoływania się, kopiowania lub łączenia materiału użytkownika po migracji.

### <a name="migration-at-a-glance"></a>Szybkie Migrowanie

1.  Utwórz migawkę istniejącej maszyny wirtualnej zgodnie z opisem wcześniej

1.  Utwórz dysk na podstawie tej migawki

1.  Utwórz nowy Data Science Virtual Machine Ubuntu

1.  Utwórz ponownie konta użytkowników na nowej maszynie wirtualnej

1.  Zainstaluj dysk maszyny wirtualnej snapshotted jako dysk danych w nowym Data Science Virtual Machine

1.  Ręczne kopiowanie pożądanych danych

### <a name="create-a-disk-from-your-vm-snapshot"></a>Tworzenie dysku na podstawie migawki maszyny wirtualnej

Jeśli migawka maszyny wirtualnej nie została jeszcze utworzona jak opisano wcześniej, zrób to. 

1. W Azure Portal Wyszukaj **dyski** i wybierz pozycję **Dodaj** , co spowoduje otwarcie strony **dysk** .

:::image type="content" source="media/ubuntu_upgrade/portal-disks-search.png" alt-text="Zrzut ekranu przedstawiający Azure Portal wyświetlania strony Wyszukiwanie dysków i przycisk Dodaj":::

2. Ustaw dla **subskrypcji** , **grupy zasobów** i **regionu** wartości z migawki maszyny wirtualnej. Wybierz **nazwę** dysku, który ma zostać utworzony.

3. Wybierz **Typ źródła** jako **migawkę** i wybierz migawkę maszyny wirtualnej jako **migawkę źródłową**. Przejrzyj i Utwórz dysk. 

:::image type="content" source="media/ubuntu_upgrade/disk-create-options.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe tworzenia dysku z opcjami":::

### <a name="create-a-new-ubuntu-data-science-virtual-machine"></a>Utwórz nowy Data Science Virtual Machine Ubuntu

Utwórz nowy Ubuntu Data Science Virtual Machine przy użyciu [Azure Portal](https://portal.azure.com) lub [szablonu ARM](./dsvm-tutorial-resource-manager.md). 

### <a name="recreate-user-accounts-on-your-new-data-science-virtual-machine"></a>Utwórz ponownie konta użytkowników na nowym Data Science Virtual Machine

Ponieważ właśnie skopiujesz dane ze starego komputera, musisz utworzyć ponownie wszystkie konta użytkowników i środowiska programowe, które mają być używane na nowym komputerze.

System Linux jest wystarczająco elastyczny, aby można było dostosować katalogi i ścieżki w nowej instalacji, aby postępować zgodnie z poprzednią maszyną. Ogólnie rzecz biorąc, łatwiej jest używać preferowanego układu nowoczesnej Ubuntu i modyfikować środowisko użytkownika i skrypty do dostosowania.

Aby uzyskać więcej informacji, zobacz [Szybki Start: konfigurowanie Data Science Virtual Machine dla systemu Linux (Ubuntu)](./dsvm-ubuntu-intro.md).

### <a name="mount-the-disk-of-the-snapshotted-vm-as-a-data-disk-on-your-new-data-science-virtual-machine"></a>Zainstaluj dysk maszyny wirtualnej snapshotted jako dysk danych w nowym Data Science Virtual Machine

1. W Azure Portal upewnij się, że Data Science Virtual Machine jest uruchomiony.

2. W Azure Portal przejdź do strony Data Science Virtual Machine. Wybierz blok **dyski** po lewej stronie szyny. Wybierz **Dołącz istniejące dyski**.

3. Z listy rozwijanej **Nazwa dysku** wybierz dysk utworzony na podstawie migawki starej maszyny wirtualnej.

:::image type="content" source="media/ubuntu_upgrade/attach-data-disk.png" alt-text="Zrzut ekranu przedstawiający stronę opcji DSVM z opcjami załączników dysku":::

4. Wybierz pozycję **Zapisz** , aby zaktualizować maszynę wirtualną.

> [!Important]
> Maszyna wirtualna powinna działać w momencie dołączenia dysku danych. Jeśli maszyna wirtualna nie jest uruchomiona, dyski mogą być dodawane w niepoprawnej kolejności, co prowadzi do rozruchowego systemu. W przypadku dodania dysku danych z maszyną wirtualną wybierz pozycję **X** obok dysku dane, uruchom maszynę wirtualną, a następnie Dołącz ją ponownie.

### <a name="manually-copy-the-wanted-data"></a>Ręczne kopiowanie pożądanych danych 

1. Zaloguj się do uruchomionej maszyny wirtualnej przy użyciu protokołu SSH.

2. Upewnij się, że dysk utworzony na podstawie migawki starej maszyny wirtualnej został dołączony, uruchamiając następujące polecenie:

    ```bash
    lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i 'sd'
    ```
    
    Wyniki powinny wyglądać podobnie do poniższej ilustracji. Na obrazie dysk `sda1` jest instalowany w katalogu głównym i `sdb2` jest `/mnt` dyskiem magazynującym. Dysk danych utworzony na podstawie migawki starej maszyny wirtualnej jest identyfikowany jako `sdc1` , ale nie jest jeszcze dostępny, co zostało potwierdzone przez brak lokalizacji instalacji. Wyniki mogą mieć różne identyfikatory, ale powinien zostać wyświetlony podobny wzorzec.
    
    :::image type="content" source="media/ubuntu_upgrade/lsblk-results.png" alt-text="Zrzut ekranu przedstawiający dane wyjściowe lsblk z niezainstalowanym dyskiem danych":::
    
3. Aby uzyskać dostęp do dysku danych, Utwórz dla niego lokalizację i zainstaluj ją. Zamień na `/dev/sdc1` odpowiednią wartość zwracaną przez `lsblk` :

    ```bash
    sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
    ```
    
4. Teraz program `/datadrive` zawiera katalogi i pliki starego Data Science Virtual Machine. Przenieś lub Skopiuj żądane katalogi lub pliki z dysku danych na nową maszynę wirtualną.

Aby uzyskać więcej informacji, zobacz [Używanie portalu do dołączania dysku danych do maszyny wirtualnej z systemem Linux](../../virtual-machines/linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk).

## <a name="connect-and-confirm-version-upgrade"></a>Nawiązywanie połączenia i Potwierdzanie uaktualnienia wersji

Niezależnie od tego, czy przeprowadzono migrację w miejscu, czy obok siebie, upewnij się, że pomyślnie uaktualniono. W sesji terminalu uruchom polecenie: 

```bash
cat /etc/os-release
```

Zobaczysz, że korzystasz z programu Ubuntu 18,04.

:::image type="content" source="media/ubuntu_upgrade/ssh-os-release.png" alt-text="Zrzut ekranu przedstawiający Terminal Ubuntu pokazujący dane wersji systemu operacyjnego":::

Zmiana wersji jest również pokazana w Azure Portal.

:::image type="content" source="media/ubuntu_upgrade/portal-showing-os-version.png" alt-text="Zrzut ekranu przedstawiający Portal pokazujący DSVM właściwości, w tym wersję systemu operacyjnego":::

## <a name="next-steps"></a>Następne kroki

- [Analiza danych z maszyną do nauki o danych Ubuntu na platformie Azure](./linux-dsvm-walkthrough.md)
- [Jakie narzędzia są dostępne na platformie Azure Data Science Virtual Machine?](./tools-included.md)