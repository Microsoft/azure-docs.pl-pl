---
title: Włączanie kompleksowego protokołu TLS na platformie Azure Application Gateway
description: Ten artykuł zawiera omówienie Application Gateway kompleksowej obsługi protokołu TLS.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 286c9329be38055808571d8d32c724d27a61cbf3
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855875"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>Omówienie kończenia protokołu TLS i kompleksowej usługi TLS z Application Gateway

Transport Layer Security (TLS), wcześniej znany jako SSL (SSL), jest standardową technologią zabezpieczeń służącą do nawiązywania zaszyfrowanego połączenia między serwerem sieci Web a przeglądarką. Ten link zapewnia, że wszystkie dane przesyłane między serwerem sieci Web i przeglądarkami pozostają w trybie prywatnym i zaszyfrowanym. Brama Application Gateway obsługuje zarówno zakończenie protokołu TLS na bramie, jak i kompleksowe szyfrowanie TLS.

## <a name="tls-termination"></a>Zakończenie protokołu TLS

Application Gateway obsługuje zakończenie protokołu TLS w bramie, po którym ruch przeważnie jest przenoszony do serwerów zaplecza. Istnieje wiele zalet zaprzestania działania protokołu TLS w usłudze Application Gateway:

- **Zwiększona wydajność** — największe zwiększenie wydajności podczas odszyfrowywania protokołu TLS jest początkową uzgadnianiem. Aby zwiększyć wydajność, serwer wykonujący odszyfrowywanie buforuje identyfikatory sesji TLS i zarządza biletami sesji TLS. W takim przypadku wszystkie żądania od tego samego klienta mogą korzystać z buforowanych wartości. Jeśli jest to wykonywane na serwerach zaplecza, po każdym zażądaniu klienta przejdź do innego serwera, klient musi ponownie przeprowadzić uwierzytelnienie. Korzystanie z biletów TLS może pomóc w ograniczeniu tego problemu, ale nie są one obsługiwane przez wszystkich klientów i mogą być trudne do skonfigurowania i zarządzania.
- **Lepsze wykorzystanie serwerów wewnętrznej bazy danych** — przetwarzanie za pośrednictwem protokołu SSL/TLS jest bardzo czasochłonne i zwiększa się w miarę wzrostu rozmiaru kluczy. Usunięcie tej pracy z serwerów zaplecza pozwala im skupić się na tym, co są najbardziej wydajne, dostarczając zawartość.
- **Inteligentne Routing** — przez odszyfrowanie ruchu, Brama aplikacji ma dostęp do zawartości żądania, takiej jak nagłówki, identyfikator URI itd., i może używać tych danych do przesyłania żądań.
- **Zarządzanie certyfikatami** — certyfikaty muszą być zakupione i zainstalowane tylko w bramie aplikacji, a nie na wszystkich serwerach zaplecza. Pozwala to zaoszczędzić czas i pieniądze.

Aby skonfigurować zakończenie protokołu TLS, do odbiornika musi zostać dodany certyfikat protokołu TLS/SSL, aby umożliwić bramie aplikacji uzyskanie klucza symetrycznego zgodnie ze specyfikacją protokołu TLS/SSL. Klucz symetryczny jest następnie używany do szyfrowania i odszyfrowywania ruchu wysyłanego do bramy. Certyfikat TLS/SSL musi być w formacie wymiany informacji osobistych (PFX). Ten format pliku umożliwia wyeksportowanie klucza prywatnego wymaganego przez bramę aplikacji w celu przeprowadzenia szyfrowania i odszyfrowywania ruchu.

> [!IMPORTANT] 
> Należy pamiętać, że certyfikat w odbiorniku wymaga przeładowania całego łańcucha certyfikatów. 


> [!NOTE] 
>
> Usługa Application Gateway nie oferuje żadnej możliwości tworzenia nowego certyfikatu ani wysyłania żądania certyfikatu do urzędu certyfikacji.

Aby połączenie TLS działało, należy się upewnić, że certyfikat TLS/SSL spełnia następujące warunki:

