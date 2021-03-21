---
title: Opis aktualizacji urządzenia dla usługi Azure IoT Hub APT manifest | Microsoft Docs
description: Opis sposobu, w jaki aktualizacja urządzenia dla IoT Hub używa manifestu apt na potrzeby aktualizacji opartej na pakiecie.
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0b68b78499aa3bf0d84d8bd0fa5ab55d1f969113
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101679591"
---
# <a name="device-update-apt-manifest"></a>Manifest APT aktualizacji urządzenia

Manifest APT jest plikiem JSON opisującym Szczegóły aktualizacji wymagane przez program obsługi aktualizacji APT. Ten plik można zaimportować do aktualizacji urządzenia dla IoT Hub tak samo jak każda inna aktualizacja.

[Dowiedz się więcej](import-update.md) o importowaniu aktualizacji do aktualizacji urządzeń.

## <a name="overview"></a>Omówienie

Gdy manifest APT jest dostarczany do agenta aktualizacji urządzenia jako aktualizacja, Agent przetworzy manifest i wykona niezbędne operacje. Operacje te obejmują pobieranie i instalowanie pakietów określonych w pliku manifestu APT i ich zależności.

Aktualizacja urządzenia obsługuje APT UpdateType i APT Update. Ta obsługa umożliwia agentowi aktualizacji urządzeń ocenę zainstalowanych pakietów Debian i zaktualizowanie wymaganych pakietów. 

## <a name="schema"></a>Schemat

Plik manifestu APT to plik JSON ze schematem z wersją.

```json
{
    "name": "<name>",
    "version": "<version>",
    "packages": [
        {
            "name": "<package name>",
            "version": "<version specifier>"
        }
    ]
}
```

Przykład:

```json
{
    "name": "contoso-iot-edge",
    "version": "1.0.0.0",
    "packages": [
        {
            "name" : "thermocontrol",
            "version" : "1.0.1"
        },
        {
            "name" : "tempreport",
            "version" : "2.0.0"
        }
    ]
}
```

### <a name="name"></a>name

Nazwa tego manifestu APT. Może to być nazwa lub identyfikator mające znaczenie dla Twoich scenariuszy. Na przykład `contoso-iot-edge`.

### <a name="version"></a>Wersja

Numer wersji dla tego manifestu APT. Na przykład `1.0.0.0`.


### <a name="packages"></a>pakiety

Lista obiektów zawierających właściwości specyficzne dla pakietu.

#### <a name="name"></a>name

Nazwa lub identyfikator pakietu. Na przykład `iotedge`.

#### <a name="version"></a>Wersja

Odpowiednie kryteria wersji pakietu. Na przykład `1.0.8-2`.

Obecnie jest obsługiwany tylko dokładny numer wersji. Numer wersji to wymagana wersja pakietu debian w formacie [epok:] upstream_version [-debian_revision].

**Epoka** jest niepodpisana int.

**upstream_version** może zawierać alfanumeryczne i znaki, takie jak ".", "+", "-" i "~". Powinien zaczynać się cyfrą.
> [!NOTE]
> wartość "1.0.8" jest równa "1.0.8-0"

Na przykład **`"name":"iotedge" and "version":"1.0.8-2"`** jest równoważne z instalacją pakietu przy użyciu polecenia `apt-get install iotedge=1.0.8-2`

> [!NOTE]
> Wartość wersji nie zawiera znaku równości

W przypadku pominięcia wersji zostanie zainstalowana najnowsza dostępna wersja określonego pakietu.

