---
title: Rozwiązywanie problemów dedykowanego modułu HSM — dedykowany moduł HSM platformy Azure | Microsoft Docs
description: Omówienie dedykowanego modułu HSM platformy Azure udostępniającego funkcje magazynu kluczy na platformie Azure, który spełnia wymogi certyfikatu FIPS 140-2 poziom 3
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: 622ead2ab58075fe6edbe2c013f14391624fd2b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88590459"
---
# <a name="troubleshooting-the-azure-dedicated-hsm-service"></a>Rozwiązywanie problemów z usługą dedykowanego modułu HSM platformy Azure

Dedykowana usługa HSM platformy Azure ma dwa różne zestawy reguł. Po pierwsze Rejestracja i wdrożenie na platformie Azure urządzeń HSM z ich podstawowymi składnikami sieciowymi. Na koniec konfiguracja urządzeń HSM w przygotowaniu do użycia/integracji z danym obciążeniem lub aplikacją. Mimo że urządzenia HSM sieci firmy Thales Luna są takie same na platformie Azure, jak w przypadku zakupu bezpośrednio od firmy Thales, fakt, że jest to zasób na platformie Azure, tworzy pewne unikatowe uwagi. Te zagadnienia i wszelkie wynikłe rozwiązania dotyczące rozwiązywania problemów oraz najlepsze rozwiązania są udokumentowane w tym miejscu, aby zapewnić wysoką widoczność i dostęp do krytycznych informacji. Gdy usługa jest używana, ostateczne informacje są dostępne za pośrednictwem żądań pomocy technicznej bezpośrednio do firmy Microsoft lub firmy Thales. 

