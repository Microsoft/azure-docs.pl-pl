---
title: 'Szybki start: wprowadzenie'
description: W tym przewodniku Szybki start dowiesz się, jak rozpocząć pracę z podstawowym przepływem pracy dla wdrożenia usługi Defender dla IoT.
ms.topic: quickstart
ms.date: 04/17/2021
ms.openlocfilehash: b1e7686e1d68d5a3f239320930d69f22c78e13cb
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750450"
---
# <a name="quickstart-get-started-with-defender-for-iot"></a>Szybki start: wprowadzenie do usługi Defender dla IoT

Ten artykuł zawiera omówienie kroków, które należy wykonać w celu skonfigurowania Azure Defender dla IoT. Ten proces wymaga:

- Zarejestruj swoją subskrypcję i czujniki w Azure Defender dla IoT portal.
- Zainstaluj czujnik i oprogramowanie lokalnej konsoli zarządzania.
- Wykonaj początkową aktywację czujnika i konsoli zarządzania.

## <a name="permission-requirements"></a>Wymagania dotyczące uprawnień

Niektóre kroki konfiguracji wymagają określonych uprawnień użytkownika.

Uprawnienia użytkownika administracyjnego są wymagane do aktywowania czujnika i konsoli zarządzania, przekazywania certyfikatów SSL/TLS i generowania nowych haseł.

W poniższej tabeli opisano uprawnienia dostępu użytkowników do Azure Defender dla IoT portali:

| Uprawnienie | Czytelnik zabezpieczeń | Administrator zabezpieczeń | Współautor subskrypcji | Właściciel subskrypcji |
|--|--|--|--|--|
| Wyświetlanie szczegółów oprogramowania, plików aktywacji i pakietów analizy zagrożeń oraz uzyskiwanie do nich dostępu  | ✓ | ✓ | ✓ | ✓ |
| Dołączanie czujnika  |  |  ✓ | ✓ | ✓ |
| Aktualizowanie cennika  |  |  ✓ | ✓ | ✓ |
| Odzyskiwanie hasła  | ✓  |  ✓ | ✓ | ✓ |

## <a name="identify-the-solution-infrastructure"></a>Identyfikowanie infrastruktury rozwiązania

**Wyjaśnienie potrzeb konfiguracji sieci**

Przeszukaj swoją architekturę sieci, monitorowaną przepustowość i inne szczegóły sieci. Aby uzyskać więcej informacji, zobacz About Azure Defender dla IoT network setup (Informacje [o konfiguracji sieci).](how-to-set-up-your-network.md)

**Wyjaśnienie, które czujniki i urządzenia konsoli zarządzania są wymagane do obsługi obciążenia sieci**

Azure Defender dla IoT obsługuje wdrożenia fizyczne i wirtualne. W przypadku wdrożeń fizycznych można kupić różne certyfikowane urządzenia. Aby uzyskać więcej informacji, zobacz [Identyfikowanie wymaganych urządzeń.](how-to-identify-required-appliances.md)

Zalecamy obliczenie przybliżonej liczby monitorowanych urządzeń. Później podczas rejestrowania subskrypcji platformy Azure w portalu zostanie poproszony o wprowadzenie tego numeru. Liczby można dodawać w odstępach 1000 sekund. Liczba monitorowanych urządzeń jest nazywana *zatwierdzone.*

## <a name="register-with-azure-defender-for-iot"></a>Zarejestruj się w Azure Defender dla IoT

Rejestracja obejmuje:

- Dołączanie subskrypcji platformy Azure do usługi Defender dla IoT.
- Definiowanie zatworonych urządzeń.
- Pobieranie pliku aktywacji dla lokalnej konsoli zarządzania.

W celu przeprowadzenia rejestracji:

1. Przejdź do witryny Azure Defender dla IoT portal.

1. Wybierz **pozycję Dołączanie subskrypcji.**

1. Na stronie **Cennik** wybierz subskrypcję lub utwórz nową i dodaj liczbę zatwierdzone urządzenia.

1. Wybierz **kartę Pobierz lokalną konsolę zarządzania** i zapisz pobrany plik aktywacji. Ten plik zawiera zagregowane zatwierdzone urządzenia, które zostały zdefiniowane. Plik zostanie przekazany do konsoli zarządzania po początkowym zalogowaniu.

