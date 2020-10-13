---
title: Włączanie roamingu stanu przedsiębiorstwa w usłudze Azure Active Directory
description: Często zadawane pytania dotyczące ustawień Enterprise State Roaming na urządzeniach z systemem Windows.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.custom: references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34b554fbef63f23b3540fe49e5c45976122add25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89268608"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Włączanie roamingu stanu przedsiębiorstwa w usłudze Azure Active Directory

Enterprise State Roaming jest dostępna dla każdej organizacji z licencją Azure AD — wersja Premium lub Enterprise Mobility + Security (EMS). Aby uzyskać więcej informacji na temat uzyskiwania subskrypcji usługi Azure AD, zobacz [stronę produktu usługi Azure AD](https://azure.microsoft.com/services/active-directory).

Po włączeniu Enterprise State Roaming organizacja zostanie automatycznie udzielona bezpłatną licencję ograniczonej użytkowania na potrzeby ochrony za pomocą usługi Azure Rights Management z Azure Information Protection. Ta bezpłatna subskrypcja jest ograniczona do szyfrowania i odszyfrowywania ustawień przedsiębiorstwa oraz danych aplikacji synchronizowanych przez Enterprise State Roaming. Aby korzystać z pełnych możliwości usługi Azure Rights Management, musisz mieć [płatną subskrypcję](https://azure.microsoft.com/pricing/details/information-protection/) .

> [!NOTE]
> Ten artykuł ma zastosowanie do starszej przeglądarki opartej na języku HTML Microsoft Edge, która została uruchomiona z systemem Windows 10 w lipcu 2015. Artykuł nie ma zastosowania do nowej przeglądarki opartej na formacie chromu Microsoft Edge wydanej 15 stycznia 2020. Aby uzyskać więcej informacji na temat zachowania synchronizacji dla nowej przeglądarki Microsoft Edge, zobacz artykuł [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="to-enable-enterprise-state-roaming"></a>Aby włączyć Enterprise State Roaming

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com/).
1. Wybierz pozycję **Azure Active Directory**  >  **urządzenia**  >  **Enterprise State roaming**.
1. Wybierz pozycję **Użytkownicy mogą synchronizować ustawienia i dane aplikacji między urządzeniami**. Aby uzyskać więcej informacji, zobacz [jak skonfigurować ustawienia urządzenia](./device-management-azure-portal.md).
  
   ![obraz ustawienia urządzenia z etykietą użytkownicy mogą synchronizować ustawienia i dane aplikacji między urządzeniami](./media/enterprise-state-roaming-enable/device-settings.png)
  
Aby urządzenie z systemem Windows 10 używało usługi Enterprise State Roaming, urządzenie musi uwierzytelnić się przy użyciu tożsamości usługi Azure AD. W przypadku urządzeń przyłączonych do usługi Azure AD tożsamość logowania użytkownika podstawowego jest tożsamością usługi Azure AD, więc nie jest wymagana żadna dodatkowa konfiguracja. W przypadku urządzeń korzystających z Active Directory lokalnych administrator IT musi [skonfigurować urządzenia hybrydowe Azure Active Directory dołączone](./hybrid-azuread-join-plan.md). 

## <a name="data-storage"></a>Magazyn danych

Enterprise State Roaming dane są hostowane w co najmniej jednym [regionie platformy Azure](https://azure.microsoft.com/regions/) , który najlepiej odpowiada wartości kraju/regionu ustawionej w wystąpieniu Azure Active Directory. Enterprise State Roaming dane są partycjonowane na podstawie trzech głównych regionów geograficznych: Ameryka Północna, EMEA i Azja I Pacyfik. Enterprise State Roaming dane dla dzierżawy znajdują się lokalnie w regionie geograficznym i nie są replikowane między regionami.  Na przykład:

| Wartość kraju/regionu | ma dane hostowane w |
| -------------------- | ------------------------ |
| Kraj/region EMEA, taki jak Francja lub Zambii | Co najmniej jeden region świadczenia usługi Azure w Europie |
| Kraj/Region Ameryki Północnej, taki jak Stany Zjednoczone lub Kanada | Co najmniej jeden region świadczenia usługi Azure w Stanach Zjednoczonych |
| Kraj/Region Azja I Pacyfik, taki jak Australia lub Nowa Zelandia | Co najmniej jeden region platformy Azure w obrębie Azji |
| Regiony Południowe i Antarktyda | Co najmniej jeden region świadczenia usługi Azure w Stanach Zjednoczonych |

Wartość kraju/regionu jest ustawiana jako część procesu tworzenia katalogu usługi Azure AD i nie można jej zmodyfikować. Jeśli potrzebujesz więcej szczegółów dotyczących lokalizacji przechowywania danych, Zastąp bilet z pomocą [techniczną platformy Azure](https://azure.microsoft.com/support/options/).

## <a name="view-per-user-device-sync-status"></a>Wyświetl stan synchronizacji urządzenia użytkownika

Wykonaj następujące kroki, aby wyświetlić raport o stanie synchronizacji urządzeń poszczególnych użytkowników.

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com/).
1. Wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.
1. Wybierz użytkownika, a następnie wybierz pozycję **urządzenia**.
1. W obszarze **Pokaż**wybierz pozycję **urządzenia Synchronizuj ustawienia i dane aplikacji** , aby wyświetlić stan synchronizacji.
  
   ![obraz przedstawiający ustawienie danych synchronizacji urządzeń](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. W przypadku synchronizacji urządzeń dla tego użytkownika widoczne są urządzenia, jak pokazano tutaj.
  
   ![obraz przedstawiający dane kolumnowy urządzenia](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Przechowywanie danych

Dane zsynchronizowane z chmurą firmy Microsoft przy użyciu Enterprise State Roaming są zachowywane do momentu ręcznego usunięcia lub do momentu ustalenia, że dane są nieaktualne. 

### <a name="explicit-deletion"></a>Jawne usunięcie

Jawne usunięcie jest możliwe, gdy administrator platformy Azure usunie użytkownika lub katalog lub jawnie poprosi o usunięcie danych.

* **Usuwanie użytkownika**: po usunięciu użytkownika z usługi Azure AD dane mobilne konta użytkownika są usuwane po 90 do 180 dni. 
* **Usuwanie katalogu**: usuwanie całego katalogu w usłudze Azure AD jest operacją natychmiastową. Wszystkie dane ustawień skojarzonych z tym katalogiem zostaną usunięte po 90 do 180 dni. 
* **Przy usuwaniu żądania**: Jeśli administrator usługi Azure AD chce ręcznie usunąć dane danych lub ustawień określonego użytkownika, administrator może wysłać bilet z pomocą [techniczną platformy Azure](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Usuwanie nieaktualnych danych

Dane, do których nie uzyskano dostępu przez rok ("okres przechowywania") będą traktowane jako nieodświeżone i mogą zostać usunięte z chmury firmy Microsoft. Okres przechowywania może ulec zmianie, ale nie będzie krótszy niż 90 dni. Nieodświeżone dane mogą być określonym zestawem ustawień systemu Windows/aplikacji lub wszystkich ustawień dla użytkownika. Na przykład:

* Jeśli żadne urządzenie nie uzyskuje dostępu do określonej kolekcji ustawień (na przykład aplikacja zostanie usunięta z urządzenia lub grupa ustawień, taka jak "motyw" jest wyłączona dla wszystkich urządzeń użytkownika), ta kolekcja będzie nieaktualna po okresie przechowywania i może zostać usunięta. 
* Jeśli użytkownik wyłączył synchronizację ustawień na wszystkich swoich urządzeniach, wówczas żadne z danych ustawień nie będą dostępne, a wszystkie dane ustawień dla tego użytkownika staną się nieaktualne i mogą zostać usunięte po okresie przechowywania. 
* Jeśli administrator katalogu usługi Azure AD wyłączy Enterprise State Roaming dla całego katalogu, wszyscy użytkownicy w tym katalogu zatrzymają synchronizację ustawień, a wszystkie dane ustawień dla wszystkich użytkowników staną się nieaktualne i mogą zostać usunięte po okresie przechowywania. 

### <a name="deleted-data-recovery"></a>Odzyskiwanie usuniętych danych

Zasad przechowywania danych nie można konfigurować. Gdy dane zostaną trwale usunięte, nie można ich odzyskać. Jednak dane ustawień zostaną usunięte tylko z chmury firmy Microsoft, a nie z urządzenia użytkownika końcowego. Jeśli dowolne urządzenie ponownie nawiązuje połączenie z usługą Enterprise State Roaming, ustawienia zostaną ponownie zsynchronizowane i zapisane w chmurze firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

* [Omówienie roamingu stanu przedsiębiorstwa](enterprise-state-roaming-overview.md)
* [Roaming ustawień i danych — często zadawane pytania](enterprise-state-roaming-faqs.md)
* [Ustawienia zasady grupy i MDM dla synchronizacji ustawień](enterprise-state-roaming-group-policy-settings.md)
* [Informacje dotyczące ustawień roamingu w systemie Windows 10](enterprise-state-roaming-windows-settings-reference.md)
* [Rozwiązywanie problemów](enterprise-state-roaming-troubleshooting.md)