- Bieżąca data i godzina przypada w zakresie dat "ważny od" i "ważny do" w odniesieniu do certyfikatu.
- „Nazwa pospolita” (CN) certyfikatu musi być zgodna z nagłówkiem hosta w żądaniu. Jeśli na przykład klient wysyła żądanie do domeny `https://www.contoso.com/`, nazwą pospolitą (CN) musi być `www.contoso.com`.

### <a name="certificates-supported-for-tls-termination"></a>Certyfikaty obsługiwane przez zakończenie protokołu TLS

Brama aplikacji obsługuje następujące typy certyfikatów:

- Certyfikat urzędu certyfikacji: certyfikat urzędu certyfikacji jest certyfikatem cyfrowym wystawionym przez urząd certyfikacji (CA)
- Certyfikat EV (rozszerzone sprawdzanie poprawności): certyfikat EV jest certyfikatem, który jest zgodny ze standardowymi zaleceniami dotyczącymi certyfikatów branżowych. Spowoduje to przekształcenie paska lokalizatora przeglądarki na zielony i opublikowanie nazwy firmy.
- Certyfikat z symbolami wieloznacznymi: ten certyfikat obsługuje dowolną liczbę poddomen opartych na *. site.com, gdzie poddomena zastąpi *. Nie jest to jednak obsługiwane w site.com, więc w przypadku, gdy użytkownicy uzyskują dostęp do witryny sieci Web bez konieczności pisania wiodącego "www", certyfikat wieloznaczny nie będzie uwzględniał tego.
- Certyfikaty z podpisem własnym: przeglądarki klienta nie ufają tym certyfikatom i ostrzegają użytkownika o tym, że certyfikat usługi wirtualnej nie jest częścią łańcucha zaufania. Certyfikaty z podpisem własnym są przydatne w przypadku testowania lub środowisk, w których Administratorzy kontrolują klientów i mogą bezpiecznie pomijać alerty zabezpieczeń przeglądarki. Obciążenia produkcyjne nigdy nie powinny korzystać z certyfikatów z podpisem własnym.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zakończenia protokołu TLS przy użyciu usługi Application Gateway](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Rozmiar certyfikatu
Sprawdź sekcję [limity Application Gateway](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) , aby poznać maksymalny obsługiwany rozmiar certyfikatu TLS/SSL.

## <a name="end-to-end-tls-encryption"></a>Kompleksowe szyfrowanie TLS

Niektórzy klienci mogą nie chcieć nieszyfrowanej komunikacji z serwerami zaplecza. Może to być spowodowane wymaganiami dotyczącymi zabezpieczeń lub zgodności albo aplikacja może akceptować jedynie bezpieczne połączenia. W przypadku takich aplikacji Brama Application Gateway obsługuje kompleksowe szyfrowanie TLS.

Kompleksowa usługa TLS pozwala bezpiecznie przesyłać poufne dane do zaszyfrowanej wewnętrznej bazy danych przy jednoczesnym wykorzystaniu korzyści z funkcji równoważenia obciążenia warstwy 7, które oferuje Usługa Application Gateway. Do tych funkcji należą koligacja sesji oparta na plikach cookie, routing oparty na adresach URL, obsługa routingu opartego na witrynach lub możliwość iniekcji nagłówków X-Forwarded-*.

Po skonfigurowaniu z kompleksowym trybem komunikacji TLS Brama aplikacji kończy sesje protokołu TLS na bramie i odszyfrowuje ruch użytkownika. Następnie stosuje skonfigurowane reguły, aby wybrać odpowiednie wystąpienie puli serwerów zaplecza w celu skierowania do nich ruchu. Następnie usługa Application Gateway inicjuje nowe połączenie TLS z serwerem zaplecza i ponownie szyfruje dane przy użyciu certyfikatu klucza publicznego serwera zaplecza przed przekazaniem żądania do zaplecza. Każda odpowiedź z serwera sieci Web przechodzi przez ten sam proces z powrotem do użytkownika końcowego. Kompleksowa usługa TLS jest włączana przez ustawienie ustawienia protokołu w [ustawieniu http zaplecza](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) na https, który następnie jest stosowany do puli zaplecza.

Zasady protokołu TLS dotyczą zarówno ruchu frontonu, jak i zaplecza. Na frontonie Application Gateway działa jako serwer i wymusza zasady. W zapleczu Application Gateway pełni rolę klienta i wysyła informacje o protokole i szyfrowania jako preferencję podczas uzgadniania TLS.

Brama Application Gateway komunikuje się tylko z tymi wystąpieniami zaplecza, które mają listy dozwolonych swój certyfikat z bramą aplikacji lub których certyfikaty są podpisane przez dobrze znane urzędy urzędów certyfikacji, gdzie nazwa POSPOLITa certyfikatu jest zgodna z nazwą hosta w ustawieniach zaplecza protokołu HTTP. Należą do nich zaufane usługi platformy Azure, takie jak Azure App Service Web Apps i Azure API Management.

Jeśli certyfikaty elementów członkowskich w puli zaplecza nie są podpisane przez dobrze znane urzędy urzędów certyfikacji, każde wystąpienie w puli zaplecza z włączonym kompleksowym protokołem TLS musi być skonfigurowane przy użyciu certyfikatu w celu umożliwienia bezpiecznej komunikacji. Dodanie certyfikatu zapewnia, że Brama aplikacji komunikuje się tylko ze znanymi wystąpieniami zaplecza. Pozwala to na zapewnienie kompleksowej komunikacji.

> [!NOTE] 
>
> Konfiguracja certyfikatu uwierzytelniania nie jest wymagana dla zaufanych usług platformy Azure, takich jak usługa Azure App Service Web Apps i Azure API Management.

> [!NOTE] 
>
> Certyfikat dodany do **Ustawienia protokołu HTTP zaplecza** służącego do uwierzytelniania serwerów zaplecza może być taki sam jak certyfikat dodany do **odbiornika** protokołu TLS w usłudze Application Gateway lub inny w celu zwiększenia bezpieczeństwa.

![scenariusz kompleksowego protokołu TLS][1]

W tym przykładzie żądania przy użyciu protokołu TLS 1.2 są kierowane do serwerów zaplecza w Pool1 przy użyciu kompleksowej usługi TLS.

## <a name="end-to-end-tls-and-whitelisting-of-certificates"></a>Kompleksowe szyfrowanie TLS i listy dozwolonych certyfikatów

Usługa Application Gateway komunikuje się tylko ze znanymi wystąpieniami zaplecza, których certyfikaty znajdują się na liście dozwolonych certyfikatów tej usługi. Aby włączyć listę dozwolonych certyfikatów, należy przekazać klucz publiczny certyfikatów serwera zaplecza do usługi Application Gateway (nie certyfikat główny). W takim przypadku możliwe będą tylko połączenia do znanych zapleczy, które znajdują się na liście dozwolonych. Połączenia do pozostałych zapleczy zakończą się błędem bramy. Certyfikaty z podpisem własnym są przeznaczone tylko do celów testowych i nie są zalecane dla obciążeń w środowisku produkcyjnym. Takie certyfikaty muszą być listy dozwolonych z bramą aplikacji, jak opisano w poprzednich krokach, zanim będzie można ich użyć.

> [!NOTE]
> Konfiguracja certyfikatu uwierzytelniania nie jest wymagana dla zaufanych usług platformy Azure, takich jak Azure App Service.

## <a name="end-to-end-tls-with-the-v2-sku"></a>Kompleksowa wersja protokołu TLS z jednostką SKU v2

Certyfikaty uwierzytelniania zostały wycofane i zastąpione przez zaufane certyfikaty główne w jednostce SKU Application Gateway v2. Działają one podobnie jak w przypadku certyfikatów uwierzytelniania z kilkoma kluczowymi różnicami:

- Certyfikaty podpisane przez dobrze znane urzędy urzędów certyfikacji, których nazwa POSPOLITa jest zgodna z nazwą hosta w ustawieniach zaplecza protokołu HTTP, nie wymagają żadnego dodatkowego kroku dla kompleksowego protokołu TLS do pracy. 

   Na przykład, jeśli certyfikaty zaplecza są wystawiane przez dobrze znany urząd certyfikacji i ma nazwę POSPOLITą contoso.com, a pole hosta ustawienia http zaplecza ma również wartość contoso.com, nie są wymagane żadne dodatkowe kroki. Można ustawić protokół ustawień protokołu HTTP zaplecza na HTTPS, a zarówno sonda kondycji, jak i ścieżka danych będzie włączona protokół TLS. Jeśli używasz Azure App Service lub innych usług sieci Web platformy Azure jako zaplecza, to są one niejawnie zaufane i nie są wymagane żadne dalsze kroki dla kompleksowego protokołu TLS.
   
> [!NOTE] 
>
> Aby certyfikat TLS/SSL był zaufany, certyfikat serwera wewnętrznej bazy danych musi zostać wystawiony przez urząd certyfikacji, który znajduje się w zaufanym magazynie Application Gateway. Jeśli certyfikat nie został wystawiony przez zaufany urząd certyfikacji, Application Gateway sprawdzi, czy certyfikat wystawiającego urzędu certyfikacji został wystawiony przez zaufany urząd certyfikacji i tak dalej, dopóki nie zostanie znaleziony zaufany urząd certyfikacji (z którym zostanie nawiązane zaufane, bezpieczne połączenie) lub nie zostanie znaleziony zaufany urząd certyfikacji (w którym momencie Application Gateway będzie oznaczać, że w złej kondycji). W związku z tym zaleca się, aby certyfikat serwera wewnętrznej bazy danych zawierał zarówno główny, jak i pośredni urząd certyfikacji.

- Jeśli certyfikat jest podpisany z podpisem własnym lub podpisany przez nieznane osoby pośredniczące, a następnie aby umożliwić kompleksową obsługę protokołu TLS w wersji 2, należy zdefiniować zaufany certyfikat główny. Application Gateway komunikuje się tylko z zapleczem, którego certyfikat główny certyfikatu serwera jest zgodny z jedną z listy zaufanych certyfikatów głównych w ustawieniu protokołu HTTP zaplecza skojarzonym z pulą.

> [!NOTE] 
>
> Certyfikat z podpisem własnym musi być częścią łańcucha certyfikatów. Jeden certyfikat z podpisem własnym bez łańcucha nie jest obsługiwany w jednostce SKU w wersji 2.

- Oprócz zgodności z certyfikatem głównym Application Gateway sprawdza także, czy ustawienie hosta określone w ustawieniu protokołu HTTP zaplecza pasuje do nazwy pospolitej (CN) przedstawionej przez certyfikat TLS/SSL serwera wewnętrznej bazy danych. Podczas próby nawiązania połączenia TLS z zapleczem Application Gateway ustawia rozszerzenie Oznaczanie nazwy serwera (SNI) na host określony w ustawieniu http zaplecza.
- Jeśli wybrano opcję **Wybierz nazwę hosta z adresu zaplecza** zamiast pola host w ustawieniu http zaplecza, nagłówek SNI jest zawsze ustawiany jako nazwa FQDN puli zaplecza, a CN w certyfikacie serwera wewnętrznej bazy danych TLS/SSL musi być zgodna z jego nazwą FQDN. Elementy członkowskie puli zaplecza z adresami IP nie są obsługiwane w tym scenariuszu.
- Certyfikat główny to zakodowany w formacie base64 certyfikat główny z certyfikatów serwera wewnętrznej bazy danych.

## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z kompleksowym protokołem TLS przejdź do strony [Konfigurowanie kompleksowej usługi TLS przy użyciu Application Gateway z programem PowerShell](application-gateway-end-to-end-ssl-powershell.md) , aby utworzyć bramę aplikacji przy użyciu kompleksowej usługi TLS.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
