---
title: Połącz SCSM z łącznik zarządzania usługami IT
description: Ten artykuł zawiera informacje na temat sposobu SCSM z łącznik zarządzania usługami IT (ITSMC) w Azure Monitor, aby centralnie monitorować elementy robocze narzędzia ITSM i zarządzać nimi.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 79706b66dba46253843b1f53a26481170d6ff723
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97729663"
---
# <a name="connect-system-center-service-manager-with-it-service-management-connector"></a>Łączenie System Center Service Manager z łącznik zarządzania usługami IT

Ten artykuł zawiera informacje dotyczące sposobu konfigurowania połączenia między wystąpieniem System Center Service Manager i łącznik zarządzania usługami IT (ITSMC) w Log Analytics, aby centralnie zarządzać elementami roboczymi.

W poniższych sekcjach znajdują się szczegółowe informacje dotyczące sposobu łączenia produktu System Center Service Manager na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że zostały spełnione następujące wymagania wstępne:

- ITSMC. Więcej informacji: [Dodawanie rozwiązania łącznik zarządzania usługami IT](./itsmc-definition.md).
- Aplikacja sieci Web Service Manager (aplikacja sieci Web) została wdrożona i skonfigurowana. [Tutaj znajdziesz](#create-and-deploy-service-manager-web-app-service)informacje o aplikacji sieci Web.
- Utworzono i skonfigurowano połączenie hybrydowe. Więcej informacji: [Skonfiguruj połączenie hybrydowe](#configure-the-hybrid-connection).
- Obsługiwane wersje Service Manager: 2012 R2 lub 2016.
- Rola użytkownika:  [Zaawansowany operator](/previous-versions/system-center/service-manager-2010-sp1/ff461054(v=technet.10)).
- Dzisiaj alerty wysyłane z Azure Monitor mogą być tworzone w zdarzeniach System Center Service Manager.

> [!NOTE]
> - Łącznik ITSM może łączyć się tylko z wystąpieniami usługi ServiceNow opartymi na chmurze. Lokalne wystąpienia usługi ServiceNow nie są obecnie obsługiwane.
> - Aby można było używać [szablonów](./itsmc-definition.md#template-definitions) niestandardowych jako części akcji, parametr "projekcja" w szablonie SCSM powinien być zamapowany na "IncidentManagement! System. robocze. incydent. projekcja "

## <a name="connection-procedure"></a>Procedura połączenia

Aby połączyć wystąpienie System Center Service Manager z usługą ITSMC, wykonaj czynności opisane w poniższej procedurze:

1. W Azure Portal przejdź do pozycji **wszystkie zasoby** i Znajdź pozycję **Servicedesk (YourWorkspaceName).**

2. W obszarze **źródła danych obszaru roboczego** kliknij pozycję **połączenia narzędzia ITSM**.

    ![Nowe połączenie](media/itsmc-connections/add-new-itsm-connection.png)

3. W górnej części okienka po prawej stronie kliknij pozycję **Dodaj**.

4. Podaj informacje zgodnie z opisem w poniższej tabeli, a następnie kliknij przycisk **OK** , aby utworzyć połączenie.

> [!NOTE]
> Wszystkie te parametry są obowiązkowe.

| **Pole** | **Opis** |
| --- | --- |
| **Nazwa połączenia**   | Wpisz nazwę wystąpienia System Center Service Manager, które chcesz połączyć z ITSMC.  Ta nazwa jest używana później podczas konfigurowania elementów roboczych w tym wystąpieniu/wyświetlania szczegółowej usługi log Analytics. |
| **Typ partnera**   | Wybierz **System Center Service Manager**. |
| **Adres URL serwera**   | Wpisz adres URL aplikacji sieci Web Service Manager. Więcej informacji na temat Service Manager aplikacji sieci Web znajduje się w [tym miejscu](#create-and-deploy-service-manager-web-app-service).
| **Identyfikator klienta**   | Wpisz wygenerowany przez siebie identyfikator klienta (przy użyciu skryptu automatycznego) do uwierzytelniania aplikacji sieci Web. Więcej informacji o zautomatyzowanym skrypcie znajduje się [tutaj.](./itsmc-service-manager-script.md)|
| **Klucz tajny klienta**   | Wpisz klucz tajny klienta wygenerowany dla tego identyfikatora.   |
| **Synchronizuj dane**   | Wybierz Service Manager elementy robocze, które chcesz synchronizować za pomocą ITSMC.  Te elementy robocze są importowane do Log Analytics. **Opcje:**  Zdarzenia, żądania zmiany.|
| **Zakres synchronizacji danych** | Wpisz liczbę dni, z których dane mają być używane. **Maksymalny limit**: 120 dni. |
| **Utwórz nowy element konfiguracji w rozwiązaniu narzędzia ITSM** | Wybierz tę opcję, jeśli chcesz utworzyć elementy konfiguracji w produkcie narzędzia ITSM. Po wybraniu Log Analytics tworzy elementy konfiguracji, których to dotyczy, w ramach obsługiwanego systemu narzędzia ITSM. **Domyślnie**: wyłączone. |

![Połączenie programu Service Manager](media/itsmc-connections/service-manager-connection.png)

**Po pomyślnym nawiązaniu połączenia i zsynchronizowaniu**:

- Wybrane elementy robocze z Service Manager są importowane do usługi Azure **log Analytics.** Możesz wyświetlić podsumowanie tych elementów roboczych na kafelku łącznik zarządzania usługami IT.

- Możesz tworzyć zdarzenia z alertów Log Analytics lub z rekordów dziennika lub z alertów platformy Azure w tym wystąpieniu Service Manager.

Dowiedz się więcej: [Utwórz narzędzia ITSM elementy robocze z alertów platformy Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="create-and-deploy-service-manager-web-app-service"></a>Tworzenie i wdrażanie usługi Service Manager Web App Service

Aby połączyć Service Manager lokalnego z usługą ITSMC na platformie Azure, firma Microsoft stworzyła Service Manager aplikację sieci Web w witrynie GitHub.

Aby skonfigurować aplikację sieci Web narzędzia ITSM dla Service Manager, wykonaj następujące czynności:

- **Wdróż aplikację internetową** — Wdróż aplikację sieci Web, ustaw właściwości i Uwierzytelnij się z usługą Azure AD. Aplikację sieci Web można wdrożyć za pomocą [zautomatyzowanego skryptu](./itsmc-service-manager-script.md) dostarczonego przez firmę Microsoft.
- **Skonfiguruj połączenie**  -  hybrydowe [Skonfiguruj to połączenie](#configure-the-hybrid-connection)ręcznie.

### <a name="deploy-the-web-app"></a>Wdrażanie aplikacji sieci Web
Użyj [skryptu](./itsmc-service-manager-script.md) automatycznego do wdrożenia aplikacji sieci Web, ustawienia właściwości i uwierzytelniania za pomocą usługi Azure AD.

Uruchom skrypt, podając następujące wymagane szczegóły:

- Szczegóły subskrypcji platformy Azure
- Nazwa grupy zasobów
- Lokalizacja
- Szczegóły serwera Service Manager (nazwa serwera, domena, nazwa użytkownika i hasło)
- Prefiks nazwy witryny dla aplikacji sieci Web
- ServiceBus przestrzeń nazw.

Skrypt tworzy aplikację internetową przy użyciu podanej nazwy (wraz z kilkoma dodatkowymi ciągami, aby była unikatowa). Generuje **adres URL aplikacji sieci Web**, **Identyfikator klienta** i **klucz tajny klienta**.

Zapisz wartości, korzystając z nich podczas tworzenia połączenia z ITSMC.

**Sprawdzanie instalacji aplikacji sieci Web**

1. Przejdź do   >  **zasobów** Azure Portal.
2. Wybierz aplikację sieci Web, kliknij pozycję **Ustawienia**  >  **Ustawienia aplikacji**.
3. Potwierdź informacje dotyczące wystąpienia Service Manager podanego w czasie wdrażania aplikacji za pomocą skryptu.

## <a name="configure-the-hybrid-connection"></a>Skonfiguruj połączenie hybrydowe

Użyj poniższej procedury, aby skonfigurować połączenie hybrydowe łączące wystąpienie Service Manager z usługą ITSMC na platformie Azure.

1. Znajdź aplikację sieci Web Service Manager w obszarze **zasoby platformy Azure**.
2. Kliknij pozycję **Ustawienia**  >  **Sieć**.
3. W obszarze **połączenia hybrydowe** kliknij pozycję **Skonfiguruj punkty końcowe połączenia hybrydowego**.

    ![Sieci połączeń hybrydowych](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. W bloku **połączenia hybrydowe** kliknij pozycję **Dodaj połączenie hybrydowe**.

    ![Dodawanie połączenia hybrydowego](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. W bloku **dodaj połączenia hybrydowe** kliknij pozycję **Utwórz nowe połączenie hybrydowe**.

    ![Nowe połączenie hybrydowe](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Wpisz następujące wartości:

   - **Nazwa punktu końcowego**: Określ nazwę nowego połączenia hybrydowego.
   - **Host punktu końcowego**: nazwa FQDN serwera zarządzania Service Manager.
   - **Port punktu końcowego**: typ 5724
   - **ServiceBus — przestrzeń nazw**: Użyj istniejącej przestrzeni nazw ServiceBus lub Utwórz nową.
   - **Lokalizacja**: Wybierz lokalizację.
   - **Nazwa**: Określ nazwę ServiceBus, jeśli tworzysz ją.

     ![Wartości połączenia hybrydowego](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Kliknij przycisk **OK** , aby zamknąć blok **Tworzenie połączenia hybrydowego** i rozpocząć tworzenie połączenia hybrydowego.

    Po utworzeniu połączenia hybrydowego zostanie ono wyświetlone w bloku.

7. Po utworzeniu połączenia hybrydowego wybierz połączenie, a następnie kliknij przycisk **Dodaj wybrane połączenie hybrydowe**.

    ![Nowe połączenie hybrydowe](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

### <a name="configure-the-listener-setup"></a>Skonfiguruj konfigurację odbiornika

Aby skonfigurować konfigurację odbiornika dla połączenia hybrydowego, należy wykonać czynności opisane w poniższej procedurze.

1. W bloku **połączenia hybrydowe** kliknij pozycję **Pobierz Menedżera połączeń** i zainstaluj go na komputerze, na którym jest uruchomione wystąpienie System Center Service Manager.

    Po zakończeniu instalacji **Menedżer połączeń hybrydowych opcja interfejsu użytkownika** jest dostępna w menu **Start** .

2. Kliknij przycisk **Menedżer połączeń hybrydowych interfejs użytkownika** , zostanie wyświetlony monit o podanie poświadczeń platformy Azure.

3. Zaloguj się przy użyciu poświadczeń platformy Azure i wybierz subskrypcję, w której utworzono połączenie hybrydowe.

4. Kliknij pozycję **Zapisz**.

Połączenie hybrydowe zostało pomyślnie nawiązane.

![pomyślne połączenie hybrydowe](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> Po utworzeniu połączenia hybrydowego Sprawdź i przetestuj połączenie, odwiedzając wdrożoną aplikację sieci Web Service Manager. Upewnij się, że połączenie zostało nawiązane pomyślnie, zanim spróbujesz połączyć się z usługą ITSMC na platformie Azure.

Poniższy przykładowy obraz przedstawia szczegóły pomyślnego połączenia:

![Test połączenia hybrydowego](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="next-steps"></a>Następne kroki

* [Przegląd łącznik ITSM](itsmc-overview.md)
* [Tworzenie narzędzia ITSM elementów roboczych na podstawie alertów platformy Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Rozwiązywanie problemów w łączniku ITSM](./itsmc-resync-servicenow.md)