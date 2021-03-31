---
title: Środowisko pulpitu wirtualnego systemu Windows — Azure
description: Zapoznaj się z podstawowymi elementami środowiska pulpitu wirtualnego systemu Windows, takimi jak pule hostów i grupy aplikacji.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 99a07dd1791b539ea44fcbab250aa9c227ee1705
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88002613"
---
# <a name="windows-virtual-desktop-environment"></a>Środowisko usługi Windows Virtual Desktop

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/environment-setup-2019.md).

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

- [Nawiązywanie połączenia z systemem Windows 10 lub Windows 7](connect-windows-7-10.md)
- [Nawiązywanie połączenia za pomocą przeglądarki sieci Web](connect-web.md)
- [Łączenie się z klientem systemu Android](connect-android.md)
- [Nawiązywanie połączenia z klientem systemu macOS](connect-macos.md)
- [Nawiązywanie połączenia z klientem systemu iOS](connect-ios.md)