> [!NOTE]
> Należy zauważyć, że przed wykonaniem jakiejkolwiek konfiguracji na nowo wdrożonym urządzeniu HSM, należy je zaktualizować przy użyciu wszelkich odpowiednich poprawek. Konkretna wymagana poprawka to [KB0019789](https://supportportal.gemalto.com/csm?id=kb_article_view&sys_kb_id=19a81c8bdb9a1fc8d298728dae96197d&sysparm_article=KB0019789) w portalu pomocy technicznej firmy Thales, który rozwiązuje problem, w którym system przestaje odpowiadać podczas ponownego uruchamiania.

## <a name="hsm-registration"></a>Rejestracja modułu HSM

Dedykowany moduł HSM nie jest dostępny do użycia w miarę dostarczania zasobów sprzętowych w chmurze, a tym samym jest cennym zasobem, który wymaga ochrony. W związku z tym korzystamy z procesu listy dozwolonych za pośrednictwem poczty e-mail HSMrequest@microsoft.com . 

### <a name="getting-access-to-dedicated-hsm"></a>Uzyskiwanie dostępu do dedykowanego modułu HSM

Jeśli uważasz, że dedykowany moduł HSM będzie pasował do wymagań dotyczących magazynu kluczy, Wyślij wiadomość e-mail HSMrequest@microsoft.com do żądania dostępu. Utwórz konspekt aplikacji, regiony, które chcesz sprzętowych modułów zabezpieczeń i ilość szukanych sprzętowych modułów zabezpieczeń. Jeśli pracujesz z przedstawicielem firmy Microsoft, takim jak dyrektor ds. kont lub architektem rozwiązań w chmurze, na przykład Dołącz je do każdego żądania.

## <a name="hsm-provisioning"></a>Inicjowanie obsługi modułu HSM

Inicjowanie obsługi urządzenia HSM na platformie Azure można wykonać za pomocą interfejsu wiersza polecenia lub programu PowerShell. Po zarejestrowaniu się w usłudze zostanie udostępniony przykładowy szablon ARM i będzie można uzyskać pomoc dotyczącą wstępnego dostosowywania. 

### <a name="hsm-deployment-failure-information"></a>Informacje o niepowodzeniach wdrożenia modułu HSM

Dedykowany moduł HSM obsługuje interfejs wiersza polecenia i program PowerShell dla wdrożenia, aby informacje o błędach w portalu były ograniczone i niepełne. Lepsze informacje można znaleźć za pomocą Eksplorator zasobów. Na stronie głównej portalu znajduje się ikona tego i bardziej szczegółowe informacje o błędzie są dostępne. Te informacje ułatwiają wiele sytuacji, w których podczas tworzenia żądania pomocy technicznej związanego z wdrażaniem zawarto bardzo dużo uwagi.

![Informacje o niepowodzeniach](./media/troubleshoot/failure-information.png)

### <a name="hsm-subnet-delegation"></a>Delegowanie podsieci modułu HSM
Liczba jednej przyczyny niepowodzenia wdrożenia to zapominanie o ustawieniu odpowiedniego delegowania dla podsieci zdefiniowanej przez klienta, na której zostanie zainicjowana sprzętowych modułów zabezpieczeń. Ustawienie delegowania jest częścią sieci wirtualnej i wymagań wstępnych podsieci do wdrożenia. więcej szczegółów można znaleźć w samouczkach.

![Delegowanie podsieci](./media/troubleshoot/subnet-delegation.png)

### <a name="hsm-deployment-race-condition"></a>Warunek wyścigu wdrożenia modułu HSM

Standardowy szablon ARM udostępniony do wdrożenia ma zasoby dotyczące modułów HSM i ExpressRoute Gateway. Zasoby sieciowe są zależnością do pomyślnego wdrożenia modułu HSM, a chronometraż może być decydujący.  Czasami pojawiły się błędy wdrażania związane z problemami zależności i ponowne uruchomienie wdrożenia często rozwiązuje problem. W przeciwnym razie usunięcie zasobów i ponowne wdrożenie często powiodło się. Po próbie tego problemu i dalszym znalezieniu go Zgłoś żądanie pomocy technicznej w Azure Portal wybranie typu problemu "problemy z skonfigurowaniem konfiguracji platformy Azure".

### <a name="hsm-deployment-using-terraform"></a>Wdrażanie modułu HSM przy użyciu Terraform

Kilku klientów użył Terraform jako środowiska automatyzacji zamiast szablonów ARM, które są dostarczane podczas rejestrowania dla tej usługi. Sprzętowych modułów zabezpieczeń nie można wdrożyć w ten sposób, ale zależne zasoby sieciowe mogą. Terraform ma moduł do wywołania minimalnego szablonu ARM, który JUT ma wdrożenie HSM.  W takiej sytuacji należy zachować ostrożność, aby zapewnić, że zasoby sieciowe, takie jak wymagana Brama ExpressRoute są w pełni wdrożone przed wdrożeniem sprzętowych modułów zabezpieczeń. Poniższe polecenie interfejsu wiersza polecenia może służyć do testowania wdrożenia zakończonego i zintegrowanego zgodnie z wymaganiami. Zastąp symbole kątowe odniesień do określonych nazw. Należy wyszukać wynik "provisioningState jest powodzenie"

```azurecli
az resource show --ids /subscriptions/<subid>/resourceGroups/<myresourcegroup>/providers/Microsoft.Network/virtualNetworkGateways/<myergateway>
```

### <a name="deployment-failure-based-on-quota"></a>Niepowodzenie wdrożenia na podstawie limitu przydziału
Wdrożenia mogą zakończyć się niepowodzeniem w przypadku przekroczenia 2 sprzętowych modułów zabezpieczeń na sygnaturę i 4 sprzętowych modułów zabezpieczeń na region. Aby uniknąć tej sytuacji, upewnij się, że usunięto zasoby z wcześniej zakończonych niepowodzeniem wdrożeń przed ponownym wdrożeniem. Zapoznaj się z poniższym elementem "Jak mogę See sprzętowych modułów zabezpieczeń", aby sprawdzić zasoby. Jeśli uważasz, że musisz przekroczyć ten limit przydziału, który jest przede wszystkim w ramach zabezpieczeń, Wyślij wiadomość e-mail HSMrequest@microsoft.com ze szczegółowymi informacjami.

### <a name="deployment-failure-based-on-capacity"></a>Niepowodzenie wdrażania na podstawie pojemności
Gdy określona sygnatura lub region jest zapełniony, oznacza to, że prawie wszystkie bezpłatne sprzętowych modułów zabezpieczeń są obsługiwane, co może prowadzić do niepowodzeń wdrażania. Każda sygnatura ma 11 sprzętowych modułów zabezpieczeń dostępnych dla klientów, co oznacza 22 dla regionu. W każdej sygnaturze znajdują się również 3 zapasy i 1 urządzenie testowe. Jeśli uważasz, że osiągnięto limit, Wyślij wiadomość e-mail HSMrequest@microsoft.com w celu uzyskania informacji na temat wypełniania określonych sygnatur.

###  <a name="how-do-i-see-hsms-when-provisioned"></a>Jak mogę zobaczyć sprzętowych modułów zabezpieczeń po zainicjowaniu obsługi administracyjnej?
Ze względu na dedykowany moduł HSM, który jest usługą listy dozwolonych, jest traktowany jako "typ ukryty" w Azure Portal. Aby wyświetlić zasoby HSM, należy zaznaczyć pole wyboru "Pokaż ukryte typy", jak pokazano poniżej. Zasób karty sieciowej zawsze jest zgodny z modułem HSM i jest dobrym miejscem, aby znaleźć adres IP modułu HSM przed użyciem protokołu SSH do nawiązania połączenia.

![Delegowanie podsieci](./media/troubleshoot/hsm-provisioned.png)

## <a name="networking-resources"></a>Zasoby sieciowe

Wdrożenie dedykowanego modułu HSM ma zależność od zasobów sieciowych i niektórych wynikających z nich ograniczeń.

### <a name="provisioning-expressroute"></a>ExpressRoute aprowizacji

Dedykowany moduł HSM używa bramy ExpressRoute jako "tunel" do komunikacji między prywatną przestrzenią adresów IP klienta i fizycznym modułem HSM w centrum danych platformy Azure.  Biorąc pod uwagę, że istnieje ograniczenie jednej bramy dla sieci wirtualnej, klienci wymagający połączenia z zasobami lokalnymi za pośrednictwem usługi ExpressRoute będą musieli użyć innej sieci wirtualnej dla tego połączenia.  

### <a name="hsm-private-ip-address"></a>Prywatny adres IP modułu HSM

Przykładowe szablony udostępniane dla dedykowanego modułu HSM zakładają, że adres IP modułu HSM zostanie automatycznie pobrany z danego zakresu podsieci. Można określić jawny adres IP dla modułu HSM za pośrednictwem atrybutu "NetworkInterfaces" w szablonie ARM. 

![Delegowanie podsieci](./media/troubleshoot/private-ip-address.png)

## <a name="hsm-initialization"></a>Inicjowanie modułu HSM

Inicjalizacja przygotowuje nowy moduł HSM do użycia lub istniejący moduł HSM do ponownego użycia. Inicjalizacja modułu HSM musi zostać zakończona, aby można było generować lub przechowywać obiekty, zezwalać klientom na łączenie lub wykonywać operacje kryptograficzne.

### <a name="lost-credentials"></a>Utracone poświadczenia

Utrata hasła administratora powłoki spowoduje utratę materiału klucza HSM. Należy wprowadzić żądanie pomocy technicznej w celu zresetowania modułu HSM.
Podczas inicjowania modułu HSM bezpieczne przechowywanie poświadczeń. Poświadczenia powłoki i modułu HSM powinny być przechowywane zgodnie z zasadami obowiązującymi w firmie.

### <a name="failed-logins"></a>Nieudane logowania

Dostarczenie nieprawidłowych poświadczeń do sprzętowych modułów zabezpieczeń może mieć konsekwencje destrukcyjne. Poniżej przedstawiono domyślne zachowania ról modułu HSM.

| Rola | Próg (liczba prób) | Wynik zbyt dużej liczby nieudanych prób logowania | Odzyskiwania |
|--|--|--|--|
| MODUŁ HSM | 3 |  Moduł HSM jest zerowy (wszystkie tożsamości obiektów HSM i wszystkie partycje zostaną usunięte)  |  Moduł HSM musi zostać ponownie zainicjowany. Zawartość można przywrócić z kopii zapasowych. | 
| Podziel na partycje | 10 |  Partycja ma wartość zero. |  Partycja musi zostać zainicjowana ponownie. Zawartość można przywrócić z kopii zapasowej. |  
| Inspekcja | 10 | Blokad | Odblokowane automatycznie po 10 minutach. |  
| Oficer kryptograficzny | 10 (można je zmniejszyć) | Jeśli zasady HSM 15: Enable, tak aby Resetowanie numeru PIN partycji zostało ustawione na 1 (włączone), role CO i CU są zablokowane.<br>Jeśli zasady HSM 15: Enable, tak aby Resetowanie numeru PIN partycji zostało ustawione na 0 (wyłączone), role CO i CU są trwale zablokowane i zawartość partycji nie jest już dostępna. Jest to ustawienie domyślne. | Rola CO należy odblokować, a poświadczenia są resetowane przez partycję, a więc przy użyciu polecenia `role resetpw -name co` .<br>Partycja musi być zainicjowana ponownie i materiał klucza przywrócony z urządzenia kopii zapasowej. |  

## <a name="hsm-configuration"></a>Konfiguracja modułu HSM 

Poniżej znajdują się sytuacje, w których błędy konfiguracji są wspólne lub mają wpływ zaufanego się na wywołania:

### <a name="hsm-documentation-and-software"></a>Dokumentacja i oprogramowanie modułu HSM
Oprogramowanie i dokumentacja urządzeń HSM firmy Thales SafeNet Luna 7 nie są dostępne w firmie Microsoft i muszą zostać pobrane bezpośrednio z usługi firmy Thales. Rejestracja jest wymagana przy użyciu identyfikatora klienta firmy Thales otrzymanego podczas procesu rejestracji. Urządzenia, zgodnie z oczekiwaniami firmy Microsoft, mają oprogramowanie wersja 7,2 i oprogramowanie układowe 7.0.3. Wcześniej w 2020 firmy Thales dokumentację publiczną i można ją znaleźć [tutaj](https://thalesdocs.com/gphsm/luna/7.2/docs/network/Content/Home_network.htm).  

### <a name="hsm-networking-configuration"></a>Konfiguracja sieci HSM

Należy zachować ostrożność podczas konfigurowania sieci w module HSM.  Moduł HSM ma połączenie za pośrednictwem bramy ExpressRoute z prywatnego obszaru adresów IP klienta bezpośrednio do modułu HSM.  Ten kanał komunikacyjny dotyczy tylko komunikacji z klientem, a firma Microsoft nie ma dostępu. Jeśli moduł HSM jest skonfigurowany w taki sposób, że ma to wpływ na tę ścieżkę sieciową, oznacza to, że cała komunikacja z modułem HSM zostanie usunięta.  W takiej sytuacji jedyną opcją jest podnoszenie żądania pomocy technicznej firmy Microsoft za pośrednictwem Azure Portal w celu zresetowania urządzenia. Ta procedura resetowania ustawia moduł HSM z powrotem do stanu początkowego, a cała konfiguracja i materiał klucza zostaną utracone.  Należy ponownie utworzyć konfigurację i po dołączeniu urządzenia do grupy HA pobieranie materiału klucza zostanie zreplikowane.  

### <a name="hsm-device-reboot"></a>Ponowne uruchomienie urządzenia HSM

Niektóre zmiany konfiguracji wymagają włączenia lub ponownego uruchomienia modułu HSM. Testy firmy Microsoft dotyczące modułu HSM na platformie Azure ustaliły, że w niektórych przypadkach ponowne uruchomienie może przestać odpowiadać. W takim przypadku należy utworzyć żądanie pomocy technicznej w Azure Portal zażądać twardego ponownego uruchomienia komputera, co może potrwać do 48 godzin, biorąc pod uwagę, że jest to proces ręczny w centrum danych platformy Azure.  Aby uniknąć tej sytuacji, upewnij się, że wdrożono poprawkę ponownego uruchamiania dostępną bezpośrednio z usługi firmy Thales. Zapoznaj się z artykułem [KB0019789](https://supportportal.gemalto.com/csm?sys_kb_id=d66911e2db4ffbc0d298728dae9619b0&id=kb_article_view&sysparm_rank=1&sysparm_tsqueryId=d568c35bdb9a4850d6b31f3b4b96199e&sysparm_article=KB0019789) w programie firmy Thales Luna Network HSM 7,2 pobieranie dla zalecanej poprawki, aby rozwiązać problem, w którym system przestaje odpowiadać podczas ponownego uruchamiania (Uwaga: należy zarejestrować się w portalu pomocy technicznej firmy Thales do pobrania).

### <a name="ntls-certificates-out-of-sync"></a>NTLS certyfikaty poza synchronizacją
Klient może utracić połączenie z modułem HSM, gdy certyfikat wygaśnie lub został nadpisany przez aktualizacje konfiguracji. Konfiguracja klienta wymiany certyfikatów powinna być stosowana ponownie z każdym modułem HSM.
Przykład rejestrowania NTLS z nieprawidłowym certyfikatem:

> NTLS [8508]: info: 0: żądanie połączenia przychodzącego...: 192.168.50.2/59415 NTLS [8508]: komunikat o błędzie z SSLAccept: błąd: 14094418: procedury SSL: ssl3_read_bytes: TLSv1 alert nieznany urząd certyfikacji NTLS [8508]: Wystąpił błąd podczas akceptowania protokołu SSL (RC_SSL_ERROR) NTLS [8508]: info: 0xc0000711: nie można nawiązać bezpiecznego kanału z klientem: 192.168.50.2/59415: RC_SSL_FAILED_HANDSHAKE NTLS [8508]: info: 0: NTLS Client "Nieznana nazwa hosta" usunięte wystąpienie połączenia: 192.168.50.2/59415

### <a name="failed-tcp-communication"></a>Komunikacja TCP zakończona niepowodzeniem

Komunikacja z instalacją klienta Luna do modułu HSM wymaga co najmniej portu TCP 1792. Należy wziąć pod uwagę, że wszystkie konfiguracje sieci zostaną zmienione w środowisku.

### <a name="failed-ha-group-member-doesnt-recover"></a>Niepowodzenie odzyskania elementu członkowskiego grupy HA

Jeśli niepomyślny członek grupy HA nie odzyska tego, należy ręcznie odzyskać go z klienta Luna za pomocą polecenia hagroup Recover.
Aby włączyć Autoodzyskiwanie, należy skonfigurować liczbę ponownych prób dla grupy HA. Domyślnie grupa HA nie będzie podejmować próby odzyskania elementu członkowskiego HA do grupy podczas odzyskiwania.

### <a name="ha-group-doesnt-sync"></a>Grupa HA nie jest zsynchronizowana

W przypadku, gdy partycje elementów członkowskich nie mają tej samej domeny klonowania, polecenie synchronizacji ha wyświetli następujący komunikat: Ostrzeżenie: Synchronizacja może zakończyć się niepowodzeniem.  Elementy członkowskie w gnieździe 0 i gnieździe 1 zawierają ustawienia powodujące konflikt klonowania klucza prywatnego.
Do grupy HA należy dodać nową partycję z poprawną domeną klonowania, a następnie usunąć niepoprawnie skonfigurowaną partycję.

## <a name="hsm-deprovisioning"></a>Anulowanie aprowizacji modułu HSM 

Tylko wtedy, gdy w pełni zakończysz pracę z modułem HSM, można anulować obsługę administracyjną, a następnie firma Microsoft zresetuje ją i zwróci ją do puli bezpłatna. 

### <a name="how-to-delete-an-hsm-resource"></a>Jak usunąć zasób HSM

Nie można usunąć zasobu platformy Azure dla modułu HSM, jeśli moduł HSM nie jest w stanie "zero".  W związku z tym przed próbą usunięcia go jako zasobu należy usunąć cały materiał klucza. Najszybszym sposobem na zeroize jest uzyskanie nieprawidłowych haseł administratora modułu HSM (Uwaga: dotyczy to administratorów modułu HSM, a nie administratora na poziomie urządzenia). Powłoka Luna ma `hsm -factoryreset` polecenie, które zeroizes, ale może być wykonywane tylko przez konsolę na porcie seryjnym, a klienci nie mają do niej dostępu.

## <a name="next-steps"></a>Następne kroki

W tym artykule udostępniono wgląd w obszary w całym cyklu życia wdrożenia modułu HSM, które mogą mieć problemy lub wymagają podania problemów. Miejmy nadzieję ten artykuł pomaga uniknąć niepotrzebnych opóźnień i frustrację, a jeśli masz odpowiednie dodatki lub zmiany, Zgłoś żądanie pomocy technicznej firmie Microsoft i daj nam znać. 
