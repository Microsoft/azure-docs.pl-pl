---
title: Identyfikowanie wymaganych urządzeń
description: Informacje o urządzeniach sprzętowych i wirtualnych certyfikowanych usługi Defender for IoT i lokalnej konsoli zarządzania.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/13/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 92bf066c9769cc4b2525923b9e18ed3c0e9c577a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937139"
---
# <a name="identify-required-appliances"></a>Identyfikowanie wymaganych urządzeń

Ten artykuł zawiera informacje dotyczące certyfikowanych usługi Defender dla urządzeń czujników usługi IoT. Usługę Defender Fort IoT można wdrożyć na urządzeniach fizycznych i wirtualnych.

Obejmuje to certyfikowane *wstępnie skonfigurowane* urządzenia, na których oprogramowanie jest już zainstalowane, a także nieskonfigurowane, certyfikowane urządzenia, na których można pobrać i zainstalować wymagane oprogramowanie.

Artykuł zawiera również specyfikacje dotyczące lokalnego urządzenia konsoli zarządzania. Lokalna Konsola zarządzania nie jest dostępna jako urządzenie wstępnie skonfigurowane.

- Jeśli chcesz kupić wstępnie skonfigurowany czujnik, przejrzyj modele dostępne w sekcji [urządzenia czujnika](#sensor-appliances) , a następnie kontynuuj zakup.

- Jeśli chcesz kupić własne urządzenie, przejrzyj modele dostępne w sekcji [urządzenia czujnika](#sensor-appliances) i w sekcji [dodatkowe certyfikowane urządzenia](#additional-certified-appliances) . Po uzyskaniu urządzenia można pobrać i zainstalować oprogramowanie.

- Jeśli chcesz kupić lokalną konsolę zarządzania, zapoznaj się z informacjami w sekcji [urządzenie lokalnej konsoli zarządzania](#on-premises-management-console-appliance) . Po uzyskaniu urządzenia można pobrać i zainstalować oprogramowanie.

Po wykonaniu zadań w tym miejscu możesz zainstalować oprogramowanie i skonfigurować sieć.

## <a name="sensor-appliances"></a>Urządzenia czujnika

Usługa Defender for IoT obsługuje wdrożenia fizyczne i wirtualne.

### <a name="physical-sensors"></a>Czujniki fizyczne

Ta sekcja zawiera omówienie dostępnych modeli czujnika fizycznego. Można kupić czujniki ze wstępnie skonfigurowanym oprogramowaniem lub niewstępnie skonfigurowanymi czujnikami zakupu.

| Typ wdrożenia | Firmowe | Przedsiębiorstwa | SMB |
|--|--|--|--|
| Image (Obraz) | :::image type="content" source="media/how-to-prepare-your-network/corporate-hpe-proliant-dl360-v2.png" alt-text="Model na poziomie firmy."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="Model na poziomie przedsiębiorstwa."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="Model na poziomie SMB."::: |
| Model | HPE ProLiant DL360 | HPE ProLiant DL20 | HPE ProLiant DL20 |
| Monitorowanie portów | Do 15 RJ45 lub 8 OPT | Do 8 RJ45 lub 6 OPT | 4 RJ45 |
| Maksymalna przepustowość [1](#anchortext) | 3 GB na sekundę | 1 GB na sekundę | 200 MB na sekundę |
| Maksymalna liczba chronionych urządzeń | 30 000 | 15 000 | 1000 |

Informacje o wymaganiach dostawcy można znaleźć w temacie [specyfikacje urządzeń](#appliance-specifications) .

Informacje o wstępnie skonfigurowanych czujników: Firma Microsoft połączyła się ze strzałką w celu zapewnienia wstępnie skonfigurowanych czujników. Aby zakupić wstępnie skonfigurowany czujnik, skontaktuj się ze strzałką pod następującym adresem: <hardware.sales@arrow.com>

Informacje o sposobie umieszczania własnego urządzenia: Zapoznaj się z opisanymi tutaj modelami. Po uzyskaniu urządzenia przejdź do usługi **Defender for IoT**  >  **Network Sensors**  >  **instalacji** ISO, aby pobrać oprogramowanie.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="Czujniki sieci ISO.":::

<a id="anchortext">1</a> pojemność przepustowości może się różnić w zależności od dystrybucji protokołów.

### <a name="virtual-sensors"></a>Czujniki wirtualne

Ta sekcja zawiera omówienie dostępnych czujników wirtualnych.

| Typ wdrożenia | Firmowe | Przedsiębiorstwa | SMB |
|--|--|--|--|
| Przepustowość Maksymalna | 2,5 GB/s | 800 MB/s | 160 MB/s |
| Maksymalna liczba chronionych urządzeń | 30 000 | 10 000 | 2500 |

## <a name="on-premises-management-console-appliance"></a>Lokalne urządzenie konsoli zarządzania

Konsola zarządzania jest dostępna jako wdrożenie wirtualne.

| Typ wdrożenia | Przedsiębiorstwa |
|--|--|
| Typ urządzenia | HPE DL20, MASZYNA WIRTUALNA |
| Liczba czujników zarządzanych | Do 300 |

Po uzyskaniu lokalnej konsoli zarządzania przejdź do pozycji **Defender for IoT**  >  **lokalna Konsola zarządzania**  >  **instalacji ISO** , aby pobrać plik ISO.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-iso-download-screen.png" alt-text="Lokalna Konsola zarządzania.":::

## <a name="appliance-specifications"></a>Specyfikacje urządzenia

W tej sekcji opisano specyfikacje sprzętu dla następujących urządzeń:

- Wdrożenie firmowe: HPE ProLiant DL360

- Wdrażanie w przedsiębiorstwie: HPE ProLiant DL20

- Wdrożenie SMB: HPE ProLiant DL20

- Specyfikacje urządzenia wirtualnego

## <a name="corporate-deployment-hpe-proliant-dl360"></a>Wdrożenie firmowe: HPE ProLiant DL360

| Składnik | Specyfikacje techniczne |
|--|--|
| Obudowa | serwer stojaków o rozmiarze 1U |
| Wymiary | 42,9 x 43,46 x 70,7 (cm)/1.69 "x 17,11" x 27,83 "(w) |
| Waga | Maks 16,27 kg (35,86 lb) |
| Procesor | Intel Xeon Silver 4215 R 3,2 GHz, pamięć podręczna 11M, 8c/16T, 130 W |
| Mikroukładu | Intel C621 |
| Pamięć | 32 GB = 2 x 16 GB 2666MT/s DDR4 ECC UDIMM |
| Magazyn | 6 x 1,2-TB SAS 12G Enterprise 10 SFF (2,5 w) w Hot-Plug dysku twardym — RAID 5 |
| Kontroler sieci | Na płycie: 2 x 1 GB Broadcom BCM5720<br>Na płycie LOM: karta portu iDRAC 1 – GB Broadcom BCM5720<br><br>Zewnętrzna: 1 x karta serwera Intel Ethernet I350 wystawcy 1 GB, niski profil |
| Zarządzanie | HPE MOP — zaawansowane |
| Dostęp do urządzenia | Dwa tylne USB 3,0<br>Jeden z przodu USB 2,0<br>Jeden wewnętrzny port USB 3,0 |
| Zasilanie | 2 x HPE 500 W gnieździe Flex z gorącym zestawem energooszczędnych technologii Platinum |
| Obsługa stojaków | HPE 1U Gen10 SFF Easy Installation Kit |

### <a name="appliance-bom"></a>BOM urządzenia

| NC | Opis | Liczba |
|--|--|--|
| P19766-B21 | HPE DL360 Gen10 8SFF NC dyrektor ds Server | 1 |
| P19766-B21 | Europa — lokalizacja wielojęzyczna | 1 |
| P24479-L21 | Intel Xeon-S 4215 R FIO Kit dla DL360 G10 | 1 |
| P24479-B21 | Intel Xeon-S 4215 R Kit dla DL360 Gen10 | 1 |
| P00922-B21 | HPE 16 GB 2Rx8 PC4-2933Y-R Smart Kit | 2 |
| 872479 – B21 | DYSK TWARDY HPE 1,2-TB SAS 10 SFF SC DS | 6 |
| 811546 – B21 | HPE 1-GbE 4-p BASE-T I350 adapter | 1 |
| P02377-B21 | HPE Smart hybrydowego Capacitor w \_ 145 mm | 1 |
| 804331 – B21 | HPE Smart Array P408i — kontroler Gen10 SR | 1 |
| 665240 – B21 | HPE 1-GbE 4-p FLR-T I350 adapter | 1 |
| 871244 – B21 | HPE DL360 Gen10 — zestaw wentylatorów o wysokiej wydajności | 1 |
| 865408 – B21 | HPE 500-W Zr. | 2 |
| 512485 – B21 | HPE MOP ADV 1 — Pomoc techniczna w 1 roku | 1 |
| 874543 – B21 | HPE 1U Gen10 SFF Easy Installation Kit | 1 |

## <a name="enterprise-deployment-hpe-proliant-dl20"></a>Wdrażanie w przedsiębiorstwie: HPE ProLiant DL20

| Składnik | Specyfikacje techniczne |
|--|--|
| Obudowa | serwer stojaków o rozmiarze 1U |
| Wymiary (wysokość x szerokość x głębokości) | 4,32 x 43,46 x 38,22 cm/1.70 x 17,11 x 15,05 cala |
| Waga | 7,9 kg/17.41 lb |
| Procesor | Intel Xeon E-2234, 3,6 GHz, 4C/8T, 71 W |
| Mikroukładu | Intel C242 |
| Pamięć | 2 x 16 GB z podwójną rangą x8 DDR4-2666 |
| Magazyn | 3 x 1 TB SATA 6G Midline 7,2 K SFF (2,5 w) — RAID 5 z inteligentną P408i |
| Kontroler sieci | Na płycie: 2 x 1 GB <br>Na płycie: karta portu MOP 1 GB <br>Zewnętrzna: 1 x HPE Ethernet 1 GB 4-Port 366FLR adapter |
| Zarządzanie | HPE MOP — zaawansowane |
| Dostęp do urządzenia | Przód: 1 x USB 3,0, 1 x port usługi MOP USB <br>Tył: 2 x USB 3,0 <br>Wewnętrzny: 1 x USB 3,0 |
| Zasilanie | Podwójne zasilacze gorącej wtyczki 500 W |
| Obsługa stojaków | Zestaw szyn krótkich HPE 1U |

### <a name="appliance-bom"></a>BOM urządzenia

| NC | Opis: wysoki koniec | Liczba |
|--|--|--|
| P06963-B21 | HPE DL20 Gen10 4SFF dyrektor ds Server | 1 |
| P06963-B21 | HPE DL20 Gen10 4SFF dyrektor ds Server | 1 |
| P17104-L21 | HPE DL20 Gen10 E-2234 FIO | 1 |
| 879507 – B21 | HPE 16-GB 2Rx8 PC4-2666V-E STND Kit | 2 |
| 655710 – B21 | HPE 1 TB DYSKU SATA 7,2 K SFF SC DS | 3 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM zestaw Riser | 1 |
| 665240 – B21 | HPE Ethernet 1 GB 4-Port 366FLR adapter | 1 |
| 782961 – B21 | HPE 12-W inteligentnej baterii magazynu | 1 |
| 869081 – B21 | HPE Smart Array P408i — kontroler SR G10 LH | 1 |
| 865408 – B21 | HPE 500-W Zr. | 2 |
| 512485 – B21 | HPE MOP ADV 1 — Pomoc techniczna w 1 roku | 1 |
| P06722-B21 | HPE DL20 Gen10 RPS pliku Enable FIO Kit | 1 |
| 775612 – B21 | Zestaw szyn krótkich HPE 1U | 1 |

## <a name="smb-deployment-hpe-proliant-dl20"></a>Wdrożenie SMB: HPE ProLiant DL20

| Składnik | Specyfikacje techniczne |
|--|--|
| Obudowa | serwer stojaków o rozmiarze 1U |
| Wymiary (wysokość x szerokość x głębokości) | 4,32 x 43,46 x 38,22 cm/1.70 x 17,11 x 15,05 cala |
| Waga | 7,88 kg/17.37 lb |
| Procesor | Intel Xeon E-2224, 3,4 GHz, 4C, 71 W |
| Mikroukładu | Intel C242 |
| Pamięć | 1 x 8 GB z podwójną rangą x8 DDR4-2666 |
| Magazyn | 2 x 1 TB SATA 6G Midline 7,2 K SFF (2,5 w) — RAID 1 z inteligentną tablicą P208i-a |
| Kontroler sieci | Na płycie: 2 x 1 GB <br>Na płycie: karta portu MOP 1 GB <br>Zewnętrzna: 1 x HPE Ethernet 1 GB 4-Port 366FLR adapter |
| Zarządzanie | HPE MOP — zaawansowane |
| Dostęp do urządzenia | Przód: 1 x USB 3,0, 1 x port usługi MOP USB <br>Tył: 2 x USB 3,0 <br>Wewnętrzny: 1 x USB 3,0 |
| Zasilanie | Zasilacz z gorącą wtyczką 290 W |
| Obsługa stojaków | Zestaw szyn krótkich HPE 1U |

### <a name="appliance-bom"></a>BOM urządzenia

| NC | Opis | Liczba |
|--|--|--|
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF dyrektor ds Server | 1 |
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF dyrektor ds Server | 1 |
| P17102-L21 | HPE DL20 Gen10 E-2224 FIO | 1 |
| 879505 – B21 | HPE 8-GB 1Rx8 PC4-2666V-E STND Kit | 1 |
| 801882 – B21 | DYSK TWARDY HPE 1 TB SATA 7,2 K LFF RW | 2 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM zestaw Riser | 1 |
| 665240 – B21 | HPE Ethernet 1 GB 4-Port 366FLR adapter | 1 |
| 869079 – B21 | HPE Smart Array E208i — kontroler SR G10 LH | 1 |
| P21649-B21 | HPE DL20 Gen10 290 W FIO PSU Kit | 1 |
| P06683-B21 | HPE DL20 Gen10 M. 2 SATA/LFF AROC | 1 |
| 512485 – B21 | HPE MOP ADV 1 — Pomoc techniczna w 1 roku | 1 |
| 775612 – B21 | Zestaw szyn krótkich HPE 1U | 1 |

## <a name="virtual-appliance-specifications"></a>Specyfikacje urządzenia wirtualnego

### <a name="sensors"></a>Sensor

| Typ | Firmowe | Przedsiębiorstwa | SMB |
|--|--|--|--|
| Procesor wirtualny | 32 | 8 | 4 |
| Pamięć | 32 GB | 32 GB | 8 GB |
| Magazyn | 5,6 TB | 1,8 TB | 500 GB |

### <a name="on-premises-management-console-appliance"></a>Lokalne urządzenie konsoli zarządzania

| Typ | Przedsiębiorstwa |
|--|--|
| Opis | Wirtualne urządzenie dla typów wdrożeń w przedsiębiorstwie |
| Procesor wirtualny | 8 |
| Pamięć | 32 GB |
| Magazyn | 1,8 TB |

Obsługiwane funkcje hypervisor: VMware ESXi w wersji 5,0 lub nowszej, funkcja Hyper-V

## <a name="additional-certified-appliances"></a>Dodatkowe certyfikowane urządzenia

Ta sekcja zawiera szczegółowe informacje o dodatkowych urządzeniach certyfikowanych przez firmę Microsoft, ale nie są oferowane jako wstępnie skonfigurowane urządzenia.

| Typ wdrożenia | Przedsiębiorstwa |
|--|--|
| Image (Obraz) | :::image type="content" source="media/how-to-prepare-your-network/deployment-type-enterprise-for-azure-defender-for-iot-v2.png" alt-text="Typ wdrożenia przedsiębiorstwa."::: |
| Model | Dell PowerEdge R340 XL |
| Monitorowanie portów | Do dziewięciu RJ45 lub sześciu OPT |
| Maksymalna przepustowość [1](#anchortext2)| 1 GB/s |
| Maksymalna liczba chronionych urządzeń | 10 000 |

<a id="anchortext2">Jeden</a> Przepustowość może się różnić w zależności od dystrybucji protokołów.

Po zakupie urządzenia przejdź do usługi **Defender for IoT**  >  **Network Sensors**  >  **instalacji** ISO, aby pobrać oprogramowanie.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="Czujniki sieci ISO.":::

## <a name="enterprise-deployment-dell-poweredge-r340-xl"></a>Wdrażanie w przedsiębiorstwie: Dell PowerEdge R340 XL

| Składnik | Specyfikacje techniczne |
|--|--|
| Obudowa | serwer stojaków o rozmiarze 1U |
| Wymiary | 42,8 x 434,0 x 596 (mm)/1,67 "x 17,09" x 23,5 "(w) |
| Waga | Maks 29,98 lb/13,6 kg |
| Procesor | Intel Xeon E-2144G 3,6 GHz, pamięci podręcznej 8 M, 4C/8T, Turbo (71 W) |
| Mikroukładu | Intel C246 |
| Pamięć | 32 GB = 2 x 16 GB 2666MT/s DDR4 ECC UDIMM |
| Magazyn | 3 x 2-TB 7,2 K RPM SATA 6-GB/s 512n 3,5-w Hot-Plug dysk twardy — RAID 5 |
| Kontroler sieci | Na płycie: 2 x 1 GB Broadcom BCM5720<br>Na płycie LOM: karta portu iDRAC 1 – GB Broadcom BCM5720 <br><br>Zewnętrzna: 1 x karta serwera Intel Ethernet I350 wystawcy 1 GB, niski profil |
| Zarządzanie | iDRAC dziewięć przedsiębiorstw |
| Dostęp do urządzenia | Dwa tylne USB 3,0 <br> Jeden z przodu USB 3,0 |
| Zasilanie | Podwójne zasilacze gorącej wtyczki 350 W |
| Obsługa stojaków | ReadyRails II przesuwa szyny dla montażowo bez narzędzi, w 4-i niewielowątkowych dziurach, a także narzędzia do montażu w stojakach, z obsługą opcjonalnego, bezwątkowego ramienia zarządzania kablem. |

## <a name="dell-r340-bom"></a>R340 BOM firmy Dell

:::image type="content" source="media/how-to-prepare-your-network/enterprise-deployment-for-azure-defender-for-iot-dell-r340-bom.png" alt-text="R340 BOM firmy Dell.":::

## <a name="next-steps"></a>Następne kroki

[Informacje o instalacji usługi Azure Defender for IoT](how-to-install-software.md)

[Informacje o usłudze Azure Defender dla sieci IoT — konfiguracja](how-to-set-up-your-network.md)
