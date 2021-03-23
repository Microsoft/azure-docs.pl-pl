---
title: 'Szybki Start: wprowadzenie'
description: W tym przewodniku szybki start dowiesz się, jak rozpocząć pracę z opisem podstawowego przepływu pracy dla wdrożenia usługi Defender for IoT.
ms.topic: quickstart
ms.date: 2/18/2021
ms.openlocfilehash: c6136d734570714b691a4ba3c2a72305c4c85590
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784496"
---
# <a name="quickstart-get-started-with-defender-for-iot"></a>Szybki Start: Rozpoczynanie pracy z usługą Defender for IoT

Ten artykuł zawiera omówienie czynności, które należy wykonać w celu skonfigurowania usługi Azure Defender for IoT. Proces wymaga:

- Zarejestruj swoją subskrypcję i czujniki w portalu usługi Azure Defender dla IoT.
- Zainstaluj czujnik i lokalne oprogramowanie konsoli zarządzania.
- Wykonaj początkową aktywację czujnika i konsoli zarządzania.

## <a name="prerequisites"></a>Wymagania wstępne

Brak

## <a name="permission-requirements"></a>Wymagania dotyczące uprawnień

Niektóre kroki instalacji wymagają określonych uprawnień użytkownika.

Do aktywowania czujnika i konsoli zarządzania wymagane są uprawnienia użytkownika administracyjnego, przekazywanie certyfikatów SSL/TLS i generowanie nowych haseł.

W poniższej tabeli opisano uprawnienia dostępu użytkowników do usługi Azure Defender dla narzędzi portalu IoT:

| Uprawnienie | Czytelnik zabezpieczeń | Administrator zabezpieczeń | Współautor subskrypcji | Właściciel subskrypcji |
|--|--|--|--|--|
| Wyświetlanie wszystkich ekranów i danych usługi Defender dla usługi IoT | ✓ | ✓ | ✓ | ✓ |
| Dołączanie czujnika  |  |  ✓ | ✓ | ✓ |
| Aktualizowanie cen  |  |  ✓ | ✓ | ✓ |
| Odzyskiwanie hasła  | ✓  |  ✓ | ✓ | ✓ |

## <a name="identify-the-solution-infrastructure"></a>Identyfikowanie infrastruktury rozwiązania

**Wyjaśnij potrzeby związane z konfiguracją sieci**

Zbadaj architekturę sieci, monitorowaną przepustowość i inne szczegóły sieci. Aby uzyskać więcej informacji, zobacz [Informacje o konfiguracji sieci usługi Azure Defender for IoT](how-to-set-up-your-network.md).

**Wyjaśnij, które czujniki i urządzenia konsoli zarządzania są wymagane do obsługi obciążenia sieciowego**

Usługa Azure Defender for IoT obsługuje zarówno wdrożenia fizyczne, jak i wirtualne. W przypadku wdrożeń fizycznych można zakupić różne certyfikowane urządzenia. Aby uzyskać więcej informacji, zobacz [identyfikowanie wymaganych urządzeń](how-to-identify-required-appliances.md).

Zalecamy Obliczanie przybliżonej liczby monitorowanych urządzeń. Później, po zarejestrowaniu subskrypcji platformy Azure w portalu, zostanie wyświetlony monit o wprowadzenie tej liczby. Liczby można dodawać w odstępach wynoszących 1 000 sekund. Liczba monitorowanych urządzeń jest nazywana *zatwierdzonymi urządzeniami*.

## <a name="register-with-azure-defender-for-iot"></a>Zarejestruj się w usłudze Azure Defender dla IoT

Rejestracja obejmuje:

- Dołączanie subskrypcji platformy Azure do usługi Defender for IoT.
- Definiowanie zatwierdzonych urządzeń.
- Pobieranie pliku aktywacji dla lokalnej konsoli zarządzania.

W celu przeprowadzenia rejestracji:

1. Przejdź do portalu usługi Azure Defender dla IoT.
1. Wybierz pozycję **subskrypcja** dołączania.
1. Na stronie **Cennik** wybierz subskrypcję lub Utwórz nową, a następnie Dodaj liczbę zatwierdzonych urządzeń.
1. Wybierz kartę **Pobierz lokalną konsolę zarządzania** i zapisz pobrany plik aktywacji. Ten plik zawiera zdefiniowane zagregowane urządzenia. Po wstępnym zalogowaniu plik zostanie przekazany do konsoli zarządzania.

