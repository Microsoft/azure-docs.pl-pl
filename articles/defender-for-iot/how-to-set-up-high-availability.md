---
title: Konfigurowanie wysokiej dostępności
description: Zwiększ odporność na wdrożenie usługi Defender for IoT, instalując urządzenie o wysokiej dostępności w lokalnej konsoli zarządzania. Wdrożenia o wysokiej dostępności zapewniają, że zarządzane czujniki stale raportują się do aktywnej lokalnej konsoli zarządzania.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: d0e09cd37fbae91d1903ca8f175c0592b567da6e
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781657"
---
# <a name="about-high-availability"></a>Informacje o wysokiej dostępności

Zwiększ odporność na wdrożenie usługi Defender for IoT, instalując urządzenie o wysokiej dostępności w lokalnej konsoli zarządzania. Wdrożenia o wysokiej dostępności zapewniają, że zarządzane czujniki stale raportują się do aktywnej lokalnej konsoli zarządzania.

To wdrożenie jest implementowane za pomocą pary lokalnej konsoli zarządzania, która obejmuje urządzenie podstawowe i pomocnicze.

## <a name="about-primary-and-secondary-communication"></a>Informacje o komunikacji podstawowej i dodatkowej

Gdy podstawowa i pomocnicza lokalna Konsola zarządzania jest sparowana:

- Do utworzenia bezpiecznego połączenia między urządzeniami głównymi i dodatkowymi zostanie zastosowany certyfikat protokołu SSL lokalnej konsoli zarządzania. PROTOKOŁU SSL może być certyfikatem z podpisem własnym instalowanym domyślnie lub certyfikatem zainstalowanym przez klienta.

- W podstawowej lokalnej konsoli zarządzania kopia zapasowa jest tworzona automatycznie w ramach pomocniczej lokacji lokalnej, co 10 minut. Zostanie utworzona kopia zapasowa konfiguracji i danych urządzenia lokalnej konsoli zarządzania. Kopie zapasowe plików i dzienników PCAP nie są uwzględniane. Można ręcznie tworzyć kopie zapasowe i przywracać PCAPs i dzienniki.

- Konfiguracja podstawowa w konsoli zarządzania jest duplikowana na serwerze pomocniczym; na przykład ustawienia systemowe. Jeśli te ustawienia są aktualizowane na serwerze podstawowym, są one również aktualizowane na serwerze pomocniczym.

- Przed wygaśnięciem licencji pomocniczej należy ją zdefiniować jako podstawową w celu zaktualizowania licencji.

## <a name="about-failover-and-failback"></a>Informacje na temat trybu failover i powrotu po awarii

Jeśli czujnik nie może połączyć się z podstawową lokalną konsolą zarządzania, automatycznie nawiąże połączenie z serwerem pomocniczym. System będzie obsługiwany zarówno przez podstawowy, jak i pomocniczy, jeśli mniej niż połowa czujników komunikuje się z pomocniczym. Pomocnicza przejmuje się, gdy komunikuje się z nią więcej niż połowa czujników. Przełączenie w tryb failover z poziomu podstawowego do pomocniczego trwa około trzy minuty. Gdy nastąpi przejście w tryb failover, podstawowa lokalna Konsola zarządzania zostanie zamrożona. W takim przypadku możesz zalogować się do usługi pomocniczej przy użyciu tych samych poświadczeń logowania.

Podczas pracy w trybie failover czujniki kontynuują próby komunikacji z urządzeniem podstawowym. Gdy więcej niż połowa czujników zarządzanych pomyślnie komunikuje się z podstawową, zostanie przywrócony podstawowy. Po przywróceniu podstawowego komunikatu pojawia się następujący komunikat w konsoli pomocniczej.

:::image type="content" source="media/how-to-set-up-high-availability/secondary-console-message.png" alt-text="Zrzut ekranu przedstawiający komunikat wyświetlany w konsoli pomocniczej po przywróceniu elementu podstawowego.":::

Po przekierowaniu Zaloguj się ponownie do urządzenia podstawowego.

## <a name="high-availability-setup-overview"></a>Omówienie konfiguracji wysokiej dostępności

Procedury instalacji i konfiguracji są wykonywane w czterech głównych etapach:

1. Zainstaluj urządzenie podstawowe lokalnej konsoli zarządzania. 

2. Skonfiguruj urządzenie podstawowe lokalnej konsoli zarządzania. Na przykład ustawienia zaplanowanej kopii zapasowej, ustawienia sieci VLAN. Szczegóły można znaleźć w podręczniku użytkownika lokalnej konsoli zarządzania. Wszystkie ustawienia są automatycznie stosowane do urządzenia pomocniczego po parowania.

3. Zainstaluj urządzenie pomocnicze lokalnej konsoli zarządzania. Aby uzyskać więcej informacji, zobacz [Informacje o instalacji usługi Defender for IoT](how-to-install-software.md).