[Dowiedz się więcej](https://www.debian.org/doc/debian-policy/ch-controlfields.html#s-f-version) o wersji pakietów debian.

> [!NOTE]
> Menedżer pakietów APT ignoruje wymagania dotyczące obsługi wersji dostarczone przez pakiet, gdy zależne pakiety do zainstalowania są automatycznie rozwiązywane. Dopóki nie zostaną podane jawne wersje pakietów zależnych, zostaną one użyte w najnowszej wersji, nawet jeśli sam pakiet może określić ścisłe wymaganie (=) w danym systemie. To automatyczne rozwiązanie może prowadzić do błędów dotyczących zależności niewypełnienia. [Więcej informacji](https://unix.stackexchange.com/questions/350192/apt-get-not-properly-resolving-a-dependency-on-a-fixed-version-in-a-debian-ubunt)

Jeśli aktualizujesz określoną wersję Azure IoT Edge Security daemon, należy dołączyć odpowiednią wersję `iotedge` pakietu i jego pakiet zależny `libiothsm-std` do manifestu apt.
[Więcej informacji](https://docs.microsoft.com/azure/iot-edge/how-to-update-iot-edge#update-the-security-daemon)

> [!NOTE]
> Manifestu apt można użyć do aktualizacji agenta aktualizacji urządzeń i jego zależności. Wyświetl nazwę agenta aktualizacji urządzenia i żądaną wersję w manifeście apt, tak jak w przypadku każdego innego pakietu. Ten manifest apt można następnie zaimportować i wdrożyć za pomocą aktualizacji urządzenia dla potoku IoT Hub. 

## <a name="removing-packages"></a>Usuwanie pakietów

Do usunięcia zainstalowanych pakietów z urządzenia można także użyć manifestu apt. Pojedynczy manifest apt może służyć do usuwania, dodawania i aktualizowania wielu pakietów. Aby usunąć pakiet, należy dodać znak minusa "-" po nazwie pakietu. Nie należy uwzględniać numeru wersji pakietów, które są usuwane. Usuwanie pakietów za poorednictwem manifestu apt nie powoduje usunięcia jego zależności i konfiguracji.

Przykład:

```json
{
    "name": "contoso-video",
    "version": "2.0.0.1",
    "packages": [
        {
            "name" : "foo-"
        }
    ]
}
```
Ten manifest apt usuwa pakiet "foo" z urządzeń, do których jest wdrożony. 

## <a name="recommended-value-for-installed-criteria"></a>Zalecana wartość dla zainstalowanych kryteriów

Kryteria instalacji manifestu APT to `<name>-<version>` `<name>` , gdzie jest nazwą manifestu apt i `<version>` jest wersją manifestu apt. Na przykład `contoso-iot-edge-1.0.0.0`. 

## <a name="guidelines-on-creating-an-apt-manifest"></a>Wytyczne dotyczące tworzenia manifestu APT

Podczas tworzenia manifestu APT należy wziąć pod uwagę pewne wskazówki:

- Zawsze upewnij się, że manifest APT jest poprawnie sformułowanym plikiem JSON
- Każdy manifest APT powinien mieć unikatową wersję. Spróbuj użyć znormalizowanej metodologii, aby zwiększyć wersję manifestu APT, dzięki czemu ma sens dla Twoich scenariuszy i można z łatwością korzystać
- Gdy chodzi o żądany stan poszczególnych pakietów, należy określić dokładną nazwę i wersję pakietu, który ma zostać zainstalowany na urządzeniu. Zawsze sprawdzaj poprawność wartości względem repozytorium pakietów, które ma być używane jako źródło pakietu
- Upewnij się, że pakiety w manifeście APT są wymienione w kolejności, w jakiej powinny być zainstalowane/usunięte
- Zawsze sprawdzaj poprawność instalacji pakietów na urządzeniu testowym, aby upewnić się, że wynik jest żądany
- W przypadku instalowania określonej wersji pakietu (na przykład `iotedge 1.0.9-1` ) najlepszym rozwiązaniem jest również posiadanie w MANIFEŚCIE apt jawnych wersji pakietów zależnych, które mają zostać zainstalowane (na przykład `libiothsm 1.0.9-1` ).
- Chociaż nie jest to wymagane, zawsze upewnij się, że manifest APT jest skumulowany, aby uniknąć nieznanego stanu urządzenia. Aktualizacja zbiorcza pozwala upewnić się, że Twoje urządzenia mają żądaną wersję każdego pakietu, którego potrzebujesz, nawet jeśli urządzenie pominął wdrożenie aktualizacji APT z powodu niepowodzenia instalacji lub przełączenia w tryb offline

Na przykład:

**Podstawowy manifest APT**

```JSON
{
    "name": "contoso-iot-edge",
    "version": "1.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        }
    ]
}
```

**ZŁA AKTUALIZACJA**

Ta aktualizacja obejmuje pakiet słupkowy, ale nie pakiet foo.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

**DOBRA AKTUALIZACJA**

Ta aktualizacja obejmuje pakiet foo, a także pakiet słupkowy.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        },
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Importuj nową aktualizację](import-update.md)

