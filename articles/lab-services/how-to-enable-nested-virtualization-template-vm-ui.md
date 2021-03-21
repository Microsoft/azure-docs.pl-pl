---
title: Włącz wirtualizację zagnieżdżoną na maszynie wirtualnej szablonu w Azure Lab Services (interfejs użytkownika) | Microsoft Docs
description: Dowiedz się, jak utworzyć maszynę wirtualną szablonu z wieloma maszynami wirtualnymi wewnątrz.  Innymi słowy należy włączyć wirtualizację zagnieżdżoną na maszynie wirtualnej szablonu w Azure Lab Services.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: f8135e11fb7b7ddb588ab3a8ed01227712072fd2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94647923"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-manually"></a>Włącz zagnieżdżoną wirtualizację na maszynie wirtualnej szablonu Azure Lab Services ręcznie

Wirtualizacja zagnieżdżona umożliwia tworzenie środowiska z obsługą wiele maszyn wirtualnych w ramach maszyny wirtualnej szablonu laboratorium. Opublikowanie szablonu zapewni każdemu użytkownikowi w laboratorium z maszyną wirtualną skonfigurowaną z wieloma maszynami wirtualnymi.  Aby uzyskać więcej informacji na temat wirtualizacji zagnieżdżonej i Azure Lab Services, zobacz [Włączanie wirtualizacji zagnieżdżonej na maszynie wirtualnej szablonu w Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

W tym artykule opisano sposób konfigurowania wirtualizacji zagnieżdżonej na maszynie szablonu w Azure Lab Services przy użyciu bezpośrednio ról i narzędzi systemu Windows.  Istnieje kilka rzeczy, które są konieczne, aby umożliwić klasy użycie wirtualizacji zagnieżdżonej.  Poniższe kroki opisują sposób ręcznego konfigurowania szablonu maszyny usług laboratoryjnych za pomocą funkcji Hyper-V.  Kroki są przeznaczone dla systemu Windows Server 2016 lub Windows Server 2019.  

>[!IMPORTANT]
>Wybierz opcję **duże (Wirtualizacja zagnieżdżona)** lub **Średni (Wirtualizacja zagnieżdżona)** dla rozmiaru maszyny wirtualnej podczas tworzenia laboratorium.  Wirtualizacja zagnieżdżona nie będzie działała inaczej.  

## <a name="enable-hyper-v-role"></a>Włącz rolę funkcji Hyper-V

Poniższe kroki opisują akcje wymagane do włączenia funkcji Hyper-V w systemie Windows Server przy użyciu dowolnej Menedżer serwera.  Po pomyślnym zakończeniu instalacji Menedżer funkcji Hyper-V będzie dostępny do dodawania, modyfikowania i usuwania maszyn wirtualnych klienta.

1. W **Menedżer serwera** na stronie Pulpit nawigacyjny kliknij pozycję **Dodaj role i funkcje**.
2. Na stronie **Zanim rozpoczniesz** kliknij przycisk **Dalej**.
3. Na stronie **Wybieranie typu instalacji** Zachowaj domyślny wybór instalacji opartej na rolach lub funkcjach, a następnie kliknij przycisk **dalej**.
4. Na stronie **Wybieranie serwera docelowego** wybierz opcję Wybierz serwer z puli serwerów.   Bieżący serwer zostanie już wybrany.  Kliknij przycisk Dalej.
5. Na **Wybieranie ról serwera** Wybierz **funkcji Hyper-V**.  
6. Zostanie wyświetlone okno podręczne **Kreator dodawania ról i funkcji** .  Wybierz opcję **Dołącz narzędzia do zarządzania (jeśli dotyczy)**.  Kliknij przycisk **Dodaj funkcje** .
7. Na stronie **Wybieranie ról serwera** kliknij przycisk **Dalej**.
8. Na **stronie Wybieranie funkcji** kliknij przycisk **dalej**.
9. Na stronie **Hyper-V** kliknij przycisk **Dalej**.
10. Na stronie **Tworzenie przełączników wirtualnych** zaakceptuj ustawienia domyślne, a następnie kliknij przycisk **dalej**.
11. Na stronie **migracja maszyny wirtualnej** zaakceptuj wartości domyślne, a następnie kliknij przycisk **dalej**.
12. Na stronie **magazyny domyślne** zaakceptuj wartości domyślne, a następnie kliknij przycisk **dalej**.
13. Na stronie **Potwierdzanie opcji instalacji** zaznacz pole wyboru **Automatycznie uruchom ponownie serwer docelowy, jeśli** jest to wymagane.
14. Po wyświetleniu okna podręcznego **Kreatora dodawania ról i funkcji** kliknij przycisk **tak**.
15. Kliknij przycisk **Zainstaluj**.
16. Zaczekaj na stronę **postęp instalacji** , aby wskazać, że rola funkcji Hyper-V została ukończona.  Komputer może zostać ponownie uruchomiony w trakcie instalacji.
17. Kliknij przycisk **Zamknij**.

## <a name="enable-dhcp-role"></a>Włącz rolę DHCP

Wszystkie maszyny wirtualne klienta funkcji Hyper-V, w których są tworzone, muszą mieć adres IP w sieci NAT.  Sieć NAT zostanie utworzona później.  Jednym ze sposobów przypisywania adresów IP jest skonfigurowanie hosta. w tym przypadku szablon maszyny wirtualnej laboratorium jako serwer DHCP.  Poniżej przedstawiono kroki wymagane do włączenia roli DHCP.

1. W **Menedżer serwera** na stronie **pulpit nawigacyjny** kliknij pozycję **Dodaj role i funkcje**.
2. Na stronie **Zanim rozpoczniesz** kliknij przycisk **Dalej**.
3. Na **Wybieranie typu instalacji** Wybierz **Instalacja roli lub funkcji** a następnie kliknij przycisk **Dalej**.
4. Na stronie **Wybieranie serwera docelowego** wybierz bieżący serwer z puli serwerów, a następnie kliknij przycisk **dalej**.
5. Na stronie **Wybieranie ról serwera** wybierz opcję **serwer DHCP**.  
6. Zostanie wyświetlone okno podręczne **Kreator dodawania ról i funkcji** .  Wybierz opcję **Dołącz narzędzia do zarządzania (jeśli dotyczy)**.  Kliknij przycisk **dodawania funkcji**.

    >[!NOTE]
    >Może zostać wyświetlony komunikat o błędzie z informacją o tym, że nie znaleziono statycznych adresów IP.  To ostrzeżenie można zignorować w naszym scenariuszu.

7. Na stronie **Wybieranie ról serwera** kliknij przycisk **Dalej**.
8. Na stronie **Wybieranie funkcji** kliknij przycisk **dalej**.
9. Na stronie **serwer DHCP** kliknij przycisk **dalej**.
10. Na stronie **Potwierdzanie opcji instalacji** kliknij pozycję **Zainstaluj**.
11. Zaczekaj na **stronę postęp instalacji** , aby wskazać, że rola DHCP została ukończona.
12. Kliknij przycisk Zamknij.

## <a name="enable-routing-and-remote-access-role"></a>Włączanie roli Routing i dostęp zdalny

1. W **Menedżer serwera** na stronie **pulpit nawigacyjny** kliknij pozycję **Dodaj role i funkcje**.
2. Na stronie **Zanim rozpoczniesz** kliknij przycisk **Dalej**.
3. Na **Wybieranie typu instalacji** Wybierz **Instalacja roli lub funkcji** a następnie kliknij przycisk **Dalej**.
4. Na stronie **Wybieranie serwera docelowego** wybierz bieżący serwer z puli serwerów, a następnie kliknij przycisk **dalej**.
5. Na stronie **Wybieranie ról serwera** wybierz pozycję **dostęp zdalny**. Kliknij przycisk **OK**.
6. Na stronie **Wybieranie funkcji** kliknij przycisk **dalej**.
7. Na stronie **dostęp zdalny** kliknij przycisk **dalej**.
8. Na stronie **usługi ról** wybierz pozycję **Routing**.
9. Zostanie wyświetlone okno podręczne **Kreator dodawania ról i funkcji** .  Wybierz opcję **Dołącz narzędzia do zarządzania (jeśli dotyczy)**.  Kliknij przycisk **dodawania funkcji**.
10. Kliknij przycisk **Dalej**.
11. Na stronie **Rola serwera sieci Web (IIS)** kliknij przycisk **Dalej**.
12. Na ekranie **Wybieranie usług ról** kliknij przycisk **Dalej**.
13. Na stronie **Potwierdzanie opcji instalacji** kliknij pozycję **Zainstaluj**.
14. Zaczekaj na stronę **postęp instalacji** , aby wskazać, że rola dostępu zdalnego została ukończona.  
15. Kliknij przycisk **Zamknij**.

## <a name="create-virtual-nat-network"></a>Tworzenie sieci wirtualnej translatora adresów sieciowych

Teraz, gdy zainstalowano wszystkie niezbędne role, należy utworzyć sieć NAT.  Proces tworzenia obejmuje utworzenie przełącznika i sieci NAT.  Sieć NAT (translator adresów sieciowych) przypisuje publiczny adres IP do grupy maszyn wirtualnych w sieci prywatnej, aby umożliwić łączność z Internetem.  W naszym przypadku Grupa prywatnych maszyn wirtualnych będzie zagnieżdżoną maszynami wirtualnymi.  Sieci NAT umożliwią komunikację między maszynami wirtualnymi. Przełącznik to urządzenie sieciowe, które obsługuje odbieranie i routing ruchu w sieci.

### <a name="create-a-new-virtual-switch"></a>Utwórz nowy przełącznik wirtualny

1. Otwórz **Menedżera funkcji Hyper-V** z poziomu narzędzi administracyjnych systemu Windows.
2. Wybierz bieżący serwer w menu nawigacji po lewej stronie.
3. Kliknij pozycję **Menedżer przełącznika wirtualnego...** z menu **Akcje** po prawej stronie **Menedżera funkcji Hyper-V**.
4. W oknie podręcznym **Menedżera przełącznika wirtualnego** wybierz pozycję **wewnętrzne** dla typu przełącznika do utworzenia.  Kliknij przycisk **Utwórz przełącznik wirtualny**.
5. Dla nowo utworzonego przełącznika wirtualnego Ustaw nazwę na coś do zapamiętania.  W tym przykładzie użyjemy elementu "LabServicesSwitch".  Kliknij przycisk **OK**.
6. Zostanie utworzona nowa karta sieciowa.  Nazwa będzie podobna do "vEthernet (LabServicesSwitch)".  Aby sprawdzić, Otwórz **Panel sterowania**, kliknij pozycję **Sieć i Internet**, kliknij pozycję **Wyświetl stan sieci i zadania**.  Po lewej stronie kliknij pozycję **Zmień ustawienia karty sieciowej**.

### <a name="create-a-nat-network"></a>Tworzenie sieci NAT

1. Otwórz narzędzie **Routing i dostęp zdalny** z narzędzi administracyjnych systemu Windows.
2. Wybierz serwer lokalny na lewej stronie nawigacyjnej.
3. Wybierz **akcję**  ->  **Konfiguruj i Włącz Routing i dostęp zdalny**.
4. Gdy zostanie wyświetlony **Kreator instalacji serwera routingu i dostępu zdalnego** , kliknij przycisk **dalej**.
5. Na stronie **Konfiguracja** wybierz pozycję Konfiguracja **translatora adresów sieciowych (NAT)** .  Kliknij przycisk **Dalej**.

    >[!WARNING]
    >Nie wybieraj opcji "dostęp do wirtualnej sieci prywatnej (VPN)" i translatora adresów sieciowych.

6. Na stronie **połączenie internetowe NAT** wybierz opcję "Ethernet".  Nie wybieraj połączenia "vEthernet (LabServicesSwitch)" utworzonego w Menedżerze funkcji Hyper-V. Kliknij przycisk **Dalej**.
7. Kliknij przycisk **Zakończ** na ostatniej stronie kreatora.
8. Po wyświetleniu okna dialogowego **Uruchamianie usługi** kliknij pozycję **Uruchom usługę**.
9. Zaczekaj, aż usługa zostanie uruchomiona.

## <a name="update-network-adapter-settings"></a>Zaktualizuj ustawienia karty sieciowej

Karta sieciowa zostanie skojarzona z adresem IP używanym przez adres IP bramy domyślnej dla utworzonej wcześniej sieci NAT.  W tym przykładzie tworzymy adres IP 192.168.0.1 z maską podsieci 255.255.255.0.  Użyjemy utworzonego wcześniej przełącznika wirtualnego.

1. Otwórz **Panel sterowania**, kliknij pozycję **Sieć i Internet**, kliknij pozycję **Wyświetl stan sieci i zadania**.
2. Po lewej stronie kliknij pozycję **Zmień ustawienia karty sieciowej**.  
3. W oknie **połączenia sieciowe** kliknij dwukrotnie pozycję "VEthernet (LabServicesSwitch)", aby wyświetlić okno dialogowe Szczegóły **stanu vEthernet (LabServicesSwitch)** .
4. Kliknij przycisk **Właściwości** .
5. Wybierz pozycję **Protokół internetowy w wersji 4 (TCP/IPv4)** , a następnie kliknij przycisk **Właściwości** .
6. W oknie dialogowym **właściwości protokołu internetowego w wersji 4 (TCP/IPv4)** wybierz pozycję **Użyj następującego adresu IP**.  W polu adres IP wprowadź wartość 192.168.0.1. W polu Maska podsieci wpisz 255.255.255.0.  Pozostaw pustą bramę domyślną.  Pozostaw również puste serwery DNS.

    >[!NOTE]
    > Naszym zakresem dla naszej sieci NAT będzie, w notacji CIDR, 192.168.0.0/24.  Spowoduje to utworzenie zakresu użytecznych adresów IP od 192.168.0.1 do 192.168.0.254.  Zgodnie z Konwencją bramy mają pierwszy adres IP w zakresie podsieci.

7. Kliknij przycisk OK.

## <a name="create-dhcp-scope"></a>Tworzenie zakresu DHCP

Aby dodać zakres DHCP, należy wykonać poniższe czynności.  W tym artykule Nasza sieć NAT to 192.168.0.0/24 w notacji CIDR.  Spowoduje to utworzenie zakresu użytecznych adresów IP od 192.168.0.1 do 192.168.0.254.  Utworzony zakres musi znajdować się w tym zakresie adresów, z których można korzystać, z wyjątkiem wcześniej utworzonego adresu IP.

1. Otwórz **Narzędzia administracyjne** i Otwórz narzędzie administracyjne **DHCP** .
2. W narzędziu **DHCP** rozwiń węzeł bieżącego serwera i wybierz pozycję **IPv4**.
3. Z menu Akcja wybierz **Nowy zakres...**
4. Po wyświetleniu **Kreatora nowego zakresu** kliknij przycisk **dalej** na stronie **powitalnej** .
5. Na stronie **Nazwa zakresu** wprowadź wartość "LabServicesDhcpScope" lub inną dopamiętaną nazwę.  Kliknij przycisk **Dalej**.
6. Na stronie **zakres adresów IP** wprowadź następujące wartości.

    - 192.168.0.100 na początkowy adres IP
    - 192.168.0.200 dla końcowego adresu IP
    - 24 dla długości
    - 255.255.255.0 dla maski podsieci

7. Kliknij przycisk **Dalej**.
8. Na stronie **Dodaj wykluczenia i Opóźnij** kliknij przycisk **dalej**.
9. Na stronie **czas trwania dzierżawy** kliknij przycisk **dalej**.
10. Na stronie **Konfigurowanie opcji DHCP** wybierz opcję **tak, chcę teraz skonfigurować te opcje**. Kliknij przycisk **Dalej**.
11. Na **routerze (Brama domyślna)**
12. Dodaj 192.168.0.1, jeśli jeszcze nie zostało to zrobione. Kliknij przycisk **Dalej**.
13. Na stronie **nazwa domeny i serwery DNS** Dodaj 168.63.129.16 jako adres IP serwera DNS, jeśli jeszcze tego nie zrobiono.  168.63.129.16 to adres IP dla statycznego serwera DNS platformy Azure. Kliknij przycisk **Dalej**.
14. Na stronie **serwery WINS** kliknij przycisk **dalej**.
15. Na stronie **Uaktywnij zakres** wybierz pozycję **tak, chcę uaktywnić ten zakres teraz**.  Kliknij przycisk **Dalej**.
16. Na stronie **Kończenie pracy Kreatora nowych zakresów** kliknij przycisk **Zakończ**.

## <a name="conclusion"></a>Podsumowanie

Teraz komputer z szablonem jest gotowy do tworzenia maszyn wirtualnych funkcji Hyper-V.   Aby uzyskać instrukcje dotyczące tworzenia maszyn wirtualnych funkcji Hyper-V, zobacz temat [Tworzenie maszyny wirtualnej w funkcji Hyper-v](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) .  Zobacz również [Centrum oceny firmy Microsoft](https://www.microsoft.com/evalcenter/) , aby wyewidencjonować dostępne systemy operacyjne i oprogramowanie.

## <a name="next-steps"></a>Następne kroki

Następne kroki są wspólne do konfigurowania dowolnego laboratorium.

- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustaw przydział](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Linki do rejestracji w wiadomościach e-mail z uczniami](how-to-configure-student-usage.md#send-invitations-to-users)