4. Parowanie podstawowych i pomocniczych lokalnych urządzeń konsoli zarządzania zgodnie z opisem w [tym miejscu](https://infrascale.secure.force.com/pkb/articles/Support_Article/How-to-access-your-Appliance-Management-Console). Podstawowa lokalna Konsola zarządzania musi zarządzać co najmniej dwoma czujnikami w celu przeprowadzenia instalacji.

## <a name="high-availability-requirements"></a>Wymagania dotyczące wysokiej dostępności

Sprawdź, czy zostały spełnione następujące wymagania dotyczące wysokiej dostępności:

- Wymagania certyfikatu

- Wymagania dotyczące oprogramowania i sprzętu

- Wymagania dotyczące dostępu do sieci

### <a name="software-and-hardware-requirements"></a>Wymagania dotyczące oprogramowania i sprzętu

- Zarówno podstawowe, jak i pomocnicze lokalne urządzenia konsoli zarządzania muszą być uruchomione identyczne modele sprzętu i wersje oprogramowania.

- System o wysokiej dostępności można skonfigurować za pomocą usługi Defender tylko dla użytkowników IoT przy użyciu narzędzi interfejsu wiersza polecenia.

### <a name="network-access-requirements"></a>Wymagania dotyczące dostępu do sieci

Należy sprawdzić, czy zasady zabezpieczeń organizacji umożliwiają dostęp do następujących usług w podstawowej i dodatkowej lokalnej konsoli zarządzania. Te usługi umożliwiają również połączenie między czujnikami i dodatkową lokalną konsolą zarządzania:

|Port|Usługa|Opis|
|----|-------|-----------|
|**443 lub TCP**|HTTPS|Przyznaje dostęp do konsoli sieci Web lokalnej konsoli zarządzania.|
|**22 lub TCP**|Protokół SSH|Synchronizuje dane między głównymi i dodatkowymi lokalnymi urządzeniami konsoli zarządzania|
|**123 lub UDP**|NTP| Synchronizacja czasu NTP lokalnej konsoli zarządzania. Sprawdź, czy aktywne i pasywne urządzenia są zdefiniowane za pomocą tej samej strefy czasowej.|

## <a name="create-the-primary-and-secondary-pair"></a>Tworzenie pary podstawowa i pomocnicza

Przed rozpoczęciem procedury należy sprawdzić, czy są włączone podstawowe i pomocnicze lokalne urządzenia konsoli zarządzania.  

### <a name="on-the-primary"></a>Na serwerze podstawowym

1. Zaloguj się do interfejsu wiersza polecenia jako użytkownik usługi Defender for IoT.

2. Uruchom następujące polecenie na podstawowym:

```azurecli-interactive
sudo cyberx-management-trusted-hosts-add -ip <Secondary IP>
```

>[!NOTE]
>W tym dokumencie podstawowa lokalna Konsola zarządzania jest określana jako podstawowa, a agent jest określany jako pomocniczy.

3. W polu Wprowadź adres IP urządzenia pomocniczego ```<Secondary ip>``` i wybierz pozycję Wprowadź. Adres IP zostanie zweryfikowany, a certyfikat SSL zostanie pobrany do podstawowego. Wprowadzenie adresu IP powoduje także skojarzenie czujników z urządzeniem pomocniczym.

4. Uruchom następujące polecenie na podstawowym serwerze, aby sprawdzić, czy certyfikat został poprawnie zainstalowany:

```azurecli-interactive
sudo cyberx-management-trusted-hosts-apply
```

5. Uruchom następujące polecenie na serwerze podstawowym. **Nie uruchamiaj z sudo.**

```azurecli-interactive
cyberx-management-deploy-ssh-key <Secondary IP>
```

Umożliwia to połączenie między głównymi i dodatkowymi urządzeniami do tworzenia kopii zapasowych i przywracania między nimi.

6. Wprowadź adres IP elementu pomocniczego i wybierz pozycję Wprowadź.

### <a name="on-the-secondary"></a>Na pomocniczym

1. Zaloguj się do interfejsu wiersza polecenia jako użytkownik usługi Defender for IoT.

2. Uruchom następujące polecenie na serwerze pomocniczym. **Nie uruchamiaj z sudo**:

```azurecli-interactive
cyberx-management-deploy-ssh-key <Primary ip>
```

Umożliwia to połączenie między głównymi i dodatkowymi urządzeniami do tworzenia kopii zapasowych i przywracania między nimi.

3. Wprowadź adres IP podstawowego i naciśnij klawisz ENTER.

### <a name="track-high-availability-activity"></a>Śledź aktywność wysokiej dostępności

Dzienniki aplikacji podstawowych można wyeksportować do zespołu pomocy technicznej usługi Defender for IoT, aby obsłużyć wszelkie problemy z wysoką dostępnością.  

Aby uzyskać dostęp do dzienników podstawowych:

1. Wybierz pozycję **Eksportuj** z okna **Ustawienia systemu** .

## <a name="update-the-on-premises-management-console-with-high-availability"></a>Aktualizowanie lokalnej konsoli zarządzania o wysokiej dostępności

Wykonaj aktualizację wysokiej dostępności w następującej kolejności. Przed rozpoczęciem nowego kroku upewnij się, że wszystkie kroki zostały wykonane.

Aby zaktualizować o wysokiej dostępności:

1. Zaktualizuj podstawową lokalną konsolę zarządzania.

2. Zaktualizuj pomocniczą lokalną konsolę zarządzania.

3. Aktualizowanie czujników.

## <a name="see-also"></a>Zobacz też

[Aktywowanie i konfigurowanie lokalnej konsoli zarządzania](how-to-activate-and-set-up-your-on-premises-management-console.md)