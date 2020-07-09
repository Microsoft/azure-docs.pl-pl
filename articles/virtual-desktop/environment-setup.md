---
title: Środowisko pulpitu wirtualnego systemu Windows — Azure
description: Podstawowe elementy środowiska pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ad535dd18b89cbe2fceab90f73789180ad332b57
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82612371"
---
# <a name="windows-virtual-desktop-environment"></a>Środowisko usługi Windows Virtual Desktop

>[!IMPORTANT]
>Ta zawartość ma zastosowanie do aktualizacji wiosennej 2020 z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows, wykorzystaj wersję 2019 bez obiektów Azure Resource Manager, zobacz [ten artykuł](./virtual-desktop-fall-2019/environment-setup-2019.md).
>
> Aktualizacja systemu Windows Virtual Desktop wiosna 2020 jest obecnie dostępna w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. 
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Windows Virtual Desktop to usługa zapewniająca użytkownikom łatwy i bezpieczny dostęp do swoich zwirtualizowanych pulpitów i programów RemoteApp. W tym temacie przedstawiono nieco więcej informacji na temat ogólnej struktury środowiska pulpitu wirtualnego systemu Windows.

## <a name="host-pools"></a>Pule hostów

Pula hostów jest kolekcją maszyn wirtualnych platformy Azure, które są rejestrowane na pulpicie wirtualnym systemu Windows jako hosty sesji podczas uruchamiania agenta pulpitu wirtualnego systemu Windows. Wszystkie maszyny wirtualne hosta sesji w puli hostów powinny być źródłem tego samego obrazu w celu zapewnienia spójnego środowiska użytkownika.

Pula hostów może być jednym z dwóch typów:

- Osobisty, gdzie każdy Host sesji jest przypisany do poszczególnych użytkowników.
- W puli, gdzie hosty sesji mogą akceptować połączenia od dowolnego użytkownika autoryzowanego do grupy aplikacji w puli hostów.

Można ustawić dodatkowe właściwości puli hostów, aby zmienić zachowanie równoważenia obciążenia, ile sesji może upłynąć każdy Host sesji i co użytkownik może zrobić, aby hosty sesji w puli hostów były zalogowane do sesji pulpitu wirtualnego systemu Windows. Możesz kontrolować zasoby publikowane dla użytkowników za pomocą grup aplikacji.

## <a name="app-groups"></a>Grupy aplikacji

Grupa aplikacji to logiczna Grupa aplikacji zainstalowanych na hostach sesji w puli hostów. Grupa aplikacji może być jednym z dwóch typów:

- Funkcja RemoteApp, w której użytkownicy uzyskują dostęp do wybranych przez siebie programów RemoteApp i publikuje je w grupie aplikacji
- Komputery stacjonarne, w których użytkownicy uzyskują dostęp do pełnego pulpitu

Domyślnie grupa aplikacji klasycznych (o nazwie "aplikacja klasyczna") jest tworzona automatycznie za każdym razem, gdy tworzona jest pula hostów. Tę grupę aplikacji można usunąć w dowolnym momencie. Nie można jednak utworzyć innej grupy aplikacji klasycznych w puli hostów, gdy istnieje grupa aplikacji klasycznych. Aby opublikować usługi RemoteApp, należy utworzyć grupę aplikacji usługi RemoteApp. Można utworzyć wiele grup aplikacji usługi RemoteApp, aby uwzględnić różne scenariusze procesów roboczych. Różne grupy aplikacji RemoteApp mogą również zawierać nakładające się usługi RemoteApp.

Aby opublikować zasoby dla użytkowników, należy przypisać je do grup aplikacji. Podczas przypisywania użytkowników do grup aplikacji należy wziąć pod uwagę następujące kwestie:

- Użytkownika można przypisać do grupy aplikacji klasycznych i grupy aplikacji RemoteApp w tej samej puli hostów. Jednak użytkownicy mogą uruchamiać tylko jeden typ grupy aplikacji na sesję. Użytkownicy nie mogą jednocześnie uruchamiać obu typów grup aplikacji w ramach jednej sesji.
- Użytkownik może być przypisany do wielu grup aplikacji w ramach tej samej puli hostów, a ich źródło danych to nagromadzenie obu grup aplikacji.

## <a name="workspaces"></a>Obszary robocze

Obszar roboczy to logiczna Grupa grup aplikacji w systemie Windows Virtual Desktop. Każda grupa aplikacji klasycznych systemu Windows musi być skojarzona z obszarem roboczym, aby użytkownicy mogli widzieć aplikacje zdalne i pulpity opublikowane na nich.  

## <a name="end-users"></a>Użytkownicy końcowi

Po przypisaniu użytkowników do grup aplikacji mogą oni łączyć się z wdrożeniem pulpitu wirtualnego systemu Windows przy użyciu dowolnego klienta pulpitu wirtualnego systemu Windows.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o dostępie delegowanym oraz o sposobach przypisywania ról użytkownikom z [dostępem delegowanym na pulpicie wirtualnym systemu Windows](delegated-access-virtual-desktop.md).

Aby dowiedzieć się, jak skonfigurować pulę hostów pulpitu wirtualnego systemu Windows, zobacz [Tworzenie puli hostów za pomocą Azure Portal](create-host-pools-azure-marketplace.md).

Aby dowiedzieć się, jak nawiązać połączenie z pulpitem wirtualnym systemu Windows, zobacz jeden z następujących artykułów:

- [Nawiązywanie połączenia z systemem Windows 10 lub Windows 7](connect-windows-7-and-10.md)
- [Nawiązywanie połączenia za pomocą przeglądarki sieci Web](connect-web.md)
- [Łączenie się z klientem systemu Android](connect-android.md)
- [Nawiązywanie połączenia z klientem systemu macOS](connect-macos.md)
- [Nawiązywanie połączenia z klientem systemu iOS](connect-ios.md)