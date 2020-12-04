---
title: Wizualizacja klastra przy użyciu usługi Azure Service Fabric Explorer
description: Service Fabric Explorer to aplikacja do inspekcji i zarządzania aplikacjami w chmurze oraz węzłami w klastrze Microsoft Azure Service Fabric.
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: a45aff305f97610cb2660c2e3f4b4427b905d7d4
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574059"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Wizualizowanie klastra przy użyciu narzędzia Service Fabric Explorer

Service Fabric Explorer (SFX) to narzędzie Open Source służące do sprawdzania klastrów Service Fabric platformy Azure i zarządzania nimi. Service Fabric Explorer to aplikacja klasyczna dla systemów Windows, macOS i Linux.

## <a name="service-fabric-explorer-download"></a>Pobieranie Service Fabric Explorer

Użyj następujących linków, aby pobrać Service Fabric Explorer jako aplikację klasyczną:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> Wersja klasyczna Service Fabric Explorer może mieć więcej niż więcej funkcji niż obsługuje klaster. Aby zapewnić pełną zgodność funkcji, można powrócić do Service Fabric Explorer wersji wdrożonej w klastrze.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>Uruchamianie Service Fabric Explorer z klastra

Service Fabric Explorer jest również hostowana w punkcie końcowym zarządzania HTTP klastra Service Fabric. Aby uruchomić SFX w przeglądarce internetowej, przejdź do punktu końcowego zarządzania HTTP klastra z dowolnej przeglądarki — na przykład https: \/ /clusterFQDN: 19080.

W przypadku konfiguracji stacji roboczej dla deweloperów można uruchomić Service Fabric Explorer w lokalnym klastrze, przechodząc do lokalizacji https://localhost:19080/Explorer . Zapoznaj się z tym artykułem, aby [przygotować środowisko programistyczne](service-fabric-get-started.md).

> [!NOTE]
> Jeśli klaster jest zabezpieczony przy użyciu certyfikatu z podpisem własnym, zostanie wyświetlony komunikat o błędzie z przeglądarki sieci Web "Ta witryna nie jest bezpieczna". Możesz po prostu wykonać większość nowoczesnych przeglądarek sieci Web, zastępując ostrzeżenie. W środowisku produkcyjnym klaster powinien być zabezpieczony przy użyciu nazwy pospolitej i certyfikatu wystawionego przez urząd certyfikacji. 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>Nawiązywanie połączenia z klastrem usługi Service Fabric
Aby nawiązać połączenie z klastrem Service Fabric, wymagany jest punkt końcowy zarządzania klastrami (FQDN/IP) i Port punktu końcowego zarządzania HTTP (domyślnie 19080). Na przykład https \: //mysfcluster.westus.cloudapp.Azure.com:19080. Użyj pola wyboru "Połącz z lokalnym", aby nawiązać połączenie z lokalnym klastrem na stacji roboczej.

### <a name="connect-to-a-secure-cluster"></a>Nawiązywanie połączenia z zabezpieczonym klastrem
Dostęp klienta do klastra Service Fabric można kontrolować przy użyciu certyfikatów lub Azure Active Directory (AAD).

W przypadku próby nawiązania połączenia z bezpiecznym klastrem, w zależności od konfiguracji klastra, konieczne będzie zaprezentowanie certyfikatu klienta lub zalogowanie się za pomocą usługi AAD.

## <a name="understand-the-service-fabric-explorer-layout"></a>Zrozumienie układu Service Fabric Explorer
Możesz nawigować przez Service Fabric Explorer przy użyciu drzewa po lewej stronie. W katalogu głównym drzewa pulpit nawigacyjny klastra zawiera omówienie klastra, w tym podsumowanie kondycji aplikacji i węzła.

