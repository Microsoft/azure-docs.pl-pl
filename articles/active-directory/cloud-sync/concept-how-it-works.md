---
title: Azure AD Connect głębokiej synchronizacji w chmurze szczegółowe — jak to działa
description: Ten temat zawiera szczegółowe informacje szczegółowe na temat działania synchronizacji z chmurą.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e1079ab4a523fbbc99c1e9190aef960b3f0723f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98613843"
---
# <a name="cloud-sync-deep-dive---how-it-works"></a>Szczegółowa synchronizacja z chmurą szczegółowe — jak to działa

## <a name="overview-of-components"></a>Przegląd składników

![Jak to działa](media/concept-how-it-works/how-1.png)

Synchronizacja z chmurą została utworzona na podstawie usług Azure AD i ma 2 kluczowe składniki:

- **Agent aprowizacji**: agent aprowizacji Azure AD Connect w chmurze to ten sam Agent, który jest przeznaczony dla ruchu przychodzącego i oparty na tej samej technologii po stronie serwera, co serwer proxy aplikacji i przekazywanie przez uwierzytelnianie. Wymagane są tylko połączenia wychodzące i agenci są aktualizowani jako aktualizacje. 
- **Usługa aprowizacji**: ta sama usługa aprowizacji jest taka sama jak w przypadku inicjowania obsługi ruchu wychodzącego i inicjowania obsługi przychodzącej z usługi Workday, która używa modelu opartego na harmonogramie. W przypadku synchronizacji z chmurą zmiany są inicjowane co 2 minuty.


## <a name="initial-setup"></a>Początkowa konfiguracja
Podczas początkowej konfiguracji jest wykonywane kilka rzeczy, które umożliwiają synchronizację z chmurą.  Są to: 

- **Podczas instalacji agenta**: należy skonfigurować agenta dla domen usługi Active Directory, z których ma zostać utworzone udostępnianie.  Ta konfiguracja rejestruje domeny w usłudze tożsamości hybrydowej i ustanawia połączenie wychodzące do usługi Service Bus nasłuchiwanie żądań.
- **Po włączeniu aprowizacji**: należy wybrać domenę usługi AD i włączyć obsługę administracyjną, która będzie uruchamiana co 2 minuty. Opcjonalnie można usunąć zaznaczenie opcji Synchronizacja skrótów haseł i zdefiniować wiadomość e-mail z powiadomieniem. Można również zarządzać transformacjami atrybutów przy użyciu Microsoft Graph interfejsów API.


## <a name="agent-installation"></a>Instalacja agenta
Poniżej przedstawiono omówienie tego, co się dzieje po zainstalowaniu agenta aprowizacji w chmurze.

- Najpierw Instalator instaluje pliki binarne agenta i usługę agenta uruchomioną w ramach konta usługi wirtualnej (NETWORK SERVICE\AADProvisioningAgent).  Konto usługi wirtualnej jest specjalnym typem konta, które nie ma hasła i jest zarządzane przez system Windows.
- Następnie Instalator uruchamia kreatora.
- Kreator wyświetli monit o poświadczenia usługi Azure AD, a następnie uwierzytelni się i pobierze token.
- Następnie Kreator pyta o bieżące poświadczenia administratorów domeny komputera.
- Przy użyciu tych poświadczeń, konto usługi zarządzane przez agenta ogólnego (GMSA) dla tej domeny jest tworzone lub zlokalizowane i ponownie używane, jeśli już istnieje.
- Usługa agenta jest teraz ponownie konfigurowana do uruchamiania w ramach GMSA.
- Kreator wyświetli monit o konfigurację domeny wraz z kontem administratora przedsiębiorstwa (EA) w przypadku każdej domeny, która ma być serwisowana przez agenta.
- Konto GMSA zostanie następnie zaktualizowane przy użyciu uprawnień, które umożliwiają mu dostęp do każdej domeny wprowadzonej powyżej.
- Następnie Kreator wyzwala rejestrację agenta
- Agent tworzy certyfikat i używa tokenu usługi Azure AD, rejestruje siebie i certyfikat z usługą tożsamości hybrydowej (jej).
- Kreator wyzwala wywołanie AgentResourceGrouping. To wywołanie do usługi administratora polega na przypisaniu agenta do co najmniej jednej domeny usługi Active Directory w jego konfiguracji.
- Kreator uruchomi teraz ponownie usługę agenta.
- Agent wywołuje usługę ładowania początkowego po ponownym uruchomieniu (i co 10 minut później), aby sprawdzić dostępność aktualizacji konfiguracji.  Usługa ładowania początkowego weryfikuje tożsamość agenta.  Aktualizuje także czas ostatniego uruchomienia.  Jest to ważne, ponieważ jeśli agenci nie są Bootstrap, nie otrzymują zaktualizowanych punktów końcowych Service Bus i mogą nie być w stanie otrzymywać żądań. 


## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Co to jest system do zarządzania tożsamościami między domenami (standard scim)?

[Specyfikacja Standard scim](https://tools.ietf.org/html/draft-scim-core-schema-01) jest standardem używanym do automatyzowania wymiany informacji o tożsamości użytkowników lub grup między domenami tożsamości, takimi jak Azure AD. Standard scim staje się de facto standardem do aprowizacji i, w połączeniu z standardami Federacji, takimi jak SAML lub OpenID Connect Connect, zapewnia administratorom kompleksowe rozwiązanie do zarządzania dostępem oparte na standardach.

Agent aprowizacji w chmurze Azure AD Connect korzysta z usługi Standard scim w usłudze Azure AD w celu udostępniania i anulowania aprowizacji użytkowników i grup.

## <a name="synchronization-flow"></a>Przepływ synchronizacji
![Inicjowanie obsługi administracyjnej ](media/concept-how-it-works/provisioning-4.png) po zainstalowaniu agenta i włączeniu aprowizacji powoduje wystąpienie następującego przepływu.

1.  Po skonfigurowaniu usługa Azure AD Provisioning wywoła usługę hybrydową usługi Azure AD w celu dodania żądania do usługi Service Bus. Agent ciągle utrzymuje połączenie wychodzące do Service Bus nasłuchiwanie żądań i od razu odbiera system do żądania zarządzania tożsamościami między domenami (standard scim). 
2.  Agent dzieli żądanie na osobne zapytania na podstawie typu obiektu. 
3.  Usługa AD zwraca wynik do agenta, a agent filtruje te dane przed wysłaniem ich do usługi Azure AD.  
4.  Agent zwraca odpowiedź Standard scim do usługi Azure AD.  Odpowiedzi te są oparte na filtrowaniu, który wystąpił w ramach agenta.  Agent używa zakresu do filtrowania wyników. 
5.  Usługa aprowizacji zapisuje zmiany w usłudze Azure AD.
6. Jeśli jest to synchronizacja różnicowa w przeciwieństwie do pełnej synchronizacji, używany jest plik cookie/znak wodny. Nowe zapytania będą otrzymywać zmiany z tego pliku cookie/znaku wodnego.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze:
W przypadku synchronizacji z chmurą obsługiwane są następujące scenariusze.


- **Istniejący klient hybrydowy z nowym lasem**: synchronizacja Azure AD Connect jest używana dla lasów podstawowych. Synchronizacja z chmurą jest używana do aprowizacji z lasu usługi AD (w tym rozłączony). Aby uzyskać więcej informacji, zobacz [samouczek.](tutorial-existing-forest.md)

 ![Istniejące hybrydy](media/tutorial-existing-forest/existing-forest-new-forest-2.png)
- **Nowy klient hybrydowy**: synchronizacja Azure AD Connect nie jest używana. Synchronizacja z chmurą jest używana do aprowizacji z lasu usługi AD.  Aby uzyskać więcej informacji, zobacz [samouczek.](tutorial-single-forest.md)
 
 ![Nowi klienci](media/tutorial-single-forest/diagram-2.png)

- **Istniejący klient hybrydowy**: synchronizacja Azure AD Connect jest używana dla lasów podstawowych. Synchronizacja z chmurą jest pilotażowa dla małego zestawu użytkowników [w lasach głównych](tutorial-existing-forest.md).

 ![Istniejący pilotaż](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

Aby uzyskać więcej informacji, zobacz [obsługiwane topologie](plan-cloud-sync-topologies.md).



## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest Azure AD Connect Sync Cloud?](what-is-cloud-sync.md)
