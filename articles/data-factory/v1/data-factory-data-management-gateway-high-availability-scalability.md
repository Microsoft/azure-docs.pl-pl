---
title: Wysoka dostępność z bramą zarządzania danymi w Azure Data Factory
description: W tym artykule wyjaśniono, jak można skalować bramę zarządzania danymi przez dodawanie kolejnych węzłów i skalowanie w górę przez zwiększenie liczby współbieżnych zadań, które można uruchomić w węźle.
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: ad34ed14682d729157f45e67eb3e0d3bb3eb39b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100391732"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Zarządzanie danymi Gateway — wysoka dostępność i skalowalność (wersja zapoznawcza)
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [własne środowisko Integration Runtime w](../create-self-hosted-integration-runtime.md)artykule. 


Ten artykuł ułatwia skonfigurowanie rozwiązania wysokiej dostępności i skalowalności przy użyciu bramy Zarządzanie danymi/integracji.    

> [!NOTE]
> W tym artykule założono, że znasz już podstawy Integration Runtime (wcześniejsza Zarządzanie danymi Gateway). Jeśli nie, zobacz [Zarządzanie danymi Gateway](data-factory-data-management-gateway.md).
> 
> **Ta funkcja w wersji zapoznawczej jest oficjalnie obsługiwana w usłudze Zarządzanie danymi Gateway 2.12. xxxx. x lub nowszej**. Upewnij się, że używasz wersji 2.12. xxxx. x lub nowszej. Pobierz najnowszą wersję Zarządzanie danymi Gateway [tutaj](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="overview"></a>Omówienie
Bramy zarządzania danymi, które są zainstalowane na wielu maszynach lokalnych z jedną bramą logiczną, można kojarzyć z portalem. Te komputery są nazywane **węzłami**. Do bramy logicznej można skojarzyć maksymalnie **cztery węzły** . Zalety posiadania wielu węzłów (maszyn lokalnych z zainstalowaną bramą) dla bramy logicznej są następujące:  

- Poprawa wydajności przenoszenia danych między lokalnymi i magazynami danych w chmurze.  
- Jeśli jeden z węzłów ulegnie awarii z jakiegoś powodu, inne węzły są nadal dostępne do przeniesienia danych. 
- Jeśli jeden z węzłów musi zostać przełączony w tryb offline w celu przeprowadzenia konserwacji, inne węzły są nadal dostępne do przeniesienia danych.

Można także skonfigurować liczbę **współbieżnych zadań przenoszenia danych** , które mogą być uruchamiane w węźle w celu skalowania w górę możliwości przenoszenia danych między lokalnymi i magazynami danych w chmurze. 

Za pomocą Azure Portal można monitorować stan tych węzłów, co pomaga zdecydować, czy dodać lub usunąć węzeł z bramy logicznej. 

## <a name="architecture"></a>Architektura 
Na poniższym diagramie przedstawiono przegląd architektury funkcji skalowalności i dostępności bramy Zarządzanie danymi: 

![Zarządzanie danymi Gateway — wysoka dostępność i skalowalność](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

**Brama logiczna** jest bramą dodawaną do fabryki danych w Azure Portal. Wcześniej można było skojarzyć tylko jedną lokalną maszynę z systemem Windows z usługą Zarządzanie danymi Gateway zainstalowaną z bramą logiczną. Ta lokalna maszyna Gateway jest nazywana węzłem. Teraz można skojarzyć maksymalnie **cztery węzły fizyczne** z bramą logiczną. Brama logiczna z wieloma węzłami nosi nazwę **bramy wielowęzłowej**.  

Wszystkie te węzły są **aktywne**. Wszystkie mogą przetwarzać zadania przenoszenia danych w celu przenoszenia danych między lokalnymi i magazynami danych w chmurze. Jeden z węzłów działa zarówno jako Dyspozytor, jak i proces roboczy. Innymi węzłami w grupach są węzły procesu roboczego. Węzeł **dyspozytora** pobiera zadania/zadania przenoszenia danych z usługi w chmurze i wysyła je do węzłów procesu roboczego (w tym samego siebie). Węzeł **procesu roboczego** wykonuje zadania przenoszenia danych w celu przenoszenia danych między lokalnymi i magazynami danych w chmurze. Wszystkie węzły są pracownikami. Tylko jeden węzeł może być wysyłany i proces roboczy.    

Zazwyczaj może zaczynać się jeden węzeł i **skalować w poziomie** , aby dodać więcej węzłów, ponieważ istniejące węzły są przeciążone przy użyciu obciążenia przenoszenia danych. Możesz również **skalować w górę** możliwości przenoszenia danych w węźle bramy, zwiększając liczbę współbieżnych zadań, które mogą być uruchamiane w węźle. Ta funkcja jest również dostępna w przypadku bramy z jednym węzłem (nawet wtedy, gdy funkcja skalowalności i dostępności nie jest włączona). 

Brama z wieloma węzłami przechowuje poświadczenia magazynu danych w synchronizacji między wszystkimi węzłami. Jeśli występuje problem z łącznością między węzłami, poświadczenia mogą nie być zsynchronizowane. Po ustawieniu poświadczeń dla lokalnego magazynu danych korzystającego z bramy program zapisuje poświadczenia w węźle dyspozytora/procesu roboczego. Węzeł dyspozytora jest synchronizowany z innymi węzłami procesu roboczego. Ten proces jest nazywany **synchronizacją poświadczeń**. Kanał komunikacji między węzłami może być **szyfrowany** za pomocą publicznego certyfikatu SSL/TLS. 

## <a name="set-up-a-multi-node-gateway"></a>Konfigurowanie bramy wielowęzłowej
W tej sekcji założono, że przeniesiono następujące dwa artykuły lub zapoznaj się z pojęciami w tych artykułach: 

- [Zarządzanie danymi Gateway](data-factory-data-management-gateway.md) — zawiera szczegółowe omówienie bramy.
- [Przenoszenie danych między lokalnymi i magazynami danych w chmurze](data-factory-move-data-between-onprem-and-cloud.md) — zawiera przewodnik z instrukcjami krok po kroku dotyczącymi używania bramy z pojedynczym węzłem.  

> [!NOTE]
> Przed zainstalowaniem bramy zarządzania danymi na lokalnym komputerze z systemem Windows Zapoznaj się z wymaganiami wstępnymi wymienionymi w [artykule głównym](data-factory-data-management-gateway.md#prerequisites).

1. W [instruktażu](data-factory-move-data-between-onprem-and-cloud.md#create-gateway)podczas tworzenia bramy logicznej Włącz funkcję **skalowalności & o wysokiej dostępności** . 

    ![Zarządzanie danymi Gateway — Włącz wysoką dostępność i skalowalność](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. Na stronie **Konfiguracja** Użyj **Ustawienia ekspresowego** lub **Ustawienia ręcznego instalacji** , aby zainstalować bramę w pierwszym węźle (na lokalnym komputerze z systemem Windows).

    ![Zarządzanie danymi Gateway — konfiguracja ekspresowa lub ręczna](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > W przypadku korzystania z opcji instalacji ekspresowej komunikacja między węzłami odbywa się bez szyfrowania. Nazwa węzła jest taka sama jak nazwa komputera. Użyj konfiguracji ręcznej, Jeśli komunikacja węzła węzła musi być zaszyfrowana lub chcesz określić wybraną nazwę węzła. Nazwy węzłów nie mogą być edytowane później.
3. W przypadku wybrania opcji **Instalacja ekspresowa**
    1. Po pomyślnym zainstalowaniu bramy zostanie wyświetlony następujący komunikat:

        ![Zarządzanie danymi Gateway — powodzenie instalacji ekspresowej](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Uruchom Zarządzanie danymi Configuration Manager dla bramy, wykonując [te instrukcje](data-factory-data-management-gateway.md#configuration-manager). Zobaczysz nazwę bramy, nazwę węzła, stan itp.

        ![Zrzut ekranu pokazujący, gdzie można zobaczyć nazwę bramy, nazwę węzła i stan.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. W przypadku wybrania opcji **Konfiguracja ręczna**:
    1. Pobierz pakiet instalacyjny z centrum pobierania Microsoft, uruchom go, aby zainstalować bramę na maszynie.
    2. Użyj **klucza uwierzytelniania** ze strony **Konfiguruj** , aby zarejestrować bramę.
    
        ![Zrzut ekranu pokazujący, gdzie używać klucza uwierzytelniania.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. Na stronie **nowy węzeł bramy** można podać niestandardową **nazwę** węzła bramy. Domyślnie nazwa węzła jest taka sama jak nazwa komputera.    

        ![Zarządzanie danymi Gateway — Określ nazwę](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. Na następnej stronie można zdecydować, czy **włączyć szyfrowanie na potrzeby komunikacji między** węzłami. Kliknij przycisk **Pomiń** , aby wyłączyć szyfrowanie (domyślnie).

        ![Zarządzanie danymi Gateway — Włączanie szyfrowania](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > Zmiana trybu szyfrowania jest obsługiwana tylko w przypadku, gdy w bramie logicznej znajduje się pojedynczy węzeł bramy. Aby zmienić tryb szyfrowania, gdy brama ma wiele węzłów, wykonaj następujące czynności: Usuń wszystkie węzły z wyjątkiem jednego węzła, Zmień tryb szyfrowania, a następnie ponownie dodaj węzły.
        > 
        > Listę wymagań dotyczących korzystania z certyfikatu TLS/SSL zawiera sekcja [wymagania dotyczące certyfikatu TLS/SSL](#tlsssl-certificate-requirements) . 
    5. Po pomyślnym zainstalowaniu bramy kliknij przycisk Uruchom Configuration Manager:
    
        ![Konfiguracja ręczna — Uruchamianie programu Configuration Manager](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)     
    6. na węźle (lokalnym komputerze z systemem Windows) widzisz Zarządzanie danymi Configuration Manager Gateway, który pokazuje stan łączności, **nazwę bramy** i **nazwę węzła**.  

        ![Zarządzanie danymi Gateway — instalacja powiodła się](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > W przypadku aprowizacji bramy na maszynie wirtualnej platformy Azure można użyć [tego szablonu Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway). Ten skrypt tworzy bramę logiczną, konfiguruje maszyny wirtualne z zainstalowanym oprogramowaniem bramy Zarządzanie danymi i rejestruje je za pomocą bramy logicznej. 
6. W Azure Portal Uruchom stronę **bramy** : 
    1. Na stronie głównej fabryki danych w portalu kliknij pozycję **połączone usługi**.
    
        ![Zrzut ekranu, który podświetla kafelek połączone usługi.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. Wybierz **bramę** , aby wyświetlić stronę **bramy** :
    
        ![Strona główna fabryki danych](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Zostanie wyświetlona strona **bramy** :   

        ![Brama z widokiem pojedynczego węzła](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Kliknij przycisk **Dodaj węzeł** na pasku narzędzi, aby dodać węzeł do bramy logicznej. Jeśli planujesz korzystanie z Instalatora ekspresowego, wykonaj ten krok z komputera lokalnego, który zostanie dodany jako węzeł do bramy. 

    ![Zarządzanie danymi Gateway — Dodaj węzeł menu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. Kroki są podobne do konfigurowania pierwszego węzła. Interfejs użytkownika Configuration Manager umożliwia ustawienie nazwy węzła w przypadku wybrania opcji instalacji ręcznej: 

    ![Configuration Manager — Zainstaluj drugą bramę](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Po pomyślnym zainstalowaniu bramy w węźle narzędzie Configuration Manager wyświetla następujący ekran:  

    ![Configuration Manager — instalacja drugiej bramy powiodła się](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Jeśli otworzysz stronę **bramy** w portalu, zobaczysz teraz dwa węzły bramy: 

    ![Brama z dwoma węzłami w portalu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Aby usunąć węzeł bramy, kliknij przycisk **Usuń węzeł** na pasku narzędzi, wybierz węzeł, który chcesz usunąć, a następnie kliknij przycisk **Usuń** z paska narzędzi. Ta akcja spowoduje usunięcie wybranego węzła z grupy. Należy pamiętać, że ta akcja nie powoduje odinstalowania oprogramowania bramy zarządzania danymi z węzła (lokalna maszyna z systemem Windows). Aby odinstalować bramę, użyj **apletu Dodaj lub usuń programy** w panelu sterowania. Po odinstalowaniu bramy z węzła zostanie ona automatycznie usunięta w portalu.   

## <a name="upgrade-an-existing-gateway"></a>Uaktualnianie istniejącej bramy
Możesz uaktualnić istniejącą bramę, aby korzystać z funkcji wysokiej dostępności i skalowalności. Ta funkcja działa tylko z węzłami z bramą zarządzania danymi w wersji >= 2.12. xxxx. Wersja bramy zarządzania danymi zainstalowaną na komputerze można sprawdzić na karcie **Pomoc** w Configuration Manager zarządzanie danymi bramy. 

1. Zaktualizuj bramę na maszynie lokalnej do najnowszej wersji, wykonując następujące czynności, pobierając i uruchamiając pakiet instalacyjny MSI z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Aby uzyskać szczegółowe informacje, zobacz sekcję dotyczącą [instalacji](data-factory-data-management-gateway.md#installation) .  
2. Przejdź do witryny Azure Portal. Uruchom **stronę Data Factory** dla fabryki danych. Kliknij kafelek połączone usługi, aby uruchomić **stronę połączone usługi**. Wybierz bramę, aby uruchomić **stronę bramy**. Kliknij i Włącz **funkcję Podgląd** , jak pokazano na poniższej ilustracji: 

    ![Zarządzanie danymi Gateway — Włącz funkcję wersji zapoznawczej](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Po włączeniu funkcji wersji zapoznawczej w portalu Zamknij wszystkie strony. Ponownie Otwórz **stronę bramy** , aby wyświetlić nowy interfejs użytkownika w wersji zapoznawczej.
 
    ![Zarządzanie danymi Gateway — powodzenie włączania funkcji w wersji zapoznawczej](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Zarządzanie danymi Gateway — interfejs użytkownika w wersji zapoznawczej](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Podczas uaktualniania nazwa pierwszego węzła jest nazwą komputera. 
3. Teraz Dodaj węzeł. Na stronie **brama** kliknij przycisk **Dodaj węzeł**.  

    ![Zarządzanie danymi Gateway — Dodaj węzeł menu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Postępuj zgodnie z instrukcjami z poprzedniej sekcji, aby skonfigurować węzeł. 

### <a name="installation-best-practices"></a>Najlepsze rozwiązania dotyczące instalacji

- Skonfiguruj plan dodatku na komputerze hosta dla bramy, aby komputer nie był w stanie hibernacji. Jeśli maszyna hosta jest w stanie hibernacji, Brama nie odpowiada na żądania danych.
- Wykonaj kopię zapasową certyfikatu skojarzonego z bramą.
- Upewnij się, że wszystkie węzły są podobnej konfiguracji (zalecane) w celu uzyskania doskonałej wydajności. 
- Dodaj co najmniej dwa węzły, aby zapewnić wysoką dostępność.  

### <a name="tlsssl-certificate-requirements"></a>Wymagania dotyczące certyfikatu TLS/SSL
Poniżej przedstawiono wymagania dotyczące certyfikatu TLS/SSL, który jest używany do zabezpieczania komunikacji między węzłami Integration Runtime:

- Certyfikat musi być publicznie zaufanym certyfikatem x509 v3. Zalecamy używanie certyfikatów wystawionych przez publiczny (zewnętrzny) urząd certyfikacji (CA).
- Każdy węzeł Integration Runtime musi ufać temu certyfikatowi, a także komputerowi klienckiemu z uruchomioną aplikacją Menedżer poświadczeń. 
  > [!NOTE]
  > Aplikacja Menedżera poświadczeń jest używana podczas bezpiecznego ustawiania poświadczeń z Kreatora kopiowania lub witryny Azure Portal. Może to być wywoływane z dowolnego komputera w tej samej sieci, w którym znajduje się magazyn danych lokalnych/prywatnych.
- Obsługiwane są certyfikaty z użyciem symboli wieloznacznych. Jeśli nazwa FQDN to **Node1.domain.contoso.com**, można użyć **_. domain.contoso.com_* jako nazwę podmiotu certyfikatu.
- Certyfikaty sieci SAN nie są zalecane, ponieważ zostanie użyty tylko ostatni element alternatywnych nazw podmiotu, a wszystkie pozostałe zostaną zignorowane ze względu na bieżące ograniczenie. Na przykład masz certyfikat sieci SAN, którego SAN to **Node1.domain.contoso.com** i **Node2.domain.contoso.com**, możesz użyć tego certyfikatu tylko na komputerze, którego nazwa FQDN to **Node2.domain.contoso.com**.
- Obsługuje wszystkie rozmiary kluczy obsługiwane przez system Windows Server 2012 R2 dla certyfikatów TLS/SSL.
- Certyfikat używający kluczy CNG nie jest obsługiwany.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>Często zadawane pytania: Kiedy nie Włącz tego szyfrowania?
Włączenie szyfrowania może spowodować dodanie pewnego kosztu do infrastruktury (będącej właścicielem certyfikatu publicznego), dlatego można pominąć włączenie szyfrowania w poniższych przypadkach:
- Gdy środowisko Integration Runtime jest uruchomione w zaufanej sieci lub sieci z przezroczystym szyfrowaniem, takim jak IP/s. Ponieważ ta komunikacja kanału jest ograniczona tylko w ramach zaufanej sieci, może nie być potrzebne dodatkowe szyfrowanie.
- Gdy środowisko Integration Runtime nie jest uruchomione w środowisku produkcyjnym. Może to pomóc w zmniejszeniu kosztu certyfikatu TLS/SSL.


## <a name="monitor-a-multi-node-gateway"></a>Monitorowanie bramy wielowęzłowej
### <a name="multi-node-gateway-monitoring"></a>Monitorowanie bramy wielowęzłowej
W Azure Portal można wyświetlić migawkę wykorzystania zasobów (procesor CPU, pamięć, Sieć (WE/wychodzącą), itp.) w każdym węźle wraz z Stanami węzłów bramy. 

![Zarządzanie danymi Gateway — monitorowanie wielu węzłów](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Na stronie **brama** można włączyć **Ustawienia zaawansowane** , aby wyświetlić metryki zaawansowane, takie jak **Sieć**(WE/out), **& stanu poświadczeń**, co jest przydatne w debugowaniu problemów z bramą i **współbieżnych zadań** (uruchomionych/ograniczających), które można odpowiednio modyfikować i zmieniać podczas dostrajania wydajności. Poniższa tabela zawiera opisy kolumn na liście **węzły bramy** :  

Właściwość monitorowania | Opis
:------------------ | :---------- 
Nazwa | Nazwa bramy logicznej i węzłów skojarzonych z bramą.  
Stan | Stan bramy logicznej i węzłów bramy. Przykład: online/offline/Limited/itd. Aby uzyskać informacje o tych Stanach, zobacz sekcję [stan bramy](#gateway-status) . 
Wersja | Pokazuje wersję bramy logicznej i każdego węzła bramy. Wersja bramy logicznej jest określana na podstawie wersji większości węzłów w grupie. Jeśli w instalatorze bramy logicznej istnieją węzły z różnymi wersjami, poprawne jest tylko te węzły, które mają ten sam numer wersji, co funkcja bramy logicznej. Inne są w trybie ograniczonym i muszą zostać ręcznie zaktualizowane (tylko w przypadku niepowodzenia aktualizacji w przypadku awarii). 
Dostępna pamięć | Dostępna pamięć w węźle bramy. Ta wartość jest migawką niemal w czasie rzeczywistym. 
Wykorzystanie procesora | Użycie procesora CPU przez węzeł bramy. Ta wartość jest migawką niemal w czasie rzeczywistym. 
Sieć (do/z) | Użycie sieci przez węzeł bramy. Ta wartość jest migawką niemal w czasie rzeczywistym. 
Zadania współbieżne (uruchomione/ograniczone) | Liczba zadań lub zadań uruchomionych w każdym węźle. Ta wartość jest migawką niemal w czasie rzeczywistym. Wartość Ogranicz oznacza maksymalne zadania współbieżne dla każdego węzła. Ta wartość jest definiowana w zależności od rozmiaru maszyny. Można zwiększyć limit skalowania w górę współbieżnego wykonywania zadań w zaawansowanych scenariuszach, w przypadku których wykorzystanie procesora CPU/pamięci/sieci jest używane, ale działania mają limit czasu. Ta funkcja jest również dostępna w przypadku bramy z jednym węzłem (nawet wtedy, gdy funkcja skalowalności i dostępności nie jest włączona). Aby uzyskać więcej informacji, zobacz sekcję [zagadnienia dotyczące skalowania](#scale-considerations) . 
Rola | Istnieją dwa typy ról — Dyspozytor i proces roboczy. Wszystkie węzły są pracownikami, co oznacza, że mogą być używane do wykonywania zadań. Istnieje tylko jeden węzeł dyspozytora, który jest używany do ściągania zadań/zadań z usług w chmurze i wysyłania ich do różnych węzłów procesu roboczego (w tym samego siebie). 

![Zarządzanie danymi Gateway — Zaawansowane monitorowanie wielu węzłów](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Stan bramy

W poniższej tabeli przedstawiono możliwe stany **węzła bramy**: 

Stan    | Komentarze/scenariusze
:------- | :------------------
Tryb online | Węzeł połączony z usługą Data Factory.
Tryb offline | Węzeł jest w trybie offline.
Uaktualnianie | Węzeł jest aktualizowany w sposób autouzupełniania.
Ograniczone | Z powodu problemu z łącznością. Może to być spowodowane problemem z portem HTTP 8050, problemem z łącznością usługi Service Bus lub problemem z synchronizacją poświadczeń. 
Nieaktywne | Węzeł jest w konfiguracji innej niż Konfiguracja innych węzłów większości.<br/><br/> Węzeł może być nieaktywny, jeśli nie może połączyć się z innymi węzłami. 


W poniższej tabeli przedstawiono możliwe stany **bramy logicznej**. Stan bramy zależy od stanu węzłów bramy. 

Stan | Komentarze
:----- | :-------
Wymaga rejestracji | Żaden węzeł nie jest jeszcze zarejestrowany w tej bramie logicznej
Tryb online | Węzły bramy są w trybie online
Tryb offline | Brak węzła w stanie online.
Ograniczone | Nie wszystkie węzły w tej bramie są w dobrej kondycji. Ten stan jest ostrzeżeniem, że niektóre węzły mogą być wyłączone. <br/><br/>Może to być spowodowane problemem z synchronizacją poświadczeń w węźle dyspozytora/proces roboczy. 

### <a name="pipeline-activities-monitoring"></a>Monitorowanie potoku/działań
Azure Portal zapewnia środowisko monitorowania potoku z szczegółowymi szczegółami na poziomie węzła. Na przykład pokazuje, które działania uruchomiono w tym węźle. Te informacje mogą pomóc w zrozumieniu problemów z wydajnością w konkretnym węźle, z powodu ograniczenia przepustowości sieci. 

![Zarządzanie danymi Gateway — monitorowanie wielu węzłów dla potoków](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Zarządzanie danymi bramy — szczegóły potoku](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Zagadnienia dotyczące skalowania

### <a name="scale-out"></a>Skalowanie w poziomie
Gdy **ilość dostępnej pamięci jest niska** i **użycie procesora CPU jest wysokie**, dodanie nowego węzła ułatwia skalowanie obciążenia między maszynami. Jeśli działania zakończą się niepowodzeniem z powodu przekroczenia limitu czasu lub węzła bramy w trybie offline, można to zrobić, jeśli dodasz węzeł do bramy.
 
### <a name="scale-up"></a>Skalowanie w górę
Gdy dostępna pamięć i procesor CPU nie są używane prawidłowo, ale bezczynna wydajność jest równa 0, należy skalować ją w górę, zwiększając liczbę współbieżnych zadań, które mogą być uruchamiane w węźle. Można również skalować w górę, gdy działania są przekroczenia limitu czasu, ponieważ Brama jest przeciążona. Jak pokazano na poniższej ilustracji, można zwiększyć maksymalną pojemność dla węzła. Sugerujemy, aby zacząć od.  

![Zarządzanie danymi — zagadnienia dotyczące skalowania](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Znane problemy/krytyczne zmiany

- Obecnie dla jednej bramy logicznej można mieć maksymalnie cztery węzły bramy fizycznej. Jeśli potrzebujesz więcej niż czterech węzłów ze względu na wydajność, Wyślij wiadomość e-mail na adres [DMGHelp@microsoft.com](mailto:DMGHelp@microsoft.com) .
- Nie można ponownie zarejestrować węzła bramy przy użyciu klucza uwierzytelniania z innej bramy logicznej w celu przełączenia z bieżącej bramy logicznej. Aby przeprowadzić ponowną rejestrację, Odinstaluj bramę z węzła, zainstaluj ponownie bramę i zarejestruj ją z kluczem uwierzytelniania dla drugiej bramy logicznej. 
- Jeśli serwer proxy HTTP jest wymagany dla wszystkich węzłów bramy, Ustaw serwer proxy w diahost.exe.config i diawp.exe.config i użyj Menedżera serwera, aby upewnić się, że wszystkie węzły mają te same diahost.exe.config i diawip.exe.config. Aby uzyskać szczegółowe informacje, zobacz sekcję [Konfigurowanie ustawień serwera proxy](data-factory-data-management-gateway.md#configure-proxy-server-settings) . 
- Aby zmienić tryb szyfrowania dla komunikacji między węzłami w bramie Configuration Manager, Usuń wszystkie węzły w portalu, z wyjątkiem jednego. Następnie dodaj węzły ponownie po zmianie trybu szyfrowania.
- Jeśli zdecydujesz się na szyfrowanie kanału komunikacji między węzłami, użyj oficjalnego certyfikatu protokołu TLS. Certyfikat z podpisem własnym może powodować problemy z łącznością, ponieważ ten sam certyfikat może nie być zaufany na liście urzędów certyfikacji na innych komputerach. 
- Nie można zarejestrować węzła bramy w bramie logicznej, gdy wersja węzła jest starsza niż wersja bramy logicznej. Usuń wszystkie węzły bramy logicznej z portalu, aby można było zarejestrować dolny węzeł wersji (starsza wersja). W przypadku usunięcia wszystkich węzłów bramy logicznej ręcznie zainstaluj i Zarejestruj nowe węzły w tej bramie logicznej. W tym przypadku Instalacja ekspresowa nie jest obsługiwana.
- Programu Express Setup nie można użyć do zainstalowania węzłów w istniejącej bramie logicznej, która nadal używa poświadczeń w chmurze. Możesz sprawdzić, gdzie są przechowywane poświadczenia z bramy Configuration Manager na karcie Ustawienia.
- Programu Express Setup nie można użyć do zainstalowania węzłów w istniejącej bramie logicznej, w której włączono szyfrowanie między węzłami. Ponieważ ustawienie trybu szyfrowania obejmuje Ręczne dodawanie certyfikatów, Instalacja ekspresowa nie jest już opcją. 
- W przypadku kopiowania plików ze środowiska lokalnego nie należy używać \\ hosta lokalnego ani C:\files, ponieważ nie jest on dostępny za pośrednictwem wszystkich węzłów. Zamiast tego należy użyć \\ ServerName\files do określenia lokalizacji plików.


## <a name="rolling-back-from-the-preview"></a>Wycofywanie z wersji zapoznawczej 
Aby wycofać z wersji zapoznawczej, Usuń wszystkie węzły, ale jeden węzeł. Nie ma znaczenia, które węzły są usuwane, ale upewnij się, że w bramie logicznej istnieje co najmniej jeden węzeł. Węzeł można usunąć przez odinstalowanie bramy na maszynie lub przy użyciu Azure Portal. W Azure Portal na stronie **Data Factory** kliknij pozycję połączone usługi, aby uruchomić stronę **połączone usługi** . Wybierz bramę, aby uruchomić stronę **bramy** . Na stronie Brama można zobaczyć węzły skojarzone z bramą. Strona umożliwia usunięcie węzła z bramy.
 
Po usunięciu kliknij pozycję **funkcje w wersji zapoznawczej** na tej samej stronie Azure Portal i Wyłącz funkcję wersji zapoznawczej. Brama została zresetowana do jednego węzła w bramie GA (ogólna dostępność).


## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami:
- [Zarządzanie danymi Gateway](data-factory-data-management-gateway.md) — zawiera szczegółowe omówienie bramy.
- [Przenoszenie danych między lokalnymi i magazynami danych w chmurze](data-factory-move-data-between-onprem-and-cloud.md) — zawiera przewodnik z instrukcjami krok po kroku dotyczącymi używania bramy z pojedynczym węzłem. 