Aby uzyskać informacje na temat odłączania subskrypcji, zobacz [odłączania a Subscription (subskrypcja](how-to-manage-sensors-on-the-cloud.md#offboard-a-subscription)).

## <a name="install-and-set-up-the-on-premises-management-console"></a>Instalowanie i Konfigurowanie lokalnej konsoli zarządzania

Po uzyskaniu lokalnego urządzenia konsoli zarządzania:

- Pobierz pakiet ISO z portalu usługi Azure Defender dla IoT.
- Zainstaluj oprogramowanie.
- Aktywuj i przeprowadź instalację początkowej konsoli zarządzania.

Aby zainstalować i skonfigurować:

1. Wybierz **wprowadzenie** z portalu Defender for IoT.
1. Wybierz kartę **lokalna Konsola administracyjna** .
1. Wybierz wersję i wybierz pozycję **Pobierz**.
1. Zainstaluj oprogramowanie lokalnej konsoli zarządzania. Aby uzyskać więcej informacji, zobacz temat [Instalacja usługi Defender for IoT](how-to-install-software.md).
1. Aktywuj i skonfiguruj konsolę zarządzania. Aby uzyskać więcej informacji, zobacz [Aktywacja i Konfigurowanie lokalnej konsoli zarządzania](how-to-activate-and-set-up-your-on-premises-management-console.md).

## <a name="onboard-a-sensor"></a>Dołączanie czujnika

Dołączanie czujnika przez zarejestrowanie go w usłudze Azure Defender dla IoT i pobranie pliku aktywacji czujnika:

1. Zdefiniuj nazwę czujnika i skojarz ją z subskrypcją.
1. Wybierz tryb zarządzania czujnikami:

   - **Czujniki połączone z chmurą**: informacje wykrywane przez czujniki są wyświetlane w konsoli czujnika. Ponadto informacje o alertach są dostarczane przez Centrum IoT Hub i mogą być współużytkowane z innymi usługami platformy Azure, takimi jak Azure.

   - **Czujniki zarządzane lokalnie**: informacje, które wykrywa czujników, są wyświetlane w konsoli czujnika. Jeśli pracujesz w sieci gapped i chcesz, aby ujednolicony widok wszystkich informacji został wykryty przez wiele zarządzanych lokalnie czujników, Pracuj z lokalną konsolą zarządzania. 

1. Pobierz plik aktywacji czujnika.

Aby uzyskać więcej informacji, zobacz Dołączanie [czujników i zarządzanie nimi w portalu Defender for IoT](how-to-manage-sensors-on-the-cloud.md).

## <a name="install-and-set-up-the-sensor"></a>Instalowanie i Konfigurowanie czujnika

Pobierz pakiet ISO z portalu usługi Azure Defender dla IoT, zainstaluj oprogramowanie i Skonfiguruj czujnik.

1. Wybierz **wprowadzenie** z portalu Defender for IoT.
1. Wybierz pozycję **Skonfiguruj czujnik**.
1. Wybierz wersję i wybierz pozycję **Pobierz**.
1. Zainstaluj oprogramowanie czujnika. Aby uzyskać więcej informacji, zobacz temat [Instalacja usługi Defender for IoT](how-to-install-software.md).
1. Aktywuj i Skonfiguruj czujnik. Aby uzyskać więcej informacji, zobacz temat [Logowanie i aktywowanie czujnika](how-to-activate-and-set-up-your-sensor.md).

## <a name="connect-sensors-to-an-on-premises-management-console"></a>Łączenie czujników z lokalną konsolą zarządzania

Połącz czujniki z konsolą zarządzania, aby upewnić się, że:

- Czujniki wysyłają informacje o alertach i spisie urządzeń do lokalnej konsoli zarządzania.

- Lokalna Konsola zarządzania umożliwia wykonywanie kopii zapasowych czujników, Zarządzanie alertami wykrywanymi przez czujniki, badanie odłączeń czujników i wykonywanie innych działań w przypadku połączonych czujników.

Zalecamy Grupowanie wielu czujników monitorujących te same sieci w jednej strefie. Spowoduje to połączenie informacji zbieranych przez wiele czujników.

Aby uzyskać więcej informacji, zobacz sekcję [łączenie czujników z lokalną konsolą zarządzania](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console).

## <a name="populate-azure-sentinel-with-alert-information-optional"></a>Wypełnianie wskaźnikowego platformy Azure informacjami o alercie (opcjonalnie)

Wyślij informacje o alercie do usługi Azure wskaźnikowej, konfigurując wskaźnik platformy Azure. Zobacz [łączenie danych z usługi Defender for IoT z wskaźnikiem na platformę Azure](how-to-configure-with-sentinel.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Usługa Azure Defender dla IoT](overview.md) 
>  — Zapraszamy! [Architektura usługi Azure Defender for IoT](architecture.md)