Aby uzyskać informacje na temat sposobu dołączania subskrypcji, zobacz [Dołączanie subskrypcji](how-to-manage-subscriptions.md#offboard-a-subscription).

## <a name="install-and-set-up-the-on-premises-management-console"></a>Instalowanie i konfigurowanie lokalnej konsoli zarządzania

Po pozyskaniu lokalnego urządzenia konsoli zarządzania:

- Pobierz pakiet ISO z witryny Azure Defender dla IoT portal.
- Zainstaluj oprogramowanie.
- Aktywowanie i przeprowadzenie początkowej konfiguracji konsoli zarządzania.

Aby zainstalować i skonfigurować:

1. Wybierz **Wprowadzenie** w portalu usługi Defender dla IoT.
1. Wybierz **kartę Lokalna konsola zarządzania.**
1. Wybierz wersję i wybierz pozycję **Pobierz.**
1. Zainstaluj oprogramowanie lokalnej konsoli zarządzania. Aby uzyskać więcej informacji, zobacz [Instalacja usługi Defender dla IoT.](how-to-install-software.md)
1. Aktywuj i skonfiguruj konsolę zarządzania. Aby uzyskać więcej informacji, zobacz [Aktywowanie i konfigurowanie lokalnej konsoli zarządzania.](how-to-activate-and-set-up-your-on-premises-management-console.md)

## <a name="onboard-a-sensor"></a>Dołączanie czujnika ##

Dołączanie czujnika przez zarejestrowanie go w Azure Defender dla IoT i pobranie pliku aktywacji czujnika:

1. Zdefiniuj nazwę czujnika i skojarz ją z subskrypcją.
1. Wybierz tryb połączenia czujnika:

   - **Czujniki połączone z chmurą:** informacje wykrywane przez czujniki są wyświetlane w konsoli czujników. Ponadto informacje o alertach są dostarczane za pośrednictwem centrum IoT i mogą być udostępniane innym usługom platformy Azure, takim jak Azure Sentinel.  Możesz również automatycznie wypychać pakiety analizy zagrożeń z portalu Azure Defender dla IoT do czujników. Aby uzyskać więcej informacji, zobacz [Analizy zagrożeń i pakiety](how-to-work-with-threat-intelligence-packages.md).

   - **Czujniki zarządzane lokalnie:** informacje wykrywane przez czujniki są wyświetlane w konsoli czujników. Jeśli pracujesz w bezprzewodowej sieci i chcesz uzyskać ujednolicony widok wszystkich informacji wykrytych przez wiele lokalnie zarządzanych czujników, pracuj z lokalną konsolą zarządzania.

1. Pobierz plik aktywacji czujnika.

Aby uzyskać szczegółowe informacje na temat dołączania, zobacz Dołączanie czujników i zarządzanie [nimi w portalu usługi Defender dla IoT.](how-to-manage-sensors-on-the-cloud.md)

## <a name="install-and-set-up-the-sensor"></a>Instalowanie i konfigurowanie czujnika

Pobierz pakiet ISO z portalu Azure Defender dla IoT, zainstaluj oprogramowanie i skonfiguruj czujnik.

1. Wybierz **Wprowadzenie** w portalu usługi Defender dla IoT.

1. Wybierz **pozycję Skonfiguruj czujnik**.

1. Wybierz wersję i wybierz pozycję **Pobierz.**

1. Zainstaluj oprogramowanie czujnika. Aby uzyskać więcej informacji, zobacz [Defender for IoT installation (Instalacja usługi Defender dla IoT).](how-to-install-software.md)

1. Aktywuj i skonfiguruj czujnik. Aby uzyskać więcej informacji, zobacz [Logowanie i aktywowanie czujnika](how-to-activate-and-set-up-your-sensor.md).

## <a name="connect-sensors-to-an-on-premises-management-console"></a>Łączenie czujników z lokalną konsolą zarządzania

Podłącz czujniki do konsoli zarządzania, aby upewnić się, że:

- Czujniki wysyłają informacje o alertach i spisie urządzeń do lokalnej konsoli zarządzania.

- Lokalna konsola zarządzania może wykonywać kopie zapasowe czujników, zarządzać alertami wykrywaymi przez czujniki, badać odłączenia czujników i wykonywać inne działania na podłączonych czujnikach.

Zalecamy zgrupowanie wielu czujników monitorujących te same sieci w jednej strefie. Spowoduje to zebranie informacji zbieranych przez wiele czujników.

Aby uzyskać więcej informacji, zobacz Connect sensors to the on-premises management console (Łączenie [czujników z lokalną konsolą zarządzania).](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console)

## <a name="populate-azure-sentinel-with-alert-information-optional"></a>Wypełnij pola Azure Sentinel informacjami o alertach (opcjonalnie)

Wysyłanie informacji o alertach do Azure Sentinel przez skonfigurowanie Azure Sentinel. Zobacz Connect your data from Defender for IoT to Azure Sentinel (Łączenie danych [z usługi Defender dla IoT z Azure Sentinel).](how-to-configure-with-sentinel.md)

## <a name="next-steps"></a>Następne kroki ##

[Witamy w Azure Defender dla IoT](overview.md)

[Azure Defender dla IoT architektury](architecture.md)