![Pulpit nawigacyjny klastra Service Fabric Explorer][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>Wyświetlanie układu klastra
Węzły w klastrze Service Fabric są umieszczane w dwuwymiarowej siatce domen błędów i domenach uaktualnienia. To umieszczenie zapewnia, że aplikacje będą nadal dostępne w obecności błędów sprzętowych i uaktualnień aplikacji. Można zobaczyć, jak bieżący klaster jest wdrażany przy użyciu mapy klastra.

![Service Fabric Explorer mapowanie klastra][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Wyświetlanie aplikacji i usług
Klaster zawiera dwa poddrzewa: jeden dla aplikacji i drugi dla węzłów.

Widok aplikacji umożliwia nawigowanie po hierarchii logicznej Service Fabric: aplikacje, usługi, partycje i repliki.

W poniższym przykładzie aplikacja **MojaApl** programu składa się z dwóch usług, **MyStatefulService** i sieci **Web**. Ponieważ **MyStatefulService** jest stanowa, zawiera partycję z jedną podstawową i dwiema replikami pomocniczymi. Z kolei WebSvcService jest bezstanowy i zawiera jedno wystąpienie.

![Widok aplikacji Service Fabric Explorer][sfx-application-tree]

Na każdym poziomie drzewa okienko główne pokazuje odpowiednie informacje o elemencie. Na przykład można zobaczyć stan kondycji i wersję dla określonej usługi.

![Service Fabric Explorer podstawowe okienko][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>Wyświetlanie węzłów klastra
Widok węzła przedstawia fizyczny układ klastra. Dla danego węzła można sprawdzić, które aplikacje mają kod wdrożony w tym węźle. Dokładniej mówiąc, można zobaczyć, które repliki są obecnie uruchomione.

## <a name="actions"></a>Akcje
Service Fabric Explorer umożliwia szybkie wywoływanie akcji na węzłach, aplikacjach i usługach w ramach klastra.

Na przykład aby usunąć wystąpienie aplikacji, wybierz aplikację z drzewa po lewej stronie, a następnie wybierz **Akcje**  >  **Usuń aplikację**.

![Usuwanie aplikacji w Service Fabric Explorer][sfx-delete-application]

> [!TIP]
> Aby wykonać te same czynności, kliknij wielokropek obok każdego elementu.
>
> Każdą akcję, którą można wykonać za pomocą Service Fabric Explorer można również wykonać za pomocą programu PowerShell lub interfejsu API REST, aby włączyć automatyzację.
>
>

Możesz również użyć Service Fabric Explorer, aby utworzyć wystąpienia aplikacji dla danego typu i wersji aplikacji. Wybierz typ aplikacji w widoku drzewa, a następnie kliknij link **Utwórz wystąpienie aplikacji** obok wersji, którą chcesz umieścić w prawym okienku.

![Tworzenie wystąpienia aplikacji w Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> Service Fabric Explorer nie obsługuje parametrów podczas tworzenia wystąpień aplikacji. Wystąpienia aplikacji używają domyślnych wartości parametrów.
>
>

## <a name="event-store"></a>Magazyn zdarzeń
EventStore to funkcja oferowana przez platformę, która zapewnia Service Fabric zdarzenia platformy dostępne w Service Fabric Explorer i za pomocą interfejsu API REST. Możesz zobaczyć widok migawki tego, co się dzieje w klastrze dla każdej jednostki, na przykład węzeł, usługa, aplikacja i zapytanie na podstawie czasu zdarzenia. Więcej informacji na temat EventStore można także znaleźć na stronie [Przegląd EventStore](service-fabric-diagnostics-eventstore.md).   

![Zrzut ekranu przedstawia okienko węzły z wybranymi ZDARZENIAmi.][sfx-eventstore]

>[!NOTE]
>Od Service Fabric wersja 6,4. EventStore nie jest domyślnie włączona i musi być włączona w szablonie Menedżera zasobów

>[!NOTE]
>Od Service Fabric wersja 6,4. Interfejsy API EventStore są dostępne tylko dla klastrów systemu Windows działających tylko na platformie Azure. Pracujemy nad przenoszeniem tej funkcji do systemu Linux oraz z naszych klastrów autonomicznych.

## <a name="image-store-viewer"></a>Przeglądarka Magazyn obrazów
Podgląd magazynu obrazów jest funkcją oferowaną w przypadku korzystania z natywnych Magazyn obrazów umożliwiających wyświetlanie bieżącej zawartości magazynu obrazów oraz pobieranie informacji o plikach i folderach oraz usuwanie plików/folderów.

![Zrzut ekranu pokazujący Podgląd Magazyn obrazów.][sfx-imagestore]

## <a name="backup-and-restore"></a>Wykonywanie kopii zapasowych i przywracanie
Service Fabric Explorer oferuje możliwość tworzenia interfejsów przy użyciu [kopii zapasowych i przywracania](./service-fabric-reliable-services-backup-restore.md). Aby można było wyświetlić funkcje tworzenia kopii zapasowych i przywracania w programie SFX, należy włączyć tryb zaawansowany.

![Włącz tryb zaawansowany][0]
 
Możliwe są następujące operacje:

* Tworzenie, edytowanie i usuwanie zasad tworzenia kopii zapasowych.
* Włącza i wyłącza tworzenie kopii zapasowej dla aplikacji, usługi lub partycji.
* Wstrzymywanie i wznawianie kopii zapasowej dla aplikacji, usługi lub partycji.
* Wyzwalanie i śledzenie kopii zapasowej partycji.
* Wyzwalanie i śledzenie przywracania dla partycji.

Aby uzyskać więcej informacji na temat usługi tworzenia kopii zapasowych i przywracania, zobacz [Dokumentacja interfejsu API REST](/rest/api/servicefabric/sfclient-index-backuprestore).
## <a name="next-steps"></a>Następne kroki
* [Zarządzanie aplikacjami Service Fabric w programie Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Service Fabric wdrażanie aplikacji przy użyciu programu PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-dashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-map.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/sfx-application-tree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/sfx-service-essentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/sfx-delete-application.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/sfx-create-app-instance.png
[sfx-eventstore]: ./media/service-fabric-diagnostics-eventstore/eventstore.png
[sfx-imagestore]: ./media/service-fabric-visualizing-your-cluster/sfx-image-store.png
[0]: ./media/service-fabric-backuprestoreservice/advanced-mode.png
